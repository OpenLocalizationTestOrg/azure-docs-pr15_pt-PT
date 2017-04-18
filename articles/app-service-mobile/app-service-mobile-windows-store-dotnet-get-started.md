<properties
    pageTitle="Criar um Universal Windows plataforma (UWP) que utiliza na aplicações móveis | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar com a utilização de back-ends do Azure aplicação móvel para a programação da aplicações Universal Windows plataforma (UWP) no c#, do Visual Basic ou JavaScript."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-windows-app"></a>Criar uma aplicação do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como adicionar um serviço baseado na nuvem back-end para uma aplicação Universal Windows plataforma (UWP). Para mais informações, consulte o artigo [o que são aplicações Mobile](app-service-mobile-value-prop.md). Seguem-se as capturas de ecrã a partir da aplicação concluída:

![Aplicação de ambiente de trabalho concluída](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Em execução num ambiente de trabalho. 

![Aplicação phone concluída](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Em execução num telemóvel

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de aplicação Mobile para UWP aplicações. 

##<a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta do Azure active. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 aplicações móveis gratuitas que pode continuar a utilizar mesmo após a sua avaliação extremidades. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Comunidade 2015] ou uma versão posterior.

>[AZURE.NOTE] Se pretender começar com a aplicação de serviço de Azure antes de se inscrever para uma conta do Azure, aceda ao [Serviço de aplicação tente](https://tryappservice.azure.com/?appServiceName=mobile). Não existem, imediatamente pode criar uma aplicação móvel starter curto na aplicação de serviço — sem cartão de crédito obrigatório e sem compromissos.

##<a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo aplicação de Mobile Azure do back-end

Siga estes passos para criar um novo aplicação Mobile do back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Tem agora aprovisionada um aplicação do Azure Mobile do back-end que pode ser utilizado pelas suas aplicações de cliente móvel. Em seguida, que irá transferir um projeto de servidor para um simples "lista a fazer" back-end e publicá-lo Azure.

## <a name="configure-the-server-project"></a>Configurar o project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-client-project"></a>Transferir e executar o projeto de cliente

Depois de ter configurado o seu back-end aplicação Mobile, pode criar uma nova aplicação de cliente ou modificar uma aplicação existente para ligar ao Azure. Nesta secção, transfira um projeto de modelo de aplicação UWP é personalizado para ligar à sua aplicação Mobile do back-end.

1. Novamente na pá **Guia de introdução** para o seu back-end aplicação Mobile, clique em **criar uma nova aplicação** > **Transferir**, em seguida, extrair projeto comprimido ficheiros para o seu computador local.

    ![Transferir o projeto de guia de introdução do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

3. (Opcional) Adicione o projecto de aplicação UWP à mesma solução como o project server. Isto torna mais fácil depurar e testar a aplicação e o back-end na mesma solução Visual Studio, se optar por fazê-lo. Para adicionar um projeto de aplicação UWP para a solução, tem de estar a utilizar o Visual Studio 2015 ou uma versão posterior.

4. Com a aplicação UWP como o projeto de arranque, prima a tecla F5 para implementar e executar a aplicação.

5. Na aplicação, escreva o texto relevante, tal como *concluída o tutorial*, na caixa de texto **Inserir um TodoItem** e, em seguida, clique em **Guardar**.

    ![Ambiente de trabalho concluída Windows guia de introdução](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Isto envia um pedido de mensagem para o nova aplicação móvel back-end que está alojado no Azure.

6. (Opcional) Parar a aplicação e reinicie-o num dispositivo diferente ou emulador móvel.

    ![Telefone de completo de guia de introdução do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Repare que os dados guardados no passo anterior são carregados a partir do Azure depois de inicia a aplicação UWP. 

##<a name="next-steps"></a>Próximos passos

* [Adicionar autenticação para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Saiba como autenticar os utilizadores da sua aplicação com um fornecedor de identidade.

* [Adicionar as notificações push para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar push notificações de suporte para a sua aplicação e configurar a sua aplicação Mobile do back-end a utilizar o Azure notificação concentradores para enviar notificações push.

* [Ativar a sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar a sua aplicação utilizando um back-end aplicação Mobile suporte offline. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Comunidade do Visual Studio 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203
