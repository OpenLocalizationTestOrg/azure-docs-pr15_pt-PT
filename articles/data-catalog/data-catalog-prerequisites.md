<properties
   pageTitle="Pré-requisitos de catálogo de dados Azure | Microsoft Azure"
   description="Azure catálogo de dados pré-requisitos - o que precisa para começar a utilizar o catálogo de dados do Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-prerequisites"></a>Azure pré-requisitos de catálogo de dados

## <a name="what-do-i-need-to-get-started-with-azure-data-catalog"></a>O que preciso começar a utilizar o catálogo de dados do Azure?

Existem algumas coisas que vai precisar de encarregam-se de antes de poder definir o **Catálogo de dados do Azure**. Não se preocupe – não demorarem tempo!

## <a name="azure-subscription"></a>Subscrição do Azure
Para configurar o catálogo de dados do Azure, tem de ser o proprietário ou coproprietário de uma subscrição do Azure.

Subscrições do Azure ajudá-lo a organizar o acesso aos recursos de serviço de nuvem como o catálogo de dados do Azure. Também pode controlar como a utilização de recursos é comunicada, de ajudam faturada e paga. Cada subscrição pode têm uma configuração de faturação e de pagamento diferente, pelo que pode ter diferentes subscrições e os diferentes planos por departamento, project, regional office e assim sucessivamente. Cada serviço em nuvem pertence a uma subscrição e, tem de ter uma subscrição antes de configurar o catálogo de dados do Azure. Para saber mais, consulte o artigo [Gerir contas, subscrições e funções administrativas](../active-directory/active-directory-assign-admin-roles.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Para configurar o catálogo de dados do Azure, tem de ter iniciado sessão com uma conta de utilizador do Azure Active Directory.

Azure Active Directory (Azure AD) fornecem uma forma fácil para a sua empresa gerir a identidade e de acesso, tanto na nuvem e no local. Os utilizadores podem utilizar uma única conta escolar ou profissional do serviço single sign-on qualquer nuvem e no local aplicação web. Catálogo de dados Azure utiliza Azure AD para autenticar início de sessão. Para saber mais, consulte o artigo [o que é o Azure Active Directory](../active-directory/active-directory-whatis.md).

> [AZURE.NOTE] O [Azure portal](http://portal.azure.com/) permite aos utilizadores inicie sessão com uma Account Microsoft pessoal ou um trabalho Azure Active Directory conta ou profissional. Para configurar o catálogo de dados do Azure através do portal Azure ou utilizando o [portal de catálogo de dados](http://www.azuredatacatalog.com) tem de ter iniciado sessão com uma conta do Azure Active Directory, não uma conta pessoal.

## <a name="active-directory-policy-configuration"></a>Configuração de política do Active Directory

Em algumas situações, os utilizadores podem encontrar uma situação na qual pode iniciar sessão no portal do catálogo de dados do Azure, mas quando tentarem iniciar sessão para a ferramenta de registo de origem de dados que encontram uma mensagem de erro impede que iniciar sessão. Este comportamento de problema pode ocorrer apenas quando o utilizador estiver na rede da empresa ou pode ocorrer apenas quando o utilizador está a ligar a partir de fora da rede da empresa.

A ferramenta de registo da origem de dados utiliza a autenticação de formulários para validar os inícios de sessão do utilizador contra do Active Directory. Para o início de sessão efetuada com êxito, deve ser ativada autenticação de formulários na política de autenticação Global por um administrador do Active Directory.

A política de autenticação Global permite métodos de autenticação para ser activados em separado para intranet e ligações extranet, conforme ilustrado abaixo. Podem ocorrer erros de início de sessão se autenticação de formulários não estiver ativada para a rede a partir do qual o utilizador está a ligar.

 ![Política de autenticação Global do Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obter mais informações, consulte [Configurar políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).
