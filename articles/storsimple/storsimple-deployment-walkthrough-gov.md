<properties 
   pageTitle="Implementar o dispositivo de StorSimple no Portal de administração pública | Microsoft Azure"
   description="Descreve os passos e práticas recomendadas para implementar o dispositivo de StorSimple atualização 1 e o serviço no portal do Azure Governo."
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
   ms.workload="NA"
   ms.date="06/17/2016"
   ms.author="v-sharos" />

# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Implementar o seu dispositivo de StorSimple no local no Portal de administração pública

[AZURE.INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## <a name="overview"></a>Descrição geral

Bem-vindo ao Microsoft Azure StorSimple implementação de dispositivo. Nestes tutoriais de implementação aplicam-se para a série de 8000 StorSimple executar software de atualização 1 no Portal de administração pública do Azure. Esta série de tutoriais descreve como configurar o seu dispositivo StorSimple e inclui uma lista de verificação de configuração, pré-requisitos de configuração e configuração detalhado passos.

As informações nestes tutoriais assume que tem revisto as precauções e descompactados, racked e ligado dispositivo StorSimple. Se ainda precisar de efetuar essas tarefas, comece com rever as [precauções de segurança](storsimple-safety.md). Dependendo do modelo do seu dispositivo, pode, em seguida, descompactar, montagem bastidor e cabo, seguindo as instruções no artigo:

- [Descompactar, bastidor montagem e cabo seu 8100](storsimple-8100-hardware-installation.md)
- [Descompactar, bastidor montagem e cabo seu 8600](storsimple-8600-hardware-installation.md)

Terá privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que reveja a lista de verificação de configuração, antes de começar. O processo de configuração e implementação pode demorar algum tempo para concluir.

> [AZURE.NOTE] As informações de implementação de StorSimple publicadas no Web site da Microsoft Azure aplica-se a dispositivos de série de StorSimple 8000 apenas. Para obter informações completas sobre os dispositivos de 7000 série, aceda a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obter informações de implementação do 7000 série, consulte o [StorSimple sistema guia de introdução](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Passos de implementação

Execute estes passos necessários para configurar o seu dispositivo StorSimple e ligá-lo no seu serviço de Gestor de StorSimple. Para além dos passos que se necessário, existem passos opcionais e procedimentos que poderá ter durante a implementação. As instruções passo a passo de implementação indicam ao deve efetuar cada um dos seguintes passos opcionais.


| Passo                                                                                   | Descrição                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **PRÉ-REQUISITOS**                                                                      | Estes tem de ser efetuados numa preparação para a implementação futura.                                                                                        |
| Lista de verificação de configuração implementação.                                                     | Utilize esta lista de verificação para recolher e registar informações antes e durante a implementação.                                                                       |
| Pré-requisitos de implementação.                                                               | Estes validam o ambiente está pronto para implementação.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **IMPLEMENTAÇÃO PASSO A PASSO**                                                                   | Estes passos são necessários para implementar o seu dispositivo StorSimple de produção.                                                                                      |
| Passo 1: Crie um novo serviço.                                                         | Configure o cloud gestão e de armazenamento para o seu dispositivo StorSimple. Ignore este passo se tiver um serviço existente para outros dispositivos StorSimple.                |
| Passo 2: Obter a chave de registo do serviço.                                               | Utilize esta tecla para registar e ligar o seu dispositivo de StorSimple com o serviço de gestão.                                                                         |
| Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple.    | Ligar o dispositivo à sua rede e registar-Azure para concluir a configuração utilizando o serviço de gestão.                                            |
| Passo 4: Concluir a configuração do dispositivo mínimo</br>Opcional: Atualize o seu dispositivo StorSimple.      | Utilize o serviço de gestão para concluir a configuração de dispositivo e ativá-la fornecer armazenamento.                                                                      |
| Passo 5: Crie um contentor de volume.                                                      | Crie um contentor a aprovisionar volumes. Tem um contentor de volume conta de armazenamento, largura de banda e definições de encriptação para todos os volumes contidas na mesma.    |
| Passo 6: Crie um volume.                                                                | Aprovisionar volumes de armazenamento no dispositivo StorSimple para os servidores de.                                                                                        |
| Passo 7: Montagem, iniciar e formatar um volume.</br>Opcional: Configure MPIO.            | Ligue os servidores ao armazenamento de iSCSI fornecido pelo dispositivo. Opcionalmente, configure MPIO para se certificar de que os seus servidores podem tolerar ligação, rede e falha da interface.                                                                                                                                                              |
| Passo 8: Tirar uma cópia de segurança.                                                                  | Configurar a política de cópia de segurança para proteger os seus dados                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **OUTROS PROCEDIMENTOS**                                                                   | Poderá ter de referir-se a estes procedimentos como implementar a sua solução.                                                                                        |
| Configure uma nova conta de armazenamento para o serviço.                                      |                                                                                                                                                               |
| Utilize betumes para ligar à consola de série do dispositivo.                                    |                                                                                                                                                               |
| Procurar e aplicar atualizações.                                                   |                                                                                                                                                               |
| Obter IQN de um anfitrião do Windows Server.                                                   |                                                                                                                                                               |
| Crie uma cópia de segurança manual.                                                                 | 
| Configure MPIO.                                                                          |


## <a name="deployment-configuration-checklist"></a>Lista de verificação de configuração de implementação

A lista de verificação de configuração de implementação seguinte descreve as informações que necessita de reunir antes de e como configurar o software no seu dispositivo StorSimple. Preparar algumas destas informações antecedência ajuda a simplificar o processo de implementação do dispositivo StorSimple no seu ambiente. Utilize esta lista de verificação para tenha também em atenção para baixo os detalhes de configuração como implementar o seu dispositivo.

| Fase                                  | Parâmetro                                         | Detalhes                                                                                                                                                                | Valores |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Cabo do seu dispositivo**                      | Acesso série                                     | Configuração do dispositivo inicial                                                                  | Sim/não |
|   |   |  |  |
| **Configurar e registar dispositivo**          | Definições de rede de dados 0                           | Endereço IP dados 0:</br>Máscara de sub-rede:</br>Gateway:</br>Servidor DNS principal:</br>Servidor de NTP principal:</br>Servidor de proxy Web IP/FQDN (opcional):</br>Porta de proxy da Web:|        |
|                                        | Palavra-passe de administrador do dispositivo                     | Palavra-passe tem de estar entre 8 e 15 caracteres que contém os carateres em minúsculas, maiúsculas, numéricos e especiais. |        |
|                                        | Palavra-passe StorSimple instantâneo Manager              | Palavra-passe tem de ser 14 ou 15 carateres que contém os carateres em minúsculas, maiúsculas, numéricos e especiais.|        |
|                                        | Chave de registo do serviço                          | Esta chave é gerada a partir do portal Azure.    |        |
|                                        | Chave de encriptação de dados de serviço                       | Esta chave é criada quando o dispositivo está registado com o serviço de gestão através do Windows PowerShell para StorSimple. Copie esta tecla e guardá-lo numa localização segura.|  |
|   |   |  |  |
| **Configuração concluída dispositivo mínimo**          | Nome amigável para o seu dispositivo                     | Este é um nome descritivo para o dispositivo. |        |
|                                        | Fuso horário                                          | O dispositivo irá utilizar neste fuso horário para todas as operações agendadas.  |        |
|                                        | Servidor de DNS secundário                              | Esta é uma configuração necessária.                                  |        |
|                                        | Interface de rede: controlador de dados 0 fixa IPs                                     | Estes IP deve ser encaminháveis à Internet.</br>Controlador de 0 fixa IP endereço:</br>Controlador de 1 fixa IP endereço:|
|   |   |  |  |
| **Definições adicionais da rede interface**  | Interface de rede: dados 1</br>Se iSCSI ativada, configure o Gateway.      | Objectivo: Na nuvem/iSCSI/não utilizados</br>Endereço IP:</br>Máscara de sub-rede:</br>Gateway:|
|                                        | Interface de rede: dados 2</br>Se iSCSI ativada, configure o Gateway.      | Objectivo: Na nuvem/iSCSI/não utilizados</br>Endereço IP:</br>Máscara de sub-rede:</br>Gateway:|
|                                        | Interface de rede: dados 3</br>Se iSCSI ativada, configure o Gateway.      | Objectivo: Na nuvem/iSCSI/não utilizados</br>Endereço IP:</br>Máscara de sub-rede:</br>Gateway:|
|                                        | Interface de rede: dados 4</br>Se iSCSI ativada, configure o Gateway.      | Objectivo: Na nuvem/iSCSI/não utilizados</br>Endereço IP:</br>Máscara de sub-rede:</br>Gateway:|
|                                        | Interface de rede: dados 5</br>Se iSCSI ativada, configure o Gateway.      | Objectivo: Na nuvem/iSCSI/não utilizados</br>Endereço IP:</br>Máscara de sub-rede:</br>Gateway:|
|   |   |  |  |
| **Criar um contentor de volume**                      | Nome do contentor de volume:                            | Nome para o contentor                                                                                                                                                 |        |
|                                        | Conta de armazenamento Azure:                            | Armazenamento nome e acesso chave da conta para associar este contentor de volume                                                                                              |        |
|                                        | Chave de encriptação de armazenamento na nuvem:                     | Chave de encriptação para armazenamento em cada contentor                                                                                                                           |        |
|   |   |  |  |
| **Criar um volume**                        | Detalhes para cada volume                           | Nome do volume:                                                                                                                                                           |        |
|                                        |                                                   | Tamanho:                                                                                                                                                                  |        |
|                                        |                                                   | Tipo de utilização:                                                                                                                                                            |        |
|                                        |                                                   | Nome do ACR:                                                                                                                                                              |        |
|                                        |                                                   | Política predefinida para cópia de segurança:                                                                                                                                                 |        |
|   |   |  |  |
| **Montagem, iniciar e formatar um volume** | Para cada servidor de anfitrião de ligar ao armazenamento de detalhes | Nome do servidor do Windows:                                                                                                                                                   |        |
|                                        |                                                   | Windows Server IQN:                                                                                                                                                    |        |
|                                        |                                                   | Nome do volume do Windows Server:                                                                                                                                                   |        |
|                                        |                                                   | NTFS montagem ponto/unidade letra:                                                                                                                                      |        |


## <a name="deployment-prerequisites"></a>Pré-requisitos de implementação

As secções seguintes explicam os pré-requisitos de configuração para o seu serviço de Gestor de StorSimple e o seu dispositivo StorSimple.

### <a name="for-the-storsimple-manager-service"></a>Para o serviço do Gestor de StorSimple

Antes de começar, certifique-se de que:

- Tem a sua conta Microsoft com credenciais de acesso.

- Tem a sua conta de armazenamento do Microsoft Azure com credenciais de acesso.

- A sua subscrição do Microsoft Azure está ativada para o serviço do Gestor de StorSimple. A sua subscrição deve ser comprada através do [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Tem acesso ao software de emulação de terminal como betumes.

### <a name="for-the-device-in-the-datacenter"></a>Para o dispositivo no Centro de dados

Antes de configurar o dispositivo, certifique-se que:

- O dispositivo é completamente descompactado, instalado no bastidor e totalmente ligado para power, rede e acesso série, tal como descrito no:

    -  [Descompactar, bastidor montagem e cabo dispositivo 8100](storsimple-8100-hardware-installation.md)
    -  [Descompactar, bastidor montagem e cabo dispositivo 8600](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Para a rede no Centro de dados

Antes de começar, certifique-se de que:

- As portas na firewall do seu centro de dados são abertas para permitir iSCSI e na nuvem tráfego, tal como descrito em [requisitos para o seu dispositivo StorSimple de rede](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>Implementação passo a passo

Utilize as seguintes instruções passo a passo para implementar o dispositivo StorSimple o Centro de dados.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço

Um serviço de Gestor de StorSimple pode gerir vários dispositivos StorSimple. Execute os passos seguintes para criar uma nova instância do serviço do Gestor de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT] Se não ativar a criação automática de uma conta de armazenamento com o seu serviço, terá de criar, pelo menos, uma conta de armazenamento depois de ter criado um serviço com êxito. Esta conta de armazenamento será utilizada quando cria um contentor de volume. 
>
> * Se não tiver criado uma conta de armazenamento automaticamente, aceda a [Configurar uma nova conta de armazenamento do serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas. 
> * Se ativou a criação automática de uma conta de armazenamento, aceda a [passo 2: obter a chave de registo do serviço](#step-2-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço

Depois do serviço de Gestor de StorSimple está a trabalhar, terá de obter a chave de registo do serviço. Esta chave é utilizada para registar e ligar o seu dispositivo de StorSimple ao serviço.

Execute os seguintes passos no Portal de administração pública.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple

Utilize o Windows PowerShell para StorSimple para concluir a configuração inicial do seu dispositivo StorSimple, tal como é explicado o procedimento seguinte. Terá de utilizar o software de emulação de terminal para concluir este passo. Para mais informações, consulte o artigo [Utilizar betumes para ligar à consola de série do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Passo 4: Concluir a configuração do dispositivo mínimo

Para a configuração de dispositivo mínimo do seu dispositivo StorSimple, são necessários para: 

- Configure o servidor de DNS secundário.
- Ative iSCSI na interface de pelo menos uma rede.
- Atribua endereços IP fixos para ambos os controladores.

Execute os seguintes passos no Portal de administração pública para concluir a configuração de dispositivo mínimo.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Passo 5: Criar um contentor de volume

Tem um contentor de volume conta de armazenamento, largura de banda e definições de encriptação para todos os volumes contidas na mesma. Terá de criar um contentor de volume antes de poder iniciar aprovisionamento volumes no seu dispositivo StorSimple. 

Execute os seguintes passos no Portal de administração pública para criar um contentor de volume.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Passo 6: Criar um volume

Depois de criar um contentor de volume, pode aprovisionar um volume de armazenamento no dispositivo StorSimple para os servidores de. Execute os seguintes passos no Portal de administração pública para criar um volume.

> [AZURE.IMPORTANT] Azure StorSimple pode criar apenas volumes pequenos aprovisionados.  Não é possível criar totalmente aprovisionada ou parcialmente aprovisionada volumes num sistema Azure StorSimple. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Passo 7: Montagem, iniciar e formatar um volume

Execute estes passos no seu anfitrião do Windows Server.

> [AZURE.IMPORTANT]

> - Para elevada disponibilidade da sua solução StorSimple, recomendamos que configure MPIO nos seus servidores de anfitrião (opcionais) antes de configurar iSCSI. Configuração de MPIO em servidores anfitriões irá Certifique-se de que os servidores de podem tolerar uma ligação, rede ou falha da interface.

> - Para MPIO e iSCSI instalação e configuração obter instruções sobre anfitrião do Windows Server, vá para [Configurar o para o seu dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Estes também irão incluir os passos para montagem, iniciar e formatar volumes de StorSimple.

> - Para MPIO e iSCSI instalação e configuração obter instruções sobre um anfitrião Linux, aceda a [Configurar o do seu anfitrião de StorSimple Linux](storsimple-configure-mpio-on-linux.md)

Se decidir não configurar MPIO, execute os seguintes passos para montagem, iniciar e formatar os volumes de StorSimple num sistema anfitrião Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Passo 8: Tirar uma cópia de segurança

Cópias de segurança no momento proteção de volumes de fornecer e melhorar o recuperação enquanto minimizar restaurar vezes. Pode tomar dois tipos de cópia de segurança no seu dispositivo StorSimple: instantâneos locais e na nuvem instantâneos. Cada um dos seguintes tipos de cópia de segurança pode ser **agendado** ou **Manual**. 

Execute os seguintes passos no Portal de administração pública para criar uma cópia de segurança agendada.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Pode demorar um manual de cópia de segurança em qualquer altura. Para obter os procedimentos, aceda a [criar uma cópia de segurança manual](#create-a-manual-backup). 

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar uma nova conta de armazenamento para o serviço

Este passo é opcional que precisa para executar a apenas se não activou a criação automática de uma conta de armazenamento com o seu serviço. Uma conta de armazenamento do Microsoft Azure é necessária para criar um contentor de volume StorSimple.

Se necessitar de criar uma conta de armazenamento Azure numa região diferente, consulte o artigo [Sobre contas de armazenamento do Azure](../storage/storage-create-storage-account.md) para obter instruções passo a passo.

Execute os seguintes passos no Portal de administração pública, na página do **Gestor de StorSimple serviço** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Utilizar betumes para ligar à consola de série do dispositivo

Para ligar ao Windows PowerShell para StorSimple, é necessário utilizar o software de emulação de terminal como betumes. Pode utilizar betumes quando aceder ao dispositivo diretamente através da consola de série ou ao abrir uma sessão de telnet a partir de um computador remoto.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Analisar e aplicar atualizações

Atualizar o seu dispositivo, pode demorar algumas horas. Execute os seguintes passos para procurar e aplicar atualizações no seu dispositivo.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Para atualizar o seu dispositivo

1.  Na página de **Início rápido** de dispositivo, clique em **dispositivos**. Selecione o dispositivo físico, clique em **Manutenção** e, em seguida, clique em **Pesquisar atualizações**.  
2.  É criada uma tarefa para verificar a existência de atualizações disponíveis. Se estão disponíveis atualizações, as **Atualizações digitalizar** alterado para **Instalar atualizações**. Clique em **instalar atualizações**. 
3.  Uma tarefa de actualização de será criada. Monitorize o estado da sua atualização ao navegar para **tarefas**.

    > [AZURE.NOTE] Quando inicia a tarefa de atualização, imediatamente apresenta o estado como 50 por cento. O estado muda para 100 por cento apenas depois da tarefa de atualização está concluída. Não existe nenhuma estado para o processo de atualização em tempo real.

4.  Depois do dispositivo é atualizado com êxito, ative interfaces de rede de dados 2 e 3 de dados se estes estão desativadas.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obter IQN de um anfitrião do Windows Server

Execute os seguintes passos para obter o iSCSI qualificado nome (IQN) de um anfitrião do Windows que está a executar o Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual

Execute os seguintes passos no Portal de administração pública para criar uma cópia de segurança manual para um único volume a pedido no seu dispositivo StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup-gov.md)]

## <a name="configure-mpio"></a>Configurar MPIO

Múltiplos caminhos e/s (MPIO) é uma funcionalidade opcional e, por predefinição, não está instalado no Windows Server. Devem ser instalada como uma funcionalidade através do Gestor de servidor. Para obter instruções de instalação MPIO, vá para [Configurar o para o seu dispositivo StorSimple](storsimple-configure-mpio-windows-server.md).

Para instruções de instalação MPIO para um dispositivo de StorSimple ligado para um anfitrião Linux, vá para [Configurar o do seu anfitrião de Linux](storsimple-configure-mpio-on-linux.md).

> [AZURE.NOTE] Não é suportada MPIO num dispositivo virtual StorSimple. 

## <a name="next-steps"></a>Próximos passos

- Configure um [dispositivo virtual](storsimple-virtual-device-u2.md).

- Utilize o [serviço do Gestor de StorSimple](https://msdn.microsoft.com/library/azure/dn772396.aspx) para gerir o seu dispositivo StorSimple.
 
