<properties 
    pageTitle="Guia de resolução de problemas do transmissão direto | Microsoft Azure" 
    description="Este tópico fornece sugestões sobre como resolver problemas de transmissão direto." 
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
    ms.topic="article" 
    ms.date="10/12/2016"  
    ms.author="juliako"/>

#<a name="troubleshooting-guide-for-live-streaming"></a>Guia de resolução de problemas do transmissão direto

Este tópico fornece sugestões sobre como resolver problemas de alguns problemas de transmissão direto.

## <a name="issues-related-to-on-premises-encoders"></a>Problemas relacionados com codificadores no local 

Esta secção fornece sugestões sobre como resolver problemas relacionados com codificadores no local que estão configuradas para enviar uma sequência de velocidade único para canais AMS que estejam ativados para codificação direto.

###<a name="problem-would-like-to-see-logs"></a>Problema: Gostaria ver os registos 

- **Potencial problema**: não é possível localizar codificador registos, que podem ajudar a depuração de problemas.
    
    - **Telestream Wirecast**: geralmente pode encontrar registos em C:\Users\{username} \AppData\Roaming\Wirecast\ 
    - **Live sob**: pode encontrar tem ligações para os registos no portal de gestão. Clique em **Estatística**, em seguida, **os registos**. Na página **Ficheiros de registo** , irá ver uma lista de registos de todos os itens de LiveEvent; Selecione a correspondência a sessão actual. 
    - **Flash Media Live Encoder**: pode encontrar o **Directório de registo...** através da navegação para o separador **Codificação de registo** .
    
###<a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: Não existe uma opção para exportar uma sequência gradual

- **Potencial problema**: O codificador a ser utilizado não desentrelaçar automaticamente. 

    **Passos de resolução de problemas**: procure uma opção desentrelaçar dentro a interface do codificador. Uma vez retirar entrelaçamento está ativado, procure novamente definições de saída gradual. 
 
###<a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: Tentou várias definições de saída codificador e continua a não conseguir ligar. 

- **Potencial problema**: não foi reposta corretamente canal codificação Azure. 

    **Passos de resolução de problemas**: Certifique-se o codificador é já não a instalação push AMS, parar e repor o canal. Assim que novamente em execução, tente ligar o seu codificador com as novas definições. Se esta ainda não corrigir o problema, experimente criar um novo canal completamente, por vezes canais podem tornar-se danificado após várias tentativas falhadas.  

- **Potencial problema**: não são ideais GOP o tamanho ou definições de fotograma chave. 

    **Passos de resolução de problemas**: intervalo de tamanho ou fotograma-chave recomendado GOP é 2 segundos. Algumas codificadores calculam esta definição no número de molduras, enquanto outras pessoas utilizam segundos. Por exemplo: quando exportar 30fps, o tamanho de GOP seria 60 molduras, que é equivalente a 2 segundos.  
     
- **Problema potencial**: portas fechadas estejam a impedir a sequência de. 

    **Passos de resolução de problemas**: quando streaming através do RTMP, verifique as definições de firewall e/ou proxy para confirmar que a saída portas 1935 e 1936 estão abertas. Quando utilizar RTP streaming, confirme que porta de saída 2010 está aberta. 


###<a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problema: Ao configurar o codificador à sequência com o protocolo RTP, não existe nenhum local para introduzir um nome de anfitrião. 

- **Problema potencial**: codificadores RTP muitos não permitirem para nomes de anfitrião e um endereço IP terá de ser adquirido.  

    **Passos de resolução de problemas**: para localizar o endereço IP, abra uma linha de comandos em qualquer computador. Para executar esta tarefa no Windows, abra o iniciador de executar (WIN + R) e escreva "cmd" para o abrir.  

    Assim que a linha de comandos estiver aberta, escreva "Ping [nome do anfitrião AMS]". 

    O nome do anfitrião pode ser derivado por omitir o número de porta a partir do Azure ingerir esta última URL, como realçado no exemplo seguinte: 

    RTP://TEST2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###<a name="problem-unable-to-playback-the-published-stream"></a>Problema: Não é possível reproduzir a sequência de publicado.
 
- **Potencial problema**: não existe nenhuma transmissão ponto final de executar ou não existe nenhuma transmissão unidades (escala) atribuídas. 

    **Passos de resolução de problemas**: navegar para o separador "Transmissão ponto final" na ferramenta de AMSE e confirmar existe um transmissão ponto final de executar o com uma unidade de transmissão. 
    


>[AZURE.NOTE] Se depois de seguir os passos de resolução de problemas que ainda não é possível com êxito transmitir em fluxo, submeta um bilhetes de suporte através do portal Azure.

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
