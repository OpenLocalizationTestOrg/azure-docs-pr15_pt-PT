<properties 
    pageTitle="Notas de lançamento do Data Management Gateway | Dados Azure fábrica" 
    description="Notas de lançamento do dados data Management Gateway tory" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="spelluru"/>

# <a name="release-notes-for-data-management-gateway"></a>Notas de lançamento do Data Management Gateway
Uma dos desafios integração de dados moderna é mover forma totalmente integrada dados de e para a no local à nuvem. Dados fábrica faz com que esta integração totalmente integrada com o Data Management Gateway, que é um agente que pode instalar no local para permitir movimento de dados híbrido.

Consulte os artigos seguintes para obter informações detalhadas sobre o Data Management Gateway e como utilizá-la: 

- [O Data Management Gateway](data-factory-data-management-gateway.md)
- [Mover dados entre no local e na nuvem utilizando a fábrica de dados do Azure](data-factory-move-data-between-onprem-and-cloud.md) 

## <a name="current-version-2260721"></a>Versão atual (2.2.6072.1)

- Suporta a definição de proxy HTTP para o gateway através do Gestor de configuração de Gateway. Se configurado, BLOBs do Azure, tabela do Azure, Lake de dados do Azure e DB do documento são acedidos através do HTTP proxy.
- Cabeçalho de suporta processamento para TextFormat ao copiar dados ou para BLOBs do Azure, Azure arquivo de Lake de dados, no local sistema de ficheiros e, no local HDFS.
- Suporta copiar dados a partir de acrescentar Blob e página Blob juntamente com o Blob bloco já suportados.
- Apresenta um novo estado do gateway **Online (limitado)**, que indica que a funcionalidade principal do gateway funciona exceto o suporte de operação interativo para copiar o assistente.
- Melhora a robustez do registo de gateway utilizando a chave de registo.

## <a name="earlier-versions"></a>Versões anteriores

## <a name="2160401"></a>2.1.6040.1

- Controlador DB2 é incluído no pacote de instalação do gateway agora. Não tem de instalá-lo em separado. 
- Controlador DB2 suporta agora z/SO e DB2 para posso (como / 400) juntamente com as plataformas suportadas já (Linux, Unix e Windows). 
- Suporta utilizar DocumentDB como uma origem ou de destino para arquivos de dados no local
- Suporta a copiar dados a partir de/para constipação/quente blob armazenamento juntamente com a conta de armazenamento de uso geral já suportado. 
- Permite-lhe ligar ao servidor do SQL no local através do gateway com privilégios de início de sessão remoto.  

## <a name="2060131"></a>2.0.6013.1

- Pode selecionar a idioma cultura para ser utilizado por um gateway durante a instalação manual.
- Quando o gateway não funciona como esperado, pode optar por enviar registos de gateway dos últimos sete dias para a Microsoft para facilitar a resolução de problemas do problema. Se o gateway não está ligado ao serviço em nuvem, pode optar por guardar e arquivar os registos de gateway.  
- Melhorias de interface de utilizador para o Gestor de configuração de gateway:
    - Tornar o estado do gateway mais visíveis no separador base.
    - Controlos de reorganização e simplificados.
