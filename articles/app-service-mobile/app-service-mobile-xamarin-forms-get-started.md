<properties
    pageTitle="Começar a trabalhar com aplicações Mobile utilizando Xamarin.Forms"
    description="Siga este tutorial para começar a utilizar aplicações do Azure Mobile para o desenvolvimento de Xamarin.Forms"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-xamarinforms-app"></a>Criar uma aplicação de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como adicionar um serviço baseado na nuvem back-end a aplicação móvel Xamarin.Forms utilizando um aplicação do Azure Mobile do back-end. Irá criar um novo back-end de aplicação Mobile e uma simples _lista a fazer_ Xamarin.Forms aplicação que armazena dados de aplicação no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de aplicações móveis para Xamarin.Forms.

##<a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta do Azure active. Se não tiver uma conta, pode inscrever-se para um avaliação do Azure e obter até 10 gratuitos aplicações móveis que pode continuar a utilizar mesmo após a sua avaliação extremidades. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Consulte o artigo [configurar e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obter instruções. 

* Um Mac com o v Xcode 7.0 ou posterior e Xamarin Studio Comunidade instalado. Consulte o artigo [configurar e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [o programa de configuração, instalar e verificações para os utilizadores de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](https://tryappservice.azure.com/?appServiceName=mobile), onde imediatamente pode criar um curto starter aplicação Mobile na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo aplicação de Mobile Azure do back-end

Siga estes passos para criar um novo aplicação Mobile do back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Tem agora aprovisionada um aplicação do Azure Mobile do back-end que pode ser utilizado pelas suas aplicações de cliente móvel. Em seguida, que irá transferir um projeto de servidor para um simples "lista a fazer" back-end e publicá-lo Azure.

## <a name="configure-the-server-project"></a>Configurar o project server

Siga os passos abaixo para configurar o project server para utilizar o Node.js ou .NET back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-xamarinforms-solution"></a>Transferir e executar a solução Xamarin.Forms

Aqui tem algumas opções. Pode transferir a solução para Mac e abra-o no Xamarin Studio ou pode transferir a solução para um computador Windows e abra-o no Visual Studio utilizando um Mac utilizarão para criar a aplicação do iOS. Consulte o artigo [configurar e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) se precisar de instruções mais detalhadas sobre os cenários de configuração de Xamarin.

Vamos continuar:

 1. No seu Mac ou no computador Windows, abra o [Portal do Azure] numa janela do browser.
 2. No pá definições para a sua aplicação móvel, clique em **Começar** (em móvel) > **Xamarin.Forms**. Em passo 3, clique em **criar uma nova aplicação** se ainda não estiver a ser selecionado.  Em seguida, clique no botão **Transferir** .

    Isto transfere um projeto que contém uma aplicação de cliente que está ligada à sua aplicação móvel. Guardar o ficheiro de projeto comprimido o computador local e tome nota do onde guardá-lo.

 3. Extrair o projeto que transferiu e, em seguida, abra-o no Xamarin Studio ou Visual Studio.

    ![][9]

    ![][8]


##<a name="optional-run-the-ios-project"></a>(Opcional) Executar o iOS projecto

Esta secção destina-se a executar o iOS Xamarin projeto para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

####<a name="in-xamarin-studio"></a>No Xamarin Studio

1. Com o botão direito do projecto do iOS e, em seguida, clique em **Definir como projeto de arranque**.
2. No menu **Executar** , clique em **Iniciar depuração** para criar o projecto e inicie a aplicação no iPhone emulador.

####<a name="in-visual-studio"></a>No Visual Studio
1. Com o botão direito do projecto do iOS e, em seguida, clique em **Definir como projeto de arranque**.
2. No menu de **Criar** , clique em **Gestor de configuração**.
3. Na caixa de diálogo **Gestor de configuração** , selecione as caixas de verificação **Criar** e **Implementar** do projeto iOS.
4. Prima a tecla **F5** para criar o projecto e inicie a aplicação no iPhone emulador.

    >[AZURE.NOTE] Se ocorrerem problemas de criar, executar NuGet Gestor de pacotes e atualizar para a versão mais recente da Xamarin suportam pacotes. Por vezes, os projetos no guia de introdução poderão desfasamento na ser atualizadas para a mais recente.    

Na aplicação, escreva texto relevante, tal como _Saber Xamarin_ e, em seguida, clique na **+** botão.

![][10]

Isto envia um pedido de mensagem para o nova aplicação móvel back-end alojado no Azure. Dados a partir do pedido são inseridos na tabela TodoItem. Itens armazenados na tabela são devolvidos pela aplicação móvel back-end e os dados são apresentados na lista.

>[AZURE.NOTE]
> Encontrará o código que acede a sua aplicação móvel do back-end no ficheiro TodoItemManager.cs c# do projeto de biblioteca de classe portable da sua solução.

##<a name="optional-run-the-android-project"></a>(Opcional) Execute o projecto Android

Esta secção destina-se a executar o projeto de droid Xamarin para Android. Pode ignorar esta secção se não estiver a trabalhar com dispositivos Android.

####<a name="in-xamarin-studio"></a>No Xamarin Studio

1. O projeto Android com o botão direito e, em seguida, clique em **Definir como projeto de arranque**.
2. No menu **Executar** , clique em **Iniciar depuração** para criar o projecto e inicie a aplicação num emulador Android.

####<a name="in-visual-studio"></a>No Visual Studio
1. Com o botão direito do projecto Android (Droid) e, em seguida, clique em **Definir como projeto de arranque**.
4. No menu de **Criar** , clique em **Gestor de configuração**.
5. Na caixa de diálogo **Gestor de configuração** , selecione as caixas de verificação **Criar** e **Implementar** do projeto Android.
6. Prima a tecla **F5** para criar o projecto e inicie a aplicação num emulador Android.

    >[AZURE.NOTE] Se ocorrerem problemas de criar, executar NuGet Gestor de pacotes e atualizar para a versão mais recente da Xamarin suportam pacotes. Por vezes, os projetos no guia de introdução poderão desfasamento na ser atualizadas para a mais recente.    


Na aplicação, escreva texto relevante, tal como _Saber Xamarin_ e, em seguida, clique na **+** botão.

![][11]

Isto envia um pedido de mensagem para o nova aplicação móvel back-end alojado no Azure. Dados a partir do pedido são inseridos na tabela TodoItem. Itens armazenados na tabela são devolvidos pela aplicação móvel back-end e os dados são apresentados na lista.

> [AZURE.NOTE]
> Encontrará o código que acede a sua aplicação móvel do back-end no ficheiro TodoItemManager.cs c# do projeto de biblioteca de classe portable da sua solução.


##<a name="optional-run-the-windows-project"></a>(Opcional) Execute o projecto do Windows


Esta secção destina-se a executar o projecto Xamarin WinApp para dispositivos com Windows. Pode ignorar esta secção se não estiver a trabalhar com dispositivos com Windows.


####<a name="in-visual-studio"></a>No Visual Studio
1. Botão direito do rato qualquer um dos projectos Windows e, em seguida, clique em **Definir como projeto de arranque**.
4. No menu de **Criar** , clique em **Gestor de configuração**.
5. Na caixa de diálogo **Gestor de configuração** , selecione as caixas de verificação **Criar** e **Implementar** do projeto Windows que tenha escolhido.
6. Prima a tecla **F5** para criar o projecto e inicie a aplicação no emulador do Windows.

    >[AZURE.NOTE] Se ocorrerem problemas de criar, executar NuGet Gestor de pacotes e atualizar para a versão mais recente da Xamarin suportam pacotes. Por vezes, os projetos no guia de introdução poderão desfasamento na ser atualizadas para a mais recente.    


Na aplicação, escreva texto relevante, tal como _Saber Xamarin_ e, em seguida, clique na **+** botão.

Isto envia um pedido de mensagem para o nova aplicação móvel back-end alojado no Azure. Dados a partir do pedido são inseridos na tabela TodoItem. Itens armazenados na tabela são devolvidos pela aplicação móvel back-end e os dados são apresentados na lista.

![][12]

> [AZURE.NOTE]
> Encontrará o código que acede a sua aplicação móvel do back-end no ficheiro TodoItemManager.cs c# do projeto de biblioteca de classe portable da sua solução.

##<a name="next-steps"></a>Próximos passos

* [Adicionar autenticação para a sua aplicação](app-service-mobile-xamarin-forms-get-started-users.md)  
Saiba como autenticar os utilizadores da sua aplicação com um fornecedor de identidade.

* [Adicionar as notificações push para a sua aplicação](app-service-mobile-xamarin-forms-get-started-push.md)  
Saiba como adicionar push notificações de suporte para a sua aplicação e configurar a sua aplicação Mobile do back-end a utilizar o Azure notificação concentradores para enviar notificações push.

* [Ativar a sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar a sua aplicação utilizando um back-end aplicação Mobile suporte offline. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede.

* [Como utilizar o cliente gerido para as aplicações móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
Saiba como trabalhar com o cliente gerido SDK na sua aplicação Xamarin. 


<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/

