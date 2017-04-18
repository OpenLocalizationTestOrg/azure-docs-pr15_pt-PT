<properties 
    pageTitle="Como criar um ambiente de aplicação de serviço" 
    description="Descrição do fluxo de criação de ambientes de serviço de aplicação" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2016" 
    ms.author="ccompy"/>

# <a name="how-to-create-an-app-service-environment"></a>Como criar um ambiente de aplicação de serviço #

### <a name="overview"></a>Descrição geral ###

Aplicação de serviço ambientes (Auxiliar) são uma opção de serviço Premium do serviço de aplicação de Azure oferece uma capacidade de configuração avançada que não está disponível nos carimbos de inquilinos com várias.  A funcionalidade de Auxiliar essencialmente implementa o serviço de aplicação do Azure na rede virtual de um cliente.  Para obter uma maior compreensão das capacidades de disponibilizadas pelos ambientes de serviço de aplicação ler [o que é um ambiente do serviço de aplicação] [ WhatisASE] documentação.

### <a name="before-you-create-your-ase"></a>Antes de criar o seu Auxiliar ###

É importante ter em consideração as coisas que não é possível alterar.  Esses aspetos que não é possível alterar sobre o Auxiliar após ter sido criada são:

- Localização
- Subscrição
- Grupo de recursos
- VNet utilizado
- Sub-rede utilizado 
- Tamanho de sub-rede

Ao escolher um VNet e especificar uma sub-rede, certifique-se de é grande o suficiente para acomodar qualquer futuro crescimento.  

### <a name="creating-an-app-service-environment"></a>Criar um ambiente de aplicação de serviço ###

Existem duas formas de aceder a criação de Auxiliar IU.  Pode ser encontrado ao procurar no Azure Marketplace ***Ambiente de serviço de aplicação*** ou acedendo através do novo -> Web + Mobile -> ambiente de serviço de aplicação.  Para criar um Auxiliar:

1. Fornece o nome do seu Auxiliar.  O nome especificada para a Auxiliar será utilizado para as aplicações que criou na Auxiliar.  Se o nome da Auxiliar é appsvcenvdemo, em seguida, o nome do subdomínio seria. *appsvcenvdemo.p.azurewebsites.net*.  Se tiver criado uma aplicação com o nome *mytestapp* , por conseguinte, em seguida, seria endereçável na *mytestapp.appsvcenvdemo.p.azurewebsites.net*.  Não pode utilizar o espaço em branco na nome do seu Auxiliar.  Se utilizar maiúsculas carateres no nome, o nome de domínio será a versão em minúsculas total desse nome.  Se utilizar um ILB, em seguida, o nome do seu Auxiliar não é utilizado na sua subdomínio mas em vez disso for explicitamente declarado durante a criação de Auxiliar

    ![][1]

2. Selecione a sua subscrição.  A subscrição utilizada para o Auxiliar também é aquele que todas as aplicações nesse Auxiliar serão criadas com.  Não pode colocar o Auxiliar um VNet que se encontra na outra subscrição

3. Selecione ou especifique um novo grupo de recursos.  O grupo de recursos utilizado para o Auxiliar tem de ser o mesmo que é utilizado para o seu VNet.  Se selecionar uma VNet pré-existentes a seleção de grupo de recursos para sua Auxiliar será atualizada para refletir que seu VNet.

    ![][2]

4. Efetue as seleções de rede Virtual e a localização.  Pode optar por criar uma nova VNet ou selecionar uma VNet pré-existentes.  Se selecionar uma VNet nova, em seguida, pode especificar um nome e uma localização. O novo VNet terão o 192.268.250.0/23 intervalo endereço e uma sub-rede com o nome **predefinido** que é definido como 192.168.250.0/24.  Basta também pode selecionar uma clássica pré-existentes ou VNet do Gestor de recursos.  A seleção de tipo de VIP determina se o seu Auxiliar pode ser acedida diretamente a partir da internet (externo) ou se utiliza uma Balanceador de carga interna (ILB).  Para saber mais sobre os mesmos lidos [com um balanceador de carga interno com um ambiente do serviço de aplicação][ILBASE].  Se selecionar um tipo de VIP de externo, em seguida, pode selecionar quantos endereços IP externos o sistema é criado com para fins IPSSL.  Se selecionar interna, em seguida, tem de especificar o subdomínio que irá utilizar o seu Auxiliar.  Podem ser implementadas ASEs em redes virtuais que utilizam *um dos* intervalos de endereços pública, espaços de endereços *ou* RFC1918 (isto é endereços privados).  Para que possa utilizar uma rede virtual com um intervalo de endereços pública, terá de criar VNet antecedência.  Quando seleciona uma VNet pré-existentes terá de criar uma nova sub-rede durante a criação de Auxiliar.  **Não pode utilizar uma sub-rede previamente criada no portal.  Pode criar um Auxiliar com uma sub-rede pré-existentes se criar o seu Auxiliar através de um modelo de Gestor de recursos.**  Para criar um Auxiliar a partir de um modelo de utilizar as informações aqui, [criar um ambiente do serviço de aplicação a partir de modelo] [ ILBAseTemplate] e daqui, [criar um ambiente do serviço de aplicação ILB a partir de modelo][ASEfromTemplate].

### <a name="details"></a>Detalhes ###

Um Auxiliar é criada com 2 termina a parte da frente e trabalhadores de 2.  Parte da frente termina funcionar como os pontos finais HTTP/HTTPS e enviar o tráfego para os trabalhadores que são as funções que alojam as suas aplicações.   Pode ajustar a quantidade de após a criação de Auxiliar e pode até mesmo configurar regras de autoscale nestes agrupamentos de recursos.  Para obter mais detalhes à volta de dimensionamento manual, gestão e monitorização uma aplicação de ambiente de serviço aceda aqui: [como configurar um ambiente do serviço de aplicação][ASEConfig] 

Apenas a um Auxiliar pode existir na sub-rede utilizada pela Auxiliar.  A sub-rede não pode ser utilizada para tudo o que não seja o Auxiliar

### <a name="after-app-service-environment-creation"></a>Após a criação de ambiente de serviço de aplicação ###

Após a criação de Auxiliar pode ajustar:

- Quantidade de termina frente (mínimo: 2)
- Quantidade dos trabalhadores (mínimo: 2)
- Quantidade de endereços IP disponível para IP SSL
- Calcular tamanhos de recursos utilizados pelos termina a frente ou trabalhadores (tamanho mínimo Front-End é P2)


Existem mais detalhes à volta manual dimensionamento, gestão e monitorização da aplicação de serviço ambientes aqui: [como configurar um ambiente do serviço de aplicação][ASEConfig] 

Para obter informações sobre autoscaling existe um guia aqui: [como configurar autoscale para um ambiente do serviço de aplicação][ASEAutoscale]

Existem dependências adicionais que não estão disponíveis para a personalização como a base de dados e o armazenamento.  Estes são geridas pelo Azure e fornecidos com o sistema.  O armazenamento de sistema suporta até 500 GB para o ambiente de serviço de aplicação completo e a base de dados é ajustado por Azure, conforme necessário, a escala do sistema.


## <a name="getting-started"></a>Introdução
Todos os artigos e como-para para ambientes de serviço de aplicação estão disponíveis no [ficheiro Leia-me para ambientes de serviço de aplicação](../app-service/app-service-app-service-environments-readme.md).

Para iniciar a aplicação de serviço ambientes, consulte [Introdução à aplicação de serviço ambientes][WhatisASE]

Para obter mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/
