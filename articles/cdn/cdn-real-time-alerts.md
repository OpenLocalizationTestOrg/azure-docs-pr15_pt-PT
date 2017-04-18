<properties
    pageTitle="Alertas em tempo real de Azure CDN | Microsoft Azure"
    description="Alertas em tempo real no Microsoft Azure CDN. Alertas em tempo real fornecem notificações sobre o desempenho do seu perfil CDN os pontos finais."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="casoper"/>

# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alertas em tempo real no Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]


## <a name="overview"></a>Descrição geral

Este documento explica alertas em tempo real no Microsoft Azure CDN. Esta funcionalidade disponibiliza notificações em tempo real sobre o desempenho do seu perfil CDN os pontos finais.  Pode configurar o correio eletrónico ou alertas HTTP com base em:

* Largura de banda
* Códigos de estado
* Estados de cache
* Ligações

## <a name="creating-a-real-time-alert"></a>Criar um alerta em tempo real

1. No [Portal do Azure](https://portal.azure.com), navegue para o seu perfil CDN.

    ![Pá de perfil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)

2. A partir do pá de perfil CDN, clique no botão **Gerir** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)

    O portal de gestão de CDN é aberta.

3. Paire sobre o separador de **análise** , em seguida, coloque o cursor sobre a lista de opções de **Estatística em tempo real** .  Clique em **alertas em tempo real**.

    ![Portal de gestão de CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)

    É apresentada a lista de configurações de alertas existentes (se existirem).

4. Clique no botão **Adicionar alerta** .

    ![Adicionar botão alerta](./media/cdn-real-time-alerts/cdn-add-alert.png)

    É apresentado um formulário para criar um novo alerta.

    ![Novo formulário de alerta](./media/cdn-real-time-alerts/cdn-new-alert.png)

5. Se quiser que este alerta esteja ativa quando clica em **Guardar**, selecione a caixa de verificação **Com capacidade de alerta** .

6. Introduza um nome descritivo para o alerta no campo **nome** .

7. Na lista pendente **Tipo de multimédia** , selecione **Objeto grande HTTP**.

    ![Tipo de multimédia com objeto grande HTTP selecionado](./media/cdn-real-time-alerts/cdn-http-large.png)

    > [AZURE.IMPORTANT] Tem de selecionar o **Objeto grande HTTP** como o **Tipo de multimédia**.  As outras opções não são utilizadas pelo **CDN Azure a partir do Verizon**.  Falha para selecionar **Objeto grande HTTP** irão provocar o alerta para nunca acionado.

8. Crie uma **expressão** para monitorizar ao selecionar uma **métrica**, o **operador**e o **valor de accionador**.

    - **Métrica**, selecione o tipo de condição que pretende monitorizadas.  **Largura de banda Mbps** é a quantidade da utilização da largura de banda em megabits por segundo.  **Total de ligações** é o número de ligações de HTTP em simultâneo ao nossos servidores edge.  Para obter definições dos vários Estados de cache e os códigos de estado, consulte o artigo [Códigos de estado do Azure CDN Cache](https://msdn.microsoft.com/library/mt759237.aspx) e [Códigos de estado do Azure CDN HTTP](https://msdn.microsoft.com/library/mt759238.aspx)
    - **O operador** é o operador matemático que estabelece a relação entre a métrica e o valor do accionador.
    - **Valor do accionador** é o valor de limite que deve ser cumprido antes de uma notificação será enviada.

    No exemplo abaixo, a expressão tem criei indica que gostaria de ser notificado quando o número de códigos de estado 404 for maior do que 25.

    ![Expressão de exemplo de alerta em tempo real](./media/cdn-real-time-alerts/cdn-expression.png)

9. Para **intervalo**, introduza a frequência que pretende utilizar a expression avaliada.

10. Na **notificação na** lista pendente, selecione quando pretende ser notificado quando a expressão for VERDADEIRO.
    
    - **Começar a condição** indica que será enviada uma notificação quando a condição especificada é detectada pela primeira vez.
    - **Fim da condição** indica que será enviada uma notificação quando já não é detectada a condição especificada. Só pode ser acionou esta notificação após a nossa rede monitorizar sistema detetado que ocorreu a condição especificada.
    - **Contínua** indica que uma notificação será enviada sempre que a rede do sistema de monitorização Deteta a condição especificada. Tenha em atenção que a rede monitorizar sistema irá dar apenas uma vez por intervalo para a condição especificada.
    - **Começar a condição e de fim** indica que uma notificação será enviada a primeira vez que a condição especificada é detectada e mais uma vez quando a condição é já não detectada.

11. Se quiser receber notificações por correio eletrónico, selecione a caixa de verificação **notificar por correio eletrónico** .  

    ![Notificar por formulário de correio eletrónico](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    No campo **para** , introduza o endereço de e-mail onde pretende que as notificações enviou. Para o **Assunto** e **corpo**, pode deixar a predefinição ou pode personalizar a mensagem de utilizar a lista de **palavras-chave disponíveis** para inserir dinamicamente dados alertas quando a mensagem é enviada.

    > [AZURE.NOTE] Pode testar a notificação de e-mail ao clicar no botão **Testar notificação** , mas apenas depois de ter sido guardada a configuração do alerta.

12. Se quiser que as notificações para publicar exclusivamente para um servidor web, selecione a caixa de verificação **notificar por HTTP Post** .

    ![Notificar por formulário HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)

    No campo **Url** , introduza o URL onde pretende que a mensagem de HTTP publicou. Na caixa de texto **cabeçalhos** , introduza os cabeçalhos de HTTP sejam enviadas no pedido de.  Pode personalizar a mensagem de utilizar a lista de **palavras-chave disponíveis** para inserir dinamicamente dados alertas quando a mensagem é enviada para **corpo** .  **Cabeçalhos** e **corpo** predefinido para uma carga de útil XML semelhante de exemplo abaixo.

    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```

    > [AZURE.NOTE] Pode testar a notificação de HTTP Post ao clicar no botão **Testar notificação** , mas apenas depois de ter sido guardada a configuração do alerta.

13. Clique no botão **Guardar** para guardar a configuração de alerta.  Se tiver marcado **Com capacidade de alerta** no passo 5, o alerta está agora ativo.

## <a name="next-steps"></a>Próximos passos

- Analisar [uma estatística em tempo real no Azure CDN](cdn-real-time-stats.md)
- Procurar forma mais aprofundada com [relatórios HTTP avançados](cdn-advanced-http-reports.md)
- Analisar [os padrões de utilização](cdn-analyze-usage-patterns.md)

