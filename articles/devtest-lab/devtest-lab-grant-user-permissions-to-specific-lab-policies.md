<properties
    pageTitle="Conceder permissões aos utilizadores para as políticas de laboratório específico | Microsoft Azure"
    description="Saiba como atribuir permissões de utilizador para as políticas de laboratório específicas em laboratório DevTest com base nas necessidades de cada utilizador"
    services="devtest-lab,virtual-machines,visual-studio-online"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

# <a name="grant-user-permissions-to-specific-lab-policies"></a>Conceder permissões aos utilizadores para as políticas de laboratório específico

## <a name="overview"></a>Descrição geral

Este artigo ilustra como utilizar o PowerShell para conceder permissões aos utilizadores a uma política de laboratório específico. Desta forma, as permissões podem ser aplicadas com base nas necessidades de cada utilizador. Por exemplo, poderá pretender conceder a capacidade de alterar as definições de política VM, mas não as políticas de custo de um utilizador específico.

## <a name="policies-as-resources"></a>Políticas, como recursos

Tal como é abordado no artigo [O controlo de acesso baseado em funções Azure](../active-directory/role-based-access-control-configure.md) , RBAC permite a gestão de acesso extensivamente de recursos para Azure. Utilizar RBAC, pode segregar direitos dentro da sua equipa DevOps e conceder apenas a quantidade de acesso aos utilizadores que precisam de efectuar os seus trabalhos.

Em laboratório de DevTest, uma política é um tipo de recurso que permite a ação de RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Cada política laboratório é um recurso no tipo de recurso de política e pode ser atribuída como um âmbito de uma função de RBAC.

Por exemplo, para poder de conceder aos utilizadores leitura/escritam permissão para a política de **Tamanhos de VM permitidos** , pretender criar uma função personalizada que funciona com o **Microsoft.DevTestLab/labs/policySets/policies/** * ação e, em seguida, atribuir aos utilizadores apropriados a esta função personalizada no âmbito da * *Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Para saber mais sobre as funções personalizadas no RBAC, consulte o artigo o [controlo de acesso de funções personalizadas](../active-directory/role-based-access-control-custom-roles.md).

##<a name="creating-a-lab-custom-role-using-powershell"></a>Criar uma função de personalizada laboratório através do PowerShell
Para poder começar a utilizar, terá de leia o seguinte artigo, irá explicam como instalar e configurar os cmdlets do Azure PowerShell: [https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre).

Assim que configurou os cmdlets do Azure PowerShell, pode efetuar as seguintes tarefas:

- Lista todas as operações/acções para um fornecedor de recursos
- Ações de lista numa determinada função:
- Crie uma função personalizada

O seguinte script do PowerShell ilustra exemplos de como efetuar as seguintes tarefas:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

##<a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Atribuir permissões a um utilizador para uma política específica utilizando funções personalizadas
Uma vez que definiu as funções personalizadas, pode atribui-los para os utilizadores. Para atribuir uma função personalizada a um utilizador, primeiro terá de obter o **ID de objecto** que representa esse utilizador. Para o fazer, utilize o cmdlet **Get-AzureRmADUser** .

No exemplo seguinte, o **ID de objecto** do utilizador *SomeUser* é 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Assim que tiver o **ID de objecto** para o utilizador e um nome de função personalizada, pode atribuir essa função ao utilizador com o cmdlet **AzureRmRoleAssignment novo** :

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/policies/AllowedVmSizesInLab

No exemplo anterior, a política de **AllowedVmSizesInLab** é utilizada. Pode utilizar qualquer um dos seguintes políticas:

- MaxVmsAllowedPerUser
- MaxVmsAllowedPerLab
- AllowedVmSizesInLab
- LabVmsShutdown

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximos passos

Uma vez-lhe atribuídas permissões de utilizador para as políticas de laboratório específica, eis alguns passos a ter em conta:

- [Proteger o acesso a um laboratório de](devtest-lab-add-devtest-user.md).

- [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).

- [Criar um modelo de laboratório](devtest-lab-create-template.md).

- [Criar artefactos personalizados para o seu VMs](devtest-lab-artifact-author.md).

- [Adicionar uma VM com artefactos para um laboratório de](devtest-lab-add-vm-with-artifacts.md).
