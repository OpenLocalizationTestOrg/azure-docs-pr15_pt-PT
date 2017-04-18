<properties
   pageTitle="Atualizar o seu dispositivo StorSimple | Microsoft Azure"
   description="Explica como utilizar a funcionalidade de atualização StorSimple para instalar atualizações de modo normal e manutenção e correções."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# <a name="update-your-storsimple-8000-series-device"></a>Atualizar o seu dispositivo StorSimple 8000 série

## <a name="overview"></a>Descrição geral

As funcionalidades de atualizações StorSimple permitem-lhe manter o seu dispositivo StorSimple facilmente atualizados. Dependendo do tipo de atualização, pode aplicar atualizações para o dispositivo através do portal do Azure clássico ou através da interface do Windows PowerShell. Neste tutorial descreve os tipos de atualização e como instalar cada um deles.

Pode aplicar dois tipos de atualizações de dispositivo: 

- Normal (ou modo Normal) atualizações
- Atualizações de modo a manutenção

Pode instalar actualizações regulares através da Azure portal clássico ou o Windows PowerShell; No entanto, tem de utilizar o Windows PowerShell para instalar as atualizações do modo de manutenção. 

Cada tipo de atualização é descrito em separado, abaixo.

### <a name="regular-updates"></a>Atualizações normais

As actualizações regulares são as atualizações não desorganização que podem ser instaladas quando o dispositivo está no modo Normal. Estas atualizações estão aplicadas através do Web site da Microsoft Update para cada controlador de dispositivo. 

> [AZURE.IMPORTANT] Um controlador activação pós-falha poderá ocorrer durante o processo de atualização. No entanto, esta irá não vai afetar disponibilidade do sistema ou operação.

- Para obter detalhes sobre como instalar actualizações regulares através do portal do Azure clássico, consulte o artigo [instalar normais atualizações através de Azure portal(#install-regular-updates-via-the-azure-classic-portal) clássico.

- Também pode instalar actualizações regulares através do Windows PowerShell para StorSimple. Para obter detalhes, consulte o artigo [instalar atualizações normais através do Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Atualizações de modo a manutenção

Atualizações de modo a manutenção são actualizações desorganização como atualiza os firmware do disco. Estas atualizações requerem o dispositivo para ser colocar no modo de manutenção. Para obter mais detalhes, consulte o artigo [passo 2: modo de manutenção introduza](#step2). Não pode utilizar o portal clássico Azure para instalar as atualizações do modo de manutenção. Em vez disso, tem de utilizar o Windows PowerShell para StorSimple. 

Para obter detalhes sobre como instalar atualizações do modo de manutenção, consulte o artigo [Manutenção instalar atualizações de modo através do Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT] Atualizações de modo a manutenção devem ser aplicadas separadamente para cada controlador de. 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalar actualizações regulares através do portal clássica do Azure

Pode utilizar o portal do Azure clássico para aplicar atualizações ao seu dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalar actualizações regulares através do Windows PowerShell para StorSimple

Em alternativa, pode utilizar o Windows PowerShell para StorSimple para aplicar atualizações normal (modo Normal).

> [AZURE.IMPORTANT] Apesar de poder instalar actualizações regulares através do Windows PowerShell para StorSimple, recomendamos que instale atualizações normais através do portal do Azure clássica. Começar em 1 de atualização, verificações de pré-lançamento serão executadas antes de instalar atualizações a partir do portal. Estas verificações pré irão têm precedência sobre falhas e certifique-se uma experiência optimizada. 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalar as atualizações do modo de manutenção através do Windows PowerShell para StorSimple

Utilizar o Windows PowerShell para StorSimple para aplicar atualizações do modo de manutenção para o seu dispositivo StorSimple. Todos os pedidos e/s são colocados em pausa neste modo. Serviços como o não voláteis memória de acesso aleatório (NVRAM) ou o serviço de cluster também são parados. Ambos os controladores são reiniciados quando introduz ou sair do modo de presente. Quando sai deste modo, todos os serviços serão retomada e devem ser saudáveis. (Isto pode demorar alguns minutos.)

Se necessitar de aplicar atualizações do modo de manutenção, receberá um alerta através do portal do Azure clássico que tem as atualizações que tem de estar instaladas. Este alerta irá incluir as instruções para utilizar o Windows PowerShell para StorSimple para instalar as atualizações. Depois de atualizar o seu dispositivo, utilize o mesmo procedimento para alterar o dispositivo para o modo normal. Para obter instruções passo a passo, consulte o artigo [passo 4: modo de manutenção de sair](#step4).

> [AZURE.IMPORTANT] 
> 
> - Antes de introduzir o modo de manutenção, verifique se ambos os controladores do dispositivo estão saudáveis ao verificar o **Estado do Hardware** na página **Manutenção** no portal do Azure clássica. Se o controlador de não for Saudável, contacte o Microsoft Support para os passos seguintes. Para obter mais informações, aceda ao contactar o suporte do Microsoft. 
> - Quando estiver no modo de manutenção, tem de aplicar a atualização pela primeira vez no controlador de um e, em seguida, no outro controlador de.

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Passo 1: Ligar à consola de série<a name="step1">

Em primeiro lugar, utilize uma aplicação como o betumes para aceder a consola de série. O procedimento seguinte explica como utilizar betumes para ligar à consola série.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Passo 2: Entrar no modo de manutenção<a name="step2">

Depois de se ligar à consola, determine se existem atualizações para o instalar e entrar no modo de manutenção instalá-los.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Passo 3: Instalar as suas atualizações<a name="step3">

Em seguida, instale as atualizações.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Passo 4: Modo de manutenção de sair<a name="step4">

Por fim, sair do modo de manutenção.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalar correcções através do Windows PowerShell para StorSimple

Ao contrário de atualizações do Microsoft Azure StorSimple, correcções são instaladas a partir de uma pasta partilhada. Tal como acontece com atualizações, existem dois tipos de correcções: 

- Correcções normais 
- Manutenção modo correcções  

Os procedimentos seguintes explicam como utilizar o Windows PowerShell para StorSimple para instalar o normal e correcções de modo de manutenção.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>O que acontece às actualizações se efetuar uma reposição de fábrica do dispositivo?

Se um dispositivo é repor as definições de fábrica do mesmo, todas as atualizações são perdidas. Depois do dispositivo de fábrica do mesmo repor está registado e configurado, terá de instalar manualmente atualizações através do portal clássico Azure e/ou do Windows PowerShell para StorSimple. Para mais informações sobre fábrica do mesmo repor, consulte o artigo [Repor o dispositivo para as predefinições de fábrica do mesmo](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre como [utilizar o Windows PowerShell para StorSimple administrar o seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).
- Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
