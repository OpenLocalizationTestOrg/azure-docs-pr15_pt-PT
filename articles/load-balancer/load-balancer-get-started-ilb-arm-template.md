<properties
   pageTitle="Criar um balanceador de carga interno utilizando um modelo no Gestor de recursos | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno utilizando um modelo no Gestor de recursos"
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

# <a name="create-an-internal-load-balancer-using-a-template"></a>Criar um balanceador de carga interno através de um modelo

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementação clássica](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar o modelo utilizando o clique para implementar

O modelo de exemplo disponível no repositório de público utiliza um ficheiro de parâmetro que contenha a predefinição valores utilizados para gerar o cenário descrito acima. Para implementar este modelo utilizando o clique para implementar, siga [esta ligação](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), clique em **implementar para Azure**, substitua os valores de parâmetro predefinido se for necessário e siga as instruções no portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar o modelo utilizando o PowerShell

Para implementar o modelo que transferiu utilizando o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../../articles/powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.
2. Transferir o ficheiro de parâmetros para o disco local.
3. Editar o ficheiro e guarde-o.
4. Execute o cmdlet **AzureRmResourceGroupDeployment novo** para criar um grupo de recursos utilizando o modelo.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar o modelo utilizando o clip do Azure

Para implementar o modelo utilizando o clip do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.
2. Execute o comando de **modo azure config** para mudar para modo de Gestor de recursos, conforme apresentado abaixo.

        azure config mode arm

    Eis o resultado esperado para o comando acima:

        info:    New mode is arm

3. Abra o ficheiro de parâmetro, selecione os seus conteúdos e guardá-lo para um ficheiro no seu computador. Neste exemplo, vamos guardou o ficheiro de parâmetros *parameters.json*.

4. Execute o comando **criar de implementação do azure grupo** para implementar o novo Balanceador de carga interno utilizando os ficheiros de modelo e um parâmetro transferido e modificação acima. A lista apresentada depois da saída explica os parâmetros utilizados.

        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Próximos passos

[Configurar um modo de distribuição do Balanceador de carga utilizando afinidade IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)



