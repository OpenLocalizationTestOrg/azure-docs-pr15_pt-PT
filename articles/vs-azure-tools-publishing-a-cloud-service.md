<properties
   pageTitle="Publicação de um serviço de nuvem utilizando as ferramentas do Azure | Microsoft Azure"
   description="Saiba mais sobre como publicar projetos de serviço de nuvem Azure utilizando o Visual Studio."
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

# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publicação de um serviço de nuvem utilizando as ferramentas do Azure

Ao utilizar as ferramentas do Azure para o Microsoft Visual Studio, pode publicar a sua aplicação Azure diretamente a partir do Visual Studio. Visual Studio suporta integrada publicação para a transição ou o ambiente de produção de um serviço na nuvem.

Antes de poder publicar uma aplicação do Azure, tem de ter uma subscrição do Azure. Também deve configurar uma conta de serviço e de armazenamento na nuvem para ser utilizado por sua aplicação. Pode configurar estas no [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.IMPORTANT] Quando publicar, pode selecionar o ambiente de implementação do seu serviço de nuvem. Também tem de selecionar uma conta de armazenamento é utilizada para armazenar o pacote de aplicação para implementação. Após a implementação, o pacote de aplicação é removido da conta de armazenamento.

Quando e testa uma aplicação do Azure, pode utilizar a Web implementar para publicar as alterações incrementada para as funções web. Depois de publicar a sua aplicação para um ambiente de implementação, implementar Web permite-lhe implementar alterações diretamente para a máquina virtual que está a ser executado o papel de web. Não possui o pacote e publicar a sua aplicação Azure completa sempre que pretende atualizar a sua função web para testar às alterações. Com esta abordagem pode ter as alterações de funções web disponíveis na nuvem para testar a ligação sem a aguardar que a aplicação para um ambiente de implementação publicação.

Utilize os seguintes procedimentos para publicar a sua aplicação Azure e para atualizar uma função de web utilizando Web implementar:

- Publicar ou compactar uma aplicação a partir do Visual Studio Azure

- Atualizar uma função de web como parte do desenvolvimento e teste de ciclo

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicar ou compactar uma aplicação do Visual Studio Azure

Quando publica as aplicações do Azure, pode efetuar uma das seguintes tarefas:

