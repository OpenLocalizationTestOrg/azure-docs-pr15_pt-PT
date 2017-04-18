<properties 
    pageTitle="Recursos de documentação WebJobs Azure" 
    description="Recomendado recursos para conhecer como utilizar Azure WebJobs e o SDK do Azure WebJobs." 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="tdykstra"/>

# <a name="azure-webjobs-documentation-resources"></a>Recursos de documentação WebJobs Azure

## <a name="overview"></a>Descrição geral

Este tópico fornece hiperligações para recursos de documentação sobre como utilizar Azure WebJobs e o SDK do Azure WebJobs. Azure WebJobs fornecem uma forma fácil de executar scripts ou programas como processos em segundo plano no contexto de uma [aplicação de serviço web app, de API aplicação ou aplicação móvel](../app-service/app-service-value-prop-what-is.md). Pode carregar e executar um ficheiro executável tal como como cmd, vampiro, exe (.NET), ps1, m, php, copiar, js e para caixa. Estes programas executar como WebJobs uma agenda (cron) ou continuamente.

É o objetivo do [WebJobs SDK](websites-webjobs-resources.md) simplificar o código que escrever para tarefas comuns que um WebJob pode efetuar, como o processamento de imagens, processamento de fila e RSS agregação, manutenção de ficheiros e enviar e-mails. O SDK WebJobs tem funcionalidades incorporadas para trabalhar com o armazenamento do Windows Azure e Bus de serviço, para agendamento de tarefas e processamento de erros e muitas outras cenários comuns. Além disso, foi concebido para ser extensible e existe um [repositório de origem para as extensões de abrir](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Funções do Azure](../azure-functions/functions-overview.md) (atualmente em pré-visualização) é baseado numa versão do SDK WebJobs que funciona com c# script, Node.js e outros idiomas. 

Criar, implementar e gerir WebJobs está totalmente integrada com ferramentas integrada no Visual Studio. Pode criar WebJobs a partir de modelos, publicar e gerir (executar/parar/monitor/depuração)-los. 

O dashboard WebJobs no portal do Azure fornece as capacidades da gestão poderosas que dar-lhe controlo total sobre a execução de WebJobs, incluindo a capacidade de invocar funções individuais dentro de WebJobs. O dashboard também apresenta a função runtimes e registo de saída. 

##<a name="getstarted"></a>Introdução aos WebJobs e o SDK WebJobs

