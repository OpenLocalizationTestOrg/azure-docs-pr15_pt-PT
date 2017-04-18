<properties
    pageTitle="Criar uma aplicação web num ambiente do serviço de aplicação"
    description="Saiba como criar aplicações web e app planos do serviço num ambiente do serviço de aplicação"
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
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="create-a-web-app-in-an-app-service-environment"></a>Criar uma aplicação web num ambiente do serviço de aplicação

## <a name="overview"></a>Descrição geral

Este tutorial mostra como criar aplicações web e planos do serviço de aplicação num [Ambiente de serviço de aplicação](app-service-app-service-environment-intro.md) (Auxiliar). 

> [AZURE.NOTE] Se pretende saber como criar uma aplicação web mas não precisa de fazê-lo num ambiente do serviço de aplicação, consulte o artigo [criar uma aplicação web do .NET](web-sites-dotnet-get-started.md) ou um dos tutoriais relacionados para outros idiomas e quadros.

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial assume que ter criado um ambiente do serviço de aplicação. Se ainda não o tiver concluído as alterações que ainda tem, consulte o artigo [criar um ambiente do serviço de aplicação](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Criar uma aplicação web

1. No [Portal do Azure](https://portal.azure.com/), clique em **Novo > Web + Mobile > Web App**. 

    ![][1]

2. Selecione a sua subscrição.  

    Se tiver múltiplas subscrições, tenha em atenção que para criar uma aplicação no seu ambiente de serviço de aplicação, tem de utilizar a mesma subscrição que utilizou quando criar o ambiente. 

3. Selecione ou crie um grupo de recursos.

    *Grupos de recursos* permitem-lhe gerir recursos Azure relacionados como uma unidade e são úteis quando estabelecer regras de *controlo de acesso baseado em funções* (RBCA) para as suas aplicações. Para obter mais informações, consulte [Gerir os recursos do Azure][ResourceGroups]. 

4. Selecione ou crie um plano de serviço de aplicação.

    *Planos do serviço de aplicação* são geridas conjuntos de aplicações web.  Normalmente quando seleciona a preços, preço cobrado é aplicado para o plano de serviço de aplicação em vez das aplicações individuais. Num Auxiliar pagar as instâncias de cluster atribuídas para o Auxiliar em vez do que listado com o seu ASP.  Para dimensionar o o número de ocorrências de uma aplicação web dimensionar o as instâncias da aplicação de serviço plano e -afeta todas as aplicações web nesse plano.  Algumas funcionalidades como faixas de site ou VNET integração também as restrições de quantidade dentro do plano.  Para obter mais informações, consulte o artigo [Descrição geral de planos do serviço de aplicação do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    É possível identificar os planos do serviço de aplicação no seu Auxiliar verificando a localização que é indicada em nome do plano.  

    ![][5]

    Se pretender utilizar um plano de serviço de aplicação que já existe no seu ambiente de serviço de aplicação, selecione esse plano. Se pretender criar um novo plano de serviço de aplicação, consulte a secção seguinte deste tutorial, [criar um plano de serviço de aplicação num ambiente do serviço de aplicação](#createplan).

5. Introduza o nome para a sua aplicação web e, em seguida, clique em **Criar**. 

    Se a sua Auxiliar utiliza uma VIP externo é o URL de uma aplicação num Auxiliar: [*nome do site*]. [*nome da sua aplicação de ambiente de serviço*]. p.azurewebsites.net em vez de [*nome do site*]. azurewebsites.net
    
    Se a sua Auxiliar utiliza uma VIP interno, em seguida, o URL de uma aplicação nesse Auxiliar é: [*nome do site*]. [*subdomínio especificado durante a criação de Auxiliar*]   
    Depois de selecionar o ASP durante a criação de Auxiliar que irá ver o subdomínio atualizar abaixo do **nome**

## <a name="createplan"></a>Criar um plano de serviço de aplicação

Quando cria um plano de serviço de aplicação num ambiente do serviço de aplicação, as opções de trabalho são diferentes à medida que não existem sem trabalhadores partilhados um Auxiliar.  Os trabalhadores que tem de utilizar são aqueles que foi alocados para o Auxiliar pelo administrador.  Isto significa que para criar um novo plano, tem de ter mais trabalhadores atribuídos para o conjunto de Auxiliar de trabalho que o número total de instâncias entre todos os planos que já existe nesse agrupamento de trabalho.  Se não tiver o suficiente trabalhadores no seu conjunto de trabalho Auxiliar para criar o seu plano, terá de trabalhar com o seu administrador de Auxiliar para obtê-lo adicionado.

Outra diferença com os planos de aplicação de serviço alojado por um ambiente do serviço de aplicação é a falta de preços de seleção.  Quando tem um ambiente do serviço de aplicação que estiver a pagar para recursos de cluster utilizados pelo sistema e não tiver adicionadas taxas para os planos de nesse ambiente.  Normalmente quando cria um plano de serviço de aplicação selecione um plano de preços que determina a sua faturação.  Um ambiente do serviço de aplicação é, essencialmente, numa localização privada onde pode criar conteúdo.  Pode pagar para o ambiente e não para alojar o seu conteúdo.

As instruções seguintes mostram como criar um plano de aplicação de serviço enquanto estiver a criar uma aplicação web, tal como é explicado na secção anterior do tutorial.

1. Clique em **Criar novo** na seleção plano IU e forneça um nome para o seu plano, tal como faria normalmente fora de um Auxiliar.

2. Selecione o Auxiliar que pretende utilizar a partir do seu seleccionador de localização.

    Uma vez que um ambiente do serviço de aplicação é, essencialmente, uma localização de implementação privado, mostra em localização. 

    ![][2]

    Após a seleção de um Auxiliar no Seletor de localização, a criação de plano de serviço de aplicação IU atualizações.  A localização agora mostra o nome do sistema Auxiliar e a região é no e o selecionador de plano comparar é substituído por um seleccionador de agrupamento de trabalho.  

    ![][3]

### <a name="selecting-a-worker-pool"></a>Selecionar um agrupamento de trabalho

Normalmente na aplicação de serviço de Azure e fora de um ambiente do serviço de aplicação, existem 3 tamanhos de cluster que estão disponíveis com a seleção de um plano do preço dedicada.  De forma semelhante para uma Auxiliar pode definir máximo de 3 conjuntos de dados dos trabalhadores e especifique o tamanho de cluster que é utilizado para esse agrupamento de trabalho.  O que isto significa para os inquilinos do Auxiliar ou em vez disso, é de selecção de um plano comparar com tamanho de cluster para o seu plano de serviço de aplicação, selecionar o que é designado por um *conjunto de trabalho*.  

A seleção de agrupamento trabalhador IU mostra o tamanho de cluster utilizado para esse agrupamento de trabalho por baixo do nome.  A quantidade disponível refere-se para calcular quantas instâncias estão disponíveis para utilização nesse agrupamento.  O conjunto total poderá ter mais ocorrências que este número, mas este valor que se refere a simplesmente quantos não estão em utilização.  Se precisar de ajustar o seu ambiente de serviço de aplicação para adicionar mais calcular recursos Consulte o artigo [configurar o seu ambiente de serviço de aplicação](app-service-web-configure-an-app-service-environment.md).

![][4]

Neste exemplo, verá apenas dois conjuntos de trabalho disponíveis. Que é uma vez que o administrador Auxiliar atribuídos apenas anfitriões para esses conjuntos de dados de duas trabalhador.  A terceira seria aparecer quando existem VMs atribuídos para o mesmo.  

## <a name="after-web-app-creation"></a>Após a criação de aplicação web

Existem algumas considerações para executar aplicações web e gestão de planos do serviço de aplicação num Auxiliar que precisam de ser tidos em conta.  

Tal como indicado anteriormente, o proprietário da Auxiliar é responsável pelo tamanho do sistema e como resultado também são responsáveis pelo que não existe uma capacidade suficiente para alojar os planos do serviço de aplicação pretendidos. Se não existirem sem trabalhadores disponíveis, não poderá criar o seu plano de serviço de aplicação.  Este é também TRUE para escalar para cima a aplicação web.  Se precisar de mais ocorrências terá de obter o seu administrador de ambiente de serviço de aplicação para adicionar mais trabalhadores.

Depois de criar o seu web app e o plano de serviço de aplicação é uma boa ideia para dimensioná-lo para cima.  Num Auxiliar sempre tem de ter, pelo menos, 2 instâncias do seu plano de serviço de aplicação para fornecer tolerância a falhas para as suas aplicações.  Um plano de aplicação de serviço de dimensionamento num Auxiliar é igual normal através da IU o plano de serviço de aplicação.  Para mais informações sobre [como dimensionar uma aplicação web num ambiente do serviço de aplicação](app-service-web-scale-a-web-app-in-an-app-service-environment.md) , de dimensionamento

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
