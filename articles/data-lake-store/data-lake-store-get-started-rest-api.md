<properties 
   pageTitle="Introdução ao arquivo de dados de Lake utilizando REST API | Microsoft Azure" 
   description="Utilizar WebHDFS REST APIs para efetuar operações no arquivo de dados de Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Introdução ao Azure dados Lake loja utilizando REST APIs

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Clip Azure](data-lake-store-get-started-cli.md)
- [NODE.js](data-lake-store-manage-use-nodejs.md)

Este artigo vai aprender a utilizar WebHDFS REST APIs e dados Lake loja REST APIs para executar a gestão de conta, bem como operações de sistema de ficheiros no Azure dados Lake arquivo. Azure dados Lake arquivo expõe as suas próprias APIs resto para operações de gestão de conta. No entanto, uma vez que o arquivo de dados de Lake é compatível com ecossistema HDFS e Hadoop, que suporta, utilizando WebHDFS REST APIs para operações de sistema de ficheiros.

>[AZURE.NOTE] Para informações detalhadas sobre a API REST suporte para o arquivo de dados de Lake, consulte o artigo [Referência de API do resto do Azure dados Lake arquivo](https://msdn.microsoft.com/library/mt693424.aspx).

## <a name="prerequisites"></a>Pré-requisitos

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Criar uma aplicação do Azure Active Directory**. Utilize a aplicação do Azure AD para autenticar a aplicação de arquivo de Lake de dados com o Azure AD. Existem abordagens diferentes para autenticar com Azure AD, que são **autenticação de utilizador final** ou do **serviço de serviço**. Para obter instruções e mais informações sobre como autenticar, consulte o artigo [autenticar com o arquivo de dados de Lake utilizando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

- [cURL](http://curl.haxx.se/). Este artigo utiliza Laço para demonstrar como efetuar chamadas de REST API contra uma conta do arquivo de Lake de dados.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como autenticar utilizando o Azure Active Directory?

Pode utilizar duas abordagens para autenticar utilizando o Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Autenticação de utilizador final (interativa)

Neste cenário, a aplicação pede ao utilizador para iniciar sessão e todas as operações são executadas no contexto do utilizador. Execute os passos seguintes para autenticação interativa.

1. Através da aplicação, redirecione o utilizador para o seguinte URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<REDIRECIONAR URI > tem de ser codificado para utilização num URL. Sim, para https://localhost, utilize `https%3A%2F%2Flocalhost`)

    Para este tutorial, pode substituir os valores de marcador de posição no URL acima e colá-lo na barra de endereço do browser. Serão redirecionados para autenticar utilizando o início de sessão Azure. Uma vez que iniciar sessão com êxito, é apresentada a resposta na barra de endereço do browser. A resposta será no seguinte formato:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capture o código de autorização da resposta. Para este tutorial, pode copiar o código de autorização a partir da barra de endereço do browser e transmitir-a no pedido de mensagem para o ponto final do token, conforme apresentado abaixo:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] Neste caso, a \<REDIRECIONAR URI > não precisa de ser codificado.

3. A resposta é um objeto de JSON que contém um token de acesso (por exemplo, `"access_token": "<ACCESS_TOKEN>"`) e um token de atualização (por exemplo, `"refresh_token": "<REFRESH_TOKEN>"`). A aplicação utiliza o token de acesso ao aceder ao arquivo de Lake de dados do Azure e o token de atualização para obter outro token de acesso, quando um token de acesso expira.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Quando o token de acesso expira, pode pedir um novo token de acesso utilizando o token de atualização, conforme apresentado abaixo:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Para mais informações sobre a autenticação de utilizador interactivo, ver o [código de autorização conceder fluxo](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Serviço de serviço de autenticação (não interativa)

Neste cenário, a aplicação fornece as suas próprias credenciais para realizar as operações. Para que isto, tem de emitir um pedido de mensagem como aquele apresentado abaixo. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

O resultado deste pedido irá incluir um token de autorização (representada por `access-token` na saída abaixo) que serão posteriormente ser efetuada com as suas chamadas REST API. Guardar este token de autenticação num ficheiro de texto; terá este neste artigo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artigo utiliza o **não interativa** abordagem. Para mais informações sobre não interativa (chamadas de serviço-para-service), consulte [serviço para chamadas de serviço utilizando credenciais](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Criar uma conta do arquivo de Lake de dados

Esta operação é baseada no REST API chamada definida [aqui](https://msdn.microsoft.com/library/mt694078.aspx).

Utilize o seguinte comando de Laço. Substituir ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

O comando acima, substitua \< `REDACTED` \> com o token de autorização que recuperou anterior. A carga útil pedido para este comando encontra-se no ficheiro **input.json** que é fornecido para a `-d` parâmetro acima. O conteúdo do ficheiro input.json semelhante ao seguinte:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## <a name="create-folders-in-a-data-lake-store-account"></a>Criar pastas numa conta dados Lake arquivo

Esta operação baseia-se a API do resto WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilize o seguinte comando de Laço. Substituir ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

O comando acima, substitua \< `REDACTED` \> com o token de autorização que recuperou anterior. Este comando cria um diretório chamado **mytempdir** na pasta raiz da sua conta do arquivo de Lake de dados.

Deverá visualizar uma resposta da seguinte forma se a operação for concluída com êxito:

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Lista de pastas numa conta dados Lake arquivo

Esta operação baseia-se a API do resto WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilize o seguinte comando de Laço. Substituir ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

O comando acima, substitua \< `REDACTED` \> com o token de autorização que recuperou anterior.

Deverá visualizar uma resposta da seguinte forma se a operação for concluída com êxito:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Carregar dados para uma conta do arquivo de Lake de dados

Esta operação baseia-se a API do resto WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Carregar dados utilizando o WebHDFS REST API é um processo de dois passos, conforme explicado abaixo.

1. Submeta um pedido de HTTP colocar sem o enviar os dados do ficheiro ser carregado. O comando seguinte, substitua ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    A exportação para este comando ser conterá um URL de Redireccionamento temporário, como um apresentado abaixo.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. Agora tem de submeter outro pedido HTTP colocar contra o URL apresentado para a propriedade de **localização** na resposta. Substituir ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    O resultado será semelhante ao seguinte:

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Ler os dados de uma conta do arquivo de Lake de dados

Esta operação baseia-se a API do resto WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Ler dados a partir de um arquivo de dados de Lake conta é um processo de dois passos.

* Pela primeira vez, submeta um pedido de obter contra o ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Isto irá devolver uma localização para submeter o pedido GET seguinte.
* Em seguida, submeter o pedido GET contra o ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Isto vai mostrar os conteúdos do ficheiro.

No entanto, porque não existe nenhuma diferença nos parâmetros de entrada entre o primeiro e o segundo passo, pode utilizar o `-L` parâmetro para submeter o pedido primeiro. `-L`opção essencialmente combina dois pedidos numa só e irá tornar Laço Refazer o pedido na nova localização. Por fim, o resultado de todas as chamadas de pedido é apresentado, tal como mostrado abaixo. Substituir ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Deverá visualizar um resultado semelhante ao seguinte:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Mudar o nome de um ficheiro numa conta dados Lake arquivo

Esta operação baseia-se a API do resto WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilize o seguinte comando Laço para mudar o nome de um ficheiro. Substituir ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Deverá visualizar um resultado semelhante ao seguinte:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Eliminar um ficheiro de uma conta do arquivo de Lake de dados

Esta operação baseia-se a API do resto WebHDFS chamada definida [aqui](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilize o seguinte comando Laço para eliminar um ficheiro. Substituir ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Deverá visualizar um resultado semelhante ao seguinte:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Eliminar uma conta de arquivo de Lake de dados

Esta operação é baseada no REST API chamada definida [aqui](https://msdn.microsoft.com/library/mt694075.aspx).

Utilize o seguinte comando Laço para eliminar uma conta do arquivo de Lake de dados. Substituir ** \<yourstorename >** com o seu nome de arquivo de Lake de dados.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Deverá visualizar um resultado semelhante ao seguinte:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Consulte também

- [Abrir as aplicações de origem de dados grande compatíveis com o arquivo de Lake de dados do Azure](data-lake-store-compatible-oss-other-applications.md)
 
