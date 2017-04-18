<properties
    pageTitle="Conceitos Mobile Cativação | Microsoft Azure"
    description="Azure conceitos de Cativação Mobile"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-concepts"></a>Azure conceitos de Cativação Mobile

Mobile Cativação define alguns conceitos comuns para todas as plataformas suportadas. Este artigo descreve brevemente esses conceitos.

Este artigo é um bom início se estiver familiarizado com o Mobile Cativação. Certifique-se também ler a documentação específica para a plataforma que estiver a utilizar, tal como-lo será refinar os conceitos descritos neste artigo com mais detalhes e exemplos, bem como limitações possíveis.

## <a name="devices-and-users"></a>Dispositivos e utilizadores
Mobile Cativação identifica os utilizadores gerando um identificador exclusivo para cada dispositivo. Este identificador chama-se o identificador de dispositivo (ou `deviceid`). É gerado a forma a que todas as aplicações em execução do mesmo dispositivo para partilhar o mesmo identificador de dispositivo.

Implicitamente, significa que Mobile Cativação considerar um dispositivo pertencer a um utilizador e, por conseguinte, os utilizadores e dispositivos são conceitos equivalentes.

## <a name="sessions-and-activities"></a>Sessões e atividades
Uma sessão é uma utilização da aplicação realizada por um utilizador, da hora do utilizador começa a utilizá-lo, até que as marcas de utilizador.

Uma actividade é uma utilização de uma determinada peça Subendereço da aplicação realizada por um utilizador (normalmente, é apresentado um ecrã, mas pode ser nada adequado para a aplicação).

Um utilizador só pode realizar uma actividade de cada vez.

Uma actividade está identificada por um nome (limitado a 64 caracteres) e pode, opcionalmente, incorporar alguns dados extra (o limite de 1024 bytes).

Sessões são automaticamente calculados da sequência de actividades executadas por utilizadores. Uma sessão é iniciada quando o utilizador inicia a sua primeira atividade e deixa de quando concluir a sua última actividade. Isto significa que uma sessão não é necessário explicitamente ser iniciada ou parados. Em vez disso, atividades são explicitamente iniciadas ou paradas. Se for comunicada sem atividade, sem sessão é comunicado.

## <a name="events"></a>Eventos
Eventos são utilizados para comunicar instantâneas ações (como botão premido ou artigos lidos por utilizadores).

Um evento pode estar relacionado com a sessão actual, para uma tarefa em execução, ou pode ser um evento autónomo.

Um evento está identificado por um nome (limitado a 64 caracteres) e pode, opcionalmente, incorporar alguns dados extra (o limite de 1024 bytes).

## <a name="error"></a>Erro
Erros são utilizados para comunicar problemas corretamente detetados pela aplicação (como acções do utilizador incorreto, ou falhas de chamada de API).

Um erro pode estar relacionado com a sessão actual, para uma tarefa em execução, ou pode ser um erro de autónomo.

Um erro é identificado por um nome (limitado a 64 caracteres) e pode, opcionalmente, incorporar alguns dados extra (o limite de 1024 bytes).

## <a name="job"></a>Tarefa
Tarefas são utilizadas para comunicar ações ter uma duração (como a duração das chamadas de API, apresentar a hora de anúncios, a duração das tarefas de fundo ou a duração das ações de utilizador).

Uma tarefa não está relacionada com uma sessão, uma vez que uma tarefa pode ser executada em segundo plano, sem qualquer interação do utilizador.

Uma tarefa é identificada por um nome (limitado a 64 caracteres) e pode, opcionalmente, incorporar alguns dados extra (o limite de 1024 bytes).

## <a name="crash"></a>Falha de sistema
Causa uma falha é enviada automaticamente pelo SDK Mobile Cativação para comunicar falhas de aplicação onde problemas não detetados pela aplicação torná-lo falha de sistema.

## <a name="application-information"></a>Informações sobre a aplicação
Informações sobre a aplicação (ou informações app) são utilizada para marcar utilizadores, ou seja, para associar a alguns dados para os utilizadores de uma aplicação (isto é semelhante a web cookies, exceto que informações app está armazenada no lado do servidor a plataforma Azure Mobile Cativação).

Informações App podem ser registadas ao utilizar a API do Mobile Cativação SDK ou ao utilizar a plataforma de Cativação Mobile API do dispositivo.

Informações App são um par de chave/valor associado a um dispositivo. A tecla é o nome das informações de aplicação (limitada para 64 letras ASCII [a-zA-Z], [0-9] de números e de sublinhado [_]). O valor (limitado para 1024 caracteres) pode ser qualquer cadeia, número inteiro, data (AAAA-MM-dd) ou booleano (VERDADEIRO ou FALSO).

Qualquer número de informações app pode ser associado a um dispositivo, dentro dos limites definidas pelos termos de preços de Cativação Mobile. Para uma determinada chave Mobile Cativação apenas mantém um registo do último conjunto de valor (sem histórico). Definir ou alterar o valor de um informações app força Mobile Cativação para voltar a avaliar audiência conjunto de critérios neste informações de aplicação (se existir) essas informações de aplicação o que significa que pode ser utilizado para acionar puxa tempo real.

## <a name="extra-data"></a>Dados adicionais
Dados extra (ou extras) se alguns dados arbitrários que podem ser anexados eventos, erros, atividades e tarefas.

Extras estão estruturadas da mesma forma a objetos JSON: que são efetuadas de uma árvore de pares valor/chave. Teclas estão limitadas a 64 ASCII letras [a-zA-Z], [0-9] de números e sublinhado [_]) e o tamanho total da extras está limitado a 1024 carateres (uma vez codificados em JSON pelo Mobile Cativação SDK).

A árvore de pares valor/chave completa é armazenada como um objeto JSON. No entanto, apenas o primeiro nível de chaves/valores é decomposto para serem acessíveis diretamente para algumas funções avançadas como segmentos (por exemplo, pode facilmente definir um segmento denominado "SciFi ventoinhas", que é constituído por todos os utilizadores ter enviado, pelo menos, 10 vezes o evento com o nome "content_viewed" com a chave extra "content_type" definido para o valor "scifi" no último mês). Recomenda-se, por conseguinte, vivamente para enviar apenas extras efetuadas das listas simples de pares valor/chave utilizando os valores escalares (por exemplo, cadeias, datas, números inteiros ou booleano).

## <a name="next-steps"></a>Próximos passos

- [Descrição geral do Windows Universal SDK para Azure Mobile Cativação](mobile-engagement-windows-store-sdk-overview.md)
- [Descrição geral do Windows Phone Silverlight SDK para Azure Mobile Cativação](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK para Azure Mobile Cativação](mobile-engagement-ios-sdk-overview.md)
- [Android SDK para Azure Cativação móvel](mobile-engagement-android-sdk-overview.md)
