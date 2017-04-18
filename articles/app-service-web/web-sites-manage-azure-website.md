<properties 
    pageTitle="Gerir uma aplicação web na aplicação de serviço do Azure" 
    description="Ligações para recursos para gestão de uma aplicação web na aplicação de serviço de Azure." 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="rachelap"/>

# <a name="manage-a-web-app-in-azure-app-service"></a>Gerir uma aplicação web na aplicação de serviço do Azure

Este tópico contém ligações para recursos para gestão de uma aplicação web na [Aplicação de serviço de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Gestão de inclui todas as tarefas que manter a sua aplicação web do executar sem problemas. 

Ao longo da duração de uma aplicação web, irá efetuar tarefas de gestão de diferentes, tal como mover a partir de implementação inicial para operação normal, atualizações e manutenção.

Muitas tarefas de gestão de aplicação web podem ser efetuadas no Portal do Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Antes de implementar a aplicação web do produção

### <a name="choose-a-tier"></a>Selecione uma camada

Aplicação de serviço de Azure-lhe fornecida em cinco camadas: livre, partilhado, Basic, padrão e Premium. Para obter informações sobre as funcionalidades e preços para cada camada, consulte o artigo [preços detalhes](/pricing/details/app-service/). 

- [Planos do serviço de aplicação](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) permitem-lhe várias aplicações web em mesma camada de grupo.
- Pode sempre [Alternar camadas](web-sites-scale.md) depois de criar a sua aplicação web.

### <a name="configuration"></a>Configuração

Utilize o [Azure Portal](https://portal.azure.com/) para definir várias opções de configuração. Para obter detalhes, consulte o artigo [Configurar web apps no Azure aplicação de serviço](web-sites-configure.md). Eis uma lista de verificação rápida:

- Selecione **runtime versões** para .NET, PHP, Java ou Python, se necessário.
- Ative **WebSockets** se a sua aplicação web utiliza o protocolo WebSocket. (Isto inclui as aplicações que utilizam [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](web-sites-nodejs-chat-app-socketio.md).)
- Está a executar tarefas contínua web? Se Sim, ativar **Sempre no**.
- Defina o **documento predefinido**, tal como Index.

Para além destas definições de configuração básica, poderá querer configurar o seguinte:

- Encriptação **Secure Socket Layer (SSL)** . Para utilizar SSL com um nome de domínio personalizado, tem de obter um certificado SSL e, em configurar a aplicação web do utilizá-la. Consulte o artigo [Ativar HTTPS para uma aplicação web na aplicação de serviço de Azure](web-sites-configure-ssl-certificate.md).
- **Nome de domínio personalizado.** Um subdomínio em azurewebsites.net fica automaticamente com a aplicação web. Pode associar um nome de domínio personalizado, tal como contoso.com. Consulte [configurar um nome de domínio personalizado no Azure aplicação de serviço](web-sites-custom-domain-name.md).

Configuração específicas do idioma:

- **PHP**: [Configurar PHP no Azure de aplicação de serviço Web Apps](web-sites-php-configure.md).
- **Python**: [Configurar Python Azure de aplicação de serviço Web Apps](web-sites-python-configure.md)


## <a name="while-your-web-app-is-running"></a>Enquanto a aplicação web do estiver em execução

Enquanto a aplicação web do estiver em execução, que pretende certificar-se de que está disponível e que-escalas para cumprir o tráfego de utilizador. Também poderá ter de resolver problemas de erros.

### <a name="monitoring"></a>Monitorização

- Através do Portal do Azure, pode [Adicionar métricas de desempenho](web-sites-monitor.md) como utilização da CPU e o número de pedidos de cliente.
- [Escala a aplicação web](web-sites-scale.md) em resposta ao tráfego. Dependendo do seu camada, é possível dimensionar o número de VMs e/ou o tamanho das instâncias VM. Em camadas Standard e Premium, pode também configurar autoscaling, para que a sua aplicação web pode ser dimensionado automaticamente, com base numa agenda fixo ou em resposta ao carregar.  
 
### <a name="backups"></a>Cópias de segurança

- Definir [cópias de segurança automáticas](web-sites-backup.md) da sua aplicação web. Saiba mais sobre as cópias de segurança [neste](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/)vídeo.
- Saiba mais sobre as opções de [recuperação de base de dados](../sql-database/sql-database-business-continuity.md) na base de dados do SQL Azure.

### <a name="troubleshooting"></a>Resolução de problemas

- Se algum problema, pode [resolver problemas no Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), utilizando os registos de diagnóstico e depuração direto na nuvem. 
- Fora do Visual Studio, existem várias formas para recolher registos de diagnóstico. Consulte o artigo [Ativar diagnósticos do registo para web apps no Azure aplicação de serviço](web-sites-enable-diagnostic-log.md).
- Para as aplicações de Node.js, consulte o artigo [como depurar uma aplicação web do Node.js na aplicação de serviço de Azure](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Restaurar dados

- [Restaurar](web-sites-restore.md) uma aplicação web que estava anteriormente cópia de segurança.


## <a name="when-you-update-your-web-app"></a>Quando atualiza a aplicação web

Se não tiver ativado cópias de segurança automáticas, pode criar uma [cópia de segurança manual](web-sites-backup.md).

Considere utilizar [testado implementação](web-sites-staged-publishing.md). Esta opção permite-lhe publicar actualizações de uma implementação transição que executa o lado a lado com a sua implementação de produção. 

Se utilizar serviços de equipa do Visual Studio, pode configurar a implementação contínua a partir do controlo da origem:

- [Utilizar o controlo de versão do Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [Utilizar Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  
