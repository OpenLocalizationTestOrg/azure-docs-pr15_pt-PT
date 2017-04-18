<properties 
    pageTitle="Utilizar o PowerShell para criar uma VM com um servidor de relatórios do modo nativo | Microsoft Azure"
    description="Este tópico descreve e explica a implementação e configuração de um servidor de relatórios de modo nativo do SQL Server Reporting Services numa máquina de Virtual do Azure. "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Utilizar o PowerShell para criar um VM Azure com um servidor de relatórios do modo nativo

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Este tópico descreve e explica a implementação e configuração de um servidor de relatórios de modo nativo do SQL Server Reporting Services numa máquina de Virtual do Azure. Os passos neste documento utilizam uma combinação de passos manuais para criar a máquina virtual e um script do Windows PowerShell para configurar o Reporting Services na VM. O script de configuração inclui abrir uma porta de firewall para HTTP ou HTTPs.

>[AZURE.NOTE] Se não forem **HTTPS** no servidor de relatórios, **ignore o passo 2**.
>
>Depois de criar a VM no passo 1, aceda à secção Utilizar script para configurar o servidor de relatórios e HTTP. Depois de executar o script, o servidor de relatório está pronto a utilizar.

## <a name="prerequisites-and-assumptions"></a>Pré-requisitos e pressupostos

