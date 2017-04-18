<properties 
    pageTitle="Sintaxe SQL e SQL da consulta para DocumentDB | Microsoft Azure" 
    description="Obter informações sobre sintaxe SQL, conceitos de base de dados e consultas SQL para DocumentDB, uma base de dados NoSQL. SQL pode utilizado como um idioma de consulta JSON no DocumentDB." 
    keywords="Sintaxe SQL, consulta sql, consultas sql, json linguagem de consulta, conceitos de base de dados e consultas de sql, funções de agregação"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="sql-query-and-sql-syntax-in-documentdb"></a>Consulta SQL e sintaxe SQL na DocumentDB
Microsoft Azure DocumentDB suporta documentos ao consultar utilizando SQL (Structured Query Language) como um idioma de consulta JSON. DocumentDB é verdadeiramente livre de esquema. Nos termos do seu compromisso ao modelo de dados JSON diretamente no motor de base de dados, fornece de indexação automática de documentos JSON sem necessidade de criação de índices secundários ou esquema explícita. 

Enquanto criava a linguagem de consulta para DocumentDB tivemos dois objectivos deve ter em conta:

-   Em vez de inventing um novo idioma de consulta JSON, queremos que suportam SQL. SQL é um dos idiomas de consulta mais familiar e populares. DocumentDB SQL fornece um modelo de programação formal formatadas consultas através de documentos JSON.
-   Como JSON documento base de dados capaz de executar JavaScript diretamente no motor da base de dados, queremos utilizar o modelo de programação do JavaScript como a Fundação de nossos linguagem de consulta. O SQL DocumentDB tem raiz no sistema de tipo de JavaScript, avaliação da expressão e invocação de função. No-ativar fornece um modelo de programação natural para projecções relacionais, a navegação hierárquico nos documentos JSON, as associações autonomamente, espaciais consultas e invocação de funções definidas pelo utilizador (UDFs) escrito completamente em JavaScript, entre outras funcionalidades. 

Vamos acreditar que estas capacidades estão tecla para reduzir a fricção entre a aplicação e a base de dados e são fundamentais para produtividade do programador.

