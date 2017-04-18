<properties
   pageTitle="Criar uma Internet opostas Balanceador de carga no Gestor de recursos utilizando um modelo de | Microsoft Azure"
   description="Saiba como criar um opostas Balanceador de carga no Gestor de recursos utilizando um modelo da Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>Criar um opostas Balanceador de carga através de um modelo da Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação do Gestor de recursos. Também pode [aprender a criar uma Internet opostas Balanceador de carga utilizando o modelo de implementação clássico](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar o modelo utilizando o clique para implementar

O modelo de exemplo disponível no repositório de público utiliza um ficheiro de parâmetro que contenha a predefinição valores utilizados para gerar o cenário descrito acima. Para implementar este modelo utilizando o clique para implementar, siga [esta ligação](http://go.microsoft.com/fwlink/?LinkId=544801), clique em **implementar para Azure**, substitua os valores de parâmetro predefinido se for necessário e siga as instruções no portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar o modelo utilizando o PowerShell

Para implementar o modelo que transferiu utilizando o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../../articles/powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.

2. Execute o cmdlet **AzureRmResourceGroupDeployment novo** para criar um grupo de recursos utilizando o modelo.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar o modelo utilizando o clip do Azure

Para implementar o modelo utilizando o clip do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.
2. Execute o comando de **modo azure config** para mudar para modo de Gestor de recursos, conforme apresentado abaixo.

        azure config mode arm

    Eis o resultado esperado para o comando acima:

        info:    New mode is arm

3. A partir do seu browser, navegue para [O modelo de guia de introdução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), copie o conteúdo do ficheiro json e cole um novo ficheiro no seu computador. Para este cenário, teria de ser copiar os valores abaixo para um ficheiro com o nome **c:\lb\azuredeploy.parameters.json**.
4. Execute o cmdlet **criar de implementação do azure grupo** para implementar o novo Balanceador de carga utilizando os ficheiros de modelo e um parâmetro transferido e modificação acima. A lista apresentada depois da saída explica os parâmetros utilizados.

        azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)
