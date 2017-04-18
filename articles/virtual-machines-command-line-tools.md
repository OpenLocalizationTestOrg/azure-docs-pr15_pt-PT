<properties
    pageTitle="Azure comandos de clip no modo de gestão de serviços | Microsoft Azure"
    description="Comandos de interface de linha de comandos Azure (CLI) no modo de gestão do serviço para gerir implementações no modelo de implementação clássico"
    services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="danlep"/>

# <a name="azure-cli-commands-in-azure-service-management-asm-mode"></a>Azure comandos de clip no modo de gestão de serviço do Azure (asm)

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)]Também pode [obter mais informações acerca de todos os comandos do modelo de Gestor de recursos](virtual-machines/azure-cli-arm-commands.md)e utilizar o clip de [Migrar recursos](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md) de clássica ao modelo de Gestor de recursos.

Este artigo fornece sintaxe e opções para comandos Azure clip que normalmente, é utilizado para criar e gerir Azure recursos no modelo de implementação clássica. Aceder a estes comandos executando o clip no modo de gestão de serviço do Azure (asm). Não se trata de uma referência completa e sua versão do clip poderá mostrar comandos ligeiramente diferentes ou parâmetros. 

Para começar a, primeiro, [instale o clip do Azure](xplat-cli-install.md) e [ligar à sua subscrição do Azure](xplat-cli-connect.md).

Sintaxe do comando atual e opções na linha de comandos, escreva `azure help` ou, para apresentar a ajuda para um comando específico, `azure help [command]`. Também encontre exemplos de clip na documentação para criar e gerir serviços Azure específicos.

Parâmetros opcionais são apresentados entre parênteses Retos (por exemplo, `[parameter]`). Todos os outros parâmetros são necessários.

Para além de parâmetros opcionais específicas do comando aqui documentados, existem três parâmetros opcionais que podem ser utilizados para apresentar resultados detalhados como opções do pedido e os códigos de estado. O `-v` parâmetro fornece registo verboso bem como a `-vv` parâmetro fornece ainda mais detalhadas registo verboso. O `--json` opção exporta o resultado no formato de json observou.

## <a name="setting-asm-mode"></a>Definir as asm modo

Utilize o seguinte comando para ativar os comandos do modo de gestão de serviços de clip Azure.

    azure config mode asm

>[AZURE.NOTE] Modo de Gestor de recursos do Azure e no modo de gestão de serviço do Azure o clip são mutuamente exclusivos. Isto é, recursos criados no modo de um não podem ser geridos a partir de outro modo.

## <a name="manage-your-account-information-and-publish-settings"></a>Gerir as informações da conta e definições de publicação
Uma forma que a clip pode ligar à sua conta é ao utilizar as informações de subscrição Azure. (Consulte o artigo [ligar a uma subscrição Azure a partir do clip o Azure](xplat-cli-connect.md) para obter outras opções.) Esta informação pode ser obtida a partir do portal clássico Azure num ficheiro de definições de publicar, conforme é aqui descrito. Pode importar o ficheiro de definições de publicar, tal como acontece persistent local definição de configuração que o clip utiliza para operações subsequentes. Só precisa de importar as definições de publicação uma vez.

**transferência de conta [opções]**

Este comando inicia num browser para transferir o ficheiro .publishsettings a partir do Azure portal clássico.

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**conta importar [opções] &lt;ficheiro >**


Este comando importa um ficheiro de publishsettings ou o certificado para que pode ser utilizado pela ferramenta de sessões no futuro.

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

> [AZURE.NOTE] O ficheiro publishsettings pode conter detalhes (ou seja, o nome da subscrição e o ID) sobre mais do que uma subscrição. Quando importar o ficheiro publishsettings, a primeira subscrição é utilizada como a descrição predefinida. Para utilizar uma subscrição diferente, execute o seguinte comando:
<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**conta desmarque [opções]**

Este comando remove o publishsettings armazenados que foram importados. Utilize este comando se tiver terminado de utilizar a ferramenta de nesta máquina e pretende assegurar que a ferramenta não pode ser utilizada com a sua conta em sessões futuras.

    ~$ azure account clear
    Clearing account info.
    info:   OK

**lista de contas de [opções]**

Lista as subscrições importadas

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
    -----  -------
    data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**conta definida [opções] &lt;subscrição&gt;**

Definir a subscrição atual

###<a name="commands-to-manage-your-affinity-groups"></a>Comandos para gerir os seus grupos afinidade

**lista de grupo afinidade conta [opções]**

Este comando lista seus grupos afinidade Azure.

Pode ser definidos grupos afinidade quando um grupo de máquinas virtuais abrange múltiplas máquinas físicas. O grupo de afinidade Especifica que os computadores físicos devem ser perto entre si quanto possível, para reduzir a latência da rede.

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**grupo de afinidade conta criar [opções] &lt;nome&gt;**

Este comando cria um grupo de afinidade

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**grupo de afinidade conta Mostrar [opções] &lt;nome&gt;**

Este comando mostra os detalhes do grupo afinidade

    ~$ azure account affinity-group show opentec
    info:    Executing command account affinity-group show
    + Getting affinity groups
    data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
    data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
    data:    Name "opentec"
    data:    Label "b3BlbnRlYw=="
    data:    Description $ i:nil "true"
    data:    Location "West US"
    data:    HostedServices ""
    data:    StorageServices ""
    data:    Capabilities Capability 0 "PersistentVMRole"
    data:    Capabilities Capability 1 "HighMemory"
    info:    account affinity-group show command OK

**conta afinidade grupo eliminar [opções] &lt;nome&gt;**

Este comando elimina o grupo afinidade especificado

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

###<a name="commands-to-manage-your-account-environment"></a>Comandos para gerir o seu ambiente de conta

**lista de envelope conta [opções]**

Lista dos ambientes de conta

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**Envelope conta Mostrar [opções] [ambiente]**

Mostrar detalhes de ambiente de conta

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
    info:    account env show command OK

**conta envelope adicionar [opções] [ambiente]**

Este comando adiciona um ambiente para a conta

**Envelope conta definir [opções] [ambiente]**

Este comando define o ambiente de conta

**conta envelope eliminar [opções] [ambiente]**

Este comando elimina o ambiente especificado da conta

## <a name="commands-to-manage-your-classic-virtual-machines"></a>Comandos para gerir as suas máquinas virtuais clássicas
O diagrama seguinte mostra como clássico Azure máquinas virtuais estão alojadas no ambiente de implementação de produção de um serviço de nuvem Azure.

