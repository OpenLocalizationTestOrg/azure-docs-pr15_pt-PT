< propriedades
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Atualizar uma aplicação para utilizar a biblioteca de cliente flexível da base de dados mais recente

Estão disponíveis através de NuGetand da interface do Gestor de NuGetPackage no Visual Studio novas versões da [biblioteca de base de dados flexível do cliente](sql-database-elastic-database-client-library.md) . Atualiza os contêm correções de erros e suportam para novas capacidades da biblioteca de cliente.

**Para a versão mais recente:** Aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Recriar a sua aplicação com a nova biblioteca, bem como alterar os seus metadados de Gestor de mapa Shard existente armazenados no seu bases de dados do SQL Azure para suportar novas funcionalidades.

Executar estes passos por ordem assegura que antigo versões da biblioteca de cliente já não estão presentes no seu ambiente quando são actualizados objectos de metadados, que significa que objetos de metadados da versão antigo não serão criados após atualização.   

## <a name="upgrade-steps"></a>Passos de actualização

**1. Actualize as suas aplicações.** No Visual Studio, transferir e fazer referência a versão mais recente da biblioteca de cliente para todos os seus projetos de desenvolvimento que utilizam a biblioteca; em seguida, reconstruir e implementar. 

 * Na sua solução do Visual Studio, selecione **Ferramentas** --> **Gestor de pacotes NuGet** -->  **Gerir pacotes de NuGet para solução**. 
 * (Visual Studio 2013) No painel da esquerda, selecione **as atualizações**e, em seguida, selecione o botão **Atualizar** no **Azure SQL da base de dados flexível escala a biblioteca do cliente** que é apresentada na janela do pacote.
 * (Visual Studio 2015) Defina a caixa de filtro para **Atualizar disponíveis**. Selecione o pacote para atualizar e clique no botão **Atualizar** .
    
 
 * Criar e implementar. 

**2. Atualize os scripts.** Se estiver a utilizar scripts de **PowerShell** para gerir shards, [Transferir a nova versão biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copiá-la para o diretório a partir do qual executar scripts. 

**3. Atualize o seu serviço de divisão em série.** Se utilizar a ferramenta de intercalação de divisão da base de dados flexível para reorganizar os dados sharded, [Transferir e implementar a versão mais recente da ferramenta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detalhadas passos de atualização para o serviço pode ser encontrado [aqui](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. atualizar as bases de dados do Gestor de mapa Shard**. Atualize os metadados do suporte do seu mapas Shard na base de dados do SQL Azure.  Existem duas formas que pode efetuar esta ação, utilizando o PowerShell ou c#. Ambas as opções são apresentadas abaixo.

***Opção 1: Atualizar metadados através do PowerShell***

1. Transferir o utilitário mais recente da linha de comandos para NuGet do [aqui](http://nuget.org/nuget.exe) e guardar para uma pasta. 

2. Abra uma linha de comandos, navegue para a mesma pasta e o comando de problema:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Navegue para a subpasta que contém a nova versão DLL cliente que apenas transferiu, por exemplo:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. Transferir o cliente de base de dados flexível atualização scriptlet a partir do [Centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e guardá-lo para a mesma pasta que contém a DLL.

5. A partir dessa pasta, executar "PowerShell.\upgrade.ps1" a partir da linha de comandos e siga os pedidos.
 
***Opção 2: Atualizar metadados utilizando c#***

Em alternativa, crie uma aplicação do Visual Studio que abre a sua ShardMapManager, itera sobre todos os shards e executa a atualização de metadados ao contactar o suporte os métodos [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) e [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) tal como no exemplo: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Estes técnicas para metadados actualizações podem ser aplicadas várias vezes sem danos. Por exemplo, se uma versão mais antiga do cliente inadvertidamente cria uma shard depois de já ter actualizado, pode executar atualização novamente ao longo de todos os shards para se certificar de que a versão mais recente de metadados está presente em toda a sua infraestrutura. 

**Nota:**  Novas versões da biblioteca de cliente publicado até à data continuar a trabalhar com versões anteriores dos metadados Shard mapa gestor no Azure SQL DB e vice versa.   No entanto, para tirar partido de algumas das novas funcionalidades no cliente do mais recente, precisa de metadados ser atualizada.   Tenha em atenção que actualizações de metadados irão não vai afetar quaisquer dados de utilizador ou dados específicos da aplicação, apenas objectos criado e utilizado pelo Gestor de mapa Shard.  E as aplicações continuam a funcionar através do sequência de actualização descrita acima. 

## <a name="elastic-database-client-version-history"></a>Histórico de versões do cliente de base de dados flexível 

Histórico de versões, aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 