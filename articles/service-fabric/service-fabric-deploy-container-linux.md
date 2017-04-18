<properties
   pageTitle="Service ferro e implementar contentores no Linux | Microsoft Azure"
   description="Ferro e a utilização de contentores Docker para implementar microservice aplicações de serviço. Este artigo descreve as funcionalidades que fornece ferro de serviço de membros em contentores e como implementar uma imagem de contentor de Docker num cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="deploy-a-docker-container-to-service-fabric"></a>Implementar um contentor Docker ferro de serviço

> [AZURE.SELECTOR]
- [Implementar o contentor Windows](service-fabric-deploy-container.md)
- [Implementar Docker contentor](service-fabric-deploy-container-linux.md)

Este artigo explica o criação de serviços em contentores em contentores de Docker no Linux.

Serviço ferro tem várias funcionalidades de contentor que ajudarão-lo com a criação de aplicações que são compostos por de microservices que estão em contentores. Estes são chamados em contentores de serviços.

As capacidades de incluem;

- Implementação da imagem contentor e ativação
- Governação de recursos
- Autenticação do repositório
- Porta de contentor para o mapeamento de porta do anfitrião
- Contentor para outro deteção e avaliação de comunicação
- Capacidade de configurar e definir variáveis de ambiente


## <a name="packaging-a-docker-container-with-yeoman"></a>Embalagem um contentor docker com yeoman
Quando embalagem um contentor no Linux, pode escolher quer ao utilizar um modelo yeoman ou [criar manualmente o pacote de aplicação](service-fabric-deploy-container.md#manually-packaging-and-deploying-a-container).

Uma aplicação de serviço ferro pode conter um ou mais membros em contentores, cada uma com uma função específica no faz a funcionalidade da aplicação. O SDK do ferro de serviço para Linux inclui um gerador de [Yeoman](http://yeoman.io/) torna mais fácil criar uma aplicação e adicionar uma imagem de contentor. Vamos utilizar Yeoman para criar uma nova aplicação com um único contentor Docker denominado *SimpleContainerApp*. Pode adicionar que mais serviços mais tarde editando a gerado manifesto ficheiros.

## <a name="create-the-application"></a>Criar a aplicação

1. Num terminal, escreva **yo azuresfguest**.

2. Para o framework escolha **contentor** .

3. Atribuir um nome da aplicação, por exemplo, SimpleContainerApp

4. Fornece o URL para a imagem em contentor a partir de um repo DockerHub. Este botão leva o formulário [repo] / [nome da imagem]

![Serviço ferro Yeoman gerador de membros em contentores][sf-yeoman]

## <a name="deploy-the-application"></a>Implementar a aplicação

Quando a aplicação estiver compilada, pode implementá-lo ao cluster local utilizando o clip do Azure.

1. Ligar para o local cluster de serviço ferro.

    ```bash
    azure servicefabric cluster connect
    ```

2. Utilize o script de instalação fornecido no modelo para copiar o pacote de aplicação para o arquivo de imagem do cluster, registe-se o tipo de aplicação e criar uma instância da aplicação.

    ```bash
    ./install.sh
    ```

3. Abra um browser e navegue para o serviço ferro Explorer na http://localhost:19080/Explorer (substituir localhost com o IP privado da VM se utilizar Vagrant no Mac OS X).

4. Expanda o nó de aplicações e tenha em atenção que agora existe uma entrada para o seu tipo de aplicação e outro para a primeira instância desse tipo.

5. Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação e anular registo o tipo de aplicação.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Próximos passos

- [Descrição geral de serviço ferro e contentores](service-fabric-containers-overview.md)
- [Interagir com clusters de serviço ferro utilizando o clip do Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png

