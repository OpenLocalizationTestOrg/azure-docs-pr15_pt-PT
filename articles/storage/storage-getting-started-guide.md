<properties
    pageTitle="Introdução ao armazenamento do Windows Azure dentro de cinco minutos | Microsoft Azure"
    description="Rapidamente começar no Microsoft Azure Blobs, tabela e filas utilizando o Azure armazenamento rápida inicia, Visual Studio e o emulador armazenamento Azure. Execute a sua aplicação de armazenamento do Windows Azure primeira dentro de cinco minutos."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="get-started-with-azure-storage-in-five-minutes"></a>Introdução ao armazenamento do Windows Azure dentro de cinco minutos

## <a name="overview"></a>Descrição geral

É fácil começar a desenvolver com o armazenamento do Windows Azure. Este tutorial mostra-lhe como obter uma aplicação de armazenamento do Windows Azure para cima e a executar o rapidamente. Irá utilizar os modelos de início rápido incluídos com o SDK do Azure para .NET. Estes inicia rápida conter código de pronto-e-Use que demonstra alguns cenários de programação básicos com o armazenamento do Windows Azure.

Para saber mais sobre o armazenamento do Windows Azure antes de consultar o código, consulte [Os passos seguintes](#next-steps).

## <a name="prerequisites"></a>Pré-requisitos

Terá os pré-requisitos seguintes antes de começar:

1. Para compilar e criar a aplicação, terá de uma versão do [Visual Studio](https://www.visualstudio.com/) instalado no seu computador.

2. Instale a versão mais recente [Azure SDK para .NET](https://azure.microsoft.com/downloads/). O SDK inclui os projectos de exemplo do guia de introdução do Azure, o emulador Azure armazenamento e a [Biblioteca de cliente do Azure armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Certifique-se de que tem o [.NET Framework 4,5](http://www.microsoft.com/download/details.aspx?id=30653) instalado no seu computador, tal como tal for exigido pelos projectos de exemplo do guia de introdução do Azure que iremos utilizar este tutorial.

    Se não tem a certeza de qual a versão do .NET Framework está instalada no seu computador, consulte o artigo [como: determinar qual .NET Framework estão instaladas versões](https://msdn.microsoft.com/vstudio/hh925568.aspx). Em alternativa, prima o botão **Iniciar** ou a tecla Windows, escreva **Painel de controlo**. Em seguida, clique em **programas** > **programas e funcionalidades**e determine se o 4,5 Framework .NET está listado entre os programas instalados.

4. Terá de uma subscrição do Azure e uma conta de armazenamento Azure.

    - Para obter uma subscrição do Azure, consulte o artigo [Versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opções de compra](https://azure.microsoft.com/pricing/purchase-options/)e [Membro oferece](https://azure.microsoft.com/pricing/member-offers/) (para os membros do MSDN, Microsoft Partner Network e BizSpark e outros programas da Microsoft).
    - Para criar uma conta de armazenamento no Azure, consulte o artigo [como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Executar a sua aplicação de armazenamento do Windows Azure primeira contra Azure armazenamento na nuvem

Assim que tiver uma conta, pode criar uma aplicação de armazenamento do Windows Azure simples utilizando um dos projetos de exemplo Azure rápida inicia no Visual Studio. Neste tutorial foca-se em projetos de exemplo para o armazenamento do Azure: **armazenamento do Windows Azure: Blobs**, **armazenamento do Windows Azure: ficheiros**, **armazenamento do Windows Azure: filas**, e **armazenamento do Windows Azure: tabelas**:

1. Inicie o Visual Studio.
2. No menu **ficheiro** , clique em **Novo projeto**.
3. Na caixa de diálogo **Novo projeto** , clique em **instalados** > **modelos** > **Visual c#** > **nuvem** > **tutoriais** > **Serviços de dados**.
    um. Selecione um dos seguintes modelos: **armazenamento do Windows Azure: Blobs**, **armazenamento do Windows Azure: ficheiros**, **armazenamento do Windows Azure: filas**, ou **armazenamento do Windows Azure: tabelas**.
    b. Certifique-se de que o **.NET Framework 4,5** está selecionada como a arquitetura de destino.
    - 3. c. Especifique um nome para o seu projeto e criar nova solução do Visual Studio, conforme apresentado:

    ![Azure início rápido][Image1]

Pretende rever o código de origem antes de executar a aplicação. Para rever o código, selecione **Explorador de solução** no menu **Ver** no Visual Studio. Em seguida, faça duplo clique no ficheiro de Program.cs.

Em seguida, execute a aplicação de exemplo:

1.  No Visual Studio, selecione **Explorador de solução** no menu **Ver** . Abra o ficheiro de App e comenta a cadeia de ligação para o emulador armazenamento Azure:

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Remova os comentários a cadeia de ligação para o serviço de armazenamento do Azure e fornecer a chave de acesso e nome de conta de armazenamento no ficheiro App:`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    Para obter a sua chave de acesso da conta de armazenamento, consulte o artigo [Gerir o seu teclas de acesso de armazenamento](storage-create-storage-account.md#manage-your-storage-access-keys).

3.  Depois de lhe fornece o nome de conta de armazenamento e tecla de acesso no ficheiro App, no menu **ficheiro** , clique em **Guardar tudo** para guardar todos os ficheiros de projeto.
4.  No menu **construir** , clique em **Criar solução**.
5.  No menu **Depurar** , prima **F11** para executar a solução passo a passo ou prima **F5** para executar a solução.


## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Executar a sua aplicação de armazenamento do Windows Azure primeira localmente contra o emulador de armazenamento do Azure

O [Azure armazenamento emulador](storage-use-emulator.md) fornece um ambiente local que emula os serviços de Blobs do Azure, tabela de fila e para fins de programação. Pode utilizar o emulador de armazenamento para testar a sua aplicação de armazenamento localmente, sem criação de uma subscrição do Azure ou uma conta de armazenamento e sem sempre qualquer custo.

Para experimentar, vamos criar uma aplicação de armazenamento do Windows Azure simples utilizando um dos projetos de exemplo Azure rápida inicia no Visual Studio. Neste tutorial foca-se em projetos de exemplo **Armazenamento de Blobs do Azure**, **Armazenamento de tabela do Azure**e **Azure fila de armazenamento** :

1. Inicie o Visual Studio.
2. No menu **ficheiro** , clique em **Novo projeto**.
3. Na caixa de diálogo **Novo projeto** , clique em **instalados** > **modelos** > **Visual c#** > **nuvem** > **tutoriais** > **Serviços de dados**.
    um. Selecione um dos seguintes modelos: **armazenamento do Windows Azure: Blobs**, **armazenamento do Windows Azure: ficheiros**, **armazenamento do Windows Azure: filas**, ou **armazenamento do Windows Azure: tabelas**.
    b. Certifique-se de que o **.NET Framework 4,5** está selecionada como a arquitetura de destino.
    c. Especifique um nome para o seu projeto e criar nova solução do Visual Studio, conforme apresentado:

    ![Azure início rápido][Image1]

4.  No Visual Studio, selecione **Explorador de solução** no menu **Ver** . Abra o ficheiro de App e o comentário saída a cadeia de ligação para a sua conta de armazenamento Azure se já tiver adicionado uma. Remova os comentários, em seguida, a cadeia de ligação para o emulador armazenamento Azure:

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Pretende rever o código de origem antes de executar a aplicação. Para rever o código, selecione **Explorador de solução** no menu **Ver** no Visual Studio. Em seguida, faça duplo clique no ficheiro de Program.cs.

Em seguida, execute a aplicação de exemplo no emulador de armazenamento de Azure:

1.  Prima a tecla Windows, procure *emulador de armazenamento do Windows Azure*, ou no botão **Iniciar** e iniciar a aplicação. Quando inicia o emulador, verá um ícone e uma notificação na área de vista de tarefas do Windows.
2.  No Visual Studio, clique em **Criar solução** , no menu de **Criar** .
3.  No menu **Depurar** , prima **F11** para executar a solução passo a passo ou prima **F5** para executar a solução a partir do início ao fim.

## <a name="next-steps"></a>Próximos passos

Consulte estes recursos para saber mais sobre o armazenamento do Windows Azure:

* [Introdução ao armazenamento do Microsoft Azure](storage-introduction.md)
* [Começar a trabalhar com o Explorador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução ao armazenamento de tabela do Azure utilizando o .NET](storage-dotnet-how-to-use-tables.md)
* [Introdução ao armazenamento de fila de espera do Windows Azure utilizando o .NET](storage-dotnet-how-to-use-queues.md)
* [Introdução ao armazenamento de ficheiros do Azure no Windows](storage-dotnet-how-to-use-files.md)
* [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md)
* [Documentação do armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Biblioteca de cliente do Microsoft Azure armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Serviços de armazenamento Azure REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
