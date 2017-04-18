<properties
   pageTitle="O Assistente de segurança do Azure AD privilegiados gestão de identidades"
   description="A primeira vez que utiliza a extensão do Azure Active Directory privilegiados gestão de identidades, será apresentada com um Assistente de segurança. Este artigo descreve os passos para utilizar o assistente."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>O Assistente de segurança do Azure AD privilegiados gestão de identidades

Se for a primeira pessoa para executar o Azure privilegiados identidade gestão (PIM) para a sua organização, ser-lhe-á apresentado um assistente. O assistente ajuda a compreender os riscos de segurança de identidades privilegiadas e como utilizar PIM para reduzir esses riscos. Não precisa de efetuar as alterações às atribuições de funções existentes no assistente, se preferir fazê-lo mais tarde.

## <a name="what-to-expect"></a>O que esperar

Antes de ser iniciada a sua organização, utilizando PIM, todas as atribuições de funções são permanentes: os utilizadores estão sempre estas funções mesmo que não têm atualmente respectivos privilégios.  O primeiro passo do assistente mostra-lhe uma lista das funções de alta privilegiados e o número de utilizadores está a ser dessas funções. Pode agregar para uma determinada função para saber mais sobre os utilizadores se uma ou mais dos mesmos estão familiarizados.

O segundo passo do assistente dá-lhe uma oportunidade para alterar as atribuições de funções do administrador.  

> [AZURE.WARNING]É importante que tem, pelo menos, um administrador global e mais do que um administrador de função privilegiados com uma conta institucional (não uma conta Microsoft). Se existir apenas um administrador de função privilegiados, a organização não poderão gerir PIM se essa conta é eliminada.
> Além disso, manter atribuições de funções permanente se um utilizador tem uma conta Microsoft (uma conta que utilizam para iniciar sessão serviços da Microsoft como o Skype e o Outlook.com). Se planeia requerer MFA para ativação para essa função, esse utilizador será bloqueado.


Depois de efetuar alterações, o assistente já não irá aparecer. Da próxima vez que o utilizador ou outra função privilegiados administrador utilizar PIM, verá o dashboard PIM.  

- Se pretender para adicionar ou remover utilizadores de funções ou alterar as atribuições de permanente para elegível, leia mais em [como adicionar ou remover funções de um utilizador](active-directory-privileged-identity-management-how-to-add-role-to-user.md).
- Se pretender para conceder acesso para gerir PIM mais utilizadores, continue a ler mais como [conceder acesso a gestão de PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).



## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
