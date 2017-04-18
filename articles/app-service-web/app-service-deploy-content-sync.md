<properties
    pageTitle="Sincronizar conteúdo de uma pasta na nuvem para a aplicação de serviço do Azure"
    description="Saiba como implementar a aplicação para a aplicação de serviço de Azure através da sincronização de conteúdo de uma pasta na nuvem."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizar conteúdo de uma pasta na nuvem para a aplicação de serviço do Azure

Este tutorial mostra-lhe como implementar a [Aplicação de serviço de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) ao sincronizar o conteúdo dos serviços em nuvem populares armazenamento como o Dropbox e o OneDrive. 

## <a name="overview"></a>Descrição geral de implementação de sincronização de conteúdo

A implementação de sincronização de conteúdo a pedido está ligada ao [motor de implementação de Kudu](https://github.com/projectkudu/kudu/wiki) integrado com o serviço de aplicação. No [Portal do Azure](https://portal.azure.com), que pode designar uma pasta no seu armazenamento em nuvem, trabalhar com o seu código de aplicação e o conteúdo dessa pasta e sincronizar a aplicação de serviço com o clique de um botão. Sincronizar conteúdo utiliza o processo de Kudu de compilação e implementação. 
    
## <a name="contentsync"></a>Como ativar a implementação de sincronização de conteúdo
Para ativar a sincronização de conteúdo a partir do [Portal do Azure](https://portal.azure.com), siga estes passos:

1. No pá da sua aplicação no Portal do Azure, clique em **Definições** > **Origem da implementação**. Clique em **Escolher origem**e, em seguida, selecione **OneDrive** ou **Dropbox** como a origem de implementação. 

    ![Sincronização de conteúdo](./media/app-service-deploy-content-sync/deployment_source.png)

    >[AZURE.NOTE] Devido a diferenças subjacentes nas APIs, o **OneDrive para empresas** não é suportada neste momento. 

2. Conclua o fluxo de trabalho de autorização para ativar a aplicação de serviço aceder a um caminho designado predefinido específico para o OneDrive ou Dropbox onde todos os seus conteúdos de aplicação de serviço serão armazenados.  
    Após a aplicação de serviço de autorização plataforma irá dar-lhe a opção para criar uma pasta de conteúdo em caminho conteúdo designado ou para escolher uma pasta existente conteúda neste caminho de conteúdo designada. Os caminhos conteúdos designados em suas contas de armazenamento na nuvem, utilizadas para a sincronização de aplicação de serviço são as seguintes:  
    * **OneDrive**:`Apps\Azure Web Apps` 
    * **Dropbox**:`Dropbox\Apps\Azure`

3. Após a sincronização de conteúdo inicial a sincronização de conteúdo pode ser iniciada a pedido a partir do portal Azure. Histórico de implementação está disponível com o pá **híbridas** .

    ![Histórico de implementação](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Mais informações para o Dropbox implementação estão disponíveis em [Implementar a partir do Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). 


