<properties
    pageTitle="Azure Active Directory B2C: Descrição geral | Microsoft Azure"
    description="Desenvolver aplicações do consumidor destinado com o Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure B2C diretório ativos: Inscrever-se e consumidores nas suas aplicações de início de sessão

Azure Active B2C de diretório é uma solução de gestão de identidades abrangente nuvem para o seu web ao público consumidor e aplicações móveis. É um serviço global altamente disponível que se adapta a centenas de milhões de identidades do consumidor. Criada com base numa plataforma segura empresarial, o Azure Active Directory B2C mantém as aplicações, a sua empresa e os utilizadores poderão protegido.

No passado, os programadores de aplicações que pretendiam para inscrever-se e inicie sessão no consumidores nas suas aplicações seriam criaram os seus próprios código. E se quiser tiver utilizado ou sistemas de bases de dados no local para armazenar os nomes de utilizador e palavras-passe. Azure Active B2C de diretório oferece os programadores uma melhor forma para integrar as aplicações com a ajuda de uma plataforma segura baseados em normas e um conjunto avançado de extensible políticas de gestão de identidades do consumidor. Quando utiliza o Azure Active Directory B2C, os utilizadores poderão pode inscrever-se para as suas aplicações utilizando as respetivas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou através da criação de novas credenciais (endereço de e-mail e palavra-passe, ou nome de utilizador e palavra-passe); chamamos as último "contas locais."

## <a name="get-started"></a>Introdução

Para criar uma aplicação que aceita consumidor inscrever-se e iniciar sessão, irá primeiro tem de registar a aplicação com um B2C do Azure Active Directory inquilino. Obter o seu próprio inquilino através dos passos descritos no [artigo criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md).

Pode escrever a aplicação contra o serviço do Azure Active Directory B2C ao selecionar enviar mensagens de protocolo diretamente, utilizando o [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) ou [Abrir ID ligar](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), ou utilizando o nossas bibliotecas para fazer o trabalho por si. Selecione a sua plataforma favorita na seguinte tabela e comece a trabalhar.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Quais são as novidades

Verificar novamente aqui com frequência para saber mais sobre futuras alterações para B2C de diretório ativa o Azure. Recomendamos também irá tweet sobre as atualizações ao utilizar @AzureAD.

- Saiba mais sobre o nosso [quadro de política extensible](active-directory-b2c-reference-policies.md) e os tipos de políticas que pode criar e utilizar nas suas aplicações.
- Marque o nosso [blogue de serviços](https://blogs.msdn.microsoft.com/azureadb2c/) para as notificações no problemas com o serviço secundárias, atualizações, estado e atenuações. Continue para monitorizar o [dashboard do Estado Azure](https://azure.microsoft.com/status/) também.
- Atual [limitações do serviço, restrições e restrições](active-directory-b2c-limitations.md).
- Por fim, um [exemplo de código](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) através do Azure AD B2C ASP.NET Core.

## <a name="how-to-articles"></a>Artigos de procedimentos

Saiba como utilizar funcionalidades específicas do Azure Active Directory B2C:

- Configure contas de [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [conta Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md)e [LinkedIn](active-directory-b2c-setup-li-app.md) para utilizar nas suas aplicações do consumidor destinado.
- [Utilizar atributos para recolher informações sobre os utilizadores poderão](active-directory-b2c-reference-custom-attr.md).
- [Ativar o autenticação Multifator do Azure nas suas aplicações do consumidor destinado](active-directory-b2c-reference-mfa.md).
- [Configurar o personalizada de palavra-repor para os seus consumidores](active-directory-b2c-reference-sspr.md).
- [Personalizar a aspeto e funcionalidade do Inscreva-se para iniciar sessão e outras páginas do consumidor destinado](active-directory-b2c-reference-ui-customization.md) que devem ser servidas pelos Azure Active Directory B2C.
- [Utilizar a Azure Active Directory Graph API através de programação criar, ler, atualizar e eliminar os consumidores](active-directory-b2c-devquickstarts-graph-dotnet.md) no inquilino do Azure Active Directory B2C.

## <a name="next-steps"></a>Próximos passos

Estas ligações será útil para explorar o serviço no profundidade:

- Consulte as [informações sobre preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Obter ajuda na pilha de conteúdo adicional utilizando o [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal) etiquetas.
- Dê-nos seus pensamentos utilizando [Voz do utilizador](https://feedback.azure.com/forums/169401-azure-active-directory/)– Queremos ouvi-los! Utilizar a expressão "AzureADB2C:" no título da sua mensagem para os podermos localizar.
- Reveja a [referência de protocolo do Azure AD B2C](active-directory-b2c-reference-protocols.md).
- Reveja a [referência de Token do Azure AD B2C](active-directory-b2c-reference-tokens.md).
- Leia as [Perguntas mais frequentes do Azure Active Directory B2C](active-directory-b2c-faqs.md).
- [Ficheiro de pedidos de Azure Active Directory B2C de suporte](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para os nossos produtos

Aconselhamos para obter notificações de quando incidentes de segurança ocorrem visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrever alertas de aviso de segurança.
