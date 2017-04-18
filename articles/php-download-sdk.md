<properties
    pageTitle="Transferir o SDK Azure para PHP"
    description="Saiba como transferir e instalar o SDK do Azure para PHP."
    documentationCenter="php"
    services="app-service\web"
    authors="allclark"
    manager="douge"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/01/2016"
    ms.author="allclark;yaqiyang"/>

#<a name="download-the-azure-sdk-for-php"></a>Transferir o SDK Azure para PHP

## <a name="overview"></a>Descrição geral

O SDK do Azure para PHP inclui componentes que permitem-lhe desenvolver, implementar e gerir pedidos de PHP Azure. Especificamente, o SDK do Azure para PHP inclui o seguinte:

* **PHP de bibliotecas do cliente para Azure**. Estas bibliotecas de classe fornecem uma interface para aceder a funcionalidades Azure, como a serviços de gestão de dados e dos serviços em nuvem.  
* **A Interface de comandos Azure para Mac, Linux e o Windows (Azure CLI)**. Este é um conjunto de comandos para implementar e gerir serviços Azure, como sites públicos do Azure e máquinas virtuais do Azure. O trabalho Azure clip qualquer plataforma, incluindo o Mac, Linux e Windows.
* **Azure PowerShell (apenas para o Windows)**. Este é um conjunto dos cmdlets do PowerShell para implementar e gerir serviços de Azure, como o serviços em nuvem e máquinas virtuais.
* **Os Azure emuladores (apenas para o Windows)**. Os armazenamento e cluster emuladores são emuladores locais dos serviços em nuvem e serviços de gestão de dados que lhe permitem testar uma aplicação localmente. O Azure emuladores executar apenas no Windows.

As secções abaixo descrevem como transferir e instalar componentes descritos acima.

As instruções neste tópico partem do princípio de que tem [PHP] [ install-php] instalado.

> [AZURE.NOTE] Tem de ter PHP 5,5 ou superior a utilizar as bibliotecas do cliente PHP para Azure.

##<a name="php-client-libraries-for-azure"></a>Bibliotecas de cliente PHP para Azure

As bibliotecas do cliente PHP para Azure fornecem uma interface para aceder a funcionalidades Azure, como a serviços de gestão de dados e na nuvem serviços, a partir de qualquer sistema operativo. Podem ser instaladas estas bibliotecas através do compositor.

Para obter informações sobre como utilizar as bibliotecas do cliente PHP para Azure, veja [como utilizar o serviço de BLOBs][blob-service], [como utilizar o serviço de tabela] [ table-service] e [como utilizar o serviço de fila de espera][queue-service].

###<a name="install-via-composer"></a>Instalar através do compositor

1. [Instalar Git][install-git].


    > [AZURE.NOTE] No Windows, também terá de adicionar o Git executável a variável de ambiente PATH.

2. Criar um ficheiro com o nome **composer.json** na raiz do seu projeto e adicionar-lhe o seguinte código:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Transferir ** [composer.phar] [ composer-phar] ** na raiz do seu projeto.

4. Abra uma linha de comandos e executar isto no seu raiz de projeto

        php composer.phar install

##<a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell e emuladores Azure

Azure PowerShell é um conjunto dos cmdlets do PowerShell para implementar e gerir dos serviços do Azure (como serviços em nuvem e máquinas virtuais). O Azure emuladores são emuladores dos serviços em nuvem e serviços de gestão de dados que lhe permitem testar localmente uma aplicação. Estes componentes são suportados apenas para o Windows.

O caminho recomendado para instalar o Azure PowerShell e o Azure emuladores é utilizar o [Microsoft Web plataforma Installer][download-wpi]. Tenha em atenção que também pode escolher instalar outros componentes de desenvolvimento, tais como PHP, SQL Server, os Drivers Microsoft para o SQL Server para PHP e WebMatrix.

Para obter informações sobre como utilizar o Azure PowerShell, consulte o artigo [como utilizar o PowerShell Azure][powershell-tools].

##<a name="azure-cli"></a>Clip Azure

O clip do Azure é um conjunto de comandos para implementar e gerir serviços Azure, como sites públicos do Azure e máquinas virtuais do Azure. Para obter informações sobre como instalar o Azure clip, consulte o artigo [instalar o clip do Azure](xplat-cli-install.md).

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
