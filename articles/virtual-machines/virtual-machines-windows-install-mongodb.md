<properties
    pageTitle="Instalar MongoDB num Windows VM | Microsoft Azure"
    description="Saiba como instalar MongoDB numa VM Azure a executar o Windows Server 2012 R2 criados com o modelo de implementação do Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalar e configurar o MongoDB uma VM do Windows no Azure
[MongoDB](http://www.mongodb.org) é uma popular abrir origem alto desempenho NoSQL base de dados. Este artigo orienta-o através de instalar e configurar MongoDB numa Windows Server 2012 R2 máquina virtual (VM) no Azure. Também pode [instalar MongoDB numa VM Linux no Azure](virtual-machines-linux-install-mongodb.md).


## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar e configurar MongoDB, tem de criar uma VM e, idealmente, adicionar-lhe um disco de dados. Consulte os artigos seguintes para criar uma VM e adicionar um disco de dados:

- [Criar uma VM de servidor do Windows através do portal Azure](virtual-machines-windows-hero-tutorial.md) ou [criar uma VM de servidor do Windows através do PowerShell do Azure](virtual-machines-windows-ps-create.md)
- [Anexar um disco de dados para uma VM de servidor do Windows através do portal Azure](virtual-machines-windows-attach-disk-portal.md) ou [anexar um disco de dados para uma VM de servidor do Windows através do PowerShell do Azure](https://msdn.microsoft.com/library/mt603673.aspx)
    
Para iniciar a instalação e configuração MongoDB, [Inicie sessão no seu VM de servidor do Windows](virtual-machines-windows-connect-logon.md) através do ambiente de trabalho remoto.


## <a name="install-mongodb"></a>Instalar MongoDB

> [AZURE.IMPORTANT] Funcionalidades de segurança de MongoDB, como a autenticação e ligação do endereço IP, não estão ativadas por predefinição. Funcionalidades de segurança devem estar ativadas antes de implementar MongoDB para ambiente de produção. Para mais informações, consulte o artigo [MongoDB segurança e autenticação](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Depois de se tenha ligado ao seu VM utilizando o ambiente de trabalho remoto, abra o Internet Explorer a partir do menu **Iniciar** na VM.

2. Selecione **utilização recomendada segurança, privacidade e as definições de compatibilidade** quando o Internet Explorer abre pela primeira vez e clique em **OK**.

3. Configuração de segurança avançada do Internet Explorer está ativada por predefinição. Adicione o Web site MongoDB à lista de sites permitidos:

    - Selecione o ícone de **Ferramentas** no canto superior direito.
    - Nas **Opções da Internet**, selecione o separador **segurança** e, em seguida, selecione o ícone de **Sites fidedignos** .
    - Clique no botão de **Sites** . Adicionar _https://\*. mongodb.org_ à lista de sites fidedignos e, em seguida, feche a caixa de diálogo.

    ![Configurar definições de segurança do Internet Explorer](./media/virtual-machines-windows-install-mongodb/configure-internet-explorer-security.png)

4. Navegue para a página de [transferências de MongoDB -](http://www.mongodb.org/downloads) (http://www.mongodb.org/downloads).

5. Por predefinição, deverá seleccionar a edição de **Servidor da Comunidade** e a versão mais recente da estável atual para o Windows Server 2008 R2 64 bits e versões posteriores. Para transferir o programa de instalação, clique em **Transferir (msi)**.

    ![Transferir MongoDB installer](./media/virtual-machines-windows-install-mongodb/download-mongodb.png)

    Execute o installer depois da transferência estiver concluída.

6. Leia e aceite o contrato de licença. Quando lhe for pedido, selecione instalar de **concluída** .

7. No ecrã final, clique em **instalar**.


## <a name="configure-the-vm-and-mongodb"></a>Configurar o VM e MongoDB

1. Variáveis de caminho não são atualizadas pelo instalador MongoDB. Sem o MongoDB `bin` a localização no seu variável de path, precisa de especificar o caminho completo cada vez que utiliza um executável MongoDB. Para adicionar a localização para a variável de path:

    - Botão direito do rato no menu **Iniciar** e selecione **sistema**.
    - Clique em **Definições avançadas do sistema**e, em seguida, clique em **Ambiente variáveis**.
    - Em **variáveis do sistema**, selecione o **caminho**e, em seguida, clique em **Editar**.

    ![Configurar variáveis de caminho](./media/virtual-machines-windows-install-mongodb/configure-path-variables.png)

    Adicionar o caminho para o seu MongoDB `bin` pasta. MongoDB é normalmente instalada no `C:\Program Files\MongoDB`. Verifique se o caminho de instalação no seu VM. O exemplo seguinte adiciona a predefinição MongoDB instalar localização para a `PATH` variável:

    ```
    ;C:\Program Files\MongoDB\Server\3.2\bin
    ```

    > [AZURE.NOTE] Não se esqueça de adicionar o ponto e vírgula à esquerda (`;`) para indicar que está a adicionar uma localização para sua `PATH` variável.

2. Crie directórios de dados e de registo de MongoDB no disco de dados. No menu **Iniciar** , selecione a **linha de comandos**. Os exemplos seguintes crie os directórios na unidade f:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```

3. Inicie uma instância de MongoDB com o seguinte comando, ajustando o caminho para os seus dados e inicie sessão diretórios, consoante adequado:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Poderá demorar vários minutos MongoDB atribuir os ficheiros de diário e começar a escutar para ligações. Todas as mensagens de registo forem instruídas para o ficheiro *F:\MongoLogs\mongolog.log* como `mongod.exe` servidor é iniciado e atribui ficheiros de diário.

    > [AZURE.NOTE] Linha de comandos fiquem com o foco nesta tarefa enquanto a instância MongoDB estiver em execução. Deixe a janela de linha de comandos aberta para continuar a executar MongoDB. Em alternativa, instale o MongoDB como serviço, tal como descrito no próximo passo.

4. Para uma experiência de MongoDB mais eficaz, instale o `mongod.exe` como um serviço. Criação de um serviço significa que não precisa de sair de uma linha de comandos em execução de cada vez que pretende utilizar MongoDB. Crie o serviço da seguinte forma, ajustando o caminho para o seu directórios de dados e de registo, consoante adequado:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```

    O comando anterior cria um serviço com o nome MongoDB, com uma descrição das "Mongo DB". Os seguintes parâmetros também são especificados:

    - O `--dbpath` opção especifica a localização do directório de dados.
    - O `--logpath` opção tem de ser utilizada para especificar um ficheiro de registo, porque o serviço em execução não possui uma janela de comando para apresentar o resultado.
    - O `--logappend` opção especifica que um reinício do serviço de faz com que o resultado acrescentar ao ficheiro de registo existente.

  Para iniciar o serviço de MongoDB, execute o seguinte comando:

    ```
    net start MongoDB
    ```

    Para mais informações sobre como criar o serviço de MongoDB, consulte o artigo [configurar um serviço do Windows para MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testar a instância MongoDB

Com MongoDB em execução como uma única ocorrência ou instalado como um serviço, pode agora começar a criar e utilizar as bases de dados. Para começar a shell de administrativa MongoDB, abrir outra janela de linha de comandos a partir do menu **Iniciar** e, introduza o seguinte comando:

```
mongo  
```

Pode listar as bases de dados com a `db` comando. Inserir alguns dados da seguinte forma:

```
db.foo.insert( { a : 1 } )
```

Procure dados da seguinte forma:

```
db.foo.find()
```

O resultado é semelhante ao exemplo seguinte:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Sair de `mongo` console da seguinte forma:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurar a firewall e as regras de grupo de segurança de rede
Agora que MongoDB está instalada e em execução, abra uma porta na Firewall do Windows para que se pode ligar remotamente para MongoDB. Para criar uma nova regra de entrada para permitir que portas TCP 27017, abra uma linha de comandos do PowerShell administrativa e introduza o seguinte comando:

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
    -Protocol TCP -LocalPort 27017 -Action Allow
```

Também pode criar a regra utilizando a ferramenta de gestão Estruturador da **Firewall do Windows com segurança avançada** . Crie uma nova regra de entrada para permitir que portas TCP 27017.

Se for necessário, crie uma regra de grupo de segurança de rede para permitir o acesso a MongoDB a partir de fora da sub-rede Azure rede virtual existente. Pode criar as regras de grupo de segurança de rede utilizando o [Azure portal](virtual-machines-windows-nsg-quickstart-portal.md) ou [Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md). Tal como acontece com as regras de Firewall do Windows, permitir porta TCP 27017 para a interface de rede virtual da sua VM MongoDB.

> [AZURE.NOTE] Porta TCP 27017 é a porta predefinida utilizada por MongoDB. Pode alterar esta porta utilizando o `--port` parâmetro ao iniciar `mongod.exe` manualmente ou de um serviço. Se alterar a porta, certifique-se atualizar as regras de Firewall do Windows e o grupo de segurança de rede nos passos anteriores.


## <a name="next-steps"></a>Próximos passos
Neste tutorial, aprendeu como instalar e configurar o MongoDB no seu VM do Windows. Agora pode aceder a MongoDB no seu Windows VM, seguindo os tópicos na [documentação MongoDB](https://docs.mongodb.com/manual/)avançados.
