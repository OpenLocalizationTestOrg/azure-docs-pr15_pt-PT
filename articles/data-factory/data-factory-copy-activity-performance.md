<properties
    pageTitle="Copiar o desempenho de atividade e guia de sintonização | Microsoft Azure"
    description="Saiba mais sobre chaves fatores que afetam o desempenho de movimento de dados no Azure dados fábrica quando utiliza a atividade de cópia."
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
    ms.date="10/25/2016"
    ms.author="jingwang"/>


# <a name="copy-activity-performance-and-tuning-guide"></a>Copiar o guia de sintonização e desempenho de atividade
Atividade de cópia de fábrica do Azure dados fornece um primeira classe seguros, fiáveis e alto desempenho de dados ao carregar solução. Permite-lhe para copiar dezenas de terabytes de dados diariamente através de uma variedade de nuvem avançada e no local arquivos de dados. Carregamento desempenho de dados do blazing fast são fundamental para se certificar de que pode concentrar-no problema de "dados grande" core: construção de soluções de análise avançadas e obter informações abrangente a partir de todos os dados.

Azure fornece um conjunto de empresarial soluções de armazém de armazenamento e dados de dados e copiar atividade oferece um experiência que seja fácil de configurar e configurar o de carregamento de dados altamente otimizado. Com apenas uma atividade de única cópia, é possível alcançar:

- Carregar os dados no **Armazém de dados do SQL Azure** na **1.2 GBps**
- Carregar os dados para o **armazenamento de Blobs do Azure** **1.0 GBps**
- Carregar dados para a **Loja do Azure dados Lake** na **1.0 GBps**


Este artigo descreve:

