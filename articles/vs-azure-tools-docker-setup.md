<properties
   pageTitle="Configurar um anfitrião Docker com VirtualBox | Microsoft Azure"
   description="Instruções passo a passo para configurar uma instância de Docker predefinida utilizando Docker máquina e VirtualBox"
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
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="configure-a-docker-host-with-virtualbox"></a>Configurar um anfitrião Docker com VirtualBox

## <a name="overview"></a>Descrição geral
Este artigo ajuda configurar uma instância de Docker predefinida utilizando Docker máquina e VirtualBox. Se estiver a utilizar o [beta Docker para Windows](http://beta.docker.com/), esta configuração não é necessária.

## <a name="prerequisites"></a>Pré-requisitos
As seguintes ferramentas têm de ser instalados.

- [Caixa de ferramentas de docker](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configurar o cliente de Docker com o Windows PowerShell

Para configurar um cliente Docker, simplesmente abrir o Windows PowerShell e execute os seguintes passos:

1. Crie uma instância de anfitrião de docker predefinida.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Verifique se que a instância predefinida está configurado e a ser executado. (Deverá ver uma instância denominada 'predefinido' em execução.

    ```PowerShell
    docker-machine ls 
    ```
        
    ![resultado de máquina docker ls][0]
 
1. Predefinir como o anfitrião atual e configurar o seu shell.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Apresente os contentores Docker ativos. A lista deve estar vazia.

    ```PowerShell
    docker ps
    ```

    ![resultado de ps docker][1]
 
> [AZURE.NOTE]Sempre que reinicie o computador de desenvolvimento, terá de reiniciar o seu anfitrião de local docker.
> Para tal, execute o seguinte comando na linha de comandos de problema: `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
