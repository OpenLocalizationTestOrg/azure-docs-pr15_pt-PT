<properties 
    pageTitle="Introdução à análise da cadeia | Microsoft Azure" 
    description="Saiba mais acerca da cadeia Analytics, um serviço gerido que o ajuda a analisar dados em tempo real a partir da Internet de coisas (IoT) em tempo real." 
    keywords="análise como um serviço, gerido serviços, processamento de sequência, streaming analytics, o que é a análise da cadeia"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="what-is-stream-analytics"></a>O que é a análise da cadeia?

Azure a análise da cadeia é um motor de processamento de totalmente geridas, rentável evento em tempo real que o ajudarão a desbloquear abrangente informações a partir de dados. Análise da cadeia torna mais fácil configurar o cálculos analíticos em tempo real na partir de dispositivos, sensores, web sites, redes sociais, aplicações, sistemas de infraestrutura e mais o fluxo de dados.

Com alguns cliques no portal do Azure, pode criar uma tarefa de análise da cadeia especificando a origem de entrada dos dados transmissão, o sink de saída para os resultados da sua tarefa, e uma transformação de dados expresso num idioma SQL gosto. Pode monitorizar e ajustar a escala de velocidade da sua tarefa no portal do Azure com uma escala de alguns quilobytes para um gigabyte ou mais dos eventos processados por segundo.

Sequência Analytics tira partido de anos de trabalho do Microsoft Research no desenvolvimento de altamente optimizada transmissão motores para processamento de sensível ao tempo e integrações de idioma para especificações intuitivas dessa.

## <a name="what-can-i-use-stream-analytics-for"></a>O que posso utilizar a análise da cadeia de?
Grandes quantidades de dados estão a fluir em alta velocidade sobre o fio de hoje. As organizações que podem processar e agir nestes dados em tempo real transmissão significativamente podem melhorar a eficiência e marcam no mercado. Cenários de análise de transmissão em tempo real podem ser encontrados em todos os ramos: análise de personalizadas e em tempo real negociação de cotações e alertas disponibilizadas pelos empresas de serviços financeiros; Deteção de fraude em tempo real; Serviços de proteção de dados e de identidade; ingestão fiável e análise dos dados gerados pelo sensores e accionadores incorporado objetos físicos (Internet das coisas, ou IoT); análise de clickstream Web; aplicações de gestão (CRM) de relação de cliente emissão e alertas quando é degradada experiência do cliente um período de tempo. Empresas estão à procura de mais flexível, fiável rentável maneira e essa análise de dados em tempo real de sequência de evento para ser concluída com êxito no mundo empresas moderna altamente competitiva.

## <a name="key-capabilities-and-benefits"></a>Principais funcionalidades e benefícios
-   **Facilidade de utilização:** Análise da cadeia suporta um modelo de consulta simples, declarativa para descrever transformações. Para otimizar para facilidade de utilização, a análise da cadeia utiliza uma variante T-SQL e remove a necessidade de clientes lidar com a complexidade de técnica do fluxo de sistemas de processamento de. Utilizar [a análise da cadeia de consulta idioma](https://msdn.microsoft.com/library/azure/dn834998.aspx) no editor de consultas no browser, irá obter conferência sentido conclusão automática para ajudá-lo rapidamente e implementar facilmente o tempo série consultas, incluindo associações temporal com base, em agregados, filtros temporais e outras operações comuns, tais como as associações, agregados, projecções e filtros. Além disso, teste relativamente a um ficheiro de dados de exemplo da consulta no browser permite rápido de desenvolvimento iterativo.  

-   **Escalabilidade:** Análise da cadeia é capaz de processamento débito evento alto de até 1GB/segundo. Integração com o [Azure evento concentradores](https://azure.microsoft.com/services/event-hubs/) e [Azure IoT concentradores](https://azure.microsoft.com/services/iot-hub/) deixar a solução ingerir esta última milhões de eventos por segundo provenientes de dispositivos ligados, clickstreams e ficheiros de registo, para atribuir um nome algumas. Para poder alcançar isto, a análise da cadeia tira partido da partições capacidade de evento concentradores, que pode lucro 1 MB/s por partição. Os utilizadores conseguem partição o cálculo para um número de passos lógicos dentro da definição de consulta, cada uma com a capacidade de ainda ser divididos para aumentar a escalabilidade.  

-   **Fiabilidade, repetibilidade e recuperação rápida:** Um serviço na nuvem, da cadeia Analytics gerido ajuda a evitar a perda de dados e fornece continuidade do negócio trabalho falhas através das capacidades recuperação incorporados. Com a capacidade de internamente manter o estado, o serviço fornece os resultados de forma repetidos garantindo que é possível arquivar eventos e reaplicar processamento no futuro, sempre obter os mesmos resultados. Permite que clientes regressar altura e investigar cálculos quando efetuar análise de cabo, análise de hipóteses, etc.  

-   **Baixo custo:** Como um serviço na nuvem, a análise da cadeia está optimizada para fornecer aos utilizadores um muito baixo custo para começar a utilizar e manter as soluções de análise em tempo real. O serviço é criado por si pagar à medida que avança com base na utilização de transmissão unidade e a quantidade de dados processados pelo sistema. A utilização deriva com base no volume dos eventos processadas e a quantidade de cluster power aprovisionado dentro do cluster para processar as respetivas tarefas de análise da cadeia.  

-   **Referenciam dados:** Análise da cadeia fornece aos utilizadores a capacidade de especificar e utilizar dados de referência. Isto pode ser históricos dados ou simplesmente não transmissão que muda com menos frequência ao longo do tempo. O sistema simplifica a utilização de dados de referência para tratado como qualquer outra recebidas evento da cadeia para participar com outras sequências de evento penetração em tempo real para efetuar transformações.  

-   **Funções definidas pelo utilizador:** Análise de sequência tem integração com o Azure máquina formação para definir as chamadas de função no serviço de aprendizagem automática como parte de uma consulta de análise da cadeia. Expande-se as capacidades de análise da cadeia para tirar partido soluções de aprendizagem do Azure máquina existentes. Para obter mais informações sobre este, reveja o [tutorial de integração de máquina aprendizagem](stream-analytics-machine-learning-integration-tutorial.md).

-   **Conectividade:** Análise da cadeia liga-se diretamente para o Azure evento concentradores e Azure IoT concentradores para ingestão da cadeia e o serviço de Blobs do Azure ingerir esta última dados histórico. Resultados podem ser escritos a partir da cadeia Analytics Azure armazenamento de Blobs ou tabelas, Azure SQL DB, Azure dados Lake armazena, DocumentDB, concentradores de evento, Azure Service Bus tópicos ou filas e do Power BI, onde poder, em seguida, ser visualizada, ainda mais processada pelos fluxos de trabalho, utilizado na análise de lote através do [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) ou novamente processado como uma série de eventos. Quando utilizar o evento concentradores é possível compor a análise da cadeia vários juntamente com outros origens de dados e os motores de processamento sem perder a transmissão natureza os cálculos.  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos
Tenha de foram introduzidas para a análise da cadeia, um serviço gerido de transmissão de análise de dados a partir da Internet de coisas. Para saber mais sobre este serviço, consulte o artigo:

- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

