<properties
    pageTitle="Proteger os recursos de nuvem com Azure MFA e o AD FS"
    description="Esta é a página de autenticação Multifator Azure que descreve como começar com o Azure MFA e o AD FS na nuvem."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Proteger nuvem recursos com autenticação Multifator de Azure e o AD FS

Se a sua organização esteja federada com o Azure Active Directory, utilize a autenticação Multifator de Azure ou os serviços de Federação do Active Directory para proteger os recursos que são acedidos pela Azure AD. Utilize os seguintes procedimentos para proteger os recursos do Azure Active Directory com autenticação Multifator de Azure ou os serviços de Federação do Active Directory.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger os recursos do Azure AD utilizar os AD FS

Para proteger o seu recurso de nuvem, primeiro ativar uma conta para os utilizadores, em seguida, definir uma regra em afirmações. Siga este procedimento para percorrer os passos:

1. Utilize os passos descritos no [autenticação multifator de para ligar para os utilizadores](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) para ativar uma conta.
2. Inicie a consola de gestão do AD FS.
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Navegue para **Confiar festa fidedignidades** e com o botão direito no confiar festa de fidedignidade. Selecione **editar regras de afirmação...**
4. Clique em **Adicionar regra...**
5. A partir do menu pendente, selecione **Enviar em afirmações através de uma regra personalizada** e clique em **seguinte**.
6. Introduza um nome para a regra afirmação.
7. Em regra personalizada: adicionar o seguinte texto:

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Afirmação correspondente:

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```

8. Clique em **OK** , em seguida, **Terminar**. Feche a consola de gestão do AD FS.

Os utilizadores, em seguida, podem concluir iniciar sessão utilizando o método no local (como smart card).

## <a name="trusted-ips-for-federated-users"></a>IPs de confiança para os utilizadores federados
IPs de confiança permitem aos administradores verificação de dois passos by-pass para endereços IP específicos, ou para os utilizadores federados que tenham pedidos provenientes dentro os seus próprios intranet. As secções seguintes descrevem como configurar IPs fidedignos autenticação Multifator Azure com utilizadores federados e de verificação de dois passos by-pass quando um pedido de provém dentro de uma intranet aos utilizadores federados. Pode fazê-lo a configurar o AD FS para utilizar uma pass-through ou filtrar um modelo de afirmação recebidas com o tipo de afirmação dentro da rede da empresa.

Este exemplo utiliza o Office 365 para o nosso festa fidedignidades confiar.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurar as regras de afirmações ADFS

É a primeira coisa que precisamos de fazer configurar os pedidos de AD FS. Vamos irá criar regras de duas afirmações, uma para o tipo de afirmação dentro da rede da empresa e uma adicional para manter os nossos utilizadores tiver sessão iniciados.

1. Abra a gestão do AD FS.
2. À esquerda, selecione **Colocar fidedignidades de terceiros**.
3. Com o botão direito no **Plataforma de identidade do Microsoft Office 365** e selecione **Editar afirmação regras de...** 
 ![Na nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. No emissão transformar regras, clique em **Adicionar regra.** 
 ![Na nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. No transformar afirmação regra Assistente para adicionar, selecione **passar através de ou filtrar uma afirmação entrada** a partir do menu pendente e clique em **seguinte**.
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. Na caixa junto ao nome da regra afirmação, dê a regra um nome. Por exemplo: InsideCorpNet.
7. A partir do menu pendente junto a receção reclamar tipo, selecione **Dentro da rede da empresa**.
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Clique em **Concluir**.
9. No emissão transformar regras, clique em **Adicionar regra**.
10. Transformar afirmação regra Assistente para adicionar, selecione **Enviar afirmações utilizando uma regra personalizada** a partir do menu pendente e clique em **seguinte**.
11. Na caixa em nome da regra afirmação: introduza *Manter utilizadores tiver sessão iniciada na*.
12. Na caixa da regra personalizada, introduza:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
![Nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Clique em **Concluir**.
14. Clique em **Aplicar**.
15. Clique em **Ok**.
16. Feche a gestão do AD FS.



### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurar o Azure Multifator IPs com utilizadores federados de confiança de autenticação
Agora que as afirmações estão no local, podemos pode configurar IPs de confiança.

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2. No lado esquerdo, clique em **Do Active Directory**.
3. Em diretório, selecione a pasta onde pretende configurar IPs de confiança.
4. No directório de que selecionou, clique em **Configurar**.
5. Na secção autenticação multifator, clique em **Gerir definições de serviço**.
6. Na página Definições de serviço, em IPs de confiança, selecione **Ignorar multi a autenticação multifator para pedidos de utilizadores federados na minha intranet.** 
 ![Na nuvem](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Clique em **Guardar**.
8. Depois de terem sido aplicadas as atualizações, clique em **Fechar**.


Já está! Neste momento, os utilizadores federados do Office 365 apenas devem ter que utilizar MFA quando uma afirmação provém fora da intranet da empresa.
