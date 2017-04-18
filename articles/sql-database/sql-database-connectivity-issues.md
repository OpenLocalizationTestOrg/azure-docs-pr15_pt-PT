<properties
    pageTitle="Corrigir um erro de ligação de SQL, o erro breves | Microsoft Azure"
    description="Saiba como resolver problemas, diagnosticar e impedir que um erro de ligação de SQL ou breves base de dados do SQL Azure. "
    keywords="ligação de SQL, cadeia de ligação, problemas de conectividade, erro breves, erro de ligação"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="daleche"/>


# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Resolução de problemas, diagnosticar e evitar erros de ligação de SQL e erros breves para base de dados SQL

Este artigo descreve como evitar, resolução de problemas, diagnosticar e mitigar erros de ligação e erros breves que a sua aplicação cliente ocorrerá quando interage com base de dados do SQL Azure. Saiba como configurar a lógica de repetição, criar a cadeia de ligação e ajustar outras definições de ligação.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Breves erros (falhas breves)

Um erro breves - além disso, falha breves - tem um problema subjacente que resolverá mais cedo própria. Um problema de erros de breves ocasional é quando o sistema Azure rapidamente desloca recursos de hardware para melhor de balanceamento de carga vários das cargas de trabalho. A maior parte dos seguintes eventos reconfiguração frequentemente concluídos em menos de 60 segundos. Durante este intervalo de tempo reconfiguração, poderá ter problemas de conectividade de base de dados do SQL Azure. Aplicações de ligar à base de dados do SQL Azure devem ser criadas para esperar estes erros breves, processá-las através da implementação de lógica de repetição no seu código em vez de para revestimentos-los aos utilizadores como erros de aplicação.

Se o seu programa de cliente é utilizando o ADO.NET, o programa é informado sobre o erro breves por lançamento de um **SqlException**. A propriedade de **número** pode ser comparada na lista de erros breves na parte superior do tópico: [códigos de erro SQL para aplicações de cliente da base de dados SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Ligação versus comando

Terá de repetir a ligação de SQL ou estabelecê-lo novamente, consoante o seguinte procedimento:

* **Ocorre um erro breves durante uma tentativa de ligação**: A ligação deve ser tentada novamente após atrasar durante vários segundos.

* **Ocorre um erro breves durante um comando de consulta SQL**: O comando não deve ser repetido imediatamente. Em vez disso, após um atraso, estabelecer a ligação deve ser recentemente. Em seguida, o comando pode ser tentada novamente.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>A lógica breves erros de repetição


Programas de cliente ocasionalmente um erro breves são mais robustos quando contêm lógica de repetição.


Quando o programa comunica com base de dados do SQL Azure por um software de terceiros 3º intermédio, inquire com o fornecedor se o software intermédio contém lógica de repetição breves erros.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Princípios para repetir


- Uma tentativa para abrir uma ligação deve ser tentada novamente se o erro é breves.


- Uma instrução de SQL SELECT falha com um problema temporário não deve ser repetida diretamente.
 - Em vez disso, estabelecer uma ligação fresca e, em seguida, volte a tentar SELECIONAR.


- Quando uma instrução SQL UPDATE falha com um problema temporário, deverá possível estabelecer uma ligação fresca antes da ATUALIZAÇÃO é repetida.
 - A lógica de repetição tem de garantir que quer a base de dados completa transação concluída, ou que a transação inteira é revertida.


#### <a name="other-considerations-for-retry"></a>Outras considerações para repetir


- Num programa batch que é iniciado automaticamente após as horas de trabalho e que irá concluir antes de manhã, pode perder de forma alguma paciente muito com intervalos de tempo tempo entre o respetivos tentativas de repetição.


- Um programa de interface de utilizador deve ter em consideração a tendência humana para dar após demasiado tempo uma atividade aguardar.
 - No entanto, a solução não deve fazer repetir a cada alguns segundos, porque essa política pode cheias o sistema com pedidos de.


#### <a name="interval-increase-between-retries"></a>Aumento do intervalo entre tentativas



Recomendamos que atrasar para 5 segundos antes da sua primeira repetição. Repetir a operação após um atraso menor do que os riscos de 5 segundos asfixiam o serviço de nuvem. Para cada subsequente repetir o atraso deve crescer exponencialmente, até um máximo de 60 segundos.

Um debate do *período de bloqueio* para clientes que utilizam ADO.NET está disponível no [SQL Server ligação agrupamento (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Também poderá querer definir um número máximo de tentativas antes do programa termina personalizada.


#### <a name="code-samples-with-retry-logic"></a>Exemplos de código com lógica de repetição


Exemplos de código com lógica de repetição, numa variedade de linguagens de programação, estão disponíveis em:

- [Bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Testar a sua lógica de repetição


Para testar a sua lógica de repetição, tem de simular ou originar um erro que pode ser corrigido enquanto o programa ainda estiver em execução.


##### <a name="test-by-disconnecting-from-the-network"></a>Teste, desligar da rede


Pode testar o seu lógica de repetição de uma forma é desligar o seu computador cliente da rede enquanto o programa está em execução. O erro será:

- **SqlException.Number** = 11001
- Mensagem: "o sistema anfitrião não é conhecido"


Como parte da primeira tentativa de repetir, o programa pode corrigir o erro ortográfico e, em seguida, tentar ligar.


Para tornar este práticos, desligue o computador da rede antes de iniciar o programa. Em seguida, o programa reconhece um parâmetro de tempo de execução que faz com que o programa:

1. Adicione temporariamente 11001 à sua lista de erros da ter em consideração como transitória.
2. Tentativa respectiva ligação à primeira como costuma fazer.
3. Depois do erro é capturado, remova 11001 a partir da lista.
4. Apresenta uma mensagem que o informa o utilizador ligue o computador da rede.
 - Coloque o cursor ainda mais execução utilizando o método de **Console.ReadLine** ou uma caixa de diálogo com um botão OK. O utilizador prime a tecla Enter depois do computador ligado à rede.
5. Tentativa novamente para se ligar, espera sucesso.


##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Corrigir erros de ortografia o nome da base de dados ao ligar


O programa pode intencionalmente incorreta o nome de utilizador antes da primeira tentativa de ligação. O erro será:

- **SqlException.Number** = 18456
- Mensagem de: "Falha ao iniciar sessão para o utilizador 'WRONG_MyUserName'."


Como parte da primeira tentativa de repetir, o programa pode corrigir o erro ortográfico e, em seguida, tentar ligar.


Para tornar esta prática, o programa conseguiu reconhecer um parâmetro de tempo de execução que faz com que o programa:

1. Adicione temporariamente 18456 à sua lista de erros da ter em consideração como transitória.
2. Adicione intencionalmente 'WRONG_' para o nome de utilizador.
3. Depois do erro é capturado, remova 18456 a partir da lista.
4. Remova 'WRONG_' a partir do nome de utilizador.
5. Tentativa novamente para se ligar, espera sucesso.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parâmetros de .NET SqlConnection para repetir de ligação


Se o seu programa de cliente se liga à base de dados do SQL Azure utilizando o .NET Framework classe **SqlConnection**, deve utilizar .NET 4.6.1 ou posterior, para que pode tirar partido sua funcionalidade de repetir de ligação. Existem detalhes da funcionalidade [aqui](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Quando constrói a [cadeia de ligação](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) para o objeto **SqlConnection** , deve coordenar os valores entre os seguintes parâmetros:

- ConnectRetryCount &nbsp; &nbsp; *(a predefinição é 1. O intervalo é 0 a 255.)*
- ConnectRetryInterval &nbsp; &nbsp; *(a predefinição é 1 segundo. O intervalo é 1 a 60.)*
- Tempo limite da ligação &nbsp; &nbsp; *(a predefinição é 15 segundos. O intervalo é 0 a 2147483647)*


Especificamente, os valores que selecionou farão o verdadeiro igualdade seguintes:

- Tempo limite da ligação = ConnectRetryCount * ConnectionRetryInterval

Por exemplo, se a contagem de = 3 e intervalo = 10 segundos, um tempo limite de apenas 29 segundos seria não se adequarem dar o sistema de tempo suficiente para as sua repetir 3º e final ao ligar: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Ligação versus comando


Os parâmetros **ConnectRetryCount** e **ConnectRetryInterval** permitir que o objeto **SqlConnection** repetir a operação de ligar sem informar ou bothering o seu programa, tal como devolver o controlo ao seu programa. O número de tentativas pode ocorrer nas seguintes situações:

- chamada do método mySqlConnection.Open
- chamada do método mySqlConnection.Execute

Existe uma certa subtileza. Se ocorre um erro breves enquanto está a ser executada a sua *consulta* , objeto **SqlConnection** não repetir a operação de ligar e -certamente não repetir a consulta. No entanto, **SqlConnection** verifica muito rapidamente a ligação antes de enviar a sua consulta para execução. Se a verificação de rápida detetar um problema de ligação, **SqlConnection** repetições a operação de ligação. Se a repetir ser bem sucedida, consulta é enviada para execução.


#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount deve ser combinado com lógica de repetição de aplicação?

Suponha que a aplicação tem lógica robusta repetir personalizado. -Poderá repetir a operação de ligar 4 vezes. Se adicionar **ConnectRetryInterval** e **ConnectRetryCount** = 3 para sua cadeia de ligação, irá aumentar a contagem de repetir a 4 * 3 = 12 tentativas. Poderá pretender não é um número de tentativas de alto.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Ligações a base de dados Azure SQL

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Ligação: Cadeia de ligação


A cadeia de ligação necessária para ligar à base de dados do SQL Azure é ligeiramente diferente de cadeia para ligar ao Microsoft SQL Server. Pode copiar a cadeia de ligação para a base de dados a partir do [Azure Portal](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Ligação: Endereço IP


Tem de configurar o servidor de base de dados SQL para aceitar a comunicação a partir do endereço IP do computador que aloja o seu programa de cliente. Pode fazê-lo ao editar as definições da firewall através do [Portal do Azure](https://portal.azure.com/).


Caso se esqueça de configurar o endereço IP, o programa irá falhar com uma mensagem de erro útil que diz o endereço IP necessário.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Para obter mais informações, consulte o artigo: [como: Configurar definições da firewall na base de dados SQL](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Ligação: portas


Normalmente, só tem de garantir que é aberta para comunicação de saída, no computador que aloja programa cliente porta 1433.


Por exemplo, quando o seu programa de cliente está alojado num computador Windows, a Firewall do Windows no anfitrião do permite-lhe abrir a porta 1433:


1. Abra o painel de controlo
2. &gt;Todos os itens do painel de controlo
3. &gt;Firewall do Windows
4. &gt;Definições avançadas
5. &gt;Regras de saída
6. &gt;Ações
7. &gt;Nova regra


Se o seu programa de cliente é alojado numa máquina de virtual Azure (VM), deverá ler:<br/>[Portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


Para obter informações gerais sobre cofiguration de portas e protocolos do endereço IP, consulte o artigo: [firewall de base de dados do SQL Azure](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Ligação: ADO.NET 4.6.1


Se utiliza o seu programa de classes ADO.NET como **SqlConnection** para ligar à base de dados do SQL Azure, recomendamos que utilize .NET Framework versão 4.6.1 ou superior.


ADO.NET 4.6.1:

- Para a base de dados SQL Azure, existe Fiabilidade melhorada quando abre uma ligação utilizando o método de **SqlConnection.Open** . O método **Abrir** agora incorpora melhor mecanismos de repetir esforço na resposta a falhas breves, para determinados erros dentro do período de tempo limite da ligação.
- Suporta o agrupamento de ligações. Isto inclui uma verificação eficiente que o objeto de ligação fornece-o programa está a funcionar.



Quando utiliza um objeto de ligação a partir de um conjunto de ligação, recomendamos que o seu programa de fechar temporariamente a ligação quando não imediatamente utilizá-lo. Voltar a abrir uma ligação não é a forma de criar uma nova ligação é dispendiosa.


Se estiver a utilizar o ADO.NET 4.0 ou versões anteriores, recomendamos que Atualize para a mais recente ADO.NET.

- A partir de Novembro de 2015, pode [Transferir ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnósticos

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnósticos: Testar se podem ligar-se utilitários


Se o programa está a falhar ligar à base de dados do SQL Azure, é uma opção de diagnóstico tentar ligar-se com um programa de utility. Idealmente o utilitário seria ligue utilizando a mesma biblioteca que utiliza o seu programa.


Em qualquer computador Windows, pode experimentar estes utilitários:

- SQL Server Management Studio (ssms.exe), que liga utilizando o ADO.NET.
- SQLCMD.exe, que liga ao utilizar [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Quando estiver ligado, teste se uma breve consulta SQL SELECT funciona.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnósticos: Verificar as portas abertas


Suponha que se suspeitar que tentativas de ligação estão a falhar devido a problemas de porta. No seu computador pode executar um utilitário de relatórios sobre as configurações de porta.


Em Linux os seguintes utilitários poderão ser útil:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Altere o valor de exemplo para ser o seu endereço IP).


No Windows a [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) utilitário poderá ser útil. Eis uma execução de exemplo que consultado a situação porta num servidor de base de dados do Azure SQL e qual foi executado num computador portátil:


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnóstico: Os erros de registo


Por vezes, melhor é diagnosticar um problema intermitente por deteção de um padrão geral sobre dias ou semanas.


O cliente pode ajudar num diagnóstico pelo registo de todos os erros encontra segura. Poderá haver uma correlação as entradas de registo com dados de erros registos de base de dados do SQL Azure propriamente dito internamente.


Biblioteca de Enterprise 6 (EntLib60) oferece classes .NET geridos para ajudar com a funcionalidade de registo:

- [5 - tão fácil como fiquem desativar um registo: utilizar o bloco de aplicação de registo](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnósticos: Examinar registos do sistema de erros


Aqui estão algumas declarações Transact-SQL SELECIONE registos essa consulta de erro e outras informações.


| Consulta de registo | Descrição |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | A vista de [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) oferece informações sobre eventos individuais, incluindo alguns que podem causar erros breves ou falhas de conectividade.<br/><br/>Idealmente pode correlacionar os valores **hora_início** ou **end_time** com informações sobre quando o seu programa de cliente teve problemas.<br/><br/>**Sugestão:** Tem de ligar à base de dados **principal** para executar este. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | A vista de [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) oferece contagens agregadas de tipos de evento, para diagnósticos adicionais.<br/><br/>**Sugestão:** Tem de ligar à base de dados **principal** para executar este. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnóstico: Pesquisa para eventos de problema no registo de base de dados SQL


Pode procurar entradas sobre eventos de problema no registo de base de dados do SQL Azure. Experimente a seguinte instrução SELECT de Transact-SQL na base de dados **principal** :


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Algumas linhas devolvidas a partir do sys.fn_xe_telemetry_blob_target_read_file


Segue-se uma linha devolvida aspeto. Os valores nulos apresentados frequentemente não são nulos em outras linhas.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Biblioteca de Enterprise 6


Biblioteca de Enterprise 6 (EntLib60) é uma estrutura de .NET classes que o ajuda a implementar os clientes robustos dos serviços em nuvem, um dos quais é o serviço de base de dados do Azure SQL. Pode localizar tópicos dedicados a cada área na qual pode dar assistência a EntLib60 visitando primeiro:

- [Biblioteca de Enterprise 6 – de Abril de 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Lógica de repetição para processamento de erros breves é uma área na qual pode dar assistência a EntLib60:

- [4 - perseverance, secreta de todos os Triumphs: utilizar o bloco de aplicação de processamento de breves de falhas](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] O código de origem para EntLib60 está disponível para o público [Transferir](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft não tem planos para fazer as atualizações de funcionalidades ou atualizações de manutenção para EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 classes para erros breves e volte a tentar


As seguintes EntLib60 classes são particularmente úteis para a lógica de repetição. Todos estes estão a ser ou são ainda em espaço de nomes de **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*No espaço de nomes* *Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling* *:*

- **RetryPolicy** classe
 - Método **ExecuteAction**


- **ExponentialBackoff** classe


- **SqlDatabaseTransientErrorDetectionStrategy** classe


- **ReliableSqlConnection** classe
 - Método de **ExecuteCommand**


No espaço de nomes **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** classe

- **NeverTransientErrorDetectionStrategy** classe


Seguem-se ligações para informações sobre EntLib60:

- Gratuito [no livro de transferência: Guia do programador a biblioteca do Microsoft Enterprise, 2º Edition](http://www.microsoft.com/download/details.aspx?id=41145)

- Melhores práticas: [repetir informações gerais](../best-practices-retry-general.md) tem uma excelente aprofundada de lógica de repetição.

- Transferência de NuGet de [Biblioteca Enterprise - processamento de falhas breves bloco de aplicações 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: O bloco de registo


- O bloco de registo é uma solução de altamente flexível e configurável que permite-lhe:
 - Criar e guardar mensagens de registo uma grande variedade de localizações.
 - Categorizar e filtrar mensagens.
 - Recolha informações contextuais que é útil para depuração e rastreio, bem como para auditoria e geral registo requisitos.


- O bloco de registo abstracts a funcionalidade de registo a partir do destino de registo para que o código da aplicação sejam consistente, independentemente da localização e escreva da loja do registo de destino.


Para obter detalhes, consulte: [5 - como fácil como fiquem desativar um registo: utilizar o bloco de aplicação registo](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Código de origem do método EntLib60 IsTransient


Em seguida, a partir de classe **SqlDatabaseTransientErrorDetectionStrategy** , é o código de origem c# para o método de **IsTransient** . O código de origem clarifica os erros foram consideradas como sejam breves e valioso da repetir, a partir de Abril de 2013.

Várias linhas de **//comment** foram removidas desta cópia para realçar legibilidade.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Próximos passos

- Para outros problemas de ligação de base de dados do Azure SQL comuns de resolução de problemas, visite a [resolução de problemas de ligação à base de dados do SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

- [Conexão do SQL Server agrupamento (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*A tentar novamente* é que uma 2.0 Apache licenciado biblioteca repetir uso geral, escrita **Python**, para simplificar a tarefa de adicionar o comportamento de repetir a quase tudo.](https://pypi.python.org/pypi/retrying)
