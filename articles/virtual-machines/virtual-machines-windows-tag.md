<properties
   pageTitle="Como marcar uma VM | Microsoft Azure"
   description="Saiba mais acerca da marcação de uma máquina de virtual Windows criada no Azure utilizando o modelo de implementação do Gestor de recursos"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como marcar uma máquina de virtual do Windows no Azure


Este artigo descreve várias formas de marcar uma máquina de virtual do Windows no Azure através do modelo de implementação do Gestor de recursos. Etiquetas são pares de chave/valor definido pelo utilizador que podem ser colocados diretamente num recurso ou um grupo de recursos. Azure atualmente suporta até 15 marcas por recurso e grupo de recursos. Etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Tenha em atenção que as etiquetas são suportadas para recursos criados através do apenas o modelo de implementação do Gestor de recursos. Se pretender marcar uma máquina de virtual Linux, consulte o artigo [como marcar uma máquina de virtual Linux no Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Etiquetas com PowerShell

Para criar, adicionar e eliminar etiquetas através do PowerShell, primeiro tem de configurar o seu [ambiente do PowerShell com o Gestor de recursos do Azure][]. Depois de concluir a configuração, pode colocar etiquetas em cluster, rede e armazenamento recursos na criação ou de um recurso é criado através do PowerShell. Este artigo irá concentrar-se em etiquetas de visualização/edição colocadas em máquinas virtuais.

Em primeiro lugar, navegue para uma Máquina Virtual através de `Get-AzureRmVM` cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se o seu Máquina Virtual já contenha etiquetas, em seguida, irá ver todas as marcas no seu recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se pretender para adicionar etiquetas através do PowerShell, pode utilizar o `Set-AzureRmResource` comando. Tenha em atenção ao atualizar etiquetas através do PowerShell, etiquetas são atualizadas como um todo. Por isso, se estiver a adicionar uma etiqueta a um recurso que já tem etiquetas, terá de incluir todas as etiquetas que pretende que seja colocado no recurso. Abaixo está um exemplo de como adicionar etiquetas adicionais a um recurso através os cmdlets do PowerShell.

Este cmdlet primeiro define todas as etiquetas colocadas em *MyTestVM* para a variável de *$tags* , utilizando o `Get-AzureRmResource` e `Tags` propriedade.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

O comando segundo apresenta as etiquetas para a variável determinada.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

O comando terceiro adiciona uma tag adicional para a variável de *$tags* . Tenha em atenção a utilização do **+=** Acrescentar novo par de valor/chave à lista de *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

O comando quarto define todas as etiquetas definidas na variável *$tags* para o recurso indicado. Neste caso, é MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

O comando quinto mostra todas as etiquetas no recurso. Como pode ver, *localização* é agora definida como uma etiqueta com *MyLocation* como o valor.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

Para saber mais acerca da marcação de através do PowerShell, consulte o artigo os [Cmdlets do Azure recurso][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Próximos passos

* Para saber mais acerca da marcação dos recursos do Azure, consulte o artigo [Descrição geral do Gestor de recursos do Azure][] e [Utilizar etiquetas para organizar os recursos do Azure][].
* Para ver como as etiquetas podem ajudar a gerir a utilização de recursos Azure, consulte [Noções sobre a sua fatura do Azure][] e [Obtenha informações para o consumo de recursos do Microsoft Azure][].

[Ambiente do PowerShell com o Gestor de recursos do Azure]: ../powershell-azure-resource-manager.md
[Cmdlets de recurso Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Descrição geral do Gestor de recursos do Azure]: ../azure-resource-manager/resource-group-overview.md
[Utilizar etiquetas para organizar os recursos do Azure]: ../resource-group-using-tags.md
[Noções sobre a sua fatura Azure]: ../billing/billing-understand-your-bill.md
[Informações de lucro para o consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md
