<properties 
   pageTitle="Programa de configuração do matriz Virtual StorSimple iSCSI server | Microsoft Azure"
   description="Descreve como executar a configuração inicial, registe-se do seu servidor de iSCSI StorSimple e concluir a configuração do dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/18/2016"
   ms.author="alkohli" />


# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Implementar StorSimple Virtual matriz – configurar o seu dispositivo virtual como um servidor iSCSI

![fluxo de processo de configuração de iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Descrição geral

Neste tutorial de implementação aplica-se para a versão de disponibilidade geral (das versões DG) de Março de 2016 a execução do Microsoft Azure StorSimple Virtual matriz (também conhecido como o dispositivo virtual do StorSimple no local ou o dispositivo virtual StorSimple). Neste tutorial descreve como executar a configuração inicial, registe-se do seu servidor de iSCSI StorSimple, concluir a configuração de dispositivo e, em seguida, criar, montagem, iniciar e formatar volumes no servidor StorSimple dispositivo virtual iSCSI. As informações de configuração de StorSimple neste artigo aplica-se apenas ao StorSimple Virtual matrizes. 

Os procedimentos descritos aqui demorar cerca de 30 minutos para 1 hora para concluir. As informações publicadas neste artigo aplica-se apenas ao StorSimple Virtual matrizes.

## <a name="setup-prerequisites"></a>Pré-requisitos de configuração

Antes de configurar e configurar o seu dispositivo virtual StorSimple, certifique-se de que:

- Possui aprovisionado um dispositivo virtual e ligado à mesma, tal como descrito [Implementar StorSimple Virtual matriz - aprovisionar uma matriz virtual em Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implementar StorSimple Virtual matriz - aprovisionar uma matriz no VMware virtual](storsimple-ova-deploy2-provision-vmware.md).

- Tem a chave de registo do serviço a partir do serviço do Gestor de StorSimple que criou para gerir dispositivos virtuais StorSimple. Para obter mais informações, consulte o artigo **passo 2: obter a chave de registo do serviço** na [Implementar StorSimple Virtual matriz - preparar o portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Se esta é a segundo ou subsequente virtual dispositivo que está a registar um serviço de Gestor de StorSimple existente, deve ter a chave de encriptação de dados do serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com êxito com este serviço. Se tiver perdido esta chave, consulte o artigo **obter a chave de encriptação de dados de serviço** utilizado [A IU da Web para administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Programa de configuração do passo a passo 

Utilize as seguintes instruções passo a passo para instalar e configurar o seu dispositivo virtual StorSimple:

-  [Passo 1: Concluir a configuração de IU local web e registar o seu dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Passo 2: Concluir a configuração de dispositivo necessário](#step-2-complete-the-required-device-setup)
-  [Passo 3: Adicionar um volume](#step-3-add-a-volume)
-  [Passo 4: Montagem, iniciar e formatar um volume](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Concluir a configuração de IU local web e registar o seu dispositivo 

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registar o dispositivo

1. Abra uma janela do browser e ligar-se para a web IU escrevendo:

    `https://<ip-address of network interface>`

    Utilize o URL da ligação indicado no passo anterior. Irá ver um erro notificá-lo de que existe um problema com o certificado de segurança do Web site. Clique em **continuar a esta página web**.

    ![Erro de certificado de segurança](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Inicie sessão no web IU do seu dispositivo virtual como **StorSimpleAdmin**. Introduza a palavra-passe de administrador dispositivo que tenham sido alteradas no passo 3: iniciar o dispositivo virtual [Implementar StorSimple Virtual matriz - aprovisionar um dispositivo virtual em Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [Implementar StorSimple Virtual matriz - aprovisionar um dispositivo virtual na VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Página de início de sessão](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Que vai ser feito para a **Home** page do. Esta página descreve as definições de vários necessárias para configurar e registar o dispositivo virtual com o serviço do Gestor de StorSimple. Tenha em atenção que as **definições de rede**, **definições de proxy de Web**e **definições de tempo** são opcionais. As definições necessárias apenas são **as definições de dispositivo** e **na nuvem**.

    ![Home page](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. Na página **definições de rede** em **interfaces de rede**, dados 0 serão automaticamente configurados para si. Cada interface de rede está definido por predefinição, para obter um endereço IP automaticamente (DHCP). Por conseguinte, um endereço IP, sub-rede e gateway serão automaticamente atribuídas (para IPv4 e IPv6).

    Quando Planifica implementar o seu dispositivo como um servidor de iSCSI (para aprovisionar o armazenamento de bloco), recomendamos que desativar a opção **automaticamente um endereço IP obter** e configurar os endereços IP estáticos.

    ![Página de definições de rede](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Se tiver adicionado mais do que uma interface de rede durante o aprovisionamento do dispositivo, pode configurá-los aqui. Tenha em atenção que pode configurar a interface de rede como IPv4 apenas ou como IPv4 e IPv6. IPv6 não são suportadas apenas as configurações.

5. Os servidores DNS são necessários porque são utilizadas quando o seu dispositivo tentativas para comunicar com o seu fornecedores de serviço de armazenamento na nuvem ou para resolver o seu dispositivo pelo nome se estiver configurado como um servidor de ficheiros. Na página **definições de rede** em **servidores DNS**:

    1. Um servidor de DNS principal e secundário serão automaticamente configurado. Se optar por configurar os endereços IP estáticos, pode especificar os servidores DNS. Para elevada disponibilidade, recomendamos que configure uma principal e um servidor DNS secundário.

    2. Clique em **Aplicar**. Isto vai aplicar e Valide as definições de rede.

6. Na página **definições do dispositivo** :

    1. Atribua um **nome** de exclusivo para o seu dispositivo. Este nome pode ser 1-15 carateres e pode conter carta, números e hífenes.

    2. Clique no ícone de **servidor iSCSI** ![ícone do servidor iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) para o **tipo** de dispositivo que está a criar. Um servidor iSCSI permite-lhe ao armazenamento de blocos de disposição.

    3. Especifique se pretende que este dispositivo para ser façam parte de um domínio. Se o seu dispositivo é um servidor iSCSI, em seguida, aderir ao domínio é opcional. Se optar por não participar no seu servidor iSCSI para um domínio, clique em **Aplicar**, esperar para que as definições ser aplicado e, em seguida, avance para o próximo passo.

        Se pretende aderir o dispositivo para um domínio. Introduza um **nome de domínio**e, em seguida, clique em **Aplicar**.

        > [AZURE.NOTE] Se participar o seu servidor iSCSI para um domínio, certifique-se de que a sua matriz virtual destina-se na sua própria unidade organizacional (OU) do Microsoft Azure Active Directory e sem objectos de política de grupo (GPO) são aplicados ao mesmo.

    5. Será apresentada uma caixa de diálogo. Introduza as suas credenciais de domínio no formato especificado. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Serão verificadas as credenciais de domínio. Verá uma mensagem de erro se as credenciais estão incorretas.

        ![credenciais](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Clique em **Aplicar**. Isto vai aplicar e Valide as definições de dispositivo.
 
7. (Opcionalmente) configure o seu servidor de proxy web. Apesar de configuração do proxy web é opcional, tenha em atenção de que se utilizar um proxy da web, só pode configurá-lo aqui.

    ![configurar o proxy web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    Na página **Web proxy** :

    1. Fornecer o **URL da Web proxy** neste formato: *http://host-IP endereço* ou *o número de FDQN:Port*. Tenha em atenção que HTTPS URLs não são suportadas.

    2. Especificar **a autenticação** como **básicos** ou **nenhum**.

    3. Se estiver a utilizar autenticação, também terá de fornecer um **nome de utilizador** e **palavra-passe**.

    4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de proxy web configurado.
 
8. (Opcionalmente) configure as definições de tempo para o seu dispositivo, como o fuso horário e os servidores NTP principais e secundários. Servidores NTP são necessários porque o seu dispositivo tem de sincronizar tempo para que o que pode autenticar com os fornecedores de serviço de nuvem.

    ![Definições de hora](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    Na página **definições de tempo** :

    1. A partir da lista pendente, selecione o **Fuso horário** com base na localização geográfica na qual o dispositivo está a ser implementado. Fuso horário predefinido para o seu dispositivo é PST. O dispositivo irá utilizar neste fuso horário para todas as operações agendadas.

    2. Especificar um **servidor NTP principal** para o seu dispositivo ou aceitar o valor predefinido de time.windows.com. Certifique-se de que a rede permite que o tráfego NTP transmitir a partir do seu centro de dados à Internet.

    3. Opcionalmente, especifique um **servidor NTP secundário** para o seu dispositivo.

    4. Clique em **Aplicar**. Isto irá validar e aplicar as definições de período de tempo configurado.

9. Configure as definições de nuvem para o seu dispositivo. Neste passo, terá de concluir a configuração de dispositivo local e, em seguida, registar o dispositivo com o seu serviço de Gestor de StorSimple.

    1. Introduza a **chave de registo do serviço** que recebeu no **passo 2: obter a chave de registo do serviço** na [Implementar StorSimple Virtual matriz - preparar o Portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. Se não estiver no primeiro dispositivo que está a registar com este serviço, terá de fornecer a **chave de encriptação de dados de serviço**. Esta chave é necessária com a chave de registo do serviço para se registar dispositivos adicionais com o serviço do Gestor de StorSimple. Para obter mais informações, consulte [obter a chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) no seu web local IU.

    3. Clique em **Registe-se**. Isto irá reinicie o dispositivo. Poderá ter de aguardar 2 a 3 minutos antes do dispositivo está registado com êxito. Depois do dispositivo tem de ser reiniciado, que irá ser feito para a página de início de sessão.

       ![Registar dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Regressar ao portal do Azure clássico. Na página de **dispositivos** , verifique se o dispositivo tem com êxito ligado ao serviço ao procurar o estado. O estado do dispositivo deve estar **activo**.

    ![Página de dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Passo 2: Concluir a configuração de dispositivo necessário

Para concluir a configuração do dispositivo do seu dispositivo StorSimple, tem de:

- Selecione uma conta de armazenamento para associar o seu dispositivo.

- Selecione definições de encriptação para os dados que são enviadas para a nuvem.

Execute os seguintes passos no portal do Azure clássico para concluir a configuração de dispositivo necessário.

#### <a name="to-complete-the-minimum-device-setup"></a>Para concluir a configuração de dispositivo mínimo

1. Na página de **dispositivos** , selecione o dispositivo que acabou de criar. Este dispositivo seria aparecer como **ativo**. Clique na seta junto ao nome do dispositivo e, em seguida, clique em **Guia de introdução**.

    ![Página de dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Clique em **Configuração concluída dispositivo** para iniciar o Assistente de dispositivo de configurar.

    ![Configurar o Assistente de dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. No Assistente de dispositivo de configurar, na página **Definições básicas** , faça o seguinte:

   1. Especificar uma conta de armazenamento para ser utilizado com o seu dispositivo. Nesta subscrição, pode selecionar uma conta de armazenamento existente a partir da lista pendente ou pode especificar **Adicionar mais** para escolher uma conta a partir de uma subscrição diferente.

   2. Defina as definições de encriptação para todos os dados em repouso que será enviada para a nuvem. (StorSimple utiliza encriptação AES 256.) Para encriptar os seus dados, selecione a caixa de verificação de **encriptação de armazenamento na nuvem de ativar** . Introduza uma encriptação de armazenamento na nuvem que contém 32 carateres. Volte a introduzir a chave para a confirmar.

   3. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Definições básicas](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    As definições agora serão atualizadas. Depois de definições são atualizadas com êxito, o botão de configuração do dispositivo completo estará disponível. Irá regressar à página de **Início rápido** do dispositivo.                                                        

>[AZURE.NOTE]Pode modificar todas as outras definições de dispositivo em qualquer altura, acedendo à página **Configurar** .

## <a name="step-3-add-a-volume"></a>Passo 3: Adicionar um volume

Execute os seguintes passos no portal do Azure clássico para criar um volume.

#### <a name="to-create-a-volume"></a>Para criar um volume

1. Na página de **Início rápido** de dispositivo, clique em **Adicionar um volume**. Esta ação inicia o Assistente adicionar um volume.

2. No Assistente adicionar um volume, em **Definições básicas**, faça o seguinte:

    1. Fornece um nome exclusivo para o volume. O nome deve ser uma cadeia que contém os carateres de 3 e 127.

    2. Forneça uma descrição para o volume. A descrição irá ajudar a identificar os proprietários de volume.

    3. Selecione um tipo de utilização para o volume. O tipo de utilização pode ser **Tiered volume** ou **localmente afixados volume.** (**Tiered volume** é a predefinição). Para das cargas de trabalho que requerem garantias locais, baixos latências e um melhor desempenho, selecione **localmente afixados** **volume**. Para todos os outros dados, selecione **Tiered** **volume**.

        Um volume localmente afixado thickly está aprovisionado e assegura que os dados primários o volume permanecem no dispositivo e não ajustar na nuvem. Se criar um volume localmente afixado, irá verificar o dispositivo de espaço disponível nas camadas locais para aprovisionar um volume do tamanho do pedido. Criar um volume localmente afixado pode exigir capaz de dados existentes a partir do dispositivo na nuvem, e o tempo despendido para criar o volume pode ser longo. O tempo total depende do tamanho do volume aprovisionado, largura de banda de rede disponíveis e os dados no seu dispositivo.

        Um volume em camadas outro lado pequenos está aprovisionado e pode ser criado muito rapidamente. Quando cria um volume em camadas, cerca de 10% do espaço está aprovisionado na camada local e 90% do espaço está aprovisionado na nuvem. Por exemplo, se aprovisionado um volume de 1 TB, 100 GB seria residem no espaço local e 900 GB seria utilizada na nuvem quando as camadas de dados. Isto sucessivamente implica é que se executar todas as espaço local no dispositivo, não é possível aprovisionar uma partilha em camadas (uma vez que 10% não estará disponível).

    4. Especifique a capacidade de aprovisionada para o volume. Tenha em atenção que a capacidade especificada deve ser mais pequena do que a capacidade disponível. Se estiver a criar um volume em camadas, o tamanho deve estar entre 500 GB e 5 TB. Para um volume localmente afixado, especifique um tamanho de volume entre 50 GB e 500 GB. Utilize a capacidade disponível como um guia para um volume de aprovisionamento. Se a capacidade de local disponível é 0 GB, em seguida, que não é permitido aprovisionar localmente afixada ou um volume em camadas.

        ![Definições básicas](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Clique no ícone de seta ![ícone de seta](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) para ir para a página seguinte.

3. Na página **Definições adicionais** , adicione um novo registo de controlo de acesso (ACR):

    1. Fornece um **nome** para o seu ACR.

    2. Em **nome de iniciador iSCSI**, forneça o iSCSI qualificado nome (IQN) do seu anfitrião do Windows. Se não tiver o IQN, aceda a [anexo r: Get IQN de um anfitrião do Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. Recomendamos que permitem uma cópia de segurança predefinido ao selecionar a caixa de verificação **Ativar uma cópia de segurança predefinidos para este volume** . A cópia de segurança predefinidos irá criar uma política que executa em cada dia (hora do dispositivo) 22:30 e cria um instantâneo de nuvem deste volume.

        ![definições adicionais](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Esta ação inicia a tarefa da criação de volume. Verá uma mensagem de progresso semelhante ao seguinte.

        ![mensagem de progresso](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Será criado um volume com as definições de especificado. Por predefinição, a monitorização e cópia de segurança irão estar ativadas para o volume.

    5. Para confirmar que o volume foi criado com êxito, aceda à página **Volumes** . Deverá ver volume listado.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Passo 4: Montagem, iniciar e formatar um volume

Execute os passos seguintes para montagem, iniciar e formatar os volumes de StorSimple num sistema anfitrião Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Montagem, iniciar e formatar um volume

1. Inicie o iniciador de iSCSI da Microsoft.

2. Na janela do **iSCSI iniciador propriedades** , no separador **deteção** , clique em **Descobrir o Portal**.

    ![descobrir o portal](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. Na caixa de diálogo **Descobrir o Portal de destino** , forneça o endereço IP da interface de rede com capacidade de iSCSI e, em seguida, clique em **OK**.

    ![Endereço IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. Na janela do **iSCSI iniciador propriedades** , no separador **destinos** , localize os **destinos Discovered**. (Cada volume será um alvo de descoberta.) O estado do dispositivo deve aparecer como **inativo**.

    ![descoberta destinos](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Selecione um dispositivo de destino e, em seguida, clique em **Ligar**. Depois do dispositivo está ligado, deverá alterar o estado para **ligado**. (Para obter mais informações sobre como utilizar o iniciador de iSCSI da Microsoft, consulte o artigo [instalar e configurar o Microsoft iSCSI iniciador] [1].

    ![Selecione o dispositivo de destino](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. No seu anfitrião do Windows, prima a tecla de logótipo do Windows + X e, em seguida, clique em **Executar**.

7. Na caixa de diálogo **Executar** , escreva **Diskmgmt**. Clique em **OK**, e aparecerá a caixa de diálogo de **Gestão do disco** . Painel da direita apresentará os volumes no seu anfitrião.

8. Na janela de **Gestão do disco** , os volumes montados aparecerá como é mostrado na seguinte ilustração. O volume de descoberta de contexto (clique no nome do disco) e, em seguida, clique em **Online**.

    ![gestão do disco](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Botão direito do rato e selecione **Iniciar disco**.

    ![iniciar o disco 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. Na caixa de diálogo, selecione discos para iniciar e, em seguida, clique em **OK**.

    ![iniciar o disco 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. O Assistente de novo Volume simples é iniciado. Selecione um tamanho de disco e, em seguida, clique em **seguinte**.

    ![Assistente de novo volume 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Atribuir uma letra de unidade para o nível de volume e, em seguida, clique em **seguinte**.

    ![Assistente de novo volume 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Introduza os parâmetros para formatar o volume. **No Windows Server, NTFS só é suportado.** Defina a Austrália para 64K. Fornece uma etiqueta para o volume. É aconselhável recomendada para este nome ser idêntico ao nome do volume fornecido no seu dispositivo virtual StorSimple. Clique em **seguinte**.

    ![Assistente de novo volume 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Verifique os valores para o volume e, em seguida, clique em **Concluir**.

    ![Assistente de novo volume 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Os volumes aparecerá como **Online** na página de **Gestão do disco** .

    ![volumes online](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Próximos passos

Saiba como utilizar a web local da IU para [administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Anexo r: obter IQN de um anfitrião do Windows Server

Execute os seguintes passos para obter o iSCSI qualificado nome (IQN) de um anfitrião do Windows que está a executar o Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obter IQN de um anfitrião do Windows

1. Inicie o iniciador de iSCSI Microsoft no seu anfitrião do Windows.

2. Na janela do **iSCSI iniciador propriedades** , no separador **configuração** , selecione e copie a cadeia do campo **Nome do iniciador** .

    ![Propriedades de iniciador iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Guarde esta cadeia.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



