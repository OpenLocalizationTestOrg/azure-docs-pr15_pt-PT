<properties 
   pageTitle="Como migrar a partir de grupos de afinidade para uma rede Virtual regionais (VNet)"
   description="Saiba como migrar a partir de grupos de afinidade para vnets regionais"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-migrate-from-affinity-groups-to-a-regional-virtual-network-vnet"></a>Como migrar a partir de grupos de afinidade para uma rede Virtual regionais (VNet)

Pode utilizar um grupo de afinidade para se certificar de que os recursos criados dentro do mesmo grupo afinidade física são alojados por servidores que são juntos, permitindo que os estes recursos comunicar mais rápido. No passado, grupos de afinidade foram um requisito para a criação de redes virtuais (VNets). Nesse momento, o serviço de Gestor de rede que geridos VNets poderia só funcionam dentro de um conjunto de servidores físicos ou unidade de escala. Melhorias arquitetura tiverem aumentado o âmbito da gestão de rede para uma região.

Estatístico como resultado destes melhoramentos arquitetura, afinidade grupos são já não são recomendados ou necessários para o redes virtuais. A utilização de grupos de afinidade VNets vai ser substituída por regiões. VNets que estão associadas regiões chamam VNets regionais.

Para além disso, recomendamos que não utilize afinidade grupos em geral. Para além do requisito de VNet afinidade grupos também foram importantes que utilize para se certificar de recursos, tais como cluster e armazenamento, foram colocados próximas umas das outras. No entanto, com a arquitetura de rede Azure atual, estes requisitos posicionamento já não são necessários. Consulte o artigo [afinidade grupos e VMs](#Affinity-groups-and-VMs) para alguns casos específicos restantes onde poderá querer utilizar um grupo de afinidade.

## <a name="creating-and-migrating-to-regional-vnets"></a>Criar e migrar para o VNets regionais

Passará, ao criar uma nova VNets, utilize a *região*. Verá esta como opção no Portal de gestão. Tenha em atenção que no ficheiro de configuração de rede, mostra como *localização*.

>[AZURE.IMPORTANT] Apesar de ser ainda tecnicamente possível criar uma rede virtual que está associada um grupo de afinidade, não existe nenhuma razão apelativa para fazê-lo. Muitas funcionalidades novas, tal como grupos de segurança de rede, só estão disponíveis ao utilizar um VNet regional e não estão disponíveis para redes virtuais que estão associadas a afinidade grupos.

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>Sobre VNets associadas atualmente afinidade grupos

VNets associadas atualmente afinidade grupos estão ativados para migração para VNets regionais. Para migrar para um VNet regional, siga estes passos:

1. Exporte o ficheiro de configuração de rede. Pode utilizar o PowerShell ou o Portal de gestão. Para obter instruções utilizando o Portal de gestão, consulte [configurar o seu VNet com um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md).

1. Edite o ficheiro de configuração de rede, substituir os valores antigos com os novos valores. 

    > [AZURE.NOTE] A **localização** é a região que especificou para o grupo de afinidade associado ao seu VNet. Por exemplo, se o seu VNet está associada um grupo de afinidade está localizado no Oeste US, quando migra, sua localização têm de apontar para EUA Ocidental. 
    
    Edite as seguintes linhas no seu ficheiro de configuração de rede, substituir os valores com a sua própria: 

    **Valor antigo:** \<VirtualNetworkSitename = AffinityGroup "VNetUSWest" = "VNetDemoAG"\> 

    **Novo valor:** \<VirtualNetworkSitename = localização "VNetUSWest" = "US Ocidental"\>

1. Guarde as alterações e [Importar](virtual-networks-using-network-configuration-file.md) a configuração de rede Azure.

>[AZURE.NOTE] Esta migração não causa qualquer tempo de inatividade para os seus serviços.

## <a name="affinity-groups-and-vms"></a>Grupos de afinidade e VMs

Tal como mencionado anteriormente, grupos de afinidade são geralmente já não são recomendados para VMs. Deve utilizar um grupo de afinidade apenas quando um conjunto de VMs tem de ter a latência absoluta mais baixa de rede entre os VMs. Ao colocar VMs num grupo afinidade, os VMs serão todos colocados na mesma cluster de computação ou escala unidade.

É importante ter em atenção que a utilizar um grupo de afinidade pode ter duas, possivelmente negativo, consequências:

- O conjunto de tamanhos VM serão limitado ao conjunto de tamanhos VM disponibilizadas pela unidade de escala cluster.

- Existe uma maior probabilidade de não ser capaz de atribuir uma nova VM. Isto acontece quando a unidade de escala específicas para o grupo afinidade estiver fora do capacidade.

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>O que fazer se tiver uma VM num grupo afinidade

VMs que se encontram atualmente num grupo afinidade não necessita de ser removido do grupo afinidade.

Assim que é implementada uma VM, é implementada para uma unidade de escala único. Afinidade grupos podem restringir o conjunto de tamanhos VM disponíveis para uma nova implementação VM, mas qualquer VM existente que é implementado já está restrita ao conjunto de tamanhos de VM disponíveis na unidade de escala, na qual a VM é implementada. Por esta razão, remover uma VM do grupo afinidade não tem qualquer efeito.
 
