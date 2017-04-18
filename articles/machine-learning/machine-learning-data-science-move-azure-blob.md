<properties
    pageTitle="Mover os dados de armazenamento de Blobs do Azure e para | Microsoft Azure"
    description="Mover os dados de armazenamento de Blobs do Azure e para"
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
    ms.date="09/14/2016"
    ms.author="bradsev;sachouks" />

# <a name="move-data-to-and-from-azure-blob-storage"></a>Mover os dados de armazenamento de Blobs do Azure e para

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Orientações sobre tecnologias utilizadas para mover os dados para e/ou a partir do armazenamento de Blobs do Azure estão ligados aqui:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
O método que se adapta melhor às depende do seu cenário. O artigo de [cenários de análise avançadas no Azure máquina aprendizagem](machine-learning-data-science-plan-sample-scenarios.md) ajuda a determinar os recursos que necessita para uma variedade de fluxos de trabalho do ciência dados utilizados no processo de análise avançadas.

> [AZURE.NOTE] Para obter uma introdução concluída ao armazenamento de Blobs do Azure, referir [Noções básicas de Blobs do Azure](../storage/storage-dotnet-how-to-use-blobs.md) e ao [Serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

Como alternativa, pode utilizar [Azure dados fábrica](https://azure.microsoft.com/services/data-factory/) para: 

- criar e agendar um pipeline de que transfere dados a partir do armazenamento de Blobs do Azure, 
- passá-lo para um serviço web de aprendizagem do Azure máquina publicado, 
- receber os resultados da análise do aspeto do Office, e 
- Carregue os resultados ao armazenamento. 

Para mais informações, consulte o artigo [Criar tubagens aspeto do Office, utilizando a fábrica de dados do Azure e Azure máquina aprendizagem](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Pré-requisitos

Este documento assume que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/transferir dados, tem de saber a sua chave de nome e a conta da conta de armazenamento Azure.

- Para configurar uma subscrição do Azure, consulte o artigo [versão de avaliação gratuita um mês](https://azure.microsoft.com/pricing/free-trial/).
- Para obter instruções sobre como criar uma conta de armazenamento e para obter conta e informações da chave, consulte o artigo [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).
