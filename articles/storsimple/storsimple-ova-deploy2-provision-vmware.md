<properties
   pageTitle="Implementar StorSimple Virtual matriz - disposição no VMware"
   description="Neste tutorial segundo série de implementação de matriz Virtual StorSimple envolve aprovisionamento um dispositivo virtual na VMware."
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
   ms.workload="NA"
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-vmware"></a>Implementar a matriz Virtual StorSimple - aprovisionar uma matriz no VMware Virtual

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Descrição geral 
Neste tutorial aprovisionamento aplica-se ao lançamento de disponibilidade geral (das versões DG) de Março de 2016 StorSimple matrizes Virtual (também conhecido como StorSimple os dispositivos virtuais no local ou dispositivos virtuais StorSimple) em execução. Neste tutorial descreve como pode aprovisionar e ligar a uma matriz Virtual StorSimple num sistema anfitrião a executar o VMware ESXi 5.5 e acima. Este artigo aplica-se para a implementação de matrizes Virtual StorSimple no portal clássico Azure, bem como a nuvem do Microsoft Azure administração pública.

Irá precisar de privilégios de administrador para aprovisionar e ligar a um dispositivo virtual. A configuração inicial e aprovisionamento pode demorar cerca de 10 minutos para concluir.


## <a name="provisioning-prerequisites"></a>Pré-requisitos de aprovisionamento

Aqui irá encontrar os pré-requisitos aprovisionamento de um dispositivo virtual num sistema anfitrião a executar o VMware ESXi 5.5 e acima.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço do Gestor de StorSimple

Antes de começar, certifique-se de que:

-   Concluiu todos os passos no [artigo preparar o portal para StorSimple Virtual matriz](storsimple-ova-deploy1-portal-prep.md).

-   Tiver transferido a imagem do dispositivo virtual para VMware a partir do portal Azure. Para obter mais informações, consulte o artigo [passo 3: Transferir a imagem do dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple 

Antes de implementar um dispositivo virtual, certifique-se de que:

-   Tiver acesso a um sistema anfitrião a executar o Hyper-V (2008 R2 ou posterior) que pode ser utilizado para uma disposição um dispositivo.

-   É possível dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual sistema anfitrião:

    -   Um mínimo de 4 núcleos.

    -   Pelo menos, 8 GB de RAM.

    -   Uma interface de rede.

    -   Um disco virtual 500 GB para os dados de sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no Centro de dados 

Antes de começar, certifique-se de que:

-   Ter revisto os requisitos de rede para implementar um dispositivo virtual StorSimple e configurado a rede do Centro de dados de acordo com os requisitos. Para mais informações, consulte o artigo [requisitos de sistema StorSimple Virtual matriz](storsimple-ova-system-requirements.md).

## <a name="step-by-step-provisioning"></a>Passo a passo de aprovisionamento 

Aprovisionar e ligar a um dispositivo virtual, terá de efetuar os seguintes passos:

1.  Certifique-se de que o sistema anfitrião tem recursos suficientes para cumprir os requisitos mínimos dispositivo virtual.

2.  Aprovisione um dispositivo virtual na sua hipervisor.

3.  Iniciar o dispositivo virtual e obtenha o endereço IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Passo 1: Certifique-se de sistema anfitrião cumpra os requisitos de dispositivo virtual mínimo

Para criar um dispositivo virtual, terá de:

-   Acesso a um sistema anfitrião com o VMware ESXi Server 5.5 e acima.

-   VMware vSphere cliente no sistema de para gerir o anfitrião do ESXi.

    -   Um mínimo de 4 núcleos.

    -   Pelo menos, 8 GB de RAM.

    -   Uma interface de rede ligado à rede capaz de encaminhamento de tráfego da Internet. A largura de banda de Internet mínima deve ser 5 Mbps para permitir que para trabalhar ideal do dispositivo.

    -   Um disco virtual 500 GB para os dados.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Passo 2: Aprovisionar um dispositivo virtual em hypervisor

Execute os passos seguintes para aprovisionar um dispositivo virtual na sua hipervisor.

1.  Copie a imagem de dispositivo virtual no sistema de. Esta é a imagem que transferiu através do portal do Azure clássico. 
    1.  Certifique-se de que este é o ficheiro de imagem mais recente que transferiu. Se a imagem tenha transferido anteriormente, transfira-novamente para se certificar de que tem a imagem mais recente. A imagem mais recente tem dois ficheiros (em vez de um).
    2.  Anote a localização onde copiou a imagem à medida que vai utilizar este mais tarde no procedimento.

2.  Inicie sessão no servidor ESXi utilizando o cliente vSphere. Terá de ter privilégios de administrador para criar uma máquina virtual.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  No cliente do vSphere, na secção de inventário no painel esquerdo, selecione o servidor de ESXi.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  Em primeiro lugar irá carregar o VMDK para o servidor de ESXi. Navegue para o separador de **configuração** no painel direito. Em **Hardware**, selecione **armazenamento**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  No painel direito, em **Datastores**, selecione o arquivo de dados onde pretende carregar a VMDK. Ao arquivo de dados tem de ter espaço suficiente para os discos SO e os dados.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Clique com botão direito e selecione **Procurar arquivo de dados**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Será apresentada uma janela do **Browser do arquivo de dados** .

    ![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  Na barra de ferramentas, clique em ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) ícone para criar uma nova pasta. Especifique o nome da pasta e tome nota do mesmo. Irá utilizar este nome de pasta mais tarde, quando criar uma máquina de virtual (recomendada prática recomendada). Clique em **OK**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  A nova pasta aparecerá no painel esquerdo do **Browser do arquivo de dados**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Clique no ícone de carregamento ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) e selecione **Carregar ficheiro**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Agora deverá procure e aponte para os ficheiros VMDK que transferiu. Será dois ficheiros. Selecione um ficheiro para carregar.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  Clique em **Abrir**. Agora, isto irá iniciar o carregamento do ficheiro VMDK ao arquivo de dados especificada. Poderá demorar vários minutos até que o ficheiro a carregar.


