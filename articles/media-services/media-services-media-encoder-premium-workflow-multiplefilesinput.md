<properties
    pageTitle="Utilizar vários ficheiros de entrada e propriedades do componente com Premium codificador | Microsoft Azure"
    description="Este tópico explica como utilizar setRuntimeProperties para utilizar vários ficheiros de entrada e transmitir dados personalizados para o processador de multimédia de fluxo de trabalho do Media Encoder Premium."
    services="media-services"
    documentationCenter=""
    authors="xpouyat"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"  
    ms.author="xpouyat;anilmur;juliako"/>

# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Utilizar vários ficheiros de entrada e propriedades do componente com codificador Premium

## <a name="overview"></a>Descrição geral

Existem cenários na qual poderá precisar de para personalizar as propriedades do componente, especificar conteúdo Clip lista XML, ou enviar vários ficheiros de entrada quando submete uma tarefa com o processador de multimédia de **Fluxo de trabalho do Media Encoder Premium** . Alguns exemplos são:

- Sobrepor texto em vídeo e definir o valor de texto (por exemplo, a data atual) o tempo de execução para cada entrada vídeo.
- Personalizar o XML de lista de ClipArt (para especificar um ou vários ficheiros de origem, com ou sem limitação por motivos, etc.).
- Sobreposto uma imagem de logótipo no vídeo de entrada enquanto o vídeo é codificado.

Para permitir que o **Fluxo de trabalho do Media Encoder Premium** saber que está a alterar algumas propriedades no fluxo de trabalho quando criar a tarefa ou enviar vários ficheiros de entrada, tem de utilizar uma cadeia de configuração que contenha **setRuntimeProperties** e/ou **transcodeSource**. Este tópico explica como utilizá-las.


## <a name="configuration-string-syntax"></a>Sintaxe da cadeia de configuração

A cadeia de configuração para definir a tarefa de codificação utiliza um documento XML que tem este aspeto:

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


O código do c# que lê a configuração de XML a partir de um ficheiro e transmite-lo para a tarefa numa tarefa é o seguinte:

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## <a name="customizing-component-properties"></a>Propriedades do componente de personalização  

### <a name="property-with-a-simple-value"></a>Propriedade com um valor simple
Em alguns casos, é útil para personalizar uma propriedade de componente juntamente com o ficheiro de fluxo de trabalho que vai ser executado por fluxo de trabalho do Media Encoder Premium.

Suponha que concebido um fluxo de trabalho desse texto sobreposições no seu vídeos e o texto (por exemplo, a data atual) é suposto para definir o tempo de execução. Pode fazê-lo ao enviar o texto para ser definido como novo valor da propriedade texto do componente de sobreposição da tarefa codificação. Pode utilizar este mecanismo para alterar outras propriedades de um componente no fluxo de trabalho (tal como a posição ou cor de sobreposição de, a velocidade de bits do codificador AVC, etc.).

**setRuntimeProperties** é utilizada para substituir uma propriedade em componentes de fluxo de trabalho.

Exemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### <a name="property-with-an-xml-value"></a>Propriedade com um valor XML

Para definir uma propriedade que espera um valor XML, incorporar utilizando `<![CDATA[ and ]]>`.

Exemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Certifique-se de que não colocar um símbolo de retorno apenas depois `<![CDATA[`.


### <a name="propertypath-value"></a>valor propertyPath

Nos exemplos anteriores, o propertyPath foi "/ do ficheiro de multimédia entrada/NomeFicheiro" ou "/ inactiveTimeout" ou "clipListXml".
Isto é, em geral, o nome do componente, em seguida, o nome da propriedade. O caminho pode ter mais ou menos níveis, como "/ primarySourceFile" (porque a propriedade está na raiz do fluxo de trabalho) ou "/ vídeo processamento/gráfico sobreposição/opacidade" (porque está a sobreposição num grupo).    

Para verificar o caminho e a propriedade nome, utilize o botão de ação que se encontra imediatamente ao lado de cada propriedade. Pode clique neste botão de ação e selecione **Editar**. Isto vai mostrar o nome real da propriedade e imediatamente acima do mesmo, o espaço de nomes.

