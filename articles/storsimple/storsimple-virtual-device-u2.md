<properties 
   pageTitle="Dispositivo virtual StorSimple Update 2 | Microsoft Azure"
   description="Saiba como criar, implementar e gerir um dispositivo virtual StorSimple numa rede virtual do Microsoft Azure. (Aplica-se ao StorSimple Update 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/23/2016"
   ms.author="alkohli" />

# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Implementar e gerir um dispositivo virtual StorSimple no Azure


##<a name="overview"></a>Descrição geral
O dispositivo virtual da série de StorSimple 8000 é uma capacidade adicional que vem com a sua solução do Microsoft Azure StorSimple. O dispositivo virtual StorSimple é executado numa máquina virtual numa rede virtual do Microsoft Azure e utilizá-lo para criar cópias de segurança e clonar dados a partir do seu anfitriões. Neste tutorial descreve como implementar e gerir um dispositivo virtual no Azure e é aplicável para todos os dispositivos virtuais executar software versão Update 2 e inferior.


#### <a name="virtual-device-model-comparison"></a>Comparação de modelos de dispositivo virtual

O dispositivo virtual StorSimple está disponível em dois modelos, um padrão 8010 (anteriormente conhecido como o 1100) e um prémio 8020 (introduzidos nas atualização 2). Uma comparação entre os dois modelos é tabulada abaixo.


