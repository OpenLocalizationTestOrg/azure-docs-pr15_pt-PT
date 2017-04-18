<properties
    pageTitle="Como gerir os certificados de Federação no Azure AD | Microsoft Azure"
    description="Saiba como personalizar a data de expiração para os seus certificados de Federação e como renovar certificados que irão expirar."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestão de certificados para federados Single Sign-On no Azure Active Directory

Este artigo aborda a questões recorrentes relacionadas com os certificados que o Azure Active Directory cria para estabelecer federado sessão único (SSO) para as suas aplicações SaaS.

Este artigo é apenas relevante para as aplicações que estão configuradas para utilizar o **Azure AD Single Sign-On**, conforme mostrado no exemplo abaixo:

![Azure AD Single Sign-On](./media/active-directory-sso-certs/fed-sso.PNG)

##<a name="how-to-customize-the-expiration-date-for-your-federation-certificate"></a>Como personalizar a data de expiração para o certificado de Federação

Por predefinição, os certificados estão definidos para expirar depois de dois anos. Pode escolher uma data de expiração diferente para o seu certificado ao seguir os passos abaixo. As capturas de ecrã incluídas utilizam equipa de vendas por exemplo, mas estes passos que podem aplicar a qualquer aplicação do SaaS federada.

1. No Azure Active Directory, na página de início rápido para a sua aplicação, clique no **Configurar serviço single sign-on**.

    ![Abra o Assistente de configuração de SSO.](./media/active-directory-sso-certs/config-sso.png)

2. Selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

3. Escreva o **URL de início de sessão** da aplicação e, selecione a caixa de verificação para **configurar o certificado utilizado para federados single sign-on**. Em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. Na página seguinte, selecione **gerar um certificado novo**e, selecione quanto tempo que pretende utilizar o certificado que seja válido para. Em seguida, clique em **seguinte**.

    ![Gerar um certificado novo](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Em seguida, clique em **Transferir o certificado**. Para saber como carregar o certificado para a sua aplicação SaaS específica, clique em **Ver instruções de configuração**.

    ![Transferir, em seguida, carregar o certificado](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. O certificado de não estar ativado até selecionar a caixa de verificação confirmação na parte inferior da caixa de diálogo e, em seguida, prima Submeter.

##<a name="how-to-renew-a-certificate-that-will-soon-expire"></a>Como renovar um certificado que irá expirar

Os passos de renovação mostrados abaixo Idealmente devem resultar em sem tempo de inatividade significativo para os seus utilizadores. Capturas de ecrã utilizadas nesta funcionalidade secção Salesforce como um exemplo, mas estes passos podem aplicar a qualquer aplicação do SaaS federada.

1. No Azure Active Directory, na página de início rápido para a sua aplicação, clique no **Configurar Single Sign-On**.

    ![Abrir o Assistente de configuração de SSO](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. Na primeira página da caixa de diálogo, **Azure AD Single Sign-On** já deve estar selecionada, pelo que, clique **seguinte**.

3. Na segunda página, selecione a caixa de verificação para **configurar o certificado utilizado para federados single sign-on**. Em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. Na página seguinte, selecione **gerar um certificado novo**e, selecione quanto tempo que pretende utilizar o novo certificado seja válido para. Em seguida, clique em **seguinte**.

    ![Gerar um certificado novo](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Clique em **Transferir o certificado**. Para rewnew com êxito o certificado, tem de executar os dois passos seguintes:

    - Carregar o novo certificado para o ecrã de configuração de início de sessão único a aplicação SaaS. Para saber como fazê-lo para a sua aplicação SaaS específica, clique em **Ver instruções de configuração**.

    - No Azure AD, selecione a caixa de verificação de confirmação na parte inferior da caixa de diálogo para ativar o novo certificado e, em seguida, clique em **seguinte** para submeter.

    > [AZURE.IMPORTANT] Serviço Single sign-on para a aplicação será desativada em momento em que quer um estes dois passos estiver concluído, mas irão estar ativada novamente uma vez que o segundo passo é concluído. Por conseguinte, para minimizar o tempo de inatividade, consulte preparar-se para realizar os dois passos num curto espaço de tempo uns dos outros.

    ![Transferir, em seguida, carregar o certificado](./media/active-directory-sso-certs/renew-config-app.PNG)

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Resolução de problemas baseadas em SAML Single Sign-On](active-directory-saml-debugging.md)
