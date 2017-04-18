<properties
    pageTitle="O que é o Azure .NET SDK"
    description="Saiba o que está incluído no Azure .NET SDK."
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor="mollybos"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="rachelap"/>

# <a name="what-is-the-azure-sdk-for-net"></a>O que é o SDK do Azure para .NET?

## <a name="overview"></a>Descrição geral

O SDK do Azure para .NET é um conjunto de ferramentas do Visual Studio, ferramentas da linha de comandos, runtime binários e bibliotecas do cliente que o ajudam a desenvolver, testar e implementar aplicações que são executados no Azure. Este artigo apresenta detalhes sobre o que obtém quando instala o SDK. Pode transferir o SDK a partir da [página de transferências do Azure](https://azure.microsoft.com/downloads/).

O SDK do Azure para .NET também inclui [bibliotecas do cliente para serviços Azure consome](http://go.microsoft.com/fwlink/?LinkId=510472). Estas bibliotecas são instaladas separadamente utilizando NuGet.

##<a id="included"></a>O que está incluído no Azure SDK para .NET

O SDK do Azure para .NET instala os produtos para os seguintes:

- [Comunidade do Visual Studio Edition 2015](#vwd)
- [Emulador de armazenamento do Microsoft Azure](#stgemulator)
- [Ferramentas de armazenamento do Microsoft Azure](#stgtools)
- [Ferramentas de criação do Microsoft Azure](#auth)
- [Emulador do Microsoft Azure](#emulator)
- [Ferramentas de HDInsight para Visual Studio e do Microsoft Hive controlador ODBC](#hdinsight)
- [Bibliotecas do Microsoft Azure para .NET](#libraries)
- [SDK aplicação móvel do Microsoft Azure](#mobile)
- [PowerShell do Microsoft Azure](#ps)
- [Ferramentas do Microsoft Azure para o Microsoft Visual Studio](#tools)
- [Ferramentas de Web para Visual Studio e do Microsoft ASP.NET](#wte)
- [Ferramentas do Microsoft Azure dados Lake para Visual Studio](#datalake)

###<a id="vwd"></a>Comunidade do Visual Studio Edition 2015

Se não tiver o Visual Studio no seu computador, o SDK instalará [2015 de edição de Comunidade do Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs).

###<a id="stgemulator"></a>Emulador de armazenamento do Microsoft Azure

O [Azure armazenamento emulador](http://msdn.microsoft.com/library/hh403989.aspx) utiliza uma instância do SQL Server e o sistema de ficheiros local para simular armazenamento do Windows Azure (filas, tabelas, blobs), para que pode testar o localmente.

###<a id="stgtools"></a>Ferramentas de armazenamento do Microsoft Azure

Este procedimento instala [AzCopy](http://aka.ms/AzCopy), uma ferramenta de linha de comandos, pode utilizar para transferir dados para e terminar uma conta de armazenamento do Windows Azure.

###<a id="auth"></a>Ferramentas de criação do Microsoft Azure

Isto inclui o seguinte:

* A [ferramenta de linha de comandos CSPack](http://msdn.microsoft.com/library/gg432988.aspx) para a criação de pacotes de implementação.
* a [ferramenta de linha de comandos CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) para encriptar palavras-passe que são utilizadas para aceder a função instâncias do serviço de nuvem através de uma ligação de ambiente de trabalho remoto.
* Binários do Runtime projetos de serviço de nuvem requerem para comunicar com o seu ambiente de trabalho do runtime e para diagnóstico. Estes binários não estão disponíveis no NuGet pacotes.

###<a id="emulator"></a>Emulador do Microsoft Azure

O [Azure emulador](http://msdn.microsoft.com/library/dn339018.aspx) simula o ambiente de serviço de nuvem, de modo a que pode testar o projetos de serviço de nuvem localmente no seu computador antes de implementá-los para Azure.

###<a id="hdinsight"></a>Ferramentas de HDInsight para Visual Studio e Microsoft Hive controlador ODBC

Ferramentas HDInsight no servidor Explorer permitem-lhe navegar ramo de bases de dados e contas ligadas armazenamento para HDInsight clusters, criar tabelas e criar e submeter ramo de consultas. Para mais informações, consulte o artigo [começar a utilizar o HDInsight Hadoop ferramentas para o Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

###<a id="libraries"></a>Bibliotecas do Microsoft Azure para .NET

Isto inclui o seguinte:

* Pacotes de NuGet para armazenamento do Windows Azure, Bus de serviço e em cache, que estão armazenados no seu computador para que o Visual Studio pode criar nova nuvem projetos de serviço enquanto estiver offline.
* Um Visual Studio Plug-in que permite projetos [Na função Cache](http://msdn.microsoft.com/library/dn386103.aspx) executar o localmente no Visual Studio.

###<a id="mobile"></a>SDK aplicação móvel do Microsoft Azure

Ferramentas para trabalhar com [Aplicações do Azure aplicação serviço Mobile](app-service-mobile/app-service-mobile-value-prop.md).

###<a id="ps"></a>PowerShell do Microsoft Azure

Azure PowerShell permite-lhe para [automatizar a criação de ambiente Azure e implementação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

###<a id="tools"></a>Ferramentas do Microsoft Azure para o Microsoft Visual Studio

Isto permite-lhe trabalhar com recursos Azure, principalmente serviços em nuvem e máquinas virtuais:

* [Criar, abrir e publicar projectos de serviço de nuvem](cloud-services/cloud-services-dotnet-get-started.md).
* [Pacotes de implementação do criar para projetos de serviço de nuvem](http://msdn.microsoft.com/library/ff683672.aspx).
* [Criar o máquinas virtuais do Azure ao criar novos projectos web](virtual-machines/virtual-machines-windows-classic-web-app-visual-studio.md).
* [Scripts de PowerShell criar durante a criação de novas máquinas virtuais](http://msdn.microsoft.com/library/dn642480.aspx).
* [Ver e gerir definições de projeto do serviço de nuvem no windows de propriedades do projeto do Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Ver e gerir [serviços em nuvem](http://msdn.microsoft.com/library/ff683675.aspx), [máquinas virtuais](http://msdn.microsoft.com/library/jj131259.aspx)e [Serviço Bus](http://msdn.microsoft.com/library/jj149828.aspx) no Explorador do servidor.
* [Executar no modo de depuração remotamente para serviços em nuvem e máquinas virtuais](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatizar recurso aprovisionamento utilizando projectos de implementação do grupo de recursos do Azure](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="wte"></a>Ferramentas de serviço da aplicação Microsoft para o Visual Studio

Isto permite-lhe trabalhar com sites públicos do Azure:

* [Publicar projectos web sites públicos do Azure](app-service-web/web-sites-dotnet-get-started.md).
* [Projetos de aplicação de consola de publicar para Azure WebJobs](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Recursos de Web site do Azure de criar e base de dados SQL ao criar um novo projeto de web ou ao publicar um projeto de web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [Scripts de implementação do PowerShell criar ao criar novos sites](http://msdn.microsoft.com/library/dn642480.aspx).
* [Gerir e resolver problemas de Web sites do Azure no Explorador do servidor](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Executar no modo de depuração remotamente para Web sites e WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug).

>[AZURE.NOTE] Não tem de instalar o SDK do Azure para .NET utilizar estas funcionalidades; também são incluídos no Visual Studio atualizações.

##<a id="datalake"></a>Ferramentas do Microsoft Azure dados Lake para Visual Studio

Para obter mais informações, consulte o artigo [Tutorial: desenvolver scripts de U SQL utilizando ferramentas de Lake de dados para o Visual Studio](data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

##<a id="notincluded"></a>O que é incluído não quando instala o SDK do Azure para .NET

Existem algumas coisas que poderá querer para o desenvolvimento Azure que não estão incluídas quando instala o SDK. O mais importantes são as seguintes:

* [Bibliotecas de cliente](http://go.microsoft.com/fwlink/?LinkId=510472).

    O SDK do Azure inclui bibliotecas do cliente para consumir serviços Azure, mas não todas são instaladas quando instalar o SDK. Se a aplicação necessitar de uma biblioteca de cliente não instala o SDK, pode obtê-lo a partir do [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Se a sua aplicação utiliza uma biblioteca de cliente o SDK instalar, é aconselhável para actualizá-lo com a versão atual na NuGet.org.

    **Cópias locais dos bibliotecas do cliente.** Copia o SDK do Azure para .NET para o seu computador os pacotes de NuGet para bibliotecas algumas cliente Azure, como o armazenamento, Bus de serviço e em cache. Estas bibliotecas de cliente são automaticamente incluídas na novos projectos de serviço de nuvem, para que os pacotes de NuGet locais ativar Visual Studio para criar projetos, mesmo se não estiver ligado à Internet. Bibliotecas de cliente são geralmente atualizadas com maior frequência do lançamento de novas versões SDK para que as bibliotecas do cliente na NuGet.org são geralmente mais atual que obtém com o SDK.

    **Modelos do Project que incluem as bibliotecas do cliente.** Apenas [Azure serviço](cloud-services/cloud-services-dotnet-get-started.md) em nuvem e de aplicação de serviço do Azure [Aplicações Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) modelos do project incluem automaticamente a algumas bibliotecas de cliente. Para outras bibliotecas ou outros modelos, instale os [pacotes de NuGet de biblioteca de cliente](http://go.microsoft.com/fwlink/?LinkId=510472) de que precisa.

* [Modelos de projecto de aplicações Mobile](./app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

    Os modelos de aplicações móveis estão disponíveis apenas no Visual Studio 2013 Update 2 e versões posteriores. Não estão disponíveis no Visual Studio 2012http ou versões anteriores e não no Visual Studio 2013 atualização 1 ou anterior, mesmo se instalar o SDK do Azure para .NET.

##<a id="faq"></a>Perguntas mais frequentes

- [Muitas funcionalidades Azure já estão no Visual Studio. É necessário instalar o SDK do Azure para .NET?](#azinvs)
- [Quero uma biblioteca de cliente. Tenho de ter que instalar o SDK do Azure para .NET obtê-lo?](#clientlib)
- [Onde encontrar as versões mais antigas do Azure SDK para .NET?](#olderversions)
- [O que é a política de ciclo de vida para versões do Azure SDK para .NET?](#lifecycle)
- [Quais as versões do SO convidado é compatível com o SDK do Azure para .NET?](#guestos)
- [Como posso desinstalar o SDK do Azure para .NET?](#uninstall)

###<a id="azinvs"></a>Muitas funcionalidades Azure já estão no Visual Studio. É necessário instalar o SDK do Azure para .NET?

É aconselhável para instalar o SDK se pretender desenvolver para o Azure utilizando as ferramentas de mais recentes. Se quiser não instalar o SDK, pode fazê-lo caso se verifiquem as seguintes condições:

* Tiver instalado a [Atualização do Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5)do mais recente.
* Está a desenvolver para apenas para sites públicos do Azure ou os serviços de telemóvel, não para serviços em nuvem ou máquinas virtuais.
* A sua aplicação não utilizar armazenamento, ou utiliza o armazenamento, mas não precisa emulador de armazenamento ou a ferramenta de AzCopy.

###<a id="clientlib"></a>Quero uma biblioteca de cliente. Tenho de ter que instalar o SDK do Azure para .NET obtê-lo?

O SDK instala bibliotecas do cliente apenas para que possa criar nuvem projetos serviço mesmo se não estiver ligado à Internet. Bibliotecas de cliente atual estão disponíveis no NuGet pacotes na [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Para obter mais informações, consulte o artigo [o que é incluído não quando instala o SDK do Azure para .NET](#notincluded) anteriormente deste documento.

###<a id="olderversions"></a>Onde encontrar as versões mais antigas do Azure SDK para .NET?

Para as versões mais antigas, consulte que o [SDK do Azure para .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) página de transferências.

###<a id="lifecycle"></a>O que é a política de ciclo de vida para versões do Azure SDK para .NET?

Consulte [serviços em nuvem da Microsoft Azure política de ciclo de vida de suporte](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>Quais as versões do SO convidado é compatível com o SDK do Azure para .NET?

Consulte o artigo [Convidado Azure SO lançamentos e da matriz de compatibilidade SDK](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>Como posso desinstalar o SDK do Azure para .NET?

Cada item neste artigo no [que está incluído no SDK Azure para .NET](#included) é um programa separado na lista de programas instalados no painel de controlo do Windows **programas e funcionalidades**.  Não existe nenhuma forma de desinstalá-los como um grupo; tem de desinstalar individualmente cada programa.

Quando tiver o SDK do Azure para .NET já instalada e instalar uma nova versão, não é geralmente necessário desinstalar o antigo. Na maioria dos casos, a instalação SDK atualiza um programa existente em vez de adicionar um novo e ao deixar o antigo.

No entanto, se pretende remover não-mais longa-necessário vestígios de uma versão anterior, desinstale apenas os programas que especificar o número da versão mais antigo e desinstalá-los apenas se estiver presente do mesmo programa com uma versão mais recente. Por exemplo, depois de atualizar a partir de 2,5 para 2.6 poderá ver versões 2.5 e 2.6 do "Do Microsoft Azure ferramentas para o Microsoft Visual Studio 2013" e pode desinstalar a versão 2.5. Mas só poderá ver a versão 2.5 "Ferramentas da Microsoft Azure criação" e seria seguro desinstalar que.

Note que os números de versão na títulos de programa apresentados em **programas e funcionalidades** podem ser enganadoras.  Por exemplo, SDK versão 2.6 inclui "Microsoft Azure Mobile aplicação SDK v 1.0" se instalar o SDK do Visual Studio 2013 e "Microsoft Azure Mobile aplicação SDK 2.0" para Visual Studio 2015; a versão não está neste caso, mas um indicador que versão do Visual Studio o programa aplica-se para a versão SDK.

Este artigo não lista todos os programas que cada versão anterior do Azure SDK incluído; Existem outros programas, que pode desinstalar a partir de versões anteriores do SDK, porque a versões anteriores do SDK incluíam, por vezes, programas que foram omitidos versões posteriores. Por exemplo, versão 2.5 instala "Azure recurso Gestor de ferramentas para Visual Studio" mas que não está na lista de neste artigo porque já não a mostra como um programa em **programas e funcionalidades**separado.  Este artigo apresenta apenas os programas que estão incluídos no Azure SDK para .NET versão 2.6.

> **Nota:** Alguns dos programas que inclui o SDK podem também ser instalados separadamente noutros contextos e podem ser necessárias, mesmo que não precisa do SDK completo. O mesmo podem ser verdadeiro dos programas que foram instalados pelo versões anteriores do SDK, mas que foram omitidos versões SDK posteriores. Quando desinstalar programas, tenha cuidado para evitar a remoção algo que ainda é necessário no seu computador.



##<a id="versions"></a>Versões

Para ver qual a versão é atual ou para a transferir as versões mais antigas, consulte a página [Azure SDK para o histórico de versões .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) .

##<a id="resources"></a>Recursos

Para transferir o SDK do Azure atual para .NET ou uma biblioteca de cliente, consulte a [página de transferências do Azure](https://azure.microsoft.com/downloads/).

Para o SDK do Azure para código de origem do .NET, incluindo nas bibliotecas de cliente, consulte o artigo [GitHub.com/Azure](https://github.com/azure/).

Para a documentação de referência do cliente Azure biblioteca, consulte o artigo [Referência de .NET Azure](https://azure.microsoft.com/documentation/api/).
