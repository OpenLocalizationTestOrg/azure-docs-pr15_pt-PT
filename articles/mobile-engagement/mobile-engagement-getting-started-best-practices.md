<properties 
    pageTitle="Azure Cativação Mobile guia de introdução com as melhores práticas"
    description="Obter o guia de introdução para Azure Mobile Cativação e as melhores práticas para ativação" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/04/2016"
    ms.author="wesmc;ricksal"/>

# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure Cativação móvel - guia de iniciação com as melhores práticas

## <a name="overview"></a>Descrição geral

**o ecrã móvel é um espaço muito sobrecarregado:** No 2013, um estudo encontrado que o dispositivo móvel média tinha 27 aplicações instaladas. Os utilizadores despendidas normalmente 30 horas por mês das aplicações. A maior parte desta vez foi gasto de rede social e jogos (cerca de 20 horas). 2014, Android market teve cerca de 1,5 milhões de aplicações para os utilizadores escolher a partir de. Apple store contidas cerca de 1.2 milhões de aplicações. Utilização da aplicação móvel ainda está a aumentar, tal como os programadores competir nisto em crescimento mercado. 

O utilizador média móvel irá instalar e desinstalar aplicações com frequência de alta dependendo alterar interesses e experiências na aplicação. Para determinar o sucesso de uma aplicação que se torne essencial para saber mais do que apenas o número de utilizadores instala a aplicação. É importante saber como útil a sua aplicação está e se esse tendência a utilização está a alterar. As seguintes questões tornam-se importantes:

- São os seus utilizadores início para localizar a sua aplicação uninteresting ou obsoleto? 
- Quantos utilizadores foram interrompidas é utilizar a aplicação a todos os? 
- Na aplicação compras mais populares, para cima ou para baixo?
- Os utilizadores não conseguem concluir fluxos de trabalho devido a problemas com a aplicação ou falta de interesse? 
- Foi que manter a aplicação úteis e relevantes por direccionar conteúdo fresco com a base de utilizador? 
- Seria este conteúdo fresco ser a mesma para todos os utilizadores ou com o foco nas segmentos de utilizador com base no comportamento na sua aplicação? 
 
As respostas a perguntas semelhantes às seguintes podem ajudar a expandir a vida e receitas da sua aplicação. Também pode ajudam a definir e manter a sua base de utilizador. 

Suporte de dados relacionados com aplicações tendem para ter alguns da retenção mais alta entre os utilizadores. Um motivo para isto é que são constantemente que oferecem fresco conteúdos a utilizadores. Adoção antecipada das notificações push útil direcionado para o segmento de utilizador tem tendência para ter um impacto alto no retenção de aplicação. 

O programa Azure Mobile Cativação foi concebido para ajudá-lo a expandir a vida e retenção da sua aplicação, fornecendo um método para recolher e analisar informações detalhadas sobre a utilização da sua aplicação. Irá ajudá-classificar a sua base de acordo com o comportamento de utilizador e a criar campanhas com o foco para fornecer notificações push e mensagens de na aplicação aos segmentos de utilizador identificados. Indicadores chave de desempenho (KPI) medem como Ativa os seus utilizadores estão com aspetos diferentes da sua aplicação. Azure Mobile Cativação fornece os métodos que precisa para determinar estes KPIs. Ajuda a aumentar o retorno do seu investimento (ROI) ao fornecer a infraestrutura de que precisa para aumentar o empenho com a sua aplicação móvel. 

Para tirar o máximo partido do Azure Mobile Cativação, tem de começar com um plano de Cativação bem estruturada. Seu plano irá ajudá-lo a identificar os dados granular, que terá de ser possível ao segmento da sua base de utilizador. Isto pode ser baseado em comportamento e experiências na aplicação. Ordem para o seu plano ser efetuada com êxito, é aconselhável para definir claramente o KPI que irá medir objetivos da sua aplicação. Com indicadores de desempenho limpar definidos, pode incorporar facilmente a lógica necessária na sua aplicação para recolher dados ajustar com que irá utilizar para analisar e avaliar os KPIs. Este tópico é um guia de procedimentos recomendados para definir os KPIs que irá utilizar com o seu plano de Cativação. 


## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Passo 1: Definir os KPIs para se ajustar ao modelo de proposta


