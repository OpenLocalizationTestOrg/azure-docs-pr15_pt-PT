<properties
    pageTitle="Atualizar o agente Linux Azure a partir do GitHub | Microsoft Azure"
    description="Saiba como a atualização Azure Linux agente para o seu VM Linux no Azure para a versão de lateset a partir do Github"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/14/2015"
    ms.author="mingzhan"/>


# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Como atualizar o Azure Linux Agent uma VM para a versão mais recente do GitHub

Para atualizar o seu [Azure Linux agente](https://github.com/Azure/WALinuxAgent) numa VM Linux no Azure, tem já tiver:

1. Uma VM Linux em execução no Azure.
2. Uma ligação a essa VM Linux utilizando SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Se vai executar esta tarefa a partir de um computador Windows, pode utilizar betumes para SSH para o seu computador Linux. Para mais informações, consulte o artigo [como iniciar sessão no Linux de executar uma Máquina Virtual](virtual-machines-linux-mac-create-ssh-keys.md).

Menção Azure distros Linux tem de colocar o pacote do Azure Linux agente nos seus repositórios, por isso Verifique e instalar a versão mais recente a partir desse repositório Distro pela primeira vez se possível.  

Para Ubuntu, basta escreva:

    #sudo apt-get install walinuxagent

E em CentOS, escreva:

    #sudo yum install waagent


Oracle Linux, certifique-se de que o `Addons` repositório está ativado. Selecione para editar o ficheiro `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste ficheiro.

Em seguida, para instalar a versão mais recente do agente de Linux Azure, escreva:


    #sudo yum install WALinuxAgent

Se não encontrar o repositório de suplemento pode adicionar simplesmente estas linhas no final do seu ficheiro .repo de acordo com a edição do seu Oracle Linux:

Para máquinas virtuais de Oracle Linux 6:

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Para máquinas virtuais de Oracle Linux 7:

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

Em seguida, escreva:

    #sudo yum update WALinuxAgent

Normalmente, isto é a totalidade precisar, mas se por algum motivo precisa de instalar a partir de https://github.com diretamente, siga os passos seguintes.


## <a name="install-wget"></a>Instalar wget

Inicie sessão no seu VM utilizando SSH.

Instalar o wget (existem algumas distros que não instalá-lo por predefinição, tais como as versões sistema Redhat, CentOS e Oracle Linux 6.4 e 6.5) ao escrever `#sudo yum install wget` na linha de comandos.


## <a name="download-the-latest-version"></a>Transferir a versão mais recente

Abra [a versão do Azure Linux agente no GitHub](https://github.com/Azure/WALinuxAgent/releases) numa página web e descobrir o número da versão mais recente. (Pode localizar a sua versão atual ao escrever `#waagent --version`.)

### <a name="for-version-20x-type"></a>Para a versão 2.0, escreva:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   A linha seguinte utiliza a versão 2.0.14 como exemplo:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### <a name="for-version-21x-or-later-type"></a>Para a versão 2.1.x ou mais tarde, escreva:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   A linha seguinte utiliza a versão 2.1.0 como exemplo:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## <a name="install-the-azure-linux-agent"></a>Instalar o agente Linux Azure

### <a name="for-version-20x-use"></a>Para a versão 2.0, utilize:

 Torne waagent executável:

    #chmod +x waagent

 Copie o ficheiro executável de novo para /usr/sbin /.

  Para a maioria dos Linux, utilize:

    #sudo cp waagent /usr/sbin

  Para CoreOS, utilize:

    #sudo cp waagent /usr/share/oem/bin/

  Se se tratar de uma nova instalação do agente de Linux Azure, execute:
 
    #sudo /usr/sbin/waagent -install -verbose

### <a name="for-version-21x-use"></a>Para a versão 2.1.x, utilize:

Poderá ter de instalar o pacote de `setuptools` primeiro – ver [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute:

    #sudo python setup.py install

## <a name="restart-the-waagent-service"></a>Reinicie o serviço de waagent

Para a maioria dos linux Distros:

    #sudo service waagent restart

Para Ubuntu, utilize:

    #sudo service walinuxagent restart

Para CoreOS, utilize:

    #sudo systemctl restart waagent

## <a name="confirm-the-azure-linux-agent-version"></a>Confirmar a versão do Azure Linux Agent

    #waagent -version

Para CoreOS, o comando acima poderá não funcionar.

Verá que a versão do Azure Linux Agent foi atualizada para a nova versão.

Para mais informações sobre o agente de Linux Azure, consulte o artigo [Azure Linux agente Leia-me](https://github.com/Azure/WALinuxAgent).
