<properties
    pageTitle="Iniciar sessão uma VM Azure clássico | Microsoft Azure"
    description="Utilize o portal clássico Azure para iniciar sessão a uma máquina virtual de Windows criada com o modelo de implementação clássico."
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
    ms.date="07/28/2016"
    ms.author="cynthn"/>


# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Início de sessão a uma máquina virtual de Windows Azure portal clássico a utilizar

No portal clássico Azure, utilize o botão de **Ligar** para iniciar uma sessão de ambiente de trabalho remoto e inicie sessão para uma VM do Windows.

Confirma que deseja ligar a uma VM Linux? Veja [como início de sessão a uma máquina virtual com o Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Obter informações sobre como [efectuar estes passos utilizando o novo portal Azure](virtual-machines-windows-connect-logon.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 

## <a name="video-walkthrough"></a>Instruções de vídeo

Eis um vídeo tutorial sobre os passos neste tutorial. Abrange igualmente os pontos finais e públicas e privadas portas utilizadas para ligar a uma VM de Windows no Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

1. Iniciar sessão no portal Azure clássico.

2. Clique em **máquinas virtuais**e, em seguida, seleccione a máquina virtual.

3. Na barra de comandos na parte inferior da página, clique em **Ligar**.

    ![Início de sessão para a máquina virtual](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)
    
> [AZURE.TIP] Se o botão ' **Ligar** ' não estiver disponível, consulte as sugestões de resolução de problemas no final deste artigo.

## <a name="log-on-to-the-virtual-machine"></a>Início de sessão para a máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Passos seguintes

-   Se o botão de **Ligar** está inactivo ou tiver outros problemas com a ligação de ambiente de trabalho remoto, tente repor a configuração. Do dashboard máquina virtual, em **Rápido rapidamente**, clique em **Repor a configuração remota**.
-   Para problemas com a palavra-passe, tente repor. Do dashboard máquina virtual, em **Rápido rapidamente**, clique em **Repor palavra-passe**.

Se as sugestões não funcionarem ou não são o que é necessário, consulte [ligações de resolução de problemas de ambiente de trabalho remoto para uma baseado no Windows Azure Máquina Virtual](virtual-machines-windows-troubleshoot-rdp-connection.md). Este artigo orienta-o a diagnosticar e resolver problemas comuns.


