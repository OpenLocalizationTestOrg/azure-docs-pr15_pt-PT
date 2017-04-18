<properties
    pageTitle="Utilizar a extensão de CustomScript numa VM Linux | Microsoft Azure"
    description="Saiba como utilizar a extensão de CustomScript para implementar no Linux máquinas virtuais no Azure criado utilizando o modelo clássico de implementação de aplicações."
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="gbowerman"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="multiple"
    ms.tgt_pltfrm="linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

#<a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Implementar uma aplicação de luz com a extensão do Azure CustomScript para Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


A extensão do Microsoft Azure CustomScript para Linux fornece uma forma para personalizar o seu máquinas virtuais (VMs) ao executar arbitrário código escrito em qualquer linguagem de scripts suportada pela VM (por exemplo, Python e festa). Isto fornece uma forma muito flexível para automatizar a implementação de aplicação para vários computadores.

Pode implementar a extensão de CustomScript utilizando o portal clássico Azure, Windows PowerShell ou a Interface de comandos do Azure (Azure CLI).

Neste artigo, que vamos utilizar o clip do Azure para implementar uma aplicação de luz simple para uma VM Ubuntu criada utilizando o modelo de implementação clássica.

## <a name="prerequisites"></a>Pré-requisitos

Neste exemplo, crie primeiro duas Azure VMs com Ubuntu 14.04 ou posterior. Os VMs são denominados *script vm* e *luz vm*. Utilize nomes únicos quando criar os VMs. Um é utilizado para executar os comandos do clip e um é utilizado para implementar a aplicação de luz.

Também precisa de uma conta de armazenamento do Windows Azure e uma tecla para aceder à mesma (pode obter este partir do Azure portal clássico).

Se precisar de ajuda sobre como criar Linux VMs no Azure referir-se a [Criar Linux de executar uma Máquina Virtual](virtual-machines-linux-classic-createportal.md).

Os comandos de instalação partem do pressuposto de Ubuntu, mas pode adaptar a instalação para qualquer distro Linux suportado.

A VM script vm tem de ter o clip de Azure instaladas, com uma ligação de trabalho ao Azure. Para obter ajuda com este consulte [instalar e configurar a Interface de comandos do Azure](../xplat-cli-install.md).

## <a name="upload-a-script"></a>Carregar um script

Vamos utilizar a extensão de CustomScript para executar um script uma VM remota para instalar a pilha de luz e criar uma página PHP. Para poder aceder o script a partir de qualquer lugar podemos irá carregá-lo como um BLOBs do Azure.

### <a name="script-overview"></a>Descrição geral de script

O exemplo de script instala uma pilha de luz Ubuntu (incluindo a configuração de uma instalação automática do MySQL), escreve um ficheiro PHP simple e inicia Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Carregar o script

Guarde o script como um ficheiro de texto, por exemplo *install_lamp.sh*e, em seguida, carregue-o para o armazenamento do Windows Azure. Pode fazê-lo facilmente com clip Azure. O exemplo seguinte carrega o ficheiro de um contentor de armazenamento com o nome "scripts". Se o contentor não existe terá de criá-la pela primeira vez.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Também crie um ficheiro JSON que descreve como transferir o script do armazenamento do Windows Azure. Guardá-lo como *public_config.json* (substituindo "mystorage" com o nome da sua conta de armazenamento):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Implementar a extensão

Agora pode utilizar o comando seguinte para implementar a extensão de CustomScript Linux a VM remota utilizando o clip do Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

O comando anterior transfere e executa o script *install_lamp.sh* no VM denominado *luz vm*.

Uma vez que a aplicação inclui um servidor web, lembre-se abrir uma porta de espera HTTP na VM remota com o comando seguinte.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Monitorização e resolução de problemas

Pode verificar no bem como o script personalizado é executado verificando o ficheiro de registo a VM remoto. SSH *vm de luz* e Cauda o ficheiro de registo com o comando seguinte.

    cd /var/log/azure/customscript
    tail -f handler.log

Depois de executar a extensão de CustomScript, pode navegar para a página PHP que criou para obter informações. A página PHP para o exemplo neste artigo está *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Recursos adicionais

Pode utilizar os mesmos passos básicos para implementar o apps mais complexos. Neste exemplo, o script de instalação foi guardado como um blob público no armazenamento do Windows Azure. Uma opção mais segura seria armazenar o script de instalação como um blob seguro com uma [Assinatura de acesso seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SA).

Recursos adicionais para Azure clip, Linux e a extensão de CustomScript são listados ao lado.

[Automatizar tarefas de personalização Linux VM utilizando CustomScript extensão](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux extensões (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux e abrir-origem computação no Azure](virtual-machines-linux-opensource-links.md)
