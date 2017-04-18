<properties
   pageTitle="Guia para utilizar PolyBase no armazém de dados SQL | Microsoft Azure"
   description="Directrizes e recomendações para utilizar PolyBase em cenários armazém de dados SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guia para utilizar PolyBase no armazém de dados SQL

Este guia dá informações práticas para utilizar PolyBase no armazém de dados SQL.

Para começar a utilizar, consulte o tutorial de [carregar dados com PolyBase][] .


## <a name="rotating-storage-keys"></a>Rodar teclas de armazenamento

A frequentemente que pretende alterar a chave de acesso ao seu armazenamento blob por motivos de segurança.

A forma mais elegante para desempenhar esta tarefa é seguir um processo conhecido como "as teclas de rodar". É provavelmente, reparou que tem duas chaves de armazenamento da sua conta de armazenamento de Blobs. Isto é, de modo a que pode transição

Rodar suas chaves de conta de armazenamento Azure é um processo simples três passos

1. Criar a segunda credencial de base de dados confinado com base na tecla de acesso de armazenamento secundário
2. Criar a segunda origem de dados externa, com base desativar esta nova credencial
3. Largue e criar as tabelas externas apontar para a nova origem de dados externos

Quando tiver migrado todos os seus externo tabelas para a nova origem de dados externos, em seguida, pode efetuar as tarefas de limpeza:

1. Origem de dados externa primeira lista pendente
2. Credenciais com base na tecla de acesso de armazenamento primário no âmbito da primeira lista pendente de base de dados
3. Inicie sessão no Azure e gerar a tecla de acesso primária pronta para utilização posterior

## <a name="query-azure-blob-storage-data"></a>Consultar dados de armazenamento de Blobs do Azure
Consultas de tabelas externas simplesmente utilizam o nome da tabela como se fosse uma tabela relacional.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] Uma consulta de uma tabela externa pode falhar com o erro *"Consulta interrompida – o valor máximo rejeitar limiar foi instalações atingida ao ler a partir de uma fonte externa"*. Isto indica que os seus dados externos contém registos *dirty* . Um registo de dados é considerado 'dirty' se o número/tipos de dados reais de colunas não correspondam as definições da coluna da tabela externa ou se os dados não está em conformidade com o formato de ficheiro externos especificado. Para corrigir, certifique-se de que a tabela externa e definições de formato de ficheiro externo estão corretas e está em conformidade com os dados externos para estas definições. No caso de estarem dirty um subconjunto dos registos de dados externos, pode optar por rejeitar estes registos das suas consultas utilizando as opções de rejeitar em criar DDL de tabela externa.


## <a name="load-data-from-azure-blob-storage"></a>Carregar os dados a partir do armazenamento de Blobs do Azure
Este exemplo carrega dados a partir do armazenamento de Blobs do Azure a base de dados do SQL armazém de dados.

Armazenar os dados diretamente remove o tempo de transferência de dados de consultas. Armazenar os dados com um índice columnstore melhora o desempenho de consulta para consultas de análise por até 10 x.

Este exemplo utiliza a declaração de criar tabela como SELECIONE para carregar os dados. A nova tabela herda as colunas com o nome da consulta. Os tipos de dados dessas colunas qual herda a definição de tabela externa.

Criar tabela como SELECIONE é uma altamente performant instrução Transact-SQL que carrega os dados em paralelo para todos os nós de cluster do seu armazém de dados SQL.  -Foi originalmente desenvolvido para o motor de processamento previstos excederem consideravelmente paralelas (MPP) no sistema de plataforma de análise e está agora no armazém de dados do SQL.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Consulte o artigo [Criar tabela como SELECIONE (Transact-SQL)][].

## <a name="create-statistics-on-newly-loaded-data"></a>Criar estatísticas nos dados carregados recentemente

Armazém de dados SQL Azure é que ainda não suporte automática criar ou actualizar as estatísticas de automaticamente.  Para obter o melhor desempenho de consultas, é importante que estatísticas de ser criada em todas as colunas de todas as tabelas após a primeira carregar ou quaisquer alterações substanciais ocorrerem dos dados.  Para obter uma explicação detalhada das estatísticas, consulte o tópico de [Estatísticas][] no grupo desenvolver de tópicos.  Abaixo está um exemplo rápido de como criar estatísticas sobre a tabela carregado neste exemplo.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Exportar dados para o armazenamento de Blobs do Azure
Esta secção mostra como exportar dados do SQL Data Warehouse ao armazenamento de Blobs do Azure. Este exemplo utiliza a criar EXTERNOS tabela como SELECIONE que é um altamente performant instrução Transact-SQL para exportar os dados em paralelo de todos os nós de cluster.

O exemplo seguinte cria uma tabela externa Weblogs2014 utilizar definições de coluna e os dados a partir de dbo. Tabela de blogues Web. A definição de tabela externa está armazenada no armazém de dados SQL e os resultados da instrução SELECT são exportados para o directório "/ Arquivar/log2014 /" dentro do contentor de BLOBs especificado pela origem de dados. Os dados são exportados no formato de ficheiro de texto especificado.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## <a name="working-around-the-polybase-utf-8-requirement"></a>Trabalhar em torno o requisito de PolyBase UTF-8
Em apresentar PolyBase suporta carregamento de ficheiros de dados que tenham sido codificado UTF-8. Tal como acontece UTF-8 utiliza a codificação de caracteres mesmo como ASCII PolyBase irá também suporte carregar dados são ASCII codificado. No entanto, PolyBase não suporta outra codificação de caráter como UTF-16 / Unicode ou de caracteres ASCII expandidos. ASCII expandido inclui caracteres com acentos tal como o trema que é comuns em alemão.

Para contornar este requisito a melhor resposta é escrever novamente para codificação UTF-8.

Existem várias formas para o fazer. Abaixo estão duas abordagens através do Powershell:

### <a name="simple-example-for-small-files"></a>Exemplo simples para ficheiros pequenos

Abaixo encontra uma linha de um script Powershell que cria o ficheiro simple.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

No entanto, embora esta é uma forma simple para codificar novamente os dados por não significa é mais eficiente. Exemplo de transmissão de io abaixo é muito, muito mais rapidamente e obtém o mesmo resultado.

### <a name="io-streaming-example-for-larger-files"></a>Exemplo de transmissão de es para ficheiros maiores

O código de exemplo abaixo é mais complexo mas como sequências as linhas de dados de origem-alvo é muito mais eficiente. Utilize esta abordagem para ficheiros maiores.

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre como mover os dados para armazém de dados SQL, consulte o artigo [Descrição geral da migração de dados][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Carregar os dados com PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Descrição geral da migração de dados]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[Criar formato de ficheiro externo (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026). aspx [criar a tabela externa (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspxx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[Criar tabela como SELECIONAR (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