Definir corretamente KPIs pode ser uma tarefa difícil para concluir. Concebido para ramos diferentes de aplicações tem os seus próprios dados específicos e objetivos. Isto pode tendência para confunda a abordagem. Para ajudar a evitar esta situação, objetivos e KPIs devem ser classificados em três categorias principais: **empresas**, **Cativação**e **técnicos**. Este é a que chamamos de **modelo de proposta**.

Um bom plano geralmente terá objetivos com os KPIs que medem sucessos em cada uma das seguintes categorias de modelo de proposta.


#### <a name="business-kpis"></a>KPIs de empresas

Empresas KPIs devem ser a peça mais fácil para criar. Provavelmente já definiu estes algumas formulário quando que tinha planeado a aplicação móvel. Estes KPIs geralmente ajudam a receita de medida e ROI para a aplicação. A lista seguinte fornece alguns KPIs de empresas que poderão ajudá-orientá-lo ao definir indicadores de desempenho de exemplo:

- KPIs de negócio de multimédia
    - Número de anúncios clicado
    - Número de página visitas por utilizador
    - Número de subscrições atuais
- KPIs de negócio de jogos
    - Número de compras de na aplicação
    - Média de receita por utilizador (ARPU)
    - Tempo gasto por sessão
    - Dias atuais e reproduzidos num nível de jogo
- Empresas de E-Commerce KPIs
    - Aplicação dias utilizada
    - Média de receita por utilizador (ARPU)
    - Montante médio na carrinho durante a finalização
    - Categoria de produto para a maioria das vistas e compras
- Banco e seguro KPIs de empresas
    - Número de contas
    - Funcionalidades ativadas
    - Páginas de oferta visitadas
    - Alertas clicado ou ativado      



#### <a name="engagement-kpis"></a>KPIs de Cativação

Um KPI de Cativação é um indicador de desempenho para medir Cativação dos seus utilizadores. Tendências nesta área ajudam a determinar a retenção da sua aplicação. Aqui estão alguns indicadores de desempenho de exemplo para este tipo de KPI:

- Utilizadores ativos nos últimos 7 dias
- Contagem de utilizador inactivo para os últimos 7 dias
- Contagem de utilizadores que não utilizou a aplicação em 30 dias  

Algumas óbvios factores externos podem influenciar indicadores nesta área. Por exemplo, pode considerar um dispositivo móvel para ser com um utilizador sempre. Isto pode ou não pode ser verdadeiro. Uma aplicação de jogos poderá tendência para ter a utilização mais elevada à volta de feriados quando um gamer poderá ser reproduzido mais enquanto não estou a trabalhar ou reduzir escola.   

Bem definidas KPIs nesta categoria deverão ajudá-lo medir a relação entre a sua aplicação e os seus clientes.



#### <a name="technical-kpis"></a>KPIs técnicos

Indicadores de desempenho nesta categoria ajudá-lo a determinar se a aplicação é comporta corretamente, pendente ou falhar. Estes indicadores podem medir o estado de funcionamento da sua aplicação e determinar textuais problemas que podem impedir os utilizadores de utilizar a aplicação. Informações recolhidas para esta categoria também podem conter informações de desempenho que seriam relevantes para as equipas de marketing. Os dados também podem ser úteis para resolução de problemas por IT e equipas para ajudar a identificar erros não reportados de suporte. 
 
Eis alguns exemplos de KPIs técnicos:

- Informações de exceção não processada ou processada e contagem 
- Carimbo de data/hora para a última falha de sistema
- Botão última clicado ou última página visitada
- Utilização de memória da aplicação
- Taxa de moldura de aplicação
- Versão do SO que a aplicação está em execução
- Versão da aplicação

Defina estas KPIs para ajudar o desempenho da aplicação de medida e pinpoint potenciais erros. Este indicadores devem ajudar a reduzir o tempo que necessita para entregar um fix para os seus clientes. Também pode ajudam a definir um segmento de utilizador quem ter encontrado um nomeadamente problemas. Pode utilizar esse segmentação de utilizador para criar campanhas para fornecer notificações relativas ao promoções potenciais e correções disponíveis para o ajudar a recuperar a satisfação do cliente. 


#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Playbook exercício 1: Criar o seu dashboard KPI

Quando definir a sua estratégia de marketing, os KPIs devem apresentar uma vista para cada um dos objetivos do principais. Devem ser pontos de dados claramente definidos que permitirão recolher informações essenciais para monitorizar a sua aplicação e o comportamento do utilizador final.

