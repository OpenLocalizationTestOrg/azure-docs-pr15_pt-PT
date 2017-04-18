<properties 
    pageTitle="Portas para além das 1433 para base de dados SQL | Microsoft Azure"
    description="Ligações de cliente do ADO.NET V12 de base de dados do SQL Azure, por vezes, ignoram o proxy e interagem diretamente com a base de dados. Portas que não seja 1433 tornam-se importantes."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="" />


<tags 
    ms.service="sql-database" 
    ms.workload="drivers"
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016"
    ms.author="annemill"/>


# <a name="ports-beyond-1433-for-adonet-45-and-sql-database-v12"></a>Portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados SQL


Este tópico descreve as alterações que V12 de base de dados do SQL Azure dá ao comportamento da ligação de clientes que utilizam o ADO.NET 4,5 ou uma versão posterior.


## <a name="v11-of-sql-database-port-1433"></a>V11 da base de dados SQL: porta 1433


Quando o seu programa de cliente utiliza ADO.NET 4,5 para ligar e de consulta com V11 de base de dados do SQL, a sequência interna é da seguinte forma:


1. ADO.NET tenta ligar à base de dados SQL.

2. ADO.NET utiliza a porta 1433 para ligar a um módulo de software intermédio, e o software intermédio liga à base de dados SQL.

3. Base de dados SQL envia a sua resposta de volta para o software de intermédio, reencaminha a resposta ADO.NET à porta 1433.


**Terminologia:** Descrevemos a sequência anterior por dizer que ADO.NET interage com base de dados SQL utilizando a *rota de proxy*. Se o software sem intermédio foram envolvido, podemos iriam dizem que foi utilizada a *rota direta* .


## <a name="v12-of-sql-database-outside-vs-inside"></a>V12 da base de dados SQL: fora vs no interior


Para ligações de V12, tem Pedimos se o programa cliente é executada *fora* ou *dentro* o limite da nuvem Azure. As subsecções discutir dois cenários comuns.


#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Fora:* Cliente é executada no seu computador de secretária


Porta 1433 é a porta apenas que têm de ser aberta no computador de secretária que aloja a sua aplicação de cliente da base de dados SQL.


#### <a name="inside-client-runs-on-azure"></a>*Dentro:* Cliente é executada no Azure


Quando executa o seu cliente dentro dos limites do Azure na nuvem, utiliza o que pode chamamos uma *rota direta* para interagir com o servidor de base de dados SQL. Depois de uma ligação é estabelecida, ainda mais interações entre o cliente e a base de dados não envolvam nenhum software intermédio proxy.


A sequência é da seguinte forma:


1. ADO.NET 4,5 (ou posterior) inicia uma breve interação com a nuvem Azure e recebe um número de porta dinamicamente identificados.
 - O número da porta dinamicamente identificados está no intervalo de 11000 11999 ou 14000 14999.

2. ADO.NET, em seguida, liga-se para o servidor de base de dados SQL diretamente, com sem software intermédio entre.

3. As consultas são enviadas diretamente para a base de dados e os resultados são devolvidos diretamente para o cliente.


Certifique-se de que a porta intervalos de 11000 11999 e 14000-14999 no computador cliente Azure ficam disponíveis para interações do cliente ADO.NET 4,5 com V12 de base de dados do SQL.

- Em particular, portas no intervalo tem de ser gratuitamente e bloqueadores saídas.

- No seu VM Azure, a **Firewall do Windows com segurança avançada** controla as definições de porta.
 - Pode utilizar a [interface de utilizador da firewall](http://msdn.microsoft.com/library/cc646023.aspx) para adicionar uma regra para a qual especificar o protocolo **TCP** juntamente com um intervalo de porta com a sintaxe como **11000 11999**.


## <a name="version-clarifications"></a>Clarificações de versão


Esta secção clarifica de identificadores que se referem a versões de produto. Lista também algumas pares de versões entre os produtos.


#### <a name="adonet"></a>ADO.NET


- ADO.NET 4.0 suporta o protocolo TDS 7.3, mas não 7.4.
- ADO.NET 4,5 e posterior suporta o protocolo TDS 7.4.


#### <a name="sql-database-v11-and-v12"></a>V11 de base de dados do SQL e V12


As diferenças de ligação de cliente entre V11 de base de dados do SQL e V12 estão realçadas neste tópico.


*Nota:* A instrução Transact-SQL `SELECT @@version;` devolve um valor que começam com um número como '11'. ou '12.' e essas correspondem aos nomes das nossas versão de V11 e V12 para base de dados SQL.


## <a name="related-links"></a>Ligações relacionadas


- ADO.NET 4.6 foi publicada em 20 de Julho de 2015. Anúncio de nascimento de um blogue da equipa do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4,5 foi disponibilizado em 15 de Agosto de 2012. Anúncio de nascimento de um blogue da equipa do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Uma mensagem no blogue sobre ADO.NET 4.5.1 está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Lista de versão do protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)


- [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)


- [Firewall de base de dados SQL Azure](sql-database-firewall-configure.md)


- [Como: Configurar definições da firewall na base de dados SQL](sql-database-configure-firewall-settings.md)

