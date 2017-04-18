<properties 
    pageTitle="Iniciar e parar máquinas virtuais com o Azure automatização - fluxo de trabalho do PowerShell | Microsoft Azure"
    description="Versão gráfica do cenário de automatização do Azure, incluindo runbooks para iniciar e parar máquinas virtuais clássicas."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="bwren" />

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Cenário de automatização Azure - iniciar e parar máquinas virtuais

Este cenário de automatização do Azure inclui runbooks para iniciar e parar máquinas virtuais clássicas.  Pode utilizar este cenário para qualquer um dos seguintes procedimentos:  

- Utilize runbooks sem modificação no seu ambiente. 
- Modificar runbooks para efetuar uma funcionalidade personalizada.  
- Ligar a runbooks a partir de outro livro de execuções como parte de uma solução global. 
- Utilize o runbooks como tutoriais para saber o livro de execuções conceitos de criação. 

> [AZURE.SELECTOR]
- [Gráficos](automation-solution-startstopvm-graphical.md)
- [Fluxo de trabalho do PowerShell](automation-solution-startstopvm-psworkflow.md)

Esta é a versão de livro execuções do PowerShell fluxo de trabalho deste cenário. Também está disponível utilizando [runbooks gráfica](automation-solution-startstopvm-graphical.md).

## <a name="getting-the-scenario"></a>Obter o cenário

Este cenário consiste em duas runbooks PowerShell fluxo de trabalho que pode transferir das ligações seguintes.  Ver a [versão gráfica](automation-solution-startstopvm-graphical.md) deste cenário para ligações para os gráficos runbooks.

