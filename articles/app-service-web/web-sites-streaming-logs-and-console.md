<properties 
    pageTitle="Transmissão de registos e consola" 
    description="Descrição geral da consola e registos de transmissão" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="byvinyal"/>

# <a name="streaming-logs-and-the-console"></a>Transmissão de consola do e registos

## <a name="streaming-logs"></a>Transmissão de registos

O **Azure portal** fornece um Visualizador de registo de transmissão integrada que permite-lhe ver eventos de rastreio de suas aplicações do **Serviço de aplicação** em tempo real.  

Configurar esta funcionalidade necessita de alguns passos simples:

- Escrever rastreios no seu código
- Ativar a aplicação **registos de diagnóstico** para a sua aplicação
- Ver a sequência de **Registos de transmissão** IU incorporada no **portal do Azure**.

### <a name="how-to-write-traces-in-your-code"></a>Como escrever rastreios no seu código ###

Escrever rastreios no seu código é fácil.  Em c# é tão fácil como escrever o seguinte código:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

A classe de rastreio encontra-se no espaço de nomes System.Diagnostics.

Numa aplicação node.js pode escrever este código para alcançar o mesmo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Como ativar e ver a transmissão de registos
![][BrowseSitesScreenshot]Diagnósticos estão ativados numa base por aplicação. Comece por navegação para o site que pretende ativar esta funcionalidade no.  
  
![][DiagnosticsLogs]A partir do menu definições, desloque para baixo para a secção de **monitorização** e clique em **Registos de diagnóstico (1)**. Em seguida, **Ativar (2)** **(Sistema de ficheiros) de registo de aplicação** ou **Aplicação registo (BLOBs)** a opção ao **nível** permite alterar o nível de gravidade dos seus rastreios para capturar. Se apenas está a tentar para se familiarizar com a funcionalidade, defina o nível para **verboso** para garantir que todos os extratos de rastreio são recolhidos.

Clique em **Guardar** no topo da pá e estiver pronto para ver os registos.

>[AZURE.NOTE] Quanto maior for o **nível de gravidade** mais recursos são consumidos para registo e para os seus rastreios mais são produzidas. Certifique-se de **nível de gravidade** está configurado para a verbosidade correta para um site de tráfego alto ou de produção. 

![][StreamingLogsScreenshot]Para ver a **transmissão registos** a partir no interior do portal Azure, clique no **fluxo de registo (1)** também na secção **monitorização** do menu definições. Se a sua aplicação está a escrever ativamente declarações de rastreio, deverá vê-los na **transmissão (2) os registos de IU** em tempo real próximo.

## <a name="console"></a>Consola
O **Azure portal** oferece um acesso de consola para a sua aplicação. Pode explorar o sistema de ficheiros da sua aplicação e executar scripts de powershell/cmd. Estão vinculados pelas mesmas permissões definir como código de aplicação em execução quando os comandos da consola em execução. Acesso a directórios protegidos ou de execução de scripts que requerem permissões elevadas está bloqueado.  

![][ConsoleScreenshot]A partir do menu definições, desloque para baixo para a secção **Ferramentas de desenvolvimento** e clique em **Consola (1)** e a **(2) consola** IU abre-se para a direita.

Para se familiarizar com a **consola**, experimente comandos básicos como:

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
