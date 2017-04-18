<properties
    pageTitle="Funcionalidades de JSON de base de dados do SQL Azure | Microsoft Azure"
    description="Base de dados SQL Azure permite-lhe análise, consulta e formatar dados em notação JavaScript objeto notação (JSON)."
    services="sql-database"
    documentationCenter=""
    authors="jovanpop-msft"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/17/2016"
    ms.author="jovanpop"
   ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Introdução ao funcionalidades JSON base de dados do SQL Azure

Base de dados SQL Azure permite-lhe analisar e consultar dados representados num formato de JavaScript Object Notation [(JSON)](http://www.json.org/) e exporte os seus dados relacionais como texto JSON.

JSON é um formato de dados populares utilizado para o intercâmbio de dados web modernos e aplicações móveis. JSON é também utilizada para armazenar dados semiestruturados nos ficheiros de registo ou em NoSQL bases de dados como [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Serviços web no resto do muitos dos resultados devolvidos formatados como texto JSON ou aceitar dados formatados como JSON. Azure mais serviços como o [Azure pesquisa](https://azure.microsoft.com/services/search/), [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/)e [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) têm pontos finais resto que devolvem ou consumam JSON.

Base de dados SQL Azure lhe permite trabalhar com dados JSON facilmente e integrar a base de dados com serviços modernos.

## <a name="overview"></a>Descrição geral

Base de dados SQL Azure fornece as seguintes funções para trabalhar com dados JSON:

![Funções JSON](./media/sql-database-json-features/image_1.png)

Se tiver texto JSON, pode extrair dados de JSON ou certifique-se de que JSON está corretamente formatada utilizando as funções incorporadas [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)e [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). A função [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) permite-lhe actualizar valor dentro do texto JSON. Para mais avançada consultar e análise, função [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) pode transformar uma matriz de objetos JSON um conjunto de linhas. Qualquer consulta SQL pode ser executada no conjunto de resultados devolvidos. Por fim, existe uma cláusula [JSON para](https://msdn.microsoft.com/library/dn921882.aspx) que lhe permite formatar dados armazenados nas suas tabelas relacionais como texto JSON.

## <a name="formatting-relational-data-in-json-format"></a>Formatação de dados relacionais em formato JSON
Se tiver um serviço web que demora dados a partir da base de dados de camadas e fornece uma resposta em JSON formatar ou lado do cliente JavaScript quadros ou bibliotecas que aceitar dados formatados como JSON, pode formatar o conteúdo da base de dados como JSON diretamente numa consulta SQL. Já não tiver que escrever código da aplicação que formatos de resultados de base de dados do SQL Azure como JSON, ou incluir algumas biblioteca de serialização JSON para converter os resultados da consulta em tabela e, em seguida, serializar objectos para o formato JSON. Em vez disso, pode utilizar a cláusula JSON para formatar resultados da consulta SQL como JSON base de dados do SQL Azure e utilizá-lo diretamente na sua aplicação.

No seguinte exemplo, as linhas de Sales.Customer formatadas como JSON utilizando a cláusula JSON para:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

A cláusula para JSON caminho formata os resultados da consulta como texto JSON. Nomes de colunas são utilizados como chaves, enquanto os valores das células são gerados como valores JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

O conjunto de resultados é formatado como uma matriz JSON onde cada linha é formatada como um objeto JSON em separado.

CAMINHO indica que pode personalizar o formato de saída do seu resultado JSON utilizando a notação de ponto no aliases de coluna. A seguinte consulta muda o nome da chave de "NomeCliente" no formato JSON de saída e coloca os números de telefone e fax no objeto sub "Contacto":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

O resultado desta consulta tem o seguinte aspeto:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Neste exemplo estamos devolvido num único objeto JSON em vez de uma matriz ao especificar a opção [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) . Pode utilizar esta opção se souber que estiver a devolver um único objeto estatístico como resultado da consulta.

O valor principal da cláusula JSON para é que permite-lhe devolver dados hierárquicos complexos da sua base de dados formatado como objectos JSON aninhados ou matrizes. O exemplo seguinte mostra como incluir encomendas que pertencem ao cliente, como uma matriz aninhada de encomendas:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Em vez de enviar separar consultas para obter dados do cliente e, em seguida, para obter uma lista de encomendas relacionadas, pode obter todos os dados necessários com uma única consulta, conforme apresentado na saída de exemplo seguinte:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Trabalhar com dados JSON

Se não tiver dados estruturados estritamente, se tiver sub-objectos complexos, matrizes ou dados hierárquicos ou se desenvolva o seu estruturas de dados ao longo do tempo, o formato JSON pode ajudá-lo para representar qualquer estrutura de dados complexos.

JSON é um formato textual que pode ser utilizado como qualquer outro tipo de cadeia base de dados do SQL Azure. Pode enviar ou armazenar dados JSON como um NVARCHAR padrão:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Os dados JSON utilizados neste exemplo são representados com o tipo de tipo. JSON pode ser inserida nesta tabela ou fornecida como um argumento do procedimento armazenado utilizando sintaxe Transact-SQL padrão, conforme mostrado no exemplo seguinte:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Qualquer lado do cliente idioma ou a biblioteca que funciona com dados de cadeia base de dados do SQL Azure também funcionam com dados JSON. JSON pode ser armazenado em qualquer tabela compatível com o tipo NVARCHAR, tal como uma tabela de memória otimizado ou uma tabela com versões de sistema. JSON não apresentar qualquer restrição o código do lado do cliente ou na camada de base de dados.

## <a name="querying-json-data"></a>Consultar dados JSON

Se tiver dados formatados como JSON armazenado em tabelas do Azure SQL, funções JSON permitem-lhe utilizar estes dados em qualquer consulta SQL.

Funções JSON que estão disponíveis no permitem de base de dados do Azure SQL são processados os dados formatados como JSON como qualquer outro tipo de dados SQL. Pode facilmente extrair os valores de texto JSON e utilizar os dados JSON qualquer consulta:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

A função JSON_VALUE extrai um valor de texto JSON armazenado na coluna de dados. Esta função utiliza um caminho de gosto JavaScript para fazer referência a um valor em texto JSON para extrair. O valor extraído pode ser utilizado em qualquer parte de uma consulta SQL.

A função JSON_QUERY é semelhante a JSON_VALUE. Ao contrário JSON_VALUE, esta função extrai complexo sub-objeto como matrizes ou objetos que são colocados na texto JSON.

A função JSON_MODIFY permite-lhe especificar o caminho do valor no texto JSON que deve ser atualizado, bem como um novo valor que irá substituir a antigo. Desta forma poderá atualizar facilmente texto JSON sem reparsing toda a estrutura.

Uma vez que está armazenado JSON num texto padrão, não existem sem garantias que os valores armazenados nas colunas de texto estão formatados corretamente. Pode verificar que texto armazenado na coluna JSON corretamente está formatado com restrições de verificação de base de dados do Azure SQL padrão e a função ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Se o texto de entrada está formatado corretamente JSON, a função ISJSON devolve o valor 1. Em cada inserir ou atualização da coluna JSON, este constrangimento verificará que o novo valor de texto não está mal JSON.

## <a name="transforming-json-into-tabular-format"></a>Transformar JSON formato tabular

Base de dados SQL Azure também lhe permite transformar coleções JSON para formatar e carregar ou consulta JSON os dados de tabela.

OPENJSON é uma função de valor de tabela que analisa JSON texto, localiza uma matriz de objetos JSON, itera os elementos da matriz e devolve uma linha no resultado de saída para cada elemento dessas matrizes.

![JSON tabular](./media/sql-database-json-features/image_2.png)

No exemplo acima, podemos pode especificar onde localizar a matriz JSON que deve ser aberta (no $. Caminho de encomendas), que colunas devem ser devolvidas como resultado e onde encontrar os valores JSON que serão devolvidos como células.

Vamos pode transformar uma matriz JSON na @orders variável para um conjunto de linhas, analisar este conjunto de resultados, ou inserir linhas numa tabela padrão:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
A coleção de encomendas formatado como uma matriz JSON e fornecidos como um parâmetro para o procedimento armazenado pode ser analisado e inserido na tabela Encomendas.

## <a name="next-steps"></a>Próximos passos

Para saber como integrar JSON na sua aplicação, consulte estes recursos:

- [Blogue do TechNet](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
- [Documentação MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
- [Canal de vídeo de 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Para saber mais sobre vários cenários para integrar JSON a sua aplicação, consulte o artigo demos neste [9 de canal de vídeo](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou encontrar um cenário que corresponde ao seu casos de utilização na [JSON blogue](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).
