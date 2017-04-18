<properties 
    pageTitle="Carregar terabytes de dados no armazém de dados SQL | Microsoft Azure" 
    description="Demonstra como 1 TB de dados pode ser carregado num armazém de dados do SQL Azure em 15 minutos com fábrica de dados do Azure" 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="jingwang"/>

# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-azure-data-factory-copy-wizard"></a>Carregar 1 TB no armazém de dados do SQL Azure em 15 minutos com Azure dados fábrica [cópia Wizard]
[Armazém de dados do SQL Azure](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é uma baseado na nuvem, escala de saída base de dados capaz de processar grandes volumes de dados, não relacionais e relacionais.  Armazém de dados SQL criada com base em arquitetura de processamento previstos excederem consideravelmente paralelas (MPP), está optimizada para enterprise dados armazém das cargas de trabalho.  Oferece elasticidade nuvem com a flexibilidade para dimensionar o armazenamento e calcular de forma independente.

Introdução ao armazém de dados do SQL Azure está agora facilita mais do que nunca com o **Azure fábrica de dados**.  Azure fábrica de dados é um serviço de integração de dados totalmente geridas baseado na nuvem, que pode ser utilizado para preencher um armazém de dados SQL com os dados a partir do seu sistema existente e poupando tempo útil enquanto avaliar armazém de dados SQL e de construção de soluções sua análise sobre a mesma.  Eis as vantagens de carregamento de dados armazém de dados SQL Azure com o Azure dados fábrica chave:

- **Fácil para configurar**: passo 5 assistente intuitivo com sem scripting necessário.
- **Arquivo de dados avançados suporte**: suporte incorporado para um conjunto avançado de arquivos de dados baseadas na nuvem e no local.
- **Seguro e em conformidade**: os dados são transferidos sobre HTTPS ou ExpressRoute e presença do serviço global garante que os seus dados nunca deixa o limite geográfico
- **Desempenho paralelo utilizando PolyBase** – utilizando Polybase é a forma mais eficiente de mover os dados para armazém de dados do SQL Azure. Utilizar a funcionalidade de BLOBs transição, é possível alcançar velocidades de carga elevada de todos os tipos de arquivos de dados para além de armazenamento de Blobs do Azure, que suporta o Polybase por predefinição.

Este artigo mostra-lhe como utilizar o Assistente para copiar dados fábrica para carregar os dados de 1 TB de armazenamento de Blobs do Azure para armazém de dados do SQL Azure em 15 minutos, a superior 1.2 GBps débito.

Este artigo fornece instruções passo a passo para mover dados para armazém de dados do SQL Azure utilizando o Assistente de cópia. 

> [AZURE.NOTE] Consulte o artigo [mover os dados de armazém de dados SQL Azure utilizando a fábrica de dados do Azure e para](data-factory-azure-sql-data-warehouse-connector.md) artigo para obter informações gerais sobre as capacidades da fábrica de dados no mover dados para/a partir do armazém de dados do SQL Azure. 
> 
> Também pode criar tubagens utilizando o portal do Azure, Visual Studio, PowerShell, etc. Consulte o artigo [Tutorial: copiar dados de Blobs do Azure para base de dados do SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções sobre rápida com instruções passo a passo para utilizar a atividade de cópia no Azure fábrica de dados.  

## <a name="prerequisites"></a>Pré-requisitos
- Armazenamento de Blobs do Azure: esta experiência utiliza armazenamento de Blobs do Azure (GRS) para armazenar o conjunto de dados de teste TPC-H.  Se não tiver uma conta de armazenamento Azure, saiba [como criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account).
- Dados [TPC H](http://www.tpc.org/tpch/) : vamos utilizar TPC H como o conjunto de dados de teste.  Para fazê-lo, tem de utilizar `dbgen` a partir do toolkit TPC H, que ajuda-o a gerar o conjunto de dados.  Pode transferir ou código de origem para `dbgen` [TPC ferramentas](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) de e compilar si próprio ou transferir o ficheiro binário compilado do [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Executar dbgen.exe com os seguintes comandos para gerar ficheiro simples de 1 TB para `lineitem` propagação da tabela ao longo de 10 ficheiros:
    - `Dbgen -s 1000 -S **1** -C 10 -T L -v`
    - `Dbgen -s 1000 -S **2** -C 10 -T L -v`
    - …
    - `Dbgen -s 1000 -S **10** -C 10 -T L -v` 

    Agora, copie os ficheiros gerados para BLOBs do Azure.  Fazer referência a [mover dados de um sistema de ficheiros no local ao utilizar a fábrica de dados do Azure e para](data-factory-onprem-file-system-connector.md) como fazê-lo utilizando a função ADF copiar.   
- Armazém de dados do SQL Azure: esta experiência carrega dados no armazém de dados do SQL Azure que criou com 6.000 DWUs

    Consulte [criar um armazém de dados do SQL Azure](../sql-data-warehouse/sql-data-warehouse-get-started-provision/) para obter instruções detalhadas sobre como criar uma base de dados do SQL armazém de dados.  Para obter o melhor desempenho de carga possíveis para armazém de dados SQL utilizando Polybase, optamos número máximo de unidades de armazém de dados (DWUs) permitidos na definição desempenho, que é 6.000 DWUs.

    > [AZURE.NOTE] 
    > Quando o carregamento de Blobs do Azure, o desempenho de carregamento dos dados são proporcionais diretamente para o número de DWUs configurar no armazém de dados SQL:
    > 
    > Carregar o 1 TB para 1.000 armazém de dados do SQL DWU leva-o até 87min (~ 200MBps débito) carregar 1 TB para 2.000 armazém de dados do SQL DWU leva-o até 46 mínimo (~ 380MBps débito) carregar 1 TB para 6.000 armazém de dados do SQL DWU leva-o até 14min (~1.2GBps débito) 

    Para criar um armazém de dados SQL com 6.000 DWUs, mova o controlo de deslize de desempenho para a direita:

    ![Controlo de deslize de desempenho](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Para uma base de dados existente que não está configurado com 6.000 DWUs, pode dimensioná-lo através do Azure portal.  Navegue para a base de dados no portal do Azure e existe um botão de **escala** no painel de **Descrição geral** do apresentado na seguinte imagem:

    ![Botão de escala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Clique no botão de **escala** para abrir o painel seguinte, mova o cursor para o valor máximo e clique em botão **Guardar** .

    ![Caixa de diálogo Escala](media/data-factory-load-sql-data-warehouse/scale-dialog.png)
    
    Esta experiência carrega dados no armazém de dados do SQL Azure utilizando `xlargerc` classe de recursos.

    Para obter melhor débito possível, cópia tem de ser executadas com um utilizador armazém de dados SQL que pertencem a `xlargerc` classe de recursos.  Saiba como fazê-lo fazendo seguinte [alterar um exemplo de classe de recursos do utilizador](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).  

- Crie esquema de tabela de destino na base de dados do armazém de dados do SQL Azure, ao executar a seguinte instrução DDL:

        CREATE TABLE [dbo].[lineitem]
        (
            [L_ORDERKEY] [bigint] NOT NULL,
            [L_PARTKEY] [bigint] NOT NULL,
            [L_SUPPKEY] [bigint] NOT NULL,
            [L_LINENUMBER] [int] NOT NULL,
            [L_QUANTITY] [decimal](15, 2) NULL,
            [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
            [L_DISCOUNT] [decimal](15, 2) NULL,
            [L_TAX] [decimal](15, 2) NULL,
            [L_RETURNFLAG] [char](1) NULL,
            [L_LINESTATUS] [char](1) NULL,
            [L_SHIPDATE] [date] NULL,
            [L_COMMITDATE] [date] NULL,
            [L_RECEIPTDATE] [date] NULL,
            [L_SHIPINSTRUCT] [char](25) NULL,
            [L_SHIPMODE] [char](10) NULL,
            [L_COMMENT] [varchar](44) NULL
        )
        WITH
        (
            DISTRIBUTION = ROUND_ROBIN,
            CLUSTERED COLUMNSTORE INDEX
        )

Com os passos de pré-requisito concluídos, vamos agora está prontos configurar a atividade de cópia utilizando o Assistente de cópia.

## <a name="launch-copy-wizard"></a>Inicie o Assistente de cópia

1.  Inicie a sessão [portal do Azure](https://portal.azure.com).
2.  Clique em **+ Novo** a partir do canto superior esquerdo, clique em **informações da empresa + analytics**e clique em **Dados fábrica**. 
6. No pá **Novo fábrica de dados** :
    1. Introduza **LoadIntoSQLDWDataFactory** para o **nome**.
        O nome da fábrica dados Azure tem de ser exclusivo global. Se receber o erro: o **nome da fábrica de dados "LoadIntoSQLDWDataFactory" não está disponível**, altere o nome da fábrica dados (por exemplo, yournameLoadIntoSQLDWDataFactory) e tente criar novamente. Consulte o tópico de [Dados Factory - as regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura para artefactos fábrica de dados.  
     
    2. Selecione a sua **subscrição**do Azure.
    3. Grupo de recursos, efetue um dos seguintes passos: 
        1. Selecione **utilizar existente** para selecionar um grupo de recursos existente.
        2. Selecione **Criar novo** para introduzir um nome para um grupo de recursos.
    3. Selecione uma **localização** para a fábrica de dados.
    4. Selecione a caixa de verificação de **Afixar ao dashboard** na parte inferior da pá.  
    5. Clique em **Criar**.
10. Após a criação está concluída, verá a **Fábrica de dados** pá conforme apresentado na seguinte imagem:

    ![Home page do fábrica dados](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
11. Na home page a fábrica de dados, clique no mosaico **copiar dados** para iniciar **O Assistente de cópia**. 

    > [AZURE.NOTE] Se vir que o browser está retido na "Autorizar...", desativar/desmarque definição **bloquear cookies de terceiros e dados do site** (ou) mantê-lo ativado e criar uma exceção para **login.microsoftonline.com** e, em seguida, tente iniciar o assistente novamente.


## <a name="step-1-configure-data-loading-schedule"></a>Passo 1: Configurar a agenda de carregamento de dados
É o primeiro passo configurar o carregamento agenda dos dados.  

Na página de **Propriedades** :
1. Introduza **CopyFromBlobToAzureSqlDataWarehouse** para **nome da tarefa**
2. Selecione a opção **executar uma vez agora** .   
3. Clique em **seguinte**.  

![Assistente de cópia - página de propriedades](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Passo 2: Configurar origem
Esta secção mostra-lhe os passos para configurar a origem: ficheiros de item de Blobs do Azure que contém a 1 linha TB TPC-H.

Selecione o **Armazenamento de Blobs do Azure** como os dados armazenam e clique em **seguinte**.

![Copiar o assistente - selecionar origem de página](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

Preencha as informações de ligação para a conta de armazenamento de Blobs do Azure e clique em **seguinte**.

![Assistente de cópia - informações de ligação de origem](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

Selecione a **pasta** que contém os ficheiros de item de linha TPC H e clique em **seguinte**.

![Copiar o assistente - selecione a pasta de entrada](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

Após clicar em **seguinte**, as definições de formato de ficheiro são detetadas automaticamente.  Certifique-se de que delimitador da coluna está ' | 'em vez do predefinido ponto e vírgula','.  Clique em **seguinte** depois de ter pré-visualizado os dados.

![Assistente de cópia - definições de formato de ficheiro](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Passo 3: Configurar o destino
Esta secção mostra-lhe como configurar o destino: `lineitem` tabela na base de dados armazém de dados do SQL Azure.

Selecione **Armazém de dados do SQL Azure** , como o arquivo de destino e clique em **seguinte**.

![Assistente de cópia - seleccione o destino arquivo de dados](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

Preencha as informações de ligação para armazém de dados do SQL Azure.  Certifique-se de que especificar que o utilizador que é um membro da função `xlargerc` (consulte a secção de **Pré-requisitos** para obter instruções detalhadas) e clique em **seguinte**. 

![Assistente de cópia - informações de ligação de destino](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

Selecione a tabela de destino e clique em **seguinte**.

![Copiar o Assistente de-página de mapeamento de tabela](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

Aceite as predefinições para o mapeamento de coluna e clique em **seguinte**.

![Copiar o Assistente de-página de mapeamento de esquema](media/data-factory-load-sql-data-warehouse/schema-mapping.png)


## <a name="step-4-performance-settings"></a>Passo 4: Definições de desempenho

**Permitir polybase** está selecionada por predefinição.  Clique em **seguinte**.

![Copiar o Assistente de-página de mapeamento de esquema](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Passo 5: Implementar e monitorizar o carregamento resultados
Clique em botão **Concluir** para implementar. 

![Assistente de cópia - página de resumo](media/data-factory-load-sql-data-warehouse/summary-page.png)

Depois da implementação estiver concluída, clique em `Click here to monitor copy pipeline` para monitorizar a cópia executar progresso.

Selecione pipeline de cópia que criou na lista de **Atividade Windows** .

![Assistente de cópia - página de resumo](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

Pode ver a cópia executar detalhes no **Explorador de janela de atividade** no painel direito, incluindo o volume de dados ler a partir de origem e escritos em destino, duração e o débito médio para executar.

Como pode ver no ecrã seguinte captura, copiar 1 TB de armazenamento de Blobs do Azure para armazém de dados SQL demorou 14 minutos, eficazmente movimentar 1,22 GBps débito!

![Assistente de cópia - caixa de diálogo com êxito](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Melhores práticas
Eis alguns procedimentos recomendados para executar a sua base de dados do armazém de dados do SQL Azure:

- Utilize uma classe de recursos maior quando carrega para um índice de COLUMNSTORE AGRUPADO.
- Para mais eficientes associações, considere utilizar distribuição hash por uma coluna em vez de predefinido arredondar distribuição robin select.
- Para velocidades carregamento mais rápidas, considere a utilização da pilha para dados breves.
- Crie estatísticas depois de concluir a ser carregados armazém de dados do SQL Azure.

Consulte o artigo [melhores práticas para armazém de dados do SQL Azure](../sql-data-warehouse/sql-data-warehouse-best-practices.md) para obter detalhes. 

## <a name="next-steps"></a>Próximos passos
- [Assistente para copiar dados fábrica](data-factory-copy-wizard.md) - este artigo fornece detalhes sobre o Assistente para copiar. 
- [Desempenho de atividade de cópia e guia optimização](data-factory-copy-activity-performance.md) - este artigo contém as medições de desempenho de referência e guia optimização.

