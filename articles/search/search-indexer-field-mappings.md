<properties
pageTitle="Mapeamentos de campos em indexadores de pesquisa do Azure"
description="Configurar mapeamentos de campo indexador Azure pesquisa para ter em conta das diferenças nos nomes de campos e representações de dados"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="10/17/2016"
ms.author="eugenesh" />

# <a name="field-mappings-in-azure-search-indexers"></a>Mapeamentos de campos em indexadores de pesquisa do Azure

Ao utilizar indexadores Azure pesquisa, ocasionalmente, pode encontrar-se em situações onde os dados de entrada não se adequarem corresponde ao esquema do seu índice de destino. Nestes casos, pode utilizar os **mapeamentos de campos** para transformar os dados da forma pretendida. 

Algumas situações onde os mapeamentos de campo são úteis:
 
- A origem de dados tem um campo `_id`, mas Azure pesquisa não permitir que os nomes dos campos começando com um sublinhado. Um mapeamento de campos permite-lhe "mudar o nome de" um campo. 
- Que pretende preencher vários campos no índice remissivo com os mesmos dados de origem do dados, por exemplo, uma vez que pretende aplicar diferentes analisadores para esses campos. Mapeamentos de campo permitem-lhe "bifurcam" um campo de origem de dados.
- Precisa para Base64 codificar ou codificar os seus dados. Mapeamentos de campo suportam várias **funções de mapeamento**, incluindo funções para Base64 codificar e descodificar.   


> [AZURE.IMPORTANT] Atualmente, a funcionalidade do campo mapeamentos está na pré-visualização. Está disponível apenas no API REST utilizando a versão **2015-02-28-pré-visualização**. Verifique se lembra, pré-visualizar APIs destinam-se para testar e avaliação e não deve ser utilizados em ambientes de produção.

## <a name="setting-up-field-mappings"></a>Configurar mapeamentos de campo

Pode adicionar mapeamentos de campo ao criar um novo indexador utilizar a API do [Indexador criar](search-api-indexers-2015-02-28-preview.md#create-indexer) . Pode gerir mapeamentos de campos num indexador indexação utilizar a API do [Indexador de atualização](search-api-indexers-2015-02-28-preview.md#update-indexer) . 

Um mapeamento de campo consiste em 3 partes: 

1. A `sourceFieldName`, que representa um campo na origem de dados. Esta propriedade é necessária. 

2. Opcional `targetFieldName`, que representa um campo no índice de pesquisa. Se for omitido, é utilizado o mesmo nome tal como a origem de dados. 

3. Opcional `mappingFunction`, que pode transformar os dados utilizando uma das várias funções predefinidas. A lista completa das funções está [abaixo](#mappingFunctions).

São adicionados os mapeamentos de campos para a `fieldMappings` matriz relativa à definição do indexador. 

Por exemplo, eis como pode acomodar diferenças nos nomes de campo: 

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Um indexador pode ter vários mapeamentos de campo. Por exemplo, eis como que pode "bifurcam" um campo:

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Pesquisa Azure utiliza comparação maiúsculas e minúsculas para resolver os nomes de campo e função nas mapeamentos de campo. Isto é conveniente (não ter de obter todas as invólucro direita), mas significa que a sua origem de dados ou o índice remissivo não pode ter campos que diferem apenas por maiúsculas/minúsculas.  

<a name="mappingFunctions"></a>
## <a name="field-mapping-functions"></a>Funções de mapeamento do campo

Estas funções atualmente são suportadas: 

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### <a name="base64encode"></a>base64Encode 

Executa a codificação de *URL seguro* Base64 da cadeia de entrada. Assume que a entrada é codificado UTF-8. 

#### <a name="sample-use-case"></a>Caso de utilização de exemplo 

Apenas os carateres de URL seguro podem aparecer numa chave de documento Azure pesquisa (uma vez que os clientes tem de conseguir endereço o documento utilizando a API de pesquisa, por exemplo). Se os seus dados contêm carateres não seguras URL e pretende utilizá-la para preencher um campo de chave no índice de pesquisa, utilize esta função.   

#### <a name="example"></a>Exemplo 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### <a name="base64decode"></a>base64Decode

Executa Base64 descodificar da cadeia de entrada. Um *URL seguro* cadeia codificada Base64 é considerada a entrada de dados. 

#### <a name="sample-use-case"></a>Caso de utilização de exemplo 

Valores de metadados personalizados blob têm de ser codificada ASCII. Pode utilizar a codificação Base64 para representar cadeias Unicode arbitrários nos metadados personalizado blob. No entanto, para tornar a procura significativo, pode utilizar esta função para transformar os dados codificados novamente cadeias "normais" quando preencher o índice de pesquisa.  

#### <a name="example"></a>Exemplo 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### <a name="extracttokenatposition"></a>extractTokenAtPosition

Divide-se um campo de cadeia através do delimitador especificado e escolhe o token na posição especificada na dividir resultante.

Por exemplo, se a entrada é `Jane Doe`, o `delimiter` é `" "`(espaço) e o `position` for 0, o resultado é `Jane`; Se o `position` for 1, o resultado é `Doe`. Se a posição que se refere a um token de que não existe, será devolvido um erro.

#### <a name="sample-use-case"></a>Caso de utilização de exemplo 

A origem de dados contiver um `PersonName` campo e pretende indexá-lo como separado duas `FirstName` e `LastName` campos. Pode utilizar esta função para dividir a entrada de dados utilizando o caráter de espaço como o delimitador.

#### <a name="parameters"></a>Parâmetros

- `delimiter`: uma cadeia para utilizar como o separador quando dividir a cadeia de entrada.
- `position`: uma posição baseado em zero número inteiro do token para escolher depois da cadeia de entrada é dividida.    

#### <a name="example"></a>Exemplo

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection

Transformações de uma cadeia formatada como uma matriz JSON de cadeias numa matriz de cadeia que pode ser utilizada para preencher um `Collection(Edm.String)` campo no índice remissivo. 

Por exemplo, se a cadeia de entrada é `["red", "white", "blue"]`, em seguida, o campo de destino do tipo de `Collection(Edm.String)` será preenchida com os três valores `red`, `white` e `blue`. Para valores de entrada que não possam ser analisadas como matrizes de cadeia JSON, será devolvido um erro. 

#### <a name="sample-use-case"></a>Caso de utilização de exemplo

Base de dados SQL Azure não tem um tipo de dados incorporado que transmitem mapas para `Collection(Edm.String)` campos na pesquisa Azure. Para preencher os campos de coleção de cadeia, formate a sua origem de dados como uma matriz de cadeia JSON e utilize esta função. 

#### <a name="example"></a>Exemplo 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar o Azure pesquisa

Se tiver pedidos de funcionalidade ou ideias para melhorias, consulte comunique-no nosso [site do site](https://feedback.azure.com/forums/263029-azure-search/).