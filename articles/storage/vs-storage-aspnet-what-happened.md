<properties
    pageTitle="O que aconteceu ao meu projecto ASP.NET? | Microsoft Azure | Serviços do visuais Studio ligado"
    description="Descreve o que acontece depois de adicionar armazenamento do Windows Azure a um projeto ASP.NET utilizando o Visual Studio ligado serviços"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projecto ASP.NET (Visual Studio Azure armazenamento ligado serviço)?

## <a name="references-added"></a>Referências adicionadas

O pacote do Azure armazenamento NuGet foi adicionado ao seu projeto do Visual Studio.  
Este pacote adiciona as referências de .NET seguintes:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System**
- **System.Spatial**

##<a name="connection-string-for-azure-storage-added"></a>Cadeia de ligação para o armazenamento do Windows Azure adicionado
No ficheiro Web. config do seu projeto, um elemento foi criado com a conta de armazenamento selecionado cadeia de ligação e chave.

Para mais informações, consulte o artigo [ASP.NET](http://www.asp.net).
