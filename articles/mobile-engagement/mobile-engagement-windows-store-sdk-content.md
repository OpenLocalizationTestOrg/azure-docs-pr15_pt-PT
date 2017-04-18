<properties 
    pageTitle="Conteúdo do Windows SDK de aplicações Universal" 
    description="Saiba mais sobre os conteúdos do SDK aplicações Universal do Windows para Azure Mobile Cativação"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-universal-apps-sdk-content"></a>Conteúdo do Windows SDK de aplicações Universal

Este documento apresenta e descreve o conteúdo implementado pelo SDK na sua aplicação.

##<a name="the-resources-folder"></a>O `/Resources` pasta

Esta pasta contém todos os recursos que necessita de Cativação Mobile. Também pode personalizá-los para se ajustar a sua aplicação.

- `EngagementConfiguration.xml`: Ficheiro de configuração do Cativação o Mobile, este é onde pode personalizar as definições de Cativação Mobile (cadeia de ligação de Cativação Mobile, falha de relatório...).

### <a name="html-folder"></a>pasta /HTML

- `EngagementNotification.html`: O `Notification` vista html estrutura de faixas de na aplicação da web.

- `EngagementAnnouncement.html`: O `Announcement` vista estrutura de html para vistas intersticial na aplicação da web.

### <a name="images-folder"></a>pasta /Images

- `EngagementIconNotification.png`: O ícone de marca corporativa apresentado no lado esquerdo de uma notificação, substituir pelo seu ícone de marca.

- `EngagementIconOk.png`: O `Ok` ícone das páginas de conteúdo de alcance do botão de ação ou de validação.

- `EngagementIconNOK.png`: O `NOK` ícone utilizado quando o botão de validação das páginas de conteúdo de atingir está desativado.
 
- `EngagementIconClose.png`: O `Close` ícone das notificações de atingir e conteúdo para o botão dispensar.

### <a name="overlay-folder"></a>pasta /Overlay

- `EngagementPageOverlay.cs`: A página de sobreposição responsável para adicionar a Cativação atingir IU na aplicação ao seu filho.
  
