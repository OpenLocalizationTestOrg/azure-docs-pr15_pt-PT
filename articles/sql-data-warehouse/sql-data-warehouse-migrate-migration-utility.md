<properties
   pageTitle="Migrar: Armazém de dados utilitário de migração | Microsoft Azure"
   description="Migre para o armazém de dados SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="data-warehouse-migration-utility-preview"></a>Utilitário de migração de armazém de dados (pré-visualização)

> [AZURE.SELECTOR]
- [Transferir utilitário de migração][]

Utilitário dados armazém migração é uma ferramenta concebida para migrar o esquema e dados do SQL Server e base de dados do SQL Azure para armazém de dados do SQL Azure. Durante a migração de esquema, a ferramenta de mapas automaticamente o correspondente esquema de origem para o destino. Depois do esquema tem foram migrado, as ferramentas de fornece a opção para mover os dados com scripts gerados automaticamente.

Para além de migração de dados e esquemas, esta ferramenta fornece-lhe a opção para gerar relatórios de compatibilidade que resumem incompatibilidades entre as instâncias de origem e de destino que impeçam a migração simplificada.

## <a name="get-started"></a>Introdução
Como um pré-requisito para a instalação, terá do utilitário da linha de comandos BCP para executar scripts de migração e o Office para ver o relatório de compatibilidade. Depois de iniciar o ficheiro executável que é transferido vai ser-lhe para aceitar o EULA um padrão antes da ferramenta de será instalada.

Além disso, para executar o Utiliy de migração, terá do seguir permissões da base de dados que esteja a visualizar para migrar: criar a base de dados, alterar qualquer da base de dados ou definição de qualquer vista.

### <a name="launching-the-tool-and-connecting"></a>Iniciar a ferramenta e ligar
Instale a ferramenta de clicando no ícone do ambiente de trabalho que é apresentada a mensagem de iniciação. Ao abrir a ferramenta de, vai ser-lhe com uma página de ligação inicial, onde pode escolher a origem e destino para a ferramenta de migração. Neste momento, suportamos o SQL Server e base de dados do SQL Azure como origens e armazém de dados SQL como um destino. Após selecionar esta lhe-á ser pedido para ligar ao seu servidor de origem em nome do servidor de preenchimento e de autenticação e, em seguida, clicando em 'Ligar'.

Depois de autenticar, a ferramenta irá mostrar uma lista de bases de dados que estão presentes no servidor que está ligado. Pode começar a migração ao selecionar uma base de dados que pretende migrar e, em seguida, clicar no 'Migrar selecionada'.

## <a name="migration-report"></a>Relatório de migração
Selecionar 'Verificar a compatibilidade de base de dados' na ferramenta de irá gerar um relatório todas as incompatibilidades objeto da base de dados a resumir pedida a migrar. Pode encontrar uma lista mais abrangente acerca de algumas das funcionalidades do SQL Server que não se encontra no armazém de dados SQL na nossa [documentação de migração][]. Depois do relatório é gerado será capaz de guardar e abrir o relatório no Excel.

Tenha em atenção que ao gerar o esquema de migração, a maior parte dos problemas identificados como 'Object' será ajustado de forma a permitir a migração de imediata de que os dados. Reveja as alterações para se certificar de que não pretende fazer ajustes adicionais antes de aplicar o esquema.

## <a name="migrate-schema"></a>Esquema de migrar

Depois de ligar, selecionar 'Esquema migrar' irá gerar um script de migração do esquema para as tabelas selecionados. Portas este script a estrutura da tabela, dados incompatíveis mapas tipos de formulários mais compatíveis e cria as credenciais de segurança e esquema se isto é indicado pelo utilizador nas definições de migração. Este código pode ser executado em relação a instância do armazém de dados SQL alvo, guardadas num ficheiro, copiados para a sua área de transferência ou até mesmo editado na linha antes de efetuar a ação mais.  

Conforme indicado acima, quando migrar rever esquema a migração as alterações que a ferramenta de fez para se certificar de que que compreende-los.  

## <a name="migrate-data"></a>Migrar dados

Ao clicar na opção 'Migrar dados' pode gerar scripts BCP que se irão mover os dados pela primeira vez aos ficheiros simples no servidor, e, em seguida, diretamente no seu armazém de dados SQL. Recomendamos que este processo para se deslocar pequenas quantidades de dados e, como o número de tentativas não são incorporados e falhas poderão ocorrer se existe uma perda da ligação de rede. Para executar esta, terá de ter o utilitário de linha de comandos BCP instalado e o esquema para os dados já deve ter sido criado.

Depois de ter preenchidos os parâmetros indicados apenas terão de clique em executar migração e um conjunto de dois pacotes será gerado a localização especificada. Execute o ficheiro de exportação para exportar dados da sua origem de migração para ficheiros simples e executar o ficheiro de importação para importar os dados para armazém de dados do SQL.

## <a name="next-steps"></a>Próximos passos
Agora que já migrado alguns dados, consulte o artigo como [desenvolver][].

<!--Image references-->

<!--Article references-->
[documentação de migração]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Transferir utilitário de migração]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
