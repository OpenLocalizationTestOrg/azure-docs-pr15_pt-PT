<properties
    pageTitle="FAQ para Linux VMs | Microsoft Azure"
    description="Fornece respostas a algumas perguntas comuns sobre máquinas virtuais de Linux criadas com o modelo de Gestor de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Perguntas frequentes acerca Linux de máquinas virtuais

Este artigo aborda algumas perguntas comuns sobre máquinas virtuais de Linux criadas no Azure utilizando o modelo de implementação do Gestor de recursos. Para a versão do Windows neste tópico, consulte o artigo [Perguntas mais pergunta acerca Windows de máquinas virtuais](virtual-machines-windows-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que pode executar numa VM Azure?

Todos os subscritores podem executar software de servidor de uma máquina virtual Azure. Para obter mais informações, consulte o artigo [Linux em Azure-Endorsed distribuições](virtual-machines-linux-endorsed-distros.md)


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quantidade de armazenamento pode utilizar com uma máquina virtual

Cada disco de dados pode ser até 1 TB. O número de discos de dados, que pode utilizar depende do tamanho da máquina virtual. Para obter detalhes, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

Uma conta de armazenamento Azure disponibiliza armazenamento para o disco de sistema operativo e quaisquer discos de dados. Cada disco é um ficheiro. vhd armazenado como um blob de página. Para preços detalhes, consulte o artigo [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine"></a>Como posso aceder ao meu máquina virtual?

Estabelecer uma ligação remoto para iniciar sessão no máquina virtual, utilizando a Shell de seguro (SSH). Consulte as instruções sobre como ligar [a partir do Windows](virtual-machines-linux-ssh-from-windows.md) ou [a partir do Linux e Mac](virtual-machines-linux-mac-create-ssh-keys.md). Por predefinição, SSH permite um máximo de 10 ligações em simultâneo. Pode aumentar este número ao editar o ficheiro de configuração.


Se estiver com problemas, consulte o artigo [resolver problemas de seguro Shell (SSH) ligações](virtual-machines-linux-troubleshoot-ssh-connection.md).


## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Pode utilizar o disco temporário (/ Dev Center/sdb1) para armazenar dados?

Não utilize o disco temporário (/ Dev Center/sdb1) para armazenar dados. Destina-se apenas aí armazenamento temporário. Risco de perda de dados que não podem ser recuperados.


## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Pode copiar ou clonar uma VM Azure existente?

Sim. Para obter instruções, consulte o artigo [como criar uma cópia de uma máquina de virtual Linux no modelo de implementação de Gestor de recursos](virtual-machines-linux-copy-vm.md).


## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que razão estou a não ver Canadá Central e as regiões do Leste Canadá através do Gestor de recursos do Azure?

Novas regiões de Canadá Central e do Leste Canadá não estão automaticamente registadas para a criação de máquina virtual para subscrições do Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implementada através do portal do Azure para qualquer outro região utilizando o Gestor de recursos do Azure. Depois de uma máquina virtual é implementada para qualquer outro região Azure, novas regiões deverão estar disponíveis para máquinas virtuais subsequentes.


## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar uma imagem para minha VM após ter sido criada?

Não. Adicionar uma imagem pode apenas ser feito hora de criação.


## <a name="are-there-any-computer-name-requirements"></a>Existem quaisquer requisitos de nome de computador?

Sim. O nome do computador, pode ser um máximo de 64 carateres de comprimento. Consulte [diretrizes de nomenclatura de infraestrutura](virtual-machines-linux-infrastructure-naming-guidelines.md) para obter mais informações à volta de nomenclatura os recursos.


## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de utilizador quando criar uma VM?

Nomes de utilizador tem de ser 1-64 carateres de comprimento.

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

As palavras-passe devem ser 6-72 carateres de comprimento e reunir 3 terminar os seguintes requisitos de 4 complexidade:

- Ter carateres inferiores
- Ter carateres superiores
- Ter um dígito
- Tem um caráter especial (Regex corresponder [\W_])

As palavras-passe seguintes não são permitidas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Word de $$ Pa</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Palavra-passe!</td>
        <td style="text-align:center">Palavra-passe1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
