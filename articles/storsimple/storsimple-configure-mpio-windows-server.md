<properties 
   pageTitle="Configurar MPIO para o seu dispositivo StorSimple | Microsoft Azure"
   description="Descreve como configurar / o i (MPIO) para o seu dispositivo StorSimple ligado a um anfitrião a executar o Windows Server 2012 R2."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-multipath-io-for-your-storsimple-device"></a>Configurar Multipath i / para o seu dispositivo StorSimple

Microsoft criadas suporte para a funcionalidade / o i (MPIO) no Windows Server ajuda compilação altamente disponíveis, tolerância a falhas SAN configurações. MPIO utiliza componentes de caminho físico redundantes — adaptadores, cabos e parâmetros — para criar caminhos lógicos entre o servidor e o dispositivo de armazenamento. Se existir uma falha de componente, a causar um caminho lógico para falhar, lógica multipathing utiliza um caminho alternativo para e/s para que as aplicações podem ainda aceder aos respetivos dados. Para além disso dependendo da sua configuração, MPIO pode também melhorar o desempenho ao balanceamento voltar a carregar em todos os caminhos estes. Para mais informações, consulte o artigo [Descrição geral do MPIO](https://technet.microsoft.com/library/cc725907.aspx "Descrição geral do MPIO e funcionalidades").  

Para alta disponibilidade da sua solução StorSimple, MPIO deve ser configurada no seu dispositivo StorSimple. Quando MPIO estiver instalado no seu servidores de anfitrião de executar o Windows Server 2012 R2, os servidores, em seguida, podem tolerar uma ligação, rede ou falha da interface. 

MPIO é uma funcionalidade opcional no Windows Server e não está instalado por predefinição. Devem ser instalada como uma funcionalidade através do Gestor de servidor. Este tópico descreve os passos que deve seguir para instalar e utilizar a funcionalidade MPIO num sistema anfitrião a executar o Windows Server 2012 R2 e ligados a um dispositivo físico StorSimple.

>[AZURE.NOTE] **Este procedimento é aplicável StorSimple 8000 apenas para séries. MPIO não é atualmente suportada num dispositivo virtual StorSimple.**

Terá de seguir estes passos para configurar MPIO no seu dispositivo StorSimple:

- Passo 1: Instalar MPIO no anfitrião do Windows Server

- Passo 2: Configurar MPIO para volumes de StorSimple

- Passo 3: Volumes de montagem StorSimple no anfitrião do

- Passo 4: Configurar MPIO para elevada disponibilidade e balanceamento de carga

Cada um dos passos acima é abordada nas secções seguintes.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Passo 1: Instalar MPIO no anfitrião do Windows Server

Para instalar esta funcionalidade no seu anfitrião do Windows Server, execute o seguinte procedimento.

#### <a name="to-install-mpio-on-the-host"></a>Para instalar o MPIO no anfitrião do

1. Abra o Gestor de servidor no seu anfitrião do Windows Server. Por predefinição, o Gestor de servidor é iniciado quando um membro do grupo Administradores inicia num computador que está a executar o Windows Server 2012 R2 ou Windows Server 2012. Se o Gestor de servidor ainda não estiver aberto, clique em **Iniciar > Gestor de servidor**.
![Gestor de servidor](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Clique em **Gestor de servidor > dashboards > Adicionar funções e funcionalidades**. Esta ação inicia o assistente **Adicionar funções e funcionalidades** .
![Adicionar funções e funcionalidades assistente 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. No assistente **Adicionar funções e funcionalidades** , faça o seguinte:

    - Na página **antes de começar** , clique em **seguinte**.
    - Na página **Selecionar o tipo de instalação** , aceite a predefinição da instalação **baseado em funções ou funcionalidade** . Clique em **seguinte**. ![Adicionar funções e Assistente para funcionalidades 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
    - Na página **Selecionar o servidor de destino** , selecione **selecionar um servidor do conjunto de servidor**. O servidor de anfitrião de deve ser detetado automaticamente. Clique em **seguinte**.
    - Na página **Selecionar funções de servidor** , clique em **seguinte**.
    - Na página **Selecione funcionalidades** , selecione **Multipath i /**e clique em **seguinte**. ![Adicionar funções e Assistente para funcionalidades 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
    - Na página **Confirmar selecções de instalação** , confirme a seleção e, em seguida, selecione **reiniciar o servidor de destino automaticamente se necessário**, conforme apresentado abaixo. Clique em **instalar**. ![Adicionar funções e Assistente para funcionalidades 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
    - Será notificado quando a instalação estiver concluída. Clique em **Fechar** para fechar o assistente. ![Adicionar funções e Assistente para funcionalidades 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Passo 2: Configurar MPIO para volumes de StorSimple

MPIO tem de ser configurado para identificar os volumes StorSimple. Para configurar MPIO reconheçam volumes de StorSimple, execute os passos seguintes.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Para configurar MPIO para volumes de StorSimple

1. Abra a **configuração MPIO**. Clique em **Gestor de servidor > dashboards > Ferramentas > MPIO**.

2. Na caixa de diálogo **Propriedades de MPIO** , selecione o separador **Descobrir com várias caminhos** .

3. Selecione **Adicionar suporte para dispositivos iSCSI**e, em seguida, clique em **Adicionar**.  
![Propriedades de MPIO descobrir Multi caminhos](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Reinicie o servidor quando lhe for pedido.
5. Na caixa de diálogo **Propriedades de MPIO** , clique no separador **Dispositivos o** . Clique em **Adicionar**.
    </br>![MPIO propriedades MPIO dispositivos](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. Na caixa de diálogo **Adicionar suporte o** , em **ID de Hardware do dispositivo**, introduza o seu número de série do dispositivo. Pode obter o número de série do dispositivo ao aceder ao seu serviço de Gestor de StorSimple e navegar para **dispositivos > Dashboard**. O número de série do dispositivo é apresentado no painel **Rapidamente rápida** do dashboard do dispositivo do lado direito.
    </br>![Adicionar o suporte de MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicie o servidor quando lhe for pedido.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Passo 3: Volumes de montagem StorSimple no anfitrião do

Depois de MPIO estiver configurado no Windows Server, o volume (s) criados no dispositivo StorSimple pode ser instalados e, em seguida, pode tirar partido das MPIO para redundância. Para um volume de montagem, execute os passos seguintes.

#### <a name="to-mount-volumes-on-the-host"></a>Para volumes de montagem no anfitrião do

1. Abra a janela de **iSCSI propriedades iniciador** no anfitrião do Windows Server. Clique em **Gestor de servidor > dashboards > Ferramentas > iniciador iSCSI**.
2. Na caixa de diálogo **iSCSI iniciador propriedades** , clique no separador de deteção e, em seguida, clique em **Descobrir o Portal de destino**.
3. Na caixa de diálogo **Descobrir o Portal de destino** , efetue o seguinte procedimento:
    
    - Introduza o endereço IP da porta de dados do seu dispositivo StorSimple (por exemplo, introduza dados 0).
    - Clique em **OK** para regressar à caixa de diálogo **Propriedades do iniciador de iSCSI** .

    >[AZURE.IMPORTANT] **Se estiver a utilizar uma rede privada para ligações iSCSI, introduza o endereço IP da porta de dados que está ligado à rede privada.**

4. Repita os passos 2 a 3 para uma segunda interface de rede (por exemplo, 1 de dados) no seu dispositivo. Tenha em atenção que estas interfaces devem ser ativadas para iSCSI. Para saber mais sobre este procedimento, consulte o artigo [Modificar interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces).
5. Selecione o separador **destinos** na caixa de diálogo **Propriedades do iniciador de iSCSI** . Deverá visualizar o destino de dispositivo StorSimple IQN em **Descobertas destinos**.
 ![iSCSI iniciador propriedades destinos separador](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Clique em **Ligar** para estabelecer uma sessão iSCSI com o seu dispositivo StorSimple. Será apresentada uma caixa de diálogo de **ligar ao destino** .

7. Na caixa de diálogo **ligar ao destino** , selecione a caixa de verificação **Ativar multi-caminho** . Clique em **Avançadas**.

8. Na caixa de diálogo **Definições avançadas** , faça o seguinte:                                       
    -    Na lista pendente **Adaptador Local** , selecione o **Iniciador do Microsoft iSCSI**.
    -    Na lista pendente **Iniciador IP** , selecione o endereço IP do anfitrião.
    -    Na lista pendente IP de **Portal de destino** , selecione IP da interface do dispositivo.
    -    Clique em **OK** para regressar à caixa de diálogo **Propriedades do iniciador de iSCSI** .

9. Clique em **Propriedades**. Na caixa de diálogo **Propriedades** , clique em **Adicionar sessão**.
10. Na caixa de diálogo **ligar ao destino** , selecione a caixa de verificação **Ativar multi-caminho** . Clique em **Avançadas**.
11. Na caixa de diálogo **Definições avançadas** :                                        
    -  Na lista pendente **adaptador Local** , selecione o Iniciador do Microsoft iSCSI.
    -  Na lista pendente **Iniciador IP** , selecione o endereço IP correspondente ao anfitrião do. Neste caso, que está a ligar duas interfaces de rede no dispositivo uma interface de rede única no anfitrião do. Por conseguinte, esta interface é o mesmo que fornecido para a primeira sessão.
    -  Na lista pendente de **IP do Portal de destino** , selecione o endereço IP da segunda interface dados ativado no dispositivo.
    -  Clique em **OK** para regressar à caixa de diálogo Propriedades de iniciador iSCSI. Tiver adicionado uma segunda sessão para o destino.

12. Abra a **Gestão de computador** ao navegar para **Gestor de servidor > dashboards > Gestão de computador**. No painel esquerdo, clique em **armazenamento > Gestão de discos**. O volume (s) criados no dispositivo StorSimple que são visíveis para este sistema anfitrião irão aparecer em **Gestão de disco** , como discos novos.

13. Iniciar o disco e criar um novo volume. Durante o processo de formato, selecione um tamanho de bloco de 64 KB.
![Gestão do disco](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Em **Gestão do disco**, com o botão direito do **disco** e selecione **Propriedades**.
15. No modelo de StorSimple # # # caixa de diálogo **Propriedades de dispositivo de disco multi-caminho** caixa, clique no separador **MPIO** .
![StorSimple 8100 multi-caminho disco DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. Na secção **DSM nome** , clique em **Detalhes** e certifique-se de que os parâmetros estão definidos para os parâmetros predefinidos. Os parâmetros predefinidos são:

    - Verificar o caminho período = 30
    - Contagem de repetições = 3
    - Período de remoção de PDO = 20
    - Intervalo de repetição = 1
    - Verificar o caminho ativado = desmarcada.


>[AZURE.NOTE] **Não modifique os parâmetros predefinidos.**

## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Passo 4: Configurar MPIO para elevada disponibilidade e balanceamento de carga

Para multi-caminho baseados elevada disponibilidade e balanceamento de carga, várias sessões devem ser adicionadas manualmente para declarar os diferentes caminhos disponíveis. Por exemplo, se tiver o anfitrião duas interfaces ligado ao SAN e o dispositivo tem duas interfaces ligadas ao SAN, em seguida, terá de quatro sessões configurado com permutações caminho inicial maiúscula (apenas duas sessões será necessário se cada interface de dados e a interface de anfitrião são numa sub-rede IP diferente e não encaminháveis).

>[AZURE.IMPORTANT] **Recomendamos que não misture 1 GbE e 10 interfaces de rede GbE. Se utilizar duas interfaces de rede, ambas as interfaces devem ser o tipo de idêntico.**

O procedimento seguinte descreve como adicionar sessões quando um dispositivo StorSimple com duas interfaces de rede está ligado para um anfitrião com duas interfaces de rede.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Para configurar MPIO para elevada disponibilidade e balanceamento de carga

1. Efetuar uma deteção do destino: na caixa de diálogo **iSCSI iniciador propriedades** , no separador **deteção** , clique em **Descobrir o Portal**.
2. Na caixa de diálogo **ligar ao destino** , introduza o endereço IP de um das interfaces de rede do dispositivo.
3. Clique em **OK** para regressar à caixa de diálogo **Propriedades do iniciador de iSCSI** .

4. Na caixa de diálogo **iSCSI iniciador propriedades** , selecione o separador **destinos** , realce o destino descoberto e, em seguida, clique em **Ligar**. É apresentada a caixa de diálogo **ligar para o destino** .

5. Na caixa de diálogo **ligar ao destino** :
    
    - Deixe o destino de selecionado predefinido definição para **Adicionar esta ligação** para a lista de alvos Favoritos. Este procedimento irá tornar o dispositivo tentar automaticamente reiniciar a ligação sempre que for reiniciado neste computador.
    - Selecione a caixa de verificação **Ativar multi-caminho** .
    - Clique em **Avançadas**.

6. Na caixa de diálogo **Definições avançadas** :
    - Na lista pendente **Adaptador Local** , selecione o **Iniciador do Microsoft iSCSI**.
    - Na lista pendente **Iniciador IP** , selecione o endereço IP do anfitrião.
    - Na lista pendente de **IP do Portal de destino** , selecione o endereço IP da interface de dados ativado no dispositivo.
    - Clique em **OK** para regressar à caixa de diálogo Propriedades de iniciador iSCSI.

7. Clique em **Propriedades**e, na caixa de diálogo **Propriedades** , clique em **Adicionar sessão**.

8. Na caixa de diálogo **ligar ao destino** , selecione a caixa de verificação **Ativar multi-caminho** e, em seguida, clique em **Avançadas**.

9. Na caixa de diálogo **Definições avançadas** :
    1. Na lista pendente **adaptador Local** , selecione o **Iniciador do Microsoft iSCSI**.
    2. Na lista pendente **Iniciador IP** , selecione o endereço IP correspondente para a interface no anfitrião do segunda.
    3. Na lista pendente de **IP do Portal de destino** , selecione o endereço IP da segunda interface dados ativado no dispositivo.
    4. Clique em **OK** para regressar à caixa de diálogo **Propriedades do iniciador de iSCSI** . Agora ter adicionado uma segunda sessão para o destino.

10. Repita os passos 8 10 para adicionar sessões adicionais (caminhos) para o destino. Com duas interfaces no anfitrião do e dois no dispositivo, pode adicionar um total de quatro sessões.

11. Depois de adicionar as sessões pretendidas (caminhos), na caixa de diálogo **iSCSI iniciador propriedades** , selecione o destino e clique em **Propriedades**. No separador sessões da caixa de diálogo de **Propriedades** , tenha em atenção a sessão quatro identificadores que correspondem aos permutações caminho possíveis. Para cancelar uma sessão, selecione a caixa de verificação junto a um identificador de sessão e, em seguida, clique em **Desligar**.

12. Para ver dispositivos apresentados no prazo de sessões, selecione o separador **dispositivos** . Para configurar a política MPIO para um dispositivo selecionado, clique em **MPIO**. A caixa de diálogo **Detalhes do dispositivo** aparecerá. No separador **MPIO** , pode selecionar as definições de **Política de balanceamento de carga** adequadas. Também pode ver o tipo de caminho **ativos** ou **modo de espera** .

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para modificar a configuração do dispositivo StorSimple](storsimple-modify-device-config.md).
 
