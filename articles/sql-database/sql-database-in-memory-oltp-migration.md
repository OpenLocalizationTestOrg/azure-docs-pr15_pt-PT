<properties
    pageTitle="Na memória OLTP melhora o desempenho do SQL txn | Microsoft Azure"
    description="Adoptem na memória OLTP para melhorar o desempenho transaccional numa base de dados SQL existente."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor="MightyPen"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="use-in-memory-oltp-preview-to-improve-your-application-performance-in-sql-database"></a>Utilização de memória OLTP (pré-visualização) para melhorar o desempenho da aplicação na base de dados SQL

[Na memória OLTP](sql-database-in-memory.md) podem ser utilizados para melhorar o desempenho da carga de trabalho OLTP nas bases de dados do [Premium](sql-database-service-tiers.md) Azure SQL sem aumentar o nível de desempenho.

Siga estes passos para adotar na memória OLTP na base de dados existente.

## <a name="step-1-ensure-your-premium-database-supports-in-memory-oltp"></a>Passo 1: Certifique-se que a base de dados Premium suporta OLTP na memória

Bases de dados de Premium criadas de Novembro de 2015 ou posterior suporta a funcionalidade de memória. Pode verificar se a sua base de dados Premium suporta a funcionalidade de memória ao executar a seguinte instrução Transact-SQL. Na memória é suportada se o resultado devolvido é 1 (não 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* designa *Representarem da transação processamento*

Se a base de dados existente tem de ser movida para uma nova base de dados V12 Premium, pode utilizar as seguintes técnicas para exportar e, em seguida, importar os seus dados.

#### <a name="export-steps"></a>Passos de exportação

Exporte a base de dados de produção para um bacpac utilizando:

- A funcionalidade [Exportar](sql-database-export.md) no [portal](https://portal.azure.com/).

- A funcionalidade de **aplicação de exportar dados camadas** numa [SSMS.exe atualizado](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. No **Explorador de objectos**, expanda o nó de **bases de dados** .
 2. Botão direito do rato o nó do seu base de dados.
 3. Clique em **tarefas** > **Exportar dados camada de aplicação**.
 4. Trabalhar com a janela do assistente que é apresentada.


#### <a name="import-steps"></a>Passos de importação

Importe o bacpac para uma nova base de dados Premium.

1. No Azure [portal](https://portal.azure.com/),
 - Navegue para o servidor.
 - Selecione a opção [Importar base de dados](sql-database-import.md) .
 - Selecione um prémio preços de camadas.

2. Utilize SSMS para importar o bacpac:
 - No **Explorador de objectos**, com o botão direito no nó de **bases de dados** .
 - Clique em **Importar dados camada aplicação**.
 - Trabalhar com a janela do assistente que é apresentada.


## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Passo 2: Identificar objetos para migrar para o OLTP na memória

SSMS inclui um relatório de **Descrição geral de análise de desempenho da transação** que pode executar relativamente a uma base de dados com uma carga de trabalho ativa. O relatório identifica tabelas e procedimentos armazenados que são candidatos para migração para OLTP na memória.

No SSMS para gerar o relatório:
- No **Explorador de objectos**, com o botão direito o nó do seu base de dados.
- Clique em **relatórios** > **relatórios padrão** > **Descrição geral de análise de desempenho da transação**.

Para mais informações, consulte o artigo [determinar se uma tabela ou armazenados procedimento devem ser convertidos para OLTP na memória](http://msdn.microsoft.com/library/dn205133.aspx).


## <a name="step-3-create-a-comparable-test-database"></a>Passo 3: Criar uma base de dados de teste comparáveis

Suponha que o relatório indica a que sua base de dados tem uma tabela que iria beneficiem das vantagens da ser convertido numa tabela de memória otimizado. Recomendamos que teste primeiro para confirmar a indicação através de testes.

Tem uma cópia de teste da sua base de dados de produção. A base de dados de teste tem de ser do mesmo nível serviço camada como a base de dados de produção.

Para facilitar a testar, aperfeiçoar a base de dados de teste da seguinte forma:

1. Ligar à base de dados de teste com SSMS.

2. Para evitar necessário especificá-las a opção com (INSTANTÂNEO) em consultas, defina a opção de base de dados conforme a seguinte instrução T SQL:
```
ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## <a name="step-4-migrate-tables"></a>Passo 4: Migrar tabelas

Tem de criar e preencher uma cópia de memória otimizado da tabela que pretende testar. Pode criá-la ao utilizar um:

- O Assistente de otimização memória útil no SSMS.
- Manual T-SQL.


#### <a name="memory-optimization-wizard-in-ssms"></a>Assistente de Otimização da memória no SSMS

Para utilizar esta opção de migração:

1. Ligar à base de dados de teste com SSMS.

2. No **Explorador de objectos**, com o botão direito na tabela e, em seguida, clique em **Advisor de Otimização da memória**.
 - É apresentado o Assistente de **Tabela memória otimizador de classificação** .

3. No assistente, clique em **validação de migração** (ou no botão **seguinte** ) para ver se a tabela tem as funcionalidades não suportadas que não são suportadas em tabelas de memória otimizado. Para obter mais informações, consulte:
 - *Lista de verificação de otimização de memória* no [Advisor de Otimização da memória](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Construções transact-SQL não suportadas pelo OLTP na memória](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Migrar para o OLTP na memória](http://msdn.microsoft.com/library/dn247639.aspx).

4. Se a tabela tem sem funcionalidades não suportadas, a classificação do pode executar o esquema real e a migração de dados por si.


#### <a name="manual-t-sql"></a>Manual T-SQL

Para utilizar esta opção de migração:

1. Ligar à sua base de dados de teste com SSMS (ou um utilitário semelhante).

2. Obter o script T-SQL completo para a sua tabela e respectivos índices.
 - No SSMS, com o botão direito o nó do seu tabela.
 - Clique em **tabela de Script como** > **criar para** > **nova janela de consulta**.

3. Na janela de script, adicionar com (MEMORY_OPTIMIZED = ON) para a instrução CREATE TABLE.

4. Se existir um índice agrupadas, altere-o para NONCLUSTERED.

5. Mudar o nome da tabela existente utilizando SP_RENAME.

6. Crie a nova cópia de memória otimizado da tabela ao executar o script CREATE TABLE editado.

7. Copie os dados para a tabela de memória otimizado utilizando o inserir... SELECIONE * PARA:
    
```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Passo 5 (opcional): Migrar procedimentos armazenados

A funcionalidade de memória também pode modificar um procedimento armazenado para um desempenho melhorado.


### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considerações de procedimentos armazenados vierem compilados

Um procedimento armazenado vierem compilado tem de ter as seguintes opções na sua cláusula T SQL com:

- NATIVE_COMPILATION

- SCHEMABINDING: o que significa que tabelas que o procedimento armazenado não pode ter as respetivas definições de coluna alteradas de qualquer forma que afeta o procedimento armazenado, a menos que larga o procedimento armazenado.


Um módulo nativo tem de utilizar um grande [blocos ATÓMICO](http://msdn.microsoft.com/library/dn452281.aspx) para a gestão da transação. Não existe nenhuma função para uma transação começar explícitas ou para anular transação. Se o seu código detectar uma violação de uma regra de negócio, pode terminar a bloco atómico com uma instrução [GERAR](http://msdn.microsoft.com/library/ee677615.aspx) .


### <a name="typical-create-procedure-for-natively-compiled"></a>CREATE PROCEDURE típicas para vierem compilado

Normalmente, T-SQL para criar um procedimento armazenado vierem compilado é semelhante a seguinte modelo:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

- Para TRANSACTION_ISOLATION_LEVEL, INSTANTÂNEO é o valor mais comum para o procedimento armazenado vierem compilado. No entanto, um subconjunto dos outros valores também são suportados:
 - FORMA REPETIDA DE LEITURA
 - SERIALIZÁVEL


- O valor de idioma tem de ser presente na vista sys.languages.


### <a name="how-to-migrate-a-stored-procedure"></a>Como migrar um procedimento armazenado

Os passos de migração são:


1. Obter o script CREATE PROCEDURE para o procedimento armazenado interpretado normal.

2. Reescreva respectivo cabeçalho para corresponder ao modelo anterior.

3. Verifica se o procedimento armazenado código T SQL utiliza as funcionalidades que não são suportadas para procedimentos armazenados vierem compilados. Implementar soluções se for necessário.
 - Para obter detalhes, consulte [Problemas de migração para compilado vierem procedimentos armazenados](http://msdn.microsoft.com/library/dn296678.aspx).

4. Mudar o nome antigo procedimento armazenado utilizando SP_RENAME. Ou simplesmente LARGÁ-lo.

5. Execute o script Criar procedimento T-SQL editada.


## <a name="step-6-run-your-workload-in-test"></a>Passo 6: Executar a sua carga de trabalho no teste

Execute uma carga de trabalho na sua base de dados de teste semelhante à carga de trabalho é executado no seu base de dados de produção. Isto deve revelar o lucro de desempenho obtido pela utilização da funcionalidade de memória para tabelas e procedimentos armazenados.

Atributos principais a carga de trabalho são:

- Número de ligações em simultâneo.

- Rácio de leitura/escrita.


Personalize e executar a carga de trabalho de teste, considere utilizar a ferramenta de ostress.exe útil, que ilustrado no [aqui](sql-database-in-memory.md).


Para minimizar a latência da rede, execute o teste na mesma região geográfica Azure onde existe a base de dados.


## <a name="step-7-post-implementation-monitoring"></a>Passo 7: Monitorização de pós-implementação

Considere a monitorização os efeitos de desempenho da sua implementações na memória na produção:

- [Armazenamento incorporado na memória monitor](sql-database-in-memory-oltp-monitoring.md).

- [Base de dados do SQL Azure utilizando vistas dinâmicas de gestão de monitorização](sql-database-monitoring-with-dmvs.md)


## <a name="related-links"></a>Ligações relacionadas

- [Na memória OLTP (na memória otimização)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Introdução aos procedimentos armazenados vierem compilados](http://msdn.microsoft.com/library/dn133184.aspx)

- [Advisor de Otimização da memória](http://msdn.microsoft.com/library/dn284308.aspx)

