<properties
    pageTitle="Anexar um disco a uma VM Linux | Microsoft Azure"
    description="Saiba como anexar um disco de dados para uma máquina virtual Azure executar Linux e inicialização-lo para que fique pronto a utilizar."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Como anexar um disco de dados a uma máquina de Virtual Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Consulte o artigo como [anexar um disco de dados utilizando o modelo de implementação do Gestor de recursos](virtual-machines-linux-add-disk.md).

Pode anexar discos vazios e discos que contêm dados para o seu VMs Azure. Os dois tipos de discos são ficheiros. vhd que residem numa conta de armazenamento Azure. Como com a adição de qualquer disco a uma máquina de Linux, depois de anexar o disco tem de iniciar e formatá-lo para que fique pronto a utilizar. Este detalhes do artigo anexar discos vazios e discos já que contêm dados para o seu VMs, bem como, em seguida, iniciar e formatar um novo disco.

> [AZURE.NOTE]É aconselhável utilizar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando cria uma máquina virtual Azure, tem um disco do sistema operativo e um disco temporário. **Não utilize o disco temporário para armazenar dados persistentes.** Como o nome implica, fornece armazenamento temporário apenas. Oferece sem cópia de segurança ou redundância porque-não reside na armazenamento Azure.
> O disco temporário é normalmente gerido pelo agente de Linux do Azure e automaticamente instalado para **/mnt/resource** (ou **/mnt** no Ubuntu imagens). Por outro lado, um disco de dados poderá ter o nome pelo Linux kernel algo parecido com `/dev/sdc`, e necessita de partição, formatar e montagem este recurso. Consulte o [Guia de utilizador do Azure Linux agente] [ Agent] para obter detalhes.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Iniciar um novo disco de dados no Linux

1. SSH para sua VM. Para obter mais detalhes, consulte o artigo [como iniciar sessão para uma máquina de virtual com Linux][Logon].

2. A seguir tem localizar o identificador de dispositivo para o disco de dados ao iniciar. Existem duas formas para o fazer:

    um) Grep para dispositivos SCSI nos registos, tal como o seguinte comando:

            $sudo grep SCSI /var/log/messages

    Para distribuições Ubuntu recentes, poderá ter de utilizar `sudo grep SCSI /var/log/syslog` uma vez que iniciar sessão `/var/log/messages` poderão ser desativados por predefinição.

    Pode encontrar o identificador do último disco de dados que foi adicionado nas mensagens que são apresentadas.

    ![Obter as mensagens do disco](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

    OU

    b) utilize o `lsscsi` comando para descobrir o id do dispositivo. `lsscsi`pode ser instalado por um `yum install lsscsi` (no vermelho chapéu com base distribuições) ou `apt-get install lsscsi` (no Debian com base distribuições). Pode encontrar o disco que está a procurar pelo seu _lun_ ou o **número de unidade lógica**. Por exemplo, o _lun_ para discos anexou pode ser facilmente vistos de `azure vm disk list <virtual-machine>` como:

            ~$ azure vm disk list TestVM
            info:    Executing command vm disk list
            + Fetching disk images
            + Getting virtual machines
            + Getting VM disks
            data:    Lun  Size(GB)  Blob-Name                         OS
            data:    ---  --------  --------------------------------  -----
            data:         30        TestVM-2645b8030676c8f8.vhd  Linux
            data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
            info:    vm disk list command OK

    Comparar estes dados com a saída de `lsscsi` para a mesma máquina virtual de exemplo:

            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

    O último número na cadeia de identificação em cada linha é o _lun_. Consulte o artigo `man lsscsi` para obter mais informações.

3. Na linha de comandos, escreva o seguinte comando para criar o seu dispositivo:

        $sudo fdisk /dev/sdc