![Diagrama técnico Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**Criar novo** cria a unidade de armazenamento de BLOBs (ou seja, e:\ no diagrama); **anexar** anexa um disco já criado, mas não anexado a uma máquina virtual.

**VM criar [opções] &lt;nome de dns > &lt;imagem > &lt;nome de utilizador > [palavra-passe]**

Este comando cria uma máquina virtual Azure. Por predefinição, cada máquina virtual (vm) é criada na sua própria serviço na nuvem. Pode especificar que uma máquina virtual deve ser adicionada a um serviço na nuvem existente através da utilização da opção - c, conforme é documentado aqui.

A vm criar comando, como o portal do Azure clássico, apenas cria máquinas virtuais no ambiente de implementação de produção. Não existe nenhuma opção para criar uma máquina virtual no ambiente de implementação de transição de um serviço na nuvem. Se a sua subscrição não tiver uma conta de armazenamento Azure existente, o comando cria um.

Pode especificar uma localização através do – parâmetro de localização, ou pode especificar um grupo de afinidade através de parâmetro – afinidade grupo. Se nenhuma for fornecida, lhe for pedido para fornecer um partir de uma lista de localizações válidas.

A palavra-passe fornecida tem de ser 8 123 carateres de comprimento e cumprir os requisitos de complexidade de palavra-passe do sistema operativo que está a utilizar para esta máquina virtual.

Se planeia utilizar SSH para gerir uma máquina de virtual Linux implementada (como faz normalmente as maiúsculas e minúsculas), tem de ativar SSH através da opção -e quando cria a máquina virtual. Não é possível ativar SSH após a criação de máquina virtual.

Máquinas virtuais de Windows pode ativar RDP mais tarde, adicionando porta 3389 como um ponto final.

São suportados os seguintes parâmetros opcionais para este comando:

**- c, – ligar** criar a máquina virtual dentro de uma implementação já criada num serviço de alojamento. Se não for utilizado - vmname com esta opção, o nome da nova máquina virtual é gerado automaticamente.<br />
**-n, o nome do – vm** Especifique o nome da máquina virtual. Este parâmetro leva-o até hospedagem nome do serviço por predefinição. Se não for especificado - vmname, o nome para a nova máquina virtual é gerado como &lt;nome do serviço >&lt;id >, onde &lt;id > é o número de máquinas virtuais existentes no serviço, bem como 1. Por exemplo, se utilizar este comando para adicionar uma máquina virtual para um serviço de alojamento Omeuserviço que tem uma máquina de virtual existente, a nova máquina virtual chama MyService2.<br />
**-u, – blob url** Especifique o URL de armazenamento de BLOBs de destino na qual pretende criar o disco do sistema máquina virtual. <br />
**-z, o tamanho da memória virtual –-passe** Especifique o tamanho da máquina virtual. Os valores válidos são: "ExtraSmall", "Pequena", "Medium", "Grandes", "ExtraLarge", "A5", "A6", "A7", "A8", "A9", "A10", "A11", "Basic_A0", "Basic_A1", "Basic_A2", "Basic_A3", "Basic_A4", "Standard_D1", "Standard_D2", "Standard_D3", "Standard_D4", "Standard_D11", "Standard_D12", "Standard_D13", "Standard_D14", "Standard_DS1", "Standard_DS2", "Standard_DS3", "Standard_DS4", "Standard_DS11", "Standard_DS12", "Standard_DS13", "Standard_DS14", "Standard_G1", "Standard_G2" , "Standard_G3", "Standard_G4", "Standard_G55". O valor predefinido é "Pequenas". <br />
**-r** Adiciona conectividade RDP para uma máquina de virtual do Windows. <br />
**-e-ssh** Adiciona SSH conectividade para uma máquina de virtual do Windows. <br />
**-t, – ssh-orientação do diapositivo notas** Especifica o certificado SSH. <br />
**-s** A subscrição <br />
**+ o, – Comunidade** A imagem especificada é uma imagem de Comunidade <br />
**-w** O nome de rede virtual <br/>
**-l, – localização** Especifica a localização (por exemplo, "América do Norte Central-nos"). <br />
**-um, – afinidade grupo** Especifica o grupo afinidade.<br />
**-w, – nome de rede virtual** Especifique a rede virtual no qual pretende adicionar a nova máquina virtual. Redes virtuais podem configurar o e geridas a partir do portal do Azure clássico.<br />
**-b, – nomes de sub-rede** Especifica os nomes de sub-rede para atribuir a máquina virtual.

Neste exemplo, o MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB é uma imagem fornecida pela plataforma. Para mais informações acerca de imagens do sistema operativo, consulte o artigo vm lista de imagens.

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************
    info:   vm create command OK

**VM criar-a partir do &lt;nome de dns > &lt;função ficheiro >**

Este comando cria uma máquina virtual Azure a partir de um ficheiro de função JSON.

    ~$ azure vm create-from my-vm example.json
    info:   OK

**lista de VM [opções]**

Este comando lista máquinas virtuais Azure. A opção de – json Especifica que os resultados são devolvidos no formato JSON não processado.

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**lista de localizações da VM [opções]**

Este comando lista todas as localizações disponíveis conta Azure.

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name
    data:   ---------------------  ------------
    data:   Azure Preview  West US
    info:   account location list command OK

**VM Mostrar [opções] &lt;nome >**

Este comando apresenta os detalhes sobre uma máquina virtual Azure. A opção de – json Especifica que os resultados são devolvidos no formato JSON não processado.

    ~$ azure vm show my-vm
    info:   Executing command vm show
    data:   {
    data:       InstanceSize: 'Small',
    data:       InstanceStatus: 'ReadyRole',
    data:       DataDisks: [],
    data:       IPAddress: '10.26.192.206',
    data:       DNSName: 'my-vm.cloudapp.net',
    data:       InstanceStateDetails: {},
    data:       VMName: 'my-vm',
    data:       Network: {
    data:           Endpoints: [
    data:               {
    data:                   Protocol: 'tcp',
    data:                   Vip: '65.52.250.250',
    data:                   Port: '63238' ,
    data:                   LocalPort: '3389',
    data:                   Name: 'RemoteDesktop'
    data:               }
    data:           ]
    data:       },
    data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
    data:   }
    info:   vm show command OK

**VM eliminar [opções] &lt;nome >**

Este comando elimina uma máquina virtual Azure. Por predefinição, este comando não elimina o BLOBs do Azure a partir do qual são criados no disco do sistema operativo e o disco de dados. Para eliminar o blob e máquina virtual no qual se baseia, especifique a opção -b.

    ~$ azure vm delete my-vm
    info:   Executing command vm delete
    info:   vm delete command OK

**VM iniciar [opções] &lt;nome >**

Este comando inicia uma máquina virtual Azure.

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**Reinicie o VM [opções] &lt;nome >**

Este comando reinicia uma máquina virtual Azure.

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**VM encerramento [opções] &lt;nome >**

Este comando encerra uma máquina virtual Azure. Pode utilizar a opção -p para especificar que o recurso cluster não são lançadas no encerramento.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**captura de VM &lt;vm nome > &lt;nome da imagem de destino >**

Este comando para capturar uma imagem de máquina virtual Azure.

Só pode ser capturada uma imagem de máquina virtual se o estado da máquina virtual for **parado**. Encerre a máquina virtual antes de continuar.

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**Exportar VM [opções] &lt;vm nome > &lt;caminho do ficheiro >**

Este comando exporta uma imagem de máquina virtual Azure para um ficheiro

    ~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

##  <a name="commands-to-manage-your-azure-virtual-machine-endpoints"></a>Comandos para gerir o seu os pontos finais de máquina virtual Azure
O diagrama seguinte mostra a arquitetura de uma implementação típica de várias instâncias de uma máquina virtual clássica. Neste exemplo, a porta 3389 está aberta em cada máquina virtual (para acesso RDP). Também existe um endereço IP interno (por exemplo, 168.55.11.1) em cada máquina virtual que é utilizada pelo balanceador de carga para encaminhar tráfego para a máquina virtual. Este endereço IP interno pode também ser utilizado para a comunicação entre máquinas virtuais.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Aceda externos pedidos para máquinas virtuais através de um balanceador de carga. Por esta razão, pedidos não não possível especificar contra uma máquina virtual específica no implementações com várias máquinas virtuais. Para implementações com várias máquinas virtuais, tem de ser configurado mapeamento de portas entre as máquinas virtuais (vm porta) e o Balanceador de carga (lb porta).

**criar o ponto final de VM &lt;vm nome > &lt;lb porta > [vm porta]**

Este comando cria um ponto final de máquina virtual. Também pode utilizar -u ou --ativar direct-servidor ENTER para especificar se, para ativar o servidor direta devolver neste ponto final, desativada por predefinição.

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**vm ponto final criar-múltiplos [opções] &lt;vm nome > &lt;lb porta > [:&lt;vm porta > [:&lt;protocolo > [:&lt;ativar direct-servidor ENTER > [:&lt;nome do conjunto lb > [:&lt;sonda protocolo > [:&lt;sonda porta > [:&lt;sonda caminho > [:&lt;nome interno lb >]]] {1 -*}**

Crie múltiplos vm pontos finais.

**ponto final de VM eliminar [opções] &lt;vm nome > &lt;nome do ponto final >**

Este comando elimina um ponto final de máquina virtual.

    ~$ azure vm endpoint delete my-vm http
    azure vm endpoint delete my-vm http
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**lista de ponto final VM &lt;vm nome >**

Este comando lista todos os pontos finais de máquina virtual. A opção de – json Especifica que os resultados são devolvidos no formato JSON não processado.

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port
    data:   ----  -------------  ----------
    data:   ssh   22             22

**actualização de ponto final da VM [opções] &lt;vm nome > &lt;nome do ponto final >**

Este comando atualizações de um ponto final de vm para novos valores utilizando estas opções.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**ponto final de VM Mostrar [opções] &lt;vm nome >**

Este comando mostra os detalhes dos pontos finais numa vm

    ~$ azure vm endpoint show "mycouchvm"
    info:    Executing command vm endpoint show
    + Getting virtual machines
    data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
    data:    Network Endpoints 0 LocalPort "5984"
    data:    Network Endpoints 0 Name "CouchDB_EP"
    data:    Network Endpoints 0 Port "5984"
    data:    Network Endpoints 0 Protocol "tcp"
    data:    Network Endpoints 0 Vip "168.61.9.97"
    data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
    data:    Network Endpoints 1 LocalPort "2020"
    data:    Network Endpoints 1 Name "CouchEP_2"
    data:    Network Endpoints 1 Port "2020"
    data:    Network Endpoints 1 Protocol "tcp"
    data:    Network Endpoints 1 Vip "168.61.9.97"
    data:    Network Endpoints 2 LocalPort "3389"
    data:    Network Endpoints 2 Name "RemoteDesktop"
    data:    Network Endpoints 2 Port "3389"
    data:    Network Endpoints 2 Protocol "tcp"
    data:    Network Endpoints 2 Vip "168.61.9.97"
    info:    vm endpoint show command OK

## <a name="commands-to-manage-your-azure-virtual-machine-images"></a>Comandos para gerir as suas imagens de máquina virtual Azure

Imagens de máquina virtual estão capturas de máquinas virtuais já configuradas que podem ser replicadas conforme necessário.

**lista de imagens VM [opções]**

Este comando obtém uma lista de imagens de máquina virtual. Existem três tipos de imagens: imagens criadas pela Microsoft, que são com o prefixo "MSFT", imagens criadas por terceiros, que são o prefixo com o nome do fornecedor e imagens criar. Para criar imagens, pode capturar uma máquina virtual existente ou criar uma imagem a partir de um VHD personalizado carregados ao armazenamento blob. Para mais informações sobre como utilizar um VHD personalizado, consulte o artigo imagem vm criar.
A opção de – json Especifica que os resultados são devolvidos no formato JSON não processado.

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK

**apresentação de imagem VM [opções] &lt;nome >**

Este comando mostra os detalhes de uma imagem de máquina virtual.

    ~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
    + Fetching VM image
    info:   Executing command vm image show
    data:   {
    data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
    data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
    data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
    data:       Category: 'Microsoft',
    data:       OS: 'Windows',
    data:       Eula: 'http://www.microsoft.com',
    data:       LogicalSizeInGB: '30'
    data:   }
    info:   vm image show command OK

**eliminação de imagem VM [opções] &lt;nome >**

Este comando elimina uma imagem de máquina virtual.

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image
    info:   vm image delete command OK

**Criar imagem VM &lt;nome > [caminho de origem]**

Este comando cria uma imagem de máquina virtual. Ficheiros personalizados. vhd são carregados blob armazenamento e, em seguida, a imagem de máquina virtual é criada a partir daí. Em seguida, utilize esta imagem máquina virtual para criar uma máquina virtual. Os parâmetros de localização e o sistema operativo são necessários.

>[AZURE.NOTE]Atualmente este comando só suporta o carregamento dos ficheiros. VHD fixo. Para carregar um ficheiro. VHD dinâmicos, utilize os [utilitários de Azure VHD para ir](https://github.com/Microsoft/azure-vhd-utils-for-go).

Alguns sistemas imponham por processar o ficheiro descritor limites. Se for excedido este limite, a ferramenta apresenta um erro de limite de descritor do ficheiro. Pode executar o comando novamente utilizando -p &lt;número > parâmetro para reduzir o número máximo de carregamentos paralelos. O número máximo predefinido de carregamentos paralelos é 96.

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

## <a name="commands-to-manage-your-azure-virtual-machine-data-disks"></a>Comandos para gerir os discos de dados de máquina virtual Azure

Dados discos são ficheiros. vhd no armazenamento blob do que podem ser utilizados por uma máquina virtual. Para obter mais informações sobre como discos de dados são implementados blob armazenamento, consulte o artigo o diagrama técnico Azure mostrado anteriormente.

Os comandos para anexar discos de dados (disco azure vm anexar e azure vm disco anexar-novo) atribuir um lógicos unidade LUN (número) para o disco de dados anexados, conforme necessário ao protocolo SCSI. O primeiro disco dados anexado a uma máquina virtual está atribuído LUN 0, o próximo é atribuído LUN 1 e assim sucessivamente.

Quando um disco de dados com o disco azure vm desanexar desanexar comando, utilize o &lt;lun&gt; parâmetros para indicar qual disco desanexar.

>[AZURE.NOTE] Qual deve sempre desanexar discos dados pela ordem inversa, começando com o LUN mais altos numeradas que foi atribuído. A camada Linux SCSI não suporta desligamento um LUN numeradas inferior enquanto um LUN superior numeradas ainda está ligado. Por exemplo, não deve desanexar LUN 0 se LUN 1 ainda está ligado.

**apresentação de disco VM [opções] &lt;nome >**

Este comando apresenta os detalhes sobre um disco Azure.

    ~$ azure vm disk show anucentos-anucentos-0-20120524070008
    info:   Executing command vm disk show
    data:   AttachedTo DeploymentName "mycentos"
    data:   AttachedTo HostedServiceName "myanucentos"
    data:   AttachedTo RoleName "myanucentos"
    data:   OS "Linux"
    data:   Location "Azure Preview"
    data:   LogicalDiskSizeInGB "30"
    data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
    data:   Name "mycentos-mycentos-0-20120524070008"
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**lista de discos VM [opções] [nome do vm]**

Este comando listas Azure discos ou discos ligados a uma máquina virtual especificada. Se é executado com um parâmetro de nome de máquina virtual, devolve todos os discos anexados à máquina virtual. LUN 1 é criado com a máquina virtual e quaisquer outros discos listados estão anexados separadamente.

    ~$ azure vm disk list mycentos
    info:   Executing command vm disk list
    data:   Lun  Size(GB)  Blob-Name
    data:   ---  --------  --------------------------------
    data:   1    30        mycentos-cb39b8223b01f95c.vhd
    data:   2    10        mycentos-e3f0d717950bb78d.vhd
    info:   vm disk list command OK

Executar este comando sem um parâmetro de nome de máquina virtual devolve todos os discos.

    ~$ azure vm disk list
    data:   Name                                        OS
    data:   ------------------------------------------  -------
    data:   mycentos-mycentos-0-20120524070008          Linux
    data:   mycentos-mycentos-2-20120525055052
    data:   mywindows-winvm-20120522223119              Windows
    info:   vm disk list command OK

**eliminar de disco VM [opções] &lt;nome >**

Este comando elimina um disco Azure a partir de um repositório de pessoal. O disco tem de ser desligado do máquina virtual antes de ser eliminada.

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052
    info:   vm disk delete command OK

**Criar VM disco &lt;nome > [caminho de origem]**

Este comando os carregamentos pendentes e registos de um disco Azure. – blob url, – localização, ou --afinidade grupo tem de ser especificado. Se utilizar este comando com [caminho de origem], o ficheiro. vhd especificado é carregado e é criada uma imagem. Em seguida, pode anexar esta imagem para uma máquina virtual utilizando vm disco anexar.

Alguns sistemas imponham por processar o ficheiro descritor limites. Se for excedido este limite, a ferramenta apresenta um erro de limite de descritor do ficheiro. Pode executar o comando novamente utilizando -p &lt;número > parâmetro para reduzir o número máximo de carregamentos paralelos. O número máximo predefinido de carregamentos paralelos é 96.

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**carregamento de disco VM [opções] &lt;caminho de origem > &lt;blob url > &lt;chave da conta de armazenamento >**

Este comando permite-lhe carregar um disco vm

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**anexar VM disco &lt;vm nome > &lt;nome da imagem de disco >**

Este comando anexa um disco existente no armazenamento blob para uma máquina virtual existente implementada num serviço de nuvem.

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**VM disco anexar-novo &lt;vm nome > &lt;tamanho no gb > [blob url]**

Este comando anexa um disco de dados para uma máquina virtual Azure. Neste exemplo, 20 é o tamanho do disco novo, em gigabytes, deve ser anexado. Opcionalmente, pode utilizar um URL de BLOBs como argumento de último para especificar o blob de destino para criar explicitamente. Se não especificar um URL de BLOBs, um objeto de BLOBs é gerado automaticamente.

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**disco VM desanexar &lt;vm nome > &lt;lun >**

Este comando separa um disco de dados ligado a uma máquina virtual Azure. &lt;LUN > identifica o disco para ser destaquem. Para obter uma lista de discos associada a um disco antes que desanexá-lo, utilize a lista de discos vm &lt;vm nome >.

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

## <a name="commands-to-manage-your-azure-cloud-services"></a>Comandos para gerir os seus serviços na nuvem Azure

Serviços em nuvem Azure são aplicações e serviços alojados no web funções e funções de trabalho. Os comandos seguintes podem ser utilizados para gerir serviços em nuvem Azure.

**serviço de criar [opções] &lt;serviceName >**

Este comando cria um serviço na nuvem

    ~$ azure service create newservicemsopentech
    info:    Executing command service create
    + Getting locations
    help:    Location:
      1) East Asia
      2) Southeast Asia
      3) North Europe
      4) West Europe
      5) East US
      6) West US
      : 6
    + Creating cloud service
    data:    Cloud service name newservicemsopentech
    info:    service create command OK

**serviço Mostrar [opções] &lt;serviceName >**

Este comando mostra os detalhes de um serviço em nuvem Azure

    ~$ azure service show newservicemsopentech
    info:    Executing command service show
    + Getting cloud service
    data:    Name newservicemsopentech
    data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
    data:    Properties location West US
    data:    Properties label newservicemsopentech
    data:    Properties status Created
    data:    Properties dateCreated
    data:    Properties dateLastModified
    info:    service show command OK

**lista de serviços [opções]**

Este comando lista serviços em nuvem Azure.

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**o serviço de eliminar [opções] &lt;nome >**

Este comando elimina um serviço em nuvem Azure.

    ~$ azure service delete myservice
    info:   Executing command service delete myservice
    info:   cloud-service delete command OK

Para forçar a eliminação, utilize o `-q` parâmetro.


## <a name="commands-to-manage-your-azure-certificates"></a>Comandos para gerir os seus certificados Azure

Os certificados de serviço Azure são certificados SSL ligados à sua conta Azure. Para mais informações sobre os certificados Azure, consulte o artigo [Gerir certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**lista de orientação do diapositivo notas serviço [opções]**

Este comando lista Azure certificados.

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services
    + Fetching certificates
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
    info:   service cert list command OK

**certificado de serviço criar &lt;prefixo dns > &lt;ficheiro > [palavra-passe]**

Este comando carrega um certificado. Deixe o pedido de palavra-passe em branco para certificados que não são protegido palavra-passe.

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password:
    + Creating certificate
    info:   service cert create command OK

**eliminar de orientação do diapositivo notas serviço [opções] &lt;impressão digital >**

Este comando elimina um certificado.

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

## <a name="commands-to-manage-your-web-apps"></a>Comandos para gerir as suas aplicações web

Uma aplicação Azure web é uma configuração de web acessível pelo URI. Aplicações Web estão alojadas em máquinas virtuais do, mas não necessita de pensar sobre os detalhes de criar e implementar a máquina virtual. Os detalhes são processados por si pelo Azure.

**lista de sites [opções]**

Este comando lista as suas aplicações web.

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**conjunto de site [opções] [nome]**

Este comando define opções de configuração para a sua aplicação web [nome]

    ~$ azure site set
    info:    Executing command site set
    Web site name: mydemosite
    + Getting sites
    + Updating site config information
    info:    site set command OK

**site deploymentscript [opções]**

Este comando gera um script de implementação personalizada

    ~$ azure site deploymentscript --node
    info:    Executing command site deploymentscript
    info:    Generating deployment script for node.js Web Site
    info:    Generated deployment script files
    info:    site deploymentscript command OK

**site criar [opções] [nome]**

Este comando cria uma aplicação web e diretório local.

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

> [AZURE.NOTE] O nome do site tem de ser exclusivo. Não é possível criar um site com o mesmo nome DNS de um site existente.

**Procurar site [opções] [nome]**

Este comando abre a sua aplicação web num browser.

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**apresentação de site [opções] [nome]**

Este comando mostra detalhes de uma aplicação web.

    ~$ azure site show mysite
    info:   Executing command site show
    info:   Showing details for site
    data:   Site AdminEnabled true
    data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
    data:   Site Name mysite
    data:   Site Owner 00060000814EDDEE
    data:   Site RepositorySiteName mysite
    data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
    data:   Site State Running
    data:   Site UsageState Normal
    data:   Site WebSpace northeuropewebspace
    data:   Config AppSettings
    data:   Config ConnectionStrings
    data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
    data:   Config DetailedErrorLoggingEnabled false
    data:   Config HttpLoggingEnabled false
    data:   Config Metadata
    data:   Config NetFrameworkVersion v4.0
    data:   Config NumberOfWorkers 1
    data:   Config PhpVersion 5.3
    data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
    data:   Config RequestTracingEnabled false
    data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
    info:   site show command OK

**Eliminar site [opções] [nome]**

Este comando elimina uma aplicação web.

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

 **trocar site [opções] [nome]**

Este comando troca duas faixas de aplicação web.

Este comando suporta a opção adicional que se segue:

**- q ou **– silencioso * *: não pedir confirmação. Utilize esta opção em scripts automatizados.


**início do site [opções] [nome]**

Este comando inicia uma aplicação web.

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**parar de site [opções] [nome]**

Este comando deixa de uma aplicação web.

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**reiniciar site [opções] [nome]**

Este comando deixa de e, em seguida, inicia uma aplicação web especificado.

Este comando suporta a opção adicional que se segue:

**– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.


**lista de localização do site [opções]**

Este comando lista as localizações da aplicação web.

    ~$ azure site location list
    info:    Executing command site location list
    + Getting locations
    data:    Name
    data:    ----------------
    data:    West Europe
    data:    West US
    data:    North Central US
    data:    North Europe
    data:    East Asia
    data:    East US
    info:    site location list command OK

###<a name="commands-to-manage-your-web-app-application-settings"></a>Comandos para gerir as definições da aplicação web app

**lista de appsetting site [opções] [nome]**

Este comando lista a definição de aplicação adicionada à aplicação web.

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**site appsetting adicionar [opções] &lt;keyvaluepair > [nome]**

Este comando adiciona uma definição de aplicação para a sua aplicação web como um par de valor de chave.

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**site appsetting eliminar [opções] &lt;chave > [nome]**

Este comando elimina a definição de aplicação especificado a partir da aplicação web.

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**site appsetting Mostrar [opções] &lt;chave > [nome]**

Este comando apresenta os detalhes da definição da aplicação especificado

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

###<a name="commands-to-manage-your-web-app-certificates"></a>Comandos para gerir os seus certificados de aplicação web

**lista de orientação do diapositivo notas site [opções] [nome]**

Este comando apresenta uma lista dos certificados de aplicação web.

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**certificado de site adicionar [opções] &lt;caminho de certificados > [nome]**

**eliminação de orientação do diapositivo notas site [opções] &lt;impressão digital > [nome]**

**apresentação de orientação do diapositivo notas [opções] do site &lt;impressão digital > [nome]**

Este comando mostra os detalhes de orientação do diapositivo notas

    ~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    Executing command site cert show
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Certificate hostNames 0=msopentech.azurewebsites.net
    data:    Certificate expirationDate
    data:    Certificate friendlyName msopentech.azurewebsites.net
    data:    Certificate issueDate
    data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
    data:    Certificate subjectName msopentech.azurewebsites.net
    data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    site cert show command OK

###<a name="commands-to-manage-your-web-app-connection-strings"></a>Comandos para gerir o seu cadeias de ligação de aplicação web

**lista de connectionstring site [opções] [nome]**

**site connectionstring adicionar [opções] &lt;NomeLigação > &lt;valor > &lt;tipo > [nome]**

**site connectionstring eliminar [opções] &lt;NomeLigação > [nome]**

**site connectionstring Mostrar [opções] &lt;NomeLigação > [nome]**

###<a name="commands-to-manage-your-web-app-default-documents"></a>Comandos para gerir os seus documentos de predefinido de aplicação web

**lista de defaultdocument site [opções] [nome]**

**site defaultdocument adicionar [opções] &lt;documento > [nome]**

**site defaultdocument eliminar [opções] &lt;documento > [nome]**

###<a name="commands-to-manage-your-web-app-deployments"></a>Comandos para gerir o seu implementações da aplicação web

**lista de implementação do site [opções] [nome]**

**implementação do site, Mostrar [opções] &lt;commitId > [nome]**

**novo lançamento de implementação site [opções] &lt;commitId > [nome]**

**site implementação github [opções] [nome]**

**conjunto de utilizador de implementação do site [opções] [nomeutilizador] [passo]**

###<a name="commands-to-manage-your-web-app-domains"></a>Comandos para gerir os seus domínios do web app

**lista de domínios do site [opções] [nome]**

**site domínio adicionar [opções] &lt;dn > [nome]**

**eliminação de domínio do site [opções] &lt;dn > [nome]**

###<a name="commands-to-manage-your-web-app-handler-mappings"></a>Comandos para gerir os mapeamentos de processador de aplicação web

**lista de processador de site [opções] [nome]**

**processador de site adicionar [opções] &lt;extensão > &lt;processador > [nome]**

**eliminação de processador de site [opções] &lt;extensão > [nome]**

###<a name="commands-to-manage-your-web-jobs"></a>Comandos para gerir as suas tarefas de Web

**lista de tarefas do site [opções] [nome]**

Este comando listar todas as tarefas de web numa aplicação web.

Este comando suporta as seguintes opções adicionais:

+ **– tipo de tarefa** &lt;tipo de tarefa >: opcional. O tipo do webjob. Um valor válido é "acionadas" ou "contínua". Por predefinição devolve webjobs de todos os tipos de.
+ **– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.

**Mostrar tarefa [opções] do site &lt;nometarefa > &lt;propriedade jobType > [nome]**

Este comando mostra os detalhes de uma tarefa de web específica.

Este comando suporta as seguintes opções adicionais:

+ **– nome da tarefa** &lt;nome da tarefa >: obrigatório. O nome do webjob.
+ **– tipo de tarefa** &lt;tipo de tarefa >: obrigatório. O tipo do webjob. Um valor válido é "acionadas" ou "contínua".
+ **– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.

**Eliminar tarefa do site [opções] &lt;nometarefa > &lt;propriedade jobType > [nome]**

Este comando elimina a tarefa de web especificada.

Este comando suporta as seguintes opções adicionais:

+ **– nome da tarefa** &lt;nome da tarefa > necessários. O nome do webjob.
+ **– tipo de tarefa** &lt;tipo de tarefa > necessários. O tipo do webjob. Um valor válido é "acionadas" ou "contínua".
+ **-q** ou **– sossegada,**: não pedir confirmação. Utilize esta opção em scripts automatizados.
+ **– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.

**o carregamento de trabalho de site [opções] &lt;nometarefa > &lt;propriedade jobType > <jobFile> [nome]**

Este comando elimina a tarefa de web especificada.

Este comando suporta as seguintes opções adicionais:

+ **– nome da tarefa** &lt;nome da tarefa >: obrigatório. O nome do webjob.
+ **– tipo de tarefa** &lt;tipo de tarefa >: obrigatório. O tipo do webjob. Um valor válido é "acionadas" ou "contínua".
+ **– ficheiro da tarefa** &lt;ficheiro da tarefa >: obrigatório. O ficheiro da tarefa.
+ **– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.

**início do trabalho [opções] do site &lt;nometarefa > &lt;propriedade jobType > [nome]**

Este comando inicia a tarefa de web especificada.

Este comando suporta as seguintes opções adicionais:

+ **– nome da tarefa** &lt;nome da tarefa >: obrigatório. O nome do webjob.
+ **– tipo de tarefa** &lt;tipo de tarefa >: obrigatório. O tipo do webjob. Um valor válido é "acionadas" ou "contínua".
+ **– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.

**parar de trabalho de site [opções] &lt;nometarefa > &lt;propriedade jobType > [nome]**

Este comando deixa a tarefa de web especificada. Podem ser terminadas apenas as tarefas contínuas.

Este comando suporta as seguintes opções adicionais:

+ **– nome da tarefa** &lt;nome da tarefa >: obrigatório. O nome do webjob.
+ **– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.

###<a name="commands-to-manage-your-web-jobs-history"></a>Comandos para gerir o seu histórico de tarefas da Web

**lista do histórico de tarefa de site [opções] [nometarefa] [nome]**

Este comando apresenta um histórico das execuções do projecto web especificada.

Este comando suporta as seguintes opções adicionais:

+ **– nome da tarefa** &lt;nome da tarefa >: obrigatório. O nome do webjob.
+ **– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.

**histórico de tarefa de sites Mostrar [opções] [nometarefa] [runId] [nome]**

Este comando obtém os detalhes do trabalho executado para a tarefa de web especificada.

Este comando suporta as seguintes opções adicionais:

+ **– nome da tarefa** &lt;nome da tarefa >: obrigatório. O nome do webjob.
+ **– Executar id** &lt;id executar >: opcional. O id do histórico de executar. Se não for especificado, mostra a mais recente executar.
+ **– ranhura** &lt;ranhura >: O nome do ranhura para reiniciar.

###<a name="commands-to-manage-your-web-app-diagnostics"></a>Comandos para gerir o seu diagnósticos de aplicação web

**registo de site transferir [opções] [nome]**

Transferir um ficheiro. zip que contém diagnósticos do web app.

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**Cauda de registo de site [opções] [nome]**

Este comando liga-se a sua terminal para o serviço de transmissão de registo.

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**conjunto de registos de site [opções] [nome]**

Este comando configura as opções de diagnóstico para a sua aplicação web.

    ~$ azure site log set -a
    info:    Executing command site log set
    + Getting output options
    help:    Output:
      1) file
      2) storage
      : 1
    Web site name: mydemosite
    + Getting locations
    + Getting sites
    + Getting site information
    + Getting diagnostic settings
    + Updating diagnostic settings
    info:    site log set command OK

