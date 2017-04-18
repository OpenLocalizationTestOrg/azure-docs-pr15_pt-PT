<properties
    pageTitle="Adicionar funcionalidades à sua aplicação web do primeira"
    description="Adicione funcionalidades fantásticas para a sua aplicação web primeira alguns minutos."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/12/2016"
    ms.author="cephalin"
/>

# <a name="add-functionality-to-your-first-web-app"></a>Adicionar funcionalidades à sua aplicação web do primeira

No [Implementar a aplicação web do primeira para Azure dentro de cinco minutos](app-service-web-get-started.md), implementado uma aplicação web do exemplo para a [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md). Neste artigo, irá adicionar rapidamente alguns ótimas funcionalidades para a sua aplicação web implementado. Em alguns minutos, irá:

- impor a autenticação para os seus utilizadores
- Dimensionar automaticamente a sua aplicação
- receber alertas sobre o desempenho da sua aplicação

Independentemente do qual a aplicação que exemplo implementado no artigo anterior, pode seguir ao longo no tutorial.

As atividades de três neste tutorial são apenas alguns exemplos de várias funcionalidades úteis que obter quando introduz a aplicação web do serviço de aplicação. Muitas das funcionalidades estão disponíveis na camada **Free** (que é o que a aplicação web do primeira está em execução no), e pode utilizar a sua avaliação créditos para experimentar a funcionalidades que requerem superior preços camadas. Tenha a certeza de que a aplicação web permanece na camada **Free** , a menos que explicitamente transforma-se uma camada comparar diferentes.