* [Introdução ao Azure WebJobs](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs são uma maravilha e deverá começar a utilizá-los momento!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Mensagem de blogue por Tróia caça).
* [Funcionalidades de Azure WebJobs](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [O que é o SDK WebJobs](websites-dotnet-webjobs-sdk.md)
* [Orientação de tarefas de fundo ao Microsoft padrões e práticas](/documentation/articles/best-practices-background-jobs/)
* [Anunciar o 1.1.0 RTM do Microsoft Azure WebJobs SDK](/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Começar a trabalhar com o SDK WebJobs Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Como utilizar o armazenamento de Azure filas com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Como utilizar o armazenamento de Blobs do Azure com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Como utilizar o armazenamento de tabela do Azure com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Como utilizar o Azure Service Bus com o SDK WebJobs](websites-dotnet-webjobs-sdk-service-bus.md)
* [Como utilizar WebHooks com o SDK WebJobs, com exemplos para GitHub, IFTTT e HTTP](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs SDK referência rápida do (transferir PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Documentação de definições de WebJobs em GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Vídeos
    * [WebJobs e o SDK WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Azure WebJobs série de vídeos no canal de 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [Introdução ao WebJobs ferramenta para Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
    * [Azure WebJobs actualização com Pranav Rastogi - expansão na versão 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Consulte também as secções seguintes [WebJobs implementar](#deploy) e [testar e depuração WebJobs](#debug).

##<a name="deploy"></a>Implementar WebJobs

* [Como implementar o Azure WebJobs utilizando o Visual Studio](websites-dotnet-deploy-webjobs.md)
* [Como implementar WebJobs utilizando o Portal do Azure](web-sites-create-web-jobs.md)
* [Activar a entrega contínua ou da linha de comandos da Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Git implementar uma aplicação de consola do .NET para Azure utilizando WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Implementar uma WebJob F # Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Implementar os serviços de personalizado como Webjobs do Azure](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Vídeos
    * [Introdução ao WebJobs ferramenta para Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Agendamento WebJobs

* [A caixa de diálogo WebJob Azure adicionar](websites-dotnet-deploy-webjobs.md#configure)
* [Criar um WebJob agendada no Portal do Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Ao ligar uma tarefa de programador para um WebJob](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Agendamento Azure WebJobs com cron expressões](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Funções de WebJob individuais utilizando o TimerTrigger SDK WebJobs de agendamento](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>Teste e depuração WebJobs

* [Novo Developer depuração de funcionalidades e para WebJobs Azure no Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Visualizar o Dashboard WebJobs](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Como escrever registos utilizando o SDK WebJobs e vê-los no Dashboard](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [WebJobs depuração remoto](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Quem escreveu esse blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Código interactivo alojamento na nuvem](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Adicionar o rastreio a Azure WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Monitorizar, diagnosticar e resolver problemas de armazenamento do Windows Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Vídeos
    * [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Dimensionamento WebJobs

* [Dimensionamento da aplicação Web com sites públicos do Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure de aplicação de serviço: criação grandes escala pronto para empresas Web Apps](https://channel9.msdn.com/Events/Build/2014/3-626). Folhas de rosto de dimensionamento do web apps com WebJobs, incluindo o SDK WebJobs.
* Vídeos
    * [Dimensionamento saída WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Recursos WebJobs adicionais

* [Mensagem de blogue das versões de WebJobs DG Azure por Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Executar tarefas de Web do Powershell no Azure de aplicação de serviço](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Introdução notificado quando o Azure acionou WebJobs conclui](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Política de retenção de cópia de segurança do Web App Simple com WebJobs](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure Web Apps e serviços em nuvem lenta no primeiro pedido](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Mostra como utilizar WebJobs para simular a funcionalidade de AlwaysOn só está disponível para a camada comparar padrão.
* [Desligar sem problemas WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Encerramento com êxito para WebJobs SDK, consulte o artigo [encerramento com êxito](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatizar cópias de segurança com o Azure WebJobs & AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vídeos
    * [Vídeos de WebJobs Azure por Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Azure WebJobs série de vídeos no canal 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Recursos adicionais de WebJobs SDK

* [Notas de lançamento do WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [Código de origem WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk)
* [Extensões WebJobs SDK código fonte](https://github.com/Azure/azure-webjobs-sdk-extensions), com o [Guia de detalhadas ao modelo de expansão](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Código fonte do WebJobs SDK Script](https://github.com/Azure/azure-webjobs-sdk-script/) (utilizado para as [Funções de Azure](../azure-functions/functions-overview.md))
* [WebJob para carregar ficheiros FREB para o armazenamento do Windows Azure utilizando o SDK WebJobs](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Alojar webjobs Azure fora Azure, com as vantagens de registo a partir de um Azure alojado webjob](http://bypassion.dk/?p=510)
* [Criar uma ferramenta de importação de dados com Azure WebJobs](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Descrição geral das funções Azure](../azure-functions/functions-overview.md)
* Vídeos
    * [Azure WebJobs série de vídeos no canal de 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Aplicações de WebJob de exemplo

* [Aplicações de exemplo fornecidas pela equipa WebJobs no GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Azure simples Web App com WebJobs a back-end utilizando o SDK WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Demonstra utilização de WebJobs agendada e condicionada por eventos. Consulte a mensagem [a reformulação repetida SiteMonitR utilizando Azure WebJobs SDK](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)do blogue.

##<a name="blogs"></a>Blogues

* [Blogue do Azure](/blog)
* [Blogue de Amit da Apple](http://blog.amitapple.com/). Realça o WebJobs (e não o SDK).
* [Blogue de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Obter ajuda com WebJobs

* [StackOverflow para WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow para o SDK WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow para as funções Azure](http://stackoverflow.com/questions/tagged/azure-functions)
* [Fórum do Azure e ASP.NET](http://forums.asp.net/1247.aspx)
* [Azure fórum da aplicação serviço Web Apps](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Site de voz de utilizador de aplicações Web Azure](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Utilize o hashtag #AzureWebJobs.
* [Um problema de erros de WebJobs ou um relatório](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

