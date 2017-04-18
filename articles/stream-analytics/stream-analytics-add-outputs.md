<properties 
    pageTitle="Como configurar dados exporta para trabalhos de análise da cadeia | Microsoft Azure" 
    description="Configurar saídas para trabalhos de análise da cadeia | segmento de caminho de formação."
    keywords="dados de saída, movimento de dados"
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

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Como configurar dados exporta para trabalhos de análise da cadeia

Tarefas de análise da cadeia Azure podem ser ligadas a um ou mais saídas de dados, que definem uma ligação para um receptor de dados existente. Tal como a tarefa de análise da cadeia processa e transformações de dados de entrada, é escrita uma sequência de eventos de exportação de dados ao resultado da sua tarefa.

Dados de análise em sequência saídas podem ser utilizadas para a origem de dashboards em tempo real ou alertas, desencadear fluxos de trabalho de movimento de dados ou simplesmente arquivar dados para o lote processamento mais tarde. Análise de sequência tem primeira classe integração com vários serviços Azure, encontram-se documentados em detalhe aqui.

Para adicionar um resultado para o seu trabalho Analytics sequência:

1. No Portal do Azure clássico, clique em **resultados** e, em seguida, clique em **Adicionar saída** no seu trabalho de análise da cadeia.

    ![Adicionar saídas](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  

    No portal do Azure clique no mosaico de **resultados** na sua tarefa de análise da cadeia.

    ![Azure Porta adicionar saídas](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)

2. Especifique o tipo do resultado:

    ![Selecione o tipo de dados de movimento](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  

    ![Azure portal escolher o tipo de dados de movimento](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)

3. Fornece um nome amigável para este resultado na caixa **Alias de saída** . Este nome pode ser utilizado numa consulta do seu trabalho mais tarde para consultar o resultado.  
    
    Preencha o resto das propriedades da ligação necessários para se ligar ao seu resultado.  Estes campos variam consoante o tipo de saída e são definidos em detalhe aqui.  

    ![Adicionar propriedades de exportação de dados](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  

4. Dependendo do tipo de resultado, poderá ter especificar como os dados são serializados ou formatados. As definições de serialização específicos para cada tipo de saída encontram-se documentados aqui.

    Preencha o resto das propriedades da ligação necessários para ligar à sua origem de dados. Estes campos variam consoante o tipo do tipo de entrada e de origem e são definidos em detalhe [aqui](stream-analytics-create-a-job.md).  

    ![Adicionar dados de saída concentrador de evento](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  

    ![Resultado dos dados portal Azure a concentrador de evento](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [Azure.Note] Qualquer elemento de saída adicionado à tarefa, tem de existir antes da tarefa é iniciada e eventos de iniciar a fluir. Por exemplo, se utilizar o armazenamento de BLOBs como uma saída, a tarefa não criará uma conta de armazenamento automaticamente. Necessita de ser criados pelo utilizador antes da tarefa de ASA é iniciada.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
