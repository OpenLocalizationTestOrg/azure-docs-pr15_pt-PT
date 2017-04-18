<properties
    pageTitle="Ligar o Excel para Hadoop com o Power Query | Microsoft Azure"
    description="Saiba como tirar partido dos componentes do business intelligence e utilizar o Power Query para Excel para aceder aos dados armazenados num Hadoop no HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>


#<a name="connect-excel-to-hadoop-by-using-power-query"></a>Ligar o Excel para Hadoop utilizando o Power Query

Uma funcionalidade chave da solução Microsoft grande dados é a integração do componentes do Microsoft business intelligence (BI) com clusters de Hadoop no Azure HDInsight. Um exemplo de principal desta integração é a capacidade de ligar o Excel para a conta de armazenamento do Windows Azure que contém os dados associados ao seu cluster Hadoop através do Microsoft Power Query para o suplemento do Excel. Este artigo explica como configurar e utilizar o Power Query para consultar os dados associados a um cluster de Hadoop gerido com HDInsight.

> [AZURE.NOTE] Enquanto os passos neste artigo podem ser utilizados com cluster uma Linux ou HDInsight baseados no Windows, Windows é necessário para a estação de trabalho do cliente.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Cluster de um HDInsight**. Para configurar um, consulte o artigo [Introdução ao Azure HDInsight][hdinsight-get-started].
- **A estação de trabalho** que está a executar o Windows 7, Windows Server 2008 R2 ou um sistema operativo posterior.
- **Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 autónomo, ou do Office 2010 Professional Plus**.


## <a name="install-power-query"></a>Instalar o Power Query

Power Query pode ser utilizado para importar dados a partir de uma variedade de origens no Microsoft Excel, onde-pode power ferramentas de BI como o PowerPivot e Power View. Em particular, o Power Query pode importar dados que tenha sido saída ou que tenha sido gerado por uma tarefa de Hadoop em execução num HDInsight cluster.

Transferir o Microsoft Power Query para Excel a partir do [Centro de transferências da Microsoft] [ powerquery-download] e instalá-lo.

## <a name="import-hdinsight-data-into-excel"></a>Importar dados do HDInsight para o Excel

O suplemento Power Query para Excel torna mais fácil importar dados a partir do seu cluster HDInsight para o Excel, onde ferramentas de BI, tais como o PowerPivot e Power Map podem ser utilizados para inspecionar, analisar e apresentam os dados.

**Para importar dados a partir de um cluster de HDInsight**

1. Abra o Excel.

2. Crie um novo livro em branco.

3. Clique no menu do **Power Query** , clique na **Partir do Azure**e, em seguida, clique em **Do Microsoft Azure HDInsight**.

    ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **Nota:** Se não vir o menu do **Power Query** , aceda a **ficheiro** > **Opções** > **Suplementos**e selecione **suplementos COM** , a partir da caixa pendente **Gerir** na parte inferior da página. Selecione o botão **Ir …** e certifique-se de que a caixa para o Power Query para Excel suplemento tiver sido selecionada.

    **Nota:** Power Query também permite-lhe importar dados a partir de HDFS ao clicar em **De outras origens**.

3. **Nome da conta**, introduza o nome da conta de armazenamento de Blobs do Azure associado com o seu cluster e, em seguida, clique em **OK**. Isto pode ser a [conta de armazenamento predefinida](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou com uma conta de armazenamento ligadas.  O formato é *https://<StorageAccountName>.blob.core.windows.net/*.

4. **Chave da conta**, introduza a chave da conta de armazenamento de BLOBs e, em seguida, clique em **Guardar**. (Necessita de fazer isto apenas na primeira vez que acede neste arquivo.)

5. No painel de **navegador** à esquerda do Editor de consultas, faça duplo clique no nome de contentor de armazenamento de Blobs. Por predefinição, o nome do contentor é o mesmo nome que o nome do cluster.

6. Localize **hivesampledata. txt** na coluna **nome** (o caminho da pasta é **... / hive/armazém/hivesampletable/**) e, em seguida, clique em **binário** à esquerda da hivesampledata. txt. Hivesampledata. txt é fornecido com todos os clusters. Opcionalmente, pode utilizar o seu ficheiro.

    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Se pretender, pode mudar o nome os nomes das colunas. Quando estiver pronto, clique em **Fechar e carregar**.  Os dados tem sido carregados ao seu livro:

    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Próximos passos

Neste artigo, o que aprendeu como utilizar o Power Query para obter dados do HDInsight para o Excel. Da mesma forma, pode obter dados do HDInsight numa base de dados do SQL Azure. Também é possível carregar dados para HDInsight. Para saber mais, consulte os artigos seguintes:

* [Ligar o Excel ao HDInsight com o controlador ODBC ramo da Microsoft][hdinsight-ODBC]
* [Carregar dados ao HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
