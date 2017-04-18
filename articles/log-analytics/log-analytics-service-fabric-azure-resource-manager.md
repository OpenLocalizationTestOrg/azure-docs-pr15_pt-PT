<properties
    pageTitle="Otimizar o seu ambiente com a solução de serviço ferro no registo de análise | Microsoft Azure"
    description="Pode utilizar a solução ferro de serviço para avaliar a risco e estado de funcionamento do seu aplicações ferro de serviço, micro serviços, nós e clusters."
    services="log-analytics"
    documentationCenter=""
    authors="niniikhena"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="nini"/>



# <a name="service-fabric-solution-in-log-analytics"></a>Solução de ferro de serviço no registo Analytics

> [AZURE.SELECTOR]
- [Gestor de recursos](log-analytics-service-fabric-azure-resource-manager.md)
- [PowerShell](log-analytics-service-fabric.md)

Este artigo descreve como utilizar a solução de serviço ferro no registo Analytics para ajudar a identificar e resolver problemas ao longo do seu cluster de serviço ferro.

A solução de serviço ferro utiliza os dados de diagnósticos do Azure a partir do seu serviço ferro VMs por recolher estes dados a partir do seu tabelas Azure WAD. Análise de registo, em seguida, lê eventos de framework ferro de serviço, incluindo **Fiável eventos do serviço**, **Ator eventos**, **Os eventos operacionais**e **Eventos ETW personalizada**. Com o dashboard de solução, são conseguem ver dos problemas e eventos relevantes no seu ambiente de serviço ferro.

Para começar a com a solução, terá de ligar o seu cluster de serviço ferro a uma área de trabalho de análise de registo. Seguem-se três cenários a ter em conta:

1. Se não tiver implementado seu cluster de serviço ferro, utilize os passos no ***Implementar um Cluster de ferro serviço ligado a uma área de trabalho de análise de registo*** para implementar um novo cluster e tê-lo configurado ao relatório para a análise de registo.

2. Se precisar de recolher contadores de desempenho a partir do seu anfitriões para utilizar outras soluções OMS como segurança no seu serviço de Cluster ferro, siga os passos no ***Implementar um Cluster de ferro serviço ligado a uma área de trabalho OMS com extensão VM instalado.***

3. Se já implementou o seu cluster de hardware de serviço e pretender ligá-lo à análise de registo, siga os passos no ***Adicionar uma conta existente do armazenamento a análise de registo.***


##<a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Implemente um Cluster de ferro serviço ligado a uma área de trabalho de análise de registo.
Este modelo faz o seguinte:


1. Implementar um cluster de Azure Service ferro já ligado a uma área de trabalho de análise de registo. Tem a opção para criar uma nova área de trabalho durante a implementar o modelo ou o nome de uma área de trabalho já existente do registo de análise de entrada.
2. Adiciona a conta de armazenamento de diagnóstico para a área de trabalho de análise de registo.
3. Ativa a solução de serviço ferro na área de trabalho a análise de registo.

