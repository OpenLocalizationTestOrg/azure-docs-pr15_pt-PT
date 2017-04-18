<properties
    pageTitle="O que aconteceu ao meu projecto de serviço de nuvem? | Microsoft Azure | Serviços do visuais Studio ligado"
    description="Descreve o que acontece num projeto de serviços na nuvem depois de ligar a uma conta de armazenamento Azure utilizando o Visual Studio ligado serviços"
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

# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projecto de serviços na nuvem (Visual Studio Azure armazenamento ligado serviço)?

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

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de ligação para o armazenamento do Windows Azure adicionado
Elementos foram criados com a cadeia de ligação e a chave da conta de armazenamento selecionado. Modificações foram efetuadas para os seguintes ficheiros:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**
