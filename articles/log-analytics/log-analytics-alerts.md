<properties 
   pageTitle="Alertas no registo Analytics | Microsoft Azure"
   description="Alertas no registo de análise identificar informações importantes no seu repositório OMS e podem importante notificá-lo de problemas ou invocar ações para tentar corrigi-los.  Este artigo descreve como criar uma regra de alerta e detalhes as ações diferentes podem pegar em."
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
   ms.date="08/22/2016"
   ms.author="bwren" />

# <a name="alerts-in-log-analytics"></a>Alertas no registo Analytics

Alertas no registo de análise identificam informações importantes no seu repositório OMS.  Regras de alertas automaticamente executar pesquisas de registo de acordo com uma agenda e criar um registo de alerta, se os resultados corresponder aos critérios específicos.  A regra, em seguida, pode executar automaticamente uma ou mais ações importante notificá-lo do alerta ou invocar outro processo.   

![Alertas de análise de registo](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Criar uma regra de alerta
Para criar uma regra de alerta, comece por criar uma pesquisa de registo para os registos que devem invocar o alerta.  O botão de **alerta** , em seguida, estarão disponível para que possa criar e configurar a regra de alerta.

1.  Na página Descrição geral de OMS, clique em **Registo de pesquisa**.
2.  Criar uma consulta de pesquisa do novo registo ou selecione uma pesquisa de registo guardado. 
3.  Clique em **alerta** na parte superior da página para abrir o ecrã de **Adicionar regra de alerta** .
4. Consulte as tabelas abaixo para obter detalhes sobre as opções para configurar o alerta.
5. Quando fornecer a janela de tempo para a regra de alerta, será apresentado o número de registos existentes que correspondem aos critérios de pesquisa para essa janela de tempo.  Isto pode ajudar a determinar a frequência com que irá dar-lhe o número de resultados que espera.
4.  Clique em **Guardar** para concluir a regra de alerta.  Será iniciado executar imediatamente.


![Adicionar regra de alerta](media/log-analytics-alerts/add-alert-rule.png)

| Propriedade | Descrição |
|:--|:--|
| **Informações sobre o alerta** | |
| Nome |  Nome exclusivo para identificar a regra de alerta. |
| Gravidade | Gravidade do alerta que é criado por esta regra. |
| Consulta de pesquisa | Selecione **utilizar atual consulta de pesquisa** para utilizar a consulta atual ou selecionar uma procura guardada existente a partir da lista.  A sintaxe de consulta é fornecida na caixa de texto onde pode modificá-lo se for necessário.  |
| Janela de tempo | Especifica o intervalo de tempo para a consulta.  A consulta devolve apenas os registos que foram criados dentro este intervalo da hora atual.  Isto pode ser qualquer valor entre 5 minutos e 24 horas.  Deve ser maior ou igual a frequência de alerta.  <br><br> Por exemplo, se a janela de tempo está definida para 60 minutos e a consulta é executada no 1:15 PM, serão devolvidos apenas os registos criados entre 12:15 PM e 1:15 PM. |
| **Agenda** |     
| Limiar | Critérios para quando deve criar um alerta.  Se o número de registos devolvidos pela consulta corresponde este critério, é criado um alerta. |
| Frequência de alerta | Especifica a frequência com que a consulta deve ser executada.  Pode ser qualquer valor entre 5 minutos e 24 horas.  Deve ser igual a ou menor do que a janela de tempo. |
| Eliminar alertas | Quando ativar supressão para a regra de alerta, ações para a regra estão desactivadas por um período de tempo depois de criar um novo alerta definido.  A regra ainda estiver em execução e irá criar registos de alertas, se os critérios são satisfeitos.  Este é para permitir-lhe tempo para corrigir o problema sem executar ações duplicadas. |
| **Ações** | |
| Notificação de e-mail | Especifique **Sim** se pretender que uma mensagem de e-mail para ser enviada quando o alerta é acionado. |
| Assunto    | Assunto: no e-mail.  Não é possível modificar o corpo da mensagem. |
| Destinatários | Endereços de todos os destinatários de correio electrónico.  Se especificar mais do que um endereço, separe os endereços com um ponto e vírgula (;)). |
| Webhook | Especifique **Sim** se pretender ligar um webhook quando o alerta é acionado. |
| Webhook URL | O URL da webhook. |
| Incluir carga útil JSON personalizado | Selecione esta opção se pretender substituir o carga útil predefinido por uma carga útil personalizada. |
| Introduza a sua carga de útil JSON personalizada | A carga útil personalizado para o webhook.  Consulte a secção anterior para obter detalhes. |
| Livro execuções | Especifique **Sim** se pretender iniciar um livro de execuções do Azure automatização quando o alerta é acionado. |
| Selecione um livro de execuções | Selecione o livro de execuções para iniciar a partir de runbooks na conta de automatização configurada na sua solução de automatização. |
| Executar no | Selecione **Azure** para executar o livro de execuções na nuvem Azure.  Selecione **Trabalhador híbrido** para executar o livro de execuções [Híbrido livro execuções trabalhador](..\automation\automation-hybrid-runbook-worker.md) no seu ambiente local. |


