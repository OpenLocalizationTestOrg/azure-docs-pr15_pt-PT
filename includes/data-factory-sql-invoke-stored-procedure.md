## <a name="invoking-stored-procedure-for-sql-sink"></a>Invocar procedimento armazenado para receber SQL

Quando copiar dados para o SQL Server ou base de dados do Azure SQL/SQL Server, um utilizador especificado procedimento armazenado pode ser configurado e chamado com parâmetros adicionais. 

Um procedimento armazenado pode ser utilizado quando mecanismos de cópia incorporados não servem o objetivo. Isto é normalmente utilizado quando processamento extra precisa de ser executadas antes da inserção final dos dados de origem na tabela de destino (intercalar colunas, procurar valores adicionais, inserção em várias tabelas...). 

Pode invocar um procedimento armazenado à escolha. O exemplo seguinte mostra como utilizar um procedimento armazenado para efetuar uma inserção simple numa tabela na base de dados. 

**Conjunto de dados de saída**

Neste exemplo, tipo está definido como: SqlServerTable. Defini-lo AzureSqlTable para utilizar com uma base de dados do Azure SQL. 

    {
      "name": "SqlOutput",
      "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlLinkedService",
        "typeProperties": {
          "tableName": "Marketing"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    
Defina a secção SqlSink numa atividade de cópia JSON da seguinte forma. Para ligar a um procedimento armazenado enquanto inserir dados, as propriedades SqlWriterStoredProcedureName e SqlWriterTableType são necessários.

    "sink":
    {
        "type": "SqlSink",
        "SqlWriterTableType": "MarketingType",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
        "storedProcedureParameters":
                {
                    "stringData": 
                    {
                        "value": "str1"     
                    }
                }
    }

Na base de dados, defina o procedimento armazenado com o mesmo nome como SqlWriterStoredProcedureName. Esta trata os dados de entrada a partir do origem especificado e inserir na tabela de saída. Repare que o nome do parâmetro do procedimento armazenado deve ser igual a tabela definida no ficheiro de JSON da tabela.

    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
    AS
    BEGIN
        DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
        INSERT [dbo].[Marketing](ProfileID, State)
        SELECT * FROM @Marketing
    END

Na base de dados, defina o tipo de tabela com o mesmo nome como SqlWriterTableType. Repare que o esquema do tipo de tabela deve ser igual ao esquema de devolvido pelos dados de entrada.

    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL
    )

A funcionalidade de procedimento armazenado tira partido das [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb675163.aspx).