<properties
   pageTitle="Implementação de Gestor de recursos e clássica | Microsoft Azure"
   description="Descreve as diferenças entre o modelo de implementação do Gestor de recursos e o clássico (ou gestão de serviços) modelo de implementação."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Gestor de recursos do Azure vs. implementação clássica: compreender os modelos de implementação e o estado dos seus recursos

Neste tópico Saiba mais sobre o Gestor de recursos do Azure e modelos de implementação clássico, o estado dos seus recursos, e por que razão os recursos foram implementados com um ou a outra. Gestor de recursos e modelos de implementação clássica representam duas maneiras diferentes de implementar e gerir as soluções do Azure. Trabalhar com eles através de dois conjuntos de API diferentes e os recursos implementados podem conter diferenças importantes. Dois modelos não estão completamente compatíveis com os outros. Este tópico descreve as diferenças.

Para simplificar a implementação e gestão de recursos, a Microsoft recomenda que utilize o Gestor de recursos para todos os recursos de novos. Se possível, a Microsoft recomenda que implementá recursos existentes através do Gestor de recursos.

Se estiver familiarizado para o Gestor de recursos, pretende reveja primeiro a terminologia definida na [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Histórico dos modelos de implementação

Azure fornecidas originalmente apenas o modelo de implementação clássica. Neste modelo, cada recurso existia forma independente; não tiver nenhuma forma de agrupar recursos relacionados. Em vez disso, tinha manualmente controlar quais os recursos que efetuou o seu solução ou a aplicação e não se esqueça de geri-los numa abordagem coordenada. Para implementar uma solução, tinha de criar cada recurso individualmente através do portal do clássico ou criar um script que implementado todos os recursos na ordem correta. Para eliminar uma solução, tinha de eliminar individualmente cada recurso. Poderá não facilmente aplicar e atualizar as políticas de controlo de acesso para recursos relacionados. Por fim, não é possível aplicar etiquetas para recursos etiquetá-los com termos que ajudá-lo a monitorizar os recursos e gerir a faturação.

Em 2014, Azure introduzidos recurso Gestor de designs, adicionado o conceito de um grupo de recursos. Um grupo de recursos é um contentor para recursos que partilham um ciclo de vida comuns. O modelo de implementação do Gestor de recursos fornece várias vantagens:

- Pode implementar, gerir e monitorizar todos os serviços para a sua solução como um grupo, em vez de processamento estes serviços individualmente.
- Repetidamente pode implementar a sua solução durante o ciclo de vida e ter confiança que os recursos são implementados num estado consistente.
- Pode aplicar o controlo de acesso a todos os recursos no seu grupo de recursos, e essas exigências são aplicadas automaticamente quando novos recursos são adicionados ao grupo de recursos.
- Pode aplicar etiquetas para recursos para organizar logicamente todos os recursos na sua subscrição.
- Pode utilizar JavaScript objeto notação (JSON) para definir a infraestrutura de para a sua solução. O ficheiro JSON é conhecido como um modelo de Gestor de recursos.
- Pode definir as dependências entre recursos, de modo que são implementadas na ordem correta.

Quando tiver sido adicionado o Gestor de recursos, todos os recursos com efeitos foram adicionados aos grupos predefinidos do recurso. Se criar um recurso através do clássico implementação agora, o recurso é automaticamente criado dentro de um grupo de recursos predefinido desse serviço, apesar de não tiver especificado esse grupo de recursos na implementação. No entanto, basta existentes dentro de um grupo de recursos não significam que foi convertido o recurso ao modelo de Gestor de recursos. Abordaremos como cada serviço trata os modelos de duas implementação na secção seguinte. 

## <a name="understand-support-for-the-models"></a>Compreender o suporte para os modelos 

Quando a decidir qual o modelo de implementação para utilizar para os recursos, existem três cenários ter em consideração:

1. O serviço suporta o Gestor de recursos e fornece um único tipo.
2. O serviço suporta Gestor de recursos, mas fornece dois tipos - um para o Gestor de recursos e outra para clássico. Este cenário só se aplica a máquinas virtuais, contas de armazenamento e redes virtuais.
3. O serviço não suporta o Gestor de recursos.

Para detetar se a um serviço suporta o Gestor de recursos, consulte [Gestor de recursos suportados fornecedores](resource-manager-supported-services.md).

Se o serviço que pretende utilizar não suporta o Gestor de recursos, deve continuar a utilizar implementação clássica.

Se o serviço de suportar o Gestor de recursos e **não é** uma máquina virtual, conta de armazenamento ou rede virtual, pode utilizar o Gestor de recursos sem qualquer complicações.

Para máquinas virtuais, contas de armazenamento e redes virtuais, se o recurso foi criado através de implementação clássica, tem de continuar a processá-lo através de operações clássicas. Se a máquina virtual, conta de armazenamento ou rede virtual foi criado através de implementação do Gestor de recursos, deve continuar a utilizar o Gestor de recursos operações. Esta distinção pode obter confusa quando a sua subscrição contém uma mistura de recursos criada através do Gestor de recursos e implementação clássica. Esta combinação de recursos pode criar resultados inesperados, porque os recursos não suportam as mesmas operações.

Em alguns casos, um comando de Gestor de recursos pode obter informações sobre um recurso criado através de implementação clássica ou pode executar uma tarefa administrativa, tal como mover um recurso clássico para outro grupo de recursos. No entanto, nestes casos não deverão dar a impressão de que o tipo de suporta operações de Gestor de recursos. Por exemplo, suponha que tem um grupo de recursos que contém uma máquina de virtual que foi criada com a implementação clássica. Se executar o seguinte comando Gestor de recursos do PowerShell:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Devolve a máquina virtual:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

No entanto, o Gestor de recursos do cmdlet **Get-AzureRmVM** devolve apenas máquinas virtuais implementadas através do Gestor de recursos. O seguinte comando não devolve a máquina virtual criada através de implementação clássica.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Apenas os recursos criados através de etiquetas de suporte do Gestor de recursos. Não é possível aplicar etiquetas para recursos clássicos.

## <a name="resource-manager-characteristics"></a>Características de Gestor de recursos

Para ajudar a compreender os dois modelos, vamos rever as características dos tipos de Gestor de recursos:

- Criada através do [Azure portal](https://portal.azure.com/).

     ![Portal do Azure](./media/resource-manager-deployment-model/portal.png)

     Para cluster, armazenamento e recursos de rede, tem a opção de utilização de implementação do Gestor de recursos ou clássico. Selecione **O Gestor de recursos**.

     ![Implementação do Gestor de recursos](./media/resource-manager-deployment-model/select-resource-manager.png)

- Criado com a versão do Gestor de recursos dos cmdlets do Azure PowerShell. Estes comandos tem o formato *AzureRmNoun verbais*.

        New-AzureRmResourceGroupDeployment

- Criado através da [Azure Gestor de recursos REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx) para operações de descanso.

- Criado através de comandos do Azure clip executados no modo de **processador** .

        azure config mode arm
        azure group deployment create 

- O tipo de recurso não inclui **(clássico)** no nome. A imagem seguinte mostra o tipo como **conta de armazenamento**.

    ![aplicação Web](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Características de implementação clássico

Também poderá conhecer o modelo de implementação clássica como o modelo de gestão de serviço.

Recursos criados no modelo de implementação clássica partilham as seguintes características:

- Criado através de do [portal clássico](https://manage.windowsazure.com)

     ![Portal clássico](./media/resource-manager-deployment-model/classic-portal.png)

     Em alternativa, o portal do Azure e especificar uma implementação **clássico** (para cluster, armazenamento e funcionamento em rede).

     ![Implementação clássica](./media/resource-manager-deployment-model/select-classic.png)

- Criado através da versão de gestão de serviço dos cmdlets do Azure PowerShell. Estes nomes de comando tem o formato *AzureNoun verbais*.

        New-AzureVM 

- Criado através da [REST API do serviço de gestão](https://msdn.microsoft.com/library/azure/ee460799.aspx) para operações de descanso.
- Criado através de comandos do Azure clip executados no modo de **asm** .

        azure config mode asm
        azure vm create 

- O tipo de recurso inclui **(clássico)** no nome. A imagem seguinte mostra o tipo como **conta de armazenamento (clássica)**.

    ![tipo de clássico](./media/resource-manager-deployment-model/classic-type.png)

Pode utilizar o portal do Azure para gerir os recursos que foram criados através de implementação clássica.

## <a name="changes-for-compute-network-and-storage"></a>Alterações para cluster, rede e armazenamento

O diagrama seguinte apresenta os recursos cluster, rede e armazenamento implementados através do Gestor de recursos.

![Arquitetura de Gestor de recursos](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Tenha em atenção as seguintes relações entre os recursos:

- Todos os recursos de existir dentro de um grupo de recursos.
- A máquina virtual depende de uma conta de armazenamento específicas definida no fornecedor de recursos de armazenamento para armazenar os seus discos no armazenamento blob do (obrigatório).
- A máquina virtual referencia uma NIC específica definida no fornecedor de recursos de rede (obrigatório) e uma disponibilidade definir definido no fornecedor de recurso cluster (opcional).
- NIC referências endereço IP atribuído a máquina virtual (obrigatório), a sub-rede da rede virtual para a máquina virtual (obrigatório) e para um grupo de segurança de rede (opcional).
- A sub-rede dentro de uma rede virtual faz referência a um grupo de segurança de rede (opcional).
- A instância do Balanceador de carga referencia o conjunto de back-end de endereços IP que incluem o NIC de uma máquina de virtual (opcional) e faz referência a um caso de carga balanceador público ou privado endereço IP (opcional).

Aqui estão os componentes e as respetivas relações para implementação clássica:

![arquitetura clássica](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

A solução clássica para alojar uma máquina virtual inclui:

- Um serviço de nuvem necessários que age como um contentor para aloja máquinas virtuais (cluster). Máquinas virtuais são fornecidas automaticamente com uma placa de interface de rede (NIC) e um endereço IP atribuído pelo Azure. Para além disso, o serviço de nuvem contém uma instância do Balanceador de carga externos, um endereço IP público e pontos finais predefinidos para permitir que o ambiente de trabalho e remote PowerShell o tráfego remoto para baseados no Windows máquinas virtuais e o tráfego de Shell seguro (SSH) para baseado em Linux máquinas virtuais.
- Uma conta de armazenamento necessários que armazena VHDs para uma máquina virtual, incluindo o sistema operativo, discos dados temporário e adicionais (armazenamento).
- Uma rede virtual opcional que age como um contentor adicional, na qual pode criar uma estrutura de sub-rede e designar sub-rede no qual está localizada a máquina virtual (rede).

A tabela seguinte descreve as alterações no interagem cluster, rede e armazenamento fornecedores de recurso:

 Item | Clássico | Gestor de recursos
 ---|---|---
| Serviço em nuvem para máquinas virtuais |  Serviço em nuvem era um contentor para a suspensão as máquinas virtuais que obrigatório disponibilidade a partir da plataforma e, em seguida, balanceamento de carga. | Serviço em nuvem já não se encontra um objeto necessário para criar uma máquina de Virtual utilizando o novo modelo. |
| Redes virtuais | Uma rede virtual é opcional para a máquina virtual. Se incluído, não pode ser implementada a rede virtual com o Gestor de recursos. | Máquina virtual necessita de uma rede virtual que tenha sido implementada com o Gestor de recursos. |
| Contas de armazenamento | A máquina virtual requer uma conta de armazenamento que armazena VHDs para o sistema operativo, discos dados temporário e adicionais. | A máquina virtual requer uma conta de armazenamento para armazenar os seus discos no armazenamento blob do. |
| Conjuntos de disponibilidade | Disponibilidade da plataforma foi indicada configurando mesmo "AvailabilitySetName" em máquinas virtuais. O número máximo de domínios falhas foi 2. | Conjunto de disponibilidade é um recurso exposto pela Microsoft.Compute fornecedor. Máquinas virtuais que requerem elevada disponibilidade devem ser incluídas no conjunto de disponibilidade. O número máximo de domínios falhas agora é 3. |
| Grupos de afinidade | Grupos de afinidade foram necessários para criar redes virtuais. No entanto, com a introdução de redes virtuais regionais, que não era necessária deixem. |Para simplificar, o conceito de grupos de afinidade não existe no APIs expostas através do Gestor de recursos do Azure. |
| Balanceamento de carga    | Criação de um serviço na nuvem fornece um balanceador de carga implícitos para as máquinas virtuais implementado. | O Balanceador de carga é um recurso exposto pelo fornecedor de Microsoft.Network. A interface de rede principal das máquinas virtuais que tem de estar balanceamento de carga deve ser referenciar o Balanceador de carga. Podem ser balanceadores de carga internas e externas. Uma instância do Balanceador de carga referencia o conjunto de back-end de endereços IP que incluem o NIC de uma máquina de virtual (opcional) e faz referência a um caso de carga balanceador público ou privado endereço IP (opcional). [Leia mais.](../articles/resource-groups-networking.md) |
|Endereço virtual IP | Serviços em nuvem obtém um VIP predefinido (endereço IP Virtual) quando uma VM é adicionada a um serviço na nuvem. O endereço IP Virtual é o endereço associado Balanceador de carga implícito.    | Endereço IP público é um recurso exposto pelo fornecedor de Microsoft.Network. Endereço IP público pode ser estática (reservado) ou dinâmica. IPs público dinâmico pode ser atribuído a um balanceador de carga. IPs público podem ser protegidos utilizando a grupos de segurança. |
|Endereço IP reservado|   Pode reservar um endereço de IP no Azure e associá-lo a um serviço na nuvem para assegurar que o endereço IP é autocolante.   | Endereço IP público podem ser criado no modo de "Estático" e que este oferece a funcionalidade mesmo como um "endereço IP reservado". IPs público estático só é possível atribuir um balanceador de carga neste momento. |
|Endereço IP público (PIP) por VM | Endereços IP público também pode ser associados a uma VM diretamente. | Endereço IP público é um recurso exposto pelo fornecedor de Microsoft.Network. Endereço IP público pode ser estática (reservado) ou dinâmica. No entanto, apenas IPs público dinâmico pode ser atribuído a uma Interface de rede para obter um endereço IP público por VM neste momento. |
|Pontos finais| Os pontos finais entrados necessários para ser configurada numa máquina Virtual de estar abertos para cima de conectividade de determinadas portas. Um dos modos comuns de estabelecer ligação ao máquinas virtuais concluídas ao configurar pontos finais de entrada. | As regras de entrada NAT pode ser configuradas no balanceadores de carga para alcançar a mesma funcionalidade da ativação de pontos finais de portas específicas para ligar para os VMs. |
|Nome de DNS| Um serviço na nuvem obteria um nome de DNS implícito globalmente exclusivo. Por exemplo: `mycoffeeshop.cloudapp.net`. | Nomes de DNS estão parâmetros opcionais que podem ser especificados num recurso endereço IP público. O FQDN é no seguinte formato - `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfaces de rede | Principais e secundárias Interface da rede e as respetivas propriedades foram definidas como a configuração de rede de uma Máquina Virtual. | Interface da rede é um recurso exposto pela Microsoft.Network fornecedor. O ciclo de vida da Interface de rede não está associado a uma Máquina Virtual. Referencia-lo endereço IP atribuído a máquina virtual (obrigatório), a sub-rede da rede virtual para a máquina virtual (obrigatório) e para um grupo de segurança de rede (opcional). |

Para obter informações sobre como ligar redes virtuais a partir de modelos de implementação diferente, consulte o artigo [Ligar redes virtuais a partir de modelos de implementação diferente no portal](./vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrar de clássico para o Gestor de recursos

Se estiver pronto para migrar os recursos de implementação clássica para implementação do Gestor de recursos, consulte o artigo:

1. [Técnico abrangente vôo picado no suportadas plataforma migração de clássico para o Gestor de recursos do Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Plataformas suportadas migração dos recursos de IaaS de clássico para o Gestor de recursos do Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migrar IaaS recursos de clássico para o Gestor de recursos do Azure utilizando o PowerShell do Azure](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migrar IaaS recursos de clássico para o Gestor de recursos do Azure utilizando o clip do Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pode criar uma máquina de virtual utilizando o Gestor de recursos do Azure para implementar numa rede virtual ou uma conta de armazenamento criada utilizando implementação clássica?**

Não é suportada. Não pode utilizar o Gestor de recursos do Azure para implementar uma máquina virtual para uma rede virtual que foi criada utilizando implementação clássica.

**Pode criar uma máquina de virtual utilizando o Gestor de recursos do Azure a partir de uma imagem do utilizador que foi criada utilizando APIs de gestão de serviço do Azure?**

Não é suportada. No entanto, pode copiar os ficheiros VHD a partir de uma conta de armazenamento que foi criada utilizando as APIs da gestão de serviço e adicioná-los para uma nova conta criada através do Gestor de recursos do Azure.

**O que é o impacto na quota de para a minha subscrição?**

As quotas de máquinas virtuais, redes virtuais e as contas de armazenamento criadas através do Gestor de recursos do Azure são separadas de outros quotas. Cada subscrição obtém quotas para criar os recursos utilizando as novas APIs. Pode ler mais sobre as quotas adicionais [aqui](../articles/azure-subscription-service-limits.md).

**Pode continuar a utilizar o meu scripts automatizados para máquinas virtuais, redes virtuais e contas de armazenamento através de API do Gestor de recursos de aprovisionamento?**

A automatização e scripts que já tiver criado continuarem a trabalhar para as máquinas virtuais existentes, redes virtuais a negrito e o modo de gestão de serviço do Azure. No entanto, os scripts tem de ser atualizados para utilizar o novo esquema para criar os mesmos recursos através do modo de Gestor de recursos.

**Onde posso encontrar exemplos de modelos de Gestor de recursos do Azure?**

Pode encontrar um conjunto abrangente de modelos de starter em [Modelos de guia de introdução do Gestor de recursos do Azure](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Próximos passos

- Para guiá-lo através da criação de modelo que define uma máquina virtual, conta de armazenamento e de rede virtual, consulte o artigo [Tutorial do Gestor de recursos do modelo](resource-manager-template-walkthrough.md).
- Para ver os comandos para implementar um modelo, consulte o artigo [Implementar uma aplicação com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md).
