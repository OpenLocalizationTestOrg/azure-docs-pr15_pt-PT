<properties
    pageTitle="Codificar um ativo utilizar Media Encoder padrão com o portal do Azure | Microsoft Azure"
    description="Neste tutorial explica os passos de codificação de um ativo utilizar Media Encoder padrão com o portal do Azure."
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


# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Codificar um ativo utilizar Media Encoder padrão com o portal do Azure

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Quando estiver a trabalhar com os serviços de multimédia do Azure um dos cenários mais comuns está a entregar velocidade ajustável transmissão os seus clientes. Dos serviços de multimédia suporta a velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas). Para preparar os seus vídeos para transmissão de velocidade ajustável, tem de codificar o vídeo de origem em ficheiros de velocidade multi. Deve utilizar o codificador **Media Encoder padrão** para codificar os seus vídeos.  

Os serviços de multimédia também fornecem embalagem dinâmica que permite-lhe entregar a sua velocidade multi MP4s nos seguintes formatos de transmissão: MPEG travessão, HLS, transmissão suaves ou HDS, sem ter de voltar Compactar para estes formatos de transmissão. Com embalagem dinâmica necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia irá criar e servir a resposta adequada baseada nos pedidos de um cliente.

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro de origem para o conjunto de velocidade multi MP4 ficheiros (os passos de codificação demonstrados mais tarde nesta secção).
- Obter, pelo menos, uma unidade de transmissão para o ponto final de transmissão a partir do qual planeia entrega o conteúdo. Para mais informações, consulte o artigo [configurar os pontos finais transmissão](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

Para dimensionar o processamento de multimédia, consulte o artigo [neste](media-services-portal-scale-media-processing.md) tópico.

## <a name="encode-with-the-azure-portal"></a>Codificar com o portal do Azure

Esta secção descreve os passos que pode tomar para codificar o seu conteúdo com o padrão de descodificador de multimédia.

1.  No [portal do Azure](https://portal.azure.com/), selecione a sua conta dos serviços de multimédia do Azure.
2.  Na janela **Definições** , selecione **recursos**.  
2.  Na janela de **activos** , selecione os elementos que pretender para codificar.
3.  Prima o botão **Codificar** .
4.  Na janela do **codificar um ativo** , selecione o processador "Media Encoder padrão" e uma configuração predefinida. Por exemplo, se souber o vídeo introdução tem uma resolução de 1920x1080 pixels, em seguida, pode utilizar o "H264 velocidade vários 1080p" predefinido. Para mais informações sobre predefinições, consulte [Este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artigo – é importante selecionar o predefinido que é o mais adequado para a sua entrada vídeo. Se tiver um vídeo de baixa resolução (640x360), em seguida, deverá não estar a utilizar a predefinição "H264 velocidade vários 1080p" predefinido.
    
    Para facilitar a gestão, tem uma opção de edição no nome do elemento de saída e o nome da tarefa.
        
    ![Codificar activos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Prima **Criar**.


##<a name="next-step"></a>Passo seguinte

Pode monitorizar o progresso da tarefa codificação com o portal Azure, conforme descrito [neste](media-services-portal-check-job-progress.md) artigo.  

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