Criar um dashboard KPI que contém o por baixo de informações

1.  Quais são os KPIs para a aplicação?
2.  Pontos de dados que irá utilizar para representar cada KPI?
3.  Onde estão localizados estes dados para a minha aplicação (ou seja, ecrã definições do sistema,...)?
4.  Pode reproduzir uma sequência de Cativação para este KPI?

Pode utilizar a folha de cálculo do **Construtor de KPI** no nosso [Multimédia Playbook modelo] [ Media Playbook link] para obter exemplos e orientações.



## <a name="step-2-your-engagement-program"></a>Passo 2: O programa de Cativação


Um programa de Cativação móvel excelente deve ser considerado um componente da chave da sua aplicação. Totalmente Isto deve incluir um programa de boas-vindas excelente que executa para um utilizador durante os primeiros dias da utilização da aplicação. Tem tendência para ter um efeito muito positivo no Cativação e retenção da sua aplicação. Estudos têm demonstrado que a maioria dos utilizadores deixar de utilizar uma aplicação alguns primeiros dias após a instalação. Pretende esforça cumprir ou exceder juros condução do cliente expetativa mais cedo enquanto o utilizador ainda com foco na sua aplicação. Certifique-se de que apresentar o valor de chave e os benefícios da sua aplicação aos seus clientes. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Notificações push são a melhor abordagem para as atribuições de antecipada com utilizadores do dispositivo móvel. No entanto, excelente deve ser cuidado quando segmentar utilizadores para as notificações push. Uma vez que assim que um utilizador é semelhante a que estão a receber spam ou uninteresting notificações, pode ter afeta grave. Em alguns cliques, um utilizador poderá eliminar a aplicação nunca para devolver. O utilizador deve estar a receber altamente personalizada na aplicação valor em vez de spam genérico.

Assim que os utilizadores estão ativamente interessados, em seguida, o programa de Cativação pode ajudar a orientar outros aspetos da aplicação.

Por exemplo, poderia configurar uma campanha que solicita a sua utilizadores ativos para classificar a aplicação. Dado que neste segmento de utilizador é o mais activo e tem a maioria dos experiência com a aplicação, seria esperado-los para atribuir a classificação mais precisa. Assim que tiver uma classificação de aplicação alta, pode ajudar a unidade para cima a transferência orgânica da sua aplicação, assim como reduzir os custos de aquisição nova do cliente.



#### <a name="engagement-sequence"></a>Sequência de Cativação


Um programa de Cativação global inclui sequências de Cativação diferente. Cada sequência destina-se para alcançar vários objectivos.


###### <a name="life-push-sequence"></a>Sequência de push vida


Objetivos para uma sequência de push vida são diferentes consoante o ciclo de vida de Cativação do utilizador com a aplicação. Um utilizador específico pode ser novas, inativa ou muito ativo. Em diferentes fases de um ciclo de vida de Cativação, os utilizadores podem beneficiar a partir do seu conteúdo fresco sob a forma de sugestões ou ligações a documentação. 

Por exemplo um novo utilizador poderá necessitar de ajuda introdução orientados para uma aplicação ou beneficiem das vantagens de um novo incentivos de utilizador semelhante a primeira vez que iniciar a aplicação...

*"Satisfeito por destinam-se a bordo! Lembre-se iniciar sessão para obter o mês 1. º gratuitos!"*


###### <a name="behavioral-push-sequence"></a>Sequência de comportamento push

A sequência de comportamento push destina-se para aumentar a utilização com base em comportamento do utilizador recolhido para a aplicação.  

Por exemplo, um utilizador ativo muito de uma aplicação de futebol fantasia poderá beneficiem das vantagens da ser interessada com a seguinte notificação push...

*"João estiver uma ventoinha de futebol grave! Inicie sessão no nossa secção Americano e ganhar acesso gratuito a para o comemorar o campeonato!"*


###### <a name="alerting-push-sequence"></a>Sequência de push alerta

Os utilizadores irão Agradecemos notícias relevantes focadas dos seus interesses. Uma sequência de push alerta melhora Cativação ao enviar alertas com base em interesses um utilizador claramente é apresentado. Isto pode ser explícito quando um utilizador seleciona os seus próprios interesses na aplicação. Pode também ser determinado implicitamente com base em dados recolhidos durante a interação do utilizador com a aplicação.

