<properties
   pageTitle="Gestão de unidades administrativas no Azure Active Directory"
   description="Utilizar as unidades administrativas para delegação mais granular de permissões no Azure Active Directory"
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

# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gestão de unidades administrativas no Azure AD - Public Preview

Este artigo descreve as unidades administrativas – um novo contentor Azure Active Directory dos recursos que podem ser utilizados para delegar permissões administrativas sobre subconjuntos de utilizadores e aplicar políticas para um subconjunto dos utilizadores. No Azure Active Directory, unidades administrativas ativar administradores centrais para delegar permissões para administradores regionais ou para definir a política a um nível granular.

Isto é útil em organizações com divisões independentes, por exemplo, uma grande Universidade é constituída por vários autónomas escolas (Business school, escola de engenharia etc.) que são independentes uns dos outros. Essas divisões tem os seus próprios administradores de TI que controlam o acesso, gerir utilizadores e definir políticas especificamente para a sua divisão. Administradores central pretenderem possam conceder estes divisão permissões de administradores sobre os utilizadores na suas divisões específicos. Mais especificamente, utilizar neste exemplo, um administrador central pode, por exemplo, crie uma unidade administrativa para uma determinada escola (Business school) e preencha-a com apenas os utilizadores da escola de empresas. Em seguida, um administrador central pode adicionar a escola de negócio a equipa de TI a uma função âmbito, por outras palavras, conceda a equipa de TI de permissões administrativas do negócio escola apenas sobre a unidade de administrativa do negócio escola.

> [AZURE.IMPORTANT]
> Pode criar e utilizar as unidades administrativas apenas se ativar o Azure Active Directory Premium. Para mais informações, consulte o artigo [Introdução ao Azure AD Premium](active-directory-get-started-premium.md).

A partir do ponto de vista para o administrador central, uma unidade administrativa é um objeto de diretório que pode ser criado e povoado com recursos. **Neste lançamento, estes recursos podem ser apenas os utilizadores.** Assim que criou e povoada, a unidade administrativa pode ser utilizada como um âmbito para restringir a permissão concedida apenas através de recursos contidos na unidade administrativa.

## <a name="managing-administrative-units"></a>Gerir unidades administrativas

Nesta versão de pré-visualizar, pode criar e gerir as unidades administrativas utilizando os cmdlets do módulo Azure Active Directory para Windows PowerShell.

Para obter mais informações sobre os requisitos de software e instalar o módulo Azure AD e para obter informações sobre os cmdlets do módulo Azure AD para gerir unidades administrativas, incluindo sintaxe, descrições do parâmetro e exemplos, consulte o artigo [Gerir Azure AD através do Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).


## <a name="next-steps"></a>Próximos passos
[Edições do Azure Active Directory](active-directory-editions.md)