>[AZURE.NOTE] A aplicação web que criou com o Azure clip é executado no **Free** camada, que permite apenas uma instância VM partilhada com as quotas de recursos. Para mais informações sobre que obterá com a camada **livre** , consulte o artigo [limites de aplicação de serviço](../azure-subscription-service-limits.md#app-service-limits).

## <a name="authenticate-your-users"></a>Autenticar os seus utilizadores

Agora, vamos ver como é fácil adicionar autenticação para a sua aplicação (leitura adicional na [Aplicação de serviço de autenticação/autorização](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)).

1. Na pá portal para a sua aplicação, que apenas aberto, clique em **Definições** > **autenticação / autorização**.  
    ![Autenticar - pá definições](./media/app-service-web-get-started/aad-login-settings.png)

2. Clique **** para ativar a autenticação.  

4. Em **Fornecedores de autenticação**, clique em **Azure Active Directory**.  
    ![Autenticar - selecione Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. No pá **Definições do Azure Active Directory** , clique em **Express**, em seguida, clique em **OK**. As predefinições de criar uma nova aplicação do Azure AD no seu diretório predefinido.  
 ![Autenticar - express configuração](./media/app-service-web-get-started/aad-login-express.png)

6. Clique em **Guardar**.  
    ![Autenticar - Guardar configuração](./media/app-service-web-get-started/aad-login-save.png)

    Assim que a alteração for bem sucedida, irá ver a campainha notificação ativar verde, juntamente com uma mensagem amigável.

7. Novamente na pá portal da sua aplicação, clique na ligação de **URL** (ou **procure** na barra de menus). A ligação é um endereço HTTP.  
    ![Autenticar - navegue para o URL](./media/app-service-web-get-started/aad-login-browse-click.png)  
    Mas assim que a aplicação que abre num novo separador, o URL caixa redirecionamentos várias vezes e termina na sua aplicação com um endereço de HTTPS. O que está a ver é que já se encontra tem sessão iniciada sua subscrição do Azure e estiver a autenticados automaticamente na aplicação.  
    ![Autenticar - tem sessão iniciada](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    Por isso, se agora abrir uma sessão de não autenticada num browser diferente, verá um ecrã de início de sessão quando navegar para o mesmo URL.  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
   Se nunca tiver concluído nada com o Azure Active Directory, no diretório da sua predefinição poderá não ter qualquer utilizadores do Azure AD. Nesse caso, provavelmente a única conta no daí é a conta Microsoft com a sua subscrição Azure. Razão pela qual que foram automaticamente tem sessão iniciada aplicação no mesmo browser anterior.
   Pode utilizar essa mesma conta Microsoft para iniciar sessão no também nesta página de início de sessão.

Parabéns, são autenticar todo o tráfego para a sua aplicação web.

Provavelmente, reparou na **autenticação / autorização** pá que pode fazer muitas mais informações, tais como:

- Ativar o início de sessão de rede social
- Ativar várias opções de início de sessão
- Alterar o comportamento predefinido quando as pessoas navegar pela primeira vez, para a sua aplicação

Aplicação de serviço fornece que necessita de uma solução de ativar chave para algumas da autenticação comuns, pelo que não necessita fornecer a lógica de autenticação si mesmo.
Para mais informações, consulte o artigo [Aplicação autenticação/autorização de serviço](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

## <a name="scale-your-app-automatically-based-on-demand"></a>Dimensionar a sua aplicação automaticamente com base na procura

Seguinte, vamos autoscale sua aplicação para que-lo será automaticamente ajustá-la capacidade para responder à procura de utilizador (leitura adicional na [escala para cima da sua aplicação no Azure](web-sites-scale.md) e a [contagem de instâncias de dimensionar automaticamente ou manualmente](../monitoring-and-diagnostics/insights-how-to-scale.md)).

Resumidamente, a aplicação web do qual escala de duas maneiras:

- [Dimensionar](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obter mais CPU, memória, espaço em disco e funcionalidades adicionais, como VMs dedicadas, domínios personalizados e certificados, teste faixas, autoscaling e muito mais. Dimensionar alterando a camada comparar da sua aplicação pertence o plano de serviço de aplicação.
- [Escala de saída](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumentar o número de VM instâncias que executar a sua aplicação.
É possível dimensionar saída até 50 instâncias, dependendo do seu comparar camada.

Sem ainda mais ado, vamos configurar autoscaling.

1. Primeiro, vamos Dimensionar para cima, para ativar autoscaling. Na pá portal da sua aplicação, clique em **Definições** > **Escala para cima (plano de serviço de aplicação)**.  
    ![Dimensionar o - pá definições](./media/app-service-web-get-started/scale-up-settings.png)

2. Desloque-se e selecione a camada **S1 padrão** , a camada mais baixa que suporta autoscaling (dentro de um círculo no captura de ecrã), em seguida, clique em **Selecionar**.  
    ![Dimensionar - selecionar camada](./media/app-service-web-get-started/scale-up-select.png)

    Terminar dimensionamento para cima.

    >[AZURE.IMPORTANT] Este camada expends seu créditos de avaliação gratuitos. Se tiver uma conta de pagamento por utilização,-tarifas à sua conta.

3. Em seguida, vamos configurar autoscaling. Na pá portal da sua aplicação, clique em **Definições** > **Escala saída (plano de serviço de aplicação)**.  
    ![Escala de saída - pá definições](./media/app-service-web-get-started/scale-out-settings.png)

4. Alterar a **escala pela** **Percentagem de CPU**. Os controlos de deslize por baixo da lista pendente atualizar em conformidade. Em seguida, defina um intervalo de **instâncias** entre **1** e **2** e um **intervalo de destino** entre **40** e **80**. Fazê-lo ao escrever nas caixas ou movendo os controlos de deslize.  
 ![Dimensionar saída - configurar autoscaling](./media/app-service-web-get-started/scale-out-configure.png)

    Baseado nesta configuração, a aplicação automaticamente escalas saída quando a utilização da CPU for superior a 80% e escalas no quando a utilização da CPU for inferior a 40%.

5. Clique em **Guardar** na barra de menus.

Parabéns, a sua aplicação está autoscaling.

É provavelmente, reparou no pá **Definições de escala** que pode fazer muitas mais informações, tais como:

- Dimensionar para um número específico de instâncias manualmente
- Dimensionar por outras métricas de desempenho, tal como fila de percentagem ou o disco de memória
- Personalizar o comportamento de dimensionamento quando uma regra de desempenho é activada
- Autoscale numa agenda
- Definir o comportamento da autoscaling para um evento de futuro

Para mais informações sobre a sua aplicação de dimensionamento, consulte o artigo [Dimensionar para cima da sua aplicação no Azure](../app-service-web/web-sites-scale.md). Para mais informações sobre escalar para fora, consulte a [contagem de instâncias de dimensionar automaticamente ou manualmente](../monitoring-and-diagnostics/insights-how-to-scale.md).

## <a name="receive-alerts-for-your-app"></a>Receber alertas para a sua aplicação

Agora que a sua aplicação está autoscaling, o que acontece quando chega a contagem de instância máximo (2) e CPU é acima utilização pretendida (80%)?
Pode definir um alerta (leitura adicional nas [notificações de alerta de receção](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)) para o informar de que esta situação, de modo que pode dimensionar ainda mais cima/fora a aplicação, por exemplo. Vamos configurar rapidamente um alerta para este cenário.

1. Na pá portal da sua aplicação, clique em **Ferramentas** > **alertas**.  
    ![Alertas - pá definições](./media/app-service-web-get-started/alert-settings.png)

2. Clique em **Adicionar alerta**. Em seguida, na caixa do **recurso** , selecione o recurso que termina com **(serverfarms)**. Esse é o seu plano de serviço de aplicação.  
    ![Alertas - Adicionar alerta para o plano de serviço de aplicação](./media/app-service-web-get-started/alert-add.png)

3. Especifique o **nome** como `CPU Maxed`, **métrica** como **Percentagem de CPU**e **limiar** como `90`, em seguida, selecione **os proprietários de correio eletrónico, contribuintes e os leitores**e, em seguida, clique em **OK**.   
 ![Alertas - configurar alerta](./media/app-service-web-get-started/alert-configure.png)

    Quando o Azure termina de criar o alerta, irá vê-los no pá **alertas** .  
    ![Alertas - vista concluída](./media/app-service-web-get-started/alert-done.png)

Parabéns, agora está a receber alertas.

Esta definição de alerta verifica a utilização da CPU em cinco minutos. Se vai esse número acima 90%, irá receber um alerta de correio eletrónico, juntamente com qualquer pessoa que está autorizado. Para ver todas as pessoas que está autorizado a receber os alertas, regresse à pá portal da sua aplicação e clique no botão do **Access** .  
![Consulte o artigo identificar quem recebe alertas](./media/app-service-web-get-started/alert-rbac.png)

Deverá ver que **os administradores de subscrição** já se encontram o **proprietário** da aplicação. Este grupo seria incluir a se for o administrador da conta da sua subscrição do Azure (por exemplo, a subscrição de avaliação). Para mais informações sobre o controlo de acesso baseado em funções Azure, consulte o artigo [Azure Role-Based o controlo de acesso](../active-directory/role-based-access-control-configure.md).

> [AZURE.NOTE] Regras de alertas é uma funcionalidade Azure. Para mais informações, consulte o artigo [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="next-steps"></a>Próximos passos

Na sua forma para configurar o alerta, provavelmente, reparou um conjunto avançado de ferramentas no pá **Ferramentas** . Aqui, pode resolver problemas de desempenho do monitor, teste para vulnerabilidades, gerir os recursos, interagir com a consola VM e adicionar extensões úteis. Vamos convidar para clicar em cada um destas ferramentas para descobrir as ferramentas simples e poderoso na sua sugestões dedo.

Saiba como fazer mais com a sua aplicação implementada. Eis apenas uma lista parcial:

- [Comprar e configurar um nome de domínio personalizado](custom-dns-web-site-buydomains-web-app.md) - comprar um domínio apelativo para a sua aplicação web em vez do *. azurewebsites.net domínio. Ou utilizar um domínio que já possui.
- [Configurar ambientes de transição](web-sites-staged-publishing.md) - implemente a sua aplicação ao URL de uma transição antes de colocá-lo para produção. Atualize a sua aplicação web ao vivo com confiança. Configure uma solução de DevOps elaborada com vários faixas de implementação.
- [Configurar a implementação contínua](app-service-continuous-deployment.md) - integrar implementação de aplicações no seu sistema de controlo de origem. Implemente Azure com cada consolidação.
- [Access no local recursos](web-sites-hybrid-connection-get-started.md) - Access existente no local da base de dados ou sistema CRM.
- [Agregar a sua aplicação](web-sites-backup.md) - configurar anterior uma cópia de segurança e restaurar para a sua aplicação web. Preparar para falhas inesperadas e recuperar dos mesmos.
- [Ativar registos de diagnóstico](web-sites-enable-diagnostic-log.md) - lerem registos do IIS rastreios Azure ou da aplicação. Lê-las numa sequência, transfira-os ou a porta-las na [Aplicação de informações](../application-insights/app-insights-overview.md) para uma análise ativar chave.
- [Digitalizar a sua aplicação para vulnerabilidades](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
digitalizar a sua aplicação web ameaças moderna utilizando o serviço fornecido pela [Tinfoil segurança](https://www.tinfoilsecurity.com/).
- [Executar tarefas em segundo plano](../azure-functions/functions-overview.md) - executar tarefas para processamento de dados, relatórios, etc.
- [Saiba como funciona a aplicação de serviço](../app-service/app-service-how-works-readme.md)
