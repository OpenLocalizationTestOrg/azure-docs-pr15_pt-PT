<properties
    pageTitle="Controlo de acesso baseado em funções | Microsoft Azure"
    description="Introdução ao gestão de acesso no controlo de acesso baseado em funções Azure no Portal do Azure. Utilize as atribuições de funções para atribuir permissões no seu diretório."
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
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="get-started-with-access-management-in-the-azure-portal"></a>Começar a trabalhar com gestão de acesso no portal do Azure

Empresas orientados segurança devem concentrar-se que lhe dá uma empregados as permissões exatas que necessitam. Permissões demasiadas expõe uma conta a intrusos. Permissões muito poucos significa que os funcionários não é possível obter o trabalho de forma eficaz. Azure baseado em funções acesso controlo RBCA () ajuda-o a resolver este problema através da oferta de gestão de acesso extensivamente do Azure.

Utilizar RBAC, pode segregar direitos dentro da sua equipa e conceder apenas a quantidade de acesso aos utilizadores que precisam de efectuar os seus trabalhos. Em vez de que lhe dá uma todos os outros ilimitado permissões na sua subscrição do Azure ou os recursos, pode permitir apenas determinadas ações. Por exemplo, utilize RBAC para permitir que um empregado gerir máquinas virtuais numa subscrição, enquanto outro pode gerir bases de dados do SQL na mesma subscrição.

## <a name="basics-of-access-management-in-azure"></a>Noções básicas de gestão do access no Azure
Cada subscrição Azure está associada um diretório do Azure Active Directory (AD). Os utilizadores, grupos e aplicações a partir desse directório podem gerir recursos na subscrição do Azure. Atribua estes direitos de acesso utilizando o Azure portal, ferramentas de linha de comandos Azure e APIs de gestão de Azure.

Conceder acesso ao atribuir o papel RBAC adequado para os utilizadores, grupos e aplicações de um determinado âmbito. O âmbito de uma atribuição de funções pode ser uma subscrição, um grupo de recursos ou um único recurso. Uma função atribuída num âmbito de elemento principal concede também acesso aos filhos nela contidas. Por exemplo, um utilizador com acesso a um grupo de recursos pode gerir todos os recursos que contém, como sites públicos, máquinas virtuais e sub-redes.

![Relação entre os elementos do Azure Active Directory - diagrama](./media/role-based-access-control-what-is/rbac_aad.png)

A função RBAC atribuir dita quais os recursos que o utilizador, grupo ou aplicação, pode gerir dentro desse âmbito.

## <a name="built-in-roles"></a>Funções incorporadas
Azure RBAC tem três funções básicas que se aplicam a todos os tipos de recursos:

- **Proprietário** tem acesso total a todos os recursos, incluindo o direito de acesso de delegado para outras pessoas.
- **Contribuinte** pode criar e gerir todos os tipos de recursos Azure mas não pode conceder acesso a outras pessoas.
- **Leitor** pode ver os recursos Azure existentes.

O resto das funções RBAC no Azure permitir a gestão dos recursos Azure específicos. Por exemplo, a função Contribuinte Máquina Virtual permite ao utilizador criar e gerir máquinas virtuais. -Não conceder-lhes acesso para a rede virtual ou a sub-rede que liga a máquina virtual.

[Funções incorporadas RBAC](role-based-access-built-in-roles.md) lista as funções disponíveis no Azure. Especifica o operações e um âmbito que cada função incorporada concede a utilizadores. Se estiver à procura para definir o seus próprio funções para ter ainda mais controlo, consulte o artigo como construir [funções de personalizada no Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Herança de hierarquia e o acesso de recursos
- Cada **subscrição** no Azure pertence a apenas um diretório.
- Cada **grupo de recursos** pertence a apenas uma subscrição.
- Grupo de recursos apenas uma pertence cada **recurso** .

Access que pode conceder na âmbitos de elemento principal é herdada na âmbitos subordinados. Por exemplo:

- Atribuir a função de leitor a um grupo do Azure AD no âmbito de subscrição. Os membros desse grupo podem ver a cada grupo de recursos e recursos na subscrição.
- Atribuir a função Contribuinte para uma aplicação do âmbito do grupo de recursos. Pode gerir recursos de todos os tipos desse grupo de recursos, mas não outros grupos de recursos na subscrição.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC vs. administradores de subscrição clássico
Os administradores de subscrição clássica e coadministradores tem acesso completo para a subscrição Azure. Que possam gerir recursos utilizando o [Azure portal](https://portal.azure.com) com APIs de Gestor de recursos do Azure ou o modelo de clássico de implementação do [Azure portal clássica](https://manage.windowsazure.com) e Azure. No modelo RBAC, os administradores clássicos são atribuídos a função de proprietário ao âmbito de subscrição.

Apenas o portal do Azure e as novas APIs de Gestor de recursos do Azure suportam Azure RBAC. Os utilizadores e aplicações que são atribuídas a funções RBAC não é possível utilizar o portal de gestão clássica e o modelo de implementação clássica Azure.

## <a name="authorization-for-management-vs-data-operations"></a>Autorização para a gestão de vs. operações de dados
Azure RBAC apenas suporta operações de gestão dos recursos Azure no portal do Azure e APIs de Gestor de recursos do Azure. Não é possível autorizar a todas as operações de nível de dados para recursos Azure. Por exemplo, pode autorizar alguém para gerir contas de armazenamento, mas não para a blobs ou tabelas dentro de uma conta de armazenamento não for possível. Do mesmo modo, uma base de dados do SQL pode ser gerido, mas não as tabelas dentro da mesma.

## <a name="next-steps"></a>Próximos passos
- Introdução ao [controlo de acesso baseado em funções no portal do Azure](role-based-access-control-configure.md).
- Consulte as [funções incorporadas RBAC](role-based-access-built-in-roles.md)
- Definir as suas próprias [funções personalizadas no Azure RBAC](role-based-access-control-custom-roles.md)
