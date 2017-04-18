<properties
        pageTitle="Repor a palavra-passe de Linux VM e chave SSH a partir do Clip | Microsoft Azure"
        description="Como utilizar a extensão de VMAccess a partir do Azure Interface da linha de comandos (CLI) para repor uma palavra-passe de Linux VM ou chave SSH, corrigir a configuração de SSH e verificar a consistência do disco"
        services="virtual-machines-linux"
        documentationCenter=""
        authors="cynthn"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines-linux"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="06/14/2016"
        ms.author="cynthn"/>

# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Como repor uma palavra-passe de Linux VM ou chave SSH, corrigir a configuração de SSH e verificar a consistência do disco utilizando a extensão de VMAccess


Se não é possível ligar a uma máquina de virtual Linux no Azure devido a uma palavra-passe esquecida, uma tecla de Shell seguro (SSH) incorreto, ou um problema com a configuração de SSH, utilize a extensão de VMAccessForLinux com o clip do Azure para repor a palavra-passe ou SSH chave, corrigir a configuração de SSH e verificar a consistência do disco. 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Com o clip Azure, utilize o comando **set extensão azure vm** a partir do seu interface de comandos (festa, terminais, linha de comandos) para comandos do access. Execute **Ajuda azure vm extensão definido** para utilização de extensão detalhadas.

Com o clip Azure, pode efetuar as seguintes tarefas:

+ [Repor a palavra-passe](#pwresetcli)
+ [Repor a chave de SSH](#sshkeyresetcli)
+ [Repor a palavra-passe e a chave SSH](#resetbothcli)
+ [Criar uma nova conta de utilizador sudo](#createnewsudocli)
+ [Repor a configuração de SSH](#sshconfigresetcli)
+ [Eliminar um utilizador](#deletecli)
+ [Apresenta o estado da extensão VMAccess](#statuscli)
+ [Verificar a consistência de discos adicionados](#checkdisk)
+ [Reparar discos adicionados no seu VM Linux](#repairdisk)


## <a name="prerequisites"></a>Pré-requisitos

Terá de fazer o seguinte procedimento:

- Terá de [instalar o clip do Azure](../xplat-cli-install.md) e [ligar à sua subscrição](../xplat-cli-connect.md) utilizar os recursos Azure associados à sua conta.
- Defina o modo correto para o modelo clássico de implementação, escrevendo o seguinte na linha de comandos:
        
        azure config mode asm
        
- Tem uma nova palavra-passe ou um conjunto de SSH chaves, se pretender repor deles. Não precisa de estas se pretender repor a configuração de SSH.


## <a name="pwresetcli"></a>Repor a palavra-passe

1. Crie um ficheiro com o nome PrivateConf.json com estas linhas. Substituir os parênteses e o & #60; o marcador de posição & #62; valores com as suas próprias informações.

        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }

2. Execute este comando, substituindo o nome do seu máquina virtual para #60; vm nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>Repor a chave de SSH

1. Crie um ficheiro com o nome PrivateConf.json com este conteúdo. Substituir os parênteses e o & #60; marcador de posição & #62; valores com as suas próprias informações.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }

2. Execute este comando, substituindo o nome do seu máquina virtual para #60; vm nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Repor a palavra-passe e a chave SSH

1. Crie um ficheiro com o nome PrivateConf.json com este conteúdo. Substituir os parênteses e o & #60; marcador de posição & #62; valores com as suas próprias informações.

        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }

2. Execute este comando, substituindo o nome do seu máquina virtual para #60; vm nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>Criar uma nova conta de utilizador sudo

Caso se esqueça o seu nome de utilizador, pode utilizar VMAccess para criar um novo com a autoridade sudo. Neste caso, o nome de utilizador e palavra-passe existentes não serão modificados.

Para criar um novo utilizador sudo com o access de palavra-passe, utilize o script em [Repor a palavra-passe](#pwresetcli) e especifique o novo nome de utilizador.

Para criar um novo utilizador sudo com o access chave SSH, utilize o script na [Repor a chave de SSH](#sshkeyresetcli) e especifique o novo nome de utilizador.

Também pode utilizar a [Repor a palavra-passe e a chave SSH](#resetbothcli) para criar um novo utilizador com palavra-passe e SSH chave acesso.

## <a name="sshconfigresetcli"></a>Repor a configuração de SSH

Se a configuração de SSH estiver num estado indesejado, também poderá perder o acesso para a VM. Pode utilizar a extensão de VMAccess para repor a configuração ao seu estado pré-definido. Para fazê-lo, basta definir a chave de "reset_ssh" para "True". A extensão irá reiniciar o servidor SSH, abrir a porta SSH no seu VM e repor a configuração de SSH para os valores predefinidos. A conta de utilizador (nome, palavra-passe ou teclas SSH) não será alterada.

> [AZURE.NOTE] O ficheiro de configuração de SSH obtém repor está localizado em /etc/ssh/sshd_config.

1. Crie um ficheiro com o nome PrivateConf.json com este conteúdo.

        {
        "reset_ssh":"True"
        }

2. Execute este comando, substituindo o nome do seu máquina virtual para #60; vm nome & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>Eliminar um utilizador

Se quiser eliminar uma conta de utilizador sem a funcionalidade de registo para a VM diretamente, pode utilizar este script.

1. Crie um ficheiro com o nome PrivateConf.json com este conteúdo, substituindo o nome de utilizador para remover para #60; usernametoremove & #62;. 

        {
        "remove_user":"<usernametoremove>"
        }

2. Execute este comando, substituindo o nome do seu máquina virtual para #60; vm nome & #62;. 

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>Apresenta o estado da extensão VMAccess

Para apresentar o estado da extensão VMAccess, execute este comando.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a>< um name = 'verificação do disco' <</a>Verificar consistência da discos adicionados

Para executar fsck em todos os discos na sua máquina de virtual Linux, terá de fazer o seguinte procedimento:

1. Crie um ficheiro com o nome PublicConf.json com este conteúdo. Verificar disco leva-o até um valor booleano para se pretende verificar discos ligados a sua máquina virtual ou não. 

        {   
        "check_disk": "true"
        }

2. Execute este comando para executar, substituindo o nome do seu máquina virtual para #60; vm nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>Reparar discos 

Para reparar discos que não são montagem ou têm erros de configuração de montagem, utilize a extensão de VMAccess para repor a configuração de montagem no seu computador de virtual Linux. O nome do seu disco para #60; yourdisk & #62; a substituir.

1. Crie um ficheiro com o nome PublicConf.json com este conteúdo. 

        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }

2. Execute este comando para executar, substituindo o nome do seu máquina virtual para #60; vm nome & #62;.

        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## <a name="next-steps"></a>Próximos passos

* Se pretender utilizar cmdlets do Azure PowerShell ou os modelos de Gestor de recursos do Azure para repor a palavra-passe ou SSH chave, corrigir configuração SSH e verificar a consistência do disco, consulte a [documentação de extensão VMAccess no GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Também pode utilizar o [Azure portal](https://portal.azure.com) para repor a palavra-passe ou SSH chave de um VM Linux implementado no modelo de implementação clássica. Não é possível utilizar atualmente não portal para este processo para uma VM Linux implementada no modelo de implementação de Gestor de recursos.

* Consulte o artigo [sobre as extensões de máquina virtual e funcionalidades](virtual-machines-linux-extensions-features.md) para obter mais informações sobre como utilizar extensões VM para máquinas virtuais Azure.
