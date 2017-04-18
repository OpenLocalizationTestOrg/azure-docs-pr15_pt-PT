## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Especificar a definição de estrutura para conjuntos de dados retangular
A secção estrutura no conjuntos de dados JSON é uma secção **opcional** para tabelas retangular (com linhas e colunas) e contém um conjunto de colunas da tabela. Irá utilizar a secção estrutura para uma das informações do tipo de fornecer para conversões de tipo de ou efetuar mapeamentos de colunas. As secções seguintes descrevem estas funcionalidades em maior detalhe. 

Cada coluna contém as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| nome | Nome da coluna. | Sim |
| tipo | Tipo de dados da coluna. Consulte a secção de conversões de tipo abaixo para mais detalhes sobre quando deve que especificar informações do tipo de | N |
| cultura | .NET com base cultura para ser utilizado quando tipo é especificado e é o tipo de .NET Datetime ou Datetimeoffset. A predefinição é "en-us".  | N |
| Formatar | Cadeia do formato para ser utilizado quando tipo é especificado e é .NET escreva Datetime ou Datetimeoffset. | N |

O exemplo seguinte mostra a secção estrutura JSON para uma tabela que tem três colunas ID de utilizador, o nome e lastlogindate.

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

Utilize as seguintes diretrizes para quando incluir informações "estrutura" e o que incluir na secção de **estrutura** .

- **Para origens de dados estruturadas** que armazenam informações de esquema e tipo de dados juntamente com os dados propriamente ditos (origens como tabela do Azure SQL Server, Oracle, etc.), deve especificar a secção "estrutura" apenas se pretender que efetue o mapeamento de colunas específicas de colunas de origem para colunas específicas sink e os seus nomes não são iguais (Consulte detalhes na secção de mapeamento de coluna abaixo). 

    Tal como mencionado acima, é opcional o tipo de informações na secção "estrutura". Para obter estruturadas fontes, informações do tipo de já se encontra disponíveis como parte da definição do conjunto de dados no arquivo de dados, pelo que não deverá incluir informações do tipo de quando incluir a secção "estrutura".
- **Para esquema em origens de dados de leitura (especificamente BLOBs do Azure)** pode optar por armazenar dados sem armazenar quaisquer informações de esquema ou tipo com os dados. Para estes tipos de origens de dados deve incluir "estrutura" nos casos seguintes 2:
    - Que pretende fazer mapeamento da coluna.
    - Quando o conjunto de dados é uma fonte numa atividade de cópia, pode fornecer informações do tipo de "estrutura" e fábrica dados utilizará estas informações de tipo para a conversão para tipos de nativos para o sink. Consulte o artigo [mover os dados de Blobs do Azure e para](../articles/data-factory/data-factory-azure-blob-connector.md) artigo para obter mais informações.

### <a name="supported-net-based-types"></a>Suportada. Tipos de líquido 
Fábrica dados suporta os seguintes CLS compatível com .NET com base no tipo valores para fornecer informações do tipo de "estrutura" para esquema em origens de dados de leitura como BLOBs do Azure.

- Int16
- Int32 
- Int64
- Única
- Dupla
- Decimal
- Byte]
- Booleano
- Cadeia 
- GUID
- Data/hora
- Datetimeoffset
- TimeSpan 

Opcionalmente, também pode especificar cadeia "Cultura" & "formato" para facilitar a análise da sua cadeia de data/hora personalizada para Datetime & Datetimeoffset. Consulte o artigo exemplo de conversão abaixo.

