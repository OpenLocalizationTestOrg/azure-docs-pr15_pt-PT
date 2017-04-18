<properties 
    pageTitle="Como dimensionar uma aplicação num ambiente de aplicação de serviço" 
    description="Dimensionamento uma aplicação num ambiente de aplicação de serviço" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>Dimensionamento aplicações num ambiente de aplicação de serviço #

No serviço de aplicação do Azure existem normalmente três elementos que pode dimensionar:

- plano de preços
- tamanho de trabalho 
- número de instâncias.

Num Auxiliar não é necessário para selecionar ou alterar o plano de preços.  Em termos de capacidades-lo já tem um prémio preços nível de funcionalidade.  

Relativamente às tamanhos de trabalho, o administrador Auxiliar pode atribuir o tamanho do recurso cluster a ser utilizado para cada conjunto de trabalho.  Que significa que pode ter 1 de agrupamento de trabalho com os recursos de cluster E4 e 2 de agrupamento de trabalho com P1 calcular recursos, se pretender.  Não tem de estar numa ordem de tamanho.  Para detalhes à volta os tamanhos e os respetivos consulte o artigo Comparar documento aqui [Preços de serviço de aplicação do Azure][AppServicePricing].  Esta opção deixa as opções de dimensionamento para aplicações web e planos do serviço de aplicação num ambiente do serviço de aplicação para ser:

- seleção de agrupamento de trabalho
- número de ocorrências

Alterar o item é feito através da UI adequada apresentada para o seu Auxiliar alojado planos do serviço de aplicação.  

![][1]

Não consigo dimensionar o seu ASP para além do número de recursos de cluster disponíveis no conjunto de trabalho se encontra o ASP.  Se precisar de calcular recursos desse conjunto de trabalho que precisa para obter o seu administrador de Auxiliar para adicioná-los.  Para informações à volta de voltar a configurar o Auxiliar ler as informações de aqui: [como configurar um ambiente de aplicação de serviço de][HowtoConfigureASE].  Também poderá querer tirar partido das funcionalidades de autoscale Auxiliar para adicionar a capacidade com base na agenda ou métricas.  Para obter mais detalhes sobre como configurar autoscale para o Auxiliar ambiente propriamente dito consulte o artigo [como configurar autoscale para um ambiente do serviço de aplicação][ASEAutoscale].

Pode criar vários aplicação com cluster recursos a partir de agrupamentos de trabalho diferentes planos do serviço, ou pode utilizar o mesmo conjunto de trabalho.  Por exemplo se tiver (10) de disponível cluster recursos no trabalho agrupamento de 1, pode optar por criar um plano de serviço de aplicação utilizando (6) cluster recursos e planear uma segunda aplicação de serviço que utiliza (4) calcule recursos.

### <a name="scaling-the-number-of-instances"></a>O número de ocorrências de dimensionamento ###

Quando cria pela primeira vez a aplicação web num ambiente do serviço de aplicação começa 1 instância.  Em seguida, é possível dimensionar ocorrências adicionais para fornecer recursos cluster adicionais para a sua aplicação saída.   

Se a sua Auxiliar tiver capacidade suficiente, em seguida, isto é bastante simple.  Vá para o serviço de aplicação planear que detém os sites que pretende dimensionar para cima e selecione escala.  Esta ação abre a IU onde pode manualmente defina a escala para o seu ASP ou configurar regras de autoscale para o seu ASP.  A escala manualmente a aplicação defina simplesmente a ***escala por*** para ***uma contagem de instância posso introduzir manualmente***.  A partir daqui arraste o controlo de deslize para a quantidade pretendida ou introduzi-lo na caixa junto ao controlo de deslize.  

![][2] 

As regras de autoscale para um ASP num Auxiliar funcionam da mesma forma que normalmente.  Pode selecionar a ***Percentagem de CPU*** em ***escala por*** e criar regras de autoscale para o seu ASP com base na percentagem de CPU ou pode criar regras mais complexas utilizando ***regras de agenda e o desempenho***.  Para ver mais completas detalhes sobre como configurar autoscale utilizar o guia de aqui [Dimensionar uma aplicação na aplicação de serviço de Azure][AppScale]. 


### <a name="worker-pool-selection"></a>Seleção de agrupamento de trabalho ###

Tal como indicado anteriormente, a seleção de agrupamento de trabalho é acedida a partir ASP UI.  Abra o pá para o ASP ao qual pretende dimensionar e selecione o conjunto de trabalho.  Irá ver todos os conjuntos de trabalho que tenha configurado no seu ambiente de serviço de aplicação.  Se tiver o conjunto de trabalho apenas um, em seguida, só verá um conjunto de listados.  Para alterar o agrupamento de trabalho ASP está no, basta selecionar o conjunto de trabalho que pretende que o seu plano de serviço de aplicação para mover para.  

![][3]

Antes de passar o ASP a partir do conjunto de uma trabalho para outra é importante para se certificar de que tem uma capacidade suficiente para o seu ASP.  Na lista de agrupamentos de trabalho, não só é o nome do conjunto de trabalho listado, mas também pode ver quantas trabalhadores estão disponíveis nesse agrupamento de trabalho.  Certifique-se de que não existem suficiente instâncias disponíveis para conter o seu plano de serviço de aplicação.  Se precisar de mais calcular recursos no conjunto de trabalho que pretende mover para, e obter o seu administrador de Auxiliar para adicioná-los.  

> [AZURE.NOTE] Mover que um ASP a partir do conjunto de um trabalho originará fria inicia das aplicações em que o ASP.  Isto pode causar os pedidos para executar lentamente tal como a sua aplicação está fria iniciada os novos recursos de cluster.  Iniciar fria pode ser evitada utilizando a [aplicação quente o capacidade] [ AppWarmup] na aplicação de serviço de Azure.  O módulo de inicialização de aplicação descrito no artigo também funciona para frio é iniciado, porque o processo de inicialização é também chamado quando aplicações estão frias iniciada novos recursos de cluster. 

## <a name="getting-started"></a>Introdução

Para iniciar a aplicação de serviço ambientes, consulte o artigo [Como para criar uma aplicação de ambiente de serviço][HowtoCreateASE]

Para obter mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
