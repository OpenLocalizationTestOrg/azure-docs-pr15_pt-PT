<properties
   pageTitle="Implementar um contentor de ASP.NET Core Linux Docker para um anfitrião Docker remoto | Microsoft Azure"
   description="Saiba como utilizar o Visual Studio Tools para Docker para implementar uma aplicação web do ASP.NET Core um contentor Docker em execução num Azure Docker anfitrião Linux VM"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="mlearned"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="mlearned"/>

# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Implementar um contentor ASP.NET para um anfitrião Docker remoto

## <a name="overview"></a>Descrição geral
Docker é um motor de contentor lightweight, semelhante em algumas formas de uma máquina de virtual, pode utilizar para o anfitrião aplicações e serviços.
Neste tutorial orienta utilizando a extensão do [Visual Studio 2015 Tools para Docker](http://aka.ms/DockerToolsForVS) para implementar uma aplicação do ASP.NET Core para um anfitrião Docker no Azure através do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, é necessário o seguinte procedimento:

- Criar um VM do Azure Docker anfitrião conforme descrito no [artigo como utilizar docker-máquina com Azure](./virtual-machines/virtual-machines-linux-docker-machine.md)
- Instalar a [Atualização de 2015 do Visual Studio 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [SDK do Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
- Instalar [Ferramentas de 2015 do Visual Studio para Docker - pré-visualização](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1. a criar uma aplicação web do ASP.NET Core
Os passos seguintes irão guiá-lo através de criar uma aplicação de ASP.NET Core básica que será utilizada neste tutorial.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Adicione suporte Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. utilizar o Script do PowerShell DockerTask.ps1 

1.  Abra uma linha de comandos do PowerShell para o diretório de raiz do seu projeto. 

    ```
    PS C:\Src\WebApplication1>
    ```

1.  Validar o controlo remoto anfitrião está em execução. Deverá ver estado = em execução 

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] Se estiver a utilizar a versão Beta Docker, o seu sistema anfitrião não estará listado aqui.

1.  Criar a aplicação utilizando o compilação parâmetro -

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] Se estiver a utilizar a versão Beta do Docker, omita - máquina argumento
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
    > ```  


1.  Executar a aplicação, utilizando o executar parâmetro -

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] Se estiver a utilizar a versão Beta Docker, omita - máquina argumento
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
    > ```

    Assim que docker for concluída, deverá ver os resultados semelhantes ao seguinte:

    ![Ver a sua aplicação][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