- Criar um pacote de serviço: pode utilizar este pacote e o ficheiro de configuração do serviço para publicar a sua aplicação para um ambiente de implementação a partir do [Azure clássica Portal](http://go.microsoft.com/fwlink/?LinkID=213885).

- Publicar o projeto do Visual Studio Azure: para publicar a sua aplicação diretamente para o Azure, utilize o Assistente de publicação. Para obter informações, consulte o artigo [Aplicação Assistente de publicação do Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Para criar um pacote de serviço do Visual Studio

1. Quando estiver pronto para publicar a sua aplicação, abra o Explorador de solução, abra o menu de atalho para o projeto Azure que contém as funções e escolha publicar.

1. Para criar apenas um pacote de serviço, siga estes passos:  

  1. No menu de atalho para o projeto Azure, selecione o **pacote**.

  1. Na caixa de diálogo **Pacote Azure aplicação** , selecione a configuração do serviço para o qual pretende criar um pacote e, em seguida, selecione a configuração da compilação.

  1. (opcional) Para ativar no ambiente de trabalho remoto para o serviço de nuvem, depois de a publicar, selecione a caixa de verificação **Ativar o ambiente de trabalho remoto para todas as funções** e, em seguida, selecione **Definições** para configurar o ambiente de trabalho remoto. Se pretender depurar depois de a publicar o seu serviço de nuvem, ative a depuração remota selecionando **Ativar depurador remoto para todas as funções**.

      Para mais informações, consulte o artigo [Utilizar o ambiente de trabalho remoto com o Azure funções](vs-azure-tools-remote-desktop-roles.md).

  1. Para criar o pacote de, selecione a ligação do **pacote** .

      Explorador de ficheiros mostra a localização do ficheiro do pacote recentemente criado. Pode copiar esta localização para que pode utilizá-lo a partir do [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885).

  1. Para publicar este pacote de um ambiente de implementação, tem de utilizar esta localização como a localização do pacote quando cria um serviço na nuvem e implementar este pacote para um ambiente com o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Para cancelar o processo de implementação, no menu de atalho para o item de linha no registo de atividade, selecione **Cancelar e remover**. Isto interrompe o processo de implementação e elimina o ambiente de implementação do Azure.

    >[AZURE.NOTE] Para remover este ambiente implementação depois de ter sido implementado, tem de utilizar o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Depois de tem iniciado seu instâncias de função, o Visual Studio automaticamente mostra o ambiente de implementação no nó **Serviços em nuvem** no Explorador do servidor. A partir daqui pode ver o estado das instâncias função individuais. Consulte o artigo [Gerir Azure recursos com o Explorador de nuvem](vs-azure-tools-resources-managing-with-cloud-explorer.md). A ilustração seguinte mostra as instâncias de função enquanto ainda se encontram em estado da inicializar:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Atualizar uma função de Web como parte do desenvolvimento e teste de ciclo

Se é estável infraestrutura de back-end da sua aplicação, mas as funções web precisam de mais frequentes atualizar, pode utilizar a Web implementar para atualizar apenas uma função da web no seu projeto. Este é útil quando não pretende reconstruir e implementar novamente as funções de trabalho de back-end ou se tiver várias funções da web e que quer atualizar apenas uma das funções web.

### <a name="requirements"></a>Requisitos de

Eis os requisitos para utilizar o Web implementar para atualizar a sua função web:

- **Para desenvolvimento e teste apenas para efeitos:** As alterações são feitas diretamente para a máquina virtual onde o papel de web está em execução. Se esta máquina virtual tem de estar reciclados, as alterações são perdidas porque o pacote original que publicou é utilizado para recriar a máquina virtual para a função. Tem de voltar a publicar a aplicação para obter as alterações mais recentes para a função da web.

- **Apenas as funções de web podem ser atualizadas:** Funções de trabalho não podem ser atualizadas. Além disso, não é possível atualizar RoleEntryPoint na web role.cs.

- **Só pode suportar uma única ocorrência de uma função da web:** Não pode ter várias instâncias de qualquer função web no seu ambiente de implementação. No entanto, vários web funções cada com apenas uma instância são suportadas.

- **Tem de ativar ligações de ambiente de trabalho remoto:** O que é necessário para que Web implementar pode utilizar o utilizador e palavra-passe para ligar à máquina virtual para implementar as alterações no servidor que execute serviços de informação Internet (IIS). Além disso, poderá ter de se ligar à máquina virtual para adicionar um certificado fidedigno para o IIS nesta máquina virtual. (Isto garante que a ligação para o IIS que é utilizado pelo Web implementar remota é segura.)

O seguinte procedimento assume que está a utilizar o Assistente de **Aplicação de Azure publicar** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Para ativar a Web implementar durante a publicação de aplicação

1. Para ativar a **Ativar Web implementar** para todos os web funções, primeiro tem de configurar ligações de ambiente de trabalho remoto. Selecione **Ativar o ambiente de trabalho remoto** para todas as funções e, em seguida, forneça as credenciais que serão utilizadas para ligar remotamente na caixa de **Configuração do ambiente de trabalho remoto** que é apresentada. Para mais informações, consulte [Utilizar o ambiente de trabalho remoto com o Azure funções](vs-azure-tools-remote-desktop-roles.md) .

1. Para ativar a Web implementar para todas as funções web na sua aplicação, selecione **Ativar Web implementação para todas as funções web**.

    Aparece um triângulo amarelo de aviso. Implementar Web utiliza um certificado autoassinado, ou não fidedigno por predefinição, o que não é recomendada para carregar os dados confidenciais. Se precisar de seguro este processo para dados confidenciais, pode adicionar um certificado SSL para ser utilizado para implementar Web ligações. Este certificado tem de estar um certificado fidedigno. Para obter informações sobre como fazer isto, consulte a secção **Para tornar Web implementar seguro** , mais adiante.

1. Selecione **seguinte** para mostrar o ecrã de **Resumo** e, em seguida, selecione **Publicar** para implementar o serviço de nuvem.

    O serviço de nuvem é publicado. A máquina virtual que é criada tem ligações remotas ativadas para IIS para que implementar Web pode ser utilizada para atualizar o seu funções da web sem a voltar a publicá-los.

    >[AZURE.NOTE] Se tiver mais do que uma instância configurada para uma função de web, é apresentada uma mensagem de aviso a indicar que cada função web serão limitada a uma instância apenas no pacote que é criado para publicar a sua aplicação. Selecione **OK** para continuar. Tal como indicado na secção requisitos, pode ter mais do que uma função de web mas apenas uma instância de cada função.

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Para atualizar a sua função Web utilizando Web implementar

1. Para utilizar Web implementar, altere código o projeto para qualquer um dos seus funções da web no Visual Studio que pretende publicar e, em seguida, botão direito do rato neste nó de projeto na sua solução e aponte para **Publicar**. É apresentada a caixa de diálogo **Publicar Web** .

1. (Opcional) Se tiver adicionado um certificado SSL fidedigno para utilizar para remotas ligações para o IIS, pode limpar a caixa de verificação **Permitir ou não fidedigno certificado** . Para obter informações sobre como adicionar um certificado para tornar o Web implementar seguro, consulte a secção **Para tornar Web implementar seguro** mais adiante neste tópico.

1. Para utilizar implementar Web, o mecanismo publicar necessita de nome de utilizador e palavra-passe que definiu para a sua ligação de ambiente de trabalho remoto quando o pacote publicado pela primeira vez.

  1. Em **nome de utilizador**, introduza o nome de utilizador.

  1. Em **palavra-passe**, introduza a palavra-passe.

  1. (Opcional) Se pretender guardar esta palavra-passe neste perfil, selecione **Guardar palavra-passe**.

1. Para publicar as alterações ao seu cargo web, selecione **Publicar**.

    A linha de estado apresenta **Publicar iniciado**. Quando tiver concluído a publicação, **Publicar com êxito** é apresentado. As alterações agora foram implementadas à função web no seu computador virtual. Agora pode começar a sua aplicação Azure no ambiente do Azure para experimentar as alterações.

### <a name="to-make-web-deploy-secure"></a>Para tornar o Web implementar seguro

1. Implementar Web utiliza um certificado autoassinado, ou não fidedigno por predefinição, o que não é recomendada para carregar os dados confidenciais. Se precisar de seguro este processo para dados confidenciais, pode adicionar um certificado SSL para ser utilizado para implementar Web ligações. Este certificado tem de estar um certificado fidedigno, obtidos a partir de uma autoridade de certificação (AC).

    Para tornar Web implementar segura para cada máquina virtual para cada um dos seus funções da web, terá de carregar o certificado fidedigno que pretende utilizar para a web implementar do [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885). Isto torna-se de que o certificado é adicionado à máquina virtual que é criada para a função web quando publicar a sua aplicação.

1. Para adicionar um certificado SSL fidedigno para o IIS para utilizar para ligações remotas, siga estes passos:

  1. Para se ligar à máquina virtual que esteja a executar o papel de web, selecione a instância da função web em **Nuvem Explorer** ou no **Explorador de servidor**e, em seguida, escolha o comando **Ligar utilizando o ambiente de trabalho remoto** . Para obter passos detalhados sobre como ligar à máquina virtual, consulte o artigo [Utilizar o ambiente de trabalho remoto com o Azure funções](vs-azure-tools-remote-desktop-roles.md).

      O browser irá pedir-lhe para transferir um. Ficheiro RDP.

  1. Para adicionar um certificado SSL, abra o serviço de gestão no Gestor de IIS. No Gestor de IIS, ative SSL ao abrir a ligação de **enlaces** no painel de **ação** . É apresentada a caixa de diálogo **Adicionar encadernação de Site** . Selecione **Adicionar**e, em seguida, selecione HTTPS na lista **tipo** de lista pendente. Na lista de **certificado SSL** , selecione o certificado SSL tinha sessão iniciada por uma AC e que carregou para o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Para mais informações, consulte o artigo [Configurar definições de ligação para o serviço de gestão](http://go.microsoft.com/fwlink/?LinkId=215824).

      >[AZURE.NOTE] Se adicionar um certificado SSL fidedigno, o triângulo amarelo de aviso deixa de aparecer no **Assistente de publicação**.

## <a name="include-files-in-the-service-package"></a>Incluir ficheiros no pacote do serviço

Poderá ter de incluir ficheiros específicos no seu pacote de serviço para que fiquem disponíveis na máquina virtual que é criada para uma função. Por exemplo, poderá pretender adicionar uma .exe ou um ficheiro. msi que é utilizado por um script de arranque para o seu pacote de serviço. Ou poderá ter de adicionar uma assemblagem que necessita de um projeto de função de função ou trabalhador da web. Para incluir ficheiros deve ser adicionados à solução para a sua aplicação Azure.

### <a name="to-include-files-in-the-service-package"></a>Para incluir ficheiros no pacote do serviço

1. Para adicionar uma assemblagem a um pacote de serviço, utilize os seguintes passos:

  1. No **Explorador de solução** abra o nó do project para o projeto que não é apresentado a assemblagem referenciada.

  1. Para adicionar a assemblagem ao projeto, abrir o menu de atalho para a pasta de **referências** e, em seguida, selecione **Adicionar referência**. É apresentada a caixa de diálogo Adicionar referência.

  1. Selecione a referência que pretende adicionar e, em seguida, selecione o botão **OK** .

      A referência é adicionada à lista na pasta de **referências** .

  1. Abrir o menu de atalho para a assemblagem que tenha adicionado e selecione **Propriedades**. É apresentada a janela de **Propriedades** .

      Para incluir esta assemblagem no pacote do serviço, na **lista cópia Local** selecione **Verdadeiro**.

1. No **Explorador de solução** abra o nó do project para o projeto que não é apresentado a assemblagem referenciada.

1. Para adicionar a assemblagem ao projeto, abrir o menu de atalho para a pasta de **referências** e, em seguida, selecione **Adicionar referência**. É apresentada a caixa de diálogo **Adicionar referência** .

1. Selecione a referência que pretende adicionar e, em seguida, selecione o botão **OK** .

    A referência é adicionada à lista na pasta de **referências** .

1. Abrir o menu de atalho para a assemblagem que tenha adicionado e selecione **Propriedades**. É apresentada a janela de propriedades.

1. Para incluir esta assemblagem no pacote do serviço, na lista de **Cópia Local** , selecione **Verdadeiro**.

1. Para incluir ficheiros no pacote do serviço que foram adicionados ao seu projeto de funções da web, abra o menu de atalho para o ficheiro e, em seguida, selecione **Propriedades**. A partir da janela de **Propriedades** , selecione **conteúdo** **Ação Criar** na caixa de listagem.

1. Para incluir ficheiros no pacote do serviço que foram adicionados ao seu projeto de função de trabalho, abrir o menu de atalho para o ficheiro e, em seguida, selecione **Propriedades**. A partir da janela de **Propriedades** , selecione **copiar se mais recente** da caixa de listagem **Copiar para directório de saída** .

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a publicação para Azure a partir do Visual Studio, consulte o artigo [Aplicação Assistente de publicação do Azure](vs-azure-tools-publish-azure-application-wizard.md).
