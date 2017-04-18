<properties
   pageTitle="Execução do Azure Active Directory | Microsoft Azure"
   description="Como implementar uma arquitetura de rede híbrido seguro utilizando o Azure Active Directory."
   services="guidance,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="telmos"/>

# <a name="implementing-azure-active-directory"></a>Execução do Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as melhores práticas para integrar no local Active Directory (AD) domínios e florestas com o Azure Active Directory para fornecer a autenticação de identidade baseado na nuvem.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Pode utilizar os serviços de identidade e diretório, como aqueles fornecida pelos serviços de diretório de AD (AD DS) para autenticar identidades. Estas identidades podem pertencer a utilizadores, computadores, aplicações ou outros recursos que fazem parte de um limite de segurança. Pode alojar o diretório e serviços de identidade no local, mas num cenário de híbrido onde os elementos de uma aplicação do estão localizados no Azure poderá ser mais eficaz para expandir a esta funcionalidade para a nuvem. Esta abordagem pode ajudar a reduzir a latência causada pela enviar autenticação e pedidos de autorização local a partir da nuvem novamente para os serviços de diretório e de identidade em execução no local.

Azure fornece duas soluções para implementar os serviços de diretório e de identidade na nuvem:

1. Pode utilizar o [Azure Active Directory (AAD)] [ azure-active-directory] para criar um domínio do AD na nuvem e ligá-lo para no local domínio do AD. AAD permite-lhe configurar sessão único (SSO) para os utilizadores a executar as aplicações acedidas através da nuvem. AAD utiliza a [ligação do Azure AD] [ azure-ad-connect] em execução no local para criar uma réplica objetos e identidades contidos no repositório no local para a nuvem.

    Também pode implementar AAD sem utilizar num diretório no local. Neste caso, AAD age como a principal fonte de todas as informações de identidade em vez de replicadas num diretório no local que contém os dados.

    Tenha em atenção que o diretório na nuvem é **não** uma extensão de um diretório no local. Em vez disso, é uma cópia que contém os objetos e identidades do mesmo. As alterações efetuadas para estes itens no local são copiadas para a nuvem, mas efetuadas alterações na nuvem **não estão** a ser replicadas novamente para o domínio no local.

    >[AZURE.NOTE] As edições do Azure Active Directory Premium suportam escrita-back das palavras-passe dos utilizadores, permitindo-os seus utilizadores no local efetuar a reposição de palavra-passe na nuvem. Esta é a única informação que AAD sincroniza novamente ao diretório no local. Para obter mais informações sobre as edições de diferentes das AAD e suas funcionalidades, consulte o artigo [Azure Active Directory edições][aad-editions].

2. Pode implementar uma VM com o AD DS como controlador de domínio no Azure, expandir a sua infraestrutura de AD existente (incluindo AD DS e AD FS) a partir do seu centro de dados no local. Esta abordagem é mais comum para cenários onde estão ligados à rede no local e à rede virtual Azure por uma ligação VPN e/ou ExpressRoute. Esta solução também suporta bidirecional replicação, permitindo-lhe efetuar alterações na nuvem e no local, onde quer que seja mais adequado.

Esta arquitetura foca-se a solução 1. Para obter mais informações acerca da solução segunda, consulte o artigo [Prolongamento do Active Directory para Azure][guidance-adds].

Casos de utilização típica para esta arquitectura incluem:

- Fornecer SSO para utilizadores finais executar SaaS e outras aplicações na nuvem, utilizando as mesmas credenciais que especificam para as aplicações no local.

- Publicar aplicações web através da nuvem para fornecer acesso aos utilizadores remotos que pertencem à sua organização no local.

- Implementar capacidades de gestão personalizada para os utilizadores finais, tal como a reposição de palavra-passe e delegar a gestão de grupo. 

    >[AZURE.NOTE] Estas capacidades podem ser controladas pelo administrador através da política de grupo.

- Situações em que a rede no local e o Azure virtual rede alojamento nuvem aplicações não estão ligadas diretamente ao utilizar um túnel VPN ou ExpressRoute circuito.

Note que AAD não fornece todas as funcionalidades do AD. Por exemplo, AAD atualmente apenas trata autenticação de utilizador em vez de autenticação de computador. Algumas aplicações e serviços, como o SQL Server, poderão precisar de autenticação de computador caso em que esta solução não é adequada. Para além disso, AAD poderão não estar adequado para sistemas onde componentes conseguiu migrar entre o limite no-local/nuvem em vez de apenas a ser copiada.

> [AZURE.NOTE] Para obter uma explicação detalhada do funcionamento do Azure Active Directory, veja [Microsoft Active Directory é explicado][aad-explained].

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte realça os componentes importantes nesta arquitectura. Para obter mais informações sobre os elementos de carga de trabalho no Azure, leia o artigo [Executar VMs para uma arquitetura de camadas no Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] Para simplificar, este diagrama apenas mostra as ligações diretamente relacionados com a AAD e não representar redirecionamentos de pedido de browser web ou outro protocolo tráfego relacionado que pode ocorrer como parte do processo de Federação de autenticação e de identidade. Por exemplo, um utilizador (no local ou remota) acede normalmente uma aplicação web através de um browser e do web app pode transparente redirecionar o browser para autenticar o pedido através de AAD. Uma vez autenticado, pode ser transferido o pedido novamente para a aplicação web juntamente com as informações de identidade adequado.

[! [0]][0]

- **Inquilino do azure Active Directory**. Esta é uma instância da AAD criada pela sua organização. -Age como um serviço de diretório simples para as aplicações da nuvem (-mantém os objectos copiados no local AD) e fornece serviços de identidade.

- **Sub-rede camada de web**. Esta sub-rede detém VMs implementam uma aplicação baseada na nuvem personalizada desenvolvida pela sua organização e para que pode agir AAD como um corretor de identidade.

- **No local servidor AD DS**. Organização provável que já tem um serviço de diretório existentes como AD DS. Pode sincronizar muitos dos itens no seu diretório de AD DS (tais como o utilizador e informações de grupo) com AAD, para ativar AAD utilizar estas informações para autenticar identidades.

- **Ligação do azure AD servidor de sincronização**. Este é um computador local que executa o serviço de sincronização de ligação do Azure AD. Instale o serviço utilizando o software de ligação do Azure AD. O serviço de sincronização de ligação do Azure AD sincroniza informações (utilizadores, grupos, contactos, etc.) contidas no no local AD a AAD na nuvem. Por exemplo, pode aprovisionar ou deprovision grupos e utilizadores no local e estas alterações são propagadas para AAD. O serviço de sincronização de ligação do Azure AD transmite informações para o inquilino AAD.

    >[AZURE.NOTE] Por motivos de segurança, palavras-passe do utilizador não são armazenadas diretamente no AAD. Em vez disso, AAD mantém um hash cada palavra-passe. Isto é suficiente para confirmar palavra-passe de um utilizador. Se um utilizador exigir a reposição de palavra-passe, tem de ser executadas no local e o novo hash enviada para AAD. Edições AAD Premium incluem funcionalidades que podem automatizar esta tarefa para permitir que utilizadores para repor a sua própria palavras-passe.

