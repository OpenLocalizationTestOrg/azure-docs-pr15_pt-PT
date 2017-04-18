<properties
    pageTitle="Glossário do Azure - dicionário Azure | Microsoft Azure"
    description="Utilize o Azure glossário para compreender a terminologia de nuvem a plataforma Azure. Este breve dicionário Azure fornece as definições para termos de nuvem comuns para Azure."
    keywords="Azure dicionário, terminologia na nuvem, Glossário do Azure, definições de terminologia, termos na nuvem"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossário do Microsoft Azure: um dicionário da terminologia nuvem a plataforma Azure

Glossário do Microsoft Azure é um dicionário breve da terminologia na nuvem para a plataforma Azure.

## <a name="find-service-definitions-and-other-cloud-terms"></a>Localizar as definições de serviço e outros termos da nuvem

* Para saber as definições dos serviços Azure e os respetivos AWS homólogos consulte o artigo [do Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

* Para indústria geral nuvem termos consulte [nuvem computação termos](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Glossário Azure com as duas referências acima fornece uma ponto a ponto taxonomia para Azure e da indústria na nuvem.  

## <a name="azure-glossary-list"></a>Lista de glossário Azure


### <a name="account"></a>conta  
Trabalho ou escola ou conta Microsoft pessoal, que é utilizada para aceder e gerir uma subscrição do Azure.  
Consulte também [como Azure subscrições estão associadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>conjunto de disponibilidade  
Uma coleção de máquinas virtuais que são geridas em conjunto para fornecer redundância de aplicação e fiabilidade. A utilização de um conjunto de disponibilidade assegura que durante um evento de manutenção planeada ou não planeado, pelo menos, uma máquina virtual está disponível.  
Consulte também [Gerir a disponibilidade de máquinas virtuais de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gerir a disponibilidade de máquinas virtuais de Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="classic-model"></a>Modelo de implementação clássica Azure  
Um dos dois [modelos de implementação](resource-manager-deployment-model.md) utilizado para implementar recursos no Azure (o novo modelo é Gestor de recursos do Azure). Alguns recursos Azure podem ser implementados num modelo ou outro, enquanto outras pessoas podem ser implementadas em ambos os modelos. Seta de quatro pontas detalhe recursos Azure individuais que model(s) um recurso pode ser implementada com.


### <a name="cli"></a>Interface de comandos Azure (CLI)  
[Interface da linha de comandos](xplat-cli-install.md) que podem ser utilizados para gerir serviços Azure a partir do Windows, os x e Linux PCs.


### <a name="powershell"></a>Azure PowerShell  
Uma [interface da linha de comandos](powershell-install-configure.md) para gerir serviços Azure através de uma linha de comandos a partir de PCs do Windows. Alguns serviços ou funcionalidades de serviço podem ser geridas apenas através do PowerShell ou o clip. Seta de quatro pontas detalhes cada recurso Azure individual que model(s) um recurso pode ser implementada com.   
Consulte também [como instalar e configurar PowerShell do Azure](powershell-install-configure.md)


### <a name="arm-model"></a>Modelo de implementação do Azure Gestor de recursos  
Um dos dois [modelos de implementação](resource-manager-deployment-model.md) utilizado para implementar recursos no Microsoft Azure (a outra é o modelo de implementação clássico). Alguns recursos Azure podem ser implementados num modelo ou outro, enquanto outras pessoas podem ser implementadas em ambos os modelos. Seta de quatro pontas detalhe recursos Azure individuais que model(s) um recurso pode ser implementada com.


### <a name="fault-domain"></a>domínio de falhas  
A colecção de máquinas virtuais num conjunto de disponibilidade possivelmente pode falhar ao mesmo tempo. Um exemplo é um grupo de máquinas num bastidor que partilham um parâmetro de origem e de rede power comuns. No Azure, as máquinas virtuais num conjunto de disponibilidade automaticamente são separadas em vários domínios falhas.  
Consulte também [Gerir a disponibilidade de máquinas virtuais de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gerir a disponibilidade de máquinas virtuais de Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="geo"></a>Geo  
Um limite definido para residência de dados que normalmente contém duas ou mais regiões. Os limites podem ser dentro ou para além dos limites nacionais e são influenciados pela Regulamento de impostos. Cada geo tem pelo menos uma região. Exemplos de geos são Ásia-Pacífico e Japão. Também denominado *geográficos*.  
Consulte também [regiões Azure](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>Geo replicação  
O processo de replicadas automaticamente conteúdo tal como blobs, tabelas e filas dentro de um par regional.  
Consulte também [Replicação de Geo ativa para a base de dados Azure SQL](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>imagem  
Um ficheiro que contém o sistema operativo e configuração da aplicação que pode ser utilizada para criar qualquer número de máquinas virtuais. No Azure existem dois tipos de imagens: VM imagem e SO imagem. Uma imagem VM inclui num sistema operativo e todos os discos anexados a uma máquina virtual quando a imagem é criada. Imagem do sistema operativo contém apenas um sistema operativo GRG com configurações do disco sem dados.  
Consulte também [navegar e selecionadas imagens de máquina virtual do Windows no Azure com PowerShell ou o clip](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>limites  
O número de recursos que podem ser criados ou o referência de desempenho que pode ser obtido. Limites estão normalmente associados subscrições, serviços e ofertas.  
Consulte também [subscrição Azure e limites de serviço, quotas e restrições](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>Balanceador de carga  
Um recurso que distribui o tráfego entre computadores numa rede. No Azure, um balanceador de carga distribuir o tráfego para o máquinas virtuais definidas num conjunto Balanceador de carga. Um [Balanceador de carga](./load-balancer/load-balancer-overview.md) pode ser acesso à internet ou pode ser interno.  


### <a name="offer"></a>oferta  
Os preços, créditos e termos relacionados aplicáveis para uma subscrição do Azure.  
Consulte a [página Detalhes da oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>Portal  
O portal Web seguro utilizado para implementar e gerir serviços Azure.  Existem duas portais: O [portal do Azure](http://portal.azure.com/) e o [portal clássica](http://manage.windowsazure.com/). Alguns serviços estão disponíveis em ambos os portais, Considerando que outras pessoas só estão disponíveis em uma ou de outra. As listas de [gráfico de disponibilidade portal Azure](https://azure.microsoft.com/features/azure-portal/availability/) que serviços estão disponíveis no qual portal.  


### <a name="region"></a>região  
Uma área dentro de uma geo que não nacionais não através de limites e contém um ou mais centros de dados. Preços, serviços regionais e tipos de oferta são expostos ao nível de região. Uma região é normalmente emparelhada com outro região, pode ser até várias cem milhas ausente, para formar um par regional. Pares regionais podem ser utilizados como um mecanismo para recuperação de falhas e cenários de elevada disponibilidade. Também conhecida geralmente como *localização*.  
Consulte também [regiões Azure](best-practices-availability-paired-regions.md)


### <a name="resource"></a>recurso  
Um item que faz parte da sua solução Azure. Cada serviço Azure permite-lhe implementar diferentes tipos de recursos, como bases de dados ou máquinas virtuais.   
Consulte também o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md)


### <a name="resource-group"></a>grupo de recursos  
Um contentor no Gestor de recursos que detém recursos relacionados para uma aplicação. O grupo de recursos pode incluir todos os recursos para uma aplicação ou apenas os recursos que estão agrupados de forma lógica em conjunto. Pode decidir como pretende atribuir recursos a grupos de recursos com base no que considera mais coerente para a sua organização.  
Consulte também o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md)


### <a name="arm-template"></a>Modelo de Gestor de recursos  
Um ficheiro de JSON que define um ou mais recursos Azure forma declarativa e que define dependências entre os recursos implementadas. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetidamente.  
Consulte também [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>fornecedor de recursos  
Um serviço que fornece os recursos pode implementar e gerir através do Gestor de recursos. Cada fornecedor de recursos oferece operações para trabalhar com os recursos que são implementados. Fornecedores de recurso podem ser acedidos através do portal do Azure, PowerShell do Azure e várias SDK de programação.  
Consulte também o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md)


### <a name="role"></a>função  
Um meio para controlar o acesso que pode ser atribuído a utilizadores, grupos e serviços. Funções que conseguem efetuar ações, tais como criar, gerir e continue a ler Azure recursos.  
Consulte também [RBAC: funções incorporadas](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>contrato de nível de serviço (SLA)  
O acordo descreve compromissos da Microsoft para o tempo útil e conectividade. Cada serviço Azure tem um SLA específico.  
Consulte também [os acordos do nível serviço](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>conta de armazenamento  
Aceder a uma conta de armazenamento que dá-lhe para os serviços de Blobs do Azure, fila de espera, tabela e ficheiro de armazenamento do Windows Azure. A conta de armazenamento fornece o espaço de nomes exclusivo para os objetos de dados de armazenamento do Windows Azure.  
Consulte também [Azure sobre contas de armazenamento](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>subscrição  
Contrato de um cliente com a Microsoft, que permite-los para obter serviços Azure. Os preços de subscrição e termos relacionados são regidos pela oferta escolhida para a subscrição. Consulte o [contrato de subscrição Online da Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/).  
Consulte também [como Azure subscrições estão associadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>etiqueta  
Um termo de indexação que permite-lhe categorizar recursos de acordo com os seus requisitos para gerir ou de faturação. Pode utilizar etiquetas quando tiver uma coleção complexa de grupos de recursos e recursos e terá de visualizar esses elementos da forma que considera mais coerente. Por exemplo, pode marcar recursos que servem uma função semelhante na sua organização ou pertencem ao mesmo departamento.  
Consulte também [utilizar etiquetas para organizar os recursos do Azure](resource-group-using-tags.md)


### <a name="update-domain"></a>Atualizar o domínio  
Colecção de máquinas virtuais num conjunto de disponibilidade são atualizados ao mesmo tempo. Máquinas virtuais no mesmo domínio atualização são reiniciadas em conjunto durante a manutenção planeada. Azure nunca reinicia mais do que um domínio de actualização de cada vez. Também conhecida como um domínio de atualização.  
Consulte também [Gerir a disponibilidade de máquinas virtuais de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gerir a disponibilidade de máquinas virtuais de Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="vm"></a>máquina virtual  
A implementação de software de um computador físico que é executada num sistema operativo. Várias máquinas virtuais pode executar em simultâneo no mesmo hardware. No Azure, estão disponíveis numa variedade de tamanhos de máquinas virtuais.  
Consulte também [documentação máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>extensão de máquina virtual  
Um recurso que implemente comportamentos ou funcionalidades que ajudam a outros programas trabalhar ou fornecer a capacidade para interagir com um computador em execução. Por exemplo, pode utilizar a extensão de acesso de VM para repor ou modifique os valores de acesso remoto numa máquina virtual Azure.  
Consulte também [sobre funcionalidades (Windows) e extensões de máquina virtual](./virtual-machines/virtual-machines-windows-extensions-features.md) ou [sobre funcionalidades (Linux) e extensões de máquina virtual](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>rede virtual  
Uma rede que fornece uma ligação entre os Azure recursos que está isolada de todos os outros inquilinos do Azure. É possível ligar a outras redes virtuais Azure através de um [Gateway de VPN do Azure](./vpn-gateway/vpn-gateway-about-vpngateways.md) e à sua rede no local utilizando [várias opções](./vpn-gateway/vpn-gateway-cross-premises-options.md). Pode controlar totalmente a blocos de endereços IP, definições de DNS, políticas de segurança e encaminhar tabelas dentro desta rede.  
Consulte também o artigo [Descrição geral de rede Virtual](./virtual-network/virtual-networks-overview.md)  

###<a name="see-also"></a>**Consulte também**  
- [Introdução ao Azure](https://azure.microsoft.com/get-started/)
- [Centro de recursos na nuvem](https://azure.microsoft.com/resources/)  
- [Azure para a sua aplicação de empresas](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure no seu centro de dados](https://azure.microsoft.com/overview/business-apps-on-azure/) 





