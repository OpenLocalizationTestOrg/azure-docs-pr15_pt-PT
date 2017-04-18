<properties
    pageTitle="Descrição geral de máquinas virtuais do Windows | Microsoft Azure"
    description="Saiba mais sobre criar e gerir máquinas virtuais Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="davidmu"/>

# <a name="overview-of-windows-virtual-machines-in-azure"></a>Descrição geral de máquinas virtuais Windows Azure

Azure máquinas virtuais (VM) é um dos vários tipos de [a pedido, recursos informáticos dimensionáveis](../app-service-web/choose-web-site-cloud-service-vm.md) que oferece Azure. Normalmente, escolher uma VM quando precisar de mais controlo sobre o ambiente informático do que as outras opções da oferta. Este artigo fornece-lhe informações sobre o deverá tomar em consideração antes de criar uma VM, como criar e como gerir.

Um VM Azure dá-lhe a flexibilidade de Virtualização sem ter de comprar e manter o hardware físico que é executada-lo. No entanto, ainda é necessário manter a VM executando tarefas, tais como configurar, corrigir e instalar o software que é executada no mesmo.

Azure máquinas virtuais podem ser utilizadas de diversas formas. Alguns exemplos são:

- **Desenvolvimento e teste** – Azure VMs oferta uma rápida e fácil para criar um computador com configurações específicas necessário código e testar uma aplicação.
- **Aplicações na nuvem** – porque a pedido para a sua aplicação pode flutuar, poderá fazer sentido económico executá-la numa VM no Azure. Pode paga a extra VMs quando precisar de-los e encerrá-los quando que não.
- **Centro de dados expandido** – máquinas virtuais numa rede virtual Azure facilmente pode estar ligado à rede da sua organização.

O número de VMs que utiliza a sua aplicação pode dimensionar para cima e out para tudo o que é necessário para corresponder às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é necessário em consideração antes de criar uma VM?

Existem sempre inúmeros [Considerações de estrutura](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md) quando construir uma infraestrutura de aplicação no Azure. Estes aspetos de uma VM são importantes para em consideração antes de começar:

- Os nomes dos seus recursos de aplicação
- A localização onde estão armazenados os recursos
- O tamanho da VM
- O número máximo de VMs que podem ser criados
- O sistema operativo que é executada a VM
- A configuração da VM depois de ser iniciado
- Os recursos relacionados que necessita da VM

### <a name="naming"></a>Atribuição de nomes

Uma máquina virtual tem um [nome](virtual-machines-windows-infrastructure-naming-guidelines.md) atribuído à mesma e que tem um nome de computador configurado como parte do sistema operativo. O nome de uma VM pode ser até 15 carateres.

Se utiliza o Azure para criar o disco do sistema operativo, o nome do computador e o nome de máquina virtual são os mesmos. Se lhe [carregar e utilizar a sua própria imagem](virtual-machines-windows-upload-image.md) que contém um sistema operativo configurado anteriormente e utilizá-la para criar uma máquina virtual, os nomes podem ser diferentes. Recomendamos que quando carregue o seu próprio ficheiro de imagem, faça o nome do computador no sistema operativo e a máquina virtual o nome da mesma.

### <a name="locations"></a>Localizações

Todos os recursos que criou no Azure são distribuídos por várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Normalmente, a região é designado por **localização** quando cria uma VM. Para uma VM, a localização especifica onde estão armazenados os discos rígido virtuais.

