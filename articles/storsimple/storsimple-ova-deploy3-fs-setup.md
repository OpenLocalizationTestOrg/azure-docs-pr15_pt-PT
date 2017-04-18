<properties
   pageTitle="Implementar StorSimple Virtual matriz 3 - configurar o dispositivo virtual como servidor de ficheiros"
   description="Neste tutorial terceiro na implementação StorSimple Virtual matriz indica ao configurar um dispositivo virtual como servidor de ficheiros."
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
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Implementar conjunto de StorSimple Virtual matriz - o como servidor de ficheiros

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introdução 

Este artigo aplica-se a versão do Microsoft Azure StorSimple Virtual matriz (também conhecido como o dispositivo de virtual StorSimple no local ou dispositivo virtual StorSimple) em execução Março de 2016 disponibilidade geral (das versões DG). Este artigo descreve como executar a configuração inicial, registe-se o servidor de ficheiros StorSimple, concluir a configuração de dispositivo e criar e ligar a partilhas SMB. Este é o último artigo na série de tutoriais de implementação necessárias para implementar completamente sua matriz virtual como um servidor de ficheiros ou num servidor de iSCSI.

O processo de instalação e configuração pode demorar cerca de 10 minutos para concluir.


## <a name="setup-prerequisites"></a>Pré-requisitos de configuração

Antes de configurar e configurar o seu dispositivo virtual StorSimple, certifique-se de que:

