<properties
   pageTitle="Ligar ao armazenamento de dados do Azure SQL | Microsoft Azure"
   description="Como localizar o servidor de cadeia de ligação e nome para o seu armazém de dados do SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# <a name="connect-to-azure-sql-data-warehouse"></a>Ligar ao armazenamento de dados do Azure SQL

Este artigo ajuda-o a ligar ao armazenamento de dados SQL pela primeira vez.

## <a name="find-your-server-name"></a>Localizar o seu nome de servidor

O primeiro passo para estabelecer ligação ao armazém de dados SQL é-lhe saber como localizar o seu nome de servidor.  Por exemplo, o nome do servidor no exemplo seguinte é sample.database.windows.net. Para localizar o nome do servidor completamente qualificado:

1. Aceda ao [Azure portal][].
2. Clique em **bases de dados SQL** 
3. Clique em da base de dados que pretende ligar.
4. Localize o nome de servidor completo.

    ![Nome de servidor completo][1]

## <a name="supported-drivers-and-connection-strings"></a>Controladores suportados e cadeias de ligação

Armazém de dados SQL Azure suporta [ADO.NET][], [ODBC][], [PHP][]e [JDBC][]. Clique dos controladores anteriores para localizar a versão mais recente e a documentação. Para gerar automaticamente a cadeia de ligação para o controlador de que está a utilizar a partir do portal Azure, pode clicar em **Mostrar cadeias de texto de ligação de base de dados** do exemplo anterior.  Seguem-se também alguns exemplos de o aspeto de uma cadeia de ligação para cada controlador.

> [AZURE.NOTE] Considere a definir o tempo limite da ligação para 300 segundos para permitir que a ligação ao permanecem curtos períodos de indisponibilidade.

### <a name="adonet-connection-string-example"></a>Exemplo de cadeia de ligação ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemplo de cadeia de ligação ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemplo de cadeia de ligação PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemplo de cadeia de ligação de JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Definições de ligação

Armazém de dados SQL uniformiza algumas definições durante a ligação e criação de objeto. Estas definições não podem ser substituídas e incluem:

| Definição de base de dados       | Valor                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | ATIVADO                           |
| [QUOTED_IDENTIFIERS][] | ATIVADO                           |
| [FORMATODEDATA][]         | MDA                          |
| [DATEFIRST][]          | 7                            |

## <a name="next-steps"></a>Próximos passos

Para ligar e de consulta com Visual Studio, consulte o artigo [consulta com o Visual Studio][]. Para saber mais sobre as opções de autenticação, consulte o artigo [autenticação para armazém de dados do SQL Azure][].

<!--Articles-->
[Consulta com o Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Autenticação para armazém de dados do Azure SQL]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[FORMATODEDATA]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png


