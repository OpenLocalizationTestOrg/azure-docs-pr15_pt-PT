<properties
   pageTitle="Configurar o seu ambiente de desenvolvimento no Mac OS X | Microsoft Azure"
   description="Instale o tempo de execução, SDK e ferramentas e criar um cluster de desenvolvimento local. Depois de concluir esta configuração, estará pronto para criar aplicações no Mac OS X."
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
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar o seu ambiente de desenvolvimento no Mac OS X

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [X](service-fabric-get-started-mac.md)

Pode criar aplicações de serviço ferro a executar num clusters Linux utilizando o Mac OS X. Este artigo aborda como configurar o seu Mac para o desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos

Ferro de serviço não é executado vierem nos X. Para executar um cluster de serviço ferro local, fornecemos-uma máquina de virtual Ubuntu pré-configurada utilizando Vagrant e VirtualBox. Antes de começar, é necessário:

- [Vagrant (v1.8.4 ou posterior)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>Criar a VM local

Para criar a VM local que contenha um cluster de serviço ferro 5-node, faça o seguinte:

1. Clonar a repo Vagrantfile

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Navegue para o local clonar do repo

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Opcional) Modificar as definições de VM predefinidas

    Por predefinição, a VM local está configurada da seguinte forma:

    - 3 GB de memória atribuída
    - Rede privada anfitrião configurada IP 192.168.50.50 ativar pass-through de tráfego de anfitrião do Mac

    Pode alterar qualquer uma destas definições ou adicionar outra configuração para a VM na Vagrantfile. Consulte a [documentação de Vagrant](http://www.vagrantup.com/docs) para a lista completa das opções de configuração.

4. Criar a VM

    ```bash
    vagrant up
    ```

    Este passo transfere a imagem VM pré-configurado, arranque localmente mesmo e, em seguida, configurar um ferro local do serviço de cluster no mesmo. Devo esperar para demorar alguns minutos. Se o programa de configuração é concluída com êxito, verá uma mensagem no resultado que indica que o cluster está a iniciar.

    ![Programa de configuração iniciar seguir VM aprovisionamento de cluster][cluster-setup-script]

5. Teste o cluster tiver sido configurado corretamente ao navegar para o serviço ferro Explorer na http://192.168.50.50:19080/Explorer (partindo do princípio de que a par de IP da rede privada a predefinição).

    ![Serviço ferro Explorer visualizados do anfitrião do Mac][sfx-mac]


## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Instalar o plug-in do serviço ferro para Eclipse Neon (opcional)

Serviço ferro fornece um plug-in para IDE Neon Eclipse que pode simplificar o processo de criação e implementação dos serviços de Java.

1. No Eclipse, certifique-se de que tem Buildship versão 1.0.17 ou posterior instalado. Pode verificar as versões dos componentes instalados ao selecionar **Ajuda > detalhes da instalação**. Pode atualizar Buildship utilizando as instruções [aqui][buildship-update].

2. Para instalar o plug-in ferro de serviço, selecione **Ajuda > instalar novo Software …**

3. Na caixa de texto "Trabalhar com", introduza: http://dl.windowsazure.com/eclipse/servicefabric.

4. Clique em Adicionar.

    ![Plug-in do Eclipse Neon para ferro de serviço][sf-eclipse-plugin-install]

5. Selecione o plug-in ferro de serviço e clique em seguinte.

6. Prossiga com a instalação e aceite o contrato de licença do utilizador final.

## <a name="next-steps"></a>Próximos passos

- [Criar a sua aplicação de serviço ferro primeira de Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Criar um cluster de ferro de serviço no portal do Azure](service-fabric-cluster-creation-via-portal.md)
- [Criar um cluster de serviço ferro utilizando o Gestor de recursos do Azure](service-fabric-cluster-creation-via-arm.md)
- [Compreender o modelo de aplicação de serviço ferro](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
