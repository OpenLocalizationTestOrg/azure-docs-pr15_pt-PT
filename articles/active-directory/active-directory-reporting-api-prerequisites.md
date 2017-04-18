<properties
    pageTitle="Pré-requisitos para aceder ao Azure AD API de elaboração de relatórios. | Microsoft Azure"
    description="Saiba mais sobre as pré-requisitos para aceder ao Azure AD API de elaboração de relatórios"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Pré-requisitos para aceder ao Azure AD API de elaboração de relatórios 

O [Azure AD relatórios APIs](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecer-lhe acesso aos dados através de um conjunto de APIs com base no resto de programação. Pode ligar a estas APIs a partir de vários idiomas e as ferramentas de programação.

A API elaboração de relatórios utiliza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar acesso à web APIs. 

Para preparar o seu acesso à API elaboração de relatórios, tem de:

1. Criar uma aplicação no seu inquilino do Azure AD 

2. Conceder as permissões adequadas de aplicação para aceder aos dados do Azure AD

3. Reunir as definições de configuração a partir do seu diretório

Para as questões, problemas ou comentários, contacte o suportehttp [AAD relatórios ajudar](mailto:aadreportinghelp@microsoft.com).


## <a name="create-an-azure-ad-application"></a>Criar uma aplicação do Azure AD

Para configurar o seu diretório para aceder ao Azure AD API de elaboração de relatórios, tem de iniciar sessão portal do Azure clássico com uma conta de administrador de subscrição Azure também é um membro da função de Administrador Global de diretório no inquilino do Azure AD.

> [AZURE.IMPORTANT] Aplicações em execução em credenciais com privilégios de "administrador" assim podem ser muito poderosas, por isso, certifique-se manter as credenciais de ID/secreta a aplicação seguro.


1. No [portal clássica Azure](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/01.png) 

2. A partir da lista **do active directory** , selecione o seu diretório.

3. No menu no canto superior, clique em **aplicações**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na barra inferior, clique em **Adicionar**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/03.png) 

5. Na **o que pretende fazer?** caixa de diálogo, clique em **Adicionar uma aplicação que está a desenvolver a minha organização**. 

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/04.png) 


6. Na caixa de diálogo **diga-nos sobre a aplicação** , execute os seguintes passos: 

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/05.png) 

    um. Na caixa de texto **nome** , escreva um nome (por exemplo: na aplicação API relatório).

    b. Selecione a **aplicação Web e/ou web API**.

    c. Clique em **seguinte**.


7. Na caixa de diálogo **Propriedades da aplicação** , execute os seguintes passos: 

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/06.png) 

    um. Na caixa de texto **URL de início de sessão** , escreva `https://localhost`.

    b. Na caixa de texto **URI de ID da aplicação** , escreva ```https://localhost/ReportingApiApp```.

    c. Clique em **concluído**.



## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder a permissão de aplicação para utilizar a API

1. No [portal clássica Azure](https://manage.windowsazure.com/), no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/01.png) 

2. A partir da lista **do active directory** , selecione o seu diretório.

3. No menu no canto superior, clique em **aplicações**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/02.png)


3. Na lista de aplicações, selecione a aplicação recentemente criada.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu no canto superior, clique em **Configurar**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/08.png)


5. Na secção **permissões para outras aplicações** , para o recurso **Azure Active Directory** , clique na lista pendente de **Permissões da aplicação** e, em seguida, selecione **os dados de directório de leitura**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/09.png)


5. Na barra inferior, clique em **Guardar**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>Reunir as definições de configuração a partir do seu diretório

Esta secção mostra-lhe como obter as seguintes definições do seu diretório:

- Nome de domínio
- ID de cliente
- Secreta de cliente

Precisa destes valores ao configurar chamadas para a API elaboração de relatórios. 


### <a name="get-your-domain-name"></a>Obter o seu nome de domínio

1. No [portal clássica Azure](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/01.png) 

2. A partir da lista **do active directory** , selecione o seu diretório.

3. No menu no canto superior, clique em **domínios**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/11.png) 

4. Na coluna **Nome de domínio** , copie o seu nome de domínio.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-applications-client-id"></a>Obter ID de cliente a aplicação

1. No [portal clássica Azure](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/01.png) 

2. A partir da lista **do active directory** , selecione o seu diretório.

3. No menu no canto superior, clique em **aplicações**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na lista de aplicações, selecione a aplicação recentemente criada.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu no canto superior, clique em **Configurar**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/08.png)

4. Copie o seu **ID de cliente**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-applications-client-secret"></a>Obter cliente palavra-passe a aplicação

Para obter cliente palavra-passe a aplicação, tem de criar uma nova chave e guardar o seu valor após guardar a nova chave de uma vez que não é possível obter mais tarde deixem este valor.

1. No [portal clássica Azure](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/01.png) 

2. A partir da lista **do active directory** , selecione o seu diretório.

3. No menu no canto superior, clique em **aplicações**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na lista de aplicações, selecione a aplicação recentemente criada.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu no canto superior, clique em **Configurar**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/08.png)

5. Na secção de **teclas** , execute os seguintes passos: 

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/14.png)

    um. A partir da lista de duração, selecione uma duração

    b. Na barra inferior, clique em **Guardar**.

    ![Registe-se a aplicação](./media/active-directory-reporting-api-prerequisites/10.png)

    c. Copie o valor de chave.

## <a name="next-steps"></a>Próximos passos

- Pretende aceder aos dados a partir do Azure AD relato API de uma maneira de programação? Consulte o artigo [Introdução ao Azure Active Directory relatórios API](active-directory-reporting-api-getting-started.md).

- Se quiser saber mais sobre relatórios do Azure Active Directory, consulte o artigo o [Azure relato Guia do Active Directory](active-directory-reporting-guide.md).  