## <a name="recommendations"></a>Recomendações

Esta secção resume recomendações para a implementação AAD da seguinte forma.

- AD Connect
- Segurança

### <a name="azure-ad-connect-sync-service-recommendations"></a>Recomendações de serviço de sincronização do Azure AD Connect

A sincronização está interessada com assegurar que as informações de identidade de utilizadores na nuvem são consistentes com que contidos no local. É o objetivo do serviço de sincronização de ligação do Azure AD manter a este consistência. Os seguintes pontos resumem recomendações para implementar o serviço de sincronização de ligação do Azure AD:

- Antes de implementar a ligação do Azure AD sync, deve determinar os requisitos de sincronização da sua organização (o que sincronizar, a partir do quais domínios e frequência. O artigo [requisitos de sincronização de diretórios determinar] [ aad-sync-requirements] descreve os pontos que deverá tomar em consideração.

- Pode executar o serviço de sincronização de ligação do Azure AD utilizando uma VM ou um computador alojado no local. Dependendo da volatilidade das informações no seu diretório de AD, assim que tiver sido executada a sincronização inicial com AAD a carregar o serviço de sincronização de ligação do Azure AD é provável que ser elevado. Utilizar uma VM permite-lhe mais facilmente dimensionar o servidor (monitor de atividade, tal como descrito na secção [Considerações de monitorização](#monitoring-considerations) para determinar se isto é necessário).

- Se tiver múltiplos domínios no local numa floresta, pode armazenar e sincronizar informações para toda a floresta para um inquilino AAD única (isto é a abordagem recomendada). Filtre informações para identidades que ocorrem em mais do que um domínio para que cada identidade aparece apenas uma vez no AAD em vez de a ser duplicado como este pode levar a inconsistências quando os dados são sincronizados. Esta abordagem requer a execução vários servidores de sincronização de ligação do Azure AD. Para obter mais informações, consulte o cenário AAD vários na secção [Considerações de topologia](#topology-considerations) . 

- Utilize a filtragem para limitar os dados armazenados num AAD para apenas que são necessários. Por exemplo, a sua organização não poderá querer armazenar informações acerca de contas inactivas ou não pessoal no AAD. A filtragem pode ser baseadas em grupos, com base no domínio, com base em or ou com base no atributo e pode combinar filtros para gerar regras mais complexas. Por exemplo, pode selecionar para sincronizar apenas objetos contidos num domínio que tenham um valor específico num atributo selecionado. Para obter informações detalhadas, consulte o artigo [ligação do Azure AD sync: configurar a filtragem][aad-filtering].

- Para implementar elevada disponibilidade para o serviço de sincronização do AD Connect, execute um servidor de transição secundário. Para obter mais informações, consulte a secção [topologia considerações](#topology-considerations) .

### <a name="security-recommendations"></a>Recomendações de segurança

Os itens seguintes resumem as recomendações de segurança principal para a implementação AAD, consoante os requisitos da sua organização:

- Gerir palavras-passe dos utilizadores.

    Se estiver a utilizar uma edição de premium do AAD, pode ativar a palavra-passe escrita voltar a partir do AAD seu diretório no local através da execução de uma instalação personalizada de ligação do Azure AD:

    [! [9]][9]

    Esta funcionalidade permite aos utilizadores, repor palavras-passe os seus próprios a partir do portal do Azure, mas só deve ser ativada após rever a política de segurança de palavra-passe da sua organização. Por exemplo, pode restringir os utilizadores que podem alterar a palavra-passe e pode personalizar a experiência de gestão de palavra-passe. Para obter mais informações, consulte o artigo [Personalizar a gestão de palavras-passe para satisfazer necessidades da sua organização][aad-password-management].

- Manutenção de proteção para as aplicações no local que possa ser acedida externamente.

    Utilize o Proxy de aplicação do Azure AD para fornecer acesso controlado para aplicações da web no local a partir de utilizadores externos através de AAD. Esta abordagem protege suas aplicações, não expor-las diretamente à Internet. Apenas os utilizadores que têm de credenciais válidas no seu diretório Azure que conseguem chegar a suas aplicações. Para obter mais informações, consulte o artigo [Ativar o Proxy de aplicação no portal do Azure][aad-application-proxy].

- Monitorização ativamente AAD de sinais de atividade suspeita.

    Considere utilizar AAD Premium P2 edition, que inclui AAD identidade proteção. Proteção de identidade utiliza algoritmos de aprendizagem máquina ajustável e heurística para detetar anomalias e eventos que podem indicar que uma identidade comprometida de riscos. Por exemplo, pode detectar potencialmente discordantes atividade como irregulares início de sessão no atividades, inícios de sessão a partir de origens desconhecidas ou de endereços IP com atividade suspeito ou inícios de sessão a partir de dispositivos que podem estar infectados. Proteção de identidade utilizar estes dados, gera relatórios e alertas permite-lhe investigar estes eventos de risco e tomar remediação adequada ou ação mitigação. Para obter mais informações, consulte o artigo [Azure Active Directory identidade Protection][aad-identity-protection].

    Também pode utilizar a funcionalidade de elaboração de relatórios do AAD no portal do Azure para monitorizar a suspeitas e outras atividades relacionadas com segurança que ocorram dentro do sistema. AAD pode gerar uma série de relatórios de resumo:

    [! [17]][17]

    Para obter mais informações sobre como utilizar estes relatórios, consulte o artigo [Azure elaboração de relatórios Guia do Active Directory][aad-reporting-guide].

## <a name="topology-considerations"></a>Considerações de topologia

Se são integrar num diretório no local com AAD, configure a ligação do Azure AD para implementar uma topologia que corresponda melhor os requisitos da sua organização. Topologias que suporta a ligação do Azure AD incluem o seguinte:

- **Única floresta, único directório AAD**. Nesta topologia, utilize ligação do Azure AD para sincronizar os objetos e informações de identidade numa ou mais domínios numa única no local floresta com um inquilino AAD único. Esta é a topologia de predefinida implementada pela instalação express de ligação do Azure AD.

    [! [1]][1]

    Não o criar múltiplos servidores de sincronização de ligação do Azure AD para ligar domínios diferentes na mesma floresta no local ao mesmo inquilino AAD, a menos que estiver a executar um servidor de sincronização de ligação do Azure AD no modo de transição (consulte teste servidor abaixo).

- **Múltiplos florestas, único directório AAD**. Utilize esta topologia se tiver mais do que uma floresta de no local. Também pode consolidar os informações de identidade para que cada utilizador exclusivo é representado uma vez no diretório AAD, mesmo se o mesmo utilizador existe mais do que uma floresta. Todos os florestas utilizam o mesmo sincronização servidor da ligação do Azure AD. O servidor de sincronização de ligação do Azure AD não tem de fazer parte de qualquer domínio, mas tem de ser acessível a partir de todos os florestas:

    [! [2]][2]

    Nesta topologia, não utilize separado ligação do Azure AD sincronizar servidores para se ligar a cada floresta no local para um inquilino AAD único. Isto pode resultar em informações de identidade duplicados no AAD se estão presentes na floresta mais do que um utilizadores.

- **Múltiplos florestas, topologias em separado**. Esta abordagem permite-lhe intercalar informações de identidade da florestas separadas um inquilino AAD único. Esta estratégia é útil se está a combinar florestas a partir de diferentes organizações (depois de uma avaliação de aquisições ou aquisição, por exemplo) e as informações de identidade para cada utilizador são mantidas em apenas uma floresta:

    Se estiverem sincronizados GAL em cada floresta, em seguida, um utilizador numa floresta pode estar presente no outro como um contacto. Isto pode ocorrer se, por exemplo, a sua organização tiver implementado o GALSync com o Gestor de identidades do Forefront 2010 ou o Gestor de identidades do Microsoft 2016. Neste cenário, pode especificar que os utilizadores devem ser identificados pelo respetivo atributo de *correio* . Também pode fazer corresponder identidades utilizando os atributos *ObjectSID* e *msExchMasterAccountSID* . Isto é útil se tiver um ou mais florestas de recurso com contas desativadas.

- **Servidor de transição**. Nesta configuração, execute uma segunda instância de servidor de sincronização de ligação do Azure AD em paralelo com o primeiro. Esta estrutura suporta cenários tais como:

    - Disponibilidade de alta.

    - Testar e implementar uma nova configuração do servidor de sincronização a ligação do Azure AD.

    - Introdução a um novo servidor e para desativar uma configuração antiga. 

    Nestes cenários, a segunda instância é executado no *modo de transição*. O servidor registos importados objetos e dados de sincronização na sua base de dados, mas não passa os dados para AAD. Apenas quando desativar o modo de transição é que o servidor comece a escrever dados AAD e também é iniciado o desempenho da palavra-passe escrita-foca para os directórios no local adequado:

    [! [4]][4]

    Para obter mais informações, consulte o artigo [ligação do Azure AD sync: tarefas operacionais e considerações][aad-connect-sync-operational-tasks].

- **Diretórios de vários AAD**. É recomendado que crie um diretório AAD único para uma organização, mas poderá haver situações onde precisa de informações da partição em directórios AAD separados. Neste caso, deverá garantir que cada objeto da floresta no local aparece apenas num diretório AAD, para evitar problemas de escrita anterior sincronização e palavra-passe.

    Para implementar neste cenário, configurar separado ligação do Azure AD sincronizar servidores para cada directório AAD e utilize a filtragem para cada servidor de sincronização de ligação do Azure AD opera sobre um conjunto de objetos mutuamente exclusivos: 

    [! [5]][5]

Para mais informações sobre estes topologias, consulte o artigo [topologias para ligação do Azure AD][aad-topologies].

## <a name="user-sign-in-considerations"></a>Considerações de início de sessão de utilizador

Por predefinição, o serviço AAD assume que os utilizadores iniciam sessão ao fornecer a mesma palavra-passe que utilizam no local e o servidor de sincronização de ligação do Azure AD configura a sincronização de palavras-passe entre o domínio no local e AAD. Para organizações muitos, isto é adequado, mas deverá tomar em consideração políticas existentes e infraestrutura da sua organização. Por exemplo:

- A política de segurança da sua organização poderá proibi-sincronizar hashes de palavra-passe para a nuvem.

- Poderá exigir que os utilizadores experiência totalmente integrada SSO (sem a palavra-passe adicional pede-lhe) quando aceder a recursos de nuvem a partir do domínio associado máquinas na rede da empresa.

- Organização poderá já ter ADFS ou de terceiros Federação fornecedor implementado. Pode configurar AAD para utilizar este infraestrutura para implementar a autenticação e o SSO em vez de através da utilização de informações de palavra-passe mantidas na nuvem.

Para mais informações, consulte o artigo [Azure AD ligar utilizador das opções de][aad-user-sign-in].

## <a name="azure-ad-application-proxy-considerations"></a>Considerações de proxy de aplicação do Azure AD

Utilize o Azure AD para fornecer acesso às aplicações no local.

- Expor aplicações web no local com o proxy de aplicação de conectores gerir ao componente de proxy do Azure AD aplicação. O conector de proxy de aplicação é aberta uma ligação de rede de saída para o proxy de aplicação do Azure AD. Pedidos de utilizadores remotos são encaminhados novamente a partir do AAD através desta ligação às aplicações web. Este mecanismo remove a necessidade de abrir entradas portas na firewall no local, como reduzir a superfície de ataque exposta pela sua organização.

Para obter mais informações, consulte o artigo [aplicações de publicar utilizando o proxy de aplicação do Azure AD][aad-application-proxy].

## <a name="object-synchronization-considerations"></a>Considerações de sincronização do objeto

A configuração predefinida de ligação do Azure AD sincroniza objectos a partir do seu diretório de AD local com base no conjunto de regras especificadas no artigo [ligação do Azure AD sync: Noções sobre a configuração predefinida][aad-connect-sync-default-rules]. Apenas objectos que satisfaçam são sincronizadas estas regras, outros são ignorados. Por exemplo, objetos de utilizador tem de ter um atributo exclusivo *sourceAnchor* e o atributo *accounEnabled* tem de ser preenchido. Objetos de utilizador que não possui um atributo *sAMAccountName* ou que iniciar com o texto *AAD_* ou *MSOL_* não estão sincronizados. Ligação do Azure AD aplica-se a várias regras a objetos de utilizador, contacto, grupo, ForeignSecurityPrincipal e computador. Se precisar de modificar o conjunto predefinido de regras, utilizar o Editor de regras de sincronização instalada com ligação do Azure AD (também documentados no [ligação do Azure AD sync: Noções sobre a configuração predefinida][aad-connect-sync-default-rules]).

Pode definir o seus próprio filtros para limitar os objetos a ser sincronizada ao domínio ou OU. Ou implementar filtragem personalizadas mais complexas, conforme descrito no [ligação do Azure AD sync: configurar a filtragem][aad-filtering].

## <a name="security-considerations"></a>Considerações de segurança

Utilize o controlo de acesso condicional para recusar pedidos de autenticação a partir de origens inesperadas:

- Acionar [Azure Multifator autenticação (MFA)] [ azure-multifactor-authentication] se um utilizador tenta ligar a partir de uma localização não fidedigna (tais como pela Internet) em vez de uma rede de confiança.

- Utilize o tipo de plataforma do dispositivo do utilizador (iOS, Android, Windows Mobile, Windows) para determinar a política de acesso para aplicações e funcionalidades.

- Registar o estado de activado/desactivado dos dispositivos dos utilizadores e incorporar estas informações para os controlos de política de acesso. Por exemplo, se o telefone de um utilizador é roubado ou deve registada como desativada para impedem de ser utilizada para obter acesso.

- Controlar o nível de acesso a um utilizador com base em membros do grupo. Utilizar [as regras de associação dinâmico AAD] [ aad-dynamic-membership-rules] para simplificar a administração de grupo. Para uma breve descrição geral de como isto funciona, consulte o artigo [Introdução às associações dinâmico para grupos][aad-dynamic-memberships].

- Utilize as políticas de risco de acesso condicional com proteção de identidade AAD para fornecer protecção avançada com base em atividades de início de sessão invulgares ou outros eventos.

Para obter mais informações, consulte o artigo [acesso condicional do Azure Active Directory][aad-conditional-access].

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Escalabilidade endereçada ao serviço AAD e a configuração do servidor de sincronização a ligação do Azure AD:

- Para o serviço AAD, não tem de configurar as opções para implementar o escalabilidade. O serviço AAD suporta escalabilidade com base em réplicas. AAD implementa uma única réplica principal, as alças de operações de escrita, e várias réplicas só de leitura secundárias. AAD transparente redireciona escritas tentadas efetuadas contra réplicas secundárias para a réplica principal. AAD fornece consistência eventual. Todas as alterações efectuadas a réplica principal são propagadas para as réplicas secundárias. Tal como a maioria das operações contra AAD são lê em vez de gravações, esta arquitetura escalas bem.

    Para obter mais informações, consulte o artigo [Azure AD: em definições avançadas do nosso directório nuvem geo redundantes, altamente disponível, distribuídos][aad-scalability].

- Para o servidor de sincronização de ligação do Azure AD, deve determinar quantos objetos que provavelmente sincronizar a partir do diretório do seu local. Se tiver menos de 100.000 objetos, pode utilizar o software de SQL Server Express LocalDB predefinido fornecido com ligação do Azure AD. Se tiver um grande número de objetos, deve instalar uma versão de produção do SQL Server e executar uma instalação personalizada a ligação do Azure AD especificar que deve utilizar uma instância do SQL Server existente.

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Tal como acontece com preocupações escalabilidade, disponibilidade abrange o serviço AAD e a configuração de ligação do Azure AD:

- O serviço AAD foi concebido para fornecer elevada disponibilidade. Não existem opções disponibilidade configuráveis pelo utilizador. É distribuído geo e é executada nos dados de várias centros de propagação em todo o mundo, com automatizado activação pós-falha. Se um centro de dados ficar disponível, AAD assegura que os dados de diretórios estão disponíveis para acesso a instância no, pelo menos, dois centros de dados regional dispersos mais.

    >[AZURE.NOTE] SLA para AAD básicas e Premium serviços garantias, pelo menos, 99,9% de disponibilidade. Não existe nenhuma SLA para a camada livre da AAD. Para obter mais informações, consulte o artigo [SLA para o Azure Active Directory][sla-aad].

- Para aumentar a disponibilidade do servidor de sincronização a ligação do Azure AD pode executar uma segunda instância no teste modo, conforme descrito na secção [Considerações de topologia](#topology-considerations) . 

    Para além disso, se não estiver a utilizar a instância do SQL Server Express LocalDB que vem com ligação do Azure AD, deve considerar elevada disponibilidade para SQL Server. Note que a solução de disponibilidade apenas elevada suportada é clusters de SQL. Soluções como espelhando as e sempre no não são suportadas pelo ligação do Azure AD.

    Para obter considerações adicionais sobre como manter a disponibilidade do servidor de sincronização de ligação do Azure AD e como recuperar após uma falha, consulte o artigo [ligação do Azure AD sync: tarefas operacionais e considerações - recuperação de falhas][aad-sync-disaster-recovery].

## <a name="management-considerations"></a>Considerações sobre a gestão

Existem dois aspetos a gerir os AAD:

1. Administrar AAD na nuvem.

2. Manter os servidores de sincronização de ligação do Azure AD.

AAD fornece as seguintes opções de gestão de domínios e directórios na nuvem:

- [Módulo do Azure Active Directory PowerShell][aad-powershell]. Utilize este módulo se precisar de script Azure AD tarefas administrativas comuns, como de gestão de utilizadores, gestão de domínios e para configurar o início de sessão único.

- Azure pá de gestão de AD no portal do Azure. Este pá fornece uma vista interativa gestão de diretório e permite-lhe controlar e configurar a maior parte dos aspetos da AAD.

    [! [10]][10]

Ligação do Azure AD instala as seguintes ferramentas que utilizar para manter os ligação do Azure AD os serviços de sincronização a partir do seu máquinas no local:

- Consola do Microsoft Azure Active Directory ligar. Esta ferramenta permite-lhe modificar a configuração do servidor do Azure AD Sync, personalizar a forma como ocorre a sincronização, ativar ou desativar o modo transição e mudar o início de sessão no modo de utilizador (pode ativar AD FS iniciar sessão com a sua infraestrutura no local).

- O Gestor de serviço de sincronização. Utilize o separador de *operações* nesta ferramenta para gerir o processo de sincronização e detetar se tem ocorrido uma falha qualquer partes do processo. Pode acionar sincronizações manualmente utilizando esta ferramenta. 

    [! [12]][12]

    No separador de *conectores* permite-lhe controlar as ligações para os domínios (no local e na nuvem) para que o motor de sincronização é anexado:

    [! [13]][13]

-  O Editor de regras de sincronização. Utilize esta ferramenta para personalizar a forma na qual os objetos são transformados quando são copiados entre um diretório no local e AAD na nuvem. Esta ferramenta permite-lhe especificar adicionais atributos e objetos para a sincronização e filtros para determinar quais instâncias devem ou não devem ser sincronizados.

    Para obter mais informações, consulte a secção do Editor de regras de sincronização no documento [ligação do Azure AD sync: Noções sobre a configuração predefinida][aad-connect-sync-default-rules].

A página [ligação do Azure AD sync: melhores práticas para alterar a configuração predefinida] [ aad-sync-best-practices] contém informações adicionais e sugestões para gerir a ligação do Azure AD.

## <a name="monitoring-considerations"></a>Considerações de monitorização

Monitorização de estado de funcionamento é executado utilizando uma série de agentes instalados no local:

- Ligação do Azure AD instala um agente que captura informações sobre sincronizações. Utilize o pá Azure Active Directory ligar-se do Estado de funcionamento no portal do Azure para monitorizar o estado de funcionamento e o desempenho da ligação do Azure AD. Para obter mais informações, consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização][aad-health].

- Para monitorizar o estado de funcionamento do AD DS Domínios e directórios a partir do Azure, instale o Azure AD ligar o estado de funcionamento do agente de AD DS num computador dentro do domínio no local. Utilize o pá Azure Active Directory ligar-se do Estado de funcionamento no portal do Azure AD DS do monitor. Para obter mais informações, consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS][aad-health-adds]

- Instale o Azure AD ligar o estado de funcionamento para monitorizar o estado de funcionamento do AD FS em execução no local e utilizar o pá Azure Active Directory ligar-se do Estado de funcionamento no portal do Azure para monitorizar a AD DS agente de AD FS. Para obter mais informações, consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS][aad-health-adfs]

Para mais informações sobre como instalar os agentes de estado de funcionamento do AD ligar e os respetivos requisitos, consulte o artigo [Azure AD ligar o estado de funcionamento do agente de instalação][aad-agent-installation].

## <a name="sample-solution"></a>Solução de amostra

Esta secção documentos os passos para criar uma solução de exemplo que pode utilizar para testar a configuração do AAD. A solução inclui os seguintes elementos:

- Uma aplicação web de camadas, seguindo a estrutura descrita através da [Execução VMs para uma arquitetura de camadas no Azure][implementing-a-multi-tier-architecture-on-Azure].

- Uma máquina de cliente de teste. Recomendamos que utilize outro VM para este computador. A configuração deste VM não é importante, desde que tiver acesso de administrador e pode ligar-se para a aplicação web de camadas.

- Um simulada ambiente no local que contém o seu próprio domínio construído utilizando AD DS.

Os cenários que demonstram a estes passos são:

- Activar acesso para a aplicação de camadas web a ser executada em nuvem para os utilizadores externos, com AAD fornecendo autenticação de palavra-passe.

- Activar acesso para a aplicação de camadas web a ser executada em nuvem para os utilizadores que executam dentro da empresa, com AAD fornecendo autenticação de palavra-passe.

### <a name="prerequisites"></a>Pré-requisitos

Os passos que se seguem partem do pressuposto dos pré-requisitos seguintes:

- Tem uma subscrição existente do Azure na qual pode criar grupos de recursos.

- Que instalou a [Interface de comandos do Azure][azure-cli].

- Ter transferiu e instalou a compilação mais recente. Consulte o artigo [aqui] [ azure-powershell-download] para obter instruções.

- Que instalou uma cópia da [makecert] [ makecert] utility no computador cliente de teste.

- Tiver acesso a um computador de desenvolvimento que tem o Visual Studio instalada.

### <a name="create-the-n-tier-web-application-architecture"></a>Criar a arquitetura de aplicação web de camadas

1. Crie uma pasta denominada `Scripts` que contém uma subpasta denominada `Parameters`.

2. Transferir o [Implementar ReferenceArchitecture.ps1] [ solution-script] script PowerShell para a pasta de Scripts.

3. Na pasta parâmetros, crie outra subpasta denominada Windows.

4. Transferir os seguintes ficheiros para a pasta de parâmetros/Windows:

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. Editar o ficheiro de **Implementar ReferenceArchitecture.ps**1 na pasta Scripts e altere a linha seguinte para especificar o grupo de recursos que deve ser criado ou utilizado para armazenar a VM e recursos criados pelo script:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-ntier-rg"
        ```

6. Edite os seguintes ficheiros na pasta **parâmetros/janela**s e defina a `resourceGroup` valor na `virtualNetworkSettings` secção em cada um destes ficheiros para ser iguais a que especificou no ficheiro de script **ReferenceArchitecture.ps1 implementar** .

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. Abrir uma janela do Azure PowerShell, mover para a pasta de Scripts e execute o seguinte comando:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
        ```

    Substituir **<subscription id>** com o seu ID de subscrição Azure.

    Para **<location>**, especifique uma região do Azure, tal como *eastus* ou *westus*.

8. Quando tiver concluído o script, utilize o portal do Azure para obter o endereço IP público do Balanceador de carga de camadas web (*RT-aad-ntier-web-lb*):

    [! [18]][18]

9. Inicie sessão no seu teste cliente computador (ou VM) e certifique-se de que consegue aceder a camada web utilizando o Internet Explorer para navegar para o endereço IP público do Balanceador de carga de web camada. A página IIS predefinida deverá aparecer.

### <a name="simulate-configuration-of-a-public-web-site"></a>Simular configuração de um web site público

>[AZURE.NOTE] Os passos seguintes simular associar a camada web com a www.contoso.com nome DNS, alterando o ficheiro de anfitriões no computador cliente de teste. Para além disso, os VMs camada web estão configurados com certificados autoassinados e FALSO autoridade de alojamento. Destina-se para testar apenas efeitos e **que não deve utilizar estes técnicas num ambiente de produção**.

1. No seu computador do cliente de teste, edite o ficheiro **C:\Windows\System32\drivers\etc\hosts** utilizando o bloco de notas e adicionar uma entrada que associa www.contoso.com o nome com o endereço IP público do Balanceador de carga de camadas web:

        ```text
        # Copyright (c) 1993-2009 Microsoft Corp.
        #
        # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
        #
        # This file contains the mappings of IP addresses to host names. Each
        # entry should be kept on an individual line. The IP address should
        # be placed in the first column followed by the corresponding host name.
        # The IP address and the host name should be separated by at least one
        # space.
        #
        # Additionally, comments (such as these) may be inserted on individual
        # lines or following the machine name denoted by a '#' symbol.
        #
        # For example:
        #
        #      102.54.94.97     rhino.acme.com          # source server
        #       38.25.63.10     x.acme.com              # x client host
        
        # localhost name resolution is handled within DNS itself.
        #   127.0.0.1       localhost
        #   ::1             localhost
        
        52.165.38.64    www.contoso.com
        ```

2. Certifique-se de que agora pode navegar para www.contoso.com a partir do computador cliente de teste. A mesma página IIS predefinido deverão aparecer como anteriormente.

3. No computador do cliente de teste, abra uma linha de comandos como administrador e utilize o utilitário makecert para c
4. criar um certificado de autoridade de certificação de raiz falso:

        ```
        makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
        ```

    Certifique-se de que são criados os seguintes ficheiros:

    - MyFakeRootCertificateAuthority.cer

    - MyFakeRootCertificateAuthority.pvk

4. Execute o seguinte comando para instalar a autoridade de certificação de raiz de teste:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Utilize a autoridade de certificação de teste para gerar um certificado para www.contoso.com:

        ```
        makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
        ```

6. Executar o comando **mmc** e adicionar snap-in certificados para a conta de computador para o computador local.

7. No */Certificates (computador Local) / pessoal/certificado/* armazenar, exporte o certificado de www.contoso.com com a sua chave privada num ficheiro denominado www.contoso.com.pfx:

    [! [20]][20]

8. Regresse ao portal do Azure e ligue-se para a camada de gestão VM (*RT-aad-ntier-gestão-vm1*). O nome de utilizador predefinido é *utilizadorteste* com palavra-passe *AweS0me@PW*:

    [! [21]][21]
    
9. A partir da camada de gestão de VM, ligue a cada da camada web VMs sucessivamente com o nome de utilizador *utilizadorteste* e palavra-passe *AweS0me@PW*e executar as seguintes tarefas. Tenha em atenção que a VMs tem privado endereços IP 10.0.1.4, 10.0.1.5 e 10.0.1.6:

    >[AZURE.NOTE] A camada web VMs usufruir endereços IP privados. Apenas pode ligar aos mesmos, utilizando a camada de gestão VM.

    1. Copie os ficheiros *www.contoso.com.pfx* e *MyFakeRootCertificateAuthority.cer* a partir do computador cliente de teste.
    
    2. Abra uma linha de comandos como administrador, mover para a pasta mantém os ficheiros que copiou e execute os seguintes comandos:
    
        ```
        certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

    3. Executar o `mmc` comando, adicionar snap-in certificados para a conta de computador para o computador local e certifique-se de que foram instalados certificados que se seguem:

        - \Certificates (computador local) \Personal\Certificates\ www.contoso.com

        - Authorities\Certificates\MyFakeRootCertificateAuthority de certificação de raiz de \Trusted \Certificates (computador local)

    4. Inicie a consola do Gestor de serviços de informação Internet (IIS) e navegue até ao *Web Site da Sites\Web* no computador.

    5. No painel de *ações* , clique em *enlaces*e adicionar uma ligação https utilizando o certificado SSL de www.contoso.com:

        [! [22]][22]

10. Regresse ao computador cliente de teste e certifique-se de que o, agora pode navegar para https://www.contoso.com.

### <a name="create-an-azure-active-directory-tenant"></a>Criar um inquilino do Azure Active Directory

1. Utilizar o portal do Azure, criar um inquilino do Azure Active Directory como *myaadname*. onmicrosoft.com, onde *myaadname* é um nome de diretório seleccionado por si.

2. Adicione um utilizador com o nome *admin* com a função GlobalAdmin ao diretório. Anote a palavra-passe gerada recentemente.

3. Utilizar o Internet Explorer, navegue para https://account.activedirectory.windowsazure.com/ e inicie sessão como admin@ *myaadname*. onmicrosoft.com. Altere a palavra-passe quando lhe for pedido.

### <a name="create-and-deploy-a-test-web-application"></a>Criar e implementar uma aplicação web de teste

1. Utilizando o Visual Studio no computador desenvolvimento, criar uma aplicação Web do ASP.NET denominada ContosoWebApp1 (utilize o .NET Framework 4.5.2):

    [! [23]][23]

2. Selecione o modelo *MVC* , alterar a autenticação para *contas de trabalho e de escola*e especifique o nome do seu inquilino AAD. Não crie uma aplicação de serviço na nuvem.

    [! [24]][24]

3. Criar e executar a aplicação para testar a autenticação. Na página de início de sessão no introduza o nome da conta admin@ *myaadname*. onmicrosoft.com, fornecer a palavra-passe e, em seguida, clique em *Iniciar sessão*:

    [! [25]][25]

4. Verifique se AAD pede permissão para iniciar sessão e leia o seu perfil e, em seguida, inicia a executar a aplicação web com a identidade do administrador.

5. Feche o Internet Explorer e regressar ao Visual Studio.

6. Abrir o ficheiro da Web. config e, na `<appSettings>` secção, altere o valor da chave de *ida: PostLogoutRedirectUri* para *https://www.contoso.com:443 /*. Guarde o ficheiro.

7. Na janela do *Explorer solução* , com o botão direito do projecto ContosoWebApp1, clique em *Publicar*.

8. Na janela de *Publicar Web* , clique em *personalizado*. Crie um perfil personalizado com o nome *ContosoWebApp1*.

9. Na página da *ligação* , defina o *método de publicar* para *Sistema de ficheiros* e especifique uma pasta denominada *ContosoWebApp*, numa localização conveniente no seu computador de desenvolvimento.

10. Na página *Definições* , defina a *configuração* para *edição*.

11. Na página de *pré-visualização* , clique em *Publicar*.

12. Ligar a cada servidor web sucessivamente (através de gestão de camadas VM) e efetuar as seguintes tarefas:

    1. Copie a pasta *ContosoWebApp* e os seus conteúdos a partir do computador de desenvolvimento para a pasta *C:\inetpub* .

    2. Utilizar a consola do Gestor de serviços de informação Internet (IIS), navegue até ao *Web Site da Sites\Web* no computador.

    3. No painel de *ações* , clique em *Definições de base*e altere o caminho físico do web site para *%SystemDrive%\inetpub\ContosoWebApp*:

        [! [26]][26]

### <a name="publish-the-test-web-application-through-aad"></a>Publicar a aplicação web do teste através de AAD

1. Iniciar sessão no portal do Azure e navegue para o seu diretório AAD.

2. No separador *aplicações* , clique na aplicação ContosoWebApp1.

3. Certifique-se de que a aplicação com êxito é adicionada ao diretório e, em seguida, clique no separador *Configurar* .

4. Alterar o *URL de início de sessão Sucessivamente* para https://www.contoso.com:443 e configurar o *URL de resposta* para https://www.contoso.com:443 (o mesmo URL).

5. Guarde a configuração.

6. No computador do cliente de teste, navegue para https://www.contoso.com. Certifique-se de que AAD pede-lhe as suas credenciais e, em seguida, inicie sessão no.

>[AZURE.NOTE] Este é o ponto final para o primeiro cenário: Ativar o acesso a aplicação web camadas executar na nuvem para os utilizadores externos, com AAD fornecendo autenticação de palavra-passe.

### <a name="create-a-simulated-on-premises-environment-with-active-directory"></a>Criar um ambiente do simulada no local com o Active Directory

O ambiente no local contém dois controladores de domínio para o `contoso.com` domínio e duas servidores para que aloja o ligação do Azure AD sincronizar serviço. Os servidores de alojamento de ligação do Azure AD não são domínio-associadas.

1. No Explorador de ficheiros, voltar para a pasta de Scripts que contém o script utilizado para criar a aplicação web de camadas.

2. Na pasta parâmetros, adicione outra pasta de sub denominada `Onpremise`.

3. Transferir os seguintes ficheiros para a pasta de parâmetros/Onpremise:

    - [Adicionar-adiciona-domínio-controller.parameters.json][add-adds-domain-controller-parameters]

    - [Criar-adiciona-floresta-extension.parameters.json][create-adds-forest-extension-parameters]

    - [virtualMachines adc.parameters.json][virtualMachines-adc-parameters]

    - [virtualMachines-adc-joindomain.parameters.json][virtualMachines-adc-joindomain-parameters]

    - [virtualMachines adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [dns.parameters.json virtualNetwork adiciona][virtualNetwork-adds-dns-parameters]

5. Editar o ficheiro de implementar ReferenceArchitecture.ps1 na pasta Scripts e altere a linha seguinte para especificar o grupo de recursos que deve ser criado ou utilizado para armazenar a VM e recursos criados pelo script:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-onpremise-rg"
        ```

6. Edite os seguintes ficheiros na pasta parâmetros/Onpremise e defina a `resourceGroup` valor na `virtualNetworkSettings` secção em cada um destes ficheiros para ser iguais a que especificou no ficheiro de script ReferenceArchitecture.ps1 implementar.

    - virtualMachines adds.parameters.json

    - virtualMachines adc.parameters.json

    - virtualNetwork.parameters.json

    - dns.parameters.json virtualNetwork adiciona

7. Abrir uma janela do Azure PowerShell, mover para a pasta de Scripts e execute o seguinte comando:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
        ```

    Substituir `<subscription id>` com o seu ID de subscrição Azure.

    Para `<location>`, especifique uma região Azure, tal como `eastus` ou `westus`.

### <a name="install-and-configure-the-azure-ad-connect-sync-service"></a>Instalar e configurar o serviço de sincronização de ligação do Azure AD

A configuração ilustrada nestes passos consiste em duas instâncias do serviço de sincronização do Azure AD Connect. O primeiro está ativo, enquanto o segundo é configurado no modo de transição para fornecer activação rápida e elevada disponibilidade, se o primeiro servidor falhar.

1. Utilizar o portal do Azure, navegue para o grupo de recursos, mantendo a tecla VMs para os serviços de sincronização de ligação do Azure AD (*RT-aad onpremise rg* por predefinição) e estabelecer ligação com *RT-aad-onpremise-adc-vm1* VM. Inicie sessão como *utilizadorteste* com palavra-passe *AweS0me@PW*.

2. Transferir a ligação do Azure AD partir [aqui][aad-connect-download].

3. Execute o installer de ligação do Azure AD e efetuar a instalação através da opção de *Personalizar* .

    >[AZURE.NOTE] Não pode utilizar a opção *Definições rápidas* , porque a VM que aloja o serviço de sincronização de ligação do Azure AD não está associado ao domínio.

4. Na página *instalar componentes necessários* , deixe as definições de configuração opcional em branco para aceitar as opções predefinidas.

5. Na página de *início de sessão no utilizador* , selecione a *Sincronização de palavra-passe*.

6. Na página *ligar ao Azure AD* , introduza admin@ *myaadname*. onmicrosoft.com, onde *myaadname* é o nome do seu inquilino AAD. Introduza a palavra-passe da conta de administrador.

7. Na página *ligar o seu diretórios* , especifique contoso.com para a floresta (escreva o valor na porque-não aparece na lista pendente), introduza CONTOSO\testuser para o nome de utilizador, especifique AweS0me@PW a palavra-passe e, em seguida, clique em *Adicionar no diretório*.

8. Na página *configuração de início de sessão no Azure AD* , aceite o valor predefinido para nome de utilizador principal. Selecione *continuar sem qualquer domínios verificados*.

    >[AZURE.NOTE] O diretório de contoso.com estão listados como *Não verificados*. Para verificar se um nome de domínio, terá de criar um registo TXT para a sua entidade de registo de nome de domínio. Neste exemplo, o domínio no local não está registado externamente. Para mais informações, consulte o artigo [Adicionar um nome de domínio personalizado para o Azure Active Directory][aad-custom-directory].

9. Na página de *filtragem de domínio e or* , selecione *sincronizar todos os domínios e ou* (a predefinição).

10. Na página *identificar exclusivamente os seus utilizadores* , aceite os valores predefinidos.

11. Na página *utilizadores e dispositivos de filtro* , selecione *sincronizar todos os utilizadores e dispositivos* (a predefinição).

12. Na página *funcionalidades opcionais* , selecione a *palavra-passe escrita não consolidada*.

13. Na página *pronto para configurar* , selecione *Iniciar o processo de sincronização quando concluir a configuração*, mas deixar a *Ativar o modo de transição* desmarcada.

14. Certifique-se de que o processo de configuração conclui sem erros e, em seguida, sair o programa de instalação.

15. A partir do portal do Azure, ligue-se ao *ar-aad-onpremise-adc-vm2* VM. Inicie sessão como *utilizadorteste* com palavra-passe *AweS0me@PW*.

16. Transfira a ligação do Azure AD e, em seguida, execute o installer.

17. O assistente e responder, tal como descrito nos passos 3 a 12.

18. Na página *pronto para configurar* , selecione *Iniciar o processo de sincronização quando concluir a configuração*e **também selecionar** *Ativar o modo de transição*. Isto faz com que o serviço de sincronização de ligação do Azure AD obter mais informações sobre contas e objetos do domínio contoso.com e armazená-los na sua base de dados, mas este não transmitir estes detalhes ao seu inquilino AAD.

14. Certifique-se de que o processo de configuração conclui sem erros e, em seguida, sair o programa de instalação.

### <a name="test-the-aad-configuration"></a>Teste a configuração de AAD

1. Através do portal Azure, mude para o seu diretório AAD, abra o pá Azure Active Directory, clique em *utilizadores e grupos*e, em seguida, clique em *todos os utilizadores* para apresentar a lista de utilizadores e grupos sincronizados com o diretório. Deverá visualizar os utilizadores para as contas seguintes:

    - administração (admin@ *myaadname*. onmicrosoft.com)

    - Conta de serviço de sincronização de diretório no local (Sync_ADC1_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

    - Conta de serviço de sincronização de diretório no local (Sync_ADC2_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

2. No portal do Azure, navegue para o grupo de recursos, mantendo a tecla VMs para os controladores de domínio do AD DS (*RT-aad onpremise rg* por predefinição) e estabelecer ligação com *RT-aad-onpremise-ad-vm1* VM. Inicie sessão como *utilizadorteste* com palavra-passe *AweS0me@PW*.

3. Utilizar a consola *utilizadores do Active Directory e computadores* , adicionar um novo utilizador de domínio com o nome constância Tibbot, com o nome de início de sessão dianet@contoso.com. Especifique uma palavra-passe da sua escolha. Tornar o utilizador um membro do grupo de administradores local (isto é apenas, de modo que pode sessão localmente como este utilizador mais tarde - as apenas máquinas no domínio de são CDs).

4. Mudar para *RT-aad-onpremise-adc-vm1* VM, abra uma janela do PowerShell e execute os seguintes comandos:

        ```[powershell]
        Import-Module ADSync
        Start-ADSyncSyncCycle -PolicyType Delta
        ```

    Verifique se o comando devolve *sucesso*.

    >[AZURE.NOTE] Este passo é necessário porque por predefinição, o processo de sincronização está agendado para executar em intervalos de 30 minutos. Estes comandos forçar uma sincronização para ocorrer imediatamente.

5. Regresse ao portal do Azure, mude para o seu diretório AAD, abrir pá o Azure Active Directory, clique em *utilizadores e grupos*e, em seguida, clique em *todos os utilizadores*. Agora deverá ver constância Tibbot (dianet@ *myaadname*. onmicrosoft.com) aparecem na lista de utilizadores.

6. Na pá Azure Active Directory, clique em *Aplicações empresariais*e, em seguida, clique em *todas as aplicações*. Clique na aplicação *ContosoWebApp1* e, em seguida, clique em *utilizadores e grupos*. Na barra de ferramentas, clique em *Adicionar*. Clique em *utilizadores e grupos*e selecione *Constância Tibbot*. Clique em *atribuir*.

7. Utilizar o Internet Explorer, navegue para o site https://account.activedirectory.windowsazure.com. Inicie sessão como dianet@ *myaadname*. onmicrosoft.com com a palavra-passe que especificou anteriormente.

    >[AZURE.NOTE] Não clique no ícone de ContosoWebApp na lista de aplicações. AAD tentará localizar a aplicação web no endereço DNS publicamente listado para www.contoso.com, qual é a diferença entre o endereço do seu Balanceador de carga web camada.

8. Clique no separador *perfil* . Devem ser apresentados os detalhes do utilizador (se tiver especificado qualquer quando foi criada).

    >[AZURE.NOTE] Se clicar em *Alterar palavra-passe*, não são permitidas para desempenhar esta tarefa, tal como esta operação tem de estar ativada por um administrador pela primeira vez. Para obter mais informações, consulte o artigo [Introdução à gestão de palavra-passe][aad-password-management].

### <a name="enable-on-premises-users-to-run-the-application-by-using-authentication-through-aad"></a>Ativar utilizadores no local executar a aplicação utilizando a autenticação através de AAD

1. Regressar ao controlador de domínio *RT-aad-onpremise-ad-vm1* VM.

2. Abra a consola do *Gestor de DNS* e adicionar um novo registo de anfitrião para www.contoso.com. Especifique o endereço IP público do Balanceador de carga de web camada.

3. Copie o ficheiro *MyFakeRootCertificateAuthority.cer* a partir do computador do cliente de teste (que criou esta ficheiros no procedimento [Simulate configuração de um web site público](#simulate-configuration-of-a-public-web-site)
    
4. Abra uma linha de comandos como administrador, mover para a pasta mantém o ficheiro que acabou de copiar e execute o seguinte comando:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Utilizar o Internet Explorer, navegue até ao https://www.contoso.com. Confirme que AAD início de sessão na página para a aplicação web do ContosoWebApp1 aparece.

6. Inicie sessão como dianet@ *myaadname*. onmicrosoft.com. A aplicação deve executar e inicie sessão corretamente.

## <a name="next-steps"></a>Próximos passos

- Aprender as melhores práticas para [Expandir o seu domínio de adiciona no local ao Azure][adds-extend-domain]

- Aprender as melhores práticas para [criar uma floresta de recursos adiciona] [ adds-resource-forest] no Azure

<!-- links -->
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: ../active-directory/active-directory-editions.md
[guidance-adds]: ./guidance-iaas-ra-secure-vnet-ad.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: ../multi-factor-authentication/multi-factor-authentication.md
[aad-conditional-access]: ../active-directory//active-directory-conditional-access.md
[aad-dynamic-membership-rules]: ../active-directory/active-directory-accessmanagement-groups-with-advanced-rules.md
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: ../active-directory/active-directory-aadconnect-user-signin.md
[aad-sync-requirements]: ../active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md
[aad-topologies]: ../active-directory/active-directory-aadconnect-topologies.md
[aad-filtering]: ../active-directory/active-directory-aadconnectsync-configure-filtering.md
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: ../active-directory/active-directory-aadconnectsync-understanding-default-configuration.md
[aad-identity-protection]: ../active-directory/active-directory-identityprotection.md
[aad-password-management]: ../active-directory/active-directory-passwords-customize.md
[aad-application-proxy]: ../active-directory/active-directory-application-proxy-enable.md
[aad-connect-sync-operational-tasks]: ../active-directory/active-directory-aadconnectsync-operations.md#staging-mode
[aad-custom-domain]: ../active-directory/active-directory-add-domain.md
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: ../active-directory/active-directory-aadconnectsync-operations.md#disaster-recovery
[aad-sync-best-practices]: ../active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration.md
[aad-adfs]: ../active-directory/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs
[aad-health]: ../active-directory/active-directory-aadconnect-health-sync.md
[aad-health-adds]: ../active-directory/active-directory-aadconnect-health-adds.md
[aad-health-adfs]: ../active-directory/active-directory-aadconnect-health-adfs.md
[aad-agent-installation]: ../active-directory/active-directory-aadconnect-health-agent-install.md
[aad-reporting-guide]: ../active-directory/active-directory-reporting-guide.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: ../powershell-install-configure.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[virtualMachines-adc-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc.parameters.json
[virtualMachines-adc-joindomain-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc-joindomain.parameters.json
[aad-connect-download]: http://www.microsoft.com/download/details.aspx?id=47594
[aad-custom-directory]: ../active-directory/active-directory-add-domain.md
[aad-password-management]: ../active-directory/active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[0]: ./media/guidance-identity-aad/figure1.png "Arquitetura de identidade nuvem utilizando o Azure Active Directory"
[1]: ./media/guidance-identity-aad/figure2.png "Única floresta, único topologia de diretório AAD"
[2]: ./media/guidance-identity-aad/figure3.png "Múltiplas florestas, topologia de diretório AAD única"
[4]: ./media/guidance-identity-aad/figure5.png "Topologia do servidor de transição"
[5]: ./media/guidance-identity-aad/figure6.png "Topologia de diretórios AAD múltipla"
[6]: ./media/guidance-identity-aad/figure7.png "Selecionar uma instalação personalizada do Azure AD Sync estabelecer ligação com uma instância específica do SQL Server"
[7]: ./media/guidance-identity-aad/figure8.png "Especificar o método de SSO para o início de sessão do utilizador"
[8]: ./media/guidance-identity-aad/figure9.png "Especificar domínio e or opções de filtragem"
[9]: ./media/guidance-identity-aad/figure10.png "Activar a palavra-passe escrita-anterior"
[10]: ./media/guidance-identity-aad/figure11.png "O pá de gestão do Azure AD no portal"
[11]: ./media/guidance-identity-aad/figure12.png "Consola de ligação do Azure AD"
[12]: ./media/guidance-identity-aad/figure13.png "No separador de operações no Gestor de serviço de sincronização"
[13]: ./media/guidance-identity-aad/figure14.png "No separador de conectores no Gestor de serviço de sincronização"
[14]: ./media/guidance-identity-aad/figure15.png "O Editor de regras de sincronização"
[15]: ./media/guidance-identity-aad/figure16.png "Pá o Azure Active Directory ligar-se do Estado de funcionamento no portal do Azure que mostra o estado de funcionamento de sincronização"
[16]: ./media/guidance-identity-aad/figure17.png "Pá o Azure Active Directory ligar-se do Estado de funcionamento no portal do Azure que mostra o estado de funcionamento do AD DS"
[17]: ./media/guidance-identity-aad/figure18.png "Relatórios de segurança disponíveis no portal do Azure"
[18]: ./media/guidance-identity-aad/figure19.png "Portal do Azure realce o endereço IP público do Balanceador de carga de camadas web"
[19]: ./media/guidance-identity-aad/figure20.png "Utilizar o Internet Explorer para navegar para o endereço IP público do Balanceador de carga de camadas web"
[20]: ./media/guidance-identity-aad/figure21.png "Os certificados snap-in a mostrar o certificado de www.contoso.com"
[21]: ./media/guidance-identity-aad/figure22.png "Ligar-se para a camada de gestão VM"
[22]: ./media/guidance-identity-aad/figure23.png "Criar a ligação HTTPS para o web site predefinido"
[23]: ./media/guidance-identity-aad/figure24.png "Criar a aplicação web do ContosoWebApp1"
[24]: ./media/guidance-identity-aad/figure25.png "Definir as propriedades de autenticação da aplicação web ContosoWebApp1"
[25]: ./media/guidance-identity-aad/figure26.png "Iniciar sessão no Azure AAD da aplicação web ContosoWebApp1"
[26]: ./media/guidance-identity-aad/figure27.png "Alterar a pasta para o web site predefinido"