<properties
   pageTitle="Implementar StorSimple Virtual matriz - disposição no Hyper-V"
   description="Neste tutorial segundo na implementação StorSimple Virtual matriz envolve aprovisionamento um dispositivo virtual em Hyper-V."
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
   ms.date="10/11/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>Implementar a matriz Virtual StorSimple - aprovisionar uma matriz Virtual no Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Descrição geral

Neste tutorial aprovisionamento aplica-se a versão do Microsoft Azure StorSimple Virtual matrizes (também conhecido como StorSimple os dispositivos virtuais no local ou dispositivos virtuais StorSimple) em execução Março de 2016 disponibilidade geral (das versões DG). Neste tutorial descreve como pode aprovisionar uma matriz Virtual StorSimple num sistema anfitrião a executar o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. Este artigo aplica-se para a implementação de matrizes Virtual StorSimple no portal clássico Azure, bem como a nuvem do Microsoft Azure administração pública.

Irá precisar de privilégios de administrador para aprovisionar e configurar um dispositivo virtual. A configuração inicial e aprovisionamento pode demorar cerca de 10 minutos para concluir.


## <a name="provisioning-prerequisites"></a>Pré-requisitos de aprovisionamento

Aqui irá encontrar os pré-requisitos aprovisionamento de um dispositivo virtual num sistema anfitrião a executar o Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço do Gestor de StorSimple

Antes de começar, certifique-se de que:

-   Concluiu todos os passos no [artigo preparar o portal para StorSimple Virtual matriz](storsimple-ova-deploy1-portal-prep.md).

