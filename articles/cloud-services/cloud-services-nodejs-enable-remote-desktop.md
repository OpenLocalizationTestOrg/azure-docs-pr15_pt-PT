<properties 
    pageTitle="Ativar o ambiente de trabalho remoto para serviços em nuvem (Node.js)" 
    description="Saiba como ativar o acesso de ambiente de trabalho remoto para as máquinas virtuais alojamento da sua aplicação Azure Node.js." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="enabling-remote-desktop-in-azure"></a>Activar o ambiente de trabalho remoto no Azure

Ambiente de trabalho remoto permite-lhe aceder ao ambiente de trabalho de uma instância de funções em execução no Azure. Pode utilizar uma ligação de ambiente de trabalho remoto para configurar a máquina virtual ou resolver problemas relacionados com a aplicação.

> [AZURE.NOTE] Este artigo aplica-se a aplicações Node.js alojadas como um serviço de nuvem Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Instalar e configurar o [Azure Powershell](../powershell-install-configure.md).
- Implemente uma aplicação de Node.js para um serviço em nuvem Azure. Para mais informações, consulte o artigo [criar e implementar uma aplicação de Node.js para um serviço em nuvem Azure](cloud-services-nodejs-develop-deploy-app.md).


## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>Passo 1: Utilizar o PowerShell Azure para configurar o serviço de acesso de ambiente de trabalho remoto

Para utilizar o ambiente de trabalho remoto, necessita de atualizar a definição de serviço Azure e configuração com um nome de utilizador, palavra-passe e certificado. 

Efetue os passos seguintes a partir de um computador que contém os ficheiros de origem para a sua aplicação.

1. Execute **o Windows PowerShell** como administrador. (A partir do **Menu Iniciar** ou **Ecrã Iniciar**, de pesquisa para o **Windows PowerShell**.)

2.  Navegue para o diretório que contém a definição de serviço (.csdef) e ficheiros de configuração (.cscfg) de serviço.

3. Introduza o seguinte cmdlet do PowerShell:

        Enable-AzureServiceProjectRemoteDesktop

4. No pedido, introduza um nome de utilizador e palavra-passe.

    ![Ativar azureserviceprojectremotedesktop][enable-rdp]

3.  Introduza o seguinte cmdlet do PowerShell para publicar as alterações:

        Publish-AzureServiceProject

    ![azureserviceproject publicar][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>Passo 2: Ligar para a instância de função

Depois de a publicar a definição do serviço de atualização, pode ligar para a instância de função.

1.  No [portal clássica Azure], selecione **Serviços em nuvem** e, em seguida, selecione o seu serviço.

    ![Azure portal clássico][cloud-services]

2.  Clique em **instâncias**e, em seguida, clique em de **produção** ou de **transição** para ver as instâncias do seu serviço de. Selecione uma instância e, em seguida, clique em **Ligar** na parte inferior da página.

    ![A página de instâncias][3]

2.  Quando clica em **Ligar**, o browser lhe pedir para guardar um ficheiro. rdp. Abra este ficheiro. (Por exemplo, se estiver a utilizar o Internet Explorer, clique em **Abrir**.)

    ![pedir para abrir ou guardar o ficheiro. rdp][4]

3.  Quando o ficheiro é aberto, é apresentada a seguinte mensagem de segurança:

    ![Aviso de segurança do Windows][5]

4.  Clique em **Ligar**, e será apresentado um aviso de segurança para introduzir as credenciais para aceder a instância. Introduza a palavra-passe que criou no [passo 1] [passo 1: configurar o serviço de acesso de ambiente de trabalho remoto através do Azure PowerShell] e, em seguida, clique em **OK**.

    ![linha de comandos do nome de utilizador/palavra-passe][6]

Quando a ligação é estabelecida, ligação de ambiente de trabalho remoto apresenta o ambiente de trabalho da instância no Azure. 

![Sessão remota do ambiente de trabalho][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>Passo 3: Configurar o serviço para desativar o acesso de ambiente de trabalho remoto 

Quando já não necessitar de ligações de ambiente de trabalho remotas para as instâncias de funções na nuvem, desative o acesso de ambiente de trabalho remoto através do [Azure PowerShell].

1.  Introduza o seguinte cmdlet do PowerShell:

        Disable-AzureServiceProjectRemoteDesktop

2.  Introduza o seguinte cmdlet do PowerShell para publicar as alterações:

        Publish-AzureServiceProject

## <a name="additional-resources"></a>Recursos adicionais

- [Aceder a função instâncias no Azure remotamente] 
- [Utilizar o ambiente de trabalho remoto com o Azure funções]
- [Centro de programadores do node.js](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure portal clássico]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
[Aceder a função instâncias no Azure remotamente]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[Utilizar o ambiente de trabalho remoto com o Azure funções]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 