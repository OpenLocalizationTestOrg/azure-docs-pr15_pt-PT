## <a name="specifying-formats"></a>Especificar formatos

Azure dados fábrica suporta os seguintes tipos de formato: 

- [Formato de texto](#specifying-textformat)
- [Formato JSON](#specifying-jsonformat)
- [Formato de Avro](#specifying-avroformat)
- [Formato ORC](#specifying-orcformat)
- [Formato de parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Especificar TextFormat

Se o formato estiver definido para **TextFormat**, pode especificar as seguintes propriedades **opcional** na secção **formato** .

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | O caráter utilizado para separar colunas num ficheiro. | Apenas um caráter é permitido. O valor predefinido é uma vírgula (','). <br/><br/>Para utilizar um caráter Unicode, consulte [Carateres Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) para obter o código correspondente para a mesma. Por exemplo, pode considerar utilizar um CARÁCT não imprimível raro que provavelmente não existe nos seus dados: Especifique "\u0001" que representa o início do cabeçalho (SOH). | N |
| rowDelimiter | O caráter utilizado para separar linhas num ficheiro. | Apenas um caráter é permitido. O valor predefinido é qualquer um dos seguintes valores na leitura: ["\r\n", "\r", "\n"] e "\r\n" no escrita. | N |
| escapeChar | O caráter especial utilizado para escape delimitador coluna no conteúdo do ficheiro de entrada. <br/><br/>Não é possível especificar escapeChar e quoteChar para uma tabela. | Apenas um caráter é permitido. Nenhum valor predefinido. <br/><br/>Exemplo: Se tiver vírgulas (', ') como o delimitador de coluna, mas pretende que o caráter de ponto e vírgula no texto (exemplo: "Olá, mundo"), pode definir '$' como caráter de escape e utilizar a cadeia "$Olá, mundo" na origem. | N | 
| quoteChar | O caráter utilizado para um valor de cadeia da proposta. Os delimitadores de colunas e linhas dentro os carateres de proposta seriam tratados como parte do valor de cadeia. Esta propriedade é aplicável aos conjuntos de dados de entrada e saídos.<br/><br/>Não é possível especificar escapeChar e quoteChar para uma tabela. | Apenas um caráter é permitido. Nenhum valor predefinido. <br/><br/>Por exemplo, se tiver vírgulas (', ') como o delimitador de coluna, mas pretende que o caráter de vírgula no texto (exemplo: < Olá, mundo >), pode definir "(aspas duplas) como o caráter de proposta e utilize a cadeia"Olá, mundo"na origem. | N |
| nullValue | Um ou mais carateres utilizados para representar um valor nulo. | Um ou mais carateres. Os valores predefinidos estão "\N" e "Nulo" em "\N" de leitura e de escrita. | N |
| encodingName | Especifique o nome de codificação. | Um nome de codificação válido. consulte o artigo [Encoding.EncodingName propriedade](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemplo: windows 1250 ou shift_jis. O valor predefinido é UTF-8. | N | 
| Primeiralinhacomocabeçalho | Especifica se a ter em conta a primeira linha como cabeçalho. Para um conjunto de dados de entrada, fábrica de dados lê primeira linha como cabeçalho. Para um conjunto de dados de saída, dados fábrica escreve primeira linha como cabeçalho. <br/><br/>Consulte o artigo [cenários para utilizar **Primeiralinhacomocabeçalho** e **skipLineCount** ](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. | VERDADEIRO<br/>FALSO (predefinição) | N |
| skipLineCount | Indica o número de linhas para ignorar durante a leitura de dados a partir de ficheiros de entrada. Se forem especificados skipLineCount e Primeiralinhacomocabeçalho, as linhas são ignoradas pela primeira vez e, em seguida, as informações de cabeçalho são de leitura do ficheiro de entrada. <br/><br/>Consulte o artigo [cenários para utilizar Primeiralinhacomocabeçalho e skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) para cenários de exemplo. | Número inteiro | N | 
| treatEmptyAsNull | Especifica se tratar de uma cadeia vazia ou nula como um valor nulo durante a leitura de dados a partir de um ficheiro de entrada. | VERDADEIRO (predefinição)<br/>FALSO | N |  

#### <a name="textformat-example"></a>Exemplo de TextFormat
O exemplo seguinte mostra algumas das propriedades de formato para TextFormat.

    "typeProperties":
    {
        "folderPath": "mycontainer/myfolder",
        "fileName": "myblobname"
        "format":
        {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";",
            "quoteChar": "\"",
            "NullValue": "NaN"
            "firstRowAsHeader": true,
            "skipLineCount": 0,
            "treatEmptyAsNull": true
        }
    },

Para utilizar uma escapeChar em vez de quoteChar, substitua a linha quoteChar com escapeChar o seguinte:

    "escapeChar": "$",



### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Cenários para utilizar Primeiralinhacomocabeçalho e skipLineCount

- Está a copiar a partir de uma origem de que não sejam ficheiro para um ficheiro de texto e gostaria de adicionar uma linha de cabeçalho que contém os metadados de esquema (por exemplo: o esquema SQL). Especificar **Primeiralinhacomocabeçalho** como verdadeiro no conjunto de dados de saída para este cenário. 
- Está a copiar a partir de um ficheiro de texto que contém uma linha de cabeçalho para um ficheiro de que não sejam sink e gostaria de largar nessa linha. Especificar **Primeiralinhacomocabeçalho** como verdadeiro no conjunto de dados de entrada.
- Está a copiar a partir de um ficheiro de texto e pretende ignorar linhas alguns no início, que não contêm dados ou de cabeçalho informações. Especifique **skipLineCount** para indicar o número de linhas para ser ignorada. Se o resto do ficheiro contém uma linha de cabeçalho, também pode especificar **Primeiralinhacomocabeçalho**. Se forem especificados **skipLineCount** e **Primeiralinhacomocabeçalho** , as linhas são ignoradas pela primeira vez e, em seguida, as informações de cabeçalho são de leitura do ficheiro de entrada

### <a name="specifying-avroformat"></a>Especificar AvroFormat
Se o formato estiver definido para AvroFormat, não terá de especificar as propriedades na secção formato dentro da secção typeProperties. Exemplo:

    "format":
    {
        "type": "AvroFormat",
    }

Para utilizar o formato de Avro numa tabela Hive, pode consultar a [Apache Hive do tutorial](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenha em atenção os seguintes pontos:  

- [Tipos de dados complexos](http://avro.apache.org/docs/current/spec.html#schema_complex) não são suportados (registos, enums, matrizes, mapas, trabalhadores e fixa)

### <a name="specifying-jsonformat"></a>Especificar JsonFormat

Se o formato estiver definido para **JsonFormat**, pode especificar as seguintes propriedades **opcional** na secção **formato** .

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| filePattern | Indica o padrão de dados armazenados em cada ficheiro JSON. Permitido os valores são: **setOfObjects** e **arrayOfObjects**. O valor **predefinido** é: **setOfObjects**. Consulte os seguintes secções para obter detalhes sobre estes padrões.| N |
| encodingName | Especifique o nome de codificação. Para a lista de nomes de codificação válidos, consulte o artigo: [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) propriedade. Por exemplo: windows 1250 ou shift_jis. O valor **predefinido** é: **UTF-8**. | N | 
| nestingSeparator | Caráter que é utilizado para separar os níveis de aninhamento. O valor predefinido é '.' (ponto). | N | 


#### <a name="setofobjects-file-pattern"></a>setOfObjects padrão de ficheiro

Cada ficheiro contém único objeto ou linha-delimitado por/concatenados vários objetos. Quando esta opção é escolhida num conjunto de dados de saída, atividade de cópia gera um único ficheiro JSON com cada objeto por linha (delimitado por linha).

**único objeto** 

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }

**JSON delimitado por linha** 

    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concatenado**

    {
        "time": "2015-04-29T07:12:20.9100000Z",
        "callingimsi": "466920403025604",
        "callingnum1": "678948008",
        "callingnum2": "567834760",
        "switch1": "China",
        "switch2": "Germany"
    }
    {
        "time": "2015-04-29T07:13:21.0220000Z",
        "callingimsi": "466922202613463",
        "callingnum1": "123436380",
        "callingnum2": "789037573",
        "switch1": "US",
        "switch2": "UK"
    }
    {
        "time": "2015-04-29T07:13:21.4370000Z",
        "callingimsi": "466923101048691",
        "callingnum1": "678901578",
        "callingnum2": "345626404",
        "switch1": "Germany",
        "switch2": "UK"
    }


#### <a name="arrayofobjects-file-pattern"></a>arrayOfObjects ficheiro padrão. 

Cada ficheiro contém uma matriz de objetos. 

    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "789037573",
            "callingnum2": "789044691",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:22.0960000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789044691",
            "switch1": "US",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:24.2120000Z",
            "callingimsi": "466922201102759",
            "callingnum1": "345698602",
            "callingnum2": "789097900",
            "switch1": "UK",
            "switch2": "Australia"
        },
        {
            "time": "2015-04-29T07:13:25.6320000Z",
            "callingimsi": "466923300236137",
            "callingnum1": "567850552",
            "callingnum2": "789086133",
            "switch1": "China",
            "switch2": "Germany"
        }
    ]

### <a name="jsonformat-example"></a>Exemplo de JsonFormat

Se tiver um ficheiro JSON com o seguinte conteúdo:  

    {
        "Id": 1,
        "Name": {
            "First": "John",
            "Last": "Doe"
        },
        "Tags": ["Data Factory”, "Azure"]
    }

e pretende copiá-la para uma tabela do Azure SQL no seguinte formato: 

ID  | Name.First | Name.Middle | Name.Last | Etiquetas
--- | ---------- | ----------- | --------- | ----
1 | João | nulo | Andrade | ["Dados Factory", "Azure"]

O conjunto de dados de entrada com o tipo de JsonFormat é definido da seguinte forma: (definição parcial com apenas as partes relevantes)

    "properties": {
        "structure": [
            {"name": "Id", "type": "Int"},
            {"name": "Name.First", "type": "String"},
            {"name": "Name.Middle", "type": "String"},
            {"name": "Name.Last", "type": "String"},
            {"name": "Tags", "type": "string"}
        ],
        "typeProperties":
        {
            "folderPath": "mycontainer/myfolder",
            "format":
            {
                "type": "JsonFormat",
                "filePattern": "setOfObjects",
                "encodingName": "UTF-8",
                "nestingSeparator": "."
            }
        }
    }

Se a estrutura não estiver definida, a atividade de cópia organiza a estrutura por predefinição e copie todas as coisa. 

#### <a name="supported-json-structure"></a>Estrutura JSON suportada
Tenha em atenção os seguintes pontos: 

- Cada objeto com um conjunto de pares valor/nome é mapeado para uma linha de dados num formato tabular. Objetos que podem ser aninhados e pode definir como aplanar a estrutura de um conjunto de dados com o separador de aninhamento (.) por predefinição. Veja o [exemplo JsonFormat](#jsonformat-example) anterior secção para obter um exemplo.  
- Se a estrutura não está definida no conjunto de dados fábrica de dados, a atividade de cópia Deteta o esquema no primeiro objeto e aplanar o objeto todo. 
- Se a entrada de dados JSON tiver uma matriz, a atividade de cópia converte o valor de matriz inteira numa cadeia. Pode optar por ignorá-lo utilizando o [mapeamento de colunas ou filtragem](#column-mapping-with-translator-rules).
- Se existirem nomes duplicados no mesmo nível, a atividade de cópia escolhe o último registo.
- Nomes de propriedade estão entre maiúsculas e minúsculas. Duas propriedades com o mesmo nome mas tripas diferentes são tratadas como duas propriedades em separado. 

### <a name="specifying-orcformat"></a>Especificar OrcFormat
Se o formato estiver definido para OrcFormat, não terá de especificar as propriedades na secção formato dentro da secção typeProperties. Exemplo:

    "format":
    {
        "type": "OrcFormat"
    }

> [AZURE.IMPORTANT] Se não estiver a copiar ficheiros ORC **como-é** entre no local e na nuvem arquivos de dados, tem de instalar o 8 JRE (Java Runtime ambiente) no seu computador de gateway. Um gateway de 64 bits requer JRE de 64 bits e gateway de 32 bits requer JRE de 32 bits. Pode encontrar ambas as versões de [aqui](http://go.microsoft.com/fwlink/?LinkId=808605). Escolha o adequado.

Tenha em atenção os seguintes pontos:

-   Dados complexos tipos não são suportados (estrutura, mapa, lista, União)
-   Ficheiro ORC tem três [Opções de compressão](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): nenhuma, ZLIB, SNAPPY. Dados fábrica suporta ler dados a partir de ficheiro ORC em qualquer um destes formatos comprimido. Utiliza a compressão codec é nos metadados ler os dados. No entanto, quando escrever para um ficheiro de ORC, dados fábrica escolhe ZLIB, qual é a predefinição para ORC. Atualmente, não existe nenhuma opção de substituir as este comportamento. 

### <a name="specifying-parquetformat"></a>Especificar ParquetFormat
Se o formato de estiver definido para ParquetFormat, não terá de especificar as propriedades na secção formato dentro da secção de typeProperties. Exemplo:

    "format":
    {
        "type": "ParquetFormat"
    }

> [AZURE.IMPORTANT] Se não estiver a copiar ficheiros de Parquet **como-é** entre no local e na nuvem arquivos de dados, tem de instalar o 8 JRE (Java Runtime ambiente) no seu computador de gateway. Um gateway de 64 bits requer JRE de 64 bits e gateway de 32 bits requer JRE de 32 bits. Pode encontrar ambas as versões de [aqui](http://go.microsoft.com/fwlink/?LinkId=808605). Escolha o adequado.

Tenha em atenção os seguintes pontos:

-   Dados complexos tipos não são suportados (mapa, lista)
-   Ficheiro de parquet tem as seguintes opções relacionadas com a compressão: nenhuma, SNAPPY, GZIP e LZO. Dados fábrica suporta ler dados a partir de ficheiro ORC em qualquer um destes formatos comprimido. Utiliza o codec de compressão nos metadados para ler os dados. No entanto, quando escrever para um ficheiro de Parquet, dados fábrica escolhe SNAPPY, que é a predefinição para o formato de Parquet. Atualmente, não existe nenhuma opção de substituir as este comportamento. 
