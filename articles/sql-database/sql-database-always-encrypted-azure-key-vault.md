<properties
    pageTitle="Sempre encriptados: Proteger os dados sensíveis a maiúsculas e base de dados do SQL Azure com encriptação da base de dados | Microsoft Azure"
    description="Proteger dados sensíveis na base de dados SQL em minutos."
    keywords="encriptação de dados, a chave de encriptação, a encriptação da nuvem"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="sstein"/>

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-azure-key-vault"></a>Sempre encriptados: Proteger dados sensíveis na base de dados SQL e armazenar das chaves de encriptação no Azure chave Cofre

> [AZURE.SELECTOR]
- [Azure cofre chave](sql-database-always-encrypted-azure-key-vault.md)
- [Arquivo de certificados do Windows](sql-database-always-encrypted.md)


Este artigo mostra-lhe como proteger dados sensíveis numa base de dados SQL com a encriptação de dados utilizando o [Assistente de encriptados sempre](https://msdn.microsoft.com/library/mt459280.aspx) no [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Também inclui instruções que lhe vai mostrar como armazenar cada chave de encriptação em Azure chave cofre.

Sempre encriptado é uma nova tecnologia de encriptação de dados na base de dados do SQL Azure e o SQL Server que o ajuda a proteger dados confidenciais at rest no servidor, durante a movimento entre cliente e servidor e, enquanto os dados estão a ser utilizado. Sempre encriptado assegura que os dados sensíveis a maiúsculas e nunca é apresentado como texto simples dentro do sistema de base de dados. Depois de configurar a encriptação de dados, apenas aplicações de cliente ou aplicação servidores que têm acesso às teclas podem aceder a dados em texto simples. Para obter informações detalhadas, consulte o artigo [Sempre encriptados (motor de base de dados)](https://msdn.microsoft.com/library/mt163865.aspx).


Depois de configurar a base de dados para utilizar sempre encriptados, irá criar uma aplicação de cliente no c# com o Visual Studio para trabalhar com os dados encriptados.

Saiba como configurar o sempre encriptados para uma base de dados do Azure SQL e siga os passos neste artigo. Este artigo vai aprender a efetuar as seguintes tarefas:

- Utilize o assistente sempre encriptados no SSMS para criar [chaves sempre encriptados](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Crie uma [chave de modelo global de coluna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Crie uma [chave de encriptação de coluna (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Criar uma tabela de base de dados e encriptar colunas.
- Crie uma aplicação que insere, seleciona e apresenta os dados das colunas encriptadas.


## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, terá de:

- Uma conta Azure e subscrição. Se não tiver uma, inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](../powershell-install-configure.md), versão 1.0 ou posterior. Tipo de **(Get-módulo azure - ListAvailable). Versão** para ver que versão do PowerShell estiver a executar.



## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Ativar a sua aplicação cliente ao aceder ao serviço de base de dados SQL

Tem de ativar a aplicação de cliente aceder ao serviço de base de dados SQL ao configurar a autenticação necessária e adquirir o *ClientId* e o *segredo* que precisa para autenticar a aplicação no código seguinte.

1. Abra o [portal clássica Azure](http://manage.windowsazure.com).
2. Selecione **Do Active Directory** e clique na instância do Active Directory que irá utilizar a aplicação.
3. Clique em **aplicações**e, em seguida, clique em **Adicionar**.
4. Escreva um nome para a sua aplicação (por exemplo: *myClientApp*), selecione a **Aplicação WEB**e clique na seta para continuar.
5. Para o **Início de sessão no URL** e **Aplicação ID URI** pode escrever um URL válido (por exemplo, *http://myClientApp*) e continuar.
6. Clique em **Configurar**.
7. Copie o seu **ID de cliente**. (Terá este valor no seu código mais tarde.)
8. Na secção de **teclas** , selecione **1 ano** a partir da lista de seta de lista pendente **Selecione duração** . (Irá copiar a chave depois de guardar no passo 14.)
11. Desloque para baixo e clique em **Adicionar aplicação**.
12. Deixe o conjunto de **Mostrar** às **Aplicações do Microsoft** e selecione **Gestão de serviço do Microsoft Azure**. Clique em marca de verificação para continuar.
13. Selecione **Gestão de serviços do Access Azure** a partir da lista pendente de **Permissões do delegado** .
14. Clique em **Guardar**.
15. Depois de guardar termina, copie o valor de chave na secção de **teclas** . (Terá este valor no seu código mais tarde.)



## <a name="create-a-key-vault-to-store-your-keys"></a>Criar uma chave cofre para armazenar as suas chaves

Agora que está configurada a sua aplicação de cliente e tiver o seu ID de cliente, é altura de criar uma chave cofre e configurar a sua política de acesso para que e a sua aplicação podem aceder aos segredos o cofre (chaves sempre encriptados). As permissões de *Criar*, *aceder*, *lista*, *início de sessão*, *Certifique-se*, *wrapKey*e *unwrapKey* são necessárias para criar uma nova chave de modelo global de coluna e de configuração de encriptação com SQL Server Management Studio.

Pode criar rapidamente uma chave cofre executando o seguinte script. Para obter uma explicação detalhada destes cmdlets e obter mais informações sobre como criar e configurar uma chave cofre, consulte o artigo [Introdução ao Azure chave cofre](../Key-Vault/key-vault-get-started.md).



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## <a name="create-a-blank-sql-database"></a>Criar uma base de dados do SQL em branco
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Aceda a **Nova** > **dados + armazenamento** > **base de dados SQL**.
3. Crie uma base de dados **em branco** com o nome **meio** num servidor novo ou existente. Para obter instruções detalhadas sobre como criar uma base de dados no portal do Azure, consulte o artigo [criar uma base de dados do SQL em minutos](sql-database-get-started.md).

    ![Criar uma base de dados em branco](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Terá da ligação de cadeia mais tarde no tutorial, por isso, depois de criar a base de dados, navegue para a nova base de dados do meio e copie a cadeia de ligação. Pode obter a cadeia de ligação em qualquer altura, mas é fácil copiá-lo no portal do Azure.

1. Aceda a **bases de dados SQL** > **meio** > **Mostrar cadeias de texto de ligação de base de dados**.
2. Copie a cadeia de ligação para **ADO.NET**.

    ![Copie a cadeia de ligação](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com SSMS

Abra SSMS e ligar ao servidor com a base de dados do meio.


1. Abra SSMS. (Aceda a **Ligar** > **Motor de base de dados** para abrir a janela de **ligar ao servidor** se não estiver aberto.)
2. Introduza o nome de servidor e as credenciais. O nome do servidor pode ser encontrado na pá de base de dados SQL e na cadeia de ligação que copiou anteriormente. Escreva o nome de servidor completo, incluindo *database.windows.net*.

    ![Copie a cadeia de ligação](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Se abrir a janela de **Nova regra de Firewall** , inicie sessão no Azure e permitir que SSMS criar uma nova regra de firewall para si.


## <a name="create-a-table"></a>Criar uma tabela

Nesta secção, irá criar uma tabela para colocar em espera dados pacientes. Não é inicialmente encriptados – irá configurar encriptação na secção seguinte.

1. Expanda **as bases de dados**.
1. Com o botão direito na base de dados do **meio** e clique em **Nova consulta**.
2. Cole o seguinte Transact-SQL (T-SQL) na nova janela de consulta e **executá** -lo.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Encriptar colunas (configurar sempre encriptados)

SSMS fornece um assistente que o ajuda a configurar facilmente sempre encriptados ao configurar a chave de modelo global de coluna, chave de encriptação de coluna e colunas encriptadas por si.

1. Expandir **bases de dados** > **meio** > **tabelas**.
2. Botão direito do rato na tabela de **ortodontia** e selecione **Encriptar colunas** para abrir o assistente sempre encriptados:

    ![Encriptar colunas](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

O assistente sempre encriptados inclui as seguintes secções: **Seleção de coluna**, a **Configuração de chave principal**, **validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de coluna##

Clique em **seguinte** na página de **Introdução** para abrir a página de **Seleção de coluna** . Nesta página, que irá selecionar as colunas que pretende para encriptar, [o tipo de encriptação e chave de encriptação que coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) para utilizar.

Encripte **niss** e **data de nascimento** informações para cada paciente. A coluna niss irá utilizar a encriptação determinista, que suporta pesquisas de igualdade, as associações e agrupar por. A coluna de data de nascimento irá utilizar a encriptação aleatório, que não suporta operações.

Defina o **Tipo de encriptação** para a coluna niss para **Deterministic** e a coluna de data de nascimento para **Randomized**. Clique em **seguinte**.

![Encriptar colunas](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Configuração de chave principal###

A página de **Configuração de modelo global de chave** é onde configurar o seu CMK e selecione o fornecedor de arquivo de chaves onde o CMK será armazenado. Atualmente, pode armazenar um CMK no arquivo de certificados do Windows, Azure chave cofre ou um módulo de segurança do hardware (HSM).

Este tutorial mostra como armazenar as suas chaves no Azure chave cofre.

1.     Selecione **Azure cofre chave**.
1.     Selecione o Cofre chave pretendido a partir da lista pendente.
1.     Clique em **seguinte**.

![Configuração de chave principal](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


### <a name="validation"></a>Validação###

Pode encriptar agora as colunas ou guardar um script PowerShell para executar o mais tarde. Para este tutorial, selecione **continuar para concluir agora** e clique em **seguinte**.

### <a name="summary"></a>Resumo ###

Certifique-se de que as definições estão todas as corrigir e clique em **Concluir** para concluir a configuração para sempre encriptados.


![Resumo](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### <a name="verify-the-wizards-actions"></a>Verifique se ações o Assistente

Depois do assistente estiver concluído, a base de dados está configurado para sempre encriptados. O Assistente de executadas as seguintes ações:

- Criou uma chave de modelo global de coluna e armazenou-o no Azure chave cofre.
- Criou uma chave de encriptação de coluna e armazenou-o no Azure chave cofre.
- Configurado as colunas selecionadas para encriptação. A tabela de ortodontia atualmente não tenha dados, mas quaisquer dados existentes nas colunas selecionadas são agora encriptados.

Pode verificar a criação de teclas na SSMS expandindo **meio** > **segurança** > **Encriptados teclas sempre**.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar uma aplicação de cliente funciona com os dados encriptados

Agora que sempre encriptados está configurado, pode criar uma aplicação que efetua *Insere* e *seleciona* nas colunas encriptadas.  

> [AZURE.IMPORTANT] A aplicação tem de utilizar [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objetos ao passar dados em texto simples no servidor com colunas sempre encriptados. Prisma valores literais sem utilizar objetos SqlParameter irá resultar numa exceção.

1. Abrir o Visual Studio e criar uma nova aplicação de consola do c#. Certifique-se de que o seu projeto está definido para o **.NET Framework 4.6** ou posterior.
2. Nome do projeto **AlwaysEncryptedConsoleAKVApp** e clique em **OK**.
![Nova aplicação de consola](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)
3. Instalar os pacotes de NuGet seguintes ao aceder a **Ferramentas** > **Gestor de pacotes NuGet** > **Consola do Gestor de pacote**.

Execute estas duas linhas de código na consola do Gestor de pacotes.

    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de ligação para ativar sempre encriptados

Esta secção explica como ativar sempre encriptados na sua cadeia de ligação de base de dados.


Para permitir sempre encriptados, tem de adicionar a palavra-chave de **Encriptação definição da coluna** a sua cadeia de ligação e defini-lo para **ativado**.

Pode defini-lo diretamente na cadeia de ligação ou pode configurá-lo utilizando [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A aplicação de exemplo na secção seguinte mostra como utilizar **SqlConnectionStringBuilder**.



### <a name="enable-always-encrypted-in-the-connection-string"></a>Ativar sempre encriptados na cadeia de ligação

Adicione a palavra-chave seguinte à sua cadeia de ligação.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Ativar sempre encriptado com SqlConnectionStringBuilder

O código seguinte mostra como ativar sempre encriptados definindo [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) para [ativado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="register-the-azure-key-vault-provider"></a>Registar o fornecedor do Cofre de chave do Azure

O código seguinte mostra como registar o fornecedor do Azure chave cofre com o controlador ADO.NET.

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## <a name="always-encrypted-sample-console-application"></a>Sempre encriptado aplicação de consola de exemplo

Este exemplo demonstra como:

- Modificar a cadeia de ligação para ativar sempre encriptados.
- Registe-se Azure chave cofre o fornecedor de arquivo de chaves a aplicação.  
- Inserir dados nas colunas encriptadas.
- Selecione um registo ao filtrar um valor específico numa coluna do encriptada.

Substitua os conteúdos de **Program.cs** o código seguinte. Substitua a cadeia de ligação para a variável de global connectionString na linha que diretamente precede o método principal com a cadeia de ligação válida a partir do portal Azure. Esta é a alteração de só que precisa de efetuar para este código.

Execute a aplicação para ver sempre encriptados em ação.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## <a name="verify-that-the-data-is-encrypted"></a>Certifique-se de que os dados são encriptados

Pode rapidamente verificar que estão encriptados dos dados reais no servidor por consultar os dados de ortodontia com SSMS (utilizando a ligação atual onde **Definição de encriptação de coluna** não está ainda activada).

Execute a seguinte consulta da base de dados do meio.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Pode ver que as colunas encriptadas não contenham quaisquer dados de texto simples.

   ![Nova aplicação de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


Para utilizar SSMS para aceder aos dados de texto simples, pode adicionar a *definição de encriptação coluna = ativado* parâmetro para a ligação.

1. No SSMS, o servidor no **Explorador do objeto** com o botão direito e selecione **Desligar**.
2. Clique em **Ligar** > **Motor de base de dados** para abrir a janela de **ligar ao servidor** e clique em **Opções**.
3. Clique em **Parâmetros de ligação adicionais** e escreva **definição de encriptação coluna = ativado**.

    ![Nova aplicação de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Execute a seguinte consulta da base de dados do meio.

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Agora pode ver os dados de texto simples nas colunas encriptadas.


    ![Nova aplicação de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Próximos passos
Depois de criar uma base de dados que utiliza sempre encriptados, poderá querer fazer o seguinte:

- [Rodar e limpar as suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrar dados que já estão encriptados com sempre encriptados](https://msdn.microsoft.com/library/mt621539.aspx).


## <a name="related-information"></a>Informações relacionadas

- [Sempre encriptados (desenvolvimento de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Encriptação de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx)
- [Encriptação do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Assistente de encriptado sempre](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blogue sempre encriptado](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
