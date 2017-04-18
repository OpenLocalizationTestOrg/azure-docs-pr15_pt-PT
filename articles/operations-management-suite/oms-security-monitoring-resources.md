<properties
   pageTitle="Monitorizar recursos na segurança de conjunto de aplicações de gestão de operações e auditoria solução | Microsoft Azure"
   description="Este documento ajuda-o a utilizar a segurança OMS e capacidades para monitorizar os recursos e identificar problemas de segurança de auditoria."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Monitorizar recursos in segurança de conjunto de aplicações de gestão de operações e solução de auditoria

Este documento ajuda-na utilizar segurança OMS e capacidades de auditoria para monitorizar os recursos e identificar problemas de segurança.

## <a name="what-is-oms"></a>O que é OMS?

Microsoft operações de gestão de conjunto de aplicações (OMS) é a nuvem da Microsoft com base solução de gestão de TI que o ajuda a gerir e proteger o seu no local e na nuvem infraestrutura. Para mais informações sobre OMS, leia o artigo [Conjunto de aplicações de gestão de operações](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Monitorizar recursos

Sempre que é possível que pretende impedir que incidentes de segurança a ocorrer em primeiro lugar. No entanto, é impossível impedir que todos os incidentes de segurança. Quando ocorrer um incidente de segurança, terá de se certificar de que o seu impacto está minimizado.  Existem três recomendações críticas que podem ser utilizadas para minimizar o número e o impacto das incidentes de segurança:

- Avalie regularmente vulnerabilidades no seu ambiente.
- Verificar periodicamente todos os sistemas de computador e dispositivos de rede para se certificar de que tem todos os patches mais recentes instalados.
- Verifique regularmente todos os registos de início e mecanismos de registo, incluindo os registos dos eventos do sistema operativo, os registos específicos da aplicação e registos de deteção intrusos do sistema.

Segurança OMS e auditoria solução permite IT para monitorizar ativamente todos os recursos que podem ajudar a minimiza o impacto das incidentes de segurança. Segurança OMS e de auditoria tem domínios de segurança que podem ser utilizados para monitorizar recursos. Os domínios de segurança oferece um acesso rápido a uma opções, para os seguintes domínios serão cobertos na mais detalhes de monitorização de segurança:

- Avaliação de software maligno
- Avaliação de atualização
- Acesso e identidades

> [AZURE.NOTE] Para obter uma descrição geral de todas as estas opções, leia a [Introdução à segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-getting-started.md).

### <a name="monitoring-system-protection"></a>Monitorização de proteção de sistema

De defesa abordagem de profundidade, é importante para o estado de segurança geral do seu activo cada camada de proteção. Computadores com ameaças detectados e computadores com proteção insuficiente são apresentados no mosaico avaliação de software maligno em domínios de segurança. Ao utilizar as informações na avaliação de software maligno, é possível identificar um plano para aplicar a proteção aos servidores que precise da mesma. Para aceder a esta opção, siga os passos abaixo:

1. No dashboard principal de **Conjunto de aplicações do Microsoft operações gestão** , clique em mosaico de **segurança e de auditoria** .

    ![Segurança e de auditoria](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. No dashboard de **segurança e de auditoria** , clique em **Avaliação Antimalware** em **Domínios de segurança**. Dashboard de **Avaliação Antimalware** aparece como é mostrado abaixo:

![Avaliação de software maligno](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Pode utilizar o dashboard de **Avaliação de software maligno** para identificar os seguintes problemas de segurança:

- **Ameaças ativas**: computadores que foram comprometidos e têm ameaças ativas no sistema.
- **Remediated ameaças**: computadores que foram comprometidas, mas as ameaças foram remediated.
- **Assinatura desatualizada**: computadores que têm de proteção contra software maligno ativada mas a assinatura é desatualizada.
- **Sem protecção em tempo real**: computadores que não têm antimalware instalado.

### <a name="monitoring-updates"></a>Atualizações de monitorização 

Aplicar as mais recentes atualizações de segurança é aconselhável segurança e devem ser introduzida na sua estratégia de gestão de atualização. Serviço do Microsoft Agent monitorização (HealthService.exe) lê atualizar informações de computadores monitorizadas e, em seguida, envia estas informações atualizadas para o serviço OMS na nuvem para processamento. O serviço do Microsoft Agent monitorização está configurado como um serviço automático e -deve estar sempre em execução no computador de destino.

![atualizações de monitorização](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Lógica é aplicada de atualizar dados e o serviço de nuvem regista os dados. Se forem encontradas atualizações em falta, estes são apresentadas no dashboard de **atualizações** . Pode utilizar o dashboard de **atualizações** para trabalhar com actualizações em falta e desenvolver um plano para aplicam-se aos servidores que precisar deles. Siga os passos abaixo para aceder ao dashboard de **atualizações** :

1. No dashboard principal de **Conjunto de aplicações do Microsoft operações gestão** , clique em mosaico de **segurança e de auditoria** .
2. No dashboard de **segurança e de auditoria** , clique em **Avaliação de atualização** em **Domínios de segurança**. Dashboard de atualização é apresentada como é mostrado abaixo:

![avaliação de atualização](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Neste dashboard pode executar uma avaliação de atualização para compreender o estado atual dos computadores e as ameaças mais importantes de endereço. Ao utilizar o mosaico **críticas ou atualizações de segurança** , os administradores de TI poderão aceder a informações detalhadas sobre as atualizações que estão em falta, conforme apresentado abaixo:

![resultado de pesquisa](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Este relatório incluem informações importantes que podem ser utilizadas para identificar o tipo de ameaça este sistema é vulnerável para, que inclui os artigos Microsoft KB associados a actualização de segurança e o Bulletin MS que tenha mais detalhes sobre a vulnerabilidade.

### <a name="monitoring-identity-and-access"></a>Monitorização de identidade e acesso

Com os utilizadores a trabalhar a partir de qualquer lugar, utilizando os dispositivos e aceder a uma vasta quantidade de aplicações na nuvem e no local, é obrigatório que as respetivas credenciais estão protegidas. Credenciais roubo ataques são aqueles em que um atacante inicialmente obtiver acesso a credenciais um utilizador habitual para aceder a um sistema no interior da rede. Muitas vezes, este ataque inicial é apenas uma forma de obter um acesso à rede, o objetivo ultimate é descobrir contas privilégio. 

Intrusos irão permanecer na rede, utilizando a ferramenta para extrair credenciais de sessões de outras contas de início de sessão iniciada no livre disponível. Dependendo da configuração do sistema, estas credenciais podem ser extraídos sob a forma de hashes, bilhetes ou texto simples par as palavras-passe.  

> [AZURE.NOTE] máquinas diretamente são expostas à Internet verá muitos tentativas falhadas que tente iniciar sessão utilizando a todo o tipo de liste famoso (por exemplo, o administrador). Na maioria dos casos é o liste famoso não é utilizados e a palavra-passe estiver suficientemente forte.

É possível identificar estes intrusos antes de comprometer a uma conta de privilégio. Pode tirar partido **de segurança de OMS e auditoria solução** para monitorizar a identidade e de acesso. Siga os passos abaixo para aceder ao dashboard de **identidade e Access** :

1. No dashboard principal de **Conjunto de aplicações do Microsoft operações gestão** clique em segurança e auditoria dispor em mosaico.
2. No dashboard de **segurança e de auditoria** , clique em **acesso e identidades** em **Domínios de segurança**. Dashboard de **identidade e Access** é apresentada como é mostrado abaixo:

![acesso e identidades](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Como parte da sua estratégia de monitorização normal, tem de incluir a monitorização de identidade. Administrador de TI deverá ter um aspeto se existir um nome de utilizador válido específica que tenha várias tentativas. Isto poderá indicar um dos intruso que adquirido o nome de utilizador real e tente força bruta ou uma ferramenta automática que utiliza codificada palavra-passe que expirou.

Ativar este dashboard IT para identificar rapidamente potenciais ameaças relacionados com a identidade e acesso aos recursos da empresa. É determinado importante também identificar tendências potenciais, por exemplo no mosaico inícios de sessão ao longo do tempo, pode ver ao longo do período de tempo quantas vezes uma tentativa de início de sessão falha foi executada. Neste caso o computador **ServidorFicheiros** recebeu 35 tentativas de início de sessão. Pode explorar mais detalhes sobre neste computador ao clicar no mesmo. 

![obter mais detalhes](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

O relatório gerado para neste computador traz valuable detalhes sobre este padrão. Provavelmente, reparou que a coluna **conta** oferece-lhe a conta de utilizador que foi utilizada para tentar aceder ao sistema, a coluna **TIMEGENERATED** dá-lhe o intervalo de tempo no qual a tentativa de foi concluída e a coluna **LOGONTYPENAME** dá-lhe a localização onde esta tentativa foi feita. Se estas tentativas foram executadas localmente no sistema de por um programa, a coluna **processo** seria ser que mostra nome o processo. Em cenários onde está a chegar a tentativa de início de sessão a partir de um programa, já tem o nome do processo disponível e agora pode executar ainda mais inquérito no sistema de destino.

## <a name="see-also"></a>Consulte também

Neste documento, o que aprendeu como utilizar segurança OMS e solução para monitorizar os recursos de auditoria. Para saber mais sobre a segurança OMS, consulte os artigos seguintes:

- [Descrição geral de gestão conjunto de aplicações (OMS) de operações](operations-management-suite-overview.md)
- [Introdução ao segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-getting-started.md)
- [Monitorização e a responder a alertas de segurança na segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-responding-alerts.md)