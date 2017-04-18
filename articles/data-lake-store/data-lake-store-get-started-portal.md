<properties 
   pageTitle="Introdução ao arquivo de dados de Lake | Azure" 
   description="Utilizar o portal para criar uma conta do arquivo de Lake de dados e executar operações básicas do arquivo de dados Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Introdução ao arquivo de Lake Azure dados utilizando o Portal do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Clip Azure](data-lake-store-get-started-cli.md)
- [NODE.js](data-lake-store-manage-use-nodejs.md)

Saiba como utilizar o Portal do Azure para criar uma conta do arquivo de Lake de dados do Azure e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a sua conta, etc. Para mais informações sobre o arquivo de dados de Lake, consulte o artigo [Descrição geral do Azure Lake arquivo de dados](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-fast-with-videos"></a>Saiba rapidamente com vídeos?

Veja os vídeos seguintes para começar a utilizar o arquivo de dados de Lake.

* [Criar uma conta do arquivo de Lake de dados](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gerir os dados no arquivo de dados de Lake utilizando o Explorador de dados](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Criar uma conta do arquivo de Lake de dados do Azure

1. Inicie sessão novo [Azure Portal](https://portal.azure.com).

2. Clique em **Novo**, clique em **dados + armazenamento**e, em seguida, clique em **Arquivo de Lake do Azure dados**. Leia as informações na pá **Azure dados Lake arquivo** e, em seguida, clique em **Criar** no canto inferior esquerdo da pá.

3. No pá **Novo dados Lake arquivo** , forneça os valores tal como apresentado na captura de ecrã abaixo:

    ![Criar uma nova conta do arquivo de Lake de dados do Azure] (./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Criar uma nova conta Azure dados Lake")

    - **Subscrição**. Selecione a subscrição sob o qual pretende criar uma nova conta do arquivo de Lake de dados.
    - **Grupo de recursos**. Selecione um grupo de recursos existente ou clique em **criar um grupo de recursos** para criar uma. Um grupo de recursos é um contentor que detém recursos relacionados para uma aplicação. Para mais informações, consulte o artigo [Grupos de recursos no Azure](azure-resource-manager/resource-group-overview.md#resource-groups).
    - **Localização**: selecione uma localização onde pretende criar a conta de arquivo de Lake de dados.

4. Selecione **Afixar Startboard** se pretender que a conta de arquivo de Lake de dados para serem acessíveis a partir do Startboard.

5. Clique em **Criar**. Se tiver optado por afixar a conta para o startboard, que é feito novamente para o startboard e pode ver o progresso da sua aprovisionamento de conta de arquivo de Lake de dados. Assim que a conta de arquivo de Lake de dados está aprovisionada, mostra a pá de conta para cima.

6. Expanda o **Essentials** pendente para ver as informações sobre a sua conta do arquivo de Lake de dados, tais como o recurso agrupe são uma parte do, a localização, etc. Clique no ícone de **Guia** para ver as ligações para outros recursos relacionados com o arquivo de dados de Lake.

    ![Conta de arquivo de Lake de dados do seu Azure] (./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Conta o Azure dados Lake")

## <a name="createfolder"></a>Criar pastas na conta Azure dados Lake arquivo

Pode criar pastas sob a sua conta de arquivo de dados de Lake para gerir e armazenar dados.

1. Abra a conta de arquivo de Lake de dados que acabou de criar. A partir do painel esquerdo, clique em **Procurar**, clique em **Arquivo de dados de Lake**e, em seguida, a partir do pá arquivo Lake de dados, clique no nome de conta sob o qual pretende criar pastas. Se afixados a conta para o startboard, clique em mosaico essa conta.

2. No seu pá de conta do arquivo de Lake de dados, clique em **Explorador de dados**.

    ![Criar pastas na conta do arquivo de dados de Lake] (./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Criar pastas na conta do arquivo de dados de Lake")

3. No seu pá de conta do arquivo de Lake de dados, clique em **Nova pasta**, introduza um nome para a nova pasta e, em seguida, clique em **OK**.
    
    ![Criar pastas na conta do arquivo de dados de Lake] (./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Criar pastas na conta do arquivo de dados de Lake")
    
    A pasta recentemente criada será listada na pá **Explorador de dados** . Pode criar pastas aninhadas até qualquer nível.

    ![Criar pastas na conta Lake de dados] (./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Criar pastas na conta Lake de dados")


## <a name="uploaddata"></a>Carregar dados à conta Azure dados Lake arquivo

Pode carregar os dados a uma conta do arquivo de Lake Azure dados diretamente no nível de raiz ou para uma pasta que criou dentro da conta. Na captura de ecrã abaixo, siga os passos para carregar um ficheiro para uma subpasta da pá **Explorador de dados** . Nesta captura de ecrã, o ficheiro é enviado para uma subpasta apresentada no menu de trilhos (marcado numa caixa vermelha).

Se está a procurar alguns dados de exemplo carregar, pode obter a pasta **Ambulância dados** a partir do [Azure dados Lake Git repositório](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Carregar dados] (./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")


## <a name="properties"></a>Ações disponíveis nos dados armazenados e propriedades

Clique no ficheiro recentemente adicionado para abrir o separador **Propriedades** . As propriedades associadas com o ficheiro e as ações que pode efetuar no ficheiro estão disponíveis neste pá. Também pode copiar o caminho completo para o ficheiro na sua conta Azure dados Lake loja, realçada na caixa vermelha na captura de ecrã abaixo.

![Propriedades nos dados] (./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados")

* Clique em **Pré-visualizar** para ver uma pré-visualização do ficheiro, diretamente a partir do browser. Pode especificar o formato da pré-visualização do também. Clique em **pré-visualização**, clique em **Formatar** no pá a **Pré-visualização do ficheiro** e na pá **Formato de ficheiro de pré-visualização** especifique as opções como o número de linhas para apresentar, codificação a utilizar, delimitador utilizar, etc.

  ![Formato de ficheiro de pré-visualização] (./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato de ficheiro de pré-visualização")

* Clique em **Transferir** para transferir o ficheiro para o seu computador.

* Clique em **Mudar o nome de ficheiro** para mudar o nome de ficheiro.

* Clique em **Eliminar ficheiro** para eliminar o ficheiro.


## <a name="secure-your-data"></a>Proteger os seus dados

Pode proteger os dados armazenados na sua conta de arquivo de Lake Azure dados utilizando o Azure Active Directory e controlo de acesso (ACL). Para obter instruções sobre como fazê-lo, consulte [proteger dados Azure dados Lake loja](data-lake-store-secure-data.md).


## <a name="delete-azure-data-lake-store-account"></a>Conta de eliminar Azure dados Lake arquivo

Para eliminar uma conta Azure dados Lake loja, a partir do seu pá de arquivo de Lake de dados, clique em **Eliminar**. Para confirmar a ação, será solicitado que introduza o nome da conta que pretende eliminar. Introduza o nome da conta e, em seguida, clique em **Eliminar**.

![Eliminar dados Lake conta] (./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Eliminar dados Lake conta")


## <a name="next-steps"></a>Próximos passos

- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Registos de diagnóstico do Access para o arquivo de dados de Lake](data-lake-store-diagnostic-logs.md)
