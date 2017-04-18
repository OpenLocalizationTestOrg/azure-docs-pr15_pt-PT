<properties
   pageTitle="Preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio | Microsoft Azure"
   description="Obter informações sobre os procedimentos para configurar serviços de conta na nuvem e o armazenamento e configurar as aplicações do Azure."
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

# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Preparar para publicar ou implementar uma aplicação do Azure do Visual Studio

## <a name="overview"></a>Descrição geral

Antes de poder publicar um projeto de serviço de nuvem, tem de configurar os seguintes serviços:

- Um **serviço na nuvem** para executar as funções no ambiente do Azure

- Uma **conta de armazenamento** que fornece acesso aos serviços Blob, fila de espera e tabela.

Utilize os seguintes procedimentos para configurar estes serviços e configurar a aplicação


## <a name="create-a-cloud-service"></a>Criar um serviço na nuvem

Para publicar um serviço na nuvem no Azure, primeiro tem de criar um serviço na nuvem, que executa as funções no ambiente do Azure. Pode criar um serviço na nuvem no [portal clássico Azure](http://go.microsoft.com/fwlink/?LinkID=213885), conforme descrito na secção **para criar um serviço na nuvem utilizando o portal clássico Azure**, mais adiante. Também pode criar um serviço na nuvem no Visual Studio utilizando o Assistente de publicação.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Para criar um serviço na nuvem utilizando o Visual Studio

1. Abrir o menu de atalho para o projeto Azure e selecione **Publicar**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. Se ainda não iniciou sessão, inicie sessão com o seu nome de utilizador e palavra-passe da conta Microsoft ou institucional que está associada a sua subscrição do Azure.

1. Selecione o botão **seguinte** para avançar para a página de **Definições** .

    ![Publicação definições comuns do Assistente](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. Na lista de **Serviços em nuvem** , selecione **Criar novo**. É apresentada a caixa de diálogo **Criar dos serviços do Azure** .

1. Introduza o nome do seu serviço de nuvem. O nome faz parte do URL do seu serviço e, consequentemente, tem de ser exclusivo global. O nome não está entre maiúsculas e minúsculas.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Para criar um serviço na nuvem utilizando o portal clássico do Azure

1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no Web site da Microsoft.

1. (opcional) Para apresentar uma lista de serviços em nuvem que já criou, selecione a ligação de serviços em nuvem no lado esquerdo da página.

1. Selecione o **+** ícone no canto inferior esquerdo de canto e, em seguida, escolha **Serviço na nuvem** no menu que é apresentada. Noutro ecrã com duas opções, **Crie rápida** e **Criar personalizado**, é apresentada. Se optar por **Criar rápida**, pode criar um serviço na nuvem, especificando o URL e a região onde vai ser física alojado apenas. Se optar por **Criar personalizado**, pode publicar imediatamente um serviço na nuvem ao especificar um pacote (.cspkg ficheiro), um ficheiro de configuração (.cscfg) e um certificado. Criar personalizada não é necessário caso pretenda publicar o seu serviço de nuvem, utilizando o comando **Publicar** num projeto Azure. O comando **Publicar** está disponível no menu de atalho para um projeto Azure.

1. Selecione a **Criação rápida** mais tarde publicar o seu serviço de nuvem utilizando o Visual Studio.

1. Especifique um nome para o seu serviço de nuvem. O URL completo aparece junto ao nome.

1. Na lista, selecione a região onde se encontram a maior parte dos seus utilizadores.

1. Na parte inferior da janela, selecione a ligação **Criar serviço na nuvem** .

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento proporciona acesso aos serviços Blob, fila de espera e tabela. Pode criar uma conta de armazenamento utilizando o Visual Studio ou o [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>Para criar uma conta de armazenamento utilizando o Visual Studio

1. No **Explorador de soluções**, abra o menu de atalho para o nó de **armazenamento** e, em seguida, selecione **Criar a conta de armazenamento**.

    ![Criar uma nova conta de armazenamento Azure](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. Selecione ou introduza as seguintes informações para a nova conta de armazenamento na caixa de diálogo **Criar conta de armazenamento** .
    - A subscrição Azure ao qual pretende adicionar a conta de armazenamento.
    - O nome que pretende utilizar para a nova conta de armazenamento.
    - A região ou grupo de afinidade (como EUA ocidental ou Ásia).
    - O tipo de replicação que pretende utilizar para a conta de armazenamento, tal como Geo redundantes.

1. Quando tiver terminado, selecione **Criar**. A nova conta de armazenamento é apresentada na lista de **armazenamento** no **Explorador do servidor**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>Para criar uma conta de armazenamento utilizando o portal clássico Azure

1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkId=253103) no Web site da Microsoft.

1. (Opcional) Para ver as suas contas de armazenamento, selecione a ligação de **armazenamento** no painel de no lado esquerdo da página.

1. No canto inferior esquerdo da página, selecione o **+** ícone.

1. No menu que aparece, selecione o **armazenamento**e, em seguida, selecione **Criar rápida**.

1. Dê um nome que irá resultar num url exclusivo à conta de armazenamento.

1. Dê um nome ao seu serviço de nuvem. O URL completo aparece junto ao nome.

1. Na lista de regiões, selecione uma região onde se encontram a maior parte dos seus utilizadores.

1. Especifique se pretende ativar geo replicação. Se ativar geo replicação, os seus dados serão guardados em várias localizações físicas para reduzir as hipóteses de perda. Esta funcionalidade faz com que armazenamento dispendioso mais, mas pode reduzir os custos ao ativar geo localização quando criar a conta de armazenamento em vez de adicionar a funcionalidade mais tarde. Para mais informações, consulte o artigo [Geo replicação](http://go.microsoft.com/fwlink/?LinkId=253108).

1. Na parte inferior da janela, selecione a ligação de **Conta de armazenamento de criar** .

Depois de criar a sua conta de armazenamento, verá os URLs que pode utilizar para aceder a recursos em cada uma dos serviços de armazenamento Azure e as teclas de acesso principais e secundários para a sua conta. Utilize estas teclas para autenticar pedidos de acordo com os serviços de armazenamento.

>[AZURE.NOTE] A tecla de acesso secundário fornece o mesmo acesso à sua conta de armazenamento como chave primária de acesso e é gerada como uma cópia de segurança deve ser comprometida a chave primária de acesso. Para além disso, recomenda-se que gerar as teclas de acesso regularmente. Pode modificar uma definição de cadeia de ligação para utilizar a chave secundária enquanto voltar a gerar a chave primária, em seguida, que pode modificá-lo para utilizar a chave primária recuperada enquanto voltar a gerar a chave secundária.

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Configurar a sua aplicação para utilizar serviços fornecidos pela conta de armazenamento

Tem de configurar qualquer função que acede aos serviços de armazenamento para utilizar os serviços de armazenamento Azure criados por si. Para executar esta tarefa, pode utilizar vários configurações do serviço para o seu projeto Azure. Por predefinição, dois são criados no seu projeto Azure. Ao utilizar vários configurações do serviço, pode utilizar a mesma cadeia de ligação no seu código, mas tem um valor diferente para uma cadeia de ligação em cada configuração de serviço. Por exemplo, pode utilizar uma configuração do serviço para executar e depurar a sua aplicação localmente utilizando o emulador armazenamento Azure e uma configuração de serviços diferente para publicar a sua aplicação ao Azure. Para mais informações sobre as configurações de serviço, consulte o artigo [Configurar o Azure projeto utilizando vários configurações do serviço](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Para configurar a aplicação para utilizar os serviços que fornece a conta de armazenamento

1. No Visual Studio abra sua solução Azure. No Explorador de solução, abra o menu de atalho para cada função no projeto Azure que acede os serviços de armazenamento e selecione **Propriedades**. No editor do Visual Studio, é apresentada uma página com o nome da função. A página apresenta os campos para o separador **configuração** .

1. Nas páginas de propriedades para a função, selecione **Definições**.

1. Na lista de **Configuração do serviço** , selecione o nome da configuração do serviço que pretende editar. Se pretender efetuar alterações a todas as configurações de serviço para esta função do, pode escolher **Todas as configurações**.  Para mais informações sobre como atualizar configurações do serviço, consulte a secção **Gerir as cadeias de ligação para contas de armazenamento** no tópico de [configurar as funções para um serviço em nuvem Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Para modificar as definições de cadeia de ligação, escolha **…** botão junto a definição. É apresentada a caixa de diálogo **Criar cadeia de ligação de armazenamento** .

1. Em **Ligar utilizando**, selecione a opção da **sua subscrição** .

1. Na lista de **subscrição** , selecione a sua subscrição. Se a lista de subscrições não inclui o aquele que pretende, selecione a ligação de **Definições de publicar transferência** .

1. Na lista **nome da conta** , selecione o seu nome de conta de armazenamento. Ferramentas Azure obtém as credenciais de conta de armazenamento automaticamente utilizando o ficheiro .publishsettings. Para especificar manualmente as suas credenciais de conta de armazenamento, escolha a opção **introduzidos manualmente as credenciais** e, em seguida, continue com este procedimento. Pode obter o seu nome de conta de armazenamento e a chave primária a partir do [Azure portal clássico](http://go.microsoft.com/fwlink/p/?LinkID=213885). Se não quiser especificar o seu armazenamento definições da conta manualmente, selecione o botão **OK** para fechar a caixa de diálogo.

1. Selecione a ligação de credenciais de **conta de armazenamento Enter** .

1. Na caixa **nome da conta** , introduza o nome da sua conta de armazenamento.

    >[AZURE.NOTE] Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e, em seguida, selecione o botão de **armazenamento** . O portal mostra uma lista de contas de armazenamento. Se escolher uma conta, é aberta uma página para a mesma. Pode copiar o nome da conta de armazenamento a partir desta página. Se estiver a utilizar uma versão anterior do portal do clássico, o nome da sua conta de armazenamento é apresentado na vista de **Contas de armazenamento** . Para copiar este nome, realce-la na janela de **Propriedades** desta vista e, em seguida, selecione as teclas Ctrl-C. Para colar o nome do Visual Studio, selecione a caixa de texto **nome da conta** e, em seguida, selecione as teclas Ctrl + V.

1. Na caixa **chave da conta** , introduza a sua chave primária, ou copie e cole-a partir do [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885).
    Para copiar esta chave:

    1. Na parte inferior da página para a conta de armazenamento adequado, selecione o botão **Gerir chaves** .

    1. Na página **Gerir o acesso de teclas** , selecione o texto da chave de acesso principal e, em seguida, selecione as teclas Ctrl + C.

    1. Em ferramentas do Azure, cole a chave na caixa **chave da conta** .

    1. Tem de selecionar uma das opções seguintes para determinar como o serviço irá aceder a conta de armazenamento:
        - **Utilizar HTTP**. Esta é a opção padrão. Por exemplo, `http://<account name>.blob.core.windows.net`.
        - **Utilizar HTTPS** para uma ligação segura. Por exemplo, `https://<accountname>.blob.core.windows.net`.
        - **Especificar os pontos finais personalizados** para cada um dos três serviços. Em seguida, pode escrever estes pontos finais para o campo para o serviço específico.

        >[AZURE.NOTE] Se criar os pontos finais personalizados, pode criar uma cadeia de ligação mais complexa. Quando utilizar este formato de cadeia, pode especificar pontos finais de serviço de armazenamento incluam um nome de domínio personalizado que tenham registados para a sua conta de armazenamento com o serviço de Blobs. Também pode conceder acesso apenas aos recursos blob num único contentor através de uma assinatura de acesso partilhado. Para mais informações sobre como criar os pontos finais personalizados, consulte o artigo [Configurar cadeias de ligação do Azure armazenamento](storage-configure-connection-string.md).

1. Para guardar estas alterações de cadeia de ligação, selecione o botão **OK** e, em seguida, selecione o botão **Guardar** na barra de ferramentas. Depois de guardar estas alterações, pode obter o valor de nesta cadeia de ligação no seu código utilizando [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Quando publicar a sua aplicação ao Azure, selecione a configuração do serviço que contém a conta de armazenamento Azure para a cadeia de ligação. Depois da aplicação é publicada, certifique-se de que a aplicação funciona conforme esperado contra os serviços de armazenamento Azure

## <a name="next-steps"></a>Próximos passos

Para saber mais acerca da publicação de aplicações para o Azure a partir do Visual Studio, consulte [publicar um serviço na nuvem utilizando as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).
