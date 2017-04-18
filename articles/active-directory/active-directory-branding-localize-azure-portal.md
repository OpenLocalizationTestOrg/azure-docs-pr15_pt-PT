<properties
pageTitle="Adicionar empresa específicas do idioma de imagem corporativa à sua página de início de sessão na pré-visualização do Azure Active Directory | Microsoft Azure"
description="Saiba como adicionar uma empresa específicas do idioma de imagem corporativa imagens e texto para uma página de início de sessão no Azure"
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
ms.date="09/12/2016"
ms.author="curtand"/>

# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Adicionar empresa específicas do idioma de imagem corporativa à sua página de início de sessão na pré-visualização do Azure Active Directory

Para evitar a ambiguidade, muitas empresas pretendem aplicadas um aspeto e funcionalidade consistente em todos os Web sites e serviços que gerir. Pré-visualização do Azure Active Directory fornece esta capacidade, permitindo-lhe personalizar o aspeto da página Iniciar sessão com o seu logótipo da empresa e esquemas de cores personalizado. [Qual é a pré-visualização?](active-directory-preview-explainer.md) A página de início de sessão no está a página que aparece quando iniciar sessão no Office 365 ou outras aplicações baseadas na web que estão a utilizar o Azure AD como o seu fornecedor de identidade. Interagir com esta página para introduzir as suas credenciais.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Personalizar a página de início de sessão para outro idioma

Pode adicionar elementos específicos de idioma à sua página de início de sessão personalizada apenas se já tiver criado uma página personalizada iniciar sessão como descrito em [Adicionar empresa imagem corporativa à sua página de início de sessão](active-directory-branding-custom-signon-azure-portal.md). Pode configurar uma sessão página por diretório com um conjunto predefinido de elementos personalizáveis. Depois de ter configurado o conjunto predefinido de elementos da página, pode configurar as versões mais para idiomas diferentes. Também pode misturar e corresponder vários elementos. Por exemplo, pode:

- Crie uma **imagem na página Iniciar sessão** , que funciona para todos os culturas, em seguida, criar versões específicas para o inglês e francês predefinida. Quando definir o seu browsers para uma das seguintes duas idiomas, é apresentada a imagem específicas do idioma, enquanto a ilustração predefinido é apresentada para todos os outros idiomas.

- Configure logótipos diferentes para a sua organização (por exemplo, versões japonês ou hebraico).

Recomendamos que mantenha o número de variações de idioma baixa, por razões de desempenho e manutenção.

**Para adicionar marca para o seu diretório da empresa:**

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

    ![Gestão de utilizadores de abertura](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. No pá **utilizadores e grupos** , selecione a **imagem corporativa empresa**.

4. No **utilizadores e grupos - imagem corporativa da empresa** pá, selecione o comando **Adicionar um idioma** .

    ![Adicionar elementos de imagem corporativa específicas do idioma](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. Modificar os elementos que pretende personalizar. Todos os elementos são opcionais.

6. Clique em **Guardar**.

Pode demorar até a uma hora para quaisquer alterações que efetuou para a página de início de sessão no imagem corporativa que seja apresentado.

## <a name="next-steps"></a>Próximos passos

[Adicionar a imagem corporativa à sua página de início de sessão de empresa](active-directory-branding-custom-signon-azure-portal.md)
