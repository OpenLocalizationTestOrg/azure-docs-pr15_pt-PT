<properties
   pageTitle="Aplicação dependência Monitor (ADM) no conjunto de gestão de operações (OMS) | Microsoft Azure"
   description="Monitor de dependência de aplicação (ADM) é uma solução de gestão de operações conjunto de aplicações (OMS) que automaticamente detetar componentes da aplicação nos sistemas Windows e Linux e mapas a comunicação entre os serviços.  Este artigo fornece detalhes para a implementação ADM no seu ambiente e utilizá-lo numa variedade de cenários."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Utilizar a solução de Monitor de dependência de aplicação no conjunto de aplicações de gestão de operações (OMS)
![Ícone de alerta de gestão](media/operations-management-suite-application-dependency-monitor/icon.png) Dependência de aplicação Monitor (ADM) automaticamente detetar componentes da aplicação nos sistemas Windows e Linux e mapas a comunicação entre os serviços. Permite-lhe ver os seus servidores de à medida que considerá-los, como sistemas interligados que entregar serviços críticos.  Aplicação dependência Monitor mostra ligações entre servidores, processos, e portas através de qualquer arquitectura ligado TCP com nenhuma configuração necessárias além instalação de um agente.

Este artigo descreve os detalhes de utilização do Monitor de dependência de aplicação.  Para obter informações sobre como configurar agentes ADM e ativação, consulte o artigo [configurar o Monitor de dependência de aplicação solução no conjunto de aplicações de gestão de operações (OMS)](operations-management-suite-application-dependency-monitor-configure.md)

