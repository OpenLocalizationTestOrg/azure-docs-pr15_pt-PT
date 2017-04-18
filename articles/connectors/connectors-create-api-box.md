<properties
    pageTitle="Adicionar o conector de caixa às suas aplicações de lógica | Microsoft Azure"
    description="Descrição geral da conexão caixa com parâmetros REST API"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-box-connector"></a>Começar a trabalhar com o conector de caixa
Ligar a caixa e criar ficheiros, eliminar ficheiros e muito mais. 

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2015-08-01-pré-visualização de aplicações de lógica.

Com a caixa, pode:

- Crie o seu fluxo de negócio com base em dados que obtém a partir da caixa. 
- Utilize accionadores quando um ficheiro é criado ou atualizado.
- Utilize as ações que copiar um ficheiro, eliminar um ficheiro e muito mais. Estas ações obtém uma resposta e, em seguida, disponibilizam o resultado para outras ações. Por exemplo, quando um ficheiro é alterado na caixa, pode levá-lo e e-mail-utilizar o Office 365.

Para adicionar uma operação nas aplicações de lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções
Caixa inclui as seguintes acionador e ações.

| Accionadores | Ações|
| --- | --- |
|<ul><li>Quando é criado um ficheiro</li><li>Quando um ficheiro é modificado</li></ul> | <ul><li>Criar o ficheiro</li><li>Quando é criado um ficheiro</li><li>Ficheiro de cópia</li><li>Eliminar o ficheiro</li><li>Extrair arquivar a pasta</li><li>Obter o conteúdo do ficheiro com o id</li><li>Aceder a conteúdo através de caminho do ficheiro</li><li>Começar com o id de metadados de ficheiro</li><li>Obter metadados do ficheiro utilizando caminho</li><li>Ficheiro de actualização</li><li>Quando um ficheiro é modificado</li></ul>

Todas as conexões suportam dados nos formatos JSON e XML.

## <a name="create-a-connection-to-box"></a>Criar uma ligação à caixa
Quando adiciona este conector às suas aplicações de lógica, tem de autorizar lógica aplicações para ligar à sua caixa de.

>[AZURE.INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]

Depois de criar a ligação, introduzir as propriedades da caixa. A **REST API referência** neste tópico descreve estas propriedades.

>[AZURE.TIP] Pode utilizar esta ligação caixa mesmo nas outras aplicações de lógica.

## <a name="swagger-rest-api-reference"></a>Referência de REST API swagger
Aplica-se para a versão: 1.0.

### <a name="create-file"></a>Criar o ficheiro
Carrega um ficheiro de caixa.  
```POST: /datasets/default/files```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|caminhopasta|cadeia|Sim|consulta|Nenhum |Caminho de pasta para carregar o ficheiro para a caixa|
|nome|cadeia|Sim|consulta|Nenhum |Nome do ficheiro para criar na caixa|
|corpo|String(Binary) |Sim|corpo|Nenhum |Conteúdo do ficheiro para carregar para a caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="when-a-file-is-created"></a>Quando é criado um ficheiro
Um fluxo de accionadores quando é criado um novo ficheiro numa pasta caixa.  
```GET: /datasets/default/triggers/onnewfile```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID pasta|cadeia|Sim|consulta|Nenhum |Identificador exclusivo da pasta na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="copy-file"></a>Ficheiro de cópia
Copia um ficheiro à caixa.  
```POST: /datasets/default/copyFile```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|origem|cadeia|Sim|consulta|Nenhum |URL para o ficheiro de origem|
|destino|cadeia|Sim|consulta| Nenhum|Caminho do ficheiro de destino na caixa de nome de ficheiro de destino|
|substituir|Booleano|N|consulta| Nenhum|Substitui o ficheiro de destino, se definida como 'true'|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="delete-file"></a>Eliminar o ficheiro
Elimina um ficheiro a partir da caixa.  
```DELETE: /datasets/default/files/{id}```


| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia|Sim|caminho|Nenhum |Identificador exclusivo do ficheiro para eliminar a partir da caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="extract-archive-to-folder"></a>Extrair arquivar a pasta
Extrai um ficheiro de arquivo para uma pasta na caixa (exemplo:. zip).  
```POST: /datasets/default/extractFolderV2```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|origem|cadeia|Sim|consulta| |Caminho para o ficheiro de arquivo|
|destino|cadeia|Sim|consulta| |Caminho na caixa para extrair o conteúdo do arquivo|
|substituir|Booleano|N|consulta| |Substitui os ficheiros de destino, se definida como 'true'|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="get-file-content-using-id"></a>Obter o conteúdo do ficheiro com o id
Obtém o conteúdo do ficheiro a partir da caixa com o id.  
```GET: /datasets/default/files/{id}/content```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia|Sim|caminho|Nenhum |Identificador exclusivo do ficheiro na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="get-file-content-using-path"></a>Aceder a conteúdo através de caminho do ficheiro
Obtém o conteúdo do ficheiro a partir da caixa utilizando caminho.  
```GET: /datasets/default/GetFileContentByPath```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|caminho|cadeia|Sim|consulta|Nenhum |Caminho exclusivo para o ficheiro na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="get-file-metadata-using-id"></a>Começar com o id de metadados de ficheiro
Obtém metadados de ficheiro a partir da caixa utilizando o id do ficheiro.  
```GET: /datasets/default/files/{id}```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia|Sim|caminho| Nenhum|Identificador exclusivo do ficheiro na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="get-file-metadata-using-path"></a>Obter metadados do ficheiro utilizando caminho
Obtém metadados de ficheiro a partir da caixa utilizando caminho.  
```GET: /datasets/default/GetFileByPath```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|caminho|cadeia|Sim|consulta|Nenhum |Caminho exclusivo para o ficheiro na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="update-file"></a>Ficheiro de actualização
Atualiza um ficheiro na caixa.  
```PUT: /datasets/default/files/{id}```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID|cadeia|Sim|caminho| Nenhum|Identificador exclusivo do ficheiro para atualizar na caixa|
|corpo|String(Binary) |Sim|corpo|Nenhum |Conteúdo do ficheiro para atualizar na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="when-a-file-is-modified"></a>Quando um ficheiro é modificado
Um fluxo de accionadores quando um ficheiro é modificado numa pasta caixa.  
```GET: /datasets/default/triggers/onupdatedfile```

| Nome|Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|ID pasta|cadeia|Sim|consulta|Nenhum |Identificador exclusivo da pasta na caixa|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


## <a name="object-definitions"></a>Definições de objecto

#### <a name="datasetsmetadata"></a>DataSetsMetadata

|Nome da propriedade | Tipo de dados | Obrigatório|
|---|---|---|
|em tabela|não definido|nenhum|
|blob|não definido|nenhum|

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|origem|cadeia|nenhum|
|DisplayName em grupos|cadeia|nenhum|
|urlEncoding|cadeia|nenhum|
|tableDisplayName|cadeia|nenhum|
|tablePluralName|cadeia|nenhum|

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|origem|cadeia|nenhum|
|DisplayName em grupos|cadeia|nenhum|
|urlEncoding|cadeia|nenhum|

#### <a name="blobmetadata"></a>BlobMetadata

|Nome da propriedade | Tipo de dados |Obrigatório|
|---|---|---|
|ID|cadeia|nenhum|
|Nome|cadeia|nenhum|
|DisplayName em grupos|cadeia|nenhum|
|Caminho|cadeia|nenhum|
|Última modificação|cadeia|nenhum|
|Tamanho|número inteiro|nenhum|
|MediaType|cadeia|nenhum|
|IsFolder|Booleano|nenhum|
|ETag|cadeia|nenhum|
|FileLocator|cadeia|nenhum|

## <a name="next-steps"></a>Próximos passos

[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).
