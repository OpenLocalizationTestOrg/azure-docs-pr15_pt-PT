<properties
    pageTitle="Azure de aplicação de serviço: Aplicações de serviço de aplicação de dimensionamento"
    description="Obter informações sobre as vantagens e desvantagens da aplicação de dimensionamento na aplicação de serviço."
    keywords="aplicação, azure aplicação do serviço, plano de serviço de aplicação dimensionáveis, do escala, custo do serviço de aplicação"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-scaling-app-service-applications"></a>Azure de aplicação de serviço: Aplicações de serviço de aplicação de dimensionamento

Aplicações alojadas no serviço de aplicação do Azure podem [alcançar escala grandes](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
No entanto, uma aplicação de dimensionamento é um problema de complexo que não tem uma solução "um tamanho de se adequa todos". Para corretamente escala, a aplicação não existem estão 3 áreas chave que irão contribuem para o sucesso de aplicações:

1. Noções sobre arquitectura da sua aplicação e respetivas fraquezas.
    * É o seu com estado aplicação? Sem estado?
    * O que são todos os componentes da sua aplicação?
        * Onde estão congestionamentos na aplicação?
    * Quando é aplicado carregar para a sua aplicação, o que irá interromper primeiro?
2. Noções sobre os requisitos de carga e o desempenho esperados.
    * A aplicação é que precisa servir um milhar utilizadores? ou um milhão?
    * Tráfego seja reencaminhado a partir de uma única localização geográfica ou globalmente?
    * Existem os variações sazonais? tráfego picos?
    * Velocidade deverá responder a aplicação? 1 segundo? 1 milissegundos?
3. Noções sobre e corretamente tirar partido da plataforma que aloja a sua aplicação.
    * Que funcionalidades de deve aproveitar para atingir os meus objetivos de escala

Esta secção irá ajudá-lo a perceber todos os fatores e ajuda que elaborar uma estratégia que tira partido das funcionalidades de aplicação de serviço necessários para atingir os seus objetivos escalabilidade.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]
