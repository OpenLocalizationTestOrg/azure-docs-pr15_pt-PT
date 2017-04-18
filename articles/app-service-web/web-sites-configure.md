<properties 
    pageTitle="Configurar aplicações web na aplicação de serviço do Azure" 
    description="Como configurar uma aplicação web nos serviços de aplicação do Azure" 
    services="app-service\web" 
    documentationCenter="" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="configure-web-apps-in-azure-app-service"></a>Configurar aplicações web na aplicação de serviço do Azure #

Este tópico explica como configurar uma aplicação web utilizando o [Portal do Azure].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="application-settings"></a>Definições da aplicação

1. No [Portal do Azure], abra o pá para a aplicação web.
2. Clique em **todas as definições**.
3. Clique em **definições da aplicação**.

![Definições da aplicação][configure01]

A **definições da aplicação** pá tem definições agrupadas em várias categorias.

### <a name="general-settings"></a>Definições gerais

**Versões do quadro**. Defina estas opções se a sua aplicação utiliza qualquer um destes quadros: 

- **.NET Framework**: configurar o .NET framework versão. 
- **PHP**: defina a versão PHP ou **DESATIVADO** para desativar PHP. 
- **Java**: selecione a versão de Java ou **DESATIVADO** para desativar Java. Utilize a opção de **Web contentor** para escolher entre as versões Tomcat e cais.
- **Python**: selecione a versão de Python ou **DESATIVADO** para desativar Python.

Por motivos técnicos, permitindo-Java para a sua aplicação desativa as opções de .NET, PHP e Python.

<a name="platform"></a>
**Plataforma**. Seleciona se a sua aplicação web é executado num ambiente de 32 bits ou 64 bits. O ambiente de 64 bits requer o modo Basic ou padrão. Liberte e modos de partilhado executar sempre num ambiente de 32 bits.

**Web Sockets**. Definir **Ativado** para ativar o protocolo WebSocket; Por exemplo, se utilizar a aplicação web do [ASP.NET SignalR] ou [socket.io].

<a name="alwayson"></a>
**Sempre no**. Por predefinição, aplicações web encontram descarregadas sejam idle por alguns período de tempo. Isto permite que o sistema de conservar os recursos. No modo de Basic ou padrão, pode ativar **Sempre no** manter a aplicação carregado sempre. Se a sua aplicação executa tarefas de web contínuo, deverá ativar **Sempre no**ou as tarefas de web poderão não ser executados sujeito.

**Gerido Pipeline versão**. Define o IIS [modo em curso]. Deixe configurado para integrados (a predefinição), exceto se tiver uma aplicação de legado que necessita de uma versão anterior do IIS.

**Trocar automática**. Se ativar trocar automática para uma ranhura de implementação, o aplicação de serviço serão automaticamente a trocar do web app para produção, quando prime uma atualização para essa ranhura. Para obter mais informações, consulte o artigo [implementar a transição fendas web apps no Azure aplicação de serviço] (web-sites-testado-publishing.md).

### <a name="debugging"></a>Depuração

A **depuração remota**. Ativa a depuração remota. Quando ativada, pode utilizar o depurador remoto no Visual Studio para ligar diretamente para a sua aplicação web. Depuração remota permanecerá ativada para 48 horas. 

### <a name="app-settings"></a>Definições da aplicação

Esta secção contém pares valor/nome que lhe web app irá carregar em Iniciar para cima. 

- Para as aplicações do .NET, estas definições são inseridas para a configuração do .NET `AppSettings` o tempo de execução, substituindo as definições de existentes. 

- Aplicações PHP, Python, Java e nó podem aceder a estas definições como variáveis de ambiente o tempo de execução. Para cada definição de aplicação, são criadas duas variáveis de ambiente; uma com o nome especificado pelo entrada da definição de aplicação e outro com um prefixo APPSETTING_. Ambos contenham o mesmo valor.

### <a name="connection-strings"></a>Cadeias de ligação

Cadeias de ligação para recursos ligadas. 

Para as aplicações do .NET, são inseridas estes cadeias de ligação para a configuração do .NET `connectionStrings` das definições no runtime, substituindo as entradas existentes onde a tecla é igual ao nome da base de dados ligada. 

Para as aplicações PHP, Python, Java e nó, estas definições estarão disponíveis como variáveis de ambiente o tempo de execução, o prefixo com o tipo de ligação. Os prefixos variável de ambiente são os seguintes: 

- SQL Server:`SQLCONNSTR_`
- MySQL:`MYSQLCONNSTR_`
- Base de dados SQL:`SQLAZURECONNSTR_`
- Personalizada:`CUSTOMCONNSTR_`

