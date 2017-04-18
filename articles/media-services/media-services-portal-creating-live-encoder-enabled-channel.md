<properties 
    pageTitle="Como efetuar a transmissão direto através dos serviços de multimédia do Azure para criar fluxos de velocidade multi com o portal do Azure | Microsoft Azure" 
    description="Neste tutorial explica os passos da criação de um canal que recebe um fluxo direto de velocidade único e codifica-lo à sequência de velocidade multi através do portal Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
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


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Como efetuar a transmissão direto através dos serviços de multimédia do Azure para criar fluxos de velocidade multi com o portal do Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Neste tutorial orienta-o através dos passos de criação de um **canal** que recebe um fluxo direto de velocidade único e codifica-lo à sequência de velocidade multi.

>[AZURE.NOTE]Para mais informações conceptuais relacionados com canais que estejam ativados para codificação live, consulte o artigo [Live transmissão utilizando os serviços de multimédia do Azure ao criar sequências de velocidade multi](media-services-manage-live-encoder-enabled-channels.md).

##<a name="common-live-streaming-scenario"></a>Cenário de transmissão direto comum

Seguem-se os passos gerais envolvidos na criação de aplicações comuns de transmissão direto.

>[AZURE.NOTE] Atualmente, a duração recomendada máxima de um evento do live é 8 horas. Contacte o suportehttp amslived em Microsoft.com se de que precisa para executar um canal para períodos de tempo mais longos.

