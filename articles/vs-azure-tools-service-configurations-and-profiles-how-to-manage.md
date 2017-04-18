<properties
   pageTitle="Como gerir as configurações de serviço e perfis | Microsoft Azure"
   description="Saiba como trabalhar com ficheiros de configuração de configurações e perfis de serviço | qual armazenar definições para os ambientes de implementação e publicar as definições para serviços em nuvem."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-manage-service-configurations-and-profiles"></a>Como gerir as configurações de serviço e de perfis

## <a name="overview"></a>Descrição geral

Durante a publicação de um serviço na nuvem, Visual Studio armazena informações de configuração no dois tipos de ficheiros de configuração: configurações e perfis de serviço. Configurações do serviço (.cscfg ficheiros) armazenam definições para os ambientes de implementação para um serviço em nuvem Azure. Azure utiliza estes ficheiros de configuração, quando gere os seus serviços na nuvem. Por outro lado, perfis (.azurePubxml ficheiros) loja definições de publicação para serviços em nuvem. Estas definições são um registo das escolher quando utilizar o Assistente de publicação e são utilizadas localmente pelo Visual Studio. Este tópico explica como trabalhar com os dois tipos de ficheiros de configuração.

## <a name="service-configurations"></a>Configurações do serviço

Pode criar vários configurações de serviço para utilizar para cada um dos seus ambientes de implementação. Por exemplo, poderá criar uma configuração de serviço para o ambiente do local que utilizar para executar e testar uma aplicação do Azure e outra configuração do serviço para o seu ambiente de produção.

Pode adicionar, eliminar, mudar o nome e modificar estas configurações de serviço com base nos seus requisitos. Pode gerir estas configurações de serviço a partir do Visual Studio, conforme apresentado na seguinte ilustração.