Por exemplo, se uma cadeia de ligação do MySql fosse chamada `connectionstring1`, deverá ser acedido através da variável de ambiente `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documentos predefinidos

O documento predefinido é a página web que é apresentado no URL de raiz para um Web site.  É utilizado o primeiro ficheiro correspondente na lista. 

Aplicações Web poderão utilizar módulos que encaminhar com base no URL, em vez de funcionar em conteúdo estático, caso em que não existem não é nenhum documento predefinida como tal.    

### <a name="handler-mappings"></a>Mapeamentos de processador

Utilize esta área para adicionar processadores dos scripts personalizados para processar pedidos para as extensões de ficheiro específica. 

- **Extensão**. A extensão de ficheiro para ser resolvido, tal como *. php ou handler.fcgi. 
- **Caminho de processador de script**. O caminho absoluto do processador script. Pedidos de ficheiros que correspondem a extensão de ficheiro serão processados pelo processador de script. Utilizar o caminho `D:\home\site\wwwroot` para fazer referência a diretório de raiz da sua aplicação.
- **Os argumentos adicionais**. Argumentos da linha de comandos opcionais para o processador de script 


### <a name="virtual-applications-and-directories"></a>Aplicações virtuais e directórios 
 
Para configurar as aplicações virtuais e directórios, especifique a cada directório virtual e o caminho físico correspondente em relação a raiz de Web site. Opcionalmente, pode selecionar a caixa de verificação de **aplicação** para marcar um diretório virtual como uma aplicação.


## <a name="enabling-diagnostic-logs"></a>Ativar registos de diagnóstico

Para ativar registos de diagnóstico:

1. Na pá para a sua aplicação web, clique em **todas as definições**.
2. Clique em **registos de diagnóstico**. 

Opções para escrever os registos de diagnóstico a partir de uma aplicação web que suporta a funcionalidade de registo: 

- **Registo de aplicação**. Escreve registos da aplicação para o sistema de ficheiros. Registo tem a validade durante um período de 12 horas. 

**Nível**. Quando o registo de aplicação é activado, esta opção especifica a quantidade de informações que serão registados (erro, aviso, informações ou verboso).

**Registo de servidor web**. Os registos são guardados no formato de ficheiro de registo expandido W3C. 

**Mensagens de erro detalhada**. Mensagens de erro detalhada guarda ficheiros. htm. Os ficheiros são guardados em /LogFiles/DetailedErrors. 

**Rastreio de pedidos de falhou**. Registos Ocorreu uma falha pedidos para ficheiros XML. Os ficheiros são guardados em /LogFiles/W3SVC*xxx*, onde xxx é um identificador exclusivo. Esta pasta contém um ficheiro XSL e um ou mais ficheiros XML. Certifique-se transferir o ficheiro XSL, uma vez que fornece funcionalidade para a formatação e filtrar os conteúdos dos ficheiros XML.

Para ver os ficheiros de registo, tem de criar as credenciais FTP, da seguinte forma:

1. Na pá para a sua aplicação web, clique em **todas as definições**.
2. Clique em **implementação credenciais**.
3. Introduza um nome de utilizador e palavra-passe.
4. Clique em **Guardar**.

![Definir credenciais de implementação][configure03]

O nome de utilizador FTP completo é "app\username" onde a *aplicação* é o nome da sua aplicação web. O nome de utilizador está listado na pá de aplicação web, em **Essentials**.  

![Credenciais de implementação de FTP][configure02]

## <a name="other-configuration-tasks"></a>Outras tarefas de configuração

### <a name="ssl"></a>SSL 

No modo de Basic ou padrão, pode carregar certificados SSL para um domínio personalizado. Para mais informações, consulte o artigo [ativar HTTPS para uma aplicação web]. 

Para ver os seus certificados carregados, clique em **Todas as definições de** > **domínios personalizados e SSL**.

### <a name="domain-names"></a>Nomes de domínio

Adicione os nomes de domínio personalizado para a sua aplicação web. Para mais informações, consulte o artigo [configurar um nome de domínio personalizado para uma aplicação web na aplicação de serviço de Azure].

Para ver os nomes de domínio, clique em **Todas as definições de** > **domínios personalizados e SSL**.

### <a name="deployments"></a>Híbridas

- Configure a implementação contínua. Consulte o artigo [Utilizar Git para implementar Web Apps no Azure aplicação de serviço](./web-sites-deploy.md).
- Faixas de implementação. Consulte o artigo [Implementar em ambientes de transição para Web Apps no Azure de aplicação de serviço].


Para ver o seu faixas de implementação, clique em **Todas as definições de** > **faixas de implementação**.

### <a name="monitoring"></a>Monitorização

No modo de Basic ou padrão, pode testar a disponibilidade de HTTP ou HTTPS os pontos finais, a partir de localizações distribuído geo até três. Um teste monitorização falha se o código de resposta HTTP é um erro (4xx ou 5xx) ou a resposta demora mais de 30 segundos. Um ponto final é considerado disponível se os testes de monitorização bem sucedida a partir de todas as localizações especificadas. 

Para obter mais informações, consulte o artigo [como: monitorizar o estado de ponto final de web].

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço], onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="next-steps"></a>Próximos passos

- [Configurar um nome de domínio personalizado no serviço de aplicação do Azure]
- [Ativar HTTPS para uma aplicação no Azure de aplicação de serviço]
- [Dimensionar uma aplicação web no serviço de aplicação do Azure]
- [Princípios básicos de monitorização para Web Apps no serviço de aplicação do Azure]

<!-- URL List -->

[SignalR do ASP.NET]: http://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no serviço de aplicação do Azure]: ./web-sites-custom-domain-name.md
[Implementar a ambientes de transição para Web Apps no Azure de aplicação de serviço]: ./web-sites-staged-publishing.md
[Ativar HTTPS para uma aplicação no Azure de aplicação de serviço]: ./web-sites-configure-ssl-certificate.md
[Como: monitorizar o estado de ponto final da web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Princípios básicos de monitorização para Web Apps no serviço de aplicação do Azure]: ./web-sites-monitor.md
[modo de pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar uma aplicação web no serviço de aplicação do Azure]: ./web-sites-scale.md
[socket.IO]: ./web-sites-nodejs-chat-app-socketio.md
[Experimente a aplicação de serviço]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
