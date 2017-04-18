<properties 
    pageTitle="Como escrever consultas em sequência Analytics | Microsoft Azure" 
    description="Escrever consultas no dados da consulta e de análise de sequência | segmento de caminho de formação."
    keywords="como escrever consultas, os dados de consultas, escrever uma consulta, escrever consultas"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

# <a name="how-to-write-queries-in-stream-analytics"></a>Como escrever consultas em sequência Analytics

Escrever consultas para sequência de processamento de lógica no Azure da cadeia Analytics é implementada como "pé consulta" que é definida antes de uma tarefa é iniciado e executados dados tal como atingir a tarefa. A transformação de dados é expressa numa linguagem de consulta SQL semelhantes, que é em grande medida um subconjunto da T-SQL com algumas extensões de idioma adicionados como [sistema baseado em janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilizadas para exprimir semântica temporal.

## <a name="writing-queries"></a>Escrever consultas: ##

1. No seu trabalho de análise da cadeia no portal de gestão do Azure, clique em **consulta**.

    ![Consulta selecionar](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  

    No Portal do Azure, clique em **consulta**.

    ![Selecionar pré-visualização de consulta](./media/stream-analytics-write-queries/query-preview-portal.png)  

2.  Novas tarefas têm um modelo de consulta para ajudar a começar. O modelo de consulta executa uma consulta "pass-through" que projetos todos os campos a partir de eventos de entradas para o resultado.  

    - Se tiver definido pelo menos uma entrada e saída para a tarefa, pode substituir o marcador de posição de "[YourOutputAlias]" e "[YourInputAlias]" campos com os aliases da entrada e saída que deseja utilizam pela primeira vez. Além disso, ainda pode autor e testar a sua consulta no Portal clássica do Azure sem que define entradas e saídas na tarefa.
    - Se pretender efetuar mais processamento do que um pass-through simple, pode editar a definição de consulta. Para começar a com a criação de consultas, veja algumas consulta comum padrões são captadas [aqui](stream-analytics-stream-analytics-query-patterns.md).  
  
    ![Janela dados da consulta](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Para validar os dados de consultas está a funcionar: ##

Pode testar a sua consulta se comporta como esperado, executando-lo no browser através de um ou mais locais JSON ficheiros que contêm os dados de teste. Isto não irá iniciar a tarefa ou ter eventuais implicações de faturaçãohttps.

> [AZURE.NOTE] Atualmente o teste de consulta no browser não é suportada no Portal do Azure.  

1.  Certifique-se de que não existem erros na consulta (caso contrário, o botão de teste será desactivado) e, em seguida, clique no botão de teste.  

    ![Teste os dados de consultas](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  

2.  Será pedido para especificar os ficheiros para cada uma das entradas referenciadas na consulta. Neste exemplo, a consulta de modelo foi deixada como-é, pelo que a caixa de diálogo é solicitar uma introduções denominada "yourinputalias".  

    ![Testar consulta de dados](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  

3.  Navegue para um ficheiro de teste. Vários ficheiros de exemplo estão disponíveis no [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) e também pode obter dados de exemplo a partir do seus próprio entradas de sequência de dados através da função de dados de exemplo no separador de entradas do tipo.  

    ![Entrada da consulta](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  

4.  Depois de fechar a caixa de diálogo, a consulta é executada sobre os dados de teste e verá os resultados na parte inferior da página de consultas.  

    ![Resumo da consulta](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
