<properties
    pageTitle="Como configurar ligações de VPN na gestão de API do Azure"
    description="Saiba como configurar uma ligação VPN na gestão de API do Azure e serviços web do access através do mesmo."
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="antonba"/>

# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Como configurar ligações de VPN na gestão de API do Azure

Suporte VPN da gestão de API permite-lhe ligar a sua API o Data Management gateway para uma rede Virtual Azure (clássico). Isto permite que os clientes de gestão de API para segura ligar aos serviços web back-end que estão no local ou caso contrário, estão inacessível à internet público.

>[AZURE.NOTE] Gestão de API Azure funciona com VNETs clássicas. Para obter informações sobre como criar um VNET clássico, consulte o artigo [criar uma rede virtual (clássica) utilizando o Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Para obter informações sobre como ligar VNETs clássicas processador VNETS, consulte o artigo [VNets clássico de para ligar para o novo VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="enable-vpn"> </a>Ligações VPN ativar

>Grupo análise só está disponível nas camadas **Premium** e **Programador** . Para mudar para a mesma, abra o seu serviço de gestão de API no [Portal clássica do Azure][] e, em seguida, abra o separador de **escala** . Na secção **Geral** , selecione a camada Premium e clique em Guardar.

Para ativar a conectividade de VPN, abra o serviço de gestão de API no [Portal clássica do Azure][] e mude para o separador de **Configurar** . 

Na secção de VPN, mude **ligação VPN** para **no**.

![Configurar o separador da instância de gestão de API][api-management-setup-vpn-configure]

Agora irá ver uma lista de todas as regiões onde o seu serviço de gestão de API está aprovisionado.

Selecione uma VPN e sub-rede para cada região. A lista de VPNs é povoada com base em redes virtuais disponíveis na sua subscrição do Azure que estão a região que está a configurar o programa de configuração.

![Selecione VPN][api-management-setup-vpn-select]

Clique em **Guardar** na parte inferior do ecrã. Não conseguir executar outras operações o serviço de gestão de API a partir do Portal de clássica Azure enquanto está a atualizar. O gateway serviço ão disponível e não devem ser afetadas runtime chamadas.

Note que o endereço VIP do gateway irá alterar cada vez VPN está ativado ou desativado.

## <a name="connect-vpn"> </a>Ligar a um serviço web atrás VPN

Depois do seu serviço de gestão de API estiver ligado a VPN, aceder a serviços web no interior da rede virtual é não diferente de aceder a serviços públicos. Basta escrever o endereço local ou o nome de anfitrião (se tiver sido configurado um servidor de DNS para a rede Virtual Azure) do seu serviço web para o campo de **URL do serviço Web** ao criar uma nova API ou editar uma existente.

![Adicionar API de VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Portas necessárias para suporte API gestão VPN

Quando uma instância do serviço de gestão de API está alojada num VNET, são utilizadas as portas na tabela seguinte. Se estas portas estão bloqueadas, o serviço poderá não funcionar corretamente. Está a ter um ou mais dos seguintes portas bloqueadas é o problema de configuração errada mais comuns ao utilizar a API gestão com um VNET.

| Porta (s)                      | Direção        | Protocolo de transporte | Objetivo                                                          | Origem / destino              |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443                      | Entrada          | TCP                | Comunicação para a gestão de API do cliente                           | INTERNET / VIRTUAL_NETWORK        |
| 80,443                       | Saída         | TCP                | Dependência de gestão de API de armazenamento Azure e Bus serviço Azure | VIRTUAL_NETWORK / INTERNET        |
| 1433                         | Saída         | TCP                | Dependências de gestão de API SQL                               | VIRTUAL_NETWORK / INTERNET        |
| 9350, 9351, 9352, 9353, 9354 | Saída         | TCP                | Dependências de gestão de API Bus de serviço                       | VIRTUAL_NETWORK / INTERNET        |
| 5671                         | Saída         | AMQP               | Dependência de gestão de API para o registo a política de concentrador de evento            | VIRTUAL_NETWORK / INTERNET        |
| 6381, 6382, 6383             | Entrada/saída | UDP RECOMENDADAS                | Dependências de gestão de API Redis Cache                       | VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445                          | Saída         | TCP                | Dependência de gestão de API de partilha de ficheiros Azure para GIT            | VIRTUAL_NETWORK / INTERNET        |

## <a name="custom-dns"> </a>o programa de configuração do custom DNS server

Gestão de API depende de um número de serviços Azure. Quando uma instância do serviço de gestão de API está alojada num VNET onde for utilizado um servidor DNS personalizado, necessita de ser conseguir resolver os nomes de anfitriões desses serviços Azure. Siga [estas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) orientações sobre a configuração DNS personalizada.  

## <a name="related-content"> </a>Relacionados com o conteúdo


* [Criar uma rede virtual com uma ligação VPN de site para o site através do Portal clássica Azure][]
* [Como utilizar o Inspector de API para rastreio chama-se de gestão de API do Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure Portal clássico]: https://manage.windowsazure.com/

[Criar uma rede virtual com uma ligação VPN de site para o site através do Portal clássica Azure]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Como utilizar o Inspector de API para rastreio chama-se de gestão de API do Azure]: api-management-howto-api-inspector.md
