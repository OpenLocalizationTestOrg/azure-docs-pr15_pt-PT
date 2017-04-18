<properties 
    pageTitle="Funcionalidade de sistema operativo numa aplicação de serviço do Azure" 
    description="Saiba mais sobre a funcionalidade do SO disponível para a web apps, back-ends de aplicação móvel e apps de API na aplicação de serviço do Azure" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/01/2016" 
    ms.author="cephalin"/>

# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidade de sistema operativo numa aplicação de serviço do Azure #

Este artigo descreve as funcionalidades de sistema operativo do plano base comuns que está disponível para todas as aplicações com o serviço de [aplicação](http://go.microsoft.com/fwlink/?LinkId=529714)do Azure. Esta funcionalidade inclui o ficheiro, rede e acesso ao registo e registos de diagnóstico e eventos. 

<a id="tiers"></a>
## <a name="app-service-plan-tiers"></a>Camadas de plano de serviços de aplicação

Aplicação de serviço é executado aplicações de cliente num ambiente de alojamento do inquilino com várias. Aplicações implementadas as camadas **Free** e **partilhado** ser executada em processos de trabalho em máquinas virtuais partilhadas, enquanto aplicações implementadas a **padrão** **Premium** camadas e executar no virtual machine(s) dedicada especificamente para as aplicações associadas a um único cliente.

Uma vez que a aplicação de serviço de suportar uma experiência de dimensionamento totalmente integrada entre diferentes camadas, a configuração de segurança imposta para as aplicações de serviço de aplicação se mantém o mesmo. Este procedimento assegura que as aplicações não inesperadamente se comportam de forma diferente, a falhar de formas inesperadas, quando muda de plano de serviço de aplicação a partir de uma camada para outra.

<a id="developmentframeworks"></a>
## <a name="development-frameworks"></a>Quadros de desenvolvimento

Camadas de preços de aplicação de serviço controlarem a quantidade de cluster recursos (CPU, armazenamento em disco, memória e saída de rede) disponíveis para aplicações. No entanto, boca de funcionalidade framework disponível para aplicações permanece a mesma, independentemente das camadas dimensionamento.

Aplicação de serviço suporta uma variedade de desenvolvimento quadros, incluindo ASP.NET, ASP clássico, node.js, PHP e Python - as quais executar como extensões no IIS. Para simplificar e normalizar configuração de segurança, aplicações de serviço de aplicação executar, normalmente, os quadros de desenvolvimento vários com as predefinições. Uma abordagem para configurar as aplicações poderia ter sido para personalizar a superfície de API e a funcionalidade para cada quadro de desenvolvimento individuais. Aplicação de serviço leva em vez disso, uma abordagem mais genérica ao ativar um plano base comuns de funcionalidade do sistema operativo, independentemente do quadro de desenvolvimento de uma aplicação.

As secções seguintes resumem gerais tipos de funcionalidade do sistema operativo disponível para as aplicações de serviço de aplicação.

<a id="FileAccess"></a>
##<a name="file-access"></a>Acesso ao ficheiro

Existem várias unidades em serviço de aplicação, incluindo unidades locais e unidades de rede.

<a id="LocalDrives"></a>
### <a name="local-drives"></a>Unidades locais

Na sua essência, a aplicação de serviço é um serviço em execução na parte superior de infraestrutura do Azure PaaS (plataforma como um serviço). Como resultado, as unidades locais que são "anexadas" para uma máquina virtual são os mesmos tipos de unidade disponíveis para qualquer função de trabalho em execução no Azure. Isto inclui uma unidade de sistema operativo (a unidade D:\), uma unidade de aplicação que contém o pacote de Azure cspkg ficheiros utilizado exclusivamente por aplicação de serviço (e inacessível aos clientes) e uma unidade de "utilizador" (a unidade C:\), cujo tamanho varia dependendo do tamanho da VM.

<a id="NetworkDrives"></a>
### <a name="network-drives-aka-unc-shares"></a>Unidades de rede (também conhecidos por partilha UNC)

Uma dos aspetos exclusivos da aplicação de serviço que torna a implementação de aplicações e manutenção simples é que todo o conteúdo do utilizador está armazenado num conjunto de partilhas UNC. Este modelo de mapas muito corretamente ao padrão de comuns de armazenamento conteúdo utilizado pelo ambientes que tenham vários servidores de balanceamento de carga de alojamento na web no local. 

Dentro de serviço de aplicação, existem número de partilhas UNC criadas cada centro de dados. Uma percentagem do conteúdo para todos os clientes no Centro de dados de cada utilizador que é atribuída para cada partilha UNC. Além disso, todo o conteúdo para a subscrição de um único cliente é colocada sempre no mesmo UNC de ficheiro partilhar. 

Tenha em atenção que devido como funcionam os serviços de nuvem, máquina virtual específica responsável pela alojamento uma partilha UNC irá alterar ao longo do tempo. Se garante instaladas de partilhas UNC por máquinas virtuais diferentes à medida que são importados para cima e para baixo durante o período de operações de nuvem normal. Por este motivo, aplicações nunca devem certificar pressupostos codificado que as informações de máquina no caminho do ficheiro UNC permanecerá estáveis ao longo do tempo. Em vez disso, deve utilizar o conveniente *faux* caminho absoluto **D:\home\site** que fornece a aplicação de serviço. Este caminho de referências absoluto faux fornece um método portátil, desconhecido de aplicação e utilizador para se referir à aplicação de um própria. Ao utilizar **D:\home\site**, um pode transferir ficheiros partilhados a partir de uma aplicação para aplicação sem ter de configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>
### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de ficheiro acesso concedido para uma aplicação

Subscrição de cada cliente tem uma estrutura de directórios reservadas numa partilha UNC específica dentro de um centro de dados. Um cliente poderá ter várias aplicações criadas dentro de um centro de dados específicos, para que todos os directórios que pertencem a uma subscrição de cliente único são criados no mesmo UNC partilhar. Partilhar pode incluir directórios como aqueles para conteúdo, erro e registos de diagnóstico e versões anteriores da aplicação criada por controlo da origem. Como esperado, directórios de aplicação de um cliente estão disponíveis para leitura e de acesso de escrita o tempo de execução por código de aplicação a aplicação.

Nas unidades locais anexadas à máquina virtual que é executada uma aplicação, a aplicação de serviço de reserva um bloco de espaço na unidade de C:\ para específicos da aplicação de armazenamento local temporário. Embora uma aplicação tem acesso de leitura/escrita completo para o seu próprio armazenamento local temporário, esse armazenamento realmente não se destinam para ser utilizado diretamente pelo código da aplicação. Em vez disso, a intenção é fornecer o armazenamento de ficheiros temporários para o IIS e web quadros de aplicação. Aplicação de serviço também limita a quantidade de armazenamento local temporário disponível para cada aplicação para impedir que as aplicações individuais consumir excessivas quantidades de armazenamento de ficheiros locais.

Dois exemplos de como a aplicação de serviço de utiliza armazenamento local temporário são o diretório ficheiros temporários ASP.NET e o diretório IIS ficheiros comprimidos. O sistema de compilação do ASP.NET utiliza o directório de "Ficheiros temporários ASP.NET" como uma localização de cache de compilação temporário. IIS utiliza o diretório de "IIS temporário comprimidos" para armazenar resposta comprimida saída. Ambos um destes tipos de ficheiro a utilização (, bem como outras pessoas) novamente mapeados na aplicação de serviço de armazenamento de local temporário-app. Este remapeamento garante que a funcionalidade continua conforme esperado.

Cada aplicação na aplicação de serviço é executado como uma identidade de processo aleatório trabalhador com poucos privilégios exclusivos denominada "identidade do conjunto aplicacional", ainda mais descrita aqui: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Código de aplicação utiliza esta identidade para básico acesso só de leitura para a unidade de sistema operativo (a unidade D:\). Isto significa que o código de aplicação pode listar estruturas de diretório comuns e ler ficheiros comuns na unidade de sistema operativo. Apesar de esta poderá aparecer ser um nível de um pouco abrangente do access, mesmo diretórios e ficheiros são acessíveis quando que aprovisionar uma função de trabalho num Azure alojado serviço e ler os conteúdos da unidade. 

<a name="multipleinstances"></a>
### <a name="file-access-across-multiple-instances"></a>Acesso ao ficheiro em várias instâncias de múltiplos

O diretório contém os conteúdos de uma aplicação e, pode escrever código da aplicação para o mesmo. Se uma aplicação é executado em várias instâncias, o diretório é partilhado entre todas as instâncias para que todas as instâncias ver do diretório do mesmo. Por isso, por exemplo, se uma aplicação guarda ficheiros carregados para o diretório, os ficheiros estão imediatamente disponíveis a todas as instâncias. 

<a id="NetworkAccess"></a>
## <a name="network-access"></a>Acesso de rede
Código de aplicação pode utilizar em TCP/IP e protocolos UDP com base para tornar saída ligações à Internet acessíveis os pontos finais que expõem serviços externos à rede. Aplicações podem utilizar estes mesmos protocolos para ligar aos serviços do Azure e #151; por exemplo, estabelecendo ligações HTTPS para base de dados SQL.

Também existe uma funcionalidade limitada para as aplicações estabelecer uma ligação de ciclo de retorno local e tiver uma aplicação escutar esse socket do ciclo de retorno local. Esta funcionalidade não existe principalmente para ativar as aplicações que escutam ciclo de retorno local sockets como parte da respetiva funcionalidade. Tenha em atenção que cada aplicação vê uma ligação de loopback "privado"; aplicação "A" não consegue ouvir um socket do ciclo de retorno local criado pela aplicação "B".

Pipes nomeados também são suportados como um dispositivo de comunicação entre processos (IPC) entre diferentes processos que constituem executar uma aplicação. Por exemplo, o módulo do IIS FastCGI baseia-se em pipes nomeados para coordenar os processos individuais que são executadas páginas PHP.


<a id="Code"></a>
## <a name="code-execution-processes-and-memory"></a>Execução de código, processos e memória
Como mencionado anteriormente, aplicações executadas dentro de processos de trabalho de baixa privilegiados utilizando uma identidade do conjunto aplicacional aleatório. Código de aplicação tem acesso ao espaço de memória associado com o processo de trabalho, bem como qualquer subprocessos que podem ser gerados por processos CGI ou outras aplicações. No entanto, uma aplicação não consegue aceder a memória ou os dados de outra aplicação mesmo se for no mesmo computador virtual.

Aplicações podem executar scripts ou páginas escritas com quadros de desenvolvimento web suportado. Aplicação de serviço não configurar quaisquer definições de framework web para modos mais restritos. Por exemplo, aplicações ASP.NET ser executada numa aplicação de serviço de ser executada em fidedignidade "completa" em vez de um modo fidedignidade mais restrito. Quadros de Web, incluindo clássica ASP tanto ASP.NET, podem ligar componentes do COM no processo (mas não fora de componentes do processo) como ADO (ActiveX Data Objects) que estão registados por predefinição no sistema operativo Windows.

Aplicações podem expandir e executar código arbitrário. É permitido para uma aplicação fazer coisas como geração shell de comandos ou executar um script PowerShell. No entanto, apesar de execução de código arbitrária e processos podem ser gerados a partir de uma aplicação, executáveis programas e scripts são ainda restritos para os privilégios atribuídos ao conjunto de aplicações principal. Por exemplo, uma aplicação pode expandir a um ficheiro executável que faz uma chamada de saída HTTP, mas que o mesmo executável não pode tentar desassociar o endereço IP de uma máquina de virtual a partir do seu NIC. Fazer uma chamada de rede de saída é permitido ao código de baixa privilegiados, mas a tentar reconfigurar as definições de rede num computador virtual requer privilégios administrativos.


<a id="Diagnostics"></a>
## <a name="diagnostics-logs-and-events"></a>Registos de diagnóstico e eventos
Informações de registo são outro conjunto de dados que algumas aplicações tentarem aceder. Os tipos de informações de registo disponíveis para a execução na aplicação de serviço de código inclui diagnóstico e registar informações geradas por uma aplicação que também está acessível facilmente para a aplicação. 

Por exemplo, registos de W3C HTTP gerados por uma aplicação do active estão disponíveis no directório registo na localização de partilha de rede criada para a aplicação, ou disponíveis no armazenamento de BLOBs se um cliente tiver configurado W3C registo ao armazenamento. A opção último permite grandes quantidades de registos para reuniu sem o risco de excedem os limites de armazenamento do ficheiro associados uma partilha de rede.

Num vein semelhante, informações de diagnóstico em tempo real a partir das aplicações do .NET também podem ser registadas utilizando o rastreio .NET e infraestrutura de diagnóstico, com as opções para escrever as informações de rastreio a partilha de rede a aplicação, ou em alternativa a uma localização de armazenamento de Blobs.

Áreas de diagnósticos do registo e rastreio que não estão disponíveis para aplicações são eventos do Windows ETW e comuns registos de eventos do Windows (por exemplo, sistema, a aplicação e a segurança registos dos eventos). Uma vez que as informações de rastreio ETW potencialmente podem ser visto globais (com ACL direita), estão bloqueados leitura e de acesso de escrita para os eventos ETW. Os programadores poderão reparar que chamadas à API para ler e escrever ETW eventos e registos dos eventos do Windows comuns são apresentados trabalhar, mas se ao facto de aplicação de serviço é "faking" as chamadas para que sejam apresentados ser concluída com êxito. Na realidade, o código da aplicação não tem acesso aos dados evento.

<a id="RegistryAccess"></a>
## <a name="registry-access"></a>Acesso ao registo
Aplicações tem acesso só de leitura para a maior parte (apesar de não todos) do registo da máquina virtual estiverem em execução no. Na prática, isto significa que chaves de registo que permita o acesso só de leitura ao grupo de utilizadores local são acessíveis por aplicações. Uma área do registo que não é atualmente suportada para ler ou acesso de escrita é o HKEY\_atual\_ramo de utilizadores.

Acesso de escrita para o registo está bloqueado, incluindo o acesso a quaisquer chaves de registo por utilizador. Da perspetiva a aplicação, acesso de escrita para o registo deve nunca confiar no ambiente do Azure uma vez que aplicações podem (ou fazer) são migrados em máquinas virtuais diferentes. O armazenamento gravável apenas persistente que pode ser dependentes por uma aplicação é a estrutura de diretório de conteúdo de por aplicação armazenada em partilhas de UNC de serviço de aplicação. 

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 
