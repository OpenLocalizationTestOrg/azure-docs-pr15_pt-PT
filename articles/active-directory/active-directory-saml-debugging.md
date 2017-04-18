<properties 
    pageTitle="Como depurar baseadas em SAML single sign-on para aplicações no Azure Active Directory | Microsoft Azure" 
    description="Saiba como depurar baseadas em SAML single sign-on para aplicações no Azure Active Directory " 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Como depurar baseadas em SAML single sign-on para aplicações no Azure Active Directory

Quando depurar uma integração de aplicações baseadas em SAML, muitas vezes é útil utilizar uma ferramenta como [Fiddler](http://www.telerik.com/fiddler) para ver o pedido SAML, a resposta SAML e o token SAML real que seja emitido para a aplicação. Ao examinar o token SAML, pode garantir que todos os atributos necessários, o nome de utilizador no assunto SAML e o emissor URI estão a chegar através de conforme esperado.

![][1]

A resposta a partir do Azure AD que contém o token SAML é normalmente que ocorre depois de um HTTP 302 redirecionar a partir do https://login.windows.net e é enviada para o configurado **URL de resposta** da aplicação. 
 
Pode ver o token SAML selecionando esta linha e, em seguida, selecionando o **Inspetores > Web Forms** separador no painel direito. A partir daí, o valor de **SAMLResponse** com o botão direito e selecione **Enviar para TextWizard**. Em seguida, selecione **Base64 a partir** do menu **transformar** permitirá codificar o token e ver o seu conteúdo.
 
**Nota**: para ver os conteúdos deste pedido HTTP, Fiddler poderá pedir-lhe configurar desencriptar de tráfego HTTPS, que terá de fazer.

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Configurar o serviço single sign-on para as aplicações que não estão na Galeria de aplicação do Azure Active Directory](active-directory-saas-custom-apps.md)
- [Como personalizar em afirmações emitidas no Token de SAML para aplicações integradas previamente](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png
