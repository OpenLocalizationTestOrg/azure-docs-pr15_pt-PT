<properties 
    pageTitle="Como autorizar programador contas utilizando o Azure Active Directory na gestão de API do Azure" 
    description="Saiba como autorizar utilizadores utilizando o Azure Active Directory na gestão de API." 
    services="api-management" 
    documentationCenter="API Management" 
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

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Como autorizar programador contas utilizando o Azure Active Directory na gestão de API do Azure


## <a name="overview"></a>Descrição geral
Este guia mostra-lhe como ativar o acesso ao portal do programador para todos os utilizadores de uma ou mais Azure Active nos diretórios. Este guia também mostra-lhe como gerir grupos de utilizadores do Azure Active Directory ao adicionar externos grupos que contêm os utilizadores de um Azure Active Directory.

>Para concluir os passos neste guia primeiro tem de ter um Azure Active Directory na qual pretende criar uma aplicação.

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Como autorizar programador contas utilizando o Azure Active Directory

Para começar a, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Isto leva-o para o portal do publisher de gestão de API.

![Portal do Publisher][api-management-management-console]

>Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

Clique em **segurança** a partir do menu **API gestão** à esquerda e clique em **Identidades externos**.

![Identidades externas][api-management-security-external-identities]

Clique em **Azure Active Directory**. Tome nota do **URL redirecionar** e mudar ao longo para o seu Azure Active Directory no Portal clássica do Azure.

![Identidades externas][api-management-security-aad-new]

Clique no botão **Adicionar** para criar uma nova aplicação do Azure Active Directory e selecione **Adicionar uma aplicação que está a desenvolver a minha organização**.

![Adicionar a nova aplicação do Azure Active Directory][api-management-new-aad-application-menu]

Introduza um nome para a aplicação, selecione a **aplicação Web e/ou Web API**e clique no botão seguinte.

![Nova aplicação do Azure Active Directory][api-management-new-aad-application-1]

Para o **URL de início de sessão**, introduza o URL de início de sessão do seu portal de programador. Neste exemplo, o **URL de início de sessão** é `https://aad03.portal.current.int-azure-api.net/signin`. 

Para o **URL de ID da aplicação**, introduza o domínio predefinido ou um domínio personalizado para o Azure Active Directory e acrescentar uma cadeia exclusiva à mesma. Neste exemplo, o domínio predefinido da **https://contoso5api.onmicrosoft.com** é utilizado com o sufixo de **/api** especificado.

![Novas propriedades de aplicação do Azure Active Directory][api-management-new-aad-application-2]

Clique no botão de verificação para guardar e criar a nova aplicação e, mude para o separador **Configurar** para configurar a nova aplicação.

![Azure Active Directory seja criada nova aplicação][api-management-new-aad-app-created]

Se vários Azure directórios ativo irão ser utilizados para esta aplicação, clique em **Sim** para a **aplicação é com várias inquilino**. A predefinição é **não**.

![Aplicação está com várias inquilino][api-management-aad-app-multi-tenant]

Copie o **URL redirecionar** a partir da secção do **Azure Active Directory** do separador **Externos identidades** no portal do publisher e cole-a na caixa de texto de **Resposta URL** . 

![URL de resposta][api-management-aad-reply-url]

Desloque-se para a parte inferior do separador configurar, selecione o menu pendente **As permissões da aplicação** e verificar os **dados de diretório de leitura**.

![Permissões da aplicação][api-management-aad-app-permissions]

Selecione pendente **Permissões de delegado** e, consulte **Ativar início de sessão e leia os perfis dos utilizadores**.

![Permissões de delegado][api-management-aad-delegated-permissions]

>Para mais informações sobre permissões de delegado e de aplicação, consulte o artigo [aceder a API do gráfico][].

Copie o **Código do cliente** para a área de transferência.

![Id de cliente][api-management-aad-app-client-id]

Mude novamente para o portal do publisher e cole o **Código do cliente** copiado a configuração da aplicação Azure Active Directory.

![Id de cliente][api-management-client-id]

Mude novamente para a configuração do Azure Active Directory e clique na **Selecione duração** pendente na secção **teclas** e especificar um intervalo. Neste exemplo é utilizada **1 ano** .

![Chave][api-management-aad-key-before-save]

Clique em **Guardar** para guardar a configuração e apresentar a chave. Copie a chave para a área de transferência.

>Anote esta chave. Depois de fechar a janela de configuração do Azure Active Directory, a tecla não pode ser apresentada novamente.

![Chave][api-management-aad-key-after-save]

Mude novamente para o portal do publisher e cole a chave na caixa de texto **Secreta de cliente** .

![Secreta de cliente][api-management-client-secret]

