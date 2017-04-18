<properties
   pageTitle="Como redimensionar uma VM Linux | Microsoft Azure"
   description="Como Dimensionar para cima ou dimensionar para baixo uma máquina de virtual Linux, ao alterar o tamanho da memória virtual."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# <a name="how-to-resize-a-linux-vm"></a>Como redimensionar uma VM Linux

## <a name="overview"></a>Descrição geral 

Depois de aprovisionar uma máquina de virtual (VM), é possível dimensionar a VM para cima ou para baixo ao alterar o [tamanho da memória virtual][vm-sizes]. Em alguns casos, tem de retirar a VM pela primeira vez. Isto pode acontecer se o novo tamanho não está disponível no cluster de hardware que aloja a VM.

Este artigo mostra como redimensionar uma VM Linux utilizando o [Clip do Azure][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica.


## <a name="resize-a-linux-vm"></a>Redimensionar uma VM Linux 

Para redimensionar uma VM, execute os passos seguintes.

1. Execute o seguinte comando do clip. Este comando lista os tamanhos VM que estão disponíveis no cluster de hardware onde está alojada a VM.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Se o tamanho pretendido estiver listado, execute o seguinte comando para redimensionar a VM.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    A VM irá reiniciar durante este processo. Após o reinício, o sistema operativo existente e os discos de dados serão mapeados novamente. Qualquer elemento no disco temporário serão perdido.

    Utilizar o `--enable-boot-diagnostics` opção permite [Diagnósticos de arranque][boot-diagnostics], para iniciar sessão qualquer erros relacionados com o arranque.

3. Caso contrário, se o tamanho pretendido não estiver listado, execute os seguintes comandos para retirar a VM, a redimensioná-la e, em seguida, reinicie a VM.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] Também retirar a VM liberta quaisquer endereços IP dinâmicos atribuídos a VM. Os discos SO e os dados não são afetados.
   
## <a name="next-steps"></a>Próximos passos

Para escalabilidade adicional, executar várias instâncias VM e dimensionar saída. Para obter mais informações, consulte o artigo [Dimensionar automaticamente Linux máquinas num conjunto de escala de Máquina Virtual][scale-set]. 

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md