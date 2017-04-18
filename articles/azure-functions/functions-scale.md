<properties
   pageTitle="Como dimensionar funções Azure | Microsoft Azure"
   description="Compreenda como Azure funções Dimensionar para satisfazer as necessidades da sua cargas de trabalho condicionada por eventos."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funções, funções, processamento de eventos, webhooks, cluster dinâmico, sem servidor arquitetura"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# <a name="how-to-scale-azure-functions"></a>Como dimensionar funções Azure

## <a name="introduction"></a>Introdução

Uma vantagem das funções Azure é que os recursos de cluster são apenas consumidos quando for necessário. Isto significa que não pagar idle VMs ou de ter para reservar capacidade para quando poderá precisar. Em vez disso, a plataforma atribui cluster power quando seu código está em execução, cresce conforme necessário para processar carga e, em seguida, pode ser dimensionado para baixo quando não está em execução código.

Dispositivo desta nova capacidade é o plano de serviço de Dynamic.  

Este artigo fornece uma descrição geral de como funciona o plano de serviço dinâmicos e como a plataforma escalas a pedido para executar o seu código.

Se de que não estiver familiarizado com funções Azure, certifique-se verificar o artigo [Descrição geral das funções Azure](functions-overview.md) para compreender melhor as suas capacidades.

## <a name="configure-azure-functions"></a>Configurar funções Azure

Duas definições principais estão relacionados com o dimensionamento:

* [Plano de serviço de aplicação do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) ou dinâmico plano de serviço
* Tamanho da memória para o ambiente de execução

O custo de uma função altera consoante o plano de serviço que selecionar. Com um plano de serviço de Dynamic, o custo é uma função de tempo de execução, o tamanho da memória e o número de execuções. Taxas de imputação dos apenas quando o código é realmente a ser executado.

Um plano de serviço de aplicação aloja o seu funções no VMs existentes, que também podem ser utilizados para executar outras código. Depois de pagar todos os meses para estes VMs, não existe nenhuma encargo extra para as funções de execução nos mesmos.

## <a name="choose-a-service-plan"></a>Selecione um plano de serviço

Quando criar funções, pode selecionar para executá-las num plano de serviço dinâmico ou de um [plano de serviço de aplicação](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
No plano de serviço de aplicação, as funções serão executado numa VM dedicada, tal como o trabalho de aplicações web hoje para Basic, padrão ou Premium SKUs.
Este VM dedicada é atribuído a suas aplicações e funções e está sempre disponível se estiver a ser ativamente executado código ou não. Este é uma boa opção se tiver VMs existentes, utilizada em que já estiver a executar o outro código ou se pretender executar funções continuamente ou quase continuamente. Uma VM decouples custo de tamanho de runtime e memória. Como resultado, pode limitar o custo de muitas funções de execução longa e o custo do VMs de uma ou mais que são executadas em.

[AZURE.INCLUDE [Dynamic Service plan](../../includes/functions-dynamic-service-plan.md)]
