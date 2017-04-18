<properties
   pageTitle="Eliminar um cluster Azure e os seus recursos | Microsoft Azure"
   description="Saiba como o para eliminar completamente um ferro serviço cluster quer eliminar o grupo de recursos que contém o cluster ou eliminando seletivamente recursos."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Eliminar um cluster de serviço ferro no Azure e os recursos que utilize

Um cluster de serviço ferro é constituído muitos outros recursos Azure além o recurso de cluster propriamente dita. Por isso, para eliminar completamente um cluster de serviço ferro também tem de eliminar todos os recursos que é constituído.
Tem duas opções: quer eliminar o grupo de recursos se encontra o cluster (que elimina o recurso de cluster e quaisquer outros recursos no grupo de recursos) ou eliminar especificamente o recurso de cluster e tem associados recursos (mas não outros recursos no grupo de recursos).

>[AZURE.NOTE] Se eliminar o recurso de cluster **não** elimina todos os outros recursos que é composto por seu cluster de serviço ferro.

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Eliminar o recurso inteiro grupo (RG) no qual o cluster de serviço ferro

Esta é a forma mais fácil para se certificar de que elimina todos os recursos associados com o seu cluster, incluindo o grupo de recursos. Pode eliminar o grupo de recursos através do PowerShell ou através do portal do Azure. Se o seu grupo de recursos tiver recursos que não estão relacionadas com o serviço de cluster ferro, em seguida, pode eliminar recursos específicos.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Eliminar o grupo de recursos através do PowerShell do Azure

Também pode eliminar o grupo de recursos ao executar os seguintes cmdlets do Azure PowerShell. Certifique-se de que Azure PowerShell 1.0 ou maior está instalado no seu computador. Se não tiver concluído isto antes, siga os passos descritos no [como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

Abra uma janela do PowerShell e execute os seguintes cmdlets PS:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Irá obter um pedido para confirmar a eliminação, se não utilizou o *-Forçar* opção. No confirmação a RG e todos os recursos contém são eliminados.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Eliminar um grupo de recursos no portal do Azure  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para o cluster de ferro de serviço que pretende eliminar.
3. Clique no nome do grupo de recursos na página cluster essentials.
4. Isto reúne a página **Essentials de grupo de recursos** .
5. Clique em **Eliminar**.
6. Siga as instruções na página para concluir a eliminação de grupo de recursos.

![Eliminar grupo de recursos][ResourceGroupDelete]


## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Eliminar o recurso de cluster e os recursos que utiliza, mas não outros recursos no grupo de recursos

Se o seu grupo de recursos tiver apenas os recursos que estão relacionados com o cluster de ferro de serviço que pretende eliminar, em seguida, é mais fácil eliminar o grupo de recursos inteira. Se quiser eliminar seletivamente os recursos um a um no seu grupo de recursos, em seguida, siga estes passos.

Se implementado seu cluster através do portal ou utilizar um dos modelos de Gestor de recursos do serviço ferro a partir da Galeria de modelo, em seguida, todos os recursos que utiliza o cluster são marcados com as seguintes duas etiquetas. Pode utilizá-los para decidir quais os recursos que pretende eliminar.

***Marcar #1:*** Chave = valor, nome de cluster = nome do cluster

***Marcar #2:*** Chave = resourceName, valor = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Eliminar recursos específicos no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para o cluster de ferro de serviço que pretende eliminar.
3. Aceda a **todas as definições** na pá essentials.
4. Clique em **etiquetas** em **Gestão de recursos** na pá definições.
5. Clique das **etiquetas** de pá de etiquetas para obter uma lista de todos os recursos com essa etiqueta.

    ![Etiquetas de recursos][ResourceTags]

6. Assim que tiver a lista de recursos com etiquetas, clique em cada um dos recursos e eliminá-los.

    ![Recursos com tags][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Eliminar os recursos através do PowerShell do Azure

Pode eliminar os recursos um a um executando os seguintes cmdlets do Azure PowerShell. Certifique-se de que Azure PowerShell 1.0 ou maior está instalado no seu computador. Se não tiver concluído isto antes, siga os passos descritos no [como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

Abra uma janela do PowerShell e execute os seguintes cmdlets PS:

```powershell
Login-AzureRmAccount
```
Para cada um dos recursos que pretende eliminar, execute o seguinte:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Para eliminar o recurso de cluster, execute o seguinte:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Próximos passos
Leia o seguinte também saber mais sobre atualizar um cluster e a partições serviços:

- [Saiba mais sobre actualizações de cluster](service-fabric-cluster-upgrade.md)
- [Saiba mais sobre como criar a partições com estado serviços para escala máxima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
