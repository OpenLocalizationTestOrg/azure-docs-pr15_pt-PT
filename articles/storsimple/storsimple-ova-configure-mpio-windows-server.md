<properties 
   pageTitle="Configurar o MPIO no seu anfitrião de matriz virtual StorSimple | Microsoft Azure"
   description="Descreve como configurar / o i (MPIO) para o seu StorSimple matriz virtual ligado a um anfitrião a executar o Windows Server 2012 R2."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>Configurar o Multipath i / no anfitrião do Windows Server para a matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve como instalar funcionalidade Multipath i / (MPIO) no seu anfitrião do Windows Server, aplicar definições de configuração específicas para volumes só StorSimple e, em seguida, verifique MPIO para StorSimple volumes. O procedimento assume que a sua matriz Virtual StorSimple 1200 com duas interfaces de rede está ligado a um anfitrião do Windows Server com duas interfaces de rede. As informações contidas neste artigo aplica-se apenas à matriz virtual. Para obter informações sobre os dispositivos de série StorSimple 8000, vá para [o configurar para StorSimple anfitrião](storsimple-configure-mpio-windows-server.md). 

A funcionalidade MPIO no Windows Server ajuda-o a compilação armazenamento altamente disponível, tolerância a falhas configurações. MPIO utiliza componentes de caminho físico redundantes — adaptadores, cabos e parâmetros — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se existir uma falha de componente, a causar um caminho lógico para falhar, lógica multipathing utiliza um caminho alternativo para e/s para que as aplicações podem ainda aceder aos respetivos dados. Para além disso dependendo da sua configuração, MPIO pode também melhorar o desempenho ao balanceamento voltar a carregar em todos os caminhos estes. Para mais informações, consulte o artigo [Descrição geral do MPIO](https://technet.microsoft.com/library/cc725907.aspx "Descrição geral do MPIO e funcionalidades").  

Para alta disponibilidade da sua solução StorSimple, configure MPIO nos anfitriões do Windows Server ligados à sua matriz de Virtual StorSimple 1200 (também conhecido como no local virtual dispositivo). Os servidores de anfitrião, em seguida, podem tolerar uma ligação, rede ou falha da interface. 

Terá de seguir estes passos para configurar MPIO: 

- Pré-requisitos de configuração

- Passo 1: Instalar MPIO no anfitrião do Windows Server

- Passo 2: Configurar MPIO para volumes de StorSimple

- Passo 3: Volumes de montagem StorSimple no anfitrião do

Cada um dos passos acima é abordada nas secções seguintes.


## <a name="prerequisites"></a>Pré-requisitos

Esta secção fornece detalhes sobre as pré-requisitos de configuração para o anfitrião do Windows Server e a sua matriz virtual.

### <a name="on-windows-server-host"></a>No anfitrião do Windows Server

-  Certifique-se de que o seu anfitrião do Windows Server tem 2 interfaces de rede ativadas.


### <a name="on-storsimple-virtual-array"></a>No StorSimple virtual matriz

- A matriz virtual deve ser configurada como um servidor iSCSI. Para saber mais, consulte o artigo [configurar o matriz virtual como um servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md). Um ou mais interfaces de rede devem estar ativadas na matriz.   

- As interfaces de rede no seu matriz virtual devem ser alcançáveis anfitrião do Windows Server.

- Um ou mais volumes devem ser criados na sua matriz de Virtual StorSimple. Para saber mais, consulte o artigo [Adicionar um volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) na sua matriz de virtual StorSimple 1200. Este procedimento, criámos 3 volumes (2 localmente afixados e 1 em camadas volume conforme apresentado abaixo) na matriz virtual.
    
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>Configuração do hardware para StorSimple virtual matriz

A figura seguinte mostra a configuração do hardware para elevada disponibilidade e balanceamento de carga multipathing do seu anfitrião do Windows Server e matriz virtual StorSimple utilizados neste procedimento.  

![configuração do hardware MPIO](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Como apresentado na figura anterior:

- A matriz de virtual StorSimple aprovisionado no Hyper-V é um dispositivo ativo de nó único configurado como um servidor iSCSI.

- Duas interfaces de rede virtual estão activadas no seu matriz. No web local IU da sua matriz de virtual 1200, certifique-se que duas interfaces de rede estão ativados para navegar para **As definições de rede** , conforme apresentado abaixo:

    ![Interfaces de rede ativadas no 1200](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
    
    Nota os endereços IPv4 das interfaces de rede ativado (Ethernet, Ethernet 2 por predefinição) e guardar para utilizar posteriormente no anfitrião do.

- Duas interfaces de rede estão activadas no seu anfitrião do Windows Server. Se as interfaces ligadas para anfitrião e matriz estiverem na mesma sub-rede, em seguida, haverá 4 caminhos disponíveis. Trata as maiúsculas/minúsculas neste procedimento. No entanto, se cada interface de rede na interface de matriz e anfitrião estiverem numa sub-rede IP diferente (e não encaminháveis), em seguida, apenas 2 caminhos estarão disponíveis.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passo 1: Instalar MPIO no anfitrião do Windows Server

MPIO é uma funcionalidade opcional no Windows Server e não está instalado por predefinição. Devem ser instalada como uma funcionalidade através do Gestor de servidor. Para instalar esta funcionalidade no seu anfitrião do Windows Server, execute o seguinte procedimento.

[AZURE.INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]


## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passo 2: Configurar MPIO para volumes de StorSimple

MPIO tem de ser configurado para identificar os volumes StorSimple. Para configurar MPIO reconheçam volumes de StorSimple, execute os passos seguintes.

[AZURE.INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passo 3: Volumes de montagem StorSimple no anfitrião do

Depois de MPIO estiver configurado no Windows Server, volumes criado na matriz StorSimple podem ser instalados e, em seguida, podem tirar partido das MPIO para redundância. Para um volume de montagem, execute os passos seguintes.

#### <a name="to-mount-volumes-on-the-host"></a>Para volumes de montagem no anfitrião do

1. Abra a janela de **iSCSI propriedades iniciador** no anfitrião do Windows Server. Clique em **Gestor de servidor > dashboards > Ferramentas > iniciador iSCSI**.
2. Na caixa de diálogo **iSCSI iniciador propriedades** , clique no separador de deteção e, em seguida, clique em **Descobrir o Portal de destino**.
3. Na caixa de diálogo **Descobrir o Portal de destino** , efetue o seguinte procedimento:
    
    - Introduza o endereço IP da interface de rede activados primeira na sua matriz de virtual StorSimple. Por predefinição, este seria **Ethernet**. 
    - Clique em **OK** para regressar à caixa de diálogo **Propriedades do iniciador de iSCSI** .

    >[AZURE.IMPORTANT] **Se estiver a utilizar uma rede privada para ligações iSCSI, introduza o endereço IP da porta de dados que está ligado à rede privada.**

4. Repita os passos 2 a 3 para uma segunda interface de rede (por exemplo, Ethernet 2) no seu matriz. 

5. Selecione o separador **destinos** na caixa de diálogo **Propriedades do iniciador de iSCSI** . Para sua matriz virtual, deverá ver cada superfície de volume como um alvo de **Descoberta destinos**. Neste caso, deverá ser descobriu três alvos (correspondente a três volumes).

    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)

6. Clique em **Ligar** para estabelecer uma sessão de iSCSI com a sua matriz StorSimple. Será apresentada uma caixa de diálogo de **ligar ao destino** . Selecione a caixa de verificação **Ativar multi-caminho** . Clique em **Avançadas**.

    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)

8. Na caixa de diálogo **Definições avançadas** , faça o seguinte:                                       
    -    Na lista pendente **Adaptador Local** , selecione o **Iniciador do Microsoft iSCSI**.
    -    Na lista pendente **Iniciador IP** , selecione o endereço IP do anfitrião.
    -    Na lista pendente IP de **Portal de destino** , selecione IP da interface de matriz.
    -    Clique em **OK** para regressar à caixa de diálogo **Propriedades do iniciador de iSCSI** .

    ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

9. Clique em **Propriedades**. 

    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
10. Na caixa de diálogo **Propriedades** , clique em **Adicionar sessão**.

    ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)

10. Na caixa de diálogo **ligar ao destino** , selecione a caixa de verificação **Ativar multi-caminho** . Clique em **Avançadas**.
11. Na caixa de diálogo **Definições avançadas** :                                        
    -  Na lista pendente **adaptador Local** , selecione o Iniciador do Microsoft iSCSI.
    -  Na lista pendente **Iniciador IP** , selecione o endereço IP correspondente ao anfitrião do. Neste caso, que está a ligar duas interfaces de rede na matriz uma interface de rede única no anfitrião do. Por conseguinte, esta interface é o mesmo que fornecido para a primeira sessão.
    -  Na lista pendente de **IP do Portal de destino** , selecione o endereço IP da segunda interface dados ativada na matriz.
    -  Clique em **OK** para regressar à caixa de diálogo Propriedades de iniciador iSCSI. Tiver adicionado uma segunda sessão para o destino.

        ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)

    - Depois de adicionar as sessões pretendidas (caminhos), na caixa de diálogo **iSCSI iniciador propriedades** , selecione o destino e clique em **Propriedades**. No separador sessões da caixa de diálogo de **Propriedades** , tenha em atenção a sessão quatro identificadores que correspondem aos permutações caminho possíveis. Para cancelar uma sessão, selecione a caixa de verificação junto a um identificador de sessão e, em seguida, clique em **Desligar**.
 
    - Para ver dispositivos apresentados no prazo de sessões, selecione o separador **dispositivos** . Para configurar a política MPIO para um dispositivo selecionado, clique em **MPIO**. O * *
    -  Detalhes** será apresentada a caixa de diálogo. No **MPIO** separador, pode selecionar a adequada **política de balanceamento de carga** definições. Também pode ver o **ativos** ou **tipo de caminho suspensão * *.

