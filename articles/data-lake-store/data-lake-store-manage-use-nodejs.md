<properties 
   pageTitle="Introdução ao Azure dados Lake lojas com o Azure SDK para Node.js | Microsoft Azure"
   description="Saiba como utilizar Node.js para trabalhar com contas do arquivo de Lake de dados e o sistema de ficheiros." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Introdução ao arquivo de Lake Azure dados com o Azure SDK para Node.js

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Clip Azure](data-lake-store-get-started-cli.md)
- [NODE.js](data-lake-store-manage-use-nodejs.md)


Saiba como utilizar o SDK do Azure para Node.js para criar uma conta do arquivo de Lake de dados do Azure e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a sua conta, etc. Para mais informações sobre o arquivo de dados de Lake, consulte o artigo [Descrição geral dos dados Lake loja](data-lake-store-overview.md). Atualmente, o SDK suporta

  *  **Versão node.js: 0.10.0 ou superior**
  *  **Versão REST API para a conta: 2015-10-01-pré-visualização**
  *  **Versão de REST API para o sistema de ficheiros: 2015-10-01-pré-visualização**

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Criar uma aplicação do Azure Active Directory**. Utilize a aplicação do Azure AD para autenticar a aplicação de arquivo de Lake de dados com o Azure AD. Existem abordagens diferentes para autenticar com Azure AD, que são **autenticação de utilizador final** ou do **serviço de serviço**. Para obter instruções e mais informações sobre como autenticar, consulte o artigo [autenticar com o arquivo de dados de Lake utilizando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Como instalar

```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Autenticar utilizando o Azure Active Directory

Os fragmentos abaixo mostram duas formas em separado de autenticar com o arquivo de dados de Lake utilizando Azure AD. Para informações detalhadas sobre no vários métodos para utilizar para autenticação com dados Lake arquivo, consulte o artigo [autenticar com o arquivo de dados de Lake utilizando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

O fragmento de abaixo também requer entradas como o nome de domínio do Azure AD, ID do cliente para uma aplicação do Azure AD, etc. Podem ser obtidos todos os seguintes detalhes de uma Azure AD aplicação que tem de criados, os detalhes dos quais também são incluídos na hiperligação acima.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Criar os clientes de arquivo de Lake de dados

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Criar uma conta do arquivo de Lake de dados

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="create-a-file-with-content"></a>Criar um ficheiro com o conteúdo
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Obter uma lista de ficheiros e pastas

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Consulte também

- [SDK do Microsoft Azure para Node.js](https://github.com/azure/azure-sdk-for-node)
- [SDK do Microsoft Azure para Node.js - gestão de Lake análise de dados](https://www.npmjs.com/package/azure-arm-datalake-analytics)
