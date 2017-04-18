
<properties
    pageTitle="Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS | Microsoft Azure"
    description="Esta é a página do Azure AD ligar-se do Estado de funcionamento que vai discutir como monitorizar o AD DS."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="arluca"/>

# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS
A seguinte documentação é específica acompanhamento Active Directory Domain Services com o Azure AD ligar-se do Estado de funcionamento. As versões suportadas do AD DS são: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

Para mais informações sobre como monitorizar o AD FS com Azure AD ligar-se do Estado de funcionamento, consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS](active-directory-aadconnect-health-adfs.md). Para além disso, para obter informações sobre a ligação do Azure AD (sincronização) com o Azure AD ligar-se do Estado de funcionamento de monitorização consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md).

![Ligação do Estado de funcionamento do Azure AD para o AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Alertas para Azure AD ligar-se o estado de funcionamento para o AD DS
Secção alertas dentro do Azure AD ligar-se do Estado de funcionamento do AD DS, fornece uma lista de alertas de activas e resolvidas, relacionadas com os controladores de domínio. Selecionar um alerta ativo ou resolvido é aberta uma nova pá com informações adicionais, juntamente com os passos de resolução e liga a documentação de apoio. Cada tipo de alerta pode ter uma ou mais ocorrências que correspondem a cada um dos controladores domínio afetados por que o alerta específico. Perto da parte inferior da pá alerta, pode fazer duplo clique um controlador de domínio afetado para abrir uma pá adicional com mais detalhes sobre essa instância de alerta.

Dentro deste pá, pode ativar notificações de correio eletrónico para alertas e alterar o intervalo de tempo na vista. Expandir o intervalo de tempo permite-lhe ver prévias alertas resolvidas.

![Erro de sincronização do Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Dashboard de controladores do domínio
Este dashboard fornece uma vista topológica do seu ambiente, juntamente com métricas operacionais chave e estado de funcionamento de cada um dos controladores de domínio monitorizada. Métricas apresentadas ajudam a identificar rapidamente, quaisquer controladores de domínio que poderão exigir mais inquérito. Por predefinição, é apresentado apenas um subconjunto das colunas. No entanto, pode encontrar todo o conjunto de colunas disponíveis, fazendo duplo clique no comando de colunas. Selecionar as colunas que mais gosta, rotações este dashboard para um único e fácil de gerir colocar para ver o estado de funcionamento do seu ambiente de AD DS.

![Controladores de domínio](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Controladores de domínio podem ser agrupados pela sua domínio respetivos ou site, que é útil para compreender a topologia de ambiente. Por fim, se clicar duas vezes no cabeçalho da pá, o dashboard maximiza para utilizar o imobiliário ecrã disponíveis. Esta vista maior é útil quando várias colunas são apresentadas.

## <a name="replication-status-dashboard"></a>Dashboard do Estado de replicação
Este dashboard fornece uma vista do Estado de replicação e topologia de replicação dos controladores de domínio monitorizada. O estado da tentativa de replicação mais recente está listado, juntamente com documentação úteis para qualquer erro que se encontra. Pode fazer duplo clique num controlador de domínio com um erro, para abrir um novo pá com informações tais como: detalhes sobre o erro, recomenda-se os passos de resolução e ligações aos documentação de resolução de problemas.

![Estado de replicação](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Monitorização
Esta funcionalidade fornece tendências de gráficas diferentes de contadores de desempenho, que são continuamente cobrados de cada uma dos controladores de domínio monitorizada. Desempenho do controlador de domínio facilmente pode ser comparado ao longo de todos os outros controladores de domínio monitorizada na sua floresta. Para além disso, pode ver vários contadores de desempenho lado a lado, que é útil quando resolução de problemas no seu ambiente.

![Monitorização](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Por predefinição, podemos ter previamente quatro contadores de desempenho; No entanto, pode incluir as outras pessoas ao clicar no comando de filtro e selecionar ou desmarcar quaisquer contadores de desempenho pretendido. Para além disso, pode fazer duplo clique num gráfico de contador de desempenho para abrir um novo pá, que inclui os pontos de dados de cada um dos controladores monitorizada domínio.

## <a name="related-links"></a>Ligações relacionadas

* [Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health.md)
* [Ligação do Azure AD instalação do agente de estado de funcionamento](active-directory-aadconnect-health-agent-install.md)
* [Operações de estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md)
* [Perguntas mais frequentes do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-faq.md)
* [Histórico de versões do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-version-history.md)