###<a name="commands-to-manage-your-web-app-repositories"></a>Comandos para gerir o seu repositórios de aplicação web

**ramo de repositório de site [opções] &lt;ramo > [nome]**

**eliminação de repositório de site [opções] [nome]**

**sincronização de repositório de site [opções] [nome]**

###<a name="commands-to-manage-your-web-app-scaling"></a>Comandos para gerir o seu dimensionamento da aplicação web

**modo de escala de sites [opções] &lt;modo > [nome]**

**escala de site instâncias [opções] &lt;instâncias > [nome]**


## <a name="commands-to-manage-azure-mobile-services"></a>Comandos para gerir os serviços do Azure Mobile

Serviços de Mobile Azure conjuga um conjunto de serviços Azure que ativar as capacidades de back-end para as suas aplicações. Comandos de serviços móveis estão divididos em categorias seguintes:

+ [Comandos para gerir as instâncias do serviço móvel](#Mobile_Services)
+ [Comandos para gerir as configurações de serviço móvel](#Mobile_Configuration)
+ [Comandos para gerir as tabelas de serviço móvel](#Mobile_Tables)
+ [Comandos para gerir os scripts de serviço móvel](#Mobile_Scripts)
+ [Comandos para gerir as tarefas agendadas](#Mobile_Jobs)
+ [Comandos para dimensionar um serviço móvel](#Mobile_Scale)

As seguintes opções aplicam a maior parte dos comandos de serviços móveis:

+ **-h** ou **– Ajuda**: apresentar informações sobre a utilização de saída.
+ **-s `<id>` ** ou **– subscrição `<id>` **: utilizar uma subscrição específica, especificada como `<id>`.
+ **+ v** ou **– verboso**: escrever registo verboso.
+ **– json**: escrever JSON saída.

### <a name="Mobile_Services"></a>Comandos para gerir as instâncias do serviço móvel

**localizações móveis [opções]**

Este comando lista localizações geográficas suportadas pelos serviços móveis.

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US
    info:    North Europe

**Mobile criar [opções] [NomeServiço] [sqlAdminUsername] [sqlAdminPassword]**

Este comando cria um serviço juntamente com um servidor de base de dados SQL e dispositivos móvel.

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

Este comando suporta as seguintes opções adicionais:

+ **- r `<sqlServer>` ** ou **– sqlServer `<sqlServer>` **: utilizar um servidor de base de dados SQL existente, especificado como `<sqlServer>`.
+ **-d `<sqlDb>` ** ou **– sqlDb `<sqlDb>` **: utilizar SQL base de dados existente, especificado como `<sqlDb>`.
+ **-l `<location>` ** ou **– localização `<location>` **: criar o serviço numa localização específica, especificada como `<location>`. Execute o azure localizações móveis para obter localizações disponíveis.
+ **– sqlLocation `<location>` **: criar o SQL server num específico `<location>`; predefinições para a localização do serviço móvel.

**Eliminar móvel [opções] [NomeServiço]**

Este comando elimina um serviço móvel juntamente com a sua base de dados SQL e servidor.

    ~$ azure mobile delete todolist -a -q
    info:    Executing command mobile delete
    data:    Mobile service todolist
    data:    SQL database todolistAwrhcL60azo1C401
    data:    SQL server fh1kvbc7la
    + Deleting mobile service
    info:    Deleted mobile service
    + Deleting SQL server
    info:    Deleted SQL server
    + Deleting mobile application
    info:    Deleted mobile application
    info:    mobile delete command OK

Este comando suporta as seguintes opções adicionais:

+ **+ d** ou **– deleteData**: eliminar todos os dados a partir deste serviço móvel da base de dados.
+ **-a** ou **– deleteAll**: eliminar a base de dados SQL e servidor.
+ **-q** ou **– sossegada,**: não pedir confirmação. Utilize esta opção em scripts automatizados.

**lista de dispositivos móvel [opções]**

Este comando lista os seus serviços de dispositivos móveis.

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**Mostrar móvel [opções] [NomeServiço]**

Este comando apresenta detalhes sobre um serviço móvel.

    ~$ azure mobile show todolist
    info:    Executing command mobile show
    + Getting information
    info:    Mobile application
    data:    status Healthy
    data:    Mobile service name todolist
    data:    Mobile service status ProvisionConfigured
    data:    SQL database name todolistAwrhcL60azo1C401
    data:    SQL database status Linked
    data:    SQL server name fh1kvbc7la
    data:    SQL server status Linked
    info:    Mobile service
    data:    name todolist
    data:    state Ready
    data:    applicationUrl https://todolist.azure-mobile.net/
    data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    webspace WESTUSWEBSPACE
    data:    region West US
    data:    tables TodoItem
    info:    mobile show command OK

**reiniciar móvel [opções] [NomeServiço]**

Este comando reinicia uma instância do serviço móvel.

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**registo móvel [opções] [NomeServiço]**

Este comando devolver os registos do serviço móvel, a exclusão de todos os tipos de registo, mas `error`.

    ~$ azure mobile log todolist -t error
    info:    Executing command mobile log
    data:
    data:    timeCreated 2013-01-07T16:04:43.351Z
    data:    type error
    data:    source /scheduler/TestingLogs.js
    data:    message This is an error.
    data:
    info:    mobile log command OK

Este comando suporta as seguintes opções adicionais:

+ **- r `<query>` ** ou **– consulta `<query>` **: executa a consulta de registo especificado.
+ **-t `<type>` ** ou **– tipo `<type>` **: filtrar os registos devolvidos pela entrada `<type>`, que pode ser `information`, `warning`, ou `error`.
+ **-k `<skip>` ** ou **– Ignorar `<skip>` **: ignora o número de linhas especificado por `<skip>`.
+ **-p `<top>` ** ou **– início `<top>` **: devolve um número específico de linhas, especificado pelo `<top>`.

> [AZURE.NOTE] O **– consulta** parâmetro terá precedência relativamente ao **– tipo**, **– Ignorar**, e **– início**.

**recuperar móvel [opções] [unhealthyservicename] [healthyservicename]**

Este comando recupera um serviço móvel do danificado ao movê-lo para um serviço móvel Saudável numa região diferente.

Este comando suporta a opção adicional que se segue:

**-q** ou **– sossegada,**: suprimir a pedir a confirmação de recuperação.

**chave móvel gerar [opções] [NomeServiço] [tipo]**

Este comando gera novamente a tecla de aplicação de serviço móvel.

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

Tipos de chave são `master` e `application`.

> [AZURE.NOTE] Quando voltar a gerar chaves, os clientes que utilizam a chave antiga poderão não ser possível aceder ao serviço móvel. Quando voltar a gerar a tecla de aplicação, deverá atualizar a sua aplicação com o novo valor de chave.

**conjunto de chaves móvel [opções] [NomeServiço] [tipo] [valor]**

Este comando define a chave do serviço móvel para um valor específico.


### <a name="Mobile_Configuration"></a>Comandos para gerir as configurações de serviço móvel

**lista de configuração móvel [opções] [NomeServiço]**

Este comando lista opções de configuração para um serviço móvel.

    ~$ azure mobile config list todolist
    info:    Executing command mobile config list
    + Getting mobile service configuration
    data:    dynamicSchemaEnabled true
    data:    microsoftAccountClientSecret Not configured
    data:    microsoftAccountClientId Not configured
    data:    microsoftAccountPackageSID Not configured
    data:    facebookClientId Not configured
    data:    facebookClientSecret Not configured
    data:    twitterClientId Not configured
    data:    twitterClientSecret Not configured
    data:    googleClientId Not configured
    data:    googleClientSecret Not configured
    data:    apnsMode none
    data:    apnsPassword Not configured
    data:    apnsCertifcate Not configured
    info:    mobile config list command OK

**configuração móvel obter [opções] [NomeServiço] [key]**

Este comando obtém uma opção de configuração específicas para um serviço móvel, neste caso dinâmico esquema.

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**configuração móvel definido [opções] [NomeServiço] [key] [valor]**

Este comando define uma opção de configuração específicas para um serviço móvel, neste caso dinâmico esquema.

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK


### <a name="Mobile_Tables"></a>Comandos para gerir as tabelas de serviço móvel

**lista de tabela móvel [opções] [NomeServiço]**

Este comando lista todas as tabelas no seu serviço móvel.

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**Mostrar tabela móvel [opções] [NomeServiço] [tabela]**

Este comando mostra devolve detalhes sobre uma tabela específica.

    ~$azure mobile table show todolist
    info:    Executing command mobile table show
    + Getting table information
    info:    Table statistics:
    data:    Number of records 5
    info:    Table operations:
    data:    Operation  Script       Permissions
    data:    ---------  -----------  -----------
    data:    insert     1900 bytes   user
    data:    read       Not defined  user
    data:    update     Not defined  user
    data:    delete     Not defined  user
    info:    Table columns:
    data:    Name  Type           Indexed
    data:    ----  -------------  -------
    data:    id    bigint(MSSQL)  Yes
    data:    text      string
    data:    complete  boolean
    info:    mobile table show command OK

**tabela móvel criar [opções] [NomeServiço] [tabela]**

Este comando cria uma tabela.

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

Este comando suporta a opção adicional que se segue:

+ **-p `&lt;permissions>` ** ou **– permissões `&lt;permissions>` **: delimitado por vírgulas lista de `<operation>` = `<permission>` pares, onde `<operation>` é `insert`, `read`, `update`, ou `delete` e `&lt;permissions>` é `public`, `application` (predefinição), `user`, ou `admin`.

**ler os dados móveis [opções] [NomeServiço] [tabela] [query]**

Este comando lê dados a partir de uma tabela.

    ~$azure mobile data read todolist TodoItem
    info:    Executing command mobile data read
    data:    id  text     complete
    data:    --  -------  --------
    data:    1   item #1  false
    data:    2   item #2  true
    data:    3   item #3  false
    data:    4   item #4  true
    info:    mobile data read command OK

Este comando suporta as seguintes opções adicionais:

+ **-k `<skip>` ** ou **– Ignorar `<skip>` **: ignora o número de linhas especificado pelo `<skip>`.
+ **-t `<top>` ** ou **– início `<top>` **: devolve um número específico de linhas, especificado pelo `<top>`.
+ **-l** ou **– lista**: devolve dados num formato de lista.

**update tabela móvel [opções] [NomeServiço] [tabela]**

Este comando altera eliminar permissões numa tabela apenas a administradores.

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

Este comando suporta as seguintes opções adicionais:

+ **-p `&lt;permissions>` ** ou **– permissões `&lt;permissions>` **: delimitado por vírgulas lista de `<operation>` = `<permission>` pares, onde `<operation>` é `insert`, `read`, `update`, ou `delete` e `&lt;permissions>` é `public`, `application` (predefinição), `user`, ou `admin`.
+ **– deleteColumn `<columns>` **: delimitado por vírgulas lista de colunas para eliminar, como `<columns>`.
+ **-q** ou **– sossegada,**: Elimina colunas sem pedir a confirmação.
+ **– addIndex `<columns>` **: delimitado por vírgulas lista de colunas a incluir no índice.
+ **– deleteIndex `<columns>` **: delimitado por vírgulas lista de colunas para excluir do índice.

**tabela móvel eliminar [opções] [NomeServiço] [tabela]**

Este comando elimina uma tabela.

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

Especifique o parâmetro - q para eliminar a tabela sem confirmação. Faça o seguinte para impedir que o bloqueio de scripts de automatização.

**dados móveis truncagem [opções] [NomeServiço] [tabela]**

Este comando remove todas as linhas de dados a partir da tabela.

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>Comandos para gerir os scripts

Comandos nesta secção são utilizados para gerir os scripts de servidor pertencem a um serviço móvel. Para mais informações, consulte o artigo [trabalhar com os scripts de servidor de serviços móveis](https://github.com/Azure/azure-mobile-services/blob/master/docs/mobile-services-how-to-use-server-scripts.md).

**lista de script móvel [opções] [NomeServiço]**

Este comando lista scripts registados, incluindo a tabela e programador de scripts.

    ~$azure mobile script list todolist
    info:    Executing command mobile script list
    + Getting script information
    info:    Table scripts
    data:    Name                   Size
    data:    ---------------------  ----
    data:    table/TodoItem.delete  256
    data:    table/Devices.insert   1660
    error:   Unable to get shared scripts
    info:    Scheduler scripts
    data:    Name                 Status     Interval   Last run   Next run
    data:    -------------------  ---------  ---------  ---------  ---------
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    info:    mobile script list command OK

**transferência de script móvel [opções] [NomeServiço] [Nomedoscript]**

Este comando transfere o script de inserir a partir da tabela TodoItem num ficheiro denominado `todoitem.insert.js` na `table` subpasta.

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

Este comando suporta as seguintes opções adicionais:

+ **-p `<path>` ** ou **– caminho `<path>` **: A localização do ficheiro na qual pretende guardar o script, onde o directório de trabalho atual é a predefinição.
+ **-f `<file>` ** ou **– ficheiro `<file>` **: O nome do ficheiro no qual pretende guardar o script.
+ **+ o** ou **– Substituir**: substituir um ficheiro existente.
+ **-c** ou **– consola**: escrever o script na consola em vez de para um ficheiro.

**carregamento de script móvel [opções] [NomeServiço] [Nomedoscript]**

Este comando carrega um script denominado `todoitem.insert.js` a partir do `table` subpasta.

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

O nome do ficheiro tem de ser composto pela partir de nomes de tabela e de operação. Têm de estar localizado na subpasta tabela em relação a localização onde o comando é executado. Também pode utilizar o **-f `<file>` ** ou **– ficheiro `<file>` ** parâmetro para especificar um nome de ficheiro diferente e o caminho para o ficheiro que contém o script para registar.


**script móvel eliminar [opções] [NomeServiço] [Nomedoscript]**

Este comando remove o script de inserir existente a partir da tabela TodoItem.

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>Comandos para gerir as tarefas agendadas

Comandos nesta secção são utilizados para gerir tarefas agendadas que pertencem a um serviço móvel. Para mais informações, consulte o artigo [agendar tarefas](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**lista de trabalhos móvel [opções] [NomeServiço]**

Este comando lista tarefas agendadas.

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**tarefa móvel criar [opções] [NomeServiço] [nometarefa]**

Este comando cria uma tarefa denominada `getUpdates` que está agendado para ser executado por hora.

    ~$azure mobile job create -i 1 -u hour todolist getUpdates
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

Este comando suporta as seguintes opções adicionais:

+ **-i `<number>` ** ou **– intervalo `<number>` **: O intervalo da tarefa, como um número inteiro. O valor predefinido é `15`.
+ **-u `<unit>` ** ou **– intervalUnit `<unit>` **: A unidade para o _intervalo_, que pode ser um dos seguintes valores:
    + **minuto** (predefinição)
    + **hora**
    + **dia**
    + **mês**
    + **nenhum** (a pedido tarefas)
+ **-t `<time>`** **– a hora de início `<time>` ** Execute a hora de início do primeiro para o script, no formato ISO. O valor predefinido é `now`.

> [AZURE.NOTE] Novas tarefas são criadas num estado desactivado, uma vez que ainda tem ser carregado um script. Utilize o comando **script móvel carregar** para carregar um script e o comando **Actualizar Projecto móvel** para ativar a tarefa.

**atualização de tarefa móvel [opções] [NomeServiço] [nometarefa]**

O seguinte comando activa deficiência `getUpdates` tarefa.

    ~$azure mobile job update -a enabled todolist getUpdates
    info:    Executing command mobile job update
    info:    mobile job update command OK

Este comando suporta as seguintes opções adicionais:

+ **-i `<number>` ** ou **– intervalo `<number>` **: O intervalo da tarefa, como um número inteiro. O valor predefinido é `15`.
+ **-u `<unit>` ** ou **– intervalUnit `<unit>` **: A unidade para o _intervalo_, que pode ser um dos seguintes valores:
    + **minuto** (predefinição)
    + **hora**
    + **dia**
    + **mês**
    + **nenhum** (a pedido tarefas)
+ **-t `<time>`** **– a hora de início `<time>` ** Execute a hora de início do primeiro para o script, no formato ISO. O valor predefinido é `now`.
+ **- um `<status>` ** ou **– estado `<status>` **: O estado da tarefa, que pode ser quer `enabled` ou `disabled`.

**tarefa móvel eliminar [opções] [NomeServiço] [nometarefa]**

Este comando remove a tarefa agendada getUpdates do servidor de fazer.

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

> [AZURE.NOTE] Eliminar uma tarefa também elimina o script carregado.

### <a name="Mobile_Scale"></a>Comandos para dimensionar um serviço móvel

Comandos nesta secção são utilizados para dimensionar um serviço móvel. Para mais informações, consulte o artigo [dimensionamento um serviço móvel](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**escala móvel Mostrar [opções] [NomeServiço]**

Este comando apresenta informações de escala, incluindo o modo de cluster atual e o número de instâncias.

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**alterar a escala móvel [opções] [NomeServiço]**

Este comando altera a escala do serviço móvel do gratuito para o modo de premium.

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

Este comando suporta as seguintes opções adicionais:

+ **- c `<mode>` ** ou **– computeMode `<mode>` **: O modo de cluster tem de ser `Free` ou `Reserved`.
+ **-i `<count>` ** ou **– numberOfInstances `<count>` **: O número de instâncias utilizado quando executar no modo reservado.

> [AZURE.NOTE] Quando configurar o modo de cluster `Reserved`, todos os seus serviços móveis na mesma região executar no modo de premium.


###<a name="commands-to-enable-preview-features-for-your-mobile-service"></a>Comandos para activar funcionalidades de pré-visualização do seu serviço de Mobile

**lista de pré-visualização móvel [opções] [NomeServiço]**

Este comando apresenta as funcionalidades de pré-visualização disponíveis no serviço especificado e se encontram ativadas.

    ~$ azure mobile preview list mysite
    info:    Executing command mobile preview list
    + Getting preview features
    data:    Preview feature  Enabled
    data:    ---------------  -------
    data:    SourceControl    No
    data:    Users            No
    info:    You can enable preview features using the 'azure mobile preview enable' command.
    info:    mobile preview list command OK

**Ativar a pré-visualização do móvel [opções] [NomeServiço] [NomeFuncionalidade]**

Este comando permite a funcionalidade de pré-visualização especificado para um serviço móvel. Quando ativada, não podem ser desativadas funcionalidades de pré-visualização para um serviço móvel.

###<a name="commands-to-manage-your-mobile-service-apis"></a>Comandos para gerir o serviço móvel do APIs

**lista de dispositivos móveis api [opções] [NomeServiço]**

Este comando apresenta uma lista de dispositivos móvel APIs personalizados que criou para o seu serviço móvel do serviço.

    ~$ azure mobile api list mysite
    info:    Executing command mobile api list
    + Retrieving list of APIs
    info:    APIs
    data:    Name                  Get          Put          Post         Patch        Delete
    data:    --------------------  -----------  -----------  -----------  -----------  -----------
    data:    myCustomRetrieveAPI   application  application  application  application  application
    info:    You can manipulate API scripts using the 'azure mobile script' command.
    info:    mobile api list command OK

**api móvel criar [opções] [NomeServiço] [apiname]**

Cria uma API personalizado serviço móvel

    ~$ azure mobile api create mysite myCustomRetrieveAPI
    info:    Executing command mobile api create
    + Creating custom API: 'myCustomRetrieveAPI'
    info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
    info:    mobile api create command OK

Este comando suporta a opção adicional que se segue:

**-p** ou **– permissões** &lt;permissões >: uma lista delimitado por vírgulas de &lt;método > =&lt;permissões > pares.

**atualização de api móvel [opções] [NomeServiço] [apiname]**

Este comando atualiza a API personalizado do serviço móvel especificado.

Este comando suporta a opção adicional que se segue:

Este comando suporta as seguintes opções adicionais:

+ **-p** ou **– permissões** &lt;permissões >: uma lista delimitado por vírgulas de &lt;método > =&lt;permissões > pares.
+ **-f** ou **– Forçar**: substitui as alterações personalizadas para o ficheiro de metadados de permissões.

**api móvel eliminar [opções] [NomeServiço] [apiname]**

    ~$ azure mobile api delete mysite myCustomRetrieveAPI
    info:    Executing command mobile api delete
    + Deleting API: 'myCustomRetrieveAPI'
    info:    mobile api delete command OK

Este comando elimina a API personalizado do serviço móvel especificado.

###<a name="commands-to-manage-your-mobile-application-app-settings"></a>Comandos para gerir as definições de aplicação do aplicação móvel

**lista de dispositivos móveis appsetting [opções] [NomeServiço]**

Este comando apresenta as definições de aplicação de aplicações móveis para o serviço especificado.

    ~$ azure mobile appsetting list mysite
    info:    Executing command mobile appsetting list
    + Retrieving app settings
    data:    Name               Value
    data:    -----------------  -----
    data:    enablebetacontent  true
    info:    mobile appsetting list command OK

**appsetting móvel adicionar [opções] [NomeServiço] [nome] [valor]**

Este comando adiciona uma definição de aplicação personalizada do seu serviço móvel.

    ~$ azure mobile appsetting add mysite enablebetacontent true
    info:    Executing command mobile appsetting add
    + Retrieving app settings
    + Adding app setting
    info:    mobile appsetting add command OK

**Eliminar appsetting móvel [opções] [NomeServiço] [nome]**

Este comando remove a definição de aplicação especificada do seu serviço móvel.

    ~$ azure mobile appsetting delete mysite enablebetacontent
    info:    Executing command mobile appsetting delete
    + Retrieving app settings
    + Removing app setting 'enablebetacontent'
    info:    mobile appsetting delete command OK

**Mostrar appsetting móvel [opções] [NomeServiço] [nome]**

Este comando remove a definição de aplicação especificada do seu serviço móvel.

    ~$ azure mobile appsetting show mysite enablebetacontent
    info:    Executing command mobile appsetting show
    + Retrieving app settings
    info:    enablebetacontent: true
    info:    mobile appsetting show command OK

## <a name="manage-tool-local-settings"></a>Gerir as definições da ferramenta local

Definições do local são o ID da subscrição e o nome de conta de armazenamento predefinido.

**lista de configuração [opções]**

Este comando apresenta as definições de configuração.

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**[opções] do conjunto de configuração &lt;nome&gt;,&lt;valor&gt;**

Este comando altera uma definição de configuração.

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

## <a name="commands-to-manage-service-bus"></a>Comandos para gerir o serviço Bus

Utilize estes comandos para gerir a sua conta de serviço Bus

**verificação de espaço de nomes de SB [opções] &lt;nome >**

Verifique se um espaço de nomes do serviço bus é legal e disponíveis.

**criar espaço de nomes de SB &lt;nome > &lt;localização >**

Cria um espaço de nomes de serviço Bus.

    ~$ azure sb namespace create mysbnamespacea-test "West US"
    info:    Executing command sb namespace create
    + Creating namespace mysbnamespacea-test in region West US
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Activating
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    _: [object Object]
    info:    sb namespace create command OK


**espaço de nomes de SB eliminar &lt;nome >**

Remova um espaço de nomes.

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**lista de espaço de nomes de SB**

Lista de todos os espaços de nomes criados para a sua conta.

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK


**lista de localizações da SB espaço de nomes**

Apresenta uma lista de todas as localizações disponíveis espaço de nomes.

    ~$ azure sb namespace location list
    info:    Executing command sb namespace location list
    + Getting locations
    data:    Name              Code
    data:    ----------------  ----------------
    data:    East Asia         East Asia
    data:    West Europe       West Europe
    data:    North Europe      North Europe
    data:    East US           East US
    data:    Southeast Asia    Southeast Asia
    data:    North Central US  North Central US
    data:    West US           West US
    data:    South Central US  South Central US
    info:    sb namespace location list command OK

**Mostrar espaço de nomes de SB &lt;nome >**

Apresentar detalhes sobre um espaço de nomes específico.

    ~$ azure sb namespace show mysbnamespacea-test
    info:    Executing command sb namespace show
    + Getting namespace
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Active
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    UpdatedAt: 2013-11-14T16:25:37.85Z
    info:    sb namespace show command OK

**verificar o espaço de nomes de SB &lt;nome >**

Verifique se o espaço de nomes está disponível.

## <a name="commands-to-manage-your-storage-objects"></a>Comandos para gerir os objetos de armazenamento

###<a name="commands-to-manage-your-storage-accounts"></a>Comandos para gerir as suas contas de armazenamento

**lista de conta de armazenamento [opções]**

Este comando apresenta as contas de armazenamento na sua subscrição.

    ~$ azure storage account list
    info:    Executing command storage account list
    + Getting storage accounts
    data:    Name             Label  Location
    data:    ---------------  -----  --------
    data:    mybasestorage           West US
    info:    storage account list command OK

**apresentação de conta de armazenamento [opções]<name>**

Este comando apresenta informações sobre a conta de armazenamento especificada, incluindo as propriedades URI e conta.

**conta de armazenamento criar [opções]<name>**

Este comando cria uma conta de armazenamento com base no menu opções fornecidas.

    ~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK

Este comando suporta as seguintes opções adicionais:

+ **-e** ou **– etiqueta** &lt;etiqueta >: A etiqueta para a conta de armazenamento.
+ **-d** ou **– Descrição** &lt;Descrição >: A conta de armazenamento de descrição.
+ **-l** ou **– localização** &lt;nome >: A região geográfica na qual pretende criar a conta de armazenamento.
+ **-a** ou **– afinidade grupo** &lt;nome >: grupo afinidade à qual pretende associar a conta de armazenamento. 
+ **– tipo**: indica o tipo de conta para criar: um dos armazenamento padrão com a opção de redundância (LRS/ZRS/GRS/RAGRS) ou Premium armazenamento (PLRS).

**conta de armazenamento definida [opções]<name>**

Este comando atualiza a conta de armazenamento especificada.

    ~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
    info:    Executing command storage account set
    + Updating storage account
    info:    storage account set command OK

Este comando suporta as seguintes opções adicionais:

+ **-e** ou **– etiqueta** &lt;etiqueta >: A etiqueta para a conta de armazenamento.
+ **-d** ou **– Descrição** &lt;Descrição >: A conta de armazenamento de descrição.
+ **-l** ou **– localização** &lt;nome >: A região geográfica na qual pretende criar a conta de armazenamento.
+ **– tipo**: indica o novo tipo de conta: um dos armazenamento padrão com a opção de redundância (LRS/ZRS/GRS/RAGRS) ou Premium armazenamento (PLRS).

**eliminar de conta de armazenamento [opções]<name>**

Este comando elimina a conta de armazenamento especificada.

Este comando suporta a opção adicional que se segue:

**-q** ou **– sossegada,**: não pedir confirmação. Utilize esta opção em scripts automatizados.

###<a name="commands-to-manage-your-storage-account-keys"></a>Comandos para gerir as suas chaves de conta de armazenamento

**[opções] da lista de teclas de conta de armazenamento<name>**

Este comando lista as teclas principais e secundárias para a conta de armazenamento especificada.

**teclas de conta de armazenamento renovar [opções]<name>**

###<a name="commands-to-manage-your-storage-container"></a>Comandos para gerir o contentor de armazenamento

**lista de contentor de armazenamento [opções] [prefixo]**

Este comando apresenta a lista de contentor de armazenamento para uma conta de armazenamento especificada. A conta de armazenamento é especificada pela cadeia de ligação ou a chave de nome e a conta da conta de armazenamento.

Este comando suporta as seguintes opções adicionais:

+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contentor de armazenamento.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento.
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento.
+ **– Depurar**: executa o comando de armazenamento no modo de depuração.

**apresentação de contentor de armazenamento [opções] [contentor]**
**contentor de armazenamento de criar [opções] [contentor]**

Este comando cria um contentor de armazenamento para a conta de armazenamento especificada. A conta de armazenamento é especificada pela cadeia de ligação ou a chave de nome e a conta da conta de armazenamento.

Este comando suporta as seguintes opções adicionais:

+ **– contentor** &lt;contentor >: O nome do contentor de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contentor de armazenamento.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento
+ **– Depurar**: executa o comando de armazenamento no modo de depuração.

**eliminar de contentor de armazenamento [opções] [contentor]**

Este comando elimina o contentor de armazenamento especificada. A conta de armazenamento é especificada pela cadeia de ligação ou a chave de nome e a conta da conta de armazenamento.

Este comando suporta as seguintes opções adicionais:

+ **– contentor** &lt;contentor >: O nome do contentor de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contentor de armazenamento.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento.
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento.
+ **– Depurar**: executa o comando de armazenamento no modo de depuração.

**contentor de armazenamento de definir [opções] [contentor]**

Este comando define a lista de controlo de acesso para o contentor de armazenamento. A conta de armazenamento é especificada pela cadeia de ligação ou a chave de nome e a conta da conta de armazenamento.

Este comando suporta as seguintes opções adicionais:

+ **– contentor** &lt;contentor >: O nome do contentor de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contentor de armazenamento.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento.
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento.
+ **– Depurar**: executa o comando de armazenamento no modo de depuração.

###<a name="commands-to-manage-your-storage-blob"></a>Comandos para gerir o seu blob de armazenamento

**lista do armazenamento blob [opções] [contentor] [prefixo]**

Este comando devolve uma lista de blobs o armazenamento no contentor de armazenamento especificada.

Este comando suporta as seguintes opções adicionais:

+ **– contentor** &lt;contentor >: O nome do contentor de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contentor de armazenamento.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento.
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento.
+ **– Depurar**: executa o comando de armazenamento no modo de depuração.

**blob armazenamento Mostrar [opções] [contentor] [blob]**

Este comando apresenta os detalhes do blob armazenamento especificada.

Este comando suporta as seguintes opções adicionais:

+ **– contentor** &lt;contentor >: O nome do contentor de armazenamento para criar.
+ **-p** ou **-prefixo** &lt;prefixo >: O prefixo de nome de contentor de armazenamento.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento.
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento.
+ **– Depurar**: executa o comando de armazenamento de depuração.

**blob armazenamento eliminar [opções] [contentor] [blob]**

Este comando suporta as seguintes opções adicionais:

+ **– contentor** &lt;contentor >: O nome do contentor de armazenamento para criar.
+ **-b** ou **– blob** &lt;blobName >: O nome do blob de armazenamento para eliminar.
+ **-q** ou **– sossegada,**: remover o blob armazenamento especificado sem confirmação.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento.
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento.
+ **– Depurar**: executa o comando de armazenamento de depuração.

**carregamento de Blobs do armazenamento [opções] [ficheiro] [contentor] [blob]**

Este comando carregue o ficheiro especificado para o blob de armazenamento specified\.

Este comando suporta as seguintes opções adicionais:

+ **– contentor** &lt;contentor >: O nome do contentor de armazenamento para criar.
+ **-b** ou **– blob** &lt;blobName >: O nome do blob de armazenamento para carregar.
+ **-t** ou **- blobtype** &lt;blobtype >: O tipo de Blobs do armazenamento: página ou bloco.
+ **-p** ou **– Propriedades** &lt;propriedades >: as propriedades de Blobs do armazenamento de ficheiros carregados. Propriedades são chave = valor par s e separadas com semicolon(;). Propriedades disponíveis são contentType, contentEncoding, contentLanguage e cacheControl.
+ **m** ou **– metadados** &lt;metadados >: os metadados de Blobs do armazenamento para ficheiros carregados. Metadados são chave = pares valor uma d separada por ponto e vírgula (;)).
+ **– concurrenttaskcount** &lt;concurrenttaskcount >: O número máximo de pedidos de carregamento em simultâneo.
+ **-q** ou **– sossegada,**: substituir o blob armazenamento especificado sem confirmação.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento.
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento.
+ **– Depurar**: executa o comando de armazenamento de depuração.

**transferência de Blobs do armazenamento [opções] [contentor] [blob] [destino]**

Este comando transfere o blob armazenamento especificada.

Este comando suporta as seguintes opções adicionais:

+ **– contentor** &lt;contentor >: O nome do contentor de armazenamento para criar.
+ **-b** ou **– blob** &lt;blobName >: O nome do armazenamento blob.
+ **-d** ou **– destino** [destino]: O caminho de ficheiro ou do diretório de destino do transferir.
+ **m** ou **– checkmd5**: O md5sum de verificação para o ficheiro transferido.
+ **– concurrenttaskcount** &lt;concurrenttaskcount > o número máximo de pedidos de carregamento em simultâneo
+ **-q** ou **– sossegada,**: substituir o ficheiro de destino sem confirmação.
+ **-a** ou **– nome da conta** &lt;accountName >: O nome da conta de armazenamento.
+ **-k** ou **– chave da conta** &lt;accountKey >: A chave de conta de armazenamento.
+ **-c** ou **– cadeia de ligação** &lt;connectionString >: A cadeia de ligação de armazenamento.
+ **– Depurar**: executa o comando de armazenamento de depuração.

## <a name="commands-to-manage-sql-databases"></a>Comandos para gerir as bases de dados SQL

Utilize estes comandos para gerir as bases de dados do SQL Azure

###<a name="commands-to-manage-sql-servers"></a>Comandos para gerir os servidores de SQL.

Utilize estes comandos para gerir os servidores de SQL

**Criar do SQL server &lt;administratorLogin > &lt;administratorPassword > &lt;localização >**

Criar um servidor de base de dados

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**apresentação do SQL server &lt;nome >**

Apresentar detalhes do servidor.

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**lista do SQL server**

Obter a lista dos servidores.

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**SQL server eliminar &lt;nome >**

Elimina um servidor

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

###<a name="commands-to-manage-sql-databases"></a>Comandos para gerir as bases de dados SQL

Utilize estes comandos para gerir as bases de dados do SQL.

**BD de SQL criar [opções] &lt;nomedoservidor > &lt;databaseName > &lt;administratorPassword >**

Cria uma instância de base de dados

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**BD de SQL, Mostrar [opções] &lt;nomedoservidor > &lt;databaseName > &lt;administratorPassword >**

Mostrar detalhes de base de dados.

    C:\windows\system32>azure sql db show fr8aelne00 newdb test
    info:    Executing command sql db show
    Administrator password: ********
    + Getting SQL server databases
    data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
    ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
    m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
    icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
    ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
    ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
    /Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
    ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
    Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
    services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
    tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
    om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
    pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
    d=2013-11-18T19:48:27Z, name=
    data:    Database Id 4
    data:    Database Name newdb
    data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database ServiceObjectiveAssignmentState 1
    data:    Database ServiceObjectiveAssignmentStateDescription Complete
    data:    Database ServiceObjectiveAssignmentErrorCode
    data:    Database ServiceObjectiveAssignmentErrorDescription
    data:    Database ServiceObjectiveAssignmentSuccessDate
    data:    Database Edition Web
    data:    Database MaxSizeGB 1
    data:    Database MaxSizeBytes 1073741824
    data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
    data:    Database CreationDate
    data:    Database RecoveryPeriodStartDate
    data:    Database IsSystemObject
    data:    Database Status 1
    data:    Database IsFederationRoot
    data:    Database SizeMB -1
    data:    Database IsRecursiveTriggersOn
    data:    Database IsReadOnly
    data:    Database IsFederationMember
    data:    Database IsQueryStoreOn
    data:    Database IsQueryStoreReadOnly
    data:    Database QueryStoreMaxSizeMB
    data:    Database QueryStoreFlushPeriodSeconds
    data:    Database QueryStoreIntervalLengthMinutes
    data:    Database QueryStoreClearAll
    data:    Database QueryStoreStaleQueryThresholdDays
    info:    sql db show command OK

**lista de db do SQL [opções] &lt;nomedoservidor > &lt;administratorPassword >**

As bases de dados da lista.

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**BD de SQL eliminar [opções] &lt;nomedoservidor > &lt;databaseName > &lt;administratorPassword >**

Elimina uma base de dados.

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

###<a name="commands-to-manage-your-sql-server-firewall-rules"></a>Comandos para gerir as suas regras de firewall do SQL Server

Utilize estes comandos para gerir as regras de firewall do SQL Server

**SQL firewallrule criar [opções] &lt;nomedoservidor > &lt;Nomedaregra > &lt;startIPAddress > &lt;endIPAddress >**

Crie uma regra de firewall para SQL Server.

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**SQL firewallrule Mostrar [opções] &lt;nomedoservidor > &lt;Nomedaregra >**

Mostrar detalhes da regra de firewall.

    ~$ azure sql firewallrule show fr8aelne00 allowed
    info:    Executing command sql firewallrule show
    + Getting firewall rule
    data:    Firewall rule Name allowed
    data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
    data:    Firewall rule State Normal
    data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
    5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
    data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
    055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
    data:    Firewall rule StartIPAddress 131.107.0.0
    data:    Firewall rule EndIPAddress 131.107.255.255
    info:    sql firewallrule show command OK

**lista de firewallrule SQL [opções] &lt;nomedoservidor >**

Liste as regras de firewall.

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**SQL firewallrule eliminar [opções] &lt;nomedoservidor > &lt;Nomedaregra >**

Este comando elimina uma regra de firewall.

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

## <a name="commands-to-manage-your-virtual-networks"></a>Comandos para gerir as suas redes Virtual

Utilize estes comandos para gerir redes Virtual

**rede vnet criar [opções] &lt;localização >**

Crie uma rede Virtual.

    ~$ azure network vnet create vnet1 --location "West US" -v
    info:    Executing command network vnet create
    info:    Using default address space start IP: 10.0.0.0
    info:    Using default address space cidr: 8
    info:    Using default subnet start IP: 10.0.0.0
    info:    Using default subnet cidr: 11
    verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
    verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
    verbose: Fetching Network Configuration
    verbose: Fetching or creating affinity group
    verbose: Fetching Affinity Groups
    verbose: Fetching Locations
    verbose: Creating new affinity group AG1
    info:    Using affinity group AG1
    verbose: Updating Network Configuration
    info:    network vnet create command OK

**Mostrar vnet de rede &lt;nome >**

Mostrar detalhes de uma rede Virtual.

    ~$ azure network vnet show vnet1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "vnet1"
    data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
    data:    AffinityGroup "AG1"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
    data:    Subnets 0 Name "subnet-1"
    data:    Subnets 0 AddressPrefix "10.0.0.0/11"
    info:    network vnet show command OK

**lista de vnet de rede**

Lista todas as redes Virtual existente.

    ~$ azure network vnet list
    info:    Executing command network vnet list
    + Fetching Virtual Networks
    data:    Name        Status   AffinityGroup
    data:    ----------  -------  -------------
    data:    vnet1      Created  AG1
    data:    vnet2      Created  AG1
    data:    vnet3      Created  AG1
    data:    vnet4      Created  AG1
    info:    network vnet list command OK


**eliminar rede vnet &lt;nome >**

Elimina a rede Virtual especificado.

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ?  (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**Exportar rede [caminho do ficheiro]**

Para a configuração de rede avançadas, pode exportar localmente a configuração de rede. A configuração de rede exportado inclui definições do servidor DNS, as definições de rede virtual, definições do site de rede local e outras definições.

**Importar de rede [caminho do ficheiro]**

Importe uma configuração de rede local.

**servidor de rede DNS registar [opções] &lt;dnsIP >**

Registe-se um servidor DNS que planeia utilizar para resolução de nomes na configuração de rede.

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**lista de servidor de DNS de rede**

Liste todos os servidores DNS da registado na configuração de rede.

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**anular o registo de servidor de rede DNS [opções] &lt;dnsIP >**

Remove uma entrada de servidor DNS da configuração de rede.

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK

