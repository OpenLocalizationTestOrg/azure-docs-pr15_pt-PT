<properties 
   pageTitle="Substituir o quadro num dispositivo StorSimple | Microsoft Azure"
   description="Descreve como remover e substituir o quadro para a sua inclusão primária StorSimple ou a inclusão EBOD."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-the-chassis-on-your-storsimple-device"></a>Substituir o quadro no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial explica como remover e substituir um quadro num dispositivo StorSimple 8000 série. O modelo de StorSimple 8100 é um dispositivo de inclusão única (um quadro), Considerando que o 8600 é um dispositivo de inclusão duplos (duas quadro). Para um modelo de 8600, existem duas quadro potencialmente poderá falhar no dispositivo: quadro para a inclusão principal ou o quadro para a inclusão EBOD.

Em ambos os casos, o quadro de substituição que é fornecido pela Microsoft está vazio. Sem Power e refrigeração módulos do (PCMs), módulos controlador, unidades de disco de estado sólido (SSDs), unidades de disco rígido (HDDs) ou EBOD módulos serão incluídos.

>[AZURE.IMPORTANT] Antes de remover e substituição de quadro, reveja as informações de segurança em [substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-chassis"></a>Remover o quadro

Execute os seguintes passos para remover o quadro no seu dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Para remover um quadro

1. Certifique-se de que o dispositivo de StorSimple é encerrar e perder a ligação a partir de todas as origens de energia.

2. Remova todas as rede e cabos SA, se aplicável.

3. Remova a unidade de bastidor.

4. Remover cada uma das unidades e tenha em atenção as faixas a partir do qual são removidos. Para mais informações, consulte o artigo [remover a unidade de disco](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. Em inclusão EBOD (se trata-se no quadro que ocorreu uma falha), remova os módulos de controlador EBOD. Para mais informações, consulte o artigo [Remover um controlador de EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 

    Em inclusão principal (se trata-se no quadro que ocorreu uma falha), remova os controladores e tenha em atenção as faixas a partir do qual são removidos. Para mais informações, consulte o artigo [Remover um controlador de](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instalar o quadro

Execute os passos seguintes para instalar o quadro no seu dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Para instalar um quadro

1. Montagem quadro no bastidor. Para mais informações, consulte o artigo [Bastidor-montagem dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [Bastidor-montagem dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Depois de quadro é instalado no bastidor, instale os módulos controlador nas mesmas posições que anteriormente foram instaladas no.

3. Instale as unidades nas posições e faixas anteriormente foram instaladas no mesmo.

    >[AZURE.NOTE] Recomendamos que instale as SSDs nas faixas primeiro e, em seguida, instale os HDDs.

2. Com o dispositivo colocado no bastidor e os componentes instalados, ligar o seu dispositivo para as origens de alimentação adequada e ativar o dispositivo. Para obter detalhes, consulte o artigo [cabo dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [um cabo dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).

