<properties
    pageTitle="Descrição geral dos diagnósticos do Azure | Microsoft Azure"
    description="Utilizar os diagnósticos do Azure para depuração, medir o desempenho, monitorização, análise de tráfego no serviços em nuvem, máquinas virtuais e ferro de serviço"
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/02/2016"
    ms.author="robb"/>


# <a name="what-is-microsoft-azure-diagnostics"></a>O que é diagnósticos do Microsoft Azure


Azure diagnóstico é a capacidade de Azure que permite a recolha de dados diagnóstico numa aplicação implementada. Pode utilizar a extensão diagnóstico a partir de um número de origens diferentes. Atualmente suportados são Web de serviço de nuvem do Azure e funções de trabalho, máquinas virtuais do Azure a executar o Microsoft Windows e o serviço ferro. Outros serviços Azure ter os seus próprios diagnósticos em separado.

## <a name="data-you-can-collect"></a>É possível recolher de dados

Diagnósticos do Azure podem recolher os seguintes tipos de dados:

Origem de dados|Descrição
---|---
Contadores de desempenho | Sistema operativo e contadores de desempenho personalizado
Registos de aplicação     | Rastrear mensagens escritas pela aplicação
Registos de eventos do Windows   | Informações enviadas para o sistema de registo de eventos do Windows
Origem do evento .NET    | O código de eventos de escrita utilizando a classe de .NET [OrigemEvento](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)
Registos do IIS             | Informações acerca de web sites IIS
Manifesto com base ETW   | Eventos de rastreio para o Windows eventos gerados por qualquer processo
Informações de estado da falha de sistema          | Informações sobre o estado do processo de trabalho uma falha de aplicação
Registos de erros personalizado    | Registos criados pela sua aplicação ou serviço
Registos de infraestrutura de diagnóstico Azure|Obter informações sobre diagnósticos próprio

A extensão de diagnóstico Azure pode transferir estes dados para uma conta de armazenamento Azure ou enviá-la para serviços como [Informações de aplicação](./application-insights/app-insights-cloudservices.md). Pode utilizar os dados para depuração e resolução de problemas, medir o desempenho, monitorização de utilização de recursos, a análise de tráfego e a capacidade de planeamento e de auditoria.


## <a name="versioning"></a>Controlo de versões
Ver [Histórico de versões de diagnóstico Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Próximos passos
Escolha qual está a tentar recolher diagnósticos no e utilizar os seguintes artigos para começar a utilizar o serviço. Utilize as ligações de diagnóstico Azure gerais para referência para tarefas específicas.

## <a name="web-apps"></a>Web Apps
Tenha em atenção que Web Apps não utilizar os diagnósticos do Azure. Localizar as informações equivalentes na [Web Apps](./app-service-web/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Serviços em nuvem utilizando diagnóstico do Azure
- Se utilizar o Visual Studio, consulte o artigo [Utilizar Visual Studio para rastrear uma aplicação de serviços em nuvem](./vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar a utilizar. Caso contrário, consulte o artigo
- [Como monitorizar a serviços em nuvem utilizando os diagnósticos do Azure](./cloud-services/cloud-services-how-to-monitor.md)
- [Configurar o Azure diagnósticos numa aplicação de serviços na nuvem](./cloud-services/cloud-services-dotnet-diagnostics.md)

Para obter mais avançados tópicos, consulte o artigo

- [Utilizar o Azure diagnósticos com informações de aplicação para serviços em nuvem](./application-insights/app-insights-cloudservices.md)
- [Analisar o fluxo de uma aplicação de serviços em nuvem com diagnósticos do Azure](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Utilizar o PowerShell para configurar o diagnóstico em serviços em nuvem](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## <a name="virtual-machines-using-azure-diagnostics"></a>Máquinas virtuais utilizando diagnóstico do Azure
- Se utilizar o Visual Studio, consulte o artigo [Utilizar Visual Studio para rastreio máquinas virtuais do Azure](./vs-azure-tools-debug-cloud-services-virtual-machines.md) para começar a utilizar. Caso contrário, consulte o artigo
- [Configurar o Azure diagnósticos numa máquina de Virtual do Azure](./virtual-machines-dotnet-diagnostics.md)

Para obter mais avançados tópicos, consulte o artigo

- [Utilizar o PowerShell para configurar o diagnósticos em máquinas virtuais do Azure](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Criar uma Máquina Virtual do Windows com a monitorização e diagnósticos de utilizar o modelo de Gestor de recursos do Azure](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="service-fabric-using-azure-diagnostics"></a>Serviço ferro utilizando diagnóstico do Azure
Introdução ao [Monitor uma aplicação de serviço ferro](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Muitos outros artigos serviço ferro diagnóstico estão disponíveis na árvore de navegação à esquerda quando chegar deste artigo.

## <a name="general-azure-diagnostics-articles"></a>Artigos geral Azure diagnóstico
- [Configuração do azure diagnóstico Schema](https://msdn.microsoft.com/library/azure/mt634524.aspx) - Saiba como alterar o ficheiro de esquema para recolher e encaminhar dados diagnóstico. Tenha em atenção que também pode utilizar Visual Studio para alterar o ficheiro de esquema.
- [Diagnósticos do Azure como os dados são armazenados no armazenamento Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - saber os nomes das tabelas e blobs onde os dados de diagnóstico escritos.
- Saiba como [utilizar contadores de desempenho no Azure diagnóstico](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Saiba como [encaminhar Azure informações de diagnóstico para informações de aplicação](./azure-diagnostics-configure-applicationinsights.md)
- Se tiver problemas com diagnóstico iniciar ou localizar os seus dados em tabelas de armazenamento do Windows Azure, consulte o artigo [Resolução de problemas diagnóstico do Azure](./azure-diagnostics-troubleshooting.md)
