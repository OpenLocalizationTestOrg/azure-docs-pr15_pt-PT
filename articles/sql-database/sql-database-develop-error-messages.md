<properties
    pageTitle="Códigos de erro SQL - erro de ligação de base de dados | Microsoft Azure"
    description="Saiba mais sobre códigos de erro SQL para as aplicações de cliente da base de dados SQL, tais como os erros comuns de ligação de base de dados, base de dados cópia problemas e erros gerais. "
    keywords="código de erro de SQL, sql do access, o erro de ligação de base de dados, códigos de erro de sql"
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="annemill"/>


# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-error-and-other-issues"></a>Códigos de erro SQL para aplicações de cliente da base de dados SQL: erro de ligação e outros problemas de base de dados


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Este artigo lista os códigos de erro SQL para aplicações de cliente de base de dados SQL, incluindo erros de ligação de base de dados, erros breves (também designados falhas breves), erros de governação do recurso, problemas de cópia da base de dados, flexível agrupamento e outros erros. A maioria das categorias são determinadas base de dados do SQL Azure e não são aplicadas ao Microsoft SQL Server.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>Erros de ligação de base de dados, erros breves e outros erros de temporários

A tabela seguinte abrange os códigos de erro SQL para erros de perda de ligação e outros erros de breves que poderá encontrar quando a aplicação tentativas para aceder a base de dados SQL. Para obter tutoriais de introdução sobre como ligar à base de dados do SQL Azure, consulte [para ligar à base de dados do SQL Azure](sql-database-libraries.md).

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>Erros de breves falhas e erros mais comuns de ligação de base de dados

Infraestrutura do Azure tem a capacidade de reconfigurar dinamicamente servidores quando surgirem grossas das cargas de trabalho no serviço de base de dados SQL.  Este comportamento dinâmico poderá fazer com que o seu programa de cliente a perder a ligação à base de dados SQL. Este tipo de condição de erro chama-se uma *Falha breves*.

Se o seu programa de cliente tiver lógica de repetição, pode tentar restabelecer uma ligação depois de atribuir à hora de falhas breves para corrigir própria.  Recomendamos que atrasar para 5 segundos antes da sua primeira repetição. Repetir a operação após um atraso menor do que os riscos de 5 segundos asfixiam o serviço de nuvem. Para cada subsequente repetir o atraso deve crescer exponencialmente, até um máximo de 60 segundos.

Erros de breves normalmente manifesto como um dos seguintes mensagens de erro a partir de programas do cliente:

- Base de dados < db_name > servidor < Azure_instance > não está disponível neste momento. Volte a tentar mais tarde a ligação. Se o problema persistir, contacte o suporte ao cliente e forneça-o ID de rastreio de sessão do < session_id >

- Base de dados < db_name > servidor < Azure_instance > não está disponível neste momento. Volte a tentar mais tarde a ligação. Se o problema persistir, contacte o suporte ao cliente e forneça-o ID de rastreio de sessão do < session_id >. (Microsoft SQL Server, erro: 40613)

- Uma ligação existente forçar foi fechada pelo anfitrião remoto.

- System.Data.Entity.Core.EntityCommandExecutionException: Ocorreu um erro ao executar a definição de comando. Consulte o artigo a exceção interna para obter detalhes. ---> System.Data.SqlClient.SqlException: Ocorreu um erro de nível de transporte ao receber os resultados do servidor. (fornecedor: fornecedor de sessão, erro: 19 - ligação física não é utilizável)

Para obter exemplos de código de lógica de repetição, consulte:

- [Bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md) 
- [Ações para corrigir erros de ligação e breves erros na base de dados SQL](sql-database-connectivity-issues.md)

