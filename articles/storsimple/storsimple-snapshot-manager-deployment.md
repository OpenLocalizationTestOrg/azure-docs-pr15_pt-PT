<properties 
   pageTitle="Implementar StorSimple instantâneo Gestor | Microsoft Azure"
   description="Saiba como transferir e instalar o Gestor de instantâneo StorSimple, um snap-in MMC para gerir funcionalidades de proteção e cópia de segurança de dados StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Implementar o snap-in MMC do Gestor de instantâneo StorSimple

## <a name="overview"></a>Descrição geral

O Gestor de instantâneo StorSimple é um snap-in Consola de gestão da Microsoft (MMC) que simplifica proteção de dados e gestão de cópia de segurança num ambiente do Microsoft Azure StorSimple. Com o StorSimple instantâneo Gestor, pode gerir Microsoft Azure StorSimple no local e na nuvem armazenamento como se fosse um sistema de armazenamento totalmente integrada, assim simplificar os processos de cópia de segurança e restauro e reduzir os custos. 

Neste tutorial descreve os requisitos de configuração, bem como procedimentos para instalar, remover e atualizar StorSimple instantâneo gestor.

>[AZURE.NOTE] 
>
>- Não pode utilizar o Gestor de instantâneo StorSimple para gerir matrizes virtuais do Microsoft Azure StorSimple (também conhecido como StorSimple no local dispositivos virtuais).
>
>- Se planear instalar StorSimple Update 2 no seu dispositivo StorSimple, certifique-se de que transferir a versão mais recente do Gestor de instantâneo StorSimple e instalá-lo **antes de instalar StorSimple Update 2**. A versão mais recente do StorSimple instantâneo gestor é compatível com versões anteriores e funciona com todas as versões de lançamento do Microsoft Azure StorSimple. Se estiver a utilizar a versão anterior do StorSimple instantâneo Gestor, terá de atualizar (não ter de desinstalar a versão anterior antes de instalar a nova versão).

## <a name="storsimple-snapshot-manager-installation"></a>Instalação StorSimple instantâneo Gestor

Gestor de instantâneo StorSimple pode ser instalado em computadores que executam o Windows Server 2008 R2 SP1, Windows Server 2012 ou sistema operativo Windows Server 2012 R2. Servidores a executar o Windows 2008 R2, também tem de instalar o Windows Server 2008 SP1 e o Windows Management Framework 3.0. 

Antes de instalar ou atualizar o snap-in StorSimple instantâneo Manager para Microsoft Management Console (MMC), certifique-se de que o servidor de dispositivo e anfitrião do Microsoft Azure StorSimple estão corretamente configurados. 

## <a name="configure-prerequisites"></a>Configurar a pré-requisitos

