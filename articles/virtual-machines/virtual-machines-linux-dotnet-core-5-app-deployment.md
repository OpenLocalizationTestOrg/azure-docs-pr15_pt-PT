<properties
   pageTitle="A automatização de implementação de aplicações com as extensões de Máquina Virtual | Microsoft Azure"
   description="Tutorial do Azure Máquina Virtual DotNet Core"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Implementação da aplicação com modelos de Gestor de recursos do Azure

Depois de todos os requisitos de infra-estruturas Azure tem sido identificados e traduzidos um modelo de implementação, a implementação de aplicação propriamente dita tem de ser resolvidos. Implementação da aplicação aqui é que fazem referência a instalar os binários da aplicação real para recursos Azure. Para a amostra de arquivo de música, .net Core, NGINX e autoridade têm de ser instalado e configurado em cada máquina virtual. Os binários loja de música precisa de estar instalado para a máquina virtual e a base de dados do arquivo de música previamente criado.

Este documento apresenta detalhes sobre como as extensões de Máquina Virtual podem automatizar implementação da aplicação e configuração para máquinas virtuais Azure. Todas as dependências e exclusivas configurações estão realçadas. Para obter a melhor experiência, previamente implemente uma instância da solução a sua subscrição do Azure e funcionam juntamente com o modelo de Gestor de recursos do Azure. O modelo concluído pode ser encontrado aqui – [Implementação de arquivo de música no Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Script de configuração

Extensões de máquina virtual são especializados programas que executam contra máquinas virtuais para fornecer a automatização de configuração. Extensões estão disponíveis para muitos fins específicos como anti-virus, configuração do registo e configuração Docker. Uma extensão de scripts personalizados pode ser utilizada para executar qualquer script contra uma máquina virtual. Com a amostra de arquivo de música, é até a extensão de scripts personalizados para configurar as máquinas virtuais Ubuntu e instale a aplicação da loja de música.

Antes de com detalhes sobre como as extensões de máquina virtual são declaradas num modelo de Gestor de recursos do Azure, examine o script que é executado. Este script configura o Ubuntu virtual machine para alojar a aplicação de arquivo de música. Quando executar, o script instala todos necessário software, instalar a aplicação de arquivo de música a partir de controlo de origem e preparar a base de dados. 

Para saber mais sobre o alojamento um .net principais aplicação no Linux, consulte [publicar no ambiente de produção Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html). 

> Este exemplo é para fins de demonstração.

```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Extensão de Script VM

Extensões VM podem ser executadas contra uma máquina virtual ao tempo de compilação incluindo o recurso de extensão no modelo de Gestor de recursos do Azure. Pode ser adicionada a extensão com o assistente Visual Studio adicionar recursos ou inserindo JSON válido para o modelo. O recurso de Script extensão estiver aninhado no interior do recurso Máquina Virtual; Isto pode ser visto no seguinte exemplo.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [VM Script extensão](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Repare na abaixo JSON que o script está armazenado no GitHub. Este script também pode ser armazenado numa armazenamento de Blobs do Azure. Além disso, os livros de Gestor de recursos do Azure permitem a cadeia de execução de script para construídos que os valores de parâmetros de modelo podem ser utilizados como parâmetros para execução de script. Neste caso os dados são fornecidos quando implementar os modelos e estes valores, em seguida, podem ser utilizadas quando executar o script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Para mais informações sobre como utilizar a extensão de scripts personalizados, consulte o artigo [extensões de script personalizado com modelos de Gestor de recursos](./virtual-machines-linux-extensions-customscript.md).

## <a name="next-step"></a>Passo seguinte

<hr>

[Explorar mais Azure modelos de Gestor de recursos](https://github.com/Azure/azure-quickstart-templates)
