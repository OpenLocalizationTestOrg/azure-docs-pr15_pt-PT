<properties
   pageTitle="Compilar da linha de comandos para Azure | Microsoft Azure"
   description="Compilar da linha de comandos para Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="command-line-build-for-azure"></a>Compilar da linha de comandos para Azure

## <a name="overview"></a>Descrição geral

Pode criar um pacote de implementação do Azure através da execução MSBuild numa linha de comandos. Pode configurar e definir compilações para depuração, teste e produção, para além de automatizar algumas do processo de criação.


## <a name="microsoft-build-engine-msbuild"></a>Motor de compilação da Microsoft (MSBuild)

Ao utilizar o Microsoft construir Engine (MSBuild), pode criar produtos na compilação ambientes de laboratório onde o Visual Studio não está instalado. MSBuild utiliza um formato XML para os ficheiros de projeto extensible e totalmente suportado pela Microsoft. Este formato de ficheiro, pode descrever o que itens têm de ser criadas para um ou mais plataformas e configurações.

Também pode executar MSBuild numa linha de comandos e este tópico descreve abordagem. Ao definir propriedades de uma linha de comandos, pode criar configurações específicas de um projeto. Do mesmo modo, também pode definir os destinos que o comando MSBuild irá criar. Para mais informações sobre parâmetros da linha de comandos e MSBuild, consulte o artigo [Referência de linha de comandos MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="installation"></a>Instalação

Como o procedimento seguinte descreve, tem de instalar software e ferramentas no servidor de compilação antes de poder criar um pacote de Azure utilizando MSBuild:

1. Instalar o .NET Framework 4 ou posterior, que inclui MSBuild.

1. Instalar as [Ferramentas de criação de Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (procure MicrosoftAzureAuthoringTools x64.msi ou MicrosoftAzureAuthoringTools x86.msi.

1. Instalar o [Azure bibliotecas para o .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (procure MicrosoftAzureLibsForNet x64.msi ou MicrosoftAzureLibs x86.msi.

1. Copie o ficheiro de Microsoft.WebApplication.targets a partir de uma instalação do Visual Studio noutro computador.

    O ficheiro está localizado no diretório c:\Programas\Microsoft ficheiros (x86) \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications (v 11.0 para Visual Studio 2012) e, deve copiá-la para o mesmo directório no servidor de compilação.

1. Instale as [Ferramentas Azure para Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Procure WindowsAzureTools.vs120.exe Criar projetos do Visual Studio 2013.

## <a name="msbuild-parameters"></a>Parâmetros de MSBuild

A forma mais simples para criar um pacote é executar MSBuild com o `/t:Publish` opção. Por predefinição, este comando cria um diretório em relação a pasta de raiz para o projeto, tal como ProjectDir\bin\Configuration\app.publish\. quando constrói um projeto Azure, gerar dois ficheiros, o ficheiro do pacote e o ficheiro de configuração acompanhamento:

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

Por predefinição, cada projeto Azure inclui um ficheiro de configuração do serviço para o local (depuração) constrói e outro para compilações da nuvem (transição ou produção), mas pode adicionar ou remover os ficheiros de configuração do serviço, conforme necessário. Quando constrói um pacote do Visual Studio, será pedido qual o ficheiro para incluir juntamente com o pacote de configuração do serviço. Quando constrói um pacote utilizando MSBuild, o ficheiro de configuração do serviço local é incluído por predefinição. Para incluir um ficheiro de configuração do serviço diferente, defina o `TargetProfile` propriedade do comando MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Se quiser utilizar um directório alternativo para o pacote armazenado e ficheiros de configuração, definir o caminho utilizando o `/p:PublishDir=Directory\` opção, incluindo o separador barra invertida à direita.

## <a name="deployment"></a>Implementação

Depois do pacote é criado, pode implemente-Azure. Para obter um tutorial que demonstre o processo, consulte o Web site Azure. Para obter informações sobre como automatizar processo, consulte o artigo [Entrega contínua para serviços em nuvem no Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).
