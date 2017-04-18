<properties
    pageTitle="Perguntas mais frequentes do Estado de funcionamento de ligação do Azure AD"
    description="Este artigo responde a questões sobre Azure AD ligar-se do Estado de funcionamento. Este artigo abrange perguntas sobre como utilizar o serviço, incluindo a faturação modelo, capacidades, limitações e suporte."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
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


# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) do Estado de funcionamento de ligação do Azure AD

Este artigo responde a questões sobre Azure AD ligar-se do Estado de funcionamento. Este artigo abrange perguntas sobre como utilizar o serviço, incluindo a faturação modelo, capacidades, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais



**P: Posso gerir vários directórios Azure AD. Como posso mudar para um com o Azure Active Directory Premium?**

Pode alternar entre diferentes inquilinos do Azure AD ao selecionar o atualmente com a sessão iniciada no nome de utilizador no canto superior direito e escolher a conta apropriada. Se a conta não se encontra listada aqui, selecione terminar sessão e, em seguida, utilizar as credenciais de administrador global do directório que tem o Azure Active Directory Premium ativada para iniciar sessão.

## <a name="installation-questions"></a>Perguntas sobre a instalação



**P: qual é o impacto de instalar o Azure AD ligar-se do Estado de funcionamento agente em servidores individuais?**

O impacto da instalação do Microsoft Azure AD ligar-se do Estado de funcionamento agentes ADFS servidores Proxy de aplicação Web, servidores de ligação do Azure AD (sycn), os controladores de domínio for mínimo relativamente às CPU, largura de banda de rede de consumo de memória e armazenamento.

Os números abaixo são uma aproximação.

- Consumo de CPU: ~ 1% de aumento
- Consumo de memória: até 10% da memória do sistema total

>[AZURE.NOTE] Trabalho o agente a ser não é possível comunicar aos Azure, o agente de armazena os dados localmente, até um limite máximo definido. O agente substitui os dados "em cache" numa base "menos recentemente servido".

- Armazenamento de memória intermédia local para Azure AD ligar Health Agents: ~ 20 MB
- Para os servidores de AD FS, é recomendável que aprovisionar um espaço em disco de 1024 MB (1 GB) para o AD FS auditoria canal para Azure AD agentes de estado de funcionamento do ligar processar todos os dados de auditoria antes de é substituída.

**P: vai ter os meus servidores de reiniciar durante a instalação do Azure AD ligar Health Agents?**

Não. A instalação dos agentes não irão necessitar que reiniciar o servidor. No entanto, instalação de alguns dos passos pré-requisito poderá ser necessário reiniciar do servidor.

Por exemplo, no Windows Server 2008 R2 a instalação do .net Framework 4,5 requer um reinício de servidor.


**P: é que o AD ligar-se do Estado de funcionamento dos serviços do Azure trabalhar através de um proxy http pass-through?**

Sim.  Para vai operações, pode configurar o agente de estado de funcionamento para reencaminhar pedidos de http saída utilizando um servidor HTTP Proxy. Para obter mais informações, consulte o artigo [Configurar Azure AD ligar-se do Estado de funcionamento agentes para utilizar em HTTP Proxy.](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)
Se precisar de configurar um proxy durante o registo do agente, poderá ter de alterar as suas definições de Proxy do Internet Explorer prévia.
1. Abrir o Internet Explorer -> Definições -> Internet Opções -> ligações -> definições de LAN.
2. Selecione utilizar um servidor Proxy para a sua LAN.
3. Se tiver portas diferentes de proxy para HTTP e HTTPS/seguro, selecione avançadas.

**P: serviços de saúde ligar Azure AD suporta autenticação básica quando se liga aos Proxies de Http?**

Não. Um mecanismo para especificar a palavra-passe arbitrário nome de utilizador para a autenticação básica atualmente não é suportado.


**P: que versão do AD DS são suportadas pelo Azure AD ligar-se do Estado de funcionamento para o AD DS?**

Monitorização do AD DS é suportada quando instalado nas seguintes versões do SO:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

## <a name="operations-questions"></a>Perguntas de operações



**P: é necessário activar a auditoria no meu servidores Proxy do AD FS aplicação ou os meus servidores de Proxy de aplicação Web?**

Não, auditoria não ser necessário nos servidores Proxy de aplicação Web (WAP). Ativá-la em servidores do AD FS.


**P: como alertas de estado de funcionamento do ligar Azure AD obter resolvidas?**

Alertas de estado de funcionamento do ligar Azure AD obter resolvidas numa condição de sucesso. Azure AD ligar Health Agents detetar e comunicar as condições de sucesso ao serviço numa base periódica. Para alguns alertas, supressão é baseado no tempo. Por outras palavras, se a mesma condição de erro não é observada dentro 72 horas a partir do alerta. ª geração, o alerta automaticamente é resolvido.




**P: que as portas da firewall é necessário abrir para o Azure AD ligar-se do Estado de funcionamento agente funcione?**

Tem de ter portas TCP/UDP recomendadas 443 e 5671 aberto para que o Azure AD ligar-se do Estado de funcionamento Agent possam comunicar com os pontos finais de serviço de estado de funcionamento do Azure AD.


**P: por que motivo vejo dois servidores com o mesmo nome no Azure AD ligar o estado de funcionamento do Portal do?**

Quando remover um agente de um servidor, o servidor não é removido automaticamente a partir do Azure AD ligar Portal.  Se manualmente removido um agente de um servidor ou removido o próprio servidor, terá de eliminar a entrada do servidor manualmente a partir do portal do Azure AD ligar-se do Estado de funcionamento. Para obter mais informações, aceda a [Eliminar uma instância de servidor ou serviço.](active-directory-aadconnect-health-operations.md#delete-a-server-or-service-instance)

Se reimaged um servidor ou criado um novo servidor com os mesmos detalhes (tal como o nome do computador) e não removeu já registado servidor a partir do portal do Azure AD ligar-se do Estado de funcionamento, instalado o agente no novo servidor, poderá ver duas entradas com o mesmo nome.  
Neste caso, deverá eliminar a entrada pertencentes ao servidor mais antigo manualmente. Os dados para este servidor devem estar desatualizados.

## <a name="related-links"></a>Ligações relacionadas

* [Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health.md)
* [Ligação do Azure AD instalação do agente de estado de funcionamento](active-directory-aadconnect-health-agent-install.md)
* [Operações de estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS](active-directory-aadconnect-health-adds.md)
* [Histórico de versões do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-version-history.md)
