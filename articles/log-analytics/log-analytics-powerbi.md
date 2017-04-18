<properties
   pageTitle="Exportar dados de análise de registo para o Power BI | Microsoft Azure"
   description="Power BI é um serviço de análise de empresas baseada na nuvem da Microsoft que fornece visualizações e relatórios para uma análise de diferentes conjuntos de dados.  Análise de registo pode continuamente exportar dados do repositório de OMS Power BI para que pode tirar partido respetivas visualizações e ferramentas de análise.  Este artigo descreve como configurar consultas no registo de análise que automaticamente exportar para o Power BI em intervalos regulares."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="export-log-analytics-data-to-power-bi"></a>Exportar dados de análise de registo para o Power BI

[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é um serviço de análise de empresas baseada na nuvem da Microsoft que fornece visualizações e relatórios para uma análise de diferentes conjuntos de dados.  Análise de registo pode automaticamente exportar dados do repositório de OMS Power BI para que pode tirar partido respetivas visualizações e ferramentas de análise.

Quando configurar o Power BI com a análise de registo, cria consultas de registo que exporte os respetivos resultados para correspondentes conjuntos de dados no Power BI.  A consulta e exportar continua a executar automaticamente uma agenda que definem para manter o conjunto de dados atualizados com os dados mais recentes recolhidos pela análise de registo.

![Análise de registo ao Power BI](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Power BI agendas

Uma *Agenda do Power BI* inclui uma pesquisa de registo que exporta um conjunto de dados a partir do repositório OMS para um conjunto de dados correspondente no Power BI e uma agenda que define com que frequência esta pesquisa é executada para manter o conjunto de dados atual.

Os campos no conjunto de dados devolvida correspondem as propriedades dos registos devolvidos pela pesquisa registo.  Se a pesquisa devolve registos de diferentes tipos o conjunto de dados inclui todas as propriedades de cada um dos tipos de registo incluídos.  

> [AZURE.NOTE] É aconselhável utilizar uma consulta de pesquisa de registo que devolve dados não processados por oposição a efetuar qualquer consolidação com comandos, como [medida](log-analytics-search-reference.md#measure).  Pode efetuar qualquer agregação e cálculos no Power BI a partir dos dados não processados.

## <a name="connecting-oms-workspace-to-power-bi"></a>Ligar a área de trabalho OMS ao Power BI

Pode exportar do registo de análise ao Power BI, tem de ligar a área de trabalho OMS à sua conta do Power BI através do seguinte procedimento.  

1. Na consola do OMS clique no mosaico **Definições** .
2. Selecione **contas**.
3. Na secção **Informações de área de trabalho** de clique em **ligar à conta do Power BI**.
4. Introduza as credenciais para a sua conta do Power BI.

## <a name="create-a-power-bi-schedule"></a>Criar uma agenda do Power BI

Crie uma agenda de Power BI para cada conjunto de dados utilizando o seguinte procedimento.

1. Na consola do OMS clique no mosaico de **Registo de pesquisa** .
2. Escreva uma nova consulta ou selecione uma procura guardada que devolve os dados que pretende exportar para o **Power BI**.  
3. Clique no botão do **Power BI** na parte superior da página para abrir a caixa de diálogo do **Power BI** .
4. Forneça as informações na seguinte tabela e clique em **Guardar**.

| Propriedade | Descrição |
|:--|:--|
| Nome | Nome para identificar a agenda quando visualizar a lista de agendas do Power BI. |
| Procura guardada | A pesquisa de registo para executar.  Pode selecionar a consulta atual ou selecione uma procura guardada existente na caixa de lista pendente. |
| Agenda | Como a frequência com que pretende iniciar a pesquisa guardada e exportar para o conjunto de dados do Power BI.  O valor tem de estar entre 15 minutos e 24 horas. |
| Nome do conjunto de dados | O nome do conjunto de dados no Power BI.  Será criada se não existir e atualizado se existir. |

## <a name="viewing-and-removing-power-bi-schedules"></a>Visualizar e remover agendas do Power BI

Ver a lista de agendas de BI do Power existentes com o procedimento seguinte.

1. Na consola do OMS clique no mosaico **Definições** .
2. Selecione **Power BI**.

Para além de detalhes da agenda, o número de vezes que a agenda executou na semana passada e o estado da última sincronização são apresentadas.  Se a sincronização encontrados erros, pode clicar na ligação para executar uma pesquisa de registo para registos com detalhes do erro.

Pode remover uma agenda clicando no **X** no **Remover coluna**.  Pode desativar uma agenda ao selecionar **desativar**.  Para modificar uma agenda tem de removê-la e recrie-o com as novas definições.

![Power BI agendas](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>Tutorial de exemplo
A secção seguinte explica um exemplo de criar uma agenda do Power BI e utilizar o seu conjunto de dados para criar um relatório simple.  Neste exemplo, todos os dados de desempenho para um conjunto de computadores é exportado para o Power BI e, em seguida, num gráfico de linha é criado para apresentar a utilização do processador.

### <a name="create-log-search"></a>Criar a pesquisa de registo
Vamos começar por criar uma pesquisa de registo para os dados que pretendemos para enviar para o conjunto de dados.  Neste exemplo, vamos utilizar uma consulta que devolve todos os dados de desempenho para computadores com um nome que começa com *srv*.  

![Power BI agendas](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>Criar a pesquisa do Power BI
Vamos clique no botão de **Power BI** para abrir a caixa de diálogo do Power BI e forneça as informações necessárias.  Pretendemos esta pesquisa para executar uma vez por hora e criar um conjunto de dados denominado *Desempenho Contoso*.  Uma vez que já temos a pesquisa abrir que cria os dados queremos, podemos manter a predefinição de *consulta de pesquisa atual utilizar* para a **Pesquisa guardado**.

![Pesquisa do Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Certifique-se de pesquisa do Power BI
Para verificar que criámos a agenda corretamente, vamos ver a lista de pesquisas do Power BI no mosaico **Definições** no dashboard de OMS.  Estamos Aguarde alguns minutos e actualizar esta vista até relatórios de que a sincronização foi executada.

![Pesquisa do Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>Verifique se o conjunto de dados no Power BI
Vamos inicie sessão na nossa conta na [powerbi.microsoft.com](http://powerbi.microsoft.com/) e desloque-se para **conjuntos de dados** na parte inferior do painel da esquerda.  É possível ver o que é apresentado o conjunto de dados de *Desempenho da Contoso* que indica que o nosso exportar tem executar com êxito.

![Conjunto de dados do Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>Criar relatório com base no conjunto de dados
Vamos selecione o conjunto de dados de **Desempenho da Contoso** e, em seguida, clique em **resultados** no painel **campos** à direita para ver os campos que fazem parte deste conjunto de dados.  Para criar um gráfico de linhas que mostra a utilização do processador para cada computador, vamos executar as seguintes ações.

1. Selecione a visualização de gráfico de linha.
2. Arraste o **nome de objecto** para **nível filtro do relatório** e verificar **processador**.
3. Arraste **CounterName** para **nível filtro do relatório** e verificar **% processador de tempo**.
4. Arraste **contravalor** aos **valores**.
5. Arraste o **computador** para a **legenda**.
6. Arraste **TimeGenerated** para o **eixo**.

É possível ver o que é apresentado no gráfico de linha resultante com os dados a partir do nosso conjunto de dados.

![Gráfico de linhas do Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>Guardar o relatório
Vamos guardar o relatório ao clicar no botão Guardar na parte superior do ecrã e validar que está agora listado na secção relatórios no painel esquerdo.

![Relatórios do Power BI](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) construir consultas que podem ser exportadas para o Power BI.
- Saiba mais sobre o [Power BI](http://powerbi.microsoft.com) para criar visualizações com base nos exportações de análise de registo.
