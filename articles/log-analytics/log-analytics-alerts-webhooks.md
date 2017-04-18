<properties
   pageTitle="Exemplo de alerta webhook de análise de registo"
   description="Uma das ações que pode executar no resposta a um alerta de análise de registo é uma *webhook*, que permite-lhe invocar um processo externo através de um pedido HTTP único. Este artigo explica um exemplo de criação de uma ação webhook num alerta de análise de registo com a margem."
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
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="webhooks-in-log-analytics-alerts"></a>Webhooks em alertas de análise de registo

Uma das ações que pode executar no resposta a um [alerta de análise de registo](log-analytics-alerts.md) é uma *webhook*, que permite-lhe invocar um processo externo através de um pedido HTTP único.  Pode ler sobre detalhes de alertas e webhooks no [alertas no registo de análise](log-analytics-alerts.md)

Neste artigo, explicaremos a através de um exemplo de criação de uma ação webhook num alerta de análise de registo com a margem que é um serviço de mensagens.

>[AZURE.NOTE] Tem de ter uma conta de folga para concluir este exemplo.  Pode inscrever-se para uma conta na [slack.com](http://slack.com)gratuita.

## <a name="step-1---enable-webhooks-in-slack"></a>Passo 1 - ativar webhooks na margem
2.  Inicie sessão no folga na [slack.com](http://slack.com).
3.  Selecione um canal na secção **canais** no painel esquerdo.  Este é o canal que será enviada para a mensagem.  Pode selecionar um dos canais predefinida como **Geral** ou **aleatório**.  Num cenário de produção, provavelmente pretender criar um canal especial como **criticalservicealerts**. <br>

    ![Folga canais](media/log-analytics-alerts-webhooks/oms-webhooks01.png)

3. Clique em **Adicionar uma aplicação ou integração personalizada** para abrir o diretório de aplicações.
3.  Escreva *webhooks* na caixa de pesquisa e, em seguida, selecione **WebHooks recebidas**. <br>

    ![Folga canais](media/log-analytics-alerts-webhooks/oms-webhooks02.png)

4.  Clique em **instalar** junto ao seu nome de equipa.
5.  Clique em **Adicionar a configuração**.
6.  Selecione o o canal que vai utilizar para este exemplo e, em seguida, clique em **Adicionar recebidas WebHooks integração**.  
6. Copie o **URL de Webhook**.  Qual vai ser colar isto para a configuração do alerta. <br>

    ![Folga canais](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Passo 2 - Criar regra alerta no registo de análise
1.  [Criar uma regra de alerta](log-analytics-alerts.md) com as seguintes definições.
    - Consulta:```    Type=Event EventLevelName=error ```
    - Verificar a existência deste alerta cada: 5 minutos
    - O número de resultados é: superior a 10
    - Sobre esta janela de tempo: 60 minutos
    - Selecione **Sim** para **Webhook** e **não** para as outras ações.
7. Cole o URL de margem para o campo de **Webhook URL** .
8. Selecione a opção para **incluir uma carga de útil JSON personalizada**.
9. A margem espera uma carga útil formatada JSON com um parâmetro de *texto*com o nome.  Este é o texto que será apresentado na mensagem que cria.  Pode utilizar um ou mais dos parâmetros de alertas utilizando o *#* símbolo tal tal como no exemplo seguinte.

    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```

    ![carga útil JSON de exemplo](media/log-analytics-alerts-webhooks/oms-webhooks07.png)

9.  Clique em **Guardar** para guardar a regra de alerta.

10. Aguarde tempo suficiente para que um alerta para ser criadas e, em seguida, selecione margem para uma mensagem que vai ser semelhante ao seguinte.

    ![exemplo webhook na margem](media/log-analytics-alerts-webhooks/oms-webhooks08.png)


### <a name="advanced-webhook-payload-for-slack"></a>Avançados carga webhook útil para margem

Pode personalizar extensivamente mensagens de entrada com margem. Para mais informações, consulte o artigo [Webhooks recebidos](https://api.slack.com/incoming-webhooks) no Web site da margem. Segue-se uma mais complexa carga útil para criar uma mensagem formatada com formatação:

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Isto iria gerar uma mensagem na margem semelhante ao seguinte.

![mensagem de exemplo na margem](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Resumo

Com esta regra alerta num local, teria de uma mensagem enviada para a margem sempre que existe correspondência.  

Este é apenas um exemplo de uma ação que pode criar no resposta a um alerta.  Pode criar uma ação de webhook chamadas de outro serviço de externo, uma ação de livro execuções para iniciar um livro de execuções Azure automatização ou uma ação de correio eletrónico para enviar uma mensagem de correio para si próprio ou outros destinatários.   

## <a name="next-steps"></a>Próximos passos

- Saiba mais acerca de [alertas no registo de análise](log-analytics-alerts.md) , incluindo outras ações.
- [Criar runbooks no Azure automatização](../automation/automation-webhooks.md) que podem ser chamados a partir de um webhook.
