<properties
   pageTitle="Construir cadeias de filtro para o Estruturador da tabela | Microsoft Azure"
   description="Construir cadeias de filtro para o Estruturador da tabela"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="constructing-filter-strings-for-the-table-designer"></a>Construir cadeias de filtro para o Estruturador da tabela

## <a name="overview"></a>Descrição geral

Para filtrar dados numa tabela do Azure que é apresentado no Visual Studio **Estruturador da tabela**, construir uma cadeia de filtro e introduzi-lo para o campo de filtro. A sintaxe da cadeia filtro definida pelo WCF Data Services e é semelhante a uma cláusula WHERE de SQL, mas é enviada para o serviço de tabela através de um pedido de HTTP. O **Estruturador de tabelas** processa a codificação inicial para si, por isso, para filtrar um valor de propriedade pretendido, só precisa de introduzir o nome da propriedade, operador de comparação, valor dos critérios e opcionalmente, booleano operador no campo de filtro. Não tem de incluir a opção de consulta $filter como faria se foram construir um URL para a tabela através da [Referência de API do resto do armazenamento dos serviços](http://go.microsoft.com/fwlink/p/?LinkId=400447)de consulta.

Os serviços de dados WCF são baseados no [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Para obter detalhes sobre a opção de consulta do sistema de filtro (**$filter**), consulte a [especificação de convenções de URI de OData](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Operadores de comparação

São suportados os seguintes operadores lógicos para todos os tipos de propriedade:

|Operador lógico|Descrição|Exemplo de cadeia de filtro|
|---|---|---|
|EQ|Igual|Localidade eq 'Redmond'|
|gt|Maior que|Preço gt 20|
|página|Maior ou igual a|Página do preço 10|
|lt|Menor que|Preço lt 20|
|Le|Menor ou igual|Le preço 100|
|não|Não é igual|Localidade 'Londres'|
|e|E|Le preço 200 e preço gt 3.5|
|ou|Ou|Le preço 3.5 ou preço gt 200|
|não|Não|não isAvailable|

Quando construir uma cadeia de filtro, é importantes as seguintes regras:

- Utilize os operadores lógicos para comparar uma propriedade para um valor. Tenha em atenção que não é possível comparar uma propriedade para um valor dinâmico; um lado da expressão tem de ser uma constante.

- Todas as partes da cadeia de filtro estão entre maiúsculas e minúsculas.

- O valor da constante deve ter o mesmo tipo de dados como a propriedade na ordem para o filtro para devolver resultados válidos. Para mais informações sobre tipos de propriedade suportados, consulte o artigo [Compreender o modelo de dados do serviço de tabela](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Propriedades da cadeia de filtragem

Quando aplica um filtro as propriedades de cadeia, coloque a constante de cadeia plicas.

Os filtros de exemplo seguintes nas propriedades **PartitionKey** e **RowKey** ; Propriedades de que não sejam chave adicionais também foi adicionadas à cadeia de filtro:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Pode incluir cada expressão de filtro entre parênteses, apesar de não é necessário:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Repare que o serviço de tabela não suporta universais consultas e não são suportadas no Estruturador da tabela quer. No entanto, pode executar o prefixo correspondência utilizando operadores de comparação no prefixo pretendido. O exemplo seguinte devolve entidades com um início de propriedade apelido com a letra 'A':

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrar as propriedades numéricos

Para filtrar num número inteiro ou número de vírgula flutuante, especifique o número sem as aspas.

Este exemplo devolve todas as entidades com uma propriedade de idade cujo valor é maior do que 30:

    Age gt 30

Este exemplo devolve todas as entidades com uma propriedade de AmountDue cujo valor é menor ou igual a 100.25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrar num Propriedades booleanas

Para filtrar um valor booleano, especifique **Verdadeiro** ou **Falso** sem as aspas.

O exemplo seguinte devolve todas as entidades onde a propriedade de IsActive está definida como **Verdadeiro**:

    IsActive eq true

Também pode escrever esta expressão de filtro sem o operador lógico. No exemplo seguinte, o serviço de tabela também devolverá todas as entidades que IsActive seja **verdadeira**:

    IsActive

Para devolver todas as entidades onde IsActive for FALSO, pode utilizar não operador:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrar as propriedades de data/hora

Para filtrar um valor DateTime, especifique a palavra-chave do **datetime** , seguida a constante de data/hora plicas. A constante de data/hora tem de ser no formato de UTC combinado, conforme descrito na [Formatação de valores de propriedade de data/hora](http://go.microsoft.com/fwlink/p/?LinkId=400449).

O exemplo seguinte devolve entidades onde a propriedade de CustomerSince é igual a 10 de Julho de 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
