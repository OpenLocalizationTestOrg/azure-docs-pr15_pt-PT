<properties
    pageTitle="Antes de implementar o conceito de pilha Azure | Microsoft Azure"
    description="Ver os requisitos de hardware e ambiente para o conceito de pilha de Azure (administrador do serviço)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="azure-stack-deployment-prerequisites"></a>Azure pré-requisitos de implementação de pilha

Antes de implementar o conceito de pilha de Azure ([Prova de conceito](azure-stack-poc.md)), certifique-se de que o computador cumpre os seguintes requisitos.
Os requisitos de implementação de 2 de pré-visualização técnica para o conceito são idênticos necessários para o 1 de pré-visualização técnica. Por conseguinte, pode utilizar o mesmo hardware utilizado para a pré-visualização de única caixa anterior.

## <a name="hardware"></a>Hardware

| Componente | Mínimo  | Recomendado |
|---|---|---|
| Unidades de disco: sistema operativo | 1 SO no disco com mínimo de 200 GB disponível para partição do sistema (SSD ou disco) | 1 SO no disco com mínimo de 200 GB disponível para partição do sistema (SSD ou disco) |
| Unidades de disco: dados de conceito de pilha Azure gerais | 4 discos. Cada disco fornece um mínimo de GB 140 da capacidade (SSD ou disco). Todos os discos disponíveis serão utilizados. | 4 discos. Cada disco fornece um mínimo de 250 GB da capacidade (SSD ou disco). Todos os discos disponíveis serão utilizados.|
| Calcular: CPU | Duas-Socket: 12 física núcleos (total)  | Duas-Socket: 16 física núcleos (total) |
| Calcular: memória | 96 GB DE RAM  | 128 GB DE RAM |
| Calcular: BIOS | Hyper-V ativado (com suporte SLAT)  | Hyper-V ativado (com suporte SLAT) |
| Rede: NIC | Windows Server 2012 R2 certificação necessários para o NIC; sem funcionalidades especializadas necessárias | Windows Server 2012 R2 certificação necessários para o NIC; sem funcionalidades especializadas necessárias |
| Certificação de logótipo HW | [Certificados para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificados para o Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

**Configuração da unidade de disco de dados:** Todas as unidades de dados devem ter o mesmo tipo (todas as SA ou todos os SATA) e capacidade. Se forem utilizadas SA unidades de disco, as unidades de disco tem anexadas através de um único caminho (sem MPIO, suporte multi-caminho é fornecida).

**Opções de configuração de HBA**
 
- (Preferencial) Simple HBA
- RAID HBA – adaptador tem de ser configurado no modo de "passar"
- RAID HBA – discos devem ser configurados como disco único, RAID-0

**Bus suportados e conteúdo multimédia escreva combinações**

-   SATA DISCO

-   DISCO SA

-   RAID DISCO

-   RAID SSD (se o tipo de multimédia é desconhecido/não for especificado\*)

-   SATA SSD + SATA DISCO

-   SA SSD + SA DISCO

\*Controladores RAID sem capacidade pass-through não consegue reconhecer o tipo de multimédia. Esses controladores irão marcar disco e SSD como não especificado. Nesse caso, a SSD será utilizada como armazenamento persistente em vez de colocação em cache dispositivos. Por conseguinte, pode implementar o conceito de pilha do Microsoft Azure nesses SSDs.

**Exemplo HBAs**: LSI 9207 8i, LSI-9300-8i ou LSI 9265 8i no modo pass-through

Configurações de OEM exemplo estão disponíveis.

## <a name="operating-system"></a>Sistema operativo

| | **Requisitos de**  |
|---|---|
| **Versão do SO** | Windows Server 2012 R2 ou posterior. A versão do sistema operativo não está crítica antes de inicia a implementação, tal como irá de arranque do computador anfitrião para o VHD que vem incluído no zip de instalação do Azure pilha. O sistema operativo e todos os patches necessários já sejam integrados numa imagem. Não utilize as teclas para ativar as ocorrências do Windows Server utilizadas no conceito.|

## <a name="deployment-requirements-check-tool"></a>Ferramenta de verificação de requisitos de implementação

Depois de instalar o sistema operativo, pode utilizar o [Verificador de implementação do Azure pilha de 2 de pré-visualização técnica](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar que o hardware cumpre todos os requisitos.



## <a name="microsoft-azure-active-directory-accounts"></a>Contas do Microsoft Azure Active Directory

A implementação do Microsoft Azure pilha conceito deve estar ligada a Azure. Por isso, tem de preparar uma conta do Microsoft Azure Active Directory antes de executar a PowerShell script implementação. Esta conta torna-se o Administrador Global para o inquilino do Azure Active Directory. Será utilizada para aprovisionar e delegar aplicações e principais de serviço para todos os serviços de pilha de Azure interagem com o Azure Active Directory e API do gráfico. Também será utilizada como o proprietário da subscrição fornecedor predefinido (o que é possível alterar posteriormente). Pode iniciar sessão no portal de administração do seu sistema de pilha de Azure utilizando esta conta.

1. Crie uma conta do Azure AD que é o administrador do diretório para pelo menos uma Azure Active Directory. Se já tiver uma, pode utilizar que. Caso contrário, pode criar uma gratuitamente em [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (na China, visite <http://go.microsoft.com/fwlink/?LinkID=717821> em vez disso.)

    Guarde estas credenciais para utilização no passo 6 de [executar o script de implementação do PowerShell](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). Esta conta de *administrador do serviço* pode configurar e gerir nuvens de recursos, contas de utilizador, planos de inquilino, quotas e preços. No portal do podem criar nuvens de Web site, máquina virtual private nuvens, criar planos e gerir as subscrições do utilizador.

2. [Criar](azure-stack-add-new-user-aad.md) , pelo menos, uma conta para que pode iniciar sessão para o conceito de pilha Azure como um inquilino.

  	| **Conta do Azure Active Directory**  | **Suportado?** |
  	|---|---| 
  	| Conta escolar ou profissional com subscrição Azure público válida  | Sim |
  	| Microsoft Account com subscrição do Azure público válida  | N |
  	| Conta escolar ou profissional com subscrição do Azure China válida  | Sim |
  	| Conta escolar ou profissional com-nos administração pública Azure subscrição válida  | Sim |


## <a name="network"></a>Rede

### <a name="switch"></a>Mudar

Uma porta disponível num parâmetro para o computador conceito.  

A máquina Azure pilha conceito suporta a ligação a uma porta de acesso de parâmetro ou ramal. Sem funcionalidades especializadas são necessários no parâmetro. Se estiver a utilizar uma porta ramal ou se tem de configurar um ID de VLAN, tem de fornecer o ID de VLAN como um parâmetro de implementação. Pode ver exemplos na [lista de parâmetros de implementação](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Sub-rede

Não estabelecer ligação a máquina conceito para as sub-redes do seguintes:
- 192.168.200.0/24
- 192.168.100.0/27
- 192.168.101.0/26
- 192.168.102.0/24
- 192.168.103.0/25
- 192.168.104.0/25

Estas sub-redes são reservados para as redes internas no ambiente do Microsoft Azure pilha conceito.

### <a name="ipv4ipv6"></a>IPv4/IPv6

Apenas IPv4 é suportada. Não é possível criar redes IPv6.

### <a name="dhcp"></a>DHCP

Certifique-se de que existe um servidor DHCP na rede que o NIC liga-se ao. Se DHCP não estiver disponível, tem de preparar uma rede IPv4 estática adicional para além da que utilizou ao anfitrião. Tem de fornecer esse endereço IP e gateway como um parâmetro de implementação. Pode ver exemplos na [lista de parâmetros de implementação](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Acesso à Internet

Pilha Azure requer o acesso à Internet, diretamente ou através de um proxy transparente. Pilha Azure não suporta a configuração de um proxy da web para ativar o acesso à Internet. O IP do anfitrião e o novo IP atribuído a BGPNAT01 MAS (pelo DHCP ou IP estático) tem de ser possível aceder à Internet. Portas 80 e 443 são utilizadas nos domínios graph.windows.net e login.windows.net.

### <a name="telemetry"></a>Telemetria

Para suportar o fluxo de dados de telemetria, porta 443 (HTTPS) deve estar aberta na sua rede. O ponto final de cliente é https://vortex-win.data.microsoft.com.


## <a name="next-steps"></a>Próximos passos

[Transferir o pacote de implementação do Azure conceito de pilha](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Implementar o conceito de pilha Azure](azure-stack-run-powershell-script.md)