- Pode copiar dados a partir de um armazenamento utilizando o [código de royalties a ferramenta de pré-visualização de cópia](data-factory-copy-data-wizard-tutorial.md). Consulte o artigo [Testado copiar](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre esta funcionalidade no geral. 
- Pode utilizar o Data Management Gateway aos dados de penetração diretamente a partir de uma base de dados do SQL Server no local para o Azure máquina aprendizagem.
- Melhorias de desempenho
    - Melhora o desempenho sobre a visualização de esquema/pré-visualização do contra do SQL Server numa ferramenta de pré-visualização do código de royalties cópia.



## <a name="11259531"></a>1.12.5953.1
- Correções de erros

## <a name="11159181"></a>1.11.5918.1

- Tamanho máximo do registo de eventos de gateway foi aumentado 1 MB para 40 MB.
- É apresentada uma caixa de diálogo de aviso caso é necessário um reinício durante a actualização automática de gateway. Pode optar por reiniciar para a direita, em seguida, ou posterior. 
- No caso de falha da atualização automática, o instalador do gateway repetições de atualização automática três vezes no máximo.
- Melhorias de desempenho
    - Melhora o desempenho para o carregamento de tabelas grandes a partir do servidor no local num cenário de código de royalties cópia.
- Correções de erros

## <a name="11058921"></a>1.10.5892.1

- Melhorias de desempenho
- Correções de erros

## <a name="1958652"></a>1.9.5865.2

- Capacidade de actualização de automática zero tátil
- Ícone do tabuleiro do novo com indicadores de estado do gateway
- Capacidade de "Atualização agora" a partir do cliente
- Capacidade para definir a hora da agenda de atualização
- Script do PowerShell para alternar actualização automática no desativar
- Suporte para o formato JSON  
- Melhorias de desempenho
- Correções de erros

## <a name="1858221"></a>1.8.5822.1

- Melhorar a experiência de resolução de problemas
- Melhorias de desempenho
- Correções de erros

### <a name="1757951"></a>1.7.5795.1

- Melhorias de desempenho
- Correções de erros

### <a name="1757641"></a>1.7.5764.1

- Melhorias de desempenho
- Correções de erros

### <a name="1657351"></a>1.6.5735.1

- Suporte no local HDFS origem/Sink
- Melhorias de desempenho
- Correções de erros

### <a name="1656961"></a>1.6.5696.1

- Melhorias de desempenho
- Correções de erros

### <a name="1656761"></a>1.6.5676.1

- Ferramentas de diagnóstico de suporte no Gestor de configuração
- Suporte de colunas da tabela para origens de dados de tabela para fábrica de dados do Azure
- Suporte SQL DW para dados Azure fábrica
- Suporte Reclusive no BlobSource e FileSource para dados Azure fábrica
- Suporte CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy no BlobSink e FileSink com cópia binária para dados Azure fábrica
- Comunicar o progresso para Azure dados fábrica de atividade de cópia de suporte
- Validação conectividade de origem de dados do suporte para dados Azure fábrica
- Correções de erros


### <a name="1656721"></a>1.6.5672.1

- Nome da tabela para a origem de dados ODBC de suporte para fábrica de dados do Azure
- Melhorias de desempenho
- Correções de erros

### <a name="1656581"></a>1.6.5658.1

- Ficheiro de suporte receber para dados Azure fábrica
- Suporte preservação de hierarquia na cópia binária para fábrica de dados do Azure
- Copiar atividade Idempotency de suporte para dados Azure fábrica
- Correções de erros

### <a name="1656401"></a>1.6.5640.1

- Suporte 3 mais origens de dados para o Azure fábrica de dados (ODBC, OData, HDFS)
- Suporte carácter de aspas no analisador de csv para fábrica de dados do Azure
- Suporte de compressão (BZip2)
- Correções de erros

### <a name="1556121"></a>1.5.5612.1

- Suporte de cinco bases de dados relacionais do Azure dados fábrica (MySQL, PostgreSQL, DB2, Teradata e Sybase)
- Suporte de compressão (Gzip e Deflate)
- Melhorias de desempenho
- Correções de erros


### <a name="1455491"></a>1.4.5549.1

- Adicionar suporte de origem de dados Oracle para fábrica de dados do Azure
- Melhorias de desempenho
- Correções de erros

### <a name="1454921"></a>1.4.5492.1

- Binário unificado que suporta serviços fábrica de dados do Microsoft Azure e do Power BI para Office 365
- Otimizar o processo de configuração de IU e registo
- Azure fábrica de dados – suporte penetração Azure e de saída para a origem de dados do SQL Server

### <a name="1253031"></a>1.2.5303.1

-   Corrigir o problema de tempo limite para suportar ligações de origem de dados demorada mais. 
    
### <a name="1155268"></a>1.1.5526.8

- Requer .NET Framework 4.5.1 como um pré-requisito durante a configuração.

### <a name="1051442"></a>1.0.5144.2

- Sem alterações que afetam cenários de fábrica do Azure dados. 
