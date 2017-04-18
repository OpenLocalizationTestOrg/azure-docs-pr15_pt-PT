<properties
    pageTitle="SQL Server Business Intelligence | Microsoft Azure"
    description="Este tópico utiliza recursos criados com o modelo clássico de implementação e descreve as funcionalidades de Business Intelligence (BI) disponíveis para o SQL Server em execução no Azure máquinas virtuais (VMs)."
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

# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence em máquinas virtuais do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

A Galeria de Máquina Virtual da Microsoft Azure inclui imagens que contêm as instalações do SQL Server. As edições do SQL Server suportadas nas imagens Galeria são os mesmos ficheiros de instalação, que pode instalar para computadores no local e máquinas virtuais. Este tópico resume as funcionalidades do SQL Server Business Intelligence (BI) instaladas nas imagens e os passos de configuração necessários após uma máquina virtual está aprovisionada. Este tópico descreve também topologias suportadas implementação para funcionalidades de BI e as melhores práticas.

## <a name="license-considerations"></a>Considerações de licença

Existem duas formas de licença do SQL Server em máquinas virtuais do Microsoft Azure:

1. Vantagens de mobilidade licença que fazem parte de Software Assurance. Para mais informações, consulte o artigo [Licença mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. Paga por taxa de horas do Azure máquinas virtuais com o SQL Server instalado. Consulte a secção "SQL Server" em [Máquinas virtuais preços](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Para mais informações sobre as taxas de licenciamento e atuais, consulte [FAQ de licenciamento em máquinas virtuais](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server imagens disponíveis na Galeria de Máquina Virtual Azure

A Galeria de Máquina Virtual da Microsoft Azure inclui várias imagens que contêm Microsoft SQL Server. O software instalado nas imagens de máquina virtual varia consoante a versão do sistema operativo e a versão do SQL Server. A lista de imagens disponíveis na Galeria de máquina virtual Azure alterado com frequência.

![Imagem SQL na Galeria VM azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) O seguinte script do PowerShell devolve a lista de imagens Azure que contêm "SQL-Server" na nomeimagem:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "settings" menu in Azure classic portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Para mais informações sobre as edições e funcionalidades que são suportadas pelo SQL Server, consulte o seguinte:

- [Edições do SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)

- [Funcionalidades suportadas pelas edições do SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Funcionalidades de BI instaladas nas imagens de galeria máquinas virtuais do SQL Server

A tabela seguinte resume as funcionalidades de Business Intelligence instaladas nas imagens de galeria comuns do Microsoft Azure Virtual Machine para SQL Server"

- SQL Server 2016 RC3

- SQL Server 2014 SP1 Enterprise

- SQL Server 2014 SP1 padrão

- SQL Server 2012 SP2 Enterprise

- SQL Server 2012 SP2 padrão

|Funcionalidades de BI do SQL Server|Instalado na imagem de galeria|Notas|
|---|---|---|
|**Modo nativo dos serviços de elaboração de relatórios**|Sim|Instalado, mas exige configuração, incluindo o URL do Gestor de relatório. Consulte a secção [Configurar o Reporting Services](#configure-reporting-services).|
|**Dos Reporting Services modo do SharePoint**|N|A imagem da Galeria de Máquina Virtual da Microsoft Azure não inclui o SharePoint ou do SharePoint ficheiros de instalação. <sup>1</sup>|
|**Extração de multidimensionais do Analysis Services e dados (OLAP)**|Sim|Instalado e configurado como a instância do Analysis Services predefinida|
|**Tabular do Analysis Services**|N|Suportado no SQL Server 2012, 2014 e 2016 imagens mas não está instalado por predefinição. Instale outra instância do Analysis Services. Consulte a secção instalar outros serviços do SQL Server e funcionalidades neste tópico.|
|**Analysis Services Power Pivot para o SharePoint**|N|A imagem da Galeria de Máquina Virtual da Microsoft Azure não inclui o SharePoint ou do SharePoint ficheiros de instalação. <sup>1</sup>|

<sup>1</sup> para obter informações adicionais sobre o SharePoint e o Azure máquinas virtuais, consulte o artigo [Microsoft Azure arquitecturas para o SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) e [Implementação do SharePoint no máquinas virtuais do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Execute o seguinte comando do PowerShell para obter uma lista de serviços instalados, que contêm "SQL" no nome do serviço.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Recomendações gerais e as melhores práticas

- O tamanho mínimo recomendado para uma máquina virtual é **A3** quando utilizar o SQL Server Enterprise Edition. O tamanho de máquina virtual **A4** é recomendado para implementações de BI do SQL Server do Analysis Services e Reporting Services.

    Para obter informações sobre os tamanhos VM atuais, consulte o artigo [Tamanhos de Máquina Virtual para Azure](virtual-machines-linux-sizes.md).

- Prática recomendada para gestão de discos é armazenar dados, registo e ficheiros de cópia de segurança nas unidades que não seja **C**: e **D**:. Por exemplo, criar discos de dados **E**: e **F**:.

    - A unidade de colocação em cache política para a unidade predefinida **C**: não é ideal para trabalhar com dados.

    - **D**: unidade é uma unidade temporária que é utilizada principalmente para o ficheiro da página. **D**: não é mantida de unidade e não é guardada no armazenamento blob do. Tarefas de gestão, tais como uma alteração para o tamanho de máquina virtual repor o **D**: unidade. É recomendável que **não** utilize a **D**: unidade para ficheiros de base de dados, incluindo tempdb.

    Para mais informações sobre como criar e como anexar discos, consulte o artigo [como anexar um disco de dados para uma Máquina Virtual](virtual-machines-windows-classic-attach-disk.md).

- Parar ou desinstalar não tenciona utilizar os serviços. Por exemplo se a máquina virtual só é utilizada para Reporting Services, parar ou desinstalar o Analysis Services e o SQL Server Integration Services. A imagem seguinte é um exemplo dos serviços que são iniciados por predefinição.

    ![Serviços do SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)

    >[AZURE.NOTE] Os cenários de BI suportados é exigido o motor de base de dados do SQL Server. Em servidor única topologia VM, o motor de base de dados é necessária para estar em execução na mesma VM.

    Para mais informações, consulte os seguintes artigos: [Desinstalar o Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) e [desinstalar uma instância do Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).

- Procure novas 'atualizações importantes' **Windows Update** . As imagens do Microsoft Azure Virtual Machine frequentemente são atualizadas; No entanto atualizações importantes poderiam ficam disponíveis a partir do **Windows Update** quando a imagem VM foi atualizada pela última vez.

## <a name="example-deployment-topologies"></a>Topologias de implementação de exemplo

Seguem-se implementações de exemplo que utilizam máquinas virtuais do Microsoft Azure. Topologias em diagramas destes são apenas algumas das topologias possíveis que pode utilizar com funcionalidades de BI do SQL Server e máquinas virtuais do Microsoft Azure.

### <a name="single-virtual-machine"></a>Única máquina Virtual

Analysis Services, Reporting Services, motor de base de dados do SQL Server e origens de dados numa única máquina virtual.

![cenário de BI iass com 1 máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Duas máquinas virtuais

- Do Analysis Services, Reporting Services e o SQL Server Database Engine numa única máquina virtual. Esta implementação inclui as bases de dados de servidor do relatório.

- Origens de dados numa segunda VM. A segunda VM inclui motor de base de dados do SQL Server como uma origem de dados.

![cenário de BI iaas com 2 máquinas virtuais](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azure mista – dados na base de dados Azure SQL

- Do Analysis Services, Reporting Services e o SQL Server Database Engine numa única máquina virtual. Esta implementação inclui as bases de dados de servidor do relatório.

- Origem de dados é uma base de dados Azure SQL.

![BI iaas cenários vm e AzureSQL como origem de dados](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Híbrido – dados no local

- Na implementação exemplo Analysis Services, Reporting Services e o motor de base de dados do SQL Server são executados numa única máquina virtual. A máquina virtual aloja as bases de dados de servidor do relatório. A máquina virtual está associada a um domínio no local através da rede Virtual do Azure ou solução de túnel algumas outras VPN.

- Origem de dados está no local.

![BI iaas cenários vm e em origens de dados local](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Relatório de configuração dos serviços de modo nativo

A imagem da Galeria de máquina virtual para SQL Server inclui modo Reporting Services nativo instalado, no entanto, o servidor de relatórios não está configurado. Os passos nesta secção Configurar o servidor de relatório do Reporting Services. Para obter informações mais detalhadas sobre como configurar o modo Reporting Services nativo, consulte o artigo [Instalar o Reporting Services nativo modo relatório Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

>[AZURE.NOTE] Para obter conteúdo semelhante que utiliza scripts do Windows PowerShell para configurar o servidor de relatórios, consulte o artigo [Utilizar o PowerShell para criar um Azure VM com um nativo modo de servidor de relatórios](virtual-machines-windows-classic-ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Ligar a Máquina Virtual e iniciar o Gestor de configuração de serviços de elaboração de relatórios

Existem duas fluxos de trabalho comuns para ligar para uma máquina de Virtual do Azure:

- Para ligar, clique no nome da máquina virtual e, em seguida, clique em **Ligar**. Uma ligação de ambiente de trabalho remoto abre e o nome do computador é preenchido automaticamente.

    ![ligar a máquina virtual azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)

- Ligar a máquina virtual com a ligação de ambiente de trabalho remoto do Windows. Na interface de utilizador do ambiente de trabalho remoto:

    1. Escreva o **nome do serviço na nuvem** como o nome do computador.

    1. Escreva dois pontos (:) e o número da porta público que esteja configurado para o TCP ambiente de trabalho ponto final remoto.

        Myservice.cloudapp.NET:63133

        Para obter mais informações, consulte o artigo [o que é um serviço na nuvem?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).

**Inicie o Gestor de configuração dos serviços de elaboração de relatórios.**

1. No **Windows Server 2012**:

1. No ecrã **Iniciar** , escreva **Reporting Services** para ver uma lista de aplicações.

1. **Gestor de configuração dos serviços de elaboração de relatórios** com o botão direito e clique em **Executar como administrador**.

1. No **Windows Server 2008 R2**:

1. Clique em **Iniciar**e, em seguida, clique em **Todos os programas**.

1. Clique em **Microsoft SQL Server 2016**.

1. Clique em **Ferramentas de configuração**.

1. **Gestor de configuração dos serviços de elaboração de relatórios** com o botão direito e clique em **Executar como administrador**.

Ou

1. Clique em **Iniciar**.

1. Na caixa de diálogo **Procurar programas e ficheiros** , escreva **o reporting services**. Se a VM estiver a executar o Windows Server 2012, escreva **o reporting services** , no ecrã Iniciar do Windows Server 2012.

1. **Gestor de configuração dos serviços de elaboração de relatórios** com o botão direito e clique em **Executar como administrador**.

    ![Procure o Gestor de configuração do ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurar o Reporting Services

**Conta de serviço e o URL do serviço web:**

1. Verifique se que o **Nome do servidor** é o nome de servidor local e clique em **Ligar**.

1. Tenha em atenção o **Nome do relatório de servidor de base de dados**em branco. A base de dados é criada quando concluir a configuração.

1. Verifique se que o **Relatório de estado do servidor** é **iniciado**. Se pretender verificar o serviço no Gestor de servidor do Windows, o serviço é o serviço do **SQL Server Reporting Services** Windows.

1. Clique em **Conta de serviço** e alterar a conta, conforme necessário. Se a máquina virtual for utilizada num ambiente no domínio que não sejam associada, a conta **ReportServer** incorporada é suficiente. Para mais informações sobre a conta de serviço, consulte o artigo [Conta de serviço](https://msdn.microsoft.com/library/ms189964.aspx).

1. No painel esquerdo, clique em **URL do serviço Web** .

1. Clique em **Aplicar** para configurar os valores predefinidos.

1. Tenha em atenção os **URLs do serviço relatório servidor Web**. Tenha em atenção a porta TCP predefinida é 80 e faz parte do URL. Um passo posterior, pode criar um ponto Microsoft Azure Máquina Virtual final da porta.

1. No painel de **resultados** , verifique se as ações que foi concluídas com êxito.

**Base de dados:**

1. No painel esquerdo, clique em **base de dados** .

1. Clique em **Alterar base de dados**.

1. Verifique se **que criar uma nova base de dados do relatório server** está selecionada e, em seguida, clique em seguinte.

1. Verifique se o **Nome de servidor** e clique em **Testar ligação**.

1. Se o resultado é **Testar ligação foi concluída com êxito**, clique em **OK** e, em seguida, clique em **seguinte**.

1. Tenha em atenção o nome da base de dados é **ReportServer** e o **modo de servidor de relatórios** é **nativo** , em seguida, clique em **seguinte**.

1. Clique em **seguinte** na página **credenciais** .

1. Clique em **seguinte** na página de **Resumo** .

1. Clique em **seguinte** na página de **progresso e de conclusão** .

**Web Portal, ou o URL Gestor de relatórios para 2012 e 2014:**

1. Clique em **URL do Portal Web**ou do **URL do Gestor de relatórios** para 2014 e 2012, no painel esquerdo.

1. Clique em **Aplicar**.

1. No painel de **resultados** , verifique se as ações que foi concluídas com êxito.

1. Clique em **Sair**.

Para obter informações sobre permissões de servidor de relatórios, consulte o artigo [Conceder permissões num servidor relatório modo nativo](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Navegue para o Gestor de relatórios local

Para verificar a configuração, navegue para o Gestor de relatórios na VM.

1. No VM, inicie o Internet Explorer com privilégios de administrador.

1. Navegue até à http://localhost/reports na VM.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Para ligar ao portal de web remoto ou Gestor de relatórios do 2014 e 2012

Se pretender ligar para o portal da web ou Gestor de relatórios para 2014 e 2012, na máquina virtual a partir de um computador remoto, crie uma nova máquina virtual ponto final do TCP. Por predefinição, o servidor de relatórios aguarda para pedidos HTTP na **porta 80**. Se configurar os URLs de servidor de relatório para utilizar uma porta diferente, tem de especificar esse número de porta nas instruções seguintes.

1. Crie um ponto final para a Máquina Virtual da porta TCP 80. Para obter mais informações, consulte a secção [pontos finais de Máquina Virtual e as portas da Firewall](#virtual-machine-endpoints-and-firewall-ports) neste documento.

1. Abra a porta 80 na firewall a máquina virtual.

1. Navegue para o portal da web ou Gestor de relatórios, utilizando a Azure Virtual Machine **DNS nome** como o nome do servidor no URL. Por exemplo:

    **Servidor de relatórios**: http://uebi.cloudapp.net/reportserver  **Web portal**: http://uebi.cloudapp.net/reports

    [Configurar uma Firewall para acesso ao servidor de relatório](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar relatórios à máquina Virtual Azure

A tabela seguinte resume algumas das opções da publicar relatórios existentes a partir de um computador no local ao servidor de relatórios alojado do Microsoft Azure Virtual Machine:

- **Report Builder**: A máquina virtual inclui o clique-uma vez a versão do Microsoft SQL Server Report Builder para SQL 2014 e 2012. Para começar a comunicar o tempo construtor a primeira na máquina virtual com 2016 de SQL:

    1. Inicie o seu browser com privilégios administrativos.

    1. Navegue para o portal de web, na máquina virtual e selecione o ícone **Transferir** no canto superior direito.
    
    1. Selecione **Report Builder**.

    Para obter mais informações, consulte [Iniciar o Report Builder](https://msdn.microsoft.com/library/ms159221.aspx).

- **Ferramentas de dados do SQL Server**: VM: ferramentas de dados do SQL Server está instalada no computador virtual e podem ser utilizadas para criar o **Relatório projectos de servidor** e relatórios na máquina virtual. Ferramentas de dados do SQL Server pode publicar os relatórios de servidor de relatórios na máquina virtual.

- **Ferramentas de dados do SQL Server: remoto**: no seu computador local, criar um projeto do Reporting Services nas ferramentas de dados do SQL Server que contém os relatórios do Reporting Services. Configure o project para ligar para o URL do serviço web.

    ![Propriedades do projeto SSDT para o SSRS project](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)

- Criar uma. VHD unidade de disco rígido que contenha relatórios e, em seguida, carregar e anexar a unidade.

    1. Criar uma. Unidade de disco rígido VHD no seu computador local que contenha os seus relatórios.

    1. Crie e instale um certificado de gestão.

    1. Carregue o ficheiro VHD para Azure utilizando o cmdlet adicionar AzureVHD [criar e carregar um VHD de servidor do Windows para Azure](virtual-machines-windows-classic-createupload-vhd.md).

    1. Anexe o disco à máquina virtual.

## <a name="install-other-sql-server-services-and-features"></a>Instalar outras funcionalidades e serviços do SQL Server

Para instalar o SQL Server adicionais serviços, como o Analysis Services no modo de tabela, execute o Assistente de configuração do SQL server. Os ficheiros de configuração são no disco local a máquina virtual.

1. Clique em **Iniciar** e, em seguida, clique em **Todos os programas**.

1. Clique em **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** ou **Microsoft SQL Server 2012** e, em seguida, clique em **Ferramentas de configuração**.

1. Clique em **Centro de instalação do SQL Server**.

Ou executar C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe ou C:\SQLServer_11.0_full\setup.exe

>[AZURE.NOTE] A primeira vez que executa a configuração do SQL Server, ficheiros de configuração mais podem ser transferidos e requerem um reinício da máquina virtual e um reinício da configuração do SQL Server.
>
>Se precisar de repetidamente Personalize a imagem selecionada a partir do Microsoft Azure Virtual Machine, considere criar a sua própria imagem do SQL Server. Funcionalidade do Analysis Services SysPrep foi ativada com SQL Server 2012 SP1 CU2. Para mais informações, consulte o artigo [Considerações para instalar o SQL Server utilizando SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) e [Suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="to-install-analysis-services-tabular-mode"></a>Para instalar o modo Tabular do Analysis Services

Os passos nesta secção **Resumir** a instalação do modo em tabela do Analysis Services. Para mais informações, consulte o seguinte:

- [Instalar o Analysis Services no modo de tabela](https://msdn.microsoft.com/library/hh231722.aspx)

- [Modelação tabular (da Adventure Works Tutorial)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Para instalar o modo Tabular do Analysis Services:**

1. No Assistente de instalação do SQL Server, clique em **instalação** no painel esquerdo e, em seguida, clique em **instalação autónoma do novo SQL server ou adicionar funcionalidades a uma instalação existente**.

    - Se vir a **Procurar pasta**, procure c:\SQLServer_13.0_full, c:\SQLServer_12.0_full ou c:\SQLServer_11.0_full e, em seguida, clique em **Ok**.

1. Clique em **seguinte** na página de atualizações de produto.

1. Na página **Tipo de instalação** , selecione **executar uma nova instalação do SQL Server** e clique em **seguinte**.

1. Na página da **Função de configuração** , clique em **Funcionalidades de instalação do SQL Server**.

1. Na página de **Seleção de funcionalidades** , clique em **Do Analysis Services**.

1. Na página **Configuração da instância** , escreva um nome descritivo, tal como **tabela** nas caixas de texto **Instância com o nome** e o **Id da instância** .

1. Na página **Configuração dos serviços de análise** , selecione o **Modo de tabela**. Adicione o utilizador atual para a lista de permissões administrativas.

1. Concluir e feche o Assistente de instalação do SQL Server.

## <a name="analysis-services-configuration"></a>Configuração dos serviços de análise

### <a name="remote-access-to-analysis-services-server"></a>Acesso remoto ao servidor do Analysis Services

Servidor do Analysis Services suporta apenas a autenticação do windows. Para aceder a Analysis Services remotamente a partir de aplicações de cliente como SQL Server Management Studio ou ferramentas de dados do SQL Server, a máquina virtual tem de ser associadas ao seu domínio local, utilizar a rede Virtual do Azure. Para obter mais informações, consulte [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md).

Recebe uma **instância predefinida** do Analysis Services no TCP porta **2383**. Abra a porta na firewall máquinas virtuais. Também recebe uma agrupadas com nome instância do Analysis Services na porta **2383**.

Para um **denominada instância** do Analysis Services, o serviço do SQL Server Browser é necessário para gerir o acesso à porta. A configuração do SQL Server Browser predefinido é de porta **2382**.

Na firewall máquinas virtuais, abra a porta **2382** e crie um estático Analysis Services com o nome porta instância.

1. Para verificar as portas que já estejam a ser utilizada na VM e o processo que estiver a utilizar as portas, execute o seguinte comando com privilégios administrativos:

        netstat /ao

1. Utilizar o SQL Server Management Studio para criar um estático Analysis Services com o nome porta instância ao atualizar 'Porta' valor na tabela como propriedades gerais da instância. Para obter mais informações, consulte o artigo o "utilizar uma porta fixa para uma predefinida ou com o nome instância" em [Configurar a Firewall do Windows para permitir acesso do Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).

1. Reinicie a instância tabular do serviço do Analysis Services.

Para obter mais informações, consulte a secção **pontos finais de Máquina Virtual e as portas da Firewall** neste documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Os pontos finais de máquina virtual e as portas da Firewall

Esta secção resume os pontos finais de Máquina Virtual do Microsoft Azure para criar e portas para abrir as firewalls máquina virtual. A tabela seguinte resume as portas **TCP** para criar os pontos finais para e as portas para abrir a firewall máquinas virtuais.

- Se estiver a utilizar uma única VM e os dois itens seguintes forem verdadeiros, não é necessário criar pontos finais de VM e não é necessário abrir as portas na firewall na VM.

    - Não remotamente ligue às funcionalidades do SQL Server na VM. Estabelecer uma ligação de ambiente de trabalho remota para a VM e aceder às funcionalidades do SQL Server localmente na VM não são considerada uma ligação remota para as funcionalidades do SQL Server.

    - Não participar a VM para um domínio no local através de rede Virtual do Azure ou de outra solução de túnel VPN.

- Se a máquina virtual não está associada a um domínio, mas pretende remotamente ligar das funcionalidades do SQL Server no VM:

    - Abra as portas na firewall na VM.

    - Crie os pontos finais de máquina virtual para as portas anotados (*).

- Se a máquina virtual está associada a um domínio utilizando um túnel VPN como Azure Virtual funcionamento em rede, em seguida, os pontos finais não são necessários. No entanto, abra as portas na firewall na VM.

  	|Porta|Tipo|Descrição|
|---|---|---|
|**80**|TCP|Servidor de relatórios de acesso remoto (*).|
|**1433**|TCP|SQL Server Management Studio (*).|
|**1434**|UDP RECOMENDADAS|Browser do SQL Server. Isto é necessário quando VM no associado a um domínio.|
|**2382**|TCP|Browser do SQL Server.|
|**2383**|TCP|Instância do SQL Server Analysis Services predefinida e agrupadas instâncias com nome.|
|**Definidas pelo utilizador**|TCP|Crie um estático Analysis Services com o nome porta instância de um número de porta que escolher e, em seguida, desbloquear o número de porta a firewall.|

Para obter mais informações sobre como criar os pontos finais, consulte o seguinte:

- Criar os pontos finais:[como configurar os pontos finais para uma Máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md).

- SQL Server: Consulte a secção "Concluir a configuração os passos para ligar à máquina virtual utilizando SQL Server Management Studio" [aprovisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

O diagrama seguinte ilustra as portas para abrir a firewall VM para permitir o acesso remoto para funcionalidades e os componentes na VM.

![portas abrir para aplicações de bi no Azure VMs](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Recursos

- Reveja a política de suporte para o software de servidor do Microsoft utilizado no ambiente do Azure Virtual Machine. O tópico seguinte resume o suporte para funcionalidades como BitLocker, activação pós-falha clusters e balanceamento de carga. [Suporte de software de servidor da Microsoft para máquinas virtuais do Azure](http://support.microsoft.com/kb/2721672).

- [SQL Server em máquinas virtuais Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md)

- [Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

- [Aprovisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md)

- [Como anexar um disco de dados a uma Máquina Virtual](virtual-machines-windows-classic-attach-disk.md)

- [Migrar uma base de dados para o SQL Server numa VM Azure](virtual-machines-windows-migrate-sql.md)

- [Determinar o modo de servidor de uma instância do Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)

- [Modelação multidimensional (da Adventure Works Tutorial)](https://technet.microsoft.com/library/ms170208.aspx)

- [Centro de documentação do Azure](https://azure.microsoft.com/documentation/)

- [Utilizar o Power BI num ambiente híbrido](https://msdn.microsoft.com/library/dn798994.aspx)

>[AZURE.NOTE] [Submeter comentários e informações de contacto através do Microsoft SQL Server ligar](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Conteúdo da Comunidade

- [Gestão de base de dados do Azure SQL com PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)
