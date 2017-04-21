

Para as aplicações que necessitam de dimensionar cluster recursos e na escala balanceamento implicitamente de operações em vários domínios falhas e atualização. Para uma introdução à escala VM conjuntos Consulte o [blogue Azure anúncio](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)recentes.

Veja estes vídeos para obter mais informações acerca de conjuntos de escala VM:

 - [Marcar Russinovich falam conjuntos de escala do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Conjuntos de escala de máquina virtual com boneco Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Criar e gerir escala VM conjuntos

Escala VM conjuntos podem ser definidos e implementado a utilizar modelos JSON e os [REST APIs](https://msdn.microsoft.com/library/mt589023.aspx) basta como VMs de Gestor de recursos do Azure individuais. Por conseguinte, qualquer métodos de implementação do Gestor de recursos do Azure padrão podem ser utilizados. Para mais informações sobre modelos, consulte [Gestor de recursos do Azure criação de modelos](../articles/resource-group-authoring-templates.md).

Um conjunto de modelos de exemplo para conjuntos de escala VM pode ser encontrado no guia de introdução do Azure modelos GitHub repositório aqui:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - Procure modelos com _vmss_ no título.

As páginas de detalhes para estes modelos, verá um botão que liga para a funcionalidade de implementação portal. Para implementar o conjunto de escala VM, clique no botão e, em seguida, preencha quaisquer parâmetros que são necessários no portal. Se não tiver a certeza se um recurso suporta maiúsculas ou com maiúsculas/minúsculas é mais seguro utilizar sempre os valores de parâmetros de minúsculas. Também existe uma útil dissecação vídeo de um modelo de conjunto de escala VM aqui:

[Escala VM definida dissecação de modelo](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Definir o dimensionamento uma escala de VM e saída

Para aumentar ou diminuir o número de máquinas virtuais num conjunto de escala VM, basta alterar a propriedade de _capacidade_ e implementar o modelo. Este simplificar torna mais fácil de escrever a sua própria camada de dimensionamento personalizada se pretende definir eventos de escala personalizada que não são suportados pelo Azure autoscale.

Se são Reimplementar um modelo para alterar a capacidade, pode definir um muito modelo mais pequeno que inclui apenas o SKU e a capacidade atualizada. Um exemplo de isto é mostrado aqui: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Para percorrer os passos que criar um conjunto de escala que está dimensionado automaticamente, consulte o artigo [Automaticamente escala máquinas num conjunto de escala de Máquina Virtual](../articles/virtual-machines/virtual-machines-windows-vmss-powershell-creating.md)

## <a name="monitoring-your-vm-scale-set"></a>Monitorizar os seus escala VM definir

Atualmente é recomendável que utilize o [Azure recurso Explorer](https://resources.azure.com) para ver VM conjuntos de escala. Conjuntos de escala VM estão um recurso em Microsoft.Compute, por isso partir deste site que pode vê-los ao expandir as ligações seguintes:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Escala VM definida cenários

Esta secção apresenta alguns cenários de conjunto de escala VM típicos. Alguns serviços Azure nível superiores (como o lote, ferro de serviço, o serviço de contentor Azure) irão utilizar estes cenários.

 - **RDP / SSH à escala VM definir instâncias** - VM de um conjunto de escala é criado dentro de uma VNET e VMs individuais no não são atribuídos endereços IP públicos. Esta é uma boa ideia porque geralmente não quiser que o overhead despesas e gestão da atribuir endereços IP separados para todos os recursos sem estado no seu cluster de grelha, e pode facilmente ligar a estes VMs a partir de outros recursos no seu VNET incluindo as quais têm endereços IP públicos como balanceadores de carga ou máquinas virtuais de autónomo.

 - **Ligar a VMs utilizando regras NAT** - pode criar um endereço IP público, atribua-o a um balanceador de carga e definir regras de entrada na NAT que mapeiam uma porta do endereço IP a uma porta numa VM no conjunto de escala de VM. Por exemplo

    Endereço IP público -> porta 50000 -> vmss\_0 -> porta 22 público IP - > 50001 porta -> vmss\_1 -> porta 22 público IP - > porta 50002 -> vmss\_2 -> porta 22

    Eis um exemplo de criação de um conjunto de escala VM que utiliza regras NAT para ativar a ligação SSH para cada VM numa escala de conjunto utilizando um endereço IP público único: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

    Eis um exemplo de fazer o mesmo com RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Ligar a VMs utilizando "jumpbox"** - se criar uma escala de VM defina e endereços de um programa autónomo VM no VNET a mesma, o autónomo VM e o conjunto de escala VM que VMS podem ligar-se entre si utilizando o seu IP interno conforme definido pelo VNET/sub-rede. Se criar um endereço IP público e atribuí-lo para o autónomo VM que pode RDP ou SSH o autónomo VM e, em seguida, ligue a partir desse computador ao seu instâncias de conjunto de escala VM. Poderá reparar neste momento que um conjunto de escala VM simple é implicitamente mais seguro do que um programa autónomo simple VM com um endereço IP público na sua configuração predefinida.

    Para um exemplo sobre esta abordagem, este modelo cria um cluster de Mesos simple que consiste um programa autónomo VM de modelo global de que gere um cluster VM conjunto de escala com base da VMs: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **Arredondar robin balanceamento de carga em escala VM definir instâncias** - se pretender entregar trabalho a um cluster de computação VMs utilizando uma abordagem de "round-robin", pode configurar um balanceador de carga Azure com balanceamento de carga regras em conformidade. Também pode definir sondas para confirmar a sua aplicação está em execução executando o ping portas com um caminho de protocolo, intervalo e pedido especificado.

    Eis um exemplo que cria um conjunto de escala VM de VMs a executar o servidor web do IIS e utiliza um balanceador de carga para equilibrar a carga de que recebe de cada VM. Também utiliza o protocolo HTTP para executar o ping um URL específico no cada VM: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) - olhar sobre o tipo de recurso Microsoft.Network/loadBalancers e networkProfile and extensionProfile na virtualMachineScaleSet.

 - **Implementar uma escala de VM definir como um cluster de computação um Gestor de cluster PaaS** - escala VM conjuntos, por vezes, são descritos como uma função de trabalho da próxima geração. É uma descrição válida, mas também executa o risco de escala confuso conjunto de funcionalidades com funcionalidades de função PaaS v1 trabalhador. Em sentido escala VM conjuntos fornecem um verdadeiro "trabalhador função" ou recurso de trabalho, em que fornecem um recurso de cluster GRG que é a plataforma/runtime independente, personalizáveis e integra no Azure IaaS de Gestor de recursos.

    Uma PaaS v1 trabalhador função, enquanto um número limitado em termos de suporte de plataforma/runtime (imagens do plataforma Windows apenas) também inclui serviços como trocar VIP, as definições de atualização configuráveis, runtime/aplicação implementação definições específicas que não são _ainda_ disponíveis na VM Dimensionar conjuntos ou serão enviados por outros serviços de PaaS nível superiores como ferro de serviço. Com isto em consideração que pode observe conjuntos de escala VM como uma infraestrutura que suporta PaaS. Isto é Soluções de PaaS por ferro serviço ou os gestores de cluster como Mesos podem ser criados na parte superior de VM Dimensionar conjuntos como uma camada cluster dimensionáveis.

    Eis um exemplo de um cluster de Mesos que implementa uma escala de VM definida na VNET mesmo como um programa autónomo VM. O autónomo VM é um modelo global de Mesos e o conjunto de escala VM representa um conjunto de nós secundária: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). As versões futuras do [Serviço de contentor Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) irão implementar mais versões complexo/temperado este cenário baseada nos conjuntos de escala VM.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>VM escala conjunto desempenho e a escala orientações

- Durante a pré-visualização pública, não crie mais de 500 VMs em vários conjuntos de escala VM cada vez.
- Planear não mais de 40 VMs por conta de armazenamento.
- Expandir as primeiras letras de nomes de conta de armazenamento quanto possíveis.  Os modelos VMSS de exemplo no [Guia de introdução do Azure modelos](https://github.com/Azure/azure-quickstart-templates/) fornecem exemplos de como o fazer.
- Se utilizar VMs personalizados, planear não mais de 40 VMs por conjunto de escala VM, numa conta armazenamento única.  Terá a imagem previamente copiada para a conta de armazenamento antes de poder começar VM escala conjunto implementação. Consulte as FAQ para obter mais informações.
- Planear mais do que 2048 VMs por VNET.  Este limite será aumentado no futuro.
- O número de VMs pode criar é limitado pelo seu cluster de quota de core em qualquer região. Poderá ter de contactar o suporte ao cliente para aumentar o limite de quota cluster aumentado, mesmo se tiver um limite máximo de núcleos para utilização com serviços em nuvem ou IaaS v1 hoje. Para consultar a sua quota pode executar o seguinte comando Azure clip: _azure vm lista-a utilização_e o seguinte comando PowerShell: _Get-AzureRmVMUsage_ (se utilizar uma versão do PowerShell abaixo 1.0 utilize _Get-AzureVMUsage_).

## <a name="vm-scale-set-frequently-asked-questions"></a>Escala VM definir perguntas mais frequentes

**Q.** Quantos VMs podem ter um conjunto de escala VM?

**RESPOSTAS.** 100 se utilizar imagens de plataforma que podem ser distribuídas em várias contas de armazenamento. Se utilizar imagens personalizadas, até 40, uma vez que as imagens personalizadas estão limitadas a uma conta de armazenamento única durante a pré-visualização.

**As perguntas** que outros limites de recursos existem para conjuntos de escala VM?

**RESPOSTAS.** Está limitado a criar mais do que 500 VMs na vários conjuntos de escala por região durante o período de pré-visualização. Os existentes [limites de serviço de subscrição do Azure /](../articles/azure-subscription-service-limits.md) aplicar.

**Q.** São discos de dados suportados dentro de conjuntos de escala VM?

**RESPOSTAS.** Não no lançamento inicial. As opções para armazenar os dados são:

- Ficheiros Azure (unidades SMB partilhado)

- Unidade de sistema operativo

- Unidade TEMP (local, não cópia por armazenamento Azure)

- Dados externos de serviço (por exemplo, DB remoto, tabelas Azure, blobs do Azure)

**Q.** Que regiões Azure suportam para conjuntos de escala VM?

**RESPOSTAS.** Qualquer região que suporta o Gestor de recursos do Azure suporta VM conjuntos de escala.

**Q.** Como pode criar uma escala de VM conjuntos utilizando uma imagem personalizada?

**RESPOSTAS.** Deixe a propriedade vhdContainers em branco, por exemplo:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**Q.** Se posso reduzir meu escala VM definir capacidade de 20 a 15, que serão removidas VMs?

**RESPOSTAS.** Máquinas virtuais são removidas da escala definido uniformemente ao longo de atualização de domínios e de falhas para maximizar disponibilidade.

**Q.** Sobre-se que, em seguida, aumentar a capacidade de 15 18?

**RESPOSTAS.** Se aumentar a 18, VMs com índice 15, 16, 17 será criado. Em ambos os casos, os VMs são distribuídos por FDs e UDs.

**Q.** Quando utilizar múltiplas extensões num conjunto de escala VM, posso impor uma sequência de execução?

**RESPOSTAS.** Não diretamente, mas para a extensão de customScript, o script foi aguardar outra extensão concluir a (por exemplo através da monitorização a extensão de iniciar sessão - ver [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**Q.** Conjuntos de escala VM funcionam com conjuntos de disponibilidade Azure?

**RESPOSTAS.** Sim. Um conjunto de escala VM é uma disponibilidade implícita definir com 3 FDs e 5 UDs. Não tem de configurar nada em virtualMachineProfile. No futuro lançamentos conjuntos de escala VM provavelmente múltiplos inquilinos do intervalo mas agora uma escala de definir é um conjunto único de disponibilidade.
