<properties
    pageTitle="Introdução ao teste de produção para Web Apps"
    description="Saiba mais sobre o teste na funcionalidade de produção (Sugestão) no Azure aplicação de serviço Web Apps."
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
    ms.date="01/13/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-test-in-production-for-web-apps"></a>Introdução ao teste de produção para Web Apps

Testes de produção ou live-testar a sua aplicação web utilizando o tráfego de cliente direto, é uma estratégia de teste que os programadores de aplicação integram cada vez na sua metodologia de [programação ágil](https://en.wikipedia.org/wiki/Agile_software_development) . Permite-lhe testar a qualidade das suas aplicações com o tráfego de utilizador direto no seu ambiente de produção, por oposição aos dados síntese num ambiente de teste. Por expor a nova aplicação aos utilizadores reais, pode ser informado sobre os problemas reais que sua aplicação poderá cara assim que é implementada. Pode verificar a funcionalidade, o desempenho e o valor das suas atualizações de aplicação contra o volume, velocidade e variedade de tráfego de utilizador real, que nunca pode aproximar num ambiente de teste.

## <a name="traffic-routing-in-app-service-web-apps"></a>Tráfego encaminhamento de na aplicação de serviço Web Apps

Com a funcionalidade encaminhamento de tráfego na [Aplicação de serviço de Azure](http://go.microsoft.com/fwlink/?LinkId=529714), pode direcionar uma parte de tráfego de utilizador direto para um ou mais [faixas de implementação](web-sites-staged-publishing.md)e, em seguida, analisar a sua aplicação com [Informações de aplicação do Azure](/services/application-insights/) [Azure HDInsight](/services/hdinsight/)ou uma ferramenta de terceiros, como [Nova Relic](/marketplace/partners/newrelic/newrelic/) para validar a alteração. Por exemplo, pode implementar os seguinte cenários com a aplicação de serviço:

- Descobrir o funcionais erros ou pinpoint congestionamentos de desempenho nas suas atualizações antes de implementação de ao nível do site
- Efetuar "teste controlada voos" das suas alterações ao medição métricas usibility da aplicação do beta
- Aumentar gradualmente por excesso para uma nova actualização e, correctamente novamente para baixo para a versão atual caso ocorra um erro 
- Otimizar os resultados de negócios da sua aplicação ao executar [A / B testa](https://en.wikipedia.org/wiki/A/B_testing) ou [testes multivariate](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) no várias faixas de implementação

### <a name="requirements-for-using-traffic-routing-in-web-apps"></a>Requisitos para utilizar o encaminhamento de tráfego nas aplicações Web

- A aplicação web do tem de executar na camada **padrão** ou **Premium** , conforme for necessário para vários faixas de implementação.
- Para poder funcione corretamente, o encaminhamento de tráfego requer os cookies estejam ativados no browser dos utilizadores. Encaminhamento de tráfego utiliza cookies para afixar um browser do cliente para uma ranhura de implementação para a vida sessão do cliente.
- Encaminhamento de tráfego suporta avançadas de sugestão de cenários, através dos cmdlets do Azure PowerShell.

## <a name="route-traffic-segment-to-a-deployment-slot"></a>Segmento de tráfego rota para uma ranhura de implementação

Nível do básicas cada cenário de sugestão, encaminha uma percentagem predefinida do seu tráfego direto para uma ranhura de implementação de que não sejam de produção. Para fazer isto, siga os passos abaixo:

>[AZURE.NOTE] Os passos aqui assume que já tem uma [ranhura não produção implementação](web-sites-staged-publishing.md) e que o conteúdo da aplicação web pretendida já está [implementado](web-sites-deploy.md) -lo.

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com/).
2. No pá da sua aplicação web, clique em **Definições** > **Encaminhamento de tráfego**.
  ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Selecione a ranhura que pretende para encaminhar o tráfego para o e a percentagem do total tráfego pretendem, em seguida, clique em **Guardar**.

    ![](./media/app-service-web-test-in-production/02-select-slot.png)

4. Aceda a pá a ranhura implementação. Agora deverá ver direto tráfego a ser encaminhado para o mesmo.

    ![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Depois de encaminhamento de tráfego está configurado, a percentagem especificada de clientes serão aleatoriamente encaminhada para sua ranhura não sejam de produção. No entanto, é importante ter em atenção que assim que um cliente é automaticamente encaminhado para uma ranhura específica,-lo irá estar "afixada" para essa ranhura para a vida útil dessa sessão do cliente. Isto concluído utilizando um cookie para afixar a sessão do utilizador. Se inspecionar os pedidos HTTP, irá encontrar um `TipMix` cookie em todos os pedidos subsequentes.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## <a name="force-client-requests-to-a-specific-slot"></a>Pedidos de cliente forçar uma ranhura específico

Para além de encaminhamento de tráfego automático, a aplicação de serviço é capaz de para encaminhar os pedidos para uma ranhura específico. Isto é útil quando pretende que os utilizadores poderá optar ativamente por participar no ou optar ativamente da sua aplicação beta. Para executar esta tarefa, utilize o `x-ms-routing-name` parâmetro de consulta.

Para reencaminhar aos utilizadores uma ranhura específica utilizando `x-ms-routing-name`, deve certificar-se de que a ranhura já é adicionada à lista encaminhamento de tráfego. Uma vez que queira encaminhar para uma ranhura explicitamente, a percentagem de encaminhamento real que definir é indiferente. Se pretender, pode criar uma "ligação beta" que os utilizadores podem clicar para aceder à aplicação beta.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### <a name="opt-users-out-of-beta-app"></a>Optar ativamente por participar utilizadores Terminar aplicação beta

Para permitir que os utilizadores optar por terminar a sua aplicação beta, por exemplo, pode colocar esta ligação na sua página web:

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

A cadeia `x-ms-routing-name=self` Especifica a ranhura de produção. Assim que o browser cliente aceder a ligação, não só é redirecionado para a ranhura de produção, como todos os pedidos subsequentes irão conter a `x-ms-routing-name=self` cookie PIN a sessão para a ranhura de produção.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### <a name="opt-users-in-to-beta-app"></a>Optar ativamente por participar os utilizadores na aplicação beta

Para permitir que utilizadores optar ativamente por participar para a sua aplicação beta, defina o mesmo parâmetro de consulta para o nome da ranhura não produção, por exemplo:

        <webappname>.azurewebsites.net/?x-ms-routing-name=staging

## <a name="more-resources"></a>Mais recursos ##

-   [Configurar o teste ambientes para web apps no serviço de aplicação do Azure](web-sites-staged-publishing.md)
-   [Implementar uma aplicação complexa seja previsível no Azure](app-service-deploy-complex-application-predictably.md)
-   [Desenvolvimento de agile software com o serviço de aplicação do Azure](app-service-agile-software-development.md)
-   [Utilizar DevOps ambientes de forma eficaz para as suas aplicações web](app-service-web-staged-publishing-realworld-scenarios.md)