
<properties
    pageTitle="Adicionar a HTTP + Swagger ação em aplicações de lógica | Microsoft Azure"
    description="Descrição geral do HTTP + Swagger ação e operações"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http--swagger-action"></a>Começar a trabalhar com a HTTP + Swagger ação

Com a HTTP + Swagger ação, pode criar uma conexão primeira classe a qualquer ponto final resto através de um [documento de Swagger](https://swagger.io). Também é possível expandir uma aplicação de lógica para ligar a qualquer ponto final resto uma experiência de Designer da aplicação de lógica primeira classe.

Para começar a utilizar com o HTTP + Swagger ação numa aplicação lógica, consulte o artigo [criar uma nova aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Utilizar HTTP + Swagger como um accionador ou uma ação

A HTTP + Swagger acionador e ação funcionar da mesma forma a [ação de HTTP](connectors-native-http.md) mas fornecer uma melhor experiência de estrutura mostrando a forma, de API e saídas no estruturador de [Swagger metadados](https://swagger.io). Além disso, pode utilizar HTTP + Swagger como um accionador. Se pretender implementar um acionador de inquéritos, deve seguir o padrão de consulta que é descrito na [criação de uma API personalizada para utilizar com aplicações de lógica](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[Saiba mais sobre accionadores de aplicação de lógica e ações.](connectors-overview.md)

Eis um exemplo de como para utilizar o protocolo HTTP + Swagger operação como uma ação num fluxo de trabalho numa aplicação lógica.

1. Selecione o botão de **Novo passo** .
2. Selecione **Adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **swagger** para lista do HTTP + Swagger ação.

    ![Selecione HTTP + Swagger ação](./media/connectors-native-http-swagger/using-action-1.png)

4. Escreva o URL para um documento Swagger:
    - Trabalhar com o Estruturador da aplicação lógica, o URL tem de ser um ponto final HTTPS e ativou CORS.
    - Se o documento Swagger não cumprir este requisito, pode utilizar o [Armazenamento do Windows Azure com CORS ativado](#hosting-swagger-from-storage) para armazenar o documento.
5. Clique em **seguinte** para ler e compor a partir do documento Swagger.
6. Adicione quaisquer parâmetros que são necessários para a chamada HTTP.

    ![Ação de HTTP concluída](./media/connectors-native-http-swagger/using-action-2.png)

1. Clique em **Guardar** no canto superior esquerdo da barra de ferramentas e a lógica aplicação voltará ambos os guardar e publicar (ativar).

### <a name="host-swagger-from-azure-storage"></a>Anfitrião Swagger a partir do armazenamento Azure

Poderá pretender fazer referência um documento Swagger que não está alojado ou que não cumprir os requisitos de publicação em origem para o estruturador e segurança. Para resolver este problema, pode armazenar o documento Swagger no armazenamento do Windows Azure e ativar CORS a referenciar o documento.  

Eis os passos para criar, configurar e armazenar documentos de Swagger no armazenamento do Windows Azure:

1. [Criar uma conta de armazenamento Azure com armazenamento de Blobs do Azure](../storage/storage-create-storage-account.md). (Para efetuar esta ação, definir permissões de **acesso do público**.)
2. Ative o CORS no blob. Pode utilizar [Este script do PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) para configurar essa definição automaticamente.
3. Carregar o ficheiro Swagger para o blob. Pode fazê-lo a partir do [Azure portal](https://portal.azure.com) ou de uma ferramenta de como o [Explorador de armazenamento do Azure](http://storageexplorer.com/).
1. Fazer referência a uma ligação HTTPS para o documento no armazenamento de Blobs do Azure. (A ligação segue o formato `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`.)



## <a name="technical-details"></a>Detalhes técnicos

Seguem-se os detalhes do accionadores e ações que este HTTP + Swagger suporta de conexão.

## <a name="http--swagger-triggers"></a>HTTP + Swagger accionadores

Um accionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido numa aplicação de lógica. [Saiba mais sobre accionadores.](connectors-overview.md) A HTTP + Swagger conexão tem um accionador.

|Accionador|Descrição|
|---|---|
|HTTP + Swagger|Efetuar uma chamada de HTTP e devolver o conteúdo de resposta|

## <a name="http--swagger-actions"></a>HTTP + Swagger ações

Uma ação é uma operação que é realizada pelo fluxo de trabalho que está definido numa aplicação de lógica. [Saiba mais sobre ações.](connectors-overview.md) A HTTP + Swagger conexão tem uma ação possível.

|Ação|Descrição|
|---|---|
|HTTP + Swagger|Efetuar uma chamada de HTTP e devolver o conteúdo de resposta|

### <a name="action-details"></a>Detalhes de ação

A HTTP + Swagger conexão vem com uma ação possível. Segue-se obter informações sobre cada uma das ações, os respetivos campos de entrada necessários e opcionais e os detalhes de saída correspondentes que estão associados a sua utilização.

#### <a name="http--swagger"></a>HTTP + Swagger

Tornar um pedido de HTTP saída com a assistência de metadados Swagger.
Um asterisco (*) significa que um campo obrigatório.

|Nome a apresentar|Nome da propriedade|Descrição|
|---|---|---|
|Método de *|método|Verbo HTTP para utilizar.|
|URI *|URI|URI para o pedido de HTTP.|
|Cabeçalhos|cabeçalhos|Um objeto JSON de cabeçalhos de HTTP para incluir.|
|Corpo|corpo|O corpo do pedido HTTP.|
|Autenticação|autenticação|Autenticação a utilizar para pedido. [Para obter mais detalhes, consulte o artigo HTTP](./connectors-native-http.md#authentication).|

**Detalhes de saída**

Resposta de HTTP

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Cabeçalhos|objecto|Cabeçalhos de resposta|
|Corpo|objecto|Objeto de resposta|
|Código de estado|Int|Código de estado HTTP|

### <a name="http-responses"></a>Respostas HTTP

Quando efetuar chamadas para várias ações, poderá obter determinadas respostas. Segue-se uma tabela que destaca as descrições e as respostas correspondentes.

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido inválido|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido.|

---

## <a name="next-steps"></a>Próximos passos

Experimente a plataforma e, em seguida, [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) agora. Pode explorar as outras conexões disponíveis nas aplicações de lógica verificando nossa [lista das APIs](apis-list.md).
