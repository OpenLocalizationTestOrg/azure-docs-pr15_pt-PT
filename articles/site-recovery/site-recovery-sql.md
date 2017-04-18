<properties 
    pageTitle="Proteger o SQL Server com recuperação de falhas do SQL Server e recuperação de sites do Azure | Microsoft Azure" 
    description="Este artigo descreve como criar uma réplica do SQL Server utilizando as capacidades de falhas Azure Site recuperação do SQL Server." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.workload="backup-recovery" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2016" 
    ms.author="raynew"/>


# <a name="protect-sql-server-with-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteger o SQL Server com recuperação de falhas do SQL Server e recuperação de sites do Azure 


O serviço de recuperação de sites do Azure contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou para um centro de dados secundária no local. Para obter uma descrição geral rápida ler [o que é o Azure recuperação de Site?](site-recovery-overview.md).

 Este artigo descreve como pode proteger o SQL Server back-end de uma aplicação utilizando uma combinação de recuperação de Site do Azure e tecnologias do SQL Server BCDR. Que deve ter uma boa compreensão das capacidades de recuperação de falhas do SQL Server (activação pós-falha clusters, grupos de Disponibilidade AlwaysOn, da base de dados espelhando as, inicie sessão envio) e de recuperação de Site do Azure, antes de implementar os cenários descritos neste artigo.



## <a name="overview"></a>Descrição geral

Muitas das cargas de trabalho utilizam do SQL Server como um foundation. Aplicações como o SharePoint, Dynamics e SAP utilizam do SQL Server para implementar os serviços de dados.  Aplicações implementar o SQL Server num número de formas diferentes:

- **Autónomo SQL Server**: SQL Server e todas as bases de dados estão alojados num único computador (física ou um virtual). Quando foi virtualizado, anfitrião clusters é utilizado para local elevada disponibilidade. É implementada sem disponibilidade de alta nível de convidado.
- **SQL Server activação pós-falha clusters instâncias (sempre no FCI)**: dois ou mais nós de instâncias do servidor SQL com discos partilhados são configurados num cluster de activação pós-falha do Windows. Se qualquer uma das instâncias cluster para baixo, cluster pode falhar ao longo do SQL Server para outra instância. Esta configuração é normalmente utilizada para HA num site principal. -Não protege contra falha ou falha na camada de armazenamento partilhado. Disco partilhado pode ser implementado utilizando ISCSI, canal Fiber ou VHDx partilhados.
- **SQL sempre no grupos de disponibilidade**: nesta configuração, dois nós são o programa de configuração partilhada nada cluster com bases de dados do SQL Server configuradas num grupo de disponibilidade com replicação síncrona e activação pós-falha automática.

Em edições de empresa, SQL Server também fornece falhas nativa tecnologias de recuperação para recuperar bases de dados para um site remoto. Neste artigo, vamos tirar partido e integrar com estes nativas tecnologias de recuperação de falhas SQL: 

- SQL sempre no grupos de disponibilidade de recuperação de falhas para SQL Server 2012 ou as edições empresariais de 2014.
- SQL espelhando as bases de dados no modo de segurança alta do SQL Server Standard edition (qualquer versão), ou para SQL Server 2008 R2.


Recuperação de sites pode proteger do SQL Server, tal como é resumido na tabela.

 |**No local para o local** | **No local para Azure** 
---|---|---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim 
**Servidor físico** | Sim | Sim


## <a name="support-and-integration"></a>Suporte e integração

Estas versões do SQL Server são suportadas pelos cenários neste artigo:


- SQL Server 2014 Enterprise e padrão
- SQL Server 2012 Enterprise e padrão
- SQL Server 2008 R2 Enterprise e padrão


Recuperação de sites pode ser integrada com tecnologias de BCDR do SQL Server nativas resumidas na tabela abaixo para fornecer uma solução de recuperação de falhas.