Os passos seguintes fornecem uma descrição geral das tarefas de configuração que tem de concluir antes de instalar o Gestor de instantâneo StorSimple. Para concluir a configuração StorSimple do Microsoft Azure e informações de configuração, incluindo os requisitos de sistema e instruções passo a passo, consulte [Implementar o dispositivo de StorSimple no local](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Antes de começar, reveja a [lista de verificação de configuração de implementação](storsimple-deployment-walkthrough.md#deployment-configuration-checklist) e e [Pré-requisitos de implementação](storsimple-deployment-walkthrough.md#deployment-prerequisites) no [Implementar o dispositivo de StorSimple no local](storsimple-deployment-walkthrough.md).
> <br>
 
### <a name="before-you-install-storsimple-snapshot-manager"></a>Antes de instalar o Gestor de instantâneo StorSimple

1. Descompactar, montagem e ligar o dispositivo do Microsoft Azure StorSimple, conforme descrito no [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Certifique-se de que o computador anfitrião está a ser executado um dos seguintes sistemas operativos:

    - Windows Server 2008 R2 (em servidores a executar o Windows 2008 R2, também terá de instalar o Windows Server 2008 SP1 e Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    Para um dispositivo virtual StorSimple, anfitrião do tem de ser uma máquina de Virtual do Microsoft Azure. 

3. Certifique-se de que cumpre todos os requisitos de configuração de Microsoft Azure StorSimple. Para obter mais detalhes, vá para a [Pré-requisitos de implementação](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Ligar o dispositivo para o anfitrião e executar a configuração inicial. Para obter mais detalhes, vá para [passos de implementação](storsimple-deployment-walkthrough.md#deployment-steps).

5. Criar volumes no dispositivo, atribua-lhes para o anfitrião e verifique se o anfitrião do pode montagem e utilizá-los. Gestor de instantâneo StorSimple suporta os seguintes tipos de volumes: 

    - Volumes básicos
    - Volumes simples
    - Volumes dinâmicos
    - Volumes dinâmicos espelhados (RAID 1)
    - Volumes partilhados de cluster
 
    Para obter informações sobre a criação de volumes no dispositivo StorSimple ou StorSimple virtual dispositivo, aceda a [passo 6: criar um volume](storsimple-deployment-walkthrough.md#step-6-create-a-volume), no [Implementar o dispositivo de StorSimple no local](storsimple-deployment-walkthrough.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalar um Gestor de instantâneo StorSimple novo

Antes de instalar o Gestor de instantâneo StorSimple, certifique-se de que os volumes que criou no dispositivo StorSimple ou StorSimple dispositivo virtual são instalados, inicializado e formatado conforme descrito na [Pré-requisitos de configurar](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Para um dispositivo virtual StorSimple, anfitrião do tem de ser uma máquina de Virtual do Microsoft Azure. 
>
>- Tem de executar o anfitrião do Windows 2008 R2, Windows Server 2012 ou Windows Server 2012 R2. Se o seu servidor a executar o Windows Server 2008 R2, também tem de instalar o Windows Server 2008 SP1 e Windows Management Framework 3.0.
>
>- Tem de configurar uma ligação de iSCSI do anfitrião do dispositivo StorSimple antes de poder ligar o dispositivo para StorSimple instantâneo Manager.

Siga estes passos para concluir uma instalação fresca do StorSimple instantâneo gestor. Se estiver a instalar uma atualização, aceda a [atualizar ou reinstalar o Gestor de instantâneo StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Passo 1: Instalar o Gestor de StorSimple instantâneo 
- Passo 2: Ligar StorSimple instantâneo gestor para um dispositivo 
- Passo 3: Verifique a ligação para o dispositivo 

###<a name="step-1-install-storsimple-snapshot-manager"></a>Passo 1: Instalar o Gestor de StorSimple instantâneo

Utilize os passos seguintes para instalar o StorSimple instantâneo Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Para instalar o Gestor de instantâneo StorSimple

1. Transferir o software de Gestor de instantâneo StorSimple (vá para [O Gestor de instantâneo StorSimple](https://www.microsoft.com/download/details.aspx?id=44220) no Microsoft Download Center) e guardá-lo localmente no anfitrião do.

2. No Explorador de ficheiros, a pasta comprimida com o botão direito e, em seguida, clique em **extrair todos**.

3. Na janela do **pastas extrair comprimidas (Zipped)** , na caixa **selecionar um destino e extrair ficheiros** , escreva ou procure o caminho onde pretender do ficheiro para ser extraída. 

       >[AZURE.IMPORTANT] Tem de instalar o Gestor de instantâneo StorSimple na unidade c:.
 
4. Selecione a caixa de verificação **Mostrar ficheiros extraídos quando concluir** e, em seguida, clique em **extrair**.

    ![Extrair a caixa de diálogo de ficheiros](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 

4. Quando a extração de estiver concluída, abre a pasta de destino. Faça duplo clique no ícone de configuração da aplicação que aparece na pasta de destino.

5. Quando a mensagem **Configuração efetuada com êxito** for apresentada, clique em **Fechar**. Deverá ver o ícone de Gestor de instantâneo StorSimple no ambiente de trabalho.

    ![ícone de ambiente de trabalho](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Passo 2: Ligar StorSimple instantâneo gestor para um dispositivo

Utilize os seguintes passos para ligar StorSimple instantâneo gestor para um dispositivo StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Para ligar StorSimple instantâneo gestor para um dispositivo

1. Clique no ícone de Gestor de instantâneo StorSimple no ambiente de trabalho. É apresentada a janela do Gestor de instantâneo StorSimple. A janela contém um painel de **ações** , um painel de **resultados** e um painel de **âmbito** . 

    ![Interface de utilizador StorSimple instantâneo Gestor](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png) 

    - O painel de **âmbito** (no painel à esquerda) contém uma lista de nós organizados numa estrutura de árvore. Pode expandir alguns nós para selecionar uma vista ou dados específicos relacionados com para esse nó. Clique no ícone de seta para expandir ou fechar um nó. Botão direito do rato um item no painel de **âmbito** para ver uma lista de ações disponíveis para esse item. 

    - Painel de **resultados** (painel central) contém informações de estado detalhadas sobre o nó, vista ou os dados que selecionou no painel de **âmbito** .

    - Painel **ações** lista as operações que pode efetuar o nó, vista ou os dados que selecionou no painel de **âmbito** .

    Para obter uma descrição completa da interface de utilizador StorSimple instantâneo Gestor, consulte o artigo [interface de utilizador StorSimple instantâneo Gestor](storsimple-use-snapshot-manager.md).

2. No painel de **âmbito** , clique com o botão direito do rato em **dispositivos** e, em seguida, clique em **configurar um dispositivo**. É apresentada a caixa de diálogo **configurar um dispositivo** .

    ![Configurar um dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 

3. Na caixa de listagem de **dispositivo** , selecione o endereço IP do Microsoft Azure StorSimple dispositivo ou dispositivo virtual. Na caixa de texto **palavra-passe** , escreva a palavra-passe de StorSimple instantâneo gestor que criou para o dispositivo no portal do Azure clássico. Clique em **OK**.

4. Gestor de instantâneo StorSimple procura para o dispositivo que identificou. Se o dispositivo está disponível, o Gestor de instantâneo StorSimple adiciona uma ligação. Pode [verificar a ligação para o dispositivo](#to-verify-the-connection) para confirmar que a ligação foi adicionada com êxito.

    Se o dispositivo não estiver disponível por qualquer motivo, StorSimple instantâneo Gestor devolve uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo **configurar um dispositivo** .

5. Quando se liga para um dispositivo, StorSimple instantâneo Gestor importa cada grupo de volume configurado para esse dispositivo, desde que o grupo de volume tem associados cópias de segurança. Grupos de volume que não têm cópias de segurança associadas não são importados. Para além disso, a cópia de segurança políticas que foram criadas para um grupo de volume não são importadas. Para ver os grupos importados, do rato em início mais **Volume grupos** no painel de **âmbito** e clique em **botão de alternar importados grupos**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Passo 3: Verifique a ligação para o dispositivo

Utilize os passos seguintes para verificar que StorSimple instantâneo Manager está ligado ao dispositivo StorSimple.

#### <a name="to-verify-the-connection"></a>Para verificar a ligação

1. No painel de **âmbito** , clique no nó de **dispositivos** .

    ![Estado do dispositivo StorSimple instantâneo Gestor](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 

2. Verifique o painel de **resultados** : 

   - Se um indicador verde aparece no ícone do dispositivo e **disponível** é apresentado na coluna **Estado** , o dispositivo está ligado. 

   - Se um indicador vermelho aparece no ícone do dispositivo e indisponível é apresentado na coluna **Estado** , o dispositivo não está ligado. 

   - Se for apresentada **Actualizar** na coluna **Estado** , em seguida, StorSimple instantâneo Manager está a obter grupos de volume e associadas cópias de segurança para um dispositivo ligado.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Atualizar ou reinstalar StorSimple instantâneo Manager

Deve desinstalar completamente StorSimple instantâneo Gestor antes de atualizar ou reinstalar o software. 

Antes de reinstalar o Gestor de instantâneo StorSimple, criar cópias de segurança da base de dados do Gestor de instantâneo StorSimple existente no computador anfitrião. Este procedimento guarda as informações de configuração e políticas de cópia de segurança para que possa restaurar facilmente estes dados de cópia de segurança.

Se estiver a atualizar ou reinstalar StorSimple instantâneo Gestor, siga estes passos:

- Passo 1: Desinstalar o Gestor de instantâneo StorSimple 
- Passo 2: Cópia de segurança da base de dados do Gestor de instantâneo StorSimple 
- Passo 3: Reinstale o StorSimple instantâneo Manager e restaurar a base de dados 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Passo 1: Desinstalar o Gestor de instantâneo StorSimple

Utilize os passos seguintes para desinstalar StorSimple instantâneo gestor.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Para desinstalar o Gestor de instantâneo StorSimple

1. No computador anfitrião, abra o **Painel de controlo**, clique em **programas**e, em seguida, clique em **programas e funcionalidades**.

2. No painel esquerdo, clique em **desinstalar ou alterar um programa**.

3. Com o botão direito **StorSimple instantâneo Manager**e, em seguida, clique em **desinstalar**.

4. Esta ação inicia o programa de configuração de StorSimple instantâneo gestor. Clique em **Modificar configuração**e, em seguida, clique em **desinstalar**.

    >[AZURE.NOTE] Se existirem quaisquer processos MMC a ser executada em segundo plano, tal como StorSimple instantâneo gestor ou gestão do disco, irá falhar a desinstalação e receberá uma mensagem para fechar todas as instâncias da MMC antes de tentar para desinstalar o programa. Selecione **Fechar automaticamente aplicações e tente reiniciá-las após a conclusão da configuração**e, em seguida, clique em **OK**.
 
5. Quando o processo de desinstalação estiver concluído, aparece uma mensagem **Com êxito o programa de configuração** . Clique em **Fechar**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Passo 2: Cópia de segurança da base de dados do Gestor de instantâneo StorSimple

Utilize os passos seguintes para criar e guardar uma cópia da base de dados StorSimple instantâneo gestor.

#### <a name="to-back-up-the-database"></a>Para criar uma cópia de segurança da base de dados

1. Pare o serviço de gestão do Microsoft StorSimple:

   1. Inicie o Gestor de servidor.

   2. No Dashboard de Gestor de servidor, no menu **Ferramentas** , selecione **Serviços**.

   3. Na página **Serviços** , selecione o **Serviço de gestão do Microsoft StorSimple**.

   4. No painel direito, em **Serviço de gestão do Microsoft StorSimple**, clique em **Parar o serviço**.

        ![Parar o serviço StorSimple Gestor](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Navegue até à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData é uma pasta oculta.

3. Localizar o ficheiro XML de catálogo, copie o ficheiro e armazenar a cópia numa localização segura ou na nuvem.

    ![Ficheiro de cópia de segurança de catálogo de StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Reinicie o serviço de gestão do Microsoft StorSimple: 

    1. No Dashboard de Gestor de servidor, no menu **Ferramentas** , selecione **Serviços**.

    2. Na página **Serviços** , selecione o **Microsoft StorSimple gestão Servic**"e".

    3. No painel direito, em **Serviço de gestão do Microsoft StorSimple**, clique em **reiniciar o serviço**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Passo 3: Reinstalar StorSimple instantâneo gestor e restaurar a base de dados

Para reinstalar o Gestor de instantâneo StorSimple, siga os passos no [instalar um Gestor de instantâneo StorSimple novo](#install-a-new-storsimple-snapshot-manager). Em seguida, utilize o seguinte procedimento para restaurar a base de dados StorSimple instantâneo gestor.

#### <a name="to-restore-the-database"></a>Para restaurar a base de dados

1. Pare o serviço de gestão do Microsoft StorSimple:

    1. Inicie o Gestor de servidor.

    2. No Dashboard de Gestor de servidor, no menu **Ferramentas** , selecione **Serviços**.

    3. Na página **Serviços** , selecione o **Serviço de gestão do Microsoft StorSimple**.

    4. No painel direito, em **Serviço de gestão do Microsoft StorSimple**, clique em **Parar o serviço**.

2. Navegue até à C:\ProgramData\Microsoft\StorSimple\BACatalog. 

     >[AZURE.NOTE] ProgramData é uma pasta oculta.

3. Elimine o ficheiro XML de catálogo e substituí-la com a versão que guardou anteriormente.

4. Reinicie o serviço de gestão do Microsoft StorSimple: 

    1. No Dashboard de Gestor de servidor, no menu **Ferramentas** , selecione **Serviços**.

    2. Na página **Serviços** , selecione o **Serviço de gestão do Microsoft StorSimple**.

    3. No painel direito, em **Serviço de gestão do Microsoft StorSimple**, clique em **reiniciar o serviço**.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre o Gestor de instantâneo StorSimple, aceda a [o que é o Gestor de instantâneo StorSimple?](storsimple-what-is-snapshot-manager.md).

- Para saber mais sobre a interface de utilizador StorSimple instantâneo Gestor, aceda a [interface de utilizador StorSimple instantâneo Gestor](storsimple-use-snapshot-manager.md).

- Para saber mais sobre como utilizar o Gestor de instantâneo StorSimple, aceda a [Utilizar o Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
