<properties 
    pageTitle="Utilizar o Gestor de recuperação para corrigir problemas de mapa shard | Microsoft Azure" 
    description="Utilizar a classe de RecoveryManager para resolver problemas com o shard mapas" 
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
    ms.date="05/05/2016" 
    ms.author="ddove"/>

# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Utilizando a classe de RecoveryManager para corrigir os problemas de mapa shard

A classe de [RecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.aspx) fornece a capacidade de facilmente detetar e corrigir eventuais inconsistências verificadas entre o mapa de global shard (GSM) e o mapa de local shard (LSM) no ambiente de uma base de dados sharded de ADO.Net aplicações. 

A GSM e LSM controlar o mapeamento de cada base de dados num ambiente sharded. Ocasionalmente, uma quebra de ocorre entre o GSM e o LSM. Nesse caso, utilize a classe de RecoveryManager para detetar e reparar a quebra.

A classe de RecoveryManager faz parte da [biblioteca de base de dados flexível do cliente](sql-database-elastic-database-client-library.md). 


![Mapa de shard][1]


Para obter definições de termos, consulte o artigo [Glossário de ferramentas da base de dados flexível](sql-database-elastic-scale-glossary.md). Para compreender como o **ShardMapManager** é utilizado para gerir os dados numa solução sharded, consulte o artigo [Shard mapear gestão](sql-database-elastic-scale-shard-map-management.md).


## <a name="why-use-the-recovery-manager"></a>Porquê utilizar o Gestor de recuperação?

Num ambiente sharded da base de dados, existe um inquilino por bases de dados e muitas bases de dados por servidor. Não existem também pode ser muitos servidores no ambiente. Cada base de dados está mapeado no mapa do shard, para que as chamadas podem ser encaminhadas com o servidor correto e a base de dados. Bases de dados são registadas de acordo com uma **chave de sharding**e cada shard é atribuído um **intervalo de valores de chave**. Por exemplo, uma chave de sharding pode representar os nomes de cliente do "D" para "f". O **mapa global shard (GSM)**estão contidos o mapeamento de todos os shards (também conhecidos por bases de dados) e os respetivos intervalos de mapeamento. Cada base de dados também contém um mapa dos intervalos de contidos na shard — isto é conhecido como o **local shard mapear (LSM)**. Quando uma aplicação liga a uma shard, o mapeamento é colocada em cache, com a aplicação para obtenção rápida. O LSM é utilizado para validar dados em cache. 

O GSM e LSM poderão tornam-se dessincronizado pelos seguintes motivos:

1. A eliminação de um shard cujo intervalo acredita-se que já não ser utilizado ou mudar o nome de um shard. Eliminar um resultados shard num **mapeamento shard órfão**. Similary, uma base de dados cujo nome foi mudado pode fazer com que um mapeamento shard órfão. Consoante a intenção da alteração, poderá precisar de shard ser removido ou a localização de shard tem de ser atualizados. Para recuperar uma base de dados eliminado, consulte o artigo [Restaurar uma base de dados para um ponto anterior no tempo, restaurar uma base de dados eliminado ou recuperar a partir de uma falha do Centro de dados](sql-database-troubleshoot-backup-and-restore.md).
2. Ocorre um evento de geo com falha. Para continuar, um tem de atualizar o nome do servidor e o nome da base de dados do Gestor de mapa shard na aplicação de e, em seguida, atualize os detalhes do mapeamento shard para todas as shards num mapa shard. Em caso de uma geo-falha na ligação, deverá essa lógica de recuperação automática dentro do fluxo de trabalho activação pós-falha. Automatizar acções de recuperação permite uma capacidade de gestão frictionless para bases de dados com capacidade de geo e evita ações humanas manuais.
3. Um shard ou a base de dados ShardMapManager é restaurado para uma versão anterior no ponto de data/hora.

Para mais informações sobre as ferramentas do Azure SQL flexível bases de dados, Geo replicação e restaurar, consulte o artigo o seguinte procedimento: 