- **Subscrição do Azure**: Verifique se o número de núcleos disponíveis na sua subscrição do Azure. Se criar o tamanho da memória virtual recomendado do **A3**, terá de núcleos disponíveis **4** . Se utilizar um tamanho da memória virtual da **A2**, terá de núcleos disponíveis de **2** .
    
    - Para verificar o limite de core da sua subscrição, no portal do Azure clássico, clique em definições no painel da esquerda e, em seguida, clique em utilização no menu superior.
    
    - Para aumentar a quota de core, contacte o [Suporte do Azure](https://azure.microsoft.com/support/options/). Para informações sobre o tamanho VM, consulte o artigo [Tamanhos de Máquina Virtual para Azure](virtual-machines-linux-sizes.md).

- **Scripts do Windows PowerShell**: O tópico assume que tem um conhecimento básico de trabalho do Windows PowerShell. Para obter mais informações sobre como utilizar o Windows PowerShell, consulte o seguinte:

    - [Iniciar o Windows PowerShell no Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
    
    - [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Passo 1: Aprovisionar uma Máquina Virtual Azure

1. Navegue até ao portal do Azure clássico.

1. Clique em **máquinas virtuais** no painel esquerdo.

    ![máquinas de virtuais do Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)

1. Clique em **Novo**.

    ![botão novo](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)

1. Clique na **partir da Galeria**.

    ![vm novo a partir de galeria](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)

1. Clique em **SQL Server 2014 RTM Standard – Windows Server 2012 R2** e, em seguida, clique na seta para continuar.

    ![seguinte](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

    Se precisar dos dados de Reporting Services pelo esforço funcionalidade de subscrições, selecione o **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Para mais informações sobre edições do SQL Server e suporte de funcionalidades, consulte o artigo [Funcionalidades suportadas pelas edições do SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).

1. Na página **configuração de Máquina Virtual** , edite os seguintes campos:
                                    
    - Se existir mais do que uma **data de lançamento de versão**, selecione a versão mais recente.
    
    - **Nome da máquina virtual**: O nome do computador também é utilizado na página configuração seguinte como o nome de serviço de nuvem DNS predefinido. O nome de DNS tem de ser exclusivo entre o Azure serviço. Considere a configurar a VM com um nome de computador que descreva a VM para que é utilizada. Por exemplo ssrsnativecloud.
    
    - **Camadas**: padrão
    
    - **Tamanho: A3** é o tamanho da memória virtual recomendado das cargas de trabalho do SQL Server. Se uma VM só é utilizada como um servidor de relatórios, um tamanho da memória virtual da A2 é suficiente, a menos que o servidor de relatórios experiências uma grande carga de trabalho. Para VM informação de preços, consulte o artigo [Máquinas virtuais preços](https://azure.microsoft.com/pricing/details/virtual-machines/).
    
    - **Novo nome de utilizador**: o nome que fornecer é criado como um administrador na VM.
    
    - **Nova palavra-passe** e **Confirmar**. Esta palavra-passe é utilizado para a nova conta de administrador e é recomendado que utilize uma palavra-passe segura.
    
    - Clique em **seguinte**. ![seguinte](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Na página seguinte, edite os seguintes campos:

    - **Serviço em nuvem**: selecione **criar um novo serviço na nuvem**.
    
    - **Nome de DNS do serviço de nuvem**: Este é o nome DNS público do serviço na nuvem que está associado a VM. O nome predefinido é o nome que escreveu do nome VM. Se nos passos posterior do tópico, criar um certificado SSL de confiança e, em seguida, é utilizado o nome DNS para o valor da "**emitido para**" do certificado.
    
    - **Rede de grupo/Virtual região/afinidade**: selecione a região mais perto aos seus utilizadores finais.
    
    - **Conta de armazenamento**: utilizar uma conta de armazenamento gerado automaticamente.
    
    - **Definir disponibilidade**: nenhuma.
    
    - **Pontos finais** Manter os pontos finais de **Ambiente de trabalho remoto** e **PowerShell** e, em seguida, adicione o ponto final de um HTTP ou HTTPS, dependendo do seu ambiente.

        - **HTTP**: portas predefinidas públicos e privados são **80**. Tenha em atenção que se utilizar uma porta privada que não seja 80, modificar **$HTTPport = 80** no http script.

        - **HTTPS**: portas predefinidas públicos e privados são **443**. É aconselhável segurança alterar a porta privada e configurar a firewall e servidor de relatórios para utilizar a porta privada. Para mais informações sobre os pontos finais, veja [como o comunicação com uma Máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md). Tenha em atenção que se utilizar uma porta que não seja 443, altere o parâmetro **$HTTPsport = 443** no HTTPS script.
    
    - Clique em seguinte. ![seguinte](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)

1. Na última página do assistente, mantenha a predefinição de **instalar o agente VM** selecionada. Os passos neste tópico não utilizarão o agente VM mas se planear manter este VM, o agente de VM e extensões permitirá melhorar ele CM.  Para mais informações sobre o agente VM, consulte o artigo [VM agente e extensões – parte 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Uma do ad extensões instaladas predefinido executar é a extensão de "BGINFO" apresenta no ambiente de trabalho VM, informações de sistema como IP internos e espaço no disco gratuito.

1. Clique em concluído. ![OK](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)

1. O **Estado** da VM apresenta como **inicial (aprovisionar)** durante o processo de aprovisionar e, em seguida, mostra como **Executar** quando estiver a VM aprovisionada e pronto a utilizar.

## <a name="step-2-create-a-server-certificate"></a>Passo 2: Criar um certificado de servidor

>[AZURE.NOTE] Se não necessitar de HTTPS no servidor de relatórios, pode experimentá **ignore o passo 2** e aceda à secção **Utilizar script para configurar o servidor de relatórios e HTTP**. Utilizar o script HTTP para configurar rapidamente o servidor de relatórios e servidor de relatórios estará pronto a utilizar.

Para poder utilizar o HTTPS a VM, é necessário um certificado SSL fidedigno. Dependendo do seu cenário, pode utilizar um dos seguintes dois métodos:

- Um certificado SSL válido emitido por uma autoridade de certificação (AC) e fidedignas pela Microsoft. Os certificados de raiz AC são necessários para ser distribuído através do Microsoft Root Certificate Program. Para mais informações sobre este programa, consulte o artigo [Windows e Windows Phone 8 SSL Root Certificate Program (membro AC)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e [Introdução para o Microsoft Root Certificate Program](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).

- Um certificado autoassinado. Certificados autoassinados não são recomendados para ambientes de produção.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Para utilizar um certificado criado por uma fidedigna certificado de certificação (AC)

1. **Pedir um certificado de servidor para o Web site a partir de uma autoridade de certificação**. 

    Pode utilizar o Assistente de certificado de servidor Web para gerar um ficheiro de pedido de certificado (CertReq) que enviar uma autoridade de certificação ou para gerar um pedido de uma autoridade de certificação online. Por exemplo, serviços de certificados Microsoft no Windows Server 2012. Dependendo do nível de garantia de identificação oferecido pelo seu certificado de servidor, é vários dias para vários meses para que a autoridade de certificação aceitar o pedido e enviar-lhe um ficheiro de certificado. 

    Para mais informações sobre a pedir um certificados de servidor, consulte o seguinte: 
    
    - Utilize [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
    
    - Ferramentas de segurança para administrar o Windows Server 2012.

    [Ferramentas de segurança para administrar o Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)

    >[AZURE.NOTE] O campo **emitido para** do certificado SSL fidedigno deve ser o mesmo que o **Nome de DNS do serviço na nuvem** que utilizou para a nova VM.

1. **Instalar o certificado de servidor no servidor Web**. Neste caso, o servidor Web é a VM que aloja o servidor de relatórios e o Web site é criado em passos posteriores quando configura o Reporting Services. Para mais informações sobre como instalar o certificado do servidor no servidor Web utilizando o snap-in de certificado MMC, consulte o artigo [instalar um certificado de servidor](https://technet.microsoft.com/library/cc740068).
    
    Se pretender utilizar o script incluído com este tópico, para configurar o servidor de relatório, o valor dos certificados **impressão digital** é necessário como um parâmetro do script. Consulte a secção seguinte para obter detalhes sobre como obter a impressão do certificado digital.

1. Atribua o certificado do servidor para servidor de relatórios. A atribuição é concluída na secção seguinte quando configura o servidor de relatórios.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Para utilizar o certificado de auto-assinado máquinas virtuais

Um certificado autoassinado foi criado a VM quando a VM aprovisionada. O certificado tem o mesmo nome que o nome de VM DNS. Para evitar erros de certificado, é necessário que o certificado é fidedigno na VM propriamente dito e também por todos os utilizadores do site.

1. Para confiar em AC raiz do certificado na VM Local, adicione o certificado para as **Autoridades de certificação de raiz fidedigna**. Segue-se um resumo dos passos necessários. Para obter passos detalhados sobre como confia na AC, consulte o artigo [instalar um certificado de servidor](https://technet.microsoft.com/library/cc740068).

    1. A partir do portal do Azure clássico, selecione a VM e clique em ligar. Dependendo da sua configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar para a VM.
    
        ![ligar a máquina virtual azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome de utilizador VM, nome de utilizador e palavra-passe que tiver configurado quando criou a VM. 
    
        Por exemplo, a imagem seguinte, o nome do VM é **ssrsnativecloud** e o nome de utilizador é **utilizadorteste**.
        
        ![nome do início de sessão inlcudes vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
    
    1. Execute mmc.exe. Para obter mais informações, consulte o artigo [como: ver certificados com MMC Snap-in](https://msdn.microsoft.com/library/ms788967.aspx).
    
    1. No menu de **ficheiro** de aplicação de consola, adicionar snap-in **certificados** , selecione a **Conta de computador** quando lhe for pedido e, em seguida, clique em **seguinte**.
    
    1. Selecione o **Computador Local** para gerir e, em seguida, clique em **Concluir**.
    
    1. Clique em **Ok** e, em seguida, expanda o **certificados - pessoal** nós e, em seguida, clique em **certificados**. O certificado adquirem o nome de DNS da VM e termina com **cloudapp.net**. Botão direito do rato no nome do certificado e clique em **Copiar**.
    
    1. Expanda o nó **Autoridades de certificação de raiz fidedigna** e, em seguida, clique em **certificados** e, em seguida, clique em **Colar**.
    
    1. Para validar, faça duplo clique no nome do certificado em **Autoridades de certificação de raiz fidedigna** e certifique-se de que não existem erros e ver o seu certificado. Se pretender utilizar o script HTTPS incluído com este tópico, para configurar o servidor de relatório, o valor dos certificados **impressão digital** é necessário como um parâmetro do script. **Para obter o valor de impressão digital**, conclua o seguinte. Também existe uma amostra de PowerShell para obter a impressão digital na secção [Utilizar script para configurar o servidor de relatórios e HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
        
        1. Faça duplo clique no nome do certificado, por exemplo ssrsnativecloud.cloudapp.net.
        
        1. Clique no separador **Detalhes** .
        
        1. Clique em **impressão digital**. O valor da impressão digital é apresentado no campo de detalhes, por exemplo a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c caixa de factos 2f.
        
        1. Copiar a impressão digital e guardar o valor mais tarde ou editar o script agora.
        
        1. (*) Antes de executar o script, remova os espaços entre os pares de valores. Por exemplo a impressão digital puderem ser verificada antes agora seria a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
        
        1. Atribua o certificado do servidor para servidor de relatórios. A atribuição é concluída na secção seguinte quando configura o servidor de relatórios.

Se estiver a utilizar um certificado SSL autoassinado, o nome do certificado corresponde já o nome do anfitrião da VM. Assim, o DNS do computador, já está registado globalmente e podem ser acedido a partir de qualquer cliente.

## <a name="step-3-configure-the-report-server"></a>Passo 3: Configurar o servidor de relatórios

Esta secção explica o configurar a VM como um servidor de relatórios de modo nativo do Reporting Services. Pode utilizar um dos seguintes métodos para configurar o servidor de relatórios:

- Utilize o script para configurar o servidor de relatórios

- Utilize o Gestor de configuração para configurar o servidor de relatórios.

Para obter passos mais detalhados, consulte a secção [ligar à máquina Virtual e iniciar o Gestor de configuração de serviços de elaboração de relatórios](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Nota de autenticação:** Autenticação do Windows é o método de autenticação recomendado e é a autenticação do Reporting Services predefinido. Apenas os utilizadores que estão configurados na VM podem aceder Reporting Services e atribuídas a funções de Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Utilizar o script para configurar o servidor de relatórios e HTTP

Para utilizar o script do Windows PowerShell para configurar o servidor de relatório, conclua os passos seguintes. A configuração inclui HTTP, não HTTPS:

1. A partir do portal do Azure clássico, selecione a VM e clique em ligar. Dependendo da sua configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar para a VM.

    ![ligar a máquina virtual azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome de utilizador VM, nome de utilizador e palavra-passe que tiver configurado quando criou a VM. 

    Por exemplo, a imagem seguinte, o nome do VM é **ssrsnativecloud** e o nome de utilizador é **utilizadorteste**.
    
    ![nome do início de sessão inlcudes vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. No VM, abra **O Windows PowerShell ISE** com privilégios administrativos. Está instalado o ISE do PowerShell por predefinição no Windows server 2012. É recomendável que utilize o ISE em vez de numa janela do Windows PowerShell padrão para que possa colar o script no ISE, modificar o script e, em seguida, execute o script.

1. No Windows PowerShell ISE, clique no menu **Ver** e, em seguida, clique em **Mostrar o painel de Script**.

1. Copie o seguinte script e colar o script para o painel de script do Windows PowerShell ISE.

        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
        
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
        
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Report Server Configuration Steps
        
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
           
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
        
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Se criou a VM com uma porta HTTP que não seja 80, modifique o parâmetro $HTTPport = 80.

1. O script está atualmente configurado para Reporting Services. Se pretender executar o script para Reporting Services, modifique parte da versão do caminho para o espaço de nomes "v11", sobre a instrução de obter WmiObject.

1. Execute o script.

**Validação**: para verificar se a funcionalidade de servidor do relatório básico está a funcionar, consulte a secção [verificar a configuração](#verify-the-configuration) mais adiante neste tópico.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Utilizar o script para configurar o servidor de relatórios e HTTPS

Para utilizar o Windows PowerShell para configurar o servidor de relatório, conclua os passos seguintes. A configuração inclui HTTPS, não HTTP.

1. A partir do portal do Azure clássico, selecione a VM e clique em ligar. Dependendo da sua configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar para a VM.

    ![ligar a máquina virtual azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome de utilizador VM, nome de utilizador e palavra-passe que tiver configurado quando criou a VM. 

    Por exemplo, a imagem seguinte, o nome do VM é **ssrsnativecloud** e o nome de utilizador é **utilizadorteste**.

    ![nome do início de sessão inlcudes vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)

1. No VM, abra **O Windows PowerShell ISE** com privilégios administrativos. Está instalado o ISE do PowerShell por predefinição no Windows server 2012. É recomendável que utilize o ISE em vez de numa janela do Windows PowerShell padrão para que possa colar o script no ISE, modificar o script e, em seguida, execute o script.

1. Para permitir que os scripts em execução, execute o seguinte comando do Windows PowerShell:

        Set-ExecutionPolicy RemoteSigned

    Em seguida, pode executar o seguinte procedimento para verificar a política de:

        Get-ExecutionPolicy

1. No **Windows PowerShell ISE**, clique no menu **Ver** e, em seguida, clique em **Mostrar o painel de Script**.

1. Copie o seguinte script e cole-a para o painel de script do Windows PowerShell ISE.

        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
        
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
        
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
        
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
        
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
        
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
        
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
        
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
        
        ## Reporting Services Report Server Configuration Steps
        
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
        
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
        
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
            
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
        
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
          
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
        
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
        
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
        
        ## 3. Setting the Report Manager URL ##
        
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
        
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
        
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
        
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
        
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
        
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
        
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time

1. Modifica o parâmetro **$certificatehash** o script:

    - Este é um parâmetro **obrigatório** . Se não guardou o valor do certificado os passos anteriores, utilize um dos seguintes métodos para copiar o valor de hash do certificado da impressão digital certificados.:

        No VM, abra o Windows PowerShell ISE e execute o seguinte comando:

            dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer

        O resultado terá uma aspeto semelhante ao seguinte. Se o script devolver uma linha em branco, a VM não tiver um certificado configurado, por exemplo, consulte a secção [para utilizar o certificado de auto-assinado máquinas virtuais](#to-use-the-virtual-machines-self-signed-certificate).
    
    OU
    
    - A VM executado mmc.exe e, em seguida, adicionar snap-in **certificados** .
    
    - No nó **As autoridades de certificação de raiz fidedignos** faça duplo clique em seu nome de certificado. Se estiver a utilizar o certificado autoassinado da VM, o certificado adquirem o nome de DNS da VM e termina com **cloudapp.net**.
    
    - Clique no separador **Detalhes** .
    
    - Clique em **impressão digital**. O valor da impressão digital é apresentado no campo de detalhes, por exemplo af 11 60 b6 4. oB 28 8 d 89 0a 82 12 ff 6º b a9 c3 66 4f 31 90 48
    
    - **Antes de executar o script**, remova os espaços entre os pares de valores. Por exemplo af1160b64b288d890a8212ff6ba9c3664f319048

1. Modifica o parâmetro **$httpsport** : 

    - Se tiver utilizado a porta 443 para o ponto final HTTPS, não é necessário atualizar este parâmetro no script. Caso contrário, utilize o valor da porta que selecionou quando configurou o ponto final privado de HTTPS na VM.

1. Modifica o parâmetro **$DNSName** : 

    - O script está configurado para um certificado de carateres universais $DNSName = "+". Se não fizer não pretende configurar para um enlace de certificado de caracteres universais, comentar $DNSName ="+"e ativar a linha seguinte, a referência de $DNSNAme completo, # # $DNSName="$server.cloudapp.net".

        Altere o valor de $DNSName se não pretender utilizar o nome DNS a máquina virtual para Reporting Services. Se utilizar o parâmetro, o certificado também tem de utilizar este nome e registar o nome globalmente num servidor de DNS.

1. O script está atualmente configurado para Reporting Services. Se pretender executar o script para Reporting Services, modifique parte da versão do caminho para o espaço de nomes "v11", sobre a instrução de obter WmiObject.

1. Execute o script.

**Validação**: para verificar se a funcionalidade de servidor do relatório básico está a funcionar, consulte a secção [verificar a configuração](#verify-the-connection) mais adiante neste tópico. Para verificar o certificado enlace abra uma linha de comandos com privilégios administrativos e, em seguida, execute o seguinte comando:

    netsh http show sslcert

O resultado será incluem o seguinte:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Utilize o Gestor de configuração para configurar o servidor de relatórios

Se não pretender executar o script do PowerShell para configurar o servidor de relatório, siga os passos nesta secção para utilizar o Gestor de configuração de modo nativo do Reporting Services para configurar o servidor de relatórios.

1. A partir do portal do Azure clássico, selecione a VM e clique em ligar. Utilize o nome de utilizador e palavra-passe que tiver configurado quando criou a VM.

    ![ligar a máquina virtual azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)

1. Executar o Windows update e instale atualizações para a VM. Se for necessário um reinício da VM, reinicie a VM e restabelecer a ligação para a VM a partir do Azure portal clássico.

1. A partir do menu Iniciar na VM, escreva o **Reporting Services** e abra o **Gestor de configuração dos serviços de elaboração de relatórios**.

1. Deixe os valores predefinidos para o **Nome de servidor** e **Instância de servidor do relatório**. Clique em **Ligar**.

1. No painel esquerdo, clique em **URL do serviço Web**.

1. Por predefinição, RS está configurado para porta HTTP 80 com IP "Atribuído todas as". Para adicionar HTTPS:

    1. No **Certificado SSL**: selecione o certificado que pretende utilizar, por exemplo [nome do VM]. cloudapp.net. Se não existirem certificados estiverem listados, consulte a secção **passo 2: criar um certificado de servidor** para obter informações sobre como instalar e confiar no certificado na VM.
    
    1. Em **Porta de SSL**: selecione 443. Se tiver configurado o ponto final privado de HTTPS na VM com uma porta privada diferente, utilize esse valor aqui.
    
    1. Clique em **Aplicar** e aguarde que a operação concluir.

1. No painel esquerdo, clique em **base de dados**.

    1. Clique em **Alterar base de dados**e.
    
    1. Clique em **criar uma nova base de dados de servidor de relatório** e, em seguida, clique em **seguinte**.
    
    1. Deixe o **Nome do servidor**predefinido: como a VM atribua um nome e deixe a predefinição de **Tipo de autenticação** como **Utilizador atual** – **Segurança integrada**. Clique em **seguinte**.
    
    1. Deixe o **Nome da base de dados** como **ReportServer** predefinido e clique em **seguinte**.
    
    1. Deixe a predefinição de **Tipo de autenticação** como **Credenciais do serviço** e clique em **seguinte**.
    
    1. Clique em **seguinte** na página de **Resumo** .
    
    1. Quando a configuração está concluída, clique em **Concluir**.

1. No painel esquerdo, clique em **Gestor de relatórios do URL**. Deixe a predefinição **Directório Virtual** como **relatórios** e clique em **Aplicar**.

1. Clique em **Sair** para fechar o Gestor de configuração de serviços de elaboração de relatórios.

## <a name="step-4-open-windows-firewall-port"></a>Passo 4: Abrir porta de Firewall do Windows

>[AZURE.NOTE] Se um dos scripts que utilizou para configurar o servidor de relatório, pode ignorar esta secção. O script incluído um passo para abrir a porta de firewall. A predefinição tiver sido portas 80 para HTTP e 443 para HTTPS.

Para ligar remotamente ao Gestor de relatórios ou servidor de relatórios na máquina virtual, um ponto final de TCP é necessária a VM. É necessário para abrir a mesma porta na firewall a VM. O ponto final foi criado quando a VM aprovisionada.

Esta secção fornece informações básicas sobre como abrir a porta de firewall. Para obter mais informações, consulte [Configurar uma Firewall de acesso de servidor de relatório](https://technet.microsoft.com/library/bb934283.aspx)

>[AZURE.NOTE] Se tiver utilizado o script para configurar o servidor de relatório, pode ignorar esta secção. O script incluído um passo para abrir a porta de firewall.

Se tiver configurado uma porta privada para HTTPS que não seja 443, modifique o seguinte script corretamente. Para abrir a porta **443** a Firewall do Windows, execute o seguinte procedimento:

1. Abra uma janela do Windows PowerShell com privilégios administrativos.

1. Se utilizou uma porta que não seja 443 quando configurou o ponto final HTTPS na VM, atualize a porta no comando seguinte e, em seguida, execute o comando:

        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443

1. Quando o comando for concluída, **Ok** é apresentado na linha de comandos.

Para verificar que é aberta a porta, abra uma janela do Windows PowerShell e execute o seguinte comando:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Verifique a configuração

Para verificar se a funcionalidade de servidor do relatório básico agora está a funcionar, abra o browser com privilégios administrativos e, em seguida, navegue para o seguinte relatório servidor ad Gestor de relatórios URLS:

- No VM, navegue para o URL do servidor de relatório:

        http://localhost/reportserver

- No VM, navegue para o URL do Gestor de relatórios:

        http://localhost/Reports

- A partir do seu computador local, navegue para o relatório **remoto** Gestor sobre a VM. Atualize o nome de DNS no exemplo seguinte conforme adequado. Quando lhe for pedido para uma palavra-passe, utilize as credenciais de administrador que criou quando a VM aprovisionada. O nome de utilizador está no [domínio]\[nome de utilizador] formato, onde o domínio é o nome do computador VM, por exemplo ssrsnativecloud\testuser. Se não estiver a utilizar HTTP**S**, remova o **s** no URL. Consulte a secção seguinte para obter informações sobre como criar utilizadores adicionais no VM.

        https://ssrsnativecloud.cloudapp.net/Reports

- A partir do seu computador local, navegue para o URL do servidor remoto relatório. Atualize o nome de DNS no exemplo seguinte conforme adequado. Se não estiver a utilizar HTTPS, remova o s no URL.

        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Criar utilizadores e atribuir funções

Depois de configurar e verificar o servidor de relatórios, uma tarefa administrativa comum é criar um ou mais utilizadores e atribuir utilizadores a funções de Reporting Services. Para mais informações, consulte o seguinte:

- [Criar uma conta de utilizador local](https://technet.microsoft.com/library/cc770642.aspx)

- [Acesso de utilizador de conceder a um servidor de relatórios (Gestor de relatório)](https://msdn.microsoft.com/library/ms156034.aspx))

- [Criar e gerir as atribuições de funções](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar relatórios à máquina Virtual Azure

A tabela seguinte resume algumas das opções da publicar relatórios existentes a partir de um computador no local ao servidor de relatórios alojado do Microsoft Azure Virtual Machine:

- **Script de RS.exe**: utilizar RS.exe script para copiar itens de relatório a partir de e servidor de relatórios existente para o Microsoft Azure Virtual Machine. Para mais informações, consulte a secção "Modo nativo para o modo nativo – máquina de Virtual do Microsoft Azure" no [exemplo Reporting Services rs.exe Script para migrar o conteúdo entre servidores de relatórios](https://msdn.microsoft.com/library/dn531017.aspx).

- **Report Builder**: A máquina virtual inclui o clique-uma vez a versão do Microsoft SQL Server Report Builder. Para começar a comunicar o tempo construtor a primeira na máquina virtual:

    1. Inicie o seu browser com privilégios administrativos.
    
    1. Procure o Gestor de relatórios na máquina virtual e clique em **Report Builder** no Friso.

    Para mais informações, consulte o artigo [instalar, Uninstalling e do Report Builder de suporte](https://technet.microsoft.com/library/dd207038.aspx).

- **Ferramentas de dados do SQL Server: VM**: Se criou a VM com SQL Server 2012, em seguida, ferramentas de dados do SQL Server está instalada no computador virtual e podem ser utilizadas para criar **Relatório projectos de servidor** e relatórios na máquina virtual. Ferramentas de dados do SQL Server pode publicar os relatórios de servidor de relatórios na máquina virtual.
    
    Se criou a VM com o SQL server 2014, pode instalar o SQL Server dados ferramentas de BI para o visual Studio. Para mais informações, consulte o seguinte:

    - [Ferramentas de dados do Microsoft SQL Server – Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)

    - [Ferramentas de dados do Microsoft SQL Server – Business Intelligence para Visual Studio 2012http](https://www.microsoft.com/download/details.aspx?id=36843)

    - [Ferramentas de dados do SQL Server e o SQL Server Business Intelligence (SSDT BI)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)

- **Ferramentas de dados do SQL Server: remoto**: no seu computador local, criar um projeto do Reporting Services nas ferramentas de dados do SQL Server que contém os relatórios do Reporting Services. Configure o project para ligar para o URL do serviço web.

    ![Propriedades do projeto SSDT para o SSRS project](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)

- **Utilizar script**: Utilize o script para copiar o conteúdo de servidor do relatório. Para mais informações, consulte o artigo [exemplo Reporting Services rs.exe Script para migrar o conteúdo entre servidores de relatórios](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimizar o custo se não estiver a utilizar a VM

>[AZURE.NOTE] Para minimizar os custos para Azure máquinas virtuais quando não estiver em utilização, encerre a VM a partir do Azure portal clássica. Se utilizar as opções de energia Windows dentro de uma VM encerrar a VM, que é ainda lhe ser cobrada a mesma quantidade para a VM. Para reduzir taxas, tem de encerrar a VM no portal do Azure clássica. Se já não precisar da VM, lembre-se eliminar a VM e os ficheiros associados. vhd para evitar taxas de armazenamento. Para obter mais informações, consulte a secção FAQ no [Máquinas virtuais preços detalhes](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Obter mais informações

### <a name="resources"></a>Recursos

- Para semelhante conteúdo relacionado com a implementação de servidor único de Business Intelligence do SQL Server e SharePoint 2013, consulte o artigo [Utilizar o Windows PowerShell para criar um Azure VM com o SQL Server BI e o SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).

- Para semelhante conteúdo relacionado com a implementação de um servidor com várias de Business Intelligence do SQL Server e SharePoint 2013, consulte [Implementar o SQL Server Business Intelligence em máquinas virtuais do Azure](https://msdn.microsoft.com/library/dn321998.aspx).

- Para obter informações gerais relacionados com implementações do SQL Server Business Intelligence em máquinas virtuais do Azure, consulte o artigo [SQL Server Business Intelligence em máquinas virtuais do Azure](virtual-machines-windows-classic-ps-sql-bi.md).

- Para mais informações sobre o custo do Azure calcular taxas, consulte o artigo separador máquinas virtuais do [Azure Calculadora comparar](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Conteúdo da Comunidade

- Para obter instruções passo a passo sobre como criar um servidor de relatórios do Reporting Services nativo modo sem utilizar o script, consulte o artigo [Alojamento SQL serviço de relatórios no Azure Virtual Machine](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Ligações para outros recursos para SQL Server em máquinas Azure VMs

[SQL Server em máquinas virtuais Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md)
