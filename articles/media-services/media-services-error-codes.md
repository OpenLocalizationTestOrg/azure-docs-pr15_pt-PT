<properties
    pageTitle="Códigos de erro do Azure dos serviços de multimédia | Microsoft Azure"
    description="O tópico fornece uma descrição geral dos serviços de multimédia do Azure de códigos de erro."
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>

# <a name="azure-media-services-error-codes"></a>Códigos de erro do Azure dos serviços de multimédia

Quando utilizar os serviços de multimédia do Microsoft Azure, poderá receber códigos de erro HTTP de serviço dependendo problemas como tokens de autenticação de expiração para ações que não são suportadas nos serviços de multimédia. Segue-se uma lista de **códigos de erro HTTP** que podem ser devolvidos dos serviços de multimédia e as causas possíveis para os mesmos.  
  
## <a name="400-bad-request"></a>400 pedido inválido

O pedido contém informações inválidas e for rejeitado devido a um dos seguintes motivos:

- Uma versão não suportada do API é especificada. Para a versão mais recente, consulte [o programa de configuração para o desenvolvimento de API de resto de serviços de multimédia](media-services-rest-how-to-use.md).
- A versão da API dos serviços de multimédia não é especificada. Para obter informações sobre como especificar a versão da API, consulte o artigo [ligar aos serviços de multimédia com a API de resto de serviços de multimédia](media-services-rest-connect-programmatically.md). 
   
    >[AZURE.NOTE] Se estiver a utilizar o .NET ou Java SDK para ligar dos serviços de multimédia, a versão da API é especificada por si, sempre que tente e executa alguns ação contra dos serviços de multimédia.