## <a name="manage-alert-rules"></a>Gerir regras de alertas
Pode obter uma lista de todas as regras de alertas no menu **alertas** de análise de registo **Definições**.  

![Gerir alertas](./media/log-analytics-alerts/configure.png)

1. Na consola do OMS selecione o mosaico **Definições** .
2. Selecione **alertas**.

Pode executar várias ações a partir desta vista.

- Desative uma regra ao selecionar **desativar** junto à mesma.
- Edite uma regra de alerta, clicando no ícone de lápis junto ao mesmo.
- Remova uma regra de alerta, clicando no ícone **X** junto ao mesmo. 


## <a name="setting-time-windows"></a>Definir tempo o windows 

### <a name="event-alerts"></a>Alertas de eventos

Eventos incluem origens de dados como registos de eventos do Windows, do sistema, e os registos de personalizada.  Pretende criar um alerta quando é criado um evento de erro específico ou quando vários eventos de erros são criados dentro de uma janela de tempo específico.

Para alertar sobre um único evento, defina o número de resultados para maior que 0 e frequência tanto a janela de tempo para 5 minutos.  Que será executar a consulta em 5 minutos e verifique a ocorrência de um único evento que foi criado desde a última vez que foi executar a consulta.  Uma maior frequência pode atrasar o tempo entre o evento que está a ser recolhidas e o alerta a ser criado.

Algumas aplicações poderão registar um erro ocasional que não deva necessariamente elevar um alerta.  Por exemplo, a aplicação pode repetir o processo que criou o evento de erro e, em seguida, teve êxito da próxima vez.  Neste caso, não poderá querer criar o alerta, a menos que são criados eventos de vários dentro de uma janela de tempo específico.  

Em alguns casos, poderá querer criar um alerta na ausência de um evento.  Por exemplo, um processo pode iniciar sessão eventos normais para indicar que está a funcionar corretamente.  Se não registar um destes eventos dentro de uma janela de tempo específico, deverá ser criado um alerta.  Neste caso seria defina o limite *inferior a*1.

### <a name="performance-alerts"></a>Alertas de desempenho

[Dados de desempenho](log-analytics-data-sources-performance-counters.md) está armazenado como registos no repositório OMS semelhante a eventos.  O valor de cada registo é a média medida durante os 30 minutos anterior.  Se pretender alerta quando um contador de desempenho excede um determinado limiar, esse limiar deverá ser incluído na consulta.

Por exemplo, se pretendia alertar quando o processador executa mais 90% para 30 minutos, que vai utilizar uma consulta como *tipo = desempenho nome de objecto = processador CounterName = "% de tempo do processador" contravalor > 90* e o limiar de para a regra de alerta para *maior que 0*.  

 Uma vez que os [registos de desempenho](log-analytics-data-sources-performance-counters.md) são agregados a cada 30 minutos, independentemente da frequência que recolher cada contador, uma janela de tempo mais pequena do que 30 minutos pode devolver sem registos.  Definir a janela de tempo para 30 minutos irá Certifique-se de que obter um único registo para cada origem ligada que representa a média sobre esse período de tempo.

