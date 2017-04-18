<properties 
    pageTitle="Configurar o codificador Telestream Wirecast para enviar um fluxo direto de velocidade única | Microsoft Azure" 
    description="Este tópico mostra como configurar o codificador direto Wirecast para enviar uma sequência de velocidade único para canais AMS que estejam ativados para codificação direto. " 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako;cenkdin;anilmur"/>

#<a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Utilizar o codificador Wirecast para enviar uma sequência de velocidade única em directo

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elemento direto](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Este tópico mostra como configurar o codificador direto [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) para enviar uma sequência de velocidade único para canais AMS que estejam ativados para codificação direto.  Para mais informações, consulte o artigo [trabalhar com os canais que estão activados para executar Live codificação com dos serviços de multimédia do Azure](media-services-manage-live-encoder-enabled-channels.md).

Este tutorial mostra como gerir serviços de multimédia do Azure (MGA) com a ferramenta do Explorador de serviços de multimédia do Azure (AMSE). Esta ferramenta apenas é executada no PC com Windows. Se estiver no Mac ou Linux, utilize o Azure portal para criar [canais](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) e [programas](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).


##<a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de serviços de multimédia do Azure](media-services-portal-create-account.md)
- Certifique-se de que existe um transmissão ponto final de executar com, pelo menos, uma unidade de transmissão atribuída. Para obter mais informações, consulte o artigo [Gerir transmissão pontos finais numa conta de serviços de multimédia](media-services-portal-manage-streaming-endpoints.md)
- Instale a versão mais recente da ferramenta de [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Inicie a ferramenta e ligar à sua conta AMS.

##<a name="tips"></a>Sugestões

- Sempre que possível, utilize uma ligação à internet de ligada por cabo.
- É uma boa regra útil quando determinar requisitos de largura de banda dupla a transmissão bitrates. Enquanto não é um requisito obrigatório, irá ajudar a mitigar o impacto de tráfego de rede.
- Quando utilizar o software baseado codificadores, feche a saída dos programas desnecessários.


## <a name="create-a-channel"></a>Criar um canal

1.  Na ferramenta de AMSE, navegue para o separador **Live** e, clique com o botão direito do rato dentro da área de canal. Selecione **Criar canal...** a partir do menu.

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Especificar um nome para o canal, descrição o campo é opcional. Em definições de canal, selecione **padrão** para a opção Live codificação, com o protocolo de entrada definido para **RTMP**. Pode deixar todas as outras definições como está.


Certifique-se de **Iniciar o novo canal agora** está selecionada.

3. Clique em **Criar canal**.
![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] O canal pode demorar 20 minutos para começar.

Enquanto está a iniciar o canal, pode [configurar o codificador](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT] Tenha em atenção que faturação é iniciada assim que canal informações compõem um estado preparado. Para mais informações, consulte o artigo [Estados do canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Configurar o codificador Telestream Wirecast

Neste tutorial são utilizadas as seguintes definições de saída. Os restantes esta secção descreve os passos de configuração mais detalhadamente. 

**Vídeo**:
 
- Codec: h. 264 
- Perfil: Alta (nível 4.0) 
- Velocidade: kbps 5000 
- Fotograma-chave: 2 segundos (60 segundos) 
- Taxa de moldura: 30
 
**Áudio**:

- Codec: AAC (LC) 
- Velocidade: 192 kbps 
- Taxa de exemplo: 44.1 kHz


###<a name="configuration-steps"></a>Passos de configuração

1. Abra a aplicação de Telestream Wirecast na máquina que está a ser utilizado e configurar o para a transmissão de RTMP.
2. Configure o resultado ao navegar para o separador **saída** e ao selecionar **Definições de saída...**.
    
    Certifique-se que o **Destino de saída** estiver definido para **RTMP servidor**.
3. Clique em **OK**.
4. Na página Definições, defina o campo de **destino** para ser **Dos serviços de multimédia do Azure**.
 
    O perfil de codificação é previamente selecionado para **H. 264 de Azure 720 p 16:9 (1280x720)**. Para personalizar estas definições, selecione o ícone de engrenagem à direita da lista pendente para baixo e, em seguida, selecione **Predefinir novo**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Configure predefinições codificador.

    Atribua um nome a configuração predefinida e verificar a existência de definições recomendados seguintes procedimentos:

    **Vídeo**
    
    - Descodificador: MainConcept h. 264
    - Molduras por segundo: 30
    - Taxa de transmissão de média: 5000 kbits/sec (podem ser ajustados com base no limitações de rede)
    - Perfil: principais
    - Fotograma chave cada: 60 molduras

    **Áudio**

    - Vel de destino: 192 kbits/seg
    - Taxa de exemplo: 44.100 kHz
     
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Prima **Guardar**.

    O campo de codificação tem agora o perfil recentemente criado estão disponíveis para seleção. 

    Certifique-se de que o novo perfil está seleccionado.

7. Obter o URL de entrada do canal para poder atribuí-lo a Wirecast **Ponto final de RTMP**.
    
    Navegar de volta para a ferramenta AMSE e verificar o estado de conclusão de canal. Assim que o estado foi alterado a partir do **início** **em execução**, pode obter o URL de entrada.
      
    Quando o canal está em execução, clique com o botão direito no nome do canal, navegue para baixo até ao pairar sobre **URL de entrada de copiar para área de transferência** e, em seguida, selecione **URL da entrada principal**.  
    
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Na janela Wirecast **Definições de saída** , cole esta informação no campo **endereço** da secção de saída e atribua um nome da cadeia. 


    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Clique **em OK**.

10. No ecrã principal do **Wirecast** , confirme origens de entrada para vídeo e áudio estão prontas e, em seguida, premir **sequência** no canto superior esquerdo.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]Antes de clicar em **sequência**, **tem** de garantir que o canal está pronto. 
>Além disso, certifique-se de que não deixar o canal num estado pronto sem uma entrada contribuição feed durante mais > 15 minutos.

##<a name="test-playback"></a>Reprodução de teste
  
1. Navegue para a ferramenta de AMSE e, clique com o botão direito do rato no canal para ensaiar. No menu, Paire o cursor sobre **a pré-visualização de reprodução** e selecione **com o Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Se a sequência de aparecer no Media player, em seguida, o codificador foi corretamente configurado para ligar à AMS. 

Se um erro é recebido, o canal terá de ser repostas e definições de codificador ajustadas. Consulte o tópico de [resolução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações.  

##<a name="create-a-program"></a>Criar um programa

1. Assim que a reprodução de canal é confirmada, crie um programa. No separador **Live** na ferramenta de AMSE, clique com o botão direito do rato dentro da área do programa e selecione **Criar novo programa**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

2. Nome do programa e, se for necessário, ajuste o **Comprimento da janela de arquivo** (que assume a predefinição de 4 horas). Também pode especificar uma localização de armazenamento ou deixe como predefinição.  
3. Selecione a caixa **Iniciar o programa agora** .
4. Clique em **Criar programa**.  
  
    Nota: A criação de programa leva menos tempo do que a criação de canal.    
 
5. Assim que o programa está em execução, confirme reprodução ao clicar com o botão direito do rato no programa do e navegar para **a reprodução de programas** e, em seguida, selecionar **com o Azure Media Player**.  
6. Assim que confirmado, botão direito do rato clique novamente em programa e selecione **copiar o URL de saída para área de transferência** (ou obter estas informações a partir da opção **definições e informações sobre o programa** a partir do menu). 

A sequência de está agora pronta para ser incorporado num leitor ou distribuído a uma audiência para visualização direto.  


## <a name="troubleshooting"></a>Resolução de problemas
 
Consulte o tópico de [resolução de problemas](media-services-troubleshooting-live-streaming.md) para obter orientações. 

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
