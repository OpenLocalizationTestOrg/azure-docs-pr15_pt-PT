<properties
   pageTitle="Migrar o seu armazém de dados SQL Azure existente para o armazenamento de premium | Microsoft Azure"
   description="Instruções para migrar uma armazém de dados SQL existente para o armazenamento de premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# <a name="migration-to-premium-storage-details"></a>Migração para Premium armazenamento detalhes
Armazém de dados SQL recentemente introduzida [Armazenamento de Premium para maior previsibilidade de desempenho][].  Vamos agora está prontos migrar armazém de dados existente neste momento, em armazenamento padrão para o armazenamento de Premium.  Continue a ler para obter mais detalhes sobre como e quando ocorrem migrações automáticas e como migrar personalizada se preferir para controlar quando ocorre o tempo de inatividade.

Se tiver mais do que um armazém de dados, utilize a [agenda de migração automática][] abaixo para determinar quando também serão migrado.

## <a name="determine-storage-type"></a>Determinar o tipo de armazenamento
Se tiver criado uma DW antes das datas abaixo, está a utilizar atualmente armazenamento padrão.  Cada Data Warehouse armazenamento padrão que está sujeito a migração automática tem um aviso no topo da pá armazém de dados no [Portal do Azure][] que diz "*futuras de uma atualização para o armazenamento de premium necessitará de uma falha.  Saiba mais ->*. "

| **Região**          | **DW criado antes da data**   |
| :------------------ | :-------------------------------- |
| Leste Austrália      | Armazenamento de Premium ainda não está disponível |
| Sudeste Austrália | 5 de Agosto de 2016                    |
| Sul do Brasil        | 5 de Agosto de 2016                    |
| Canadá Central      | 25 de Maio de 2016                      |
| Leste Canadá         | 26 de Maio de 2016                      |
| Central (EUA)          | 26 de Maio de 2016                      |
| China Nordeste          | Armazenamento de Premium ainda não está disponível |
| América do Norte China         | Armazenamento de Premium ainda não está disponível |
| Este asiático           | 25 de Maio de 2016                      |
| Leste dos EUA             | 26 de Maio de 2016                      |
| Texto da Ásia US2            | 27 de Maio de 2016                      |
| Índia Central       | 27 de Maio de 2016                      |
| Sul Índia         | 26 de Maio de 2016                      |
| Oeste Índia          | Armazenamento de Premium ainda não está disponível |
| Japão leste          | 5 de Agosto de 2016                    |
| Japão oeste          | Armazenamento de Premium ainda não está disponível |
| América do Norte Central (EUA)    | Armazenamento de Premium ainda não está disponível |
| Europa Norte        | 5 de Agosto de 2016                    |
| Sul Central (EUA)    | 27 de Maio de 2016                      |
| Sudeste asiático      | 24 de Maio de 2016                      |
| Europa Ocidental         | 25 de Maio de 2016                      |
| Oeste Central (EUA)     | 26 de Agosto de 2016                   |
| Ocidental dos e.u.a.             | 26 de Maio de 2016                      |
| US2 Ocidental            | 26 de Agosto de 2016                   |

## <a name="automatic-migration-details"></a>Detalhes da migração automáticas
Por predefinição, podemos irá migrar a base de dados por si durante 6 pm e 6: 00 na hora do seu região local durante a [agenda de migração automática][] abaixo.  Seu armazém de dados existente serão inutilizável durante a migração.  Vamos estimar que a migração irá demorar cerca uma hora por TB de armazenamento por armazém de dados.  Também irá assegurar a que não são cobrados durante qualquer parte da migração automática.

> [AZURE.NOTE] Não conseguir utilizar o seu armazém de dados existente durante a migração.  Assim que a migração estiver concluída, o seu armazém de dados serão novamente online.

Os detalhes do abaixo são os passos que a Microsoft está a demorar em seu nome para concluir a migração e não requer qualquer participação da sua parte.  Neste exemplo, imagine que seu DW existente no armazenamento padrão atualmente chama-se "MyDW."