Por exemplo, o utilizador de uma aplicação E Commerce regularmente poderá comprar uma marca corporativa específica de café que tenha capturado com um KPI de negócio. O seguinte alerta pode melhorar Cativação este utilizador com a aplicação.
 
*"Olá Wes, um dos seus favoritas marcas de café estará em venda 25% desativar a primeira semana de Setembro de 2015. Agradecemos-lhe como um cliente e pretendia para se certificar de foram deverá ter em consideração."*

###### <a name="rentention-push-sequence"></a>Sequência de push Rentention

Esta sequência destina-se para manter os utilizadores que utilizem um campanhas de notificações push repetitivas para o ajudar a unidade um hábito normal de envolventes com a aplicação. Isto pode ajudar a aumentar a retenção de aplicação se o utilizador dispõe as interações. 

Por exemplo, o utilizador de uma aplicação relacionadas desporto poderá receber a seguinte notificação push semanais com base em equipas de Favoritos do utilizador:

*"Para a oportunidade de ganhar pontos 200, aceda voto se Yankees a Nova Iorque irá ganham os respetivos jogo esta semana contra Évora azul Jays!"*


#### <a name="the-3w-approach"></a>A abordagem 3W

Dominar as sequências de diferentes push irá ajudá-lo contrate com os utilizadores finais. No entanto, ainda precisar de utilizar a abordagem 3W para personalizar as notificações. A abordagem 3W deverá abordar quem, o quê e quando para cada notificação. Se claramente satisfaçam destas três perguntas que notificações deve ser corretamente com foco para Cativação.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### <a name="who-the-user-segment-that-will-receive-messages"></a>Quem: O utilizador segmento, que irá receber mensagens

Direccionar notificações aos seus utilizadores deve ser considerada um canal de comunicação muito sensíveis. Certifique-se as notificações que destinam-se para enviar a um segmento de utilizador também estão limitadas aos interesses desse segmento de utilizador. Uma notificação de forma incorreta encaminhada é muito provável que tem um efeito negativo num utilizador. Estes podem considerá-lo à esquerda para a sua aplicação que está a ser desinstalada o spam. 

Utilize uma combinação de critérios específicos técnicos e de comportamentais quando definir segmentos de utilizador que irão receber notificações. Um exemplo simples que define um segmento de utilizador pode ser semelhante ao seguinte instrução:

"Todos os utilizadores que iniciada a uma aplicação móvel para a primeira vez 3 dias há e visitou a página de início de sessão duas vezes sem realmente concluir um início de sessão".
 
Declaração de que ajuda a identificar os dados que precisa para recolher para suportar um cenário específico.


###### <a name="what-the-message-that-you-will-send"></a>O que: A mensagem que vai enviar

**Tom**

No seu as atribuições de utilizar um tom de adequado para sua para os seus utilizadores segmentados. Este é altamente uma boa forma de se ligar a sua os utilizadores finais e promover juros de um utilizador na sua aplicação. 

**Redirecionamento**

Uma notificação de emissão pode ser utilizada para abrir mais do que compõem o pedido. Se a mensagem de notificação fornece um contexto como notícias difusão ou uma promoção de produto, esta notificação podem ligação abrangente diretamente para o conteúdo correto dentro da aplicação. Para suportar esta funcionalidade, tem de criar um esquema para permitir que a aplicação de gerir o redirecionamento de URL. Quando trabalha com as sequências de Cativação, este é um passo importante que não deve ser esquecido.

Também pode ser gerido redirecionamento para outros sistemas. Por exemplo, com um URL de ação é possível redirecionar utilizadores finais muitos outros sistemas incluem os seguintes:

- Um Web site
- Uma caixa de correio com já configurar o e-mail
- Uma caixa de SMS
- Um serviço de acesso telefónico
- Diretamente para a aplicação de arquivo de classificação a aplicação. 

Este procedimento fornece inúmeras oportunidades contrate utilizadores finais e criar regras de automática para melhorar a espetáculos.


**/ Conteúdos no formato**

Diferentes tipos e formatos de notificações Push:

