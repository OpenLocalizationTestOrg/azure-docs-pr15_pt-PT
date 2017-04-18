<properties
    pageTitle="Sincronização do Azure AD Connect: executar o Assistente de instalação uma segunda vez | Microsoft Azure"
    description="Explica como o Assistente de instalação funciona a segunda vez que executá-la."
    keywords="O Assistente de instalação de ligação do Azure AD permite-lhe configurar definições de manutenção da segunda vez que executá-la"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronização do Azure AD Connect: executar o Assistente de instalação uma segunda vez
A primeira vez que executa o Assistente de instalação de ligação do Azure AD-orienta como configurar a sua instalação. Se executar o Assistente de instalação novamente, oferece-opções para manutenção.

Pode encontrar o Assistente de instalação no menu Iniciar com o nome **ligação do Azure AD**.

![Menu Iniciar](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Quando inicia o Assistente de instalação, verá uma página com estas opções:

![Página com uma lista de tarefas adicionais](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Se tiver instalado o ADFS com ligação do Azure AD, tem opções ainda mais. As opções adicionais para ADFS encontram-se documentados na [Gestão de ADFS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Selecione uma das tarefas e clique em **seguinte** para continuar.

> [AZURE.IMPORTANT] Apesar de ter o Assistente de instalação abrir, todas as operações do motor de sincronização são suspensa. Certifique-se de que fechar o Assistente de instalação, assim que tiver concluído as alterações à configuração.

## <a name="view-current-configuration"></a>Configuração da vista atual
Esta opção dá-lhe uma vista rápida do seu opções atualmente configuradas.

![Página com uma lista de todas as opções e o seu estado](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Clique em **anterior** para voltar. Se selecionar **Sair**, fechar o Assistente de instalação.

## <a name="customize-synchronization-options"></a>Personalizar as opções de sincronização
Esta opção é utilizada para fazer alterações na configuração de sincronizar. Ver um subconjunto de opções a partir do caminho de instalação de configuração personalizado. Verá esta opção, mesmo se tiver utilizado instalação rápida inicialmente.

- [Adicionar mais directórios](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Para remover um diretório, consulte [Eliminar uma conexão](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Alterar domínio e filtragem de or](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Remova a filtragem de grupo.
- [Funcionalidades opcionais alterar](active-directory-aadconnect-get-started-custom.md#optional-features).

As outras opções da instalação inicial não podem ser alteradas e não estão disponíveis. Estas opções são:

- Altere o atributo a utilizar para userPrincipalName e sourceAnchor.
- Altere o método de junção para objetos de floresta diferente.
- Active a filtragem baseada em grupos.

## <a name="refresh-directory-schema"></a>Atualizar o esquema de diretório
Esta opção é utilizada se tiver alterado o esquema de uma das suas no local florestas do AD DS. Por exemplo, poderá ter instalado Exchange ou atualizado para um esquema do Windows Server 2012 com objectos de dispositivo. Neste caso, tem de indicar a ligação do Azure AD para ler novamente o esquema do AD DS e atualizar a cache. Esta ação também gera novamente as regras de sincronização. Se adicionar o esquema do Exchange, por exemplo, as regras de sincronização para o Exchange são adicionadas à configuração.

Quando selecionar esta opção, todos os directórios na configuração são listados. Pode manter a predefinição e atualizar todos os florestas ou anular a seleção de alguns dos mesmos.

![Página com uma lista de todos os directórios no ambiente](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurar o modo de transição
Esta opção permite-lhe ativar e desativar o modo transição no servidor. Podem encontrar mais informações sobre a transição modo e como são utilizadas em [operações](active-directory-aadconnectsync-operations.md#staging-mode).

Mostra a opção se transição atualmente está ativada ou desactivada:  
![Opção que também esteja a mostrar o estado atual de modo de transição](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Para alterar o estado, selecione esta opção e selecionar ou anular a seleção na caixa de verificação.  
![Opção que também esteja a mostrar o estado atual de modo de transição](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Alterar a sessão do utilizador
Esta opção permite-lhe alterar de sincronização de palavra-passe para a Federação ou a outra forma à volta. Não é possível alterar para **não a configurar**.

Para mais informações sobre esta opção, consulte o artigo [utilizador iniciar sessão](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o modelo de configuração utilizado pelo ligação do Azure AD sync no [Noções sobre declarativa aprovisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
