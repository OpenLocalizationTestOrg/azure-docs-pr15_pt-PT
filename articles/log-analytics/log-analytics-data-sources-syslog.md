<properties 
   pageTitle="Mensagens do sistema no registo de análise | Microsoft Azure"
   description="Syslog é um protocolo de registo de eventos que é comum para Linux.   Este artigo descreve como configurar colecção de mensagens do sistema no detalhes de registos criam no repositório de OMS e de análise de registo."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />


# <a name="syslog-data-sources-in-log-analytics"></a>Origens de dados do sistema no registo de análise

Syslog é um protocolo de registo de eventos que é comum para Linux.  Aplicações irão enviar mensagens que podem ser armazenadas no computador local ou entregue a um Recolectores do sistema.  Quando o agente OMS para Linux estiver instalado,-configura o daemon do sistema local para reencaminhar mensagens para o agente.  O agente, em seguida, envia a mensagem para onde é criado um registo correspondente no repositório de OMS a análise de registo.  

> [AZURE.NOTE]Análise de registo suporta colecção de mensagens enviadas por rsyslog ou GN do sistema. O daemon do sistema de predefinido na versão 5 da versão Enterprise Linux chapéu vermelho, CentOS e Oracle Linux (sysklog) não é suportado para recolha de eventos do sistema. Para recolher dados do sistema de nesta versão destes distribuições, o [rsyslog daemon](http://rsyslog.com) deve ser instalado e configurado para substituir sysklog.

![Colecção do sistema](media/log-analytics-data-sources-syslog/overview.png)


## <a name="configuring-syslog"></a>Configuração do sistema
Agente do OMS para Linux só irá recolher eventos com as instalações e severities estão especificados na sua configuração.  Pode configurar do sistema através do portal do OMS ou ao gerir os ficheiros de configuração no seu agentes Linux.


### <a name="configure-syslog-in-the-oms-portal"></a>Configurar do sistema no portal do OMS

Configure do sistema no [menu de dados em definições de análise de registo](log-analytics-data-sources.md#configuring-data-sources).  Esta configuração é entregue ao ficheiro de configuração no cada agente Linux.

Pode adicionar uma nova funcionalidade escrevendo no seu nome e clicando em **+**.  Para cada funcionalidade, serão recolhidas apenas as mensagens com as severities selecionados.  Verifique os severities para a funcionalidade específica ao qual pretende recolher.  Não pode fornecer qualquer dos critérios adicional para filtrar mensagens.

![Configurar do sistema](media/log-analytics-data-sources-syslog/configure.png)


Por predefinição, todas as alterações à configuração automaticamente são enviadas para todos os agentes.  Se quiser configurar manualmente do sistema no cada agente Linux, em seguida, desmarque a caixa *Aplicar abaixo configuração para minha máquinas Linux*.


### <a name="configure-syslog-on-linux-agent"></a>Configurar o do sistema no Linux agente

Quando instala um ficheiro de configuração do sistema predefinido que define a facilidade e gravidade das mensagens que são recolhidas [OMS agente é instalado num cliente Linux](log-analytics-linux-agents.md).  Pode modificar este ficheiro para alterar a configuração.  O ficheiro de configuração é diferente consoante o daemon do sistema que tenha instalado o cliente.

> [AZURE.NOTE] Se editar a configuração do sistema, tem de reiniciar o daemon do sistema para que as alterações sejam aplicadas.

#### <a name="rsyslog"></a>rsyslog

Ficheiro de configuração para rsyslog está localizado em **/etc/rsyslog.d/95-omsagent.conf**.  Os seus conteúdos predefinido são apresentados abaixo.  Isto recolhe do sistema mensagens enviadas a partir do agente de local para todas as instalações com um nível de aviso ou superior.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Pode remover uma instalação ao remover a sua secção do ficheiro de configuração.  Pode limitar os severities que são recolhidas para uma determinada função modificando entrada que instalações.  Por exemplo, para limitar a funcionalidade de utilizador para mensagens com uma gravidade do erro ou superior seria modificar nessa linha do ficheiro de configuração para o seguinte procedimento:

    user.error  @127.0.0.1:25224


#### <a name="syslog-ng"></a>GN do sistema

Ficheiro de configuração para rsyslog é localização na **/etc/syslog-ng/syslog-ng.conf**.  Os seus conteúdos predefinido são apresentados abaixo.  Isto recolhe do sistema mensagens enviadas a partir do agente de local para todas as instalações e severities todos os.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };
    
    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };
    
    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };
    
    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };
    
    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };
    
    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };
    
    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Pode remover uma instalação ao remover a sua secção do ficheiro de configuração.  Pode limitar os severities que são recolhidas para uma determinada função removendo-los a partir da sua lista.  Por exemplo, para limitar a funcionalidade de utilizador a mensagens apenas alertas e críticas, terá de modificar essa secção do ficheiro de configuração para o seguinte procedimento:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="changing-the-syslog-port"></a>Alterar a porta do sistema

O agente OMS recebe mensagens do sistema no cliente local porto 25224.  Pode alterar esta porta adicionando a secção seguinte para o ficheiro de configuração do agente OMS que se encontra no **/etc/opt/microsoft/omsagent/conf/omsagent.conf**.  Substitua 25224 na entrada de **porta** o número da porta que pretende.  Tenha em atenção que também será necessário modificar o ficheiro de configuração para o daemon do sistema enviar mensagens para esta porta.

    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.syslog
    </source>


## <a name="data-collection"></a>Recolha de dados

O agente OMS recebe mensagens do sistema no cliente local porto 25224. Ficheiro de configuração para o daemon do sistema reencaminha do sistema mensagens enviadas a partir da aplicação para esta porta onde são recolhidas pela análise de registo.


## <a name="syslog-record-properties"></a>Propriedades do registo do sistema

Registos do sistema tenham um tipo de **Syslog** e tem as propriedades da tabela seguinte.

| Propriedade | Descrição |
|:--|:--|
| Computador | Computador que o evento foi recolhido a partir do. |
| Instalações | Define a parte do sistema que gerou a mensagem. |
| HostIP | Endereço IP do sistema de enviar a mensagem.  |
| Nome do anfitrião | Nome do sistema de enviar a mensagem. |
| SeverityLevel | Nível de gravidade do evento. |
| SyslogMessage | Texto da mensagem. |
| ID de processo | ID do processo que gerou a mensagem. |
| EventTime | Data e hora em que o evento foi gerado.



## <a name="log-queries-with-syslog-records"></a>Consultas de registo com os registos do sistema

A tabela seguinte fornece exemplos diferentes de consultas de registo que obter registos do sistema.

| Consulta | Descrição |
|:--|:--|
| Tipo = do sistema | Todos os Syslogs. |
| Tipo = do sistema SeverityLevel = erro | Todos os registos do sistema com gravidade do erro. |
| Tipo = Syslog & #124; medir count() pelo computador | Registos de contagem do sistema pelo computador. |
| Tipo = Syslog & #124; medir count() pela facilidade | Registos de contagem do sistema por instalações. |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [as pesquisas de registo](log-analytics-log-searches.md) analisar os dados recolhidos a partir de origens de dados e soluções. 
- Utilize [Campos personalizados](log-analytics-custom-fields.md) para analisar dados de registos do sistema para campos individuais.
- [Configurar Linux agentes](log-analytics-linux-agents.md) para recolher outros tipos de dados. 