| Livro execuções | Ligação | Tipo | Descrição |
|:---|:---|:---|:---|
| Iniciar AzureVMs | [Iniciar o Azure VMs clássicos](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | Fluxo de trabalho do PowerShell | É iniciado todas as máquinas virtuais clássicas numa subscriptionor Azure todas as máquinas virtuais com um nome de serviço em particular. |
| Parar AzureVMs | [Parar o Azure VMs clássicos](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | Fluxo de trabalho do PowerShell | Deixa de todas as máquinas virtuais numa conta de automatização ou todas as máquinas virtuais com um nome de serviço em particular.  |


## <a name="installing-and-configuring-the-scenario"></a>Instalar e configurar o cenário

### <a name="1-install-the-runbooks"></a>1. a instalar o runbooks

Depois de transferir o runbooks, pode importá-los com o procedimento importar [um livro de execuções](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook).

### <a name="2-review-the-description-and-requirements"></a>2. rever os requisitos e descrição
Os runbooks incluir texto de ajuda remova que inclui uma descrição e elementos necessários.  Também pode obter as mesmas informações a partir deste artigo. 

### <a name="3-configure-assets"></a>3. Configurar elementos
Os runbooks necessitam os ativos seguintes que tem de criar e preencher com valores adequados.

| Tipo de elementos | Nome do activo | Descrição |
|:---|:---|:---|:---|
| Credenciais | AzureCredential | Contém as credenciais para uma conta que possua autoridade para iniciar e parar máquinas virtuais na subscrição Azure.  Em alternativa, pode especificar outro activo credencial no parâmetro **credencial** da atividade **AzureAccount adicionar** . |
| Variável | AzureSubscriptionId | Contém o ID da subscrição da sua subscrição do Azure. |

## <a name="using-the-scenario"></a>Utilizar o cenário

### <a name="parameters"></a>Parâmetros

Os runbooks tem os seguintes parâmetros.  Tem de fornecer valores para qualquer parâmetros obrigatórios e opcionalmente, pode fornecer valores para outros parâmetros dependendo das suas necessidades.

| Parâmetro | Tipo | Obrigatório | Descrição |
|:---|:---|:---|:---|
| ServiceName | cadeia | N | Se for fornecido um valor, em seguida, todas as máquinas virtuais com esse nome de serviço são iniciadas ou paradas.  Não se for fornecido nenhum valor, em seguida, todas as máquinas virtuais clássicas na subscrição do Azure são iniciadas ou paradas. |
| AzureSubscriptionIdAssetName | cadeia | N | Contém o nome do [recurso variável](#installing-and-configuring-the-scenario) que contém o ID da subscrição da sua subscrição do Azure.  Se não especificar um valor, é utilizado *AzureSubscriptionId* .  |
| AzureCredentialAssetName | cadeia | N | Contém o nome do [recurso de credenciais](#installing-and-configuring-the-scenario) que contém as credenciais para o livro de execuções utilizar.  Se não especificar um valor, é utilizado *AzureCredential* .  |

### <a name="starting-the-runbooks"></a>Iniciar o runbooks

Pode utilizar qualquer um dos métodos no [início de um livro de execuções no Azure automatização](automation-starting-a-runbook.md) para iniciar um da runbooks neste cenário.

Os comandos de exemplo seguinte utiliza o Windows PowerShell para executar **StartAzureVMs** para iniciar a todas as máquinas virtuais com o nome do serviço *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>Saída

Os runbooks irá [uma mensagem de saída](automation-runbook-output-and-messages.md) para cada máquina virtual que indica se ou não a instrução comece ou deixe de foram submetida com êxito.  Pode procurar uma cadeia específica na saída para determinar o resultado de cada livro de execuções.  As cadeias de saída possíveis estão listadas na tabela seguinte.

| Livro execuções | Condição | Mensagem |
|:---|:---|:---|
| Iniciar AzureVMs | Já está em execução máquina virtual  | MyVM já está em execução |
| Iniciar AzureVMs | Iniciar pedido para máquina virtual submetido com êxito | MyVM ter sido iniciado |
| Iniciar AzureVMs | Ocorreu uma falha de pedido de início para máquina virtual  | MyVM Ocorreu uma falha ao iniciar |
| Parar AzureVMs | Já está parada máquina virtual  | Já está parado MyVM |
| Parar AzureVMs | Parar de pedido de máquina virtual submetido com êxito | Deixou MyVM |
| Parar AzureVMs | Ocorreu uma falha de pedido de paragem para máquina virtual  | MyVM Ocorreu uma falha ao parar |

Por exemplo, o fragmento de código seguintes a partir de um livro de execuções tenta iniciar todas as máquinas virtuais com o nome do serviço *MyServiceName*.  Se qualquer um da falhas de pedidos de início, podem ser tomadas acções de erro. 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>Pormenor

Segue-se uma divisão detalhada de runbooks neste cenário.  Pode utilizar estas informações para personalizar a runbooks ou apenas para saber das mesmas para o seus próprio cenários de automatização de criação.

### <a name="parameters"></a>Parâmetros

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Inicia o fluxo de trabalho ao obter os valores para os [parâmetros de entrada](#using-the-scenario).  Se os nomes de elementos não são fornecidos são utilizados os nomes predefinidos.

### <a name="output"></a>Saída

    # Returns strings with status messages
    [OutputType([String])]

Esta linha declara que a saída do livro de execuções será uma cadeia.  Não é necessária, mas é aconselhável para quando o livro de execuções é utilizado como um [livro de execuções subordinado](automation-child-runbooks.md) para que um livro de execuções principal ficará a saber o tipo de saída esperar.

### <a name="authentication"></a>Autenticação

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

As seguintes linhas definir as [credenciais](automation-configuring.md#configuring-authentication-to-azure-resources) e subscrição Azure que será utilizada para o resto do livro de execuções.
Em primeiro lugar utilizamos **Get-AutomationPSCredential** para obter os elementos que detém as credenciais com acesso a iniciar e parar máquinas virtuais na subscrição do Azure. **Adicionar AzureAccount** utiliza, em seguida, este recurso para definir as credenciais.  O resultado é atribuído a uma variável fictícia para que este não está incluído nos resultados do livro execuções.  

Elemento variável com o ID da subscrição, em seguida, é obtido com **Get-AutomationVariable** e a subscrição configurou com **Selecione AzureSubscription**.

### <a name="get-vms"></a>Obter VMs

    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
    { 
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else 
    { 
        $VMs = Get-AzureVM
    }

**Get-AzureVM** é utilizada para obter as máquinas virtuais que livro de execuções funcionam com.  Se for fornecido um valor na variável de entrada **NomeServiço** , em seguida, são obtidas os apenas as máquinas virtuais com esse nome de serviço.  Se **ServiceName** estiver vazia, em seguida, são obtidas os todas as máquinas virtuais.

### <a name="startstop-virtual-machines-and-send-output"></a>Início/fim máquinas virtuais e enviar a saída

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

O próximo passo linhas através de cada máquina virtual.  Em primeiro lugar a **PowerState** da máquina virtual está selecionada para ver se já está em execução ou parado, dependendo do livro de execuções.  Se já estiver no estado de destino, uma mensagem é enviada para a saída e as extremidades de livro execuções.  Caso contrário, em seguida, **AzureVM iniciar** ou **Parar AzureVM** é utilizado para tentar iniciar ou parar a máquina virtual com o resultado do pedido armazenado a uma variável.  Em seguida, é enviada uma mensagem para exportar especificando se o pedido para iniciar ou parar foi submetido com êxito.


## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre como trabalhar com subordinado runbooks, consulte o artigo [subordinado runbooks na automatização do Azure](automation-child-runbooks.md) 
- Para saber mais acerca das mensagens de saída durante a execução de livro execuções e registo para o ajudar a resolver problemas, consulte o artigo [saída do livro execuções e mensagens de automatização do Azure](automation-runbook-output-and-messages.md)