Recomendamos que a introdução ao ver o vídeo seguinte, onde Aravind Ramachandran mostra as capacidades de consultar do DocumentDB, e acedendo a nossa [Parques de consulta](http://www.documentdb.com/sql/demo), onde pode experimentar DocumentDB e executar consultas SQL contra a nossa conjunto de dados.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Em seguida, regresse neste artigo, onde vamos começar com um tutorial de consulta SQL orienta-o através de alguns documentos JSON simples e comandos SQL.

## <a name="getting-started-with-sql-commands-in-documentdb"></a>Introdução ao comandos SQL na DocumentDB
Para ver DocumentDB SQL no trabalho, vamos começar com alguns documentos JSON simples e percorra algumas consultas simples contra-lo. Considere estes dois documentos JSON sobre famílias de tipos de duas. Tenha em atenção que com DocumentDB, não precisamos de criar qualquer esquemas ou índices secundários explicitamente. Vamos terão de inserir os documentos JSON para uma coleção de DocumentDB e subsequentemente de consulta. Aqui temos uma JSON simple de documentos para a família Andersen, os elementos principais, crianças (e os respetivos companhia), informações de endereço e registo. O documento tem cadeias, números, valores booleanos, matrizes e propriedades aninhadas. 

**Documento**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


Eis um segundo documento com uma diferença ténue – `givenName` e `familyName` são utilizados em vez de `firstName` e `lastName`.

**Documento**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



Agora vamos experimentar algumas consultas contra estes dados para compreender alguns dos elementos principais do DocumentDB SQL. Por exemplo, a seguinte consulta irá devolver os documentos que o campo id corresponde `AndersenFamily`. Uma vez que é um `SELECT *`, o resultado da consulta é o documento JSON concluído:

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Agora, considere as maiúsculas/minúsculas onde precisamos de reformatar o resultado da JSON numa forma diferente. Esta consulta projectos um novo objeto JSON com dois campos seleccionados, nome e Localidade, quando a localidade tem o mesmo nome como o estado. Neste caso, corresponde a "NY, NY".

**Consulta**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Resultados**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


A seguinte consulta devolve todos os nomes das crianças dado dentro da família cujo id corresponde ao `WakefieldFamily` ordenada pela cidade da residência.

**Consulta**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Gostaríamos de chamar a atenção para alguns aspetos relevantes DocumentDB linguagem de consulta através de exemplos que podemos visualizou até ao momento:  
 
-   Uma vez que DocumentDB SQL funciona em valores JSON, trata árvore em forma de entidades em vez de linhas e colunas. Por conseguinte, o idioma permite-lhe fazer referência a nós da árvore a qualquer profundidade arbitrária, tal como `Node1.Node2.Node3…..Nodem`, semelhante ao SQL relacional que fazem referência a referência duas parte da `<table>.<column>`.   
-   O idioma de consulta estruturada funciona com dados de menos de esquema. Por conseguinte, o sistema de tipo tem de estar vinculado dinamicamente. A mesma expressão poderia lucro diferentes tipos de documentos diferentes. O resultado de uma consulta for um valor JSON válido, mas não há garantia de ser de um esquema fixo.  
-   DocumentDB apenas suporta documentos JSON estritas. Isto significa que o sistema de tipo e expressões estão restritas fazer em relação apenas aos tipos JSON. Consulte a [especificação de JSON](http://www.json.org/) para obter mais detalhes.  
-   Uma coleção de DocumentDB é um contentor de esquema de royalties de documentos JSON. As relações no entidades de dados no interior e ao longo de documentos numa coleção de implicitamente são captadas por retenção e não por chave primária e relações de chaves externas. Este é um aspecto importante património destacam tendo em conta as associações de documento dentro outros fabricantes referidas neste artigo.

## <a name="documentdb-indexing"></a>DocumentDB indexação

Antes de recebemos para a sintaxe SQL de DocumentDB, vale explorar o design de indexação no DocumentDB. 

É o objetivo do índices de base de dados servir consultas no respetivo vários formulários e formas com consumo de recursos mínima (como CPU e entrada/saída) ao fornecer débito boa e a latência baixa. Muitas vezes, a escolha do índice de à direita para consultar uma base de dados requer muito planeamento e pioneira. Esta abordagem coloca um desafio para esquema menos de bases de dados onde os dados não está em conformidade com um esquema estritamente e evolução deste rapidamente. 

Por conseguinte, quando podemos concebido DocumentDB indexação subsystem, vamos definir os objetivos seguintes:

-   Indexar documentos sem necessidade de esquema: A indexação subsystem não necessitam de quaisquer informações de esquema ou fazer qualquer suposições acerca do esquema dos documentos. 

-   Suporte para consultas relacionais e hierárquicas eficientes e em formato RTF: O índice suporta a linguagem de consulta DocumentDB eficientemente, incluindo o suporte para projecções hierárquicos e relacionais.

-   Suporte para consultas consistentes in face of uma constante volume de gravações: para alta escrita débito das cargas de trabalho com consultas consistentes, o índice é actualizado incrementada, eficientemente e online face a um constante volume de gravações. A atualização de índice remissivo consistentes é crucial para servir as consultas ao nível de consistência no qual o utilizador configurado o serviço de documentos.

-   Suporte para multitenancy: atribuído o modelo de reserva com base governação recurso ao longo de inquilinos, atualizações de índice são executadas dentro do orçamento de recursos do sistema (CPU, memória e operações de entrada/saída por segundo) atribuídos por réplica. 

-   Eficiência de armazenamento: eficácia de custo, a sobrecarga de armazenamento no disco do índice remissivo é delimitada e previsíveis. O que é crucial porque DocumentDB permite o programador tornar os variações de custo com base entre índice sobrecarga em relação o desempenho da consulta.  

Consulte as [amostras DocumentDB](https://github.com/Azure/azure-documentdb-net) no MSDN para amostras que mostra como configurar a política para uma coleção de indexação. Vamos agora obter os detalhes da sintaxe SQL de DocumentDB.


## <a name="basics-of-a-documentdb-sql-query"></a>Noções básicas de uma consulta DocumentDB SQL
Cada consulta consiste numa cláusula SELECT e opcional FROM e WHERE cláusulas por normas ANSI SQL. Normalmente, para cada consulta, a origem na cláusula FROM é enumerada. Em seguida, o filtro na cláusula WHERE é aplicado a origem de obter um subconjunto dos documentos JSON. Por fim, a cláusula SELECT é utilizada para os valores JSON solicitados na lista de selecção de projeto.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a name="from-clause"></a>Cláusula FROM
O `FROM <from_specification>` cláusula é opcional, a menos que a origem é filtrada ou projectada mais tarde na consulta. É o objetivo desta cláusula especificar a origem de dados no qual tem a trabalhar com a consulta. Frequentemente toda a coleção é a origem, mas é possível especificar um subconjunto da coleção de em vez disso. 

Gostar de uma consulta `SELECT * FROM Families` indica que toda a colecção famílias é a origem através da qual enumerar. Um identificador especial raiz pode ser utilizado para representar a coleção de em vez de utilizar o nome da coleção. A lista seguinte contém as regras que são impostas por consulta:

- A coleção de pode ser com alias, tal como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é equivalente a `Families`. `AS`é o identificador de uma palavra-chave opcional para alias.

-   Não pode estar vinculada nota que uma vez com alias, a origem original. Por exemplo, `SELECT Families.id FROM Families f` é sintaxe inválido, uma vez que o identificador "Famílias de tipos de" já não pode ser resolvido.

-   Todas as propriedades que precisam de ser referenciada tem de ser completamente qualificadas. Em falta de adesão esquema restrita, isto é imposto para evitar quaisquer enlaces ambíguos. Por conseguinte, `SELECT id FROM Families f` é sintaxe inválido desde a propriedade `id` não está ligado.
    
### <a name="sub-documents"></a>Subendereço de documentos
Também pode ser reduzida a origem para um subconjunto mais pequeno. Por exemplo, para enumerar apenas uma sub-árvore de em cada documento, a sub-raiz poderia, em seguida, tornar-se a origem, conforme mostrado no seguinte exemplo.

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

O exemplo acima utilizados uma matriz, como a origem, um objeto também pode ser utilizado como a origem, que é o que é apresentado no seguinte exemplo. Qualquer válido JSON valor (não não definido) que pode ser encontrada na origem será considerado para inclusão no resultado da consulta. Se não tem mais algumas famílias de tipos de um `address.state` valor, serão excluídos no resultado da consulta.

**Consulta**

    SELECT * 
    FROM Families.address.state

**Resultados**

    [
      "WA", 
      "NY"
    ]


## <a name="where-clause"></a>Cláusula WHERE
A cláusula WHERE (**`WHERE <filter_condition>`**) é opcional. Especifica as condições que os documentos JSON fornecidos pela origem satisfazer para poder ser incluídos como parte do resultado. Qualquer documento JSON têm de ser avaliados as condições especificadas como "true" para ser consideradas para o resultado. A cláusula WHERE é utilizada pela camada de índice remissivo para determinar o subconjunto absoluto mais pequeno de documentos de origem que podem fazer parte do resultado. 

A seguinte consulta os pedidos de documentos que contenham uma propriedade de nome cujo valor é `AndersenFamily`. Qualquer outro documento que não tem uma propriedade de nome, ou onde o valor não corresponder a `AndersenFamily` é excluído. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


No exemplo anterior mostrava uma consulta de igualdade simples. DocumentDB SQL também suporta a uma variedade de expressões escalares. Utilizadas com mais frequência são expressões binários e unário. Referências de propriedades do objeto JSON origem de também são expressões válidas. 

Os seguintes operadores binários atualmente são suportados e podem ser utilizados em consultas, conforme mostrado nos exemplos seguintes:  
<table>
<tr>
<td>Média aritmética</td> 
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bit a bit</td>    
<td>|, &, ^, <<, >>, >>> (shift da direita preenchimento de zero) </td>
</tr>
<tr>
<td>Lógico</td>
<td>E, OU, NÃO</td>
</tr>
<tr>
<td>Comparação</td> 
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Cadeia</td> 
<td>|| (concatenar)</td>
</tr>
</table>  

Vamos dar uma olhadela a alguns consultas utilizando operadores binários.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Os operadores unário +,-, ~ e não também são suportados e podem ser utilizados no interior de consultas, conforme mostrado no exemplo seguinte:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Para além de binários e unário operadores, referências de propriedade também são permitidas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o documento JSON que contém a propriedade `isRegistered` em que o valor da propriedade é igual a JSON `true` valor. Quaisquer outros valores (FALSO, nulo, indefinido, `<number>`, `<string>`, `<object>`, `<array>`, etc.) oportunidades potenciais para o documento de origem a ser excluído do resultado. 

### <a name="equality-and-comparison-operators"></a>Operadores de igualdade e comparação
A tabela seguinte mostra o resultado de comparações de igualdade no DocumentDB SQL entre dois tipos de JSON.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Abrir</strong>
         </td>
         <td valign="top">
            <strong>Indefinido</strong>
         </td>
         <td valign="top">
            <strong>Nulo</strong>
         </td>
         <td valign="top">
            <strong>Booleano</strong>
         </td>
         <td valign="top">
            <strong>Número</strong>
         </td>
         <td valign="top">
            <strong>Cadeia</strong>
         </td>
         <td valign="top">
            <strong>Objecto</strong>
         </td>
         <td valign="top">
            <strong>Matriz</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Indefinido<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Nulo<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Booleano<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Número<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Cadeia<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objecto<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Indefinido </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Matriz<strong>
         </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
Indefinido </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Para outras operadores de comparação, como >, > =,! =, < e < =, as seguintes regras aplicam-se:   

-   Comparação entre tipos de resultados no indefinido.
-   Comparação entre dois objetos ou duas matrizes resultados na indefinido.   

Se o resultado da expressão escalar no filtro é não definido, o documento correspondente seria não será incluído no resultado, uma vez que indefinido logicamente não equivale a "true".

### <a name="between-keyword"></a>ENTRE palavras-chave
Também pode utilizar a palavra-chave entre para expressar consultas de intervalos de valores como ANSI SQL. Pode ser utilizado entre contra cadeias ou números.

Por exemplo, esta consulta devolve todos os documentos da família na qual o grau do primeiro subordinado é entre 1-5 (ambos inclusive). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Ao contrário no ANSI SQL, também pode utilizar a cláusula entre na cláusula FROM como no exemplo seguinte.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Para mais rápidos tempos de execução de consulta, lembre-se criar uma política de indexação que utiliza um tipo de índice remissivo intervalo contra quaisquer propriedades/caminhos numéricos são filtradas na cláusula entre. 

A diferença entre a utilização de entre no DocumentDB e o ANSI SQL principal é que pode expressar consultas de intervalo contra propriedades dos tipos de versões mistas – por exemplo, poderá ter de ser um número (5) "classe" em alguns documentos e cadeias em outras pessoas ("grade4"). Nestes casos, como em JavaScript, uma comparação entre dois tipos diferentes resultados "Indefinido" e o documento será ignorada.

### <a name="logical-and-or-and-not-operators"></a>Lógico (AND, OR e não) operadores
Trabalhar com os operadores lógicos em valores booleanos. As tabelas de verdade lógicas para estes operadores são apresentadas nas seguintes tabelas.

OU|VERDADEIRO|FALSO|Indefinido
---|---|---|---
VERDADEIRO|VERDADEIRO|VERDADEIRO|VERDADEIRO
FALSO|VERDADEIRO|FALSO|Indefinido
Indefinido|VERDADEIRO|Indefinido|Indefinido

E|VERDADEIRO|FALSO|Indefinido
---|---|---|---
VERDADEIRO|VERDADEIRO|FALSO|Indefinido
FALSO|FALSO|FALSO|FALSO
Indefinido|Indefinido|FALSO|Indefinido

NÃO|  |
---|---
VERDADEIRO|FALSO
FALSO|VERDADEIRO
Indefinido|Indefinido

### <a name="in-keyword"></a>Palavra-chave
A palavra-chave no pode ser utilizada para verificar se um valor especificado corresponde ao qualquer valor numa lista. Por exemplo, esta consulta devolve todos os documentos da família onde o id é um dos "WakefieldFamily" ou "AndersenFamily". 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Este exemplo devolve todos os documentos onde o estado é qualquer um dos valores especificados.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Ternário (?) e operadores adesão (?)
Os operadores Ternário e adesão podem ser utilizados para criar expressões condicionais, semelhantes ao populares linguagens de programação como c# e JavaScript. 

O operador Ternário (?) pode ser muito útil quando construir novas propriedades JSON no momento. Por exemplo, agora pode escrever consultas para classificar os níveis de classe para um formato legível humano como principiantes/intermédio/avançados conforme apresentado abaixo.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Também pode aninhar as chamadas para o operador like na consulta abaixo.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Como com outros operadores de consulta, se as propriedades referenciadas na expressão condicional estão em falta em qualquer documento ou se os tipos de comparadas são diferentes, em seguida, esses documentos serão excluídos nos resultados da consulta.

O operador de adesão (?) pode ser utilizado para eficazmente verificar a presença de uma propriedade (também conhecido como é definida) num documento. Isto é útil quando consultar contra semiestruturados ou dados dos tipos de mistos. Por exemplo, esta consulta devolve o "Apelido" se existir ou "Apelido", caso ainda não apresentar.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a name="quoted-property-accessor"></a>Acessor de propriedade proposto
Também pode aceder a propriedades utilizando o operador de propriedade entre aspas `[]`. Por exemplo, `SELECT c.grade` e `SELECT c["grade"]` são equivalentes. Esta sintaxe é útil quando precisar de uma propriedade que contém espaços, carateres especiais, ou acontece partilhar o mesmo nome de uma palavra-chave SQL ou uma palavra reservada de escape.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a name="select-clause"></a>Cláusula SELECT
A cláusula SELECT (**`SELECT <select_list>`**) é obrigatório e especifica o que vai ser valores obtidos a partir da consulta, tal como no ANSI SQL. São transmitidos subconjunto de que está foram filtrado na parte superior de documentos de origem para a projecções fase, onde são obtidos os valores especificados JSON e um novo objeto JSON fórmula foi criado, para cada entrada passada para o mesmo. 

O exemplo seguinte mostra uma consulta SELECIONAR normal. 

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propriedades aninhadas
No seguinte exemplo, vamos são a projetar duas propriedades aninhadas `f.address.state` e `f.address.city`.

**Consulta**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projecções também suporta expressões JSON conforme mostrado no seguinte exemplo.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Vamos olhar a função dos `$1` aqui. O `SELECT` cláusula tem de criar um objecto JSON e uma vez que é fornecida sem tecla, utilizamos os nomes de variáveis argumento implícito começando `$1`. Por exemplo, esta consulta devolve duas variáveis de argumento implícito, denominadas `$1` e `$2`.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Atribuir um alias
Agora vamos alargar o exemplo acima com explícito atribuir um alias de valores. Tal como está a palavra-chave utilizada para atribuir um alias. Tenha em atenção que é opcional, conforme mostrado enquanto a projetar do segundo valor como `NameInfo`. 

No caso de uma consulta tem duas propriedades com o mesmo nome, a atribuição de aliases deve ser utilizada para mudar o nome de uma ou ambas as propriedades para que estes estão desambiguados no resultado previsto.

**Consulta**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expressões escalares
Para além de referências de propriedade, a cláusula SELECT também suporta a expressões escalares constantes, expressões aritméticos, expressões lógicas, etc. Por exemplo, eis uma consulta de "Olá mundo" simples.

**Consulta**

    SELECT "Hello World"

**Resultados**

    [{
      "$1": "Hello World"
    }]


Eis um exemplo mais complexo do que utiliza uma expressão escalar.

**Consulta**

    SELECT ((2 + 11 % 7)-2)/3   

**Resultados**

    [{
      "$1": 1.33333
    }]


No exemplo seguinte, o resultado da expressão escalar é um valor booleano.

**Consulta**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Resultados**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Criação de objeto e matriz
Outra funcionalidade chave do DocumentDB SQL é a criação de matriz/objeto. No exemplo anterior, tenha em atenção que criámos um novo objeto JSON. Da mesma forma, um também pode construir matrizes conforme mostrado nos exemplos seguintes.

**Consulta**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Resultados**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a name="value-keyword"></a>Palavra-chave de valor
A palavra-chave **valor** fornece uma forma para devolver o valor JSON. Por exemplo, a consulta é mostrada abaixo devolve o escalar `"Hello World"` em vez de `{$1: "Hello World"}`.

**Consulta**

    SELECT VALUE "Hello World"

**Resultados**

    [
      "Hello World"
    ]


A seguinte consulta devolve o valor JSON sem o `"address"` etiqueta nos resultados de.

**Consulta**

    SELECT VALUE f.address
    FROM Families f 

**Resultados**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

O exemplo seguinte expande para mostrar como devolver valores primitivos JSON (nível da folha da árvore de JSON). 

**Consulta**

    SELECT VALUE f.address.state
    FROM Families f 

**Resultados**

    [
      "WA",
      "NY"
    ]


###<a name="-operator"></a>* Operador
O operador especial (*) é suportado para o documento como do project-é. Quando utilizado, tem de ser o único campo previsto. Enquanto uma consulta como `SELECT * FROM Families f` é válido, `SELECT VALUE * FROM Families f ` e `SELECT *, f.id FROM Families f ` não são válidos.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###<a name="top-operator"></a>Operador superior
A palavra-chave superior pode ser utilizada para limitar o número de valores de uma consulta. Quando a parte superior é utilizada em conjunto com a cláusula ORDER BY, o conjunto de resultados é limitado para o primeiro número de N de valores e ordenadas; caso contrário, devolve o primeiro número N de resultados por ordem indefinido. Como prática recomendada, numa instrução SELECT, utilize sempre uma cláusula ORDER BY com a cláusula superior. Este é a única forma de maneira previsível indicar as linhas são afetadas por parte superior. 


**Consulta**

    SELECT TOP 1 * 
    FROM Families f 

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

INÍCIO pode ser utilizado com um valor da constante (como mostrado acima) ou com o valor de uma variável através de consultas parametrizadas. Para obter mais detalhes, consulte o artigo parametrizadas abaixo.

## <a name="order-by-clause"></a>Cláusula ORDER BY
Como no ANSI SQL, pode incluir uma cláusula Order By opcional ao consultar. A cláusula pode incluir um argumento ASC/DESC opcional para especificar a ordem pela qual devem ser obtidos resultados. Para mais detalhada sobre Order By, consulte [DocumentDB ordem por instruções passo a passo](documentdb-orderby.md).

Por exemplo, eis uma consulta que obtém famílias de tipos de ordem do nome da cidade residência.

**Consulta**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**Resultados**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

E Eis uma consulta que obtém famílias de tipos de por ordem de data de criação, que está armazenada como um número que representa a época de tempo, seja, o tempo decorrido desde a 1 de Janeiro de 1970 em segundos.

**Consulta**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**Resultados**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## <a name="advanced-database-concepts-and-sql-queries"></a>Base de dados avançadas conceitos e consultas SQL
### <a name="iteration"></a>Iteração
Uma nova construção foi adicionada através a palavra-chave **IN** no SQL DocumentDB para fornecer suporte para iteração matrizes JSON. A origem de fornece suporte para iteração. Vamos começar com o exemplo seguinte:

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Agora vamos olhar outra consulta executa a iteração através da subordinados na coleção de. Tenha em atenção a diferença na matriz saída. Este exemplo divide `children` e organiza os resultados para uma única matriz.  

**Consulta**

    SELECT * 
    FROM c IN Families.children

**Resultados**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Isto pode ser ainda mais utilizado para filtrar em cada entrada da matriz individual, conforme mostrado no seguinte exemplo.

**Consulta**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Resultados**  

    [{
      "givenName": "Lisa"
    }]

### <a name="joins"></a>Associações
Numa base de dados relacional, é muito importante a necessidade de participar em tabelas. É o corolário lógico à estruturação de esquemas normalizados. Contrariamente a esta situação, DocumentDB trata-se com o modelo de dados não normalizado de esquema de royalties documentos. Este é o equivalente a lógico por "associação automática".

A sintaxe compatível com o idioma é a associação de associação < from_source2 > < from_source1 >... PARTICIPAR < from_sourceN >. Resumindo, isto devolve um conjunto de **N**- cadeia de identificação (cadeia de identificação com valores de **N** ). Cada cadeia de identificação não tem os valores produzidos por iteração todos os aliases de coleções de sites sobre os seus conjuntos de respetivos. Por outras palavras, este é um produto cruzado completo dos conjuntos de participantes na associação.

Os exemplos seguintes mostram como funciona a cláusula de associação. No exemplo seguinte, o resultado está vazio desde o produto cruzado de cada documento de origem e um conjunto vazio está vazio.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Resultados**  

    [{
    }]


No seguinte exemplo, a associação é entre a raiz de documentos e o `children` raiz subpastas. É um produto cruzado entre dois objectos JSON. O facto de crianças uma matriz não é eficaz na associação uma vez que recomendamos está a lidar com uma única raiz que é a matriz de subordinados. Por conseguinte, o resultado contém apenas dois resultados, uma vez que o número de produto cruzado de cada documento com a matriz contiver exatamente apenas um documento.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


O exemplo seguinte mostra uma associação mais convencional:

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



É a primeira coisa a tenha em atenção que a `from_source` de **ADERIR a** cláusula é uma iteração. Sim, o fluxo neste caso é da seguinte forma:  

-   Expanda cada elemento subordinado **c** na matriz.
-   Aplica um produto cruzado com a raiz do documento **f** com cada elemento subordinado **c** que foi simplificados no primeiro passo.
-   Por fim, apenas a propriedade de nome do objeto **f** raiz do project. 

O primeiro documento (`AndersenFamily`) contém apenas um elemento subordinado, por isso o conjunto de resultados contém apenas num único objeto correspondente para este documento. O segundo documento (`WakefieldFamily`) contém dois subordinados. Por isso, produto cruzado produz um objeto separado para cada um dos subordinados, assim que resulta em dois objectos, uma para cada um dos subordinados correspondentes a este documento. Note que os campos de raiz em ambos os estes documentos será mesmos, tal como seria de esperar um produto cruzado.

O utilitário real da associação é cadeia de identificação do formulário a partir do produto cruzado numa forma que caso contrário, é difícil ao projeto. Além disso, como podemos ver no exemplo abaixo, pode filtrar na combinação de uma cadeia de identificação que permite-lhe o utilizador optar por uma condição cumprida pela cadeia de identificação global.

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**Resultados**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Este exemplo é uma extensão natural do exemplo anterior e executa uma associação dupla. Por isso, produto cruzado podem ser visualizado como o seguinte pseudo código.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`tem um filho que tenha uma pet. Sim, o número de produto cruzado contiver uma linha (1*1*1) a partir desta família. No entanto, WakefieldFamily tem dois subordinados, mas apenas uma subordinado "Jesse" tem companhia. Jesse tem 2 companhia embora. Por conseguinte, produto cruzado rendimentos 1*1*2 = 2 linhas a partir desta família.

No exemplo seguinte, existe um filtro adicional no `pet`. Isto exclui todos os cadeia de identificação onde o nome de pet não é "Sombreado". Repare que temos conseguir criar cadeia de identificação de matrizes, filtro em qualquer um dos elementos de cadeia de identificação e qualquer combinação dos elementos do project. 

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Resultados**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a name="javascript-integration"></a>Integração de JavaScript
DocumentDB fornece um modelo de programação para lógica da aplicação JavaScript com base em execução diretamente nas coleções de ficheiros em termos de procedimentos armazenados e accionadores. Esta opção permite-para ambos:

-   Capacidade para efetuar operações CRUD transaccionais do alto desempenho e consultas contra documentos numa coleção de ao abrigo a integração abrangente de runtime JavaScript diretamente no motor de base de dados. 
-   Modelação de natural de fluxo de controlo, a variável do controlo do âmbito e atribuição e integração de exceção processamento primitivas com as transações de base de dados. Para obter mais detalhes acerca do suporte de DocumentDB para JavaScript integração, consulte a documentação de programabilidade JavaScript server lado.

###<a name="user-defined-functions-udfs"></a>Funções (UDFs) definidas pelo utilizador
Juntamente com os tipos de já definidos neste artigo, DocumentDB SQL fornece suporte para o funções de definidos pelo utilizador (UDF). Em particular, UDFs escalares são suportadas onde os programadores podem passar nos argumentos de zero ou muitos e devolver um resultado único argumento novamente. Cada um destes argumentos são verificadas para a ser valores JSON legais.  

A sintaxe SQL de DocumentDB é expandida para suportar a lógica de aplicação personalizada utilizando as funções definidas pelo utilizador. UDFs podem estar registado junto de DocumentDB e, em seguida, ser referenciadas como parte de uma consulta SQL. Na verdade, as UDFs exquisitely foram concebidos para ser chamado por consultas. Como um corolário para esta opção, UDFs não têm acesso ao objeto de contexto que têm os outros tipos de JavaScript (procedimentos armazenados e accionadores). Uma vez que executar consultas como só de leitura, podem executar principal ou réplicas secundárias. Por conseguinte, UDFs foram concebidos para ser executado em réplicas secundárias ao contrário de outros tipos de JavaScript.

Abaixo está um exemplo de como um UDF pode ser registado na base de dados DocumentDB, especificamente numa coleção de documento.

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
                                                                             
O exemplo anterior cria um UDF cujo nome tenha `REGEX_MATCH`. Aceitar os seus dois valores de cadeia JSON `input` e `pattern` e verifica se as correspondências primeiros o padrão de especificado na segunda através da função string.match() do JavaScript.


Vamos agora pode utilizar este UDF numa consulta de uma projecções. Tem de ser qualificadas UDFs com o prefixo entre maiúsculas e minúsculas "udf." Quando a chamada a partir consultas. 

>[AZURE.NOTE] Antes de 17/3/2015, DocumentDB suportadas UDF chamadas sem o "udf". SELECIONE REGEX_MATCH() como o prefixo. Foi preterida este padrão de chamada.  

**Consulta**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Resultados**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

UDF pode também ser utilizado no interior de um filtro conforme mostrado no exemplo abaixo, também qualificado com "udf." prefixo:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Resultados**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Na sua essência UDFs são expressões escalares válidos e podem ser utilizados em projecções e filtros. 

Para expandir os potência de UDFs, vamos observar outro exemplo com lógica condicional do:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
    
    
Segue-se um exemplo que exerce UDF.

**Consulta**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Resultados**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Como exibir os exemplos anteriores, UDFs integram o poder dos JavaScript idioma com o SQL DocumentDB para fornecer uma interface programável rich fazer lógica condicional, procedimento complexa com a ajuda das capacidades de runtime JavaScript integradas.

DocumentDB SQL fornece os argumentos para as UDFs para cada documento na origem na fase atual (cláusula WHERE ou cláusula SELECT) de processamento UDF. O resultado é incorporado forma totalmente integrada no pipeline de execução geral. Se as propriedades referido por UDF parâmetros não estão disponíveis no valor JSON, o parâmetro é considerado como não definido e, por conseguinte, a invocação UDF totalmente é ignorada. Da mesma forma se o resultado de UDF for indefinido,-não está incluído no resultado. 

Em resumo, UDFs são excelentes ferramentas para fazer a lógica empresarial complexo como parte da consulta.

### <a name="operator-evaluation"></a>Avaliação de operador
DocumentDB, tem de ser uma base de dados JSON, relação desenha paralelas com a sua semântica de avaliação e operadores JavaScript. Enquanto o DocumentDB tenta preservar a semântica de JavaScript em termos de suporte JSON, a avaliação de operação difere em algumas instâncias.

No DocumentDB SQL, ao contrário no SQL tradicional, os tipos de valores são muitas vezes não conhecidos até que os valores são realmente obtidos a partir da base de dados. Para poder eficazmente executar consultas, a maior parte dos operadores têm requisitos de tipo estrito. 

DocumentDB SQL não executar conversões implícitas, ao contrário JavaScript. Por exemplo, uma consulta como `SELECT * FROM Person p WHERE p.Age = 21` corresponde ao documentos que contêm uma propriedade de idade cujo valor é 21. Qualquer outro documento corresponde à propriedade cuja idade da variações possivelmente infinito cadeia "21" ou outros como "021", "21.0", "0021", "00021", etc. será não que deverá ser correspondido. Isto é em contrapartida JavaScript onde os valores de cadeia estão convertido implicitamente no números (com base em operador, ex: = =). Esta opção é crucial para o índice eficiente correspondente no DocumentDB SQL. 

## <a name="parameterized-sql-queries"></a>SQL parametrizadas
DocumentDB suporta consultas com parâmetros expressos com familiares @ notação. SQL parametrizada fornece robusta processamento e sair do utilizador de entrada, impedindo acidental exposição de dados através de introdução de SQL. 

Por exemplo, pode escrever uma consulta que utiliza o apelido e endereço estado como parâmetros e, em seguida, executá-la para vários valores de último nome e endereço Estado com base na entrada do utilizador.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Neste pedido, em seguida, pode ser enviado para DocumentDB como consulta parametrizada JSON como é mostrado abaixo.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

O argumento para a parte superior pode ser definido utilizando parametrizadas como é mostrado abaixo.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Valores de parâmetros podem ser qualquer JSON válido (cadeias, números, valores booleanos nulos, até mesmo matrizes ou aninhadas JSON). Também dado que DocumentDB é menos de esquema, parâmetros não são validados face ao qualquer tipo.

##<a name="built-in-functions"></a>Funções incorporadas
DocumentDB também suporta um número de funções incorporadas para operações comuns que podem ser utilizadas dentro de consultas como as funções definidas pelo utilizador (UDFs).

<table>
<tr>
<td>Funções matemáticas</td> 
<td>ABS, Arred. excesso, EXP, Arred, registo, LOG10, POWER, ROUND, início de sessão, RAIZQ, QUADRADO, truncar, ACOS, ASEN, ATAN, ATN2, COS, COT, graus, PI, em radianos, sen e TAN</td>
</tr>
<tr>
<td>Tipo de verificação de funções</td>    
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED e IS_PRIMITIVE</td>
</tr>
<tr>
<td>Funções de cadeia</td>   
<td>CONCAT, contém, ENDSWITH, INDEX_OF, à esquerda, comprimento, inferior, funções SUPRESQ, substituir, replicação, inversa, direita, SUPRDIR, STARTSWITH, SUBCADEIA e canto superior</td>
</tr>
<tr>
<td>Funções de matriz</td>    
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE</td>
</tr>
<tr>
<td>Funções espaciais</td>  
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID e ST_ISVALIDDETAILED</td>
</tr>
</table>  

Se estiver a utilizar uma função definida pelo utilizador (UDF) para o qual uma função incorporada está agora disponível, deve utilizar a função incorporada correspondente à medida que vai ser mais rápido executar e forma mais eficiente. 

### <a name="mathematical-functions"></a>Funções matemáticas
As funções matemáticas executam um cálculo, normalmente, com base em valores de entrada que são fornecidos como argumentos e devolvem um valor numérico. Eis uma tabela de suportados funções matemáticas incorporadas.

<table>
<tr>
<td><strong>Utilização</strong></td>
<td><strong>Descrição</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td> 
<td>Devolve o valor absoluto (positivo) da expressão numérica especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">Arred. excesso (num_expr)</a></td> 
<td>Devolve o menor valor de número inteiro maior ou igual a, a expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">Arred (num_expr)</a></td> 
<td>Devolve o número inteiro maior menor ou igual a expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>Devolve o exponente da expressão numérica especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [, base])</a></td> 
<td>Devolve o logaritmo natural da expressão numérica especificada ou o logaritmo de utilizar a base especificada</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">Log10 (num_expr)</a></td> 
<td>Devolve o valor de logarítmico de base 10 da expressão numérica especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">Arred (num_expr)</a></td> 
<td>Devolve um valor numérico, arredondado para o valor de número inteiro mais próximo.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">Truncar (num_expr)</a></td> 
<td>Devolve um valor numérico, truncado para o valor de número inteiro mais próximo.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">RAIZQ (num_expr)</a></td>   
<td>Devolve a raiz quadrada da expressão numérica especificada.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">QUADRADO (num_expr)</a></td>   
<td>Devolve o quadrado da expressão numérica especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POTÊNCIA (num_expr; num_expr)</a></td>   
<td>Devolve o poder da expressão numérica especificado para o valor especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">Início de sessão (num_expr)</a></td>   
<td>Devolve o valor de início de sessão (-1, 0, 1) da expressão numérica especificado.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>   
<td>Devolve o ângulo em radianos, cujo co-seno é a expressão numérica especificada; Também denominado arco de cosseno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASEN (num_expr)</a></td>   
<td>Devolve o ângulo em radianos, cujo seno é a expressão numérica especificada. Isto é também chamado arco seno.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>   
<td>Devolve o ângulo em radianos, cuja tangente é a expressão numérica especificada. Isto é também chamado arco tangente.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>Devolve o ângulo em radianos, entre o eixo x positivo e padrão com raios a partir da origem para o ponto (y; x), em que x e y são os valores das duas expressões flutuar especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>Devolve o cosseno do ângulo especificado, trigonométricos em radianos, na expressão especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>Devolve a cotangente trigonométricas do ângulo especificado, em radianos, na expressão numérica especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">GRAUS (num_expr)</a></td> 
<td>Devolve o ângulo correspondente em graus para um ângulo especificado em radianos.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI)</a></td>   
<td>Devolve o valor da constante de PI.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANOS (num_expr)</a></td> 
<td>Devolve radianos quando uma expressão numérica, em graus, é introduzida.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">Sen (num_expr)</a></td> 
<td>Devolve o seno do ângulo especificado, trigonométricos em radianos, na expressão especificado.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td> 
<td>Devolve a tangente da expressão de entrada, a expressão especificada.</td>
</tr>

</table> 

Por exemplo, agora, pode executar consultas de como o seguinte:

**Consulta**

    SELECT VALUE ABS(-4)

**Resultados**

    [4]

A diferença entre funções do DocumentDB em comparação comparada ANSI SQL principal é que foram concebidos para trabalhar bem com dados de menos de esquema e mistas esquema. Por exemplo, se tiver um documento onde a propriedade tamanho está em falta ou que tenha um valor não numérico, como "desconhecida", em seguida, o documento é ignorado sobre, em vez de devolver um erro.

### <a name="type-checking-functions"></a>Tipo de verificação de funções
As funções de verificação do tipo de permitem-lhe verificar o tipo de uma expressão dentro de consultas SQL. Funções de verificação de tipo podem ser utilizadas para determinar o tipo de propriedades dentro dos documentos ao compor uma mensagem quando esta for a variável ou desconhecidas. Eis uma tabela de tipo incorporado suportado verificação funções.

<table>
<tr>
  <td><strong>Utilização</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Devolve um valor booleano que indica se o tipo do valor é uma matriz.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Devolve um valor booleano que indica se o tipo do valor é um valor booleano.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Devolve um valor booleano que indica se o tipo do valor é nulo.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Devolve um valor booleano que indica se o tipo do valor é um número.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Devolve um valor booleano que indica se o tipo do valor é um objeto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Devolve um valor booleano que indica se o tipo do valor é uma cadeia.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Devolve um valor booleano que indica se foi atribuída a propriedade de um valor.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Devolve um valor booleano que indica se o tipo do valor é uma cadeia, número, booleano ou nulo.</td>
</tr>

</table>

Utilizar estas funções, agora, pode executar consultas de como o seguinte:

**Consulta**

    SELECT VALUE IS_NUMBER(-4)

**Resultados**

    [true]

### <a name="string-functions"></a>Funções de cadeia
As seguintes funções escalares executar uma operação um valor de entrada de cadeia e devolvem uma cadeia, o valor numérico ou booleano. Eis uma tabela de funções de cadeia incorporados:

Utilização|Descrição
---|---
[COMPRIMENTO (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Devolve o número de carateres da expressão de cadeia especificada
[CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Devolve uma cadeia que é o resultado de concatenação de duas ou mais valores de cadeia.
[SUBCADEIA (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Devolve a parte de uma expressão de cadeia.
[STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Devolve um booleano que indica se a primeira cadeia da expressão termina com o segundo
[ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Devolve um booleano que indica se a primeira cadeia da expressão termina com o segundo
[CONTÉM (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Devolve um booleano que indica se a primeira cadeia da expressão contém o segundo.
[INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Devolve a posição inicial da primeira ocorrência da segunda cadeia da expressão à primeira expressão de cadeia especificada ou -1 se a cadeia não for encontrada.
[LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Devolve a parte de uma cadeia com o número especificado de carateres para a esquerda.
[RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Devolve a parte direita de uma cadeia com o número especificado de carateres.
[LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Devolve uma expressão de cadeia depois remove espaços em branco à esquerda.
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Devolve uma cadeia da expressão após truncar todos os espaços à direita.
[INFERIOR (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Devolve uma expressão de cadeia depois de converter dados de caracteres em maiúsculas em minúsculas.
[Canto superior (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Devolve uma expressão de cadeia depois de converter dados de caracteres em minúsculas para maiúsculas.
[Substituir (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Substitui todas as ocorrências de um valor de cadeia especificada com outro valor de cadeia.
[REPLICAÇÃO (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Repete um valor de cadeia um número especificado de vezes.
[INVERSA (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Devolve a ordem inversa de um valor de cadeia.

Utilizar estas funções, agora, pode executar consultas semelhante ao seguinte. Por exemplo, pode voltar a designação da família em maiúsculas da seguinte forma:

**Consulta**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Resultados**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Ou concatenar cadeias como neste exemplo:

**Consulta**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Resultados**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Funções de cadeia podem também ser utilizadas na cláusula WHERE para filtrar os resultados, como no exemplo seguinte:

**Consulta**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Resultados**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funções de matriz
As seguintes funções escalares executam uma operação um valor de entrada de matriz e devolvido numérico, valor booleano ou numa matriz. Eis uma tabela de funções de matriz incorporados:

Utilização|Descrição
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Devolve o número de elementos da expressão matriz especificada.
[ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Devolve uma matriz, que é o resultado de concatenação de duas ou mais valores de matriz.
[ARRAY_CONTAINS (arr_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Devolve um valor booleano que indica se uma matriz contém o valor especificado.
[ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Devolve a parte de uma expressão de matriz.

Funções de matriz podem ser utilizadas para manipular matrizes dentro JSON. Por exemplo, eis uma consulta que devolve todos os documentos em que é um dos elementos principais "Robin Wakefield". 

**Consulta**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Eis outra exemplo que utiliza ARRAY_LENGTH para obter o número de filhos por família.

**Consulta**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Resultados**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Funções espaciais

DocumentDB suporta as seguintes funções incorporadas abrir Geoespaciais Consortium (OGC) para consultar geoespaciais. Para obter mais detalhes sobre geoespaciais suporte DocumentDB, consulte o artigo [trabalhar com dados geoespaciais no Azure DocumentDB](documentdb-geospatial.md). 

<table>
<tr>
  <td><strong>Utilização</strong></td>
  <td><strong>Descrição</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Devolve a distância entre as duas expressões de ponto GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Devolve uma expressão booleana que indica se o ponto de GeoJSON especificado no primeiro argumento estiver dentro polígono GeoJSON no segundo argumento.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devolve um valor booleano que indica se a expressão de ponto ou polígono GeoJSON especificada é válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devolve um valor JSON que contém um valor booleano valor se a expressão de ponto ou polígono GeoJSON especificada é válida e inválido, para além do motivo como um valor de cadeia.</td>
</tr>
</table>

Funções espaciais podem ser utilizadas para executar proximidade querries contra dados espaciais. Por exemplo, eis uma consulta que devolve todos os documentos da família que estão dentro de 30 km da localização especificada utilizando a função incorporada ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Se incluir indexação espacial na política de indexação, em seguida, "consultas distância" serão servidas eficazmente através de um índice. Para obter mais detalhes sobre a indexação espacial, consulte a secção abaixo. Se não tiver um índice espacial para caminhos especificados, ainda pode realizar consultas espaciais ao especificar `x-ms-documentdb-query-enable-scan` cabeçalho de pedido com o conjunto de valor para "true". No .NET, pode fazê-lo ao passar o argumento **FeedOptions** opcional para consultas com [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) definida como verdadeiro. 

ST_WITHIN podem ser utilizados para verificar se um ponto de se situar dentro de um polígono. Frequentemente polígonos são utilizados para representar os limites de como códigos postais, limites de estado ou formações naturais. Novamente se incluir indexação espacial na política de indexação, em seguida, "tem" consultas serão servidas eficazmente através de um índice. 

Argumentos polígono no ST_WITHIN podem conter apenas um único toque, ou seja, os polígonos não pode conter buracos nas mesmas. Verifique os [limites de DocumentDB](documentdb-limits.md) para o número máximo de pontos de permitidos num polígono para uma consulta de ST_WITHIN.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Semelhante a como sem correspondência de tipos de funciona no DocumentDB consulta, se o valor de localização especificado no quer argumento está mal ou é inválido, em seguida, irá devolver **indefinido** e documento avaliado para ignorado dos resultados da consulta. Se a sua consulta não devolver resultados, execute ST_ISVALIDDETAILED para depuração por que motivo o tipo de spatail é inválido.     

ST_ISVALID e ST_ISVALIDDETAILED podem ser utilizados para verificar se um objecto espacial é válido. Por exemplo, a seguinte consulta verifica a validade de um ponto com uma fora do valor de latitude intervalo (-132.8). ST_ISVALID devolve apenas um valor Booleano e ST_ISVALIDDETAILED devolve o valor Booleano e uma cadeia que contém o motivo por que é considerada inválida.

**Consulta**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Estas funções podem também ser utilizadas para validar polígonos. Por exemplo, aqui utilizamos ST_ISVALIDDETAILED para validar um polígono que não está fechado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
Que é moldado o espaciais funções e a sintaxe SQL para DocumentDB. Agora vamos dar uma como LINQ consultar funciona e como o interage com a sintaxe podemos visualizou até ao momento.

## <a name="linq-to-documentdb-sql"></a>LINQ para DocumentDB SQL
LINQ é um modelo de programação .NET que expresse cálculo como consultas no sequências de objetos. DocumentDB fornece um cliente de biblioteca de lado interface com LINQ, facilitando uma conversão entre JSON e .NET objetos e um mapeamento a partir de um subconjunto de LINQ consultas para DocumentDB consultas. 

A imagem abaixo mostra a arquitetura do consultas LINQ utilizando DocumentDB de suporte.  Utilizar o cliente de DocumentDB, os programadores podem criar um objeto de **IQueryable** diretamente consultas o fornecedor de consulta DocumentDB, que, em seguida, traduz a consulta LINQ para uma consulta de DocumentDB. A consulta, em seguida, é transferida para o servidor de DocumentDB para obter um conjunto de resultados no formato JSON. Dos resultados devolvidos são serialização para uma sequência de objetos de .NET do lado do cliente.

![Arquitetura de suportar as consultas LINQ utilizando DocumentDB - sintaxe SQL, linguagem de consulta JSON, conceitos de base de dados e consultas de SQL][1]
 


### <a name="net-and-json-mapping"></a>Mapeamento de JSON e .NET
O mapeamento entre objetos .NET e documentos JSON é natural - cada campo de membro de dados é mapeado para um objeto JSON, onde o nome do campo é mapeado para a parte "chave" do objeto e a parte "valor" é modo recursivo mapeado para a parte do valor do objeto. Considere o seguinte exemplo. O objeto de família criado é mapeado para o documento JSON, conforme apresentado abaixo. E vice-versa, o documento JSON está mapeado novamente para um objeto de .NET.

**C# classe**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ para tradução de SQL
O fornecedor de consulta DocumentDB executa um melhor mapeamento de esforço de uma consulta LINQ para uma consulta DocumentDB SQL. Na descrição da seguinte, podemos partem do pressuposto que o leitor tem uma familiaridade básica do LINQ.

Em primeiro lugar, para o sistema de tipo, suportamos todos os JSON tipos primitivos – tipos numéricos, booleano, cadeia e nulo. Estes tipos de JSON são suportados. As expressões seguintes do escalares são suportadas.

-   Valores constantes – estas inclui valores constantes dos tipos de dados primitivos ao tempo a consulta é avaliada.

-   Expressões de índice de matriz da propriedade / – estas expressões referem-se para a propriedade de um objeto ou um elemento de matriz.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Expressões aritméticos - estas incluem expressões aritméticos comuns em valores numéricos e booleanos. Para a lista completa, consulte a especificação de SQL.

        2 * family.children[0].grade;
        x + y;

-   Expressão de comparação de cadeia - estas incluem comparar um valor de cadeia algum valor da constante de cadeia.  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   Objeto/matriz expressão criação - estas expressões devolvido um objeto do tipo de valor composta ou escreva anónima ou de uma matriz desses objetos. Estes valores podem ser aninhadas.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### <a name="list-of-supported-linq-operators"></a>Lista de operadores LINQ suportados
Aqui está uma lista de operadores LINQ suportados no fornecedor LINQ incluído com o DocumentDB .NET SDK.

-   **Selecione**: projecções traduzem para SQL SELECT, incluindo a construção de objeto
-   **Onde**: filtros traduzir para SQL onde e suporte tradução entre & &, | | e! para os operadores SQL
-   **SelectMany**: permite que reforma de matrizes para a cláusula SQL PARTICIPAR. Podem ser utilizados para cadeia/ninho expressões para filtrar em elementos de matriz
-   **OrdenarPor e OrderByDescending**: converte para ordenar por ordem ascendente/descendente:
-   **CompareTo**: converte comparações de intervalo. Mais utilizadas em cadeias de uma vez que não estiver comparáveis no .NET
-   **Tomar**: converte para o início de SQL para limitar os resultados de uma consulta
-   **Funções matemáticas**: suporta a tradução de. Abs, Acos, ASEN do líquido, Atan, Arred. excesso, Cos, Exp, Arred, registo, Log10, p, Round, início de sessão, sen, RAIZQ, Tan, Truncate para as equivalentes funções incorporadas de SQL.
-   **Funções de cadeia**: suporta a tradução de. Concat, contém, EndsWith do líquido, IndexOf, contagem, ToLower, TrimStart, substituir, inversa, TrimEnd, StartsWith, subcadeia, ToUpper para as equivalentes funções incorporadas de SQL.
-   **Funções de matriz**: suporta a tradução de. Do líquido Concat, contém e contar para as equivalentes funções incorporadas de SQL.
-   **Funções da extensão Geoespaciais**: suporta a tradução de métodos de stub distância, dentro IsValid e IsValidDetailed para as equivalentes funções incorporadas de SQL.
-   **Função de extensão de função definida pelo utilizador**: tradução suporta a partir do método de stub UserDefinedFunctionProvider.Invoke para o utilizador correspondente definidos função.
-   **Diversos**: suporta tradução da adesão e operadores condicionais. Pode traduzir contém a cadeia contém, ARRAY_CONTAINS ou o em SQL, dependendo do contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL
Aqui estão alguns exemplos que ilustram a forma como alguns dos operadores de consulta LINQ padrão são convertidas para baixo até DocumentDB consultas.

#### <a name="select-operator"></a>Selecione o operador
A sintaxe é `input.Select(x => f(x))`, onde `f` é uma expressão escalar.

**Expressão lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expressão lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expressão lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Operador de SelectMany
A sintaxe é `input.SelectMany(x => f(x))`, onde `f` é uma expressão escalar que devolve um tipo de coleções de sites.

**Expressão lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Onde operador
A sintaxe é `input.Where(x => f(x))`, onde `f` é uma expressão escalar que devolve um valor booleano.

**Expressão lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expressão lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Composta consultas SQL
Os operadores acima podem ser compostos para formar consultas mais eficientes. Uma vez que DocumentDB suportar coleções aninhadas, a composição pode ser concatenada ou aninhada.

#### <a name="concatenation"></a>Concatenação 

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar com um opcional `SelectMany` consulta seguidas por vários `Select` ou `Where` operadores.


**Expressão lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expressão lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expressão lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expressão lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Aninhamento

A sintaxe é `input.SelectMany(x=>x.Q())` onde Q é um `Select`, `SelectMany`, ou `Where` operador.

Numa consulta aninhadas, a consulta interna é aplicada a cada elemento da coleção de externa. Uma funcionalidade importante é que a consulta interna pode referir-se os campos dos elementos na coleção de externa como associações.

**Expressão lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expressão lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expressão lambda LINQ**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a name="executing-sql-queries"></a>Executar consultas de SQL
DocumentDB expõe recursos através de um REST API que possa ser chamado por qualquer linguagem capaz de fazer pedidos HTTP/HTTPS. Para além disso, DocumentDB oferece programação bibliotecas para vários idiomas populares como .NET, Node.js, JavaScript e Python. A API do resto das bibliotecas de vários todos os suportam e consultar através de SQL. O .NET SDK suporta LINQ consultar para além de SQL.

Os exemplos seguintes mostram como criar uma consulta e submeter a mesma contra uma conta de base de dados DocumentDB.

### <a name="rest-api"></a>REST API
DocumentDB oferece um modelo de programação RESTful abrir através de HTTP. Contas de base de dados podem ser aprovisionadas utilizar uma subscrição do Azure. O modelo de recurso DocumentDB é composta por um conjuntos de recursos numa conta de base de dados, cada um dos quais é endereçável utilizando um URI lógico e estável. Um conjunto de recursos é referido como um feed neste documento. Uma conta de base de dados for constituída por um conjunto de bases de dados, cada uma contendo várias coleções de sites, cada um dos qual de virar conter documentos, UDFs e outros tipos de recursos.

O modelo básico interação com estes recursos é através os verbos HTTP GET, colocar, publicar e eliminar com a sua interpretação padrão. O verbo mensagem é utilizado para criação de um novo recurso, executar um procedimento armazenado ou para a emissão de uma consulta de DocumentDB. Consultas sempre são apenas operações sem efeitos secundários de leitura.

Os exemplos seguintes mostram uma mensagem para uma consulta de DocumentDB efetuada contra uma coleção de que contém os dois documentos de exemplo que estamos ter revisto até ao momento. A consulta tem um filtro simple a propriedade do nome do JSON. Tenha em atenção a utilização do `x-ms-documentdb-isquery` e o tipo de conteúdo: `application/query+json` cabeçalhos para indicar que a operação é uma consulta.


**Pedido**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


O segundo exemplo mostra uma consulta mais complexa que devolve múltiplos resultados da associação.

**Pedido**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Se não consegue ajustar resultados de uma consulta dentro de uma única página de resultados, em seguida, REST API devolve um token de continuação de notas através de `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginar resultados por incluindo o cabeçalho nos resultados subsequentes. O número de resultados por página também pode ser controlado através de `x-ms-max-item-count` cabeçalho número.

Para gerir a política de consistência de dados de consultas, utilize o `x-ms-consistency-level` cabeçalho como todos os pedidos de REST API. Para consistência sessão, é necessário para a mais recente de eco também `x-ms-session-token` cabeçalho Cookie no pedido de consulta. Tenha em atenção que política indexação a coleção consultado também pode influenciar a consistência dos resultados da consulta. Com a predefinição indexação as definições de política, coleções do índice é sempre atual com o conteúdo do documento e os resultados da consulta irão corresponder a consistência escolhida para os dados. Se a política de indexação é reduzida para lenta, consultas podem devolver resultados obsoletos. Para obter mais informações, consulte [Níveis de consistência DocumentDB] [consistency-levels].

Se a política de indexação configurada na coleção de não consegue suportar a consulta especificada, o servidor de DocumentDB devolve 400 "pedido inválido". Isto é devolvido para consultas de intervalo contra caminhos configuradas para pesquisas hash (igualdade) e para caminhos explicitamente excluídos da indexação. O `x-ms-documentdb-query-enable-scan` cabeçalho pode ser especificado para permitir que a consulta efetuar uma pesquisa quando um índice remissivo não está disponível.

### <a name="c-net-sdk"></a>SDK C# (.NET)
O .NET SDK suporta LINQ e SQL consultar. O exemplo seguinte mostra como executar a consulta de filtro simples introduzida anterior deste documento.


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Este exemplo se compara duas propriedades para igualdade dentro de cada documento e utiliza projecções anónimas. 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


O exemplo seguinte mostra associações, expressas através de LINQ SelectMany.


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



O cliente de .NET itera automaticamente todas as páginas de resultados da consulta nos blocos de foreach conforme mostrado acima. As opções de consulta introduzidas na secção REST API também estão disponíveis no .NET SDK usando o `FeedOptions` e `FeedResponse` classes no método CreateDocumentQuery. O número de páginas pode ser controlado utilizando o `MaxItemCount` definição. 

Também explicitamente pode controlar a paginação criando `IDocumentQueryable` utilizando o `IQueryable` objeto e, em seguida, ao ler o artigo o` ResponseContinuationToken` valores e prisma-los novamente como `RequestContinuationToken` no `FeedOptions`. `EnableScanInQuery`pode ser definido para activar pesquisas quando a consulta não sejam suportada pela política de indexação configurada. Para coleções de sites com partições, pode utilizar `PartitionKey` para executar a consulta relativamente a um único partição (embora DocumentDB automaticamente pode extrair isto do texto da consulta), e `EnableCrossPartitionQuery` para executar consultas que poderão ter de ser executado contra a partições múltiplas. 

Consulte [exemplos DocumentDB .NET](https://github.com/Azure/azure-documentdb-net) para amostras mais contendo consultas. 

### <a name="javascript-server-side-api"></a>API do lado do servidor de JavaScript 
DocumentDB fornece um modelo de programação para lógica da aplicação JavaScript com base em execução diretamente nas coleções de ficheiros com accionadores e procedimentos armazenados. A lógica JavaScript registada num nível de coleções de sites, em seguida, pode emitir operações de base de dados nas operações as documentos da coleção de determinado. Estas operações são moldadas no ambientes transações ÁCIDAS.

O exemplo seguinte Mostrar como utilizar o queryDocuments no servidor API JavaScript para tornar as consultas a partir do interior procedimentos armazenados e accionadores.


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a name="aggregate-functions"></a>Funções de agregação

Suporte nativo para funções de agregação é no programa works, mas se precisar de contar ou somar a funcionalidade entretanto, é possível alcançar o mesmo resultado utilizando métodos diferentes.  

No caminho de leitura:

- Pode executar funções de agregação ao obter os dados e efetuar uma contagem localmente. É recomendável para utilizar uma projecções de consulta tarifa reduzida como `SELECT VALUE 1` em vez de documento completo, como `SELECT * FROM c`. Isto ajuda a maximizar o número de documentos processados em cada página de resultados, evitando assim IDA adicionais para o serviço se for necessário.
- Também pode utilizar um procedimento armazenado para minimizar a latência da rede em repetidos round viagens. Para uma amostra de procedimento armazenado que calcula a contagem de uma consulta de filtro determinado, consulte o artigo [Count.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/stored-procedures/Count.js). O procedimento armazenado pode permitir que os utilizadores combinar lógica de negócio rich juntamente com efetuar agregações de forma eficaz.

No caminho de escrita:

- Outro padrão comuns é previamente agregar os resultados no caminho "escrever". Este é especialmente apelativo quando o volume de pedidos de "ler" for superior a que os pedidos de "escrever". Uma vez previamente agregado, os resultados estão disponíveis com um único ponto pedido de leitura.  É a melhor forma de previamente agregar no DocumentDB definir um accionador que é chamado com cada "escrever" e actualizar um documento de metadados que tenha os resultados mais recentes para a consulta que está a ser verificada. Por exemplo, introduza observe a amostra de [UpdateaMetadata.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/triggers/UpdateMetadata.js) , que atualiza a minSize, maxSize e tamanho total do documento para a coleção de metadados. O exemplo pode ser prolongado para atualizar um contador, soma, etc.

##<a name="references"></a>Referências
1.  [Introdução ao Azure DocumentDB][introduction]
2.  [Especificação DocumentDB SQL](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [Exemplos de DocumentDB .NET](https://github.com/Azure/azure-documentdb-net)
4.  [Níveis de consistência DocumentDB][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  Especificação de JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  Técnicas de avaliação de consulta para bases de dados grandes [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Processamento nos sistemas de base de dados relacional paralela, premir uma American Society of mechanical IEEE computador, 1994 de consultas
11. Tan lu, Ooi, processamento nos sistemas de base de dados relacional paralela, premir uma American Society of mechanical IEEE computador, 1994 de consultas.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, o André Tomkins: Latim porco: um idioma não para externa para processamento de dados, SIGMOD 2008.
13.     G. Graefe. A arquitetura cascatas para otimização de consulta. IEEE dados Eng. Bull., 18(3): 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 
