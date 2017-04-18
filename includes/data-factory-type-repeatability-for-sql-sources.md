## <a name="repeatability-during-copy"></a>Repetibilidade durante a cópia

Quando copiar dados para o Azure SQL/SQL Server a partir de outros dados armazena um tem de ter repetibilidade em mente para evitar resultados inesperados. 

Ao copiar dados para a base de dados do Azure SQL/SQL Server, irá cópia atividade por predefinição ACRESCENTAR o conjunto de dados para a tabela sink por predefinição. Por exemplo, ao copiar dados a partir de uma origem de ficheiro CSV (dados de valores separados por vírgulas) que contém dois registos de base de dados do Azure SQL/SQL Server, este é o aspeto a tabela:
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

Suponha que encontrou erros no ficheiro de origem e atualizados a quantidade de tubos para baixo a partir de 2 a 4 no ficheiro de origem. Se executar novamente no setor de dados para esse período, encontrará dois registos novos acrescentados a base de dados do Azure SQL/SQL Server. O abaixo assume nenhuma das colunas na tabela tem restrição de chave primária.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Para evitar esta situação, terá de especificar a semântica de UPSERT, tirando partido do abaixo 2 mecanismos indicados abaixo.

> [AZURE.NOTE] Um setor pode ser voltar a executar automaticamente no Azure dados fábrica de acordo com a política de repetir especificada.

### <a name="mechanism-1"></a>Mecanismo 1

Pode tirar partido **sqlWriterCleanupScript** propriedade efetue primeiro a ação de limpeza quando um setor é executado. 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

O script de limpeza seria ser executado primeiro durante a cópia de um determinado setor que pretende eliminar os dados a partir da tabela SQL correspondente a esse setor. A atividade subsequentemente irá inserir os dados na tabela de SQL. 

Se o setor agora é o voltar a executar, em seguida, que irá encontrar que a quantidade é atualizada como pretendido.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Suponha que o registo de anilha simples é removido de csv original. Em seguida, executar novamente no setor seria produzir o seguinte resultado: 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

Novo nada teve ser feito. A atividade de cópia executou o script de limpeza para eliminar os dados correspondentes para esse setor. Em seguida, -ler a entrada de dados a partir de csv (que continha, em seguida, apenas 1 registo) e inserido-lo a tabela. 

### <a name="mechanism-2"></a>Mecanismo 2
> [AZURE.IMPORTANT] sliceIdentifierColumnName não é suportada para armazém de dados do SQL Azure neste momento. 

Mecanismos de outro para alcançar repetibilidade são fazendo com que uma coluna dedicada (**sliceIdentifierColumnName**) na tabela de destino. Esta coluna seria utilizada por Azure dados fábrica para se certificar que de origem e destino manter-se sincronizada. Esta abordagem funciona quando existe flexibilidade na alterar ou definir o esquema de tabela do SQL de destino. 

Esta coluna seria utilizada por Azure dados fábrica para fins de repetibilidade e no processo de fábrica do Azure dados não tornará quaisquer alterações de esquema para a tabela. Forma de utilizar esta abordagem:

1.  Defina uma coluna de tipo binário (32) o destino da tabela de SQL. Deve ser sem restrições esta coluna. Vamos dar um nome esta coluna como 'ColumnForADFuseOnly' para este exemplo.
2.  Utilizá-las na atividade de cópia da seguinte forma:

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Azure dados fábrica irão povoar esta coluna de acordo com a sua necessidade para se certificar que de origem e destino manter-se sincronizada. Os valores desta coluna não devem ser utilizados fora neste contexto pelo utilizador. 

Semelhante ao mecanismo 1, atividade de cópia será automaticamente pela primeira vez limpar os dados para o setor determinado a partir do destino da tabela do SQL e, em seguida, execute a atividade de cópia normalmente para inserir os dados de origem para o destino para esse setor. 
