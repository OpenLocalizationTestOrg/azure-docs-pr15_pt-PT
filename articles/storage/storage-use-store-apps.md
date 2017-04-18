<properties
    pageTitle="Utilizar o armazenamento Azure nas aplicações da loja Windows | Microsoft Azure"
    description="Saiba como criar uma aplicação da loja Windows que utiliza o armazenamento de Blobs do Azure, fila de espera, tabela ou ficheiro."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>
    
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Como utilizar o armazenamento do Windows Azure nas aplicações da loja Windows

## <a name="overview"></a>Descrição geral

Este guia mostra como começar a desenvolver uma aplicação da loja Windows que utiliza armazenamento do Windows Azure.

## <a name="download-required-tools"></a>Transferir ferramentas necessárias

- [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) torna mais fácil criar, depurar, localize o pacote e implementar aplicações da loja Windows. É necessário o Visual Studio 2012 ou posterior.
- A [Biblioteca de cliente de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage) fornece uma biblioteca de classes Runtime do Windows para trabalhar com o armazenamento do Windows Azure.
- [WCF dados serviços ferramentas para o Windows Store aplicações](http://www.microsoft.com/download/details.aspx?id=30714) expande a experiência de referência do serviço adicionar com suporte de OData do lado do cliente para as aplicações da loja Windows no Visual Studio.

## <a name="develop-apps"></a>Desenvolver aplicações

### <a name="getting-ready"></a>Preparar-se

Crie um projeto de aplicação da loja Windows novo no Visual Studio 2012 ou posterior:

![arquivo de aplicações-armazenamento-vs-projetos][store-apps-storage-vs-project]

Em seguida, adicione uma referência para a biblioteca de cliente de armazenamento do Azure clicando em **referências**, clicando em **Adicionar referência**e, em seguida, navegar para o armazenamento cliente biblioteca para Runtime do Windows que transferiu:

![loja de aplicações--Escolher-biblioteca de armazenamento][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>Utilizar a biblioteca com os serviços Blob e fila de espera

Neste momento, a sua aplicação está pronta para os serviços de Blobs do Azure e fila de espera de chamadas. Adicione as seguintes instruções de **utilizar** para que os tipos de armazenamento do Windows Azure podem ser referenciados diretamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Em seguida, adicione um botão à sua página. Adicione o seguinte código ao seu evento **clique em** e modificar o seu método de processador de eventos ao utilizar a [palavra-chave de assíncrona](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Este código assume que tem variáveis de cadeia duas, *accountName* e *accountKey*. Que representam o nome da sua conta de armazenamento e a chave de conta que está associada essa conta.

Criar e executar a aplicação. Clicar no botão verificar se um contentor denominado *container1* existe na sua conta e, em seguida, criá-lo caso não esteja.

### <a name="using-the-library-with-the-table-service"></a>Utilizar a biblioteca com o serviço de tabela

Tipos de que são utilizados para comunicar com o serviço de tabela do Azure dependem WCF Data Services para a biblioteca de aplicação da loja Windows. Em seguida, adicione uma referência para as bibliotecas WCF necessárias utilizando a consola do Gestor de pacote:

![loja de aplicações--pacote-Gestor de armazenamento][store-apps-storage-package-manager]

Utilize o seguinte comando para o Gestor de pacotes de ponto para a localização no seu computador:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Este comando irá adicionar automaticamente todas as referências necessárias ao seu projeto. Se não pretende utilizar a consola do Gestor de pacote, pode adicionar a pasta de NuGet de serviços de dados WCF no seu computador local para a lista de origens de pacote e, em seguida, adicione a referência através da IU, tal como descrito [Gerir NuGet pacotes](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog)ao utilizar a caixa de diálogo.

Quando tiver referenciada o pacote de NuGet de serviços de dados WCF, altere o código no evento de **clique** o botão:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Este código verifica se uma tabela designada *tabela1* existe na sua conta e, em seguida, cria-lo caso não esteja.

Também pode adicionar uma referência a Microsoft.WindowsAzure.Storage.Table.dll, que se encontra disponível no mesmo pacote que transferiu. Esta biblioteca contém funcionalidades adicionais, tal como serialização de reflexo e consultas genéricas. Tenha em atenção que esta biblioteca não suporta JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
