<properties
   pageTitle="Configurar o seu ambiente de desenvolvimento Linux | Microsoft Azure"
   description="Instale o tempo de execução e SDK e criar um cluster de desenvolvimento local no Linux. Depois de concluir esta configuração, estará pronto para criar aplicações."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# <a name="prepare-your-development-environment-on-linux"></a>Preparar o seu ambiente de desenvolvimento em Linux


> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [X](service-fabric-get-started-mac.md)

 Para implementar e executar o [Azure Service ferro aplicações](service-fabric-application-model.md) no seu computador de desenvolvimento Linux, instale o tempo de execução e SDK comuns. Também pode instalar o SDK opcional para Java e .NET Core.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="supported-operating-system-versions"></a>Versões de sistemas operativos suportados
As seguintes versões de sistemas operativos são suportadas para o desenvolvimento:

- Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>Atualizar as suas fontes possam

Para instalar o SDK e o pacote de associado do runtime através do possam obter, primeiro tem de actualizar suas fontes provável.

1. Abra um terminal.
2. Adicione o repo serviço ferro à sua lista de origens.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Adicione a nova chave GPG para o seu porta-chaves provável.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Atualize as suas listas de pacote com base em repositórios recentemente adicionados.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Instalar e configurar o SDK

Assim que as suas fontes são atualizadas, pode instalar o SDK.

1. Instale o pacote de serviço ferro SDK. Será pedido para confirmar a instalação e para aceitar o contrato de licença.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Execute o script de configuração SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>Configurar o clip em diferentes plataformas Azure

O [clip do Azure em diferentes plataformas] [ azure-xplat-cli-github] inclui comandos para interagir com entidades ferro de serviço, incluindo clusters e as aplicações. Baseia-se Node.js por isso, [Certifique-se de que instalou o nó] [ install-node] antes de prosseguir com as instruções abaixo.

1. Clonar repo o github para o seu computador de desenvolvimento.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Mudar para o repo clonado e instale dependências o clip através do Gestor de pacote nó (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Crie um symlink a partir da pasta Reciclagem/azure do repo clonado a /usr/bin/azure para que é adicionado ao seu caminho e comandos estão disponíveis a partir de qualquer directório.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Por fim, ative os comandos de serviço ferro a conclusão automática.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Configurar um cluster local

Se tudo o que tem instalada com êxito, deverá conseguir iniciar um cluster local.

1. Execute o script de configuração de cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Abra um browser e navegue para http://localhost:19080/Explorer. Se tiver iniciado cluster, deverá ver o dashboard do serviço ferro Explorer.

    ![Serviço ferro Explorer em Linux][sfx-linux]

Neste momento, que conseguem implementar pacotes de aplicações de serviço ferro pré-concebidos ou novos com base em contentores de convidado ou executáveis convidado. Para criar novos serviços utilizando o Java ou .NET Core SDK, siga os passos de configuração opcionais abaixo.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Instalar o Java SDK e Eclipse Neon Plug-in (opcional)

O SDK do Java fornece bibliotecas e modelos de necessários para criar serviços de serviço ferro utilizando Java.

1. Instale o pacote de Java SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Execute o script de configuração SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Pode instalar o plug-in Eclipse para ferro de serviço a partir de dentro Eclipse Neon IDE.

1. No Eclipse, certifique-se de que tem Buildship versão 1.0.17 ou posterior instalado. Pode verificar as versões dos componentes instalados ao selecionar **Ajuda > detalhes da instalação**. Pode atualizar Buildship utilizando as instruções [aqui][buildship-update].

2. Para instalar o plug-in ferro de serviço, selecione **Ajuda > instalar novo Software …**

3. Na caixa de texto "Trabalhar com", introduza: http://dl.windowsazure.com/eclipse/servicefabric

4. Clique em Adicionar.

    ![Plug-in do Eclipse][sf-eclipse-plugin]

5. Selecione o plug-in ferro de serviço e clique em seguinte.

6. Prossiga com a instalação e aceite o contrato de licença do utilizador final.

## <a name="install-the-net-core-sdk-optional"></a>Instale o .NET Core SDK (opcional)

O .NET Core SDK fornece bibliotecas e modelos de necessários para criar serviços de serviço ferro utilizando em diferentes plataformas .NET Core.

1. Instale o pacote de .NET Core SDK.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Execute o script de configuração SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Próximos passos

- [Criar uma aplicação de Java primeira no Linux](service-fabric-create-your-first-linux-application-with-java.md)

- [Preparar o seu ambiente de desenvolvimento nos x](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
