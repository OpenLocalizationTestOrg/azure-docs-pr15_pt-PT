<properties
    pageTitle="Adicionar o armazenamento de Blobs do Azure conexão nas suas aplicações de lógica | Microsoft Azure"
    description="Descrição geral de armazenamento de Blobs do Azure conector com parâmetros REST API"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="anneta"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-azure-blob-storage-connector"></a>Começar a trabalhar com o conector de armazenamento de Blobs do Azure
Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados. Executar várias ações, como carregar, atualizar, obtenha e eliminar blobs no armazenamento de Blobs do Azure. 

Com o armazenamento de Blobs do Azure,:

- Construa o seu fluxo de trabalho ao carregar novos projectos, ou obter ficheiros que foram atualizados recentemente.
- Utilize as ações para obter os metadados de ficheiro, eliminar um ficheiro, copiar ficheiros e muito mais. Por exemplo, quando uma ferramenta é atualizada num web site Azure (um accionador), em seguida, atualize um ficheiro no armazenamento de BLOBs (uma ação). 

Este tópico mostra-lhe como utilizar o conector de armazenamento de BLOBs numa aplicação lógica e também lista as ações.

>[AZURE.NOTE] Esta versão do artigo aplica-se para disponibilidade geral lógica aplicações (das versões DG). 

