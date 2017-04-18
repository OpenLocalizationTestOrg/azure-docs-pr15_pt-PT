<properties
    pageTitle="Ligar computadores com o Windows para o registo de análise | Microsoft Azure"
    description="Este artigo mostra os passos para ligar os computadores de Windows na sua infraestrutura no local diretamente à OMS ao utilizar uma versão personalizada do Microsoft monitorização agente MMA ()."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="connect-windows-computers-to-log-analytics"></a>Ligar computadores com o Windows para a análise de registo

Este artigo mostra os passos para ligar os computadores com o Windows na sua infraestrutura no local diretamente para OMS áreas de trabalho com uma versão personalizada do Microsoft monitorização agente MMA (). Tem de instalar e ligar agentes para todos os computadores que pretende que a bordo para OMS por ordem das mesmas para enviar dados para OMS e para ver e agir nesses dados no portal do OMS. Cada agente pode comunicar a várias áreas de trabalho.

Pode instalar o agentes utilizando a configuração, linha de comandos, ou com pretendido estado configuração (DSC) no Azure automatização.  

>[AZURE.NOTE] Para máquinas virtuais em execução no Azure pode simplificar a instalação utilizando a [extensão de máquina virtual](log-analytics-azure-vm-extension.md).

Em computadores com ligação à Internet, o agente irá utilizar a ligação à Internet para enviar dados para OMS. Para computadores que não tem ligação à Internet, pode utilizar um proxy ou o reencaminhador de análise de registo de OMS.

Ligar os computadores do Windows para OMS é simples com 3 passos simples:

1. Transferir o ficheiro de configuração do agente
2. Instalar o agente utilizando o método que escolher
3. Configurar o agente ou adicionar áreas de trabalho adicionais, se for necessário

O diagrama seguinte mostra a relação entre o seu computadores com o Windows e OMS após ter instalado e configurado agentes.

