<properties
    pageTitle=" Escala de transmissão pontos finais com o portal do Azure | Microsoft Azure"
    description="Neste tutorial orienta-o através dos passos de dimensionamento pontos finais da transmissão com o portal do Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
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


# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Escala de transmissão pontos finais com o portal do Azure

##<a name="overview"></a>Descrição geral

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Quando estiver a trabalhar com os serviços de multimédia do Azure um dos cenários mais comuns está a entregar vídeo através de velocidade ajustável transmissão os seus clientes. Dos serviços de multimédia suporta a velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas).

Dos serviços de multimédia fornece embalagem dinâmica que permite-lhe entregar a sua velocidade ajustável MP4 codificado conteúdo transmissão formatos suportados dos serviços de multimédia (MPEG travessão, HLS, transmissão suaves, HDS) just-in-time, sem ter de armazenar previamente embalados versões de cada um destes transmissão formatos.

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro mezzanine (origem) para um conjunto de ficheiros de velocidade ajustável MP4 (os passos de codificação são demonstrados mais tarde neste tutorial).  
- Crie, pelo menos, uma unidade de transmissão para o *ponto final de transmissão* a partir do qual planeia entrega o conteúdo. Os passos abaixo Mostrar como alterar o número de unidades transmissão.

Com embalagem dinâmica necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia irá criar e servir a resposta adequada baseada nos pedidos de um cliente.

Além disso, pode controlar a capacidade do serviço de transmissão ponto final para processar às necessidades de largura de banda crescentes ajustando transmissão unidades. Recomenda-se para atribuir uma ou mais unidades de escala de aplicações no ambiente de produção. Unidades de transmissão fornecerem-lhe ambas as capacidades de saída dedicada que podem ser comprados em incrementos de 200 Mbps e funcionalidades adicionais que a funcionalidade que inclui: [embalagem dinâmica](media-services-dynamic-packaging-overview.md), CDN integração e configuração avançada. Para mais informações, consulte o artigo [Gerir os pontos finais transmissão com o portal do Azure](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Escala de transmissão pontos finais

Para criar e alterar o número de unidades reservadas de transmissão, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/), selecione a sua conta dos serviços de multimédia do Azure.
2. Na janela **Definições** , selecione **os pontos finais de transmissão**.
3. Clique no ponto final transmissão ao qual pretende dimensionar. 
4. Mover o controlo de deslize para especificar o número de unidades em sequência
 
![Transmissão de ponto final](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

As seguintes considerações aplicam-se:

- A alocação de qualquer novas unidades transmissão pode demorar cerca de 20 minutos a concluir. 
- Atualmente, aceder a partir de qualquer valor positivo de transmissão de unidades voltar a nenhuma, pode desativar a pedido transmissão para até uma hora.
- O número máximo de unidades especificadas para o período de 24 horas é utilizado para calcular o custo. Para obter informações sobre preços detalhes, consulte o artigo [Detalhes de preços de serviços de multimédia](http://go.microsoft.com/fwlink/?LinkId=275107).

##<a name="next-steps"></a>Próximos passos

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