| Modelo de dispositivo          | 8010<sup>1</sup>                                                                     | 8020                                                                                                                               |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacidade máxima**      | 30 TB                                                                     | 64 TB                                                                                                                                |
| **Azure VM**              | Standard_A3 (4 núcleos, 7 GB de memória)                                                                      | Standard_DS3 (4 núcleos, 14 GB de memória)                                                                                                                          |
| **Compatibilidade entre versões** | Versões em execução previamente atualizar 2 ou posterior                                             | Versões em execução Update 2 ou posterior                                                                                                  |
| **Disponibilidade de região**   | Todas as regiões Azure                                                         | Azure regiões que suportam o armazenamento de Premium<br></br>Para obter uma lista das regiões, consulte [suportados regiões para 8020](#supported-regions-for-8020) |
| **Tipo de armazenamento**          | Utiliza o armazenamento do Windows Azure padrão para discos locais<br></br> Saiba como [criar uma conta de armazenamento padrão]() | Utiliza o armazenamento do Windows Azure Premium para discos locais<sup>2</sup> <br></br>Saiba como [criar uma conta de armazenamento Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)                                                               |
| **Orientação de carga de trabalho**     | Item nível obtenção de ficheiros a partir de cópias de segurança                                              | Dev Center em nuvem e testar cenários, baixa latência, mais elevadas desempenho das cargas de trabalho <br></br>Dispositivo secundário, para recuperação de falhas                                                                                            |
 
<sup>1</sup> *anteriormente conhecido como o 1100*.

<sup>2</sup> *a 8010 e 8020 utilizam o armazenamento de padrão do Azure para a camada na nuvem. A diferença existe apenas na camada local dentro do dispositivo*.

#### <a name="supported-regions-for-8020"></a>Regiões suportadas para 8020

As regiões de armazenamento Premium são atualmente suportadas para 8020 estão distribuídos num quadro abaixo. Esta lista irá constantemente atualizada Premium armazenamento fica disponível em regiões mais. 

| S. nenhum.                                                  | Atualmente suportados pelo regiões |
|---------------------------------------------------------|--------------------------------|
| 1                                                       | Central (EUA)                     |
| 2                                                       |  Leste dos EUA                       |
| 3                                                       |  Leste dos EUA 2                     |
| 4                                                       | Ocidental dos e.u.a.                        |
| 5                                                       | Europa Norte                   |
| 6                                                       | Europa Ocidental                    |
| 7                                                       | Sudeste asiático                 |
| 8                                                       | Japão leste                     |
| 9                                                       | Japão oeste                     |
| 10                                                      | Leste Austrália                 |
| 11                                                      | Austrália Sudeste *           |
| 12                                                      | Ásia *                     |
| 13                                                      | US Central Sul *              |

* Armazenamento de premium foi iniciado recentemente nestes geos.

Este artigo descreve o processo de implementação de um dispositivo virtual StorSimple no Azure passo a passo. Depois de ler este artigo, irá:

- Compreenda como o dispositivo virtual difere o dispositivo físico.

- Poderá criar e configurar o dispositivo virtual.

- Ligar para o dispositivo virtual.

- Saiba como trabalhar com o dispositivo virtual.

Neste tutorial aplica-se a todos os StorSimple virtuais dispositivos que executem o Update 2 e superior. 

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Como o dispositivo virtual difere o dispositivo físico

O dispositivo virtual StorSimple é uma versão só de software do StorSimple que é executada num único nó no Microsoft Azure Máquina Virtual. O dispositivo virtual suporta cenários de recuperação de falhas na qual o dispositivo físico não está disponível e é adequado para utilização em obtenção ao nível do item a partir de cópias de segurança, recuperação de falhas no local e na nuvem Dev Center e teste cenários.

#### <a name="differences-from-the-physical-device"></a>Diferenças do dispositivo física

A tabela seguinte mostra algumas diferenças fundamentais entre o dispositivo virtual StorSimple e o dispositivo físico StorSimple.

|                             | Dispositivo físico                                          | Dispositivo virtual                                                                            |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Localização**                    | Encontra-se no Centro de dados.                               | É executado no Azure.                                                                            |
| **Interfaces de rede**          | Tem seis interfaces de rede: dados 0 a 5 de dados.                  | Tem apenas uma interface de rede: dados 0.                                        |
| **Registo**                | Registados durante o passo de configuração.                | O registo é uma tarefa em separado.                                                          |
| **Chave de encriptação de dados de serviço** | Gerar no dispositivo físico e, em seguida, atualize o dispositivo virtual com a nova chave.           | Não é possível gerar a partir do dispositivo virtual. |


## <a name="prerequisites-for-the-virtual-device"></a>Pré-requisitos para o dispositivo virtual

As secções seguintes explicam os pré-requisitos configuração para o seu dispositivo virtual StorSimple. Antes de implementar um dispositivo virtual, reveja as [Considerações de segurança para utilizar um dispositivo virtual](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Requisitos do Azure

Antes de aprovisionar o dispositivo virtual, precisa de efetuar as seguintes preparações no seu ambiente Azure:

- Para o dispositivo virtual, [Configurar uma rede virtual no Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Se utilizar o armazenamento de Premium, terá de criar uma rede virtual numa região Azure que suporta o armazenamento de Premium. Mais informações sobre [regiões que são atualmente suportadas para 8020](#supported-regions-for-8020).
- É aconselhável utilizar o servidor DNS predefinido fornecido pela Azure em vez de especificar o seu próprio nome de servidor DNS. Se o nome do servidor DNS não é válido ou se não conseguir resolver os endereços IP corretamente o servidor de DNS, a criação do dispositivo virtual irá falhar.
- Site de ponto e site para o site são opcional, mas não são obrigatórios. Se pretender, pode configurar estas opções para cenários mais avançadas. 
- Pode criar [máquinas virtuais do Azure](../virtual-machines/virtual-machines-linux-about.md) (servidores do anfitrião) na rede virtual que pode utilizar os volumes expostos pelo dispositivo virtual. Nestes servidores tem de cumprir os seguintes requisitos:                            
    - Ser Windows ou Linux VMs com iSCSI software iniciador instalado.
    - Estar em execução na mesma rede virtual como o dispositivo virtual.
    - Será possível ligar ao destino iSCSI do dispositivo virtual através do endereço IP interno do dispositivo virtual.

- Certifique-se de que configurou suporte para o tráfego iSCSI e na nuvem na mesma rede virtual.


#### <a name="storsimple-requirements"></a>Requisitos de StorSimple

Antes de criar um dispositivo virtual de fazer as seguintes atualizações no seu serviço de Azure StorSimple:


- Adicione [registos de controlo de acesso](storsimple-manage-acrs.md) para as VMs que irão ser servidores de anfitriões para o seu dispositivo virtual.

- Utilize uma [conta de armazenamento](storsimple-manage-storage-accounts.md#add-a-storage-account) na mesma região como o dispositivo virtual. Contas de armazenamento de diferentes regiões podem resultar em mau desempenho. Pode utilizar uma conta padrão ou armazenamento Premium com o dispositivo virtual. Obter mais informações sobre como criar uma [conta de armazenamento padrão]() ou uma [conta de armazenamento de Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Utilize uma conta de armazenamento diferente para a criação de dispositivo virtual da que utilizou para os seus dados. Utilizando a mesma conta de armazenamento pode resultar em mau desempenho.

Certifique-se de que tem as seguintes informações antes de começar:

- A clássica portal conta Azure com credenciais de acesso.

- Uma cópia da chave de encriptação de dados de serviço a partir do seu dispositivo físico.


## <a name="create-and-configure-the-virtual-device"></a>Criar e configurar o dispositivo virtual

Antes de executar estes procedimentos, certifique-se de que se satisfeitos os [Pré-requisitos para o dispositivo virtual](#prerequisites-for-the-virtual-device). 

Depois de ter criado uma rede virtual, configurado um serviço de Gestor de StorSimple e registado o seu dispositivo StorSimple físico com o serviço, pode utilizar os seguintes passos para criar e configurar um dispositivo virtual StorSimple. 

### <a name="step-1-create-a-virtual-device"></a>Passo 1: Criar um dispositivo virtual

Execute os passos seguintes para criar o dispositivo virtual StorSimple.

[AZURE.INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Se a criação do dispositivo virtual falhar neste passo, poderá não ter conectividade à Internet. Para obter mais informações, aceda a [resolver problemas de falhas de conectividade de Internet](#troubleshoot-internet-connectivity-errors) quando creatig um dispositivo virtual.


### <a name="step-2-configure-and-register-the-virtual-device"></a>Passo 2: Configurar e registar o dispositivo virtual

Antes de iniciar este procedimento, certifique-se de que tem uma cópia da chave de encriptação de dados de serviço. A chave de encriptação de dados do serviço foi criada quando tiver configurado o seu dispositivo StorSimple primeiro e lhe foram indicado guardá-lo numa localização segura. Se não tiver uma cópia da chave de encriptação de dados de serviço, deve contactar a Microsoft Support para obter assistência.

Execute os seguintes passos para configurar e registar o seu dispositivo virtual StorSimple.
[AZURE.INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Passo 3: (Opcional) modificar as definições de configuração do dispositivo

A secção seguinte descreve as definições de configuração do dispositivo necessário para o StorSimple dispositivo virtual se pretender utilizar CHAP, StorSimple instantâneo gestor ou alterar a palavra-passe de administrador do dispositivo.

#### <a name="configure-the-chap-initiator"></a>Configurar o iniciador CHAP

Este parâmetro contém as credenciais de que o seu dispositivo virtual (destino) espera do iniciadores (servidores) que estão a tentar aceder os volumes. Os iniciadores irão fornecer um nome de utilizador CHAP e uma palavra-passe CHAP se identifiquem para o seu dispositivo durante esta autenticação. Para obter passos detalhados, consulte [Configurar CHAP para o seu dispositivo](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurar o destino da CHAP

Este parâmetro contém as credenciais de que o seu dispositivo virtual utiliza quando um iniciador com capacidade de CHAP os pedidos de autenticação comum ou bidirecional. Dispositivo virtual irá utilizar inverter CHAP nome de utilizador e palavra-passe inverter CHAP para identificar propriamente dito ao iniciador de durante este processo de autenticação. Repare que as definições de destino CHAP são as definições globais. Quando estes forem aplicados, todos os volumes ligados ao dispositivo virtual armazenamento irão utilizar manual. Para obter passos detalhados, consulte [Configurar CHAP para o seu dispositivo](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurar a palavra-passe StorSimple instantâneo Gestor

Software do Gestor de instantâneo StorSimple reside no seu anfitrião do Windows e permite aos administradores gerir cópias de segurança do seu dispositivo StorSimple sob a forma de local e na nuvem instantâneos.

>[AZURE.NOTE] Para o dispositivo virtual, o seu anfitrião do Windows é uma máquina de virtual Azure.

Quando configurar um dispositivo no Gestor de instantâneo StorSimple, vai ser-lhe que forneça o endereço IP do dispositivo StorSimple e palavra-passe para autenticar o dispositivo de armazenamento. Para obter passos detalhados, aceda a [palavra-passe configurar o StorSimple instantâneo Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Alterar a palavra-passe de administrador do dispositivo 

Ao utilizar a interface do Windows PowerShell para aceder ao dispositivo virtual, será exigido que para introduzir uma palavra-passe de administrador do dispositivo. Para a segurança dos seus dados, são necessários para alterar esta palavra-passe antes do dispositivo virtual pode ser utilizado. Para obter passos detalhados, aceda a [palavra-passe de administrador de dispositivo configurar](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Ligar remotamente ao dispositivo virtual
Acesso remoto para o seu dispositivo virtual através da interface do Windows PowerShell não está ativado por predefinição. Tem de ativar primeiro gestão remota no dispositivo virtual e, em seguida, ative-o no cliente que será utilizado para aceder ao seu dispositivo virtual.

O processo de dois passos para ligar remotamente é detalhado abaixo.

### <a name="step-1-configure-remote-management"></a>Passo 1: Configurar a gestão remota

Execute os seguintes passos para configurar a gestão remota para o seu dispositivo virtual StorSimple.

[AZURE.INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Passo 2: Aceder remotamente o dispositivo virtual

Depois de ter ativado gestão remota na página de configuração de dispositivo de StorSimple, pode utilizar o Windows PowerShell remoto para ligar para o dispositivo virtual a partir de outra máquina virtual dentro da mesma rede virtual; Por exemplo, pode ligar do anfitrião do VM que configurado e utilizado para ligar iSCSI. Na maioria das implementações, que irá já tenha aberto um ponto final de público para aceder ao seu sistema anfitrião VM que pode utilizar para aceder ao dispositivo virtual.

>[AZURE.WARNING] **Para maior segurança, recomendamos que utilize HTTPS quando se liga aos pontos finais e em seguida, elimine os pontos finais depois de concluir a sua sessão remota do PowerShell.**

Deve seguir os procedimentos [para ligar remotamente ao seu dispositivo StorSimple](storsimple-remote-connect.md) para configurar a comunicação remota para o seu dispositivo virtual.

## <a name="connect-directly-to-the-virtual-device"></a>Ligar diretamente para o dispositivo virtual

Também pode ligar diretamente para o dispositivo virtual. Se pretender ligar diretamente para o dispositivo virtual a partir de outro computador fora da rede virtual ou fora do ambiente do Microsoft Azure, tem de criar os pontos finais adicionais, tal como descrito no procedimento que se segue. 

Execute os passos seguintes para criar um ponto final de público no dispositivo virtual.

[AZURE.INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Recomendamos que liga a partir de outra máquina virtual dentro da mesma rede virtual porque esta prática minimiza o número de pontos finais públicos na sua rede virtual. Quando utilizar este método, basta ligar à máquina virtual através de uma sessão de ambiente de trabalho remoto e, em seguida, configurar essa máquina virtual para utilização, tal como faria com qualquer outro cliente Windows numa rede local. Não tem de acrescentar o número da porta público porque a porta já será conhecida.

## <a name="work-with-the-storsimple-virtual-device"></a>Trabalhar com o dispositivo virtual StorSimple

Agora que tiver criado e configurado o dispositivo virtual StorSimple, está pronto para começar a trabalhar com o mesmo. Pode trabalhar com contentores de volume, volumes e políticas de cópia de segurança num dispositivo virtual tal como faria num dispositivo StorSimple físico; a única diferença é que é necessário para se certificar de que seleciona o dispositivo virtual da sua lista de dispositivo. Referem-se para [utilizar o serviço do Gestor de StorSimple para gerir um dispositivo virtual](storsimple-manager-service-administration.md) para obter os procedimentos passo a passo as várias tarefas de gestão para o dispositivo virtual.

As secções seguintes abordam algumas das diferenças que irá encontrar quando trabalha com o dispositivo virtual.

### <a name="maintain-a-storsimple-virtual-device"></a>Manter um dispositivo virtual StorSimple

Porque é um dispositivo só de software, a manutenção para o dispositivo virtual é mínima quando comparados com a manutenção para o dispositivo físico. Tem as seguintes opções:

- **Atualizações de software** – pode ver a data em que o software foram atualizados pela última vez, juntamente com qualquer atualizar mensagens de estado. Pode utilizar o botão **Digitalizar atualizações** na parte inferior da página para efetuar uma análise manual, se pretender verificar a existência de novas atualizações. Se estão disponíveis atualizações, clique em **Instalar actualizações** para instalar. Porque não existe uma única interface no dispositivo virtual, isto significa que não haja uma interrupção do serviço ligeira quando estão aplicadas atualizações. O dispositivo virtual irá encerrar e reiniciar (se necessário) para aplicar atualizações que foram disponibilizadas. Para obter um procedimento passo a passo, aceda ao [atualizar o seu dispositivo](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Suporte de pacote** – pode criar e carregar um pacote de suporte para o ajudar a Microsoft Support resolução de problemas com o seu dispositivo virtual. Para obter um procedimento passo a passo, consulte [criar e gerir um pacote de suporte](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Contas de armazenamento para um dispositivo virtual

Contas de armazenamento são criadas para utilização pelo serviço StorSimple Gestor, o dispositivo virtual e o dispositivo físico. Quando cria suas contas de armazenamento, recomendamos que utilize um identificador de região no nome amigável para ajudar a garantir que a região é consistente ao longo de todos os componentes de sistema. Para um dispositivo virtual, é importante que todos os componentes estar na mesma região para evitar problemas de desempenho.

Para obter um procedimento passo a passo, consulte [Adicionar uma conta de armazenamento](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Desativar um dispositivo virtual StorSimple

Desativá-los um dispositivo virtual elimina a VM e os recursos criados quando foi aprovisionado. Depois do dispositivo virtual é desactivado, não pode ser restaurada para o respetivo estado anterior. Antes de desativar o dispositivo virtual, certifique-se parar ou eliminar clientes e anfitriões que dependem.

Desativá-los um dispositivo virtual resulta nas seguintes ações:

- O dispositivo virtual é removido.

- O disco de sistema operativo e discos de dados criados para o dispositivo virtual são removidos.

- O serviço alojado e de rede virtual criado durante o aprovisionamento são retidas. Se não estiver a utilizá-las, deverá eliminá-los manualmente.

- Instantâneo de nuvem criado para o dispositivo virtual é retido.

Para obter um procedimento passo a passo, aceda a [desativar e eliminar o seu dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

Assim que o dispositivo virtual é apresentado como desativada na página do Gestor de StorSimple serviço, pode eliminar o dispositivo virtual a partir da lista de dispositivo na página de **dispositivos** .


### <a name="start-stop-and-restart-a-virtual-device"></a>Iniciar, parar e reiniciar um dispositivo virtual
Ao contrário o dispositivo físico StorSimple, não existe nenhuma power em ou potência desativar botão para transmitir num dispositivo virtual StorSimple. No entanto, poderão existir ocasiões onde precisa para parar e reinicie o dispositivo virtual. Por exemplo, algumas actualizações poderão exigir que seja reiniciado a VM para concluir o processo de atualização. A forma mais fácil para si iniciar, parar e reiniciar um dispositivo virtual é utilizar a consola de gestão de máquinas virtuais.

Quando observe a consola de gestão, o estado do dispositivo virtual está **em execução** porque é iniciado por predefinição após ter sido criada. Pode iniciar, parar e reiniciar uma máquina virtual em qualquer altura.

[AZURE.INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Repor as predefinições de fábrica do mesmo

Se decidir que pretende apenas recomeçar com o seu dispositivo virtual, basta desativar e eliminá-la e, em seguida, crie um novo. Tal como quando o seu dispositivo físico é reposto, o novo dispositivo virtual não terão atualizações instaladas; por isso, certifique-se para verificar a existência de actualizações antes de utilizá-lo.


## <a name="fail-over-to-the-virtual-device"></a>Falha ao longo para o dispositivo virtual

Recuperação de falhas (DR) é um dos cenários principais que o dispositivo virtual StorSimple foi concebido para o. Neste cenário, o dispositivo StorSimple físico ou centro de dados inteiro poderá não estar disponível. Felizmente, pode utilizar um dispositivo virtual para restaurar operações numa localização alternativa. Durante DR, os contentores de volume a partir do dispositivo de origem alterar a propriedade e são transferidos para o dispositivo virtual. As pré-requisitos para DR são que tenha sido criado e configurado o dispositivo virtual, todos os volumes dentro do contentor de volume foi reencaminhados offline e o contentor de volume tem associado um instantâneo da nuvem.

>[AZURE.NOTE] 
>
> - Quando utilizar um dispositivo virtual como o dispositivo secundário para DR, tenha em atenção que a 8010 tem 30 TB de armazenamento padrão e 8020 tem 64 TB de armazenamento de Premium. O dispositivo de virtual capacidade ao 8020 superior pode ser mais adequado para um cenário de DR.
> - Não é possível activação pós-falha ou clonar a partir de um dispositivo em execução Update 2 para um dispositivo de executar 1 software anterior à atualização. No entanto pode falhar ao longo de um dispositivo em execução Update 2 para um dispositivo de executar a atualização 1 (1.1 ou 1.2)

Para obter um procedimento passo a passo, aceda ao [activação pós-falha para um dispositivo virtual](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## <a name="shut-down-or-delete-the-virtual-device"></a>Encerrar ou eliminar o dispositivo virtual

Se anteriormente configurado e utilizado um StorSimple dispositivo virtual, mas agora pretende parar de vencem ao cluster de taxas para a sua utilização, pode encerrar o dispositivo virtual. Encerrar o dispositivo virtual, não elimina o sistema operativo ou discos de dados no armazenamento. Parar de taxas vencem na sua subscrição, mas continuarão taxas de armazenamento para os discos SO e dados.

Se eliminar ou encerrar o dispositivo virtual, aparecerá como **Offline** na página de dispositivos do serviço do Gestor de StorSimple. Pode escolher desativar ou eliminar o dispositivo se pretender eliminar as cópias de segurança criadas pelo dispositivo virtual também. Para mais informações, consulte o artigo [desativar e eliminar um dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

   
## <a name="troubleshoot-internet-connectivity-errors"></a>Resolução de problemas de conectividade de Internet 

Durante a criação de um dispositivo virtual, caso não haja nenhuma conectividade à Internet, o passo de criação falhará. Para resolver a falha se devido a ligação à Internet, execute os seguintes passos no portal do Azure clássico:

1. Crie uma máquina de virtual do Windows server 2012 no Azure. Esta máquina virtual deve utilizar a mesma conta de armazenamento, VNet e sub-rede conforme utilizado pelo seu dispositivo virtual. Se já tiver um anfitrião do Windows Server existente no Azure utilizando a mesma conta de armazenamento, Vnet e sub-rede, também pode utilizá-lo para resolver problemas de conectividade de Internet.
2. Registo remoto para a máquina virtual que criou no passo anterior. 
3. Abra uma janela de comando dentro da máquina virtual (Win + R e em seguida, escreva `cmd`).
4. Execute o seguinte cmd na linha de comandos.

    `nslookup windows.net`

5. Se `nslookup` falhar, em seguida, falha de conectividade de Internet está a impedir que o dispositivo virtual registar para o serviço do Gestor de StorSimple. 
6. Efetue as alterações necessárias para a sua rede virtual para garantir que o dispositivo virtual é possível aceder à sites Azure tal como "windows.net".

## <a name="next-steps"></a>Próximos passos

- Saiba como [utilizar o serviço do Gestor de StorSimple para gerir um dispositivo virtual](storsimple-manager-service-administration.md).
 
- Compreender como [restaurar um volume StorSimple a partir de um conjunto de cópia de segurança](storsimple-restore-from-backup-set.md). 

