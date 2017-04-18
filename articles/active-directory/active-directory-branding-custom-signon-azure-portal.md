<properties
pageTitle="Personalizar a sua página de início de sessão na pré-visualização do Azure Active Directory | Microsoft Azure"
description="Saiba como adicionar uma marca para a página de início de sessão no Azure da empresa"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/30/2016"
ms.author="curtand"/>

# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Adicionar empresa imagem corporativa à sua página de início de sessão na pré-visualização do Azure Active Directory

Para evitar a ambiguidade, muitas empresas pretendem aplicadas um aspeto e funcionalidade consistente em todos os Web sites e serviços que gerir. Pré-visualização do Azure Active Directory fornece esta capacidade, permitindo-lhe personalizar o aspeto da página Iniciar sessão com o seu logótipo da empresa e esquemas de cores personalizado. [Qual é a pré-visualização?](active-directory-preview-explainer.md) A página de início de sessão no está a página que aparece quando iniciar sessão no Office 365 ou outras aplicações baseadas na web que estão a utilizar o Azure AD como o seu fornecedor de identidade. Interagir com esta página para introduzir as suas credenciais.

Se pretender mostrar a sua marca corporativa da empresa, cores e outros elementos personalizáveis nesta página, consulte as seguintes imagens para compreender a diferença entre as duas experiências.

A seguinte captura de ecrã mostra e exemplo para a página de início de sessão do Office 365 num computador de secretária **antes de** uma personalização:

![Início de sessão na página do Office 365 antes de personalização](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

A seguinte captura de ecrã mostra e exemplo para a página de início de sessão do Office 365 num computador de secretária **após** uma personalização:

![Início de sessão na página do Office 365 depois de personalização](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## <a name="customizing-the-sign-in-page"></a>Personalizar a página de início de sessão

Normalmente, se precisar de acesso baseado em browser a suas nuvem aplicações e serviços que a sua organização subscreveu, utilize a página de início de sessão.

Se tiver aplicado alterações à sua página de início de sessão, pode demorar até a uma hora para as alterações sejam apresentadas.

Uma página de início de sessão com marca corporativa só aparece quando visita um serviço com um URL de específicas do inquilino como https://outlook.com/**contoso**com ou https://mail. **contoso**. com.

Quando visita um serviço com URLs específicos que não sejam inquilino (por exemplo: https://mail.office365.com), será apresentada uma que não seja marca início de sessão na página. Neste caso, a imagem corporativa é apresentada uma vez que introduziu o seu ID de utilizador ou selecionar um mosaico do utilizador.

> [AZURE.NOTE]
>
- O nome de domínio tem de aparecer como "Ativa" na parte **domínios** do portal do Azure em que configurou imagem corporativa. Para mais informações, consulte o artigo [Adicionar nomes de domínio personalizado](active-directory-domains-add-azure-portal.md).
- Página de início de sessão no imagem corporativa não utilizava para consumidor página Iniciar sessão da Microsoft. Se iniciar sessão com uma conta Microsoft, poderá ver uma lista com marca corporativa dos mosaicos de utilizador composto por Azure AD, mas a imagem corporativa da sua organização não se aplica à Microsoft início de sessão na página de conta.

Na sua página de início de sessão no, a caixa de verificação **manter a sessão iniciada** permite que um utilizador permanecem com sessão iniciada na quando que feche e abra novamente o seu browser. 

   ![Manter-me tiver sessão iniciada no](./media/active-directory-branding-custom-signon-azure-portal/01.png)

Não-efeito duração da sessão. Pode ocultar a caixa de verificação a página de início de sessão no Azure Active Directory.
Se é apresentada a caixa de verificação depende a definição de **manter iniciada desativado**.

   ![Manter-me tiver sessão iniciada no](./media/active-directory-branding-custom-signon-azure-portal/02.png)


Para ocultar a caixa de verificação, configure esta definição para **Sim**. 

> [AZURE.NOTE] Algumas funcionalidades do SharePoint Online e o Office 2010 dependem utilizadores ser capaz desta caixa de verificação. Se configurar esta definição para oculta, os seus utilizadores poderão ver adicionais e inesperados pedidos para iniciar sessão.




**Para adicionar marca para o seu diretório da empresa:**

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

    ![Gestão de utilizadores de abertura](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. No pá **utilizadores e grupos** , selecione a **imagem corporativa empresa**.

4. No **utilizadores e grupos - imagem corporativa da empresa** pá, selecione o comando **Editar** .

    ![Editar imagem corporativa personalizada](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modificar os elementos que pretende personalizar. Todos os elementos são opcionais.

6. Clique em **Guardar**.

Pode demorar até a uma hora para quaisquer alterações que efetuou para a página de início de sessão no imagem corporativa que seja apresentado.

## <a name="next-steps"></a>Próximos passos

[Adicionar a marca da empresa específicas do idioma](active-directory-branding-localize-azure-portal.md)
