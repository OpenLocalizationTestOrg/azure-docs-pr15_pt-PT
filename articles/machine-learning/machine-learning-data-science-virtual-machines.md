<properties
    pageTitle="Máquinas virtuais ciência de dados no Azure | Microsoft Azure"
    description="Definir o uma máquina de Virtual de ciências de dados"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="data-science-virtual-machines-in-azure"></a>Máquinas virtuais ciência de dados no Azure

As instruções são fornecidas aqui que descrevem como configurar um VM Azure e um VM Azure com o serviço do SQL como servidores IPython bloco de notas. A máquina virtual do Windows está configurada com ferramentas como IPython bloco de notas, Explorador de armazenamento do Azure e AzCopy, bem como outras utilitários que são úteis para projetos de ciências de dados de suporte. Azure Explorador de armazenamento e AzCopy, por exemplo, fornecem formas convenientes para carregar os dados ao armazenamento Azure a partir do seu computador local ou para transferi-lo para o seu computador local a partir do armazenamento. 

Este hiperligações do menu para tópicos que descrevem como configurar os ambientes de ciências dados vários utilizados pelo [Processo de ciências de dados de equipa (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Vários tipos de máquinas virtuais Azure podem ser aprovisionados e configurados para ser utilizado como parte de um ambiente de ciências dados baseado na nuvem. A decisão sobre que versão do máquina virtual para utilizar depende o tipo e a quantidade de dados para ser modelados com formação de máquina e localização de destino para os dados na nuvem. 

* Para obter orientações sobre as questões a ter em consideração quando tomar esta decisão, consulte o artigo [Planear o Azure máquina dados ciência ambiente de aprendizagem](machine-learning-data-science-plan-your-environment.md). 
* Para um catálogo de alguns dos cenários que poderá encontrar quando efetuar análise avançadas, consulte o artigo [cenários para a tecnologia no Azure máquina aprendizagem e o processo de análise avançadas](machine-learning-data-science-plan-sample-scenarios.md)

São fornecidos dois conjuntos de instruções:

* [Configurar uma máquina virtual Azure como um servidor de IPython bloco de notas para análise avançadas](machine-learning-data-science-setup-virtual-machine.md) mostra como pode aprovisionar uma máquina virtual Azure com IPython bloco de notas e outras ferramentas utilizadas para fazer ciência de dados para casos em que um formulário de armazenamento Azure diferente de SQL pode ser utilizado para armazenar os dados.

* [Configurar uma máquina de virtual Azure SQL Server como um servidor de IPython bloco de notas para análise avançadas](machine-learning-data-science-setup-sql-server-virtual-machine.md) mostra como pode aprovisionar uma máquina de virtual Azure SQL Server com IPython bloco de notas e outras ferramentas utilizadas para fazer ciência de dados para casos em que uma base de dados do SQL pode ser utilizado para armazenar os dados.

Uma vez aprovisionada e configurado, estes máquinas virtuais estão prontas a utilizar como servidores IPython bloco de notas para a informações detalhadas e processamento de dados e para outras tarefas necessárias em conjunto com Azure máquina aprendizagem e o processo de ciências de dados de equipa (TDSP). Os passos no processo de ciências de dados são mapeados no [caminho de formação TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir passos que mover dados para o SQL Server ou HDInsight, processam e de exemplo-lo lá preparação para a formação dos dados de com Azure máquina de formação.


> [AZURE.NOTE] Azure máquinas virtuais são preços como **pagar apenas para que utiliza**. Para se certificar de que lhe não é a serem faturadas quando não utilizar o seu máquina virtual,-tem de estar no estado de **parado (Deallocated)** a partir do [Azure clássica Portal](http://manage.windowsazure.com/). Para instruções passo a passo ou como retirar máquina virtual, consulte o artigo [encerramento e retirar máquina virtual quando não estiver em utilização](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 
