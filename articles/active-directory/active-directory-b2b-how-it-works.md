<properties
   pageTitle="Pré-visualização de colaboração do Azure AD B2B: como funciona | Microsoft Azure"
   description="Descreve como o Azure Active Directory B2B colaboração suporta as relações de publicação em empresa ao ativar parceiros de negócios aceder a suas aplicações empresariais seletivamente"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-how-it-works"></a>Pré-visualização de colaboração do Azure AD B2B: como funciona
Colaboração do Azure AD B2B baseia-se um convite e resgate modelo. Forneça os endereços de e-mail das partes que pretende trabalhar com, juntamente com as aplicações que pretenda utilizar. Azure AD-los envia um convite de e-mail com uma ligação. O utilizador parceiro segue a ligação e é pedido que inicie sessão com a sua conta do Azure AD ou início de sessão para uma nova Azure AD de conta.

1. O administrador convida utilizadores parceiros ao carregar [um ficheiro. csv estruturadas](active-directory-b2b-references-csv-file-format.md) através do portal Azure.
2. O portal envia convidar estes utilizadores parceiro os e-mails.
3. Os utilizadores de parceiro clique na ligação no e-mail e lhe for pedidos para iniciar sessão com as suas credenciais de trabalho (se se encontram já Azure AD) ou para inscrever-se como um utilizador de colaboração do Azure AD B2B.
4. Utilizadores de parceiro são redirecionados para a aplicação que foi convidados, onde tenham acesso agora.

## <a name="directory-operations"></a>Operações de diretório
Utilizadores do parceiro existem no seu Azure AD como os utilizadores externos. Isto significa que o administrador pode aprovisionar licenças, atribuir a associação a grupos e ainda mais conceder acesso às aplicações da empresa através do portal do Azure ou através do PowerShell do Azure, tal como para os utilizadores na sua empresa.

Enquanto uma paga Azure AD subscrição (Basic ou Premium) não é necessária utilizar Azure AD B2B, inquilinos que têm uma paga subscrição do Azure AD (Basic ou Premium) receber os seguintes benefícios adicionais:

 - Administradores podem atribuir grupos para as aplicações, fornecendo para gestão mais simples de acesso de utilizador convidado.
 - Administrador inquilino imagem corporativa é utilizado para criar marca para os e-mails de convite e experiência de reembolso, fornecendo mais contexto para convidado utilizadores do parceiro.

## <a name="related-articles"></a>Artigos relacionados
 Procure os nossos outros artigos sobre colaboração B2B do Azure AD

 - [O que é o Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Obter instruções detalhadas sobre](active-directory-b2b-detailed-walkthrough.md)
 - [Referência de formato de ficheiro CSV](active-directory-b2b-references-csv-file-format.md)
 - [Formato de token de utilizadores externos](active-directory-b2b-references-external-user-token-format.md)
 - [Alterações de atributo de objeto do utilizador externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limitações de pré-visualização atual](active-directory-b2b-current-preview-limitations.md)
 - [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