![Gerir as configurações de serviço](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

Também pode abrir a caixa de diálogo **Gerir configurações** de páginas de propriedade a função. Para abrir as propriedades de uma função no seu projeto Azure, abrir o menu de atalho para essa função e, em seguida, selecione **Propriedades**. No separador **Definições** , expanda a lista de **Configuração do serviço** e, em seguida, selecione **Gerir** para abrir a caixa de diálogo **Gerir configurações** .

### <a name="to-add-a-service-configuration"></a>Para adicionar uma configuração de serviço

1. No Explorador de solução abrir o menu de atalho para o projeto Azure e, em seguida, selecione **Gerir configurações**.

    É apresentada a caixa de diálogo **Gerir configurações do serviço** .

1. Para adicionar uma configuração do serviço, terá de criar uma cópia de uma configuração existente. Para fazer isto, selecione a configuração que pretende copiar a partir da lista de nome e, em seguida, selecione **criar uma cópia**.

1. (Opcional) Dê um nome diferente à configuração do serviço, selecione a configuração do serviço novo a partir da lista de nome e, em seguida, selecione **Mudar o nome**. Na caixa de texto **nome** , escreva o nome que pretende utilizar para esta configuração de serviço e, em seguida, clique **em OK**.

    Um novo ficheiro de configuração do serviço, que se chamar ServiceConfiguration. [Nome do novo] .cscfg é adicionada ao projeto Azure no Explorador de soluções.


### <a name="to-delete-a-service-configuration"></a>Para eliminar uma configuração de serviço

1. No Explorador de solução, abrir o menu de atalho para o projeto Azure e, em seguida, selecione **Gerir configurações**.

    É apresentada a caixa de diálogo **Gerir configurações do serviço** .

1. Para eliminar uma configuração do serviço, selecione a configuração que pretende eliminar a partir da lista de **nome** e, em seguida, clique em **Remover**. É apresentada uma caixa de diálogo para confirmar que pretende eliminar esta configuração.

1. Selecione **Eliminar**.

     O ficheiro de configuração do serviço é removido do projeto Azure no Explorador de soluções.


### <a name="to-rename-a-service-configuration"></a>Para mudar o nome de uma configuração do serviço

1. No Explorador de solução, abrir o menu de atalho para o projeto Azure e, em seguida, selecione **Gerir configurações**.

    É apresentada a caixa de diálogo **Gerir configurações do serviço** .

1. Para mudar o nome de uma configuração do serviço, selecione a configuração do serviço novo a partir da lista de **nome** e, em seguida, selecione **Mudar o nome**. Na caixa de texto **nome** , escreva o nome que pretende utilizar para esta configuração de serviço e, em seguida, clique **em OK**.

    O nome do ficheiro de configuração do serviço é alterado no projeto Azure no Explorador de soluções.

### <a name="to-change-a-service-configuration"></a>Para alterar uma configuração do serviço

- Se quiser alterar uma configuração do serviço, abra o menu de atalho para a função específica que pretende alterar no projeto Azure e, em seguida, clique em **Propriedades**. Consulte o artigo [como: configurar as funções para um serviço em nuvem Azure com o Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx) para obter mais informações.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Torne as combinações de definição diferente ao utilizar perfis

Ao utilizar um perfil, pode automaticamente preencher no **Assistente de publicação** com diferentes combinações das definições para fins diferentes. Por exemplo, pode ter um perfil para depuração e outra para o lançamento constrói. Nesse caso, o perfil **Depurar** teria **IntelliTrace** ativado e a configuração **de depuração** selecionado e seu perfil de **lançamento** teria **IntelliTrace** desativado e a configuração de **lançamento** selecionada. Pode também utilizar perfis diferentes para implementar um serviço utilizando uma conta de armazenamento diferente.

Ao executar o assistente pela primeira vez, é criado um perfil predefinido. Visual Studio armazena o perfil num ficheiro que tenha uma extensão de .azurePubXml, é adicionada ao projeto Azure na pasta de **perfis** . Se especificar manualmente opções diferentes ao executar o assistente mais tarde, o ficheiro é automaticamente actualizada. Antes de executar o procedimento seguinte, que deve ter já publicado seu serviço de nuvem pelo menos uma vez.

### <a name="to-add-a-profile"></a>Para adicionar um perfil

1. Abrir o menu de atalho para o seu projeto Azure e, em seguida, selecione **Publicar**.

1. Junto à lista de **perfil de destino** , selecione o botão **Guardar perfil** , como a seguinte ilustração mostra. Esta ação cria um perfil por si.

    ![Criar um novo perfil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. Depois do perfil é criado, selecione **< … gerir >** na lista de **perfis de destino** .

    É apresentada a caixa de diálogo **Gerir perfis** , como a seguinte ilustração mostra.

    ![Gerir perfis de diálogo](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. Na lista **nome** , escolha um perfil e, em seguida, selecione **Criar uma cópia**.

1. Selecione o botão **Fechar** .

    O novo perfil é apresentada na lista de perfil de alvos.

1. Na lista de **perfil de destino** , selecione o perfil que acabou de criar. As definições do Assistente de publicação são preenchidas com as opções a partir do perfil que selecionou.

1. Selecione os botões **anterior** e **seguinte** para apresentar cada página do Assistente de publicação e, em seguida, personalize as definições deste perfil. Consulte o artigo [Aplicação Assistente de publicação do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) para obter informações.

1. Quando terminar de personalizar as definições, selecione **seguinte** para voltar à página de definições. O perfil é guardado quando publicar o serviço utilizando estas definições ou se selecionar **Guardar** ao lado da lista de perfis de.

### <a name="to-rename-or-delete-a-profile"></a>Mudar o nome ou eliminar um perfil

1. Abrir o menu de atalho para o seu projeto Azure e, em seguida, selecione **Publicar**.

1. Na lista de **perfil de destino** , selecione **Gerir**.

1. Na caixa de diálogo **Gerir perfis de** , selecione o perfil que pretende eliminar e, em seguida, selecione **Remover**.

1. Na caixa de diálogo de confirmação que é apresentada, selecione **OK**.

1. Selecione **Fechar**.

### <a name="to-change-a-profile"></a>Para alterar um perfil

1. Abrir o menu de atalho para o seu projeto Azure e, em seguida, selecione **Publicar**.

1. Na lista de **perfil de destino** , selecione o perfil que pretende alterar.

1. Selecione os botões **anterior** e **seguinte** para apresentar cada página do Assistente de publicação e, em seguida, altere as definições que pretende. Consulte o artigo [Aplicação Assistente de publicação do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) para obter informações.

1. Depois de terminar de alterar as definições, selecione **seguinte** para voltar à página de **Definições** .

1. (Opcional) selecione **Publicar** para publicar o serviço de nuvem utilizando as novas definições. Se não pretende publicar o seu serviço de nuvem neste momento e fechar o Assistente de publicação, o Visual Studio pergunta se pretende guardar as alterações ao perfil.

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre como configurar outras partes do seu projeto do Visual Studio Azure, consulte [configurar um projecto do Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)
