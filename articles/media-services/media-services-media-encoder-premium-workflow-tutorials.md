<properties 
    pageTitle="Tutoriais de fluxo de trabalho do Avanced Media Encoder Premium" 
    description="Este documento contém instruções passo a passo que mostram como efetuar tarefas avançadas com o fluxo de trabalho do Media Encoder Premium e também como criar fluxos de trabalho complexos estruturador do fluxo de trabalho." 
    services="media-services" 
    documentationCenter="" 
    authors="xstof" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"  
    ms.author="xstof;xpouyat;juliako"/>

#<a name="advanced-media-encoder-premium-workflow-tutorials"></a>Tutoriais de fluxo de trabalho do Media Encoder Premium avançados

##<a name="overview"></a>Descrição geral 

Este documento contém instruções passo a passo que mostram como personalizar fluxos de trabalho com o **Designer de fluxo de trabalho**. Pode encontrar os ficheiros de fluxo de trabalho real [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

##<a name="toc"></a>ÍNDICE

Aborda os seguintes tópicos:

- [Codificação MXF para uma única velocidade MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
    - [Iniciar um novo fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new) 
    - [Utilizar a entrada de ficheiro de multimédia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
    - [Inspecionar sequências de multimédia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
    - [Adicionar um vídeo codificador para. Geração de ficheiro MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
    - [Codificação a sequência de áudio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
    - [Multiplexing sequências de áudio e vídeo para um contentor MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
    - [Escrever o ficheiro de MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
    - [Criar um activo de serviços de multimédia do ficheiro de exportação](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
    - [Teste o fluxo de trabalho concluído localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
- [Codificação MXF para multibitrate MP4s - embalagem dinâmico activado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
    - [Adicionar uma ou mais saídas MP4 adicionais](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
    - [Configurar os nomes de saída do ficheiro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
    - [Adicionar uma faixa de áudio em separado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
    - [Adicionar a. Ficheiro SMIL ISM](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
- [Codificação MXF para multibitrate MP4 - plano melhorado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
    - [Descrição geral de fluxo de trabalho para melhorar](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_overview)
    - [Convenções de nomenclatura de ficheiros](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
    - [Propriedades do componente de publicação para a raiz de fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
    - [Tiver gerado o ficheiro de exportação nomes dependem de valores de propriedade publicados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
- [Adicionar miniaturas a multibitrate saída MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
    - [Descrição geral de fluxo de trabalho para adicionar miniaturas para](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to_multibitrate_MP4_overview)
    - [Adicionar a codificação de JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
    - [Trabalhar com a conversão de espaço de cor](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
    - [Escrever as miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
    - [Detetar erros num fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
    - [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
- [Limitação por motivos baseados no tempo de saída multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
    - [Descrição geral de fluxo de trabalho para começar a adicionar limitação por motivos para](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
    - [Utilizar o Recortador da cadeia](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
    - [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
- [Introdução ao componente de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
    - [Scripting dentro de um fluxo de trabalho: Olá a todos](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
- [Baseado na moldura limitação por motivos de saída multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
    - [Descrição geral de plano para começar a adicionar limitação por motivos para](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
    - [Utilizar a lista de clips XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
    - [Modificar a lista de ClipArt a partir de um componente de script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
    - [Adicionar uma propriedade de conveniência ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

##<a id="MXF_to_MP4"></a>Codificação MXF para uma única velocidade MP4
 
Neste tutorial vamos criar uma única velocidade. Ficheiro de MP4 com AAC-HE codificado áudio a partir de um. Ficheiro de entrada MXF. 

###<a id="MXF_to_MP4_start_new"></a>Iniciar um novo fluxo de trabalho 

Abrir o estruturador do fluxo de trabalho e selecione "Ficheiro"-"nova área de trabalho"-"transcodificar plano" 

O novo fluxo de trabalho irá mostrar 3 elementos: 

- Ficheiro de origem principal
- Lista de clips XML
- Ficheiro/elementos de saída  

![Novo fluxo de trabalho codificação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Novo fluxo de trabalho codificação*

###<a id="MXF_to_MP4_with_file_input"></a>Utilizar a entrada de ficheiro de multimédia

Para aceitar o nosso ficheiro de multimédia de entrada, um é iniciado com a adição de um componente de entrada de ficheiro de multimédia. Para adicionar um componente ao fluxo de trabalho, procure na caixa de pesquisa de repositório e arrastar a entrada pretendida para o painel de designer. Fazê-lo para a entrada de ficheiro de multimédia e ligar o componente de ficheiro de origem principal para o pin de entrada do nome de ficheiro da entrada de ficheiro de multimédia.

![Entrada de ficheiros de multimédia ligada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada de ficheiros de multimédia ligada*

Antes de podemos fazer muito mais preciso, irá pela primeira vez precisamos de indicar ao estruturador de fluxo de trabalho que ficheiro de exemplo estamos gostaria de utilizar para estruturar o nosso fluxo de trabalho com. Para fazê-lo, clique no fundo do painel designer e procure a propriedade do ficheiro de origem principal no painel de propriedades à direita. Clique no ícone da pasta e selecione o ficheiro pretendido para testar o fluxo de trabalho com. Assim que este estiver concluída, o componente de entrada do ficheiro de multimédia serão inspecionar o ficheiro e preencher o seu PIN de saída para refletir o ficheiro que seja inspecionado.

![Entrada de ficheiros de multimédia povoada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada de ficheiros de multimédia povoada*

Enquanto Isto especifica com que entrada gostaríamos de fazer trabalhar com,-não indicar ao ainda onde a saída codificada deve ir para. Ficheiro de origem semelhante ao modo como a principal foi configurado, agora configurar a propriedade variável de pasta de saída, imediatamente abaixo do mesmo.

![Propriedades de entrada e saída configuradas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriedades de entrada e saída configuradas*

###<a id="MXF_to_MP4_streams"></a>Inspecionar sequências de multimédia

Muitas vezes tem desejado saber a sequência de aspeto desta forma monetários através do fluxo de trabalho. Para inspecionar uma sequência em qualquer ponto no fluxo de trabalho, basta clicar uma saída ou em qualquer um dos componentes de pino de entrada. Neste caso, experimente clicar no pin de saída não comprimidas vídeo a partir do nosso entrada de ficheiro de multimédia. Uma caixa de diálogo será aberto que permite a inspecionar o vídeo de saída.

![Da inspeção de pino de saída de vídeo não comprimidas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Da inspeção de pino de saída de vídeo não comprimidas*

No nosso caso,-informa-nos por exemplo estamos está a trabalhar com uma entrada de 1920x1080 24 frames por segundo no 4: amostragem 2:2 de um vídeo de quase 2 minutos.

###<a id="MXF_to_MP4_file_generation"></a>Adicionar um vídeo codificador para. Geração de ficheiro MP4

Tenha em atenção que agora, um vídeo não comprimidas e várias PIN de saída de áudio não comprimido está disponível para utilização no nosso entrada de ficheiro de multimédia. Para poder codificar o vídeo de entrada, é necessário um componente de codificação - neste caso para gerar. Ficheiros MP4.

Para codificar a sequência de vídeo para h. 264, adicione o componente de codificador vídeo AVC para a superfície de designer. Este componente leva-o até uma sequência de vídeo descomprimir como entrada e oferece uma AVC comprimida sequência de vídeo no seu pin de saída.

![Não ligadas AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Não ligadas AVC Encoder*

As respetivas propriedades determinam como a codificação exatamente acontece. Vamos dar uma olhadela em algumas das definições do mais importantes:

- Saída largura e altura de saída: estes determinam a resolução do vídeo codificado. No nosso caso vamos com 640x360
- Taxa de moldura: quando estão definidos para pass-through-irá apenas adotar a taxa de moldura de origem, é possível substituí-lo embora. Tenha em atenção que essa transformação de velocidade de fotogramas não é movimento compensação.
- Perfil e nível: estes determinam o perfil AVC e o nível. Para convenientemente obter mais informações sobre os diferentes níveis e perfis, clique no ícone de ponto de interrogação no componente AVC vídeo codificador e a página de ajuda irá mostrar mais detalhes sobre cada um dos níveis. Para o nosso exemplo, vamos aceda com perfil principais ao nível 3,2 (a predefinição).
- Classificar controlar o modo e velocidade de bits (kbps): os nossos cenário podemos optar ativamente por participar para uma constante velocidade (CBR) a 1200 kbps de saída
- Formato de vídeo: Este é sobre VUI (informações da eficiência de utilização de vídeo) que obtém escrita para a sequência de h. 264 (lado informações sobre o que pode ser utilizados por um descodificador para melhorar a apresentação mas não é essencial para corretamente descodificar):
- NTSC (típica para Japão, utilizando 30 fps ou dos e.u.a.)
- PAL (típica para Europa, utilizando 25 fps)
- Modo de tamanho de GOP: Recomendamos irá configurar tamanho GOP fixo para as nossas finalidades com um intervalo de chave de 2 segundos com GOPs fechado. Este procedimento garante fornece compatibilidade com os serviços de multimédia do Azure embalagem dinâmica.

Feed nosso codificador AVC, ligue o pin de saída do vídeo não comprimidas do componente de entrada de ficheiro de multimédia o vídeo não comprimidas pino de entrada do codificador AVC.

![Ligada AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Descodificador de AVC principais ligada*

###<a id="MXF_to_MP4_audio"></a>Codificação a sequência de áudio

Neste momento, podemos ter codificado vídeo, mas a sequência de áudio não comprimida original ainda precisa de ser comprimido. Para este vamos com AAC codificação pelo componente codificador AAC (áudio Dolby). Adicioná-la para o fluxo de trabalho.

![Não ligadas AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Descodificador AAC desligado*

Agora existe uma incompatibilidade: existe apenas um única não comprimido áudio pino de entrada do codificador AAC enquanto provavelmente mais do que a entrada de ficheiro de multimédia terão duas diferentes não comprimidas sequência de áudio disponíveis: uma para o canal de áudio para a esquerda e outra para a direita. (Se estiver a trabalhar com som panorâmico, é 6 canais.) Por isso, não é possível ligar diretamente o áudio a partir da origem de entrada do ficheiro de multimédia para o codificador áudio AAC. O componente AAC espera uma sequência de áudio "intercalada" chamada: uma única sequência tem à esquerda e direita canais intercalados com os outros. Quando podemos souber a partir de ficheiro de multimédia nosso origem quais faixas de áudio estão em que posição na origem, podemos pode gerar essa intercalado sequência de áudio com as posições de orador corretamente atribuídos para a esquerda e direita.

Em primeiro lugar um pretenderá geradas uma sequência de intercalado a partir dos canais áudio origem necessária. O componente de áudio da cadeia Interleaver processará isto para-nos. Adicioná-lo para o fluxo de trabalho e ligar saídas de áudio de entrada de ficheiro de multimédia na mesma.

![Ligado Interleaver sequência de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Ligado Interleaver sequência de áudio*

Agora que temos uma sequência de áudio intercalada, podemos ainda não tiver especificar onde atribuir as posições de altifalante para a esquerda ou direita para. Para poder especificar isto, podemos pode tirar partido de Assigner de posição do orador.

![Adicionar um Assigner de posição de orador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Adicionar um Assigner de posição de orador*

Configure o Assigner de posição de orador para utilizar com uma sequência de entrada estéreo através de um filtro codificador de predefinir de "Personalizada" e o canal pré-configurar denominada "2.0 (L, R)". (Isto irá atribuir a posição de altifalante para a esquerda para canal 1 e a posição de orador à direita para canal 2.)

Ligar a saída de Assigner de posição de altifalante para a entrada do codificador AAC. Em seguida, onde está o codificador AAC para trabalhar com um "2.0 (L, R)" canal predefinidos, para que saiba-lo a lidar com áudio estéreo como entrada.

###<a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexing sequências de áudio e vídeo para um contentor MP4

Tendo em conta os nossos AVC codificada sequência de vídeo e os nossos AAC codificado sequência de áudio, podemos pode capturar ambos para um. Contentor de MP4. O processo de misturar sequências diferentes para um único chama-se "multiplexing" (ou "muxing"). Neste caso podemos estiver intercalada o áudio e as sequências de vídeo numa única coerente. Pacote de MP4. O componente que coordenadas este processo para uma. Contentor de MP4 chama Multiplexador a ISO MPEG-4. Adicionar um para a superfície de designer e ligue AVC vídeo codificador e descodificador a AAC a suas entradas do tipo.

![MPEG4 ligada Multiplexador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*MPEG4 ligada Multiplexador*

###<a id="MXF_to_MP4_writing_mp4"></a>Escrever o ficheiro de MP4

Quando escrever um ficheiro de exportação, é utilizado o componente de saída do ficheiro. Vamos pode ligar-se isto para o resultado de Multiplexador a ISO MPEG-4, de modo a que obtém escrita respectiva saída no disco. Para fazer isto, ligue o pin de saída do contentor (MPEG-4) para o pin de entrada de escrita de saída do ficheiro.

![Ligado saída do ficheiro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Ligado saída do ficheiro*

O nome do ficheiro que será utilizada é determinada pela propriedade do ficheiro. Enquanto essa propriedade pode ser codificado para um determinado valor, muito provavelmente uma pretende configurá-lo através de uma expressão.

Para o fluxo de trabalho determinar automaticamente o resultado da propriedade de nome a partir de uma expressão do ficheiro, clique em indisponível junto ao nome do ficheiro (junto ao ícone de pasta). No menu pendente, em seguida, selecione "Expressão". Isto apresentará o editor de expressões. Desmarcar o conteúdo do editor de pela primeira vez.

![Editor de expressão vazia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de expressão vazia*

O editor de expressões permite para introduzir um valor literal, misturado com uma ou mais variáveis. Variáveis começam com um sinal de dólar. À medida que de clicar na tecla $, o editor de irá mostrar uma caixa de lista pendente com a opção de variáveis disponíveis. No nosso caso vamos utilizar uma combinação da variável de diretório de saída e a variável de nome de ficheiro de entrada base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Preenchida saída do Editor de expressões](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Preenchida saída do Editor de expressões*

>[AZURE.NOTE]Para poder ver ver um ficheiro de saída da sua tarefa codificação no Azure, tem de fornecer um valor no editor de expressão. 

Quando a expressão confirme ao atingir ok, a janela de propriedade pré-visualizar para o valor a remonta de propriedade ficheiro neste ponto específico.

![Ficheiro expressão resultar em dir de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Ficheiro expressão resultar em dir de saída*

###<a id="MXF_to_MP4_asset_from_output"></a>Criar um activo de serviços de multimédia do ficheiro de exportação

Enquanto podemos tenha escrito um ficheiro de exportação MP4, precisamos ainda indicar que este ficheiro pertence a activo saída que irão gerar dos serviços de multimédia estatístico como resultado da execução deste fluxo de trabalho. Para o efeito, é utilizado o nó do ficheiro de saída/ativos na tela de fluxo de trabalho. Todos os ficheiros recebidos para este nó irão tornar parte de elemento dos serviços de multimédia do Azure resultante.

Ligue o componente de saída do ficheiro para o componente de ficheiro de saída/ativos para terminar o fluxo de trabalho.

![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fluxo de trabalho concluído*

###<a id="MXF_to_MP4_test"></a>Teste o fluxo de trabalho concluído localmente

Para testar o fluxo de trabalho localmente, clicar no botão Reproduzir na barra de ferramentas na parte superior. Quando terminar do fluxo de trabalho em execução, inspecionar a saída gerada na pasta saída configurado. Irá ver o ficheiro de exportação MP4 terminado que foi codificado do ficheiro de origem de entrada MXF.

##<a id="MXF_to_MP4_with_dyn_packaging"></a>Codificação MXF para MP4 - multibitrate embalagem dinâmica activado

Este tutorial vamos criar um conjunto de vários ficheiros de MP4 a velocidade com AAC codificado áudio a partir de um único. Ficheiro de entrada MXF.

Quando uma saída de elementos de velocidade multi é pretendida para utilização em combinação com as funcionalidades de embalagem dinâmico disponibilizadas pelos serviços de multimédia do Azure, vários ficheiros de serem alinhados à GOP MP4 de cada uma resolução e velocidade diferentes terá de ser gerado. Para fazê-lo, o tutorial de [Codificação MXF para uma única velocidade MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) fornece-nos com um bom ponto de partida.

![Iniciar fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Iniciar fluxo de trabalho*

###<a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Adicionar uma ou mais saídas MP4 adicionais

Todos os ficheiros de MP4 no nosso activo dos serviços de multimédia do Azure resultante irão suportar uma resolução e velocidade diferente. Vamos adicionar um ou mais ficheiros de saída MP4 ao fluxo de trabalho.

Para se certificar-se de que temos todos os nossos codificadores vídeos criadas com as mesmas definições, é mais prático duplicar codificador vídeo AVC já existente e configurar outra combinação de resolução e velocidade de bits (vamos adicionar um dos 960 x 540 25 frames por segundo Mbps 2,5). Para duplicar o codificador existente, cópia colá-lo na superfície da estruturador.

Ligue o pin de saída do vídeo não comprimidas da entrada de ficheiro multimédia nosso novo componente de AVC.

![Segundo codificador de AVC ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Segundo codificador de AVC ligado*

Agora adapta a configuração para o nosso novo codificador de AVC exportar 960 x 540 Mbps 2,5. (Utilize o propriedades "largura de saída", "Saída altura" e "Velocidade (kbps)" para que esta.)

Tendo em conta queremos utilizar o activo resultante juntamente com embalagem dinâmico dos serviços de multimédia do Azure, o ponto final de transmissão tem de ser capaz de gerar a partir destes ficheiros MP4 fragmentos HLS/Fragmented MP4/travessão que estão exatamente alinhados umas às outras de forma a que os clientes que estão a mudar entre diferentes bitrates obtêm uma experiência de áudio e vídeo de contínuo suave único. Para tornar que ocorrem, precisamos de Certifique-se de que, as propriedades de ambos os codificadores AVC a GOP ("grupo de imagens") tamanho para ambos os ficheiros de MP4 está definido para 2 segundos, pode ser efetuados por:

- definir o modo de tamanho GOP como tamanho fixo GOP e
- Intervalo de moldura a chave para dois segundos.
- também definir o controlo de IDR GOP fechado GOP para garantir que todos os GOP estiverem em repouso sobre as suas próprias sem dependências

Para tornar os nossos fluxo de trabalho conveniente para compreender, mudar o nome codificador AVC primeiro para "vídeo AVC codificador 640x360 1200kbps" e a segunda descodificador AVC "vídeo AVC codificador 960 x 540 2500 kbps".

Agora adicione um segundo ISO MPEG-4 Multiplexador e uma segunda saída do ficheiro. Ligar a Multiplexador ao novo codificador AVC e certifique-se de que respectiva saída é direccionada para a saída do ficheiro. Em seguida, também ligar entrada a AAC codificador de áudio saída para o novo do Multiplexador. A saída do ficheiro sucessivamente é, em seguida, possível ligar para o nó do ficheiro de saída/ativos para adicioná-lo ao activo de serviços de multimédia que será criado.

![Segundo misturador e saída do ficheiro ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Segundo misturador e saída do ficheiro ligado*

Para compatibilidade com embalagem dinâmica dos serviços de multimédia do Azure, configure fragmento de modo o Multiplexador a contagem de GOP ou a duração e definido GOPs por segmento como 1. (Este deverá predefinido.)

![Modos de segmento misturador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos de segmento misturador*

Nota: poderá pretende repetir o processo para quaisquer outras velocidade e resolução combinações que pretende ter adicionadas à saída ativo.

###<a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurar os nomes de saída do ficheiro

Temos mais do que um único ficheiro adicionado ao activo de saída. Este procedimento fornece uma necessidade de Certifique-se dos que nomes de ficheiro para cada um dos ficheiros de saída são diferentes uns dos outros e talvez mesmo aplicar uma convenção de nomenclatura para que se torne limpar do nome do ficheiro está a trabalhar com.

Atribuir nomes a saída do ficheiro podem ser controlado através de expressões no estruturador de. Abrir o painel de propriedade para um dos componentes de saída do ficheiro e abra o editor de expressão para a propriedade do ficheiro. Os nossos primeiro ficheiro de exportação tiver sido configurado através da expressão seguinte (consulte o tutorial para passar da [MXF para uma única velocidade saída MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Isto significa que o nosso filename é determinada pela duas variáveis: o diretório de saída para escrever no e o nome de base de ficheiro de origem. O antigo exposto como uma propriedade na raiz de fluxo de trabalho e o último é determinado pela ficheiro de entrada. Repare que o directório de saída está a utilizar para testar a local; Esta propriedade será substituída pelo motor de fluxo de trabalho quando o fluxo de trabalho é executado pelo processador baseado na nuvem multimédia nos serviços de multimédia do Azure.
Para dar ambos os nossos ficheiros de saída de uma atribuição de nomes de saída consistente, altere o primeiro ficheiro atribuir nomes a expressão para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e o segundo para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Execute um teste intermédio executar para se certificar de que ambos os ficheiros de saída MP4 são gerados corretamente.

###<a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Adicionar uma faixa de áudio em separado

Como vamos ver mais tarde quando podemos gerar um ficheiro de .ism para condizer com os nossos ficheiros de saída MP4, recomendamos também necessitam um ficheiro de MP4 só de áudio como a faixa de áudio para os nossos transmissão ajustável. Para criar este ficheiro, adicione uma misturador adicional para o fluxo de trabalho (Multiplexador ISO-MPEG-4) e ligue pino de saída o codificador AAC com o seu pin de entrada para registar 1.

![Áudio misturador adicionado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Áudio misturador adicionado*

Crie um terceiro componente de saída do ficheiro para a sequência de saída a partir do misturador de saída e configurar o ficheiro atribuir nomes a expressão como:
    
    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Áudio misturador criação de saída do ficheiro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Áudio misturador criação de saída do ficheiro*

###<a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Adicionar a. Ficheiro SMIL ISM

Para a embalagem dinâmica trabalhar em conjunto com MP4 ficheiros (tanto o MP4 só de áudio) no nosso elementos dos serviços de multimédia, recomendamos também precisa de um ficheiro de manifesto (também designado por um ficheiro de "SMIL": linguagem de integração de multimédia sincronizados). Este ficheiro indica a serviços de multimédia do Azure que ficheiros MP4 estão disponíveis para embalagem dinâmica e quais da ter em conta para a transmissão de áudio. Um ficheiro de manifesto típico de um conjunto do MP4 com uma única sequência de áudio tem o seguinte aspeto:
    
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

O ficheiro de .ism contém dentro de uma instrução de parâmetro, uma referência a cada um dos ficheiros de vídeo MP4 individuais e além essas referências de ficheiro de áudio também um (ou mais) para um MP4 que apenas contém o áudio.

Gerar o ficheiro de manifesto para os nossos conjunto do MP4 pode ser feito através de um componente denominado o "AMS manifesto Writer". Para utilizá-la, arraste-o para a superfície e ligar o PIN de saída "Escrever completa" a partir de três componentes de saída do ficheiro para a entrada de AMS manifesto sénior. Em seguida, certifique-se ligar a saída do gravador de manifesto AMS para saída ficheiro/ativo.

Tal como acontece com os nossos outros ficheiro saída componentes, configure o nome de saída do ficheiro .ism com uma expressão:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Os nossos fluxo de trabalho concluído o aspeto de abaixo:

![MXF terminado ao fluxo de trabalho do multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF terminado ao fluxo de trabalho do multibitrate MP4*

##<a id="MXF_to__multibitrate_MP4"></a>Codificação MXF para multibitrate MP4 - plano melhorado

No [Tutorial de fluxo de trabalho anterior](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) podemos visualizou como um único activo entrado MXF pode ser convertido um ativo de saída com multi velocidade MP4 ficheiros, de um ficheiro de MP4 só de áudio e de um ficheiro de manifesto para utilização em conjunto com embalagem dinâmico dos serviços de multimédia do Azure.

Este tutorial irá mostrar como alguns dos aspectos podem ser melhorado e efetuadas mais conveniente.

###<a id="MXF_to_multibitrate_MP4_overview"></a>Descrição geral de fluxo de trabalho para melhorar

![Multibitrate MP4 fluxo de trabalho para melhorar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Multibitrate MP4 fluxo de trabalho para melhorar*

###<a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenções de nomenclatura de ficheiros

No fluxo de trabalho anterior podemos especificados uma expressão simples como base para gerar nomes de ficheiro de exportação. Temos alguma duplicação apesar: todas as os componentes de ficheiro de exportação individuais especificado como expressão.

Por exemplo, o nosso componente de saída do ficheiro para o primeiro ficheiro de vídeo está configurado com esta expressão:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Enquanto para a segunda saída vídeo, temos uma expressão como:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Não seria mais limpo menos erro sujeito e mais conveniente se pode remover algumas desta duplicação e fazer coisas mais configuráveis em vez disso? Felizmente podemos: capacidades de expressão no estruturador em conjunto com a capacidade de criar propriedades personalizadas no nosso raiz de fluxo de trabalho irão dar-numa camada adicionada de conveniência.

Vamos assumir que podemos irá unidade configuração do nome de ficheiro a partir do bitrates dos ficheiros MP4 individuais. Estes bitrates que podemos irá destinam-se para configurar num único local central (da raiz da nossa graph), a partir de onde irá ser acedidos para configurar e geração de nome de ficheiro de unidade. Para fazer isto, vamos começar publicando propriedade velocidade de ambos os codificadores AVC na raiz da nossa fluxo de trabalho, para que se torne acessível a partir de ambas as a raiz, assim como as codificadores AVC. (Mesmo se apresentado em dois pontos diferentes, existe apenas um valor subjacente.)

###<a id="MXF_to__multibitrate_MP4_publishing"></a>Propriedades do componente de publicação para a raiz de fluxo de trabalho

Abra o codificador AVC primeiro, vá para a propriedade de velocidade (kbps) e na lista pendente, selecione publicar.

![A propriedade de velocidade de publicação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*A propriedade de velocidade de publicação*

Configure a caixa de diálogo Publicar para publicar na raiz da nossa grafo do fluxo de trabalho, com um nome publicado de "video1bitrate" e um nome a apresentar legíveis da "Vídeo 1 velocidade". Configurar um personalizado nome do grupo designado "Transmissão Bitrates" e visitas publicar.

![A propriedade de velocidade de publicação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Caixa de diálogo publicação para a propriedade de velocidade*

Repetir a mesma para a propriedade de velocidade do segundo codificador AVC e o nome "video2bitrate" com um nome a apresentar de "Vídeo 2 velocidade de bits", no grupo personalizado mesmo "Transmissão Bitrates".

Se agora, vamos inspecionar as propriedades de raiz do fluxo de trabalho, vamos ver os nossos grupo personalizado com as propriedades de publicados duas aparecer. Ambos estão refletir o valor da sua velocidade de codificador AVC respetivos.

![Props velocidade publicados na raiz de fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Sempre que queremos aceder a estas propriedades de código ou de uma expressão, podemos pode fazê-lo da seguinte forma:

- a partir do código inline a partir de um componente imediatamente abaixo da raiz: node.getPropertyAsString('.. / video1bitrate', nulo)
- dentro de uma expressão: ${ROOT_video1bitrate}
 
Vamos completar o grupo de "Transmissão Bitrates" publicando nossa velocidade de pista de áudio no mesmo assim. A partir de propriedades do codificador AAC, procure a definição de velocidade e selecione publicar na lista pendente junto ao mesmo. Publicar na raiz da graph com o nome "audio1bitrate" e apresentar o nome "Velocidade de 1 de áudio" no nosso grupo personalizado "Transmissão Bitrates".

![Caixa de diálogo publicação de velocidade de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Caixa de diálogo publicação de velocidade de áudio*

![Props resultantes de áudio e vídeo na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Props resultantes de áudio e vídeo na raiz*

Tenha em atenção que a alteração de qualquer uma das três valores também novamente configura e altera os valores nos respetivos componentes que estão ligadas com (e onde publicada a partir do).

###<a id="MXF_to__multibitrate_MP4_output_files"></a>Tiver gerado o ficheiro de exportação nomes dependem de valores de propriedade publicados

Em vez de hardcoding nossos nomes de ficheiro gerado, vamos agora pode alterar os nossos expressão de nome de ficheiro em cada uma dos componentes de saída do ficheiro para depender as propriedades de velocidade que acabou de ser publicados na raiz do gráfico. Começar com os nossos primeira saída do ficheiro, localize a propriedade de ficheiro e editar a expressão da seguinte forma:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Os diferentes parâmetros nesta expressão podem ser acedidos e introduzidos pelo atingir o cifrão no teclado enquanto na janela da expressão. É um dos parâmetros disponíveis nossa propriedade video1bitrate que recomendamos publicados anteriormente.

![Aceder a parâmetros dentro de uma expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Aceder a parâmetros dentro de uma expressão*

Faça o mesmo para a saída de ficheiro para o nosso vídeo segunda:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e para a saída de ficheiros só de áudio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se agora, vamos alterar a velocidade de bits para qualquer um dos ficheiros de áudio ou vídeo, a reconfigurar o codificador respetivos e Convenção de nome de ficheiro baseado em velocidade será respeitada todas as automática.

##<a id="thumbnails_to__multibitrate_MP4"></a>Adicionar miniaturas a multibitrate saída MP4

Começar a partir de um fluxo de trabalho que gera [um multibitrate MP4 saída a partir de um MXF de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos será agora estar a procurar para adicionar miniaturas para o resultado.

###<a id="thumbnails_to__multibitrate_MP4_overview"></a>Descrição geral de fluxo de trabalho para adicionar miniaturas para

![Multibitrate MP4 fluxo de trabalho para iniciar a partir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4 fluxo de trabalho para iniciar a partir*

###<a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Adicionar a codificação de JPG

Coração da nossa geração miniatura será o componente de JPG codificador conseguir JPG ficheiros de saída.

![Descodificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Descodificador JPG*

Diretamente no entanto podemos não é possível ligar os nossos sequência de vídeo não comprimidas a partir da entrada de ficheiro de multimédia para o codificador JPG. Em vez disso, espera entregue frames individuais. Esta ação, podemos fazer através do componente de porta de fotograma de vídeo.

![Ligar uma porta de moldura ao codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ligar uma porta de moldura ao codificador JPG*

A porta de moldura, uma vez por cada tantos segundos ou molduras permite um fotograma de vídeo passar. O intervalo e o tempo de desfasamento com que isto acontece é configurável nas propriedades.

![Propriedades de porta fotograma de vídeo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriedades de porta fotograma de vídeo*

Vamos criar uma miniatura minuto ao definir o modo como o tempo (segundos) e o intervalo para 60.

###<a id="thumbnails_to__multibitrate_MP4_color_space"></a>Trabalhar com a conversão de espaço de cor

Enquanto seria parecer lógico que agora pode ser ligado ambos os PIN de vídeo não comprimidas da porta de moldura e a entrada de ficheiro de multimédia, podemos obteria um aviso se podemos seria fazê-lo.

![Erro de espaço de cor de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erro de espaço de cor de entrada*

Isto acontece porque a forma na qual cor informações são representadas no nossa original não processada não comprimida sequência de vídeo, provenientes do nosso MXF, é a diferença entre o que o codificador JPG está à espera. Mais especificamente, um chamados "cor espaço em" de "RGB" ou "Tons de cinzento" é suposto fluxo no. Isto significa que sequência de vídeo entrada da porta moldura vídeo será necessário terem uma conversão aplicada relativamente ao seu espaço de cores em primeiro lugar.

Arraste para o fluxo de trabalho conversor de espaço cor - Intel e ligá-lo à nossa porta de moldura.

![Ligar um conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Ligar um conversor de espaço de cor*

Na janela de propriedades, selecione a entrada de BGR 24 a partir da lista de predefinido.

###<a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Escrever as miniaturas

Diferente do nosso vídeo MP4, o componente JPG codificador será de saída de mais de um ficheiro. Para poder lidar com este problema, pode ser utilizado um componente de cena pesquisa JPG ficheiro sénior:-vai levar as miniaturas JPG recebidas e escrevê-los saída, cada nome de ficheiro a ser com o sufixo por um número diferente. (O número normalmente que indica o número de segundos/unidades na sequência do qual foi desenhada a miniatura da.)


![Introdução ao gravador de ficheiro a cena pesquisa JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introdução ao gravador de ficheiro a cena pesquisa JPG*

Configurar a propriedade do caminho da pasta de saída com a expressão: ${ROOT_outputWriteDirectory} 

e a propriedade nome do ficheiro prefixo com: 

    ${ROOT_sourceFileBaseName}_thumb_

O prefixo vai determinar como os ficheiros miniaturas estão a ser com o nome. Estes serão terá o sufixo com um número que indica a posição a miniatura na sequência.


![Propriedades de pesquisa JPG ficheiro sénior cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriedades de pesquisa JPG ficheiro sénior cena*

Ligar a cena pesquisa JPG ficheiro sénior para o nó do ficheiro de saída/ativos.

###<a id="thumbnails_to__multibitrate_MP4_errors"></a>Detetar erros num fluxo de trabalho

Ligar a entrada do conversor do espaço de cor a observou não comprimida saída de vídeo. Efetue agora um teste local de execução do fluxo de trabalho. Não existe a oportunidade de boa o fluxo de trabalho inesperadamente parar a execução e indicar com um contorno vermelho no componente que encontrou um erro:

![Erro de espaço conversor de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erro de espaço conversor de cor*

Clique no pequeno ícone de "E" vermelho no canto superior direito de canto do componente conversor de espaço de cor para ver o que é o motivo a tentativa de codificação falhou.

![Caixa de diálogo de erro de espaço conversor de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Caixa de diálogo de erro de espaço conversor de cor*

Desativa saída, como pode ver, que o espaço de cor recebidas padrão para que o conversor de espaço de cor tem de ser rec601 para os nossos pedida conversão de YUV RGB. Aparentemente o nossa sequência não indica do rec601. (Rec 601 é uma norma de codificação entrelaçados sinais de vídeos analógicos no formulário de vídeo digital. Especifica uma região activa abrangendo 720 luminosidade amostras e 360 chrominance amostras por linha. A cor de codificação de sistema é conhecida como YCbCr 4:2:2.)

Para corrigir isto, irá indicamos nos metadados da nossa sequência que recomendamos está a trabalhar com conteúdo rec601. Para fazê-lo vamos utilizar um componente do QuickBooks de tipo de dados de vídeo, vamos irá colocar entre os nossos origem não processada e o componente de conversão de espaço de cor. Este QuickBooks de tipo de dados permite que a atualização manual de determinados dados vídeos propriedades tipo. Configure-o para indicar um padrão de espaço de cor de "Rec 601". Isto vai originar a QuickBooks de tipo de dados de vídeo marcar a sequência com o espaço de cor "Rec 601" se não tiver sem espaço de cor ainda definido. (Não substituirá quaisquer metadados existente, a menos que a caixa de verificação Ignorar deu.)

![Atualizar o espaço de cor padrão no QuickBooks de tipo de dados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Atualizar o espaço de cor padrão no QuickBooks de tipo de dados*

###<a id="thumbnails_to__multibitrate_MP4_finish"></a>Fluxo de trabalho concluído

Agora que os nossos nosso fluxo de trabalho for concluído, faça outro teste executar para vê-lo a passar.

![Fluxo de trabalho concluído para multi mp4 saída com miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fluxo de trabalho concluído para multi mp4 saída com miniaturas*

##<a id="time_based_trim"></a>Limitação por motivos baseados no tempo de saída multibitrate MP4

Começar a partir de um fluxo de trabalho que gera [um multibitrate MP4 saída a partir de um MXF de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos será agora ser está à procura para limitar o vídeo de origem com base em carimbos de data / hora.

###<a id="time_based_trim_start"></a>Descrição geral de fluxo de trabalho para começar a adicionar limitação por motivos para

![Iniciar fluxo de trabalho para adicionar a limitação por motivos para](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Iniciar fluxo de trabalho para adicionar a limitação por motivos para*

###<a id="time_based_trim_use_stream_trimmer"></a>Utilizar o Recortador da cadeia

O componente de sequência Recortador permite cortar início e fim de uma sequência de entrada base temporiza informações (segundos, minutos,...). O Recortador não suporta a limitação por motivos de baseada na moldura.

![Recortador de sequência](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Recortador de sequência*

Em vez de ligar a codificadores AVC e assigner de posição de altifalante para a entrada de ficheiro de multimédia diretamente, podemos irá colocar entre aqueles a Recortador da cadeia. (Um para o sinal de vídeo e para o sinal de áudio intercalado.)

![Colocar Recortador da cadeia entre](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Colocar Recortador da cadeia entre*

Vamos configurar a Recortador para que recomendamos apenas processará vídeo e áudio entre 15 segundos e 60 segundos no vídeo.

Vá para as propriedades de Recortador de sequência de vídeo e configurar a hora de início (menores de 15 anos) e propriedades de hora de fim (60s). Para se certificar-se de que tanto o nosso Recortador de áudio e vídeo sempre está configurado para a mesma início e fim valores, podemos irá publicar aqueles na raiz do fluxo de trabalho.

![Publicar a propriedade de hora de início a partir da cadeia Recortador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicar a propriedade de hora de início a partir da cadeia Recortador*

![Publicar o diálogo de propriedade para hora de início](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicar o diálogo de propriedade para hora de início*

![Publicar o diálogo de propriedade para hora de fim](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publicar o diálogo de propriedade para hora de fim*


Se agora, vamos inspecionar na raiz da nossa fluxo de trabalho, ambas as propriedades será uma forma ordenada apresentados e configuráveis a partir daí.

![Publicado propriedades disponíveis na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicado propriedades disponíveis na raiz*

Agora, abra as propriedades de limitação por motivos de Recortador de áudio e configurar horas de início e fim com uma expressão que refere-se para as propriedades do publicados na raiz da nossa fluxo de trabalho.

Para a hora de início de áudio limitação por motivos:

    ${ROOT_TrimmingStartTime}

e para a hora de fim:

    ${ROOT_TrimmingEndTime}

###<a id="time_based_trim_finish"></a>Fluxo de trabalho concluído

![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fluxo de trabalho concluído*


##<a id="scripting"></a>Introdução ao componente de script

Componentes de script podem executar scripts arbitrários durante a fase de execução de nossos fluxo de trabalho. Existem quatro scripts diferentes que podem ser executadas, cada uma com características específicas e os seus próprios colocar o ciclo de vida do fluxo de trabalho:

- **commandScript**
- **realizeScript**
- **processInputScript**
- **lifeCycleScript**

A documentação do componente de script vai mais detalhadamente para cada um dos acima. Na [secção seguinte](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), o componente de scripts **realizeScript** é utilizado para construir um xml cliplist no momento, quando o fluxo de trabalho for iniciado. Este script chama-se durante a configuração de componente, o que acontece apenas uma vez no ciclo de vida do mesmo.


###<a id="scripting_hello_world"></a>Scripting dentro de um fluxo de trabalho: Olá a todos

Arraste um componente de script para a superfície de designer e mude o nome (por exemplo, "SetClipListXML").

![Adicionar um componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente de script*

Quando inspecionar as propriedades do componente de script, os quatro tipos diferentes de script será apresentado, cada configuráveis para um script diferente.

![Propriedades do componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente de script*

Desmarque a processInputScript e abra o editor para a realizeScript. Agora podemos estão configurados e pronto para iniciar o scripting.

Scripts sejam escritos em Groovy, uma linguagem de scripts dinamicamente compilada para a plataforma Java que mantém a compatibilidade com Java. Na verdade, a maior parte dos código Java é código Groovy válido.

Vamos escrever um script de groovy mundo Olá simples no contexto da nossa realizeScript. No editor, introduza o seguinte:

    node.log("hello world");

Execute agora executar um teste local. Após a execução deste inspecionar (através do separador de sistema no componente de script) a propriedade de registos.

![Resultado do registo de Olá mundo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Resultado do registo de Olá mundo*

O objeto de nó chamamos o método de registo, que se refere a nossa "nó" atual ou o componente podemos estiver scripting em. Todos os componentes tem, como tal, a capacidade de dados do registo de saída, disponível através do separador de sistema. Neste caso, podemos saída o literal de cadeia "Olá". Importante para compreender aqui é que isto pode revelar-se para ser uma ferramenta de depuração inestimável, fornecendo-lhe conhecimentos aprofundados sobre o que o script é realmente a fazer.

A partir no nosso ambiente scripting, também temos acesso às propriedades no outros componentes. Experimente o seguinte:


    //inspect current node: 
    def nodepath = node.getNodePath(); 
    node.log("this node path: " + nodepath);
    
    //walking up to other nodes: 
    def parentnode = node.getParentNode(); 
    def parentnodepath = parentnode.getNodePath(); 
    node.log("parent node path: " + parentnodepath);
    
    //read properties from a node: 
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null ); 
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null); 
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

Janela nosso registo irá mostrar-no seguinte procedimento:

![Registo de saída para aceder a caminhos de nó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Registo de saída para aceder a caminhos de nó*


##<a id="frame_based_trim"></a>Baseado na moldura limitação por motivos de saída multibitrate MP4

Começar a partir de um fluxo de trabalho que gera [um multibitrate MP4 saída a partir de um MXF de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos será agora ser está à procura para limitar o vídeo de origem com base na moldura contagens.

###<a id="frame_based_trim_start"></a>Descrição geral de plano para começar a adicionar limitação por motivos para

![Fluxo de trabalho para começar a adicionar limitação por motivos para](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Fluxo de trabalho para começar a adicionar limitação por motivos para*

###<a id="frame_based_trim_clip_list"></a>Utilizar a lista de clips XML

Todos os tutoriais anterior do fluxo de trabalho, utilizámos o componente de entrada de ficheiro de multimédia como nossa vídeo origem de entrada. Para este cenário específico no entanto, iremos utilizar o componente origem da lista de ClipArt em vez disso. Tenha em atenção que esta não deve ser a forma como preferida de trabalho; utilizar apenas a origem de lista de ClipArt quando existe um motivo real para fazê-lo (como na abaixo caso, onde está a efetuar podemos utilização das capacidades de limitação por motivos de lista clip).

Para mudar da nossa entrada de ficheiro de multimédia à origem de lista de ClipArt, arraste o componente origem da lista de ClipArt para superfície da estrutura e ligue o pin do Clip lista XML para o nó do Clip lista XML do estruturador de fluxo de trabalho. Isto deve preencher a origem de lista de ClipArt com o PIN de saída, de acordo com os nossos vídeo de entrada. Ligar-se o PIN não comprimidas vídeo e áudio não comprimidas a partir da a origem de lista de ClipArt para os respetivos AVC codificadores e Interleaver de sequência de áudio. Agora, remova a entrada de ficheiro de multimédia.

![Substituído a entrada de ficheiro de multimédia com a origem da lista de ClipArt](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Substituído a entrada de ficheiro de multimédia com a origem da lista de ClipArt*

O componente origem da lista de Clip leva como a respectiva entrada um "Clip lista XML". Quando selecionar o ficheiro de origem para testar com localmente, este xml de lista de ClipArt é preenchido automaticamente para si.

![Propriedade de Clip lista XML preenchido automaticamente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriedade de Clip lista XML preenchido automaticamente*

Está à procura um pouco mais perto xml, este é como aspeto:

![Caixa de diálogo do clip lista editar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Caixa de diálogo do clip lista editar*

Isto no entanto não refletir as funcionalidades do clip lista xml. Uma das opções que temos é adicionar um elemento de "Cortar" em ambos os o vídeo e áudio origem, da seguinte forma:

![Adicionar um elemento compactar à lista de ClipArt](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Adicionar um elemento compactar à lista de ClipArt*

Se modificar o xml de lista clip assim acima e executar um teste local executar, irá ver o vídeo corretamente foi cortada entre 10 e 20 segundos no vídeo.

Contrariamente ao que acontece quando o fizer executar um local no entanto, esta xml cliplist muito mesmo não teria o mesmo efeito quando aplicados num fluxo de trabalho é executado dos serviços de multimédia do Azure. Quando inicia o Azure Premium codificador, cliplist xml é gerado sempre novamente, com base no ficheiro de entrada a tarefa de codificação que foi fornecida. Isto significa que as alterações que recomendamos fazer xml Infelizmente seriam sobrepostas.

Para combater o xml cliplist a ser limpeza seletiva quando uma tarefa de codificação for iniciada, podemos pode voltar gerá-lo no momento imediatamente após o início da nossa fluxo de trabalho. Podem ser tomadas essas ações personalizadas através do que é designado por um "componente de script". Para mais informações, consulte o artigo [Introdução ao componente de script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).


Arraste um componente de script para a superfície estruturador e mude o nome para a "SetClipListXML".

![Adicionar um componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente de script*

Quando inspecionar as propriedades do componente de script, os quatro tipos diferentes de script será apresentado, cada configuráveis para um script diferente.

![Propriedades do componente de script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente de script*


###<a id="frame_based_trim_modify_clip_list"></a>Modificar a lista de ClipArt a partir de um componente de script

Antes de voltar podemos pode escrever xml cliplist gerado durante o arranque do fluxo de trabalho, irá precisamos de tem acesso a propriedade de xml cliplist e o conteúdo. Vamos pode fazê-lo da seguinte forma:

    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Lista de clips recebidas a ser tem sessão iniciada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clips recebidas a ser tem sessão iniciada*

Em primeiro lugar precisamos de uma forma para determinar a partir do qual ponto até que ponto queremos cortar o vídeo. Para tornar este conveniente ao utilizador menos técnica do fluxo de trabalho, publica duas propriedades na raiz do gráfico. Para executar esta tarefa, clique com o botão direito do rato em superfície de estruturador e selecione "Adicionar propriedade":

- Primeira propriedade: "ClippingTimeStart" do tipo de: "Código de hora"
- Segundo propriedade: "ClippingTimeEnd" do tipo de: "Código de hora"

![Adicionar a caixa de diálogo de propriedade para hora de início de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adicionar a caixa de diálogo de propriedade para hora de início de recorte*

![Publicado recorte props de tempo na raiz de fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicado recorte props de tempo na raiz de fluxo de trabalho*

Configure ambas as propriedades para um valor adequado:

![Configurar o início de recorte e de fim propriedades](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurar o início de recorte e de fim propriedades*

Agora, a partir de dentro nosso script, podemos pode aceder a ambas as propriedades, da seguinte forma:

    
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Janela de registo com início e de fim de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Janela de registo com início e de fim de recorte*

Vamos analisar as cadeias de código de hora para um mais conveniente para utilizar o formulário, utilizando uma expressão regular simple:
    
    //parse the start timing: 
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1); 
    node.log("timecode start is: " + starttimecode); 
    def startframerate = startregresult.group(2); 
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend); 
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2); 
    node.log("framerate end is: " + endframerate);

![Janela de registo com a saída de código de hora analisado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Janela de registo com a saída de código de hora analisado*

Estas informações à mão, vamos agora pode modificar xml cliplist para refletir as horas de início e de fim para o recorte de precisão de moldura pretendida do filme.

![Código de scripts para adicionar elementos compactar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código de scripts para adicionar elementos compactar*

Isto foi feito através de operações de manipulação de cadeia normal. Clip modificado lista xml resultante é escrito novamente para a propriedade clipListXML na raiz de fluxo de trabalho através do método "DefinirPropriedade". A janela de registo depois de executar o outro teste seria nos mostre o seguinte procedimento:

![Registo da lista de clips resultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registo da lista de clips resultante*

Faça um teste-use para ver como as sequências de vídeo e áudio tem sido cortadas. À medida que vai fazer mais do que um teste-use com valores diferentes para os pontos de limitação por motivos, vai notar que aqueles irão não ser tidos em conta no entanto! O motivo para isto é que o estruturador, ao contrário de tempo de execução Azure, não substitui cliplist xml cada executar. Isto significa que só muito pela primeira vez que definiu a sessão e terminar pontos, originará xml transformar, todas as outras horas, os nossos cláusula atento (se (clipListXML.indexOf ("<trim>") = = -1)) irá impedir o fluxo de trabalho de adicionar outro elemento compactar quando já existe um apresentar.

Para tornar os nossos fluxo de trabalho conveniente para testar localmente, podemos melhor adicionar algum código de arquivamento de gengibre inspeciona se um elemento compactar já estava a apresentar. Se Sim, podemos pode removê-lo antes de continuar, alterando o xml com os novos valores. Em vez de utilizar manipulações cadeia simples, é provavelmente mais seguro fazê-lo através do modelo de objeto de real xml de análise.

Antes de podemos pode adicionar esse código apesar, irá precisamos de adicionar um número de declarações de importar no início da nossa script pela primeira vez:
    
    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;

Após esta ação, iremos pode adicionar o código de limpeza necessário:

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already: 
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes: 
    elementsToDelete.each{ 
        e -> e.getParentNode().removeChild(e);
    }; 
    node.log("deleted any existing trim nodes");
    
    //serialize the modified clip list xml dom into a string: 
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result); 
    clipListXML = result.getWriter().toString();
    
Este código vai apenas acima do ponto no qual recomendamos adicionar os elementos Compactar ao cliplist xml.

Neste momento, vamos executar e modificar os nossos fluxo de trabalho como tanto quanto queremos enquanto está a ter nunca aplicar as alterações de tempo.    

###<a id="frame_based_trim_clippingenabled_prop"></a>Adicionar uma propriedade de conveniência ClippingEnabled

À medida que poderá não sempre pretende limitar a ocorrer, vamos conclusão desativar os nossos fluxo de trabalho ao adicionar um sinalizador conveniente booleano que indica se pretende ou não podemos ativar limitar / recorte.

Tal como antes, publicar uma nova propriedade na raiz da nossa fluxo de trabalho denominado "ClippingEnabled" de BOOLEANO"tipo".

![Publicado uma propriedade de ativação de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicado uma propriedade de ativação de recorte*

Com a abaixo cláusula atento simples, vamos pode verificar se limitação por motivos for necessária e decidir se nossa lista de clips como tal necessita de ser alterada ou não.

    //check if clipping is required: 
    def clippingrequired = node.getProperty("../ClippingEnabled"); 
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML); 
        node.log("no clipping required"); 
        return; 
    }


###<a id="code"></a>Código completo

    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;
    
    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping: 
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);
    
    //for local testing: delete any pre-existing trim elements 
    //from the clip list xml by parsing the xml into a DOM:
    
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder(); 
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath(); 
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }
    
    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e -> 
        e.getParentNode().removeChild(e); 
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return; 
    }

    //add trim elements to cliplist xml 
    if ( clipListXML.indexOf("<trim>") == -1 ) 
    {
        //trim video 
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode + 
            " </outPoint>\n </trim> \n"); 
        //trim audio 
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" + 
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML ); 
        node.setProperty("../clipListXml",clipListXML); 
    }


##<a name="also-see"></a>Consulte também 

[Introdução ao Premium codificação de multimédia do Microsoft Azure serviços](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como utilizar Premium codificação de multimédia do Microsoft Azure serviços](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificação de conteúdo a pedido com o serviço de multimédia do Microsoft Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

[Codecs e formatos de fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Ficheiros de fluxo de trabalho de exemplo](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta de Explorador dos serviços de multimédia do Azure](http://aka.ms/amse)

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
