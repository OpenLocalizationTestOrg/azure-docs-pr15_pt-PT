<properties
   pageTitle="Adicionar Automatização Azure runbooks para os planos de recuperação | Microsoft Azure"
   description="Este artigo descreve como recuperação de sites do Azure agora permite-lhe expandir os planos de recuperação utilizando a automatização de Azure para concluir tarefas complexas durante a recuperação do Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans---classic"></a>Adicionar Automatização Azure runbooks para os planos de recuperação - clássico


Neste tutorial descreve como recuperação de sites do Azure integra-se com a automatização Azure para fornecer expansão para os planos de recuperação. Planos de recuperação podem orquestrar recuperação do seu máquinas virtuais protegidos utilizando a recuperação de sites do Azure para replicação nuvem secundário e a replicação para cenários Azure. Também ajudar na efetuar a recuperação **precisão de forma consistente**, **possa ser repetida**e **automatizada**. Se estão a falhar ao longo do seu máquinas virtuais para Azure, integração com o Azure automatização expande os planos de recuperação e fornece-lhe capacidade de executar runbooks, permitindo assim que as tarefas de automatização eficientes.

Se tiver não recebeu a informação sobre Azure automatização ainda, inscrever-se [aqui](https://azure.microsoft.com/services/automation/) e transferir os respetivos scripts de exemplo [aqui](https://azure.microsoft.com/documentation/scripts/). Leia mais sobre a [Recuperação de Site do Azure](https://azure.microsoft.com/services/site-recovery/) e como orquestrar recuperação para Azure utilizando os planos de recuperação [aqui](https://azure.microsoft.com/blog/?p=166264).

Neste tutorial breve, vamos abordar como pode integrar o Azure automatização runbooks para os planos de recuperação. Iremos automatizar tarefas simples que anteriormente necessário intervenção manual e veja como converter uma recuperação do passo multi uma acção de recuperação de clique único. Vamos abordar como pode resolver problemas de um script simples se errado-lo também.

## <a name="protect-the-application-to-azure"></a>Proteger a aplicação para Azure

Diga-na começar com uma aplicação simple que consiste duas máquinas virtuais. Aqui, temos uma aplicação de HRweb do Fabrikam. Fabrikam-HRweb-frontend e Fabrikam-Hrweb-back-end são as duas máquinas virtuais protegidas de forma a Azure utilizando Azure a recuperação do Site. Para proteger as máquinas virtuais utilizando Azure a recuperação do Site, siga os passos abaixo.

1.  Active a protecção para máquinas virtuais.

2.  Certifique-se de que as máquinas virtuais tiver concluído a replicação inicial e são replicação.

3.  Aguarde até que a replicação inicial for concluído e o estado de replicação diz protegido.

![](media/site-recovery-runbook-automation/01.png)
---------------------

Neste tutorial, irá criar um plano de recuperação para a aplicação Fabrikam HRweb activação pós-falha a aplicação para Azure. Em seguida, podemos a irá integrar com um livro de execuções irá criar um ponto final na falha ao longo do Azure máquina virtual para servir páginas web na porta 80.

Primeiro, vamos criar um plano de recuperação nossa aplicação.

## <a name="create-the-recovery-plan"></a>Criar o plano de recuperação

Para recuperar a aplicação para Azure, é necessário criar um plano de recuperação.
Utilizar um plano de recuperação que pode especificar a ordem de recuperação das máquinas virtuais. A máquina virtual colocada no grupo 1 irá recuperar e comece a primeira e, em seguida, vai ter de seguir a máquina virtual no grupo 2.

Crie um plano de recuperação com um aspeto semelhante abaixo.

![](media/site-recovery-runbook-automation/12.png)

Para ler mais sobre os planos de recuperação, leia documentação [aqui](https://msdn.microsoft.com/library/azure/dn788799.aspx "aqui").

Em seguida, vamos criar erros necessários no Azure automatização.

## <a name="create-the-automation-account-and-its-assets"></a>Criar a conta de automatização e os seus activos

Precisa de uma conta de automatização do Azure para criar runbooks. Se ainda não tiver uma conta, navegue para o separador de automatização do Azure representada pelo ![](media/site-recovery-runbook-automation/02.png)e criar uma nova conta.

1.  Dê um nome para identificar à conta.

2.  Especifique uma região geográfica onde pretende colocar a conta.

Recomenda-se para colocar a conta na mesma região como o Cofre de recuperação automática.

![](media/site-recovery-runbook-automation/03.png)

Em seguida, crie os ativos seguintes na conta.

### <a name="add-a-subscription-name-as-asset"></a>Adicionar um nome de subscrição como activo

1.  Adicionar uma nova definição ![](media/site-recovery-runbook-automation/04.png) de elementos de automatização do Azure e selecione para![](media/site-recovery-runbook-automation/05.png)

2.  Selecione o tipo de variável como **cadeia**

3.  Especifique o nome da variável como **AzureSubscriptionName**

    ![](media/site-recovery-runbook-automation/06.png)

4.  Especifique o nome real do Azure subscrição como o valor da variável.

    ![](media/site-recovery-runbook-automation/07_1.png)

É possível identificar o nome da sua subscrição a partir da página de definições da sua conta no portal do Azure.

### <a name="add-an-azure-login-credential-as-asset"></a>Adicionar uma credencial de início de sessão Azure como activo

Automatização Azure utiliza Azure PowerShell para ligar para a subscrição e opera sobre erros aí. Esta ação, terá autenticar com a sua conta Microsoft ou uma conta escolar ou profissional.
Pode armazenar as credenciais da conta num activo a ser utilizado em segurança ao livro de execuções.

1.  Adicionar uma nova definição ![](media/site-recovery-runbook-automation/04.png) de elementos de automatização do Azure e selecione![](media/site-recovery-runbook-automation/09.png)

2.  Selecione o tipo de credencial como **Credenciais do Windows PowerShell**

3.  Especifique o nome como **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Especifique o nome de utilizador e palavra-passe para iniciar sessão com.

Agora ambas as seguintes definições estão disponíveis no seus ativos.

![](media/site-recovery-runbook-automation/11.png)

Obter mais informações sobre como ligar à sua subscrição através do PowerShell são dado [aqui](../powershell-install-configure.md).

Em seguida, irá criar um livro de execuções no Azure automatização que pode adicionar um ponto final para a máquina virtual front-end após activação pós-falha.

## <a name="azure-automation-context"></a>Contexto de automatização Azure

Recuperação automática transmite uma variável de contexto para o livro de execuções para ajudar a escrever scripts determinista. Um poderia exigir que os nomes dos serviço em nuvem e a Máquina Virtual são previsíveis, mas não acontece que não é sempre as maiúsculas/minúsculas devido a determinados cenários como aquele onde o nome do nome do máquina virtual poderá ter sido alterado devido carateres não suportados no Azure. Por conseguinte, estas informações são transferidas para o plano de recuperação de recuperação automática como parte do *contexto*.

Segue-se um exemplo do aspeto da variável de contexto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


A tabela abaixo contém o nome e descrição para cada variável no contexto.

**Nome da variável** | **Descrição**
---|---
RecoveryPlanName | Nome do plano que está a executar. Ajuda-o a tomar medidas com base no nome com o mesmo script
FailoverType | Especifica se a activação pós-falha é testar, planeado ou não planeado.
FailoverDirection | Especifique se recuperação é para principal ou secundário
ID de grupo | Identificar o número de grupo dentro do plano de recuperação quando o plano está em execução
VmMap | Matriz de todas as máquinas virtuais no grupo
Chave VMMap | Chave exclusiva (GUID) para cada VM. É o mesmo que o ID de VMM da máquina virtual onde aplicável.
Nome da função | Nome da VM Azure que está a ser recuperada
CloudServiceName | Nome do serviço na nuvem Azure sob o qual a máquina virtual é criada.


Para identificar a chave de VmMap no contexto que poderia também ir para a página de propriedades VM no recuperação automática e observe a propriedade VM GUID.

![](media/site-recovery-runbook-automation/13.png)

## <a name="author-an-automation-runbook"></a>Autor de um livro de execuções de automatização

Agora, crie livro de execuções para abrir a porta 80 na máquina virtual front-end.

1.  Criar um novo livro de execuções na conta Azure automatização com o nome **OpenPort80**

    ![](media/site-recovery-runbook-automation/14.png)

2.  Navegue para a vista de autor do livro de execuções e introduza o modo de rascunho.

3.  Em primeiro lugar, especifique a variável para utilizar como o contexto de plano de recuperação

    ```
        param (
            [Object]$RecoveryPlanContext
        )

    ```

4.  Tornar a ligar para a subscrição utilizando o nome de credenciais e subscrição

    ```
        $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

        # Connect to Azure
        $AzureAccount = Add-AzureAccount -Credential $Cred
        $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
        Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    ```

    Note que utilizam os ativos Azure – **AzureCredential** e **AzureSubscriptionName** aqui.

5.  Agora pode especificar os detalhes de ponto final e o GUID da máquina virtual para o qual pretende expor o ponto final. Neste caso a máquina virtual front-end.

    ```
        # Specify the parameters to be used by the script
        $AEProtocol = "TCP"
        $AELocalPort = 80
        $AEPublicPort = 80
        $AEName = "Port 80 for HTTP"
        $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
    ```

    Especifica o protocolo de ponto final Azure, porta local na VM e respectiva mapeada Porta pública. Estas variáveis são parâmetros de uma ferramenta necessária aos comandos Azure que adicionar pontos finais ao VMs. O VMGUID detém o GUID da máquina virtual que precisa de ser executadas.

6.  O script agora irá extrair contexto para determinado GUID de VM e criar um ponto final na máquina virtual referenciada por-lo.

    ```
        #Read the VM GUID from the context
        $VM = $RecoveryPlanContext.VmMap.$VMGUID

        if ($VM -ne $null)
        {
            # Invoke pipeline commands within an InlineScript

            $EndpointStatus = InlineScript {
                # Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
                # Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

                $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                    Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                    Update-AzureVM
                Write-Output $Status
            }
        }
    ```

7. Uma vez concluída, esta visitas publicar ![](media/site-recovery-runbook-automation/20.png) para permitir que o script para estar disponível para a execução.

O script concluído é dado abaixo para referência

```
  workflow OpenPort80
  {
    param (
        [Object]$RecoveryPlanContext
    )

    $Cred = Get-AutomationPSCredential -Name 'AzureCredential'

    # Connect to Azure
    $AzureAccount = Add-AzureAccount -Credential $Cred
    $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

    # Specify the parameters to be used by the script
    $AEProtocol = "TCP"
    $AELocalPort = 80
    $AEPublicPort = 80
    $AEName = "Port 80 for HTTP"
    $VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

    #Read the VM GUID from the context
    $VM = $RecoveryPlanContext.VmMap.$VMGUID

    if ($VM -ne $null)
    {
        # Invoke pipeline commands within an InlineScript

        $EndpointStatus = InlineScript {
            # Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
            # This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

            $Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
                Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
                Update-AzureVM
            Write-Output $Status
        }
    }
  }
```

## <a name="add-the-script-to-the-recovery-plan"></a>Adicione o script para o plano de recuperação

Assim que o script estiver pronto, pode adicioná-la para o plano de recuperação que criou anteriormente.

1.  No plano de recuperação que criou, optar por adicionar um script após o grupo de 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Especifique um nome de script. Este é apenas um nome amigável para este script para a mostrar dentro do plano de recuperação.

3.  Na activação pós-falha ao Azure script – selecione o nome da conta de automatização do Azure.

4.  Na Azure Runbooks, selecione o livro de execuções que criou.

![](media/site-recovery-runbook-automation/16.png)

## <a name="primary-side-scripts"></a>Scripts primária do lado do

Quando está a executar uma activação pós-falha ao Azure, também pode escolher executar scripts do lado do principal. Estes scripts serão executado no servidor VMM durante activação pós-falha.
Scripts primária do lado do só estão disponíveis apenas para encerramento pré e publicam as fases encerramento. Isto acontece porque poderemos esperar o site principal para estar normalmente disponível quando uma falhas atinja.
Durante uma falha na ligação não planeada, apenas se optar por para operações de site principal, tentará executar os scripts lado primária. Se não estão acessíveis ou limite de tempo, a activação pós-falha continuará a recuperar as máquinas virtuais.
Scripts primária do lado do são não disponíveis para Sites de VMware/física/Hyper-v sem VMM protegido de forma a Azure - enquanto activação pós-falha ao Azure.
No entanto, quando lhe reposição de recurso a partir do Azure para no local, scripts lado primária (Runbooks) pode ser utilizada para todos os destinos exceto VMware.

## <a name="test-the-recovery-plan"></a>Testar o plano de recuperação

Assim que tiver adicionado o livro de execuções para o plano que pode iniciar um teste activação pós-falha e vê-la na ação. Recomenda-se sempre para executar um teste activação pós-falha para testar a sua aplicação e o plano de recuperação para se certificar de que não existem erros.

1.  Selecione o plano de recuperação e iniciar um teste activação pós-falha.

2.  Durante a execução de plano, pode ver se o livro de execuções tenha sido executado ou não através do respetivo estado.

    ![](media/site-recovery-runbook-automation/17.png)

3.  Também pode ver o estado de execução do livro de execuções detalhadas na página de tarefas de automatização do Azure do livro de execuções.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Após completa o activação pós-falha, para além do resultado da execução livro execuções, pode ver se a execução do é efetuada com êxito ou não visitar a página de máquina virtual Azure e consultar os pontos finais.

![](media/site-recovery-runbook-automation/19.png)

## <a name="sample-scripts"></a>Scripts de exemplo

Enquanto podemos efectuei através de automatizar uma frequentemente utilizados tarefa da ação de adicionar um ponto final para uma máquina virtual Azure neste tutorial, que pode fazer um número de outras tarefas de automatização poderosas utilizando a automatização Azure. Microsoft e a Comunidade de automatização do Azure fornecem runbooks de exemplo que podem ajudar a começar a criar o seu próprio soluções e utilitário runbooks, que pode utilizar como blocos modulares para tarefas de automatização maiores. Começar a utilizá-los a partir da Galeria e crie planos de poderosas recuperação de um clique para as suas aplicações utilizando Azure a recuperação do Site.

## <a name="additional-resources"></a>Recursos adicionais

[Descrição geral de automatização Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Descrição geral de automatização Azure")

[Scripts de exemplo do Azure automatização] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Scripts de exemplo do Azure automatização")
