<properties
    pageTitle="Sempre encriptados: Proteger os dados sensíveis a maiúsculas e base de dados do SQL Azure com encriptação da base de dados | Microsoft Azure"
    description="Proteger dados sensíveis na base de dados SQL em minutos."
    keywords="encriptar dados, encriptação de sql, encriptação da base de dados, dados importantes, sempre encriptados"
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

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Sempre encriptadas: Proteger dados sensíveis na base de dados SQL e armazenar o seu chaves de encriptação no arquivo de certificados do Windows

> [AZURE.SELECTOR]
- [Azure cofre chave](sql-database-always-encrypted-azure-key-vault.md)
- [Arquivo de certificados do Windows](sql-database-always-encrypted.md)


Este artigo mostra-lhe como proteger dados sensíveis numa base de dados SQL com a encriptação da base de dados utilizando o [Assistente de encriptados sempre](https://msdn.microsoft.com/library/mt459280.aspx) no [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). -Lo também mostra-lhe como armazenar as suas chaves de encriptação no arquivo de certificados do Windows.

Sempre encriptado é uma nova tecnologia de encriptação de dados na base de dados do SQL Azure e do SQL Server que o ajuda a proteger os dados confidenciais no resto no servidor, durante movimento entre cliente e servidor e, enquanto os dados estão a ser utilizado, garantindo que esses dados sensíveis a maiúsculas e nunca é apresentado como texto simples dentro do sistema de base de dados. Depois de encriptar dados, apenas aplicações de cliente ou aplicação servidores que têm acesso às teclas podem aceder a dados em texto simples. Para obter informações detalhadas, consulte o artigo [Sempre encriptados (motor de base de dados)](https://msdn.microsoft.com/library/mt163865.aspx).

Depois de configurar a base de dados para utilizar sempre encriptados, irá criar uma aplicação de cliente no c# com o Visual Studio para trabalhar com os dados encriptados.

Siga os passos neste artigo para saber como configurar o sempre encriptados para uma base de dados do Azure SQL. Este artigo vai aprender a efetuar as seguintes tarefas:

- Utilize o assistente sempre encriptados no SSMS para criar [Encriptados chaves sempre](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Crie um [Modelo global de coluna chave (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Crie uma [coluna de chave de encriptação (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Criar uma tabela de base de dados e encriptar colunas.
- Crie uma aplicação que insere, seleciona e apresenta os dados das colunas encriptadas.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, terá de:

- Uma conta Azure e subscrição. Se não tiver uma, inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versão 13.0.700.242 ou posterior.
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) ou posterior (no computador cliente).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).



## <a name="create-a-blank-sql-database"></a>Criar uma base de dados do SQL em branco
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **Novo** > **dados + armazenamento** > **base de dados SQL**.
3. Crie uma base de dados **em branco** com o nome **meio** num servidor novo ou existente. Para obter instruções detalhadas sobre como criar uma base de dados no portal do Azure, consulte o artigo [criar uma base de dados do SQL em minutos](sql-database-get-started.md).

    ![Criar uma base de dados em branco](./media/sql-database-always-encrypted/create-database.png)

Terá a cadeia de ligação mais tarde no tutorial. Quando a base de dados estiver criada, vá para a nova base de dados do meio e copie a cadeia de ligação. Pode obter a cadeia de ligação em qualquer altura, mas é fácil copiá-lo quando estiver no portal do Azure.

1. Clique em **bases de dados SQL** > **meio** > **Mostrar cadeias de texto de ligação de base de dados**.
2. Copie a cadeia de ligação para **ADO.NET**.

    ![Copie a cadeia de ligação](./media/sql-database-always-encrypted/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Ligar à base de dados com SSMS

Abra SSMS e ligar ao servidor com a base de dados do meio.


1. Abra SSMS. (Clique em **Ligar** > **Motor de base de dados** para abrir a janela de **ligar ao servidor** se não estiver aberto).
2. Introduza o nome de servidor e as credenciais. O nome do servidor pode ser encontrado na pá de base de dados SQL e na cadeia de ligação que copiou anteriormente. Escreva o nome de servidor completo incluindo *database.windows.net*.

    ![Copie a cadeia de ligação](./media/sql-database-always-encrypted/ssms-connect.png)

Se abrir a janela de **Nova regra de Firewall** , inicie sessão no Azure e permitir que SSMS criar uma nova regra de firewall para si.


## <a name="create-a-table"></a>Criar uma tabela

Nesta secção, irá criar uma tabela para colocar em espera dados pacientes. Este será inicialmente – uma tabela normal irá configurar encriptação na secção seguinte.

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

SSMS fornece um Assistente para configurar facilmente sempre encriptados ao configurar a CMK, CEK e colunas encriptadas por si.

1. Expandir **bases de dados** > **meio** > **tabelas**.
2. Botão direito do rato na tabela de **ortodontia** e selecione **Encriptar colunas** para abrir o assistente sempre encriptados:

    ![Encriptar colunas](./media/sql-database-always-encrypted/encrypt-columns.png)

O assistente sempre encriptados inclui as seguintes secções: **Seleção de coluna**, a **Configuração de chave principal** (CMK), **validação**e **Resumo**.

### <a name="column-selection"></a>Seleção de coluna ###

Clique em **seguinte** na página de **Introdução** para abrir a página de **Seleção de coluna** . Nesta página, que irá selecionar as colunas que pretende para encriptar, [o tipo de encriptação e chave de encriptação que coluna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) para utilizar.

Encripte **niss** e **data de nascimento** informações para cada paciente. A coluna **niss** irá utilizar a encriptação determinista, que suporta pesquisas de igualdade, as associações e agrupar por. A coluna de **data de nascimento** irá utilizar a encriptação aleatório, que não suporta operações.

Defina o **Tipo de encriptação** para a coluna **niss** para **Deterministic** e a coluna de **data de nascimento** para **Randomized**. Clique em **seguinte**.

![Encriptar colunas](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuração de chave principal###

A página de **Configuração de modelo global de chave** é onde configurar o seu CMK e selecione o fornecedor de arquivo de chaves onde o CMK será armazenado. Atualmente, pode armazenar um CMK no arquivo de certificados do Windows, Azure chave cofre ou um módulo de segurança do hardware (HSM). Este tutorial mostra como armazenar as suas chaves no arquivo de certificados do Windows.

Verifique se o **arquivo de certificados do Windows** está selecionada e clique em **seguinte**.

![Configuração de chave principal](./media/sql-database-always-encrypted/master-key-configuration.png)


### <a name="validation"></a>Validação###

Pode encriptar agora as colunas ou guardar um script PowerShell para executar o mais tarde. Para este tutorial, selecione **continuar para concluir agora** e clique em **seguinte**.

### <a name="summary"></a>Resumo###

Certifique-se de que as definições estão todas as corrigir e clique em **Concluir** para concluir a configuração para sempre encriptados.

![Resumo](./media/sql-database-always-encrypted/summary.png)


### <a name="verify-the-wizards-actions"></a>Verifique se ações o Assistente

Depois do assistente estiver concluído, a base de dados está configurado para sempre encriptados. O Assistente de executadas as seguintes ações:

- Criado um CMK.
- Criado um CEK.
- Configurado as colunas selecionadas para encriptação. Tabela **pacientes** atualmente não tenha dados, mas quaisquer dados existentes nas colunas selecionadas são agora encriptados.

Pode verificar a criação de teclas na SSMS ao aceder a **meio** > **segurança** > **Encriptados teclas sempre**. Agora pode ver as novas chaves que o assistente gerado por si.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Criar uma aplicação de cliente funciona com os dados encriptados

Agora que sempre encriptados está configurado, pode criar uma aplicação que efetua *Insere* e *seleciona* nas colunas encriptadas. Para executar com êxito a aplicação de exemplo, tem de executá-lo no mesmo computador onde executou o assistente sempre encriptados. Para executar a aplicação noutro computador, tem de implementar os certificados de sempre encriptados para o computador a executar a aplicação de cliente.  

> [AZURE.IMPORTANT] A aplicação tem de utilizar [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objetos ao passar dados em texto simples no servidor com colunas sempre encriptados. Prisma valores literais sem utilizar objetos SqlParameter irá resultar numa exceção.


1. Abrir o Visual Studio e criar uma nova aplicação de consola do c#. Certifique-se de que o seu projeto está definido para o **.NET Framework 4.6** ou posterior.
2. Nome do projeto **AlwaysEncryptedConsoleApp** e clique em **OK**.

![Nova aplicação de consola](./media/sql-database-always-encrypted/console-app.png)



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar a cadeia de ligação para ativar sempre encriptados

Esta secção explica como ativar sempre encriptados na sua cadeia de ligação de base de dados. Irá modificar a aplicação de consola que acabou de criar na secção seguinte, "Sempre encriptados aplicação da consola de exemplo."


Para permitir sempre encriptados, tem de adicionar a palavra-chave de **Encriptação definição da coluna** a sua cadeia de ligação e defini-lo para **ativado**.

Pode defini-lo diretamente na cadeia de ligação ou pode configurá-lo ao utilizar um [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A aplicação de exemplo na secção seguinte mostra como utilizar **SqlConnectionStringBuilder**.

> [AZURE.NOTE] Esta é a única alteração necessária numa aplicação cliente específica sempre encriptados. Se tiver uma aplicação existente que armazena a sua cadeia de ligação externamente (ou seja, um ficheiro de configuração), poderá conseguir ativar sempre encriptados sem alterar qualquer código.


### <a name="enable-always-encrypted-in-the-connection-string"></a>Ativar sempre encriptados na cadeia de ligação

Adicione a palavra-chave seguinte à sua cadeia de ligação:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Ativar sempre encriptado com uma SqlConnectionStringBuilder

O código seguinte mostra como ativar sempre encriptados definindo a [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) para [ativado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Sempre encriptado aplicação de consola de exemplo

Este exemplo demonstra como:

- Modificar a cadeia de ligação para ativar sempre encriptados.
- Inserir dados nas colunas encriptadas.
- Selecione um registo ao filtrar um valor específico numa coluna do encriptada.

Substitua os conteúdos de **Program.cs** o código seguinte. Substitua a cadeia de ligação para a variável de global connectionString na linha do diretamente acima do método principais a sua cadeia de ligação válida a partir do portal Azure. Esta é a alteração de só que precisa de efetuar para este código.

Execute a aplicação para ver sempre encriptados em ação.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
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

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
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

Pode rapidamente verificar que estão encriptados dos dados reais no servidor consultando os dados de **ortodontia** com SSMS. (Utilize sua ligação atual onde a definição de encriptação de coluna não está ainda activada.)

Execute a seguinte consulta da base de dados do meio.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Pode ver que as colunas encriptadas não contenham quaisquer dados de texto simples.

   ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-encrypted.png)


Para utilizar SSMS para aceder aos dados de texto simples, pode adicionar a **definição de encriptação coluna = ativado** parâmetro para a ligação.

1. Na SSMS, com o botão direito seu servidor no **Explorador do objeto**e, em seguida, clique em **Desligar**.
2. Clique em **Ligar** > **Motor de base de dados** para abrir a janela de **ligar ao servidor** e, em seguida, clique em **Opções**.
3. Clique em **Parâmetros de ligação adicionais** e escreva **definição de encriptação coluna = ativado**.

    ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Execute a seguinte consulta da base de dados do **meio** .

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Agora pode ver os dados de texto simples nas colunas encriptadas.


    ![Nova aplicação de consola](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Se ligar ao SSMS (ou qualquer cliente) a partir de um computador diferente, não terá acesso às chaves de encriptação e não conseguir desencriptar os dados.



## <a name="next-steps"></a>Próximos passos
Depois de criar uma base de dados que utiliza sempre encriptados, poderá querer fazer o seguinte:

- Execute este exemplo a partir de um computador diferente. -Não terá acesso às chaves de encriptação, de modo a não terão acesso aos dados de texto simples e não será executado com êxito.
- [Rodar e limpar as suas chaves](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrar dados que já estão encriptados com sempre encriptados](https://msdn.microsoft.com/library/mt621539.aspx).
- [Implementar sempre encriptados certificados para outros computadores cliente](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (consulte a secção de ""efetuar certificados disponíveis para aplicações e utilizadores).

## <a name="related-information"></a>Informações relacionadas

- [Sempre encriptados (desenvolvimento de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Encriptação de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx)
- [Encriptação do SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Assistente sempre encriptado](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blogue sempre encriptado](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
