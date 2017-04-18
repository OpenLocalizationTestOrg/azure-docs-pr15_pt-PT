<properties
    pageTitle="Tutorial: Integração do Azure Active Directory ao Facebook no trabalho | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e o Facebook no local de trabalho."
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
    ms.date="04/26/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-facebook-at-work"></a>Tutorial: Integração do Azure Active Directory ao Facebook no trabalho

Objectivo deste tutorial é mostrar-lhe como integrar o Facebook no local de trabalho com o Azure Active Directory (Azure AD).

Integrar o Facebook no local de trabalho com o Azure AD fornece os seguintes benefícios: 

- Pode controlar no Azure AD quem tem acesso ao Facebook no trabalho 
- Pode aprovisionar automaticamente a conta para utilizadores que foi concedido acesso ao Facebook no trabalho
- Pode permitir que os utilizadores para automaticamente obter assinado-on ao Facebook no trabalho (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central 

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com CS estrelas, tem os seguintes itens:

- Uma subscrição do Azure AD
- Facebook no local de trabalho com o single sign-on activado

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 


## <a name="adding-facebook-at-work-from-the-gallery"></a>Adicionar Facebook no trabalho a partir da Galeria
Para configurar a integração do Facebook no trabalho para o Azure AD, é necessário adicionar Facebook no trabalho a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o Facebook no trabalho a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Facebook no local de trabalho**.

7. No painel de resultados, selecione **Facebook no local de trabalho**e, em seguida, clique em **Concluir** para adicionar a aplicação.


### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

Esta secção descreve como permitir que os utilizadores autenticar ao Facebook no local de trabalho com a sua conta no Azure Active Directory, utilizando a Federação com base no protocolo SAML.

**Para configurar Azure AD serviço single sign-on com Facebook no trabalho, execute os seguintes passos:**

1.  Depois de adicionar Facebook no local de trabalho no portal do Azure clássico, clique em **Configurar Single Sign-On**.

2.  No ecrã **Configurar o URL da aplicação** , introduza o URL, onde os utilizadores irão inicie sessão no seu Facebook na aplicação de trabalho. Este é o Facebook no URL de inquilino do trabalho (exemplo: https://example.facebook.com/). Assim que tiver terminado, clique em **seguinte**.

3.  Numa janela do browser web diferente, inicie sessão na sua Facebook no site de empresa de trabalho como administrador.

4. Siga as instruções no seguinte URL para configurar o Facebook no trabalho para utilizar o Azure AD como um fornecedor de identidades: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  Uma vez concluída, para regressar às janelas do browser que mostra o Azure portal clássico, clique na caixa de verificação para confirmar a que tiver concluído o procedimento, em seguida, clique em **seguinte** e **concluída**.


## <a name="automatically-provisioning-users-to-facebook-at-work"></a>Aprovisionamento automaticamente os utilizadores ao Facebook no trabalho

Azure AD suporta a capacidade de synchonize automaticamente os detalhes de conta de utilizadores atribuídos ao Facebook no local de trabalho. Este sincronização automático do permite Facebook no trabalho para obter os dados que necessita para autorizar utilizadores para aceder à, adiantá-los a tentar iniciar sessão pela primeira vez. -Lo também retirar Aprovisiona utilizadores do Facebook no local de trabalho quando acesso foi revogado no Azure AD.

Aprovisionamento automático pode ser configurado ao clicar em **Configurar aprovisionamento de conta** na janela portal clássica do Azure.

Para detalhes adicionais sobre como configurar aprovisionamento automático, consulte o artigo [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## <a name="assigning-users-to-facebook-at-work"></a>Atribuir utilizadores ao Facebook no trabalho

Para aprovisionada AAD os utilizadores consigam ver Facebook no local de trabalho no seu painel de acesso, estas têm atribuídas acesso dentro do Azure portal clássico.

**Para atribuir aos utilizadores para o Facebook no trabalho:**

1.  Na página de início do Facebook no local de trabalho no portal do Azure clássico, clique em **atribuir contas**.

2.  No menu **Mostrar** , selecione se pretende atribuir um utilizador ou um grupo ao Facebook no local de trabalho e clique no botão de marca de verificação.

3.  Na lista resultante, selecione os utilizadores ou grupo a quem pretende atribuir o Facebook no local de trabalho.

4.  No rodapé da página, clique no botão **atribuir** .


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png




