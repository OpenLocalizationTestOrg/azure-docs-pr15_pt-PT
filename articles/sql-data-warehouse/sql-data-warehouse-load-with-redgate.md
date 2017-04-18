<properties
   pageTitle="Utilizar dados plataforma Studio do Redgate para carregar os dados para armazém de dados SQL | Microsoft Azure"
   description="Saiba como utilizar dados plataforma Studio do Redgate para cenários de armazenamento de dados."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>


# <a name="load-data-with-redgate-data-platform-studio"></a>Carregar os dados com Redgate dados plataforma Studio

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)
- [Fábrica de dados](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

Este tutorial mostra-lhe como utilizar [Dados plataforma Studio do Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) para mover dados do SQL Server no local para armazém de dados do SQL Azure. Dados plataforma Studio aplica-se as correções de compatibilidade e otimizações, mais adequadas para que fique a forma mais rápida de introdução ao armazém de dados SQL.

> [AZURE.NOTE] [Redgate](http://www.red-gate.com) é um já parceiro da Microsoft que fornece várias ferramentas do SQL Server. Esta funcionalidade no dados plataforma Studio foi disponibilizada livre para uso comercial e não comercial.

## <a name="before-you-begin"></a>Antes de começar
### <a name="create-or-identify-resources"></a>Criar ou identificar recursos

Antes de iniciar este tutorial, tem de ter:

- **Base de dados no local SQL Server**: os dados que pretende importar para armazém de dados SQL tem de ter um SQL Server no local (versão 2008R2 ou acima). Dados plataforma Studio não é possível importar dados diretamente a partir de uma base de dados do SQL Azure ou de ficheiros de texto.
- **Conta de armazenamento do Azure**: dados plataforma Studio fases os dados de armazenamento de Blobs do Azure antes de o carregar armazém de dados SQL. A conta de armazenamento tem de utilizar o modelo de implementação de "Gestor de recursos" (a predefinição) em vez do modelo de implementação "Clássico". Se não tiver uma conta de armazenamento, saiba como criar uma conta de armazenamento. 
- **Armazém de dados SQL**: Este tutorial move os dados do SQL Server no local para armazém de dados SQL, pelo que necessita de ter um armazém de dados online. Se ainda não tiver um armazém de dados, saiba como criar um armazém de dados do SQL Azure.

> [AZURE.NOTE] Desempenho melhorado se a conta de armazenamento e o armazenamento de dados são criados na mesma região.

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Passo 1: Inicie sessão no Studio de plataforma de dados com a sua conta Azure
Abra o browser e navegue para o Web site [Studio de plataforma de dados](https://www.dataplatformstudio.com/) . Inicie sessão com a mesma conta Azure que utilizou para criar o armazém armazenamento de dados e conta. Se o seu endereço de e-mail está associado uma trabalho ou conta escolar e uma conta Microsoft, certifique-se de que selecione a conta que tem acesso aos seus recursos.

> [AZURE.NOTE] Se esta for a primeira vez que utilizar dados plataforma Studio, são-lhe pedido para conceder a permissão de aplicação para gerir os recursos do Azure.

## <a name="step-2-start-the-import-wizard"></a>Passo 2: Iniciar o Assistente de importação
A partir do ecrã principal do DPS, selecione a ligação do armazém de dados do SQL Azure da importação para iniciar o Assistente de importação.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Passo 3: Instalar o plataforma Studio data Gateway
Para ligar à sua base de dados do SQL Server no local, tem de instalar o Gateway DPS. O gateway é um agente do cliente que fornece acesso ao seu ambiente no local, extrai os dados e os carregamentos pendentes-lo à sua conta de armazenamento. Nunca transmite os seus dados através dos servidores do Redgate. Para instalar o Gateway:

1.  Clique na ligação **Criar Gateway**
2. Transferir e instalar o Gateway utilizando o instalador do fornecidos

![][2]

> [AZURE.NOTE] O Gateway pode ser instalado em qualquer computador com acesso à rede à base de dados do SQL Server de origem. Acede a base de dados do SQL Server utilizando a autenticação do Windows com as credenciais do utilizador actual.

Depois de instalado, o estado do Gateway muda para ligado e pode selecionar seguinte.

## <a name="step-4-identify-the-source-database"></a>Passo 4: Identificar a base de dados de origem
Na caixa de texto *Introduzir o nome de servidor* , introduza o nome do servidor que aloja a sua base de dados e selecione **seguinte**. Em seguida, no menu pendente, selecione a base de dados que pretende importar dados a partir de.

![][3]

DPS inspeciona a base de dados selecionado para tabelas a importar. Por predefinição, DPS importa todas as tabelas na base de dados. Pode selecionar ou desmarcar tabelas expandindo a ligação de todas as tabelas. Selecione o botão seguinte para avançar.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Passo 5: Escolher uma conta de armazenamento para testar os dados
DPS pede-lhe uma localização para a fase os dados. Selecione uma conta existente do armazenamento da sua subscrição e selecione **seguinte**.

> [AZURE.NOTE] DPS irá criar um novo contentor de BLOBs na conta de armazenamento de que selecionou e utilizar uma pasta distinta para cada importação.

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Passo 6: Selecione um armazém de dados
Em seguida, selecionar uma base de dados do [Armazém de dados do SQL Azure](http://aka.ms/sqldw) online para importar dados para. Assim que tiver selecionado a base de dados, tem de introduzir as credenciais para ligar à base de dados e selecione **seguinte**.

![][5]

> [AZURE.NOTE] DPS intercala as tabelas de dados de origem do armazém de dados. DPS avisa se o nome da tabela requê-la para substituir as tabelas existentes no armazém de dados. Opcionalmente, pode eliminar os objectos existentes no armazém de dados ao assinalando eliminar todos os objetos existentes antes de importar.

## <a name="step-7-import-the-data"></a>Passo 7: Importar os dados
DPS confirmar que, afinal importar os dados. Basta clicar no botão de importação de iniciar para começar a importação de dados.

![][6]

DPS apresenta uma visualização que mostra o progresso da extrair e carregar os dados do SQL Server no local e o progresso de importação para armazém de dados SQL.

![][7]

Quando a importação estiver concluída, DPS apresenta um resumo da importação de dados e um relatório de alteração das correções de compatibilidade ter sido efetuada.

![][8]

## <a name="next-steps"></a>Próximos passos
Para explorar os seus dados dentro armazém de dados do SQL, comece por visualização:

- [Consulta Azure SQL dados armazém (Visual Studio)][]
- [Visualizar dados com o Power BI][]

Para saber mais acerca dados plataforma Studio do Redgate:

- [Visite na home page do DPS](http://www.dataplatformstudio.com/)
- [Ver uma demonstração de DPS no Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Para obter uma descrição geral de outras formas de migrar e carregue os seus dados no armazém de dados SQL consulte:

- [Migrar a sua solução para armazém de dados SQL][]
- [Carregar os dados para armazém de dados do SQL Azure](./sql-data-warehouse-overview-load.md)

Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento armazém de dados SQL](./sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Consulta Azure SQL dados armazém (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualizar dados com o Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrar a sua solução para armazém de dados SQL]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md