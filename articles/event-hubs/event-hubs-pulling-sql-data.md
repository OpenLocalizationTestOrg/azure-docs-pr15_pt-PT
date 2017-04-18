<properties
   pageTitle="No dados do SQL no Azure evento concentradores | Microsoft Azure"
   description="Descrição geral dos controladores de evento importar a partir de exemplo SQL"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="pulling-data-from-sql-into-an-azure-event-hub"></a>No dados a partir do SQL a um concentrador de eventos do Azure

Uma arquitetura de típica para uma aplicação para o processamento de dados em tempo real envolve pela primeira vez conduza-lo a um concentrador de evento Azure. Pode ser um cenário de IoT, tal como monitorizar o tráfego da parte diferentes uma auto-estrada, ou um cenário de jogos, monitorização ações de um acumule de contestants frenzied ou um cenário de empresa, tais como a forma de exploração de construção de monitorização. Nestes casos, o produtor de dados é geralmente externos objetos que produza os dados de séries de tempo que precisa para recolher, analisar, armazenar e act relativamente a e, poderá investido muitas esforço para edifício saída a infraestrutura para estes processos. O que pode fazer se pretende trazer dados de algo como uma base de dados em vez de uma origem de dados de fluxo e utilizá-la em conjunto com outros dados transmissão? Considerar as maiúsculas/minúsculas em que pretende utilizar a análise da cadeia de Azure, Explorador de dados remota (RDX) ou outra ferramenta de algumas para analisar e agir no lentamente alterar dados a partir do Microsoft Dynamics AX ou um sistema de gestão de produção personalizada? Para resolver este problema, escrita e origem abrir uma amostra de pequenas na nuvem que pode modificar e implementar o que irá separar os dados a partir de uma tabela SQL e enviá-lo a um concentrador de evento Azure para utilizar como uma entrada nas suas aplicações analytical descendentes. Aperceba este é um cenário raro e o oposto de o que fazer normalmente com um concentrador de evento. No entanto, se encontrar-se na situação onde este é o que precisa, pode encontrar o código na galeria do Azure amostras, [aqui](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).  

Tenha em atenção que o código neste exemplo é apenas que, numa amostra. É **não** se destina a ser uma aplicação de produção e não tentativas foram efetuadas para que seja adequado para utilização num ambiente - é stricly um DIY, com foco nos programador exemplo. Tem de rever todo o tipo de segurança, desempenho, a funcionalidade e fatores de custos antes de liquidar numa arquitetura de fim para fim.

## <a name="application-structure"></a>Estrutura de aplicação

A aplicação escrita c# e o ficheiro de readme.md no exemplo contém todas as informações que necessita para modificar, criar e publicar a aplicação. As secções seguintes fornecem uma descrição de alto nível do que faz a aplicação.

Vamos começar com partem do princípio de que tem acesso a uma tabela do SQL Azure. Também terá da configurou um concentrador de evento Azure e saber a ligação cadeia necessária para aceder à mesma.

Quando a solução SqlToEventHub é iniciado, lê um ficheiro de configuração (App) para obter um número de coisas, conforme descrito no ficheiro readme.md. Estes são muito facilmente compreensíveis, tal como o nome da tabela de dados, etc e não precisa de rehash as explicações aqui. 

Assim que a aplicação tem ler o ficheiro de configuração,-vai para um ciclo, leitura da tabela do SQL e conduza registos com o concentrador de evento, em seguida, a aguardar para um intervalo de sono das definidos pelo utilizador antes de fazê-lo recomeçar novamente. Algumas coisas estão salientar:

1. A aplicação é baseada partem do princípio de que está a ser actualizada a tabela do SQL por alguns processo externo e que pretende enviar todas as e apenas as actualizações a um concentrador de evento.
2. A tabela de SQL tem de ter um campo que tem um número exclusivo e crescente, por exemplo, um número de registo. Isto pode ser tão simple como um campo denominado "Id" ou mais nada que é incrementado como tudo o que atualiza a base de dados adiciona registos tal como "Creation_time" ou "Sequence_number". A aplicação de notas e armazena o valor desse campo em cada iteração. Em cada fase subsequente através do ciclo, a aplicação de consultas, essencialmente, a tabela para todos os registos onde o valor desse campo excede o valor-viu a hora da última através de tudo. Estamos a chamar neste último valor "offset".
3. A aplicação cria uma tabela "TableOffsets" ao arrancar, para armazenar os desvios. A tabela é criada com a consulta "CreateOffsetTableQuery" definida no ficheiro de configuração. 
4. Existem várias consultas utilizadas para trabalhar com a tabela de deslocamento, definida no ficheiro de configuração, como "OffsetQuery", "UpdateOffsetQuery" e "InsertOffsetQuery". Não deve alterar estes.
5. Por fim, a consulta "Consultas de dados" definida no ficheiro de configuração é a consulta que será executada para separar os registos da tabela SQL. É atualmente limitado aos registos de início 1.000 na cada pass-through tudo para fins de optimização - se, por exemplo, 25.000 registos tiverem sido adicionados à base de dados desde a última consulta, poderá demorar algum tempo para executar a consulta. Ao limitar a consulta a 1.000 registos de cada vez, as consultas são muito mais rápidas. Selecionar parte superior 1.000 simples emite sucessivas lotes de 1.000 registos com o concentrador de evento.    

## <a name="next-steps"></a>Próximos passos

Para implementar a solução, clonar ou transferir a aplicação de SqlToEventHub, edite o ficheiro App, construi-lo e finalmente publicá-lo. Assim que tiver publicado a aplicação, pode vê-la em execução no portal do Azure clássico em serviços em nuvem e monitorizar os eventos chegar ao seu centro de evento. Tenha em atenção que a frequência será determinada pelo duas coisas: a frequência das atualizações de para a tabela de SQL e o intervalo de sono das que especificou no ficheiro de configuração para a aplicação.