1.  Depois do carregamento estar concluído, irá ver o ficheiro no arquivo de dados na pasta que criou. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

    Agora irá precisar de carregar o ficheiro VMDK segundo ao arquivo de dados do mesmo.

1.  Voltar para a janela de cliente do vSphere. Com o servidor de ESXi selecionado, botão direito do rato e selecione **Nova Máquina Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Será apresentada uma janela **Criar Nova Máquina Virtual** . Na página **configuração** , selecione a opção **personalizada** . Clique em **seguinte**.
    ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  Na página de **nome e localização** , especifique o nome da sua máquina virtual. Este nome deve corresponder o nome da pasta (melhor prática recomendada) que especificou anteriormente no passo 8.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  Na página de **armazenamento** , selecione um arquivo de dados que pretende utilizar para aprovisionar a VM.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  Na página **Máquina Virtual versão** , selecione **Máquina Virtual versão: 8**. Tenha em atenção que versões 8 a 11 são suportadas.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  Na página do **sistema operativo convidado** , selecione o **sistema operativo convidado** como **Windows**. Para obter a **versão**, na lista pendente, selecione **Microsoft Windows Server 2012 (64 bits)**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  Na página **CPUs** , ajuste o **número de sockets virtuais** e o **número de núcleos por virtual socket** para que seja o **Número Total de núcleos** 4 (ou mais). Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  Na página de **memória** , especifique 8 GB (ou mais) de RAM. Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  Na página de **rede** , especifique o número das interfaces de rede. O requisito mínimo é uma interface de rede.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  Na página **Controlador SCSI** , aceite a predefinição **LSI lógica SA controlador**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  Na página **selecionar um disco** , selecione **utilizar um disco virtual existente**. Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  Na página **Selecionar disco existente** , em **Disco caminho do ficheiro**, clique em **Procurar**. Esta ação abre uma caixa de diálogo **Procurar Datastores** . Navegue para a localização onde carregou o VMDK. Agora irá ver apenas um ficheiro no arquivo de dados como os dois ficheiros que carregou inicialmente foram intercalados. Selecione o ficheiro e clique em **OK**. Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  Na página **Opções avançadas** , aceite a predefinição e clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  Na página **pronto para concluir** , reveja todas as definições de associada a nova máquina virtual. Selecione **Editar as definições de máquina virtual antes da sua conclusão**. Clique em **continuar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  Na página **Propriedades máquinas virtuais** do, no separador **Hardware** , localize o hardware do dispositivo. Selecione **novo disco rígido**. Clique em **Adicionar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Isto reúne a janela **Adicionar Hardware** . Na página **Tipo de dispositivo** , em **Escolher o tipo de dispositivo que pretende adicionar**, selecione **no disco rígido** e clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  Na página **selecionar um disco** , selecione **criar um novo disco virtual**. Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  Na página **criar um disco** , altere o **Tamanho do disco** para 500 GB (ou mais). Em o **Aprovisionamento de disco**, selecione **Aprovisionar fina**. Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  Na página **Opções avançadas** , aceite a predefinição.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  Na página **pronto para concluir** , reveja as opções de disco. Clique em **Concluir**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Agora irá devolver para a página de propriedades de Máquina Virtual. Um novo disco rígido é adicionado à sua máquina virtual. Clique em **Concluir**.
  
    ![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Com o seu máquina virtual selecionada no painel direito, navegue até ao separador **Sumário** . Reveja as definições para a sua máquina virtual.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Agora está aprovisionada máquina virtual. O passo seguinte é power nesta máquina e obter o endereço IP.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Passo 3: Iniciar o dispositivo virtual e obter o período de inquérito

Execute os passos seguintes para iniciar o seu dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual

1.  Inicie o dispositivo virtual. No Gestor de configuração, o vSphere no painel esquerdo, selecione o seu dispositivo e com o botão direito para apresentar o menu de contexto. Selecione **Power** e, em seguida, selecione **Power no**. Isto deve power no seu computador virtual. Pode ver o estado no painel de **Tarefas recentes** parte inferior do cliente vSphere.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  As tarefas de configuração, irão demorar alguns minutos a concluir. Assim que o dispositivo está em execução, navegue para o separador de **consola** . Envie Ctrl + Alt + Delete que inicie sessão no dispositivo. Em alternativa, pode aponte o cursor na janela da consola e prima Ctrl + Alt + Insert. O utilizador predefinido está *StorSimpleAdmin* e a palavra-passe predefinida é a *Palavra-passe1*.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Por motivos de segurança, a palavra-passe de administrador do dispositivo expira a primeira de início de sessão. Vai ser-lhe para alterar a palavra-passe.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Introduza uma palavra-passe que contém, pelo menos, 8 carateres. A palavra-passe tem de conter 3 fora do 4 destes requisitos: carateres em maiúsculas, minúsculas, numéricos e especiais. Volte a introduzi-la para confirmar. Será notificado do que a palavra-passe foi alterada.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Depois da palavra-passe é alterada com êxito, poderá reinicie o dispositivo virtual. Aguarde que o reinício concluir. Consola do Windows PowerShell do dispositivo pode ser apresentada juntamente com uma barra de progresso.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  Os passos 6 a 8 só se aplicam quando iniciar o num ambiente não DHCP. Se estiver num ambiente DHCP, em seguida, ignore estes passos e aceda ao passo 9. Se iniciado o seu dispositivo num ambiente não DHCP, irá ver o ecrã seguinte. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

    Agora terá de configurar a rede.

1.  Utilizar o `Get-HcsIpAddress` comando para listar as interfaces de rede ativadas no seu dispositivo virtual. Se o seu dispositivo tem uma interface de rede única ativada, o nome predefinido atribuído a esta interface é `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Utilizar o `Set-HcsIpAddress` cmdlet para configurar a rede. Um exemplo é apresentado abaixo:


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Após a configuração inicial está concluída e o dispositivo tem de ter iniciado para cima, verá o texto da faixa de dispositivo. Tome nota do endereço IP e o URL apresentado no texto da faixa para gerir o dispositivo. Irá utilizar este endereço IP para ligar para a web IU do seu dispositivo virtual e concluir a configuração do local e registo.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Opcional) Execute este passo apenas se estiver a implementar o seu dispositivo na nuvem administração pública. Agora irá ativar o modo de Estados Unidos Federal Information Processing padrão (FIPS) no seu dispositivo. A norma FIPS 140 define algoritmos aprovados para utilização por Federal-nos sistemas informáticos, administração pública para a proteção de dados confidenciais.
    1. Para ativar o modo FIPS, execute o cmdlet seguinte:
        
        `Enter-HcsFIPSMode`

    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptografia entre em vigor.

        > [AZURE.NOTE] Pode ativar ou desativar o modo FIPS no seu dispositivo. Alternados o dispositivo entre o modo FIPS e que não sejam FIPS não são suportado.


Se o seu dispositivo não cumprir os requisitos mínimos de configuração, irá ver um erro no texto da faixa (mostrado abaixo). Terá de modificar a configuração do dispositivo para que este tenha recursos adequados para cumprir os requisitos mínimos. Pode, em seguida, reinicie e ligar ao dispositivo. Consultar os requisitos mínimos configuração [passo 1: Certifique-se de que o sistema anfitrião cumpre os requisitos de dispositivo virtual mínimo](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Se cara qualquer outro tipo de erro durante a configuração inicial utilizando a web local da IU, consulte a seguintes fluxos de trabalho [da IU da web local](storsimple-ova-web-ui-admin.md)ao utilizar Gerir sua matriz de Virtual StorSimple.

-   Execute testes de diagnóstico para [resolver problemas de configuração de IU da web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   O [pacote de registo de gerar e ver os ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package)...

## <a name="next-steps"></a>Próximos passos

-   [Configurar a sua matriz de Virtual StorSimple como um servidor de ficheiros](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar a sua matriz de Virtual StorSimple como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

