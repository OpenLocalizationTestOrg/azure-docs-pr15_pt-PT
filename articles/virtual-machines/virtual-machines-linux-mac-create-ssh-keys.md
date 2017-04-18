<properties
    pageTitle="Criar SSH teclas no Linux e Mac | Microsoft Azure"
    description="Gerar e utilizar teclas SSH no Linux e Mac para o Gestor de recursos e modelos de implementação clássico num Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="v-livech"/>

# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Criar SSH teclas no Linux e Mac para Linux VMs no Azure

Com um par SSH pode criar máquinas virtuais no Azure predefinido a utilizar teclas SSH para autenticação, eliminando a necessidade de palavras-passe iniciar sessão.  As palavras-passe podem ser estimadas e abra o seu VMs até um força bruta tentativas adivinhar a sua palavra-passe. VMs criados com modelos de Azure ou os `azure-cli` podem incluir a sua chave pública SSH como parte de implementação, remover uma configuração de implementação de mensagem.  Se estiver a ligar a uma VM Linux a partir do Windows, consulte o artigo [deste documento.](virtual-machines-linux-ssh-from-windows.md)

Requer o artigo:

- uma conta Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Clip do Azure](../xplat-cli-install.md) tem sessão iniciada com`azure login`

- no modo do Azure clip _tem de estar no_ Gestor de recursos do Azure`azure config mode arm`

## <a name="quick-commands"></a>Comandos rápidos

Nos seguintes comandos, substitua os exemplos da suas própria escolhas.

SSH teclas por predefinição são mantida na `.ssh` diretório.  

```bash
cd ~/.ssh/
```

Se não tiver um `~/.ssh` diretório a `ssh-keygen` comando irá criá-la por si com as permissões corretas.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

Introduza o nome do ficheiro que é guardado na `~/.ssh/` diretório:

```bash
id_rsa
```

Introduza a frase de acesso do id_rsa:

```bash
correct horse battery staple
```

Agora existe um `id_rsa` e `id_rsa.pub` SSH par chave no `~/.ssh` diretório.

```bash
ls -al ~/.ssh
```

Adicionar a chave recentemente criada para `ssh-agent` no Linux e Mac (também adicionados ao porta-chaves dos x):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Copie a chave pública SSH ao seu servidor Linux:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

Teste o utilizar teclas em vez de uma palavra-passe de início de sessão:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>Obter instruções detalhadas sobre

