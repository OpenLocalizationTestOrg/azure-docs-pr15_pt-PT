<properties
    pageTitle="Implementação flighting (testes beta) na aplicação de serviço do Azure"
    description="Saiba como flight novas funcionalidades na sua aplicação ou beta teste as suas atualizações neste tutorial de fim para fim. -Conjuga funcionalidades de aplicação de serviço, como publicação contínua, faixas, encaminhamento de tráfego e integração de informações da aplicação."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cephalin"/>
# <a name="flighting-deployment-beta-testing-in-azure-app-service"></a>Implementação flighting (testes beta) na aplicação de serviço do Azure

Este tutorial mostra-lhe como o fazer *implementações flighting* através da integração as diversas capacidades do [Serviço de aplicação do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e [Informações de aplicação do Azure](/services/application-insights/). 

*Flighting* é um processo de implementação valida uma nova funcionalidade ou alterar com um número limitado de clientes reais e é importante testes num cenário de produção. É semelhante a beta e é por vezes conhecido como "teste controlada voo". Muitos grandes empresas com uma presença na web utilizam esta abordagem para obter validação antecipada no respetivas atualizações de aplicação no respetivo exercícios práticos do [desenvolvimento ágil](https://en.wikipedia.org/wiki/Agile_software_development). Aplicação de serviço de Azure permite-lhe integrar o teste de produção contínuo publicação e informações de aplicação para implementar o mesmo cenário DevOps. Vantagens desta abordagem incluem:

- **Lucro real comentários _antes das_ atualizações são lançadas para produção** - a única coisa melhor do que obtenham comentários, assim que libertar está a ganhar comentários antes de libertar a. Pode testar atualizações com o tráfego do utilizador real e comportamentos mais cedo pretendem no ciclo de vida de produto.
- **Melhorar [desenvolvimento contínuo orientadas por teste (CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development) ** - ao integrar o teste de produção com integração contínua e instrumentação com informações de aplicação, validação do utilizador acontece mais cedo e automaticamente no seu ciclo de vida do produto. Isto ajuda a reduzir o tempo os investimentos em execução de teste manual.
- **Otimização testar o fluxo de trabalho** - ao automatizar teste de produção com instrumentação monitorização contínua, que pode potencialmente efetuar aos objetivos da vários tipos de testes num único processo, como [integração](https://en.wikipedia.org/wiki/Integration_testing), [regressão](https://en.wikipedia.org/wiki/Regression_testing), [textuais](https://en.wikipedia.org/wiki/Usability_testing), acessibilidade, localização, [Desempenho](https://en.wikipedia.org/wiki/Software_performance_testing), [segurança](https://en.wikipedia.org/wiki/Security_testing)e [aceitação](https://en.wikipedia.org/wiki/Acceptance_testing).

Uma implementação flighting não está praticamente a encaminhar tráfego direto. Nessa implementação que pretende obter conhecimentos aprofundados o mais rapidamente possível, se dever-se uma erros inesperados, degradação do desempenho, problemas da experiência de utilizador. Lembre-se de que está a lidar com os clientes reais. Por isso, para fazê-lo à direita, terá de fazer-se de que configurou sua implementação flighting para recolher todos os dados que precisa para tomar uma decisão para o próximo passo. Este tutorial mostra-lhe como recolher dados com informações de aplicação, mas pode utilizar o novo Relic ou outras tecnologias que se adequa ao seu cenário. 

## <a name="what-you-will-do"></a>O que vai fazer

Neste tutorial, irá aprender trazer os seguinte cenários em conjunto para testar a sua aplicação de serviço de aplicação de produção:

- [Encaminhar o tráfego de produção](app-service-web-test-in-production-get-start.md) para a sua aplicação beta
- [Instrument sua aplicação](../application-insights/app-insights-web-track-usage.md) para obter métricas úteis
- Implemente a sua aplicação beta e controlar métricas de aplicação direto contínua
- Comparar métrica entre a aplicação de produção e a aplicação beta para ver como as alterações de código traduzem nos resultados

## <a name="what-you-will-need"></a>O que irá precisar

-   Uma conta do Azure
-   Uma conta de [GitHub](https://github.com/)
- Visual Studio 2015 - pode transferir a [edição de Comunidade](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx).
-   Shell de Git (instalado com o [GitHub para Windows](https://windows.github.com/)) - Isto permite-lhe executar a Git e o PowerShell comandos na mesma sessão
-   Bits mais recentes do [PowerShell do Azure](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)
-   Noções básica sobre dos seguintes procedimentos:
    -   Implementação do modelo de [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) (consulte o artigo [Implementar uma aplicação complexa seja previsível no Azure](app-service-deploy-complex-application-predictably.md))
    -   [Git](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Precisa de uma conta Azure para concluir este tutorial:
> + Pode [Abrir uma conta Azure gratuitamente](/pricing/free-trial/) - obtém créditos pode utilizar para experimentar o nosso pagos serviços Azure e mesmo depois de que está a ser utilizados para cima pode manter a conta e utilização livre Azure serviços, como Web Apps.
> + Pode [Ativar benefícios de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/) - subscrição o Visual Studio fornece-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.
>
> Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="set-up-your-production-web-app"></a>Configurar o seu produção web app

>[AZURE.NOTE] O script utilizado neste tutorial irá configurar automaticamente a publicação contínua a partir do repositório de GitHub. Isto requer que as suas credenciais GitHub já sejam encontram armazenadas no Azure, caso contrário, a implementação em script irá falhar ao tentar configurar definições de controlo de origem para o web apps.
>
>Para armazenar as suas credenciais GitHub Azure, crie uma aplicação web no [Portal do Azure](https://portal.azure.com/) e [Configurar GitHub implementação](app-service-continuous-deployment.md#Step7). Só tem de fazer uma vez.

Num cenário de DevOps típico, tiver uma aplicação que está a ser executado em direto no Azure e que pretende efetuar alterações ao mesmo através da publicação contínua. Neste cenário, irá implemente produção um modelo que tenha desenvolvido e testado.

1.  Crie o seu próprio bifurcação do repositório [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) . Para obter informações sobre como criar o seu bifurcação, consulte o artigo [bifurcação um Repo](https://help.github.com/articles/fork-a-repo/). Quando a bifurcação estiver criada, pode vê-lo no seu browser.

    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  Abra uma sessão de Git Shell. Se ainda não tenha da Shell de Git, instale agora [GitHub para Windows](https://windows.github.com/) .
3.  Crie um clonar local do seu bifurcação executando o seguinte comando:

        git clone https://github.com/<your_fork>/ToDoApp.git

4.  Assim que tiver o seu clonar local, navegue para * &lt;repository_root >*\ARMTemplates e executar o deploy.ps1 script com um sufixo exclusivo, conforme apresentado abaixo:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.  Quando lhe for pedido, escreva o nome de utilizador pretendida e a palavra-passe de acesso de base de dados. Porque é necessário especificá-las novamente ao atualizar o grupo de recursos, lembre-se as suas credenciais de base de dados.

    Deverá ver o progresso de aprovisionamento recursos de vários Azure. Quando concluir a implementação, o script irá iniciar a aplicação no browser e dar-lhe um sinal sonoro amigável.
    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.  Novamente na sessão de Git Shell, execute:

        .\swap –Name ToDoApp<your_suffix>

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.  Quando tiver terminado do script, volte ao navegar para endereço o front-end (http://ToDoApp*&lt;your_suffix >*.azurewebsites.net/) para ver a aplicação a ser executada em produção.
5.  Inicie sessão no [Portal do Azure](https://portal.azure.com/) e veja o que é criado.

    Deverá conseguir ver duas aplicações web no mesmo grupo de recursos, uma com o `Api` sufixo no nome. Se observe a vista do grupo de recursos, também verá a base de dados do SQL e servidor, o plano de serviço de aplicação e as faixas de transição para o web apps. Procure entre os diferentes recursos e compará-los com * &lt;repository_root >*\ARMTemplates\ProdAndStage.json para ver como estão configurados no modelo.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Tem de configurar a aplicação de produção.  Agora, vamos imagine que recebe de comentários que textuais fraca para a aplicação. Por isso, decida investigar. Vai instrumento a sua aplicação para lhe dar feedback.

## <a name="investigate-instrument-your-client-app-for-monitoringmetrics"></a>Investigar: Instrumento a sua aplicação de cliente para/métricas de monitorização

5. Abrir * &lt;repository_root >*\src\MultiChannelToDo.sln no Visual Studio.
6. Restaurar todos os pacotes de Nuget clicando com o solução > **Gerir pacotes de NuGet para solução** > **Restaurar**.
6. Botão direito do rato **MultiChannelToDo.Web** > **Adicionar telemetria de informações de aplicação** > **Configurar definições** > alterar o grupo de recursos para ToDoApp*&lt;your_suffix >* > **Adicionar informações de aplicação para o Project**.
7. No Portal do Azure, abra o pá para o recurso de conhecimentos aprofundados de aplicação **MultiChannelToDo.Web** . Na peça de **Estado de funcionamento da aplicação** , clique em **aprender a recolher dados de carregamento de página do browser** > copiar código.
7. Adicionar o código copiado para instrumentação JS para * &lt;repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml, imediatamente antes do remate `<heading>` etiqueta. Deve conter a chave de instrumentação exclusivo do seu recurso de conhecimentos aprofundados de aplicação.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>

11. Envie eventos personalizados para as informações de aplicação para o rato cliques ao adicionar o código seguinte para a parte inferior do corpo:

        <script>
            $(document.body).find("*").click(function(event) {

                appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
            });
        </script>

    Este fragmento de JavaScript envia um evento personalizado para informações de aplicação sempre que um utilizador clica em qualquer lugar no web app.

12. Na Shell de Git, consolide e transmitir as suas alterações para sua bifurcação no GitHub. Aguarde, em seguida, para que os clientes Atualize o browser.

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.  Trocar a aplicação implementado as alterações à produção:

        .\swap –Name ToDoApp<your_suffix>

13. Navegue para o recurso de informações da aplicação que configurou. Clique em eventos de personalizada.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    Se não vir métricas para eventos personalizados, aguarde alguns minutos e clique em **Atualizar**.

Suponha que vê gostar de um gráfico abaixo:

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

E a grelha de evento abaixo do mesmo:

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

De acordo com o código da aplicação ToDoApp, o evento **botão** corresponde ao botão para submeter e o evento de **entrada** corresponde à caixa de texto. Até ao momento, coisas fazem sentido. No entanto, mas parece existe uma boa quantidade de cliques e muito poucos cliques nos itens a fazer (os eventos de **lista horizontal** ).

Com base em este formulário o seu hipótese de que alguns utilizadores estão confundidas que parte da IU é clicável e é porque o cursor está estilizado para seleção de texto quando-paira sobre os itens de lista e os seus ícones.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Isto pode ser um exemplo contrived. No entanto, que vai fazer uma melhoria para a sua aplicação e, em seguida, execute uma implementação flighting para obter comentários da eficiência de utilização de clientes direto.

### <a name="instrument-your-server-app-for-monitoringmetrics"></a>Instrumento a aplicação do servidor para/métricas de monitorização
Este é uma tangente uma vez que o cenário demonstrado neste tutorial negócios apenas com a aplicação de cliente. No entanto, para integridade irá configurar a aplicação do lado do servidor.

6. Botão direito do rato **MultiChannelToDo** > **Adicionar telemetria de informações de aplicação** > **Configurar definições** > alterar o grupo de recursos para ToDoApp*&lt;your_suffix >* > **Adicionar informações de aplicação para o Project**.
12. Na Shell de Git, consolide e transmitir as suas alterações para sua bifurcação no GitHub. Aguarde, em seguida, para que os clientes Atualize o browser.

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.  Trocar a aplicação implementado as alterações à produção:

        .\swap –Name ToDoApp<your_suffix>

Já está!

## <a name="investigate-add-slot-specific-tags-to-your-client-app-metrics"></a>Investigar: Adicionar etiquetas de ranhura específicos a sua métricas de aplicação de cliente

Nesta secção, irá configurar as faixas de implementação diferentes para enviar telemetria ranhura específicas para o mesmo recurso de informações da aplicação. Desta forma, pode comparar os dados de telemetria entre o tráfego de faixas diferentes (ambientes de implementação) para ver facilmente o efeito das suas alterações de aplicação. Ao mesmo tempo, pode separar o tráfego de produção do resto para que possa continuar monitorizar a sua aplicação de produção, conforme necessário.

Uma vez que está a recolha de dados sobre o comportamento do cliente, irá [Adicionar um inicializador de telemetria para o seu código JavaScript](../application-insights/app-insights-api-custom-events-metrics.md#js-initializer) index.cshtml. Se pretende testar o desempenho do lado do servidor, por exemplo, também pode fazer da mesma forma no seu código de servidor (consulte o artigo [Aplicação informações API para eventos personalizados e métricas](../application-insights/app-insights-api-custom-events-metrics.md).

1. Em primeiro lugar, adicionar o código bewteen as duas `//` comentários abaixo no JavaScript bloquear que o utilizador adicionado à `<heading>` marcar anterior.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Este código inicializador faz com que o `appInsights` objeto para adicionar a uma propriedade personalizada denominado `Environment` para cada porção de telemetria envia.

2. Em seguida, adicione esta propriedade personalizada como uma [definição de ranhura](web-sites-staged-publishing.md#AboutConfiguration) para a sua aplicação web no Azure. Para fazer isto, execute os seguintes comandos na sessão de Git Shell.

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    A Web. config no projeto já define o `environment` definição de aplicação. Com esta definição, quando testar a aplicação localmente, seu métricas serão marcadas com `VS Debugger`. No entanto, quando prime as suas alterações para Azure, Azure irá encontrar e utilizar o `environment` app em vez disso, a definição configuração do web app e o seu métricas serão marcados com `Production`.

3. Consolide e transmitir as alterações de código para sua bifurcação no GitHub e, em seguida, aguarde para os seus utilizadores utilizar a nova aplicação (é Atualize o browser). Demora cerca de 15 minutos para a nova propriedade ser apresentada na aplicação conhecimentos de forma `MultiChannelToDo.Web` recurso.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. Agora, ir novamente para o pá **eventos personalizados** e filtrar as métricas `Environment=Production`. Agora deverá conseguir ver todos os novos eventos personalizados na ranhura produção com este filtro.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. Clique no botão **Favoritos** para guardar as definições atuais do Explorador de métricas algo parecido com **eventos personalizados: produção**. Facilmente pode alternar entre a esta vista e uma vista de ranhura implementação mais tarde.

    > [AZURE.TIP] Para análise ainda mais eficientes, considere a [integração do seu recurso de informações da aplicação com o Power BI](../application-insights/app-insights-export-power-bi.md).

### <a name="add-slot-specific-tags-to-your-server-app-metrics"></a>Adicionar etiquetas de ranhura específicos a sua métricas de aplicação do servidor
Novamente, para integridade irá configurar a aplicação do lado do servidor. Ao contrário da aplicação de cliente que é implementada no JavaScript, etiquetas ranhura específicas para a aplicação de servidor é implementada com código .NET.

1. Abrir * &lt;repository_root >*\src\MultiChannelToDo\Global.asax.cs. Adicionar o bloco de código abaixo, imediatamente antes do remate chaveta de espaço de nomes.

        namespace MultiChannelToDo
        {
                ...

                // Begin new code
            public class ConfigInitializer
            : ITelemetryInitializer
            {
                void ITelemetryInitializer.Initialize(ITelemetry telemetry)
                {
                    telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
                }
            }
                // End new code
        }

2. Corrigir os erros de resolução do nome adicionando o `using` declarações abaixo para o início do ficheiro:

        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;

3. Adicionar o código abaixo para o início da `Application_Start()` método:

        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. Consolide e transmitir as alterações de código para sua bifurcação no GitHub e, em seguida, aguarde para os seus utilizadores utilizar a nova aplicação (é Atualize o browser). Demora cerca de 15 minutos para a nova propriedade ser apresentada na aplicação conhecimentos de forma `MultiChannelToDo` recurso.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## <a name="update-set-up-your-beta-branch"></a>Actualização: Configurar o seu ramo beta

2. Abrir * &lt;repository_root >*\ARMTemplates\ProdAndStagetest.json e localizar o `appsettings` recursos (Procurar `"name": "appsettings"`). Existem 4-las, uma para cada ranhura. 

2. Para cada `appsettings` recurso, adicionar um `"environment": "[parameters('slotName')]"` definição de aplicação para o fim da `properties` matriz. Não se esqueça terminar a linha anterior com um ponto e vírgula.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
    
    Que acabou de adicionar o `environment` definição de aplicação para todas as faixas no modelo.
    
2. No mesmo ficheiro, localize o `slotconfignames` recursos (Procurar `"name": "slotconfignames"`). Existem 2-las, uma para cada aplicação.

2. Para cada `slotconfignames` recurso, adicionar `"environment"` para o fim da `appSettingNames` matriz. Não se esqueça terminar a linha anterior com um ponto e vírgula.

    Que acabou de criar o `environment` aplicação definição joystick para respectiva ranhura respetivos implementação para ambas as aplicações.  

3. Na sua sessão de Git Shell, execute os seguintes comandos com o mesmo sufixo do grupo de recursos que utilizou antes.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. Quando lhe for pedido, especifique as credenciais de base de dados SQL mesmo, como antes. Em seguida, quando lhe for pedido para atualizar o grupo de recursos, escreva `Y`, em seguida, `ENTER`.

    Assim que o script termina, todos os recursos no grupo de recursos original são mantidos, mas uma nova ranhura denominada "beta" é criada no mesmo com a configuração do mesmo como ranhura "Transição" que foi criada no início.

    >[AZURE.NOTE] Este método de criação de implementação diferentes ambientes é diferente do método no [desenvolvimento de Agile software com a aplicação de serviço de Azure](app-service-agile-software-development.md). Aqui, pode cria ambientes de implementação com faixas de implementação, onde existem como criar ambientes de implementação com grupos de recursos. Gerir ambientes de implementação com grupos de recursos permite-lhe manter o ambiente de produção off-limits para os programadores, mas não é fácil de teste de produção, que pode efetuar facilmente com faixas.

Se pretender, também pode criar uma aplicação alfa através da execução

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Para este tutorial, é irá utilizar apenas o manter a aplicação beta.

## <a name="update-push-your-updates-to-the-beta-app"></a>Actualização: Transmitir as suas atualizações para a aplicação beta

Voltar para a sua aplicação que pretende para melhorar.

1. Certifique-se que está agora no seu ramo beta

        git checkout beta

2. No * &lt;repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml, localize o `<li>` tag e adicionar o `style="cursor:pointer"` atributo, conforme apresentado abaixo.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. consolidar e push para Azure.

4. Certifique-se de que a alteração agora é reflectida na ranhura beta ao navegar para http://todoapp*&lt;your_suffix >*-beta.azurewebsites.net/. Se não vir a alteração ainda, atualize o seu browser para obter o código javascript novo.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Agora que tem a alteração em execução no ranhura beta, está pronto para efetuar uma implementação flighting.

## <a name="validate-route-traffic-to-the-beta-app"></a>Validar: Encaminhar tráfego para a aplicação beta

Nesta secção, irá encaminhar o tráfego para a aplicação beta. For sake of clareza de demonstração, que vai para encaminhar uma parte significativa o tráfego de utilizador para o mesmo. Na realidade, a quantidade de tráfego que pretende encaminhar dependem sua situação específica. Por exemplo, se o site estiver na escala do microsoft.com, em seguida, poderá ter menos de uma percentagem do total tráfego para poder obter dados úteis.

1. Na sua sessão de Git Shell, execute os seguintes comandos para encaminhar metade o tráfego de produção para a ranhura beta:

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  O `ReroutePercentage=50` propriedade especifica que 50%, o tráfego de produção será encaminhado para URL a aplicação beta (especificado pela `ActionHostName` propriedade).

2. Desloque-se agora para http://ToDoApp*&lt;your_suffix >*. azurewebsites.net. 50% de tráfego de agora deve ser redirecionado para a ranhura beta.

3. No seu recurso de informações da aplicação, filtrar as métricas por ambiente = "beta".

    > [AZURE.NOTE] Se guardar esta vista filtrada como favorito outra, pode inverter facilmente as vistas do explorer métrica entre as vistas de beta e de produção.

Suponha que em informações de aplicação, verá algo semelhante ao seguinte:

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

Não só é isto que mostra que não existem muitos cliques mais sobre o `<li>` etiquetas, mas parece haver um picos cliques no `<li>` etiquetas. Em seguida, poderá concluir que as pessoas que descobriu o novo `<li>` etiquetas são clicáveis e agora estiver desmarcação todas as suas tarefas concluídas anteriormente na aplicação.

Com base nos dados da sua implementação flighting, decida que a sua nova IU está pronta para produção.

## <a name="go-live-move-your-new-code-into-production"></a>Aceda direto: mover o seu novo código de erro para produção

Agora está pronto para mover a atualização para produção. O que é ótimo é que agora que sabe que a atualização da já foi validada _antes de_ -seguia produção. Agora pode confiante implemente-o. Uma vez que efetuar uma atualização para a aplicação de cliente AngularJS, apenas validado o código do lado do cliente. Se fosse efetuar alterações à aplicação Web API back-end, conseguiu validar as suas alterações da mesma forma e fácil.

1. Na Shell de Git, remova a regra de encaminhamento de tráfego executando o seguinte comando:

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. Execute os comandos Git:

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. Aguarde alguns minutos para o novo código de implementado a transição ranhura, em seguida, iniciação http://ToDoApp*&lt;your_suffix >*-staging.azurewebsites.net para verificar que a nova atualização é aquece na transição ranhura. Lembre-se de que o ramo principal do seu bifurcação está ligado a ranhura transição da sua aplicação.

3. Agora, trocar a transição ranhura em produção

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## <a name="summary"></a>Resumo ##

Aplicação de serviço de Azure torna mais fácil para pequenas - para médias empresas testar das aplicações de cliente destinado de produção, algo que já foi feito tradicional em empresas de grandes. Começadas, este tutorial lhe atribuiu o conhecimento terá de reúnem aplicação de serviço e informações de aplicação para o tornar possível implementação flighting e até mesmo outros cenários de teste de produção, no seu mundo DevOps. 

## <a name="more-resources"></a>Mais recursos ##

-   [Desenvolvimento de agile software com o serviço de aplicação do Azure](app-service-agile-software-development.md)
-   [Configurar o teste ambientes para web apps no serviço de aplicação do Azure](web-sites-staged-publishing.md)
-   [Implementar uma aplicação complexa seja previsível no Azure](app-service-deploy-complex-application-predictably.md)
-   [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md)
-   [JSONLint - a validação JSON](http://jsonlint.com/)
-   [Git ramificação – ramificação básicos e intercalar](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [Azure PowerShell](../powershell-install-configure.md)
-   [Project Kudu Wiki](https://github.com/projectkudu/kudu/wiki)