10. Repita os passos 8-11 para adicionar sessões adicionais (caminhos) para o destino. Com duas interfaces no anfitrião do e dois na matriz virtual, pode adicionar um total de quatro sessões para cada destino. 

    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)

11. Terá de repetir estes passos para cada volume (superfícies como um destino).

    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)

12. Abra a **Gestão de computador** ao navegar para **Gestor de servidor > dashboards > Gestão de computador**. No painel esquerdo, clique em **armazenamento > Gestão de discos**. O volume (s) criada na matriz virtual StorSimple que são visíveis para este anfitrião irão aparecer em **Gestão de discos** como discos novos.

13. Iniciar o disco e criar um novo volume. Durante o processo de formato, selecione um tamanho de unidade de alocação (Austrália) de 64 KB. Repita o processo para todos os volumes disponíveis.

    ![Gestão do disco](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)

14. Em **Gestão do disco**, com o botão direito do **disco** e selecione **Propriedades**.

15. Na caixa de diálogo **Propriedades do dispositivo multi-caminho disco** , clique no separador **MPIO** .

    ![Propriedades do disco](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)

16. Na secção **DSM nome** , clique em **Detalhes** e certifique-se de que os parâmetros estão definidos para os parâmetros predefinidos. Os parâmetros predefinidos são:

    - Verificar o caminho período = 30
    - Contagem de repetições = 3
    - Período de remoção de PDO = 20
    - Intervalo de repetição = 1
    - Verificar o caminho ativado = desmarcada.

    >[AZURE.NOTE] **Não modifique os parâmetros predefinidos.**


## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar a sua matriz de Virtual StorSimple](storsimple-ova-manager-service-administration.md).
 
