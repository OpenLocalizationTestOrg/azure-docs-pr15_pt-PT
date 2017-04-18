<properties
   pageTitle="Encriptar discos numa VM Linux | Microsoft Azure"
   description="Como encriptar discos numa VM Linux utilizando o clip do Azure e o modelo de implementação do Gestor de recursos"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/11/2016"
   ms.author="iainfou"/>

# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>Encriptar discos numa VM Linux utilizando o clip do Azure
Para melhorado máquina virtual (VM) segurança e conformidade, discos virtuais no Azure podem ser encriptados em repouso. Discos são encriptados utilizando as teclas de criptografia que são protegidas num cofre de chave do Azure. Controlar estas teclas criptografia e pode auditar a sua utilização. Este artigo explica detalhadamente como encriptar discos virtuais numa VM Linux utilizando o clip do Azure e o modelo de implementação do Gestor de recursos.


## <a name="quick-commands"></a>Comandos rápidos
Se precisa de realizar rapidamente a tarefa, os seguintes detalhes de secção a base para os comandos para encriptar discos virtuais no seu VM. Obter informações mais detalhadas e o contexto para cada passo podem encontrar o resto do documento, [começando aqui](#overview-of-disk-encryption).

Precisa de [Mais recente Azure clip](../xplat-cli-install.md) instalados e tiver sessão iniciada na utilizando o modo de Gestor de recursos da seguinte forma:

```
azure config mode arm
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem `myResourceGroup`, `myKeyVault`, e `myVM`.

Em primeiro lugar, ativar o fornecedor do Azure chave cofre dentro da sua subscrição do Azure e como criar um grupo de recursos. O exemplo seguinte cria um nome de grupo de recursos `myResourceGroup` na `WestUS` localização:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Crie um cofre chave Azure. O exemplo seguinte cria uma cofre chave com o nome `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Criar uma chave de criptografia no cofre chave e ativá-la para encriptação do disco. O exemplo seguinte cria uma chave denominada `myKey`:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Crie um ponto final de utilizar o Azure Active Directory para processamento de autenticação e trocar de criptografia teclas do cofre chave. O `--home-page` e `--identifier-uris` não necessita de ser endereço encaminhável real. Para o mais alto nível de segurança, segredos cliente deverão ser utilizados em vez de palavras-passe. O clip do Azure atualmente não é possível gerar segredos de cliente. Só podem ser gerados segredos de cliente no portal do Azure. O exemplo seguinte cria um ponto final Azure Active Directory com o nome `myAADApp` e utiliza uma palavra-passe de `myPassword`. Especifique a sua própria palavra-passe da seguinte forma:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Nota a `applicationId` apresentado no resultado do comando anterior. Este ID da aplicação é utilizado nos passos seguintes:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Adicione um disco de dados para um VM existente. O exemplo seguinte adiciona um disco de dados para uma VM denominada `myVM`:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Reveja os detalhes para Cofre de tecla e a chave que criou. Precisa do ID da chave cofre, URI e chave de URL no passo final. O exemplo seguinte revê os detalhes do cofre chave denominada `myKeyVault` e chave denominada `myKey`:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Encripte seus discos da seguinte forma, introduzir o seus próprio os nomes dos parâmetros ao longo:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

O clip do Azure não fornece verbosas erros durante o processo de encriptação. Para informações adicionais de resolução de problemas, reveja `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Enquanto o comando anterior tem muitas variáveis e poderá não obter muito indicação Control por que motivo o processo de falhar, um exemplo de comando concluída seria da seguinte forma:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Por fim, reveja o estado de encriptação novamente para confirmar o seu discos virtuais agora tenham sido encriptados. O exemplo seguinte verifica o estado de um VM denominada `myVM` na `myResourceGroup` grupo de recursos:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Descrição geral de encriptação do disco
Discos virtuais no Linux VMs são encriptados em repouso utilizar [encriptação dm](https://wikipedia.org/wiki/Dm-crypt). Não existe nenhuma encargo para encriptar discos virtuais no Azure. Teclas de criptografia são armazenadas no Azure chave cofre através de proteção de software, ou pode importar ou gerar suas chaves nos módulos de segurança do Hardware (Os HSMs) certificados para FIPS normas de nível 2 140-2. Manter o controlo destas teclas criptografia e pode auditar a sua utilização. Estas teclas criptografia são utilizadas para encriptar e desencriptar discos virtuais anexados a sua VM. Um ponto final Azure Active Directory fornece um mecanismo seguro para a emissão estas teclas criptografia como VMs são powered e desativar.

O processo para encriptar uma VM é da seguinte forma:

1. Crie uma chave de criptografia num cofre de chave do Azure.
2. Configure a tecla de criptografia para ser utilizado para encriptar discos.
3. Para ler a chave de criptografia do Cofre de palavras chave Azure, crie um ponto final utilizando o Azure Active Directory com as permissões adequadas.
4. Emita o comando para encriptar o seu discos virtuais, especificando o ponto final do Azure Active Directory e a chave de criptografia adequado para ser utilizado.
5. O ponto final do Azure Active Directory solicita a chave de criptografia necessária do Azure chave cofre.
6. Os discos virtuais são encriptados utilizando a chave de criptografia fornecida.


## <a name="supporting-services-and-encryption-process"></a>Processo de encriptação e serviços de suporte
Encriptação do disco baseia-se sobre os seguintes componentes adicionais:

- **Azure chave cofre** - utilizado para salvaguardar chaves de criptografia e segredos utilizados para o processo de encriptação/desencriptação disco. 
  - Caso exista alguma, pode utilizar uma chave de cofre existente do Azure. Não possui a dedicar cofre chave a encriptação discos.
  - Para separar os limites administrativos e visibilidade chave, pode criar dedicada chave cofre.
- **Azure Active Directory** - trata do seguro intercâmbio de autenticação para ações pedidas e chaves de criptografia necessárias. 
  - Normalmente, pode utilizar uma instância do Azure Active Directory existente para a aplicação de alojamento. 
  - A aplicação é mais de um ponto final para os serviços de chave do cofre e Máquina Virtual pedir e obter emitido as teclas de criptografia adequadas. Não está a desenvolver uma aplicação real que integra-se com o Azure Active Directory.


## <a name="requirements-and-limitations"></a>Requisitos e limitações
Requisitos para encriptação do disco e cenários suportados:

- O seguinte Linux servidor SKUs - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e vermelho chapéu Enterprise Linux.
- Todos os recursos (como chave cofre, conta de armazenamento e VM) tem de ser na mesma região Azure e subscrição.
- A padrão, D, DS, G e s série VMs.

Atualmente, não é suportada encriptação do disco nos cenários seguintes:

- Camada básica VMs.
- VMs criadas utilizando o modelo de implementação clássica.
- Desativar a encriptação disco SO Linux VMs.
- Atualizar as teclas de criptografia uma VM Linux já encriptada.


## <a name="create-the-azure-key-vault-and-keys"></a>Criar Cofre de chave do Azure e chaves
Para concluir o resto deste guia, precisa de [Mais recente Azure clip](../xplat-cli-install.md) instalados e tiver sessão iniciada na utilizando o modo de Gestor de recursos da seguinte forma:

```
azure config mode arm
```

Em todo os exemplos de comando, substitua todos os parâmetros de exemplo com o seu próprio nomes, localização e valores de chave. Os exemplos seguintes utilizam uma convenção de `myResourceGroup`, `myKeyVault`, `myAADApp`, etc.

O primeiro passo é criar uma Cofre de chave do Azure para armazenar as suas chaves criptografia. Azure chave cofre pode armazenar chaves, segredos ou palavras-passe que lhe permitem implementá-las em segurança no seu aplicações e serviços. Para encriptação de disco virtual, utilize cofre chave para armazenar uma chave de criptografia que é utilizada para encriptar ou desencriptar seus discos virtuais. 

Ativar o fornecedor do Azure chave cofre na sua subscrição do Azure e, em seguida, criar um grupo de recursos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `WestUS` localização:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Azure chave cofre que contém as chaves de criptografia e recursos de cluster associados, tais como o armazenamento e a VM propriamente dito tem residem na mesma região. O exemplo seguinte cria uma Cofre de chave do Azure denominada `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Pode armazenar teclas criptografia utilizando o software ou da proteção do modelo de segurança de Hardware (HSM). Utilizar um HSM requer um prémio chave cofre. Existe um custo adicional para a criação de um prémio cofre chave em vez de padrão Cofre de chave que armazena chaves protegido por software. Para criar uma chave de cofre premium, no passo anterior adicionar `--sku Premium` ao comando. O exemplo seguinte utiliza protegido por software chaves, uma vez que criámos cofre chave padrão. 

Para ambos os modelos de proteção contra a plataforma Azure tem de lhe ser concedido acesso para pedir as teclas de criptografia quando a VM botas desencriptar os discos virtuais. Criar uma chave de encriptação do Cofre de tecla e, em seguida, ativá-la para utilização com encriptação virtual do disco. O exemplo seguinte cria uma chave denominada `myKey` e, em seguida, a ativar para encriptação do disco:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Criar a aplicação do Azure Active Directory
Quando discos virtuais estão encriptados ou desencriptar, utilize um ponto final para processar a autenticação e de intercâmbio de criptografia teclas do cofre chave. Este ponto final, uma aplicação do Azure Active Directory, permite a plataforma Azure pedir das chaves de criptografia adequadas em nome da VM. Uma instância do Azure Active Directory predefinida está disponível na sua subscrição, apesar de muitas organizações tem dedicado directórios Azure Active Directory.

Como não estiver a criar uma aplicação completa do Azure Active Directory, o `--home-page` e `--identifier-uris` parâmetros no exemplo seguinte não necessita de ser endereço encaminhável real. O exemplo seguinte também especifica uma palavra-passe com base em palavra-passe em vez de gerar chaves a partir de portal do Azure. Como desta vez, gerar chaves não pode ser feita o clip do Azure. 

Crie uma aplicação do Azure Active Directory. O exemplo seguinte cria uma aplicação chamada `myAADApp` e utiliza uma palavra-passe de `myPassword`. Especifique a sua própria palavra-passe da seguinte forma:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Tome nota da `applicationId` que é devolvido na saída do comando anterior. Este ID da aplicação é utilizado em alguns dos passos restantes. Em seguida, crie um nome principal do serviço (SPN) para que a aplicação está acessível no seu ambiente. Para encriptar ou desencriptar discos virtuais com êxito, tem de estar definidas para permitir que a aplicação do Azure Active Directory para ler as teclas de permissões da chave de criptografia armazenados num chave cofre. 

Crie o SPN e defina as permissões adequadas da seguinte forma:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Adicionar um disco virtual e rever o estado de encriptação
Para encriptar realmente alguns discos virtuais, permite adicionar um disco para uma VM existente. Adicione um disco de dados de 5Gb para um existente VM da seguinte forma:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Os discos virtuais atualmente não são encriptados. Rever o estado atual de encriptação da sua VM da seguinte forma:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Encriptar discos virtuais
Para encriptar agora os discos virtuais, pode reúnem todos os componentes anterior:

1. Especifique a aplicação do Azure Active Directory e a palavra-passe.
2. Especifique o Cofre chave para armazenar os metadados para os seus discos encriptados.
3. Especifique as teclas de criptografia para utilizar para encriptação real e desencriptar.
4. Especifique se pretende encriptar no disco do sistema operativo, os discos de dados ou todos.

Permite-reveja os detalhes do Cofre de chave do Azure e a chave que criou, à medida que precisa do ID do cofre chave, URI e, em seguida, tecla URL no passo final:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Encriptar o seu discos virtuais utilizando os resultados obtidos a `azure keyvault show` e `azure keyvault key show` comandos da seguinte forma:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Como o comando anterior tem muitas variáveis, o exemplo seguinte é o comando completo para referência:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

O clip do Azure não fornece verbosas erros durante o processo de encriptação. Para informações adicionais de resolução de problemas, reveja `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` no VM que está a encriptar.

Por fim, permite rever o estado de encriptação novamente para confirmar o seu discos virtuais agora tenham sido encriptados:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Adicionar dados adicionais discos
Assim que tiver encriptado seus discos de dados, pode adicionar mais tarde discos virtuais adicionais ao seu VM e também encriptá-las. Quando executa o `azure vm enable-disk-encryption` comando, incrementar a versão de sequência utilizando o `--sequence-version` parâmetro. Este parâmetro da versão de sequência permite-lhe executar operações repetidas sobre a mesma VM.

Por exemplo, permite adicionar um segundo disco virtual ao seu VM da seguinte forma:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Volte a executar o comando para encriptar discos virtuais, este adicionar tempo de `--sequence-version` parâmetro e incrementar o valor a partir do nosso primeira execução da seguinte forma:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre a gestão de Cofre de chave do Azure, incluindo a eliminação de chaves de criptografia e cofres, consulte o artigo [Gerir Cofre de palavras chave utilizando clip](../key-vault/key-vault-manage-with-cli.md).
- Para obter mais informações sobre a encriptação de disco, como a preparar um encriptada VM personalizado para carregar para o Azure, consulte o artigo [Azure disco encriptação](../security/azure-security-disk-encryption.md).