* [Descrição geral: Na nuvem recuperação de falhas de bases de dados e continuidade empresas com base de dados SQL](sql-database-business-continuity.md) 
* [Introdução às ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md)  
* [Gestão de ShardMap](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Obter RecoveryManager a partir de um ShardMapManager 

O primeiro passo é criar uma instância de RecoveryManager. O [método GetRecoveryManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager.aspx) devolve o Gestor de recuperação para a instância atual do [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) . Para poder endereço eventuais inconsistências verificadas no mapa do shard, primeiro terá de obter RecoveryManager para o mapa shard específico. 

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager(); 

Neste exemplo, o RecoveryManager está inicializado a partir do ShardMapManager. ShardMapManager que contém uma ShardMap também já está inicializado. 

Uma vez que este código da aplicação manipula o mapa de shard propriamente dito, as credenciais utilizadas no método de fábrica do mesmo (no exemplo acima, smmConnectionString) devem ser credenciais que têm permissões de leitura / escrita na base de dados GSM referenciado pela cadeia de ligação. Estas credenciais são normalmente diferentes das credenciais utilizadas para abrir as ligações para o encaminhamento de dependentes de dados. Para mais informações, consulte o artigo [utilizar credenciais no cliente do flexível da base de dados](sql-database-elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Remover um shard do ShardMap depois de um shard é eliminada

O [método de DetachShard](https://msdn.microsoft.com/library/azure/dn842083.aspx) separa o shard determinado do mapa de shard e elimina as mapeamentos associados a shard.  

* O parâmetro de localização é a localização de shard, especificamente o nome de servidor e o nome de base de dados, do shard a ser desligado. 
* O parâmetro shardMapName é o nome do mapa shard. Este procedimento só é necessário quando vários mapas shard são geridos pelo mesmo gestor de mapa de shard. Opcional. 

**Importante**: utilizar esta técnica apenas se tem a certeza de que o intervalo para o mapeamento actualizado está vazio. Métodos acima não verificar dados para o intervalo a ser movido, por isso, é melhor incluir verificações no seu código.

Este exemplo remove shards o mapa shard. 

    rm.DetachShard(s.Location, customerMap); 

O mapa na localização shard GSM antes da eliminação do shard. Porque o shard foi eliminado, é considerada trata intencional e o intervalo de chave sharding já não está a ser utilizado. Se não for o caso, pode executar o tempo de ponto de restaurar. Para recuperar o shard a partir de uma ponto na hora anterior. (Neste caso, reveja a secção abaixo para detetar inconsistências shard.) Para recuperar, consulte o artigo [Restaurar uma base de dados para um ponto anterior no tempo, restaurar uma base de dados eliminado ou recuperar a partir de uma falha do Centro de dados](sql-database-troubleshoot-backup-and-restore.md).

Uma vez que é considerada que a eliminação de base de dados foi intencional, a ação de limpeza administrativo final se eliminar a entrada para shard no Gestor de mapa de shard. Isto impede que a aplicação escrever inadvertidamente informações a um intervalo que não é esperado.

## <a name="to-detect-mapping-differences"></a>Para detetar diferenças de mapeamento 

O [método de DetectMappingDifferences](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences.aspx) seleciona e devolve um dos mapas shard (locais ou globais) como a origem da verdade e reconcilia mapeamentos em ambos os mapas shard (GSM e LSM).

    rm.DetectMappingDifferences(location, shardMapName);

* A *localização* Especifica o nome de servidor e o nome da base de dados. 
* O parâmetro *shardMapName* é o nome do mapa shard. Este procedimento só é necessário se vários mapas shard são geridos pelo mesmo gestor de mapa de shard. Opcional. 

## <a name="to-resolve-mapping-differences"></a>Para resolver diferenças de mapeamento

O [método de ResolveMappingDifferences](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences.aspx) seleciona um dos mapas shard (locais ou globais) como a origem da verdade e reconcilia mapeamentos em ambos os mapas shard (GSM e LSM).

    ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   
* O parâmetro *RecoveryToken* enumera as diferenças na mapeamentos entre o GSM e LSM para o shard específico. 

* A [enumeração MappingDifferenceResolution](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution.aspx) é utilizado para indicar o método para resolver a diferença entre os mapeamentos de shard. 
* É recomendado **MappingDifferenceResolution.KeepShardMapping** no caso do LSM contém o mapeamento correctos e, por conseguinte, o mapeamento do shard deverá ser utilizado. Este é normalmente as maiúsculas/minúsculas trabalho uma activação pós-falha: o shard se encontra num servidor novo. Uma vez que a shard primeiro tem de ser removido de GSM (utilizando o método de RecoveryManager.DetachShard), um mapeamento já não existe no GSM. Por conseguinte, ao LSM deve ser utilizada para restabelecer o mapeamento de shard.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Anexar uma shard para o ShardMap depois de um shard é restaurado 

O [método de AttachShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard.aspx) anexa o shard determinado ao mapa de shard. Em seguida, detetar eventuais inconsistências verificadas mapa de shard e atualiza os mapeamentos para corresponder ao shard no ponto do restauro shard. É considerada que a base de dados é também cujo nome foi mudado para refletir o nome da base de dados original (antes de quando o shard foi restaurada), desde o ponto-in de tempo a restauração predefinições para uma nova base de dados acrescentado com o carimbo de data/hora. 

    rm.AttachShard(location, shardMapName) 

* O parâmetro de *localização* é o nome de servidor e o nome da base de dados, de shard a ser anexado. 

* O parâmetro *shardMapName* é o nome do mapa shard. Este procedimento só é necessário quando vários mapas shard são geridos pelo mesmo gestor de mapa de shard. Opcional. 

Este exemplo adiciona uma shard ao mapa de shard qual foi recentemente restaurado a partir de uma versão anterior no ponto de data/hora. Desde que foi restaurado a shard (nomeadamente o mapeamento shard na LSM) está potencialmente inconsistente com a entrada shard a GSM. Fora do código neste exemplo, o shard foi restaurado e mudar o nome para o nome original da base de dados. Uma vez que este foi restaurado, é considerada que o mapeamento na LSM é o mapeamento fidedigno. 

    rm.AttachShard(s.Location, customerMap); 
    var gs = rm.DetectMappingDifferences(s.Location); 
      foreach (RecoveryToken g in gs) 
       { 
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
       } 

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Actualizar localizações shard após uma geo com falha (restaurar) dos shards

Trabalho um geo-falha na ligação, a base de dados secundária é feita escrever acessível e torna-se a nova base de dados principal. O nome do servidor e potencialmente a base de dados (dependendo da sua configuração), pode ser diferente a partir do principal original. Assim as entradas de mapeamento para shard na GSM e LSM tem de ser corrigidas. Da mesma forma, se a base de dados for restaurado para um nome ou localização diferente, ou para um ponto anterior no tempo, isto poderá causar inconsistências nos mapas shard. O Gestor de mapa Shard trata a distribuição dos ligações abertas para a base de dados correto. Distribuição é baseada os dados no mapa shard e o valor da chave de sharding que é o pedido da aplicação de destino. Depois de um geo-falha na ligação, estas informações têm de ser atualizadas com nome de servidor precisos, nome da base de dados e mapeamento de shard da base de dados recuperado. 

## <a name="best-practices"></a>Melhores práticas

Recuperação e geo com falha são operações normalmente geridas por um administrador de nuvem da aplicação intencionalmente utilizando uma das funcionalidades de continuidade do negócio de bases de dados do Azure SQL. Planeamento de continuidade do negócio requer processos, procedimentos e medidas para se certificar de que as operações de negócio podem continuar sem interrupções. Os métodos de disponíveis como parte da classe RecoveryManager deverá ser utilizado dentro este fluxo de trabalho para garantir que a GSM e LSM são atualizadas com base em recuperação seguimento. Existem 5 passos básicos para corretamente garantir que a GSM e LSM refletem as informações precisas após um evento de activação pós-falha. O código da aplicação para executar estes passos pode ser integrado de fluxo de trabalho e ferramentas existentes. 

1. Obter o RecoveryManager a partir de ShardMapManager. 
2. Desligar o shard antiga do mapa de shard.
3. Anexe a shard novo para o mapa shard, incluindo a nova localização de shard.
4. Deteta inconsistências nas mapeamento entre o GSM e LSM. 
5. Resolva as diferenças entre o GSM e LSM, considerar como o LSM. 

Este exemplo executa os seguintes passos:
1. Remove shards o mapa de Shard que refletir localizações shard antes do evento activação pós-falha.
2. Anexa shards ao mapa de Shard refletir as novas localizações shard (o parâmetro "Configuration.SecondaryServer" é o novo nome de servidor, mas o mesmo nome de base de dados).
3. Obtém os tokens de recuperação por detetar mapeamento as diferenças entre o GSM e LSM para cada shard. 
4. Resolve as inconsistências por considerar como o mapeamento de LSM de cada shard. 

    var shards = smm. GetShards();  foreach (shard g no shards) {se (s.Location.Server = = Configuration.PrimaryServer) {ShardLocation slNew = ShardLocation novo (Configuration.SecondaryServer, s.Location.Database); 
        
          rm.DetachShard(s.Location); 
        
          rm.AttachShard(slNew); 
        
          var gs = rm.DetectMappingDifferences(slNew); 
    
          foreach (RecoveryToken g in gs) 
            { 
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping); 
            } 
        } 
    } 



[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
 
