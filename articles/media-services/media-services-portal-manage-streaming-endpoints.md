<properties 
    pageTitle="Gerir os pontos finais da transmissão com o portal do Azure | Microsoft Azure" 
    description="Este tópico mostra como gerir os pontos finais da transmissão com o portal do Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016"
    ms.author="juliako"/>


#<a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gerir os pontos finais da transmissão com o portal do Azure

## <a name="overview"></a>Descrição geral

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Serviços de multimédia do Microsoft Azure, um **Ponto final de transmissão** representa um serviço de transmissão que pode entregar conteúdos diretamente para uma aplicação do leitor de cliente ou a rede de entrega um conteúdo (CDN) para distribuição ainda mais. Serviços de multimédia também fornecem integração simples com o Azure CDN. A sequência de saída de um serviço de StreamingEndpoint pode ser uma sequência live ou um vídeo a pedido activo na sua conta dos serviços de multimédia.

Além disso, pode controlar a capacidade do serviço de transmissão ponto final para processar às necessidades de largura de banda crescentes ajustando transmissão unidades. Recomenda-se para atribuir uma ou mais unidades de escala de aplicações no ambiente de produção. Unidades de transmissão fornecerem-lhe com capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidades adicionais, que inclui: [embalagem dinâmica](media-services-dynamic-packaging-overview.md), CDN integração e configuração avançada.

>[AZURE.NOTE]Apenas são faturada quando o seu ponto final de transmissão está a executar o estado.

Este tópico fornece uma descrição geral dos recursos funcionais principais que são fornecidos transmissão os pontos finais. O tópico também mostra como utilizar o portal do Azure para gerir os pontos finais transmissão. Para obter informações sobre como dimensionar o ponto final de transmissão, consulte o artigo [neste](media-services-portal-scale-streaming-endpoints.md) tópico.

## <a name="start-managing-streaming-endpoints"></a>Comece a gerir os pontos finais transmissão

Para começar a gerir os pontos finais transmissão para a sua conta, faça o seguinte.

1. No [portal do Azure](https://portal.azure.com/), selecione a sua conta dos serviços de multimédia do Azure.
2. Na janela **Definições** , selecione **os pontos finais de transmissão**.

    ![Transmissão de ponto final](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##<a name="adddelete-a-streaming-endpoint"></a>Adicionar/eliminar um ponto final de transmissão

Para adicionar/eliminar ponto final de transmissão através do portal Azure, faça o seguinte:

1. Para adicionar um ponto final de transmissão, clique no **+ ponto final** na parte superior da página. 
2. Para eliminar um ponto final de transmissão, prima o botão **Eliminar** . 

    A predefinição de transmissão de ponto final não pode ser eliminada.
2. Clique no botão **começar** para iniciar o ponto final de transmissão.

    ![Transmissão de ponto final](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Pode ter até dois pontos finais de transmissão por predefinição. Se precisar de pedir mais, consulte o artigo [Quotas e limitações](media-services-quotas-and-limitations.md).
    
##<a id="configure_streaming_endpoints"></a>Configurar o ponto final de transmissão

Transmissão de ponto final permite-lhe configurar as seguintes propriedades quando tem, pelo menos, 1 unidade de escala: 

- Controlo de acesso
- Controlo de cache
- Cruzada políticas de acesso de sites

Para obter informações detalhadas sobre estas propriedades, consulte o artigo [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Pode configurar o ponto final de transmissão efetuando o seguinte procedimento:

1. Selecione o ponto final de transmissão ao qual pretende configurar.
1. Clique em **Definições**.
  
Segue-se uma breve descrição dos campos.

![Transmissão de ponto final](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. Política de cache máximo: utilizados para configurar a duração da cache de activos servida através deste ponto final de transmissão. Se nenhum valor for definido, a predefinição é utilizada. Também podem ser definidos os valores predefinidos diretamente no Azure armazenamento. Se Azure CDN estiver ativado para o ponto final de transmissão, não deve definir o valor de política de cache para menos de 600 segundos.  

2. Permitido endereços IP: utilizado para especificar os endereços IP que seriam permitidos para ligar para o ponto final de transmissão publicado. Se não endereços IP especificado, qualquer endereço IP seria conseguir ligar. Endereços IP podem ser especificados como um único endereço IP (por exemplo, ' 10.0.0.1'), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com o endereço IP e uma máscara de pontilhado sub-rede decimal (por exemplo, ' 10.0.0.1(255.255.255.0)').

3. Configuração para a autenticação de cabeçalho de assinatura Akamai: utilizado para especificar como o pedido de autenticação de cabeçalho de assinatura do Akamai servidores está configurado. Expiração está em UTC.



##<a id="enable_cdn"></a>Ativar a integração do Azure CDN

Pode especificar para ativar a integração do Azure CDN para um transmissão ponto final de (-está desativado por predefinição.)

Para definir a integração do Azure CDN como verdadeiro:

- O ponto final de transmissão tem de ter, pelo menos, uma unidade de transmissão. Se mais tarde que pretende definir as unidades da escala como 0, primeiro tem de desativar a integração de CDN. Por predefinição quando cria um novo transmissão uma unidade transmissão de ponto final é definida automaticamente.

- O ponto final de transmissão tem de ser num estado parado. Assim que a CDN obtém ativada, pode iniciar o ponto final de transmissão. 

Pode demorar até 90 mínimo para a integração do Azure CDN obter ativadas.  Bastam até duas horas para que as alterações ser ativa todos os o POP de CDN.

Integração de CDN é activada no todos os centros de dados Azure: oeste-nos, Oriente-nos, Europa Norte, Europa Ocidental, Japão oeste, Japão Oriente, Sudeste asiático e Ásia.

Assim que estiver ativada, obtém desactivada a configuração do **Controlo de acesso** .

![Transmissão de ponto final](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] Azure dos serviços de multimédia integração com o Azure CDN é implementado no **Azure CDN a partir do Verizon**.  Se pretender utilizar **CDN Azure a partir do Akamai** para dos serviços de multimédia do Azure, tem de [configurar manualmente o ponto final](../cdn/cdn-create-new-endpoint.md).  Para mais informações sobre as funcionalidades de Azure CDN, consulte o artigo [Descrição geral CDN](../cdn/cdn-overview.md).

###<a name="additional-considerations"></a>Considerações adicionais

- Quando CDN é ativada para um ponto final de transmissão, clientes não é possível pedir conteúdo diretamente a partir da origem. Se precisar da capacidade de testar o seu conteúdo com ou sem CDN, pode criar outra transmissão ponto final de que não está CDN ativado.
- O nome do anfitrião transmissão ponto final se mantém o mesmo depois de activar CDN. Não precisa de efetuar as alterações ao seu fluxo de trabalho de serviços de multimédia depois de CDN está ativado. Por exemplo, se o nome do anfitrião ponto final transmissão strasbourg.streaming.mediaservices.windows.net, depois de activar CDN, é utilizado o nome do anfitrião mesmo exata.
- Para o novos pontos finais de transmissão, pode ativar a CDN simplesmente ao criar um novo ponto final; pontos finais de transmissão existentes, terá primeiro, pare o ponto final e, em seguida, ativar a CDN.
 

Para obter mais informações, consulte [dos serviços de multimédia do Azure anunciar o integração com o Azure CDN (rede de entrega de conteúdos)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##<a name="next-steps"></a>Próximos passos

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