![OMS direct-agente de diagrama](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## <a name="system-requirements-and-required-configuration"></a>Requisitos de sistema e configuração necessária
Antes de instalar ou implementar agentes, reveja os seguintes detalhes para garantir a que cumprir requisitos necessários.

- Só pode instalar o MMA OMS em computadores com o Windows Server 2008 SP 1 ou posterior ou Windows 7 SP1 ou posterior.
- Terá de uma subscrição do OMS.  Para obter informações adicionais, consulte o artigo [começar a trabalhar com a análise de registo](log-analytics-get-started.md).
- Cada computador com o Windows tem de conseguir ligar à Internet utilizando HTTPS. Esta ligação pode ser direta por um proxy, ou através do reencaminhador de análise de registo de OMS.
- Pode instalar o MMA OMS em computadores autónomas, servidores e máquinas virtuais. Se pretende ligar alojado no Azure máquinas virtuais OMS, consulte o artigo [máquinas virtuais de Azure ligar-se para a análise de registo](log-analytics-azure-vm-extension.md).
- O agente tem de utilizar porta TCP 443 para vários recursos. Para mais informações, consulte o artigo [Configurar definições de proxy e a firewall no registo de análise](log-analytics-proxy-firewall.md).

## <a name="download-the-agent-setup-file-from-oms"></a>Transferir o ficheiro de configuração do agente de OMS
1. No portal do OMS, na página **Descrição geral** , clique no mosaico **Definições** .  Clique no separador de **Origens ligadas** na parte superior.  
    ![Separador de origens ligada](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Em **Anexar computadores diretamente**, clique em **Transferir o Windows Agent** aplicável ao seu tipo de processador de computador para transferir o ficheiro de configuração.
3. À direita do **ID de área de trabalho**, clique no ícone de copiar e colar o ID do bloco de notas.
4. À direita da **Chave primária**, clique no ícone de copiar e colar a chave de bloco de notas.     
    ![copiar o ID de área de trabalho e de chave primária](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## <a name="install-the-agent-using-setup"></a>Instalar o agente de utilizar o programa de configuração
1. Execute a configuração ao instalar o agente num computador que pretende gerir.
2. Na página de boas-vindas, clique em **seguinte**.
3. Na página de termos de licenciamento, leia a licença e, em seguida, clique em **posso Concordo**.
4. Na página de pasta de destino, alterar ou manter a pasta de instalação predefinido e, em seguida, clique em **seguinte**.
5. Na página de opções de configuração do agente, pode optar por ligar o agente para análise de registo Azure (OMS), Gestor de operações, ou pode deixar as opções em branco se pretender configurar o agente mais tarde. Clique em **seguinte**.   
    - Se optar por ligar-se para a análise do Azure Log (OMS), cole o **ID da área de trabalho** e a **Chave de área de trabalho (chave primária)** , que copiou no bloco de notas no procedimento anterior e, em seguida, clique em **seguinte**.  
        ![Colar ID da área de trabalho e de chave primária](./media/log-analytics-windows-agents/connect-workspace.png)
    - Se optar por ligar ao Gestor de operações, escreva o **Nome do grupo de gestão**, nome de **Servidor de gestão** e **Porta do servidor de gestão**e, em seguida, clique em **seguinte**. Na página de conta da ação de agente, selecione a conta de sistema Local ou uma conta de domínio local e, em seguida, clique em **seguinte**.  
        ![configuração da gestão de grupo](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![conta agente de ação](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. No pronto para página instalar, reveja as opções e, em seguida, clique em **instalar**.
7. Na configuração concluída com êxito de página, clique em **Concluir**.
8. Quando estiver concluído, o **Microsoft Agent monitorização** é apresentada no **Painel de controlo**. Pode rever a sua configuração aí e certifique-se de que o agente está ligado à operacionais informações (OMS). Quando estiver ligado a OMS, o agente apresenta uma mensagem a informar: **o agente de monitorização da Microsoft com êxito tem ligado ao serviço do conjunto de aplicações do Microsoft operações gestão.**

## <a name="install-the-agent-using-the-command-line"></a>Instalar o agente utilizando a linha de comandos
- Modificar e, em seguida, utilize o exemplo seguinte para instalar o agente utilizando a linha de comandos.

    >[AZURE.NOTE] Se pretende atualizar um agente, terá de utilizar a análise de registo de scripting API. Consulte a secção seguinte para atualizar um agente.

    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>Atualizar o agente e adicionar uma área de trabalho utilizando um script
Pode atualizar um agente e adicionar uma área de trabalho utilizando a análise de registo de scripting API com o exemplo seguinte do PowerShell.

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

>[AZURE.NOTE] Caso tenha utilizado linha de comandos ou script anteriormente para instalar ou configurar o agente, `EnableAzureOperationalInsights` foi substituído pelo `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalar o agente de utilizar DSC no Azure automatização

>[AZURE.NOTE] Este exemplo de script e procedimento não será atualizado um agente existente.

1. Importe o xPSDesiredStateConfiguration DSC módulo do [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) para automatização Azure.  
2.  Crie activos variáveis de automatização do Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Defina *OPSINSIGHTS_WS_ID* ao seu ID de área de trabalho de análise de registo de OMS e defina *OPSINSIGHTS_WS_KEY* para a chave primária da área de trabalho.
3.  Utilizar o script abaixo e guardá-lo como MMAgent.ps1
4.  Modificar e, em seguida, utilize o exemplo seguinte para instalar o agente de utilizar DSC no Azure automatização. Importe MMAgent.ps1 para automatização Azure utilizando a interface de automatização do Azure ou cmdlet.
5.  Atribua um nó na configuração. O MMA vai ser seguia para o nó e dentro de 15 minutos o nó irá verificar a sua configuração.

```
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Configurar um agente manualmente ou adicionar áreas de trabalho adicionais
Se tiver instalado agentes mas não configurá-los ou se pretende que o agente para comunicar a várias áreas de trabalho, pode utilizar as seguintes informações para ativar um agente ou reconfigurá-lo. Depois de ter configurado o agente, registe-se com o serviço do agente e irá obter informações de configuração necessárias e pacotes de gestão que contêm informações de solução.

1. Após instalar o Microsoft monitorização Agent, abra o **Painel de controlo**.
2. Abra **O Microsoft Agent monitorização** e, em seguida, clique no separador de **Análise de registo de Azure (OMS)** .   
3. Clique em **Adicionar** para abrir a caixa **Adicionar uma área de trabalho de análise de registo** .
4. Cole o **ID da área de trabalho** e a **Chave de área de trabalho (chave primária)** , que copiou no bloco de notas no procedimento anterior para a área de trabalho que pretende adicionar e, em seguida, clique em **OK**.  
    ![configurar informações operacionais avançados](./media/log-analytics-windows-agents/add-workspace.png)

Depois dos dados são recolhidos de computadores monitorizadas pelo agente, o número de computadores controlado pelo OMS aparecerá no portal do OMS no separador **Origens ligadas** **Definições** como **Servidores ligados**.


## <a name="to-disable-an-agent"></a>Para desativar um agente
1. Depois de instalar o agente, abra o **Painel de controlo**.
2. Abra o Microsoft Agent monitorização e, em seguida, clique no separador de **Análise de registo de Azure (OMS)** .
3. Selecione uma área de trabalho e, em seguida, clique em **Remover**. Repita este passo para todas as outras áreas de trabalho.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Opcionalmente, configurar agentes de um relatório a um grupo de gestão do Gestor de operações

Se utilizar o Gestor de operações na sua infraestrutura de TI, também pode utilizar o agente MMA como um agente do Gestor de operações.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Para configurar agentes de MMA para comunicar a um grupo de gestão do Gestor de operações
1.  No computador onde está instalado o agente, abra o **Painel de controlo**.
2.  Abra **O Microsoft Agent monitorização** e, em seguida, clique no separador de **Gestor de operações** .
    ![Separador Microsoft Gestor de operações de agente de monitorização](./media/log-analytics-windows-agents/om-mg01.png)
3.  Se os servidores de Gestor de operações ter integração com o Active Directory, clique em **atualizar automaticamente as atribuições de grupo de gestão do AD DS**.
4.  Clique em **Adicionar** para abrir a caixa de diálogo **Adicionar um grupo de gestão** .  
    ![No Microsoft Agent monitorização de adicionar um grupo de gestão](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  Na caixa **nome do grupo de gestão** , escreva o nome do seu grupo de gestão.
6.  Na caixa **servidor de gestão principal** , escreva o nome do computador do servidor de gestão principal.
7.  Na caixa **porta do servidor de gestão** , escreva o número da porta TCP.
8.  Em **Conta agente de ação**, selecione a conta de sistema Local ou uma conta de domínio local.
9.  Clique em **OK** para fechar a caixa de diálogo **Adicionar um grupo de gestão** e, em seguida, clique em **OK** para fechar a caixa de diálogo **Propriedades do agente de monitorização Microsoft** .

## <a name="optionally-configure-agents-to-use-the-oms-log-analytics-forwarder"></a>Opcionalmente, configurar agentes de utilizar o reencaminhador de análise de registo de OMS

Se tiver servidores ou clientes que não possui uma ligação à Internet, ainda pode tê-los enviar dados para OMS utilizando o reencaminhador de análise de registo de OMS.  Quando utiliza o reencaminhador, todos os dados do agentes é enviada através de um único servidor que tenha acesso à Internet. O reencaminhador transfere dados a partir dos agentes para OMS diretamente sem analisar os dados que são transferidos.

Consulte o artigo [OMS registo Analytics reencaminhador](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) para saber mais sobre o reencaminhador, incluindo a instalação e configuração.

Para obter informações sobre como configurar o seu agentes a utilizar um servidor proxy, que é neste caso o reencaminhador OMS, consulte o artigo [Configurar definições de proxy e a firewall no registo de análise](log-analytics-proxy-firewall.md).

## <a name="optionally-configure-proxy-and-firewall-settings"></a>Opcionalmente, configurar definições de proxy e a firewall
Se tiver servidores proxy ou firewalls no seu ambiente que restringir o acesso à Internet, consulte o artigo [Configurar definições de proxy e a firewall no registo Analytics](log-analytics-proxy-firewall.md) para ativar a sua agentes comunicar com o serviço OMS.

## <a name="next-steps"></a>Próximos passos

- [Soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md) para adicionar a funcionalidade e reunir dados.
- [Configurar as definições de proxy e a firewall no registo de análise](log-analytics-proxy-firewall.md) se a sua organização utiliza uma firewall ou servidor proxy para que agentes possam comunicar com o serviço de análise de registo.
