
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparar a cadeia de ligação


A tabela seguinte compara as cadeias de ligação que o programa c# tem de se ligar ao SQL Server no local versus a base de dados do SQL Azure na nuvem. As diferenças estão a negrito.


| Cadeia de ligação para<br/>Base de dados Azure SQL | Cadeia de ligação para<br/>Microsoft SQL Server |
| :-- | :-- |
| Servidor =**tcp:**{your_serverName_here}**. database.windows.net,1433**;<br/>ID de utilizador = {your_loginName_here}**@{your_serverName_here}**;<br/>Palavra-passe = {your_password_here};<br/>**Base de dados = {your_databaseName_here};**<br/>**Tempo limite da ligação = 30**;<br/>**Encriptar = True**;<br/>**TrustServerCertificate = False**; | Servidor = {your_serverName_here};<br/>ID de utilizador = {your_loginName_here};<br/>Palavra-passe = {your_password_here}; |


O **base de dados =** é opcional para SQL Server, mas é necessário para a base de dados SQL.


[Propriedades do .NET ADO SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) - aborda todos os parâmetros detalhadamente.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
