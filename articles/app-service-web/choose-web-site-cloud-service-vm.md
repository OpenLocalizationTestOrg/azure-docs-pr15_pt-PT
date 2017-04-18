<properties
    pageTitle="Comparação de aplicação de serviço, máquinas virtuais, ferro de serviço e serviços em nuvem Azure | Microsoft Azure"
    description="Saiba como escolher entre a aplicação de serviço de Azure, máquinas virtuais, ferro de serviço e serviços em nuvem para alojar aplicações web."
    services="app-service\web, virtual-machines, cloud-services"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2016"
    ms.author="tdykstra"/>

# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparação de aplicação de serviço, máquinas virtuais, ferro de serviço e serviços em nuvem Azure

## <a name="overview"></a>Descrição geral

Azure oferece várias formas de sites de web do anfitrião: [Aplicação de serviço de Azure][], [máquinas virtuais][], [Ferro de serviço][]e [Serviços em nuvem][]. Este artigo ajuda-o a compreender as opções e faça a escolha correta para a aplicação web.

Aplicação de serviço de Azure é a melhor escolha para a maior parte dos web apps. Implementação e gestão sejam integrados na plataforma, sites podem rapidamente Dimensionar para processar cargas de tráfego alta e o Gestor de tráfego e balanceamento de carga incorporados fornecer disponibilidade elevada. Pode mover a sites existentes para a aplicação de serviço de Azure facilmente com uma [ferramenta de migração online](https://www.migratetoazure.net/), utilize uma aplicação do código fonte do aberto a partir da Galeria de aplicação Web ou criar um novo site com as ferramentas da sua preferência e quadro. A funcionalidade de [WebJobs][] torna mais fácil de adicionar a tarefa em segundo plano processamento para a sua aplicação do serviço de aplicação web.

Serviço ferro é uma boa escolha se estiver a criar uma nova aplicação ou voltar a escrever uma aplicação existente para utilizar uma arquitetura de microservice. As aplicações, executar um conjunto de máquinas partilhado, podem iniciar pequenas e crescem em sequência para dimensionar grandes com centenas ou milhares de máquinas conforme necessário. Serviços com estado tornam mais fácil de forma consistente e fiável armazenar o estado da aplicação e serviço ferro gere automaticamente a partições de serviço, o dimensionamento e disponibilidade por si.  Serviço ferro também suporta WebAPI com a Interface de Web aberta para .NET (OWIN) e ASP.NET Core.  Em comparação com a aplicação de serviço, serviço ferro também fornece mais controlo sobre ou acesso direto aos, a infraestrutura de subjacente. Pode remoto para os servidores ou configurar tarefas de arranque do servidor. Serviços em nuvem é semelhante a ferro de serviço no grau de controlo versus facilidade de utilização, mas agora é um serviço de legado e serviço ferro é recomendado para o desenvolvimento de novo.

Se tiver uma aplicação existente que iria necessitar de alterações substanciais a executar o serviço de aplicação ou serviço ferro, pode escolher máquinas virtuais para simplificar a migrar para a nuvem. No entanto, corretamente configurar, proteger e mantendo VMs requer muito mais tempo e conhecimentos IT comparadas com o serviço de aplicação do Azure e ferro de serviço. Se estiver a considerar máquinas virtuais do Azure, certifique-se de que tomar em consideração o esforço de manutenção em curso necessário para correcção, atualizar e gerir o seu ambiente VM. Azure máquinas virtuais está infraestrutura-como-a-Service (IaaS), enquanto o serviço de aplicação e serviço ferro são plataforma-como-a-Service (Paas). 

## <a name="features"></a>Comparação de funcionalidades

A tabela seguinte compara as funcionalidades do serviço de aplicação, serviços em nuvem, máquinas virtuais e ferro de serviço para o ajudar a tornar a melhor escolha. Para obter informações sobre SLA para cada opção, consulte [Os acordos do nível de serviço Azure](/support/legal/sla/).

Funcionalidade|Aplicação de serviço (aplicações web)|Serviços em nuvem (funções web)|Máquinas virtuais|Serviço ferro|Notas
---|---|---|---|---|---
Junto à instantâneas implementação|X|||X|Implementar uma aplicação ou uma atualização de aplicação para um serviço na nuvem ou criando uma VM demora vários minutos, pelo menos, implementar uma aplicação para uma aplicação web demora segundos.
Dimensionar para maiores máquinas sem novo lançamento|X|||X|
Instâncias do servidor Web partilham conteúdos e configuração, o que significa que não tem de voltar a implementar ou reconfigurar à medida que dimensionar.|X|||X|
Vários ambientes de implementação (produção e transição)|X|X||X|Serviço ferro permite-lhe para ter vários ambientes para as suas aplicações ou para implementar versões diferentes do seu aplicação lado a lado.
Gestão de atualização automática do SO|X|X|||As atualizações automáticas do sistema operativo estão a ser planeadas para libertar um ferro serviço futuro.
Mudar de forma totalmente integrada plataforma (mover facilmente entre de 32 bits e 64 bits)|X|X|||
Implementar código com GIT, FTP|X||X||
Implementar código com implementar Web|X||X||Serviços em nuvem suporta a utilização da Web implementar implementar actualizações de instâncias de função individuais. No entanto, não pode utilizá-la para implementação inicial de uma função, e se implementar Web por uma atualização tem que implementar separadamente para cada instância de uma função. Várias instâncias são necessários para poder qualificar-me para o serviço de nuvem SLA para ambientes de produção.
Suporte de WebMatrix|X||X||
Acesso a serviços como o serviço Bus, armazenamento, base de dados SQL|X|X|X|X|
Web do anfitrião ou camada de serviços web de uma arquitetura de várias camadas|X|X|X|X|
Camada anfitrião de uma arquitetura de várias camadas|X|X|X|X|Aplicação de serviço web apps facilmente podem alojar uma camada REST API e a funcionalidade de [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) pode alojar o processamento de tarefas em segundo plano. Pode executar WebJobs num Web site dedicado para alcançar escalabilidade independente para a camada. A funcionalidade de [API aplicações](../app-service-api/app-service-api-apps-why-best-platform.md) de pré-visualização fornece ainda mais funcionalidades para restantes serviços de alojamento.
Suporte de MySQL-como-a-service integrado|X|X|X||Serviços em nuvem podem integrar o MySQL-como-a-service através do ofertas do ClearDB, mas não como parte do fluxo de trabalho Azure Portal.
Suporte para ASP.NET, clássica ASP, Node.js, PHP, Python|X|X|X|X|Serviço ferro suporta a criação de um web front-end utilizando [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) ou pode implementar qualquer tipo de aplicação (Node.js, Java, etc) como [Convidado executável](../service-fabric/service-fabric-deploy-existing-app.md).
Dimensionar saída para várias instâncias sem novo lançamento|X|X|X|X|Máquinas virtuais pode dimensionar saída para várias instâncias, mas os serviços em execução nos mesmos têm de ser escritos para processar este escala de saída. Tem de configurar um balanceador de carga para encaminhar pedidos ao longo de máquinas e criar um grupo de afinidade para impedir que reinicia simultânea de todas as instâncias devido a falhas de manutenção ou hardware.
Suporte para SSL|X|X|X|X|Aplicação de serviço das aplicações web, SSL para nomes de domínio personalizado só é suportada para o modo Basic e padrão. Para obter informações sobre como utilizar SSL web Apps, consulte o artigo [configurar um certificado SSL para um Web site Azure](../app-service-web/web-sites-configure-ssl-certificate.md).
Integração do Visual Studio|X|X|X|X|
Depuração remota|X|X|X||
Implementar código com TFS|X|X|X|X|
Isolamento de rede com a [Rede Virtual do Azure](/services/virtual-network/)|X|X|X|X|Consulte também [integração de rede Virtual Azure Web sites](/blog/2014/09/15/azure-websites-virtual-network-integration/)
Suporte para [O Gestor de tráfego Azure](/services/traffic-manager/)|X|X|X|X|
Monitorização de ponto final integrada|X|X|X||
Acesso de ambiente de trabalho remoto para servidores||X|X|X|
Instalar quaisquer MSI personalizado||X|X|X|Serviço ferro permite-lhe alojar qualquer ficheiro executável como [Convidado executável](../service-fabric/service-fabric-deploy-existing-app.md) ou pode instalar o qualquer aplicação as VMs.
Capacidade para definir/executar tarefas de arranque||X|X|X|
Pode ouvir os eventos ETW||X|X|X|

## <a name="scenarios"></a>Cenários e recomendações

Eis alguns cenários de aplicação comuns com recomendações Control qual Azure web alojamento opção poderá ser mais adequada para cada um.

- [Preciso de um web front end com fundo transformação e da base de dados back-end para executar aplicações empresariais integradas com o elementos local.](#onprem)
- [Precisa de uma forma fiável para alojar o meu Web site da empresa que se adapta bem e aceder a ofertas globais.](#corp)
- [Tenho de ter uma aplicação do IIS6 em execução no Windows Server 2003.](#iis6)
- [Sou proprietário de um pequenas empresas e preciso de uma forma dispendiosa para alojar o meu site, mas com futuro crescimento deve ter em conta.](#smallbusiness)
- [Sou um web ou de gráfico designer e quiser estruturar e construir web sites para os meus clientes.](#designer)
- [Estou a migrar minha aplicação várias camada com uma web front-end para a nuvem.](#multitier)
- [Depende da minha aplicação do Windows altamente personalizado ou Linux ambientes e pretende movê-lo na nuvem.](#custom)
- [O meu site utiliza o software de abrir origem e eu pretender alojá-lo no Azure.](#oss)
- [Posso ter uma aplicação de linha de negócio que necessita para ligar à rede empresarial.](#lob)
- [Pretende alojar uma REST API ou serviço web para clientes móveis.](#mobile)


### <a id="onprem"></a>Preciso de um web front end com fundo transformação e da base de dados back-end para executar aplicações empresariais integradas com o elementos local.

Azure de aplicação de serviço é uma excelente solução para aplicações empresariais complexos. Permite-lhe desenvolver aplicações que Dimensionar automaticamente numa plataforma desequilibradas carga, são protegidas com o Active Directory e ligue aos recursos no local. Faz a gestão de nessas aplicações fácil através de um portal de elevada e APIs e permite-lhe ganhar visão como clientes estão a utilizá-los com ferramentas de conhecimentos aprofundados de aplicação. A funcionalidade de [Webjobs][] permite-lhe executar processos em segundo plano e tarefas como parte do seu camada da web, enquanto híbrido conectividade e de funcionalidades VNET tornam mais fácil ligar novamente a recursos no local. Aplicação de serviço de Azure fornece SLA do três 9 para aplicações web e permite-lhe:

* Execute as aplicações de forma fiável numa plataforma nuvem reparação personalizada, correcção automática.
* Dimensionar automaticamente ao longo de uma rede global de centros de dados.
* Criar cópias de segurança e restaurar para recuperação de falhas.
* Estar ISO, SOC2 e PCI em conformidade.
* Integrar com o Active Directory

### <a id="corp"></a>Precisa de uma forma fiável para alojar o meu Web site da empresa que se adapta bem e aceder a ofertas globais.

Azure de aplicação de serviço é uma excelente solução para alojamento Web sites da empresa. Permite-web apps para dimensionar rapidamente e facilmente para satisfazer a procura através de uma rede global da centros de dados. Oferece alcance local, tolerância a falhas e gestão de tráfego inteligente. Tudo numa plataforma que fornece ferramentas de gestão de elevada, permitindo-lhe ganhar visão estado de funcionamento do site e o tráfego do site de forma rápida e fácil. Aplicação de serviço de Azure fornece SLA do três 9 para aplicações web e permite-lhe:

* Execute os Web sites públicos sujeito numa plataforma nuvem reparação personalizada, correcção automática.
* Dimensionar automaticamente ao longo de uma rede global de centros de dados.
* Criar cópias de segurança e restaurar para recuperação de falhas.
* Gerir os registos e tráfego com ferramentas integradas.
* Estar ISO, SOC2 e PCI em conformidade.
* Integrar com o Active Directory

### <a id="iis6"></a>Tenho de ter uma aplicação do IIS6 em execução no Windows Server 2003.

Aplicação de serviço de Azure torna mais fácil evitar os custos de infraestrutura associados migrar aplicações do IIS6 mais antigas. Microsoft criou [orientações de migração detalhadas e migração fáceis de utilizar ferramentas](https://www.movemetowebsites.net/) que permitem-lhe verificar a compatibilidade e identificar as alterações que precisam de ser efetuadas. Integração com o Visual Studio, TFS e ferramentas CMS comuns torna mais fácil de implementação de aplicações do IIS6 diretamente para a nuvem. Depois de implementado, o Portal de Azur fornece ferramentas de gestão robustos que permitem-lhe dimensionar para baixo para gerir custos e até reunir procura conforme necessário. Com a ferramenta de migração, pode:

* Forma rápida e fácil migre a sua aplicação da web Windows Server 2003 legada na nuvem.
* Optar ativamente por participar para deixar o seu anexado SQL da base de dados no local para criar uma aplicação híbrida.
* Mover automaticamente a sua base de dados do SQL juntamente com a sua aplicação legada.

### <a id="smallbusiness"></a>Sou proprietário de um pequenas empresas e preciso de uma forma dispendiosa para alojar o meu site, mas com futuro crescimento deve ter em conta.

Azure de aplicação de serviço é uma excelente solução para este cenário, uma vez que pode começar a utilizá-lo gratuitamente e, em seguida, adicionar mais funcionalidades quando deles necessita. Cada aplicação web gratuito vem com um domínio fornecido pela Azure (*your_company*. azurewebsites.net), e a plataforma inclui ferramentas de implementação e gestão integradas, bem como uma galeria de aplicação que tornam mais fácil começar a utilizar. Existem muitos outros serviços e opções de dimensionamento que permitir que o site evoluir com a procura de um aumento de utilizador. Com o serviço de aplicação do Azure, pode:

- Começar com a camada gratuita e, em seguida, dimensionar para cima, conforme necessário.
- Utilize a Galeria de aplicação para configurar as aplicações de web populares, tal como WordPress rapidamente.
- Adicione funcionalidades e serviços Azure adicionais para a sua aplicação, conforme necessário.
- Proteger a sua aplicação web com HTTPS.

### <a id="designer"></a>Sou um web ou designer gráfico e quiser estruturar e criar Web sites para os meus clientes

Para os programadores web e designers de sites, serviço de aplicação do Azure integra-se facilmente com uma variedade de quadros e ferramentas, inclui suporte de implementação para Git e FTP e oferece uma integração com ferramentas e serviços como o Visual Studio e base de dados SQL. Com a aplicação de serviço, pode:

- Utilizar ferramentas de linha de comandos para [tarefas automatizadas][scripting].
- Trabalhar com populares idiomas como [.net][dotnet], [PHP][], [Node.js][nodejs]e [Python][].
- Selecione três níveis diferentes de dimensionamento para escalar para cima para capacidades muito alta.
- Integrar com outros serviços do Azure, tal como a [Base de dados SQL][sqldatabase], [Serviço Bus] [ servicebus] e [armazenamento][]ou ofertas de parceiro a partir da [Loja do Azure][azurestore], tal como MySQL e MongoDB.
- Integre com ferramentas, como o Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Estou a migrar a minha aplicação várias camada com uma web front-end na nuvem

Se estiver a executar uma aplicação de várias camada, tal como um servidor web que liga a uma base de dados, o serviço de aplicação do Azure é uma boa opção oferece uma integração com base de dados do SQL Azure. E pode utilizar a funcionalidade de WebJobs para back-end processos em execução.

Escolher serviço ferro para um ou mais dos seus camadas se precisar de mais controlam sobre o ambiente de servidor, tais como a capacidade de remoto no seu servidor ou configurar tarefas de arranque do servidor.

Selecione máquinas virtuais de um ou mais dos seus camadas se pretender utilizar a sua própria imagem de máquina ou executar software de servidor ou serviços que não é possível configurar o serviço ferro.

### <a id="custom"></a>Depende da minha aplicação do Windows altamente personalizado ou Linux ambientes e pretende movê-lo na nuvem.

Se a sua aplicação requer complexa instalação ou configuração de software e o sistema operativo, máquinas virtuais está, provavelmente, a melhor solução. Com máquinas virtuais, pode:

- Utilize a Galeria de Máquina Virtual para começar com um sistema operativo, tal como o Windows ou Linux e, em seguida, personalizá-lo para aos requisitos da aplicação.
- Criar e carregar uma imagem personalizada de um servidor no local existente para ser executado numa máquina virtual no Azure.

### <a id="oss"></a>O meu site utiliza o software de abrir origem e eu pretender alojá-lo no Azure

Se o seu quadro abrir origem é suportado no serviço de aplicação, os idiomas e quadros necessários a sua aplicação são configurados automaticamente para si. Serviço de aplicação permite-lhe:

- Utilizar vários populares abrir origem idiomas, tais como [.NET][dotnet], [PHP][], [Node.js][nodejs]e [Python][].
- Configure o WordPress, Drupal, Umbraco, DNN e muitas outras aplicações de terceiros web.
- Migrar uma aplicação existente ou crie um novo a partir da Galeria de aplicação.

Se sua framework abrir origem não é suportado no serviço de aplicação, pode executá-la das outras web Azure que aloja opções. Com máquinas virtuais, instalar e configurar o software a imagem do computador, que pode ser Windows ou baseado em Linux.

### <a id="lob"></a>Tenho de ter uma aplicação de linha de negócio que necessita para ligar à rede empresarial

Se pretender criar uma aplicação de linha de negócio, o seu Web site poderá implicar acesso direto aos serviços ou dados na rede da empresa. Isto é possível na aplicação de serviço, ferro de serviço e máquinas virtuais utilizando o [serviço de rede Virtual Azure](/services/virtual-network/). Serviço de aplicação pode utilizar a [funcionalidade de integração de VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), que permite que as aplicações Azure para executarem como se fossem na rede da sua empresa.

### <a id="mobile"></a>Eu pretender alojar uma REST API ou serviço web para clientes móveis

Serviços web baseadas em HTTP permitem-lhe suportar uma variedade de clientes, incluindo os clientes móveis. Quadros como ASP.NET Web API integram com o Visual Studio para que seja mais fácil criar e consumir restantes serviços.  Estes serviços são expostos a partir de um ponto final de web, por isso, é possível utilizar qualquer alojamento técnica no Azure na web para suportam este cenário. No entanto, a aplicação de serviço é uma excelente escolha para alojamento REST APIs. Com a aplicação de serviço, pode:

- Criar rapidamente uma [aplicação móvel](../app-service-mobile/app-service-mobile-value-prop.md) ou [aplicação API](../app-service-api/app-service-api-apps-why-best-platform.md) para alojar o serviço web HTTP do Azure globalmente distribuídas centros de dados.
- Migrar serviços existentes ou criar novos.
- Alcançar SLA para disponibilidade com uma única ocorrência ou dimensionar a saída de vários computadores dedicada.
- Utilize o site publicado para fornecer REST APIs para qualquer clientes HTTP, incluindo os clientes móveis.

> [AZURE.NOTE]
> Se pretender começar com a aplicação de serviço de Azure antes de inscrever-se para uma conta, aceda a <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, onde pode imediatamente criar uma aplicação starter curto na aplicação de serviço de Azure gratuitamente. Sem cartão de crédito obrigatório, sem compromissos.

## <a id="nextsteps"></a>Próximos passos

Para mais informações sobre os três opções de alojamento na web, consulte o artigo [Introdução ao Azure](../fundamentals-introduction-to-azure.md).

Para começar a trabalhar com as opções que escolher para a sua aplicação, consulte os seguintes recursos:

* [Azure de aplicação de serviço](/documentation/services/app-service/)
* [Serviços em nuvem Azure](/documentation/services/cloud-services/)
* [Azure máquinas virtuais](/documentation/services/virtual-machines/)
* [Serviço ferro](/documentation/services/service-fabric)

<!-- URL List -->

[Azure de aplicação de serviço]: /services/app-service/
[Serviços em nuvem]: http://go.microsoft.com/fwlink/?LinkId=306052
[Máquinas virtuais]: http://go.microsoft.com/fwlink/?LinkID=306053
[Serviço ferro]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[Armazenamento]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
