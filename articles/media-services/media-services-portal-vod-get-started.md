<properties
    pageTitle=" Introdução ao fornecer conteúdo a pedido através do portal Azure | Microsoft Azure"
    description="Neste tutorial orienta-o através dos passos de execução de um serviço de entrega de conteúdos invés (VoD) básico com a aplicação de serviços de multimédia do Azure (MGA) utilizando o portal do Azure."
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Introdução ao fornecer conteúdo a pedido através do portal Azure

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

Neste tutorial orienta-o através dos passos de execução de um serviço de entrega de conteúdos invés (VoD) básico com a aplicação de serviços de multimédia do Azure (MGA) utilizando o portal do Azure.

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Neste tutorial inclui as seguintes tarefas:

1.  Crie uma conta dos serviços de multimédia do Azure.
2.  Configure o ponto final de transmissão.
1.  Carregar um ficheiro de vídeo.
1.  Codificar o ficheiro de origem para o conjunto de velocidade ajustável MP4 ficheiros.
1.  Publica os elementos e obter transmissão e URLs de transferência gradual.  
1.  Reproduza o seu conteúdo.


## <a name="create-an-azure-media-services-account"></a>Criar uma conta de serviços de multimédia do Azure

Os passos nesta secção Mostrar como criar uma conta de AMS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** > **Web + Mobile** > **dos serviços de multimédia**.

    ![Criar dos serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. **Criar conta de serviços de multimédia** introduza valores necessários.

    ![Criar dos serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Em **Nome da conta**, introduza o nome da nova conta de AMS. Um nome de conta dos serviços de multimédia é todos os números em minúsculas ou letras sem espaços e é 3 para 24 carateres de comprimento.
    2. Na sua subscrição, selecione entre as diferentes subscrições Azure que tiver acesso a.
    
    2. No **Grupo de recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é um conjunto de recursos que partilham o ciclo de vida, permissões e políticas de. Saiba mais [aqui](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. **Localização**, selecione a região geográfica é utilizada para armazenar os registos de multimédia e de metadados para a sua conta dos serviços de multimédia. Esta região é utilizada para processar e transmitir em fluxo os ficheiros de multimédia. Apenas as regiões dos serviços de multimédia disponíveis aparecem na caixa de lista pendente. 
    
    3. Na **Conta de armazenamento**, selecione uma conta de armazenamento para fornecer o armazenamento de Blobs do conteúdo multimédia da sua conta dos serviços de multimédia. Pode selecionar uma conta de armazenamento existente na mesma região geográfica como a sua conta dos serviços de multimédia, ou pode criar uma conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras de nomes de conta de armazenamento são iguais para contas dos serviços de multimédia.

        Saiba mais sobre o armazenamento [aqui](storage-introduction.md).

    4. Selecione **Afixar ao dashboard** para ver o progresso de implementação de conta.
    
7. Clique em **Criar** na parte inferior do formulário.

    Assim que a conta estiver criada com êxito, o estado muda para **a executar**. 

    ![Definições de serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para gerir a sua conta AMS (por exemplo, carregar vídeos, codificar elementos, monitorizar o progresso da tarefa) utilize a janela **Definições** .

## <a name="manage-keys"></a>Gerir chaves

Precisa do nome da conta e as informações da chave primárias através de programação acesso à conta dos serviços de multimédia.

1. No portal do Azure, selecione a sua conta. 

    Na janela **Definições** que é apresentado à direita. 

2. Na janela **Definições** , selecione **teclas**. 

    As janelas de **Gerir chaves** mostra o nome da conta e as teclas principais e secundárias é apresentada. 
3. Prima o botão Copiar para copiar os valores.
    
    ![Teclas de serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>Configurar pontos finais de transmissão

Quando estiver a trabalhar com os serviços de multimédia do Azure um dos cenários mais comuns está a entregar vídeo através de velocidade ajustável transmissão os seus clientes. Dos serviços de multimédia suporta a velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas).

Dos serviços de multimédia fornece embalagem dinâmica, que permite-lhe entregar a sua velocidade ajustável MP4 codificado conteúdo transmissão formatos suportados dos serviços de multimédia (MPEG travessão, HLS, transmissão suaves, HDS) just-in-time, sem ter de armazenar previamente embalados versões de cada um destes transmissão formatos.

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro mezzanine (origem) para um conjunto de ficheiros de velocidade ajustável MP4 (os passos de codificação são demonstrados mais tarde neste tutorial).  
- Crie, pelo menos, uma unidade de transmissão para o *ponto final de transmissão* a partir do qual planeia entrega o conteúdo. Os passos abaixo Mostrar como alterar o número de unidades transmissão.

Com embalagem dinâmica, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia constrói e serve a resposta adequada baseada nos pedidos de um cliente.

Para criar e alterar o número de unidades reservadas de transmissão, faça o seguinte:


1. Na janela **Definições** , clique em **pontos finais de transmissão**. 

2. Clique na predefinição transmissão ponto final. 

    A janela de **Detalhes de ponto final de transmissão de predefinido** é apresentada.

3. Para especificar o número de unidades transmissão, controlo de deslize **transmissão unidades** .

    ![Transmissão de unidades](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Clique no botão **Guardar** para guardar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode demorar até 20 minutos a concluir.

## <a name="upload-files"></a>Carregar ficheiros

Sequência de vídeos através dos serviços de multimédia do Azure, tem de carregar os vídeos de origem, codificá-los para vários bitrates e publicar o resultado. O primeiro passo é abrangido nesta secção. 

1. Na janela da **definição** , clique em **recursos**.

    ![Carregar ficheiros](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. Clique no botão **carregar** .

    A janela **carregar um vídeo activo** é apresentada.

    >[AZURE.NOTE] Não existe nenhuma limitação de tamanho do ficheiro.
    
4. Navegue para o vídeo pretendido no seu computador, selecione-o e clique em OK.  

    Inicia o carregamento e pode ver o progresso sob o nome de ficheiro.  

Assim que o carregamento for concluída, verá o novo imobilizado listado na janela de **recursos** . 

## <a name="encode-assets"></a>Codificar activos

Quando estiver a trabalhar com os serviços de multimédia do Azure um dos cenários mais comuns está a entregar velocidade ajustável transmissão os seus clientes. Dos serviços de multimédia suporta a velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas). Para preparar os seus vídeos para transmissão de velocidade ajustável, tem de codificar o vídeo de origem em ficheiros de velocidade multi. Deve utilizar o codificador **Media Encoder padrão** para codificar os seus vídeos.  

Serviços de multimédia também fornecem embalagem dinâmica, que permite-lhe entregar a sua velocidade multi MP4s nos seguintes formatos de transmissão: MPEG travessão, HLS, transmissão suaves ou HDS, sem ter de voltar a compactar para estes formatos de transmissão. Com embalagem dinâmica, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia constrói e serve a resposta adequada baseada nos pedidos de um cliente.

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro de origem para o conjunto de velocidade multi MP4 ficheiros (os passos de codificação demonstrados mais tarde nesta secção).
- Obter, pelo menos, uma unidade de transmissão para o ponto final de transmissão a partir do qual planeia entrega o conteúdo. Para mais informações, consulte o artigo [configurar os pontos finais transmissão](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Utilizar o portal para codificar

Esta secção descreve os passos que pode tomar para codificar o seu conteúdo com o padrão de descodificador de multimédia.

1.  Na janela **Definições** , selecione **recursos**.  
2.  Na janela de **activos** , selecione os elementos que pretender para codificar.
3.  Prima o botão **Codificar** .
4.  Na janela do **codificar um ativo** , selecione o processador "Media Encoder padrão" e uma configuração predefinida. Por exemplo, se souber o vídeo introdução tem uma resolução de 1920x1080 pixels, em seguida, pode utilizar o "H264 velocidade vários 1080p" predefinido. Para mais informações sobre predefinições, consulte [Este](https://msdn.microsoft.com/library/azure/mt269960.aspx) artigo – é importante selecionar o predefinido que é o mais adequado para a sua entrada vídeo. Se tiver um vídeo de baixa resolução (640x360), em seguida, deverá não estar a utilizar a predefinição "H264 velocidade vários 1080p" predefinido.
    
    Para facilitar a gestão, tem uma opção de edição no nome do elemento de saída e o nome da tarefa.
        
    ![Codificar activos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Prima **Criar**.

### <a name="monitor-encoding-job-progress"></a>Monitorizar o progresso codificação de tarefa

Para monitorizar o progresso da tarefa de codificação de, clique em **Definições** (na parte superior da página) e, em seguida, selecione **tarefas**.

![Tarefas](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicar conteúdos

Para fornecer o utilizador com um URL que pode ser utilizado para transmitir em fluxo ou transferir o seu conteúdo, tem primeiro de "publicação" a sua elementos criando um localizador. Locator fornece acesso aos ficheiros contidas num elemento. Dos serviços de multimédia suporta dois tipos de Locator: 

- Transmissão Locator (OnDemandOrigin), utilizado para a transmissão ajustável (por exemplo, a sequência de TRAÇO MPEG, HLS ou transmissão suave). Para criar um localizador de transmissão seu activo tem de conter um ficheiro de .ism. 
- Gradual Locator (SA), utilizado para a entrega de vídeo através de transferência gradual.


Um URL de transmissão tem o seguinte formato e utilizá-lo para reproduzir activos transmissão suaves.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Para criar um HLS transmissão URL, acrescentar (formato = m3u8 aapl) para o URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Para criar um TRAÇO MPEG transmissão URL, acrescentar (formato = mpd-tempo-LCR) para o URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Um URL de SA tem o seguinte formato.

    {blob container name}/{asset name}/{file name}/{SAS signature}

>[AZURE.NOTE] Se tiver utilizado o portal para criar Locator antes de Março de 2015, Locator com uma data de expiração de dois anos foram criado.  

Para atualizar uma data de validade um locator, utilize [resto](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) APIs. Quando atualizar a data de expiração de um localizador de SA, o URL for alterado.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Utilizar o portal para publicar um ativo

Utilizar o portal para publicar um ativo, faça o seguinte:

1. Selecione **Definições** > **elementos**.
1. Selecione os elementos que pretende publicar.
1. Clique no botão **Publicar** .
1. Selecione o tipo de localizador.
2. Prima a **Adicionar**.

    ![Publicar](./media/media-services-portal-vod-get-started/media-services-publish1.png)

O URL é adicionado à lista de **URLs publicado**.

## <a name="play-content-from-the-portal"></a>Reproduzir conteúdo a partir do portal

Portal do Azure fornece um leitor de conteúdo que pode utilizar para testar o seu vídeo.

Clique no vídeo pretendido e, em seguida, clique no botão **Reproduzir** .

![Publicar](./media/media-services-portal-vod-get-started/media-services-play.png)

Algumas considerações aplicam-se:

- Certifique-se de que publicou o vídeo.
- Este **Media player** reproduz da predefinição de transmissão de ponto final. Se pretende reproduzir a partir de um ponto final a transmissão de não predefinida, clique em para copiar o URL e utilizar o leitor de outra. Por exemplo, [Leitor de serviços de multimédia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

##<a name="next-steps"></a>Próximos passos

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