## <a name="alert-actions"></a>Acções de alerta

Para além de criar um registo de alerta, pode configurar a regra de alerta para executar automaticamente uma ou mais ações.  Ações importante podem notificá-lo do alerta ou invocar algumas processo que tenta corrigir o problema que foi detetado.  As seguintes secções descrevem as ações que estão atualmente disponíveis.

### <a name="email-actions"></a>Ações de e-mail
Ações de e-mail enviar um e-mail com os detalhes do alerta para um ou mais destinatários.  Pode especificar o assunto da mensagem, mas do conteúdo é um formato padrão fórmula criado pela análise de registo.  Inclui informações de resumo, tal como o nome do alerta para além de detalhes de até dez registos devolvidos pela pesquisa registo.  Também inclui uma ligação para um registo de pesquisa no registo de análise que irá devolver todo o conjunto de registos dessa consulta.   O remetente da mensagem é *equipa de conjunto de aplicações do Microsoft operações gestão &lt; noreply@oms.microsoft.com *. 


### <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook permitem-lhe invocar um processo externo através de um pedido de HTTP POST único.  O serviço que está a ser denominado deve webhooks de suporte e determinar como irá utilizar qualquer carga útil que recebe.  Também pode chamar um REST API que não suporta especificamente webhooks desde o pedido seja num formato que compreende API.  Exemplos de utilização de um webhook em resposta a um alerta estiver a utilizar um serviço como o [margem](http://slack.com) para enviar uma mensagem com os detalhes de alerta de ou criar um incidente de um serviço como o [PagerDuty](http://pagerduty.com/).  

Instruções de criação de uma regra de alerta com um webhook para ligar a um serviço de exemplo concluída estão disponível em [Webhooks no registo Analytics alertas](log-analytics-alerts-webhooks.md).

Webhooks incluir um URL e uma carga útil formatada JSON se os dados enviados para o serviço externo.  Por predefinição, a carga de útil irá incluir os valores na seguinte tabela.  Pode optar por substituir carga este útil por um personalizado do seu próprio.  Nesse caso pode utilizar as variáveis na tabela para cada um dos parâmetros para incluir o seu valor na sua carga útil personalizado.


| Parâmetro | Variável | Descrição |
|:--|:--|:--|
| AlertRuleName | #alertrulename | Nome da regra alerta. |
| AlertThresholdOperator | #thresholdoperator | Operador de limiar para a regra de alerta.  *Maior* ou *menor*. |
| AlertThresholdValue | #thresholdvalue | Valor do limiar para a regra de alerta. |
| LinkToSearchResults | #linktosearchresults | Ligar a procura de registo de análise de registo que devolve os registos desde a consulta que criou o alerta. |
| ResultCount  | #searchresultcount | Número de registos nos resultados da pesquisa. |
| SearchIntervalEndtimeUtc  | #searchintervalendtimeutc | Hora de fim para a consulta no formato de UTC. |
| SearchIntervalInSeconds | #searchinterval | Janela de tempo para a regra de alerta. |
| SearchIntervalStartTimeUtc  | #searchintervalstarttimeutc | Hora para a consulta de início no formato de UTC. |
| SearchQuery | #searchquery | Consulta de pesquisa de registo utilizada pela regra de alerta. |
| SearchResults | Consulte abaixo | Registos devolvidos pela consulta no formato JSON.  Limitado pela primeira vez 5000 registos. |
| WorkspaceID | #workspaceid | ID da área de trabalho OMS. |


Por exemplo, poderá especificar carga seguinte útil personalizado que inclua um único parâmetro designado *texto*.  O serviço que este webhook chamadas seria ser esperado este parâmetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Este carga útil exemplo seria resolver para algo semelhante ao seguinte quando é enviado para o webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir os resultados da pesquisa uma carga útil personalizada, adicione a linha seguinte como uma propriedade de nível superior na carga json útil.  

    "IncludeSearchResults":true