Para saber mais sobre aplicações de lógica, consulte o artigo [o que são as aplicações de lógica](../app-service-logic/app-service-logic-what-are-logic-apps.md) e [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-azure-blob-storage"></a>Ligar ao armazenamento de Blobs do Azure

Antes de pode aceder a sua aplicação de lógica qualquer serviço, primeiro, criar uma *ligação* ao serviço. Uma ligação fornece conectividade entre uma aplicação de lógica e outro serviço. Por exemplo, para ligar a uma conta de armazenamento, pela primeira vez criar uma de armazenamento de BLOBs *ligação*. Para criar uma ligação, introduza as credenciais que normalmente utiliza para aceder ao serviço que está a ligar. Por isso, com o Azure armazenamento, introduza as credenciais para a conta de armazenamento para criar a ligação. 

#### <a name="create-the-connection"></a>Criar a ligação

>[AZURE.INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]
 
## <a name="use-a-trigger"></a>Utilize um acionador de

Este conector não tem qualquer accionadores. Utilize os outros accionadores para iniciar a aplicação de lógica, tais como um acionador de periodicidade, um acionador de HTTP Webhook, accionadores disponíveis com outros conectores e muito mais. [Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) fornece um exemplo.

## <a name="use-an-action"></a>Utilizar uma ação
    
Uma ação é uma operação realizada pelo fluxo de trabalho definido numa aplicação de lógica.

1. Selecione o sinal de adição. Verá várias opções: **Adicionar uma ação**, **Adicionar uma condição**ou uma das opções **mais** .

    ![](./media/connectors-create-api-azureblobstorage/add-action.png)

2. Selecione **Adicionar uma ação**.

3. Na caixa de texto, escreva "blob" para obter uma lista de todas as ações disponíveis.

    ![](./media/connectors-create-api-azureblobstorage/actions.png) 

4. No nosso exemplo, escolha **AzureBlob - obter utilizando caminho de metadados de ficheiro**. Se já existe uma ligação, em seguida, selecione **…** (Mostrar) Image para selecionar um ficheiro.

    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)

    Se lhe for pedido para as informações de ligação, em seguida, introduza os detalhes para criar a ligação. [Criar a ligação](connectors-create-api-azureblobstorage.md#create-the-connection) neste tópico descreve estas propriedades. 

    > [AZURE.NOTE] Neste exemplo, vamos obter os metadados de um ficheiro. Para ver os metadados, adicione outra ação que cria um novo ficheiro utilizando outro conector. Por exemplo, adicione uma ação de OneDrive cria um novo ficheiro "Testar" com base nos metadados. 

5. **Guardar** as suas alterações (canto superior esquerdo da barra de ferramentas). A sua aplicação de lógica está guardada e pode ser activada automaticamente.

> [AZURE.TIP] [Explorador de armazenamento](http://storageexplorer.com/) é uma ótima ferramenta para gerir múltiplas contas de armazenamento.

## <a name="technical-details"></a>Detalhes técnicos

## <a name="storage-blob-actions"></a>Ações de Blobs do armazenamento

|Ação|Descrição|
|--- | ---|
|[Obter metadados de ficheiro](connectors-create-api-azureblobstorage.md#get-file-metadata)|Esta operação obtém os metadados de ficheiro com o id de ficheiro.|
|[Ficheiro de actualização](connectors-create-api-azureblobstorage.md#update-file)|Esta operação atualiza um ficheiro.|
|[Eliminar o ficheiro](connectors-create-api-azureblobstorage.md#delete-file)|Esta operação elimina um ficheiro.|
|[Obter metadados do ficheiro utilizando caminho](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path)|Esta operação obtém utilizando o caminho de metadados de ficheiro.|
|[Aceder a conteúdo através de caminho do ficheiro](connectors-create-api-azureblobstorage.md#get-file-content-using-path)|Esta operação obtém conteúdo utilizando o caminho do ficheiro.|
|[Obter o conteúdo do ficheiro](connectors-create-api-azureblobstorage.md#get-file-content)|Esta operação obtém o conteúdo do ficheiro com o id.|
|[Criar o ficheiro](connectors-create-api-azureblobstorage.md#create-file)|Esta operação carrega um ficheiro.|
|[Ficheiro de cópia](connectors-create-api-azureblobstorage.md#copy-file)|Esta operação copia um ficheiro ao armazenamento de Blobs do Azure.|
|[Extrair arquivar a pasta](connectors-create-api-azureblobstorage.md#extract-archive-to-folder)|Esta operação extrai um ficheiro de arquivo para uma pasta (exemplo:. zip).|

### <a name="action-details"></a>Detalhes de ação

Nesta secção, consulte o artigo os detalhes específicos sobre cada ação, incluindo as propriedades de entrada opcionais ou obrigatórias e qualquer associados com o conector de saída correspondente.

#### <a name="get-file-metadata"></a>Obter metadados de ficheiro
Esta operação obtém os metadados de ficheiro com o id de ficheiro.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID de *|Ficheiro|Selecionar um ficheiro|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
BlobMetadata

| Nome da propriedade | Tipo de dados |
|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|


#### <a name="update-file"></a>Ficheiro de actualização
Esta operação atualiza um ficheiro.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID de *|Ficheiro|Selecionar um ficheiro|
|corpo *|Conteúdo do ficheiro|Conteúdo do ficheiro para atualizar|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
BlobMetadata

| Nome da propriedade | Tipo de dados |
|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|


#### <a name="delete-file"></a>Eliminar o ficheiro
Esta operação elimina um ficheiro.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID de *|Ficheiro|Selecionar um ficheiro|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
Nenhum.


#### <a name="get-file-metadata-using-path"></a>Obter metadados do ficheiro utilizando caminho
Esta operação obtém utilizando o caminho de metadados de ficheiro.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|caminho *|Caminho do ficheiro|Selecionar um ficheiro|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
BlobMetadata

| Nome da propriedade | Tipo de dados |
|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|


#### <a name="get-file-content-using-path"></a>Aceder a conteúdo através de caminho do ficheiro
Esta operação obtém conteúdo utilizando o caminho do ficheiro.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|caminho *|Caminho do ficheiro|Selecionar um ficheiro|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
Nenhum.


#### <a name="get-file-content"></a>Obter o conteúdo do ficheiro
Esta operação obtém o conteúdo do ficheiro com o id.  

|Nome da propriedade| Tipo de dados|Descrição|
| ---|---|---|
|ID de *|cadeia|Selecionar um ficheiro|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
Nenhum.


#### <a name="create-file"></a>Criar o ficheiro
Esta operação carrega um ficheiro.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|caminhopasta *|Caminho da pasta|Selecione uma pasta|
|nome *|Nome do ficheiro|Nome do ficheiro para carregar|
|corpo *|Conteúdo do ficheiro|Conteúdo do ficheiro para carregar|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
BlobMetadata

| Nome da propriedade | Tipo de dados | 
|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|


#### <a name="copy-file"></a>Ficheiro de cópia
Esta operação copia um ficheiro ao armazenamento de Blobs do Azure.  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|origem *|Url de origem|Especificar o Url para o ficheiro de origem|
|destino *|Caminho do ficheiro de destino|Especificar o caminho do ficheiro de destino, incluindo o nome de ficheiro de destino|
|substituir|Substituir?|Deverá um ficheiro de destino existente ser substituído (verdadeiro/falso)?  |

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
BlobMetadata

| Nome da propriedade | Tipo de dados |
|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|

#### <a name="extract-archive-to-folder"></a>Extrair arquivar a pasta
Esta operação extrai um ficheiro de arquivo para uma pasta (exemplo:. zip).  

|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|origem *|Caminho do ficheiro de arquivo de origem|Selecione um ficheiro de arquivo|
|destino *|Caminho da pasta de destino|Selecionar os conteúdos para extrair|
|substituir|Substituir?|Deverá um ficheiro de destino existente ser substituído (verdadeiro/falso)?|

Um asterisco (*) significa que a propriedade é necessária.

##### <a name="output-details"></a>Detalhes de saída
BlobMetadata

| Nome da propriedade | Tipo de dados |
|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|


## <a name="http-responses"></a>Respostas HTTP

Quando efetuar chamadas para as ações diferentes, poderá receber determinadas respostas. A tabela seguinte descreve as respostas e as respectivas descrições:  

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido|
|predefinido|A operação falhou.|

## <a name="next-steps"></a>Próximos passos

[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore as outras conexões disponíveis nas aplicações de lógica na nossa [lista APIs](apis-list.md).



