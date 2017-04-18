<properties 
    pageTitle="Como efetuar a transmissão em direto com codificadores no local através do portal Azure | Microsoft Azure" 
    description="Neste tutorial explica os passos da criação de um canal que está configurado para entrega de pass-through." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>Como efetuar live transmissão com codificadores no local através do portal Azure

> [AZURE.SELECTOR]
- [Portal]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [RESTO]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

Neste tutorial orienta-o através dos passos de através do portal Azure para criar um **canal** que está configurado para entrega de pass-through. 

##<a name="prerequisites"></a>Pré-requisitos

A seguinte é necessários para completar a Iniciação:

- Uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos serviços de multimédia. Para criar uma conta dos serviços de multimédia, consulte o artigo [como criar uma conta de serviços de multimédia](media-services-portal-create-account.md).
- Uma câmara Web. Por exemplo, [Telestream Wirecast codificador](http://www.telestream.net/wirecast/overview.htm).

É altamente recomendável para rever os seguintes artigos:

- [Serviços de multimédia do Microsoft Azure RTMP de suporte e Live codificadores](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Descrição geral do Live vapor utilizando os serviços de multimédia do Azure](media-services-manage-channels-overview.md)
- [Live transmissão com codificadores no local que criar sequências de velocidade multi](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Cenário de transmissão direto comum

Os passos seguintes descrevem tarefas envolvidas na criação de aplicações de transmissão direto comuns que utilizam os canais que estão configurados para entrega de pass-through. Este tutorial mostra como criar e gerir um canal pass-through e eventos ao vivo.

1. Liga uma câmara de vídeo num computador. Iniciação e configurar um codificador direto no local que exporta uma sequência RTMP ou fragmentado MP4 multi velocidade. Para mais informações, consulte o artigo [suporte de RTMP de serviços de multimédia do Azure e codificadores Live](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Este passo também pode ser realizado depois de criar o seu canal.

1. Criar e comece a um canal pass-through.
1. Obter o canal ingerir esta última URL. 

    O URL de ingest é utilizado pelo codificador direto para enviar a sequência de para o canal.
1. Obter o URL de pré-visualização do canal. 

    Utilize este URL para verificar o seu canal corretamente está a receber o fluxo direto.

3. Crie um evento/programa direto. 

    Ao utilizar o portal do Azure, criar um evento direto também cria um ativo. 
      
    >[AZURE.NOTE]Certifique-se de ter, pelo menos, uma transmissão unidade reservada no ponto final transmissão a partir do qual pretende em sequência de conteúdo.
1. Inicie o evento/programa quando estiver pronto para começar a transmissão e arquivo.
2. Opcionalmente, o codificador live pode serão avisados de que para iniciar um anúncio. Anúncio de é inserido na sequência de saída.
1. Pare o evento/programa sempre que pretender parar streaming e arquivo o evento.
1. Eliminar o evento/programa (e, opcionalmente, eliminar o activo).     

>[AZURE.IMPORTANT] Reveja a [transmissão em direto com codificadores no local que criar sequências de velocidade multi](media-services-live-streaming-with-onprem-encoders.md) para saber mais sobre os conceitos e considerações relacionados com a transmissão em direto com codificadores no local e pass-through canais.

##<a name="to-view-notifications-and-errors"></a>Para ver as notificações e erros

Se pretender ver notificações e erros produzidos pelo portal do Azure, clique no ícone de notificação.

![Notificações](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##<a name="configure-streaming-endpoints"></a>Configurar pontos finais de transmissão 

Dos serviços de multimédia fornece embalagem dinâmica, que permite-lhe entregar a sua velocidade multi MP4s nos seguintes formatos de transmissão: MPEG travessão, HLS, transmissão suaves ou HDS, sem ter de voltar a compactar para estes formatos de transmissão. Com embalagem dinâmica necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia constrói e serve a resposta adequada baseada nos pedidos de um cliente.

Para tirar partido da embalagem dinâmico, o que precisa para obter, pelo menos, uma unidade de transmissão para o ponto final de transmissão a partir do qual planeia entrega o conteúdo.  

Para criar e alterar o número de unidades reservadas de transmissão, faça o seguinte:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Na janela **Definições** , clique em **pontos finais de transmissão**. 

2. Clique na predefinição transmissão ponto final. 

    A janela de **Detalhes de ponto final de transmissão de predefinido** é apresentada.

3. Para especificar o número de unidades transmissão, controlo de deslize **transmissão unidades** .

    ![Transmissão de unidades](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. Clique no botão **Guardar** para guardar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode demorar até 20 minutos a concluir.
    
##<a name="create-and-start-pass-through-channels-and-events"></a>Criar e iniciar pass-through canais e eventos

Um canal está associado a eventos/programas permite-lhe controlar a publicação e armazenamento dos segmentos numa sequência direto. Canais gerir eventos. 
    
Pode especificar o número de horas que pretende manter o conteúdo gravado para o programa ao definir o comprimento da **Janela de arquivo** . Este valor pode ser definido a partir de um mínimo de 5 minutos para um máximo de 25 horas. Comprimento da janela de arquivo dita também que a quantidade máxima de clientes de tempo pode atingir trás no tempo da posição atual direto. Podem executar eventos sobre a quantidade de tempo especificada, mas conteúdo determinado atrás o comprimento de janela continuamente é eliminado. Este valor desta propriedade também determina quanto tempo podem aumentar os manifestos de cliente.

Cada evento está associado um ativo. Para publicar o evento, terá de criar um locator pedido para o ativo associado. Está a ter este locator permite-lhe construir um URL de transmissão pode fornecer aos seus clientes.

Um canal suporta até três eventos em simultâneo em execução, para que possa criar múltiplas arquivos do mesmo fluxo recebido. Esta opção permite-lhe publicar e arquivar diferentes partes do evento, conforme necessário. Por exemplo, a necessidade de negócio é para arquivar 6 horas de um programa, mas difundir apenas últimos 10 minutos. Para realizar esta tarefa, é necessário criar duas programas em simultâneo em execução. Um programa está definido para arquivar 6 horas do evento, mas o programa não está a ser publicado. O outro programa está definido para arquivar para 10 minutos e este programa é publicado.

Não deve reutilizar eventos ao vivo existentes. Em vez disso, criar e iniciar um novo evento para cada evento.

Inicie o evento quando estiver pronto para começar a transmissão e arquivo. Pare o programa sempre que pretender parar streaming e arquivo o evento. 

Para eliminar conteúdo arquivado, pare e eliminar o evento e, em seguida, eliminar o activo associado. Não pode ser eliminado um ativo se for utilizado por um evento; o evento deve ser eliminado pela primeira vez. 

Mesmo depois de parar e eliminar o evento, os utilizadores seria conseguir transmitir em fluxo o conteúdo arquivado como um vídeo a pedido, para desde que não está a eliminar elemento.

Se pretender manter o conteúdo arquivado, mas não a tem disponível para transmissão, elimine o localizador de transmissão.

###<a name="to-use-the-portal-to-create-a-channel"></a>Utilizar o portal para criar um canal 

Esta secção mostra como utilizar a opção **Criar rápidas** para criar um canal pass-through.

Para obter mais detalhes sobre pass-through canais, consulte o artigo [Live transmissão com codificadores no local que criar sequências de velocidade multi](media-services-live-streaming-with-onprem-encoders.md).

1. No [portal do Azure](https://portal.azure.com/), selecione a sua conta dos serviços de multimédia do Azure.
2. Na janela **Definições** , clique em **Live transmissão**. 

    ![Introdução](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    A janela de **transmissão direto** é apresentada.

3. Clique em **Criar rápidas** para criar um canal pass-through com o RTMP ingerir esta última protocolo.

    É apresentada a janela de **Criar um novo canal** .
4. Dê um nome ao novo canal e clique em **Criar**. 

    Esta ação cria um canal pass-through com o RTMP ingerir esta última protocolo.

##<a name="create-events"></a>Criar eventos

1. Selecione um canal para o qual pretende adicionar um evento.
2. Prima o botão **Live evento** .

![Evento](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##<a name="get-ingest-urls"></a>Obter ingerir esta última URLs

Assim que o canal for criado, pode obter ingerir esta última URLs que irá fornecer ao vivo codificador. O codificador utiliza estes URLs para um fluxo direto de entrada.

![Criado](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##<a name="watch-the-event"></a>Ver o evento

Para ver o evento, clique em **Ver** no portal do Azure ou copie o URL de transmissão e utilizar um leitor da sua escolha. 
 
![Criado](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Evento direto obter automaticamente convertidos em conteúdo a pedido quando parado.

##<a name="clean-up"></a>Limpar o

Para obter mais detalhes sobre pass-through canais, consulte o artigo [Live transmissão com codificadores no local que criar sequências de velocidade multi](media-services-live-streaming-with-onprem-encoders.md).

- Pode ser terminado um canal apenas quando deixou de todos os eventos por programas no canal.  Assim que estiver parado o canal, não-assumir quaisquer encargos. Quando precisar de iniciá-lo novamente, terá a mesma ingerir esta última URL, pelo que não necessita de reconfigurar o seu codificador.
- Um canal pode ser eliminado apenas quando todos os eventos direto no canal de terem sido eliminados.

##<a name="view-archived-content"></a>Ver o conteúdo arquivado

Mesmo depois de parar e eliminar o evento, os utilizadores seria conseguir transmitir em fluxo o conteúdo arquivado como um vídeo a pedido, para desde que não está a eliminar elemento. Não pode ser eliminado um ativo se for utilizado por um evento; o evento deve ser eliminado pela primeira vez. 

Para gerir os seus ativos, selecione a **definição** e clique em **recursos**.

![Elementos](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##<a name="next-step"></a>Passo seguinte

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