4. Quando lhe for pedido, escreva **n** para criar uma nova partição.


    ![Criar o dispositivo](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. Quando lhe for pedido, escreva **p** para tornar a partições a partição principal. Escreva **1** para torná-lo a primeira partição e, em seguida, tipo enter para aceitar o valor predefinido para os gráficos de cilindros. Em alguns sistemas, pode mostrar os valores predefinidos do primeiro e últimos sectores, em vez de gráficos de cilindros. Pode optar por aceitar estas predefinições.


    ![Criar partição](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. Escreva **p** para ver os detalhes sobre o disco que está a ser dividida em partições.


    ![Informações da lista de disco](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. Escreva **w** para escrever as definições do disco.


    ![Escrever as alterações no disco](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. Agora pode criar o sistema de ficheiros na nova partição. Acrescentar o número da partição para o ID do dispositivo (no exemplo seguinte `/dev/sdc1`). O exemplo seguinte cria uma partição de ext4 num /dev/sdc1:

        # sudo mkfs -t ext4 /dev/sdc1

    ![Criar o sistema de ficheiros](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

    >[AZURE.NOTE] Os sistemas de SuSE Linux Enterprise 11 apenas suportam acesso só de leitura para sistemas de ficheiros ext4. Para estes sistemas, recomenda-se para formatar o novo sistema de ficheiros como ext3 em vez de ext4.


9. Torne um diretório para montagem novo sistema de ficheiros, da seguinte forma:

        # sudo mkdir /datadrive


10. Por fim pode montagem da unidade, da seguinte forma:

        # sudo mount /dev/sdc1 /datadrive

    O disco de dados está agora pronto para utilizar como **/datadrive**.
    
    ![Criar o directório e montagem do disco](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. Adicione a nova unidade para /etc/fstab:

    Para garantir que a unidade é remontada automaticamente após reiniciar o computador tem de ser adicionada para o ficheiro de fstab/etc /. Além disso, recomenda-se vivamente que o UUID (universal Identificador exclusivo) é utilizado na /etc/fstab para referir-se a unidade de em vez de apenas o nome do dispositivo (ou seja, /dev/sdc1). Utilizar o UUID evita o disco incorreto a ser instalado para uma determinada localização, se o SO Deteta um erro no disco durante o arranque e qualquer restantes discos de dados, em seguida, a ser atribuídos esses IDs de dispositivo. Para localizar o UUID da nova unidade, pode utilizar o utilitário **blkid** :

        # sudo -i blkid

    O resultado é semelhante ao seguinte:

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


    >[AZURE.NOTE] Editar um ficheiro **/etc/fstab** indevidamente poderá resultar em com que o sistema. Se não souber, consulte a documentação da distribuição para obter informações sobre como editar corretamente este ficheiro. Também é recomendável que é criada uma cópia de segurança do ficheiro /etc/fstab antes de o editar.

    Em seguida, abra o ficheiro de **/etc/fstab** num editor de texto:

        # sudo vi /etc/fstab

    Neste exemplo, vamos utilizar o valor UUID para o novo dispositivo **/dev/sdc1** que foi criado nos passos anteriores e o ponto de montagem **/datadrive**. Adicione a linha seguinte para o fim do ficheiro **/etc/fstab** :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2

    Em alternativa, nos sistemas com base em SuSE Linux poderá ter de utilizar um formato ligeiramente diferente:

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    
    >[AZURE.NOTE] O `nofail` opção garante que a VM é iniciado, mesmo se o sistema de ficheiros está danificado ou que o disco não existir durante o arranque. Sem esta opção, que poderá encontrar comportamento conforme descrito no [é possível SSH Linux VM devido a erros FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Agora pode testar o que o sistema de ficheiros é instalado corretamente ao desmontar e, em seguida, remounting, ou seja, o sistema de ficheiros utilizar o exemplo um ponto de montagem `/datadrive` criou nos passos anteriores:

        # sudo umount /datadrive
        # sudo mount /datadrive

    Se o `mount` comando produz um erro, selecione o ficheiro de fstab/etc/para sintaxe correta. Se as unidades de dados adicionais ou a partições são criadas, introduzi-las na/etc/fstab separadamente também.

    Torne a unidade gravável utilizando este comando:

        # sudo chmod go+w /datadrive

>[AZURE.NOTE] Remover subsequentemente um disco de dados sem o editarem fstab pode causar a VM falha arrancar. Se esta é uma ocorrência comuns, a maior parte dos distribuições fornecem quer o `nofail` e/ou `nobootwait` opções de fstab que lhe permitem um sistema para iniciar o mesmo se o disco falhar para montagem na hora de arranque. Documentação a distribuição para mais informações sobre estes parâmetros.

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte de cortar/UNMAP para Linux no Azure
Algumas kernels Linux suportam operações de cortar/UNMAP para eliminar blocos não utilizados no disco. Estas operações são principalmente úteis na armazenamento padrão para informar Azure eliminados páginas já não são válida e podem ser eliminados. Eliminando páginas pode guardar o custo, se criar ficheiros grandes e, em seguida, elimine-as.

Existem duas formas de ativar cortar suportam no seu VM Linux. Como habitualmente, consulte a distribuição para a abordagem recomendada:

- Utilizar o `discard` montagem opção no `/etc/fstab`, por exemplo:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- Em alternativa, pode executar o `fstrim` manualmente do comando da linha de comandos ou adicioná-lo à sua crontab para executar regularmente:

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>Resolução de problemas
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]


## <a name="next-steps"></a>Próximos passos
Pode ler mais sobre como utilizar o seu VM Linux nos seguintes artigos:

- [Como iniciar sessão para uma máquina de virtual com Linux][Logon]

- [Como desanexar um disco a partir de uma máquina de virtual Linux](virtual-machines-linux-classic-detach-disk.md)

- [Utilizar o clip do Azure com o modelo de implementação clássico](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md
