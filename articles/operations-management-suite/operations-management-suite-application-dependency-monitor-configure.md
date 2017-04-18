<properties
   pageTitle="Configurar o Monitor de dependência de aplicação (ADM) no conjunto de gestão de operações (OMS) | Microsoft Azure"
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

# <a name="configuring-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Configurar a solução de Monitor de dependência de aplicação no conjunto de aplicações de gestão de operações (OMS)
![Ícone de alerta de gestão](media/operations-management-suite-application-dependency-monitor/icon.png) Dependência de aplicação Monitor (ADM) automaticamente detetar componentes da aplicação nos sistemas Windows e Linux e mapas a comunicação entre os serviços. Permite-lhe ver os seus servidores de à medida que considerá-los, como sistemas interligados que entregar serviços críticos.  Aplicação dependência Monitor mostra ligações entre servidores, processos, e portas através de qualquer arquitectura ligado TCP com nenhuma configuração necessárias além instalação de um agente.

Este artigo descreve os detalhes de configurar agentes de Monitor de dependência de aplicação e ativação.  Para informações sobre como utilizar ADM, consulte o artigo [utilizar o Monitor de dependência de aplicação solução no conjunto de aplicações de gestão de operações (OMS)](operations-management-suite-application-dependency-monitor.md)

>[AZURE.NOTE]Aplicação dependência Monitor atualmente está na pré-visualização privada.  Pode pedir acesso a ADM privado pré-visualização na [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Durante a pré-visualização privada, todas as contas OMS ter acesso ilimitado a ADM.  Nós ADM são gratuitas, mas os dados de análise de registo para tipos de AdmComputer_CL e AdmProcess_CL irão com tráfego limitados, como qualquer outra solução.
>
>Depois de ADM entra pré-visualização do público, será disponível apenas para clientes gratuitos e pagos das conhecimentos aprofundados e a análise no OMS preços plano.  Contas de camada gratuito serão limitadas aos 5 nós ADM.  Se estiver a participar na pré-visualização do privadas e não são inscrito no OMS preços plano quando ADM entra pré-visualização do público, ADM estarão desativado nesse momento. 



## <a name="connected-sources"></a>Origens ligadas
A tabela seguinte descreve as origens ligadas que são suportadas pela solução ADM.

| Origem ligada | Suportada | Descrição |
|:--|:--|:--|
| [Agentes do Windows](../log-analytics/log-analytics-windows-agents.md) | Sim | ADM analisa e recolhe dados de computadores de agente do Windows.  <br><br>Note que, para além de agente OMS, agentes do Windows requerem o agente de dependência da Microsoft.  Consulte o artigo os [suportados sistemas operativos](#supported-operating-systems) para uma lista completa das versões do sistema operativo. |
| [Agentes de Linux](../log-analytics/log-analytics-linux-agents.md) | Sim | ADM analisa e recolhe dados de computadores de agente Linux.  <br><br>Tenha em atenção que, para além de agente OMS, Linux agentes requerem o Microsoft Agent de dependência.  Consulte o artigo os [suportados sistemas operativos](#supported-operating-systems) para uma lista completa das versões do sistema operativo. |
| [Grupo de gestão de SCOM](../log-analytics/log-analytics-om-agents.md) | Sim | ADM analisa e recolhe dados a partir do Windows e Linux agentes num grupo de gestão de SCOM ligada. <br><br>É necessária uma ligação direta do computador de agente SCOM para OMS. Dados são enviados diretamente a partir do reencaminhado do grupo de gestão do repositório OMS.|
| [Conta de armazenamento Azure](../log-analytics/log-analytics-azure-storage.md) | N | ADM recolhe dados provenientes de computadores agente, para que não haja dados a partir dos mesmos para recolher a partir do armazenamento Azure. |

Tenha em atenção que ADM suporta apenas plataformas de 64 bits.

No Windows, o Microsoft monitorização agente MMA () é utilizado pelo SCOM e OMS para recolher e enviar monitorizar os dados.  (Este agente denomina o agente de SCOM, OMS agente, MMA ou agente direta, dependendo do contexto.)  SCOM e OMS fornecem diferentes terminar as versões de caixa de MMA, mas estas versões podem cada relatório para SCOM para OMS ou para ambos.  No Linux, o agente de OMS para Linux reúne e envia dados para OMS de monitorização.  Pode utilizar ADM em servidores com agentes direta OMS ou nos servidores que estejam anexados a OMS através de SCOM gestão de grupos.  Para esta documentação, podemos irá referir-se a todos estes agentes – no Linux ou o Windows, quer ligado a uma MG SCOM ou diretamente à OMS – como "OMS agente de", a menos que o nome específico de implementação do agente de é necessária para contexto.

O agente ADM não transmitir quaisquer dados propriamente dito e não requer quaisquer alterações às portas ou firewalls.  Sempre os dados do ADM são transmitidos pelo agente OMS para OMS, seja direta ou através do OMS Gateway.

![Agentes ADM](media/operations-management-suite-application-dependency-monitor/agents.png)

Se for um cliente SCOM com um grupo de gestão de ligado à OMS:

- Se o seu agentes SCOM podem aceder à internet para ligar à OMS, não é configuração adicional necessária.  
- Se o seu agentes SCOM não consegue aceder OMS através da internet, terá de configurar o Gateway OMS para trabalhar com SCOM.
  
Se estiver a utilizar o agente direta OMS, terá de configurar o agente de OMS para ligar a OMS ou ao seu Gateway OMS.  O Gateway OMS podem ser transferido das [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Evitar dados duplicados

Se for um cliente SCOM, não deve configurar o seu agentes SCOM para comunicar diretamente para OMS ou dados serão comunicados duas vezes.  No ADM, isto irá resultar em computadores aparecem duas vezes na lista de máquina.

Configuração do OMS deve ocorrer em apenas uma das seguintes localizações:

- O painel de conjunto de gestão de operações de consola SCOM para computadores geridos
- Configuração de informações operacionais Azure nas propriedades MMA

Utilizar ambas as configurações com a área de trabalho mesmo em cada causará duplicação de dados. Utilizar ambas com diferentes áreas de trabalho pode resultar em configuração em conflito (que solução ADM ativada e o outro sem) que pode impedir que os dados a fluir para ADM completamente.  

Note que mesmo se a máquina propriamente dito não é especificada na configuração de OMS da consola SCOM, se um grupo de instância tal como "Grupo de instâncias do Windows Server" está ativo,-pode ainda resultar na máquina receção OMS configuração do através do SCOM.



## <a name="management-packs"></a>Pacotes de gestão
Quando é activado ADM uma área de trabalho OMS, um 300KB é enviada Management Pack para todos os o Microsoft monitorização agentes nessa área de trabalho.  Se estiver a utilizar agentes SCOM num [grupo de gestão de ligado](../log-analytics/log-analytics-om-agents.md), o pacote de gestão de ADM será implementado a partir do SCOM.  Se os agentes diretamente estiverem ligados, MP será enviado por OMS.

MP é denominado Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Destina-se para *%Programfiles%\Microsoft monitorização Agent\Agent\Health Service State\Management Packs\*.  A origem de dados utilizada pelo pacote de gestão é *% Program files%\Microsoft monitorização Agent\Agent\Health serviço State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configuração
Para além do Windows e Linux computadores têm um agente instalado e ligado ao OMS, o instalador do agente de dependência deve ser transferido a partir da solução ADM e, em seguida, instalado como raiz ou administrador em cada servidor gerida.  Quando o agente ADM estiver instalado num servidor reportar a OMS, mapas de dependência ADM irão aparecer dentro de 10 minutos.  Se tiver problemas, e-mail [oms-adm-support@microsoft.com](mailto:oms-adm-support@microsoft.com).


### <a name="migrating-from-bluestripe-factfinder"></a>Migrar a partir do BlueStripe FactFinder
Monitor de dependência de aplicação irá entregar BlueStripe tecnologia para OMS em fases. FactFinder ainda é suportada para clientes existentes mas já não se encontra disponível para compra individual.  Esta versão de pré-visualização do agente de dependência só pode comunicar com OMS.  Se for um cliente FactFinder atual, identifique um conjunto de servidores de teste para ADM que não são geridos pelo FactFinder. 

### <a name="download-the-dependency-agent"></a>Transfira o agente de dependência
Para além de agente de gestão da Microsoft (MMA) e OMS Linux agente que disponibiliza a ligação entre o computador e OMS, todos os computadores analisados por aplicação dependência Monitor tem de ter o agente de dependência instalado.  No Linux, tem de estar instalado antes do agente de dependência agente do OMS para Linux. 

![Mosaico da aplicação do Monitor de dependência](media/operations-management-suite-application-dependency-monitor/tile.png)

Para transferir o agente de dependência, clique em **Configurar solução** no mosaico **Aplicação dependência Monitor** para abrir o pá **Agente de dependência** .  O pá agente de dependência tem ligações para o Windows e os agentes Linux. Clique na ligação para transferir cada agente adequada. Consulte as secções seguintes para obter detalhes sobre como instalar o agente em diferentes sistemas.

### <a name="install-the-dependency-agent"></a>Instalar o agente de dependência

#### <a name="microsoft-windows"></a>Microsoft Windows
Privilégios de administrador são necessários para instalar ou desinstalar o agente.

O agente de dependência está instalado em computadores com Windows com o agente de ADM-Windows.exe. Se executar este executável sem quaisquer opções, em seguida, irá iniciar um assistente que pode seguir para efetuar a instalação de forma interativa.  

Utilize os passos seguintes para instalar o agente de dependência em cada computador Windows.

1.  Certifique-se de que está instalado o agente OMS utilizando as instruções em computadores ligar diretamente para OMS.
2.  Transfira o agente do Windows e executá-la com o seguinte comando.<br>*Agente de ADM-Windows.exe*
3.  Siga o Assistente para instalar o agente.
4.  Se o agente de dependência falhar começar, verifique os registos para obter informações de erro detalhada. No agentes do Windows, o directório de registo é *C:\Programas\Microsoft Files\BlueStripe\Collector\logs*. 

Agente de dependência para Windows pode ser desinstalado por um administrador através do painel de controlo.


#### <a name="linux"></a>Linux
É necessário acesso de raiz para instalar ou configurar o agente.

O agente de dependência está instalado em computadores de Linux com ADM-Agent-Linux64.bin, um script de shell com um ficheiro binário extracção automática. Pode executar o ficheiro com m ou adicionar permissões para o próprio ficheiro de execução.
 
Utilize os passos seguintes para instalar o agente de dependência em cada computador Linux.

1.  Certifique-se de que está instalado o agente OMS utilizando as instruções fornecidas na [recolher e gerir os dados de computadores Linux.  Isto tem de estar instalado antes do agente de dependência Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Instale o agente de dependência Linux como raiz utilizando o seguinte comando.<br>*m ADM-Agent-Linux64.bin*.
3.  Se o agente de dependência falhar começar, verifique os registos para obter informações de erro detalhada. No Linux agentes, o directório de registo é */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Desinstalar o agente de dependência em Linux
Para desinstalar completamente o agente de dependência de Linux, tem de remover o agente de si próprio e o proxy de qual é instalado automaticamente com o agente de.  Pode desinstalar o ambos com o seguinte comando único.

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>Instalar a partir de uma linha de comandos
A secção anterior fornece orientações sobre como instalar o agente de Monitor de dependência utilizando as opções predefinidas.  As secções abaixo fornecem orientações para instalar o agente a partir de uma linha de comandos utilizando as opções personalizadas.

#### <a name="windows"></a>Windows
Utilize as opções a partir da tabela abaixo para efetuar a instalação a partir de uma linha de comandos. Para ver uma lista da instalação do sinalizadores de executar o instalador com a /? sinalize da seguinte forma.

    ADM-Agent-Windows.exe /?

| Sinalizador | Descrição |
|:--|:--|
| /S | Execute uma instalação silenciosa sem avisos ao utilizador. |

Ficheiros para o Windows dependência Agent são colocados na *C:\Programas\Microsoft Files\BlueStripe\Collector* por predefinição.


#### <a name="linux"></a>Linux
Utilize as opções a partir da tabela abaixo para efetuar a instalação. Para ver uma lista da instalação do sinalizadores executar a instalação de programa com a - ajuda sinalizador da seguinte forma.

    ADM-Agent-Linux64.bin -help

| Descrição do sinalizador
|:--|:--|
| -d | Execute uma instalação silenciosa sem avisos ao utilizador. |
| – verificar | Verifica se as permissões e o sistema operativo, mas não instala o agente. |

Ficheiros do agente de dependência são colocados nos seguintes directórios.

| Ficheiros | Localização |
|:--|:--|
| Ficheiros principais | /usr/lib/bluestripe-collector |
| Ficheiros de registo | /var/OPT/Microsoft/Dependency-Agent/log |
| Ficheiros de configuração | /etc/OPT/Microsoft/Dependency-Agent/Config |
| Serviço executáveis | /sbin/bluestripe-collector<br>/sbin/bluestripe-collector-Manager |
| Ficheiros de armazenamento binário | /var/OPT/Microsoft/Dependency-Agent/Storage |



## <a name="troubleshooting"></a>Resolução de problemas
Se tiver problemas com o Monitor de dependência de aplicação, pode recolher informações de resolução de problemas de vários componentes utilizando as seguintes informações.

### <a name="windows-agents"></a>Agentes do Windows

#### <a name="microsoft-dependency-agent"></a>Agente de dependência da Microsoft
Para gerar dados resolução de problemas do agente de dependência de, abra uma linha de comandos como administrador e executar o script CollectBluestripeData.vbs utilizando o seguinte comando.  Pode adicionar – ajuda Sinalizar para apresentar opções adicionais.

    cd C:\Program Files\Bluestripe\Collector\scripts
    cscript CollectDependencyData.vbs

O pacote de suporte de dados é guardado no diretório % perfil de utilizador % para o utilizador actual.  Pode utilizar o – ficheiro <filename> opção para guardá-lo para uma localização diferente.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Dependência de Microsoft Agent Management Pack para MMA
O pacote de gestão do agente de dependência é executado no interior do agente de gestão da Microsoft.  -Recebe dados do agente de dependência de e encaminha-lo para o serviço de nuvem ADM.
  
Certifique-se de que o pacote de gestão é transferido executando os seguintes passos.

1.  Procure um ficheiro denominado Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp em C:\Program Files\Microsoft monitorização Agent\Agent\Health Service State\Management Packs.  
2.  Se o ficheiro não é apresentar e o agente está ligado a um grupo de gestão de SCOM, em seguida, certifique-se de que-tiver sido importado SCOM ao verificar os pacotes de gestão na área de trabalho de administração da consola do operações.

ADM MP escreve eventos no registo de eventos do Windows do Gestor de operações.  O registo pode ser [procurado no OMS](../log-analytics/log-analytics-log-searches.md) através da solução de registo do sistema, onde pode configurar os ficheiros de registo para carregar.  Se estiverem ativados eventos de depuração, são escritos o registo de eventos de aplicação, com a origem do evento *AdmProxy*.

#### <a name="microsoft-monitoring-agent"></a>Agente de monitorização da Microsoft
Para recolher rastreios de diagnóstico, abra uma linha de comandos como administrador e execute os seguintes comandos: 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Rastreios são escritos c:\Windows\Logs\OpsMgrTrace.  Pode deixar do rastreio com StopTracing.cmd.


### <a name="linux-agents"></a>Agentes de Linux

#### <a name="microsoft-dependency-agent"></a>Agente de dependência da Microsoft
Para gerar dados resolução de problemas do agente de dependência de início de sessão com uma conta que possui privilégios sudo ou raiz de e execute o seguinte comando.  Pode adicionar – ajuda Sinalizar para apresentar opções adicionais.

    /usr/lib/bluestripe-collector/scripts/collect-dependency-agent-data.sh

O pacote de suporte de dados guardado no /var/opt/microsoft/dependency-agent/log (se raiz) em directório de instalação do agente ou para o diretório do utilizador a executar o script (se não raiz).  Pode utilizar o – ficheiro <filename> opção para guardá-lo para uma localização diferente.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Dependência de Microsoft Agent Fluentd Plug-in para Linux
O plug-in da Fluentd do agente de dependência é executado no interior o agente de Linux OMS.  -Recebe dados do agente de dependência de e encaminha-lo para o serviço de nuvem ADM.  

Os registos são escritos os seguintes duas ficheiros.

- /var/OPT/Microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>Agente OMS para Linux
Um recurso de resolução de problemas para ligar os servidores de Linux ao OMS pode ser encontrado aqui: [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

Os registos para o agente OMS para Linux estão localizados no */var/optar ativamente por participar/microsoft/omsagent/registo/*.  

Os registos para omsconfig (configuração agente) estão localizados no */var/optar ativamente por participar/microsoft/omsconfig/registo/*.
 
O registo para os componentes OMI e SCX que fornecem dados de métricas de desempenho estão localizados no */var/optar ativamente por participar/omi/registo/* e */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Recolha de dados
Que pode esperar cada agente para transmitir aproximadamente 25MB por dia, dependendo do seu sistema as dependências complexa como são.  Dados de dependência ADM são enviados por cada agente de cada 15 segundos.  

O agente ADM normalmente consome % de 0,1 da memória do sistema e % de 0,1 da CPU do sistema.


## <a name="supported-operating-systems"></a>Sistemas operativos suportados
As secções seguintes listam os sistemas operativos suportados para o agente de dependência.   arquitecturas de 32 bits não são suportadas para qualquer sistema operativo.

### <a name="windows-server"></a>Windows Server
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Ambiente de trabalho Windows
- Nota: Windows 10 ainda não é suportada
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Chapéu vermelho Enterprise Linux, CentOS Linux e Linux Oracle (com RHEL Kernel)
- Apenas predefinido e SMP Linux kernel lançamentos do são suportados.
- Disponibilização de kernel não padrão, tais como PAE e Xen, não são suportadas para qualquer distribuição Linux. Por exemplo, um sistema com a cadeia de lançamento do "2.6.16.21-0.8-xen" não é suportado.
- Kernels personalizados, incluindo recompilações de kernels padrão, não são suportadas
- Kernel centos Plus não é suportada.
- Oracle inviolável Kernel (UEK) é abrangido numa secção diferente abaixo.


#### <a name="red-hat-linux-7"></a>Chapéu vermelho Linux 7
| Versão do SO | Versão do kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Chapéu vermelho Linux 6
| Versão do SO | Versão do kernel |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6,7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Chapéu vermelho Linux 5
| Versão do SO | Versão do kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5,9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux c / Kernel inviolável (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versão do SO | Versão do kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versão do SO | Versão do kernel
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5,9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versão do SO | Versão do kernel
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versão do SO | Versão do kernel
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Diagnóstico e a utilização de dados
Microsoft recolhe automaticamente a utilização e dados de desempenho através da sua utilização do serviço de Monitor de dependência de aplicação. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, a segurança e a integridade do serviço de Monitor de dependência de aplicação. Dados incluem informações sobre a configuração do seu software como o sistema operativo e versão e também incluem o endereço IP, o nome de DNS e o nome de estação de trabalho para poder fornecem capacidades de resolução de problemas precisas e eficientes. Não recolhemos nomes, endereços ou outras informações de contacto.

Para mais informações sobre a recolha de dados e a utilização, consulte a [Declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Próximos passos
- Saiba como [utilizar o Monitor de dependência de aplicação de](operations-management-suite-application-dependency-monitor.md) uma vez terem sido implementado e configurado.
