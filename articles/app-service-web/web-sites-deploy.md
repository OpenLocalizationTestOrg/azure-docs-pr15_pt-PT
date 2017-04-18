<properties
    pageTitle="Implemente a sua aplicação para Azure de aplicação de serviço"
    description="Saiba como implementar a aplicação para a aplicação de serviço de Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cephalin;dariac"/>
    
# <a name="deploy-your-app-to-azure-app-service"></a>Implemente a sua aplicação para Azure de aplicação de serviço

Este artigo ajuda a determinar a melhor opção para implementar os ficheiros para a sua aplicação web, aplicação móvel do back-end ou aplicação API para a [Aplicação de serviço de Azure](http://go.microsoft.com/fwlink/?LinkId=529714)e, em seguida, orienta-o para recursos adequados com instruções específicas para a opção que prefere.

## <a name="overview"></a>Descrição geral de implementação do Azure aplicação de serviço

Serviço de aplicação Azure mantém a arquitetura de aplicação para si (ASP.NET, PHP, Node.js, etc). Algumas quadros estiverem ativadas por predefinição, enquanto outras pessoas, como Java e Python, poderá ter uma configuração de marca de verificação simples para ativá-la. Além disso, pode personalizar o seu quadro de aplicação, tal como a versão PHP ou número de bits da sua runtime. Para mais informações, consulte o artigo [Configurar a aplicação na aplicação de serviço de Azure](web-sites-configure.md).

Uma vez que não têm de se preocupar a arquitetura de servidor ou aplicação web, implementá-lo na sua aplicação do serviço de aplicação é uma questão de implementação do seu código, binários, ficheiros de conteúdo e estrutura os respetivos respetivos diretório, para o [diretório de **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o **/site/wwwroot/App_Data/tarefas/** directório para WebJobs). Aplicação de serviço suporta as seguintes opções de implementação: 

- [FTP ou FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): utilização seu favoritas FTP ou FTPS activada ferramenta para mover os seus ficheiros para Azure, a partir do [FileZilla](https://filezilla-project.org) para comunicação IDES completa: como [NetBeans](https://netbeans.org). Estritamente este é um processo de carregamento de ficheiro. Sem serviços adicionais é fornecida pelo serviço de aplicação, tal como o controlo de versão, gestão de ficheiros de estrutura, etc. 

- [Kudu (Git que ou OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): Utilize o [motor de implementação](https://github.com/projectkudu/kudu/wiki) no serviço de aplicação. O código de emissão para Kudu diretamente a partir de qualquer repositório. Kudu também fornece serviços adicionados sempre que o código seguia à mesma, incluindo o controlo de versão, restauro de pacote, MSBuild e [web hooks](https://github.com/projectkudu/kudu/wiki/Web-hooks) para contínua implementação e outras tarefas de automatização. O motor de implementação Kudu suporta 3 diferentes tipos de origens de implementação de:   
    * Sincronizar conteúdo do OneDrive e Dropbox   
    * Com base no repositório de implementação contínua com a sincronização automática do GitHub, Bitbucket e serviços de equipa do Visual Studio  
    * Implementação baseada em repositório com a sincronização manual do local Git  

- [Implementar Web](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): implementar código para a aplicação de serviço de diretamente da sua Microsoft favorito ferramentas, tais como o Visual Studio utilizando a mesma ferramenta que automatiza implementação para servidores IIS. Esta ferramenta suporta apenas de detecção de diferenças implementação, criação de base de dados, transformações de cadeias de ligação, etc. Implementar Web difere Kudu nesse binários da aplicação são criadas as antes de serem disponibilizados ao Azure. Semelhante ao FTP, sem serviços adicionais é fornecida pelo serviço de aplicação.

Ferramentas de desenvolvimento web populares suportam um ou mais dos seguintes processos de implementação. Enquanto a ferramenta de que escolher determina os processos de implementação, que pode tirar partido, a funcionalidade de DevOps real à sua disposição depende a combinação do processo de implementação e ferramentas específicas que escolher. Por exemplo, se executar Web implementar a partir do [Visual Studio com Azure SDK](#vspros), apesar de não receber automatização do Kudu, obter restauro de pacote e MSBuild automatização no Visual Studio. 

>[AZURE.NOTE] Estes processos de implementação não realmente [aprovisionar os Azure recursos](../resource-group-template-deploy-portal.md) que poderão é necessárias sua aplicação. No entanto, a maior parte dos artigos sobre como utilizar ligados mostra-lhe como pode aprovisionar a aplicação e implementar o seu código ao mesmo ponto a ponto. Também pode encontrar opções adicionais para recursos Azure na secção de [implementação de automatizar utilizando ferramentas da linha de comandos](#automate) de aprovisionamento.
     
## <a name="ftp"></a>Implemente através de FTP ao copiar ficheiros para o Azure manualmente
Se utilizou manualmente a copiar o conteúdo da web para um servidor web, pode utilizar um utilitário [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) para copiar ficheiros, tais como o Explorador do Windows ou [FileZilla](https://filezilla-project.org/).

Os profissionais de cópia dos ficheiros manualmente são:

- Familiaridade e complexidade mínima para ferramentas de FTP. 
- Saber exatamente onde os ficheiros são aplicado.
- Segurança adicionada com FTPS.

Desvantagens de cópia dos ficheiros manualmente são:

- Está com dificuldades saber como implementar ficheiros para os directórios corretos na aplicação de serviço. 
- Sem controlo de versão para anulação quando ocorrem falhas.
- Sem histórico de implementação incorporados para a resolução de problemas de implementação.
- Implementação longa potencial vezes porque muitos ferramentas FTP não fornecer só de detecção de diferenças copiar e simplesmente copiar todos os ficheiros.  

### <a name="howtoftp"></a>Como implementar ao copiar ficheiros para o Azure manualmente
Copiar ficheiros para o Azure envolve alguns passos simples:

1. Partindo do princípio de que já foram estabelecidas as credenciais de implementação, obter as informações de ligação de FTP ao aceder a **Definições** > **Propriedades**e, em seguida, copiar os valores para o **Utilizador FTP/implementação**, **Nome de anfitrião FTP**e **Ftps:// Host Name**. Copie o valor de utilizador do **Utilizador FTP/implementação** , tal como apresentados ao Portal do Azure, incluindo o nome da aplicação para fornecer um contexto adequado para o servidor FTP.
2. A partir do seu cliente FTP, utilize as informações de ligação que reuniu para ligar à sua aplicação.
3. Copie os ficheiros e os respetivos estrutura do directório respetivos para o [diretório de **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o **/site/wwwroot/App_Data/tarefas/** directório para WebJobs).
4. Navegue para o URL da sua aplicação para verificar que a aplicação está a funcionar corretamente. 

Para obter mais informações, consulte o seguinte recurso:

* [Criar uma aplicação web do PHP MySQL e implementar utilizando FTP](web-sites-php-mysql-deploy-use-ftp.md).

## <a name="dropbox"></a>Implemente ao sincronizar com uma pasta na nuvem
Uma boa alternativa para [copiar os ficheiros manualmente](#ftp) está a sincronizar ficheiros e pastas para a aplicação de serviço a partir de um serviço de armazenamento na nuvem como o OneDrive e o Dropbox. Sincronizar com uma pasta de nuvem utiliza o processo de Kudu de implementação (consulte [Descrição geral dos processos de implementação](#overview)).

Os profissionais de sincronizar com uma pasta de nuvem são:

- Simplificar de implementação. Serviços como o OneDrive e Dropbox fornecem clientes de ambiente de trabalho de sincronização, pelo seu diretório local de trabalho também é no diretório da sua implementação.
- Implementação com um clique.
- Todas as funcionalidades do motor de implementação Kudu estão disponível (por exemplo, restauro de pacote, automatização).

Desvantagens de sincronizar com uma pasta de nuvem são:

- Sem controlo de versão para anulação quando ocorrem falhas.
- Sem implementação automatizada, sincronização manual, é necessária.

### <a name="howtodropbox"></a>Como implemente ao sincronizar com uma pasta na nuvem
No [Portal do Azure](https://portal.azure.com), que pode designar uma pasta para a sincronização de conteúdo no seu armazenamento em nuvem OneDrive ou Dropbox, trabalhar com o seu código de aplicação e o conteúdo dessa pasta e sincronizar a aplicação de serviço com o clique de um botão.

* [Sincronizar conteúdo de uma pasta na nuvem para a aplicação de serviço de Azure](app-service-deploy-content-sync.md). 

## <a name="continuousdeployment"></a>Implementar constantemente a partir de um serviço de controlo da origem baseado na nuvem
Se a sua equipa de desenvolvimento utiliza um serviço de gestão (SMS) de código fonte baseado na nuvem como o [Serviços de equipa do Visual Studio](http://www.visualstudio.com/), [GitHub](https://www.github.com)ou [BitBucket](https://bitbucket.org/), pode configurar a aplicação de serviço para integrar com o seu repositório e implementar continuamente. 

Profissionais de implementação de um serviço de controlo da origem baseado na nuvem são:

- Controlo de versão para ativar reposição da versão anterior.
- Capacidade de configurar a implementação contínua para Git (e que onde aplicável) repositórios. 
- Implementação de ramo específicas, pode implementar ramos diferentes a diferentes [faixas](web-sites-staged-publishing.md).
- Todas as funcionalidades do motor de implementação Kudu estão disponível (por exemplo, controlo de versões de implementação, anular, restauro de pacote, automatização).

Con de implementação de um serviço de controlo da origem baseado na nuvem é:

- Alguns conhecimentos sobre o serviço SMS respetivos necessário.

###<a name="vsts"></a>Como implementar continuamente a partir de um serviço de controlo da origem baseado na nuvem
No [Portal do Azure](https://portal.azure.com), pode configurar a implementação contínua do GitHub, Bitbucket e serviços de equipa do Visual Studio.

* [Implementação de contínuo Azure de aplicação de serviço](app-service-continuous-deployment.md). 

## <a name="localgitdeployment"></a>Implementar a partir de local Git
Se a sua equipa de desenvolvimento utiliza um serviço de gestão (SMS) do código no local origem local com base em Git, poderá configurar esta como uma origem de implementação para a aplicação de serviço. 

Profissionais de implementar a partir de local Git são:

- Controlo de versão para ativar reposição da versão anterior.
- Implementação de ramo específicas, pode implementar ramos diferentes a diferentes [faixas](web-sites-staged-publishing.md).
- Todas as funcionalidades do motor de implementação Kudu estão disponível (por exemplo, controlo de versões de implementação, anular, restauro de pacote, automatização).

Con de implementação do local Git é:

- Alguns dados de conhecimento do sistema SMS respetivos necessário.
- Sem soluções Ativar chave para implementação contínua. 

###<a name="vsts"></a>Como implementar a partir de local Git
No [Portal do Azure](https://portal.azure.com), pode configurar a implementação de Git local.

* [Implementação de local Git Azure de aplicação de serviço](app-service-deploy-local-git.md). 
* [Publicar aplicações Web a partir de qualquer repo git/hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html).  

## <a name="deploy-using-an-ide"></a>Implementar a utilizar um IDE
Se já estiver a utilizar o [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) com um [Azure SDK](https://azure.microsoft.com/downloads/)ou outros conjuntos de aplicações do IDE como [Xcode](https://developer.apple.com/xcode/), [Eclipse](https://www.eclipse.org)e [IntelliJ IDEIA](https://www.jetbrains.com/idea/), pode implementar Azure diretamente a partir do seu IDE. Esta opção é ideal para um programador individual.

Visual Studio suporta todos os três implementação processos (FTP, Git e implementar Web), dependendo da sua preferência, enquanto outras comunicação IDES: pode implementar a aplicação de serviço caso tenham integração de FTP ou Git (consulte [Descrição geral dos processos de implementação](#overview)).

Os profissionais de implementação do utilizando um IDE são:

- Minimize potencialmente ferramentas para o ciclo de vida de aplicação de fim para fim. Desenvolver, depurar, controlar e implemente a sua aplicação para todos os Azure sem passar fora da sua IDE. 

Desvantagens de implementação do utilizando um IDE são:

- Adicionado complexidade no ferramentas.
- Ainda requer um sistema de controlo da origem de um projeto de equipa.

<a name="vspros"></a>Prós adicionais de implementação do utilizando o Visual Studio com Azure SDK são:

- Azure SDK torna os Azure recursos cidadãos primeira classe no Visual Studio. Criar, eliminar, editar, iniciar e parar de aplicações, base de dados back-end SQL da consulta, live-depurar a aplicação Azure e muito mais. 
- Live edição de ficheiros de código no Azure.
- Live depuração de aplicações no Azure.
- Integrada explorer Azure.
- Implementação apenas de detecção de diferenças. 

###<a name="vs"></a>Como implementar diretamente a partir do Visual Studio

* [Introdução ao Azure e ASP.NET](web-sites-dotnet-get-started.md). Como criar e implementar um projeto de web do ASP.NET MVC simples utilizando o Visual Studio e implementar a Web.
* [Como implementar o Azure WebJobs utilizando o Visual Studio](websites-dotnet-deploy-webjobs.md). Como configurar projetos de aplicação de consola para que estes implementarem como WebJobs.  
* [Implementar uma aplicação de seguro ASP.NET MVC 5 com associação, OAuth e base de dados do SQL às aplicações Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Como criar e implementar um projeto de web do ASP.NET MVC com uma base de dados do SQL, utilizando o Visual Studio, Web implementar e entidade Framework código primeiro migrações.
* [Implementação de Web do ASP.NET utilizando o Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Uma série de tutoriais de 12 partes que abrange um intervalo mais completo de tarefas de implementação que as outras nesta lista. Algumas funcionalidades de implementação do Azure foram adicionadas desde que foi escrito o tutorial, mas notas adicionadas mais tarde explicam o que está em falta.
* [Implementar um Web site ASP.NET para Azure no Visual Studio 2012, a partir de um repositório de Git diretamente](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Explica como implementar um projeto de web do ASP.NET no Visual Studio, utilizando o Git Plug-in para consolidar o código Git e Azure conexão para o repositório de Git. Iniciar no Visual Studio 2013, o suporte de Git está incorporado e não necessita de instalação do plug-ins.

###<a name="aztk"></a>Como implementar a utilizar os conjuntos de ferramentas do Azure para Eclipse e IntelliJ IDEIA

Microsoft torna implementar Web Apps para Azure diretamente a partir do Eclipse e IntelliJ através da [Toolkit de Azure para Eclipse](../azure-toolkit-for-eclipse.md) e o [Toolkit de Azure para IntelliJ](../azure-toolkit-for-intellij.md). Os seguintes tutoriais ilustram os passos que estão envolvidos na implementar simples um "Olá" mundo Web App para Azure utilizando um dos IDE:

*  [Crie uma aplicação Web do mundo Olá para Azure no Eclipse](./app-service-web-eclipse-create-hello-world-web-app.md). Este tutorial mostra-lhe como utilizar o Toolkit de Azure para Eclipse para criar e implementar um Olá mundo Web App para o Azure.
*  [Crie uma aplicação Web do mundo Olá para Azure no IntelliJ](./app-service-web-intellij-create-hello-world-web-app.md). Este tutorial mostra-lhe como utilizar o Toolkit de Azure para IntelliJ para criar e implementar um Olá mundo Web App para o Azure.


## <a name="automate"></a>Automatizar a implementação utilizando ferramentas da linha de comandos

* [Automatizar a implementação com MSBuild](#msbuild)
* [Copiar ficheiros com ferramentas FTP e scripts](#ftp)
* [Automatizar a implementação com o Windows PowerShell](#powershell)
* [Automatizar a implementação com a gestão de .NET API](#api)
* [Implementar a partir do Azure Interface de comandos (Azure CLI)](#cli)
* [Implementar a partir da Web implementar a linha de comandos](#webdeploy)
* [Utilização de Scripts de FTP lote](http://support.microsoft.com/kb/96269).
 
Outra opção de implementação é utilizar um serviço baseado na nuvem como o [Polvo implementar](http://en.wikipedia.org/wiki/Octopus_Deploy). Para mais informações, consulte o artigo [Implementar ASP.NET aplicações para o Azure Web Sites](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatizar a implementação com MSBuild

Se utilizar o [Visual Studio IDE](#vs) para o desenvolvimento, pode utilizar [MSBuild](http://msbuildbook.com/) para automatizar nada que pode fazer no seu IDE. Pode configurar MSBuild utilizar [Implementar Web](#webdeploy) ou [FTP/FTPS](#ftp) para copiar ficheiros. Implementar Web também pode automatizar muitos outros relacionados com a implementação de tarefas, tais como implementar bases de dados.

Para obter mais informações sobre a implementação da linha de comandos utilizando MSBuild, consulte os seguintes recursos:

* [Implementação de Web do ASP.NET utilizando o Visual Studio: implementação de linha de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Décimo numa série de tutoriais sobre a implementação do Azure utilizando o Visual Studio. Mostra como utilizar a linha de comandos para implementar depois da configurar o publicar perfis no Visual Studio.
* [Dentro do motor de compilação da Microsoft: utilizar MSBuild e Team Foundation compilação](http://msbuildbook.com/). Livro de disco rígido copiar que inclui os capítulos sobre como utilizar MSBuild para implementação.

###<a name="powershell"></a>Automatizar a implementação com o Windows PowerShell

Pode executar MSBuild ou FTP funções de implementação do [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Se fazê-lo, também pode utilizar uma coleção de cmdlets do Windows PowerShell que facilitam a gestão de Azure REST API ligar.

Para obter mais informações, consulte os seguintes recursos:

* [Implementar uma aplicação web ligada a um repositório de GitHub](app-service-web-arm-from-github-provision.md)
* [Aprovisionar uma aplicação web com uma base de dados SQL](app-service-web-arm-with-sql-database-provision.md)
* [Aprovisionar e implementar microservices seja previsível no Azure](app-service-deploy-complex-application-predictably.md)
* [Edifício reais nuvem aplicações com o Azure - automatizar tudo](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Livro de endereços "e" capítulo que explica como a aplicação de exemplo apresentada no livro de "e" utiliza scripts do Windows PowerShell para criar um ambiente de teste Azure e implementar à mesma. Consulte a secção de [recursos](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) para estabelecer ligações a documentação do Azure PowerShell adicional.
* [Utilização de Scripts de PowerShell do Windows para publicar para Dev Center e ambientes de teste](../vs-azure-tools-publishing-using-powershell-scripts.md). Como utilizar o Windows PowerShell implementação scripts que gera Visual Studio.

###<a name="api"></a>Automatizar a implementação com a gestão de .NET API

Pode escrever código c# para executar funções MSBuild ou FTP para implementação. Se fazê-lo, pode aceder a gestão de Azure REST API para executar funções de gestão do site.

Para obter mais informações, consulte o seguinte recurso:

* [Automatizar tudo com as bibliotecas de gestão do Azure e .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introdução à gestão de .NET API e ligações para mais documentação.

###<a name="cli"></a>Implementar a partir do Azure Interface de comandos (Azure CLI)

Pode utilizar a linha de comandos no Windows, Mac ou Linux máquinas para implementar utilizando FTP. Se fazê-lo, também pode aceder a API utilizando o clip do Azure de gestão do resto do Azure.

Para obter mais informações, consulte o seguinte recurso:

* [Ferramentas de linha de comandos do azure](/downloads/#cmd-line-tools). Página Portal no Azure.com para informações sobre a ferramenta linha de comandos.

###<a name="webdeploy"></a>Implementar a partir da Web implementar a linha de comandos

[Implementar Web](http://www.iis.net/downloads/microsoft/web-deploy) é software da Microsoft para a implementação IIS que não só disponibiliza funcionalidades de sincronização do ficheiro inteligente, mas também pode executar ou coordenar muitas outras tarefas relacionadas com a implementação que não podem ser automatizadas quando utiliza o FTP. Por exemplo, a Web implementar pode implementar uma nova base de dados ou atualizações de base de dados juntamente com a sua aplicação web. Implementar Web também pode minimizar o tempo necessário para actualizar um site existente, desde que inteligente-pode copiar apenas os ficheiros alterados. Microsoft Visual Studio e Team Foundation Server têm suporte para a Web implementar incorporados, mas também pode utilizar Web implementar diretamente a partir da linha de comandos para automatizar a implementação. Comandos de implementar Web são muito poderosos mas a curva de aprendizagem podem ser acentuada.

Para obter mais informações, consulte o seguinte recurso:

* [Aplicações Web simples: implementação](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blogue por David Ebbo sobre uma ferramenta que ele escreveu para que seja mais fácil utilizar Web implementar.
* [Ferramenta de implementação do web](http://technet.microsoft.com/library/dd568996). Documentação oficial no site da Microsoft TechNet. Datada mas ainda um bom local para começar.
* [Utilizar Web implementar](http://www.iis.net/learn/publish/using-web-deploy). Documentação oficial no site do Microsoft IIS.NET. Também datada mas um bom local para começar.
* [Implementação de Web do ASP.NET utilizando o Visual Studio: implementação de linha de comandos](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild é o motor de compilação utilizadas pelo Visual Studio e -lo também pode ser utilizado na linha de comandos para implementar aplicações web às aplicações Web. Neste tutorial faz parte de uma série que é principalmente sobre a implementação do Visual Studio.

##<a name="nextsteps"></a>Próximos passos

Em alguns cenários poderá conseguir mudar de e para trás facilmente entre uma transição e uma versão de produção da sua aplicação. Para mais informações, consulte o artigo [Testado implementação na Web Apps](web-sites-staged-publishing.md).

Ter um plano de cópia de segurança e restauro num local é uma parte importante qualquer fluxo de trabalho de implementação. Para obter informações sobre a aplicação de serviço de cópia de segurança e restaurar a funcionalidade, consulte o artigo [Cópias de segurança do Web Apps](web-sites-backup.md).  

Para obter informações sobre como utilizar o controlo de acesso baseado em funções do Azure para gerir o acesso a implementação de aplicação de serviço, consulte o artigo [RBAC e publicação na Web App](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).


 
