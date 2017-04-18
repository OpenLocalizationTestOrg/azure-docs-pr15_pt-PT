## <a name="fileshare-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados de partilha de ficheiros

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](../articles/data-factory/data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados. 

A secção **typeProperties** é diferente para cada tipo de conjunto de dados. Fornece informações que seja específicas com o tipo de conjunto de dados. A secção de typeProperties para um conjunto de dados do tipo de **partilha de ficheiros de** conjunto de dados tem as seguintes propriedades:

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
caminhopasta | Caminho de sub para a pasta. Utilizar o caráter de escape ' \ ' para carateres especiais na cadeia. Consulte o artigo [exemplo ligadas definições de serviço e conjunto de dados](#sample-linked-service-and-dataset-definitions) para obter exemplos.<br/><br/>Pode combinar esta propriedade com **partitionBy** ter caminhos de pastas com base no setor tempos de data de início/fim. | Sim
nome de ficheiro | Especifique o nome do ficheiro na **caminhopasta** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar um valor para esta propriedade, a tabela que aponta para todos os ficheiros na pasta.<br/><br/>Quando não é especificado um nome de ficheiro de um conjunto de dados de saída, o nome do ficheiro gerado seria nas seguintes neste formato: <br/><br/>Dados. <Guid>. txt (exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | N
partitionedBy | partitionedBy pode ser utilizado para especificar uma caminhopasta dinâmica, nome de ficheiro para os dados da série de tempo. Por exemplo, caminhopasta parametrizada para cada hora de dados. | N
Formatar | São suportados os seguintes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**e **OrcFormat**. Defina a propriedade de **tipo** em Formatar para um destes valores. Consulte as secções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificando JsonFormat](#specifying-jsonformat)e [Especificando OrcFormat](#specifying-orcformat) para obter detalhes. Se pretender copiar os ficheiros como-é entre lojas com base no ficheiro (cópia binária), pode ignorar a secção formato em ambas as definições do conjunto de dados de entrada e saída. | N
fileFilter | Especificar um filtro para ser utilizado para selecionar um subconjunto de ficheiros na caminhopasta em vez de todos os ficheiros.<br/><br/>Permitido valores são: `*` (múltiplos carateres) e `?` (caráter individual).<br/><br/>Exemplos 1:`"fileFilter": "*.log"`<br/>Exemplo 2:`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter é aplicável para um conjunto de dados de partilha de ficheiros entrado. Esta propriedade não é suportada com HDFS.  | N
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip**, **Deflate**e **BZip2** e níveis de suportados são: **Optimal** e **mais rápida**. Atualmente, as definições de compressão não são suportadas para dados **AvroFormat** ou **OrcFormat**. Para mais informações, consulte a secção de [suporte de compressão](#compression-support) .  | N |
| useBinaryTransfer | Especificar se utilizar o modo de transferência binária. Verdadeiro para modo binário e ASCII FALSO. Valor predefinido: VERDADEIRO. Esta propriedade apenas pode ser utilizada quando tipo de serviço ligadas associado é tipo: ServidorFTP. | N | 
 

> [AZURE.NOTE] nome de ficheiro e fileFilter não podem ser utilizados em simultâneo.

### <a name="using-partionedby-property"></a>Utilizar a propriedade partionedBy

Tal como mencionado na secção anterior, pode especificar uma caminhopasta dinâmica, nome de ficheiro de dados da série de tempo com partitionedBy. Pode fazê-lo com as macros de dados fábrica e a variável do sistema SliceStart, SliceEnd indicar o período de tempo lógicos para um setor dados indicada. 

Para saber mais sobre os conjuntos de dados de série de tempo, agendamento e os setores do gráfico, consulte o artigo [Criar conjuntos de dados](../articles/data-factory/data-factory-create-datasets.md), [agendamento & execução](../articles/data-factory/data-factory-scheduling-and-execution.md)e [Tubagens criar](../articles/data-factory/data-factory-create-pipelines.md) artigos. 

#### <a name="sample-1"></a>Exemplo 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Neste exemplo {setor} é substituído com o valor da variável do sistema SliceStart fábrica de dados no formato (YYYYMMDDHH) especificado. O SliceStart que se refere a hora de início no setor. A caminhopasta é diferente para cada setor. Exemplo: wikisampledataout/wikidatagateway/2014100103 ou wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2"></a>Exemplo 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Neste exemplo, ano, mês, dia e hora da SliceStart são extraídos para variáveis separadas que são utilizadas pelo propriedades caminhopasta e nome de ficheiro.
