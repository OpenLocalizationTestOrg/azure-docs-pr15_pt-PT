<properties
    pageTitle="WebJobs no Azure de aplicação de serviço"
    description="Saiba como criar WebJobs para executar testes de fundo, interagir com os serviços, como o armazenamento e Bus de serviço e de criar tarefas agendadas."
    services="app-service"
    documentationCenter=""
    authors="christopheranderson"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="chrande"/>

# <a name="using-webjobs-in-azure-app-service"></a>Utilizar WebJobs no Azure de aplicação de serviço

Este artigo contém ligações para recursos de documentação sobre como utilizar Azure WebJobs e o SDK do Azure WebJobs. Azure WebJobs fornecem uma forma fácil de executar scripts ou programas como processos em segundo plano na [Aplicação de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Pode carregar e executar um ficheiro executável tal como como cmd, vampiro, exe (.NET), ps1, m, php, copiar, js e para caixa. Estes programas executados como WebJobs uma agenda (cron) ou continuamente.

O SDK WebJobs torna mais fácil de utilizar o armazenamento do Windows Azure. O SDK WebJobs tem um sistema de accionador que funciona com o Microsoft Azure armazenamento de Blobs, filas e tabelas, bem como serviço Bus filas e enlace.

Criar, implementar e gerir WebJobs está totalmente integrada com ferramentas integrada no Visual Studio. Pode criar WebJobs a partir de modelos, publicar e gerir (executar/parar/monitor/depuração)-los.

O dashboard WebJobs no portal do Azure fornece as capacidades da gestão poderosas que dar-lhe controlo total sobre a execução de WebJobs, incluindo a capacidade de invocar funções individuais dentro de WebJobs. O dashboard também apresenta a função runtimes e registo de saída.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]