[![Implementar Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)


Depois de selecionar o botão de implementar acima, serão entregues no portal do Azure com parâmetros para editar. Certifique-se de que criar um novo grupo de recursos se introduzir um novo nome de área de trabalho de análise de registo: ![ferro de serviço](./media/log-analytics-service-fabric/2.png)

![Serviço ferro](./media/log-analytics-service-fabric/3.png)

Aceite os termos legais e visitas "Criar" para começar a implementação. Assim que a implementação estiver concluída, deverá ver a nova área de trabalho e cluster criado e o WADServiceFabric * evento, WADWindowsEventLogs e WADETWEvent tabelas adicionadas:

![Serviço ferro](./media/log-analytics-service-fabric/4.png)

##<a name="deploy-a-service-fabric-cluster-connected-to-an-oms-workspace-with-vm-extension-installed"></a>Implemente um Cluster de ferro serviço ligado a uma área de trabalho OMS com extensão VM instalado.
Este modelo faz o seguinte:

1. Implementar um cluster de Azure Service ferro já ligado a uma área de trabalho de análise de registo. Pode criar uma nova área de trabalho ou utilize um já existente.
2. Adiciona as contas de armazenamento de diagnóstico para a área de trabalho de análise de registo.
3. Ativa a solução de serviço ferro na área de trabalho de análise de registo.
4. Instala a extensão do agente MMA em cada escala VM definir no seu cluster de serviço ferro. Com o agente de MMA instalado, são conseguem ver métricas de desempenho sobre os nós.


[![Implementar Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)


Seguir os mesmos passos acima, os necessários parâmetros de entrada e iniciar uma implementação. Mais uma vez deverá ver a nova área de trabalho, cluster e as tabelas WAD criadas todos os:

![Serviço ferro](./media/log-analytics-service-fabric/5.png)

###<a name="viewing-performance-data"></a>Ver dados de desempenho

Para ver dados de desempenho a partir do seu nós:
</br>
- Inicie a área de trabalho de análise de registo a partir do portal Azure.

![Serviço ferro](./media/log-analytics-service-fabric/6.png)

- Aceda a definições no painel da esquerda e selecione dados >> contadores de desempenho Windows >> "Adicionar contadores de desempenho seleccionados": ![ferro de serviço](./media/log-analytics-service-fabric/7.png)

- No registo de pesquisa, utilize as seguintes consultas para do delve para métricas chave sobre os nós de:
</br>

    um. Compare a utilização da CPU média em todos os seus nós na última uma hora para ver os nós que estão a ter problemas e intervalo de tempo que um nó tinha um coletor:

    ``` Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR. ```

    ![Serviço ferro](./media/log-analytics-service-fabric/10.png)


    b. Ver gráficos de linhas semelhantes para memória disponível em cada nó com esta consulta:

    ```Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.```

    Para ver uma lista de todos os seus nós, que mostra o valor médio exato MB disponíveis para cada nó, utilize esta consulta:

    ```Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer ```

    ![Serviço ferro](./media/log-analytics-service-fabric/11.png)


    c. No caso que pretende desagregar um nó específico ao examinar a hora a hora média, mínima, máxima e 75 percentil de utilização da CPU, não conseguir fazê-lo ao utilizar esta consulta (substitua campo do computador):

    ```Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR```

    ![Serviço ferro](./media/log-analytics-service-fabric/12.png)

    Leia mais informações acerca de métricas de desempenho no registo de análise [aqui]. (https://blogs.technet.microsoft.com/msoms/tag/metrics/)


##<a name="adding-an-existing-storage-account-to-log-analytics"></a>Adicionar uma conta existente do armazenamento a análise de registo

Este modelo adiciona simplesmente suas contas de armazenamento existentes para uma área de trabalho de análise de registo de nova ou existente.
</br>

[![Implementar Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

>[AZURE.NOTE] Em selecionar um grupo de recursos, se estiver a trabalhar com uma área de trabalho de análise de registo já existente, selecione "Utilizar existente" e procure o grupo de recursos que contém a área de trabalho OMS. Criar uma nova um se caso contrário.
![Serviço ferro](./media/log-analytics-service-fabric/8.png)

Depois de ter sido implementado Este modelo, será capaz de ver os ligado à área de trabalho de análise de registo de conta de armazenamento. Esta instância, adicionei uma conta de armazenamento mais para a área de trabalho do Exchange que criei acima.
![Serviço ferro](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Visualizar os eventos ferro de serviço

Quando as implementações são concluídas e a solução de serviço ferro foi ativada na área de trabalho, selecione o mosaico **Ferro de serviço** no portal do registo Analytics para iniciar o dashboard ferro de serviço. O dashboard inclui as colunas na tabela seguinte. Cada coluna enumera os eventos de dez principais por contagem correspondam a critérios essa coluna para o intervalo de tempo especificado. Pode executar uma pesquisa de registo que fornece a lista inteira ao clicar em **Ver todos os** na parte inferior direita de cada coluna ou ao clicar no cabeçalho da coluna.

| **Evento ferro de serviço** | **Descrição** |
| --- | --- |
| Dos problemas | Uma apresentação de problemas, tais como RunAsyncFailures RunAsynCancellations e nó pendentes. |
| Eventos operacionais | Eventos operacionais dos como implementações e atualização de aplicação. |
| Eventos de um serviço fiável | Eventos de um serviço fiável dessas um Runasyncinvocations. |
| Ator eventos | Eventos de ator dos gerado pelo seu micro-serviços, como exceções lançadas por um método de ator, e ator e deactivations e assim sucessivamente. |
| Eventos de aplicações | Todos os ETW eventos personalizados gerados pelas suas aplicações. |

![Dashboard de ferro de serviço](./media/log-analytics-service-fabric/sf3.png)

![Dashboard de ferro de serviço](./media/log-analytics-service-fabric/sf4.png)


A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para ferro de serviço.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows|![N](./media/log-analytics-malware/oms-bullet-red.png)|![N](./media/log-analytics-malware/oms-bullet-red.png)| ![Sim](./media/log-analytics-malware/oms-bullet-green.png)|            ![N](./media/log-analytics-malware/oms-bullet-red.png)|![N](./media/log-analytics-malware/oms-bullet-red.png)|10 minutos |


>[AZURE.NOTE] Pode alterar o âmbito destes eventos da solução serviço ferro ao clicar em **dados com base nos últimos 7 dias** na parte superior do dashboard. Também pode mostrar eventos gerados situada nos últimos 7 dias, 1 dia ou seis horas. Em alternativa, pode selecionar **personalizado** para especificar um intervalo de datas personalizado.


## <a name="next-steps"></a>Próximos passos

- Utilize [Pesquisas de registo no registo de análise](log-analytics-log-searches.md) para ver dados detalhados de evento de serviço ferro.
