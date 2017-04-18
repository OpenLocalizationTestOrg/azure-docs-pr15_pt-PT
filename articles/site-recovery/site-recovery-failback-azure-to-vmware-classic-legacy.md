<properties 
   pageTitle="Falhar anterior VMware de máquinas virtuais e servidores físicos a partir do Azure para VMware (Legado) | Microsoft Azure" 
   description="Este artigo descreve como falhar novamente uma máquina de virtual VMware que é foram replicada para Azure com Azure recuperação de Site." 
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
   ms.workload="storage-backup-recovery" 
   ms.date="10/05/2016"
   ms.author="ruturajd@microsoft.com"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>Falhar anterior VMware de máquinas virtuais e servidores físicos a partir do Azure para VMware com Azure Site recuperação (Legado)

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-failback-azure-to-vmware.md)
- [Azure Portal clássico](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal clássica Azure (Legado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)


O serviço de recuperação de sites do Azure contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou para um centro de dados secundária no local. Para obter uma descrição geral rápida ler [o que é o Azure recuperação de Site?](site-recovery-overview.md)

## <a name="overview"></a>Descrição geral

Este artigo descreve como falhar anterior VMware de máquinas virtuais e Windows/Linux servidores físicos a partir do Azure ao seu site no local, depois de ter replicada a partir do seu site no local para Azure.

>[AZURE.NOTE] Este artigo descreve um cenário de legado. Apenas deve utilizar as instruções neste artigo se replicadas para Azure utilizando [estas instruções legadas](site-recovery-vmware-to-azure-classic-legacy.md). Se lhe Configura replicação, utilizando a [implementação melhorada](site-recovery-vmware-to-azure-classic-legacy.md) , em seguida, siga as instruções [neste](site-recovery-failback-azure-to-vmware-classic.md) artigo para voltar a falhar. 


## <a name="architecture"></a>Arquitetura

Este diagrama representa o cenário activação e reposição de recurso. As linhas azuis são as ligações utilizadas durante activação pós-falha. As linhas vermelhas são as ligações utilizadas durante a reposição de recurso. As linhas com setas aceda através da internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>Antes de começar 

- Deverá ter Ocorreu uma falha ao longo do seu VMware VMs ou servidores físicos e devem estar em execução no Azure.
- Note que apenas pode falhar anterior VMware de máquinas virtuais e Windows/Linux físicos servidores do Azure máquinas virtuais de VMware no site principal no local.  Se está a falhar novamente uma máquina física, activação pós-falha ao Azure irá convertê-lo para um VM Azure e reposição de recurso para VMware irá convertê-lo para um VM VMware.

Eis como configurar a reposição de recurso:

1. **Configurar o componentes de reposição de recurso**: terá de configurar um servidor de vContinuum no local e aponte-lo para o servidor de configuração VM no Azure. Também irá configurar o um servidor de processo como um VM Azure para enviar dados novamente para o servidor de destino principal no local. Registe-se do servidor de processo com o servidor de configuração resolvido o activação pós-falha. Instalar um servidor de destino principal no local. Se precisar de um servidor de destino principal do Windows-está configurado automaticamente quando instala vContinuum. Se precisar de Linux terá de configurá-lo manualmente num servidor separado.
2. **Ativar a proteção e reposição de recurso**: depois de ter configurado os componentes, no vContinuum terá activar proteção para Ocorreu uma falha ao longo do Azure VMs. Vai executar uma verificação de disponibilidade nas VMs e executar uma activação pós-falha a partir do Azure ao seu site no local. Após termina de reposição de recurso voltar a proteger máquinas no local para que começam de replicadas para o Azure.



## <a name="step-1-install-vcontinuum-on-premises"></a>Passo 1: Instalar vContinuum no local

Terá de instalar um servidor de vContinuum no local e aponte-lo para o servidor de configuração.

1.  [Transferir vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Em seguida, transfira a versão [vContinuum atualizar](http://go.microsoft.com/fwlink/?LinkID=533813) .
3. Instale a versão mais recente do vContinuum. Na página de **boas-vindas** , clique em **seguinte**.
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  Na primeira página do assistente, especifique o endereço IP do servidor CX e a porta do servidor CX. Selecione **utilizar HTTPS**.

    ![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Localize o servidor de configuração o endereço IP no separador **Dashboard** do servidor de configuração VM no Azure.
    ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Localize o servidor de configuração HTTPS pública porta no separador **pontos finais** do servidor de configuração VM no Azure.

    ![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. Na página **Detalhes da frase de acesso de CS** especifique a frase de acesso que anotou para baixo quando se registou o servidor de configuração. Se não se lembrar-dar entrada **c:\\Program Files (x86)\\InMage sistemas\\privada\\connection.passphrase** no servidor de configuração VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  Na página **Selecionar a localização de destino** especificar onde pretende instalar o servidor de vContinuum e clique em **instalar**.

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Depois de concluir a instalação, pode iniciar o vContinuum.
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## <a name="step-2-install-a-process-server-in-azure"></a>Passo 2: Instalar um servidor de processo no Azure 

Tem de instalar um servidor de processo no Azure para que o VMs no Azure podem enviar os dados para um servidor de destino principal no local. 

1.  Na página **Configuração de servidores** no Azure, selecione para adicionar um novo servidor do processo.

    ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Especificar um servidor de processo nome e um nome e palavra-passe para ligar à máquina virtual como administrador. Selecione o servidor de configuração para o qual pretende registar o servidor do processo. Isto deve ser o mesmo servidor que está a utilizar para proteger e falhar ao longo do seu máquinas virtuais.
3.  Especificar o Azure rede em que o servidor de processo deve ser implementado. Deverá ser a mesma rede que o servidor de configuração. Especificar uma sub-rede de endereço seleccionado IP exclusiva e comece a implementação.

    ![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Uma tarefa é acionada para implementar o servidor do processo.

    ![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Depois do servidor de processo é implementado no Azure pode iniciar sessão no servidor utilizando as credenciais que especificou. Registe-se o servidor de processo da mesma forma que se registou o servidor de processo no local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] Os servidores de registado durante a reposição de recurso não estará visíveis em Propriedades de VM em recuperação de sites. Só estão visíveis no separador **servidores** do servidor de configuração para o qual estão registados. Pode demorar cerca de 10 a 15 minutos até que o servidor de processo é apresentado no separador.


## <a name="step-3-install-a-master-target-server-on-premises"></a>Passo 3: Instalar um servidor de destino principal no local

Dependendo do seu sistema operativo do máquinas virtuais de origem, tem de instalar uma Linux ou um servidor de destino principal do Windows no local.

### <a name="deploy-a-windows-master-target-server"></a>Implementar um servidor de destino principal do Windows

Já está agrupado um alvo principal do Windows com a configuração de vContinuum. Quando instala o vContinuum, um servidor principal também é implementado no mesmo computador e registado para o servidor de configuração.

1.  Para começar a implementação, crie uma vazia no local no anfitrião do ESX para o qual pretende recuperar os VMs a partir do Azure de computador.

2.  Certifique-se de que não existem, pelo menos, dois discos anexados a VM – um é utilizado para o sistema operativo e o outro é utilizado para a unidade de retenção.

3.  Instale o sistema operativo.

4.  Instale o vContinuum no servidor. Isto também conclui a instalação do servidor de destino principal.

### <a name="deploy-a-linux-master-target-server"></a>Implementar um servidor de destino mestra Linux

1.  Para começar a implementação, crie uma vazia no local no anfitrião do ESX para o qual pretende recuperar os VMs a partir do Azure de computador.

2.  Certifique-se de que não existem, pelo menos, dois discos anexados a VM – um é utilizado para o sistema operativo e o outro é utilizado para a unidade de retenção.

3.  Instale o sistema operativo Linux. O sistema de destino mestra Linux não deve utilizar LVM para espaços de armazenamento de raiz ou de retenção. Um servidor de destino mestra Linux está configurado para evitar a deteção de partições/discos LVM por predefinição.
4.  Partições que pode criar:

    ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Proceder a registar abaixo passos de instalação antes de iniciar a instalação de destino principal.


#### <a name="post-os-installation-steps"></a>Publicar OS passos de instalação

Para obter os identificadores de SCSI para cada um no disco rígido de SCSI numa máquina virtual Linux, ativar o parâmetro "disco. EnableUUID = TRUE "da seguinte forma:

1. Encerre o seu máquina virtual.
2. A entrada VM no painel à esquerda com o botão direito > **Editar definições**.
3. Clique no separador **Opções** . Selecione **Avançadas\>item geral** > **Parâmetros de configuração**. A opção de **Parâmetros de configuração** só está disponível quando o computador está desligado.

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Verifica se uma linha com **disco. EnableUUID** existir. Se e está definido como **Falso** , em seguida, defina-lo como **Verdadeiro** (não sensível a maiúsculas). Se existir e está definido como VERDADEIRO, clique em **Cancelar** e testar o comando SCSI dentro do sistema operativo convidado depois de ter sido iniciado para cima. Se não existe clique em **Adicionar linha**.
5. Adicione disco. EnableUUID na coluna **nome** . Defina o valor como verdadeiro. Não adicione os valores acima juntamente com aspas.

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>Transfira e instale os pacotes adicionais

Nota: Certifique-se que o sistema tem ligação à internet antes de transferir e instalar pacotes de adicionais.

\#instalar o yum -y xfsprogs perl lsscsi rsync wget kexec-ferramentas

Este comando estes 15 pacotes as transferências do repositório de CentOS 6.6 e instala-las:

BC-1.06.95-1.el6.x86\_rotações/de 64. minuto

busybox-1.15.1-20.el6.x86\_rotações/de 64. minuto

elfutils-efectuar 0.158 3.2.el6.x86\_rotações/de 64. minuto

kexec-ferramentas 2.0.0 280.el6.x86\_rotações/de 64. minuto

lsscsi-0.23-2.el6.x86\_rotações/de 64. minuto

e 2.03-lzo-3.1.el6\_5.1.x86\_rotações/de 64. minuto

Perl-5.10.1-136.el6\_6.1.x86\_rotações/de 64. minuto

Perl-módulo-incorporável-3.90-136.el6\_6.1.x86\_rotações/de 64. minuto

Perl-vagem-sai-pontos 1.04-136.el6\_6.1.x86\_rotações/de 64. minuto
    
Perl-vagem-simples-3.13-136.el6\_6.1.x86\_rotações/de 64. minuto

Perl-efectuar 5.10.1 136.el6\_6.1.x86\_rotações/de 64. minuto

Perl versão-0,77 136.el6\_6.1.x86\_rotações/de 64. minuto

rsync-3.0.6-12.el6.x86\_rotações/de 64. minuto

1.el6.x86 chamativas 1.1.0\_rotações/de 64. minuto

wget-1.12-5.el6\_6.1.x86\_rotações/de 64. minuto

Nota: Se o computador de origem utiliza Reiser ou XFS sistema de ficheiros para o dispositivo de raiz ou de arranque, em seguida, seguintes pacotes devem ser transferidas e instaladas no Linux mestra destino antes de proteção.

\#/usr/local de CD

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\#rotações/minuto - ivh kmod-reiserfs-0,0-1.el6.elrepo.x86\_64. rotações/minuto reiserfs-utilitários-3.6.21-1.el6.elrepo.x86\_rotações/de 64. minuto

\#wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\#rotações/minuto - ivh xfsprogs-3.1.1-16.el6.x86\_rotações/de 64. minuto

#### <a name="apply-custom-configuration-changes"></a>Aplicar alterações de configuração personalizado

Antes de aplicar estas alterações Certifique-se de que concluiu a secção anterior, em seguida, siga estes passos:


1. Copie o RHEL 6-64 Unified Agent binário para o sistema operativo utilizado recentemente criado.

2. Execute este comando para untar o ficheiro binário: **alcatrão - zxvf \<nome de ficheiro\> **

3. Execute este comando para conceder permissões: \# **chmod 755./ApplyCustomChanges.sh**

4. Executar o script: ** \# ./ApplyCustomChanges.sh**. Execute o script apenas uma vez no servidor. Reinicie o servidor após o script é executado.



### <a name="install-the-linux-server"></a>Instalar o servidor Linux


1. [Transferir](http://go.microsoft.com/fwlink/?LinkID=529757) o ficheiro de instalação.
2. Copie o ficheiro para o destino de modelo global de Linux virtual machine utilizar um utilitário de cliente sftp da sua escolha. Em alternativa pode iniciar sessão máquina Linux destino mestra virtual e utilizar wget para transferir o pacote de instalação da hiperligação fornecida
3. Iniciar sessão a Linux destino mestra servidor máquina virtual a utilizar um ssh cliente da sua preferência
4. Se estiver ligado à rede Azure implementou do seu servidor de destino mestra Linux através de uma ligação VPN, em seguida, utilize o endereço IP interno para o servidor que pode encontrar no separador de **Dashboard** de máquina virtual e porta 22 para ligar para o servidor utilizando seguro da Shell de destino mestra Linux.
5. Se estiver a estabelecer ligação ao servidor de destino principal de Linux através de uma ligação à internet público utilizar endereço IP virtual público do servidor de destino principal de Linux (a partir do separador de **Dashboard** máquinas virtuais) e o ponto final público criado ssh iniciar sessão para o servder Linux.
6. Extrair os ficheiros de arquivo de alcatrão do installer de destino mestra Server gzipped Linux ao executar: *"alcatrão – xvzf Microsoft-recuperação automática\_UA\_8.2.0.0\_RHEL6 64\"* do diretório que contém o ficheiro de instalação.

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Se extraídas o installer ficheiros para um directório diferente, mude para o directório para que os conteúdos do tar arquivar foram extraídos. A partir de executar este caminho de diretório "sudo. / install.sh".

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Quando lhe for pedido para escolher uma função primária selecione **2 (destino do modelo global)**. Deixe as outras opções de instalação interativos nos valores predefinidos.
9. Aguarde para continuar a instalação e a Interface de configuração do anfitrião apareça. O utilitário de configuração do anfitrião para o sarget mestra Linux servidor é um utilitário de linha de comandos. Não redimensionar o ssh janela utilitário de cliente. Utilize as teclas de seta para selecionar a opção **Global** e prima ENTER no teclado.

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. No campo **IP** introduza o endereço de IP interno do servidor de configuração (pode encontrá-lo no separador **Dashboard** do servidor de configuração VM) e prima ENTER. Na **porta** introduza **22** e prima ENTER. 
11.  Deixe **Utilizar HTTPS** como **Sim**e prima ENTER.
12.  Introduza a frase de acesso que foi gerado no servidor de configuração. Se estiver a utilizar um cliente para a partir de um computador Windows para ssh à máquina de virtual Linux pode utilizar Shift + Insert para colar o conteúdo da área de transferência. Copie a frase de acesso para a área de transferência local utilizando Ctrl-C e utilize Shift + Insert para colá-lo. Prima ENTER.
13.  Utilize a tecla de seta para a direita para navegar para sair e prima ENTER. Aguarde concluir a instalação.

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Se, por alguma razão que falhou registar o seu servidor de destino mestra Linux para o servidor de configuração que pode fazer por isso, volte ao executar o anfitrião do utilitário de configuração de /usr/local/ASR/Vx/bin/hostconfigcli (primeiro terá de definir permissões de acesso para este diretório ao executar chmod como um utilizador super).


#### <a name="validate-master-target-registration"></a>Validar o registo de destino principal

Validar a que o servidor de destino mestra foi registado com êxito para o servidor de configuração num cofre Azure recuperação de sites > **Servidor de configuração de** > **Detalhes do servidor**.

>[AZURE.NOTE] Depois de registar o servidor de destino principal, se receber erros de configuração de que a máquina virtual poderá ter sido eliminada a partir do Azure ou que os pontos finais não estiverem configurados corretamente, isto acontece porque apesar de configuração de destino mestra é detectada pelo Azure dndpoints quando o modelo global de destino é implementado no Azure, esta não é verdadeira para um local principal destino server no local. Isto não afeta a reposição de recurso e pode ignorar estes erros. 



## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>Passo 4: Proteger as máquinas virtuais para o site no local

Tem de proteger VMs para o site no local antes que falha novamente.

### <a name="before-you-begin"></a>Antes de começar

Quando uma VM é Ocorreu uma falha ao longo do Azure, adiciona uma unidade extra temp para ficheiro da página. Esta é uma unidade extra que não é necessária, normalmente, por sua falha ao longo do VM uma vez que pode já ter uma unidade dedicada para o ficheiro de página. Antes de começar inversa proteção das máquinas virtuais, é necessário para se certificar de que esta unidade é disponibilizada offline para que não obter protegido. Faça o seguinte da seguinte forma:

1.  Abra a gestão de computador e selecione gestão de armazenamento para que lista os discos online e anexado ao computador.
2.  Selecione o disco temporário ligado ao computador e escolha para colocá-lo offline. 

### <a name="protect-the-vms"></a>Proteger os VMs

1. No portal do Azure, verifique os Estados de máquina virtual para se certificar de que está a falhou sobre.

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Inicie o vContinuum no seu computador.

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Clique em **Novo proteção** e selecione o tipo de sistema de operação, o 

4.  Na janela da nova que abrir, selecione o **tipo de SO** > **Obter detalhes** para VMs que pretende voltar a falhar. **Detalhes do servidor principal**, identificar e selecione as máquinas virtuais que pretende proteger. VMs são listadas sob o nome do anfitrião vCenter que estavam no antes de activação pós-falha.
5.  Quando seleciona uma máquina virtual para proteger (e -lo já falhou sobre para Azure) numa janela de pop-up fornece duas entradas para a máquina virtual. Isto acontece porque o servidor de configuração Deteta duas instâncias das máquinas virtuais registadas para a mesma. Tem de remover a entrada para a VM no local para que pode proteger a VM correta. Para identificar a entrada Azure VM correcta aqui, pode iniciar sessão para a VM Azure e aceda a c:\Programas ficheiros (x86) \Microsoft Azure Site Recovery\Application Data\etc. No drscout.conf ficheiro, identifique o ID do anfitrião. Na caixa de diálogo vContinuum, mantenha a entrada para o qual encontrado o ID do anfitrião na VM. Elimine todas as outras entradas. Para selecionar a VM correta pode referir-se para o seu endereço IP. O IP endereço intervalo no local será a VM no local.

    ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. No servidor vCenter pare a máquina virtual. Também pode eliminar o VMs no local.
7. Em seguida, especifique o servidor de MT de no local para o qual pretende proteger os VMs. Para fazer isto, ligar ao servidor vCenter ao qual pretende falhar novamente.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Selecione o servidor de destino principal com base no anfitrião do aos quais que pretende recuperar a VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Forneça a opção de replicação para cada uma das máquinas virtuais. Para fazer isto é necessário selecionar o arquivo de dados de lado recuperação ao qual os VMs serão recuperados. A tabela resume as diferentes opções que tem de fornecer para cada VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

    **Opção** | **Opção recomendada valor**
    ---|---
    Endereço IP do servidor de processo | Selecionar o servidor de processo implementado no Azure
    Tamanho de retenção em MB| 
    Valor de retenção | 1
    Dias/horas | Dias
    Intervalo de consistência | 1
    Selecione arquivo de dados de destino | Ao arquivo de dados disponível no site de recuperação. O arquivo de dados deverá ter espaço suficiente e estar disponíveis ao anfitrião do ESX no qual pretende restaurar a máquina virtual.


10. Configure as propriedades que irá adquirir a máquina virtual após activação pós-falha ao site no local. As propriedades estão resumidas na tabela seguinte.

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    **Propriedade** | **Detalhes**
    ---|---
    Configuração de rede| Para cada foi detectado um adaptador de rede, selecioná-la e clique em **Alterar** para configurar o endereço IP de reposição de recurso para a máquina virtual. 
    Configuração do hardware| Especifique a memória e CPU para a VM. Definições podem ser aplicadas a todas as VMs está a tentar proteger. Para identificar os valores corretos de memória e CPU, pode referir-se para o tamanho de papel IAAS VMs e ver o número de núcleos e memória atribuída.
    Nome a apresentar | Depois de reposição de recurso para no local, pode mudar o nome as máquinas virtuais como irá aparecem no inventário vCenter. O nome predefinido é o nome de anfitrião do computador de máquina virtual. Para identificar o nome VM, pode consultar a lista de VM no grupo proteção.
    Configuração NAT | Explicados detalhadamente abaixo

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>Configurar definições de NAT

1. Para permitir proteção das máquinas virtuais, dois canais de comunicação tem de ser estabelecida. O primeiro canal é entre a máquina virtual e o servidor do processo. Este canal recolhe os dados a partir da VM e envia-o para o servidor de processo que, em seguida, envia os dados para o servidor de destino principal. Se o servidor de processo e a máquina virtual para ser protegidos estiverem na mesma rede virtual Azure, em seguida, não precisa de utilizar as definições NAT. Caso contrário, especifique as definições NAT. Ver o endereço IP público do servidor processo no Azure. 

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. O segundo canal é entre o servidor de processo e o servidor de destino principal. A opção para utilizar NAT ou não depende se estiver a utilizar uma ligação VPN com base ou comunicar através da internet. Não selecione NAt se estiver a utilizar o VPN, mas apenas se estiver a utilizar uma ligação à internet.

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Se ainda não o tenha eliminado máquinas virtuais no local conforme especificado e se o arquivo de dados que está a falhar voltar a ainda estiver contiver o VMDK antigo terá de se certificar de que a reposição de recurso que VM é criado num novo local. Para fazer isto selecionar as definições **Avançadas** e especifique uma pasta alternativa para restaurar nas **Definições de nome de pasta**. Deixe as outras opções com as predefinições. Aplica as definições de nome de pasta para todos os servidores de.

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Execute uma verificação de preparação para se certificar de que as máquinas virtuais estão prontas para ser protegidos para o local. 

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Aguarde para o mesmo concluir. Se a com êxito todos os VMs pode especificar um nome para o plano de proteção. Em seguida, clique em **proteger** para começar.

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. Pode monitorizar o progresso no vContinuum.

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Após o passo que concluir a **Ativação do plano de proteção** pode monitorizar proteção VM no portal do recuperação de sites.

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Pode ver o estado exato clicando na VM e monitorizar o progresso.

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>Preparar o plano de reposição de recurso

Pode preparar um plano de reposição de recurso com vContinuum, de modo a que pode ser falha à aplicação novamente o site no local em qualquer altura. Estes planos de recuperação são muito semelhantes às planos de recuperação em recuperação de sites.

1.  Iniciação vContinuum e selecione **Gerir planos** > **recuperar.** Pode ver da lista de todos os planos que foram utilizados para falhar ao longo do VMs. Pode utilizar os planos do mesmo para recuperar.

    ![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Selecione o plano de proteção e todos os VMs que pretende recuperar no mesmo. Quando seleciona cada VM pode ver mais detalhes, incluindo o servidor ESX de destino e o disco de VM de origem. Clique em **seguinte** para iniciar o assistente recuperar e selecione VMs que pretende recuperar.

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Pode recuperar com base em várias opções, mas recomendamos que utilize a **Etiqueta mais recente** e selecione **aplicar para todos os VMs** para se certificar de que os dados mais recentes máquina virtual serão utilizados.
4. Executar o **verificação da preparação.** Isto vai verificar se os parâmetros direita são configurados para activar a recuperação VM. Clique em **seguinte** se todas as verificações estiverem bem sucedidas. Se não verifique o registo e resolver os erros.

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  Configuração de **VM** Certifique-se de que as definições recuperação estão definidas corretamente. Pode alterar as definições de VM se precisar de.

    ![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Reveja a lista de máquinas virtuais que serão recuperados e especificar uma ordem de recuperação. Tenha em atenção que VMs estão listados utilizando o nome de anfitrião do computador. Poderá ser difícil mapear o nome de anfitrião do computador para a máquina virtual.
Para mapear os nomes, aceda a máquinas virtuais **Dashboard** no Azure e verifique o nome do anfitrião VM.

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Especifique um nome de plano e selecione **recuperar mais tarde**. Recomendamos que para recuperar mais tarde, uma vez que a proteção inicial poderão não estar concluída. 
11. Clique em **recuperar** para guardar o plano ou acionar a recuperação ter caso tenha selecionado para recuperar agora e não mais tarde. Pode verificar o estado de recuperação para ver se o plano do foi guardado.

    ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>Recuperar máquinas virtuais

Depois do plano do foi criado, pode recuperar as máquinas virtuais. Antes de verificar que as máquinas virtuais tiver concluído a sincronização. Se o estado de replicação mostra OK foi concluída a proteção e o limiar RPO tenha sido respeitado. Pode verificar o estado de funcionamento nas propriedades VM.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Desative as máquinas virtuais Azure antes de iniciar a recuperação. Isto garante não haja nenhuma cérebro dividida e que os utilizadores só irão aceder uma cópia da aplicação. 


1.  Inicie o plano guardado. No vContinuum selecione **Monitor** os planos. Lista todos os planos que tem sido executados.

    ![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Selecione o plano de **recuperação** e clique em **Iniciar**. Pode monitorizar recuperação. Depois de tem sido desligados VMs, pode ligar aos mesmos no vCenter.

    ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>Proteger e o Azure novamente depois de reposição de recurso

Após ter sido concluída a reposição de recurso que irá provavelmente pretende proteger as máquinas virtuais novamente. Faça o seguinte da seguinte forma:

1.  Certifique-se a máquinas virtuais no local está a funcionar e que aplicações estão acessíveis.
2.  No portal do Azure recuperação de sites, selecione as máquinas virtuais e eliminá-los. Selecione esta opção para desativar a proteção das máquinas virtuais. Este procedimento assegura que os VMs mais não estão protegidas.
3.  No Azure eliminar a falha ao longo do Azure máquinas virtuais
4.  Elimine a máquina virtual antiga no vSpehere. Estes são os VMs que anteriormente Falha ao longo ao Azure.
5.  No portal do Site recuperação Protege os VMs que tiver falhado recentemente sobre. Depois de estes estiver protegidas pode adicioná-los para um plano de recuperação.
 
## <a name="next-steps"></a>Próximos passos



- [Leia sobre](site-recovery-vmware-to-azure-classic.md) replicadas máquinas virtuais de VMware e servidores físicos para o Azure utilizando a implementação melhorada.

 
