<properties
   pageTitle="Criar uma aplicação de serviço ferro primeira no Linux utilizando c# | Microsoft Azure"
   description="Criar e implementar uma aplicação de serviço ferro utilizando C#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>


# <a name="create-your-first-azure-service-fabric-application"></a>Criar uma aplicação do Azure Service ferro primeira

> [AZURE.SELECTOR]
- [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

Serviço ferro fornece SDK para criação de serviços no Linux no .NET Core e Java. Neste tutorial, vamos veja como criar uma aplicação para Linux e criar um serviço utilizando c# (.NET Core).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem de [configurar o seu ambiente de desenvolvimento Linux](service-fabric-get-started-linux.md). Se estiver a utilizar o Mac OS X, pode [configurar um ambiente de uma caixa Linux numa máquina virtual utilizando Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Criar a aplicação

Uma aplicação de serviço ferro pode conter um ou mais serviços, cada uma com uma função específica no faz a funcionalidade da aplicação. O SDK do ferro de serviço para Linux inclui um gerador de [Yeoman](http://yeoman.io/) torna mais fácil para criar o primeiro serviço e para adicionar mais tarde. Vamos utilizar Yeoman para criar uma aplicação com um único serviço.

1. Num terminal, escreva o seguinte comando para começar a construir andaime:`yo azuresfcsharp`

2. Dê um nome da aplicação.

3. Escolha o tipo do seu serviço primeiro e o nome. Para efeitos neste tutorial, podemos escolher um serviço do Ator fiável.

  ![Gerador de serviço ferro Yeoman para C#][sf-yeoman]

>[AZURE.NOTE] Para mais informações sobre as opções, consulte o artigo [Descrição geral do modelo programação ferro de serviço](service-fabric-choose-framework.md).

## <a name="build-the-application"></a>Criar a aplicação

Os modelos de serviço ferro Yeoman incluem um script de compilação que pode utilizar para criar a aplicação a partir do terminal (depois de navegar para a pasta de aplicações).

  ```bash
 cd myapp 
 ./build.sh 
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicie o cliente de teste e executar uma activação pós-falha

Projetos do ator não fazer nada sozinho. Necessitam de outro serviço ou cliente para lhes envia mensagens. O modelo do ator inclui um script de teste simples que pode utilizar para interagir com o serviço do ator.

1. Execute o script utilizando o utilitário de monitorização para ver os resultados do serviço do ator.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. No Explorador de ferro de serviço, localize o nó que aloja a réplica principal para o serviço do ator. A captura de ecrã abaixo, é nó 3.

    ![Localizar a réplica principal no serviço ferro Explorer][sfx-primary]

3. Clique no nó que se encontram no passo anterior e, em seguida, selecione **desativar (reiniciar)** a partir do menu de ações. Esta ação reinicia um de cinco nós no seu cluster local forçar uma activação pós-falha a uma réplica secundária em execução noutro nó. Como efetuar esta ação, paga a atenção para o resultado a partir de cliente de teste e repare que o contador continua a incrementar apesar da activação pós-falha.


## <a name="next-steps"></a>Próximos passos

- [Saiba mais sobre fiável intervenientes](service-fabric-reliable-actors-introduction.md)
- [Interagir com clusters de serviço ferro utilizando o clip do Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
