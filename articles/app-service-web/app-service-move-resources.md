<properties
    pageTitle="Mover recursos da aplicação Web para outro grupo de recursos"
    description="Descreve os cenários onde pode mover Web aplicações e serviços de aplicação de um grupo de recursos para outro."
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/04/2016"
    ms.author="zarizvi"/>
    
# <a name="supported-move-configurations"></a>Configurações de mover suportados

Pode mover utilizar a [Api de recursos de mover processador](../resource-group-move-resources.md)de recursos de Azure Web App.

Azure Web Apps atualmente suporta os seguinte cenários de mover:

* Mover todo o conteúdo de um grupo de recursos (aplicações web, planos de serviço de aplicação e certificados) para outro grupo de recursos 
    * Nota: O grupo de recursos de destino não pode conter quaisquer recursos Microsoft.Web neste cenário
* Mover aplicações web individuais para um grupo de recursos diferentes, enquanto ainda alojamento-los no seu plano de serviço de aplicação atual (o plano de serviço de aplicação permanece no antigo grupo de recursos)