**Funcionalidade** |**Detalhes** | **Versão do SQL Server** 
---|---|---
**Grupo de Disponibilidade AlwaysOn** | Executar várias instâncias de autónomo do SQL Server num cluster de activação pós-falha tem vários nós.<br/><br/>Bases de dados podem ser agrupadas activação pós-falha em grupos que podem ser copiados (espelhado) instâncias do SQL Server para que não é necessária nenhuma armazenamento partilhado.<br/><br/>Fornece recuperação de falhas entre um site principal e um ou mais sites secundários. Dois nós podem ser configurados numa partilhada nada cluster com bases de dados do SQL Server configurado num grupo de disponibilidade com replicação síncrona e activação pós-falha automática. | SQL Server 2014 & 2012 Enterprise edition
**Activação pós-falha clusters (AlwaysOn FCI)** | SQL Server tira partido de activação pós-falha Windows clusters para elevada disponibilidade das cargas de trabalho do SQL Server no local.<br/><br/>Nós executar instâncias do SQL Server com discos partilhados estão configurados num cluster de activação pós-falha. Se for uma instância para baixo cluster falha ao longo do outro.<br/><br/>Cluster não protege contra falha ou falhas de armazenamento partilhado. O disco partilhado pode ser implementado com iSCSI, canal fiber, ou partilhados VHDXs. | Edições do SQL Server Enterprise<br/><br/>SQL Server Standard edition (limitado para dois nós apenas)
**Base de dados espelhando as (modo de segurança alta)** | Protege a uma base de dados simples para uma única cópia secundária. Disponível em ambos os segurança alta (síncrono) e modos de replicação (assíncrona) de alto desempenho. Não necessita de um cluster de activação pós-falha. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise todas as edições
**Autónomo SQL Server** | O SQL Server e a base de dados estão alojados num único servidor (físico ou virtual). Anfitrião clusters é utilizado para elevada disponibilidade, se o servidor pertencer virtual. Sem disponibilidade de alta nível de convidado. | Enterprise ou Standard edition

## <a name="deployment-recommendations"></a>Recomendações de implementação


A tabela seguinte resume as nossas recomendações para integrar tecnologias do SQL Server BCDR com recuperação de sites.

**Versão** |**Edition** | **Implementação** | **No local no local** | **No local para Azure** 
---|---|---|---|---
SQL Server 2012 ou de 2014 | Enterprise | Instância de cluster de activação pós-falha | Grupos de Disponibilidade AlwaysOn | Grupos de Disponibilidade AlwaysOn
 | Enterprise | Grupos de Disponibilidade AlwaysOn para elevada disponibilidade | Grupos de Disponibilidade AlwaysOn | Grupos de Disponibilidade AlwaysOn
 | Padrão | Instância de cluster de activação pós-falha (FCI) | Replicação de recuperação de site com o espelho local | Replicação de recuperação de site com o espelho local
 | Enterprise ou padrão | Autónomo | Replicação de recuperação do site | Replicação de recuperação do site
SQL Server 2008 R2 | Enterprise ou padrão | Instância de cluster de activação pós-falha (FCI) | Replicação de recuperação de site com o espelho local | Replicação de recuperação de site com o espelho local
 | Enterprise ou padrão | Autónomo | Replicação de recuperação do site | Replicação de recuperação do site
SQL Server (qualquer versão) | Enterprise ou padrão | Ocorrência de cluster de activação pós-falha - aplicação DTC | Replicação de recuperação do site | Não é suportado


## <a name="deployment-prerequisites"></a>Pré-requisitos de implementação

Eis o que precisa antes de começar:

