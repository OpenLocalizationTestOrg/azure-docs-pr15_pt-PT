<properties
    pageTitle="Ligação do Azure AD: Introdução introdução utilizando as definições express | Microsoft Azure"
    description="Saiba como transferir, instalar e executar o Assistente de configuração para ligação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath"/>

# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Introdução ao ligação do Azure AD utilizando as definições express
Azure AD Connect **Definições rápidas** é utilizado quando tem um único floresta topologia e [sincronização de palavra-passe](../active-directory-aadconnectsync-implement-password-synchronization.md) para autenticação. **Definições rápidas** é a opção predefinida e é utilizado para o cenário mais frequentemente implementado. Está apenas alguns cliques breves fora do escritório para alargar o seu diretório no local para a nuvem.

Antes de iniciar a instalação de ligação do Azure AD, certifique-se para [Transferir a ligação do Azure AD](http://go.microsoft.com/fwlink/?LinkId=615771) e conclua exigidos pré passos no [ligação do Azure AD: Hardware e pré-requisitos](../active-directory-aadconnect-prerequisites.md).

Se as definições de express não corresponder a topologia, consulte o artigo [documentação relacionada](#related-documentation) para outros cenários.

## <a name="express-installation-of-azure-ad-connect"></a>Instalação rápida de ligação do Azure AD
Pode ver estes passos em ação, na secção [vídeos](#videos) .

1. Inicie sessão como administrador para o servidor que pretende instalar a ligação do Azure AD no local. Deve fazê-lo no servidor que pretende ser o servidor de sincronização.
2. Navegue para e faça duplo clique em **AzureADConnect.msi**.
3. No ecrã de boas-vindas, selecione a caixa concordar com os termos de licenciamento e clique em **continuar**.  
4. No ecrã de definições Express, clique em **definições de utilização de express**.  
![Ligar-se de boas-vindas do Azure AD](./media/active-directory-aadconnect-get-started-express/express.png)
5. Em ligar ao ecrã do Azure AD, introduza o nome de utilizador e palavra-passe de administrador global para o Azure AD. Clique em **seguinte**.  
![Ligar ao Azure AD](./media/active-directory-aadconnect-get-started-express/connectaad.png) se receber um erro e a ter problemas com a conectividade, em seguida, consulte [resolução de problemas de conectividade](../active-directory-aadconnect-troubleshoot-connectivity.md).
6. Em ligar ao ecrã de AD DS, introduza o nome de utilizador e palavra-passe para uma conta de administrador da empresa. Pode introduzir a peça de domínio no formato de NetBios ou FQDN, ou seja, FABRIKAM\administrator ou fabrikam.com\administrator. Clique em **seguinte**.  
![Ligar ao AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. A página de [**configuração de início de sessão no Azure AD**](../active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) mostra apenas se não foi concluída, [Verifique se os domínios do seu](../active-directory-add-domain.md) nos [Pré-requisitos](../active-directory-aadconnect-prerequisites.md).
![Domínios não verificados](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png)  
Se vir nesta página, em seguida, reveja todas as marcados **Não adicionada** e **Não verificado**o domínio. Certifique-se de foram verificados desses domínios que utiliza no Azure AD. Quando tiver verificado os domínios do seu, clique no símbolo de atualização.
8. No pronto para configurar o ecrã, clique em **instalar**.
    - Opcionalmente, no pronto para Configurar página, pode desmarcar a caixa de verificação **Iniciar o processo de sincronização assim que conclui a configuração** . Deve desmarcar esta caixa de verificação se pretender efetuar configuração adicional, como [Filtrar](../active-directory-aadconnectsync-configure-filtering.md). Se desmarcar esta opção, o assistente configura sincronização mas deixa o programador, desactivado. Não é executado até que ativá-lo manualmente executando [novamente o Assistente de instalação](../active-directory-aadconnectsync-installation-wizard.md).
    - Se tiver o Exchange no seu no local Active Directory, em seguida, também terá uma opção para ativar a [**implementação híbrida do Exchange**](https://technet.microsoft.com/library/jj200581.aspx). Ative esta opção se planeia ter caixas de correio do Exchange, tanto na nuvem e no local ao mesmo tempo.
![Pronto para configurar a ligação do Azure AD](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. Quando concluir a instalação, clique em **Sair**.
10. Após a instalação estiver concluída, inicie sessão e volte a iniciar sessão antes de utilizar o Gestor de serviço de sincronização ou Editor de regras de sincronização.

## <a name="videos"></a>Vídeos

Para um vídeo sobre como utilizar a instalação rápida, consulte:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## <a name="next-steps"></a>Próximos passos
Agora que tem ligação do Azure AD instalado pode [verificar a instalação e atribuir licenças](../active-directory-aadconnect-whats-next.md).

Saiba mais sobre estas funcionalidades que foram ativadas com a instalação: [atualização automática](../active-directory-aadconnect-feature-automatic-upgrade.md), [impedir acidental eliminações](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)e [Azure AD ligar-se do Estado de funcionamento](../active-directory-aadconnect-health-sync.md).

Saiba mais sobre estes tópicos comuns: [scheduler e como acionar sincronização](../active-directory-aadconnectsync-feature-scheduler.md).

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Documentação relacionada

Tópico |  
--------- | ---------
Azure AD Connect descrição geral | [Integrar a suas identidades no local com o Azure Active Directory](../active-directory-aadconnect.md)
Instalar o utilizando as definições personalizadas | [Instalação personalizada de ligação do Azure AD](active-directory-aadconnect-get-started-custom.md)
Actualizar a partir do DirSync | [Atualizar a partir da ferramenta de sincronização do Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Contas utilizadas para a instalação | [Mais informações sobre permissões e contas de ligação do Azure AD](active-directory-aadconnect-accounts-permissions.md)
