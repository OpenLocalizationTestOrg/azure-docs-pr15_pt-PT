<properties 
   pageTitle="Alterar as palavras-passe StorSimple | Microsoft Azure" 
   description="Descreve como utilizar o serviço do Gestor de StorSimple para alterar o seu Gestor de instantâneo StorSimple e do dispositivo administrador palavras-passe." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/17/2016"
   ms.author="alkohli"/>

# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Utilizar o serviço do Gestor de StorSimple para alterar as palavras-passe StorSimple

## <a name="overview"></a>Descrição geral 

O portal clássico Azure página **Configurar** contém todos os parâmetros de dispositivo pode reconfigurar num dispositivo StorSimple gerido por um serviço de Gestor de StorSimple. Neste tutorial explica como pode utilizar a página de **Configurar** para alterar o seu administrador do dispositivo ou palavra-passe StorSimple instantâneo Manager.

## <a name="change-the-device-administrator-password"></a>Alterar a palavra-passe de administrador do dispositivo

Quando utilizar interface do Windows PowerShell para aceder ao dispositivo StorSimple, são necessários para introduzir uma palavra-passe de administrador do dispositivo. Quando o primeiro dispositivo StorSimple está registado com um serviço, a palavra-passe predefinida para esta interface é a *Palavra-passe1*. Para a segurança dos seus dados, são necessários para alterar esta palavra-passe no final do processo de registo. Não é possível sair a partir do processo de registo sem alterar esta palavra-passe. Para obter mais informações, consulte o artigo [passo 3: configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Em seguida, pode ser alterada a palavra-passe que foi definida pela primeira vez através da interface do Windows PowerShell durante o registo através do portal do Azure clássico. Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a palavra-passe de administrador do dispositivo

1. No portal do clássico, clique em **dispositivos** > **Configurar** para o seu dispositivo.

2. Desloque-se até à secção de **Palavra-passe de administrador do dispositivo** . Fornece uma palavra-passe de administrador que contém a partir de 8 a 15 carateres. A palavra-passe tem de ser uma combinação de 3 ou mais dos carateres em maiúsculas, minúsculas, numéricos e especiais.

3. Confirme a palavra-passe.

4. Clique em **Guardar** na parte inferior da página.

Agora deverá ser actualizada a palavra-passe de administrador do dispositivo. Pode utilizar esta palavra-passe modificada para aceder à interface do Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Alterar a palavra-passe StorSimple instantâneo Gestor

Software do Gestor de instantâneo StorSimple reside no seu anfitrião do Windows e permite aos administradores gerir cópias de segurança do seu dispositivo StorSimple sob a forma de local e na nuvem instantâneos.

Quando configurar um dispositivo no StorSimple instantâneo Manager, lhe ser pedido para fornecer o endereço IP do dispositivo e a palavra-passe para autenticar o dispositivo de armazenamento. Em primeiro lugar, esta palavra-passe está configurada através da interface do Windows PowerShell. Para obter mais informações, consulte o artigo [passo 3: configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Em seguida, pode ser alterada a palavra-passe que foi definida pela primeira vez através da interface do Windows PowerShell durante o registo através do portal do clássico. Execute os seguintes passos para alterar a palavra-passe StorSimple instantâneo gestor.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Para alterar a palavra-passe StorSimple instantâneo Gestor

1. No portal do clássico, clique em **dispositivos** > **Configurar** para o seu dispositivo.

2. Desloque para baixo para a secção **StorSimple instantâneo Gestor** . Introduza uma palavra-passe é 14 ou 15 carateres. Certifique-se de que a palavra-passe contém uma combinação de 3 ou mais dos carateres em maiúsculas, minúsculas, numéricos e especiais.

3. Confirme a palavra-passe.

4. Clique em **Guardar** na parte inferior da página.

A palavra-passe do Gestor de instantâneo StorSimple agora deve ser atualizada.
 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [segurança StorSimple](storsimple-security.md).

- Saiba mais sobre como [modificar a configuração do dispositivo](storsimple-modify-device-config.md).

- Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
