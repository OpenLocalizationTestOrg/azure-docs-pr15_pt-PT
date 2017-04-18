<properties 
    pageTitle="Utilizar os privilégios de raiz em máquinas virtuais de Linux | Microsoft Azure" 
    description="Saiba como utilizar os privilégios de raiz numa máquina de virtual Linux no Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>


# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Utilizar os privilégios de raiz em máquinas virtuais de Linux no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Por predefinição, o `root` utilizador é desativado num máquinas virtuais de Linux no Azure. Os utilizadores podem executar comandos com privilégios elevados utilizando o `sudo` comando. No entanto, a experiência pode variar dependendo de como o sistema aprovisionado.

1. **Chave SSH e palavra-passe palavra-passe ou apenas** - a máquina virtual aprovisionada com um certificado de um dos (`.CER` ficheiro) ou tecla SSH, bem como uma palavra-passe, ou apenas um nome de utilizador e palavra-passe. Neste caso `sudo` irá pedir por palavra-passe do utilizador antes de executar o comando.

2. **Apenas a chave SSH** - máquina virtual aprovisionada com um certificado (`.cer`, `.pem`, ou `.pub` ficheiro) ou tecla SSH, mas nenhuma palavra-passe.  Neste caso `sudo` **não será** pedido palavra-passe do utilizador de antes de executar o comando.


## <a name="ssh-key-and-password-or-password-only"></a>SSH chave e palavra-passe ou palavra-passe apenas

Inicie sessão na máquina virtual Linux utilizando a autenticação de palavra-passe ou tecla SSH, em seguida, execute os comandos utilizando `sudo`, por exemplo:

    # sudo <command>
    [sudo] password for azureuser:

Neste caso será pedido ao utilizador para uma palavra-passe. Depois de introduzir a palavra-passe `sudo` vai executar o comando com `root` privilégios.

Também pode ativar passwordless sudo editando a `/etc/sudoers.d/waagent` de ficheiros, por exemplo:

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Esta alteração irá permitir para sudo passwordless pelo utilizador "azureuser".

## <a name="ssh-key-only"></a>SSH chave apenas

Inicie sessão na máquina virtual Linux utilizando a autenticação de chave SSH, em seguida, execute os comandos utilizando `sudo`, por exemplo:

    # sudo <command>

Neste caso o utilizador será **não** ser-lhe pedido para uma palavra-passe. Depois de premir `<enter>`, `sudo` vai executar o comando com `root` privilégios.

 