**Permitido inquilinos** Especifica os directórios que tenham acesso a APIs da instância do serviço de gestão de API. Especifique os domínios das instâncias do Azure Active Directory ao qual pretende conceder acesso. Separe os vários domínios com consistir, espaços ou vírgulas.

![Inquilinos permitidos][api-management-client-allowed-tenants]

Vários domínios podem ser especificados na secção **Inquilinos permitidos** . Antes de qualquer utilizador pode iniciar sessão a partir de um domínio diferente do domínio original onde a aplicação foi registada, um administrador global do domínio diferente tem de conceder permissão para a aplicação para aceder a dados de diretório. Para conceder permissão, um administrador global tem de iniciar sessão na aplicação e clique em **Aceitar**. No exemplo seguinte `miaoaad.onmicrosoft.com` foi adicionado ao administrador global e de **Inquilinos permitido** administrador a partir desse domínio é início de sessão pela primeira vez.

![Permissões][api-management-permissions-form]

>Se um administrador que não sejam global tenta iniciar sessão antes de que são concedidas permissões por um administrador global, falha a tentativa de início de sessão e é apresentado um ecrã de erro.

Assim que a configuração pretendida for especificada, clique em **Guardar**.

![Guardar][api-management-client-allowed-tenants-save]

Assim que as alterações são guardadas, os utilizadores na especificado Azure Active Directory podem iniciar sessão no portal do programador ao seguir os passos em [Iniciar sessão no portal do programador com uma conta do Azure Active Directory][].

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Como adicionar um grupo externa para o Azure Active Directory

Depois de activar o acesso aos utilizadores de um Azure Active Directory, pode adicionar os grupos do Azure Active Directory para gestão de API para gerir mais facilmente a associação dos programadores no grupo com os produtos pretendidas.

> Para configurar um grupo do Azure Active Directory externo, o Azure Active Directory tem primeiro de ser configurado no separador identidades seguindo o procedimento na secção anterior. 

Grupos do Azure Active Directory externos são adicionados a partir do separador **visibilidade** do produto para o qual pretende conceder acesso ao grupo. Clique em **produtos**e, em seguida, clique no nome do produto pretendido.

![Configurar o produto][api-management-configure-product]

Mudar para o separador **visibilidade** e clique em **Adicionar grupos a partir do Azure Active Directory**.

![Adicionar grupos][api-management-add-groups]

Selecione o **Azure Active Directory inquilino** a partir da lista pendente e, em seguida, escreva o nome do grupo pretendido nos **grupos** para ser adicionada a caixa de texto.

![Selecione o grupo][api-management-select-group]

Este nome de grupo pode encontrar na lista **grupos** para o Azure Active Directory, conforme mostrado no seguinte exemplo.

![Lista de grupos do Azure Active Directory][api-management-aad-groups-list]

Clique em **Adicionar** para validar o nome do grupo e adicione o grupo. Neste exemplo, os **Programadores de 5 Contoso** o grupo externo é adicionado. 

![Grupo adicionado][api-management-aad-group-added]

Clique em **Guardar** para guardar a nova seleção de grupo.

Assim que um grupo do Azure Azure Active Directory tiver sido configurado a partir de um produto, este fica disponível para ser verificada no separador **visibilidade** para os outros produtos na instância do serviço de gestão de API.

Para rever e configurar as propriedades para grupos externos assim o tiverem sido adicionados, clique no nome do grupo a partir do separador de **grupos** .

![Gerir grupos][api-management-groups]

A partir daqui pode editar o **nome** e a **Descrição** do grupo.

![Editar grupo][api-management-edit-group]

Os utilizadores do configurado Azure Active Directory podem inicie sessão no portal de programador e vista e subscrever quaisquer grupos para os quais têm visibilidade ao seguir as instruções na secção seguinte.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Como iniciar sessão no portal do programador com uma conta do Azure Active Directory

Para iniciar sessão no portal do programador com uma conta do Azure Active Directory configurada nas secções anteriores, abra uma nova janela do browser a utilizar o **URL de início de sessão** a partir de configuração da aplicação do Active Directory e clique em **Azure Active Directory**.

![Portal de programador][api-management-dev-portal-signin]

Introduza as credenciais de um dos utilizadores na sua Azure Active Directory e clique em **Iniciar sessão**.

![Início de sessão][api-management-aad-signin]

Poderá ser-lhe com um formulário de registo se é necessária qualquer informação adicional. Preencha o formulário de registo e clique em **Inscrever-se**.

![Registo][api-management-complete-registration]

O utilizador agora tem sessão iniciada no portal do programador para a instância do serviço de gestão de API.

![Registo concluído][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introdução ao Azure API gestão]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Aceder ao grafo do API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Iniciar sessão no portal do programador com uma conta do Azure Active Directory]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