- Armazena os [números de referência de desempenho](#performance-reference) para origem suportada e sink dados para o ajudar a planear o seu projeto;
- Funcionalidades que podem impulsionar o débito cópia em cenários diferentes, incluindo [cópia paralela](#parallel-copy), [unidades de movimento de dados na nuvem](#cloud-data-movement-units)e [testado cópia](#staged-copy);
- [Orientação de sintonização do desempenho](#performance-tuning-steps) sobre como ajustar o desempenho e os fatores chaves que podem afetar o desempenho de cópia.

> [AZURE.NOTE] Se não estiver familiarizado com cópia atividade em geral, consulte o artigo [mover dados utilizando a atividade de cópia](data-factory-data-movement-activities.md) antes de ler este artigo.

## <a name="performance-reference"></a>Referência de desempenho

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE] É possível alcançar débito superior, tirando partido mais unidades de movimento de dados (DMUs) que o predefinido DMUs máximos, que é 8 para uma atividade de cópia na nuvem para nuvem executar. Por exemplo, com 100 DMUs, pode copiar dados a partir de Blobs do Azure ao arquivo do Azure dados Lake à taxa de 1 gigabyte por segundo. Consulte a secção de [unidades de movimento de dados na nuvem](#cloud-data-movement-units) para obter detalhes sobre esta funcionalidade. Contacte o [suporte do Azure](https://azure.microsoft.com/support/) para pedir DMUs mais.

Pontos a tenha em atenção:

- Débito é calculado utilizando a seguinte fórmula: [ler tamanho dos dados de origem] / [executar a duração de atividade cópia].
- Os números de referência de desempenho da tabela foram medidos utilizando o conjunto de dados [TPC H](http://www.tpc.org/tpch/) numa atividade de única cópia executar.
- Para copiar entre arquivos de dados na nuvem, defina **cloudDataMovementUnits** para 1 e 4 (ou 8) para comparação. **parallelCopies** não é especificado. Consulte a secção da [cópia paralela](#parallel-copy) para obter detalhes sobre estas funcionalidades.
- Arquivos de dados Azure, de origem e sink sejam da mesma região Azure.
- Implementação híbrida (no local à nuvem, ou na nuvem para o local) movimento de dados, uma única ocorrência de gateway estava a ser executado num computador que foi separado do arquivo de dados no local. A configuração está listada na tabela seguinte. Quando uma única atividade foi em execução no gateway, a operação de cópia consumida apenas uma pequena parte da CPU do computador de teste, memória ou largura de banda de rede.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 núcleos 2.20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Memória</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Rede</td>
        <td>Interface da Internet: 10 Gbps; interface da intranet: 40 Gbps</td>
    </tr>
    </table>

## <a name="parallel-copy"></a>Cópia paralela
Pode ler os dados a partir da origem ou escrever dados para o destino **em paralelo dentro de uma atividade de cópia executar**. Esta funcionalidade melhora o débito de uma operação de cópia e reduz o tempo necessário para mover os dados.

Esta definição é a diferença entre a propriedade de **simultaneidade** na definição de atividade. A propriedade de **simultaneidade do** determina o número de **que é executado em simultâneo cópia atividade** para processar dados a partir do windows actividade diferente (AM de 1 a 2 AM, 2 AM 3 AM, 3, AM 4 AM e assim sucessivamente). Esta funcionalidade é útil ao efetuar uma carga histórica. A funcionalidade de cópia paralela aplica-se a uma **única atividade executar**.

Vamos analisar um cenário de exemplo. No exemplo seguinte, vários setores a partir do passado tem de ser processada. Dados fábrica executa uma instância da atividade de cópia (executar uma atividade) para cada setor:

- No setor de dados a partir da janela de atividade primeiro (AM de 1 a 2 AM) = = > atividade executar 1
- No setor de dados a partir da janela de atividade segunda (2 AM para AM 3) = = > atividade executar 2
- No setor de dados a partir da janela de atividade segunda (3 AM para 4 AM) = = > atividade executar 3

E assim sucessivamente.

Neste exemplo, quando o valor de **simultaneidade do** está definido como 2, **atividade executar 1** e **atividade execute 2** copiar dados de duas atividade windows **em simultâneo** para melhorar o desempenho de movimento de dados. No entanto, se vários ficheiros estão associados com atividade executar 1, o serviço de movimento de dados copia ficheiros a partir da origem para o ficheiro de um destino uma vez.

### <a name="parallelcopies"></a>parallelCopies
Pode utilizar a propriedade **parallelCopies** para indicar paralelismo que pretende copiar atividade para utilizar. Poderá pensar esta propriedade como o número máximo de threads dentro de atividade de cópia que pode ler a partir da sua origem ou escrever o arquivos de dados sink em paralelo.

Para cada actividade de cópia executar, dados fábrica determina o número de cópias paralelas utilizar para copiar dados a partir da origem de dados, armazenam e para os dados de destino armazenam. O número de cópias, paralelas que utiliza predefinido depende do tipo de origem e sink que está a utilizar.  

Origem e sink |   Contagem de cópia paralela predefinida determinada pelo serviço
------------- | -------------------------------------------------
Copiar dados entre lojas com base no ficheiro (armazenamento de BLOBs; Arquivo de dados de Lake; Amazon S3; um sistema de ficheiros no local; um HDFS no local) | Entre 1 e 32. Depende do tamanho dos ficheiros e o número de unidades de movimento de dados na nuvem (DMUs) utilizado para mover dados entre dois arquivos de dados na nuvem ou a configuração física de máquina de Gateway utilizada para obter uma cópia de híbrido (para copiar dados para ou a partir de uma loja de dados no local).
Copiar dados a partir do **arquivo de origem de dados ao armazenamento de tabela do Azure** | 4
Todos os outros pares de origem e sink | 1

Normalmente, o comportamento predefinido deve dar-lhe o melhor débito. No entanto controlar a carga máquinas que alojam os seus dados armazena ou para copiar o desempenho, pode optar por substituir o valor predefinido e especificar um valor para a propriedade **parallelCopies** . O valor tem de estar entre 1 e 32 (ambos inclusive). Em tempo de execução, para um melhor desempenho, atividade de cópia utiliza um valor que é menor ou igual ao valor de definida por si.

    "activities":[  
        {
            "name": "Sample copy activity",
            "description": "",
            "type": "Copy",
            "inputs": [{ "name": "InputDataset" }],
            "outputs": [{ "name": "OutputDataset" }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                },
                "parallelCopies": 8
            }
        }
    ]

Pontos a tenha em atenção:

- Ao copiar dados entre arquivos baseados em ficheiro, paralelismo acontece ao nível do ficheiro. Não existe nenhuma das secções num único ficheiro. Não é mais do que o número de ficheiros que tem o número de cópias paralelas o serviço de movimento dados utiliza para a operação de cópia em tempo de execução real. Se o comportamento de cópia **mergeFile**, atividade de cópia não é possível tirar partido das paralelismo.
- Quando especificar um valor para a propriedade **parallelCopies** , considere o aumento de carga no seu arquivos de dados de origem e sink e para o gateway se for uma cópia de híbrido. Isto acontece especialmente quando tiver várias atividades ou será executado em simultâneo das atividades mesmo que executa o mesmo arquivo de dados. Se notar que o arquivo de dados ou o Gateway está sise com a carregar, diminua o valor de **parallelCopies** para dispensar a carregar.
- Ao copiar dados a partir de arquivos de que não são baseados em ficheiros para lojas são baseados no ficheiro, o serviço de movimento dados ignora a propriedade **parallelCopies** . Mesmo se for especificado paralelismo, não é aplicada neste caso.

> [AZURE.NOTE] Tem de utilizar o Data Management Gateway versão 1.11 ou posterior para utilizar a funcionalidade de **parallelCopies** após fazer uma cópia de híbrido.

### <a name="cloud-data-movement-units"></a>Unidades de movimento de dados na nuvem
Uma **unidade de movimento de dados na nuvem (DMU)** é uma medida que representa a potência (uma combinação de alocação de recursos de rede, memória e CPU) de uma única unidade na fábrica de dados. Um DMU pode ser utilizada numa operação de cópia na nuvem para nuvem, mas não numa cópia de híbrido.

Por predefinição, a fábrica de dados utiliza uma única nuvem DMU para efetuar uma única actividade de cópia executar. Para substituir esta predefinição, especifique um valor para a propriedade **cloudDataMovementUnits** da seguinte forma. Para obter informações sobre o nível de ganho desempenho poderá obter quando configurar mais unidades para uma origem de cópia específica e sink, consulte a [referência de desempenho](#performance-reference).

    "activities":[  
        {
            "name": "Sample copy activity",
            "description": "",
            "type": "Copy",
            "inputs": [{ "name": "InputDataset" }],
            "outputs": [{ "name": "OutputDataset" }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                },
                "cloudDataMovementUnits": 4
            }
        }
    ]

Os **valores permitidos** para a propriedade **cloudDataMovementUnits** são 1 (predefinição), 2, 4 e 8. O **número real de nuvem DMUs** que utiliza a operação de cópia em tempo de execução é igual a ou menor do que o valor configurado, dependendo do seu padrão de dados. 

> [AZURE.NOTE] Se precisar de mais nuvem DMUs para um caudal superior, contacte o [suporte do Azure](https://azure.microsoft.com/support/). A definição de 8 e acima atualmente funciona apenas quando copia vários ficheiros de armazenamento de BLOBs ao armazenamento Blob, do arquivo de dados de Lake ou base de dados SQL Azure, e o tamanho do ficheiro é maior ou igual a 16 MB individualmente.

Para uma melhor utilização estas duas propriedades e para melhorar a sua débito de movimento de dados, consulte o artigo [casos de utilização de exemplo](#case-study-use-parallel-copy). Não tem de configurar **parallelCopies** para tirar partido do comportamento predefinido. Se a configurar e **parallelCopies** é demasiado pequeno, vários nuvem DMUs poderá não ser totalmente utilizada.  

É **importante** lembrar-se que são cobrados com base no tempo total da operação de cópia. Se uma tarefa de cópia utilizado para tirar uma hora com unidade uma nuvem e agora demora 15 minutos com quatro unidades de nuvem, o título geral permanece quase a mesma. Por exemplo, utilizar quatro unidades na nuvem. A primeira unidade de nuvem passa 10 minutos, a segunda opção, 10 minutos, o terceiro, 5 minutos e a quarta, 5 minutos, tudo numa atividade de cópia executar. São cobradas para o tempo total cópia (movimento de dados), que é 10 + 10 + 5 + 5 = 30 minutos. Utilizar **parallelCopies** não afeta faturação.

## <a name="staged-copy"></a>Copiar faseada
Ao copiar dados a partir de um arquivo de dados de origem para um arquivo de dados sink, pode optar por utilizar armazenamento de BLOBs como um arquivo de transição provisório. Transição é particularmente útil nos casos seguintes:

1.  **Pretende ingerir esta última dados a partir de vários arquivos de dados para o SQL armazém de dados através do PolyBase**. Armazém de dados SQL utiliza PolyBase como um mecanismo alto débito para carregar uma grande quantidade de dados no armazém de dados SQL. No entanto, os dados de origem tem de estar no armazenamento de BLOBs e tem de cumprir critérios adicionais. Quando carrega dados a partir de um arquivo de dados que não seja o armazenamento de BLOBs, pode ativar o dados copiar através do armazenamento de Blobs do transição provisório. Nesse caso, a fábrica de dados executa as transformações de dados necessários para se certificar de que o mesmo satisfaz os requisitos da PolyBase. Em seguida, utiliza PolyBase para carregar os dados para armazém de dados SQL. Para obter mais detalhes, consulte o artigo [Utilizar PolyBase para carregar os dados armazém de dados do SQL Azure](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
2.  **, Por vezes, ocorre o mais tempo para realizar uma circulação de dados híbrido (ou seja, para copiar entre um dados no local armazenar e dados uma nuvem armazenam) através de uma ligação de rede lenta**. Para melhorar o desempenho, pode comprimir o dados no local para que demora menos tempo para mover dados para o arquivo de dados transição na nuvem. Em seguida, pode descomprimir os dados na loja transição antes de carregar para o arquivo de dados de destino.
3.  **Não pretende abrir portas diferente de portas 80 e 443 na sua firewall, devido às políticas de TI da empresa**. Por exemplo, ao copiar dados a partir de uma loja de dados no local para um Lavatório de base de dados do Azure SQL ou uma sink armazém de dados do SQL Azure, tem de ativar a saída comunicação TCP no porta 1433 para a firewall do Windows e a firewall da empresa. Neste cenário, tirar partido do gateway a primeira copiar dados para uma instância de teste de armazenamento de BLOBs através de HTTP ou HTTPS na porta 443. Em seguida, carrega os dados numa base de dados SQL ou armazém de dados SQL de transição de armazenamento de Blobs. Deste fluxo, não tem de ativar porta 1433.

### <a name="how-staged-copy-works"></a>Como faseada cópia funciona
Quando ativar a funcionalidade de transição, pela primeira vez os dados são copiados a partir da loja de dados de origem para o arquivo de dados de transição (trazer seu próprio). Em seguida, os dados são copiados a partir da loja de dados de transição para o arquivo de dados sink. Dados fábrica gere automaticamente o fluxo de duas fases por si. Dados fábrica limpa também temporários dados a partir do armazenamento transição depois do movimento de dados estiver concluído.

Cenário de cópia na nuvem (origem e sink dados armazena está todas na nuvem), o gateway não é utilizado. O serviço de dados fábrica executa operações de cópia.

![Testado cópia: cenário nuvem](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

No cenário de cópia híbrido (origem for no local e sink estiver na nuvem), o gateway move dados a partir da loja de dados de origem para um transição arquivo de dados. Serviço de dados de fábrica do mesmo move dados a partir da loja de dados de transição para o arquivo de dados sink. Copiar dados a partir de um arquivo de dados na nuvem para um arquivo de dados no local através da transição também é suportada com o fluxo espelhado.

![Testado cópia: cenário híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Quando ativar movimento de dados utilizando um arquivo de teste, pode especificar se pretende que os dados comprimidos antes de mover os dados a partir da loja de dados de origem para um arquivo de dados provisório ou transição e, em seguida, descomprimir antes de mover os dados a partir de um intercalar ou arquivo de dados para o arquivo de dados sink de transição.

Atualmente, não é possível copiar dados entre dois arquivos de dados no local ao utilizar um arquivo de transição. Recomendamos que esta opção para estar disponível brevemente.

### <a name="configuration"></a>Configuração
Configure a definição de **enableStaging** numa atividade de cópia para especificar se pretende que os dados a testado no armazenamento Blob do antes de carregar para um arquivo de dados de destino. Quando definir **enableStaging** verdadeiro, especifique as propriedades adicionais indicadas na tabela seguinte. Se não tiver uma, também tem de criar um armazenamento do Windows Azure ou armazenamento partilhado associado assinatura o serviço de acesso para transição.

Propriedade | Descrição | Valor predefinido | Obrigatório
--------- | ----------- | ------------ | --------
**enableStaging** | Especifique se pretende copiar dados através de um arquivo de transição de intercalar. | FALSO | N
**linkedServiceName** | Especifique o nome de um [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) ligado serviço, que se refere a instância de armazenamento que pode utilizar como um arquivo de transição provisório. <br/><br/> Não é possível utilizar o armazenamento com uma assinatura de acesso partilhado para carregar os dados para SQL armazém de dados através do PolyBase. Pode utilizá-lo em todas as outras situações. | N/D | Sim, quando **enableStaging** está definida como verdadeiro
**caminho** | Especifique o caminho de armazenamento de BLOBs que pretende que contêm os dados faseados. Se não fornecer um caminho, o serviço cria um contentor para armazenar dados temporários. <br/><br/> Especifique um caminho apenas se utilizar o armazenamento com uma assinatura de acesso partilhado ou exigir temporários dos dados numa localização específica. | N/D | N
**enableCompression** | Especifica se os dados devem ser comprimidos antes de este é copiado para o destino. Esta definição reduz o volume de dados a serem transferidos. | FALSO | N

Eis uma definição de exemplo da atividade de cópia com as propriedades que são descritos na tabela anterior:

    "activities":[  
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [{ "name": "OnpremisesSQLServerInput" }],
        "outputs": [{ "name": "AzureSQLDBOutput" }],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob",
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
    ]


### <a name="billing-impact"></a>Impacto de faturação
Lhe vai ser cobrada com base em dois passos: copiar duração e tipo. 

- Ao utilizar o teste durante uma cópia na nuvem (copiar dados a partir de um arquivo de dados na nuvem para outro arquivo de dados da nuvem), lhe vai ser cobrada [soma da duração da cópia para o passo 1 e o passo 2] x [preço de unidade de cópia de nuvem].
- Ao utilizar o teste durante uma cópia de híbrido (copiar dados a partir de uma loja de dados no local para um arquivo de dados na nuvem), lhe vai ser cobrada durante [híbrido cópia] x [preço de unidade de cópia híbrido] + [duração cópia na nuvem] x [preço de unidade de cópia de nuvem].


## <a name="performance-tuning-steps"></a>Passos de sintonização do desempenho
Sugerimos que seguir estes passos para ajustar o desempenho do seu serviço de fábrica de dados com cópia atividade:

1.  **A caixa de verificação estabelecer um plano base**. Durante a fase de desenvolvimento, teste o pipeline utilizando atividade de cópia contra uma amostra de representante de dados. Pode utilizar a fábrica de dados [Cortar modelo](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) para limitar a quantidade de dados que com quem trabalha.

    Recolha tempo de execução e características de desempenho ao utilizar a **monitorização e gestão de aplicação**. Selecione **Monitor e gerir** na sua página de base de dados fábrica do mesmo. Na vista de árvore, selecione o **conjunto de dados de saída**. Na lista de **Atividade Windows** , selecione a atividade de cópia executar. **Atividade Windows** listas a duração de atividade de cópia e o tamanho dos dados que são copiados. O débito está listado na **Atividade janela Explorador**. Para saber mais sobre a aplicação, consulte o artigo [Monitor e gerir tubagens Azure fábrica de dados ao utilizar a monitorização e gestão de aplicação](data-factory-monitor-manage-app.md).

    ![Executar detalhes de atividade](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

    Posteriormente neste artigo, pode comparar o desempenho e configuração do seu cenário a de atividade de cópia [referência de desempenho](#performance-reference) de nossos testes.
2. **Diagnosticar e otimizar o desempenho**. Se o desempenho que é observar não cumprir as suas expectativas, terá de identificar congestionamentos de desempenho. Em seguida, otimizar o desempenho para remover ou reduzir o efeito de congestionamentos. Uma descrição completa dos diagnóstico de desempenho está fora do âmbito deste artigo, mas eis algumas considerações comuns:
    - Funcionalidades de desempenho:
        - [Cópia paralela](#parallel-copy)
        - [Unidades de movimento de dados na nuvem](#cloud-data-movement-units)
        - [Copiar faseada](#staged-copy)   
    - [Origem](#considerations-for-the-source)
    - [Receber](#considerations-for-the-sink)
    - [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
    - [Compressão](#considerations-for-compression)
    - [Mapeamento de colunas](#considerations-for-column-mapping)
    - [O Data Management Gateway](#considerations-for-data-management-gateway)
    - [Outras considerações](#other-considerations)

3. **Expandir a configuração para todo o conjunto de dados**. Quando estiver satisfeito com os resultados da execução e desempenho, pode expandir a definição e período ativo de tubagem para cobrir todo o conjunto de dados.

## <a name="considerations-for-the-source"></a>Considerações para a origem
### <a name="general"></a>Geral
Certifique-se de que o arquivo de dados subjacentes não é por sise outras das cargas de trabalho que estiver a executar no ou contra-lo. 

Para arquivos de dados da Microsoft, consulte o artigo [monitorização e optimização tópicos](#performance-reference) que sejam específicas arquivos de dados e ajuda a que compreender dados armazenam características de desempenho, minimizar tempos de resposta e maximizar o débito.

Se copiar dados a partir do armazenamento de BLOBs para armazém de dados SQL, considere utilizar **PolyBase** para aumentar o desempenho. Consulte o artigo [Utilizar PolyBase para carregar os dados armazém de dados do SQL Azure](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter detalhes.


### <a name="file-based-data-stores"></a>Arquivos de dados com base no ficheiro
*(Inclui o armazenamento de BLOBs, dados Lake loja, Amazon S3, sistemas de ficheiros no local e HDFS no local)*

- **Tamanho do ficheiro média e contagem de ficheiros**: copiar atividade transfere um ficheiro de dados, uma vez. Com a mesma quantidade de dados para ser movida, o débito geral é inferior se os dados consiste em muitos ficheiros pequenos em vez de alguns ficheiros grandes devido a fase de arranque para cada ficheiro. Por isso, se possível, combine ficheiros pequenos ficheiros maiores para obter mais elevado débito.
- **Formato de ficheiro e a compressão**: para obter mais formas melhorar o desempenho, consulte as secções [Considerações para serialização e desserialização](#considerations-for-serialization-and-deserialization) e [Considerações sobre a compressão](#considerations-for-compression) .
- Para o cenário de **sistema de ficheiros no local** em que o **Data Management Gateway** é necessária, consulte a secção [Considerações para data Management Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Arquivos de dados relacionais
*(Inclui a base de dados SQL; Armazém de dados SQL; Amazon Redshift; Bases de dados do SQL Server; e Oracle, bases de dados do MySQL, DB2, Teradata, Sybase e PostgreSQL, etc.)*

- **Padrão de dados**: O esquema de tabela afeta débito de cópia. Um tamanho de linha grandes dá-lhe um melhor desempenho do que o tamanho da linha pequeno, para copiar a mesma quantidade de dados. O motivo é que a base de dados forma mais eficiente, pode obter menos lotes de dados que contenham linhas menos.
- **Consulta ou um procedimento armazenado**: otimizar a lógica da consulta ou procedimento armazenado que especificou na origem de atividade de cópia para obter dados de forma mais eficiente.
- Para **bases de dados relacionais no local**, como SQL Server e Oracle, que necessitam da utilização do **Data Management Gateway**, consulte a secção [Considerações para data Management Gateway](#considerations-on-data-management-gateway) .

## <a name="considerations-for-the-sink"></a>Considerações para o sink

### <a name="general"></a>Geral
Certifique-se de que o arquivo de dados subjacentes não é por sise outras das cargas de trabalho que estiver a executar no ou contra-lo. 

Para arquivos de dados da Microsoft, consulte a [monitorização e optimização tópicos](#performance-reference) que sejam específicas arquivos de dados. Estes tópicos podem ajudar a compreender as características de desempenho de arquivo de dados e como minimizar tempos de resposta e maximizar o débito.

Se estiver a copiar dados a partir do **armazenamento de BLOBs** para **Armazém de dados SQL**, considere utilizar **PolyBase** para aumentar o desempenho. Consulte o artigo [Utilizar PolyBase para carregar os dados armazém de dados do SQL Azure](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter detalhes.


### <a name="file-based-data-stores"></a>Arquivos de dados com base no ficheiro
*(Inclui o armazenamento de BLOBs, dados Lake loja, Amazon S3, sistemas de ficheiros no local e HDFS no local)*

- **Comportamento de cópia**: Se copiar dados a partir de um arquivo de dados com base no ficheiro diferentes, atividade de cópia tem três opções através da propriedade **copyBehavior** . Preserva hierarquia, organiza hierarquia ou intercala ficheiros. Em preservação ou atenuamento hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas a intercalar os ficheiros faz com que uma sobrecarga de desempenho aumentar.
- **Formato de ficheiro e a compressão**: consulte as secções [Considerações para serialização e desserialização](#considerations-for-serialization-and-deserialization) e [Considerações sobre a compressão](#considerations-for-compression) para saber mais formas melhorar o desempenho.
- **Armazenamento de BLOBs**: atualmente, apenas suporta de armazenamento de BLOBs bloquear blobs para transferência de dados otimizada e débito.
- **Sistemas de ficheiros no local** em cenários que necessitam da utilização do **Data Management Gateway**, consulte a secção [Considerações para data Management Gateway](#considerations-for-data-management-gateway) .

### <a name="relational-data-stores"></a>Arquivos de dados relacionais
*(Que inclui a base de dados SQL, armazém de dados SQL, bases de dados do SQL Server e bases de dados Oracle)*

- **Copiar o comportamento**: consoante as propriedades que definiu para **sqlSink**, atividade de cópia escreve dados para a base de dados de destino de diferentes formas.
    - Por predefinição, as utilizações de serviço de movimento dados API cópia em volume para inserir dados acrescente o modo, que fornece o melhor desempenho.
    - Se configurar um procedimento armazenado no sink, a base de dados aplica-se a uma linha de dados uma vez em vez de um carregamento em volume. Desempenho desce significativamente. Se o conjunto de dados for grande, quando aplicável, considere mudar para o utilizando a propriedade **sqlWriterCleanupScript** .
    - Se configurar a propriedade **sqlWriterCleanupScript** para cada actividade de cópia executar, o serviço de accionadores o script e, em seguida, utilizar a API de cópia em volume para inserir os dados. Por exemplo, para substituir toda a tabela com os dados mais recentes, pode especificar um script para primeiro eliminar todos os registos antes dos novos dados a partir da origem de carregamento em massa.
- **Dados padrão e lote o tamanho**:
    - Esquema de tabela afeta débito de cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grandes dá-lhe melhor desempenho do que um tamanho de linha pequenas porque a base de dados mais eficientemente possível consolidar lotes menos de dados.
    - Atividade de cópia insere os dados numa série de secções. Pode definir o número de linhas num lote, utilizando a propriedade **writeBatchSize** . Se os seus dados tiverem pequenas linhas, pode definir a propriedade **writeBatchSize** com um valor superior para beneficiar dos gerais de lote inferior e superior débito. Se o tamanho de linha dos seus dados for grande, tenha cuidado quando aumentar o **writeBatchSize**. Um valor alto pode conduzir a uma falha de cópia causada pela sobrecarga a base de dados.
- Para **bases de dados relacionais no local** , como o SQL Server e Oracle, que exigem a utilização do **Data Management Gateway**, consulte a secção [Considerações para data Management Gateway](#considerations-for-data-management-gateway) .


### <a name="nosql-stores"></a>NoSQL armazena
*(Inclui o armazenamento de tabela e Azure DocumentDB)*

- **Armazenamento de tabela**:
    - **Partição**: escrever dados significativamente a partições intercaladas diminui o desempenho. Ordenar os dados de origem com chave partição para que os dados são inseridos eficazmente uma partição após o outro ou ajustar a lógica para escrever os dados para uma única partição.
- Para **DocumentDB**:
    - **Tamanho do lote**: A propriedade **writeBatchSize** define o número de pedidos de paralelos ao serviço de DocumentDB para criar documentos. Que pode esperar um melhor desempenho quando aumentar **writeBatchSize** , porque mais pedidos paralelos são enviados para DocumentDB. No entanto, a aguardar limitação quando escreve DocumentDB (a mensagem de erro é "Pedir taxa for grande"). Vários fatores podem causar a limitação, incluindo o tamanho do documento, o número de termos em documentos e política indexação a coleção de destino. Para obter mais elevado débito de cópia, considere utilizar uma coleção de melhor, por exemplo, S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações para serialização e desserialização
Serialização e desserialização podem ocorrer ao seu conjunto de dados de entrada ou saída conjunto de dados é um ficheiro. Atualmente, a atividade de cópia suporta Avro e texto (por exemplo, CSV e TSV) formatos de dados.

**Copiar o comportamento**:

-   Copiar ficheiros entre arquivos de dados com base no ficheiro:
    - Quando a entrada e saídos conjuntos de dados ambos os têm o mesmo ou nenhuma definição de formato de ficheiro, o serviço de movimento dados executa uma cópia sem qualquer serialização ou desserialização binária. Verá um débito superior em comparação com o cenário, na qual as definições de formato de ficheiro de origem e sink são diferentes uns dos outros.
    - Quando de entrada e saída conjuntos de dados de ambas as está no formato de texto e apenas a codificação de tipo for diferente, o serviço de movimento de dados é apenas que codificação de conversão. Não ações qualquer serialização e desserialização, o que faz com que algumas desempenho sobrecarga em comparação comparado uma cópia binária.
    - Quando a entrada e saídos conjuntos de dados ambos os têm diferentes formatos de ficheiro ou configurações diferentes, como delimitadores, o serviço de movimento dados deserializes origem de dados para transmitir em fluxo, transformar e, em seguida, serializá-lo para o formato de saída que indicado. Esta operação produzir um desempenho muito mais significativo sobrecarga em comparação comparado outros cenários.
- Ao copiar ficheiros para a um arquivo de dados que não é baseada no ficheiro (por exemplo, a partir de uma loja com base no ficheiro para um arquivo relacional), o passo serialização ou desserialização é necessário. Este passo resulta no envio uma sobrecarga de desempenho significativa.

**Formato de ficheiro**: O formato de ficheiro que escolher poderá afetar o desempenho de cópia. Por exemplo, Avro está num formato binário compacto que armazena os metadados com os dados. Suporte abrangente-possui no ecossistema Hadoop para processar e consultar. No entanto, Avro é mais dispendioso para serialização e desserialização, que resulta numa inferior débito de cópia comparadas com o formato de texto. Tornar a sua escolha do formato de ficheiro em todo o fluxo de processamento holistically. Comece com o que os dados de formulário está armazenado no arquivos de dados de origem ou para ser extraídas de sistemas externos; o melhor formato para armazenamento, processamento analítico e consultar; e em que formato os dados devem ser exportados para marts de dados para relatórios e de visualização de ferramentas. Por vezes, um formato de ficheiro que é reduzido para ler e desempenho de escrita poderá ser uma boa escolha quando considerar o processo de analytical global.

## <a name="considerations-for-compression"></a>Considerações sobre a compressão
Quando o conjunto de dados de entrada ou saído é um ficheiro, pode definir cópia atividade para desempenhar compressão ou descompressão à medida que escreve dados para o destino. Quando escolhe compressão, efetuar uma variação entre a entrada/saída (e/s) e CPU. Comprimir os custos de dados extra na cluster recursos. Mas em troca, reduz armazenamento e de rede e/s. Consoante os seus dados, poderá ver uma intensidade nas débito cópia global.

**Codec**: copiar atividade suporta gzip, bzip2 e os tipos de compressão Deflate. Azure HDInsight pode consumir todos os três tipos de processamento. Cada codec de compressão tem vantagens. Por exemplo, bzip2 tem o débito cópia mais baixo, mas obter o melhor desempenho de consulta ramo com bzip2, uma vez que pode dividi-la para processamento. GZip é a opção mais equilibrada e são utilizada mais frequentemente. Selecione o codec que melhor se adequa ao seu cenário de fim para fim.

**Nível**: pode escolher entre duas opções para cada codec de compressão: comprimidos mais rápida e optimizada comprimido. A opção comprimida mais rápida comprime os dados mais rapidamente possível, mesmo se o ficheiro resultante não está optimizada comprimido. A opção ideal comprimida demora mais tempo a compressão do e produz uma quantidade mínima de dados. Pode testar ambas as opções para ver que fornece um melhor desempenho geral no seu caso.

**Considerações A**: para copiar uma grande quantidade de dados entre um arquivo no local e na nuvem, considere utilizar o armazenamento de BLOBs intercalar com a compressão. Utilizar o armazenamento provisório é útil quando a largura de banda de rede da sua empresa e os seus serviços Azure é o factor de limitação e pretende que o conjunto de dados de entrada e conjunto de dados de saída ambos os ser no formulário não comprimido. Mais especificamente, pode dividi atividade uma única cópia em atividades de duas cópia. Copia à primeira atividade de cópia da origem para um blob provisório ou transição no formulário comprimido. A segunda atividade de cópia copia os dados comprimidos transição e, em seguida, descomprime enquanto escreve o sink.

## <a name="considerations-for-column-mapping"></a>Considerações para mapeamento de colunas
Pode definir a propriedade **columnMappings** numa atividade de cópia para todos os mapa ou um subconjunto das colunas entradas para as colunas de saída. Depois do serviço de movimento dados lê os dados a partir da origem, necessita de executar o mapeamento de colunas nos dados antes de escreve os dados o sink. Este processamento extra reduz o débito de cópia.

Se os dados de origem armazenam é como consultáveis, por exemplo, se for um arquivo relacional como base de dados SQL ou do SQL Server, ou se é um arquivo de NoSQL como o armazenamento de tabela ou DocumentDB, considere conduza a filtragem de coluna e a reordenação lógica para a propriedade de **consulta** em vez de utilizar o mapeamento de colunas. Desta forma, a projecções ocorrem enquanto o serviço de movimento dados lê dados a partir da loja de dados de origem, onde é muito mais eficiente.

## <a name="considerations-for-data-management-gateway"></a>Considerações para o Data Management Gateway
Para obter recomendações de configuração de Gateway, consulte o artigo [Considerações para utilizar o Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Ambiente de máquina de gateway**: Recomendamos que utilize uma máquina dedicada ao anfitrião do Data Management Gateway. Utilizar ferramentas de como o desempenho examinar a utilização da CPU, memória e largura de banda durante uma operação de cópia no seu computador de Gateway. Mudar para uma máquina mais eficazes se CPU, a memória ou a largura de banda de rede torna-se um congestionamento.

**É executado em simultâneo de cópia de atividade**: uma única instância do Data Management Gateway pode servir vários cópia atividade será executado em simultâneo ou em simultâneo. O número máximo de tarefas em simultâneo é calculado baseada na configuração do hardware máquina de Gateway. Tarefas de cópia adicionais são colocadas na fila até que são recolhidas para cima por Gateway ou até que a outra tarefa o tempo limite. Para evitar contenção de recursos na máquina de Gateway, pode testar a sua agenda de atividade de cópia para reduzir o número de tarefas de cópia na fila de cada vez ou, considere dividir a carregar para os vários computadores de Gateway.


## <a name="other-considerations"></a>Outras considerações
Se o tamanho dos dados que pretende copiar for grande, pode ajustar a lógica empresarial para criar mais partições os dados utilizando o mecanismo slicing numa fábrica de dados. Em seguida, agende cópia atividade para executar com mais frequência para reduzir o tamanho de dados para cada actividade de cópia executar.

Tenha cuidado o número de conjuntos de dados e as atividades de cópia que exige o fábrica de dados à connector para o arquivo de dados mesmo ao mesmo tempo. Muitos trabalhos de cópia em simultâneo poderão optimizar um arquivo de dados e conduzir a desempenho degradado, tentativas interno tarefa de cópia e em alguns casos, falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: cópia de um servidor do SQL no local ao armazenamento Blob
**Cenário**: uma tubagem baseia-se para copiar dados do SQL Server no local ao armazenamento Blob no formato CSV. Para tornar a tarefa de cópia mais rápido, ficheiros CSV deverão ser comprimidos bzip2 formato.

**Teste e análise**: débito da atividade de cópia é menor que 2 MBps, que é muito mais lento do que o valor de referência de desempenho.

**Análise de desempenho e Otimização do**: para resolver o problema de desempenho, vamos ver como os dados são processados e movidos.

1.  **Ler dados**: Gateway abre uma ligação para o SQL Server e envia a consulta. SQL Server responde ao enviar o fluxo de dados para o Gateway através da intranet.
2.  **Dados Serialize e comprimir**: Gateway serializes o fluxo de dados para o formato CSV e comprime os dados a uma sequência de bzip2.
3.  **Escrever dados**: o Gateway carrega a sequência de bzip2 ao armazenamento Blob do através da Internet.

Como pode ver, os dados está a ser processadas e movido de uma forma sequencial transmissão: SQL Server > LAN > Gateway > WAN > Blob armazenamento. **O desempenho geral é controlado pelo débito mínimo através do pipeline de**.

![Fluxo de dados](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores poderão fazer com que a redução do desempenho:

-   **Origem**: o SQL Server propriamente dito tem baixo débito devido a cargas muito carregadas.
-   **O Data Management Gateway**:
    -   **LAN**: Gateway está localizado longe do computador do SQL Server e tiver uma ligação de largura de banda baixa.
    -   **Gateway**: Gateway atingiu suas limitações de carga para realizar as seguintes operações:
        -   **Serialização**: serializar o fluxo de dados para o formato CSV tem débito lento.
        -   **Compressão**: optar por um codec de compressão lenta (por exemplo, bzip2, que é 2,8 MBps com núcleo i7).
    -   **WAN**: A largura de banda entre à rede empresarial e os seus serviços Azure está baixa (por exemplo, T1 = 1,544 kbps; T2 = 6,312 kbps).
-   **Receber**: tem de armazenamento de BLOBs baixo débito. (Este cenário é provável porque o respetivo SLA garantias um mínimo de 60 MBps.)

Neste caso, compressão de dados bzip2 poderá ser lento pipeline de todo. Mudar para um codec de compressão gzip poderá facilidade este congestionamento.


## <a name="sample-scenarios-use-parallel-copy"></a>Cenários de exemplo: Utilize a funcionalidade copiar paralela  

**Cenário i:** Copie 1.000 1 MB ficheiros do sistema de ficheiros no local ao armazenamento Blob.

**Análise e Otimização do desempenho**: por exemplo, se tiver instalado o gateway num computador Quadrilátero com núcleo, dados fábrica utiliza 16 cópias paralelas para mover os ficheiros do sistema de ficheiros ao armazenamento Blob em simultâneo. Este execução paralela deve resultar em alto débito. Também pode especificar explicitamente a contagem de cópias paralelo. Quando copia muitos ficheiros pequenos, paralelas cópias ajudam significativamente débito utilizando recursos de forma mais eficaz.

![Cenário 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Cenário II**: copiar 20 blobs de 500 MB de armazenamento de BLOBs para a análise de dados Lake arquivo e, em seguida, o desempenho do.

**Análise e Otimização do desempenho**: neste cenário, fábrica de dados copia os dados armazenamento de Blobs do arquivo de dados de Lake utilizando única cópia (**parallelCopies** definido como 1) e as unidades de movimento único nuvem dados. O que é observar de débito serão perto que descritos na [secção de referência de desempenho](#performance-reference).   

![Cenário 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Cenário III**: tamanho de ficheiro Individual é maior do que dezenas de MB e total volume é grande.

**Análise e ativar o registo de desempenho**: aumentar **parallelCopies** não origina um melhor desempenho cópia devido às limitações de recursos do DMU único nuvem. Em vez disso, deverá especificar mais nuvem DMUs para obter mais recursos para executar o movimento de dados. Não especificar um valor para a propriedade **parallelCopies** . Dados fábrica trata paralelismo por si. Neste caso, se definir **cloudDataMovementUnits** para 4, de caudal cerca quatro vezes ocorre.

![Cenário 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referência
Seguem-se o desempenho de monitorização e Otimização do referências para algumas das lojas dados suportadas:

- Armazenamento Azure (incluindo armazenamento de BLOBs e armazenamento de tabela): [destinos escalabilidade de armazenamento do Windows Azure](../storage/storage-scalability-targets.md) e [desempenho de armazenamento do Windows Azure e lista de verificação escalabilidade](../storage//storage-performance-checklist.md)
- Base de dados SQL Azure: Pode [monitorizar o desempenho](../sql-database/sql-database-service-tiers.md#monitoring-performance) e verifique a percentagem de unidade (DTU) da transação da base de dados
- Armazém de dados do SQL Azure: A sua capacidade de é medida em unidades de armazém de dados (DWUs); consulte o artigo [Gerir calcular power no armazém de dados do SQL Azure (descrição geral)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
- Azure DocumentDB: [níveis de desempenho no DocumentDB](../documentdb/documentdb-performance-levels.md)
- Do SQL Server no local: [Monitor do computador e optimizar o desempenho](https://msdn.microsoft.com/library/ms189081.aspx)
- Servidor de ficheiros no local: [Otimização do desempenho do servidores de ficheiros](https://msdn.microsoft.com/library/dn567661.aspx)
