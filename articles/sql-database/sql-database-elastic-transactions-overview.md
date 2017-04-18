<properties
   pageTitle="Transações distribuídas ao longo de bases de dados da nuvem"
   description="Descrição geral das transações de base de dados flexível com base de dados Azure SQL"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jhubbard"
   editor="torsteng"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="05/27/2016"
   ms.author="torsteng"/>

# <a name="distributed-transactions-across-cloud-databases"></a>Transações distribuídas ao longo de bases de dados da nuvem

As transações de base de dados flexível para a base de dados do SQL Azure (SQL DB) permitem-lhe executar as transações que aparecem em várias bases de dados SQL DB. As transações de base de dados flexível para SQL DB estão disponíveis para aplicações do .NET utilizando o ADO .NET e integram com a experiência familiar programação, utilizando as classes [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Para obter a biblioteca, consulte o artigo [.NET Framework 4.6.1 (Web Installer)](https://www.microsoft.com/download/details.aspx?id=49981).

No local, como um cenário normalmente necessária a executar o Microsoft Distributed Transaction coordenador (MSDTC). Uma vez que MSDTC não está disponível para a aplicação de plataforma-como-a-Service no Azure, a capacidade para coordenar transações distribuídas agora foi diretamente integrada no SQL DB. Aplicações podem ligar a qualquer base de dados do SQL à iniciação transações distribuídas e uma das bases de dados transparente irá coordenar operação distribuída, conforme apresentado na figura seguinte. 

  ![Transações distribuídas com base de dados do SQL Azure utilizando as transações de base de dados flexível ][1]

## <a name="common-scenarios"></a>Cenários comuns

As transações de base de dados flexível para SQL DB permitem que as aplicações efetuar alterações Atómica a dados armazenados em várias diferentes bases de dados do SQL. Pré-visualização do foca-se em experiências de desenvolvimento do lado do cliente no c# e .NET. Para mais tarde é planeada de uma experiência do lado do servidor utilizando o T-SQL.  
As transações de base de dados flexível destina-se os cenários seguintes:

* Aplicações de base de dados com várias no Azure: com este cenário, dados são verticalmente divididos em várias bases de dados SQL DB dessa diferentes tipos de dados se encontram bases de dados diferentes. Algumas operações requerem as alterações aos dados que são mantidos em duas ou mais bases de dados. A aplicação utiliza as transações de base de dados flexível para coordenar as alterações ao longo de bases de dados e certifique-se atomicidade.

* Aplicações de base de dados sharded no Azure: com este cenário, a camada de dados utiliza a [biblioteca de cliente de base de dados flexível](sql-database-elastic-database-client-library.md) ou self sharding para os dados de partições horizontalmente ao longo de muitas bases de dados na base de dados do SQL. Um caso de utilização proeminentes é a necessidade de efetuar alterações atómica para uma aplicação do inquilino com várias sharded quando inquilinos do intervalo de alterações. Pense por exemplo uma transferência de um inquilino para outra, ambas as que reside no bases de dados diferentes. Um segundo caso é sharding extensivamente para acomodar necessidades da capacidade para um inquilino grande que por sua vez normalmente implica que precisa de algumas operações atómica esticar através de várias bases de dados utilizados para ao mesmo inquilino. Um caso de terceiro é atómica atualizações para fazer referência a dados que são replicados em bases de dados. Agora podem ser coordenadas operações Atómica e transaccionadas, ao longo destas linhas em várias bases de dados com a pré-visualização.
As transações de base de dados flexível utilizam consolidação de duas fases para garantir atomicidade da transação através de bases de dados. É uma boa opção para transações que envolvam menos de 100 bases de dados de uma vez dentro de uma única transação. Estes limites não são impostos, mas um devo esperar desempenho e taxas de sucesso para as transações de base de dados flexível para sofrem quando excedem estes limites.


## <a name="installation-and-migration"></a>Instalação e migração

As capacidades para transações de base de dados flexível no SQL DB são fornecidas através de atualizações para as bibliotecas .NET System.Data.dll e System.Transactions.dll. As DLLs Certifique-se de que consolidação de duas fases é utilizada sempre que necessário para se certificar de atomicidade. Para iniciar a programação de aplicações utilizando as transações de base de dados flexível, instale o [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) ou uma versão posterior. Ao ser executada numa versão anterior do .NET framework, as transações irão falhar promover a uma transação distribuída e uma exceção será elevada.

Após a instalação, pode utilizar a transação distribuída APIs no Transactions com ligações ao DB do SQL. Se tiver existentes MSDTC aplicações utilizando estas APIs, basta reconstruir as aplicações existentes para .NET 4.6 depois de instalar o 4.6.1 Framework. Se os seus projetos alvo .NET 4.6, automaticamente utilizará as DLLs atualizadas da nova versão do quadro e distribuída transação chamadas à API em conjunto com ligações para SQL DB agora será bem sucedida.

Lembre-se de que as transações de base de dados flexível não necessitam de instalação do MSDTC. Em vez disso, as transações de base de dados flexível diretamente são geridas pelo e dentro DB do SQL. Significativamente simplifica nuvem cenários uma vez que não é necessária utilizar transações distribuídas com SQL DB uma implementação do MSDTC. 4 da secção explica detalhadamente como implementar o .NET framework necessário juntamente com as suas aplicações na nuvem para Azure e as transações de base de dados flexível.

## <a name="development-experience"></a>Experiência de desenvolvimento

### <a name="multi-database-applications"></a>Base de dados com várias aplicações

O código de exemplo seguinte utiliza a experiência de programação familiar com Transactions .NET. A classe de TransactionScope estabelece uma transação ambiente no .NET. ("Transação ambiente" é aquele que encontra-se no tópico atual.) Todas as ligações abertas dentro o TransactionScope participam na operação. Se participam de bases de dados diferentes, a transação é automaticamente elevada a uma transação distribuída. O resultado da transação é controlado pelo definir o âmbito para concluir para indicar uma consolidação.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Aplicações de base de dados sharded
 
As transações de base de dados flexível para SQL DB também suportam coordenar transações distribuídas que utilizar o método de OpenConnectionForKey da biblioteca de cliente da base de dados flexível para abrir as ligações para um dados com escala saída camada. Considere casos onde precisa de garante consistência transaccional para alterações através de vários valores de chave sharding diferente. Ligações para shards que aloja os valores da chave sharding diferentes são controladas utilizando OpenConnectionForKey. No caso de geral, as ligações podem ser shards diferentes dessa garantindo que garantias transaccionais requer uma transação distribuída. O código de exemplo seguinte ilustra esta abordagem. Parte do princípio de que uma variável denominada shardmap é utilizada para representar um mapa de shard a partir da biblioteca de cliente da base de dados flexível:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }
        
        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Instalação do .NET para serviços em nuvem Azure

Azure fornece várias ofertas para aplicações do .NET anfitrião. Uma comparação entre as ofertas de diferentes está disponível no [Azure Service de aplicação, serviços em nuvem e máquinas virtuais comparação](../app-service-web/choose-web-site-cloud-service-vm.md). Se o convidado SO da oferta for menor que .NET 4.6.1 necessários para o transações flexível, terá de atualizar o sistema operativo convidado para 4.6.1. 

Para os serviços de aplicação do Azure, actualizações para o convidado SO atualmente não são suportadas. Para Azure máquinas virtuais simplesmente inicie sessão na VM e execute o installer para o .NET framework mais recente. Para serviços em nuvem Azure, tem de incluir a instalação de uma versão mais recente do .NET para as tarefas de arranque da sua implementação. Os conceitos e os passos encontram-se documentados no [.NET instalar numa função de serviço na nuvem](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Tenha em atenção que o Windows installer para .NET 4.6.1 poderá requerem mais armazenamento temporário durante o processo de bootstrapping no serviços em nuvem Azure que o programa de instalação para .NET 4.6. Para assegurar uma instalação com êxito, é necessário aumentar o armazenamento temporário do seu serviço de nuvem Azure no seu ficheiro ServiceDefinition.csdef na secção LocalResources e as definições de ambiente da sua tarefa de arranque, conforme mostrado no seguinte exemplo:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
    
## <a name="transactions-across-multiple-servers"></a>Transações por vários servidores

As transações de base de dados flexível são suportadas nos servidores lógicos diferentes do base de dados do SQL Azure. Quando as transações atravessam limites do servidor lógicos, os servidores participantes primeiro tem de ser introduzidos uma relação de comunicação comum. Assim que tiver sido estabelecida a relação de comunicação, quaisquer bases de dados em qualquer um dos dois servidores podem participar em transações flexível com bases de dados a partir de outro servidor. Com as transações que abrangem mais do que dois servidores lógicos, uma relação de comunicação tem de estar no local para qualquer par dos servidores lógicos.

Utilize os seguintes cmdlets do PowerShell para gerir relações de comunicação entre servidores para as transações de base de dados flexível:

* **Novo AzureRmSqlServerCommunicationLink**: Utilize este cmdlet para criar uma nova relação de comunicação entre dois servidores lógicos no Azure SQL DB. A relação é simétrica o que significa que ambos os servidores podem iniciar transações com o outro servidor.
* **Get-AzureRmSqlServerCommunicationLink**: Utilize este cmdlet para obter as relações de comunicação existentes e respectivas propriedades.
* **Remover AzureRmSqlServerCommunicationLink**: Utilize este cmdlet para remover uma relação de comunicação existente. 


## <a name="monitoring-transaction-status"></a>Monitorizar o estado da transação

Utilize vistas dinâmicas de gestão (DMVs) no SQL DB para monitorizar estado e progresso das suas transações de base de dados flexível em curso. Todos os DMVs relacionados com operações são relevantes para distribuído transações na base de dados do SQL. Pode encontrar na lista correspondente de DMVs aqui: [da transação dinâmico gestão de vistas e funções (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Estes DMVs são particularmente úteis:

* **sys.dm\_SR Silva\_ativo\_transações**: listas transações atualmente ativas e o respetivo estado. A coluna UOW (unidade de trabalho) pode identificar as transações de subordinados de diferentes que pertencem, para a mesma transação distribuída. Todas as operações dentro da mesma transação distribuída conter o mesmo valor UOW. Consulte a [documentação das DMV](https://msdn.microsoft.com/library/ms174302.aspx) para obter mais detalhes.
* **sys.dm\_SR Silva\_base de dados\_transações**: fornece informações adicionais sobre as transações, como o posicionamento da transação no registo de. Consulte a [documentação das DMV](https://msdn.microsoft.com/library/ms186957.aspx) para obter mais detalhes.
* **sys.dm\_SR Silva\_bloqueios**: fornece informações sobre os bloqueios que estão atualmente mantidos por transações em curso. Consulte a [documentação das DMV](https://msdn.microsoft.com/library/ms190345.aspx) para obter mais detalhes.

## <a name="limitations"></a>Limitações 

As seguintes limitações atualmente aplicam-se para as transações de base de dados flexível na base de dados do SQL:

* Apenas as transações através de bases de dados no SQL DB são suportadas. Outros [X / abrir XA](https://en.wikipedia.org/wiki/X/Open_XA) fornecedores de recursos e bases de dados fora do SQL DB não podem participar em transações de base de dados flexível. Isto significa que as transações de base de dados flexível não possa esticar ao longo no local do SQL Server e bases de dados do SQL Azure. Para obter transações distribuídas no local, continue a utilizar MSDTC. 
* Transações a partir de uma aplicação do .NET apenas coordenado de cliente são suportadas. Suporte do lado do servidor para T-SQL, tal como começar transação distribuído é planeada, mas ainda não está disponível. 
* Apenas as bases de dados no Azure SQL DB V12 são suportadas.
* Transações em serviços WCF não são suportadas. Por exemplo, tem um método de serviço WCF que executa uma transação. Envolver a chamada dentro de um âmbito da transação falhará como um [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="additional-resources"></a>Recursos adicionais

Ainda não utilizar capacidades flexível bases de dados para aplicações do Azure? Veja os nossos [Mapa de documentação](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/). Para as questões, consulte comunique-no [Fórum de base de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para pedidos de funcionalidades, consulte adicioná-los para o [Fórum de comentários de base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