1. **Anúncios** : permite-lhe enviar mensagens de publicidade aos utilizadores a momentos diferentes (fora da aplicação, na aplicação ou sempre).
2. **Inquéritos** : ativado para reunir informações a partir dos utilizadores finais ao solicitando-lhes perguntas. As respostas, em seguida, estão disponíveis quando criar critérios aos utilizadores finais de destino.
3. **Dados emite** : permite-lhe enviar um ficheiro de dados binários ou base64 para atualizar a aplicação. As informações contidas num push de dados são enviadas para a sua aplicação para personalizar a experiência dos utilizadores na sua aplicação. A aplicação tem de ser desenvolvido para suportar os dados num push de dados.
4. **Mosaicos (apenas para o Windows Phone)** : com capacidade de utilizar o Microsoft emissão notificação de serviço (MPNS) para enviar uma notificação de emissão nativo que contém dados XML (suportados desde SDK versão 0.9.0. A carga final útil para mosaicos não pode exceder 32 quilobytes.). A mensagem é apresentada diretamente no mosaico da sua área.
5. **Vista da Web** : uma vista da web é um pop-up que contenham conteúdos da web. Este pop-up é apresentada quando o utilizador final tem clicada a notificação de push. Uma vista da web permite-lhe ter mais interação com o utilizador final.
 
>[AZURE.NOTE] Certifique-se de que o conteúdo que estiver a enviar como notificações push está em conformidade com a plataforma respetivas (iOS, Android, Windows) diretrizes para desenvolver aplicações e enviar as notificações push.

 


###### <a name="when-the-timing-of-your-campaign"></a>Quando: A temporização da campanha

Quando é a melhor hora para ativar uma campanha de acionar notificações push? Deverá manual ou automática? Deve-ser periódico? Para determinar a altura certa ou a frequência é essencial participar utilizadores com os melhores resultados. Para cada sequência de Cativação e cenário, tem de especificar quando estará a melhor hora para enviar notificações push. Aqui estão alguns exemplos possíveis:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Se estiver a enviar notificações muitos diariamente, tem de tomar grave consideração que os utilizadores poderão notar que as suas comunicações como spam. 

Azure Mobile Cativação disponibiliza duas formas de ajudar a evitar as comunicações apercebidas como spam. Primeiro, utilize segmentação grão fino para se certificar de que não-alvo os mesmos utilizadores. Para além disso, o Azure Mobile Cativação fornece uma funcionalidade de "quota". Esta funcionalidade pode limitar notificações enviadas para uma campanha. Por exemplo, definir uma quota predefinida para 5 por semana irá garantir que um utilizador incluído como parte do segmento de utilizador da campanha recebe mais do que 5 notificações para essa semana.





#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Playbook Exercício 2: Criar o seu programa de Cativação

Dedique algum tempo resumir os objetivos e definir campanhas de que esperar para reproduzir utilizando sequências específicas. Certifique-se de que aplicar a abordagem 3W para as notificações no seu campanhas. 

Utilizar a folha de cálculo do **Programa de Cativação** no [Modelo de Playbook multimédia] [ Media Playbook link] para obter exemplos e orientações.


## <a name="step-3-app-integration"></a>Passo 3: Aplicação integração


#### <a name="create-a-tag-plan"></a>Criar um plano de etiqueta

Para integrar o Azure Mobile Cativação na sua aplicação terá de criar um plano de etiqueta. O plano de etiqueta é a pedra angular do projeto. Define a relação entre especificações de marketing, o fluxo de trabalho da aplicação e os dados de tags real recolhidos na aplicação medir KPIs. Indica que a análise poderão ver no portal. Também ajuda a definir segmentos de utilizador e enviar notificações push com o foco participar os seus utilizadores finais. Assim que tiver o plano de etiqueta definido, adicionar o código para integrá-lo na sua aplicação é simple utilizando o SDK do Azure Mobile Cativação.

Um plano de etiqueta não deve marcar tudo numa aplicação. Só deve incluir os dados de etiqueta que faz parte da sua estratégia de Cativação móvel. Provavelmente, será diversificado entre aplicações. O [Modelo de Playbook multimédia] [ Media Playbook link] fornecidos pelo Azure Mobile Cativação ajuda-o a criar um plano de etiqueta com um determinado método. Utilize a folha de cálculo do **Plano de etiqueta** como um guia para construir o seu plano de etiqueta.

Quando definir uma secção de etiqueta na folha de cálculo, ser muito específicas. Isto é muito importante para evitar a ambiguidade. Detalhe cada esperado cenário em que será enviada cada etiqueta. Inclua o nome da atividade onde cada etiqueta está incorporada. Isto deve todas ser incluído na parte **Informative** da folha de cálculo. A folha de cálculo do plano de etiqueta deve ser a referência principal para teste de verificação. 

