<properties
   pageTitle="Aplicações de lógica exemplos e cenários | Microsoft Azure"
   description="Ver exemplos de aplicações comuns lógica e saiba como implementar cenários comuns"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-examples-and-common-scenarios"></a>Cenários comuns e exemplos de aplicações de lógica

Este cenários comuns de detalhes de documento e exemplos para ajudá-lo a compreender algumas formas do podem utilizar as aplicações de lógica para automatizar processos de negócio. 

## <a name="custom-triggers-and-actions"></a>Accionadores personalizados e acções

Existem várias formas pode acionar uma aplicação de lógica a partir de outra aplicação. Eis alguns exemplos comuns:

- [Criar um accionador personalizado ou ação](app-service-logic-create-api-app.md)
- [Ações de execução longa](app-service-logic-create-api-app.md)
- [Acionador de pedido HTTP (POST)](app-service-logic-http-endpoint.md)
- [Webhook accionadores e acções](app-service-logic-create-api-app.md)
- [Inquéritos accionadores](app-service-logic-create-api-app.md)

### <a name="scenarios"></a>Cenários

- [Pedido síncrona resposta](app-service-logic-http-endpoint.md)
- [Pedido de resposta com SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>Processamento de erros e registo

- [Exceção e processamento de erros](app-service-logic-exception-handling.md)
- [Configurar alertas do Azure e diagnósticos de](app-service-logic-monitor-your-logic-apps.md)

### <a name="scenarios"></a>Cenários

- [Casos de utilização: Erro e processamento de exceção](app-service-logic-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>Implementar e gerir

- [Criar uma implementação automatizada](app-service-logic-create-deploy-template.md)
- [Criar e implementar lógica aplicações a partir do Visual Studio](app-service-logic-deploy-from-vs.md)
- [Aplicações de lógica de monitorização](app-service-logic-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>Tipos de conteúdo, conversões e transformações

A lógica aplicações [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs) contém muitas funções para permitir a converter e trabalhar com diferentes tipos de conteúdo.  Além do motor executará nenhuma todos-pode ser utilizados para preservar tipos de conteúdo como fluxos de dados através do fluxo de trabalho.

- [Processamento de tipos de conteúdo](app-service-logic-content-type.md) , como aplicação/json, aplicação/xml e/texto simples
- [Criação de definições de fluxo de trabalho](app-service-logic-author-definitions.md)
- [Referência de linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>Secções e ciclo

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Até](app-service-logic-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>Integração com funções Azure

- [Integração de funções Azure](app-service-logic-azure-functions.md)

### <a name="scenarios"></a>Cenários

- [Função Azure como um accionador Bus de serviço](app-service-logic-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP, resto e SOAP

 - [Chamar SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Vamos irá continue a adicionar exemplos e cenários para este documento. Utilize a secção de comentários abaixo para diga-no que exemplos ou cenários que gostaria de ver aqui.