-   Tiver transferido a imagem do dispositivo virtual para Hyper-V a partir do portal Azure. Para obter mais informações, consulte o artigo [passo 3: Transferir a imagem do dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

    > [AZURE.IMPORTANT] O software a executar a matriz Virtual StorSimple apenas pode ser utilizado em conjunto com o serviço do Gestor de Storsimple.

### <a name="for-the-storsimple-virtual-device"></a>Para o dispositivo virtual StorSimple

Antes de implementar um dispositivo virtual, certifique-se de que:

-   Tiver acesso a um sistema anfitrião a executar o Hyper-V no Windows Server 2008 R2 ou posterior que pode ser utilizados para uma disposição um dispositivo.

-   É possível dedicar os seguintes recursos para aprovisionar o seu dispositivo virtual sistema anfitrião:

    -   Um mínimo de 4 núcleos.

    -   Pelo menos, 8 GB de RAM.

    -   Uma interface de rede.

    -   Um disco virtual 500 GB para os dados de sistema.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no Centro de dados

Antes de começar, reveja os requisitos de rede para implementar um dispositivo virtual StorSimple e configurar a rede do Centro de dados adequadamente. Para mais informações, consulte o artigo [requisitos da rede StorSimple Virtual matriz](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Passo a passo de aprovisionamento

Aprovisionar e ligar a um dispositivo virtual, terá de efetuar os seguintes passos:

1.  Certifique-se de que o sistema anfitrião tem recursos suficientes para cumprir os requisitos mínimos dispositivo virtual.

2.  Aprovisione um dispositivo virtual na sua hipervisor.

3.  Iniciar o dispositivo virtual e obtenha o endereço IP.

Cada um destes passos é explicado nas secções seguintes.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>Passo 1: Certifique-se de que o sistema anfitrião cumpre os requisitos de dispositivo virtual mínimo

Para criar um dispositivo virtual, terá de:

-   A função Hyper-V instalada no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.

-   Gestor de Hyper-V Microsoft num cliente do Microsoft Windows ligado ao anfitrião.

Tem de se certificar de que o hardware subjacente (sistema anfitrião) no qual está a criar o dispositivo virtual é capaz da dedicar os seguintes recursos para o seu dispositivo virtual:

- Um mínimo de 4 núcleos.
- Pelo menos, 8 GB de RAM.
- Uma interface de rede.
- Um disco virtual 500 GB para os dados de sistema.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Passo 2: Aprovisionar um dispositivo virtual em hypervisor

Execute os passos seguintes para aprovisionar um dispositivo na sua hipervisor.

#### <a name="to-provision-a-virtual-device"></a>Aprovisionar um dispositivo virtual

1.  No seu anfitrião do Windows Server, copie a imagem de dispositivo virtual numa unidade local. Esta é a imagem (VHD ou VHDX) que transferiu através do portal do Azure. Anote a localização onde copiou a imagem à medida que vai utilizar este mais tarde no procedimento.

2.  Abra o **Gestor de servidor**. No canto superior direito, clique em **Ferramentas** e selecione **Gestor de Hyper-V**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

    Se estiver a executar o Windows Server 2008 R2, abra o Gestor de Hyper-V. No Gestor de servidor, clique em **funções > Hyper-V > Gestor de Hyper-V**.

1.  No **Gestor de Hyper-V**, no painel de âmbito, o nó do seu sistema para abrir o menu de contexto com o botão direito e, em seguida, clique em **Novo** > **Máquina Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  Na página **antes de começar** do Assistente de Máquina Virtual novo, clique em **seguinte**.

1.  Na página **Especificar nome e localização** , forneça um **nome** para o seu dispositivo virtual. Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  Na página **Especificar geração** , selecione o tipo de imagem do dispositivo e, em seguida, clique em **seguinte**. Esta página não é apresentado se estiver a utilizar o Windows Server 2008 R2.

    * Selecione **2. ª geração** se tiver transferido uma imagem de .vhdx para o Windows Server 2012 ou posterior.
    * Selecione **1. ª geração** se tiver transferido uma imagem. vhd para o Windows Server 2008 R2 ou posterior.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  Na página **atribuir memória** , especifique uma **memória de arranque** pelo menos **8192 MB**, não activar a memória dinâmica e, em seguida, clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  Na página **Configurar rede** , especifique o parâmetro virtual que está ligado à Internet e, em seguida, clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  Na página de **disco rígido virtual ligar** , escolha **utilizar um disco rígido virtual existente**, especifique a localização da imagem dispositivo virtual (.vhdx ou. vhd) e, em seguida, clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  Reveja o **Resumo** e, em seguida, clique em **Concluir** para criar a máquina virtual. Mas não avançar ainda - ainda é necessário adicionar alguns núcleos CPU e uma segunda unidade. 

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Para cumprir os requisitos mínimos, terá de 4 núcleos. Para adicionar processadores virtuais, com o seu sistema anfitrião seleccionado na janela do **Gestor de Hyper-V** , no painel direito, na lista de **máquinas virtuais**, localize a máquina virtual que acabou de criar. Selecione o nome do computador com o botão direito e selecione **Definições**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  Na página **Definições** , no painel da esquerda, clique em **processador**. No painel direito, defina o **número de processadores virtuais** 4 (ou mais). Clique em **Aplicar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Para cumprir os requisitos mínimos, também precisa de adicionar um disco de dados virtual 500 GB. Na página **Definições** :

    1.  No painel esquerdo, selecione **Controlador SCSI**.
    2.  No painel direito, selecione a **Unidade de rígido** e clique em **Adicionar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  Na página de **unidade de disco rígido** , selecione a opção **Virtual disco** e clique em **Novo**. Isto irá iniciar o **Assistente de novo no disco rígido Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  Na página **antes de começar** do assistente no disco rígido Virtual novo, clique em **seguinte**.

1.  Na **página escolher o formato de disco**, aceite a opção predefinida de formato **VHDX** . Clique em **seguinte**. Não verá este ecrã se estiver a executar o Windows Server 2012 R2 ou Windows Server 2008 R2.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  Na **página escolher tipo de disco**, definir tipo de disco rígido virtual como **dinamicamente expandir** (recomendado). Se optar por disco **tamanho fixo** , também funcionam, mas poderá ter de esperar muito tempo. Recomendamos que não utilize a opção **Differencing** . Clique em **seguinte**. Note que **dinamicamente expandir** é a predefinição no Windows Server 2012 R2 e Windows Server 2012. No Windows Server 2008 R2, a predefinição é **tamanho fixo**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  Na página **Especificar nome e localização** , forneça um **nome** , bem como **localização** (, pode navegar para um) para o disco de dados. Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  Na página **Configurar disco** , selecione a opção **criar um novo em branco virtual no disco rígido** e especificar o tamanho como **500 GB** (ou mais). Enquanto 500 GB é o requisito mínimo, pode sempre aprovisionar um disco maior. Tenha em atenção que não é possível expandir ou encolher o disco de uma vez aprovisionado. Para mais informações sobre o tamanho do disco para aprovisionar, reveja a secção de redimensionamento no documento [práticas recomendadas](storsimple-ova-best-practices.md#configuration-best-practices) . Clique em **seguinte**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  Na página de **Resumo** , reveja os detalhes do seu disco dados virtual e se satisfeito, clique em **Concluir** para criar o disco. O assistente irá fechar e um disco rígido virtual será adicionado ao seu computador.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  Irá regressar à página de **Definições** . Clique em **OK** para fechar a página de **Definições** e regressar à janela do Gestor de Hyper-V.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Passo 3: Iniciar o dispositivo virtual e obter o período de inquérito

Execute os passos seguintes para iniciar o seu dispositivo virtual e ligar ao mesmo.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual

1.  Inicie o dispositivo virtual.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Depois do dispositivo está em execução, selecione o dispositivo, clique com botão direito e selecione **Ligar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Poderá ter de esperar entre 5 10 minutos para o dispositivo para pronta a ser. É apresentada uma mensagem de estado na consola para indicar o progresso. Depois do dispositivo está pronto, aceda a **ação**. Prima `Ctrl + Alt + Delete` que inicie sessão no dispositivo virtual. O utilizador predefinido está *StorSimpleAdmin* e a palavra-passe predefinida é a *Palavra-passe1*.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Por motivos de segurança, a palavra-passe de administrador do dispositivo expira a primeira de início de sessão. Vai ser-lhe para alterar a palavra-passe.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

    Introduza uma palavra-passe que contém, pelo menos, 8 carateres. A palavra-passe deve satisfazer, pelo menos, 3 terminar os seguintes requisitos de 4: carateres em maiúsculas, minúsculas, numéricos e especiais. Volte a introduzi-la para confirmar. Será notificado do que a palavra-passe foi alterada.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Depois da palavra-passe com êxito for alterada, pode reiniciar o dispositivo virtual. Aguarde que o dispositivo iniciar.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

    Consola do Windows PowerShell do dispositivo será apresentada juntamente com uma barra de progresso.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Os passos 6 a 8 só se aplicam quando iniciar o num ambiente não DHCP. Se estiver num ambiente DHCP, em seguida, ignore estes passos e aceda ao passo 9. Se iniciado o seu dispositivo num ambiente não DHCP, irá ver o ecrã seguinte.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

    Agora terá de configurar a rede.

1.  Utilizar o `Get-HcsIpAddress` comando para listar as interfaces de rede ativadas no seu dispositivo virtual. Se o seu dispositivo tem uma interface de rede única ativada, o nome predefinido atribuído a esta interface é `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  Utilizar o `Set-HcsIpAddress` cmdlet para configurar a rede. Um exemplo é apresentado abaixo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Após a configuração inicial está concluída e o dispositivo tem de ter iniciado para cima, verá o texto da faixa de dispositivo. Tome nota do endereço IP e o URL apresentado no texto da faixa para gerir o dispositivo. Irá utilizar este endereço IP para ligar para a web IU do seu dispositivo virtual e concluir a configuração do local e registo.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (Opcional) Execute este passo apenas se estiver a implementar o seu dispositivo na nuvem administração pública. Agora irá ativar o modo de Estados Unidos Federal Information Processing padrão (FIPS) no seu dispositivo. A norma FIPS 140 define algoritmos aprovados para utilização por Federal-nos sistemas informáticos, administração pública para a proteção de dados confidenciais.
    1. Para ativar o modo FIPS, execute o cmdlet seguinte:

        `Enter-HcsFIPSMode`

    2. Reinicie o seu dispositivo depois de ter ativado o modo FIPS para que as validações criptografia entre em vigor.

        > [AZURE.NOTE] Pode ativar ou desativar o modo FIPS no seu dispositivo. Alternados o dispositivo entre o modo FIPS e que não sejam FIPS não são suportado.

Se o seu dispositivo não cumprir os requisitos mínimos de configuração, irá ver um erro no texto da faixa (mostrado abaixo). Terá de modificar a configuração do dispositivo para que este tenha recursos adequados para cumprir os requisitos mínimos. Pode, em seguida, reinicie e ligar ao dispositivo. Consultar os requisitos mínimos configuração [passo 1: Certifique-se de que o sistema anfitrião cumpre os requisitos de dispositivo virtual mínimo](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Se cara qualquer outro tipo de erro durante a configuração inicial utilizando a web local da IU, consulte a seguintes fluxos de trabalho [da IU da web local](storsimple-ova-web-ui-admin.md)ao utilizar Gerir sua matriz de Virtual StorSimple.

-   Execute testes de diagnóstico para [resolver problemas de configuração de IU da web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   O [pacote de registo de gerar e ver os ficheiros de registo](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![ícone de vídeo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png)  **vídeo disponível**

Veja o vídeo para saber como pode aprovisionar o uma matriz Virtual StorSimple em Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## <a name="next-steps"></a>Próximos passos

-   [Configurar a sua matriz de Virtual StorSimple como um servidor de ficheiros](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar a sua matriz de Virtual StorSimple como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)
