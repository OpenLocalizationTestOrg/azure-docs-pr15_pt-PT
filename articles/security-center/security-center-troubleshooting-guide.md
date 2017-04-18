<properties
   pageTitle="Guia de resolução de problemas no Centro de segurança do Azure | Microsoft Azure"
   description="Este documento ajuda para resolver problemas no Centro de segurança do Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-troubleshooting-guide"></a>Guia de resolução de problemas do Centro de segurança do Azure
Este guia é para profissionais de TI (tecnologia) de informação, analistas de segurança de informações e os administradores a nuvem cujas organizações estiver a utilizar o Centro de segurança do Azure e necessitar de resolver que problemas relacionados do Centro de segurança.

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Este guia explica como resolver problemas relacionados do Centro de segurança. A maior parte de resolução de problemas concluído no Centro de segurança será executada verificando primeiro os registos de [Registo de auditoria](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) para o componente de falhado. Através de registos de auditoria, pode determinar:

- Quais as operações que foram tidos local
- Quem iniciou a operação
- Quando ocorreu a operação
- O estado da operação de
- Os valores das outras propriedades que podem ajudá-lo a operação de investigação

O registo de auditoria contém todas as operações de escrita (hoje, publicar, DELETE) executadas nos recursos, no entanto, não inclui operações de leitura (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Resolução de problemas de instalação do agente monitorização no Windows

O Centro de segurança monitorização agente é utilizado para executar a recolha de dados. Depois de recolha de dados está ativada e o agente corretamente é instalado no computador de destino, devem ser estes processos em execução:

- ASMAgentLauncher.exe - Azure agente de monitorização 
- ASMMonitoringAgent.exe - extensão Azure monitorização de segurança
- ASMSoftwareScanner.exe – Gestor de pesquisa Azure

A extensão de monitorização de segurança do Azure analisa para vários configuração relevantes de segurança e recolhe registos de segurança do máquina virtual. O Gestor de pesquisa será utilizado como um scanner de patches.

Se a instalação é executada com sucesso deverá ver uma entrada semelhante ao abaixo nos registos de auditoria para o destino VM:

![Registos de auditoria](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

Também pode obter mais informações sobre o processo de instalação lendo os registos do agente, que se encontra no *%systemdrive%\windowsazure\logs* (exemplo: C:\WindowsAzure\Logs).

> [AZURE.NOTE] Se o Centro de agente de segurança do Azure é falha, terá de reiniciar o destino VM uma vez que não existe nenhuma comando para parar e iniciar o agente.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Resolução de problemas de instalação do agente monitorização no Linux
Quando a resolução de problemas de instalação do agente de VM num sistema Linux deve Certifique-se de que foi transferida a extensão para/var/biblioteca/waagent /. Pode executar o comando abaixo para verificar se foi instalado:

`cat /var/log/waagent.log` 

Os outros ficheiros de registo que pode rever para resolver o problema finalidade são: 

- /var/log/mdsd.err
- / var registo/azure /

Num sistema de trabalho deverá ver uma ligação para o processo de mdsd no TCP 29130. Este é o syslog comunicar com o processo de mdsd. Pode validar este comportamento ao executar o comando em baixo:

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>Contactar o suporte da Microsoft

Alguns problemas podem ser identificados com as diretrizes fornecidos neste artigo, documentado outras pessoas, que também pode encontrar no Centro de segurança público [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter). No entanto se de que precisa de mais de resolução de problemas, pode abrir um novo pedido de suporte utilizando o Portal de Azure conforme apresentado abaixo: 

![Suporte técnico da Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Consulte também

Neste documento, o que aprendeu como configurar políticas de segurança no Centro de segurança do Azure. Para saber mais sobre o Centro de segurança do Azure, consulte o seguinte:

- [Guia de operações de planeamento de centro de segurança do Azure e](security-center-planning-and-operations-guide.md) — obter informações sobre como planear e compreenda as considerações de estrutura para adotar o Centro de segurança do Azure.
- [Estado de funcionamento de segurança no Centro de segurança do Azure de monitorização](security-center-monitoring.md) — Saiba como monitorizar o estado de funcionamento dos seus recursos Azure
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança
- [Monitorização soluções de parceiros com o Centro de segurança do Azure](security-center-partner-solutions.md) — Saiba como monitorizar o estado de funcionamento de soluções de parceiros.
- [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço
- [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — localizar mensagens de blogue sobre Azure segurança e conformidade
