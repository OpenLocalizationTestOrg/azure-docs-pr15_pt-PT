<properties 
   pageTitle="Adicionar serviços Mobile utilizando serviços ligados no Visual Studio | Microsoft Azure"
   description="Adicionar serviços Mobile utilizando a caixa de diálogo do Visual Studio adicionar serviços ligados"
   services="visual-studio-online"
   documentationCenter="na"
   authors="mlhoop"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="12/16/2015"
   ms.author="mlearned" />

# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Adicionar serviços de Mobile utilizando o Visual Studio ligado Services

Com o Visual Studio 2015, pode ligar aos serviços de Mobile do Azure utilizando a caixa de diálogo **Adicionar serviço ligado** . Pode ligar a partir de qualquer aplicação de cliente c#, qualquer aplicação JavaScript ou em diferentes plataformas Cordova aplicação. Depois de se ligar, pode criar e aceder aos dados, criar APIs personalizados e as tarefas agendadas ou adicionar suporte para notificações push.  A operação de serviços ligados adiciona todas as referências adequadas e código de ligação. Também pode tirar partido de incorporados suporte para autenticação com uma variedade de esquemas de identidade popular, tal como do Azure AD, Facebook, Twitter e Accounts Microsoft.

## <a name="supported-project-types"></a>Tipos de projecto suportados

>[AZURE.NOTE] No Visual Studio 2015, adicionar Azure Mobile serviços a um projetos Universal Windows (Windows 10) utilizando a caixa de diálogo Adicionar serviços ligados não é suportada. Pode adicionar serviços de Mobile Azure instalando pacotes adequados através do Gestor de pacote NuGet para o seu projeto.

Pode utilizar a caixa de diálogo Serviços ligados para ligar aos serviços do Azure Mobile nos seguintes tipos de projeto.

- Projetos .NET da loja Windows 8.1, telefone e aplicação Universal

- Projetos JavaScript da loja Windows 8.1, telefone e aplicação Universal

- Projetos criados utilizando o Visual Studio Tools para Apache Cordova


## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Ligar aos serviços de Mobile do Azure utilizando a caixa de diálogo Adicionar serviços ligados

1. Certifique-se de que tem uma conta Azure. Se não tiver uma conta Azure, pode inscrever-se para uma [versão de avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).

1. Abra a caixa de diálogo **Adicionar serviços ligados** .
 - Para as aplicações do .NET, abra o projeto no Visual Studio, abrir o menu de contexto para o nó **referências** no Explorador de solução e, em seguida, selecione **Adicionar serviço ligado**
 
        ![Connecting to Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - Para Apache Cordova projetos de aplicação, abra o projeto no Visual Studio, abrir o menu de contexto para o nó do projeto no Explorador de solução e, em seguida, selecione **Adicionar serviço ligado**.

1. Na caixa de diálogo **Adicionar serviço ligado** , selecione **Azure Mobile serviços**e, em seguida, selecione o botão **Configurar** . Poderá ser-lhe pedido que inicie sessão no Azure se ainda não o tiver feito.

    ![Adicionar um serviço móvel do Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. Na caixa de diálogo **Serviços de Mobile Azure** , escolha um serviço móvel existente se tiver um. Se necessitar de criar um novo serviço móvel Azure, siga o procedimento abaixo para fazê-lo. Caso contrário, avance para o passo seguinte.

    Para criar uma nova conta de serviço móvel:
    1. Selecione a ligação de **Serviço criar **na parte inferior da caixa de diálogo.
        ![Adicionar novo serviço móvel do ligada](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)




    2. Na caixa de diálogo **Criar serviço móvel** , pode escolher um serviço móvel do JavaScript back-end ou um serviço móvel do .NET back-end a partir da lista pendente de **Runtime** . 
  
        ![Criação de um serviço móvel](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        Um serviço de back-end JavaScript é simples e poderoso. Se criar um serviço móvel do JavaScript back-end, o código de JavaScript do lado do servidor é armazenado na nuvem, mas pode editar scripts de servidor utilizando o Explorador do servidor ou o portal de gestão Azure. 

        Um serviço móvel do .NET back-end dá-lhe todas as potencialidades e flexibilidade da Web API e Framework entidade. Se criar um serviço móvel do .NET back-end, um projeto é criado por si e adicionado à sua solução. 

    1. Selecione a **região** onde pretende que o serviço móvel e, em seguida, introduza um nome de utilizador e palavra-passe para o servidor.
 
    1. Após introduzir as informações necessárias, selecione o botão **Criar** para criar o serviço móvel.
    2. O novo serviço móvel deverá aparecer na lista de serviço na caixa de diálogo **Serviços de Mobile Azure** . Selecione o novo serviço móvel na lista e, em seguida, selecione no botão **Adicionar** para adicionar o serviço ao seu projeto.
    

1. Reveja a página de introdução ao obter que aparece e descubra como o seu projeto foi modificado. Sempre que adiciona um serviço ligado, é apresentada uma página de introdução no seu browser. Pode rever os passos seguintes sugestões e exemplos de código ou mudar para a página o que aconteceu para ver que referências foram adicionadas ao seu projeto e como os seus ficheiros de código e configuração foram modificados.

1. Utilizar os exemplos de código como guia, comece a escrever código para aceder ao serviço móvel!

## <a name="how-your-project-is-modified"></a>Como o seu projeto é modificado

Modo como o Visual Studio modifica projeto depende do tipo de projeto. Para c# aplicações de cliente, consulte o artigo [o que aconteceu – c# projetos](http://go.microsoft.com/fwlink/p/?LinkId=513119). Para as aplicações de cliente JavaScript, consulte o artigo [o que aconteceu – JavaScript projetos](http://go.microsoft.com/fwlink/p/?LinkId=513120). Para Cordova aplicações, consulte o artigo [o que aconteceu – Cordova projetos](http://go.microsoft.com/fwlink/p/?LinkId=513116).


##<a name="next-steps"></a>Próximos passos

Colocar questões e obter ajuda: 

 - [Fórum MSDN: De serviços móveis Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [Azure serviços móveis no blogue da equipa do Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)

 - [Azure serviços móveis na azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)

 - [Documentação de serviços móveis Azure em azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)



