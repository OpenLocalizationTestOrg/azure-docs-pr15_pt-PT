<properties 
    pageTitle="Guia de introdução: máquina aprendizagem recomendações API | Microsoft Azure" 
    description="Azure máquina aprendizagem recomendações - guia de introdução" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-machine-learning-recommendations-api"></a>Guia de introdução para a API de recomendações de aprendizagem máquina

>[AZURE.NOTE]Deverá começar a utilizar o serviço de disfunção API recomendações em vez de nesta versão. O serviço de disfunção recomendações irá substituição deste serviço e todas as novas funcionalidades, serão desenvolvidas aí. Tem novas funcionalidades como lotes suporte, uma melhor API Explorer, uma experiência de início de sessão no/faturação superfície, mais consistente API mais limpeza, etc.
> Saiba mais sobre como [Migrar para o novo serviço disfunção](http://aka.ms/recomigrate)


Este documento descreve como incorporada para o serviço ou aplicação para utilizar recomendações de formação do Microsoft Azure máquina. Pode encontrar mais detalhes sobre a API recomendações na [Galeria](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="general-overview"></a>Descrição geral

Para utilizar recomendações de aprendizagem do Azure máquina, terá de siga os passos seguintes:

* Criar um modelo de-um modelo é um contentor de dados de utilização, dados do catálogo e o modelo de recomendação.
* Importar dados de catálogo - um catálogo contém informações de metadados nos itens. 
* Importar dados de utilização - dados de utilização podem ser carregados numa das formas de 2 (ou ambas):
    * Ao carregar um ficheiro que contém os dados de utilização.
    * Ao enviar eventos de aquisição de dados.
    Normalmente carregar um ficheiro de utilização para poder criar um modelo de recomendação inicial (arranque) e utilizá-la até o sistema reúne suficiente dados utilizando o formato de aquisição de dados.
* Construa um modelo de recomendação - esta é uma operação assíncrona na qual o sistema de recomendação leva-o até todos os dados de utilização e cria um modelo de recomendação. Esta operação pode demorar alguns minutos ou várias horas dependendo do tamanho dos dados e os parâmetros de configuração da compilação. Quando acionar a compilação, irá obter um ID de compilação. Utilizá-lo para verificar quando o processo de criação foi terminado antes de começar a consumir recomendações.
* Consuma recomendações - obter recomendações para um item específico ou lista de itens.

Os passos acima são efetuados através da API de recomendações de aprendizagem do Azure máquina.  Pode transferir uma aplicação de exemplo que cada um dos seguintes passos de implementa o [também o gallery.](http://1drv.ms/1xeO2F3)

##<a name="limitations"></a>Limitações

* O número máximo de modelos de por subscrição é 10.
* O número máximo de itens que pode conter um catálogo é 100,000.
* O número máximo de pontos de utilização que são mantidas é ~ 5,000,000. O mais antigo será eliminado se novos vão ser carregados ou comunicados.
* O tamanho máximo de dados que podem ser enviados mensagem (por exemplo, importar catálogo dados, importar dados de utilização) é 200MB.
* O número de transações por segundo para uma compilação de modelo de recomendação que não esteja ativa é ~ 2TPS. Uma compilação de modelo de recomendação que está ativa pode sem soltar 20TPS.

##<a name="integration"></a>Integração

###<a name="authentication"></a>Autenticação
O Azure Marketplace suporta as Basic ou OAuth método de autenticação. Pode encontrar facilmente as teclas de conta ao navegar para as teclas no mercado nas [suas definições de conta](https://datamarket.azure.com/account/keys). 
####<a name="basic-authentication"></a>Autenticação básica
Adicione o cabeçalho de autorização:

    Authorization: Basic <creds>
               
    Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
    
Converter Base64 (c#)

    var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
    var creds = Convert.ToBase64String(bytes);
    
Converter Base64 (JavaScript)

    var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
    



###<a name="service-uri"></a>Serviço URI
A raiz de serviço URI para o Azure máquina aprendizagem recomendações APIs é [aqui.](https://api.datamarket.azure.com/amla/recommendations/v2/)

O serviço completo URI é expresso utilizando elementos da especificação de OData.

###<a name="api-version"></a>Versão da API
No final, cada chamada à API terá um parâmetro de consulta denominado apiVersion deve estar definida como "1.0".

###<a name="ids-are-case-sensitive"></a>IDs são sensíveis a maiúsculas
IDs de devolvido por qualquer uma das APIs, são sensíveis a maiúsculas e deverão ser utilizados como tal, quando passado como parâmetros na API as chamadas subsequentes. Por exemplo, IDs de modelo e IDs de catálogo são sensíveis a maiúsculas.

###<a name="create-a-model"></a>Criar um modelo
Criar um pedido de "Criar modelo":

| Método de HTTP | URI |
|:--------|:--------|
|MENSAGEM     |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelName   |   Letras apenas números (A-Z, -z) (0-9), hífenes (-) e sublinhado (_) são permitidos.<br>Comprimento máximo: 20 |
|   apiVersion      | 1.0 |
|||
| Corpo do pedido | NENHUM |


**Resposta**:

Código de estado HTTP: 200

- `feed/entry/content/properties/id`-Contém o ID do modelo.
**Nota**: o ID de modelo é sensível a maiúsculas.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>


###<a name="import-catalog-data"></a>Importar dados de catálogo

Se carregar vários ficheiros de catálogo para o mesmo modelo com várias chamadas, podemos irá inserir apenas os novos itens de catálogo. Itens existentes irão permanecer com os valores originais.

| Método de HTTP | URI |
|:--------|:--------|
|MENSAGEM     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId |   Identificador exclusivo do modelo (sensível a maiúsculas e minúsculas)  |
| nome de ficheiro | Identificador textual do catálogo.<br>Letras apenas números (A-Z, -z) (0-9), hífenes (-) e sublinhado (_) são permitidos.<br>Comprimento máximo: 50 |
|   apiVersion      | 1.0 |
|||
| Corpo do pedido | Os catálogos de dados. Formato:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Nome</th><th>Obrigatório</th><th>Tipo</th><th>Descrição</th></tr><tr><td>Id do item</td><td>Sim</td><td>Comprimento alfanumérico, max 50</td><td>Identificador exclusivo de um item</td></tr><tr><td>Nome do item</td><td>Sim</td><td>Comprimento alfanumérico, max 255</td><td>Nome do item</td></tr><tr><td>Categoria do produto</td><td>Sim</td><td>Comprimento alfanumérico, max 255</td><td>Categoria à qual este item pertence (por exemplo, cozinha livros, teatro...)</td></tr><tr><td>Descrição</td><td>N</td><td>Comprimento alfanumérico, max 4000</td><td>Descrição do produto</td></tr></table><br>Tamanho máximo do ficheiro é 200MB.<br><br>Exemplo:<br><pre>2406e770-769c-4189-89de-1c9283f93a96, Clara Callan, livro de endereços<br>21bf8088-b6c0-4509-870c-e1c7ac78304a, a sala esquecer: uma Fiction (Byzantium livro), do livro<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23, Spadework, livro de endereços<br>552a1940-21e4-4399-82bb-594b46d7ed54, retenção de animais, livro de endereços</pre> |


**Resposta**:

Código de estado HTTP: 200

- `Feed\entry\content\properties\LineCount`-Aceite número de linhas.
- `Feed\entry\content\properties\ErrorCount`-Número de linhas que não foram inseridas devido a um erro.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
        <subtitle type="text">Import catalog file</subtitle>
        <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
     </entry>
    </feed>


###<a name="import-usage-data"></a>Importar dados de utilização

####<a name="uploading-a-file"></a>Carregar um ficheiro
Esta secção mostra como carregar dados de utilização utilizando um ficheiro. Pode ligar a esta API várias vezes com dados de utilização. Todos os dados de utilização serão guardados para todas as chamadas.

| Método de HTTP | URI |
|:--------|:--------|
|MENSAGEM     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId |   Identificador exclusivo do modelo (sensível a maiúsculas e minúsculas) |
| nome de ficheiro | Identificador textual do catálogo.<br>Letras apenas números (A-Z, -z) (0-9), hífenes (-) e sublinhado (_) são permitidos.<br>Comprimento máximo: 50 |
|   apiVersion      | 1.0 |
|||
| Corpo do pedido | Dados de utilização. Formato:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nome</th><th>Obrigatório</th><th>Tipo</th><th>Descrição</th></tr><tr><td>Id de utilizador</td><td>Sim</td><td>Alfanuméricos</td><td>Identificador exclusivo de um utilizador</td></tr><tr><td>Id do item</td><td>Sim</td><td>Comprimento alfanumérico, max 50</td><td>Identificador exclusivo de um item</td></tr><tr><td>Tempo</td><td>N</td><td>Data no formato: aaaa/MM/ddTHH (por exemplo, 2013/06/20T10:00:00)</td><td>Tempo de dados</td></tr><tr><td>Evento</td><td>Não, se fornecido, em seguida, também tem de colocar data</td><td>Um dos seguintes procedimentos:<br>• Clique em<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Compra</td><td></td></tr></table><br>Tamanho máximo do ficheiro é 200MB.<br><br>Exemplo:<br><pre>149452, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645, 1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951, 1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**Resposta**:

Código de estado HTTP: 200

- `Feed\entry\content\properties\LineCount`-Aceite número de linhas.
- `Feed\entry\content\properties\ErrorCount`-Número de linhas que não foram inseridas devido a um erro.
- `Feed\entry\content\properties\FileId`-Identificador de ficheiros.


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="using-data-acquisition"></a>Aquisição de dados a utilizar
Esta secção mostra como enviar eventos em tempo real para recomendações de aprendizagem do Azure máquina, normalmente, a partir do seu Web site.

| Método de HTTP | URI |
|:--------|:--------|
|MENSAGEM     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
|Corpo do pedido| Introdução de dados de evento para cada evento que pretende enviar. Deverá enviar para a mesma sessão de utilizador ou browser o mesmo ID no campo ID de sessão. (Consulte exemplo do corpo de evento abaixo).|


- Exemplo de evento 'Clique em':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemplo de evento 'RecommendationClick':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RecommendationClick</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemplo de evento 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemplo de evento 'RemoveShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RemoveShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Exemplo de evento 'Compra':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
            <Name>Purchase</Name> 
            <PurchaseItems>
            <PurchaseItems>
                <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                <Count>3</Count>
            </PurchaseItems>
        </PurchaseItems>
        </EventData>
        </EventData>
        </Event>

- Exemplo 2 eventos, 'Clique' e 'AddShopCart' de enviar:

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        <ItemName>itemName</ItemName>
        <ItemDescription>item description</ItemDescription>
        <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
        </EventData>
        </Event>

**Resposta**: código de estado HTTP: 200

###<a name="build-a-recommendation-model"></a>Construa um modelo de recomendação

| Método de HTTP | URI |
|:--------|:--------|
|MENSAGEM     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
| modelId | Identificador exclusivo do modelo (sensível a maiúsculas e minúsculas)  |
| userDescription | Identificador textual do catálogo. Tenha em atenção que se utiliza o espaços tem codificá-lo com % 20 em vez disso. Consulte o artigo exemplo acima.<br>Comprimento máximo: 50 |
| apiVersion | 1.0 |
|||
| Corpo do pedido | NENHUM |

**Resposta**:

Código de estado HTTP: 200

Este é uma API assíncrona. Irá obter um ID de compilação como uma resposta. Para saber quando foi terminado a compilação, deve chamar a API "Obter constrói estado de um modelo" e localizar este ID de compilação na resposta. Tenha em atenção que uma compilação pode tomar a partir de minutos para horas, dependendo do tamanho dos dados.

Não é possível consumir recomendações até a compilação termina.

Estado de compilação válida:

- Criar – modelo foi criado.
- Na fila – compilação de modelo foi acionou e -está em fila de espera.
- Edifício – modelo está a ser criado.
- Sucesso – compilação terminou com êxito.
- Erro de – compilação terminou com uma falha.
- Compilar cancelada – foi cancelada.
- Cancelar – compilação está a ser cancelada.


Tenha em atenção que pode ser encontrado o ID de compilação sob o caminho seguinte:`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
    </entry>
    </feed>

###<a name="get-build-status-of-a-model"></a>Obter o estado de compilação de um modelo

| Método de HTTP | URI |
|:--------|:--------|
|OBTER     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId         |   Identificador exclusivo do modelo (sensível a maiúsculas e minúsculas)    |
|   onlyLastBuild   |   Indica se deve devolver todos os o histórico de compilação do modelo ou apenas o estado da compilação mais recente. |
|   apiVersion      |   1.0                                 |


**Resposta**:

Código de estado HTTP: 200

A resposta inclui uma entrada por compilação. Cada entrada tem os seguintes dados:

- `feed/entry/content/properties/UserName`– Nome do utilizador.
- `feed/entry/content/properties/ModelName`– Nome do modelo.
- `feed/entry/content/properties/ModelId`– Identificador exclusivo do modelo.
- `feed/entry/content/properties/IsDeployed`– Se a compilação é implementada (também conhecido como Compilar ativo).
- `feed/entry/content/properties/BuildId`– Crie Identificador exclusivo.
- `feed/entry/content/properties/BuildType`-Tipo da compilação.
- `feed/entry/content/properties/Status`– Crie estado. Pode ser um dos seguintes: erro, edifício, em fila de espera, Cancelling, cancelada, sucesso
- `feed/entry/content/properties/StatusMessage`– Mensagem de estado detalhadas (aplicável apenas ao Unidos específicos).
- `feed/entry/content/properties/Progress`– Crie progresso (%).
- `feed/entry/content/properties/StartTime`– Construa a hora de início.
- `feed/entry/content/properties/EndTime`– Construa a hora de fim.
- `feed/entry/content/properties/ExecutionTime`– Construa a duração.
- `feed/entry/content/properties/ProgressStep`– Obter detalhes sobre a fase actual se encontra uma compilação em curso.

Estado de compilação válida:
- Entrada do pedido de compilação criado – foi criada.
- Na fila – compilação pedido foi acionou e -está em fila de espera.
- Edifício – compilação esteja no processo.
- Sucesso – compilação terminou com êxito.
- Erro de – compilação terminou com uma falha.
- Compilar cancelada – foi cancelada.
- Cancelar – compilação está a ser cancelada.

Valores válidos para construir tipo:
- Classificação - Construa a classificação. (Para rank construir detalhes, consulte para o documento "Documentação máquina aprendizagem recomendação API".)
- Recomendação - compilação de recomendação.
- Fbt - frequentemente em conjunto comprada compilação.

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get builds status of a model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###<a name="get-recommendations"></a>Obter recomendações

| Método de HTTP | URI |
|:--------|:--------|
|OBTER     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
| modelId | Identificador exclusivo do modelo (sensível a maiúsculas e minúsculas) |
| itemIds | Lista dos produtos que recomendamos para separados por vírgulas.<br>Comprimento máximo: 1024 |
| numberOfResults | Número de resultados necessários |
| includeMetatadata | Utilização futura sempre false |
| apiVersion | 1.0 |

**Resposta:**

Código de estado HTTP: 200

A resposta inclui uma entrada por item recomendada. Cada entrada tem os seguintes dados:

- `Feed\entry\content\properties\Id`-ID do item recomendadas.
- `Feed\entry\content\properties\Name`-Nome do item.
- `Feed\entry\content\properties\Rating`-Classificação da recomendação; número mais alto significa confiança superior.
- `Feed\entry\content\properties\Reasoning`-Recomendação argumentos (por exemplo, explicações recomendação).

OData XML

A resposta de exemplo abaixo inclui 10 itens iniciais recomendadas:

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="update-model"></a>Modelo de actualização
Pode atualizar a descrição do modelo ou o ID de compilação ativo.
*ID de compilação ativo* - cada compilação para cada modelo tem um ID de compilação. O ID de compilação ativo é a primeira compilação bem sucedida de cada novo modelo. Assim que tiver um ID de compilação ativa e o que fazer constrói adicionais para o mesmo modelo, é necessário explicitamente defini-lo como o ID de compilação predefinido se pretender. Quando consumir recomendações, se não especificar o ID de compilação que pretende utilizar, a predefinição um será utilizada automaticamente.

Este mecanismo permite-lhe - assim que tiver um modelo de recomendação no produção - para construir modelos novos e testá-las antes de promover produção.

| Método de HTTP | URI |
|:--------|:--------|
|COLOCAR     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Exemplo:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|   Nome do parâmetro  |   Valores válidos                        |
|:--------          |:--------                              |
| ID | Identificador exclusivo do modelo (sensível a maiúsculas e minúsculas) |
| apiVersion | 1.0 |
|||
| Corpo do pedido | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Tenha em atenção que as etiquetas XML, descrição e ActiveBuildId são opcionais. Se não pretende definir descrição ou ActiveBuildId, remova a totalidade do código. |

**Resposta**:

Código de estado HTTP: 200

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Update an Existing Model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
    <rights type="text" />
     <updated>2014-10-05T10:27:17Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
    </feed>

##<a name="legal"></a>Legal
Este documento é fornecido "como-é". Informações e vistas expressas neste documento, incluindo o URL e outras referências de Web site da Internet, podem ser alteradas sem aviso. Alguns exemplos descritos neste artigo são fornecidos para ilustração apenas e são fictícios. Sem associação real ou a ligação destina-se deve ser inferida. Este documento não fornecer-lhe direitos legais a nenhuma propriedade intelectual em qualquer produto da Microsoft. Pode copiar e utilize este documento interna, para fins de referência. © 2014 Microsoft. Todos os direitos reservados. 
 
