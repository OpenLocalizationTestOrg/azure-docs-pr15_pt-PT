<properties 
   pageTitle="Depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio | Microsoft Azure"
   description="Depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-a-published-cloud-service-with-intellitrace-and-visual-studio"></a>Depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio

##<a name="overview"></a>Descrição geral

Com IntelliTrace, pode iniciar sessão extensas informações de depuração de uma instância de função quando é executada no Azure. Se precisar de localizar a causa de um problema, pode utilizar os registos de IntelliTrace para se deslocar pelos seu código do Visual Studio, como se estivesse em execução no Azure. Na verdade, IntelliTrace registos chave execução de código e dados de ambiente, quando a aplicação Azure está a funcionar como um serviço na nuvem no Azure e permite-lhe os dados a partir do Visual Studio registados de reprodução. Como alternativa, pode utilizar a depuração remota para anexar diretamente a um serviço na nuvem que está a ser executado no Azure. Consulte o artigo [depuração de serviços em nuvem](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT] IntelliTrace destina-se apenas cenários de depuração e não deve ser utilizado para uma implementação de produção.

>[AZURE.NOTE] Pode utilizar IntelliTrace se tiver o Visual Studio Enterprise instalado e os destinos aplicação Azure .NET Framework 4 ou uma versão posterior. IntelliTrace recolhe informações para as Azure funções. As máquinas virtuais para estas funções sempre executadas sistemas operativos de 64 bits.

## <a name="to-configure-an-azure-application-for-intellitrace"></a>Para configurar uma aplicação do Azure para IntelliTrace

Para ativar IntelliTrace para uma aplicação do Azure, tem de criar e publicar a aplicação a partir de um projeto do Visual Studio Azure. Tem de configurar IntelliTrace para a sua aplicação Azure antes de a publicar para Azure. Se publicar a sua aplicação sem configurar IntelliTrace mas, em seguida, decidir que pretende para o fazer, tem de publicar a aplicação novamente a partir do Visual Studio. Para mais informações, consulte [publicar um serviço na nuvem utilizando as ferramentas do Azure](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quando estiver pronto para implementar a aplicação Azure, certifique-se de que os seus alvos de compilação projeto estão definidos para **Depurar**.

1. Abrir o menu de atalho para o projeto Azure no Explorador de solução e escolha **Publicar**.
 
    Aparece o Assistente da aplicação de Azure publicar.

1. Para recolher registos de IntelliTrace para a sua aplicação quando é publicado na nuvem, selecione a caixa de verificação **Ativar IntelliTrace** .

    >[AZURE.NOTE] Pode ativar a IntelliTrace ou criação de perfis durante a publicação de aplicação do Azure. Não é possível ativar ambos.

1. Para personalizar a configuração de IntelliTrace básica, selecione a hiperligação **Definições** .

    A caixa de diálogo Definições de IntelliTrace é apresentada, conforme apresentado na figura seguinte. Pode especificar quais os eventos para registo, se pretende recolher informações sobre a chamada, quais módulos e processos para recolher registos para e quanto espaço para atribuir a gravação. Para mais informações sobre IntelliTrace, consulte o artigo [depuração com IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

O registo de IntelliTrace é um ficheiro de registo circular do tamanho máximo especificado nas definições de IntelliTrace (o tamanho predefinido é 250 MB). Registos de IntelliTrace são recolhidos para um ficheiro no sistema de ficheiros da máquina virtual. Quando pedir os registos de início, um instantâneo é visto essa numa hora e é transferido para o seu computador local.

Após a aplicação Azure foi publicada Azure, pode determinar se IntelliTrace ter sido ativada a partir do nó Azure calcular no Explorador do servidor, conforme apresentado na seguinte imagem:

![VST_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="downloading-intellitrace-logs-for-a-role-instance"></a>Transferir IntelliTrace registos para uma instância de função

Pode transferir IntelliTrace registos de início de uma instância de função a partir do nó **Serviços em nuvem** no **Explorador do servidor**. Expanda o nó de **Serviços em nuvem** até localizar a instância que lhe interessam, abra o menu de atalho para esta instância e escolha **Ver registos de IntelliTrace**. Os registos de IntelliTrace são transferidos para um ficheiro num diretório no seu computador local. Os registos de cada vez que solicitar a IntelliTrace, é criado um novo instantâneo.

Quando os registos são transferidos, o Visual Studio mostra o progresso da operação de na janela do registo de atividade de Azure. Como apresentado na figura seguinte, pode expandir o item de linha para a operação ver mais detalhes.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Pode continuar a trabalhar no Visual Studio enquanto os registos de IntelliTrace estão a ser transferidas. Quando o registo de concluir a transferência, serão abertos automaticamente no Visual Studio.

>[AZURE.NOTE] Os registos de IntelliTrace podem conter exceções que a arquitetura gera e subsequentemente processa. Código de estrutura interna gera estes exceções como uma parte normal do início para cima de uma função, para que pode ignorá-los.

## <a name="see-also"></a>Consulte também

[Depuração de serviços em nuvem](https://msdn.microsoft.com/library/ee405479.aspx)

