<properties 
    pageTitle="Guia de utilizador do agente de Linux | Microsoft Azure" 
    description="Saiba como instalar e configurar o agente de Linux (waagent) para gerir a interação do seu computador virtual com Azure ferro controlador." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



# <a name="azure-linux-agent-user-guide"></a>Guia de utilizador do agente de Linux Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introdução

O Microsoft Azure Linux Agent (waagent) gere Linux & FreeBSD aprovisionamento e interação VM com o controlador de ferro Azure. Fornece as seguintes funcionalidades para Linux e FreeBSD IaaS implementações:

> [AZURE.NOTE] Consulte o artigo agente do Azure Linux [Leia-me](https://github.com/Azure/WALinuxAgent/blob/master/README.md) para detalhes adicionais.

* **Imagem de aprovisionamento**
  - Criação de uma conta de utilizador
  - Configurar tipos de autenticação SSH
  - Implementação de chaves públicas SSH e os pares de chaves
  - Definir o nome do anfitrião
  - O nome do anfitrião de publicação para a plataforma de DNS
  - Relatórios SSH anfitrião chave identificação para a plataforma
  - Gestão de recursos do disco
  - Formatação e montagem o disco de recursos
  - Configurar o espaço de trocar

* **Funcionamento em rede**
  - Gere rotas para melhorar a compatibilidade com servidores do DHCP plataforma
  - Garante a estabilidade do nome da interface de rede

* **Kernel**
  - Configura an virtual (desativar para kernel < 2.6.37)
  - Consome entropia Hyper-V para /dev/random
  - Configura tempos limite de SCSI para o dispositivo de raiz (que pode ser remoto)

* **Diagnósticos**
  - Consola redirecionamento à porta série

* **SCVMM híbridas**
    - Detetar e bootstraps agente do VMM para Linux quando a ser executado num ambiente do sistema Centro Gestor de Máquina Virtual 2012 R2

* **Extensão VM**
    - Componente criado pela Microsoft e parceiros para Linux VM (IaaS) para ativar o software e automatização de configuração de inserção
    - Implementação de referência VM extensão no [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


## <a name="communication"></a>Comunicação
O fluxo de informações a partir da plataforma para o agente de ocorre através de dois canais:

* Um tempo de arranque anexados DVD para implementações IaaS. Este DVD inclui um ficheiro de configuração compatível com OVF que inclui todas as informações de aprovisionamento que não seja o keypairs SSH real.
* Um ponto final de TCP expor um REST API utilizada para obter implementação e topologia de configuração.


## <a name="requirements"></a>Requisitos de
Os seguintes sistemas testados e são conhecidos para trabalhar com o agente de Linux Azure:

> [AZURE.NOTE] Esta lista pode ser diferentes a partir da lista oficial dos sistemas suportados na plataforma do Microsoft Azure, conforme é aqui descrito: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

* CoreOS
* CentOS 6.3 +
* Chapéu vermelho Enterprise Linux 6,7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Outros sistemas suportados:

* FreeBSD 10 + (Azure Linux agente v2.0.10 +)

O agente Linux depende alguns pacotes de sistema para funcionar corretamente:

* Python 2.6 +
* OpenSSL 1.0 +
* Antes de OpenSSH 5.3 +
* Utilitários de sistema de ficheiros: sfdisk fdisk, mkfs, parted
* Ferramentas de palavra-passe: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: rota de ip
* Kernel suporte para montagem UDF filesystems.

## <a name="installation"></a>Instalação
Utilizar um rotações/minuto ou um pacote de DEBORA a partir do repositório de pacote a distribuição de instalação é o método de instalação e atualização o agente de Linux Azure preferido. Todos os os [fornecedores de distribuição a menção](virtual-machines-linux-endorsed-distros.md) integrar o pacote do agente Azure Linux as respetivas imagens e repositórios.

Consulte a documentação no [Azure Linux agente repo no Github](https://github.com/Azure/WALinuxAgent) para opções de instalação avançado, tal como instalar a partir de origem ou para localizações personalizadas ou prefixos.


## <a name="command-line-options"></a>Opções de linha de comandos

### <a name="flags"></a>Sinalizadores

- verboso: aumentar verbosidade do comando especificado
- forçar: Ignorar confirmação interativa para alguns comandos

### <a name="commands"></a>Comandos

- Ajuda: lista os comandos suportados e os sinalizadores.

- deprovision: tentativa de limpar do sistema e torná-la adequado para aprovisionamento novamente. Esta operação eliminados o seguinte procedimento:
 * Todas as chaves de anfitrião SSH (se existir Provisioning.RegenerateSshHostKeyPair y no ficheiro de configuração)
 * Configuração do servidor de nomes no /etc/resolv.conf
 * Raiz palavra-passe de /etc/shadow. (se existir Provisioning.DeleteRootPassword y no ficheiro de configuração)
 * Concessões de cliente DHCP em cache
 * Nome do anfitrião repõe para localhost.localdomain


> [AZURE.WARNING] Deprovisioning não garante que a imagem está desmarcada todas as informações confidenciais e adequado para redistribuição.


- deprovision + utilizador: executa tudo em - deprovision (acima) e também elimina a última conta de utilizador aprovisionada (obtida de /var/lib/waagent) e associada de dados. Este parâmetro é quando Aprovisiona retirar uma imagem que estava anteriormente aprovisionamento do Azure para que possam estar capturado e voltar a ser utilizada.

- versão: apresenta a versão do waagent

- serialconsole: Configura LARVAS para marcar ttyS0 (a primeira porta série) como consola de arranque. Isto assegura que os registos de arranque kernel são enviados para a porta série e disponibilizados para depuração.

- daemon: executar waagent como um daemon para gerir a interação com a plataforma. Este argumento é especificado para waagent no waagent inicialização script.

- iniciar: executar waagent como um processo de fundo


## <a name="configuration"></a>Configuração

Um ficheiro de configuração (/ etc/waagent.conf) controla as ações de waagent. Um ficheiro de configuração de exemplo é apresentado abaixo:

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

As várias opções de configuração são descritas detalhadamente abaixo. Opções de configuração são de três tipos; Booleano, a cadeia ou o número inteiro. As opções de configuração booleana podem ser especificadas como "y" ou "n". A especial palavra-chave "Nenhum" podem ser utilizados para alguns tipo configuração entradas de cadeia tal como descrito abaixo.

**Provisioning.Enabled:**  
Tipo: booleana  
Predefinido: y

Isto permite ao utilizador ativar ou desativar a funcionalidade de aprovisionamento do agente. Valores válidos são "y" ou "n". Se aprovisionamento estiver desativado, as teclas de anfitrião e utilizador SSH na imagem são preservadas e qualquer configuração especificada no Azure aprovisionamento API é ignorada.

> [AZURE.NOTE] O `Provisioning.Enabled` predefinições de parâmetro para "n" no Ubuntu nuvem imagens que utilizam a inicialização da nuvem para aprovisionar.

  
**Provisioning.DeleteRootPassword:**  
Tipo: booleana  
Predefinido: n

Se o conjunto, a palavra-passe de raiz no ficheiro de sombra/etc/é apagado durante o processo de aprovisionamento.


**Provisioning.RegenerateSshHostKeyPair:**  
Tipo: booleana  
Predefinido: y

Se o conjunto, todos os SSH anfitrião chaves pares (algoritmo ecdsa, dsa e rsa) é eliminado durante o processo de aprovisionamento de /etc/ssh /. E é gerado um par de chaves fresco único.

O tipo de encriptação para o par de chaves fresco é configurável pela entrada Provisioning.SshHostKeyPairType. Tenha em atenção que algumas distribuições irão recriar os pares de chaves SSH para todos os tipos de encriptação em falta quando o daemon SSH for reiniciado (por exemplo, após reiniciar o computador).


**Provisioning.SshHostKeyPairType:**  
Tipo: cadeia  
Predefinido: rsa

Isto pode ser definido para um tipo de algoritmo de encriptação que é suportado pelo daemon SSH na máquina virtual. Os valores normalmente suportados estão "rsa", "dsa" e "algoritmo ecdsa". Tenha em atenção que "putty.exe" no Windows não suporta "algoritmo ecdsa". Por isso, se pretender utilizar putty.exe no Windows para ligar a uma implementação Linux, utilize "rsa" ou "dsa".


**Provisioning.MonitorHostName:**  
Tipo: booleana  
Predefinido: y

Se definir, waagent vai monitorizar a máquina de virtual Linux para alterações de nome do anfitrião (tal como devolvido pelo comando "nome do anfitrião") e atualizar automaticamente a configuração de rede na imagem para refletir a alteração. Para transmitir a alteração de nome para os servidores DNS, redes vai ser reiniciado na máquina virtual. Isto resultará em Resumo perda de ligação à Internet.


**Provisioning.DecodeCustomData**  
Tipo: booleana  
Predefinido: n

Se definir, waagent irá descodificar CustomData a partir do Base64.


**Provisioning.ExecuteCustomData**  
Tipo: booleana  
Predefinido: n

Se definir, waagent executará CustomData depois de aprovisionamento.


**Provisioning.PasswordCryptId**  
Tipo: cadeia  
Predefinido: 6

Algoritmo de utilizado pelo encriptação ao gerar hash de palavra-passe.  
 1 - MD5  
 2º - Blowfish  
 5 - SHA-256  
 6 - SHA-512  


**Provisioning.PasswordCryptSaltLength**  
Tipo: cadeia  
Predefinido: 10

Comprimento do sal aleatório utilizado quando gerar hash de palavra-passe.


**ResourceDisk.Format:**  
Tipo: booleana  
Predefinido: y

Se definir, o disco de recurso fornecido pela plataforma será formatado e instalado pelo waagent se o tipo de sistema de ficheiros pedido pelo utilizador na "ResourceDisk.Filesystem" alguma coisa que não seja "ntfs". Uma única partição tipo Linux (83) será disponibilizada no disco. Tenha em atenção que esta partição não será formatada se podem ser instalado com êxito.


**ResourceDisk.Filesystem:**  
Tipo: cadeia  
Predefinido: ext4

Especifica o tipo de sistema de ficheiros para o disco de recursos. Valores suportados variam consoante a distribuição Linux. Se a cadeia é X, em seguida, mkfs. X deve estar presente na imagem Linux. Imagens de SLES 11 normalmente devem utilizar 'ext3'. Imagens de FreeBSD devem utilizar 'ufs2' aqui.


**ResourceDisk.MountPoint:**  
Tipo: cadeia  
Predefinido: recurso/mnt / 

Especifica o caminho no qual o disco de recurso é instalado. Repare que o disco de recurso é um disco *temporário* e pode ser esvaziado quando a VM será descontinuada.


**ResourceDisk.MountOptions**  
Tipo: cadeia  
Predefinido: nenhuma

Especifica disco montagem opções a transmitir ao comando montagem + o. Esta é uma lista de separados por ponto e de valores, ex. 'nodev, nosuid'. Consulte o artigo mount(8) para obter detalhes.


**ResourceDisk.EnableSwap:**  
Tipo: booleana  
Predefinido: n

Se definir, um ficheiro de trocar (/ ficheiro de comutaçao) é criada no disco recurso e adicionado espaço de trocar do sistema.


**ResourceDisk.SwapSizeMB:**  
Tipo: número inteiro  
Predefinido: 0

O tamanho do ficheiro trocar em megabytes.


**Logs.Verbose:**  
Tipo: booleana  
Predefinido: n

Se é aumentado conjunto, verbosidade de registo. Waagent inicia sessão /var/log/waagent.log e melhora a funcionalidade de logrotate de sistema para rodar registos.


**SISTEMA OPERATIVO. EnableRDMA**  
Tipo: booleana  
Predefinido: n

Se definir, o agente irá tentar instalar e, em seguida, carregar um controlador de kernel RDMA que corresponde à versão do firmware no hardware subjacente.


**SISTEMA OPERATIVO. RootDeviceScsiTimeout:**  
Tipo: número inteiro  
Predefinido: 300

Este procedimento configura o tempo limite SCSI em segundos nas unidades de disco e dados SO. Se não for definido, o sistema são utilizadas predefinições.


**SISTEMA OPERATIVO. OpensslPath:**  
Tipo: cadeia  
Predefinido: nenhuma

Isto pode ser utilizado para especificar um caminho alternativo para openssl binário para utilizar para operações de criptografia.


**HttpProxy.Host, HttpProxy.Port**  
Tipo: cadeia  
Predefinido: nenhuma

Se definir, o agente irá utilizar este servidor proxy para aceder à internet. 


## <a name="ubuntu-cloud-images"></a>Nuvem de Ubuntu imagens

Tenha em atenção que Ubuntu nuvem imagens utilizem-las [na nuvem-inicialização](https://launchpad.net/ubuntu/+source/cloud-init) para executarem várias tarefas de configuração caso contrário, deverá ser geridas pelo agente do Azure Linux.  Tenha em atenção as diferenças seguintes:


- **Provisioning.Enabled** assume a predefinição de "n" no Ubuntu nuvem imagens que utilizar inicialização na nuvem para efetuar tarefas de aprovisionamento.

- Os seguintes parâmetros de configuração não tem qualquer efeito no Ubuntu nuvem imagens que utiliza na nuvem-inicialização para gerir o disco de recurso e trocar espaço:

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Consulte os seguintes recursos para configurar o ponto de montagem do recurso no disco e trocar espaço no Ubuntu nuvem imagens durante o aprovisionamento:

 - [Ubuntu Wiki: Configurar a partições trocar](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Injectando dados personalizados para uma Máquina Virtual Azure](virtual-machines-windows-classic-inject-custom-data.md)

