<properties 
    pageTitle="Bloquear recursos com o Gestor de recursos | Microsoft Azure" 
    description="Impedir que os utilizadores atualizar ou eliminar determinados recursos ao aplicar uma restrição a todos os utilizadores e funções." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tomfitz"/>

# <a name="lock-resources-with-azure-resource-manager"></a>Bloquear recursos com o Gestor de recursos do Azure

Como administrador, poderá ter de bloquear uma subscrição, grupo de recursos ou recurso para impedir que outros utilizadores na sua organização a partir de eliminar ou modificar recursos críticos acidentalmente. Pode definir o nível de bloqueio para **CanNotDelete** ou **só de leitura**. 

- **CanNotDelete** significa que os utilizadores autorizados ainda podem ler e modificar um recurso, mas não é possível eliminá-la. 
- **Só de leitura** significa que os utilizadores autorizados podem ler a partir de um recurso, mas não podem eliminá-la ou execute as ações no mesmo. A permissão no recurso é restrita à função de **leitor** . 

Aplicar **só de leitura** pode conduzir a resultados inesperados porque algumas operações que pareçam ler operações requerem realmente ações adicionais. Por exemplo, colocar um cadeado **só de leitura** numa conta de armazenamento impede que todos os utilizadores listar as teclas. Lista de operação de teclas é processada através de um pedido de mensagem, uma vez que as teclas devolvidas estão disponíveis para operações de escrita. Outro exemplo, colocar um cadeado **só de leitura** num recurso de aplicação de serviço impede que Visual Studio Server Explorer visualização de ficheiros para o recurso porque essa interação requer o acesso de escrita.

Ao contrário de controlo de acesso baseado em funções, utilize bloqueios de gestão para aplicar uma restrição ao longo de todos os utilizadores e funções. Para obter informações sobre como definir permissões para utilizadores e funções, consulte o artigo [O controlo de acesso baseado em funções Azure](./active-directory/role-based-access-control-configure.md).

Quando aplica um cadeado num âmbito de elemento principal, todos os recursos de subordinados herdam o bloqueio mesmo. Pares recursos que adicione posteriormente herdam o bloqueio do principal. O bloqueio mais restritivo na herança terá precedência.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quem pode criar ou eliminar bloqueios na sua organização

Para criar ou eliminar bloqueios de gestão, tem de ter acesso a **Microsoft.Authorization/\* ** ou **Microsoft.Authorization/locks/\* ** ações. Das funções incorporadas, apenas o **proprietário** e o **Administrador de acesso de utilizador** são concedidas essas ações.

## <a name="creating-a-lock-through-the-portal"></a>Criar um cadeado através do portal

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Criar um cadeado num modelo

O exemplo seguinte mostra um modelo que cria um cadeado numa conta de armazenamento. A conta de armazenamento no qual pretende aplicar o bloqueio é fornecida como um parâmetro. O nome do bloqueio é criado ao concatenar o nome do recurso com **/Microsoft.Authorization/** e o nome do bloqueio, neste **myLock**de maiúsculas/minúsculas.

O tipo de fornecido é específico ao tipo de recurso. Para armazenamento, defina o tipo da "Microsoft.Storage/storageaccounts/providers/locks".

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Criar um bloqueio com a REST API

Pode bloquear implementados recursos com a [API REST para bloqueios de gestão](https://msdn.microsoft.com/library/azure/mt204563.aspx). REST API permite-lhe criar e eliminar bloqueios e obter informações sobre bloqueios existentes.

Para criar um cadeado, execute:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

O âmbito pode ser uma subscrição, grupo de recursos ou recurso. O bloqueio-nome é o que pretende ligar o bloqueio. Para obter versão api, utilize **2015-01-01**.

No pedido de incluir um objeto JSON que especifica as propriedades para o bloqueio.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

Para obter exemplos, consulte o artigo [REST API para bloqueios de gestão](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## <a name="creating-a-lock-with-azure-powershell"></a>Criar um cadeado com PowerShell do Azure

Pode bloquear implementados recursos com PowerShell do Azure utilizando o **Novo AzureRmResourceLock** conforme mostrado no seguinte exemplo.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell fornece outros comandos para trabalhar bloqueios, tais como **Conjunto AzureRmResourceLock** para atualizar um cadeado e **AzureRmResourceLock remover** para eliminar um cadeado.

## <a name="next-steps"></a>Próximos passos

- Para mais informações sobre como trabalhar com bloqueios de recursos, consulte o artigo [Bloqueio para baixo o Azure de recursos](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- Para saber mais sobre como organizar logicamente os recursos, consulte o artigo [utilizar etiquetas para organizar os recursos](resource-group-using-tags.md)
- Para alterar o grupo de recursos um recurso encontra-se na, consulte o artigo [Mover recursos para o novo grupo de recursos](resource-group-move-resources.md)
- Pode aplicar restrições e convenções ao longo da sua subscrição com políticas personalizadas. Para mais informações, consulte o artigo [Utilizar a política para gerir os recursos e controlar o acesso](resource-manager-policy.md).
