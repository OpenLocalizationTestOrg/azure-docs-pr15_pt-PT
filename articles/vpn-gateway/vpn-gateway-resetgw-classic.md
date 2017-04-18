<properties
   pageTitle="Repor um Gateway Azure VPN | Microsoft Azure"
   description="Este artigo explica o repor o Gateway da VPN Azure. O artigo aplica-se ao gateways VPN o clássico, tanto os modelos de implementação do Gestor de recursos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Repor um Gateway de VPN Azure através do PowerShell


Este artigo explica o repor o Gateway da VPN Azure utilizando os cmdlets do PowerShell. Estas instruções incluem o modelo clássico de implementação e o modelo de implementação do Gestor de recursos.

Repor o gateway Azure VPN é útil se perder a conectividade de VPN cruzada local num ou mais túneis S2S VPN. Esta situação, os dispositivos de VPN no local estão todos a funcionar corretamente, mas não forem capazes de estabelecer túneis IPsec com os gateways de Azure VPN. 

Cada gateway Azure VPN é composto por duas VM instâncias em execução numa configuração suspensão ativo. Quando utilizar o cmdlet do PowerShell para repor o gateway, reinicia o gateway e, em seguida, reaplica as configurações de publicação em local ao mesmo. O gateway mantém o endereço IP público que já tem. Isto significa que não é necessário atualizar a configuração do router VPN com um novo endereço IP público para o gateway Azure VPN.  

Assim que o comando é emitido, a instância atual ativa do gateway Azure VPN é reiniciada imediatamente. Haverá uma breve intervalo: controla durante a mudança da instância ativa (a ser reiniciado), para a instância em espera. O intervalo deve ser inferior a um minuto.

Se a ligação não é restaurada após o primeiro reinício, emita o mesmo comando novamente para reiniciar a instância VM segunda (o gateway ativo novo). Se o ser duas reiniciado forem pedido novamente para regressar, haverá um período ligeiramente maior onde estão a ser reiniciadas ambas as instâncias VM (ativas e espera). Isto irá provocar um intervalo mais longo depende da conectividade VPN, até 2 a 4 minutos para VMs concluir o ser reiniciado.

Depois de ser duas reiniciado, se ainda estiver a ter problemas de conectividade de publicação em local, abra um pedido de suporte a partir do portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Antes de repor o seu gateway, verifique se os itens de chaves listados abaixo para cada túnel de VPN IPsec Site-para-Site (S2S). Qualquer erro de correspondência na itens irá resultar em desligar de túneis S2S VPN. Verificar e corrigir as configurações para o seu no local e gateways Azure VPN poupa-lhe a partir de ser reiniciado desnecessários e interrupções para outras ligações úteis os gateways.

Verifique os seguintes itens antes de repor o seu gateway:

- Os endereços de IP de Internet (VIP) para o gateway Azure VPN e o gateway VPN no local estão configurados corretamente no Azure e as políticas VPN no local.
- A chave previamente partilhada tem de ser igual em gateways VPN do Azure e no local.
- Se aplicar configuração IPsec/IKE específica, tal como encriptação, hashing algoritmos e PFS (perfeita reencaminhar segredo), certifique-se que os o Azure no local VPN gateways e tem as configurações do mesmo.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Repor um Gateway VPN utilizando o modelo de implementação de gestão de recursos

O cmdlet do Gestor de recursos do PowerShell para repor o gateway está `Reset-AzureRmVirtualNetworkGateway`. O exemplo seguinte repõe Azure VPN gateway, "VNet1GW", no grupo de recursos "TestRG1".

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Repor um Gateway VPN utilizando o modelo clássico de implementação

É o cmdlet do PowerShell para repor o gateway Azure VPN `Reset-AzureVNetGateway`. O exemplo seguinte repõe o gateway Azure VPN para a rede virtual denominado "ContosoVNet".
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Resultado:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Próximos passos
    
Consulte a [referência do cmdlet do PowerShell gestão de serviços](https://msdn.microsoft.com/library/azure/mt617104.aspx) e a [referência do cmdlet do PowerShell o Gestor de recursos](http://go.microsoft.com/fwlink/?LinkId=828732) para obter mais informações.






