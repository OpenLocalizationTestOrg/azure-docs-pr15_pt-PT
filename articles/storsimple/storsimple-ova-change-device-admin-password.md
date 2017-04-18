<properties 
   pageTitle="Alterar a palavra-passe de administrador do dispositivo virtual de StorSimple | Microsoft Azure"
   description="Descreve como utilizar o portal do Azure clássico ou web StorSimple Virtual matriz da IU para alterar a palavra-passe de administrador do dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Alterar a palavra-passe de administrador do dispositivo de matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Ao utilizar a interface do Windows PowerShell para aceder ao dispositivo virtual StorSimple, são necessários para introduzir uma palavra-passe de administrador do dispositivo. Quando o dispositivo de StorSimple pela primeira vez está aprovisionado e iniciado, a palavra-passe predefinida é a *Palavra-passe1*. Para a segurança dos seus dados, a palavra-passe predefinida expira a primeira vez que iniciar sessão e é necessários para alterar esta palavra-passe.

Também pode utilizar a web local IU ou portal clássico do Azure para alterar a palavra-passe de administrador do dispositivo a qualquer momento após o dispositivo está implementado no seu ambiente de produção. Cada um destes procedimentos é descrito neste artigo.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Utilizar o portal do clássico Azure para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo através do portal do Azure clássica.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Para alterar a palavra-passe de administrador do dispositivo através do portal clássica do Azure

1. No portal do, clique em **dispositivos** > **configuração** para o seu dispositivo.

2. Desloque-se até à secção de **Palavra-passe de administrador do dispositivo** . Fornece uma palavra-passe de administrador que contém a partir de 8 a 15 carateres. A palavra-passe tem de ser uma combinação de carateres em maiúsculas, minúsculas, numéricos e especiais.

3. Confirme a palavra-passe.

4. Clique em **Guardar** na parte inferior da página.

Agora deverá ser actualizada a palavra-passe de administrador do dispositivo. Pode utilizar esta palavra-passe modificada para aceder ao dispositivo localmente.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Utilizar a web de matriz Virtual StorSimple da IU para alterar a palavra-passe

Execute os seguintes passos para alterar a palavra-passe de administrador do dispositivo através da IU da web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para alterar a palavra-passe de administrador do dispositivo através da web local da IU

1. No local IU da web, clique em **Manutenção** > **Alterar palavra-passe** para o seu dispositivo.

    ![alterar a palavra-passe1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Introduza a **palavra-passe atual**.

3. Fornece uma **nova palavra-passe**. A palavra-passe tem de ser, pelo menos, 8 carateres de comprimento. Tem de conter 3 de 4 das seguintes opções: carateres em maiúsculas, minúsculas, numéricos e especiais.

    Tenha em atenção que a palavra-passe não pode ser iguais às últimas 24 palavras-passe.

3. Volte a introduzi-la para confirmar.

    ![alterar a palavra-passe2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. Na parte inferior da página, clique em **Aplicar**. A palavra-passe nova, em seguida, será aplicada. Se a alteração de palavra-passe não é bem sucedida, irá ver a seguinte mensagem de erro.

    ![Erro de palavra-passe](./media/storsimple-ova-change-device-admin-password/image42.png)

    Depois da palavra-passe é atualizada com êxito, será notificado. Em seguida, pode utilizar esta palavra-passe modificada para aceder ao dispositivo localmente.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).
