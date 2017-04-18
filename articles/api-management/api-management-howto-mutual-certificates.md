<properties 
    pageTitle="Como proteger os serviços de back-end utilizando cliente de autenticação de certificados na gestão de API do Azure" 
    description="Saiba como seguro serviços de back-end utilizando a autenticação de certificado de cliente na gestão de API do Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Como proteger os serviços de back-end utilizando cliente de autenticação de certificados na gestão de API do Azure

Gestão de API fornece a capacidade para proteger o acesso ao serviço de uma API back-end utilizando os certificados de cliente. Este guia mostra como gerir certificados no portal do publisher de API e como configurar uma API para utilizar um certificado para aceder ao seu serviço de back-end.

Para obter informações sobre a gestão de certificados utilizando a gestão de API REST API, consulte o artigo [Azure API gestão REST API certificado entidade][].

## <a name="prerequisites"> </a>Pré-requisitos

Este guia mostra-lhe como configurar o seu instância do serviço de gestão de API para utilizar a autenticação de certificado de cliente para aceder ao serviço de back-end para uma API. Antes de seguir os passos neste tópico, deve ter o seu serviço de back-end configurado para a autenticação de certificado de cliente ([para configurar autenticação de certificados no Web sites do Azure consulte este artigo][]) e, têm acesso ao certificado e a palavra-passe para o certificado para carregar no portal do publisher de gestão de API.

## <a name="step1"> </a>Carregar um certificado de cliente

Para começar a, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Isto leva-o para o portal do publisher de gestão de API.

![Portal de API Publisher][api-management-management-console]

>Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

Clique em **segurança** no menu de **Gestão de API** à esquerda e clique em **certificados de cliente**.

![Certificados de cliente][api-management-security-client-certificates]

Para carregar um certificado novo, clique em **Carregar certificado**.

![Carregar certificado][api-management-upload-certificate]

Navegue até ao seu certificado e, em seguida, introduza a palavra-passe para o certificado.

>O certificado tem de estar no formato **. pfx** . Certificados autoassinados são permitidos.

![Carregar certificado][api-management-upload-certificate-form]

Clique em **carregar** para carregar o certificado.

>A palavra-passe de certificado é validada neste momento. Se estiver incorreta é apresentada uma mensagem de erro.

![Certificado carregado][api-management-certificate-uploaded]

Assim que o certificado é carregado, é apresentado no separador **certificados de cliente** . Se tiver vários certificados, certifique-se de uma nota do assunto ou os últimos quatro carateres da impressão digital, que são utilizados para selecionar o certificado ao configurar uma API para utilizar certificados, como descrito na secção [Configurar uma API para utilizar um certificado de cliente para autenticação de gateway][] que se segue.

>Para desativar a validação de cadeia do certificado ao utilizar, por exemplo, um certificado autoassinado, siga os passos descritos neste FAQ [item](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Eliminar um certificado de cliente

Para eliminar um certificado, clique em **Eliminar** ao lado do certificado pretendido.

![Eliminar certificado][api-management-certificate-delete]

Clique em **Sim, eliminá-la** para confirmar.

![Confirmar a eliminação][api-management-confirm-delete]

Se for o certificado utilizado por uma API, em seguida, é apresentado um ecrã de aviso. Para eliminar o certificado tem de remover o certificado a partir de qualquer APIs que estão configurados para utilizá-la.

![Confirmar a eliminação][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurar uma API para utilizar um certificado de cliente para autenticação de gateway

Clique em **APIs** a partir do menu de **Gestão de API** à esquerda, clique no nome da API do pretendido e clique no separador **segurança** .

![Segurança API][api-management-api-security]

Selecione **os certificados de cliente** da lista pendente **com credenciais** .

![Certificados de cliente][api-management-mutual-certificates]

Selecione o certificado pretendido a partir da lista pendente de **certificado de cliente** . Se existirem vários certificados pode observe o assunto ou os últimos quatro carateres da impressão digital conforme indicado na secção anterior para determinar o certificado correcto.

![Selecione o certificado][api-management-select-certificate]

Clique em **Guardar** para guardar a alteração de configuração à API.

>Esta alteração é aplicada imediatamente e chamadas para operações desse API irão utilizar o certificado para autenticar-se num servidor de back-end.

![Guardar alterações da API][api-management-save-api]

>Quando é especificado um certificado para a autenticação de gateway para o serviço de segurança de uma API, torna-se parte da política para esse API e podem ser visualizada no editor de política.

![Política de certificado][api-management-certificate-policy]

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre outras formas de proteger o seu serviço de back-end, tal como HTTP autenticação basic ou partilhada secreta, consulte o vídeo seguinte.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introdução ao Azure API gestão]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance

[Azure entidade de API gestão REST API certificado]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Para configurar autenticação de certificados no Web sites do Azure consulte este artigo]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configurar uma API para utilizar um certificado de cliente para autenticação de gateway]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 