1.  Microsoft muda o nome "MyDW" para "MyDW_DO_NOT_USE_ [carimbo]"
2.  Microsoft interrompe "MyDW_DO_NOT_USE_ [carimbo]".  Durante este período de tempo, é disponibilizada uma cópia de segurança.  Poderá ver várias pausa/currículos se podemos deparar-se com problemas durante este processo.
3.  A Microsoft cria uma nova DW denominada "MyDW" Premium armazenamento da cópia de segurança tomada no passo 2.  "MyDW" não serão apresentados até após o restauro está concluído.
4.  Uma vez concluída a restaurar, devolve "MyDW" para o mesmo DWUs e em pausa ou ativo Estado em que estava antes da migração.
5.  Assim que a migração estiver concluída, o Microsoft elimina "MyDW_DO_NOT_USE_ [carimbo]"
    
> [AZURE.NOTE] Estas definições não utilizava como parte da migração:
> 
>   -  Auditoria ao nível da base de dados tem de voltar a ser activado
>   -  As regras da firewall ao nível de **base de dados** tem de ser foi novamente adicionado.  As regras da firewall ao nível do **servidor** não são ser afetada.

### <a name="automatic-migration-schedule"></a>Agenda de migração automática
Migrações automáticas ocorrerem a partir de 6 pm – 6: 00 (hora local por região) durante a agenda de indisponibilidade seguinte.

| **Região**          | **Data de início estimado**     | **Data de fim estimada**       |
| :------------------ | :--------------------------- | :--------------------------- |
| Leste Austrália      | Não foi determinado ainda           | Não foi determinado ainda           |
| Sudeste Austrália | 10 de Agosto de 2016              | 24 de Agosto de 2016              |
| Sul do Brasil        | 10 de Agosto de 2016              | 24 de Agosto de 2016              |
| Canadá Central      | 23 de Junho de 2016                | 1 de Julho de 2016                 |
| Leste Canadá         | 23 de Junho de 2016                | 1 de Julho de 2016                 |
| Central (EUA)          | 23 de Junho de 2016                | 4 de Julho de 2016                 |
| Leste China          | Não foi determinado ainda           | Não foi determinado ainda           |
| América do Norte China         | Não foi determinado ainda           | Não foi determinado ainda           |
| Este asiático           | 23 de Junho de 2016                | 1 de Julho de 2016                 |
| Leste dos EUA             | 23 de Junho de 2016                | 11 de Julho de 2016                |
| Texto da Ásia US2            | 23 de Junho de 2016                | 8 de Julho de 2016                 |
| Índia Central       | 23 de Junho de 2016                | 1 de Julho de 2016                 |
| Sul Índia         | 23 de Junho de 2016                | 1 de Julho de 2016                 |
| Oeste Índia          | Não foi determinado ainda           | Não foi determinado ainda           |
| Japão leste          | 10 de Agosto de 2016              | 24 de Agosto de 2016              |
| Japão oeste          | Não foi determinado ainda           | Não foi determinado ainda           |
| América do Norte Central (EUA)    | Não foi determinado ainda           | Não foi determinado ainda           |
| Europa Norte        | 10 de Agosto de 2016              | 31 de Agosto de 2016              |
| Sul Central (EUA)    | 23 de Junho de 2016                | 2 de Julho de 2016                 |
| Sudeste asiático      | 23 de Junho de 2016                | 1 de Julho de 2016                 |
| Europa Ocidental         | 23 de Junho de 2016                | 8 de Julho de 2016                 |
| Oeste Central (EUA)     | 14 de Agosto de 2016              | 31 de Agosto de 2016              |
| Ocidental dos e.u.a.             | 23 de Junho de 2016                | 7 de Julho de 2016                 |
| US2 Ocidental            | 14 de Agosto de 2016              | 31 de Agosto de 2016              |

## <a name="self-migration-to-premium-storage"></a>Migração Self ao armazenamento de Premium
Se pretender para controlar quando o seu tempo de inatividade irá ocorrer, pode utilizar os seguintes passos para migrar uma armazém de dados existente no armazenamento padrão para o armazenamento de Premium.  Se optar por migrar personalizada, tem de concluir a migração Self antes de começa a migração automática nesse região para evitar os riscos da migração automática a causar um conflito (consulte a [agenda de migração automática][]).