- Uma implementação do SQL Server no local a executar uma versão suportada do SQL Server. Normalmente, também terá de um Active Directory para o SQL server.
- As pré-requisitos para o cenário que pretende implementar. Pré-requisitos podem encontrar em cada artigo implementação. Ligações para estes são fornecidas num [Descrição geral de recuperação do Site](site-recovery-overview.md).
- Se quiser configurar uma recuperação no Azure, terá de executar a ferramenta de [Avaliação do Azure Máquina Virtual preparação](http://www.microsoft.com/download/details.aspx?id=40898) em máquinas virtuais do SQL Server para se certificar de que está a compatível com o Azure e recuperação de sites.


## <a name="set-up-active-directory"></a>Configurar o Active Directory

Irá precisar do Active Directory no site de recuperação secundário para SQL Server ser executado corretamente. Existem duas opções:

- **Pequena empresa**— se tiver um pequeno número de aplicações e um único controlador de domínio para o site no local e pretende falhar ao longo de todo o site, recomendamos que utilize repication de recuperação de sites para criar uma réplica o controlador de domínio para o Centro de dados secundário ou para Azure.

- **Médio para o enterprise grande**— se tiver um grande número de aplicação, estiver a executar uma floresta do Active Directory e pretende falhar início ao aplicação ou a carga de trabalho, recomendamos que configura o controlador de domínio adicional no Centro de dados secundário ou no Azure. Tenha em atenção que se estiver a utilizar grupos de Disponibilidade AlwaysOn para recuperar para um site remoto, recomendamos que configurar o outro controlador de domínio adicionais no site secundário ou Azure, para utilizar para a instância do SQL Server recuperada.

As instruções neste documento presumir que se encontram um controlador de domínio está disponível na localização secundária. [Leia mais](site-recovery-active-directory.md) sobre como proteger do Active Directory com recuperação de sites.

## <a name="integrate-protection-with-sql-server-always-on-on-premises-to-azure"></a>Integrar proteção com o SQL Server Always-On (no local para Azure)


Recuperação de sites vierem suporta AlwaysOn de SQL. Se criou um grupo de disponibilidade de SQL com uma máquina virtual Azure configurar como 'Secundário', em seguida, pode utilizar a recuperação de Site para gerir o activação pós-falha dos grupos de disponibilidade. 

>[AZURE.NOTE] Esta funcionalidade está atualmente na pré-visualização e disponível quando são geridos servidores de anfitrião de Hyper-V o Centro de dados principal no nuvens VMM e quando VMware configuração é gerida por um [Servidor de configuração](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Direita agora esta funcionalidade não está disponível no novo portal do Azure.

#### <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa de integrar o SQL AlwaysOn recuperação de sites:

- No local do SQL Server (servidor autónomo ou um cluster de activação pós-falha).
- Um ou mais Azure máquinas virtuais com o SQL Server instalado
- Um grupo de disponibilidade de SQL configurar entre no local do SQL Server e o SQL Server em execução no Azure
- PowerShell remoto deve ser ativado no computador do SQL Server no local. O servidor VMM ou o servidor de configuração deverá conseguir efetuar chamadas do PowerShell remotas para o SQL Server.
- Uma conta de utilizador deve ser adicionada o SQL Server no local, nestes grupos de utilizadores SQL com, pelo menos, estas permissões:
    - GRUPO de disponibilidade ALTER: permissões [aqui](https://msdn.microsoft.com/library/hh231018.aspx)e [aqui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - Base de dados do ALTER - permissões[aqui](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Uma conta de RunAs deve ser criada no servidor VMM ou uma conta deve ser criada no servidor de configuração utilizando o CSPSConfigtool.exe para o utilizador mencionado no passo anterior 
- O módulo SQL PS deve ser instalado no SQL servidores a executar no local e em máquinas virtuais Azure
- O agente VM devem ser instaladas máquinas virtuais em execução no Azure
- NTAUTHORITY\System deve ter seguir permissões no SQL Server em máquinas virtuais no Azure a executar o:
    - GRUPO de disponibilidade ALTER - permissões [aqui](https://msdn.microsoft.com/library/hh231018.aspx)e [aqui](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
    - Base de dados do ALTER - permissões [aqui](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  <a name="step-1-add-a-sql-server"></a>Passo 1: Adicionar um SQL Server


1. Clique em **Adicionar SQL** para adicionar um novo SQL Server. 

    ![Adicionar SQL](./media/site-recovery-sql/add-sql.png)

2. Em **Configurar definições de SQL** > **nome** fornecer um nome amigável para consultar o SQL Server.
3. **No SQL Server (FQDN)** especifique o FQDN da origem do SQL Server que pretende adicionar. Caso o SQL Server estiver instalado num Cluster de activação pós-falha, em seguida, forneça o FQDN do cluster e não de qualquer um de nós do cluster.  
4. Na **Instância do SQL Server** selecione a instância predefinida ou fornecer o nome da instância personalizado.
5. Em **Servidor de gestão de** selecionar um servidor VMM ou servidor de configuração de registado no cofre recuperação de sites. Recuperação de site utiliza este servidor de gestão para comunicar com o SQL Server.
6. Em **Executar como conta** fornece o nome de uma conta de RunAs que foi criada no servidor VMM especificado ou a conta que foi criada no servidor Configuraaaon. Esta conta é utilizada para aceder ao SQL Server e deve ter permissões de leitura e activação pós-falha em grupos de disponibilidade no computador do SQL Server.

    ![Adicionar a caixa de diálogo SQL](./media/site-recovery-sql/add-sql-dialog.png)

Depois de adicionar o SQL Server aparecerá no separador **Servidores SQL Server** . 

![Lista do SQL Server](./media/site-recovery-sql/sql-server-list.png)


#### <a name="step-2-add-a-sql-availability-group"></a>Passo 2: Adicionar um grupo de disponibilidade de SQL

1. Após o SQL Server máquina é adicionada que o passo seguinte é adicionar os grupos de disponibilidade a recuperação de sites. Para o fazer, desagregar dentro do SQL Server adicionou no passo anterior e clique em Adicionar grupo de disponibilidade de SQL. 

    ![Adicionar AG de SQL](./media/site-recovery-sql/add-sqlag.png)

2. Grupo de disponibilidade de SQL pode ser replicadas para um ou mais máquinas virtuais no Azure. Quando adicionar o grupo de disponibilidade de sql que é necessários para fornecer o nome e a subscrição da máquina virtual Azure onde pretende que o grupo de disponibilidade para ser Ocorreu uma falha ao longo por recuperação de sites.

    ![Caixa de diálogo SQL AG adicionar](./media/site-recovery-sql/add-sqlag-dialog.png)

3. No exemplo acima disponibilidade grupo DS1-AG ficaria primária numa máquina virtual SQLAGVM2 em execução no interior da subscrição DevTesting2 numa activação pós-falha. 

>[AZURE.NOTE] Apenas os grupos de disponibilidade que estão principal no SQL Server adicionou no passo acima estão disponíveis para ser adicionada a recuperação de sites. Se tiver criado uma disponibilidade grupo principal no SQL Server ou se tiver adicionado mais grupos de disponibilidade do SQL Server após foi adicionada, atualize-utilizar a opção de atualização disponível no SQL Server.

#### <a name="step-3-create-a-recovery-plan"></a>Passo 3: Criar um plano de recuperação

O passo seguinte é criar um plano de recuperação utilizando máquinas virtuais e os grupos de disponibilidade. Selecione o mesmo VMM servidor ou servidor de configuração que utilizou no passo 1 como origem e Microsoft Azure como destino.

![Criar plano de recuperação](./media/site-recovery-sql/create-rp1.png)

![Criar plano de recuperação](./media/site-recovery-sql/create-rp2.png)

No exemplo a aplicação do Sharepoint é composta por 3 máquinas virtuais que utilizar um grupo de disponibilidade de SQL como respetivo back-end. Este plano de recuperação que podemos poderia selecione ambas as o grupo de disponibilidade, assim máquina virtual que constituir a aplicação. 

Pode personalizar ainda mais o plano de recuperação movendo máquinas virtuais a grupos de activação pós-falha diferente para a ordem dos activação pós-falha de sequência. Grupo de disponibilidade é sempre Ocorreu uma falha ao longo do primeiro como seria utilizada como back-end de qualquer aplicação. 

![Personalizar o plano de recuperação](./media/site-recovery-sql/customize-rp.png)

### <a name="step-4--fail-over"></a>Passo 4: Falha ao longo

Opções de activação pós-falha diferentes estão disponíveis quando um grupo de disponibilidade foi adicionado a um plano de recuperação.

Activação pós-falha | Detalhes
--- | ---
**Activação pós-falha planeada** | Activação pós-falha planeada implica um sem dados perda activação pós-falha. Para alcançar disponibilidade modo esse grupo de disponibilidade de SQL pela primeira vez está definida como síncrono e, em seguida, um activação pós-falha é acionada para tornar o grupo de disponibilidade principal para a máquina virtual fornecida ao adicionar o grupo de disponibilidade a recuperação de sites. Assim que a activação pós-falha estiver concluída, o modo de disponibilidade está definido para o mesmo valor, tal como estava antes da activação planeada pós-falha foi acionou.
**Não planeado activação pós-falha** | Não planeado activação pós-falha pode resultar na perda de dados. Enquanto acionar activação pós-falha não planeado o modo de disponibilidade do grupo de disponibilidade não é alterado e o it for feito principal para a máquina virtual fornecida ao adicionar o grupo de disponibilidade a recuperação de sites. Assim que não planeado activação pós-falha está concluído e o servidor no local com o SQL Server está disponível novamente, inverter replicação tem de ser acionou no grupo de disponibilidade. Note que esta ação não está disponível no plano de recuperação e pode ser reencaminhada no grupo de disponibilidade do SQL no separador servidores SQL Server
**Teste activação pós-falha** | Teste failover para o grupo de disponibilidade de SQL não é suportada. Se acionar Failover de teste de um plano de recuperação que contém o grupo de disponibilidade de SQL, activação pós-falha seria ignorado para disponibilidade de grupo.


Considere estas opções de activação pós-falha.

Opção | Detalhes
--- | ---
**Opção 1** | 1. Execute um teste activação pós-falha da aplicação e camadas de front-end.<br/><br/>2. a atualizar a camada de aplicação para aceder a cópia de réplica no modo só de leitura e executar um teste só de leitura da aplicação.
**Opção 2** | 1. a criar uma cópia da instância máquina virtual do réplica SQL Server (utilizando clonar VMM para o site para o site ou Azure cópia de segurança) e colocá-lo para cima numa rede de teste<br/><br/> 2. Execute o teste activação pós-falha utilizando o plano de recuperação.

Passo 5: Falhar novamente

Se quiser tornar a disponibilidade de grupo novamente principal no SQL Server no local, em seguida, pode fazê-lo ao acionar planeado activação pós-falha no plano de recuperação e escolher a direção do Microsoft Azure para servidor de VMM no local.

>[AZURE.NOTE] Depois de uma replicação inversa activação pós-falha não planeado tem de ser acionou no grupo de disponibilidade para retomar a replicação. Até que isto é feito o permanece replicação suspenso.



### <a name="protect-machines-without-a-vmm-server-or-a-configuration-server"></a>Proteger máquinas sem um servidor de VMM ou um servidor de configuração

Para os ambientes que não são geridos por um servidor de VMM ou um servidor de configuração, Azure automatização Runbooks podem ser utilizados para configurar um script activação pós-falha de grupos de disponibilidade de SQL. Seguem-se os passos para configurar que:

1.  Crie um ficheiro local para o script Falha ao longo de um grupo de disponibilidade. Este exemplo de script Especifica um caminho para o grupo de disponibilidade na réplica Azure e falha-lo ao longo da essa instância de réplica. Este script será executado no SQL Server máquina de virtual réplica passando é com a extensão de scripts personalizados.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.  Carregue o script para um blob numa conta de armazenamento Azure. Utilize este exemplo:

        $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.  Crie um livro de execuções do Azure automatização para invocar os scripts na máquina virtual réplica do SQL Server no Azure. Utilize este exemplo de script para fazer isto. [Saiba mais](site-recovery-runbook-automation.md) sobre como utilizar automatização runbooks no planos de recuperação. 

        workflow SQLAvailabilityGroupFailover
        {
            param (
                [Object]$RecoveryPlanContext
            )

            $Cred = Get-AutomationPSCredential -name 'AzureCredential'
    
            #Connect to Azure
            $AzureAccount = Add-AzureAccount -Credential $Cred
            $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
            Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
            InLineScript
            {
            #Update the script with name of your storage account, key and blob name
            $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
            $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
            Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
            if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                #Skipping TFO in this version.
                #We will update the script in a follow-up post with TFO support
                Write-output "tfo: Skipping SQL Failover";
                }
            else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.
       
                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
                Write-output "Completed AG Failover";

                }
        
            }
        }

4.  Quando cria um plano de recuperação para a aplicação de adicionar um passo de script "previamente Agrupar 1 arranque" invoca livro de execuções automatização Falha ao longo de grupos de disponibilidade.

## <a name="integrate-protection-with-sql-alwayson-on-premises-to-on-premises"></a>Integrar proteção SQL AlwaysOn (no local para no local)

Se estiver a utilizar o SQL Server grupos de disponibilidade para elevada disponibilidade, ou uma instância de cluster activação pós-falha, recomendamos que utilizar grupos de disponibilidade no site de recuperação. Note que estas orientações são para as aplicações que não utilizam o transações distribuídas.

1. [Configurar bases de dados](https://msdn.microsoft.com/library/hh213078.aspx) em grupos de disponibilidade.
2. Crie uma nova rede virtual num site secundário.
3. Configure um site para o site VPN entre a nova rede virtual e o site principal.
4. Criar uma máquina virtual no site de recuperação e instalar o SQL Server no mesmo.
5. Expandir os grupos de Disponibilidade AlwaysOn existentes para a nova máquina de virtual do SQL Server. Configure esta instância do SQL Server como uma cópia de réplica assíncrona.
6. Crie um ponto de grupo de disponibilidade escuta ou atualizar a escuta existente para incluir o comportamento assíncrono réplica virtual machine.
7. Certifique-se de que o farm de aplicação está configuração utilizando o serviço de escuta. Se for configuração através do nome do servidor de base de dados, actualize-o para utilizar o serviço de escuta, pelo que não necessita de reconfigurá-lo após a activação pós-falha.

Para aplicações que utilizam transações distribuídas podemos recomendação utilizar [Recuperação de sites com a replicação de SAN](site-recovery-vmm-san.md) ou [replicação de site para o site de servidor VMWare/física](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerações de plano de recuperação

1. Adicione este exemplo de script à biblioteca VMM nos sites principais e secundários.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Quando cria um plano de recuperação para a aplicação de adicionar um passo de script "previamente Agrupar 1 arranque" invoca o script Falha ao longo de grupos de disponibilidade.



## <a name="protect-a-standalone-sql-server"></a>Proteger um programa autónomo do SQL Server

Nesta configuração, recomendamos que utilize replicação de recuperação de sites para proteger a máquina do SQL Server. Os passos exatos depende se do SQL Server esteja configurado como uma máquina virtual ou servidor físico e se pretende criar uma réplica para Azure ou um secundário no local site. Obter instruções para todos os cenários de implementação na [Descrição geral de recuperação do Site](site-recovery-overview.md).


## <a name="protect-a-sql-server-cluster-standard-or-2008-r2"></a>Proteger um cluster de SQL Server (padrão ou 2008 R2)

Para um cluster com o SQL Server Standard edition ou o SQL Server 2008 R2, recomendamos que utilize replicação de recuperação de sites para proteger o SQL Server.

### <a name="on-premises-to-on-premises"></a>No local para o local

- Se a aplicação utiliza transações distribuídas Recomendamos que implementar [Recuperação de sites com a replicação de SAN](site-recovery-vmm-san.md) para um ambiente do Hyper-V e um [servidor de VMware/física para VMware](site-recovery-vmware-to-vmware.md) para ambiente de VMware.

- Para as aplicações que não sejam DTC, tirar partido a abordagem acima para recuperar o cluster como um servidor autónomo por tirar partido de uma segurança alta local DB simétricas.

### <a name="on-premises-to-azure"></a>No local para Azure

Recuperação de sites não suporta convidado cluster quando de replicadas para o Azure. SQL Server também não fornece uma solução de recuperação de falhas de custo de baixa Standard edition. Recomendamos que proteger o cluster de SQL Server no local para um programa autónomo do SQL Server e recuperá-la no Azure.


1. Configure uma instância do SQL Server autónomo adicionais no site no local.
2. Configure esta instância para servir como um espelho para as bases de dados que necessitam de proteção. Configure o espelhando as no modo de segurança alta.
3.  Configurar a recuperação de Site no site no local com base no ambiente de ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [servidor de VMware/física](site-recovery-vmware-to-azure-classic.md).
4.  Utilize a replicação de recuperação de sites para criar uma réplica a nova instância do SQL Server para Azure. É uma cópia de espelho segurança alta e por isso, será sincronizado com o cluster principal, mas vai ser replicada para Azure utilizando a replicação de recuperação de sites.

O gráfico seguinte ilustra esta configuração.

![Cluster padrão](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### <a name="failback-considerations"></a>Considerações sobre a reposição de recurso

Para clusters padrão de SQL, reposição de recurso depois de um activação não planeado pós-falha irá exigir uma cópia de segurança do SQL e restaurar da instância simétricas para original cluster e restabelecer a simétricas.

## <a name="next-steps"></a>Próximos passos
[Saiba mais](site-recovery-best-practices.md) sobre a preparar implementar recuperação de sites.










 