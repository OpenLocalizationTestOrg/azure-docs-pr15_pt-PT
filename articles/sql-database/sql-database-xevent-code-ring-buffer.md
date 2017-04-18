<properties 
    pageTitle="Código de memória intermédia em anel XEvent para a base de dados SQL | Microsoft Azure" 
    description="Fornece uma amostra de código Transact-SQL que fez fácil e rápida através da utilização de destino memória intermédia em anel, base de dados do SQL Azure." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Código de destino de memória intermédia para eventos adicional na base de dados SQL de toque

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Um exemplo de código completo que pretende para a forma mais fácil rápida captura informações sobre e relatórios para um evento adicional durante um teste. O destino mais fácil para os dados de evento adicional é o [destino de memória intermédia em anel](http://msdn.microsoft.com/library/ff878182.aspx).


Este tópico apresenta uma amostra de código Transact-SQL que:


1. Cria uma tabela com dados para demonstrar com.

2. Cria uma sessão para um evento existente adicional, nomeadamente **sqlserver.sql_statement_starting**.
    - O evento está limitado à instruções SQL que contêm uma cadeia de atualização específica: **declaração como '% ATUALIZAÇÃO tabEmployee %'**.
    - Escolhe enviar o resultado do evento para um alvo de tipo de memória intermédia em anel, nomeadamente **package0.ring_buffer**.

3. Inicia a sessão do evento.

4. Problemas de algumas instruções SQL UPDATE simples.

5. Problemas de uma instrução SQL SELECT ao obter a saída de evento da memória intermédia em simultâneo.
    - **sys.dm_xe_database_session_targets** e outras vistas de gestão de dinâmica (DMVs) são associadas.

6. Impede a sessão do evento.

7. Remove o destino da memória intermédia em anel, para libertar o seus recursos.

8. Remove a sessão do evento e a tabela de demonstração.


## <a name="prerequisites"></a>Pré-requisitos


- Uma conta Azure e subscrição. Pode inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Quaisquer bases de dados que pode criar uma tabela no.
 - Opcionalmente, pode [criar uma base de dados de demonstração sobre **AdventureWorksLT** ](sql-database-get-started.md) em minutos.


- SQL Server Management Studio (ssms.exe), idealmente a versão mais recente mensal atualização. Pode transferir o ssms.exe mais recente a partir de:
 - Tópico intitulado [Transferir o SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Uma ligação direta para a transferência.](http://go.microsoft.com/fwlink/?linkid=616025)


## <a name="code-sample"></a>Exemplo de código


Com modificação muito secundária, o exemplo seguinte de código memória intermédia em anel pode ser executado em base de dados do SQL Azure ou Microsoft SQL Server. A diferença é a presença do nó 'antecedendo' na nome algumas vistas de gestão de dinâmica (DMVs), utilizado na cláusula FROM no passo 5. Por exemplo:

- sys.dm_xe**antecedendo**_session_targets
- sys.dm_xe_session_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## <a name="ring-buffer-contents"></a>Conteúdos de memória intermédia em simultâneo


Ssms.exe é utilizada para executar o código de exemplo.


Para ver os resultados, recomendamos clicado na célula em de cabeçalho de coluna **target_data_XML**.

Em seguida, no painel de resultados Recomendamos clicado na célula em de cabeçalho de coluna **target_data_XML**. Esta, clique em criada noutro separador ficheiro no ssms.exe no qual o conteúdo da célula de resultado foi apresentado, como XML.


O resultado é mostrado na seguinte bloco. Parecer longo, mas é apenas dois **<event>** elementos.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Libertar recursos contidos pela sua memória intermédia em anel


Quando tiver terminado com a sua memória intermédia em anel, pode removê-la e solte seus recursos emitir um **ALTER** semelhante ao seguinte:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


A definição da sua sessão de evento é atualizada, mas não é ignorada. Mais tarde pode adicionar outra instância da memória intermédia em simultâneo para a sua sessão de evento:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Obter mais informações


O tópico principal para eventos adicional na base de dados do SQL Azure é:


- [Considerações sobre o evento expandidos na base de dados SQL](sql-database-xevent-db-diff-from-svr.md), que contrasta alguns aspetos da eventos adicional que diferem entre base de dados do SQL Azure versus Microsoft SQL Server.


Outros tópicos de exemplo de código para eventos adicional estão disponíveis nas ligações seguintes. No entanto, tem de verificar periodicamente qualquer exemplo para ver se a amostra destinos Microsoft SQL Server versus base de dados do SQL Azure. Em seguida, pode decidir se pequenas alterações são necessárias para executar o exemplo.


- Exemplo de base de dados do SQL Azure código: [código de destino do ficheiro de evento para eventos adicional na base de dados SQL](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
