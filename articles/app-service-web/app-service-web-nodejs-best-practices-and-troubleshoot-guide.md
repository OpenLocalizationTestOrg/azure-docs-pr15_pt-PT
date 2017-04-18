<properties
    pageTitle="Melhores práticas e guia de resolução de problemas para aplicações nó sobre aplicações Web do Azure"
    description="Aprenda as melhores práticas e passos de resolução de problemas para as aplicações de nó no Azure Web Apps."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="ranjithr"
    manager="wadeh"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="06/06/2016"
    ms.author="ranjithr;wadeh"/>
    
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Melhores práticas e guia de resolução de problemas para aplicações nó sobre aplicações Web do Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Neste artigo, irá aprender as melhores práticas e os passos de resolução de problemas para [aplicações nó](app-service-web-nodejs-get-started.md) em execução no Azure Webapps (com [iisnode](https://github.com/azure/iisnode)).

>[AZURE.WARNING] Tenha cuidado quando utilizar os passos de resolução de problemas no seu site de produção. Recomendação é resolver a sua aplicação numa configuração não produção por exemplo a ranhura transição e quando o problema é corrigido, trocar seu ranhura transição com o seu ranhura de produção.

## <a name="iisnode-configuration"></a>Configuração de IISNODE

Este [ficheiro de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra todas as definições que podem ser configuradas para iisnode. Algumas das definições que serão útil para a sua aplicação são:

* nodeProcessCountPerApplication

    Esta definição controla o número de processos de nó que são iniciadas por aplicação IIS. Valor predefinido é 1. Pode iniciar o do node.exe tantas como a contagem de core VM por esta definição para 0. Valor recomendado é 0 para a maioria dos aplicação para que podem utilizar todos os tarolos no seu computador. NODE.exe é única por tópicos para que um node.exe irá consumir um máximo de 1 core e para obter um desempenho máximo partido da sua aplicação de nó pretendido para utilizar todos os tarolos.

* nodeProcessCommandLine

    Esta definição controla o caminho para o node.exe. Pode definir este valor para apontar para a sua versão de node.exe.

* maxConcurrentRequestsPerProcess

    Esta definição controla o número máximo de pedidos em simultâneo enviadas por iisnode para cada node.exe. No azure webapps, o valor predefinido para que esta está infinito. Não terá de se preocupar esta definição. Fora do azure webapps, o valor predefinido é 1024. Poderá pretender configurar esta dependendo quantas solicitações que obtém a sua aplicação e como a mesma rapidez com a aplicação processa cada pedido.

* maxNamedPipeConnectionRetry

    Esta definição controla o número máximo de vezes iisnode volte a tentar fazer ligação no pipes para enviar o pedido sobre para node.exe. Esta definição em conjunto com namedPipeConnectionRetryDelay determina o limite de tempo total de cada pedido dentro iisnode. Valor predefinido é 200 no Azure Webapps. Total de tempo limite em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

* namedPipeConnectionRetryDelay

    Esta definição controla a quantidade de tempo (ms) iisnode irá aguardar entre cada tentativa para enviar o pedido para node.exe sobre o pipe nomeado. Valor predefinido é 250ms.
    Total de tempo limite em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

    Por predefinição, o limite de tempo total no iisnode no azure webapps é 200 \* 250ms = 50 segundos.

* logDirectory

    Esta definição controla o diretório onde iisnode iniciará sessão stdout/stderr. Valor predefinido é iisnode que é relativo o diretório de script principal (diretório onde está presente server.js principal)

* debuggerExtensionDll

    Esta definição controla que versão do inspector nó iisnode irá utilizar quando depurar sua aplicação de nó. Atualmente iisnode-inspector-0.7.3.dll e iisnode inspector.dll são os valores válidos apenas 2 para que esta definição. Valor predefinido é iisnode-inspector-0.7.3.dll. versão iisnode-inspector-0.7.3.dll utiliza nó-inspector-0.7.3 e utiliza websockets, por isso, é necessário ativar websockets no seu azure Web App para utilizar esta versão. Consulte o artigo <http://www.ranjithr.com/?p=98> para obter mais detalhes sobre como configurar iisnode para utilizar o inspector de nó novo.

* flushResponse

    O comportamento predefinido do IIS é que-serve de memória intermédia dados de resposta para cima para 4MB antes de esvaziar ou até ao fim da resposta, que vier primeiro. iisnode oferece uma definição de configuração para substituir este comportamento: para esvaziar um fragmento do corpo da entidade de resposta assim iisnode recebe-lo de node.exe, tem de definir o iisnode/@flushResponse atributo na Web. config como 'true':
    
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```

    Ativar esvaziar de cada fragmento do corpo da entidade de resposta adiciona uma sobrecarga de desempenho que reduz o débito do sistema, ~ 5% (partir v0.1.13), por isso, é melhor esta definição apenas para os pontos finais que requerem a transmissão de resposta do âmbito (por exemplo, utilizando o <location> elemento na Web. config)

    Para além disso, para a transmissão de aplicações, terá também definir responseBufferLimit da sua processador iisnode como 0.
    
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```

* watchedFiles

    Esta é uma lista de ponto e vírgula separada de ficheiros que irá observadas para as alterações. Uma alteração a um ficheiro faz com que a aplicação a Reciclagem. Cada entrada é composta por um nome de directório opcional e o nome de ficheiro necessário que são relativamente às diretório onde está localizado o ponto de entrada de aplicação principal. Caracteres universais são permitidos na apenas a parte de nome de ficheiro. Valor predefinido é "\*. js;web.config"

* recycleSignalEnabled

    Valor predefinido é falso. Se ativada, a aplicação de nó pode ligar a um pipe nomeado (variável de ambiente IISNODE\_controlo\_PIPE) e enviar uma mensagem "Reciclagem". Isto vai originar w3wp a Reciclagem correctamente.

* idlePageOutTimePeriod

    Valor predefinido é 0, que significa que esta funcionalidade está desativada. Quando definir algum valor maior que 0, iisnode irá página saída todos os seus processos de subordinados cada milissegundos 'idlePageOutTimePeriod'. Para compreender a página que saída meios, consulte a [documentação](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx). Esta definição será útil para as aplicações que consumam muitas memória e pretende, ocasionalmente, a memória pageout disco para libertar algumas RAM.

>[AZURE.WARNING] Tenha cuidado quando activar as seguintes definições de configuração no aplicações de produção. Recomendação é para não ativá-las em aplicações de produção direto.

* debugHeaderEnabled

    O valor predefinido é falso. Se definida como VERDADEIRO, iisnode irá adicionar cabeçalho de resposta um HTTP iisnode-depuração para cada resposta do HTTP envia que o valor do cabeçalho iisnode depuração é um URL. Peças individuais de informações de diagnóstico podem ser obtidas verificando o fragmento de URL, mas é obtida uma visualização muito melhor abrindo o URL no browser.

* loggingEnabled

    Esta definição controla o registo de stdout e stderr por iisnode. Iisnode irá capturar stdout/stderr de processos de nó-inicia e escrita ao diretório especificado na definição de 'logDirectory'. Depois de ativar esta é, a aplicação irá estar a escrever os registos para o sistema de ficheiros e, consoante a quantidade de registo concluído pela aplicação, podem existir implicações no desempenho.

* devErrorsEnabled

    Valor predefinido é falso. Quando definida como VERDADEIRO, iisnode irá exibir o código de estado HTTP e o código de erro Win32 no seu browser. O código de win32 será útil nas depuração determinados tipos de problemas.
    
* debuggingEnabled (não ativar no site de produção direto)

    Esta definição controla a funcionalidade de depuração. Iisnode está integrado com o inspector de nó. Ao ativar esta definição, pode ativar a depuração da sua aplicação de nó. Assim que esta definição está ativada, iisnode irá os ficheiros de nó-inspector necessárias no directório de 'debuggerVirtualDir' no primeiro pedido depuração à aplicação nó de esquema. Pode carregar o inspector de nó ao enviar um pedido para http://yoursite/server.js/debug. Pode controlar o segmento de URL depuração com a definição de 'debuggerPathSegment'. Por predefinição debuggerPathSegment = 'Depurar'. Pode definir este num GUID por exemplo para que seja mais difícil de descoberta por outras pessoas.

    Selecione esta [ligação](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre depuração.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/resolução de problemas

### <a name="my-node-application-is-making-too-many-outbound-calls"></a>Está a minha aplicação nó fazer chamadas de saída demasiadas.

Muitas aplicações pretender fazer ligações de saída como parte do seu funcionamento normal. Por exemplo, quando um pedido de é recebida, a aplicação de nó seria querer contacte um REST API noutro local e obter algumas informações para processar o pedido. Pretende de utilizar um agente do activo manter quando efetuar chamadas de http ou https. Por exemplo, pode utilizar o módulo agentkeepalive como seu agente vivo manter quando efetuar estas chamadas de saída. Isto torna-se de que os sockets são reutilizados no seu azure Web App VM e reduzindo o tempo de criação de novos sockets para todos os pedidos de saída. Além disso, isto torna-se de que estiver a utilizar o menor número de sockets para efetuar muitas pedidos de saída e, consequentemente, não exceda o ' maxsockets ' que está atribuídos por VM. Recomendação sobre Azure Webapps seria definir o valor de ' maxsockets ' agentKeepAlive para um total de 160 sockets por VM. Isto significa que se tiver node.exe 4 em execução na VM, teria pretende definir a ' maxsockets agentKeepAlive ' 40 por node.exe que é 160 total por VM.

Exemplo de configuração do agentKeepALive:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Este exemplo assume que tem node.exe 4 em execução no seu VM. Se tiver um número diferente de node.exe em execução na VM, terá de modificar o ' maxsockets ' definição em conformidade.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>Está a minha aplicação nó consumir demasiado CPU.

Provavelmente, vai obter uma recomendação a partir do Azure Webapps no seu portal sobre consumo de cpu alta. Também pode configurar monitores para ver para determinados [métricas](web-sites-monitor.md). Quando verifica a utilização da CPU no [Azure Portal Dashboard](../application-insights/app-insights-web-monitor-performance.md), verifique os valores de máximo CPU modo a não perder saída os valores de pico.
Em casos onde pensa que está a sua aplicação consumir demasiado CPU e não é possível explicar o motivo, terá da aplicação de nó do perfil.

### 

#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>A aplicação de nó no azure webapps com V8-gerador de perfis de criação de perfis

Por exemplo, apresentamos lhe permite ter uma aplicação do mundo Olá ao qual pretende perfil conforme apresentado abaixo:

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

Aceda ao seu SMS site https://yoursite.scm.azurewebsites.net/DebugConsole

Verá uma linha de comandos conforme apresentado abaixo. Ir para o seu diretório de site/wwwroot

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando "npm instalar gerador de perfis v8"

Isto deve instalar v8-gerador de perfis em nó\_diretório módulos e todas as suas dependências.
Agora, edite o seu server.js para criar um perfil da aplicação.

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

As alterações acima serão a função WriteConsoleLog de perfil e, em seguida, escrever a saída de perfil para ficheiro 'profile.cpuprofile' em seu wwwroot do site. Envie um pedido para a sua aplicação. Irá ver um ficheiro 'profile.cpuprofile' criou em seu wwwroot do site.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Transfira este ficheiro e terá de abrir este ficheiro com ferramentas de F12 Chrome. Acertar F12 no chrome, em seguida, clique no "Perfis separador". Clique no botão "Carga". Selecione o ficheiro de profile.cpuprofile que acabou de transferir. Clique no perfil que acabou de ser carregado.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Verá que 95% das vezes foi consumida por função WriteConsoleLog conforme apresentado abaixo. Isto também mostra-lhe os números de linha exata e ficheiros de origem que causaram o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Está a minha aplicação nó consumir demasiado memória.

Provavelmente, vai obter uma recomendação a partir do Azure Webapps no seu portal sobre consumo de memória alta. Também pode configurar monitores para ver para determinados [métricas](web-sites-monitor.md). Quando verifica a utilização da memória no [Azure Portal Dashboard](../application-insights/app-insights-web-monitor-performance.md), verifique os valores de máximo de memória modo a não perder saída os valores de pico.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>Deteção de perda e num diff de pilha para node.js 

Pode utilizar [nó memwatch](https://github.com/lloyd/node-memwatch) para ajudá-lo a identificar fugas de memória.
Pode instalar memwatch tal como gerador de perfis v8 e editar o seu código para capturar e detecção de diferenças pilhas para identificar a memória fugas na sua aplicação.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Meu node.exe são introdução sacrificados aleatoriamente 

Existem algumas razões porque é que isto poderá estar a ter:

1.  A aplicação é deitar exceções não identificadas – Please verificação d\\casa\\ficheiros de registo\\aplicação\\ficheiro de registo errors.txt para obter os detalhes no excepção iniciada. Este ficheiro tem o rastreio da pilha para corrigir a aplicação com base neste.

2.  A aplicação está a consumir demasiado memória qual é a afetar outros processos de introdução. Se o valor total memória VM ficar próximo 100%, o node.exe pode ser cancelada pelo Gestor de processo para permitir que outros processos a oportunidade de efetuar algum do trabalho. Para corrigir, certifique-se a que sua aplicação não tem pouca memória ou se a aplicação realmente precisa de utilizar muitas memória, consulte Dimensionar para uma maior VM com muitas mais RAM.

### <a name="my-node-application-does-not-start"></a>Não iniciar a minha aplicação nó

Se a sua aplicação está a devolver 500 erros no arranque, podem existir algumas razões:

1.  NODE.exe não está presente no local correto. Verifique nodeProcessCommandLine definição.

2.  Ficheiro de script principal não é presente no local correto. Verifique a Web. config e certifique-se de que o nome do ficheiro de script principal na secção processadores corresponde ao ficheiro de script principal.

3.  Configuração da Web. config não está correcta – veja os nomes de definições/valores.

4.  Iniciar fria – a aplicação está a demorar demasiado tempo a arranque. Se a sua aplicação demora mais tempo do que (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 segundos, iisnode devolverão 500 Erro. Aumente os valores destas definições para corresponder ao seu hora de início de aplicação para impedir que iisnode de esgotar o tempo limite e devolver o erro 500.

### <a name="my-node-application-crashed"></a>Minha aplicação nó falhada

A aplicação é deitar exceções não identificadas – Please verificação d\\casa\\ficheiros de registo\\aplicação\\ficheiro de registo errors.txt para obter os detalhes no excepção iniciada. Este ficheiro tem o rastreio da pilha para corrigir a aplicação com base neste.

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>Minha aplicação nó demora demasiado tempo a arranque (fria de início)

Mais comuns motivo para isto é que a aplicação tem muitas ficheiros no nó\_módulos e a aplicação tenta carregar a maior parte destes ficheiros durante o arranque. Por predefinição, uma vez que os seus ficheiros residem na partilha de rede no Azure Webapps, carregar ficheiros tantos pode demorar algum tempo.
Algumas soluções para torná-lo mais rápido são:

1.  Certifique-se de que tem uma estrutura de dependência simples e não dependências duplicadas utilizando npm3 para instalar os módulos.

2.  Experimente preguiça para carregar o nó do seu\_módulos e não carregar todos os módulos no arranque. Isto significa que a chamada para require('module') deve ser feita quando precisar realmente dentro da função que tentar utilizar o módulo.

3.  Azure Webapps oferece uma funcionalidade denominada local cache. Esta funcionalidade copia o conteúdo da partilha de rede para o disco local na VM. Uma vez que os ficheiros são locais, o tempo de carregamento de nó\_módulos é muito mais rápida. -Este [documentação](../app-service/app-service-local-cache.md) explica como utilizar a Local Cache mais detalhadamente.

## <a name="iisnode-http-status-and-substatus"></a>Estado de http IISNODE e subestado

Este [ficheiro de origem](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) lista todos o iisnode de combinação de estado/subestado possíveis pode devolver em caso de erro.

Ativar FREB para a sua aplicação ver o código de erro win32 (Certifique-se de que ativa FREB apenas nos sites de que não sejam produção por motivos de desempenho).

| Estado de HTTP | Subestado http | Razão possível?                                                                                                                                                                                            
|-------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------
| 500         | 1000           | Ocorreu algum problema despacho o pedido para IISNODE – veja se node.exe foi iniciado para cima. NODE.exe poderia ter falhado no arranque. Verifique a configuração da Web. config erros.                                                                                                                                                                                                                                                                                     |
| 500         | 1001           | -Win32Error 0x2 - aplicação não está a responder para o URL. Verifique as regras de reescrita de URL ou se a aplicação express tem as rotas corretas definidas. -Está ocupado Win32Error 0x6d – pipe nomeado – Node.exe não está a aceitar pedidos porque o pipe está ocupado. Verificar a utilização da cpu alta. -Outros erros de – Verifique se node.exe falhou.
| 500         | 1002           | NODE.exe falhou – veja d:\\casa\\ficheiros de registo\\registo-errors.txt para rastreio da pilha.                                                                                                                                                                                                                                                                                                                                                                                        |
| 500         | 1003           | Encaminhar configuração problema – nunca deverá ver isto mas se fizer, a configuração de pipes estiver incorreta.                                                                                                                                                                                                                                                                                                                                                          |
| 500         | 1004-1018 forem apresentados      | Ocorreu algum erro ao enviar o pedido ou a resposta para a node.exe de processamento. Verifique se node.exe falhou. verificar a d:\\casa\\ficheiros de registo\\registo-errors.txt para rastreio da pilha.                                                                                                                                                                                                                                                                                    |
| 503         | 1000           | Não existe memória suficiente para atribuir mais ligações por pipe com nome. Por que motivo está a sua aplicação consumir memória muito de verificação. Selecione o valor da definição maxConcurrentRequestsPerProcess. Se sua não infinito e tem muitas pedidos, aumentar este valor para evitar este erro.                                                                                                                                                                                                                                                                                                                  |
| 503         | 1001           | Não foi possível emitir pedido node.exe porque a aplicação é Reciclagem. Após a aplicação tem reciclados, pedidos devem ser fornecidos normalmente.                                                                                                                                                                                                                                                                                                               |
| 503         | 1002           | Não foi possível emitir o código de erro de win32 de verificação por motivo real – pedido para um node.exe.                                                                                                                                                                                                                                                                                                                                                                               |
| 503         | 1003           | Pipe nomeado está ocupado – veja se nó está a consumir muitas CPU                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Existe uma definição de NODE.exe denominado nó\_pendente\_PIPE\_instâncias. Por predefinição fora do azure webapps este valor é 4. Isto significa que essa node.exe só pode aceitar 4 pedidos cada vez no pipe nomeado. No Azure Webapps, este valor definido para 5000 e este valor deve ser está suficientemente bom para a maioria dos nó aplicações em execução no azure webapps. Deverá visualizar não 503.1003 no azure webapps porque temos um valor alto para o nó\_pendente\_PIPE\_instâncias.  |

## <a name="more-resources"></a>Mais recursos

Siga estas ligações para saber mais sobre aplicações node.js na aplicação de serviço de Azure.

* [Introdução ao Node.js web apps no serviço de aplicação do Azure](app-service-web-nodejs-get-started.md)
* [Como depurar uma aplicação web do Node.js na aplicação de serviço do Azure](web-sites-nodejs-debug.md)
* [Utilização Node.js módulos com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure de aplicação de serviço Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro de programadores do node.js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar a consola de depuração Kudu Super secreta](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)