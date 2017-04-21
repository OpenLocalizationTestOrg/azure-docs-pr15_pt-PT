
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### <a name="example-config-file-for-connection-string-security"></a>Ficheiro de configuração de exemplo para a segurança de cadeia de ligação


É provisórias infundada para colocar a cadeia de ligação como literais no seu código c#. É aconselhável colocar a cadeia de ligação num ficheiro de configuração. Não existem pode editar a cadeia qualquer momento, sem necessidade de recompilar.

Vamos assumir o seu programa compilado do c# se chamar **ConsoleApplication1.exe**e que este .exe encontra-se num **bin\debug\* * diretório.

Neste exemplo, a maior parte das partes da sua cadeia de ligação são armazenados num ficheiro de configuração denominado exatamente **ConsoleApplication1.exe.config**. Este ficheiro config também tem de estar na **bin\debug\**.

No XML do ficheiro de configuração seguintes verá uma cadeia de ligação com o nome **ConnectionString4NoUserIDNoPassword**. O código c# procura esta cadeia.

Tem de editar reais nomes para os marcadores de posição:

- {your_serverName_here}
- {your_databaseName_here}



        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
        
            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"
        
                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



Esta ilustração optamos por omitir dois parâmetros:

- ID de utilizador = {your_userName_here};
- Palavra-passe = {your_password_here};


Pode incluí-los, mas, por vezes, é melhor ter o programa obter esses valores de entrada do teclado pelo utilizador. Depende do.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
