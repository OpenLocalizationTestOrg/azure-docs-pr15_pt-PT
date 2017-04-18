<properties 
    pageTitle="Personalizar Hadoop clusters para o processo de ciências de dados de equipa | Microsoft Azure" 
    description="Módulos de Python populares disponibilizados em clusters de Azure HDInsight Hadoop personalizados."
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
    ms.author="hangzh;bradsev" />

# <a name="customize-azure-hdinsight-hadoop-clusters-for-the-team-data-science-process"></a>Personalizar o Azure HDInsight Hadoop clusters para o processo de ciências de dados de equipa 

Este artigo descreve como personalizar um cluster de HDInsight Hadoop instalando Anaconda de 64 bits (Python 2.7) em cada nó quando o cluster está aprovisionado como um serviço de HDInsight. Também mostra como aceder ao headnode submeter tarefas personalizadas para o cluster. Esta personalização torna muitos módulos Python populares que estão incluídos no Anaconda convenientemente disponível para utilização no funções definidas pelo utilizador (UDFs) que foram concebidos para processar ramo de registos no cluster. Para obter instruções sobre os procedimentos utilizados neste cenário, consulte o artigo [como pretende submeter ramo de consultas](machine-learning-data-science-move-hive-tables.md#submit).

No menu abaixo ligações para tópicos que descrevem como configurar os ambientes de ciências dados vários utilizados pelo [Processo de ciências de dados de equipa (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]


## <a name="customize"></a>Personalizar Cluster de Hadoop Azure HDInsight

Para criar um cluster de HDInsight Hadoop personalizado, os utilizadores têm de iniciar sessão no [**Portal do Azure clássico**](https://manage.windowsazure.com/), clique em **Novo** no canto inferior esquerdo e, em seguida, selecione serviços de dados -> HDINSIGHT -> **Criar PERSONALIZADO** para ativar a janela de **Detalhes de Cluster** . 

![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

O nome do cluster criada na página Configuração de 1 de entrada e aceitar os valores predefinidos para os outros campos. Clique na seta para ir para a página de configuração seguinte. 

![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

Na página de configuração 2, o número de **Nós de dados**de entrada, selecione a **Região/VIRTUAL rede**e selecione os tamanhos do **Nó de cabeça** e o **Nó de dados**. Clique na seta para ir para a página de configuração seguinte.

>[AZURE.NOTE] A **Rede região/VIRTUAL** tem de ser igual a região da conta de armazenamento que vai ser utilizado para o cluster HDInsight Hadoop. Caso contrário, quarta página de configuração, a conta de armazenamento que os utilizadores que pretende utilizar não serão apresentados na lista pendente do **Nome da conta**.

![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png)

Na página de configuração 3, forneça um nome de utilizador e palavra-passe para o cluster HDInsight Hadoop. **Não** selecione _Enter a Metastore ramo/Oozie_. Em seguida, clique na seta para ir para a página de configuração seguinte. 

![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png)

Na página de configuração 4, especifique o nome de conta de armazenamento, o contentor predefinido do cluster HDInsight Hadoop. Se os utilizadores seleccionam _criar o contentor predefinido_ na lista pendente **Contentor predefinido** , será criado um contentor com o mesmo nome como cluster. Clique na seta para ir para a última página de configuração.

![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png)

Na página de configuração de final **Script ações** , clique em botão **Adicionar ação de script** e preencha os campos de texto com os seguintes valores.
 
* **Nome** - qualquer cadeia como o nome desta ação de script. 
* **Tipo de nó** - selecionar **todos os nós**. 
* **URI de SCRIPT** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
    * *publicscripts* é um contentor público na conta de armazenamento 
    * *getgoing* que utilizamos para partilhar ficheiros de script do PowerShell para facilitar o trabalho de utilizadores no Azure. 
* **Parâmetros** - (deixar em branco)

Por fim, clique na marca de verificação para iniciar a criação do cluster HDInsight Hadoop personalizada. 

![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a>Aceder o nó de cabeça de Cluster de Hadoop

Os utilizadores tem de ativar acesso remoto ao cluster Hadoop no Azure antes de poderem aceder o nó cabeça do Hadoop cluster através de RDP. 

1. Inicie sessão para o [**Portal Azure clássico**](https://manage.windowsazure.com/), selecione **HDInsight** à esquerda, selecione o seu cluster de Hadoop a partir da lista de clusters de, clique no separador **configuração** e, em seguida, clique no ícone **ATIVAR remoto** na parte inferior da página.
    
    ![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png)

2. Na janela de **Configurar o ambiente de trabalho remoto** , introduza os campos nome de utilizador e palavra-passe e, selecione a data de expiração para acesso remoto. Em seguida, clique na marca de verificação para ativar o acesso remoto para o nó do Hadoop cluster cabeça.

    ![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png)
    
>[AZURE.NOTE] O nome de utilizador e palavra-passe para o acesso remoto não estão a nome de utilizador e palavra-passe que utiliza quando criou o cluster Hadoop. Estes são um conjunto de credenciais em separado. Além disso, a data de expiração de acesso remoto tem de estar dentro de 7 dias a partir da data atual.

Depois de acesso remoto está ativado, clique em **Ligar** na parte inferior da página para remoto para o nó cabeça. Iniciar sessão para o nó do Hadoop cluster cabeça ao introduzir as credenciais para o utilizador de acesso remoto que especificou anterior.

![Criar a área de trabalho](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png)

Os passos no processo de análise avançadas são mapeados no [Processo de ciências de dados de equipa (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir passos que mover dados para o HDInsight, processam e de exemplo-lo lá preparação para a formação dos dados de com Azure máquina de formação.

Consulte o artigo [como pretende submeter ramo de consultas](machine-learning-data-science-move-hive-tables.md#submit) para obter instruções sobre como aceder ao módulos Python incluídos nos Anaconda a partir do nó do cluster no funções definidas pelo utilizador (UDFs) são utilizados para processar registos de ramo armazenados no cluster de cabeça.

 
