<properties
    pageTitle="Acerca de imagens para máquinas virtuais de Windows | Microsoft Azure"
    description="Saiba mais sobre como as imagens são utilizadas com máquinas virtuais Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="about-images-for-windows-virtual-machines"></a>Acerca de imagens para máquinas de virtuais do Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## <a name="working-with-images"></a>Trabalhar com imagens

Pode utilizar o módulo Azure PowerShell para gerir as imagens disponíveis à sua subscrição do Azure. Também pode utilizar o portal do Azure clássico para algumas tarefas de imagem, mas a linha de comandos dá-lhe mais opções.


-   **Obter todas as imagens**:`Get-AzureVMImage`devolve uma lista de todas as imagens disponíveis na sua subscrição atual: as imagens, assim como as fornecida pela Azure ou parceiros. Isto significa que poderá obter uma lista de grandes dimensões. Os exemplos seguintes mostram como obter uma lista de endereço mais curta.
-   **Obter famílias de tipos de imagem**:`Get-AzureVMImage | select ImageFamily` obtém uma lista de famílias de tipos de Imagem mostrando cadeias **ImageFamily** propriedade.
-   **Obter todas as imagens numa família específica**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **Encontrar imagens de VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` Isto funciona filtrando a propriedade de DataDiskConfiguration, que só se aplica a VM imagens. Este exemplo também filtra a saída para apenas o nome da etiqueta e a imagem.
-   **Guardar uma imagem GRG**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **Guardar uma imagem especializada**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] O parâmetro OSState é necessário se pretender criar uma imagem VM, que inclui discos de dados, bem como o disco do sistema operativo. Se não utiliza o parâmetro, o cmdlet cria uma imagem do sistema operativo. O valor do parâmetro indica se a imagem é generalized ou especializada, com base em se o disco do sistema operativo foi preparado para reutilização.
-   **Eliminar uma imagem**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`


## <a name="next-steps"></a>Próximos passos

Também pode [criar uma máquina do Windows através do portal clássico](virtual-machines-windows-classic-tutorial.md)