- Foi especificada numa propriedade indefinida. É o nome da propriedade na mensagem de erro. Apenas as propriedades que são membros de uma determinada entidade podem ser especificadas. Consulte o artigo [Referência da API do resto do Azure multimédia serviços](http://msdn.microsoft.com/library/azure/hh973617.aspx) para uma lista de entidades e respetivas propriedades.
- Um valor da propriedade inválido foi especificado. É o nome da propriedade na mensagem de erro. Consulte a hiperligação anterior para tipos de propriedade válida e os respetivos valores.
- Um valor de propriedade está em falta e é necessário.
- Parte do URL especificado contém um valor errado.
- Foi uma tentativa de actualizar uma propriedade WriteOnce.
- Uma tentativa é efetuada para criar uma tarefa que tem um activo teclado com um AssetFile principal que não foi especificada ou não foi possível determinar.
- Foi uma tentativa de atualizar um localizador de SA. Locator SA apenas pode ser criado ou eliminado. Transmissão Locator pode ser atualizado. Para mais informações, consulte o artigo [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).
- Uma operação não é suportada ou consulta foi submetida. 

## <a name="401-unauthorized"></a>401 não autorizado

O pedido não foi possível autenticar (antes de podem ser autorizado) devido a um dos seguintes motivos:

- Cabeçalho de autenticação em falta.
- Valor do cabeçalho de autenticação incorretas.
    - O token expirou. Se utilizar a API REST diretamente, consulte o artigo [para ligar dos serviços de multimédia com os serviços de multimédia REST API](media-services-rest-connect_programmatically.md) para saber como gerar um novo token de autenticação. Se estiver a utilizar o .NET ou Java SDK, crie um objeto CloudMediaContext ou MediaContract para gerar o token. Para mais informações sobre como fazer isto, consulte o artigo [ligar aos serviços de multimédia com o SDK de serviços de multimédia para .NET](media-services-dotnet-connect-programmatically.md).
    - O token contém uma assinatura inválida.</li></ul></li></ul>

## <a name="403-forbidden"></a>403 Proibido

O pedido não é permitido devido a um dos seguintes motivos:

- A conta dos serviços de multimédia não é possível localizar ou foi eliminada.
- A conta dos serviços de multimédia é desativada e o tipo de pedido não é HTTP GET. Operações de serviço irão devolver uma resposta de 403.
- O token de autenticação não contém informações de credenciais do utilizador: AccountName e/ou SubscriptionId. Pode encontrar estas informações na extensão da IU dos serviços de multimédia para a sua conta dos serviços de multimédia no Portal de gestão do Azure.
- Não é possível aceder ao recurso.
    - Foi feita uma tentativa para utilizar uma MediaProcessor que não está disponível para a sua conta dos serviços de multimédia.
    - Foi uma tentativa de atualizar um JobTemplate definida dos serviços de multimédia.
    - É efetuada uma tentativa para substituir o localizador de algumas outras dos serviços de multimédia da conta.
    - Foi uma tentativa de substituir ContentKey algumas outras dos serviços de multimédia da conta.

- Não foi possível criar o recurso devido a uma quota de serviço que foi se encontram disponíveis para a conta dos serviços de multimédia. Para mais informações sobre as quotas de serviço, consulte o artigo [Quotas e limitações](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 não encontrado

O pedido não é permitido num recurso devido a um dos seguintes motivos:

- Foi uma tentativa de atualizar uma entidade que não existe.
- É efetuada uma tentativa para eliminar uma entidade que não existe.
- Uma tentativa foi efetuada para criar uma entidade que liga a uma entidade que não existe.
- Foi uma tentativa de obter uma entidade que não existe.
- É efetuada uma tentativa para especificar uma conta de armazenamento não está associada a conta dos serviços de multimédia.  

## <a name="409-conflict"></a>409 conflito

O pedido não é permitido devido a um dos seguintes motivos:

- AssetFile mais do que um tem o nome do elemento especificado.
- Uma tentativa foi efetuada para criar uma segunda AssetFile primária dentro de elemento.
- Uma tentativa foi efetuada para criar um ContentKey com o Id especificado já a ser utilizado.
- Uma tentativa foi efetuada para criar um Locator com o Id especificado já a ser utilizado.
- IngestManifestFile mais do que um tem o nome especificado dentro de IngestManifest.
- Foi uma tentativa de ligação uma segunda encriptação de armazenamento ContentKey para elemento encriptados de armazenamento.
- Foi uma tentativa de ligação ContentKey o mesmo para elemento.
- Uma tentativa foi efetuada para criar um localizador de um ativo cujo contentor de armazenamento está em falta ou já não está associada o elementos.
- Uma tentativa foi efetuada para criar um localizador de um ativo que já tenha 5 Locator em utilização. (Armazenamento azure impõe o limite de cinco políticas de acesso partilhado no contentor de armazenamento de uma).
- Ligar a conta de armazenamento de um ativo para um IngestManifestAsset não é igual a conta de armazenamento utilizada pelo IngestManifest principal.  

## <a name="500-internal-server-error"></a>Erro de servidor interno 500

Durante o processamento do pedido, dos serviços de multimédia encontrar algum erro que impede o processamento de continuar. Isto pode ser devido a um dos seguintes motivos:

- Criar um recurso ou tarefa falha porque informações de quota de serviço a conta dos serviços de multimédia estão temporariamente indisponíveis.
- Criar um contentor de armazenamento de BLOBs activo ou IngestManifest falha porque informações de conta de armazenamento a conta estão temporariamente indisponíveis.
- Outro erro inesperado. 

## <a name="503-service-unavailable"></a>503 Serviço indisponível

O servidor está atualmente não é possível receber pedidos. Este erro pode ser causado por excessivos pedidos de serviço. Serviços de multimédia limitação mecanismo restringe a utilização de recursos para as aplicações que efectuar pedido excessivo ao serviço.

>[AZURE.NOTE]Verificar a mensagem de erro e a cadeia de código de erro para obter informações mais detalhadas sobre o motivo obteve o erro 503. Este erro não significa sempre limitação.

Descrições de estado possíveis são:

- "O servidor está ocupado. Será executado anterior deste tipo de pedido de demorou mais segundos {0}."
- "O servidor está ocupado. Mais do que {0} pedidos por segundo podem estar limitada."
- "O servidor está ocupado. Mais do que podem ser limitados {0} pedidos em segundos {1}."

Para processar este erro, recomendamos que utilize exponencial back-off repetir lógica. Isto significa que utilizar aguarda gradualmente mais longa entre tentativas para respostas de erro consecutivas.  Para mais informações, consulte o artigo [Bloco de aplicações processamento breves falhas](https://msdn.microsoft.com/library/hh680905.aspx). 

>[AZURE.NOTE]Se estiver a utilizar o [Azure SDK de serviços de multimédia para .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), a lógica de repetição para o erro 503 foi implementada pelo SDK.  
  
## <a name="see-also"></a>Consulte também  

[Códigos de erro de gestão de serviços de multimédia](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