Utilizar chaves públicas e privadas de SSH é a forma mais fácil para iniciar sessão na sua servidores Linux. [Criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) fornece uma forma muito mais segura para iniciar sessão na sua Linux ou BSD VM no Azure que palavras-passe, que podem ser forçada bruta extremidade mais facilmente. A chave pública pode ser partilhada com qualquer pessoa; mas apenas o utilizador (ou sua infraestrutura de segurança local) possuir a chave privada.  A chave privada SSH deve ter uma [palavra-passe segura muito](https://www.xkcd.com/936/) (origem:[xkcd.com](https://xkcd.com)) para salvaguardá-lo.  Esta palavra-passe é apenas aceder a chave privada de SSH e, em seguida, **não é** a palavra-passe da conta de utilizador.  Quando adiciona uma palavra-passe para a sua chave de SSH, encripta a chave privada para que a chave privada é inútil sem a palavra-passe para a desbloquear.  Se um intruso roubo a chave privada e essa tecla não tem uma palavra-passe, teria poderá utilizar essa chave privada que inicie sessão na quaisquer servidores que tenham a chave pública correspondente.  Se uma chave privada esteja protegido por palavra-não podem ser utilizados por esse intruso, fornecendo uma camada adicional de segurança para sua infraestrutura no Azure.

Este artigo cria ficheiros chave *ssh rsa* formatado, que lhe foram recomendados para implementações no Gestor de recursos.  chaves *SSH rsa* são necessários no [portal](https://portal.azure.com) para implementações clássica e o Gestor de recursos.

## <a name="create-the-ssh-keys"></a>Criar as teclas SSH

Azure necessita de 2048-bit, ssh rsa Formatar chaves públicas e privadas. Para criar a utilização de chaves `ssh-keygen`, que pede uma série de perguntas e, em seguida, escreve uma chave privada e uma chave pública correspondente. Quando é criado um VM Azure, a chave pública é copiada para `~/.ssh/authorized_keys`.  Teclas SSH no `~/.ssh/authorized_keys` são utilizadas para desafio o cliente para corresponder a chave privada correspondente numa ligação de início de sessão SSH.

## <a name="using-ssh-keygen"></a>Utilizar ssh keygen

Este comando cria uma palavra-passe protegida (encriptados) SSH par utilizam RSA de 2048-bit e -é Comentada para identificar facilmente.  

Iniciar ao alterar nos diretórios, para que todos os seus ssh chaves são criadas nesse directório.

```bash
cd ~/.ssh
```

Se não tiver um `~/.ssh` diretório a `ssh-keygen` comando irá criá-la por si com as permissões corretas.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

_Comando explicado_

`ssh-keygen`= programa utilizado para criar as teclas

`-t rsa`= tipo de tecla para criar o que é o [RSA formato](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`= bits da chave

`-C "myusername@myserver"`= um comentário acrescentado para o fim do ficheiro de chave pública para identificar facilmente.  Normalmente, uma mensagem de e-mail é utilizada como o comentário mas pode utilizar o melhor se adequa para sua infraestrutura.

### <a name="using-pem-keys"></a>Utilizar teclas PEM

Se estiver a utilizar a clássica implementar modelo (Azure clássica Portal ou o clip de gestão de serviço do Azure `asm`), poderá ter de utilizar PEM formatado SSH teclas para aceder à sua VMs Linux.  Eis como criar uma chave PEM a partir de uma SSH chave pública existente e uma existente x509 certificado.

Para criar um PEM formatada chave a partir de uma chave pública de SSH existente:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exemplo de ssh keygen

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Guardar ficheiros de chave:

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

O nome de par de chaves para deste artigo.  Ter um par de chaves denominado **id_rsa** é a predefinição e algumas ferramentas podem pensar o nome de ficheiro de chave privada **id_rsa** , pelo que está a ter um é uma boa ideia. Diretório `~/.ssh/` é a localização predefinida para os pares de chaves SSH e o ficheiro de configuração SSH.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
Uma listagem do `~/.ssh` diretório. `ssh-keygen`cria a `~/.ssh` diretório se não existir e também define os modos de propriedade e ficheiro corretos.

Palavra-passe chave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`refere-se a uma palavra-passe, como "uma frase de acesso."  É *vivamente* recomendado para adicionar uma palavra-passe ao seu pares de chaves. Sem uma palavra-passe proteger o par de chaves, qualquer pessoa com ficheiro de chave privada pode utilizá-lo para iniciar sessão qualquer servidor que tem a chave pública correspondente. Adicionar uma palavra-passe oferece mais proteção no caso de uma pessoa é possível aceder ao seu ficheiro de chave privada, tendo em conta tempo para alterar as teclas utilizado para autenticar o utilizador.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Utilizar ssh agente para armazenar a sua palavra-passe da chave privada

Para evitar a escrever a palavra-passe do ficheiro de chave privada com início de sessão cada SSH, pode utilizar `ssh-agent` para a sua palavra-passe do ficheiro de chave privada em cache. Se estiver a utilizar um Mac, a porta-chaves dos x segura armazena as palavras-passe da chaves privadas quando invocar `ssh-agent`.

Verifique se o primeiro `ssh-agent` está a ser executado

```bash
eval "$(ssh-agent -s)"
```

Agora, adicionar a chave privada `ssh-agent` utilizando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

A palavra-passe da chave privada agora está armazenado no `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um ficheiro de configuração SSH

É aconselhável recomendado para criar e configurar uma `~/.ssh/config` ficheiro para acelerar a iniciar sessão ins e para otimizar o comportamento de cliente do SSH.

O exemplo seguinte mostra uma configuração padrão.

### <a name="create-the-file"></a>Criar o ficheiro

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edite o ficheiro para adicionar a nova configuração SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Exemplo `~/.ssh/config` ficheiro:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Esta configuração SSH dá-lhe secções para cada servidor para permitir que cada um deles ter as suas próprias dedicada par chave. As predefinições (`Host *`) são para qualquer anfitriões que não correspondam aos anfitriões específicos mais para cima no ficheiro de configuração.


### <a name="config-file-explained"></a>Ficheiro de configuração explicado

`Host`= o nome do anfitrião do que está a ser denominado no terminal.  `ssh fedora22`indica `SSH` utilizar os valores no bloco de definições denominada `Host fedora22` Nota: este pode ser qualquer etiqueta que é lógica para a sua utilização e não representar o nome do anfitrião real de qualquer servidor.

`Hostname 102.160.203.241`= o endereço IP ou o nome DNS para o servidor a ser acedido.

`User myusername`= a conta de utilizador remoto para utilizar ao iniciar sessão no servidor.

`PubKeyAuthentication yes`= indica SSH que pretende utilizar uma chave SSH para iniciar sessão.

`IdentityFile /home/myusername/.ssh/id_id_rsa`= a chave privada SSH e chave pública correspondente a utilizar para autenticação.


## <a name="ssh-into-linux-without-a-password"></a>SSH para Linux sem uma palavra-passe

Agora que tem um par de chaves SSH e um ficheiro de configuração SSH configurado, é possível que inicie sessão na sua VM Linux rapidamente e segura. A primeira vez que iniciar sessão no servidor com uma chave de SSH os pedidos de comando para a frase de acesso para esse ficheiro chave.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Comando explicado

Quando `ssh fedora22` é executado SSH localiza e carrega quaisquer definições a partir do `Host fedora22` bloco e, em seguida cargas todas as restantes definições a última bloqueio de, `Host *`.

## <a name="next-steps"></a>Próximos passos

Seta para cima seguinte é criar Azure Linux VMs utilizando a nova chave pública SSH.  Azure VMs que são criados com uma chave pública SSH como login melhor estão seguros que VMs criados com o método de início de sessão do predefinido, palavras-passe.  Azure VMs criadas utilizando as teclas SSH são por predefinição configurada com palavras-passe desactivadas, evitando forçada bruta algum tentativas.

- [Criar uma VM Linux seguro utilizando um modelo do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Criar uma VM Linux seguro através do portal Azure](virtual-machines-linux-quick-create-portal.md)
- [Criar uma VM Linux seguro utilizando o clip do Azure](virtual-machines-linux-quick-create-cli.md)
