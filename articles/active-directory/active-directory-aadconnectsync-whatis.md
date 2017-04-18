<properties
    pageTitle="Sincronização do Azure AD Connect: compreender e personalizar a sincronização | Microsoft Azure"
    description="Explica como ligação do Azure AD sincronizar funciona e como personalizar."
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
    ms.date="08/29/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Sincronização do Azure AD Connect: compreender e personalizar a sincronização
Os serviços de sincronização do Azure Active Directory estabelecer ligação (ligação do Azure AD sync) é um componente principal da ligação do Azure AD. Leva-o cuidado de todas as operações que estão relacionados com para sincronizar os dados de identidade entre o seu ambiente no local e o Azure AD. Sincronização do Azure AD Connect é a tarefa sucessora de DirSync, Azure AD Sync e Forefront Gestor de identidade com o Azure Active Directory Connector configurado.

Este tópico é base para a **sincronização de ligação do Azure AD** (também denominado **motor de sincronização**) e listas de ligações para todos os outros tópicos relacionados com a mesma. Para estabelecer ligações a ligação do Azure AD, consulte o artigo [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).

O serviço de sincronização é composta por dois componentes, o componente de **ligação do Azure AD sync** no local e do lado do serviço no Azure AD denominado de **ligação do Azure AD sync serviço**. O serviço está comuns para DirSync, Azure AD Sync e ligação do Azure AD.

## <a name="azure-ad-connect-sync-topics"></a>Tópicos de sincronização do Azure AD Connect

Tópico | O que abrange e quando a leitura
----- | -----
**Noções básicas de sincronização do Azure AD Connect** |
[Noções sobre a arquitetura](active-directory-aadconnectsync-understanding-architecture.md) | Para aqueles que estiver familiarizado com o motor de sincronização e quer saber mais sobre a arquitetura e os termos utilizados.
[Conceitos técnicos](active-directory-aadconnectsync-technical-concepts.md) | Uma versão abreviada do tópico arquitetura e saem explica os termos utilizados.
[Ligar topologias para Azure AD](active-directory-aadconnect-topologies.md) | Descreve os diferentes topologias e cenários que suporta o motor de sincronização.
**Configuração personalizada** |
[Executando novamente o Assistente de instalação](active-directory-aadconnectsync-installation-wizard.md) | Explica que opções tem disponíveis ao executar o Assistente de instalação de ligação do Azure AD novamente.
[Noções sobre aprovisionamento declarativa](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| Descreve o modelo de configuração denominado aprovisionamento declarativa.
[Noções sobre declarativas expressões de aprovisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Descreve a sintaxe para a linguagem da expressão utilizada num aprovisionamento declarativa.
[Noções sobre a configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md)| Descreve as regras de caixa de saída e a configuração predefinida. Também descreve como as regras funcionam em conjunto para os cenários de fora da caixa trabalhar.
[Noções sobre utilizadores e contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continua no tópico anterior e descreve como a configuração para os utilizadores e contactos funciona em conjunto, nomeadamente num ambiente floresta múltipla.
[Como efetuar uma alteração para a configuração predefinida](active-directory-aadconnectsync-change-the-configuration.md) | Explica como fazer uma configuração comum alterar para fluxos de atributo.
[Práticas recomendadas para alterar a configuração predefinida](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Limitações de suporte e para fazer alterações na configuração out of box.
[Configurar a filtragem](active-directory-aadconnectsync-configure-filtering.md) | Descreve as diferentes opções para como limitar quais os objectos estão a ser sincronizada para Azure AD e passo a passo como configurar estas opções.
**Funcionalidades e aos cenários** |
[Impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Descreve a funcionalidade *impedir eliminações acidentais* e como configurá-lo.
[Programador](active-directory-aadconnectsync-feature-scheduler.md) | Descreve o scheduler incorporado, que está a importar, sincronizar e exportação de dados.
[Implementar a sincronização de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) | Descreve como funciona a sincronização de palavra-passe, como implementar e como trabalhar com e resolução de problemas.
[Dispositivo repetição de escrita](active-directory-aadconnect-feature-device-writeback.md) | Descreve como funciona o dispositivo escrita não consolidada no ligação do Azure AD.
[Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md) | Descreve como expandir o esquema do Azure AD com o seus próprio atributos personalizados.
**Serviço de sincronização** |
[Funcionalidades de serviço de sincronização do Azure AD Connect](active-directory-aadconnectsyncservice-features.md) | Descreve lado do serviço de sincronização e como alterar as definições de sincronização no Azure AD.
[Duplicar RDP de atributo](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | Descreve como ativar e utilizar RDP de valores duplicados atributo **userPrincipalName** e **proxyAddresses** .
**Interface de utilizador e operações** |
[Gestor de serviço de sincronização](active-directory-aadconnectsync-service-manager-ui.md) | Descreve o IU do Gestor de serviço de sincronização, incluindo [operações](active-directory-aadconnectsync-service-manager-ui-operations.md), [conexões](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)e separadores de [Pesquisa Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) .
[Tarefas operacionais e considerações](active-directory-aadconnectsync-operations.md) | Descreve preocupações operacionais avançadas, tal como recuperação de falhas.
**Como...** |
[Repor a conta do Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) | Como repor as credenciais da conta de serviço utilizado para ligar a partir de ligação do Azure AD sync ao Azure AD.
**Obter mais informações e referências** |
[Portas](active-directory-aadconnect-ports.md) | Lista as portas que precisa para abrir entre o motor de sincronização e o directórios no local e o Azure AD.
[Atributos sincronizados com o Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Lista todos os atributos que está a ser sincronizados entre no local AD e Azure AD.
[Referência de funções](active-directory-aadconnectsync-functions-reference.md) | Enumera todas as funções disponíveis no aprovisionamento declarativa.

## <a name="additional-resources"></a>Recursos adicionais

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
