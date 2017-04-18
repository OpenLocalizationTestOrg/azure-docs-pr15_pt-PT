<properties 
    pageTitle="Mover os dados de armazenamento de Blobs do Azure através do Explorador de armazenamento do Azure e para | Microsoft Azure" 
    description="Mover os dados de armazenamento de Blobs do Azure através do Explorador de armazenamento do Azure e para" 
    services="machine-learning,storage" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover os dados de armazenamento de Blobs do Azure através do Explorador de armazenamento do Azure e para

Azure Explorador de armazenamento é uma ferramenta gratuita da Microsoft que lhe permite trabalhar com dados de armazenamento do Windows Azure no Windows, macOS e Linux. Este tópico descreve como utilizá-la para carregar e transferir dados do armazenamento de Blobs do Azure. A ferramenta de pode ser transferida a partir do [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/).

Orientações sobre tecnologias utilizadas para mover os dados para e/ou a partir do armazenamento de Blobs do Azure estão ligados aqui:
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]   

 
> [AZURE.NOTE] Se estiver a utilizar VM tiver sido configurada com os scripts fornecidos pela [dados ciência Virtual máquinas no Azure](machine-learning-data-science-virtual-machines.md), Explorador de armazenamento do Azure já está instalado na VM.
 
> [AZURE.NOTE] Para obter uma introdução completa ao armazenamento de Blobs do Azure, consulte [Noções básicas de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   

## <a name="prerequisites"></a>Pré-requisitos

Este documento assume que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/transferir dados, tem de saber a sua chave de nome e a conta da conta de armazenamento Azure. 

- Para configurar uma subscrição do Azure, consulte o artigo [versão de avaliação gratuita um mês](https://azure.microsoft.com/pricing/free-trial/).
- Para obter instruções sobre como criar uma conta de armazenamento e para obter conta e informações da chave, consulte o artigo [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). Tome nota a tecla de acesso para a sua conta de armazenamento que precisar esta tecla para ligar à conta com a ferramenta do Explorador de armazenamento do Azure.
- A ferramenta do Explorador de armazenamento do Azure pode ser transferida a partir do [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/). Aceite as predefinições durante a instalação.


<a id="explorer"></a>
## <a name="use-azure-storage-explorer"></a>Utilizar o Explorador de armazenamento Azure 

Os passos seguintes do documento como carregar/transferir dados através do Explorador de armazenamento do Azure. 

1.  Inicie o Explorador de armazenamento do Microsoft Azure.
2.  Para ativar o Assistente de **Iniciar sessão na sua conta...** , selecione o ícone de **definições de conta Azure** , em seguida, **Adicionar uma conta** e introduza credenciais. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3.  Para ativar o Assistente de **ligar ao armazenamento do Windows Azure** , selecione o ícone de **ligar ao armazenamento Azure** . ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Introduza a chave de acesso da sua conta de armazenamento Azure no Assistente de **ligar ao armazenamento do Azure** e, em seguida, **seguinte**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Introduza o nome de conta de armazenamento na caixa **nome da conta** e, em seguida, selecione **seguinte**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Agora deverá estar listada a conta de armazenamento adicionada. Para criar um contentor blob numa conta de armazenamento, com o botão direito a nó **Blob contentores** essa conta, selecione **Criar contentor Blob**e introduza um nome.
7. Para carregar os dados a um contentor, selecione o contentor de destino e clique no botão **carregar** .![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique no **…** à direita da caixa de **ficheiros** , selecione um ou vários ficheiros para carregue a partir do sistema de ficheiros e clique em **carregar** para começar a carregar os ficheiros.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
7. Para transferir dados, selecionar o blob no contentor correspondente para transferir e clique em **Transferir**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)


