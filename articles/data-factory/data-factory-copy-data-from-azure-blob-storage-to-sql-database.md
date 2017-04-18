<properties
    pageTitle="Copiar dados de armazenamento de BLOBs para base de dados SQL | Microsoft Azure"
    description="Este tutorial mostra-lhe como utilizar cópia atividade no pipeline fábrica de dados do Azure para copiar dados de armazenamento de BLOBs para base de dados SQL."
    Keywords="blob sql, armazenamento de BLOBs copiar dados"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Copiar dados de armazenamento de BLOBs para base de dados SQL com a fábrica de dados 
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Copiar o Assistente](data-factory-copy-data-wizard-tutorial.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modelo do Gestor de recursos Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Neste tutorial, crie uma fábrica de dados com uma tubagem para copiar dados a partir do armazenamento de BLOBs para base de dados SQL.

A atividade de cópia executa o movimento de dados no Azure fábrica de dados. Estiver ligado a por um serviço disponível globalmente que pode copiar dados de entre vários arquivos de dados de forma segura, fiável e dimensionáveis. Consulte o artigo [Dados movimento atividades](data-factory-data-movement-activities.md) artigo para obter detalhes sobre a atividade de cópia.  

> [AZURE.NOTE] Para obter uma descrição detalhada do serviço fábrica de dados, consulte o artigo [Introdução ao Azure fábrica de dados](data-factory-introduction.md) .

##<a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Subscrição azure**.  Se não tiver uma subscrição, pode criar uma conta de avaliação gratuita apenas de duas minutos. Consulte o artigo [Versão de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
- **Conta de armazenamento azure**. Utilize o armazenamento de BLOBs como um arquivo de dados de **origem** neste tutorial. Se não tiver uma conta de armazenamento Azure, consulte o artigo [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) para obter os passos criar uma.
- **Base de dados azure SQL**. Utilize uma base de dados do Azure SQL como um arquivo de dados de **destino** neste tutorial. Se não tiver uma base de dados do Azure SQL que pode utilizar no tutorial, veja [como criar e configurar uma base de dados do SQL Azure](../sql-database/sql-database-get-started.md) para criar uma.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Utilizar o SQL Server Management Studio ou Visual Studio para criar uma base de dados de exemplo e para ver os dados de resultados na base de dados.  

## <a name="collect-blob-storage-account-name-and-key"></a>Recolher nome de conta de armazenamento de BLOBs e chave 
Precisa do nome da conta e a chave de conta da sua conta de armazenamento Azure para fazer este tutorial. Tenha em atenção para baixo do **nome da conta** e a **chave de conta** para a sua conta de armazenamento Azure.

1. Inicie a sessão [portal do Azure](https://portal.azure.com/).
2. Clique em **mais serviços** no menu à esquerda e selecione **Contas de armazenamento**.

    ![Procurar - contas de armazenamento](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\browse-storage-accounts.png)
3. No pá **Armazenamento contas** , selecione a **conta de armazenamento Azure** que pretende utilizar neste tutorial.
4. Selecione **as teclas de acesso** de ligação em **Definições**.
5.  Clique em **Copiar** (imagem) botão junto à caixa de texto **nome de conta de armazenamento** e guardar/colá-la num local (por exemplo: num ficheiro de texto).
6. Repita o passo anterior para copiar ou uma nota para baixo a **chave1**.
    
    ![Tecla de acesso de armazenamento](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\storage-access-key.png)
7. Feche todas as lâminas ao clicar em **X**.

## <a name="collect-sql-server-database-user-names"></a>Recolher do SQL server, base de dados, nomes de utilizador
Tem os nomes de servidor, base de dados e utilizador para fazer este tutorial do Azure SQL. Tenha em atenção para baixo de nomes de **servidor**, **base de dados**e **utilizador** para a base de dados do Azure SQL.

1. No **portal do Azure**, clique em **mais serviços** no lado esquerdo e selecione **bases de dados SQL**.
2. No **pá de bases de dados do SQL**, selecione a **base de dados** que pretende utilizar neste tutorial. Tenha em atenção para baixo no **nome da base de dados**.  
3. No pá **base de dados SQL** , clique em **Propriedades** em **Definições**.
4. Anote os valores para o **Nome de servidor** e **Início de sessão do servidor de administrador**.
5. Feche todas as lâminas ao clicar em **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que os serviços Azure aceder ao SQL server 
Certifique-se de **que **Permitir o acesso aos serviços Azure** definição ativada para o seu Azure SQL server para que o serviço de dados fábrica pode aceder ao servidor do Azure SQL** . Para verificar e ative esta definição, efetue os seguintes passos:

1. Clique em **mais serviços** concentrador à esquerda e clique em **servidores de SQL**.
2. Selecione o seu servidor e clique em **Firewall** em **Definições**. 
4. Na pá **as definições da Firewall** , clique em **ligado** para **Permitir o acesso aos serviços Azure**.
5. Feche todas as lâminas ao clicar em **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar o armazenamento de BLOBs e base de dados SQL 
Agora, se prepare o armazenamento de Blobs do Azure e a base de dados Azure SQL para o tutorial executando os seguintes passos:  

1. Iniciar o bloco de notas, cole o seguinte texto e guardá-lo como **emp.txt** para **C:\ADFGetStarted** pasta no seu disco rígido.

        John, Doe
        Jane, Doe

2. Utilize ferramentas, como o [Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) para criar o contentor **adftutorial** e para carregar o ficheiro **emp.txt** para o contentor.

    ![Explorador de armazenamento Azure. Copiar dados de armazenamento de BLOBs para base de dados SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Utilize o seguinte script SQL para criar a tabela de **emp** na base de dados SQL Azure.  


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    **Se tiver o SQL Server 2012/2014 instalado no seu computador:** siga as instruções do [passo 2: ligar à base de dados SQL da gestão de Azure SQL base de dados com o SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md#Step2) artigo ligar ao seu servidor Azure SQL e executar o script de SQL. Este artigo utiliza o [portal Azure clássico](http://manage.windowsazure.com), não o [novo portal Azure](https://portal.azure.com), para configurar a firewall para um servidor do Azure SQL.

    Se o cliente não é permitido para aceder ao servidor do Azure SQL, terá de configurar a firewall do seu servidor de Azure SQL permitir o acesso a partir do seu computador (endereço IP). Consulte [Este artigo](../sql-database/sql-database-configure-firewall-settings.md) para obter os passos configurar a firewall para o seu servidor Azure SQL.

Concluiu os pré-requisitos. Pode criar uma fábrica de dados utilizando uma das seguintes formas. Clique dos separadores na parte superior ou as ligações seguintes para executar o tutorial.     

- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Copiar o Assistente](data-factory-copy-data-wizard-tutorial.md)
