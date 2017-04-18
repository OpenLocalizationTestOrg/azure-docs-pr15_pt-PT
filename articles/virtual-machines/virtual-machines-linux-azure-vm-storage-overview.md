<properties
  pageTitle="Azure e armazenamento de Linux VM | Microsoft Azure"
  description="Descreve Azure padrão e armazenamento de Premium com máquinas virtuais de Linux."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines-linux"
  authors="vlivech"
  manager="timlt"
  editor=""/>

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure"
  ms.date="10/04/2016"
  ms.author="v-livech"/>

# <a name="azure-and-linux-vm-storage"></a>Armazenamento de Azure e Linux VM

Armazenamento Azure é a solução de armazenamento na nuvem para aplicações modernas que dependem de durabilidade, disponibilidade e escalabilidade para satisfazer as necessidades dos seus clientes.  Para além de tornar possíveis para os programadores criem aplicações em grande escala para suportar novos cenários, armazenamento do Windows Azure também fornece a Fundação de armazenamento para máquinas virtuais do Azure.

## <a name="azure-storage-standard-and-premium"></a>Armazenamento Azure: Standard e Premium

Pode baseia-se do Azure VM nos armazenamento padrão discos ou discos de armazenamento de premium.  Quando utilizar o Portal para escolher a VM tem de alternar uma lista pendente no ecrã Noções básicas para ver discos padrão e premium.  A captura de ecrã abaixo realça esse menu do botão de alternar.  Quando alternada para SSD, armazenamento de premium só serão apresentados VMs ativadas, todas as cópias por SSD unidades.  Quando alternada para disco, armazenamento padrão ativada VMs unidades de disco de cópia de segurança a girar serão apresentadas, juntamente com armazenamento premium VMs cópias por SSD.

  ![screen1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Quando criar uma VM a partir da `azure-cli` pode escolher entre standard e premium quando escolher o tamanho da memória virtual através de `-z` ou `--vm-size` clip sinalizador.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Criar uma VM com padrão armazenamento VM no clip

O sinalizador do clip `-z` escolhe Standard_A1 com A1 a ser um armazenamento padrão com base Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Criar uma VM com armazenamento de premium no clip

O sinalizador do clip `-z` seleciona Standard_DS1 com DS1 a ser um armazenamento premium baseada Linux VM.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Armazenamento padrão

Azure armazenamento padrão é o tipo de predefinição de armazenamento.  Armazenamento padrão é rentável mas ainda ser performant.  

## <a name="premium-storage"></a>Armazenamento de Premium

Armazenamento de Premium Azure fornece suporte do disco de alto desempenho, baixa latência para máquinas virtuais executadas posso/the-com um grau elevado cargas de trabalho. Discos de máquina virtual (VM) que utilizem Premium armazenamento armazenam dados em unidades de estado sólido (SSDs). Pode migrar discos VM da sua aplicação para o armazenamento do Windows Azure Premium para tirar partido da velocidade e o desempenho destes discos.

Funcionalidades Premium do armazenamento:

- Premium armazenamento discos: Azure Premium armazenamento suporta discos VM que podem ser anexados a série DS, DSv2 ou s Azure VMs.

- Premium página Blob: Prémio armazenamento suporta Blobs de página de Azure, são utilizados para colocar em espera discos persistentes para máquinas virtuais do Azure (VMs).

- Armazenamento localmente redundante Premium: Uma conta de armazenamento de Premium apenas suporta localmente redundantes armazenamento (LRS) como a opção de replicação e mantém três cópias dos dados numa única região.

- [Armazenamento de Premium](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Armazenamento de Premium suportado VMs

Armazenamento de Premium suporta a série de DS, série de DSv2, série s e máquinas virtuais do Fs série Azure (VMs). Pode utilizar discos de armazenamento de padrão e Premium com o armazenamento de Premium suportado de VMs. Mas não é possível utilizar discos de armazenamento de Premium com série VM, que não sejam Premium armazenamento compatíveis.

Seguem-se as distribuições Linux que recomendamos validados com armazenamento Premium.

| Distribuição | Versão                 | Kernel suportado    |
|--------------|-------------------------|---------------------|
| Ubuntu       | 12.04                   | 3.2.0-75.110+       |
| Ubuntu       | 14.04                   | 3.13.0-44.73+       |
| Debian       | 7. x, 8                | 3.16.7-ckt4-1+      |
| SLES         | SLES 12                 | 3.12.36-38.1+       |
| SLES         | SLES 11 SP4             | 3.0.101-0.63.1+     |
| CoreOS       | 584.0.0+                | 3.18.4+             |
| Centos       | 6.5, 6.6, 6,7, 7.0, 7.1 | 3.10.0-229.1.2.el7+ |
| RHEL         | 6.8 +, 7.2 +              |                     |


## <a name="file-storage"></a>Armazenamento de ficheiros

Armazenamento de ficheiros Azure oferece partilhas de ficheiros na nuvem através do protocolo SMB padrão. Com os ficheiros do Azure, pode migrar aplicações empresariais que dependem de servidores de ficheiros para Azure. Aplicações em execução no Azure podem facilmente montagem partilhas de ficheiros a partir do Azure máquinas virtuais Linux. E com a versão mais recente do armazenamento de ficheiros, também pode montagem uma partilha de ficheiros a partir de uma aplicação no local que suporte SMB 3.0.  Porque partilhas de ficheiros são partilhas SMB, pode aceder-lhes através do sistema de ficheiros padrão APIs.

Armazenamento de ficheiros é incorporado na mesma tecnologia como armazenamento de BLOBs, tabela e fila de espera, para que o armazenamento de ficheiros oferece a disponibilidade, durabilidade, escalabilidade e redundância geo incorporada na plataforma de armazenamento Azure. Para obter detalhes sobre destinos de desempenho de armazenamento de ficheiros e limites, consulte o artigo escalabilidade de armazenamento do Azure e metas de desempenho.

- [Como utilizar o armazenamento de ficheiros do Azure com Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Armazenamento quente

A camada de armazenamento quente Azure está optimizada para armazenar os dados são acedidos frequentemente.  Armazenamento quente é o tipo de armazenamento de BLOBs stores.

## <a name="cool-storage"></a>Armazenamento modernos

A camada de armazenamento modernos Azure está optimizada para armazenar os dados que é com pouca frequência acedido e longa vida. Casos de utilização de exemplo para o armazenamento do modernos incluem cópias de segurança, conteúdo multimédia, dados científicos, conformidade e dados de arquivo. Em geral, quaisquer dados que raramente são acedidos são um candidato perfeito modernos armazenamento.

|                             | Camadas de armazenamento quente      | Camadas de armazenamento modernos     |
|:----------------------------|:---------------------:|:---------------------:|
| Disponibilidade                | 99,9%                 | 99%                   |
| Disponibilidade (lê GRS RT) | 99,99%                | 99,9%                 |
| Custos de utilização               | Custos de armazenamento mais elevados  | Reduzir os custos de armazenamento   |
|                             | Acesso inferior          | Acesso mais elevado         |
|                             | e custos da transação | e custos da transação |


## <a name="redundancy"></a>Redundância

Os dados na sua conta de armazenamento do Microsoft Azure são sempre replicados para se certificar de durabilidade e elevada disponibilidade, o SLA de armazenamento do Azure mesmo face a falhas de hardware breves da reunião.

Quando cria uma conta de armazenamento, tem de selecionar uma das seguintes opções de replicação:

- Armazenamento localmente redundante (LRS)
- Armazenamento de zona redundantes (ZRS)
- Armazenamento geo redundantes (GRS)
- Acesso de leitura armazenamento geo redundantes (GRS RT)

### <a name="locally-redundant-storage"></a>Armazenamento localmente redundante

Armazenamento localmente redundante (LRS) replica os seus dados no interior da região em que criou a sua conta de armazenamento. Para maximizar durabilidade, todos os pedidos de efetuadas contra dados na sua conta de armazenamento são de replicadas três vezes. Estas três réplicas residem na falhas separada domínios e atualização.  Um pedido de com êxito devolve apenas depois de ter sido escrito para todas as três réplicas.

### <a name="zone-redundant-storage"></a>Armazenamento de zona redundantes

Armazenamento de zona redundantes (ZRS) replica os seus dados através de dois ou três instalações, dentro de uma única região ou entre as duas regiões, fornecendo durabilidade mais elevada que LRS. Se a sua conta de armazenamento tiver ZRS ativado, os seus dados são resistentes mesmo no caso de falha de uma das instalações.

### <a name="geo-redundant-storage"></a>Armazenamento geo redundantes

Armazenamento geo redundantes (GRS) replica os seus dados para uma região secundária que seja centenas de milhas afastando-o a região principal. Se a sua conta de armazenamento tiver GRS ativado, os seus dados são resistentes mesmo no caso de uma falha de regional concluída ou uma falhas em que a região principal não é recuperável.

### <a name="read-access-geo-redundant-storage"></a>Armazenamento de geo redundantes de acesso de leitura

Acesso de leitura armazenamento geo redundantes (GRS RT) maximiza a disponibilidade para a sua conta de armazenamento, ao fornecer acesso só de leitura para os dados na localização secundário, para além da replicação entre as duas regiões fornecida pela GRS. Na eventualidade de dados ficam indisponíveis na região principal, a aplicação pode ler dados da região do secundário.

Para um vôo picado abrangente para armazenamento Azure redundância consulte:

- [Azure replicação de armazenamento](../storage/storage-redundancy.md)

## <a name="scalability"></a>Escalabilidade

Armazenamento Azure é previstos excederem consideravelmente dimensionável, por isso pode armazenar e processo centenas de terabytes de dados para suportar os cenários de grande dados necessários ao análise científica, financeira e aplicações de multimédia. Ou pode armazenar pequenas quantidades de dados necessários para um site público pequenas empresas. Onde se situar às suas necessidades, pode pagar apenas para os dados que está a armazenar. Armazenamento Azure atualmente armazena dezenas de trillions de objetos de cliente exclusivo e alças de em média milhões de pedidos de por segundo.

Para contas de armazenamento padrão: uma conta de armazenamento padrão tem uma taxa de pedido de total máximo de 20.000 IOPS. O total IOPS através de todos os seus discos máquina virtual numa conta armazenamento padrão não deve exceder este limite.

Para contas de armazenamento premium: uma conta de armazenamento premium tem uma taxa de débito total máximo de 50 Gbps. O débito total em todos os seus discos VM não deve exceder este limite.

## <a name="availability"></a>Disponibilidade

Vamos garante que pelo menos 99,99% (99,9 para modernos camada de acesso) da hora, podemos com êxito processará pedidos para ler dados a partir de contas de acesso de leitura Geo armazenamento redundante (GRS RT), desde que tentativas falhadas de ler dados a partir da região primária são repetidas na região do secundário.

Vamos garante que pelo menos 99,9% (99% para modernos camada de acesso) da hora, podemos com êxito processará pedidos para ler dados a partir do localmente redundantes armazenamento (LRS), zona redundantes armazenamento (ZRS) e contas de armazenamento redundantes Geo (GRS).

Vamos garante que pelo menos 99,9% (99% para modernos camada de acesso) da hora, podemos com êxito processará pedidos para escrever dados localmente redundantes armazenamento (LRS), zona redundantes armazenamento (ZRS) e contas do armazenamento redundantes Geo (GRS) e contas do armazenamento redundante Geo de acesso de leitura (GRS RT).

- [Azure SLA armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)


## <a name="regions"></a>Regiões

Azure é disponibilizado em 30 regiões em todo o mundo e tem anunciada planos 4 regiões adicionais. Expansão geográfico é uma prioridade para Azure porque permite dos nossos clientes alcançar um melhor desempenho- e -suportam os respetivos requisitos e preferências de localização de dados.  Região mais recente do azures à iniciação é na Alemanha.

A Microsoft Cloud Alemanha fornece uma opção diferenciada aos serviços do Microsoft Cloud já disponíveis Europa, criar um aumento oportunidades para inovação e crescimento económico para altamente regulamentados parceiros e clientes na Alemanha, da União Europeia (UE) e a associação de comércio livre Europeia (EFTA).

Dados do cliente nestes centros de dados novos, no Magdeburg e Frankfurt, são geridos sob o controlo de um utilizador fidedigno dados, sistemas de T internacional, uma empresa alemão independente e subsidiária da marcos alemães Telekom. Serviços em nuvem comercial da Microsoft nestes centros de dados adiram dados alemãs processamento regulamentos e dar à clientes opções adicionais de como e onde os dados são processados.


- [Mapa de regiões Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Segurança

Armazenamento Azure fornece um conjunto abrangente de capacidades de segurança em conjunto permitir que os programadores criem aplicações seguras. A conta de armazenamento propriamente dita pode ser protegida utilizando o controlo de acesso baseado em funções e o Azure Active Directory. Dados podem ser protegidos em trânsito entre uma aplicação e Azure ao utilizar a encriptação do lado do cliente, HTTPS ou SMB 3.0. Dados podem ser definidos encriptados automaticamente quando escritas armazenamento do Windows Azure utilizando o serviço de armazenamento encriptação (SSE). Sistema operativo e dados discos utilizados por máquinas virtuais podem ser definidos para ser encriptado através de encriptação do Azure do disco. Pode ser concedido acesso delegado para os objetos de dados no armazenamento Azure utilizar assinaturas de acesso partilhado.

### <a name="management-plane-security"></a>Segurança do plano de gestão

O plano de gestão é composta pelos recursos utilizados para gerir a sua conta de armazenamento. Nesta secção, irá falarmos sobre o modelo de implementação do Gestor de recursos do Azure e como utilizar o controlo de acesso baseado em funções RBCA () para controlar o acesso às suas contas de armazenamento. Recomendamos também serão falar sobre como gerir as suas chaves de conta de armazenamento e como a gerá-los.

### <a name="data-plane-security"></a>Plano de segurança dos dados

Nesta secção, abordaremos permissão do acesso aos objetos de dados reais na sua conta de armazenamento, como blobs, ficheiros, filas e tabelas, utilizar assinaturas de acesso partilhado e armazenados políticas de acesso. Abordaremos SA de nível de serviço e SA ao nível da conta. Recomendamos também verá como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo utilizado para HTTPS e como revogar uma assinatura de acesso partilhado sem aguardá-lo a expirar.

## <a name="encryption-in-transit"></a>Encriptação em trânsito

Esta secção explica como proteger dados quando são transferidas ou desaparecer armazenamento do Windows Azure. Iremos falar sobre a utilização recomendada de HTTPS e a encriptação utilizado pelo SMB 3.0 para Azure partilhas de ficheiros. Recomendamos também irá demorar um olhar sobre a encriptação do lado do cliente, que permite-lhe para encriptar os dados antes de serem transferidas para o armazenamento numa aplicação cliente e desencriptar os dados após a transferência armazenamento.

## <a name="encryption-at-rest"></a>Encriptação em repouso

Irá falarmos sobre encriptação de serviço de armazenamento (SSE) e como pode ativá-la para uma conta de armazenamento, que resulta no seu bloco de blobs, blobs de página e acrescentar blobs automaticamente a ser encriptados quando escritas armazenamento do Windows Azure. Vamos também abordar como pode utilizar a encriptação de disco do Azure e explore as diferenças básicas e casos de encriptação do disco versus SSE versus encriptação do lado do cliente. Vamos abordar conformidade FIPS para computadores de Governo dos Estados Unidos brevemente.

- [Guia de segurança de armazenamento Azure](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Reduções de custos

- [Custo de armazenamento](https://azure.microsoft.com/pricing/details/storage/)

- [Calculadora de armazenamento de custos](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limites de armazenamento

- [Limites do serviço de armazenamento](../azure-subscription-service-limits.md#storage-limits)
