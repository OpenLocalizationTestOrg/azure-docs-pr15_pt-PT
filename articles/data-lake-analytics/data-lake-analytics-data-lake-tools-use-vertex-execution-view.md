<properties 
   pageTitle="Utilizar a vista de execução de vértice nas ferramentas de Lake de dados para o Visual Studio | Microsoft Azure" 
   description="Saiba como utilizar a vista de execução de vértice para tarefas de dados Lake Analytics exame." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/13/2016"
   ms.author="jgao"/>

# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Utilizar a vista de execução de vértice nas ferramentas de Lake de dados para o Visual Studio

Saiba como utilizar a vista de execução de vértice para tarefas de dados Lake Analytics exame.

## <a name="prerequisites"></a>Pré-requisitos

- Alguns conhecimentos básicos da utilização das ferramentas de Lake de dados para o Visual Studio para desenvolver script U SQL.  Consulte o artigo [Tutorial: desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Abrir a vista de execução do vértice

Para uma determinada tarefa, pode clicar na ligação "Vértice execução vista" no canto inferior esquerdo. Poderá ser-lhe pedido para carregar perfis pela primeira vez e, pode demorar algum tempo consoante a conectividade de rede.

![Vista de execução de vértice das ferramentas de dados Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Compreender a vista de execução do vértice

Depois de introduzir a vista de execução vértice, existem três partes:

![Vista de execução de vértice das ferramentas de dados Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

- Selector de vértice: à esquerda está no Seletor de vértice.  Pode selecionar os vértices pelas funcionalidades (tais como os dados de 10 principais ler ou selecione pela fase).

    Uma dos filtros utilizados mais comuns é vértices no caminho crítico. Caminho crítico é o caminho mais longo de uma tarefa de U-SQL. É útil para otimizar os seus trabalhos, verificando qual vértice leva a hora da célula mais longa.

- Painel Centro superior:

    ![Vista de execução de vértice das ferramentas de dados Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

    Esta vista também mostra o estado de execução de todos os vértices. Converte o tempo em conformidade para o seu computador local e mostra o estado de diferentes no cores diferentes.

- O painel de centro da parte inferior:

    ![Vista de execução de vértice das ferramentas de dados Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

    - Nome do processo: O nome da instância vértice. É composta de diferentes partes no StageName | VertexName | VertexRunInstance. Por exemplo, o vértice de .v1 [62] SV7_Split designa a segunda instância em execução (.v1, índice, começando pela 0) vértice do número de 62 na fase SV7_Split.
    - Total dados leitura/escrita: Os dados estavam lidos/escritos por este vértice.
    - Estado de estado/sair: O estado final quando o vértice é terminado.
    - Tipo de falha do código de sair: O erro quando o vértice falhou.
    - Razão de criação: Por que motivo o vértice foi criado.
    - Fila de latência/NP de latência/processo de recurso latência: o tempo despendido para o vértice para aguardar de recursos, para processar dados e para se manter na fila de espera.
    - GUID do processo/criador de blocos: GUID para o vértice a execução atual ou o criador de blocos.
    - Versão: a N-ésimo instância do vértice a execução (o sistema pode agendar a novas instâncias de um vértice para muitos motivos, por exemplo falha na ligação, calcular redundância, etc.)
    - Versão criada hora.
    - Processar criar Iniciar hora/processo em fila de espera tempo/processo Iniciar hora/processo concluída tempo: quando o processo de vértice é iniciado criação; Quando inicia o processo de vértice fila; Quando inicia o processo de determinadas vértice; Quando o vértice determinadas estiver concluída.

## <a name="next-steps"></a>Próximos passos

- Para obter uma descrição geral do Lake a análise de dados, consulte o artigo [Descrição geral do Azure dados Lake Analytics](data-lake-analytics-overview.md).
- Para começar a desenvolver aplicações U SQL, consulte o artigo [scripts de desenvolver U-SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para saber U SQL, consulte [Introdução ao idioma do Azure dados Lake Analytics U-SQL](data-lake-analytics-u-sql-get-started.md).
- Para tarefas de gestão, consulte o artigo [Gerir Azure dados Lake a análise de utilização Azure portal](data-lake-analytics-manage-use-portal.md).
- Para registar informações de diagnóstico, consulte o artigo [aceder a registos de diagnóstico para análise do Azure dados Lake](data-lake-analytics-diagnostic-logs.md)
- Para ver uma consulta mais complexa, consulte o artigo [registos de Web site de analisar utilizando a análise do Azure dados Lake](data-lake-analytics-analyze-weblogs.md).
- Para ver os detalhes de tarefa, consulte o artigo [utilizar tarefa Browser e vista de tarefa para tarefas Azure dados lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)