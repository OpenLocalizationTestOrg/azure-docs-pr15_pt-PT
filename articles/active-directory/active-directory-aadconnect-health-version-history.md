<properties
    pageTitle="Histórico de versões do Estado de funcionamento de ligação do Azure AD"
    description="Este documento descreve as versões do Azure AD ligar-se do Estado de funcionamento e o que tenha sido incluído nessas versões."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-version-release-history"></a>Ligação do Azure AD do Estado de funcionamento: Lançamento histórico de versões

A equipa do Azure Active Directory actualiza regularmente Azure AD ligar-se do Estado de funcionamento com novas funcionalidades. Este artigo lista as funcionalidades que foram disponibilizadas e versões.

## <a name="october-2016"></a>De Outubro de 2016
**Actualização do agente:**
- Azure AD ligar Health agent do AD FS \(versão 2.6.408.0\)
    1. Melhorias na detecção pedidos de autenticação de endereços IP de cliente
    2. Correções de erros relacionados com a alertas
- Agente de estado de funcionamento do AD ligar Azure AD DS (versão 2.6.408.0)
    1. Correções de erros relacionados com alertas.
- Azure agente de estado de funcionamento do AD ligar-se para a sincronização (versão 2.6.353.0) lançada com ligação do Azure AD versão 1.1.281.0
    1. Fornecer os dados necessários para os relatórios de erro de sincronização
    2. Correções de erros relacionados com a alertas

**Novas funcionalidades de pré-visualizar:**
- Ligar-se de relatórios de erro de sincronização do Azure AD

**Novas funcionalidades:**
- Azure AD ligar o estado de funcionamento do AD FS - campo do endereço IP está disponível no relatório sobre os principais 50 utilizadores com palavra-passe incorretas nome de utilizador.

## <a name="july-2016"></a>De Julho de 2016

**Novas funcionalidades de pré-visualizar:**

- [Ligação do azure AD estado de funcionamento para o AD DS](active-directory-aadconnect-health-adds.md).


## <a name="january-2016"></a>Janeiro de 2016


**Actualização do agente:**

- Azure AD ligar Health agent do AD FS (versão 2.6.91.1512)


**Novas funcionalidades:**

- [Ferramenta de conectividade de teste do Azure AD ligar agentes de estado de funcionamento](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## <a name="november-2015"></a>De Novembro de 2015


**Novas funcionalidades:**

- Suporte para [o controlo de acesso baseado em funções](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Novas funcionalidades de pré-visualizar:**

- [Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md).

**Problemas fixos:**

- Correções de erros visto durante a registos de agente de erros.

## <a name="september-2015"></a>De Setembro de 2015

**Novas funcionalidades:**

- Errado relatório de palavra-passe do nome de utilizador do AD FS
- Suporte para configurar não autenticados Proxy HTTP
- Suporte para configurar o agente no Server core
- Melhorias para alertas de AD FS
- Melhorias no agente de estado de funcionamento do ligar Azure AD para conectividade e de dados do AD FS carregar.


**Problemas fixos:**

- Correções de erros na utilização de informações para tipos de erro do AD FS.


## <a name="june-2015"></a>Junho de 2015

**Lançamento inicial do Azure AD ligar-se do Estado de funcionamento do AD FS e Proxy do AD FS.**

**Novas funcionalidades:**

- Alertas para monitorizar os servidores de AD FS e Proxy do AD FS com notificações de correio eletrónico.
- Acesso fácil a topologia de AD FS e padrões em contadores de desempenho do AD FS.
- Tendência no pedidos tokens com êxito nos servidores do AD FS agrupados por aplicações, os métodos de autenticação, pedir etc de localização de rede.
- Tendências de pedido falhado nos servidores do AD FS agrupados por aplicações, etc de tipos de erro.
- Implementação do agente mais simples utilizando credenciais de Administrador Global do Azure AD.  




## <a name="next-steps"></a>Próximos passos
Saiba mais sobre o [Monitor suas no local identidade infraestrutura e sincronização dos serviços na nuvem](active-directory-aadconnect-health.md).