Por exemplo, para criar uma carga útil personalizada que inclui apenas o nome do alerta e os resultados da pesquisa, pode utilizar o seguinte. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Pode percorrer um exemplo completo da criação de uma regra de alerta com um webhook para iniciar um serviço externo na [webhook exemplo de alerta de análise de registo](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Ações de livro execuções

Ações de livro execuções iniciar um livro de execuções no Azure automatização.  Para poder utilizar este tipo de ação, tem de ter a [solução de automatização](log-analytics-add-solutions.md) instalado e configurado na sua área de trabalho OMS.  Se não tiver instalado quando cria uma nova regra de alerta, é apresentada uma ligação para o instalar.  Pode selecionar a partir do runbooks na conta de automatização que configurou da solução de automatização.

Ações de livro execuções inicie o livro de execuções utilizando um [webhook](../automation/automation-webhooks.md).  Quando criar a regra de alerta,-lo irá criar automaticamente um novo webhook para o livro de execuções com o nome **OMS alerta remediação** seguido por um GUID.  

Não é possível diretamente povoar quaisquer parâmetros do livro de execuções, mas o [parâmetro $WebhookData](../automation/automation-webhooks.md) irá incluir os detalhes do alerta, incluindo os resultados da pesquisa de registo que foi criado.  Livro de execuções terá de definir **$WebhookData** como um parâmetro para a mesma aceder às propriedades do alerta.  Os dados do alerta estão disponíveis no formato de json numa única propriedade denominado **SearchResults** na propriedade **RequestBody** da **$WebhookData**.  Isto vai ter com as propriedades da tabela seguinte.


| Nó | Descrição |
|:--|:--|
| ID         | Caminho e o GUID da pesquisa. |
| __metadata | Informações sobre o alerta, incluindo o número de registos e o estado dos resultados da pesquisa. |
|  valor     |  Entrada separada para cada registo nos resultados da pesquisa.  Os detalhes da entrada devolvida correspondem as propriedades e valores do registo.   |

Por exemplo, o livro de execuções seguinte seria extrair os registos devolvidos pela pesquisa registo e atribuir diferentes propriedades com base no tipo de cada registo.  Tenha em atenção que inicia o livro de execuções convertê **RequestBody** a partir do json para que pode ser trabalhou com como um objeto no PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value
    
    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer
        
        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }
        
        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>Alertas de registos

Registos de alertas criados por alertas regras no registo de análise de ter um **tipo** de **alerta** e um **SourceSystem** de **OMS**.  Possuem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Tipo          | *Alerta* |
| SourceSystem  | *OMS* |
| AlertSeverity | Nível de gravidade do alerta. |
| AlertName     | Nome do alerta. |
| Consulta         | Texto da consulta que foi executado.  |
| QueryExecutionEndTime   | Fim do intervalo de tempo para a consulta. |
| QueryExecutionStartTime | Início do intervalo de tempo para a consulta.  |
| TimeGenerated | Data e hora foi criado o alerta. |

Existem outros tipos de registos alertas criados pela [solução de gestão de alerta](log-analytics-solution-alert-management.md) e por [exporta do Power BI](log-analytics-powerbi.md).  Estes todos tenham um **tipo** de **alerta** , mas são distinguidos por sua **SourceSystem**.




## <a name="next-steps"></a>Próximos passos

- Instale a [solução de gestão de alerta](log-analytics-solution-alert-management.md) para analisar alertas criadas no registo de análise juntamente com alertas recolhidas a partir do Gestor de operações de centro do sistema (SCOM).
- Leia mais sobre [as pesquisas de registo](log-analytics-log-searches.md) que pode gerar alertas.
- Conclua as instruções para [configurar um webook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
- Saiba como escrever [runbooks no Azure automatização](https://azure.microsoft.com/documentation/services/automation) para solucionar uma problemas identificados por alertas.