Na secção de **dados para recolher** , a equipa de desenvolvimento deve encontrar os tipos de, nomes, valores e pares valor e chave informações extra necessários para cada etiqueta que será incorporada na aplicação.

Recomendamos que reveja o plano de etiqueta com todas as equipas associadas ao projecto. Efetuar correções necessárias e confirme que tudo está desmarcado para as equipas de marketing e desenvolvimento.

Folha de cálculo **instrução de trabalho** pode ser utilizada para ajudar o guia de que todas as pessoas envolvidas no projeto.


#### <a name="data-types"></a>Tipos de dados

Estes são os tipos de suporte de dados ao Azure Mobile Cativação comuns.

###### <a name="devices-and-users"></a>Dispositivos e utilizadores

Azure Mobile Cativação identifica os utilizadores gerando um identificador exclusivo para cada dispositivo. Este identificador chama-se o identificador de dispositivo (ou deviceid). É gerado a forma a que todas as aplicações em execução no mesmo que o dispositivo para partilhar o mesmo identificador de dispositivo.

###### <a name="sessions-and-activities"></a>Sessões e atividades

Uma sessão é uma instância da aplicação que está a ser executada por um utilizador. A sessão ocupa a partir do momento, que o utilizador inicia a aplicação, até parar.

Uma actividade é um agrupamento lógico de um conjunto de ações que a aplicação poderá fazer durante uma sessão. É normalmente um ecrã específico na aplicação, mas pode ser que nada definidas pela lógica da aplicação. No mínimo deve marcar cada actividade ou ecrã para a sua aplicação. Isto permitirá compreender o caminho do utilizador.


###### <a name="events"></a>Eventos

Eventos são utilizados para comunicar a interação do utilizador com a aplicação. Podem ser instantâneas ações, como partilha de conteúdos ou iniciar um vídeo. Etiquetagem eventos irá fornecer-lhe com conjuntos de dados que mostram a forma como os utilizadores interagem com a aplicação. 

###### <a name="jobs"></a>Tarefas

Tarefas são utilizadas para comunicar ações que têm uma duração. Alguns exemplos seriam incluem:

- Execução dos API chamadas
- Apresentar o tempo de anúncios
- Duração de tarefas do fundo 
- Duração do processo de compra
- Ver um vídeo


###### <a name="errors"></a>Erros

Erros são utilizados para comunicar problemas detectados pela aplicação. Por exemplo, ações do utilizador incorreto, ou falhas de chamada de API.

###### <a name="application-information"></a>Informações sobre a aplicação

Informações sobre a aplicação (informações App) é utilizado para marcar dados relacionados com a experiência do utilizador com uma aplicação. Este é gerado pelo interação de um utilizador com a aplicação. 

Para obter uma chave de informações de app determinado Azure Mobile Cativação apenas mantém um registo do valor mais recente (sem histórico). Informações de App revela o estado da sua aplicação ou os utilizadores finais. Por exemplo o estado de início de sessão, ou grupo de Favoritos do produto de um utilizador.

###### <a name="crash-data"></a>Dados de falha de sistema

Os dados recolhidos automaticamente pelas falhas de aplicação Mobile Cativação SDK relatórios não processadas pela aplicação uma falha de sistema. Por exemplo uma exceção não processada que ocorre.


###### <a name="extra-data"></a>Dados adicionais

Eventos, erros, atividades e tarefas podem ser melhoradas com parâmetros. Este é um programador pode fornecer-como dados específicos da aplicação de informações de extra. Isto é importante para definir segmentação extensivamente. 

Por exemplo, o valor de uma tag "artigo" permitirá aos utilizadores finais segmento com base em quem visualizados esse artigo. No entanto, que podem não ser suficientes. Pode ser melhor se essa mesma etiqueta "artigo" também incluído extra-info, tal como "news_category" dentro de uma actividade. Seria úteis para determinar dinamicamente as categorias de favoritas para o utilizador. 

Informações extra são comunicada como um par de chave/valor. No exemplo para esta aplicação de multimédia, as informações do extra para "news_category" seria o valor para nessa categoria. Por exemplo, "desportos", "economia" ou "políticas de alto nível".





#### <a name="tag-and-sdk-integration"></a>Integração de etiqueta e SDK 

