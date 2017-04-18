<properties
   pageTitle="Criar um projeto Azure com Visual Studio | Microsoft Azure"
   description="Criar um projeto Azure com Visual Studio"
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

# <a name="creating-an-azure-project-with-visual-studio"></a>Criar um projeto Azure com o Visual Studio

As ferramentas de Azure para Visual Studio fornecer um modelo que lhe permite criar um serviço na nuvem para Azure. As ferramentas de também ajudam a configurar, depurar e implementar o serviço de nuvem para Azure.

Uma solução de serviço de nuvem Azure contém os seguintes tipos de projetos:

- **Azure project**

    O projecto Azure tem associações para os projectos de função da solução. Também inclui a definição de serviço e os ficheiros de configuração do serviço. O ficheiro de definição de serviço define as definições de tempo de execução do seu pedido, incluindo funções de que são necessárias, pontos finais e tamanho de máquina virtual. O ficheiro de configuração do serviço configura quantas instâncias de uma função são executadas e os valores das definições definidas para uma função. Para obter mais informações sobre estas definições, consulte o artigo [como: configurar as funções para um serviço em nuvem Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

- **Projecto de funções da Web**

    Uma função de trabalho executa o processamento de fundo. Uma função de trabalho pode comunicar com os serviços de armazenamento e com outros serviços baseados na Internet. Uma função de trabalho pode ter qualquer número de pontos finais HTTP, HTTPS ou TCP.

    - **Função de Web do ASP.NET**, para criar uma aplicação com um front-end web do ASP.NET
    - **Função de Web do ASP.NET MVC5**
    - **Função de Web do ASP.NET MVC4**
    - **Função de Web do ASP.NET MVC3**
    - **Função de Web do serviço WCF**, para a criação de um serviço WCF
    - **Função de Web do Silverlight negócio aplicação** (requer o Visual Studio 2012http)

- **Função de trabalho de cache**

    Função que fornece uma cache dedicada para a sua aplicação.

- **Função de trabalho com fila de Bus de serviço**

    Fila de bus de serviço que fornece uma funcionalidade de colocação de mensagem para comunicar com o processo de trabalho. Para mais informações, consulte o artigo [como utilizar serviço Bus filas](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Para criar um projeto de serviço de nuvem Azure no Visual Studio

1. Inicie o Microsoft Visual Studio como administrador.

1. Na barra de menus, selecione o **ficheiro**, **Novo** **projeto**.

1. No painel de **Tipos de projeto** , escolha **nuvem** do projeto Visual c# ou do Visual Basic nós de modelo.

1. No painel de **modelos** , escolha **Serviço em nuvem Azure**.

1. Especificar qual a versão do .NET Framework que pretende utilizar para desenvolver o seu projeto.

1. Introduza um nome e uma localização para o seu projeto e um nome para a solução. Selecione o botão **OK** .

1. Na caixa de diálogo **Novo projeto de Azure** , selecione as funções que pretende adicionar e escolha o botão de seta para a direita para adicioná-los à sua solução. Pode adicionar quantas funções que precisar.

1. Para mudar o nome de uma função que adicionou ao seu projeto, paire sobre o papel na caixa de diálogo **Novo projeto do Azure** e selecione o ícone **Mudar o nome** para a direita da função. Também pode mudar uma função dentro da sua solução depois foi adicionado.
