<properties
    pageTitle="Criar uma imagem VM a partir de um VM Azure | Microsoft Azure"
    description="Saiba como criar uma imagem VM GRG a partir de um VM Azure existentes criados no modelo de implementação de Gestor de recursos"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>


# <a name="download-the-template-for-a-vm"></a>Transferir o modelo para uma VM

Quando cria uma VM no Azure utilizando o portal ou o PowerShell, um modelo de Gestor de recursos é automaticamente criado por si. Pode utilizar este modelo para duplicar rapidamente uma implementação. O modelo contém informações sobre todos os recursos num grupo de recursos. Para uma máquina virtual, isto significa que os contentores de modelo tudo o que é criado em apoio VM nesse grupo de recursos, incluindo os recursos de redes.

## <a name="download-the-template-using-the-portal"></a>Transferir o modelo através do portal

1. Inicie a sessão [portal do Azure](https://portal.azure.com/).
2. Um menu concentrador, selecione **máquinas virtuais**.
3. Selecione a máquina virtual a partir da lista.
5. Selecione o **script de automatização**.
6. Selecione **Transferir** e guardar o ficheiro. zip no seu computador local.
7. Abra o ficheiro. zip e extrair os ficheiros para uma pasta. O ficheiro. zip irá conter:
    
    - Deploy.ps1
    - Deploy.SH 
    - deployer.RB
    - DeploymentHelper.cs
    - Parameters.JSON
    - Template.JSON

O ficheiro .json é o modelo.
    
## <a name="download-the-template-using-powershell"></a>Transferir o modelo através do PowerShell

Também pode transferir o ficheiro de modelo .json utilizando o cmdlet [AzureRMResourceGroup de exportação](https://msdn.microsoft.com/library/mt715427.aspx) . Pode utilizar o `-path` parâmetro para fornecer o nome de ficheiro e o caminho para o ficheiro .json. Este exemplo mostra como transferir o modelo do grupo de recursos com o nome **myResourceGroup** para a pasta **C:\users\public\downloads** no seu computador local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a utilização de modelos de recursos de implementação, consulte o artigo [Tutorial do Gestor de recursos do modelo](../resource-manager-template-walkthrough.md).