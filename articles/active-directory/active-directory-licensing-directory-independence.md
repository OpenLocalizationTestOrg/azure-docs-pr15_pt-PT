<properties
   pageTitle="Adicionar e gerir vários directórios Azure Active Directory | Microsoft Azure"
   description="Instruções e as melhores práticas para adicionar e gerir directórios o Azure Active Directory, explicar directórios como um recursos totalmente independente"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Adicionar e gerir vários directórios Azure Active Directory

No Azure Active Directory (Azure AD), cada directório é um recurso totalmente independente: um ponto, totalmente em destaque e logicamente independente de outros directórios que faz a gestão. Não existe nenhuma relação de elemento principal-subordinado entre diretórios. Esta independência entre directórios inclui independência de recursos, independência administrativa e independência de sincronização.

##<a name="resource-independence"></a>Independência de recursos

Se criar ou eliminar um recurso de um diretório, tem nenhum impacto no qualquer recurso no diretório outra, com a exceção parcial de utilizadores externos, descrito abaixo. Se utilizar um domínio personalizado 'contoso.com' com um diretório, não podem ser utilizado com qualquer outro directório.

##<a name="administrative-independence"></a>Independência administrativa

Se um utilizador que não sejam administrativo do directório 'Contoso' cria um diretório de teste 'Teste', em seguida:
- Por predefinição, o utilizador cria um diretório é adicionado como um utilizador externo nesse directório novo e foi atribuído a função de administrador global nesse directório.
- Os administradores do directório 'Contoso' têm sem privilégios administrativos diretos para directório 'Teste', a não ser um administrador de 'Teste' concede especificamente-los estes privilégios. Os administradores de 'Contoso' podem controlar o acesso ao diretório 'Teste' se como controlar a conta de utilizador que criou 'Teste'.
- Se alterar (adicionar ou remover) uma função de administrador para um utilizador no diretório de um, a alteração não afeta a qualquer função de administrador que o utilizador poderá ter noutro directório.

##<a name="synchronization-independence"></a>Independência de sincronização

Pode configurar cada directório Azure AD separadamente para obter dados sincronizados a partir de uma única ocorrência de quer:
  - A ferramenta de sincronização de diretórios (DirSync), para sincronizar dados com uma única floresta AD.
  - O Azure Active Directory Connector para Forefront Gestor de identidades, para sincronizar dados com um ou mais florestas no local e/ou origens de dados que não sejam Azure AD.

##<a name="add-an-azure-ad-directory"></a>Adicionar um diretório do Azure AD

Para adicionar um diretório do Azure AD no portal do Azure clássico, selecione a extensão do Azure Active Directory à esquerda e toque em **Adicionar**.

> [AZURE.NOTE]   Ao contrário de outros recursos Azure, os directórios não estão recursos subordinado de uma subscrição do Azure. Se cancelar ou permitir a sua subscrição do Azure a expirar, pode ainda aceder os dados de directório utilizando Azure PowerShell, a API do Azure Graph ou outras interfaces como o Centro de administração do Office 365. Também pode associar outra subscrição diretório.

Para uma descrição geral do problemas de licenciamento do Azure AD e as melhores práticas, consulte o artigo [o que é Azure Active Directory licenciamento?](active-directory-licensing-what-is.md).