### <a name="self-migration-instructions"></a>Instruções de migração Self
Se pretender para controlar o tempo de inatividade, personalizada pode migrar o seu armazém de dados utilizando a cópia de segurança/restauro.  Parte da restauro da migração é suposta demorar cerca uma hora por TB de armazenamento por DW.  Se quiser manter o mesmo nome após a migração estiver concluída, siga os passos para obter [os passos para mudar o nome durante a migração][]. 

1.  [Colocar em pausa][] seu DW que tenha uma cópia de segurança automática
2.  [Restaurar][] a partir do seu instantâneo mais recente
3.  Elimine o DW existente no armazenamento padrão. **Se não conseguirem efetuar este passo, será cobrado para ambas as DWs.**

> [AZURE.NOTE] Estas definições não utilizava como parte da migração:
> 
>   -  Auditoria ao nível da base de dados tem de voltar a ser activado
>   -  As regras da firewall ao nível de **base de dados** tem de ser foi novamente adicionado.  As regras da firewall ao nível do **servidor** não são ser afetada.

#### <a name="optional-steps-to-rename-during-migration"></a>Opcional: passos para mudar o nome durante a migração 
Duas bases de dados no mesmo servidor lógico não podem ter o mesmo nome. Armazém de dados SQL suporta agora a capacidade de mudar o nome de um DW.

Neste exemplo, imagine que seu DW existente no armazenamento padrão atualmente chama-se "MyDW."

1.  Mudar o nome "MyDW" utilizando o comando de alterar a base de dados que se segue para algo como "MyDW_BeforeMigration."  Este comando elimina se todas as operações existentes e tem de ser feito na base de dados principal para ser concluída com êxito.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.  [Colocar em pausa][] "MyDW_BeforeMigration"-leva-o até uma cópia de segurança automática
3.  [Restaurar][] a partir da sua mais recente do instantâneo uma nova base de dados com o nome que utilizou para ter (ex: "MyDW")
4.  Elimine "MyDW_BeforeMigration".  **Se não conseguirem efetuar este passo, será cobrado para ambas as DWs.**

> [AZURE.NOTE] Estas definições não utilizava como parte da migração:
> 
>   -  Auditoria ao nível da base de dados tem de voltar a ser activado
>   -  As regras da firewall ao nível de **base de dados** tem de ser foi novamente adicionado.  As regras da firewall ao nível do **servidor** não são ser afetada.

## <a name="next-steps"></a>Próximos passos
Com a alteração ao armazenamento de Premium, podemos tem também foi aumentado o número de ficheiros de BLOBs de base de dados na arquitetura do seu armazém de dados subjacente.  Para maximizar os benefícios de desempenho desta alteração, recomendamos que se recompilar os índices de Columnstore agrupadas utilizando o seguinte script.  O script abaixo () funciona por forçar alguns dos seus dados existentes para os blobs adicionais.  Não se tomar nenhuma ação, os dados naturalmente redistribuir ao longo do tempo como carregar mais dados em tabelas seu armazém de dados.

**Pré-requisitos:**

1.  Armazém de dados deverá ser possível executar com 1.000 DWUs ou superior (consulte o artigo [power cluster de escala][])
2.  Executar o script de utilizador deve ser na [função mediumrc][] ou superior
    1.  Para adicionar um utilizador a esta função, execute o seguinte: 
        1.  ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Se encontrar problemas com o seu armazém de dados, [criar uma bilhetes de suporte][] e referência "Armazenamento de migração para Premium" como a causa possível.

<!--Image references-->

<!--Article references-->
[agenda de migração automática]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[criar um bilhetes de suporte]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Colocar em pausa]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Restaurar]: sql-data-warehouse-restore-database-portal.md
[passos para mudar o nome durante a migração]: #optional-steps-to-rename-during-migration
[power cluster de escala]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[função mediumrc]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Armazenamento de Premium para maior previsibilidade de desempenho]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Portal do Azure]: https://portal.azure.com
