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


# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Adicionar Automatização Azure runbooks para os planos de recuperação


Neste tutorial descreve como recuperação de sites do Azure integra-se com a automatização Azure para fornecer expansão para os planos de recuperação. Planos de recuperação podem orquestrar recuperação do seu máquinas virtuais protegidos utilizando a recuperação de sites do Azure para replicação nuvem secundário e a replicação para cenários Azure. Também ajudar na efetuar a recuperação **precisão de forma consistente**, **possa ser repetida**e **automatizada**. Se estão a falhar ao longo do seu máquinas virtuais para Azure, integração com o Azure automatização expande os planos de recuperação e fornece-lhe capacidade de executar runbooks, permitindo assim que as tarefas de automatização eficientes.

Se tiver não recebeu a informação sobre Azure automatização ainda, inscreverem [aqui](https://azure.microsoft.com/services/automation/) e transferir os respetivos scripts de exemplo [aqui](https://azure.microsoft.com/documentation/scripts/). Leia mais sobre a [Recuperação de Site do Azure](https://azure.microsoft.com/services/site-recovery/) e como orquestrar recuperação para Azure utilizando os planos de recuperação [aqui](https://azure.microsoft.com/blog/?p=166264).

Neste tutorial, vamos abordar como pode integrar o Azure automatização runbooks para os planos de recuperação. Iremos automatizar tarefas simples que anteriormente necessário intervenção manual e veja como converter uma recuperação do passo com várias uma acção de recuperação de clique único. Vamos abordar como pode resolver problemas de um script simples se errado-lo também.

## <a name="customize-the-recovery-plan"></a>Personalizar o plano de recuperação

1. Comece diga-por operning pá o recurso do plano de recuperação. Pode ver que o plano de recuperação tem duas máquinas virtuais adicionadas à mesma para a recuperação. 

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
---------------------

2. Clique no botão Personalizar para começar a adicionar um livro de execuções. Isto irá abrir o plano de recuperação personalizar pá.


    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


3. Clique com o botão direito do rato no grupo iniciar 1 e selecione para adicionar uma 'Adicionar mensagem ação'.

4. Selecione a selecionar um script na pá novo.

5. Nome o script 'Olá mundo'.

6. Selecione um nome de conta de automatização. Esta é a conta de automatização do Azure. Note que esta conta pode estar em qualquer geografia Azure, mas tem de estar na mesma subscrição do cofre recuperação de sites.

7. Selecione um livro de execuções da conta de automatização. Este é o script que será executada durante a execução do plano de recuperação após a recuperação ter do primeiro grupo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)


8. Clique em OK para guardar o script. Isto irá adicionar o script para o grupo de ação de mensagem de grupo 1: Iniciar.


    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Pontos importantes da ação de adicionar um script

1. Pode clique com o botão direito do rato sobre o script e optar por 'Eliminar passo' ou 'Actualizar script'.

2. Um script pode ser executados em Azure enquanto activação pós-falha nos locais para Azure e pode ser executados em Azure como um script do lado primária antes de encerramento, durante a reposição de recurso a partir do Azure para no local.

3. Quando um script é executado, irá inserção um contexto de plano de recuperação.

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
CloudServiceName (no modelo de implementação do Gestor de recursos) | Nome do grupo de recursos do Azure sob o qual a máquina virtual é criada.


## <a name="using-complex-variables-per-recovery-plan"></a>Utilizar variáveis complexo pelo plano de recuperação

Por vezes, um livro de execuções necessita de mais informações que apenas o RecoveryPlanContext. Não existe nenhuma mecanismos primeira classe para passar um parâmetro para um livro de execuções. No entanto, se pretender utilizar o mesmo script através de vários planos de recuperação que pode utilizar a variável de recuperação planear contexto 'RecoveryPlanName' e utilizar a seguir a experimental técnica para utilizar uma variável de complexo Azure automatização num livro de execuções. O exemplo abaixo mostra como pode criar três elementos de diferentes complexo variável e utilizá-los no livro de execuções com base no nome do plano de recuperação.

Considere a que pretende utilizar 3 parâmetros adicionais de um livro de execuções. Diga-nos codificá-los num formulário JSON {"Var1": "testautomation", "Var2": "Não planeado", "Var3": "PrimaryToSecondary"}

Utilize [AA complexo variável](../automation/automation-variables.md#variable-types Complex variable) para criar um novo activo de automatização.
Atribuir um nome a variável como <RecoveryPlanName>- parâmetros.
Pode utilizar a referência aqui para criar uma [variável de complexo](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Para os planos de recuperação diferente, a variável de como o nome

1. recoveryPlanName1 >-parâmetros
2. recoveryPlanName2 >-parâmetros
3. recoveryPlanName3 >-parâmetros

Agora, no script referir-se para parâmetros como

1. Obter o nome RP a partir do $rpname = $Recoveryplancontext variável
2. Obter activo de $paramValue = "$($rpname) parâmetros"
3. Utilize esta opção como uma variável de complexo para o plano de recuperação ao contactar o suporte Get-AzureAutomationVariable [-AutomationAccountName] <String> -nome $paramValue.

Por exemplo, para obter o parâmetro/complexo variável para o plano de recuperação SharepointApp, crie uma variável de complexo de automatização do Azure denominada 'SharepointApp parâmetros'.

Utilizá-la no plano de recuperação, subtraindo a variável do elemento utilizando a instrução Get-AzureAutomationVariable [-AutomationAccountName] <String> [-nome] $paramValue. [Isto de referência para obter mais detalhes](https://msdn.microsoft.com/library/dn913772.aspx)

Desta forma o mesmo script pode ser utilizada para o plano de recuperação diferente ao armazenar a variável de complexo específico de plano nos ativos.

## <a name="sample-scripts"></a>Scripts de exemplo

Para um repositório de scripts que pode importar diretamente para a sua conta de automatização, consulte o artigo [do Kristian Nese repositório OMS scripts](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

O script aqui é um modelo de Gestor de recursos do Azure que irá implementar todos os abaixo scripts

* NSG

Livro de execuções NSG serão atribuir endereços IP público para cada VM dentro do plano de recuperação e anexar seus adaptadores de rede virtual a um grupo de segurança de rede que irá permitir a comunicação predefinido

* PublicIP

Livro de execuções do endereço IP público irá atribuir endereços IP público para cada VM dentro do plano de recuperação. Acesso aos máquinas e aplicações irá variam consoante as definições da firewall dentro de cada convidado


* CustomScript

Livro de execuções CustomScript irá atribuir endereços IP público para cada VM dentro do plano de recuperação e instalar uma extensão de scripts personalizados que puxa o script que fizer referência ao durante a implementação do modelo

* NSGwithCustomScript

NSGwithCustomScript livro execuções irão atribuir endereços IP público para cada VM dentro de recuperação planear, instalar um personalizado utilizando extensão de script e ligue os adaptadores de rede virtual para permitir um NSG predefinido comunicação de entrada e saída de acesso remoto

## <a name="additional-resources"></a>Recursos adicionais

[Serviço de automatização Azure são executadas como conta](../automation/automation-sec-configure-azure-runas-account.md)

[Descrição geral de automatização Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Descrição geral de automatização Azure")

[Scripts de exemplo do Azure automatização] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Scripts de exemplo do Azure automatização")
