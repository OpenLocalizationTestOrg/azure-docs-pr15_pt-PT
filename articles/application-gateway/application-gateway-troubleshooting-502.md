<properties
   pageTitle="Resolver problemas de erros de aplicação Gateway incorretas Gateway (502) | Microsoft Azure"
   description="Saiba como resolver problemas de erros de aplicação Gateway 502"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Resolução de erros de gateway inválido no Gateway de aplicação

## <a name="overview"></a>Descrição geral

Depois de configurar um Gateway de aplicação do Azure, um dos erros que poderão encontrar utilizadores é "erro no servidor: 502 - servidor Web recebeu uma resposta inválida enquanto age como um servidor de gateway ou proxy". Isto pode ocorrer devido ao principais seguintes razões:

- Agrupamento de back-end Azure Gateway de aplicação não está configurado ou vazio.
- Nenhum dos VMs ou instâncias num conjunto de escala VM estão em bom estado.
- VMs ou instâncias do conjunto de escala VM de back-end não estão a responder à sonda de estado de funcionamento de predefinido.
- Configuração inválida ou incorreta do Estado de funcionamento personalizado sondas.
- Solicitar tempo limite ou problemas de conectividade com pedidos de utilizador.

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazia

### <a name="cause"></a>Causa

Se o Gateway aplicação não inclui VMs nem VM escala definida configurado no conjunto de endereços de back-end, não é possível encaminhar qualquer pedido de cliente e inicia um erro de gateway inválido.

### <a name="solution"></a>Solução

Certifique-se de que o conjunto de endereços de back-end não está vazio. Isto pode ser feito quer através do PowerShell, clip ou portal.

    Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

