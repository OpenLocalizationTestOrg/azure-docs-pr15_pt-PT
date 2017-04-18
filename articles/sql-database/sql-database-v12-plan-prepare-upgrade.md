<properties
    pageTitle="Planear a sua atualização para o V12 de base de dados do SQL | Microsoft Azure"
    description="Descreve as preparações e limitações envolvidos na atualizar para a versão V12 do Azure SQL da base de dados."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genemi"/>


# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Planear e preparar-se para atualizar para o V12 de base de dados SQL


Este tópico descreve o planeamento e preparações, tem de executar para atualizar as bases de dados do Azure SQL a partir de versão V11 para V12.


Um novo [Azure Portal](https://portal.azure.com/) está disponível para suportar a sua atualização para o V12.


A tabela seguinte lista outros tópicos de ajuda para V12.


| Título e de ligação | Descrição de conteúdo |
| :--- | :--- |
| [O que há de novo no V12 de base de dados SQL](sql-database-v12-whats-new.md) | Descreve os detalhes de como o V12 dá Azure SQL da base de dados mais perto ao paridade completa com o Microsoft SQL Server. |
| [Criar uma base de dados no V12 de base de dados SQL](sql-database-get-started.md) | Descreve como pode criar uma nova base de dados do Azure SQL na versão V12. Descreve várias opções para além de apenas uma base de dados vazia. |


## <a name="plan-ahead"></a>Planear com antecedência


As seguintes subsecções descrevem aprendizagem e terá de resolver antes de um ações por defeito até atualizar a sua base de dados do Azure SQL para V12 tomada de decisões.

### <a name="version-clarification"></a>Clarificar de versão


Este documento refere-se a atualização da base de dados do Microsoft Azure SQL da versão V11 para V12. Mais formal os números são perto seguintes dois valores, à medida que comunicado pela instrução Transact-SQL **SELECIONE @@version; ** :


- 12.0.2000.8 *(ou um pouco mais elevado, V12)*
- 11.0.9228.18 *(V11)*
 - Por vezes V11 também era designado por SAWA v2.

### <a name="service-tier-planning"></a>Camadas de planeamento do serviço


Começando V12, base de dados do SQL Azure irá suportar as camadas do serviço com o nome Basic, padrão e Premium. A camada de serviço Web e empresas não é suportada no V12. Por isso, se planeia atualizar a sua base de dados do Azure SQL que se encontra atualmente a camada de serviço Web ou de negócio, tem de decidir o que deve ser sua nova camada de serviços.


Para obter informações detalhadas sobre as camadas de serviço Basic, padrão e Premium, consulte:

- [Camadas de serviços de base de dados SQL](sql-database-service-tiers.md)
- [Atualizar bases de dados do SQL da base de dados Web/empresas para o novo serviço camadas](sql-database-upgrade-server-portal.md)



### <a name="review-the-geo-replication-configuration"></a>Rever a configuração de Geo replicação


Se a base de dados do Azure SQL estiver configurado para a replicação Geo, deve sua configuração atual do documento e parar replicação Geo, antes de começar as ações de preparação para a atualização. Após a atualização for concluída terá de reconfigurar a base de dados para a replicação Geo.


A estratégia é manter a origem intactos e testar numa cópia da base de dados.


## <a name="preparation-actions"></a>Ações de preparação


Depois de ter concluído o planeamento, pode efetuar os passos de ação descritos nas seguintes subsecções para se preparar para a fase final de atualização.


Descrições detalhadas da fase final de atualização estão disponíveis nos tópicos de ajuda que estão ligadas a na parte superior deste tópico de ajuda.


### <a name="service-tier-actions"></a>Ações de camada de serviço


Os preços camada do serviço Web e empresas não é suportado no V12.


Se a base de dados do SQL Azure de V11 for uma base de dados Web ou de negócio, o processo de atualização oferece mudar a sua base de dados para uma camada suportada. A atualização recomenda uma camada que se adequa o histórico de carga de trabalho da base de dados. No entanto, pode escolher qualquer camada suportada que goste.


Pode reduzir os passos necessários durante a atualização ao mudar a sua base de dados V11 afastando-o a camada de Web e empresas antes de começar a atualização. Pode fazê-lo ao utilizar o novo [Azure Portal](https://portal.azure.com/).


Se tiver a certeza de qual camada de serviços para mudar para, o nível de S2 da camada padrão poderá ser uma opção de inicial cuidada. Qualquer camada menor terão menos recursos do que a camada Web e empresas tinha.


### <a name="suspend-geo-replication-during-upgrade"></a>Suspender Geo replicação durante a atualização


Não é possível executar a atualização para V12 se Geo replicação está ativa na sua base de dados. Primeiro tem de reconfigurar a base de dados para parar de utilizar a replicação Geo.


Após a atualização for concluída pode configurar a sua base de dados para utilizar novamente Geo replicação.


### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Portas abertas numa VM Azure para conectividade de cliente


Se o programa cliente liga-se ao V12 de base de dados do SQL enquanto o cliente é executada numa máquina virtual Azure (VM), terá de abrir os seguintes intervalos de porta na VM:

- 11000 11999
- 14000 14999


Clique [aqui](sql-database-develop-direct-route-ports-adonet-v12.md) para obter detalhes sobre as portas para V12 de base de dados do SQL. As portas são necessários ao melhorias no desempenho no V12 de base de dados do SQL.


##<a id="limitations"></a>Limitações durante e após a atualização para V12


### <a name="portals-for-v12"></a>Portais para V12


Existem três portais para Azure e cada tem capacidades diferentes relativamente ao V12 de base de dados do SQL.


- [http://portal.Azure.com/](https://portal.azure.com/)<br/>Este Portal Azure são as novidades e ainda é Estado de pré-visualização. Este portal ainda não está na completo das versões geral disponibilidade (DG). Este portal:
 - Pode gerir V12 e servidor da sua base de dados.
 - Pode atualizar a sua base de dados V11 para V12.


- [http://Manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Eventualmente poderá ser descontinuada este Portal clássica Azure. Este portal:
 - Pode gerir V12 e servidor da sua base de dados.
 - Pode *não* atualização do seu V11 da base de dados para V12.


- (http://*NomeSeuServidor*. database.windows.net)<br/>Base de dados do Azure SQL Portal clássico:
 - Pode*não* gerir V12 servidores.


Aconselhamos para ligar à sua bases de dados do Azure SQL com Visual Studio 2015 (VS2015). VS2015 podem ser utilizados para tarefas como as seguintes:


- Para executar uma instrução Transact-SQL.
- Para estruturar um esquema.
- Desenvolver uma base de dados online ou offline.


Ou em vez disso, gratuitamente pode transferir o [Visual Studio Comunidade 2015](https://www.visualstudio.com/vs/community/), que é uma versão completa do VS2015.


No mais antigos Azure clássica Portal do, na página de base de dados, pode clicar em **Abrir no Visual Studio** à iniciação VS2015 no seu computador como se ligar à sua base de dados do SQL Azure.


Para outra alternativa, pode utilizar o SQL Server Management Studio (SSMS) 2014 com [CU6](http://support.microsoft.com/kb/3031047/) para ligar à base de dados do SQL Azure. Obter mais detalhes são esta mensagem de blogue:<br/>[Ferramenta de cliente atualizações de base de dados do SQL Azure](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


> [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="limitation-during-upgrade-to-v12"></a>Limitação *durante a* atualização para o V12


A base de dados V11 permanece disponível para acesso aos dados durante a atualização para V12. Ainda existem algumas limitações a ter em conta.


| Limitação | Descrição |
| :--- | :--- |
| Duração da atualização | A duração da atualização depende do tamanho, a edição e o número de bases de dados no servidor. O processo de atualização pode executar horas para dias para servidores especialmente para servidores que tem as bases de dados:<br/><br/>*Com mais de 50 GB, ou<br/> * Na camada serviço que não sejam premium<br/><br/>Criação de novas bases de dados no servidor durante a atualização, também pode aumentar a duração de atualização. |
| Sem replicação Geo | Geo replicação não é suportada num servidor V12 que está envolvido atualmente numa atualização da V11. |
| Base de dados está temporariamente indisponível na fase final da atualização para V12 | As bases de dados que pertencem ao seu servidor V11 permanecem disponíveis durante o processo de atualização. No entanto, a ligação ao servidor e as bases de dados está temporariamente indisponível na fase final, quando o parâmetro ao longo do começa a partir do V11 para o V12 pronto.<br/><br/>O parâmetro ao longo período pode variar entre 40 segundos para 5 minutos. Para a maioria dos servidores, mudar sobre é esperado para concluir no prazo de 90 segundos. Aumenta mudar ao longo do tempo para servidores que têm um grande número de bases de dados ou, quando as bases de dados têm escrita grossa cargas de trabalho. |


### <a name="limitation-after-upgrade-to-v12"></a>Limitação de *depois de* atualizar para o V12


| Limitação | Descrição |
| :--- | :--- |
| Não pode reverter para V11 | Após um atualização no local, o resultado não pode ser revertido ou revertido. |
| Camadas de Web ou para empresas | Assim que a atualização é iniciado, o servidor para a nova base de dados V12 pode já não reconhece ou aceitar a camada de serviço Web ou para empresas. |



### <a name="export-and-import-after-upgrade-to-v12"></a>Exportar e importar *após a* atualização para V12


Pode exportar ou importar uma base de dados V12 utilizando o [Portal do Azure](https://portal.azure.com/). Ou pode exportar ou importar utilizando qualquer uma das seguintes ferramentas:


- SQL Server Management Studio (SSMS)
- Visual Studio 2015
- Arquitetura de aplicação camada de dados (DacFx)


No entanto, utilizar as ferramentas, tem primeiro de ter ou instalar as atualizações mais recentes para se certificar de que suportam as novas funcionalidades de V12:


- [Atualização cumulativa 6 para SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Atualização de Fevereiro de 2015 para o SQL Server da base de dados ferramenta no Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Fevereiro de 2015 camada de dados de arquitetura de aplicação (DacFx) para V12 de base de dados do Azure SQL](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] As ligações de ferramenta anteriores foram atualizadas 2 de Março de 2015 ou depois. Recomendamos que utilize estas atualizações mais recentes destas ferramentas.


#### <a name="automated-export"></a>Exportar automatizado


Exportar automatizado continua a ser disponíveis como pré-visualização.  Não cliente ferramenta são necessárias actualizações ao utilizar automatizado exportar.  Se escolher tirar o ficheiro resultante e importar para um servidor no local, as atualizações de ferramentas listadas acima são necessários para importar com êxito.


### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Restaurar para V12 de uma base de dados eliminado V11

Cenário que se segue explica que pode ser restaurado uma base de dados eliminado V11 Azure SQL para um servidor de base de dados do V12 Azure SQL.

1. Suponha que tem um servidor de base de dados do V11 Azure SQL. <br/> No servidor tiver uma base de dados na camada de serviço Web-e-Business obsoleta.
2. Eliminar a base de dados.
3. Actualizar o servidor para V12.
4. Seguinte que restaurar a base de dados para o servidor. <br/> A base de dados, assim, é atualizado para o V12, ao nível S0 da camada serviço padrão.
5. Pode mudar a base de dados para qualquer camada de serviço suportado, se S0 não for a sua preferência.


### <a name="powershell-cmdlets"></a>Cmdlets do PowerShell


Os cmdlets do PowerShell estão disponíveis para iniciar, parar ou monitorizar uma atualização para V12 de base de dados do SQL Azure a partir do V11 ou qualquer outra versão do tipo de ficheiro V12.

- [Atualização para o SQL V12 de base de dados através do PowerShell](sql-database-upgrade-server-powershell.md)

Para a documentação de referência sobre estes cmdlets do PowerShell, consulte:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Iniciar AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Parar AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Significa que o cmdlet parar Cancelar, não coloque o cursor. Não existe nenhuma forma de retomar uma atualização, que não seja o iniciar novamente a partir do início. O cmdlet parar limpa e disponibiliza todos os recursos adequados.


## <a name="failure-resolution"></a>Resolução de falha


Se a atualização falhar por qualquer motivo ímpar, a base de dados V11 permanece ativa e disponíveis como normal.


## <a name="related-links"></a>Ligações relacionadas


- [Funcionalidades de pré-visualização](https://azure.microsoft.com/services/preview/) do Microsoft Azure


<!--Anchors-->
[Subheading 1]: #subheading-1