Um debate do *período de bloqueio* para clientes que utilizam ADO.NET está disponível no [SQL Server ligação agrupamento (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### <a name="transient-fault-error-codes"></a>Códigos de erro breves de falhas

Os seguintes erros são breves e devem ser tentada novamente no lógica da aplicação 

| Código de erro | Gravidade | Descrição |
| ---: | ---: | :--- |
| 4060 | 16 | Não é possível abrir a base de dados "% & #x2a; ls" pedido por login. Ocorreu uma falha em login. |
|40197|17|O serviço encontrou um erro processar o seu pedido. Volte a tentar. Código de erro %d.<br/><br/>Receberá este erro quando o serviço é premida devido ao software ou actualizações de hardware, falhas de hardware ou outros problemas de activação pós-falha. O código de erro (%d) incorporado numa mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou activação pós-falha que ocorreram. Alguns exemplos do erro códigos são incorporados na mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>Voltar a ligar ao seu servidor de base de dados SQL serão automaticamente ligá-lo para uma cópia da base de dados saudável. A aplicação tem de capturas de registo de erros 40197, o código de erro incorporado (%d) na mensagem para resolução de problemas e experimente voltar a ligar à base de dados do SQL até os recursos estão disponíveis e a sua ligação é estabelecida novamente.|
|40501|20|O serviço está ocupado. Repetir o pedido após 10 segundos. ID do incidente: %ls. Código: %d.<br/><br/>*Nota:* Para obter mais informações, consulte:<br/>• [Limites de recursos base de dados do SQL azure](sql-database-resource-limits.md).
|40613|17|Base de dados '% & #x2a; ls' no servidor '% & #x2a; ls' não está disponível neste momento. Volte a tentar mais tarde a ligação. Se o problema persistir, contacte o suporte ao cliente e forneça-o ID da rastreio sessão de '% & #x2a; ls'.|
|49918|16|Não consegue processar pedido. Não existem recursos suficientes para processar pedido.<br/><br/>O serviço está ocupado. Tente novamente mais tarde o pedido. |
|49919|16|Não é possível o processo de criar ou actualizar o pedido. Demasiado muitas criar ou actualizar operações em curso para a subscrição "% ld".<br/><br/>O serviço está ocupado processamento vários criar ou actualizar pedidos para a sua subscrição ou servidor. Pedidos atualmente são bloqueados para optimização do recurso. Consulta [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Aguarde até que pendentes criar ou actualizar pedidos concluídos ou eliminar um seus pedidos pendentes e volte a tentar mais tarde o seu pedido. |
|49920|16|Não consegue processar pedido. Demasiadas operações em curso para a subscrição "% ld".<br/><br/>O serviço está ocupado de processamento de pedidos de múltiplos para esta subscrição. Pedidos atualmente são bloqueados para optimização do recurso. [Sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) para o estado de funcionamento da consulta. Aguarde até que os pedidos pendentes estão concluída ou eliminar um dos seus pedidos pendentes e tente novamente o seu pedido mais tarde. |

## <a name="database-copy-errors"></a>Erros de cópia de base de dados

Podem ser encontrados os seguintes erros ao copiar uma base de dados na base de dados do SQL Azure. Para mais informações, consulte o artigo [copiar uma base de dados do SQL Azure](sql-database-copy.md).


|Código de erro|Gravidade|Descrição|
|---:|---:|:---|
|40635|16|O cliente com o endereço IP '% & #x2a; ls' temporariamente está desativado.|
|40637|16|Criar cópia de base de dados atualmente está desactivada.|
|40561|16|A cópia da base de dados falhou. Base de dados de origem ou de alvos não existe.|
|40562|16|A cópia da base de dados falhou. A base de dados de origem foi eliminada.|
|40563|16|A cópia da base de dados falhou. A base de dados de destino foi eliminada.|
|40564|16|Cópia da base de dados falhou devido a um erro interno. Verifique Remover base de dados de destino e tente novamente.|
|40565|16|A cópia da base de dados falhou. Mais do que 1 cópia da base de dados em simultâneo a mesma origem é permitida. Verifique Remover base de dados de destino e tente novamente mais tarde.|
|40566|16|Cópia da base de dados falhou devido a um erro interno. Verifique Remover base de dados de destino e tente novamente.|
|40567|16|Cópia da base de dados falhou devido a um erro interno. Verifique Remover base de dados de destino e tente novamente.|
|40568|16|A cópia da base de dados falhou. Base de dados de origem tem ficam indisponível. Verifique Remover base de dados de destino e tente novamente.|
|40569|16|A cópia da base de dados falhou. Base de dados de destino tem ficam indisponível. Verifique Remover base de dados de destino e tente novamente.|
|40570|16|Cópia da base de dados falhou devido a um erro interno. Verifique Remover base de dados de destino e tente novamente mais tarde.|
|40571|16|Cópia da base de dados falhou devido a um erro interno. Verifique Remover base de dados de destino e tente novamente mais tarde.|

## <a name="resource-governance-errors"></a>Erros de governação do recurso

Os seguintes erros são causados pela excessiva utilização de recursos enquanto estiver a trabalhar com a base de dados do SQL Azure. Por exemplo:

- Uma transação foi aberta durante muito tempo.
- Uma transação está a prejudicá demasiadas bloqueios.
- Uma aplicação é consumir demasiada memória.
- Uma aplicação é utilizado por outros demasiada `TempDb` espaço.

Tópicos relacionados:

* Obter informações mais detalhadas estão disponíveis aqui: [limites de recursos base de dados do SQL Azure](sql-database-resource-limits.md)

|Código de erro|Gravidade|Descrição|
|---:|---:|:---|
|10928|20|ID do recurso: %d. O limite de %s para a base de dados é %d e foi atingido. Para mais informações, consulte o artigo [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>O ID do recurso indica o recurso que atingiu o limite. Para threads de trabalho, o ID do recurso = 1. Para sessões, o ID do recurso = 2.<br/><br/>*Nota:* Para obter mais informações sobre este erro e como para resolver a situação, consulte:<br/>• [Limites de recursos base de dados do SQL azure](sql-database-resource-limits.md). |
|10929|20|ID do recurso: %d. O mínimo de garantia %s é %d, o limite máximo é %d e a utilização do atual para a base de dados é %d. No entanto, o servidor está ocupado e maiores do que %d pedidos de suporte para esta base de dados. Para mais informações, consulte o artigo [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Caso contrário, volte a tentar novamente mais tarde.<br/><br/>O ID do recurso indica o recurso que atingiu o limite. Para threads de trabalho, o ID do recurso = 1. Para sessões, o ID do recurso = 2.<br/><br/>*Nota:* Para obter mais informações sobre este erro e como para resolver a situação, consulte:<br/>• [Limites de recursos base de dados do SQL azure](sql-database-resource-limits.md).|
|40544|20|A base de dados chegou a sua quota de tamanho. Partição ou eliminar dados, largue índices ou consultar a documentação para resoluções possíveis.|
|40549|16|Sessão é terminada porque tem uma transação da execução longa. Experimente encurtar a transação.|
|40550|16|Foi terminada a sessão porque-tenha adquirido demasiadas bloqueios. Experimente leitura ou modificar menos linhas numa única transação.|
|40551|16|A sessão foi terminada devido a excessiva `TEMPDB` a utilização. Tente modificar a sua consulta para reduzir a utilização do espaço tabela temporária.<br/><br/>*Sugestão:* Se estiver a utilizar objetos temporários, poupar espaço na `TEMPDB` largando objetos temporários depois de já não forem necessárias pela sessão da base de dados.|
|40552|16|A sessão foi terminada devido a utilização do espaço de registo da transação excessiva. Tente modificar menos linhas numa única transação.<br/><br/>*Sugestão:* Se efetuar em volume insere utilizando o `bcp.exe` utilitário ou o `System.Data.SqlClient.SqlBulkCopy` classe, tente utilizar a `-b batchsize` ou `BatchSize` opções para limitar o número de linhas copiado para o servidor em cada transação. Se estiver a reconstruir um índice com o `ALTER INDEX` instrução, tente utilizar a `REBUILD WITH ONLINE = ON` opção.|
|40553|16|A sessão foi terminada devido à utilização de memória excessiva. Tente modificar a sua consulta para processar menos linhas.<br/><br/>*Sugestão:* Reduzir o número de `ORDER BY` e `GROUP BY` operações no seu código Transact-SQL reduz os requisitos de memória da sua consulta.|

## <a name="elastic-pool-errors"></a>Erros de agrupamento flexível

Os seguintes erros relacionados com criar e utilizar Elastics conjuntos de dados.

| Númeroerro | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | O conjunto de flexível atingiu o limite de armazenamento. Utilização de armazenamento para o conjunto de flexível não pode exceder MB (%d). | Limite de espaço de agrupamento flexível em MB. | Tentar escrever dados numa base de dados quando atingiu o limite de armazenamento do agrupamento de flexível. | Fórum considere aumentar as DTUs do agrupamento flexível se possível de forma a aumentar o seu limite de armazenamento, reduzir o armazenamento utilizado por bases de dados individuais dentro do conjunto de flexível ou remover bases de dados do conjunto de flexível. |
| 10929 | EX_USER | O mínimo de garantia %s é %d, o limite máximo é %d e a utilização do atual para a base de dados é %d. No entanto, o servidor está ocupado e maiores do que %d pedidos de suporte para esta base de dados. Consulte o artigo [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obter assistência. Caso contrário, volte a tentar novamente mais tarde. | Mínimo DTU por bases de dados; Máximo DTU por bases de dados | O número total de trabalhadores em simultâneo (pedidos) em todas as bases de dados no conjunto de flexível tentou excederem o limite de agrupamento. | Fórum considere aumentar DTUs do agrupamento flexível se possível de forma a aumentar o seu limite de trabalho ou remover bases de dados do conjunto de flexível. |
| 40844 | EX_USER | Base de dados '%ls' servidor '%ls' é uma base de dados de edição de '%ls' num conjunto de dados flexível e não pode ter uma relação de cópia contínuo. | nome, edição de base de dados, nome de servidor de base de dados | Um comando StartDatabaseCopy é emitido para um db não premium num conjunto de dados flexível. | Brevemente |
| 40857 | EX_USER | Agrupamento de flexível não encontrado para server: '%ls', o nome do conjunto de dados flexível: '%ls'. | nome do servidor; nome do conjunto flexível | Conjunto flexível especificado não existe no servidor especificado. | Forneça um nome de agrupamento flexível válida. |
| 40858 | EX_USER | Já existe flexível conjunto '%ls' no server: '%ls' | nome do conjunto flexível, nome de servidor | Já existe um conjunto de flexível especificado no servidor lógico especificado. | Forneça o novo nome do agrupamento flexível. |
| 40859 | EX_USER | Agrupamento de flexível não suporta a camada de serviços '%ls'. | camada de serviços de agrupamento flexível | Camadas de serviço especificado não são suportada para agrupamento flexível aprovisionamento. | Fornecer a edição correta ou deixe camada de serviços em branco para utilizar a camada de serviço predefinido. |
| 40860 | EX_USER | Combinação de agrupamento flexível '%ls' e o serviço de objectivo '%ls' é inválida. | nome do conjunto flexível; nome de objetivo de nível de serviço | Flexível objectivo agrupamento e serviço pode ser especificado em conjunto apenas se objectivo de serviço for especificado como 'ElasticPool'. | Especifique correta combinação de agrupamento flexível e objectivo de serviço. |
| 40861 | EX_USER | A edição de base de dados ' %. *ls não podem ser diferentes da camada de serviço de agrupamento flexível que é ' %.* ls'. | edição de base de dados, camada de serviços de agrupamento flexível | A edição de base de dados é diferente da camada de serviço de agrupamento flexível. | Não especifique uma edição de base de dados que é diferente da camada de serviço de agrupamento flexível.  Tenha em atenção que a edição de base de dados não necessitam de ser especificado. |
| 40862 | EX_USER | Nome do conjunto de dados flexível tem de ser especificado se o objectivo de serviço de agrupamento flexível for especificado. | Nenhum | Objectivo de serviço de agrupamento flexível não identifica um agrupamento de flexível. | Especifique o nome do conjunto de dados flexível se utilizar o objectivo de serviço de agrupamento flexível. |
| 40864 | EX_USER | DTUs para o conjunto de flexível tem de ser, pelo menos, (%d) DTUs para camada de serviços ' %. * dos ls. | DTUs para agrupamento flexível; camada de serviços de agrupamento flexível. | Tentar configurar DTUs para o conjunto de flexível abaixo do limite mínimo. | Volte a tentar definição as DTUs para o elásticos agrupamento, pelo menos, o limite mínimo. |
| 40865 | EX_USER | DTUs para o conjunto de flexível não podem exceder (%d) DTUs para camada de serviços ' %. * dos ls. | DTUs para agrupamento flexível; camada de serviços de agrupamento flexível. | Tentar configurar DTUs para o conjunto de flexível acima do limite máximo. | Volte a tentar definir DTUs para o conjunto de flexível para não maior que o limite máximo. |
| 40867 | EX_USER | O DTU max por bases de dados tem de estar na, pelo menos, (%d) para a camada de serviços ' %. * dos ls. | Máximo DTU por bases de dados; camada de serviços de agrupamento flexível | Tentar definir o máximo DTU por bases de dados abaixo do limite suportado. | Considere utilizar a camada de serviço de agrupamento flexível que suporte a definição pretendida. |
| 40868 | EX_USER | O DTU max por bases de dados não pode exceder (%d) para a camada de serviços ' %. * dos ls. | Máximo DTU por bases de dados; camada de serviços de agrupamento flexível. | Tentar definir o máximo DTU por bases de dados para além do limite suportado. | Considere utilizar a camada de serviço de agrupamento flexível que suporte a definição pretendida. |
| 40870 | EX_USER | O campo % DTU Mín por bases de dados não pode exceder (%d) para a camada de serviços ' %. * dos ls. | Mínimo DTU por bases de dados; camada de serviços de agrupamento flexível. | Tentar definir o mínimo DTU por bases de dados para além do limite suportado. | Considere utilizar a camada de serviço de agrupamento flexível que suporte a definição pretendida. |
| 40873 | EX_USER | O número de bases de dados (%d) e DTU mínimo por bases de dados (%d) não pode exceder DTUs do agrupamento de flexível (%d). | Número de bases de dados no conjunto de flexível; Mínimo DTU por bases de dados; DTUs do agrupamento de flexível. | Tentar especificar o mínimo DTU para bases de dados no conjunto de flexível excede DTUs do agrupamento de flexível. | Considere DTUs do conjunto flexível, de aumentar ou diminuir o mínimo DTU por bases de dados ou diminuir o número de bases de dados no conjunto de flexível. |
| 40877 | EX_USER | Não pode ser eliminado um agrupamento de flexível, a menos que o mesmo não contiver quaisquer bases de dados. | Nenhum | O conjunto de flexível contém um ou mais bases de dados e, consequentemente, não pode ser eliminado. | Remova as bases de dados do conjunto de flexível para poder eliminá-la. |
| 40881 | EX_USER | O conjunto de flexível ' %. * ls atingiu o limite de contagem de base de dados.  O limite de contagem de base de dados para o conjunto de flexível não pode exceder (%d) para um conjunto de flexível com (%d) DTUs. | Nome do conjunto de flexível; limite de contagem de base de dados do agrupamento de flexível; "e" DTUs para agrupamento de recursos. | Tentar criar ou adicionar a base de dados ao agrupamento de flexível quando atingiu o limite de contagem de base de dados do agrupamento de flexível. | Fórum considere aumentar DTUs do agrupamento flexível se possível de forma a aumentar o seu limite de base de dados ou remover bases de dados do conjunto de flexível. |
| 40889 | EX_USER | O DTUs ou limite de armazenamento para o conjunto de flexível ' %. * ls não podem ser diminuídas desde que não seria fornece espaço de armazenamento suficiente para as suas bases de dados. | Nome do conjunto flexível. | Tentar diminuir o limite de armazenamento do conjunto de flexível por baixo da respetiva utilização de armazenamento. | Fórum considere reduzir a utilização de armazenamento de bases de dados individuais no conjunto de flexível ou remover bases de dados do conjunto de para reduzir o DTUs ou limite de armazenamento. |
| 40891 | EX_USER | O campo % DTU Mín por bases de dados (%d) não pode exceder o máximo DTU por bases de dados (%d). | Mínimo DTU por bases de dados; DTU max por bases de dados. | Tentar definir o mínimo DTU por bases de dados mais elevados que o comprimento máximo DTU por bases de dados. | Certifique-se de que o mínimo DTU por bases de dados não ultrapasse o máximo DTU por bases de dados. |
| TBD | EX_USER | O tamanho do armazenamento de uma base de dados individual num conjunto de dados flexível não pode exceder o tamanho máximo permitido pela ' %. * ls agrupamento flexível camada de serviço. | camada de serviços de agrupamento flexível | O tamanho máximo da base de dados excede o tamanho máximo permitido pela camada de serviço de agrupamento flexível. | Defina o tamanho máximo da base de dados dentro dos limites do tamanho máximo permitido pela camada de serviço de agrupamento flexível. |

Tópicos relacionados:

* [Criar um conjunto de dados da base de dados flexível (c#)](sql-database-elastic-pool-create-csharp.md) 
* [Gerir um agrupamento de base de dados flexível (c#)](sql-database-elastic-pool-manage-csharp.md). 
* [Criar um conjunto de dados da base de dados flexível (PowerShell)](sql-database-elastic-pool-create-powershell.md) 
* [Monitor e gerir um agrupamento de base de dados flexível (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

## <a name="general-errors"></a>Erros gerais

Os seguintes erros não abrange quaisquer categorias anterior.

|Código de erro|Gravidade|Descrição|
|---:|---:|:---|
|15006|16|<AdministratorLogin>Não é um nome válido porque contém carateres inválidos.|
|18452|14|Falha ao iniciar sessão. O início de sessão é a partir de um domínio não fidedigno e não podem ser utilizado com o Windows authentication.%. & #x2a; ls (inícios de sessão do Windows não são suportados nesta versão do SQL Server.)|
|18456|14|Ocorreu uma falha com início de sessão do utilizador ' %. & #x2a;ls'.% & #x2a ls % & #x2a; ls (falhou o início de sessão para o utilizador "% & #x2a; ls". Falha ao alterar a palavra-passe. Alterar palavra-passe durante o início de sessão não é suportada nesta versão do SQL Server.)|
|18470|14|Ocorreu uma falha com início de sessão do utilizador '% & #x2a; ls'. Razão: A conta é disabled.%. & #x2a; ls|
|40014|16|Não podem ser utilizados várias bases de dados a mesma transação.|
|40054|16|Tabelas sem um índice agrupado não são suportadas nesta versão do SQL Server. Criar um índice agrupado e tente novamente.|
|40133|15|Esta operação não é suportada nesta versão do SQL Server.|
|40506|16|Não é válido para esta versão do SQL Server SID especificado.|
|40507|16|' %. & #x2a; não não possível invocar dos ls com parâmetros nesta versão do SQL Server.|
|40508|16|Declaração de utilização não é suportada para alternar entre as bases de dados. Utilize uma nova ligação para ligar a uma base de dados diferente.|
|40510|16|Declaração de '% & #x2a; ls' não é suportada nesta versão do SQL Server|
|40511|16|Função incorporada '% & #x2a; ls' não é suportada nesta versão do SQL Server.|
|40512|16|Preterido funcionalidade '%ls' não é suportada nesta versão do SQL Server.|
|40513|16|Servidor variável '% & #x2a; ls' não é suportada nesta versão do SQL Server.|
|40514|16|Não é suportada '%ls' nesta versão do SQL Server.|
|40515|16|Referência a nome da base de dados e/ou servidor em '% & #x2a; ls' não é suportada nesta versão do SQL Server.|
|40516|16|Objectos temporárias globais não são suportados nesta versão do SQL Server.|
|40517|16|Opção de palavra-chave ou declaração '% & #x2a; ls' não é suportada nesta versão do SQL Server.|
|40518|16|Comando DBCC '% & #x2a; ls' não é suportada nesta versão do SQL Server.|
|40520|16|Classe passíveis de serem protegidos '% S_MSG' não suportados nesta versão do SQL Server.|
|40521|16|Classe passíveis de serem protegidos '% S_MSG' não suportadas no âmbito servidor nesta versão do SQL Server.|
|40522|16|Tipo de base de dados principal '% & #x2a; ls' não é suportado nesta versão do SQL Server.|
|40523|16|Criação de utilizador implícito '% & #x2a; ls' não é suportada nesta versão do SQL Server. Crie explicitamente o utilizador antes de o utilizar.|
|40524|16|Tipo de dados '% & #x2a; ls' não é suportada nesta versão do SQL Server.|
|40525|16|COM '%.ls' não é suportada nesta versão do SQL Server.|
|40526|16|' %. & #x2a; fornecedor de conjunto de linhas dos ls não são suportados nesta versão do SQL Server.|
|40527|16|Servidores ligados não são suportados nesta versão do SQL Server.|
|40528|16|Os utilizadores não podem ser mapeados para certificados, chaves assimétricas ou inícios de sessão do Windows nesta versão do SQL Server.|
|40529|16|Função incorporada '% & #x2a; ls' num representação contexto não é suportado nesta versão do SQL Server.|
|40532|11|Não é possível abrir o servidor "% & #x2a; ls" pedido pelo login. Ocorreu uma falha em login.|
|40553|16|A sessão foi terminada devido à utilização de memória excessiva. Tente modificar a sua consulta para processar menos linhas.<br/><br/>*Nota:* Reduzir o número de `ORDER BY` e `GROUP BY` operações no seu código Transact-SQL ajuda a reduzir os requisitos de memória da sua consulta.|
|40604|16|Porque seria exceder a quota do servidor, poderia não criar/ALTER base de dados.|
|40606|16|Anexar bases de dados não é suportada nesta versão do SQL Server.|
|40607|16|Inícios de sessão do Windows não são suportados nesta versão do SQL Server.|
|40611|16|Os servidores podem ter mais pelo 128 regras de firewall definidas.|
|40614|16|Endereço IP de início de regra de firewall não pode exceder o endereço IP final.|
|40615|16|Não é possível abrir o servidor '{0}' pedido pelo login. O cliente com o endereço IP '{1}' não é permitido para aceder ao servidor.  Para ativar o acesso, utilizar o Portal de base de dados do SQL ou executar sp_set_firewall_rule da base de dados principal para criar uma regra de firewall para este endereço IP ou um intervalo de endereços.  Poderá demorar até cinco minutos para que esta alteração entre em vigor.|
|40617|16|O nome da regra firewall que começa com <rule name> é demasiado longo. Comprimento máximo é 128.|
|40618|16|O nome da regra firewall não pode ser vazio.|
|40620|16|O início de sessão falhou para o utilizador "% & #x2a; ls". Falha ao alterar a palavra-passe. Alterar palavra-passe durante o início de sessão não é suportada nesta versão do SQL Server.|
|40627|20|Operação no servidor '{0}' e '{1}' base de dados está em curso. Aguarde alguns minutos antes de tentar novamente.|
|40630|16|Falha na validação de palavra-passe. A palavra-passe não cumprir os requisitos de política porque é demasiado pequeno.|
|40631|16|A palavra-passe que especificou é demasiado longa. A palavra-passe deve ter mais do que os 128 carateres.|
|40632|16|Falha na validação de palavra-passe. A palavra-passe não cumpre os requisitos da política de uma vez que não é suficientemente complexa.|
|40636|16|Não é possível utilizar um nome de base de dados reservadas '% & #x2a; ls' nesta operação.|
|40638|16|Id da subscrição inválidos < id da subscrição >. Subscrição não existe.|
|40639|16|Pedido de não estar em conformidade com esquema: <schema error>.|
|40640|20|O servidor encontrou uma exceção inesperada.|
|40641|16|A localização especificada é inválida.|
|40642|17|Atualmente, o servidor está ocupado. Tente novamente mais tarde.|
|40643|16|O valor de cabeçalho x-ms-versão especificado é inválido.|
|40644|14|Ocorreu uma falha ao autorizar acesso à subscrição especificado.|
|40645|16|Nomedoservidor <servername> não podem ser vazia nem é nula. -Lo só pode ser efetuada para cima de letras em minúsculas «a»-«z», os números 0-9 e o hífen. Hífen não pode conduzir ou seguimento no nome.|
|40646|16|ID da subscrição não pode ser vazia.|
|40647|16|Subscrição < id da subscrição não tem nomedoservidor de servidor.|
|40648|17|Demasiados pedidos tem sido executados. Volte a tentar mais tarde.|
|40649|16|Tipo de conteúdo inválido é especificado. Aplicação/xml só é suportada.|
|40650|16|Subscrição < id da subscrição > não existir ou não está pronta para a operação.|
|40651|16|Ocorreu uma falha ao criar servidor porque a subscrição < id da subscrição > está desactivada.|
|40652|16|Não pode mover ou criar servidor. Subscrição < id da subscrição > irá exceder quota de servidor.|
|40671|17|Falha de comunicação entre o gateway e o serviço de gestão. Volte a tentar mais tarde.|
|40852|16|Não é possível abrir a base de dados ' %. *ls' no servidor ' %.* ls pedido pelo login. Só é permitido acesso à base de dados utilizando uma cadeia de ligação com segurança ativada. Para aceder a esta base de dados, modifique as cadeias de ligação para conter 'seguro' o FQDN -.database.windows de nome de servidor do servidor .net devem ser modificados para .database de nome de servidor. `secure`. windows.net.|
|45168|16|O sistema de SQL Azure está em caso de carga e está a colocar um limite superior em simultâneo operações DB CRUD para um único servidor (por exemplo, criar base de dados). O servidor especificado na mensagem de erro excedeu o número máximo de ligações em simultâneo. Tente novamente mais tarde.|
|45169|16|O sistema SQL azure está em caso de carga e está a colocar um limite superior no número de operações CRUD de servidor em simultâneo para uma subscrição única (por exemplo, criar server). A subscrição especificada na mensagem de erro excedeu o número máximo de ligações em simultâneo, e o pedido foi negado. Tente novamente mais tarde.|

## <a name="related-links"></a>Ligações relacionadas

- [Limitações de geral de base de dados do Azure SQL e diretrizes](sql-database-general-limitations.md)
- [Limites de recursos base de dados SQL Azure](sql-database-resource-limits.md)
