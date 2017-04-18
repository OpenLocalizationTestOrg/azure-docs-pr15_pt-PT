<properties
   pageTitle="Depuração de aplicações num contentor Docker local | Microsoft Azure"
   description="Obter informações sobre como modificar uma aplicação que está a ser executado num contentor de Docker local, atualizar o contentor através de edição e atualização e definir depuração de pontos de interrupção"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/22/2016"
   ms.author="mlearned" />

# <a name="debugging-apps-in-a-local-docker-container"></a>Depuração de aplicações num contentor Docker local

## <a name="overview"></a>Descrição geral
O Visual Studio Tools para Docker fornece uma forma consistente para desenvolver-se de um e validar a sua aplicação localmente num contentor Linux Docker.
Não tem de reiniciar o contentor de cada vez que efetuar um código de alterar.
Este artigo irá ilustrar como utilizar a funcionalidade "Editar e atualização de" para iniciar uma aplicação Web do ASP.NET Core num contentor de Docker local, efetue as alterações necessárias e, em seguida, atualize o browser para ver essas alterações.
-Lo também lhe mostra como definir os pontos de interrupção para depuração.

> [AZURE.NOTE] Suporte de contentor Windows irá ser entregue num lançamento futuro

## <a name="prerequisites"></a>Pré-requisitos
As seguintes ferramentas têm de ser instalados.

- [Actualização de 2015 do Visual Studio 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- Instalar a [Atualização de 2015 do Visual Studio 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [SDK do Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Para executar contentores Docker localmente, terá de um cliente local docker.
Pode utilizar o lançamento [Docker caixa de ferramentas](https://www.docker.com/products/overview#/docker_toolbox) que exige o Hyper-V para ser desativados ou pode utilizar [Docker para versão Beta do Windows](https://beta.docker.com) que utiliza o Hyper-V e requer o Windows 10.

Se utilizar a caixa de ferramentas de Docker, terá de [configurar o cliente Docker](./vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. a criar uma aplicação web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Adicione suporte Docker

[AZURE.INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]


## <a name="3-edit-your-code-and-refresh"></a>3. editar o seu código e atualização

Para iteramos rapidamente as alterações, pode iniciar a aplicação de um contentor e continuar a efetuar alterações, veja como faria com IIS Express.

1. Definir a configuração da solução `Debug` e prima ** &lt;CTRL + F5 >** para construir a sua imagem docker e executá-la localmente.

    Assim que a imagem de contentor foi criada e está a ser executado num contentor de Docker, Visual Studio iniciará do Web app no browser predefinido.
    Se estiver a utilizar o browser Microsoft Edge ou caso contrário ter erros, consulte a secção [resolução de problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md) .

1. Aceda à página sobre, que é onde iremos para efetuar os nossos alterações.

1. Volte ao Visual Studio e abra `Views\Home\About.cshtml`.

1. Adicione o seguinte conteúdo HTML para o fim do ficheiro e guarde as alterações.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```

1.  Ver na janela de saída, quando a compilação .NET foi concluída e ver essas linhas, mude novamente para o seu browser e atualizar a página sobre.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.  As alterações foram aplicadas!

## <a name="4-debug-with-breakpoints"></a>4. depurar com pontos de interrupção

Muitas vezes, alterações terão ainda mais controlo, tirar partido das funcionalidades de depuração do Visual Studio.

1.  Volte ao Visual Studio e abra`Controllers\HomeController.cs`

1.  Substitua o conteúdo do método About() com o seguinte:

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ````

1.  Definir um ponto de interrupção à esquerda da `string message`… linha.

1.  Acertar ** &lt;F5 >** para iniciar a depuração.

1.  Navegue até à página sobre para acertar o seu ponto de interrupção.

1.  Mudar para Visual Studio para ver o ponto de interrupção e verificar se o valor da mensagem.

    ![][2]

##<a name="summary"></a>Resumo

Com o [Visual Studio 2015 Tools para Docker](https://aka.ms/DockerToolsForVS), pode obter a produtividade de localmente, a trabalhar com o realismo de produção de desenvolvimento de dentro de um contentor Docker.

## <a name="troubleshooting"></a>Resolução de problemas

[Resolução de problemas de desenvolvimento do Visual Studio Docker](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Mais informações sobre Docker com Visual Studio, Windows e Azure

- [Ferramentas de docker para Visual Studio](http://aka.ms/dockertoolsforvs) - desenvolver o seu código .NET Core num contentor
- [Ferramentas de docker para serviços de equipa do Visual Studio](http://aka.ms/dockertoolsforvsts) - criar e implementar contentores docker
- [Docker ferramentas para o código do Visual Studio](http://aka.ms/dockertoolsforvscode) - serviços de idiomas para editar ficheiros de docker, com mais de e2e de cenários, a chegar
- [Informações sobre o contentor Windows](http://aka.ms/containers)- Windows Server e informações do servidor de Nano
- [O serviço de contentor Azure](https://azure.microsoft.com/services/container-service/) - [contentor Azure Service conteúdo](http://aka.ms/AzureContainerService)
-    Para obter mais exemplos de trabalhar com Docker, consulte o artigo [trabalhar com Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) a partir de ligar a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para obter mais tutoriais a partir de demonstração HealthClinic.biz, consulte o artigo [Tutoriais de ferramentas de programador do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Várias ferramentas Docker

[Algumas ferramentas docker excelente (blogue de Carlos Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Artigos boas

[Introdução ao Microservices a partir do NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Apresentações

- [Sílvio Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introdução ao ASP.NET Core @ construir 2016 - onde que na demonstração](https://channel9.msdn.com/Events/Build/2016/B810)
- [Desenvolver aplicações .NET em contentores, 9 de canal](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