Para obter instruções passo a passo para integrar o SDK do Azure Mobile Cativação a sua aplicação, siga a documentação de [Cativação SDK integração](mobile-engagement-windows-store-integrate-engagement.md) no Web site Azure. Selecione a plataforma de destino as ligações na parte superior da página.

Recomendamos que criar projetos para duas aplicações criadas na parte superior do Azure Mobile Cativação. Um para desenvolvimento e transição de teste e outro para transição de produção. Seu departamento de TI pode promover, em seguida, a partir de transição de teste para produção quando o utilizador testes de aceitação dos forem bem sucedido.



#### <a name="user-acceptance-testing-uat"></a>Testes (UAT) de aceitação de utilizador

Utilizador testes de aceitação dos (UAT) envolve certificando-se de que tudo está a funcionar como planeado. Fluxos de trabalho podem ser concluídos e reunirem todos necessários dados com base no seu plano de etiqueta:
 
- Informações de marcação deve estar no local de acordo com os conceitos AZME documentados
- Todas as informações que necessárias são recolhidas (incluindo Extra informações, valor aplicação informações)
- Correspondências de nomenclatura de acordo com esquema planear de etiqueta
- Não existe sem etiquetas duplicadas enviadas

Testar cuidadosamente todos os tipos de comportamento de notificação que tenha incorporado na sua aplicação

- Dados de anúncios, inquéritos, emite fora da aplicação e na aplicação
- Vistas do texto/Web
- Atualização do distintivo, categorias



#### <a name="setup"></a>Programa de configuração

Configurar o Azure Mobile Cativação é muito simple. Toda a documentação relacionada com a interface de utilizador está disponível no Web site da Azure Mobile Cativação, [como navegar na interface de utilizador](mobile-engagement-user-interface-home.md).

É recomendado que começa ao configurar as funções direita e associações a uma função para os utilizadores do seu projeto. Isto ajuda a gerir o acesso adequado para a plataforma para todos os utilizadores. As funções podem incluir:

- Administradores
- Programadores
- Visualizadores 

Posteriormente:
- Registe-se a sua deviceID para testar no seu dispositivo.
- Aceda a definições da sua conta e configurar o fuso horário para ter gráficos e a hora de entrega de notificação definida para o fuso horário.
- Aceda a definições da sua aplicação e registar a "-informações App" terá de utilizador final de destino alcance.

Para mais informações sobre como executar a sua primeira campanha de notificações push, reveja [como começar a utilizar e gerir puxa para começar uma conversação aos seus utilizadores finais](mobile-engagement-how-tos.md).



## <a name="conclusion"></a>Conclusão


Programas de Cativação são iterativos e deve continuamente melhorar a sua como experimentar com o que funciona melhor para a sua aplicação. 

Inicialmente, enquanto desenvolver experiência com Cativação estratégias não tente para criar uma estratégia de Cativação global inteira. Tomar uma abordagem de passo a passo identificar os KPIs e como tirar partido-los. Estratégia de Cativação será exclusiva para cada aplicação.

Depois de ter desenvolvido alguma experiência pode considerar adicionando o seguinte para os programas de Cativação:

- Controlo: Adquire utilizadores e provavelmente defina as origens de recolha de dados. Azure Cativação móvel pode ser ligada a origens de recolha de dados. Permite-lhe monitorizar espetáculos de cada origem. Esta informação estarão interessante para maximizar o seu investimento aquisição. 

- A / B testes: Esta é uma parte essencial do programa de Cativação. Cada aplicação tem as suas próprias detalhes. Com A / B teste, pode melhorar o seu programa de Cativação.

- Localização Geo: Esta é uma oportunidade grande para marcas. Graças a esta funcionalidade pode chegar a hora e o local certo. Recomendamos que recolheu suficiente dados de comportamento do utilizador final antes de começar a utilizar geo localização a verificar.

- Push de dados: push de dados é uma push invisível. Push dados permite personalizar a sua aplicação com base no comportamento do utilizador final. Por exemplo, se um segmento de utilizador frequentemente consulta produtos de alta tecnologia, o proprietário da aplicação pode enviar uma push de dados que irão personalizar a home page com conteúdo tech alta.



## <a name="next-steps"></a>Próximos passos

- [Criar uma conta Azure Mobile Cativação](mobile-engagement-create.md).
- Visite [definir sua estratégia de Mobile Cativação](mobile-engagement-define-your-mobile-engagement-strategy.md) para saber mais sobre como definir a sua estratégia de Cativação Mobile.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
