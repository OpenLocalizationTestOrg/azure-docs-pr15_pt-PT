<properties
   pageTitle="Suporte de Gateway WebSocket aplicações | Microsoft Azure"
   description="Esta página fornece uma descrição geral do suporte do WebSocket de Gateway de aplicação."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-websocket-support"></a>Suporte de Gateway WebSocket de aplicações

Aplicação Gateway fornece suporte nativo para WebSocket através de todos os tamanhos de gateway. Não existe nenhuma definição para seletivamente ativar ou desativar o suporte de WebSocket configuráveis pelo utilizador. Pode continuar a utilizar um padrão HTTPListener na porta 80/443 para receber WebSocket tráfego. Tráfego de WebSocket, em seguida, é direcionado para o servidor de back-end activados WebSocket utilizando o agrupamento de back-end adequado conforme especificado no regras de aplicação de gateway. Protocolo de WebSocket padronizado na [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação em full duplex entre servidor e cliente através de uma ligação de TCP longa. Esta funcionalidade permite para uma comunicação mais interativa entre o servidor web e o cliente, o que pode ser bidireccional sem necessidade de inquéritos como necessários no implementações baseado em HTTP.  WebSocket baixa tem sobrecarga ao contrário de HTTP e pode reutilizar a mesma ligação TCP para vários/respostas a pedidos que resulta numa utilização mais eficaz dos recursos. WebSocket protocolos foram concebidos para trabalhar sobre portas HTTP tradicionais 80 e 443.

O servidor de back-end tem responder a sondas de gateway de aplicação, são descritas na secção [Descrição geral do Estado de funcionamento sonda](application-gateway-probe-overview.md) . Aplicação gateway do Estado de funcionamento sondas só estão HTTP/HTTPS, isto significa que todos os servidores de back-end tem de responder a HTTP sondas para o gateway de aplicação encaminhar o tráfego de WebSocket no servidor.

## <a name="listener-configuration-element"></a>Elemento de configuração de escuta

HTTPListener existente pode ser utilizado para suportar WebSocket. Segue-se um fragmento de elemento HttpListeners a partir de um ficheiro de modelo de exemplo. Seria necessário listeners HTTP e HTTPS para suportar WebSocket e proteger o tráfego de WebSocket. Da mesma forma pode utilizar o [portal](application-gateway-create-gateway-portal.md) ou [PowerShell](application-gateway-create-gateway-arm.md) para criar um gateway aplicação com listeners na porta 80/443 para suportar o tráfego de WebSocket.


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuração da regra BackendAddressPool, BackendHttpSetting e encaminhamento

BackendAddressPool deve ser utilizada para definir um agrupamento de back-end com servidores WebSocket ativado. BackendHttpSetting deve ser definido com back-end portas 80/443 apenas. Propriedades para afinidade com base em cookies e requestTimeouts não são relevantes para o tráfego de WebSocket. Não existe nenhuma alteração exigida regra de encaminhamento. Regra de encaminhamento 'Básicas' devem continuar a ser utilizado para associam a escuta adequada para o conjunto de endereços de back-end correspondente. 

    "requestRoutingRules": [{
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }, {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }

        }
    }]

## <a name="websocket-enabled-backend"></a>Back-end de WebSocket activado

O back-end tem de ter um servidor de web HTTP/HTTPS a executar o configurado no porta (normalmente 80/443) para WebSocket trabalhar. Este requisito é porque o protocolo WebSocket requer o handshake inicial estar HTTP com atualização para o protocolo WebSocket como um campo de cabeçalho.

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

Outro motivo para isto é que essa sonda de estado de funcionamento de back-end de gateway aplicação suporta apenas os protocolos HTTP/HTTPS. Se o servidor de back-end não responder à HTTP/HTTPS sondas, tomar fora do conjunto de dados back-end e não pedidos incluindo os pedidos de WebSocket, chegar a esta back-end.

## <a name="next-steps"></a>Próximos passos

Depois de formação sobre o suporte de WebSocket, aceda a [criar um gateway de aplicação](application-gateway-create-gateway.md) para começar com uma aplicação web do WebSocket ativado.
