<properties
   pageTitle="Gerir as aplicações no Visual Studio | Microsoft Azure"
   description="Utilize o Visual Studio para criar, desenvolver, compactar, implementar e depurar as suas aplicações de serviço ferro e serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Utilizar o Visual Studio para simplificar a escrever e gerir as suas aplicações de serviço ferro

Pode gerir as aplicações de ferro de serviço do Azure e serviços através do Visual Studio. Assim que tiver [configurar o seu ambiente de desenvolvimento](service-fabric-get-started.md), pode utilizar o Visual Studio para criar aplicações de serviço ferro, adicionar serviços ou compactar, registo e implementar aplicações no seu cluster de desenvolvimento local.

## <a name="deploy-your-service-fabric-application"></a>Implementar a aplicação de serviço ferro

Por predefinição, a implementar uma aplicação combina os passos seguintes para uma operação simple:

1. Criar o pacote de aplicação
2. Carregar o pacote de aplicação para o arquivo de imagem
3. Registar o tipo de aplicação
4. A remoção de quaisquer instâncias da aplicação em execução
5. Criar uma nova instância da aplicação

No Visual Studio, premindo a tecla **F5** vai também implementar a aplicação e anexar o depurador a todas as instâncias da aplicação. Pode utilizar **Ctrl + F5** para implementar uma aplicação sem depuração ou pode publicar a um cluster local ou remoto utilizando o perfil de publicar. Para obter mais informações, consulte [publicar uma aplicação a um cluster remota utilizando o Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Modo de depuração de aplicação

Por predefinição, o Visual Studio remove instâncias existentes do tipo de aplicação quando para de depuração ou (se tiver implementado a aplicação sem anexar o depurador), quando implementar novamente a aplicação. Nesse caso, são removidos todos os dados da aplicação. Durante a depuração localmente, poderá pretender manter os dados que já criou quando testar uma nova versão da aplicação, que pretende manter a execução de aplicação ou pretende sessões de depuração subsequentes para atualizar a aplicação. Serviço do Visual Studio ferro Tools fornecem uma propriedade denominada **Modo de depuração de aplicação**, quais os controlos se o **F5** deve desinstalar a aplicação, manter a aplicação em execução depois de uma sessão de depuração termina ou ativar a aplicação de ser atualizado no sessões depuração subsequentes, em vez de removidos e novamente implementada.

#### <a name="to-set-the-application-debug-mode-property"></a>Para definir a propriedade do modo de depuração de aplicação

1. No menu de atalho do projeto de aplicação, selecione **Propriedades** (ou prima a tecla **F4** ).
2. Na janela de **Propriedades** , defina a propriedade do **Modo de depuração de aplicação** .

    ![Definir a propriedade de modo de depuração de aplicação][debugmodeproperty]

Estas são as opções de **Modo de depuração de aplicações** disponíveis.

1. **Atualização automática**: A aplicação continua a ser executada quando termina a sessão de depuração. A próxima **F5** tratará a implementação como uma atualização ao utilizar o modo não monitorizada automático para actualizar rapidamente a aplicação para uma versão mais recente por uma cadeia de data acrescentada. O processo de atualização preserva quaisquer dados introduzidos numa sessão de depuração anterior.

2. **Manter a aplicação**: A aplicação mantém em execução no cluster quando termina a sessão de depuração. Na seguinte **F5** a aplicação será removida e será implementada a aplicação recentemente concebida para o cluster.

3. **Remover a aplicação** faz com que a aplicação ser removido quando termina a sessão de depuração.

Para **Atualizar automaticamente** os dados são preservados aplicando as capacidades de atualização da aplicação do serviço ferro, mas está optimizada para otimizar de desempenho em vez de segurança. Para mais informações sobre a atualização de aplicações e como pode efetuar uma atualização num ambiente real, consulte o artigo [atualizar a aplicação de serviço ferro](service-fabric-application-upgrade.md).

![Exemplo de nova versão da aplicação com data acrescentada][preservedata]

>[AZURE.NOTE] Esta propriedade não existe antes de versão 1.1 das ferramentas de ferro do serviço para o Visual Studio. Antes de 1.1, utilize a propriedade **Preservar dados iniciar** para alcançar o mesmo comportamento. A opção "Manter aplicação" foi introduzida numa versão 1.2 das ferramentas de ferro do serviço para o Visual Studio.

## <a name="add-a-service-to-your-service-fabric-application"></a>Adicionar um serviço para a sua aplicação de serviço ferro

Pode adicionar novos serviços à aplicação para expandir a sua funcionalidade.  Para certificar-se de que o serviço está incluído no seu pacote de aplicação, adicione o serviço através do item de menu **Novo serviço de ferro...** .

![Adicionar um novo serviço de ferro à aplicação][newservice]

Selecione um tipo de projecto ferro de serviço para adicionar a sua aplicação e, especifique um nome para o serviço.  Consulte o artigo [Escolher um quadro para o seu serviço](service-fabric-choose-framework.md) para o ajudar a decidir o tipo de serviço para utilizar.

![Selecione um tipo de projecto de serviço de ferro para adicionar a sua aplicação][addserviceproject]

O novo serviço será adicionado à sua solução e o pacote de aplicação existente. As referências de serviço e uma instância do serviço predefinido são adicionadas à manifesto da aplicação. O serviço será criado e iniciado da próxima vez que implementar a aplicação.

![O novo serviço será adicionado à sua manifesto de aplicação][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Compactar a aplicação de serviço ferro

Para implementar a aplicação e dos seus serviços a um cluster, necessita de criar um pacote de aplicação.  O pacote organiza manifesto da aplicação, manifest(s) de serviço e outros ficheiros necessários num esquema específico.  Visual Studio configura e gere o pacote na pasta do projeto de aplicação, no diretório 'pkg'.  Clicar no **pacote** a partir do menu de contexto da **aplicação** cria ou atualiza o pacote de aplicação.  Pretende efetuar esta ação se implementar a aplicação utilizando scripts de PowerShell personalizados.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Remover aplicações e tipos de aplicação através do Explorador da nuvem

Pode executar operações de gestão de cluster básicas a partir do Visual Studio através do Explorador na nuvem, pode iniciar a partir do menu de **vista** . Por exemplo, pode eliminar aplicações e anular o aprovisionamento tipos de aplicação no locais ou remotos clusters.

![Remover uma aplicação](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Para mais rica funcionalidade de gestão de cluster, consulte o artigo [visualizar o seu cluster com o Explorador de ferro de serviço](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

- [Modelo de aplicação de serviço ferro](service-fabric-application-model.md)
- [Implementação da aplicação de serviço ferro](service-fabric-deploy-remove-applications.md)
- [Gestão de parâmetros de aplicação para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
- [Depurar sua aplicação de serviço ferro](service-fabric-debugging-your-application.md)
- [Visualizar o seu cluster através do Explorador de ferro de serviço](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
