<properties
    pageTitle="O que aconteceu ao meu projecto WebJob (Visual Studio Azure armazenamento ligado serviço)? | Microsoft Azure"
    description="Descreve o que aconteceu num projeto Azure WebJob depois de ligar a uma conta de armazenamento utilizando o Visual Studio ligado serviços"
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

# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projecto WebJob (Visual Studio Azure armazenamento ligado serviço)?

## <a name="references-added"></a>Referências adicionadas

O pacote do Azure armazenamento NuGet foi adicionado à ou atualizado no seu projeto do Visual Studio.  
Este pacote adiciona as referências de .NET seguintes:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de ligação para o armazenamento do Windows Azure adicionado
No ficheiro App do seu projeto, as entradas **AzureWebJobsStorage** e **AzureWebJobsDashboard** foram atualizadas com a cadeia de ligação a conta de armazenamento selecionado e chave.

Para mais informações, consulte o artigo [recursos de documentação Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).
