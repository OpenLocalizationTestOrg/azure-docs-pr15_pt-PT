<properties
   pageTitle="Introdução ao segurança de conjunto de aplicações de gestão de operações e auditoria solução | Microsoft Azure"
   description="Este documento ajuda-o para começar a utilizar com segurança de conjunto de aplicações de gestão de operações e capacidades de soluções de auditoria para monitorizar a sua nuvem híbrido."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="get-started-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>
 
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Introdução ao segurança de conjunto de aplicações de gestão de operações e solução de auditoria
Este documento ajuda-na começar a trabalhar rapidamente com capacidades de soluções de segurança de conjunto de aplicações (OMS) de gestão de operações e de auditoria, ajudando-cada opção.

## <a name="what-is-oms"></a>O que é OMS?
Microsoft operações de gestão de conjunto de aplicações (OMS) é baseado na nuvem IT solução de gestão da Microsoft de que o ajuda a gerir e proteger o seu no local e na nuvem infraestrutura. Para mais informações sobre OMS, leia o artigo [Conjunto de aplicações de gestão de operações](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>Dashboard de segurança OMS e de auditoria

A solução OMS segurança e de auditoria fornece uma vista completa da sua organização postura de segurança IT com consultas de pesquisa incorporada dos problemas que requerem a sua atenção. O dashboard de **segurança e auditoria** é no ecrã principal de tudo relacionados com a segurança no OMS. Fornece alto nível visão o estado de segurança dos seus computadores. Também inclui a capacidade de ver todos os eventos a partir das últimas 24 horas, 7 dias, ou qualquer outro período de tempo personalizado. Para aceder ao dashboard de **segurança e de auditoria** , siga estes passos:

1. No dashboard principal de **Conjunto de aplicações do Microsoft operações gestão** , clique em mosaico **Definições** no lado esquerdo.
2. No pá **Definições** , em **soluções** , clique em **segurança** e de auditoria.
3. Dashboard de **segurança e auditoria** irão aparecer:

    ![Dashboard de segurança OMS e de auditoria](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Se estiver a aceder a este dashboard pela primeira vez e não tiver dispositivos controlados pelo OMS, os mosaicos não serão preenchidos com dados obtidos do agente de. Depois de instalar o agente, pode demorar algum tempo para preencher, por conseguinte, o que vê inicialmente poderão estar em falta alguns dados à medida que ainda estão a carregar para a nuvem.  Neste caso, é normal para ver alguns mosaicos sem corpóreos informações. Leia [computadores com Windows ligar diretamente OMS](https://technet.microsoft.com/library/mt484108.aspx) para obter mais informações sobre como instalar agente OMS numa reunião do sistema do Windows e [computadores de ligar Linux para OMS](https://technet.microsoft.com/library/mt622052.aspx) para obter mais informações sobre como executar esta tarefa num sistema Linux.

> [AZURE.NOTE] O agente irá recolher as informações com base nos atuais eventos que estejam ativados, por exemplo, nome do computador, IP endereço e nome de utilizador. No entanto sem/ficheiros de documentos, nome da base de dados ou dados privados são recolhidos.   

As soluções são uma coleção de regras de aquisição lógica, de visualização e de dados que resolvem os desafios cliente-chave. Segurança e auditoria é uma solução, as outras pessoas podem ser adicionadas em separado. Leia o artigo [Adicionar soluções](https://technet.microsoft.com/library/mt674635.aspx) para obter mais informações sobre como adicionar uma nova solução.

Dashboard de segurança de OMS e auditoria está organizado em quatro categorias principais:

- **Segurança domínios**: nesta área que poderão ainda mais explorar registos de segurança ao longo do tempo, aceder a avaliação de software maligno, atualize a avaliação, segurança da rede, identidade e aceder a informações, computadores com eventos de segurança e rapidamente têm acesso ao dashboard do Centro de segurança do Azure.
- **Problemas dos**: esta opção irá permitir-lhe identificar rapidamente o número de problemas ativos e gravidade destes problemas.
- **DLP (pré-visualização)**: permite-lhe identificar padrões de ataque através de visualizar os alertas de segurança, tal como ocorrem contra os recursos.
- **Análise de ameaça**: permite-lhe identificar padrões de ataque ao visualizar o número total de servidores com o tráfego de IP malicioso saída, o tipo de ameaça malicioso e um mapa que mostra onde estas IPs são provenientes de. 
- **Consultas de segurança comuns**: esta opção fornece uma lista das consultas de segurança mais comuns que pode utilizar para monitorizar o seu ambiente. Quando clica dessas consultas, é aberta a pá de **pesquisa** com os resultados para essa consulta.

> [AZURE.NOTE] Para mais informações sobre como o OMS mantém os seus dados seguro, leia que como OMS protege os seus dados.

## <a name="security-domains"></a>Domínios de segurança

Quando monitorizar recursos, é importante possam aceder rapidamente o estado atual do seu ambiente. No entanto também é importante poder controlar anterior eventos que ocorreram no passado que pode levar a uma melhor compreensão das novidades no seu ambiente em determinadas ponto no tempo. 

> [AZURE.NOTE] retenção de dados é de acordo com a OMS preços plano. Para obter mais informações, visite o [Conjunto de aplicações do Microsoft operações de gestão de](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx) preços de página.

Cenários de inquérito de resposta e forensics incidentes diretamente terão a vantagem dos resultados no mosaico **Registos de segurança ao longo do tempo** .

![Registos de segurança ao longo do tempo](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Quando clicar neste mosaico, será aberto o pá de **pesquisa** , que mostra um resultado de consulta para **Eventos de segurança** (tipo = SecurityEvents) com os dados com base nos últimos sete dias, conforme apresentado abaixo:

![Registos de segurança ao longo do tempo](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

O resultado de pesquisa é dividido em dois painéis: painel do lado esquerdo dá-lhe uma explicação detalhada do número de eventos de segurança que foram encontrados os computadores em que foram detetados estes eventos, o número de contas que foram descobertas nestes computadores e os tipos de atividades. Painel do lado direito fornece-lhe os resultados total e uma vista cronológica dos eventos de segurança com atividade de nome e eventos do computador. Também pode clicar em **Mostrar mais** para ver mais detalhes sobre este evento, tal como os dados do evento, o ID do evento e a origem do evento.

> [AZURE.NOTE] Para mais informações sobre a consulta de pesquisa OMS, leia [OMS Procurar referência](https://technet.microsoft.com/library/mt450427.aspx).

### <a name="antimalware-assessment"></a>Avaliação Antimalware

Esta opção permite-lhe identificar rapidamente computadores com proteção insuficiente e computadores que são comprometidas por uma peça de software maligno. Estado de avaliação de software maligno e ameaças detectadas nos servidores monitorizadas são lidos e, em seguida, os dados são enviados para o serviço OMS na nuvem para processamento. Servidores com detetado ameaças e servidores com protecção insuficiente são apresentados no dashboard de avaliação do software maligno, que é acessível depois de clicar no mosaico **Antimalware avaliação** . 

![avaliação de software maligno](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Tal como faria com qualquer outro mosaico dinâmico disponíveis no Dashboard de OMS, quando clica no mesmo, será aberto o pá de **pesquisa** com o resultado da consulta. Para esta opção, se clicar na opção **Não relatórios** em **Estado de proteção**, terá o resultado de consulta que mostre esta entrada individual que contém o nome do computador e a sua classificação, conforme apresentado abaixo:

![resultado de pesquisa](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [AZURE.NOTE] *classificação* é um grau que lhe dá uma para refletir o estado da protecção (, desative atualizado, etc.) e ameaças que sejam encontram. Está a ter que como um número ajuda a tornar agregações.

Se clicar no nome do computador, terá a vista cronológica do Estado de proteção para este computador. Este é muito útil para cenários na qual é necessário compreender se o antimalware quando foi instalado e algumas ponto que foi removida.   

### <a name="update-assessment"></a>Avaliação de atualização 

Esta opção permite-lhe determinar rapidamente a exposição global para potenciais problemas de segurança e se ou são estas atualizações críticas como para o seu ambiente. Segurança OMS e solução de auditoria fornecem apenas a visualização destas atualizações, os dados reais provêm de [Soluções de atualizações do sistema](https://technet.microsoft.com/library/mt484096.aspx), que é um módulo de diferente OMS. Eis um exemplo das atualizações de:

![actualizações do sistema](./media/oms-security-getting-started/oms-getting-started-fig6.png)

> [AZURE.NOTE] Para mais informações sobre a solução de atualizações, leia [actualizar servidores com a solução actualizações do sistema](https://technet.microsoft.com/library/mt484096.aspx).

### <a name="identity-and-access"></a>Acesso e identidades

Identidade deve ser o plano de controlo para a sua empresa, protegendo a sua identidade deve ser sua prioridade superior. Enquanto no passado foram limites à volta de organizações e essas perímetro foram um dos limites de defesa principais, hoje em dia com mais dados e apps mais mover para a nuvem a identidade torna-se o perímetro novo. 

> [AZURE.NOTE] Atualmente, os dados baseados apenas nos dados de início de sessão de eventos de segurança (evento ID 4624) nos futuros inícios de sessão do Office 365 e dados do Azure AD também serão incluídos.

Através da monitorização de atividades de identidade lhe poderão efetuar ações pro-activos antes de um incidente de leva-o até local ou reactivas ações para parar de uma tentativa de ataque. Dashboard de **identidade e o Access** fornece-lhe uma descrição geral do Estado da sua identidade, incluindo o número de tentativas falhadas de início de sessão, a conta de utilizador que tenham sido utilizado durante a esses tentativas, contas que foram bloqueadas, contas com foram alteradas ou repor a palavra-passe e atualmente o número de contas que iniciou sessão. 

Quando clica no mosaico **identidade e acesso** irá ver o dashboard seguinte:

![acesso e identidades](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

As informações disponíveis neste dashboard imediatamente podem ajudá-lo para identificar uma atividade suspeita potencial. Por exemplo, existem 338 tenta iniciar sessão como **administrador** e 100% destas tentativas falhou. Isto pode ser causado por um ataques relativamente a esta conta. Se clicar nesta conta que irá obter mais informações podem ajudá-lo para determinar o recurso de destino para que este ataque potencial:

![resultados da pesquisa](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

O relatório detalhado fornece informações importantes sobre este evento, incluindo: o computador de destino, o tipo de início de sessão (neste maiúsculas e iniciar sessão na rede), a actividade (este evento 4625 maiúsculas e minúsculas) e uma linha cronológica abrangente de cada tentativa. 

### <a name="computers"></a>Computadores

Este mosaico pode ser utilizado para aceder a todos os computadores que tenham ativamente eventos de segurança. Quando clica neste mosaico irá ver a lista de computadores com eventos de segurança e o número de eventos em cada computador:

![Computadores](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

Pode continuar a seu inquérito, clicando em cada computador e rever os eventos de segurança que tenham sido sinalizados.

### <a name="azure-security-center"></a>Centro de segurança do Azure

Este mosaico que mostra basicamente é um atalho para aceder ao dashboard do Centro de segurança do Azure. Para mais informações sobre esta solução, leia a [Introdução ao centro de segurança do Azure](../security-center/security-center-get-started.md) .

## <a name="notable-issues"></a>Dos problemas

A intenção principal deste grupo de opções consiste em fornecer uma vista rápida dos problemas que tem no seu ambiente por categorizá-los no crítica, aviso e informativo. O mosaico de tipo de problema activo é uma visualização destes problemas, mas não lhe permite explorar mais detalhes sobre os mesmos, para que tem de utilizar a parte inferior neste mosaico que tem o nome do problema (nome), objetos quantos tinha isto acontecer (CONTAGEM) e como crítico é (GRAVIDADE).

![Dos problemas](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Pode ver que estes problemas já foram abrangidos nas diferentes áreas do grupo de **Segurança domínios** , reforça a intenção desta vista: visualizar os problemas mais importantes no seu ambiente a partir de um só local.

## <a name="detections-preview"></a>DLP (pré-visualização)

Está a intenção principal desta opção permitir que IT para identificar rapidamente potenciais ameaças através do seu ambiente de trabalho e gravidade desta ameaça.

![Ameaça Intel](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Esta opção também pode ser utilizada durante um inquérito do incidente de resposta para executar a avaliação e obter mais informações sobre o ataque.

> [AZURE.NOTE] Para obter mais informações sobre como utilizar OMS para incidente de resposta, veja [como tirar partido do Centro de segurança do Azure & conjunto de aplicações do Microsoft operações gestão para uma resposta do incidente](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703).

## <a name="threat-intelligence"></a>Análise de ameaça

A nova secção de informações da empresa ameaça da solução de segurança e auditoria apresenta os padrões de ataque possíveis de várias formas: o número total de servidores com o tráfego de IP malicioso saída, o tipo de ameaça malicioso e um mapa que mostra onde estas IPs são provenientes de. Pode interagir com o mapa e clique em IPs para obter mais informações.

Pioneses amarelos no mapa indicam tráfego de entrada de IPs malicioso. Não é invulgar para servidores que são expostos à internet para ver o tráfego de malicioso de entrada, mas recomendamos que reveja estas tentativas para se certificar de que nenhuma foi efetuada com êxito. Estes indicadores são baseados no IIS registos, WireData e os registos de Firewall do Windows.  

![Ameaça Intel](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Consultas de segurança comuns

A lista de consultas de segurança comuns disponíveis pode ser útil para aceder rapidamente às informações do recurso e personalizá-lo com base nas necessidades do seu ambiente. Estas consultas comuns são:

- Todas as atividades de segurança
- Atividades de segurança no computador "computer01.contoso.com" (substitua com o seu próprio nome de computador)
- Atividades de segurança no computador "computer01.contoso.com" para a conta "Administrador" (substitua com nomes de conta de computador e)
- Inicie sessão na atividade pelo computador
- Contas quem terminadas antimalware Microsoft em qualquer computador
- Computadores onde o processo de antimalware do Microsoft foi terminado
- Computadores onde foi "hash.exe" executadas (substitua com o nome do processo diferente)
- Todos os nomes de processo que foram executados
- Atividade pela conta de início de sessão
- Contas quem remotamente tem sessão iniciada no computador "computer01.contoso.com" (substitua com o seu próprio nome de computador)

## <a name="see-also"></a>Consulte também

Neste documento, foram introduzidas a solução OMS segurança e de auditoria. Para saber mais sobre a segurança OMS, consulte os artigos seguintes:

- [Descrição geral de gestão conjunto de aplicações (OMS) de operações](operations-management-suite-overview.md)
- [Monitorização e a responder a alertas de segurança na segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-responding-alerts.md)
- [Monitorizar recursos na segurança de conjunto de aplicações de gestão de operações e solução de auditoria](oms-security-monitoring-resources.md)
