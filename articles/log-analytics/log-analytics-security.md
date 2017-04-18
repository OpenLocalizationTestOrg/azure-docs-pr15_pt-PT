<properties
    pageTitle="Iniciar sessão de segurança dos dados de análise | Microsoft Azure"
    description="Saiba mais sobre como registo protege a sua privacidade e de análise protege os seus dados."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="banders"/>

# <a name="log-analytics-data-security"></a>Iniciar sessão de segurança dos dados de análise

A Microsoft está empenhada em proteger a sua privacidade e proteger os seus dados, enquanto faz software e serviços que ajudam a gerir a infraestrutura de TI da sua organização. Vamos reconhecer quando confiar dos seus dados para outras pessoas, dessa fidedignidade requer estritas segurança. Microsoft cumpre as diretrizes segurança e conformidade estritamente — a partir de codificação de funcionamento de um serviço.

Proteger e proteger dados são uma prioridade superior na Microsoft. Contacte-nos com qualquer perguntas, sugestões ou questões sobre qualquer uma das seguintes informações, incluindo os nossos políticas de segurança no [Azure opções de suporte](http://azure.microsoft.com/support/options/).

Este artigo explica como dados são recolhidos, processados e protegidos pela análise de registo no conjunto de aplicações de gestão de operações (OMS). Pode utilizar agentes para ligar ao serviço web, utilize o Gestor de operações do Centro de sistema para recolher dados operacionais ou obter dados a partir do Azure diagnósticos utilizado pelo registo de análise. Os dados recolhidos são enviados através da Internet através do portal autenticação baseada em certificado SSL 3 para o serviço de análise de registo, que está alojado no Microsoft Azure. Os dados são comprimidos pelo agente antes de ter sido enviada.

O serviço de análise de registo faz a gestão dos seus dados baseado na nuvem em segurança através dos seguintes métodos:

- separação de dados
- retenção de dados
- segurança física
- gestão de incidentes
- conformidade
- certificações normas de segurança


## <a name="data-segregation"></a>Separação de dados

Dados do cliente são mantidos logicamente em separado no cada componente em todo o serviço OMS. Todos os dados está etiquetado por organização. Este etiquetagem persistir durante o ciclo de vida de dados e é aplicada em cada camada do serviço. Cada cliente tem um dedicada BLOBs do Azure que aloja os dados a longo prazo

## <a name="data-retention"></a>Retenção de dados

Os dados de pesquisa do registo indexados são armazenados e mantidos de acordo com o seu plano comparar. Para mais informações, consulte o artigo [Preços de análise de registo](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft elimina os dados do cliente de 30 dias após a área de trabalho OMS estiver fechada. Microsoft também elimina a conta de armazenamento do Azure onde os dados estão localizados. Quando os dados do cliente são removidos, não existem unidades físicas destruídas.

A tabela seguinte lista algumas das soluções disponíveis OMS e exemplos dos tipos de dados que recolherem.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Avaliação de configuração | Dados de configuração, metadados e dados de estado |
| Planeamento de capacidades | Dados de desempenho e metadados |
| Antimalware | Dados de configuração e metadados |
| Avaliação de atualização do sistema | Metadados e dados de estado |
| Gestão de registo | Definidos pelo utilizador registos de eventos, registos de eventos do Windows e/ou os registos do IIS |
| Registo de alterações | Inventário de software e os metadados de serviço do Windows |
| SQL e avaliação do Active Directory | Dados, dados do registo, dados de desempenho e gestão de dinâmicos do SQL Server ver resultados |

A tabela seguinte mostra exemplos dos tipos de dados:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Alerta | Alertar nome, descrição do alerta, BaseManagedEntityId, ID do problema, IsMonitorAlert, RuleId, ResolutionState, prioridade, gravidade, categoria, proprietário, ResolvedBy, TimeRaised, TimeAdded, última modificação, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuração | IDCliente, AgentID, ID da entidade, ManagedTypeID, ManagedTypePropertyID, Valoratual, ChangeDate |
| Evento | IDdoevento, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, número, categoria, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Nota:** Quando terminar a sessão eventos com campos personalizados para o registo de eventos do Windows, OMS recolhe-los. |
| Metadados | BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NomeRede, endereço IP, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, endereço IP, NetbiosDomainName, LogicalProcessors, NomeDNS, DisplayName em grupos, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Desempenho | Nome de objecto, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado | StateChangeEventId, StateId, NewHealthState, OldHealthState, contexto, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, última modificação, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Segurança física

As análises de registo no serviço OMS é assistida por pessoal da Microsoft e todas as atividades são registadas e podem ser auditadas. O serviço é executado completamente no Azure e está em conformidade com os critérios de engenharia comuns Azure. Pode ver os detalhes sobre a segurança física de activos Azure na página 18 da [Descrição geral de segurança do Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Direitos de acesso físico para proteger áreas são alterados dentro de um dia útil para qualquer pessoa que já não tem responsabilidade para o serviço OMS, incluindo transferência e taxas de cessação. Pode obter informações sobre a infraestrutura de física global que utilizamos no [Microsoft centros de dados](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gestão de incidentes

OMS tem um processo de gestão de incidentes adiram todos os serviços Microsoft. Resumindo, podemos:

- Utilizar um modelo de responsabilidade partilhada onde uma parte de responsabilidade de segurança pertence Microsoft enquanto algumas parte pertence ao cliente
- Gerir incidentes de segurança Azure
  - Detetar um incidente da indicação primeiro, para começar a um inquérito
  - Avalie o impacto e gravidade de um incidente de por um membro da equipa de resposta a incidentes na chamada. A avaliação baseada em provas, pode ou não pode resultar em ainda mais a elevação para a equipa de resposta de segurança.
  - Diagnosticar um incidente de especialistas segurança resposta para realizar o inquérito técnico ou forenses, identificar estratégias de retenção, mitigação e solução. Se a equipa de segurança acredita que os dados do cliente podem tornar-se expostos para um indivíduo ilícito ou não autorizado, paralela execução do processo de notificação do incidente de cliente começa em paralelo.  
  - Regularização e recuperar a partir do incidente. A equipa de resposta incidente cria um plano de recuperação para mitigar o problema. Passos de retenção crise como colocar em quarentena sistemas afetados poderão ocorrer imediatamente e em paralelo com diagnóstico. Mais longa atenuações de termos podem planeadas que possa ocorrer depois de ter passado o risco de imediato.  
  - Feche o incidente e realizar post-mortem. A equipa de resposta incidente cria uma post-mortem que contorna os detalhes do incidente, com a intenção de rever as políticas, procedimentos e processos para impedir que uma nova ocorrência do evento.
- Notificar os clientes dos incidentes de segurança
  - Determinar o âmbito de clientes afetados e para fornecer a qualquer pessoa que é afetada como detalhadas um aviso possível
  - Crie um aviso de clientes com detalhadas suficiente para que estes possam efetuar um inquérito no respetivo fim e cumprir os compromissos que tenham efetuado aos seus utilizadores finais enquanto não indevidamente atrasar o processo de notificação.
  - Confirme e declarar incidente, conforme necessário.
  - Notifica os clientes com uma notificação de incidente sem atraso razoável e em conformidade com qualquer compromisso legal ou contratual. Notificação de incidentes de segurança será enviada para um ou mais dos administradores de um cliente por qualquer meio que seleciona Microsoft, incluindo por correio eletrónico.
- Realizar preparação de equipa e formação
  - Pessoal da Microsoft é necessários para completar a segurança e formação de detecção, que ajuda a-los para identificar e comunicar problemas de segurança suspeita.  
  - Operadores de trabalhar no serviço do Microsoft Azure têm obrigações de formação de adição adjacente o seu acesso aos sistemas sensíveis a maiúsculas e dados do cliente de alojamento.
  - Pessoal de resposta de segurança do Microsoft recebe formação especializada para as suas funções


Trabalho perda de dados de qualquer cliente, podemos notificar cada cliente dentro de um dia. No entanto, perda de dados do cliente nunca ocorreu com OMS. Para além disso, podemos manter cópias dos dados que foram criados e geograficamente é distribuído.

Para mais informações sobre como o Microsoft responde a incidentes de segurança, consulte o artigo [Resposta de segurança do Microsoft Azure na nuvem](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Conformidade

Programa de segurança e governação de informações a OMS software development e serviço da equipa suporta as suas necessidades de negócio e adere às leis e regulamentos tal como descrito em [Centro de fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/) e [Conformidade de centro de fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Como OMS estabelece os requisitos de segurança, identifica os controlos de segurança, gere e monitoriza riscos também são descritos aí. Todos os anos, podemos conduzir uma revisão das políticas, padrões, procedimentos e orientações.

Cada membro de equipa de desenvolvimento OMS recebe formação de segurança de aplicação formal. Internamente, utilizamos um sistema de controlo de versão para o desenvolvimento de software. Cada projeto software está protegido pelo sistema de controlo de versão.

A Microsoft tem uma equipa de segurança e conformidade que associação e avalia a todos os serviços no Microsoft. Os responsáveis pela segurança informações compõem a equipa e não estão associadas os departamentos de engenharia desenvolver OMS. Os agentes de segurança tem os seus próprios cadeia de gestão e realizar avaliações independentes de produtos e serviços para se certificar de segurança e conformidade.

Área da Microsoft do Conselho é notificada pelo e programas de relatório anual sobre todos a segurança de informações na Microsoft.

A equipa de desenvolvimento e serviço de software OMS ativamente está a trabalhar com as equipas Microsoft Legal e conformidade e outros parceiros de indústria para adquirir uma variedade de certificações.

## <a name="security-standards-certifications"></a>Certificações normas de segurança

Análise de registo no OMS atualmente satisfazer as seguintes normas de segurança:

- [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) e [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498) em conformidade
- Pagamento cartão norma da indústria (PCI em conformidade) dados segurança (PCI DSS) pelo Conselho de normas de segurança PCI.
- [Tipo de serviço organização controlos (SOC) 1 1 e introduza 1 2 SOC](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) compatíveis
- Critérios de engenharia comuns do Windows
- Certificação computação fidedigna da Microsoft
- Como um serviço Azure, os componentes que utiliza OMS adiram requisitos de conformidade Azure. Pode ler mais em [Conformidade de centro de fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).


## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança de informática em nuvem
O diagrama seguinte mostra uma arquitetura de segurança na nuvem como o fluxo de informações da sua empresa e como são protegido tal como está move o cursor para o serviço de análise de registo, finalmente visto por si no portal do OMS. Obter mais informações sobre cada passo seguem o diagrama.

![Imagem de recolha de dados OMS e segurança](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. inscrever-se para recolher dados e de análise de registo

Para a sua organização enviar dados para a análise de registo, configurar agentes do Windows, agentes executar no Azure máquinas virtuais ou agentes OMS para Linux. Se utilizar o Gestor de operações agentes, em seguida, que irá utilizar um Assistente de configuração na consola de operações configurá-los. Os utilizadores (que poderão ser, outros utilizadores individuais ou um grupo de pessoas) crie um ou mais contas OMS (OMS as áreas de trabalho) e registar agentes utilizando uma das seguintes contas:


- [ID de organizacional](../active-directory/sign-up-organization.md)

- [Conta Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Uma área de trabalho OMS é onde dados são recolhidos, agregados, analisados e apresentados. Uma área de trabalho é principalmente utilizada como uma forma de dados de partição e cada área de trabalho é exclusiva. Por exemplo, poderá pretender ter os seus dados de produção geridos com um OMS da área de trabalho e os dados de teste geridos com outra área de trabalho. Áreas de trabalho também ajudam a um administrador controlar o acesso aos dados. Cada área de trabalho pode ter várias contas de utilizador associadas com o mesmo e, cada conta de utilizador pode aceder a várias OMS da área de trabalho. Criar áreas de trabalho com base na região do Centro de dados. Cada área de trabalho é replicada para outros centros de dados na região, principalmente para disponibilidade do serviço OMS.

Para Gestor de operações, quando concluir o Assistente de configuração, cada grupo de gestão do Gestor de operações estabelece uma ligação com o serviço de análise de registo. Em seguida, utilize o Assistente para computadores adicionar para escolher quais os computadores no grupo de gestão de permitidos para enviar dados para o serviço. Para outros tipos de agente, cada uma liga-se em segurança para o serviço OMS.

Todas as comunicações entre os sistemas ligados e o serviço de análise de registo está encriptada.  O protocolo TLS (HTTPS) é utilizado para encriptação.  O processo Microsoft SDL é seguido para garantir a análise de registo está atualizada com os avanços mais recentes nos protocolos criptografia.

Cada tipo de agente recolhe dados para análise de registo. O tipo de dados que são recolhidos é dependente os tipos de soluções utilizadas. Pode ver um resumo de recolha de dados na [solutions de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md). Para além disso, obter informações mais detalhadas coleção estão disponíveis para a maioria das soluções. Uma solução é um conjunto de vistas predefinidas, consultas de pesquisa do registo, regras de recolha de dados e lógica de processamento. Apenas os administradores podem utilizar a análise de registo para importar uma solução. Depois da solução é importada, este é movido para os servidores de gestão do Gestor de operações (se utilizado) e, em seguida, para quaisquer agentes que escolheu. Mais tarde, os agentes de recolhem os dados.

## <a name="2-send-data-from-agents"></a>2. enviar dados agentes

Registar todos os tipos de agente com uma chave de inscrição e uma ligação segura é estabelecida entre o agente e o serviço de análise de registo utilizando a autenticação baseada em certificados e SSL com porta 443. OMS utiliza um arquivo secreto para gerar e manter as teclas. Chaves privadas são rodadas cada cerca de 90 dias e são armazenadas no Azure e são geridas pelas operações Azure que seguem práticas estritas de regulamentação e conformidade.

Com o Gestor de operações, registar uma área de trabalho com o serviço de análise de registo e uma ligação HTTPS segura é estabelecida entre o servidor de gestão do Gestor de operações.

Para agentes do Windows em execução no Azure máquinas virtuais, uma chave de armazenamento só de leitura é utilizada para ler diagnóstico eventos no Azure tabelas.

Se qualquer agente não conseguir comunicar com o serviço por qualquer motivo, os dados recolhidos estão armazenados localmente numa cache temporária e o servidor de gestão tenta reenviar os dados de cada minutos 8 horas 2. Dados em cache o agente estão protegidos por arquivo de credenciais do sistema operativo. Se o serviço não é possível processar os dados 2 horas depois, os agentes irão fila de espera os dados. Se ficar cheio fila de espera, é iniciado OMS largando tipos de dados, começando com dados de desempenho. O limite da fila agente é uma chave de registo para que possa modificá-lo, se necessário. Dados recolhidos são comprimidos e enviados para o serviço, ignorando bases de dados no local, para que não adiciona qualquer carregamento às mesmas. Depois dos dados recolhidos são enviados, este é removido da cache de.

Conforme descrito acima, os dados a partir do seu agentes são enviados através de SSL para centros de dados do Microsoft Azure. Opcionalmente, pode utilizar ExpressRoute para fornecer segurança adicional para os dados. ExpressRoute é uma forma para ligar directamente ao Azure da sua rede WAN existente, tal como um protocolo com várias etiqueta mudança VPN (MPLS), fornecida por um fornecedor de serviço de rede. Para mais informações, consulte o artigo [ExpressRoute](https://azure.microsoft.com/services/expressroute/).


## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. o serviço de análise de registo recebe e processa dados

O serviço de registo Analytics assegura que os dados a receber está a partir de uma fonte fidedigna por validar os certificados e a integridade dos dados com a autenticação do Azure. Os dados não processados não transformados em seguida, são guardados como um blob de [Armazenamento do Windows Azure](../storage/storage-introduction.md) e não estão encriptados. No entanto, cada BLOBs do Azure armazenamento tem um conjunto de conjunto único de teclas que é acessível apenas para esse utilizador. O tipo de dados que estão armazenados é dependente os tipos de soluções que foram importados e utilizadas para recolher dados. Em seguida, o serviço de análise de registo processa os dados não processados para o blogue do armazenamento Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. utilizar a análise de registo para aceder aos dados

Pode iniciar sessão para a análise de registo no portal do OMS utilizando a conta institucional ou conta Microsoft que configurou anteriormente. Todo o tráfego entre o portal de OMS e a análise de registo no OMS é enviado através de um canal HTTPS seguro. Quando utilizar o portal OMS, é gerado um ID de sessão no cliente do utilizador (web browser) e os dados são armazenados numa local cache até que a sessão é terminada. Quando terminar, a cache é eliminada. Cookies do lado do cliente, que não contiverem informações de identificação pessoal, não são removidos automaticamente. Cookies de sessão são marcados HTTPOnly e são protegidos. Após um período inactivo previamente determinado, a sessão do portal de OMS é terminada.

Utilizando o portal OMS, pode exportar dados para um ficheiro CSV e pode aceder a dados utilizando APIs da pesquisa. Exportar CSV está limitada à 50.000 linhas por exportar e dados de API são restringidos a 5.000 linhas por pesquisa.

## <a name="next-steps"></a>Próximos passos

- [Começar a trabalhar com a análise de registo](log-analytics-get-started.md) para saber mais sobre a análise de registo e obter cima e a ser executado em minutos.
