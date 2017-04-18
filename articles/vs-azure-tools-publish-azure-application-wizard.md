<properties 
   pageTitle="Publicar o Assistente da aplicação Azure | Microsoft Azure"
   description="Aplicação Azure Assistente de publicação"
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

# <a name="publish-azure-application-wizard"></a>Aplicação Azure Assistente de publicação

## <a name="overview"></a>Descrição geral

Depois de desenvolver uma aplicação web no Visual Studio, pode publicar dessa aplicação mais facilmente para um serviço em nuvem Azure utilizando o Assistente da **Aplicação de Azure publicar** . A primeira secção explica os passos que tem de concluir antes de utilizar o assistente e as secções restantes explicam as funcionalidades do assistente.

>[AZURE.NOTE] Este tópico é sobre como implementar aos serviços em nuvem, não a web sites. Para obter informações sobre como implementar a web sites, consulte o artigo [como implementar um Web Site do Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder publicar a aplicação web Azure, tem de ter uma conta Microsoft e uma subscrição do Azure e tiver que associar a aplicação web um serviço em nuvem Azure. Se já tiver concluído estas tarefas, pode avançar para a secção seguinte.

1. Obter uma conta Microsoft e uma subscrição do Azure. Que pode experimentar uma subscrição de Azure gratuita um mês gratuito [aqui](https://azure.microsoft.com/pricing/free-trial/)

1. Crie um serviço na nuvem e uma conta de armazenamento no Azure. Pode fazê-lo a partir do Explorador de servidor no Visual Studio, ou utilizando o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Ative a sua aplicação web para Azure. Para ativar a aplicação web ser publicado Azure a partir do Visual Studio, terá de associá-la com um projeto de serviço de nuvem Azure no Visual Studio. Para criar o projeto de serviço de nuvem associado, abrir o menu de atalho para o projeto para a aplicação web e, em seguida, selecione converter, **Converter para o projeto de serviço de nuvem Azure**.

1. Depois do projeto de serviço de nuvem é adicionado à sua solução, abra novamente o menu de atalho mesmo e, em seguida, selecione **Publicar**. Para obter mais informações sobre como ativar aplicações para o Azure, consulte o artigo [como: migrar e publicar uma aplicação Web para um serviço em nuvem Azure a partir do Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx).

>[AZURE.NOTE] Certifique-se de que inicie o Visual Studio com credenciais de administrador (executar como administrador).

1. Quando estiver pronto para publicar a sua aplicação, abrir o menu de atalho para o projeto de serviço de nuvem Azure e, em seguida, selecione **Publicar**. Os seguintes passos mostram o Assistente de aplicação de Azure publicar.

## <a name="choosing-your-subscription"></a>Escolher a sua subscrição

### <a name="to-choose-a-subscription"></a>Para escolher uma subscrição

1. Antes de utilizar o assistente pela primeira vez, tem de iniciar sessão. Selecione a ligação **Sign In** . Inicie sessão no portal do Azure quando lhe for pedido e fornecer o nome de utilizador Azure e a palavra-passe. 

    ![Este é um dos ecrãs de Assistente de publicação](./media/vs-azure-tools-publish-azure-application-wizard/IC799159.png)

    A lista de subscrições preenche com subscrições associadas à sua conta. Também poderá ver subscrições a partir de qualquer ficheiros de subscrição que importou anteriormente.

1. Na lista **Escolher a sua subscrição** , selecione a subscrição a utilizar para esta implementação.

   Se optar por **< … gerir >**, é apresentada a caixa de diálogo **Gerir subscrições** e pode escolher a conta de utilizador e de subscrição que pretende utilizar. No separador **contas** mostra todas as suas contas e o separador de **subscrições** mostra todas as subscrições associadas com as contas. Também pode escolher uma região a partir do qual de utilizar recursos Azure, bem como criar ou importar certificados para a sua subscrição a partir do portal Azure. Se tiver importado quaisquer subscrições de um ficheiro de subscrição, os certificados associados irão aparecer no separador **certificados** . Quando tiver terminado, selecione o botão **Fechar** .

    ![Gerir as subscrições](./media/vs-azure-tools-publish-azure-application-wizard/IC799160.png)

    >[AZURE.NOTE] Um ficheiro de subscrição pode conter mais do que uma subscrição.

1. Selecione o botão **seguinte** para continuar. 

    Se não existirem quaisquer serviços em nuvem na sua subscrição, terá de criar um serviço na nuvem no Azure para alojar o seu projeto. É apresentada a caixa de diálogo **criar serviço em nuvem e conta de armazenamento** .

    Especificar um novo nome para o serviço de nuvem. O nome deve ser exclusivo no Azure. Em seguida, especifique uma região ou grupo de afinidade para um centro de dados estiver perto de ou a maior parte dos seus clientes. Este nome também é utilizado para uma nova conta de armazenamento Azure cria do seu serviço de nuvem.

1. Modificar quaisquer definições que pretende para esta implementação e, em seguida, publicá-lo ao selecionar o botão **Publicar** (a secção seguinte fornece mais detalhes sobre as várias definições). Para rever as definições antes de publicar, selecione o botão **seguinte** .

    >[AZURE.NOTE] Se optar por publicar neste passo, pode monitorizar o estado deste implementação no Visual Studio.

Pode modificar as definições comuns e avançadas para uma implementação utilizando o Assistente da **Aplicação de Azure publicar** . Por exemplo, pode escolher uma definição de implementar a aplicação de ambiente de teste antes de libertar a mesma. A ilustração seguinte mostra o separador de **Definições comuns** para uma implementação do Azure.

![Definições comuns](./media/vs-azure-tools-publish-azure-application-wizard/IC749013.png)

## <a name="configuring-your-publish-settings"></a>Configurar as definições de publicação

### <a name="to-configure-the-publish-settings"></a>Para configurar as definições de publicar

1. Na lista de **serviço na nuvem** , execute um dos seguintes conjuntos de passos:

   1. Na caixa de lista pendente, selecione um serviço de nuvem existente. É apresentada a localização do Centro de dados para o serviço. Deve Anote esta localização e certifique-se de que a localização de conta de armazenamento é no Centro de dados do mesmo.

    1. Selecione **Criar novo** para criar um serviço na nuvem que aloja Azure. Na caixa de diálogo **Criar serviço na nuvem** , forneça um nome para o serviço e, em seguida, especifique uma região ou grupo de afinidade para especificar a localização do Centro de dados que pretende alojar este serviço na nuvem. O nome deve ser exclusivo no Azure.

1. Na lista de **ambiente de** , selecione **produção** ou **transição**. Selecione o ambiente de teste se pretende implementar a aplicação de ambiente de teste. Pode mover a aplicação de ambiente de produção mais tarde.

1. Na lista **Criar configuração** , selecione **Depurar** ou **lançamento**.

1. Na lista de **configuração do serviço** , selecione **na nuvem** ou **Local**.

    Selecione a caixa de verificação **Ativar o ambiente de trabalho remoto para todas as funções** se pretender que possam remotamente ligar ao serviço. Esta opção é principalmente utilizada para resolução de problemas. Quando selecionar esta caixa de verificação, é apresentada a caixa de diálogo de **Configuração do ambiente de trabalho remoto** . Selecione a ligação de definições para alterar a configuração.

    Selecione a caixa de verificação **Ativar Web implementação para todas as funções web** para ativar a implementação web para o serviço. Tem de ativar o ambiente de trabalho remoto utilizar esta funcionalidade. Para mais informações, consulte [[publicar um serviço na nuvem utilizando as ferramentas do Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx). Para mais informações sobre implementar Web, consulte a [[publicação de um serviço de nuvem utilizando as ferramentas do Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx).

1. Selecione o separador de **Definições avançadas** . No campo de **etiqueta de implementação** , aceitar o nome predefinido ou introduza um nome à sua escolha. Para anexar a data à etiqueta de implementação, deixe a caixa de verificação selecionada.

    ![Terceiro ecrã do Assistente de publicação](./media/vs-azure-tools-publish-azure-application-wizard/IC749014.png)

1. Na lista **conta de armazenamento** , selecione a conta de armazenamento para utilizar para esta implementação. Compare as localizações dos centros de dados para o seu serviço de nuvem e a sua conta de armazenamento. Idealmente, estas localizações devem ser o mesmo.

    >[AZURE.NOTE] A conta de armazenamento Azure armazena o pacote de implementação de aplicações. Após a aplicação é implementada, o pacote é removido da conta de armazenamento.

1. Selecione a caixa de verificação **Atualizar implementação** se pretende implementar apenas componentes actualizados. Este tipo de implementação pode ser mais rápido do que uma implementação completa. Selecione a ligação de **Definições** para abrir a caixa de diálogo **implementação atualizar as definições** , mostrada na seguinte ilustração. 

    ![Definições de implementação](./media/vs-azure-tools-publish-azure-application-wizard/IC617060.png)

    Pode escolher qualquer uma das duas opções para a implementação de atualização, utilizarão ou em simultânea. Uma implementação utilizarão atualiza uma instância implementada cada vez, para que a sua aplicação permanece online e disponível para utilizadores. Uma implementação simultânea atualiza todas as instâncias implementadas ao mesmo tempo. Actualização simultânea é mais rápida que atualização elementar, mas se selecionar esta opção, a aplicação pode não estar disponível durante o processo de atualização.

    Deverá selecione a caixa de verificação se implementação não pode ser atualizada, efetue uma implementação completa, se pretender que a implementação completa para executada automaticamente se falhar de uma implementação de atualização. Uma implementação completa repõe o endereço IP (VIP) virtual para o serviço de nuvem. Para obter mais informações, consulte o artigo [como: manter um endereço de IP Virtual constante para um serviço na nuvem](https://msdn.microsoft.com/library/azure/jj614593.aspx).


1. Para depurar seu serviço, selecione a caixa de verificação **Ativar IntelliTrace** ou, se estiver a implementar uma configuração **Depurar** e pretende depurar o seu serviço de nuvem no Azure, selecione a caixa de verificação **Ativar depurador remoto para todas as funções** para implementar os serviços de depuração remotos.

2. Para criar um perfil da aplicação, selecione a caixa de verificação **Ativar a criação de perfis** e, em seguida, selecione a ligação de **Definições** para apresentar as opções de perfis. 


    >[AZURE.NOTE] Tem de utilizar o Visual Studio Ultimate ativar IntelliTrace ou de criação de perfis de interação camada (Sugestão) e não é possível ativar ambos ao mesmo tempo.

    Para mais informações, consulte o artigo [depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx) e [testar o desempenho de um serviço na nuvem](https://msdn.microsoft.com/library/azure/hh369930.aspx).

1. Selecione **seguinte** para ver a página de resumo para a aplicação.

## <a name="publishing-your-application"></a>A aplicação de publicação

1. Pode optar por criar um perfil de publicação a partir das definições que escolheu. Por exemplo, poderá criar um perfil para um ambiente de teste e outro para produção. Para guardar este perfil, selecione o ícone **Guardar** . O assistente cria o perfil e guarda-lo do projeto do Visual Studio. Para modificar o nome do perfil, abra a lista de **perfil de destino** e, em seguida, selecione **< … gerir >**.

    ![Ecrã de resumo do Assistente de publicação](./media/vs-azure-tools-publish-azure-application-wizard/IC749015.png)

    >[AZURE.NOTE] O perfil de publicação é apresentado no Explorador de solução no Visual Studio e as definições do perfil são escritas para um ficheiro com uma extensão de .azurePubxml. Definições são guardadas como atributos de etiquetas XML.

1. Selecione **Publicar** para publicar a sua aplicação. Pode monitorizar o estado do processo na janela de **saída** no Visual Studio.

## <a name="see-also"></a>Consulte também

[Como: migrar e publicar uma aplicação Web para um serviço em nuvem Azure do Visual Studio](https://msdn.microsoft.com/library/azure/hh420322.aspx)

[Publicação de um serviço de nuvem utilizando as ferramentas do Azure](https://msdn.microsoft.com/library/azure/ff683672.aspx)

[Depuração de um serviço de nuvem publicados com IntelliTrace e o Visual Studio](https://msdn.microsoft.com/library/azure/ff683671.aspx)

[Testar o desempenho de um serviço na nuvem](https://msdn.microsoft.com/library/azure/hh369930.aspx)

