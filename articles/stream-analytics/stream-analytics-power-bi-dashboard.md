<properties
    pageTitle="Dashboard de BI Power na sequência Analytics | Microsoft Azure"
    description="Utilize um dashboard do Power BI transmissão em tempo real para recolher informações da empresa e analisar dados de grande volume a partir de uma tarefa de análise da cadeia."
    keywords="dashboard de análise, dashboard em tempo real"
    services="stream-analytics"
    documentationCenter=""
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

#  <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Transmitir em fluxo Analytics e do Power BI: um dashboard de análise em tempo real de transmissão de dados

Azure da cadeia Analytics permite-lhe tirar partido de um das líder ferramentas de business intelligence, o Microsoft Power BI. Saiba como utilizar a análise da cadeia de Azure para analisar o volume de alta, transmissão dados e obter o conhecimentos aprofundados num dashboard de análise do Power BI em tempo real.

Utilize o [Microsoft Power BI](https://powerbi.com/) para criar um dashboard direto rapidamente. [Veja um vídeo que ilustram o cenário](https://www.youtube.com/watch?v=SGUpT-a99MA).

Neste artigo, saiba como criar o seus próprio ferramentas de informações da empresa empresariais personalizadas através da utilização do Power BI como uma saída para as tarefas de análise da cadeia de Azure e utilizam um dashboard em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

* Conta do Microsoft Azure
* Uma entrada de dados para a tarefa de análise da cadeia consumir dados em tempo real a partir de. Análise da cadeia aceita entrada a partir do armazenamento Azure evento concentradores ou BLOBs do Azure.  
* Conta escolar ou profissional do Power BI

## <a name="create-azure-stream-analytics-job"></a>Criar tarefa Azure da cadeia Analytics

A partir do [Portal clássica Azure](https://manage.windowsazure.com), clique em **novo, serviços de dados, a análise da cadeia, criação rápida**.

Especificar os seguintes valores, em seguida, clique em **criar a análise de fluxo de trabalho**:

* **Nome da tarefa** - introduza um nome de tarefa. Por exemplo, **DeviceTemperatures**.
* **Região** - selecione a região onde pretende que a tarefa localizada. Considere a colocação a tarefa e centro do evento na mesma região para assegurar um desempenho ideal e evitar sempre custos de transferência de dados entre regiões.
* **Conta de armazenamento** - selecione a conta de armazenamento que pretende utilizar para armazenar dados de monitorização para todas as tarefas de análise da cadeia em execução no interior esta região. Tem a opção escolher uma conta de armazenamento existente ou criar um novo.

Clique em **Análise de sequência** no painel da esquerda para listar as tarefas de análise da cadeia.

![graphic1][graphic1]

> [AZURE.TIP] A nova tarefa será listada com o estado **Não iniciou**. Repare que o botão **Iniciar** na parte inferior da página está desativado. Este é um comportamento esperado como tem de configurar a entrada de tarefa, saída, consulta, e assim sucessivamente antes de poder iniciar a tarefa.

## <a name="specify-job-input"></a>Especifique a entrada de tarefa

Para este tutorial, vamos são partindo do princípio que está a utilizar o evento concentrador como uma entrada com serialização JSON e codificação UTF-8.

* Clique no nome da tarefa.
* Clique em **entradas** situado na parte superior da página e, em seguida, clique em **Adicionar entrada**. A caixa de diálogo que é aberta irá guiá-lo-o através de um número de passos para configurar o seu teclado.
*   Selecione **fluxo de dados**e, em seguida, clique no botão à direita.
*   Selecione **Concentrador de evento**e, em seguida, clique no botão à direita.
*   Escreva ou selecione os seguintes valores na terceira página:
  * **Alias entrada** - introduza um nome amigável para esta tarefa de entrada. Tenha em atenção que irá utilizar este nome na consulta mais tarde.
  * É **Concentrador de evento** - se o concentrador de evento criado na mesma subscrição do que a tarefa de análise da cadeia, selecione o espaço de nomes se encontra o concentrador de evento.
*   Se for o concentrador de evento numa subscrição diferente, selecione **Utilizar concentrador de evento a partir de outra subscrição** e introduzir manualmente as informações para **o espaço de nomes do serviço Bus**, **Nome do concentrador do evento**, **Evento concentrador nome da política**, **A chave de política de concentrador de evento**e **Contagem de partições concentrador de evento**.

> [AZURE.NOTE]  Este exemplo utiliza o número predefinido de partições, que é 16.

* **Nome do concentrador de evento** - selecione o nome do Centro de eventos do Azure tiver.
* **Nome da política concentrador evento** - selecione a política de concentrador de evento para o concentrador de evento que está a utilizar. Certifique-se de que esta política tem gerir permissões.
*   **Grupo de consumidor do evento concentrador** – pode deixar esta vazia ou especificar um grupo do consumidor tiver no seu centro de evento. Note que cada grupo consumidor de um concentrador de evento pode ter apenas 5 leitores de cada vez. Por isso, decida o grupo do consumidor direita para a tarefa em conformidade. Se deixar o campo em branco, irá utilizar o grupo do consumidor predefinido.

*   Clique no botão à direita.
*   Especifique os seguintes valores:
  * **Formato de serializador evento** - JSON
  * **Codificação** - UTF8
*   Clique no botão de verificação para adicionar esta origem de e para verificar se a análise da cadeia com êxito pode ligar para o centro do evento.

## <a name="add-power-bi-output"></a>Adicionar saída do Power BI

1.  Clique em **saída** situado na parte superior da página e, em seguida, clique em **Adicionar saída**. Irá ver o que Power BI estiverem listadas como uma opção de saída.

    ![graphic2][graphic2]  

2.  Selecione **Power BI** e, em seguida, clique no botão à direita.
3.  Irá ver um ecrã de como o seguinte:

    ![graphic3][graphic3]  

4.  Neste passo, fornece uma conta escolar ou profissional no resultado da tarefa a análise da cadeia. Se já tiver conta do Power BI, selecione **Autorizar agora**. Caso contrário, selecione **Inscrever-se agora**. [Eis um blogue boa observar detalhes de inscrição do Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]  

5.  Seguinte irá ver um ecrã de como o seguinte:

    ![graphic4][graphic4]  

Forneça os valores como abaixo:

* **Alias de saída** – que pode incluir qualquer alias de saída é fácil para que se refira. Este alias de saída é particularmente útil se decidir que tenha saídas de vários para a tarefa. Nesse caso, tem de fazer referência a este resultado na sua consulta. Por exemplo, vamos utilizar o valor de alias de saída = "OutPbi".
* **Nome do conjunto de dados** - fornecer um nome de conjunto de dados que pretende que o seu ter de saída do Power BI. Por exemplo, vamos utilizar "pbidemo".
*   **Nome da tabela** - fornecer um nome de tabela em conjunto de dados do resultado do Power BI. Digamos que chamamos-"pbidemo". Atualmente, saída do Power BI a partir de tarefas de análise da cadeia só pode ter uma tabela num conjunto de dados.
*   **Área de trabalho** – Selecione uma área de trabalho no inquilino do Power BI em qual será criado o conjunto de dados.

>   [AZURE.NOTE] Não deve explicitamente criar este conjunto de dados e a tabela na sua conta do Power BI. Serão automaticamente criados quando iniciar a sua tarefa de análise da cadeia e a tarefa é iniciada saída meios Power BI. Se a sua consulta de tarefa não devolver os resultados, o conjunto de dados e a tabela não serão criados.

*   Clique em **OK**, **Testar ligação** e agora exportar a configuração está concluída.

>   [AZURE.WARNING] Tenha também em atenção de que se Power BI já tinha um conjunto de dados e uma tabela com o mesmo nome como um fornecidas nesta tarefa de análise da cadeia, serão substituídos os dados existentes.


## <a name="write-query"></a>Escrever consulta

Aceda ao separador **consulta** da sua tarefa. Escreva a sua consulta, que o resultado que pretende no seu Power BI. Por exemplo, pode ser algo como a seguinte consulta SQL:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



Inicie a sua tarefa. Valide que seu centro de evento está a receber eventos e a sua consulta gera os resultados esperados. Se a sua consulta exporta 0 linhas, conjunto de dados do Power BI e tabelas não serão automaticamente criadas.

## <a name="create-the-dashboard-in-power-bi"></a>Criar o dashboard no Power BI

Aceda a [Powerbi.com](https://powerbi.com) e inicie sessão com a sua conta escolar ou profissional. Se a consulta da tarefa de análise da cadeia apresenta resultados, irá ver o que seu conjunto de dados já tiver sido criado:

![graphic5][graphic5]

Para criar o dashboard, vá para a opção de Dashboards e criar um novo Dashboard.

![graphic6][graphic6]

Neste exemplo estamos irá etiqueta "Demonstração Dashboard"-lo.

Agora, clique no conjunto de dados criado pela sua tarefa de análise da cadeia (pbidemo no nosso exemplo atual). Que vai ser feito para uma página para criar um gráfico na parte superior deste conjunto de dados. Segue-se mas um exemplo de relatórios que pode criar:

Selecione temp Σ e campos de tempo. Será automaticamente acederem à e data de nascimento para o gráfico:

![graphic7][graphic7]

Com esta situação, irão receber automaticamente um gráfico como abaixo:

![graphic8][graphic8]

Na secção valor, clique na lista pendente para baixo para temp e selecione **média** para a temperatura e no gráfico, clique em **visualização** e escolha o **gráfico de linhas**:

![graphic9][graphic9]

Agora, obterá um gráfico de linhas de média ao longo do tempo.  Utilizar a opção do pin como abaixo, pode afixar isto o dashboard que criou anteriormente:

![graphic10][graphic10]

Agora ao visualizar o dashboard com este relatório afixado, verá atualizar o relatório em tempo real. Experimente alterar desta forma os dados nos seus eventos – temp coletor ou algo e irá ver o efeito em tempo real das que refletidas no dashboard.

Nota a que este tutorial demonstrado como criar mas um tipo de gráfico para um conjunto de dados. Power BI pode ajudar a criar outras ferramentas de cliente business intelligence para a sua organização. Outro exemplo de um dashboard do Power BI, veja o vídeo [Introdução ao Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Para obter mais informações sobre como configurar uma saída do Power BI e utilizar grupos de Power BI, reveja a [secção do Power BI](stream-analytics-define-outputs.md#power-bi) de [que compreender a análise da cadeia exporta](stream-analytics-define-outputs.md "exporta Noções sobre a análise da cadeia"). Outro recurso útil para saber mais sobre como criar Dashboards com o Power BI é [Dashboards no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limitações e as melhores práticas

Power BI utiliza as restrições de simultaneidade do e débito conforme é aqui descrito: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Preços do Power BI")

Devido do Power BI cair propriamente dito mais naturalmente casos onde Azure da cadeia Analytics faz uma redução de carga dados significativos.
Recomendamos que utilize TumblingWindow ou HoppingWindow para se certificar de que push dados seria no máximo 1 push/segundo e que a sua consulta cair dentro os requisitos de débito – pode utilizar a seguinte equação para calcular o valor para conceder a janela segundos:

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Por exemplo – se tiver 1.000 dispositivos enviar dados cada segundo, estiver ao Power BI Pro SKU que suporte 1.000.000 linhas/hora e pretende obter dados médios por dispositivo no Power BI que pode fazer no máximo um push em segundos 4 por dispositivo (como mostrado abaixo):  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

O que significa que podemos quiser alterar a consulta original para:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### <a name="powerbi-view-refresh"></a>Obter a atualização de vista

Uma pergunta comuns é "Por que motivo não o dashboard automática de atualização no obter?".

Para realizar esta, no obter utilizam as perguntas e respostas e fazer uma pergunta como "Valor máximo por temp onde carimbo hoje é" e afixar esse mosaico para o dashboard.

### <a name="renew-authorization"></a>Renovar a autorização

Terá de autenticar novamente a sua conta do Power BI se a palavra-passe foi alterado desde que a tarefa foi criada ou última autenticada. Se a autenticação Multifator (MFA) é configurado no seu inquilino do Azure Active Directory (AAD) também será necessário para renovar a autorização de Power BI em 2 semanas. Um sintoma deste problema é sem saída de tarefa e um "erro do utilizador de autenticar" nos registos do operação:

![graphic12][graphic12]

Da mesma forma, se uma tarefa tenta iniciar sessão enquanto o token tiver expirado, irá ocorrer um erro e o início do trabalho irá falhar. O erro será o aspeto algo como abaixo:

![Erro de validação de obter](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 
 

Para resolver este problema, pare o seu trabalho em execução e aceda ao seu resultado do Power BI.  Clique na ligação "Renovar autorização" e reinicie o seu trabalho desde a última vez parado para evitar a perda de dados.

![Obter a renovação de validação](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

Assim que a autorização é atualizada com o Power BI irá ver um alerta verde na área de autorização:

![Obter a renovação de validação](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure sequência Analytics](stream-analytics-introduction.md)
- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Escala Azure da cadeia Analytics tarefas](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png