-   Possui aprovisionado um dispositivo virtual e ligado à mesma, tal como descrito na [aprovisionar uma matriz Virtual StorSimple em Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou [aprovisionar uma matriz Virtual StorSimple no VMware](storsimple-ova-deploy2-provision-vmware.md).

-   Tem a chave de registo do serviço a partir do serviço do Gestor de StorSimple que criou para gerir dispositivos virtuais StorSimple. Para obter mais informações, consulte o artigo [passo 2: obter a chave de registo do serviço](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para StorSimple Virtual matriz.

-   Se esta é a segundo ou subsequente virtual dispositivo que está a registar um serviço de Gestor de StorSimple existente, deve ter a chave de encriptação de dados do serviço. Esta chave foi gerada quando o primeiro dispositivo foi registado com êxito com este serviço. Se tiver perdido esta chave, consulte o artigo [obter a chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) para sua matriz de Virtual StorSimple.

## <a name="step-by-step-setup"></a>Programa de configuração do passo a passo

Utilize as seguintes instruções passo a passo para instalar e configurar o seu dispositivo virtual StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Passo 1: Concluir a configuração de IU local web e registar o seu dispositivo 


#### <a name="to-complete-the-setup-and-register-the-device"></a>Para concluir a configuração e registar o dispositivo

1.  Abra uma janela do browser e ligue a web local IU. Tipo: 

    `https://<ip-address of network interface>`

    Utilize o URL da ligação indicado no passo anterior. Irá ver um erro que indica que não existe um problema com o certificado de segurança do Web site. Clique em **continuar a esta página Web**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Inicie sessão no web IU do seu dispositivo virtual como **StorSimpleAdmin**. Introduza a palavra-passe de administrador dispositivo que tenham sido alteradas no passo 3: iniciar o dispositivo virtual em [aprovisionar uma matriz Virtual StorSimple em Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) ou em [aprovisionar uma matriz Virtual StorSimple no VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Que vai ser feito para a **Home** page do. Esta página descreve as definições de vários necessárias para configurar e registar o dispositivo virtual com o serviço do Gestor de StorSimple. Tenha em atenção que as **definições de rede**, **definições de proxy de Web**e **definições de tempo** são opcionais. As definições necessárias apenas são **as definições de dispositivo** e **na nuvem**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  Na página **definições de rede** em **interfaces de rede**, dados 0 serão automaticamente configurados para si. Cada interface de rede está definido por predefinição, para obter o endereço IP automaticamente (DHCP). Por conseguinte, um endereço IP, sub-rede e gateway serão automaticamente atribuídas (para IPv4 e IPv6).

    ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

    Se tiver adicionado mais do que uma interface de rede durante o aprovisionamento do dispositivo, pode configurá-los aqui. Tenha em atenção que pode configurar a interface de rede como IPv4 apenas ou como IPv4 e IPv6. IPv6 não são suportadas apenas as configurações.

1.  Os servidores DNS são necessários porque são utilizadas quando o seu dispositivo tentativas para comunicar com o seu fornecedores de serviço de armazenamento na nuvem ou para resolver o seu dispositivo pelo nome quando configurado como um servidor de ficheiros. Na página de **definições de rede** em **servidores DNS**:

    1.  Um servidor de DNS principal e secundário serão automaticamente configurado. Se optar por configurar os endereços IP estáticos, pode especificar os servidores DNS. Para elevada disponibilidade, recomendamos que configure uma principal e um servidor DNS secundário.

    2.  Clique em **Aplicar**. Isto vai aplicar e Valide as definições de rede.

2.  Na página de **definições do dispositivo** :

    1.  Atribua um **nome** de exclusivo para o seu dispositivo. Este nome pode ser 1-15 carateres e pode conter carta, números e hífenes.

    2.  Clique no ícone de **servidor de ficheiros** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) para o **tipo** de dispositivo que está a criar. Servidor de ficheiros permitirá criar pastas partilhadas.

    3.  Como o dispositivo é um servidor de ficheiros, terá de aderir o dispositivo para um domínio. Introduza um **nome de domínio**.

    1.  Clique em **Aplicar**.

2.  Será apresentada uma caixa de diálogo. Introduza as suas credenciais de domínio no formato especificado. Clique no ícone de verificação. Serão verificadas as credenciais de domínio. Verá uma mensagem de erro se as credenciais estão incorretas.

    ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Clique em **Aplicar**. Isto vai aplicar e Valide as definições de dispositivo.

    ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

    > [AZURE.NOTE]
    > 
    > Certifique-se de que a matriz virtual destina-se na sua própria unidade organizacional (OU) do Active Directory e sem objectos de política de grupo (GPO) são aplicados ao mesmo ou herdados. Política de grupo pode instalar aplicações como o software de antivírus na matriz StorSimple Virtual. Instalar software adicional não é suportada e poderia conduzir a danos nos dados. 

1.  (Opcionalmente) configure o seu servidor de proxy web. Apesar de configuração do proxy web é opcional, tenha em atenção de que se utilizar um proxy da web, só pode configurá-lo aqui.

    ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

    Na página de **proxy da Web** :

    1.  Fornecer o **URL da Web proxy** neste formato: *http://&lt;endereço IP do anfitrião ou FDQN&gt;: número de porta*. Tenha em atenção que HTTPS URLs não são suportadas.

    2.  Especificar **a autenticação** como **básicos** ou **nenhum**.

    3.  Se utilizar a autenticação, também terá de fornecer um **nome de utilizador** e **palavra-passe**.

    4.  Clique em **Aplicar**. Isto irá validar e aplicar as definições de proxy web configurado.

1.  (Opcionalmente) configure as definições de tempo para o seu dispositivo, como o fuso horário e os servidores NTP principais e secundários. Servidores NTP são necessários porque o seu dispositivo tem de sincronizar tempo para que o que pode autenticar com os fornecedores de serviço de nuvem.

    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

    Na página de **definições de tempo** :

    1.  Na lista pendente, selecione o **Fuso horário** com base na localização geográfica na qual o dispositivo está a ser implementado. Fuso horário predefinido para o seu dispositivo é PST. O dispositivo irá utilizar neste fuso horário para todas as operações agendadas.

    2.  Especificar um **servidor NTP principal** para o seu dispositivo ou aceitar o valor predefinido de time.windows.com. Certifique-se de que a rede permite que o tráfego NTP transmitir a partir do seu centro de dados à Internet.

    3.  Opcionalmente, especifique um **servidor NTP secundário** para o seu dispositivo.

    4.  Clique em **Aplicar**. Isto irá validar e aplicar as definições de período de tempo configurado.

1.  Configure as definições de nuvem para o seu dispositivo. Neste passo, terá de concluir a configuração de dispositivo local e, em seguida, registar o dispositivo com o seu serviço de Gestor de StorSimple.

    1.  Introduza a **chave de registo do serviço** que recebeu no [passo 2: obter a chave de registo do serviço](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para StorSimple Virtual matriz.

    2.  Ignorar este passo se este é o seu dispositivo primeiro registar com este serviço e vá para o passo seguinte. Se não estiver no primeiro dispositivo que está a registar com este serviço, terá de fornecer a **chave de encriptação de dados de serviço**. Esta chave é necessária com a chave de registo do serviço para se registar dispositivos adicionais com o serviço do Gestor de StorSimple. Para obter mais informações, consulte para obter a [chave de encriptação de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) web local IU.

    3.  Clique em **Registe-se**. Isto irá reinicie o dispositivo. Poderá ter de aguardar 2 a 3 minutos antes do dispositivo está registado com êxito. Depois do dispositivo tem de ser reiniciado, que irá ser feito para a página de início de sessão.

        ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
    

1.  Regressar ao portal do Azure clássico. Na página de **dispositivos** , verifique se o dispositivo tem com êxito ligado ao serviço ao procurar o estado. O estado do dispositivo deve estar **activo**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Passo 2: Concluir a configuração de dispositivo necessário

Para concluir a configuração do dispositivo do seu dispositivo StorSimple, tem de:

-   Selecione uma conta de armazenamento para associar o seu dispositivo.

-   Selecione definições de encriptação para os dados que são enviadas para a nuvem.

Execute os seguintes passos no [portal clássica Azure](https://manage.windowsazure.com/) para concluir a configuração de dispositivo necessário.

#### <a name="to-complete-the-minimum-device-setup"></a>Para concluir a configuração de dispositivo mínimo

1.  Na página de **dispositivos** , selecione o dispositivo que acabou de criar. Este dispositivo seria aparecer como **ativo**. Clique na seta contra o nome do dispositivo e, em seguida, clique em **Guia**.

2.  Clique em **Configuração concluída dispositivo** para iniciar o Assistente de dispositivo de configurar.

3.  No Assistente de dispositivo de configurar na página de **Definições básicas** , faça o seguinte:

    1.  Especificar uma conta de armazenamento para ser utilizado com o seu dispositivo. Pode selecionar uma conta de armazenamento existente nesta subscrição na lista pendente ou especificar **Adicionar mais** para escolher uma conta a partir de uma subscrição diferente.

    2.  Defina as definições de encriptação para todos os dados-em-restantes (encriptação AES) que serão enviadas para a nuvem. Para encriptar os seus dados, selecione a caixa de combinação para **Ativar chave de encriptação de armazenamento na nuvem**. Introduza uma encriptação de armazenamento na nuvem que contém 32 carateres. Volte a introduzir a chave para a confirmar. Uma chave AES 256 bits será utilizada com a tecla definidos pelo utilizador para encriptação.

    3.  Clique no ícone de verificação ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

        ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

As definições agora serão atualizadas. Depois de definições são atualizadas com êxito, o botão de configuração do dispositivo completo ser cinzento. Irá regressar à página de **Início rápido** do dispositivo.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> Pode modificar todas as outras definições de dispositivo em qualquer altura, acedendo à página **Configurar** .

## <a name="step-3-add-a-share"></a>Passo 3: Adicionar uma partilha

Execute os seguintes passos no [portal clássica Azure](https://manage.windowsazure.com/) para criar uma partilha.

#### <a name="to-create-a-share"></a>Para criar uma partilha

1.  Na página de **Início rápido** de dispositivo, clique em **Adicionar uma partilha**. Esta ação inicia o Assistente adicionar um partilhar.

    ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  Na página **Definições básicas** , faça o seguinte:

    1.  Especifique um nome exclusivo para o partilhar. O nome deve ser uma cadeia que contém os carateres de 3 e 127.

    2.  (Opcional) Forneça uma descrição para a partilha. A descrição irá ajudar a identificar os proprietários de partilhar.

    3.  Selecione um tipo de utilização para a partilha. O tipo de utilização pode ser **Tiered** ou **localmente afixados**, com em camadas a ser predefinido. Para das cargas de trabalho que requerem garantias locais, baixos latências e um melhor desempenho, selecione uma partilha **localmente afixados** . Para todos os outros dados, selecione uma partilha de **Tiered** .

    Uma partilha localmente afixada thickly está aprovisionada e assegura que os dados principais na partilha permanecem locais para o dispositivo e não ajustar na nuvem. Uma partilha em camadas outro lado pequenos está aprovisionada. Quando cria uma partilha em camadas, 10% do espaço está aprovisionado na camada local e 90% do espaço está aprovisionado na nuvem. Por exemplo, se aprovisionado um volume de 1 TB, 100 GB seria residem no espaço local e 900 GB seria utilizada na nuvem quando as camadas de dados. Isto sucessivamente implica se executar todas as espaço local no dispositivo, não é possível aprovisionar uma partilha em camadas.

1.  Especifique a capacidade de aprovisionada para a sua partilha. Tenha em atenção que a capacidade especificada deve ser mais pequena do que a capacidade disponível. Se utilizar uma partilha em camadas, o tamanho de partilhar deve estar entre 500 GB e de 20 TB. Para uma partilha localmente afixada, especifique um tamanho de partilhar entre 50 GB e 2 TB. Utilize a capacidade disponível como um guia de aprovisionamento de uma partilha. Se a capacidade de local disponível 0 GB, em seguida, irá não lhe ser permitido aprovisionar partilhas locais ou em camadas.

    ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Clique no ícone de seta ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) para ir para a página seguinte.

1.  Na página de **Definições adicionais** , atribuem as permissões para o utilizador ou grupo que irá estar a aceder a esta partilha. Especifique o nome do utilizador ou grupo de utilizadores no *<john@contoso.com>* formatar. Recomendamos que utilize um grupo de utilizadores (em vez de um único utilizador) para permitir que os privilégios de administrador aceder a estas partilhas. Depois de ter atribuído as permissões aqui, em seguida, pode utilizar o Explorador do Windows para modificar estas permissões.

    ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Clique no ícone de verificação ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Uma partilha será criada com as definições de especificado. Por predefinição, irão estar ativadas para a partilha de monitorização e cópia de segurança.

## <a name="step-4-connect-to-the-share"></a>Passo 4: Ligar para a partilha

Agora terá de ligar a partilha (s) que criou no passo anterior. Execute estes passos no seu anfitrião do Windows Server.

#### <a name="to-connect-to-the-share"></a>Para ligar a partilha

1.  Prima ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + r. Na janela executar, especifique o * \\ * como o caminho, substituir o *nome de servidor do ficheiro* com o nome do dispositivo que lhe atribuída ao seu servidor de ficheiro. Clique em **OK**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  Isto vai abrir o Explorador. Agora deverá conseguir ver as acções que criou como pastas. Selecione e faça duplo clique numa partilha (pasta) para ver os conteúdos.

    ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  Agora pode adicionar ficheiros a estas partilhas e tomar uma cópia de segurança.

![ícone de vídeo](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **vídeo disponível**

Veja o vídeo para saber como pode configurar e registar uma matriz Virtual StorSimple como um servidor de ficheiros.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## <a name="next-steps"></a>Próximos passos

Saiba como utilizar a web local da IU para [administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).
