<properties 
    pageTitle="Restaurar uma aplicação no Azure" 
    description="Saiba como restaurar a sua aplicação a partir de uma cópia de segurança." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2016" 
    ms.author="cephalin"/>

# <a name="restore-an-app-in-azure"></a>Restaurar uma aplicação no Azure

Este artigo mostra-lhe como restaurar uma aplicação na [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md) tenha anteriormente cópia de segurança (ver a [sua aplicação no Azure para subir](web-sites-backup.md)). Pode restaurar a sua aplicação com o seu bases de dados ligada (base de dados SQL ou MySQL) a pedido para um estado anterior ou criar uma nova aplicação baseada em uma cópia de segurança da sua aplicação original. Criar uma nova aplicação que é executada em paralelo para a versão mais recente pode ser úteis para A / B testes.

Restaurar a partir de cópias de segurança está disponível para as aplicações a ser executada em camadas **e não padrão **Premium** ** . Para obter informações sobre a sua aplicação de dimensionamento, consulte o artigo [Dimensionar para cima de uma aplicação no Azure](web-sites-scale.md). Camada **Premium** permite um maior número de cópias de segurança diárias a ser executado que camada **padrão** .

<a name="PreviousBackup"></a>
## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar uma aplicação a partir de uma cópia de segurança existente

1. No pá **Definições** da sua aplicação no Portal do Azure, clique em **cópias de segurança** para apresentar o pá **cópias de segurança** . Em seguida, clique em **Restaurar agora** na barra de comandos. 
    
    ![Selecione restaurar agora][ChooseRestoreNow]

3. Na pá **Restaurar** , selecione primeiro a origem de cópia de segurança. 

    ![](./media/web-sites-restore/021ChooseSource.png)
    
    A opção de **cópia de segurança da aplicação** mostra-lhe todas as cópias de segurança existentes da aplicação atual e facilmente pode selecionar um. 
    A opção de **armazenamento** permite-lhe selecionar qualquer ficheiro ZIP cópia de segurança a partir de qualquer conta de armazenamento do Windows Azure e contentor na sua subscrição existente. 
    Se estiver a tentar restaurar uma cópia de segurança de outra aplicação, utilize a opção de **armazenamento** .

4. Em seguida, especifique o destino para restaurar a aplicação em **Restaurar destino**.

    ![](./media/web-sites-restore/022ChooseDestination.png)
    
    >[AZURE.WARNING] Se escolher **Substituir**, todos os dados existentes na sua aplicação atual serão apagados. Antes de clicar em **OK**, certifique-se de que é exatamente o pretende fazer.
    
    Pode selecionar **Aplicação existente** para restaurar a cópia de segurança de aplicação para outra aplicação no mesmo grupo de recursos. Antes de utilizar esta opção, deverá já tiver criado noutra aplicação no seu grupo de recursos com espelhando as configuração da base de dados para um definida na cópia de segurança de aplicação. 
    
5. Clique em **OK**.

<a name="StorageAccount"></a>
## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Transferir ou eliminar uma cópia de segurança de uma conta de armazenamento
    
1. A partir do principal **Procurar** pá do Portal do Azure, selecione **contas de armazenamento**.
    
    Será apresentada uma lista das suas contas de armazenamento existente. 
    
2. Selecione a conta de armazenamento que contém a cópia de segurança que pretende transferir ou eliminar.
    
    Será apresentado pá para a conta de armazenamento.

3. Na pá de accountn armazenamento, selecione o contentor que pretende
    
    ![Vista contentores][ViewContainers]

4. Selecione o ficheiro de cópia de segurança que pretende transferir ou eliminar.

    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)

5. Clique em **Transferir** ou **Eliminar** consoante o pretende fazer.  

<a name="OperationLogs"></a>
## <a name="monitor-a-restore-operation"></a>Monitorizar uma operação de restauro
    
1. Para ver os detalhes sobre o sucesso ou falha da operação de restauro de aplicação, navegue para o **Registo de auditoria** pá no portal do Azure. 
    
    O **registos de auditoria** pá apresenta todas as operações, juntamente com o nível, estado, recursos e detalhes do tempo.
    
2. Desloque-se para baixo para encontrar a pretendida restaurar o funcionamento e clique para selecioná-la.

O pá detalhes irá apresentar as informações disponíveis relacionadas com a operação de restaurar.
    
## <a name="next-steps"></a>Próximos passos

Também pode fazer cópia de segurança e restaurar as aplicações de serviço de aplicação utilizar REST API (consulte o artigo [Utilizar resto para fazer cópia de segurança e restaurar a aplicação de serviço de aplicações](websites-csm-backup.md)).

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 
