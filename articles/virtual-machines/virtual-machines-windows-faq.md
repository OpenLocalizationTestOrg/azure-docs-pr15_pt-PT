<properties
    pageTitle="FAQ para o Windows VMs | Microsoft Azure"
    description="Fornece respostas a algumas perguntas comuns sobre máquinas virtuais de Windows criadas com o modelo de Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Perguntas frequentes acerca Windows de máquinas virtuais 


Este artigo aborda algumas perguntas comuns sobre máquinas virtuais de Windows criadas no Azure utilizando o modelo de implementação do Gestor de recursos. Para obter a versão Linux neste tópico, consulte a [pergunta acerca Linux de máquinas virtuais mais frequentes](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que pode executar numa VM Azure?

Todos os subscritores podem executar software de servidor de uma máquina virtual Azure. Para obter informações sobre a política de suporte para executar software de servidor do Microsoft no Azure, consulte o artigo [Microsoft software de servidor de suporte para máquinas virtuais do Azure](https://support.microsoft.com/kb/2721672)

Algumas versões do Windows 7 e Windows 8.1 estão disponíveis para subscritores do benefício do MSDN Azure e subscritores Dev Center do MSDN e testar repartição, para as tarefas de desenvolvimento e teste. Para obter detalhes, incluindo instruções e limitações, consulte o artigo [imagens de cliente do Windows para subscritores do MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quantidade de armazenamento pode utilizar com uma máquina virtual

Cada disco de dados pode ser até 1 TB. O número de discos de dados, que pode utilizar depende do tamanho da máquina virtual. Para obter detalhes, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md).

Uma conta de armazenamento Azure disponibiliza armazenamento para o disco de sistema operativo e quaisquer discos de dados. Cada disco é um ficheiro. vhd armazenado como um blob de página. Para preços detalhes, consulte o artigo [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Como posso aceder ao meu máquina virtual?

Estabelecer uma ligação remoto com ligação de ambiente de trabalho remoto (RDP) para uma VM do Windows. Para obter instruções, consulte o artigo [como ligar e iniciar sessão para uma máquina virtual Azure a executar o Windows](virtual-machines-windows-connect-logon.md). São suportados um máximo de duas ligações em simultâneo, a menos que o servidor está configurado como um anfitrião da sessão de serviços de ambiente de trabalho remoto.  


Se estiver a ter problemas com o ambiente de trabalho remoto, consulte o artigo [resolver problemas de ambiente de trabalho remoto ligações para uma baseados no Windows Azure Máquina Virtual](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Se estiver familiarizado com Hyper-V, poderá ser está à procura de uma ferramenta semelhante ao VMConnect. Azure não oferece uma ferramenta semelhante, uma vez que o acesso à consola para uma máquina virtual não é suportado.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Pode utilizar o disco temporário (a unidade d: por predefinição) para armazenar dados?

Não utilize o disco temporário para armazenar dados. Só é armazenamento temporário, por isso que corra o risco perder os dados que não podem ser recuperados. Perda de dados pode ocorrer quando a máquina virtual move o cursor para um anfitrião diferente. Redimensionar uma máquina virtual, atualizar o anfitrião ou uma falha de hardware no anfitrião do é algumas das razões que poderá mover uma máquina virtual.

Se tiver uma aplicação que precise de utilizar a letra da unidade d:, pode reatribuir letras de unidade para que o disco temporário utiliza algo que não d:. Para obter instruções, consulte o artigo [alterar a letra da unidade de disco temporário Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como posso alterar a letra da unidade de disco temporário?

Pode alterar a letra da unidade ao mover o ficheiro da página e reatribuir letras de unidade, mas é necessário para se certificar de que execute os passos por uma ordem específica. Para obter instruções, consulte o artigo [alterar a letra da unidade de disco temporário Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Pode adicionar uma VM existente para um conjunto de disponibilidade?

Não. Se pretender que o seu VM façam parte de um conjunto de disponibilidade, tem de criar a VM dentro do conjunto. Atualmente não existe uma maneira de adicionar uma VM para uma disponibilidade definida após ter sido criada.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Pode carregar uma máquina virtual para o Azure?

Sim. Para obter instruções, consulte o artigo [carregar uma imagem VM do Windows Azure](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk"></a>Pode redimensionar o disco SO?

Sim. Para obter instruções, consulte o artigo [como expandir a unidade de sistema operativo de uma Máquina Virtual num grupo de recursos do Azure](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Pode copiar ou clonar uma VM Azure existente?

Sim. Para obter instruções, consulte o artigo [como criar uma cópia de uma máquina de virtual do Windows no modelo de implementação de Gestor de recursos](virtual-machines-windows-vhd-copy.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que razão estou a não ver Canadá Central e as regiões do Leste Canadá através do Gestor de recursos do Azure?

Novas regiões de Canadá Central e do Leste Canadá não estão automaticamente registadas para a criação de máquina virtual para subscrições do Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implementada através do portal do Azure para qualquer outro região utilizando o Gestor de recursos do Azure. Depois de uma máquina virtual é implementada para qualquer outro região Azure, novas regiões deverão estar disponíveis para máquinas virtuais subsequentes.

## <a name="does-azure-support-linux-vms"></a>Azure suporta Linux VMs?

Sim. Para criar rapidamente uma VM Linux para experimentar a, consulte o artigo [criar uma VM Linux no Azure através do Portal](virtual-machines-linux-quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar uma imagem para minha VM após ter sido criada?

Não. Adicionar uma imagem pode apenas ser feito hora de criação.

## <a name="are-there-any-computer-name-requirements"></a>Existem quaisquer requisitos de nome de computador?

Sim. O nome do computador pode conter no máximo 15 carateres de comprimento. Consulte [diretrizes de nomenclatura de infraestrutura](virtual-machines-windows-infrastructure-naming-guidelines.md) para obter mais informações à volta de nomenclatura os recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de utilizador quando criar uma VM?

Liste pode ser um máximo de 20 caracteres de comprimento e não pode terminar com um período ("."). 

Liste as seguintes não é permitidos:

<table>
    <tr>
        <td style="text-align:center">administrador </td><td style="text-align:center"> Admin </td><td style="text-align:center"> utilizador </td><td style="text-align:center"> Utilizador1</td>
    </tr>
    <tr>
        <td style="text-align:center">teste </td><td style="text-align:center"> Utilizador2 </td><td style="text-align:center"> Teste1 </td><td style="text-align:center"> Utilizador3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> um</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">cópia de segurança </td><td style="text-align:center"> consola </td><td style="text-align:center"> David </td><td style="text-align:center"> convidado</td>
    </tr>
    <tr>
        <td style="text-align:center">João </td><td style="text-align:center"> proprietário </td><td style="text-align:center"> raiz </td><td style="text-align:center"> servidor</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> suporte </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sobre</td>
    </tr>
    <tr>
        <td style="text-align:center">Teste2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> Utilizador4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de palavra-passe ao criar uma VM?

As palavras-passe devem ser 8 123 carateres de comprimento e reunir 3 terminar os seguintes requisitos de 4 complexidade:

- Ter carateres inferiores
- Ter carateres superiores
- Ter um dígito
- Tem um caráter especial (Regex corresponder [\W_])

As palavras-passe seguintes não são permitidas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Word de $$ Pa</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Palavra-passe!</td><td style="text-align:center">Palavra-passe1</td><td style="text-align:center">Password22</td><td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