1. Liga uma câmara de vídeo num computador. Iniciação e configurar um codificador direto no local que pode exportar uma sequência de velocidade única de uma das seguintes protocolos: RTMP, transmissão suaves ou RTP (MPEG-TS). Para mais informações, consulte o artigo [suporte de RTMP de serviços de multimédia do Azure e codificadores Live](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Este passo também pode ser realizado depois de criar o seu canal.

1. Criar e comece a um canal. 

1. Obter o canal ingerir esta última URL. 

    O URL de ingest é utilizado pelo codificador direto para enviar a sequência de para o canal.
1. Obter o URL de pré-visualização do canal. 

    Utilize este URL para verificar o seu canal corretamente está a receber o fluxo direto.

3. Crie um evento/programa (que também irá criar um ativo). 
1. Publica o evento (que irá criar um locator pedido para o ativo associado).  

    Certifique-se de ter, pelo menos, uma transmissão unidade reservada no ponto final transmissão a partir do qual pretende em sequência de conteúdo.
1. Inicie o evento quando estiver pronto para começar a transmissão e arquivo.
2. Opcionalmente, o codificador live pode serão avisados de que para iniciar um anúncio. Anúncio de é inserido na sequência de saída.
1. Pare o evento sempre que pretender parar streaming e arquivo o evento.
1. Eliminar o evento (e, opcionalmente, eliminar o activo).   

##<a name="in-this-tutorial"></a>Neste tutorial

Neste tutorial, o portal do Azure é utilizado para realizar as seguintes tarefas: 

2.  Configure os pontos finais transmissão.
3.  Crie um canal que está ativado para efetuar a codificação direto.
1.  Obter o URL ingerir esta última para fornecê-la para live codificador. O live codificador irá utilizar este URL para ingerir esta última a sequência de para o canal. .
1.  Criar um evento/programa (e de um ativo)
1.  Publicar o elemento e obter transmissão URLs  
1.  Reproduzir o seu conteúdo 
2.  Limpar

##<a name="prerequisites"></a>Pré-requisitos

A seguinte é necessários para completar a iniciação.

- Para concluir este tutorial, é necessária uma conta Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- Uma conta dos serviços de multimédia. Para criar uma conta dos serviços de multimédia, consulte o artigo [Criar conta](media-services-portal-create-account.md).
- Uma câmara Web e um codificador que pode enviar uma única velocidade live da cadeia.

##<a name="configure-streaming-endpoints"></a>Configurar pontos finais de transmissão 

Dos serviços de multimédia fornece embalagem dinâmica que permite-lhe entregar a sua velocidade multi MP4s nos seguintes formatos de transmissão: MPEG travessão, HLS, transmissão suaves ou HDS, sem ter de voltar Compactar para estes formatos de transmissão. Com embalagem dinâmica necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia irá criar e servir a resposta adequada baseada nos pedidos de um cliente.

Para tirar partido da embalagem dinâmico, o que precisa para obter, pelo menos, uma unidade de transmissão para o ponto final de transmissão a partir do qual planeia entrega o conteúdo.  

Para criar e alterar o número de unidades reservadas de transmissão, faça o seguinte:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e selecione a sua conta AMS.
1. Na janela **Definições** , clique em **pontos finais de transmissão**. 

2. Clique na predefinição transmissão ponto final. 

    A janela de **Detalhes de ponto final de transmissão de predefinido** é apresentada.

3. Para especificar o número de unidades transmissão, controlo de deslize **transmissão unidades** .

    ![Transmissão de unidades](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-streaming-units.png)

4. Clique no botão **Guardar** para guardar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode demorar até 20 minutos a concluir.

##<a name="create-a-channel"></a>Criar um canal

1. No [portal do Azure](https://portal.azure.com/), selecione dos serviços de multimédia e, em seguida, clique no seu nome de conta dos serviços de multimédia.
2. Selecione **Live transmissão**.
3. Selecione **Criar personalizado**. Esta opção permitirá que criar um canal que está ativado para codificação direto.

    ![Criar um canal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
    
4. Clique em **Definições**.
    
    1.  Escolha o tipo de canal **Live codificação** . Este tipo Especifica que pretende criar um canal que está ativado para codificação direto. Isto significa que a receção velocidade única sequência é enviada para o canal e codificada para uma sequência de velocidade multi utilizando as definições de codificador direto especificado. Para mais informações, consulte o artigo [Live transmissão utilizando os serviços de multimédia do Azure ao criar sequências de velocidade multi](media-services-manage-live-encoder-enabled-channels.md). Clique em OK.
    2. Especifique o nome de um canal.
    3. Clique em OK na parte inferior do ecrã.
    
5. Selecione o separador **Ingest** .

    1. Nesta página, pode selecionar um protocolo de transmissão. Para o tipo de canal **Live codificação** , opções de protocolo válidos são:
        
        - Única velocidade Fragmented MP4 (transmissão suave)
        - Velocidade única RTMP
        - (MPEG-TS) RTP: Sequência de transporte MPEG-2 sobre RTP.
        
        Para explicação detalhada sobre cada protocolo, consulte o artigo [Live transmissão utilizando os serviços de multimédia do Azure ao criar sequências de velocidade multi](media-services-manage-live-encoder-enabled-channels.md).
    
        Não é possível alterar a opção de protocolo enquanto o canal ou estiver a executar o respetivos associados/programas de eventos. Se necessitar de protocolos diferentes, deverá criar canais separados para cada protocolo transmissão.  

    2. Pode aplicar restrição de IP na ingest. 
    
        Pode definir os endereços IP que são permitidos para ingerir esta última um vídeo para este canal. Permitido endereços IP podem ser especificados como um único endereço IP (por exemplo, ' 10.0.0.1'), um intervalo de IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22') ou um intervalo de IP com um endereço IP e uma máscara de pontilhado sub-rede decimal (por exemplo, ' 10.0.0.1(255.255.252.0)').

        Se sem endereços IP forem especificados e não existe nenhuma definição de regra, em seguida, sem endereço IP é permitido. Para permitir que qualquer endereço IP, crie uma regra e defina 0.0.0.0/0.

6. No separador **pré-visualização** , aplicam-se uma restrição de IP sobre a pré-visualização.
7. No separador **codificação** , especifique o codificação predefinido. 

    Atualmente, o sistema apenas predefinido, pode selecionar é **predefinido 720p**. Para especificar uma configuração personalizada predefinida, abra uma bilhetes de suporte da Microsoft. Em seguida, introduza o nome da predefinido criado por si. 

>[AZURE.NOTE] Atualmente, o início do canal pode demorar até 30 minutos. Reposição do canal pode demorar até 5 minutos.

Depois de criado o canal, pode clique no canal e selecione **Definições** onde pode ver as configurações de canais. 

Para mais informações, consulte o artigo [Live transmissão utilizando os serviços de multimédia do Azure ao criar sequências de velocidade multi](media-services-manage-live-encoder-enabled-channels.md).


##<a name="get-ingest-urls"></a>Obter ingerir esta última URLs

Assim que o canal for criado, pode obter ingerir esta última URLs que irá fornecer ao vivo codificador. O codificador utiliza estes URLs para um fluxo direto de entrada.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##<a name="create-and-manage-events"></a>Criar e gerir eventos

###<a name="overview"></a>Descrição geral

Um canal está associado a eventos/programas permite-lhe controlar a publicação e armazenamento dos segmentos numa sequência direto. Canais gerir eventos/programas. A relação de canal e o programa é muito semelhante a multimédia tradicional onde um canal tem uma constante sequência de conteúdo e um programa está confinado a algumas evento temporizado desse canal.

Pode especificar o número de horas que pretende manter o conteúdo para o evento gravado ao definir o comprimento da **Janela de arquivo** . Este valor pode ser definido a partir de um mínimo de 5 minutos para um máximo de 25 horas. Comprimento da janela de arquivo dita também que a quantidade máxima de clientes de tempo pode atingir trás no tempo da posição atual direto. Podem executar eventos sobre a quantidade de tempo especificada, mas conteúdo determinado atrás o comprimento de janela continuamente é eliminado. Este valor desta propriedade também determina quanto tempo podem aumentar os manifestos de cliente.

Cada evento está associado um ativo. Para publicar o evento tem de criar um locator pedido para o ativo associado. Está a ter este locator permite-lhe criar um URL de transmissão pode fornecer aos seus clientes.

Um canal suporta até três eventos em simultâneo em execução, para que possa criar múltiplas arquivos do mesmo fluxo recebido. Esta opção permite-lhe publicar e arquivar diferentes partes do evento, conforme necessário. Por exemplo, a necessidade de negócio é para arquivar 6 horas do evento, mas difundir apenas últimos 10 minutos. Para realizar esta tarefa, tem de criar duas em simultâneo a executar o evento. Um evento está definido para arquivar 6 horas do evento, mas o programa não está a ser publicado. O outro evento está definido para arquivar para 10 minutos e este programa é publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e iniciar um novo programa para cada evento.

Inicie um evento/programa quando estiver pronto para começar a transmissão e arquivo. Pare o evento sempre que pretender parar streaming e arquivo o evento. 

Para eliminar conteúdo arquivado, pare e eliminar o evento e, em seguida, eliminar o activo associado. Não pode ser eliminado um ativo se for utilizado pelo evento; o evento deve ser eliminado pela primeira vez. 

Mesmo depois de parar e eliminar o evento, os utilizadores seria conseguir transmitir em fluxo o conteúdo arquivado como um vídeo a pedido, para desde que não está a eliminar elemento.

Se pretender manter o conteúdo arquivado, mas não a tem disponível para transmissão, elimine o localizador de transmissão.

###<a name="createstartstop-events"></a>Criar/início/fim eventos

Assim que tiver a sequência de a fluir para o canal pode começar o evento de transmissão através da criação de um programa, imobilizado e transmissão Locator. Isto irá arquivar a sequência de e prepará-lo para visualizadores através do ponto final de transmissão. 

Existem duas formas de iniciar o evento: 

1. Na página de **canal** , prima **Live evento** para adicionar um novo evento.

    Especificar: nome do evento, nome de elementos, a janela arquivo e opção encriptação.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Se para a esquerda **publicar este evento direto agora** selecionada, irá obter criado o evento os URLs de publicação.
    
    Pode premir **Iniciar**, sempre que estiver pronto para transmitir em fluxo o evento.

    Assim que iniciar o evento, pode premir **monitorização** para iniciar a reprodução o conteúdo.

2. Em alternativa, pode utilizar um atalho e prima o botão **Ir Live** na página de **canal** . Esta opção criará uma predefinição de elementos, programa e transmissão Locator.

    O evento com o nome **predefinido** e a janela de arquivo está definida para 8 horas.

Pode ver o evento publicado a partir da página **Live evento** . 

Se clicar em **Desativar Air**, irá parar a todos os eventos direto. 


##<a name="watch-the-event"></a>Ver o evento

Para ver o evento, clique em **Ver** no portal do Azure ou copie o URL de transmissão e utilizar um leitor da sua escolha. 
 
![Criado](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Evento direto converte automaticamente a pedido conteúdo quando parado eventos.

##<a name="clean-up"></a>Limpar o

Se são feitas a transmissão de eventos e pretende limpar os recursos aprovisionados anterior, siga o procedimento seguinte.

- Pare o envio de sequências do codificador.
- Pare o canal. Assim que estiver parado o canal, não-lo será assumir quaisquer encargos. Quando precisar de iniciá-lo novamente, terá a mesma ingerir esta última URL, pelo que não necessita de reconfigurar o seu codificador.
- Pode deixar o ponto final transmissão, a menos que pretenda continuar fornecer o arquivo do seu evento live como uma sequência da pedido. Se for o canal estado parado,-não será assumir quaisquer encargos.
  
##<a name="view-archived-content"></a>Ver o conteúdo arquivado

Mesmo depois de parar e eliminar o evento, os utilizadores seria conseguir transmitir em fluxo o conteúdo arquivado como um vídeo a pedido, para desde que não está a eliminar elemento. Não pode ser eliminado um ativo se for utilizado por um evento; o evento deve ser eliminado pela primeira vez. 

Para gerir os seus ativos, selecione a **definição** e clique em **recursos**.

![Elementos](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

##<a name="considerations"></a>Considerações sobre

- Atualmente, a duração recomendada máxima de um evento do live é 8 horas. Contacte o suportehttp amslived em Microsoft.com se de que precisa para executar um canal para períodos de tempo mais longos.
- Certifique-se de ter, pelo menos, uma transmissão unidade reservada no ponto final transmissão a partir do qual pretende em sequência de conteúdo.


##<a name="next-step"></a>Passo seguinte

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
