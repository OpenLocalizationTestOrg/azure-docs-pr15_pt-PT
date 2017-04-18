<properties
    pageTitle="Implementar luz numa máquina virtual Linux | Microsoft Azure"
    description="Saiba como instalar a pilha de luz numa VM Linux"
    services="virtual-machines-linux"
    documentationCenter="virtual-machines"
    authors="jluk"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="juluk"/>

# <a name="deploy-lamp-stack-on-azure"></a>Implementar a pilha de luz no Azure
Este artigo irá guiá-lo através de como implementar um servidor web, MySQL e PHP (na pilha de lâmpada) no Azure Apache. Terá uma conta Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e o [Clip do Azure](../xplat-cli-install.md) que está [ligado à sua conta Azure](../xplat-cli-connect.md).

Existem dois métodos para instalar luz abordado neste artigo:

## <a name="quick-command-summary"></a>Resumo dos comandos rápida

1) Implementar luz no novo VM

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Implementar luz no VM existente

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Implementar luz no novo VM instruções passo a passo

Pode começar por criar um novo [grupo de recursos](../azure-resource-manager/resource-group-overview.md) que irá conter a VM:

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Para criar a VM propriamente dito, pode utilizar um modelo de Gestor de recursos do Azure já escrito encontrado [aqui no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Deverá visualizar uma resposta perguntar algumas entradas mais:

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Agora ter criado uma VM Linux com luz já instalada no mesmo. Se pretender, pode verificar a instalação por saltar para baixo até [verificar luz instalado com êxito].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Implementar luz no tutorial de VM existente

Se precisar de ajuda para criar uma VM Linux pode head [aqui para saber como criar uma VM Linux] (. / virtual-machines-linux-quick-create-cli.md). Em seguida, terá SSH para a VM Linux. Se precisar de ajuda com a criação de uma chave SSH pode head [aqui para saber como criar uma chave SSH no Linux/Mac] (. / virtual-machines-linux-mac-create-ssh-keys.md).
Se já tiver uma tecla SSH, vá para a frente e SSH para o seu VM Linux com `ssh username@uniqueDNS`.

Agora que está a trabalhar no seu VM Linux, podemos irá guiá-lo através da instalação a pilha de luz em distribuições com base em Debian. Os comandos exatos podem ser diferentes para outros distros Linux.

#### <a name="installing-on-debianubuntu"></a>Instalar num Debian/Ubuntu

Terá de pacotes seguintes instalados: `apache2`, `mysql-server`, `php5`, e `php5-mysql`. Pode instalar estes diretamente capturando estes pacotes ou utilizando Tasksel. Instruções para ambas as opções estão indicadas abaixo.
Antes de instalar terá de transferir e atualizar listas de pacote.

    user@ubuntu$ sudo apt-get update
    
##### <a name="individual-packages"></a>Pacotes individuais
Utilizar o possam obter:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Utilizar Tasksel
Em alternativa pode transferir Tasksel, uma ferramenta de Debian/Ubuntu que instala múltiplos pacotes relacionados como coordenada "tarefa" para o seu sistema.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Depois de executar a qualquer uma das opções acima vai ser-lhe instalar estes pacotes e um número de outras dependências. Prima 'y' e, em seguida, Enter para continuar e siga quaisquer outros pedidos para definir uma palavra-passe administrativa para MySQL. Isto irá instalar as extensões PHP necessárias mínimas necessárias para utilizar PHP com MySQL. 

![][1]

Execute o seguinte comando para ver outras extensões PHP que estão disponíveis como pacotes:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Criar documento info.php

Agora deverá conseguir verificar que versão do Apache, MySQL e PHP tiver através da linha de comandos ao escrever `apache2 -v`, `mysql -v`, ou `php -v`.

Se pretender para testar adicional, pode criar uma página de informações PHP rápida para visualizar num browser. Crie um novo ficheiro com Nano editor de texto com este comando:

    user@ubuntu$ sudo nano /var/www/html/info.php

No editor de texto do GNU Nano, adicione as linhas seguintes:

    <?php
    phpinfo();
    ?>

Em seguida, guardar e sair do editor de texto.

Reinicie Apache com este comando, para que todas as instalações do novas entrarão em vigor.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Certifique-se de luz instalado com êxito

Agora pode verificar a página de informações da PHP que acabou de criar no seu browser ao aceder a http://youruniqueDNS/info.php,-deverá ter o aspeto semelhante ao seguinte.

![][2]

Pode verificar a instalação do Apache ao ver a página predefinida do Apache2 Ubuntu acedendo a http://youruniqueDNS/. Deverá ver algo parecido com.

![][3]

Parabéns, tiver o programa de configuração apenas uma pilha de luz no seu VM Azure!

## <a name="next-steps"></a>Próximos passos

Consulte a documentação Ubuntu na pilha de luz:

- [https://help.ubuntu.com/Community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png
