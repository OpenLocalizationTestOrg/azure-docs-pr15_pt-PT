<properties 
    pageTitle="Selecionar nomes de utilizador para Linux | Microsoft Azure" 
    description="Saiba como selecionar nomes de utilizador para uma máquina de virtual Linux no Azure." 
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



#<a name="selecting-user-names-for-linux-on-azure"></a>Selecionar nomes de utilizador para Linux no Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Quando aprovisionar o uma máquina de virtual Linux no Azure tem de especificar o nome de um utilizador que não sejam raiz que pode utilizar mais tarde que inicie sessão na VM. Pode escolher o nome do novo utilizador ou se aprovisionamento através do portal do Azure clássico pode aceitar o nome predefinido "azureuser".

Na maioria dos casos este utilizador não existe na imagem base e será criado durante o processo de aprovisionamento. Se o utilizador existir na imagem VM base, em seguida, o agente do Azure Linux simplesmente configura a palavra-passe e/ou SSH chave para esse utilizador com base nas informações que especificou quando criar a VM.

**No entanto**, Linux define um conjunto de nomes de utilizador que não deve ser utilizado. O processo de aprovisionamento irá **falhar** se tentar aprovisionar um VM Linux utilizando um utilizador existente do sistema, que é definido como um utilizador com UID 0 99. Um exemplo típico é o `root` utilizador, que possui UID 0.

 - Consulte também: [intervalos padrão Linux Base - ID de utilizador](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Segue-se uma lista de utilizadores comuns de sistema incorporada para CentOS e Ubuntu que deve evitar utilizar quando Aprovisiona o uma máquina de virtual Linux no Azure. Esta lista é apenas um exemplo, consulte a documentação para a sua distribuição para se certificar de que o nome de utilizador que optar por não entram em conflito com um utilizador existente do sistema.


## <a name="centos"></a>CentOS
- abrt
- ADM
- áudio
- Reciclagem
- CD-ROM
- cgred
- daemon
- dbus
- chamadas
- DIP
- disco
- disquetes
- FTP
- FTP
- jogos
- Gopher
- haldaemon
- parar
- kmem
- bloquear
- LP
- correio
- Homem
- memória
- nfsnobody
- ninguém
- NTP
- operador
- oprofile
- postdrop
- Postfix
- qpidd
- raiz
- RPC
- rpcuser
- saslauth
- encerramento
- slocate
- sshd
- stapdev
- stapusr
- sincronização
- sobre
- banda
- teste
- tcpdump
- TTY
- utilizadores
- utempter
- utmp
- UUCP
- vcsa
- vídeo
- roda


## <a name="ubuntu"></a>Ubuntu
- ADM
- Admin
- áudio
- cópia de segurança
- Reciclagem
- CD-ROM
- crontab
- daemon
- chamadas
- DIP
- disco
- fax
- disquetes
- Fusível
- jogos
- gnats
- IRC
- kmem
- horizontal
- libuuid
- lista
- LP
- correio
- Homem
- messagebus
- mlocate
- netdev
- notícias
- ninguém
- nogroup
- operador
- plugdev
- proxy
- raiz
- SASL
- sombra
- SRC
- SSH
- sshd
- pessoal
- sudo
- sincronização
- sobre
- syslog
- banda
- TTY
- utilizadores
- utmp
- UUCP
- vídeo
- Voice
- whoopsie
- dados do www

 