Esta tabela mostra algumas formas do que pode obter uma lista de localizações disponíveis.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | Selecione uma localização a partir da lista quando cria uma VM. |
| Azure PowerShell | Utilize o comando [Get-AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx) . |
| REST API | Utilize a operação de [localizações da lista](https://msdn.microsoft.com/library/dn790540.aspx) . |

### <a name="vm-size"></a>Tamanho da memória virtual

O [tamanho](virtual-machines-windows-sizes.md) da VM que utiliza é determinada pela carga de trabalho que pretende executar. O tamanho que escolher, em seguida, determina fatores como capacidade power, memória e armazenamento de processamento. Azure oferece uma grande variedade de tamanhos de vários tipos de utilizações de suporte.

Azure encargos um [preço à hora](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) com base em tamanho e o sistema operativo a VM. Horas parcial, Azure encargos apenas para os minutos utilizados. Armazenamento é de preços e cobrado separadamente.

### <a name="vm-limits"></a>Limites VM

A sua subscrição tem predefinidos [limites de quota](../azure-subscription-service-limits.md) num local que pode afetar a implementação de muitos VMs para o seu projeto. O limite atual numa base por subscrição é 20 VMs por região. Podem ser aumentados limites através de um bilhetes de suporte pedir um aumento de apresentação.

### <a name="operating-system-disks-and-images"></a>Sistema operativo discos e imagens

Máquinas virtuais utilize [discos rígido virtuais (VHDs)](virtual-machines-windows-about-disks-vhds.md) para armazenar o seu sistema operativo (OS) e os dados. VHDs também são utilizados para as imagens que pode escolher a partir de instalar um SO. 

Azure fornece muitas [imagens marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) para utilizar com várias versões e tipos de sistemas operativos Windows Server. Imagens de Marketplace são identificadas por publisher de imagem, oferta, sku e versão (normalmente versão especificada como mais recente). 

Esta tabela mostra algumas formas que pode encontrar as informações de uma imagem.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | Os valores são automaticamente especificados por si quando seleciona uma imagem para utilizar. |
| Azure PowerShell | [Get AzureRMVMImagePublisher](https://msdn.microsoft.com/library/mt603484.aspx) -localização "localização"<BR>[Get AzureRMVMImageOffer](https://msdn.microsoft.com/library/mt603824.aspx) -localização "localização"-"publisherName" do Publisher<BR>[Get AzureRMVMImageSku](https://msdn.microsoft.com/library/mt619458.aspx) -localização "localização"-Publisher "publisherName"-oferecer "offerName" |
| REST APIs | [Fabricantes de imagem de lista](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[Imagem de lista oferece](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[Lista skus de imagem](https://msdn.microsoft.com/library/mt743701.aspx) |

Pode optar por [carregar e utilizar a sua própria imagem](virtual-machines-windows-upload-image.md) e quando o fizer, o nome do publisher, oferta e sku não são utilizadas.

### <a name="extensions"></a>Extensões

VM [extensões](virtual-machines-windows-extensions-features.md) dar as capacidades de adicionais VM através de configuração de implementação de mensagem e tarefas automatizadas.

Podem ser feitas estas tarefas comuns utilizando extensões:

- **Executar scripts personalizados** – a [Extensão de Script personalizada](virtual-machines-windows-extensions-customscript.md) ajuda a configurar das cargas de trabalho na VM ao executar o script quando a VM está aprovisionada.
- **Implementar e gerir as configurações** – a [extensão do PowerShell pretendido estado configuração (DSC)](virtual-machines-windows-extensions-dsc-overview.md) ajuda-o a configurar o DSC numa VM para gerir as configurações e ambientes.
- **Recolher dados de diagnóstico** – a [Extensão de diagnósticos do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) ajuda a configurar a VM para recolher dados de diagnóstico podem ser utilizados para monitorizar o estado de funcionamento da sua aplicação.

### <a name="related-resources"></a>Recursos relacionados

Os recursos nesta tabela são utilizados pela VM e tem de existir ou ser criada quando a VM é criada.

| Recurso | Obrigatório | Descrição |
| -------- | -------- | ----------- |
| [Grupo de recursos](../azure-resource-manager/resource-group-overview.md) | Sim | A VM tem de estar contida num grupo de recursos. |
| [Conta de armazenamento](../storage/storage-create-storage-account.md) | Sim | A VM tem a conta de armazenamento para armazenar os seus discos rígido virtuais. |
| [Rede virtual](../virtual-network/virtual-networks-overview.md) | Sim | A VM tem de ser um membro de uma rede virtual. |
| [Endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) | N | A VM pode ter um endereço IP público atribuído à mesma a remotamente aceder aos mesmos. |
| [Interface da rede](../virtual-network/virtual-network-network-interface-overview.md) | Sim | A VM tem a interface de rede para comunicar na rede. |
| [Discos de dados](virtual-machines-windows-attach-disk-portal.md) | N | A VM pode incluir discos de dados para expandir as capacidades de armazenamento. |

## <a name="how-do-i-create-my-first-vm"></a>Como posso criar meu VM primeiro?

Tem várias opções para criar o seu VM. Na opção que efetuar depende do ambiente que estiver numa. 

Esta tabela fornece informações para o ajudar a começar a criar o seu VM.

| Método | Artigo |
| ------ | ------- |
| Portal do Azure | [Criar uma máquina de virtual a executar o Windows através do portal](virtual-machines-windows-hero-tutorial.md) |
| Modelos | [Criar uma máquina de virtual do Windows com um modelo de Gestor de recursos](virtual-machines-windows-ps-template.md) |
| Azure PowerShell | [Criar uma VM do Windows através do PowerShell](virtual-machines-windows-ps-create.md) |
| SDK do cliente | [Implementar recursos Azure utilizando c#](virtual-machines-windows-csharp.md) |
| REST APIs | [Criar ou actualizar uma VM](https://msdn.microsoft.com/library/mt163591.aspx) |

Espero aconteçam nunca, mas, ocasionalmente, algo de errado. Se esta situação acontece para si, consulte as informações no [Gestor de recursos de resolução de problemas de implementação com a criação de uma máquina de virtual do Windows Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Como posso gerir o VM que criei?

VMs podem ser geridos utilizando um portal de baseada no browser, ferramentas da linha de comandos com o suporte para scripting, ou diretamente através da APIs. Algumas tarefas de gestão típicos que podem efetuar estão a obter informações sobre uma VM, iniciar sessão para uma VM, gestão de disponibilidade e efetuar cópias de segurança.

### <a name="get-information-about-a-vm"></a>Obter informações sobre uma VM

Esta tabela mostra-lhe algumas formas do que pode obter informações sobre uma VM.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | No menu concentrador, clique em **máquinas virtuais** e, em seguida, selecione a VM a partir da lista. No pá para a VM, ter acesso às informações de descrição geral, definição de valores e monitorizar métricas. |
| Azure PowerShell | Para obter informações sobre como utilizar o PowerShell para gerir VMs, consulte o artigo [gerir máquinas virtuais do Azure utilizando o Gestor de recursos e PowerShell](virtual-machines-windows-ps-manage.md). |
| REST API | Utilize a operação de [obter VM informações](https://msdn.microsoft.com/library/mt163682.aspx) para obter informações sobre uma VM. |
| SDK do cliente | Para obter informações sobre como utilizar c# para gerir VMs, consulte o artigo [gerir máquinas virtuais do Azure utilizando o Gestor de recursos do Azure e c#](virtual-machines-windows-csharp-manage.md). |

### <a name="log-on-to-the-vm"></a>Inicie sessão na VM

Utilize o botão Ligar no portal do Azure para [Iniciar uma sessão de ambiente de trabalho remoto (RDP)](virtual-machines-windows-connect-logon.md). Coisas, por vezes, podem correr mal quando tentar utilizar uma ligação remota. Se esta situação acontece para si, consulte o artigo as informações de ajuda em [ligações de resolver problemas de ambiente de trabalho remoto para uma máquina virtual Azure a executar o Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Gerir disponibilidade

É importante para si compreender como para [assegurar a elevada disponibilidade](virtual-machines-windows-manage-availability.md) para a sua aplicação. Esta configuração envolve criar múltiplas VMs para se certificar de que pelo menos um está em execução.

Para a sua implementação qualifique para o nosso Contrato de nível de serviço VM 99.95, é necessário implementar duas ou mais VMs executar a sua carga de trabalho no interior de um [conjunto de disponibilidade](virtual-machines-windows-infrastructure-availability-sets-guidelines.md). Esta configuração garante que seu VMs são distribuídos por vários domínios falhas e são implementadas no anfitriões com o windows de manutenção diferente. O total [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explica a disponibilidade de garantia de Azure como um todo.
 
### <a name="back-up-the-vm"></a>Criar uma cópia de segurança a VM
 
[Serviços de recuperação cofre](../backup/backup-introduction-to-azure-backup.md) é utilizado para proteger dados e elementos de serviços de cópia de segurança do Azure e recuperação de sites do Azure. Pode utilizar um cofre de serviços de recuperação para [Implementar e gerir as cópias de segurança para VMs implementado Gestor de recursos através do PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Próximos passos

- Se for a intenção para trabalhar com Linux VMs, observe [Azure e Linux](virtual-machines-linux-azure-overview.md).
- Saiba mais sobre as diretrizes à volta de configurar a sua infraestrutura no [Tutorial de infraestrutura de exemplo Azure](virtual-machines-windows-infrastructure-example.md).
- Certifique-se de que segue as [Melhores práticas para executar uma VM do Windows no Azure](virtual-machines-windows-guidance-compute-single-vm.md).