>[AZURE.NOTE]Aplicação dependência Monitor atualmente está na pré-visualização privada.  Pode pedir acesso a ADM privado pré-visualização na [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Durante a pré-visualização privada, todas as contas OMS ter acesso ilimitado a ADM.  Nós ADM são gratuitas, mas os dados de análise de registo para tipos de AdmComputer_CL e AdmProcess_CL irão com tráfego limitados, como qualquer outra solução.
>
>Depois de ADM entra pré-visualização do público, será disponível apenas para clientes gratuitos e pagos das conhecimentos aprofundados e a análise no OMS preços plano.  Contas de camada gratuito serão limitadas aos 5 nós ADM.  Se estiver a participar na pré-visualização do privadas e não são inscrito no OMS preços plano quando ADM entra pré-visualização do público, ADM estarão desativado nesse momento. 


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de utilização: Tornar a da empresa processa dependência deverá ter em consideração

### <a name="discovery"></a>Deteção
ADM cria automaticamente um mapa de referência comuns de dependências de nos seus servidores, processos e serviços de terceiros 3º.  Detetar e mapas de todas as dependências TCP, identificar inesperada ligações, remotas 3º sistemas de terceiros que depende dependências de e para as áreas escuras tradicionais da sua rede como o DNS e AD.  ADM Deteta ligações de rede com falha que os sistemas geridos estão a tentar efetuar, para o ajudar a identificar potenciais servidor uma configuração errada, falhas de serviço e problemas de rede.

### <a name="incident-management"></a>Gestão de incidentes
ADM ajuda a eliminar suposições de isolamento de problema ao mostrando-lhe como sistemas estão ligados e afetar entre si.  Para além de ligações falhadas, informações sobre os clientes ligados ajudar a identificar balanceadores de carga não foram corretamente configurados, surpreendente ou excessiva carga serviços críticos e sem controlo os clientes como máquinas programador falar para sistemas de produção.  Fluxos de trabalho integrados com OMS Alterar controlo também permite-lhe ver se um evento de alterar num computador back-end ou serviço explica a causa de raiz de um incidente.

### <a name="migration-assurance"></a>Migração Assurance
ADM permite-lhe planear eficazmente, acelerar e validar migrações Azure, garantindo que não resta nada e não existem sem falhas inesperada.  Pode descobrir todos os sistemas interdependentes que precisam de migrar em conjunto, avaliar a configuração do sistema e a capacidade e identificar se um sistema em execução ainda está a funcionar utilizadores ou é um candidato a para desativar o em vez de migração.  Depois do mover estiver concluída, pode verificar no carga de cliente e de identidade para verificar que os sistemas de ensaio e clientes estiver a estabelecer ligação.  Se as suas definições de planeamento e de firewall sub-rede a ter problemas, ligações falhadas nos mapas ADM irão apontar para os sistemas de que necessitam de conectividade.

### <a name="business-continuity"></a>Continuidade de negócios
Se estiver a utilizar o Azure recuperação de sites e precisa de ajuda que define a sequência de recuperação para o seu ambiente de aplicação, ADM pode automaticamente mostrar-lhe como sistemas dependem entre si para se certificar de que o seu plano de recuperação é fidedigna.  Ao escolher um servidor crítico e ver os seus clientes, é possível identificar os sistemas de front-end que devem ser recuperados apenas depois de que o servidor crítico é restaurado e está disponível.  Por outro lado, verificando dependências de back-end de um servidor crítico, é possível identificar os sistemas que tem de ser recuperados antes do sistema de foco for restaurado.

### <a name="patch-management"></a>Gestão de patches
ADM melhora a utilização da avaliação de atualização do sistema OMS, mostrando-lhe que outros equipas e servidores dependem no seu serviço de, para que o utilizador pode notificá-los com antecedência antes de publicar o seu sistemas para baixo para a aplicação de patches.  Também ADM melhora a gestão de patches no OMS mostrando-lhe se os seus serviços estão disponíveis e correctamente ligadas depois de serem corrigidos e reiniciados. 


## <a name="mapping-overview"></a>Descrição geral de mapeamento
Agentes ADM reunir informações sobre todos os processos ligado TCP no servidor onde estão instalados, bem como para obter mais detalhes sobre as ligações de entrada e saídas de cada processo.  Utilizando a lista de máquina no lado esquerdo da solução ADM, máquinas com agentes de ADM podem ser seleccionadas para visualizar os respetivos dependências ao longo de um intervalo de tempo selecionado.  Dependência de máquina foco num computador específico de mapas e mostrar todos os computadores que estão clientes TCP diretos ou servidores desse computador.

![Descrição geral ADM](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

Máquinas podem ser expandidas no mapa para mostrar os processos em execução com ligações de rede activas durante o intervalo de tempo selecionado.  Quando um computador remoto com um agente ADM é expandido para mostrar detalhes do processo, apenas os processos de comunicar com o computador de foco são apresentados.  A contagem de agentless máquinas front-end, ligar-se para o computador foco é indicada no lado esquerdo dos processos que se ligam a.  Se a máquina de foco está a fazer uma ligação para uma máquina de back-end sem um agente que back-end é representado com um nó no mapa que mostra o endereço de IPv4 e o nó pode ser expandido para mostrar portas individuais e serviços que está a comunicar com o computador de foco.

Por predefinição, mapas ADM mostram os últimos 10 minutos de informações de dependência.  Utilizar os controlos de tempo no canto superior esquerdo, mapas podem ser consultados para intervalos de tempo histórico, até ao nível de uma hora, para mostrar como dependências consultado no passado, por exemplo, durante um incidente ou antes de Ocorreu uma alteração.    Dados ADM são armazenados de 30 dias nas áreas de trabalho pagas e para 7 dias nas áreas de trabalho gratuitos.

![Mapa de máquina com as propriedades de máquina selecionado](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>Ligações falhadas
Ligações falhadas são apresentadas nos mapas do ADM para processos e computadores, com uma Mostrar a linha vermelha tracejadas se um sistema de cliente está a falhar alcançar um processo ou porta.  Ligações falhadas são reportadas a partir de qualquer sistema com um agente ADM implementado se esse sistema for a tentar efectuar a ligação falha.  ADM medidas isto por observar sockets TCP que falharem para estabelecer uma ligação.  Isto pode ser devido a uma firewall, uma configuração errada no cliente do ou server ou um serviço remoto a ser indisponível. 

![Ligações falhadas](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

Noções sobre ligações falhadas ajudá-lo de resolução de problemas, validação de migração, análise de segurança e compreender arquitetura global.  Por vezes, falha ligações são inofensivas, mas muitas vezes apontam diretamente para um problema, tal como um ambiente do activação pós-falha inesperadamente a tornar-se inacessível, … camadas de aplicação ou dois não ser capaz de falar após uma migração de nuvem.  Na imagem acima, IIS e WebSphere são ambos em execução, mas não podem ligar. 

## <a name="computer-and-process-properties"></a>Propriedades do processo e computador
Ao navegar um mapa ADM, pode selecionar máquinas e processos para obter um contexto adicional sobre as suas propriedades.  Máquinas fornecem informações sobre o nome de DNS, endereços IPv4 do, capacidade de memória e CPU, VM tipo, versão do sistema operativo, reinicie o última vez e os IDs dos seus agentes OMS e ADM.

Detalhes do processo são recolhidas a partir do sistema operativo metadados sobre processos em execução, incluindo o nome do processo, descrição do processo, nome de utilizador e domínio (no Windows), nome da empresa, nome do produto, versão do produto, directório de trabalho, linha de comandos e hora de início do processo.

![Propriedades do processo](media/operations-management-suite-application-dependency-monitor/process-properties.png)

O painel de resumo do processo fornece informações adicionais sobre conectividade desse processo, incluindo o respetivo portas vinculadas, ligações de entrada e saídas e falhou ligações. 

![Resumo do processo](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>Integração de monitorização OMS
Integração do ADM com registo de alterações é automática quando ambas as soluções estiver ativadas e configuradas na sua área de trabalho OMS.

O painel de resumo de máquina indica se eventos de registo de alterações ocorreram na máquina selecionada durante o intervalo de tempo selecionado.

![Painel Resumo de máquina](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

O painel de controlo de alteração de máquina mostra uma lista de todas as alterações, com o primeiro mais recente, juntamente com uma ligação para explorar o registo de pesquisa para detalhes adicionais.
![Painel de controlo de alteração de máquina](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

Segue-se uma vista evento de alteração de configuração de desagregação após selecionar **Mostrar na análise de registo**.
![Evento de alteração de configuração](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)


## <a name="log-analytics-records"></a>Análise de registos
Dados de inventário de computador e o processo de ADM estão disponíveis para [pesquisa](../log-analytics/log-analytics-log-searches.md) no registo de análise.  Isto pode ser aplicado a cenários incluindo planeamento de migração, análise da capacidade, deteção e resolução de problemas de desempenho ad hoc. 

É gerado um registo por hora para cada computador exclusivo e processo para além de registos geradas quando que processo ou computador é iniciado ou no-efectuar-se para ADM.  Estes registos tem as propriedades nas seguintes tabelas. 

Existem geradas internamente propriedades que pode utilizar para identificar processos exclusivos e computadores:

- PersistentKey_s exclusivamente é definida pela configuração do processo, por exemplo, linha de comandos e o utilizador ID.  É exclusivo para um determinado computador, mas pode ser repetida nos computadores.
- ProcessId_s e ComputerId_s são globalmente exclusivos no modelo de ADM.



### <a name="admcomputercl-records"></a>AdmComputer_CL registos
Registos com um tipo de **AdmComputer_CL** tem dados de inventário para servidores com agentes de ADM.  Estes registos tem as propriedades da tabela seguinte.  

| Propriedade | Descrição |
|:--|:--|
| Tipo | *AdmComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Nome do computador Windows ou Linux |
| CPUSpeed_d | Velocidade da CPU em MHz |
| DnsNames_s | Lista de todos os nomes DNS para este computador |
| IPv4s_s | Lista de todos os endereços IPv4 utilizado por neste computador |
| IPv6s_s | Lista de todos os endereços do IPv6 utilizado por neste computador.  (ADM identifica os endereços do IPv6, mas não verificou IPv6 dependências.) |
| Is64Bit_b | VERDADEIRO ou FALSO, com base no tipo de SO |
| MachineId_s | Um GUID interno exclusivo ao longo de uma área de trabalho OMS  |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemVersion_s | Cadeia de versão do SO longa |
| TimeGenerated | Data e hora em que foi criado o registo. |
| TotalCPUs_d | Número de núcleos CPU |
| TotalPhysicalMemory_d | Capacidade de memória em MB |
| VirtualMachine_b | TRUE ou false dependendo se o sistema operativo é convidado VM |
| VirtualMachineID_g | ID de VM Hyper-V |
| VirtualMachineName_g | Nome VM Hyper-V |
| VirtualMachineType_s | Hyperv, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |


### <a name="admprocesscl-type-records"></a>Registos de tipo de AdmProcess_CL 
Registos com um tipo de **AdmProcess_CL** tem dados de inventário para ligado TCP processos em servidores com agentes de ADM.  Estes registos tem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Tipo | *AdmProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | Linha de comandos completa do processo |
| CompanyName_s | Nome da empresa (a partir do Windows PE ou rotações/de Linux minuto) |
| Description_s | Descrição do processo longo (a partir do Windows PE ou rotações/de Linux minuto) |
| FileVersion_s | Versão de ficheiro executável (a partir do Windows PE, apenas para o Windows) |
| FirstPid_d | ID do processo de sistema operativo |
| InternalName_s | Nome do ficheiro executável interno (a partir do Windows PE, apenas para o Windows) |
| MachineId_s | GUID interno exclusivo ao longo de uma área de trabalho OMS  |
| Name_s | O nome executável do processo |
| Path_s | Caminho do sistema de ficheiros do executável processo |
| PersistentKey_s | GUID interno exclusivo neste computador |
| PoolId_d | ID interno para agregar processos com base em linhas de comandos semelhantes. |
| ProcessId_s | GUID interno exclusivo ao longo de uma área de trabalho OMS  |
| ProductName_s | Cadeia de nome de produto (a partir do Windows PE ou rotações/de Linux minuto) |
| ProductVersion_s | Cadeia de versão do produto (a partir do Windows PE ou rotações/de Linux minuto) |
| StartTime_t | Hora de início do processo no relógio do computador local |
| TimeGenerated | Data e hora em que foi criado o registo. |
| UserDomain_s | Domínio de proprietário do processo (apenas para o Windows) |
| UserName_s | Nome do proprietário do processo (apenas para o Windows) |
| WorkingDirectory_s | Directório de trabalho do processo |


## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>A capacidade de memória física de todos os computadores geridos da lista. 
Tipo = AdmComputer_CL | Selecione TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![Exemplo de ADM da consulta](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name-dns-ip-and-os-version"></a>Nome do computador de lista, versão DNS, IP e SO.
Tipo = AdmComputer_CL | Selecione ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s | dedup ComputerName_s

![Exemplo de ADM da consulta](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Encontrar todos os processos com "sql" na linha de comandos
Tipo = AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![Exemplo de ADM da consulta](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>Depois de visualizar dados de evento para um dado processo, utilize o seu ID de máquina para obter o nome do computador
Tipo = AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | ComputerName_s distintos

![Exemplo de ADM da consulta](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>Listar todos os computadores com SQL
Tipo = AdmComputer_CL MachineId_s IN {tipo = AdmProcess_CL \*sql\* | MachineId_s distintos} | ComputerName_s distintos

![Exemplo de ADM da consulta](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista de todas as versões de produto exclusiva do Laço no meu Centro de dados
Tipo = AdmProcess_CL Name_s = laço | ProductVersion_s distintos

![Exemplo de ADM da consulta](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computador de todos os computadores que executam CentOS

![Exemplo de ADM da consulta](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)



## <a name="diagnostic-and-usage-data"></a>Diagnóstico e a utilização de dados
Microsoft recolhe automaticamente a utilização e dados de desempenho através da sua utilização do serviço de Monitor de dependência de aplicação. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, a segurança e a integridade do serviço de Monitor de dependência de aplicação. Dados incluem informações sobre a configuração do seu software como o sistema operativo e versão e também incluem o endereço IP, o nome de DNS e o nome de estação de trabalho para poder fornecem capacidades de resolução de problemas precisas e eficientes. Não recolhemos nomes, endereços ou outras informações de contacto.

Para mais informações sobre a recolha de dados e a utilização, consulte a [Declaração de privacidade do Microsoft Online Services](hhttps://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre como [Iniciar sessão pesquisas](../log-analytics/log-analytics-log-searches.md] in Log Analytics to retrieve data collected by Application Dependency Monitor.)