O resultado do cmdlet anterior deve conter o conjunto de endereços de back-end não está vazio. Segue-se um exemplo onde o dois conjuntos de dados são devolvidos que estão configurados com FQDN ou endereços IP para back-end VMs. O estado de aprovisionamento do BackendAddressPool deve ser 'foi concluída com êxito'.
    
    BackendAddressPoolsText: 
            [{
                "BackendAddresses": [{
                    "ipAddress": "10.0.0.10",
                    "ipAddress": "10.0.0.11"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool01",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
            }, {
                "BackendAddresses": [{
                    "Fqdn": "xyx.cloudapp.net",
                    "Fqdn": "abc.cloudapp.net"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool02",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
            }]


## <a name="unhealthy-instances-in-backendaddresspool"></a>Instâncias danificadas BackendAddressPool

### <a name="cause"></a>Causa

Se todas as instâncias do BackendAddressPool estão danificadas, em seguida, Gateway de aplicação não teria de qualquer back-end a pedido do utilizador encaminhar para. Isto também é possível as maiúsculas/minúsculas quando estão em bom estadas instâncias de back-end, mas não possui a aplicação necessária implementada.

### <a name="solution"></a>Solução

Certifique-se de que as instâncias são saudáveis e a aplicação está configurada corretamente. Verifique se as instâncias de back-end conseguem responder a um ping a partir de outro VM no VNet mesmo. Se configurado com um ponto final público, certifique-se de que está serviceable um pedido de browser para a aplicação web.

## <a name="problems-with-default-health-probe"></a>Problemas com a sonda de estado de funcionamento predefinido

### <a name="cause"></a>Causa

502 erros também podem ser frequentes indicadores que a sonda de estado de funcionamento predefinido não é possível alcançar VMs back-end. Quando uma instância de Gateway aplicação está aprovisionada, configura automaticamente uma sonda de estado de funcionamento predefinido para cada BackendAddressPool utilizando as propriedades do BackendHttpSetting. Nenhuma entrada de utilizador é necessário para definir este sonda. Especificamente, quando uma regra de balanceamento de carga está configurada, uma associação é tornada entre um BackendHttpSetting e BackendAddressPool. Uma sonda predefinido está configurada para cada um dos seguintes associações e Gateway aplicação inicia uma ligação de verificação do Estado de funcionamento periódicos para cada instância no BackendAddressPool porto especificado no elemento BackendHttpSetting. Tabela seguinte lista os valores associados a sonda de estado de funcionamento predefinido.


|Propriedade sonda | Valor | Descrição|
|---|---|---|
| Sonda de URL| http://127.0.0.1/ | Caminho de URL |
| Intervalo | 30 | Sonda intervalo em segundos |
| Limite de tempo  | 30 | Tempo limite de sonda em segundos |
| Limiar danificado | 3 | Sonda Repetir contagem. O servidor de back-end está marcado para baixo após a contagem de falhas consecutivas sonda atinge o limiar de danificado. |

### <a name="solution"></a>Solução

- Certifique-se de que um site predefinido está configurado e de que está a escutar na 127.0.0.1.
- Se BackendHttpSetting Especifica uma porta que não seja 80, o site predefinido deve ser configurado para ouvir esse porto.
- A chamada para http://127.0.0.1:port deve devolver um código de resultado HTTP de 200. Isto deve ser devolvido dentro do período de tempo limite de 30 seg.
- Certifique-se de que a porta configurada está aberta e que não existem regras de firewall ou grupos de segurança do Azure rede, que bloquear o tráfego recebido ou envio na porta configurado.
- Se for utilizado com o FQDN ou o endereço IP público Azure VMs clássicos ou serviço na nuvem, certifique-se de que o [ponto final](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) correspondente é aberta.
- Se a VM está configurada através do Gestor de recursos do Azure e fora da VNet onde é implementado Gateway de aplicação, [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) tem de ser configurado para permitir o acesso a porta pretendida.


## <a name="problems-with-custom-health-probe"></a>Problemas com a sonda de estado de funcionamento personalizado

### <a name="cause"></a>Causa

Estado de funcionamento personalizado sondas permitem flexibilidade adicional para a pesquisar o comportamento predefinido. Ao utilizar sondas personalizadas, os utilizadores podem configurar o intervalo de sonda, o URL e caminho para testar e quantas respostas falhadas para aceitar antes de marcar a instância do conjunto de dados back-end como danificado. As seguintes propriedades adicionais são adicionadas.

|Propriedade sonda| Descrição|
|---|---|
| Nome | Nome da sonda. Este nome é utilizado para fazer referência a sonda nas definições de HTTP back-end. |
| Protocolo | Protocolo utilizado para enviar a sonda. A sonda irá utilizar o protocolo definido nas definições de HTTP back-end |
| Anfitrião |  Para enviar a sonda de nome do anfitrião. Aplicável apenas quando o site com várias está configurado no Gateway de aplicação. Este é diferente do nome do anfitrião VM.  |
| Caminho | Caminho relativo da sonda. O caminho válido começa a partir de '/'. A sonda é enviada para \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\> |
| Intervalo | Sonda intervalo em segundos. Este é o intervalo de tempo entre duas sondas consecutivas.|
| Limite de tempo | Sonda de limite de tempo em segundos. A sonda está marcada como falhou se não for recebida uma resposta válida durante este período de tempo limite. |
| Limiar danificado | Sonda Repetir contagem. O servidor de back-end está marcado para baixo após a contagem de falhas consecutivas sonda atinge o limiar de danificado. |


### <a name="solution"></a>Solução

Valide que o estado de funcionamento de personalizada sonda está configurado corretamente como sendo a tabela anterior. Para além dos passos de resolução de problemas anteriores, certifique-se também o seguinte procedimento:

- Certifique-se de que a sonda está corretamente especificada pelo [Guia](application-gateway-create-probe-ps.md).
- Se estiver configurado Gateway de aplicação para um único local, por predefinição anfitrião do nome deverá ser especificado como '127.0.0.1', a menos que caso contrário, é configurado no sonda personalizada.
- Certifique-se de que uma chamada para http://\<anfitrião\>:\<porta\>\<caminho\> devolve um código de resultado HTTP de 200.
- Certifique-se de que intervalo, tempo limite e UnhealtyThreshold estão dentro dos intervalos aceitáveis.

## <a name="request-time-out"></a>Pedido de tempo limite

### <a name="cause"></a>Causa

Quando um pedido de utilizador é recebido, o Gateway aplicação aplica-se as regras configuradas para o pedido e encaminha-o para uma instância do conjunto de dados back-end. Aguarda para um intervalo de tempo de uma resposta da instância de back-end configurável. Por predefinição, este intervalo é **30 segundos**. Se o Gateway de aplicação não recebe uma resposta da aplicação de back-end neste intervalo, um pedido de utilizador seria vir um erro 502.

### <a name="solution"></a>Solução

Gateway de aplicação permite aos utilizadores configurar esta definição através do BackendHttpSetting, que pode ser aplicada em seguida, para diferentes conjuntos de dados. Podem ter diferentes conjuntos de dados back-end BackendHttpSetting diferente e configurado o pedido, por conseguinte, diferente tempo limite.

    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## <a name="next-steps"></a>Próximos passos

Se os passos anteriores não resolver o problema, abra uma [bilhetes de suporte](https://azure.microsoft.com/support/options/).
