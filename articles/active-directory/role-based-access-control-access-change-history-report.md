<properties
    pageTitle="Criar um relatório do access alterar histórico | Microsoft Azure"
    description="Gere um relatório que lista todas as alterações no access para as suas subscrições Azure com o controlo de acesso baseado em funções nos últimos 90 dias."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="create-an-access-change-history-report"></a>Criar um relatório de histórico de alteração do access

Qualquer altura alguém concede ou revoga acesso dentro de subscrições, as alterações obtenham tem sessão iniciadas no Azure eventos. Pode criar relatórios de histórico de alteração do access para ver todas as alterações nos últimos 90 dias.

## <a name="create-a-report-with-azure-powershell"></a>Criar um relatório com PowerShell do Azure
Para criar um relatório de histórico de alteração do access no PowerShell, utilize o `Get-AzureRMAuthorizationChangeLog` comando. Obter mais detalhes sobre este cmdlet estão disponíveis na [Galeria de PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Ao ligar para este comando, pode especificar que propriedade das atribuições que pretende que seja listado, incluindo o seguinte:

| Propriedade | Descrição |
| -------- | ----------- |
| **Ação** | Se o access foi concedida ou revogado |
| **Autor da chamada** | O proprietário do responsável para que a alteração do access |
| **Data** | A data e hora em que o access foi alterado |
| **Nome_do_directório** | O Azure Active directory |
| **PrincipalName** | O nome do utilizador, grupo ou aplicação |
| **PrincipalType** | Se a atribuição foi para um utilizador, grupo ou aplicação |
| **RoleId** | GUID da função que foi concedida ou revogado |
| **Nome da função** | A função que foi concedida ou revogada |
| **NomeÂmbito** | O nome da subscrição, grupo de recursos ou recurso |
| **ScopeType** | Se a atribuição foi na subscrição, no grupo de recursos ou no âmbito de recursos |
| **SubscriptionId** | GUID da subscrição do Azure |
| **SubscriptionName** | O nome da subscrição do Azure |

Este comando de exemplo lista todas as alterações ao acesso a subscrição para os últimos sete dias:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - captura de ecrã](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Criar um relatório com clip do Azure
Para criar um relatório de histórico de alteração do access na interface de linha de comandos do Azure (CLI), utilize o `azure role assignment changelog list` comando.

## <a name="export-to-a-spreadsheet"></a>Exportar para uma folha de cálculo
Para guardar o relatório ou manipular os dados, exporte as alterações do access para um ficheiro. csv. Em seguida, pode ver o relatório numa folha de cálculo para revisão.

![Changelog visualizada como uma folha de cálculo - captura de ecrã](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="see-also"></a>Consulte também
- Introdução ao [Controlo de acesso Azure Role-Based](role-based-access-control-configure.md)
- Trabalhar com [funções de personalizada no Azure RBAC](role-based-access-control-custom-roles.md)
