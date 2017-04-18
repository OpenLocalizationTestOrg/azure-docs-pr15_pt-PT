<properties 
   pageTitle="Aceder a nuvens Azure privadas com o Visual Studio | Microsoft Azure"
   description="Saiba como aceder a nuvem privado recursos utilizando o Visual Studio."
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

# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Aceder a nuvens Azure privadas com o Visual Studio

##<a name="overview"></a>Descrição geral

Por predefinição, o Visual Studio suporta pontos finais de resto nuvem Azure público. Isto pode ser um problema, no entanto, se estiver a utilizar o Visual Studio com uma nuvem Azure privada. Pode utilizar certificados para configurar o Visual Studio para aceder a pontos finais de resto nuvem Azure privado. Pode obter estes certificados através da sua Azure publicar o ficheiro de definições.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Para aceder a um Azure privado na nuvem no Visual Studio

1. No [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) para o cloud privado, transfira o ficheiro de definições de publicar ou contacte o administrador para um ficheiro de definições de publicar. Na versão pública do Azure, a ligação para transferir esta é [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Ficheiro que transferir deve ter uma extensão de .publishsettings).

1. No **Explorador de servidor** no Visual Studio, escolha o nó do **Azure** e, no menu de atalho, escolha o comando **Gerir subscrições** .

    ![Gerir o comando de subscrições](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Na caixa de diálogo **Gerir subscrições do Microsoft Azure** , selecione o separador **certificados** e, em seguida, selecione o botão de **importação** .

    ![Importar certificados Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Na caixa de diálogo **Importar Microsoft Azure subscrições** , navegue para a pasta onde que guardou o ficheiro de definições de publicar e escolha o ficheiro, em seguida, selecione o botão de **importação** . Isto importa os certificados no ficheiro de definições de publicar para Visual Studio. Agora deverá conseguir interagir com os recursos da nuvem privado.

    ![Importar definições de publicação](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Próximos passos

[Publicar num serviço de nuvem Azure a partir do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Como: transferir e importar publicar definições e informações da subscrição](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

