<properties 
    pageTitle="Como configurar o computador para o desenvolvimento de serviços de multimédia com o .NET" 
    description="Saiba mais sobre as pré-requisitos para utilizar o SDK de serviços de multimédia para .NET dos serviços de multimédia. Saiba também como criar uma aplicação do Visual Studio." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/24/2016"  
    ms.author="juliako"/>

#<a name="media-services-development-with-net"></a>Desenvolvimento dos serviços de multimédia com o .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Este tópico explica como iniciar a programação de aplicações dos serviços de multimédia utilizando o .NET.

A biblioteca do **Azure multimédia serviços .NET SDK** permite-lhe contra dos serviços de multimédia utilizando o .NET do programa. Para tornar ainda mais fácil desenvolver com .NET, a biblioteca de **Azure multimédia serviços .NET SDK extensões** é fornecida. Esta biblioteca contém um conjunto de métodos de extensão e funções auxiliares que simplificam o seu código .NET. Ambas as bibliotecas estão disponíveis através de **NuGet** e **GitHub**.


##<a name="prerequisites"></a>Pré-requisitos

-   Uma conta dos serviços de multimédia numa subscrição Azure nova ou existente. Consulte o tópico [como criar uma conta de serviços de multimédia](media-services-portal-create-account.md).
-   Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET framework 4,5.
-    Visual Studio 2015, Visual Studio 2013, Visual Studio 2012http ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express).


##<a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

Esta secção mostra-lhe como criar um projeto no Visual Studio e configurá-la para o desenvolvimento dos serviços de multimédia.  Neste caso, o projeto é uma aplicação de consola do Windows c#, mas os mesmos passos de configuração mostrados aqui aplicam-se a outros tipos de projetos que pode criar para aplicações dos serviços de multimédia (por exemplo, uma aplicação do Windows Forms ou uma aplicação Web do ASP.NET).

Esta secção mostra como utilizar **NuGet** para adicionar .NET SDK serviços de multimédia e outras bibliotecas do dependentes.

Em alternativa, pode obter os bits mais recentes do Media serviços .NET SDK do GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) e [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), criar a solução e adicionar as referências para o projeto de cliente. Tenha em atenção que todas as dependências aceder transferidas e extraídas automaticamente.

1. Crie uma nova aplicação de consola do c# no Visual Studio 2010 SP1 ou posterior em versões VS. Introduza o **nome**, **localização**e **nome da solução**e, em seguida, clique em OK.

2. Crie a solução.

2. Utilize **NuGet** para instalar e adicionar **Azure multimédia serviços .NET SDK extensões**. Instalar este pacote, também instala o **.NET SDK serviços de multimédia** e adiciona todas as outras dependências.

    Certifique-se de que tem a versão mais recente do NuGet instalado. Para obter mais informações e instruções de instalação, consulte o artigo [NuGet](http://nuget.codeplex.com/).

2. No Explorador de solução, com o botão direito no nome do projeto e selecione gerir NuGet pacotes...

    É apresentada a caixa de diálogo Gerir pacotes de NuGet.

3. Na Galeria Online, procure Azure MediaServices extensões, selecione extensões de SDK de .NET de serviços de multimédia do Azure e, em seguida, clique no botão instalar.

    O projeto é modificado e referências a extensões SDK .NET dos serviços de multimédia, .NET SDK serviços de multimédia e outros conjuntos dependentes são adicionadas.

4. Para promover um ambiente de desenvolvimento mais limpo, considere ativar NuGet pacote restaurar. Para obter mais informações, consulte o artigo [NuGet pacote restaurar "](http://docs.nuget.org/consume/package-restore).

3. Adicione uma referência a **System.Configuration** assemblagem. Esta assemblagem contém o System.Configuration. Classe de **ConfigurationManager** que é utilizado para aceder a ficheiros de configuração (por exemplo, App).

    Para adicionar referências utilizando a caixa de diálogo Gerir referências, com o botão direito no nome do projeto no Explorador de solução. Em seguida, selecione Adicionar e referências.

    É apresentada a caixa de diálogo Gerir referências.

4. Em conjuntos do .NET framework, localize e selecione a assemblagem System.Configuration e prima OK.
5. Abra o ficheiro App (adicionar o ficheiro ao seu projeto se não foi adicionada por predefinição) e adicionar uma secção de *appSettings* para o ficheiro.     
Defina os valores para a sua dos serviços de multimédia do Azure nome e conta chave da conta, conforme mostrado no seguinte exemplo.

    Para localizar o nome e a chave de valores, aceda ao portal do Azure e selecione a sua conta. Na janela definições que é apresentado à direita. Na janela definições, selecione teclas. Clicando no ícone junto a cada caixa de texto copia o valor para a área de transferência do sistema.


        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

6. Substitui as existente **utilizando** demonstrações no início do ficheiro Program.cs com o código seguinte.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

Neste momento, está pronto para iniciar a programação de uma aplicação dos serviços de multimédia.    


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