![Ação/Edit](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriedade](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Vários ficheiros de entrada

Cada tarefa submetidos para o **Fluxo de trabalho do Media Encoder Premium** necessita de dois elementos:

- A primeira parte é um *Recurso de fluxo de trabalho* que contém um ficheiro de fluxo de trabalho. Pode estruturar ficheiros de fluxo de trabalho utilizando o [Designer de fluxo de trabalho](media-services-workflow-designer.md).
- O segundo é um *Elementos de multimédia* que contém os ficheiros de multimédia que pretende codificar.

Quando estiver a enviar vários ficheiros de multimédia para o **Fluxo de trabalho do Media Encoder Premium** codificador, as restrições seguintes aplicam-se:

- Todos os ficheiros de multimédia devem estar no mesmo *Elementos de multimédia*. Utilizar vários elementos de multimédia não é suportada.
- Tem de definir o ficheiro principal este elementos de multimédia (Idealmente, este é o ficheiro de vídeo principal que o codificador é-lhe pedido para processar).
- É necessário transmitir dados de configuração, que inclui o elemento **setRuntimeProperties** e/ou **transcodeSource** para o processador.
  - **setRuntimeProperties** é utilizada para substituir a propriedade do nome de ficheiro ou outra propriedade em componentes de fluxo de trabalho.
  - **transcodeSource** é utilizada para especificar o conteúdo do Clip lista XML.

Ligações no fluxo de trabalho:

 - Se utilizar vários componentes de entrada do ficheiro de multimédia e plano para utilizar **setRuntimeProperties** para especificar o nome do ficheiro, em seguida, não liga o pin do componente de ficheiro principal às mesmas. Certifique-se de que não existe nenhuma ligação entre o objeto de ficheiro principal e o Input(s) de ficheiro de multimédia.
 - Se preferir utilizar Clip lista XML e um componente de origem de multimédia, em seguida, pode ligar tanto em conjunto.

![Sem ligação a partir de ficheiro de origem primária a entrada de ficheiro de multimédia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Não existe nenhuma ligação do ficheiro principal a entrada de ficheiro de multimédia componente (s) se utilizar setRuntimeProperties para definir a propriedade nome do ficheiro.*

![Ligação a partir da lista de clips XML ao Clip origem da lista](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Pode ligar Clip lista XML a origem de multimédia e utilize transcodeSource.*


### <a name="clip-list-xml-customization"></a>Personalização de XML de lista de ClipArt
Pode especificar XML lista Clip no fluxo de trabalho no runtime através da utilização **transcodeSource** na cadeia de configuração XML. Isto requer o pin do Clip lista XML de estar ligado o componente de origem de multimédia no fluxo de trabalho.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Se pretender especificar /primarySourceFile para utilizar esta propriedade para atribuir um nome os ficheiros de saída através de 'Expressões', em seguida, recomendamos que prisma Clip lista XML como uma propriedade de *depois da* propriedade /primarySourceFile, para evitar ter da lista de clips ser substituída pela definição /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Com precisão de moldura adicional limitação por motivos:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## <a name="example"></a>Exemplo

Considere um exemplo na qual pretende sobreposição de uma imagem de logótipo no vídeo de entrada enquanto o vídeo é codificado. Neste exemplo, o vídeo introdução é denominado "MyInputVideo.mp4" e o logótipo se chamar "MyLogo.png". Deve efetuar os seguintes passos:

- Criar um fluxo de trabalho ativos com o ficheiro de fluxo de trabalho (consulte o exemplo seguinte).
- Criar um elementos de multimédia, que contém dois ficheiros: MyInputVideo.mp4 como o ficheiro principal e MyLogo.png.
- Enviar uma tarefa para o processador de multimédia de fluxo de trabalho do Media Encoder Premium com os ativos de introdução acima e especifique a seguinte cadeia de configuração.

Configuração:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


No exemplo acima, o nome do ficheiro de vídeo é enviado para o componente de entrada do ficheiro de multimédia e a propriedade primarySourceFile. O nome do ficheiro logótipo é enviado para outra entrada de ficheiro de multimédia que está ligado ao componente de sobreposição do gráfico.

>[AZURE.NOTE]O nome de ficheiro de vídeo é enviado para a propriedade primarySourceFile. O motivo para isto é utilizar esta propriedade no fluxo de trabalho para criar o nome do ficheiro correto saída utilizar expressões, por exemplo.


### <a name="step-by-step-workflow-creation-that-overlays-a-logo-on-top-of-the-video"></a>Criação de fluxo de trabalho passo a passo que se sobrepõe um logótipo na parte superior do vídeo     

Eis os passos para criar um fluxo de trabalho que entra em dois ficheiros como entrada: um vídeo e de uma imagem. Irá sobreposição da imagem na parte superior do vídeo.

Abrir o **Estruturador do fluxo de trabalho** e selecione **ficheiro** > **Nova área de trabalho** > **Transcodificar plano**.

O novo fluxo de trabalho mostra três elementos:

- Ficheiro de origem principal
- Lista de clips XML
- Ficheiro/elementos de saída  

![Novo fluxo de trabalho codificação](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Novo fluxo de trabalho codificação*


Para aceitar o ficheiro de multimédia de entrada, comece com a adição de um componente de entrada de ficheiro de multimédia. Para adicionar um componente ao fluxo de trabalho, procure na caixa de pesquisa de repositório e arrastar a entrada pretendida para o painel de designer.

Em seguida, adicione o ficheiro de vídeo a ser utilizado para estruturar o seu fluxo de trabalho. Para fazê-lo, clique em Painel de fundo no estruturador de fluxo de trabalho e procure a propriedade do ficheiro de origem principal no painel de propriedades à direita. Clique no ícone da pasta e selecione o ficheiro de vídeo adequado.

![Origem de ficheiro principal](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Origem de ficheiro principal*


Em seguida, especifique o ficheiro de vídeo no componente de entrada do ficheiro de multimédia.   

![Origem de entrada do ficheiro de multimédia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Origem de entrada do ficheiro de multimédia*


Assim que este estiver concluída, o componente de entrada do ficheiro de multimédia serão inspecionar o ficheiro e preencher o seu PIN de saída para refletir o ficheiro que-seja inspecionado.

O passo seguinte consiste em Adicionar um "vídeo dados tipo QuickBooks" para especificar o espaço de cor para Rec.709. Adicionar um "vídeo formato conversor" que está definido para tipo de dados esquema/esquema = configuráveis plana. Isto irá converter a sequência de vídeo num formato que pode ser retirada como uma origem do componente de sobreposição.

![vídeo QuickBooks de tipo de dados e conversor de formato](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*QuickBooks de tipo de dados de vídeo e conversor de formato*

![Tipo de esquema = configuráveis plana](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Tipo de esquema é configuráveis plana*

Em seguida, adicionar um componente de sobreposição de vídeo e ligue o pin vídeo (não comprimido) para o pin vídeo (não comprimido) da entrada de ficheiro de multimédia.

Adicionar outra entrada de ficheiro de multimédia (para carregar o ficheiro de logótipo), clique neste componente e mude o nome para a "Logótipo de entrada do ficheiro de multimédia" e selecione uma imagem (um ficheiro. png por exemplo) na propriedade do ficheiro. Ligue o pin de imagens não comprimidas ao pin de sobreposição de imagens não comprimidas.

![Origem de ficheiro de imagem e o componente de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Origem de ficheiro de imagem e o componente de sobreposição*


Se pretende modificar a posição de logótipo no vídeo (por exemplo, poderá pretender Posicione-a na 10 por cento fora de canto superior esquerdo do vídeo), desmarque a caixa de verificação "Introdução Manual". Pode fazê-lo porque está a utilizar uma entrada de ficheiro de multimédia para fornecer o ficheiro do logótipo para o componente de sobreposição.

![Posição de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posição de sobreposição*


Para codificar a sequência de vídeo para h. 264, adicione os componentes de codificador AVC vídeo codificador e AAC para a superfície de designer. Ligue o PIN.
Configurar o codificador AAC e selecione o formato de áudio conversão/predefinido: 2.0 (L, R).

![Codificadores de áudio e vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de áudio e vídeo*


Agora adicione os componentes **ISO Mpeg-4 Multiplexador** e **Saída do ficheiro** e ligue o PIN, conforme mostrado.

![MP4 Multiplexador e saída do ficheiro](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 Multiplexador e saída do ficheiro*


Tem de definir o nome para o ficheiro de exportação. Clique no componente de **Saída do ficheiro** e editar a expressão para o ficheiro:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome de saída do ficheiro](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome de saída do ficheiro*

Pode executar o fluxo de trabalho localmente para verificar que está a funcionar corretamente.

Depois de terminar, pode executá-la dos serviços de multimédia do Azure.

Em primeiro lugar, preparar um ativo nos serviços de multimédia do Azure com os dois ficheiros: o ficheiro de vídeo e o logótipo. Pode fazê-lo utilizando o .NET ou REST API. Também pode fazer isto, utilizando o Azure portal ou o [Explorador dos serviços de multimédia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Este tutorial mostra-lhe como gerir os ativos com AMSE. Existem duas formas de adicionar ficheiros de um ativo:

- Criar uma pasta local, copie os dois ficheiros e arraste e largue a pasta para o separador **ativo** .
- Carregar o ficheiro de vídeo como um activo, apresentar as informações de elementos, aceda ao separador ficheiros e carregar um ficheiro adicional (logótipo).

>[AZURE.NOTE]Certifique-se definir um ficheiro principal no activo (o principal ficheiro de vídeo).

![Ficheiros de elementos no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Ficheiros de elementos no AMSE*


Selecione o elemento e selecione para codificá-lo com Premium Encoder. Carregue o fluxo de trabalho e selecioná-la.

Clique no botão para transmitir dados para o processador e adicionar o XML seguinte para definir as propriedades de tempo de execução:

![Descodificador Premium no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Descodificador Premium no AMSE*


Em seguida, cole os seguintes dados XML. Tem de especificar o nome do ficheiro de vídeo para a entrada de ficheiro de multimédia e a primarySourceFile. Especifique o nome do nome do ficheiro para o logótipo demasiado.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Se utilizar o .NET SDK para criar e executar a tarefa, estes dados XML tem de ser transmitido como a cadeia de configuração.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Depois da tarefa estiver concluída, o ficheiro de MP4 no elemento de exportação apresenta a sobreposição!

![Sobreposição no vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sobreposição no vídeo*


Pode transferir o fluxo de trabalho de exemplo do [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## <a name="see-also"></a>Consulte também

- [Introdução ao Premium codificação de multimédia do Microsoft Azure serviços](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Como utilizar a codificação de Premium nos serviços de multimédia do Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Codificação de conteúdo a pedido com os serviços de multimédia do Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Codecs e formatos de fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

- [Ficheiros de fluxo de trabalho de exemplo](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Ferramenta de Explorador dos serviços de multimédia do Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
