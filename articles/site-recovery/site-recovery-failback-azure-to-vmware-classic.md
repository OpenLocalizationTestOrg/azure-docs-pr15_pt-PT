<properties 
   pageTitle="Falhar anterior VMware de máquinas virtuais e servidores físicos para o site no local | Microsoft Azure"
   description="Saiba mais sobre falhar novamente para o site no local após activação pós-falha de VMware VMs e os servidores físicos para Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Falhas anterior VMware de máquinas virtuais e servidores físicos para o site no local

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-failback-azure-to-vmware.md)
- [Azure Portal clássico](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal clássica Azure (Legado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



Este artigo descreve como falhar novamente Azure máquinas virtuais do Azure para o site no local. Quando estiver pronto para voltar falhar máquinas virtuais VMware ou Windows/Linux servidores físicos após estes tiver falhado sobre a partir do no local do site para Azure utilizando este [tutorial](site-recovery-vmware-to-azure-classic.md), siga as instruções neste artigo.



## <a name="overview"></a>Descrição geral

Este diagrama mostra a arquitetura de reposição de recurso para este cenário.

Utilize esta arquitetura quando o servidor de processo está no local e estiver a utilizar um ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Utilize esta arquitetura quando o servidor de processo está no Azure e tiver uma VPN ou uma ligação de ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Para ver a lista completa de portas e protocolos a reposição de recurso architechture diagrama referir-se para a imagem abaixo

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Eis como funciona a reposição de recurso:

- Depois de ter Ocorreu uma falha ao longo ao Azure, falhar novamente para o seu site no local em alguns fases:
    - **Fase 1**: voltar a proteger o Azure VMs para que começam de replicadas novamente para o VMs VMware em execução no seu site no local. Ativar reprotection move o cursor a VM para um grupo de proteção de reposição de recurso que foi criado automaticamente quando criou originalmente o grupo de proteção de activação pós-falha. Se tiver adicionado o que seu grupo de proteção de activação pós-falha para um plano de recuperação, em seguida, no grupo de proteção de reposição de recurso foi adicionado automaticamente ao plano.  Durante a voltar a proteger Especifique como planear a falha novamente.
    - **Fase 2**: depois do Azure VMs são replicação ao seu site no local, execute uma falha ao longo do falha novamente a partir do Azure.
    - **Fase 3**: depois dos seus dados tem falhado novamente, voltar a proteger os VMs no local que falhou voltar a, para que começam de replicadas para o Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### <a name="failback-to-the-original-or-alternate-location"></a>Reposição de recurso para a localização original ou alternativa

Se tiver falhado sobre uma VM VMware pode falhar voltar à mesma origem VM se ainda existir no local. Neste cenário, apenas as alterações de delta irão falhou novamente. Tenha em atenção que:

- Se tiver falhado sobre servidores físicos, em seguida, reposição de recurso é sempre uma VM VMware novo.
    - Antes de voltar falhar uma máquina física tenha em atenção que
    - Máquina física protegida será voltar atrás como uma máquina de Virtual quando ocorreu uma falha ao longo do novamente a partir do Azure ao VMware
    - Certifique-se-o a descobrir, pelo menos, um modelo global de destino Server juntamente com os anfitriões ESX/ESXi necessários para o qual tem de reposição de recurso.
- Se falhar novamente para a VM original é necessário o seguinte procedimento:
    - Se a VM é gerida por um servidor de vCenter anfitrião ESX de destino de modelo global deve ter acesso ao arquivo de dados VMs.
    - Se a VM for num anfitrião ESX, mas não é gerida pelo vCenter no disco rígido da VM tem de ser num arquivo de dados acessível ao anfitrião de o MT.
    - Se a VM se encontrar um anfitrião ESX e não utiliza vCenter devem concluir deteção do anfitrião de o MT ESX antes de voltar a proteger. Este procedimento se aplica se de que está a falhar demasiado servidores físicos novamente.
    - Outra opção (se existir a VM no local) é eliminá-lo antes de efetuar reposição de uma recurso. Em seguida, reposição de recurso, em seguida, irá criar uma nova VM no anfitrião do mesmo como o anfitrião ESX destino principal.
    
- Quando lhe reposição de recurso para uma localização alternativa os dados será recuperada para o mesmo arquivo de dados e o mesmo anfitrião ESX como que utilizado pelo servidor de destino principal no local. 


## <a name="prerequisites"></a>Pré-requisitos

- Terá de um ambiente do VMware para poder falhar cópia de segurança VMware VMs e servidores físicos. Falha voltar a um servidor físico não é suportada.
- Para poder voltar a falhar, deverá de ter criado uma rede Azure quando configura inicialmente proteção. Reposição de recurso necessita de uma ligação VPN ou ExpressRoute da rede Azure na qual o Azure VMs estão localizados para o site no local.
- Se o VMs que pretende falhar voltar a são geridos por um servidor de vCenter terá Certifique-se tem as permissões necessárias para deteção de VMs nos servidores vCenter. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Se estiverem presentes numa VM instantâneos reprotection irá falhar. Pode eliminar os instantâneos ou discos. 
- Antes que falha novamente terá de criar um número de componentes:
    - **Criar um servidor de processo no Azure**. Este é um VM Azure que terá de criar e manter executar durante a reposição de recurso. Pode eliminar o computador após a conclusão da reposição de recurso.
    - **Criar um servidor de destino principal**: O servidor de destino mestra envia e recebe dados de reposição de recurso. O servidor de gestão que criou no local tem um servidor de destino mestra instalado por predefinição. No entanto, consoante o volume de tráfego de volta falhado poderá ter de criar um servidor de destino mestra separada para a reposição de recurso.
    - Se pretender criar um servidor de destino mestra adicionais em execução no Linux, terá de configurar a VM Linux antes de instalar o servidor de destino principal, conforme descrito abaixo.
- Servidor de configuração de é necessário no local quando o que fazer a reposição de uma recurso. Durante a reposição de recurso, a máquina virtual tem de existir na configuração servidor base de dados, a falhar qual reposição de recurso impossível ser efetuada com êxito. Por isso, certifique-se de que tirar regular cópia de segurança agendada do seu servidor. Em caso de uma falhas, terá de restaurá-lo com o mesmo endereço IP, para que funcione reposição de recurso.

## <a name="set-up-the-process-server-in-azure"></a>Configurar o servidor de processo no Azure

Tem de instalar um servidor de processo no Azure para que o Azure VMs pode enviar os dados para o servidor de destino principal no local. 

1.  No portal do recuperação de sites > **Servidores de configuração** , selecione para adicionar um novo servidor do processo.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Especificar um nome de servidor do processo e introduza um nome e palavra-passe que irá utilizar para ligar para a VM Azure como administrador. No **Servidor de configuração de** selecionar o servidor de gestão de no local e especificar o Azure rede em que o servidor de processo deve ser implementado. Isto deve ser a rede na qual o Azure VMs estão localizados. Especificar um endereço IP exclusivo da sub-rede, selecione e comece a implementação.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

    Uma tarefa para implementar o servidor de processo será acionada

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

    Depois do servidor de processo é implementado no Azure que pode iniciar sessão utilizando as credenciais que especificou. A primeira vez que iniciar sessão a caixa de diálogo de servidor do processo será executada. Escreva o endereço IP do servidor de gestão no local e respetivo frase de acesso. Deixe a predefinição de porta 443. Também pode deixar a predefinição porta 9443 para replicação de dados, a menos que modificou especificamente esta definição, ao configurar o management server no local. 

    >[AZURE.NOTE] O servidor não estar visível em **Propriedades VM**. Só é visível no separador **servidores** no servidor de gestão de ao qual está foi registado. -Pode demorar cerca de 10 a 15 minutos para o servidor de processo que seja apresentado.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurar o destino mestra servidor no local

O servidor de destino mestra recebe os dados de reposição de recurso. Um servidor de destino mestra é instalado automaticamente o servidor de gestão de no local, mas se de que está a falhar novamente muitos dados poderá ter de configurar um servidor de destino mestra adicionais. Faça o seguinte da seguinte forma:

>[AZURE.NOTE] Se pretender instalar um servidor de destino principal no Linux, siga as instruções no procedimento seguinte.

1. Se estiver a instalar o servidor de destino principal no Windows, abra a página de início rápido a partir da VM que estiver a instalar o servidor de destino principal e transferir o ficheiro de instalação para o Assistente de configuração de Unified do Azure Site recuperação.
2. Executar a configuração e na **antes de começar** , selecione **Adicionar os servidores de processo adicionais para dimensionar saída implementação**.
3. Concluir o Assistente da mesma forma que efetuou quando a [configurar o servidor de gestão de](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Na página **Detalhes de configuração de servidor** , especifique o endereço IP do servidor este destino principal e uma frase de acesso para aceder a VM.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurar uma VM Linux como o servidor de destino principal
Para configurar o servidor de gestão com o servidor de destino principal como uma VM Linux terá de instalar o cêntimos) S 6.6 mínimas sistema, operativo obter os IDs de SCSI para cada disco rígido do SCSI, instalar alguns pacotes adicionais e aplicar algumas alterações personalizadas.

#### <a name="install-centos-66"></a>Instalar CentOS 6.6

1.  Instale o sistema operativo mínimo CentOS 6.6 no servidor de gestão de VM. Manter o ISO numa unidade de DVD e o sistema de arranque. Ignorar o teste de multimédia, selecione-nos em inglês no idioma, selecione **Dispositivos de armazenamento básica**, verifique que a unidade de disco rígido não ter quaisquer dados importantes e clique em **Sim**, eliminar quaisquer dados. Introduza o nome do anfitrião do servidor de gestão e selecione adaptador de rede de servidor.  Na caixa de diálogo **Editar sistema** selecione** ligar-se automaticamente** e adicionar um endereço IP estático, rede e as definições de DNS. Especificar um fuso horário e uma palavra-passe de raiz para aceder ao servidor de gestão. 
2.  Quando lhe for perguntado o tipo de instalação gostaria selecione **Criar esquema personalizado** como a partição. Depois de clicar em **seguinte** selecione **Libertar** e clique em criar. Criar **/**, **/var/crash** e **/ home a partições** com **FS tipo:** **ext4**. Criar o partiçao trocar como **FS tipo: trocar**.
3.  Se forem encontrados dispositivos pré-existentes que será apresentada uma mensagem de aviso. Clique em **Formatar** para formatar a unidade com as definições de partição. Clique em **Alterar para o disco de escrita** para aplicar as alterações de partição.
4.  Selecione **instalar carregador de arranque** > **seguinte** para instalar o carregador de arranque na partição de raiz.
5.  Quando a instalação estiver concluída clique em **reiniciar**.


#### <a name="retrieve-the-scsi-ids"></a>Obter os IDs SCSI

1. Após a instalação obtenha os IDs de SCSI para cada disco rígido do SCSI na VM. Para efetuar este encerrar o servidor de gestão de VM, nas propriedades de VM no VMware com o botão direito na entrada VM > **Editar definições de** > **Opções**.
2. Selecione **Avançadas** > **Geral item** e clique em **Parâmetros de configuração**. Esta opção estará de-active quando o computador está a ser executado. Para o tornar ativa o computador tem de ser encerrar.
3. Se a linha **disco. EnableUUID** existe certificar-se de que o valor é definido como **Verdadeiro** (sensível a maiúsculas e minúsculas). Se já não pode cancelar e testar o comando SCSI dentro de um sistema operativo convidado após é iniciado. 
4.  Se a linha não existentes clique em **Adicionar linha** – e adicioná-lo com o valor **Verdadeiro** . Não utilize aspas.

#### <a name="install-additional-packages"></a>Instalar pacotes adicionais

Terá de transferir e instalar alguns pacotes adicionais. 

1.  Certifique-se que o servidor de destino mestra está ligado à internet.
2.  Executar este comando para transferir e instalar 15 pacotes a partir do repositório de CentOS: **# yum instalar – y xfsprogs perl lsscsi rsync wget kexec-ferramentas**.
3.  Se as máquinas origem proteger estiver a executar o Linux co Reiser ou XFS sistema para o dispositivo de raiz ou de arranque, de ficheiros, em seguida, deve transferir e instalar pacotes adicionais da seguinte forma:

    - # <a name="cd-usrlocal"></a>/usr/local de CD
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>reiserfs de kmod reiserfs-0,0 1.el6.elrepo.x86_64.rpm da – ivh rotações/minuto-utilitários-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rotações/minuto – ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Aplicar alterações personalizadas

O seguinte procedimento para aplicar as alterações personalizadas depois de fazer concluir os passos de instalação pós e os pacotes de instalado:

1.  Copie o RHEL 6-64 Unified Agent binário para a VM. Execute este comando para untar o ficheiro binário: **alcatrão – zxvf <file name> **
2.  Execute este comando para conceder permissões: **# chmod 755./ApplyCustomChanges.sh**
3.  Executar o script: **#./ApplyCustomChanges.sh**. Uma vez, só deve executar o script. Reinicie o servidor após o script executada com sucesso.


## <a name="run-the-failback"></a>Executar a reposição de recurso

### <a name="reprotect-the-azure-vms"></a>Voltar a proteger os VMs Azure

1.  No portal do recuperação de sites > separador **máquinas** selecione a VM que é foi Ocorreu uma falha ao longo do e clique em **proteger novamente**.
2.  No **Modelo global de servidor de destino** e no **Processo Server** , selecione o servidor de destino principal no local e o servidor de processo Azure VM.
3.  Selecione a conta que tiver configurado para ligar para a VM.
4.  Selecione a versão de reposição de recurso do grupo de proteção. Por exemplo, se a VM estiver protegida no PG1, em seguida, que terá de selecionar PG1_Failback.
5.  Se pretender recuperar para uma localização alternativa, selecione a unidade de retenção e arquivo de dados configurado para o servidor de destino principal. Quando falha novamente para o site no local a VMs VMware no plano de proteção de reposição de recurso irá utilizar o arquivo de dados mesmo como o servidor de destino principal. Se pretender recuperar a réplica Azure VM ao mesmo no local VM, em seguida, a VM no local já deverá estar no arquivo de dados mesmo que o servidor de destino principal. Se não existe nenhuma VM no local será criado um novo durante reprotection.

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.  Depois de clicar em **OK** para começar a reprotection uma tarefa começa a replicação a VM a partir do Azure para o site no local. Pode controlar o progresso no separador **tarefas** .

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Executar uma activação pós-falha ao site no local

Após reprotection a VM é movida para a versão de reposição de recurso do seu grupo de proteção e é automaticamente adicionada ao plano de recuperação que utilizou para a activação pós-falha ao Azure se existir.

1.  Na página **Plano de recuperação** selecione o plano de recuperação que contém o grupo de reposição de recurso e clique em **activação pós-falha** > **Activação pós-falha não planeado**.
2.  Na **Confirmar activação pós-falha** Verifique a direção de activação pós-falha (para Azure) e selecione o ponto de recuperação que pretende utilizar para o activação pós-falha (mais recente). Caso a ativado **Multi VM** quando configurou propriedades de replicação pode recuperar para a aplicação mais recente ou um ponto de recuperação falha consistentes. Clique na marca de verificação para iniciar a activação pós-falha.
3.  Durante a activação pós-falha recuperação do Site irá encerrar o VMs Azure. Depois de verificar que esse reposição de recurso foi concluída, tal como é esperado pode pode verificar que a VMs Azure ter sido encerradas conforme esperado.

### <a name="reprotect-the--on-premises-site"></a>Voltar a proteger o site no local

Após a conclusão de reposição de recurso os seus dados serão novamente no site no local, mas não protegidos. Para iniciar a replicação de Azure novamente, efetue o seguinte procedimento:

1.  No portal do recuperação de sites > **máquinas** selecione de separador as VMs que tenham falhado ao criar de segurança e clique em **proteger novamente**. 
2.  Depois de confirmar que a replicação para Azure está a funcionar como esperado, no Azure pode eliminar o VMs Azure (atualmente não a ser executado) que foram falhou novamente.


### <a name="common-issues-in-failback"></a>Problemas comuns no reposição de recurso

1. Se executar a deteção de vCenter utilizador só de leitura e proteger máquinas virtuais,-lo com êxito e funciona activação pós-falha. No momento da voltar a proteger, irá falhar uma vez que não pode ser descobertas a datastores. Como um sintoma não verá datastores listado protegendo novamente. Para resolver esta questão, pode atualizar a credencial vCenter com adequado conta que possua permissões e volte a tentar a tarefa. [Saiba mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Quando lhe reposição de recurso uma VM Linux e executá-la no local, verá que o pacote do Gestor de rede é desinstalado a partir do computador. Isto acontece porque quando a VM for recuperada no Azure, o pacote do Gestor de rede é removido.
3. Quando uma VM está configurada com endereço IP estático e Ocorreu uma falha ao longo do Azure, o endereço IP for adquirido através de DHCP. Quando falham ao longo do anterior para no local, continua a VM utilizar DHCP para adquirir o endereço IP. É necessário manualmente início de sessão para o computador e configurar o endereço IP de volta para o endereço estático se necessário.
4. Se estiver a utilizar a edição gratuita ESXi 5.5 ou vSphere 6 hipervisor gratuito edition, activação pós-falha seria bem sucedida, mas não será bem sucedida a reposição de recurso. Irá ned atualizar para um dos licença de avaliação para ativar a reposição de recurso.

## <a name="failing-back-with-expressroute"></a>Retroceder falhar com ExpressRoute

Pode falhar novamente ao longo de uma ligação VPN ou Azure ExpressRoute. Se pretender utilizar a nota ExpressRoute o seguinte:

- ExpressRoute deve ser configurada na rede virtual Azure para o qual a origem de falhas máquinas sobre e, no qual VMs Azure estão localizados depois da activação pós-falha ocorre.
- Dados são replicados numa conta de armazenamento Azure num ponto final de público. Deve configurar efectuado público no ExpressRoute com o Centro de dados de destino para a replicação de recuperação de Site utilizar ExpressRoute.



