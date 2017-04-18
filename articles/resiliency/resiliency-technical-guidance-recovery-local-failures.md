<properties
   pageTitle="Orientação técnica: recuperação de falhas locais no Azure | Microsoft Azure"
   description="Artigo no compreensão e estruturar e são, altamente disponível, aplicações tolerância a falhas, bem como o planeamento de recuperação de falhas focalizada num locais falhas dentro Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Orientação técnica do Azure RDP: recuperação de falhas locais no Azure

Existem dois principais ameaças para disponibilidade da aplicação:

* Falha de dispositivos, tais como unidades e servidores
* O esgotamento dos recursos críticos, tal como cluster em condições de carga de pico

Azure fornece uma combinação de gestão de recursos, elasticidade, balanceamento de carga e criar a partições para ativar elevada disponibilidade nestas circunstâncias. Algumas destas funcionalidades são executadas automaticamente para todos os serviços Azure. No entanto, em alguns casos, o programador da aplicação tem de efetuar algum do trabalho adicional para beneficiar das mesmas.

##<a name="cloud-services"></a>Serviços em nuvem

Azure serviços em nuvem consiste em coleções de uma ou mais funções web ou de trabalho. Uma ou mais ocorrências de uma função podem executar em simultâneo. A configuração determina o número de instâncias. Instâncias de função são monitorizadas e geridas através de um componente denominado o controlador de ferro. O controlador de ferro Deteta e responde a falhas de hardware e software automaticamente.

Todas as instâncias de função é executado no seu próprio máquina virtual (VM) e comunica com o controlador de ferro através de um agente de convidado. O agente de convidado recolhe métricas de recurso e nó, incluindo a utilização, o estado VM, registos de início, utilização de recursos, exceções e condições de falha. O controlador de ferro consultas o agente de convidado a intervalos configuráveis e reinicia o VM se o agente de convidado deixar de responder. Em caso de falha de hardware, o controlador de ferro associado move todas as instâncias de função afetado para um novo nó de hardware e reconfigura a rede para encaminhar tráfego aí.

Para beneficiar destas funcionalidades, os programadores devem Certifique-se de que todas as funções de serviço evitem armazenar estado nas instâncias de função. Em vez disso, todos os dados persistentes devem ser acedidos a partir do armazenamento resistente, como o armazenamento do Windows Azure ou base de dados do SQL Azure. Esta opção permite-quaisquer funções processar pedidos. Também significa que instâncias de função podem aceder para baixo em qualquer altura sem criar inconsistências no estado de breves ou persistente do serviço.

O requisito de registo para armazenar o estado externamente às funções tem várias implicações. Implica, por exemplo, que todas as alterações relacionadas a uma tabela de armazenamento do Windows Azure devem ser alteradas numa única transação de grupo da entidade, se possível. Obviamente, não está sempre é possível efetuar todas as alterações de uma única transação. Tem de tomar cuidado especial para se certificar de que falhas de instância da função não causar problemas quando interromper a operações de execução longa que aparecem em duas ou mais atualizações para o estado do serviço de persistente. Se tentativas de outra função repetir a essa operação, deverá antecipar e processar as maiúsculas/minúsculas onde o trabalho foi parcialmente concluído.

Por exemplo, considere um serviço que a partições de dados através de vários stores. Se uma função de trabalho vai enquanto a mesma é reposicionar um shard, não poderá concluir a mudança do shard. Ou a mudança, poderá ser repetida a partir do seu inicio por uma função de trabalho diferente, potencialmente a causar dados órfãos ou danos nos dados. Para evitar problemas, operações de execução longa tem de ser um ou ambos os seguintes procedimentos:

 * *Idempotent*: forma repetida sem lado efeitos. Para ser idempotent, uma operação de execução longa deverá ter o mesmo efeito independentemente do número de vezes é executado, mesmo quando for interrompida durante a execução.
 * *Incrementada reinício*: conseguir continuar a partir do ponto mais recente de falha. Para ser incrementada reinício, uma operação de execução longa deve ser composto uma sequência de menores atómica operações. Também-deverá registar o progresso resistente armazenamento, para que cada invocação subsequente escolhe ponto onde parou predecessora.

Por fim, todas as operações de execução longa devem ser invocar repetidamente até que teve êxito. Por exemplo, uma operação de aprovisionamento pode ser colocada numa fila Azure e, em seguida, removida da fila por uma função de trabalho apenas quando o mesmo é concluída com êxito. Recolha de lixo poderá ser necessária para limpar dados interrompida operações criar.

###<a name="elasticity"></a>Elasticidade

O número inicial de instâncias em execução para cada função é determinado na configuração de cada função. Os administradores devem inicialmente configurar cada função para executar com duas ou mais ocorrências com base em carga esperada. Mas pode facilmente Dimensionar instâncias de função para cima ou para baixo como a utilização padrões alterar. Pode fazê-lo manualmente no portal do Azure ou pode automatizar o processo ao utilizar o Windows PowerShell, de API do serviço de gestão ou ferramentas de terceiros. Para mais informações, consulte o artigo [como autoscale uma aplicação](../cloud-services/cloud-services-how-to-scale.md).

###<a name="partitioning"></a>Criação de partições

O controlador de ferro Azure utiliza dois tipos de partições:

* Um *Atualizar domínio* é utilizada para atualizar instâncias de função de um serviço em grupos. Azure implementa instâncias do serviço para vários domínios de atualização. Por uma atualização no local, o controlador de ferro traz para baixo de todas as instâncias de no domínio de uma atualização, atualiza-los e, em seguida, reinicia-las antes de passar para o domínio de atualização seguinte. Esta abordagem impede a totalidade do serviço indisponível durante o processo de atualização.
* Um *domínio de falhas* define potenciais pontos de falha do hardware ou de rede. Para qualquer função que tenha mais do que uma instância, o controlador de ferro assegura que as instâncias são distribuídas por vários domínios falhas, para impedir que falhas de hardware isoladas perturbar o serviço. Domínios falhas governem exposição todos os servidor e falhas do cluster.

O [contrato de nível de serviço (SLA) Azure](https://azure.microsoft.com/support/legal/sla/) garante que quando duas ou mais ocorrências de funções web são implementadas falha diferentes e actualizar domínios, irá têm conectividade externa, pelo menos, 99.95% das vezes. Ao contrário de actualização de domínios, não existe nenhuma forma de controlar o número de domínios falhas. Azure atribui domínios falhas e distribui instâncias de função através de lhes automaticamente. At, pelo menos, duas primeiras ocorrências de cada função são colocadas na falha diferentes e actualizar domínios para se certificar de que qualquer função com, pelo menos, duas instâncias irá satisfazem a SLA. Isto é representado no diagrama seguinte.

![Vista simplificada do isolamento de domínio de falhas](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png)

###<a name="load-balancing"></a>Balanceamento de carga

Todo o tráfego de entrada a uma função de web transmite através de um balanceador de carga sem estado, que distribui os pedidos de cliente entre as instâncias de função. Ocorrências individuais função não têm endereços IP públicos e não estiverem endereçáveis da Internet. Funções de Web são sem estado para que possa ser encaminhado qualquer pedido de cliente a nenhuma instância de função. Um evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) é elevado cada 15 segundos. Pode utilizar esta para indicar se a função está pronta para receber tráfego ou se está ocupado e deverá ser reencaminhado terminar a rotação do Balanceador de carga.

##<a name="virtual-machines"></a>Máquinas virtuais

Azure máquinas virtuais difere da plataforma funções em várias aspectos em relação elevada disponibilidade de calcular um serviço (PaaS). Em alguns casos, tem de fazer o trabalho adicional para garantir a disponibilidade de alta.

###<a name="disk-durability"></a>Durabilidade disco

Ao contrário de instâncias de função PaaS, dados armazenados em unidades de máquina virtual são persistentes mesmo quando a máquina virtual é reatribuída. Azure máquinas virtuais utilize discos VM existentes como blobs no armazenamento do Windows Azure. Devido às características disponibilidade do armazenamento do Windows Azure, os dados armazenados em unidades de uma máquina virtual também são altamente disponíveis.

Tenha em atenção que a unidade D (no Windows VMs) é a exceção para esta regra. Unidade D é realmente físico armazenamento no servidor bastidor que aloja a VM e os respetivos dados serão perdidos se a VM é reciclada. Unidade D destina-se apenas armazenamento temporário. No Linux, Azure "normalmente" (mas não sempre) expõe o disco local temporário como /dev/sdb dispositivo de bloco. É frequentemente instalado pelo agente Linux Azure como /mnt/resource ou /mnt pontos de montagem (configuráveis através de /etc/waagent.conf).

###<a name="partitioning"></a>Criação de partições

Azure vierem compreenda as camadas numa aplicação PaaS (função de web e a função de trabalho) e, por conseguinte, pode corretamente distribuí-los em todos os domínios de falhas e atualização. Em contrapartida, as camadas numa infraestrutura como uma aplicação de serviço (IaaS) devem ser definidas manualmente através de conjuntos de disponibilidade. Conjuntos de disponibilidade são necessários para uma SLA em IaaS.

![Disponibilidade de conjuntos de máquinas virtuais Azure](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png)

No diagrama anterior, são atribuídos a camada de serviços de informação Internet (IIS) (que funciona como uma camada de aplicação web) e a camada SQL (que funciona como uma camada de dados) aos conjuntos de disponibilidade diferente. Isto assegura que todas as instâncias de cada camada têm redundância de hardware através da distribuição máquinas virtuais através de domínios falhas e que toda camadas não são tidos durante uma atualização.

###<a name="load-balancing"></a>Balanceamento de carga

Se o VMs devem ter tráfego distribuído pela-las, tem de agrupar VMs num aplicação e carregar o saldo do através de um ponto final TCP ou UDP específico. Para mais informações, consulte o artigo [balanceamento de carga em máquinas virtuais](../virtual-machines/virtual-machines-linux-load-balance.md). Se o VMs recebem entrada a partir de outra origem (por exemplo, um mecanismo colocação), não é necessário um balanceador de carga. O Balanceador de carga utiliza uma verificação de integridade básicas para determinar se o tráfego deve ser enviado para o nó. Também é possível criar o seus próprio sondas para implementar o métricas de estado de funcionamento específicos da aplicação que determinam se a VM deve receber tráfego.

##<a name="storage"></a>Armazenamento

Armazenamento Azure é o serviço de dados resistentes do plano base para Azure. Fornece blob, tabela, fila de espera e armazenamento em disco VM. Utiliza uma combinação de replicação e gestão de recursos para fornecer elevada disponibilidade dentro de uma única Centro de dados. A disponibilidade de armazenamento do Windows Azure SLA garante que pelo menos 99,9% das vezes:

* Corretamente formatados os pedidos para adicionar, atualizar, ler e eliminar dados serão com êxito e corretamente processados.
* Contas de armazenamento terão conectividade ao gateway para a Internet.

###<a name="replication"></a>Replicação

Armazenamento Azure facilita durabilidade de dados mantendo várias cópias de todos os dados em unidades diferentes ao longo subsistemas totalmente independente armazenamento físico dentro de uma região. Dados são replicados modo síncrono e todas as cópias são consolidadas antes da escrita for confirmada. Armazenamento Azure é vivamente consistente, o que significa lê é garantir para refletir o escreve mais recente. Além disso, as cópias dos dados de são automática e continuamente analisadas para detetar e reparar bit podridão, uma ameaça muitas vezes esquecida para a integridade dos dados armazenados.

Beneficiam dos serviços de replicação utilizando armazenamento do Windows Azure. O Programador de serviço não precisa de fazer o trabalho adicional para recuperar a partir de uma falha de local.

###<a name="resource-management"></a>Gestão de recursos

Contas de armazenamento criadas depois de Maio de 2014, podem crescer e até 500 TB (o número máximo de anterior era 200 TB). Se for necessário espaço adicional, aplicações devem ser concebidas para utilizar várias contas de armazenamento.

###<a name="virtual-machine-disks"></a>Discos máquina virtual

Disco uma máquina virtual é armazenado como um blob de página no armazenamento do Windows Azure, atribuindo-lhe as mesmas propriedades durabilidade e escalabilidade como armazenamento de Blobs. Este design torna os dados no disco uma máquina virtual persistente, mesmo se o servidor a executar a VM falhar e a VM tem de ser reiniciada noutro servidor.

##<a name="database"></a>Base de dados

###<a name="sql-database"></a>Base de dados SQL

Base de dados SQL Azure fornece da base de dados como um serviço. -Permite que as aplicações rapidamente aprovisionar, inserir dados para e bases de dados relacionais consulta. Fornece muitas das funcionalidades do SQL Server e funcionalidade, familiares enquanto abstracting a carga de hardware, configuração, corrigir e RDP.

>[AZURE.NOTE] Base de dados SQL Azure não fornece paridade uma funcionalidade com o SQL Server. Tem de se destina satisfazer um conjunto diferente de requisitos – que tem exclusivamente adequados às aplicações na nuvem (flexível escala, base de dados como um serviço para reduzir os custos de manutenção e assim sucessivamente). Para obter mais informações, consulte o artigo [Escolha uma opção do SQL Server nuvem: base de dados SQL Azure (PaaS) ou do SQL Server no Azure VMs (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

####<a name="replication"></a>Replicação

Base de dados SQL Azure fornece RDP incorporado para a falha do nível de nó. Todas as gravações para uma base de dados são automaticamente replicadas para nós de fundo de duas ou mais através de uma técnica de consolidação de quórum. (A página principal e pelo menos uma secundário tem de confirmar que a atividade destina-se para o registo de transações antes da transação é considerada efetuada com êxito e devolve.) No caso de falha de nó, a base de dados automaticamente falha sobre a uma das réplicas secundárias. Isto faz com que uma interrupção de ligação breves para aplicações de cliente. Por este motivo, todos os clientes de base de dados do Azure SQL tem de implementar alguma forma de processamento de ligação breves. Para mais informações, consulte o artigo [Repetir orientações específicas do serviço](../best-practices-retry-service-specific.md).

####<a name="resource-management"></a>Gestão de recursos

Cada base de dados, quando criado, está configurado com um limite de tamanho superior. O tamanho máximo atualmente disponível é 1 TB (tamanho de limites variam com base no seu camada de serviço, consulte o artigo [camadas de serviços e os níveis de desempenho de bases de dados do SQL Azure](../sql-database/sql-database-resource-limits.md#service-tiers-and-performance-levels). Quando uma base de dados de acertos o limite de tamanho superior,-rejeita comandos adicionais INSERT ou UPDATE. (Consultar e eliminação de dados são continua a ser possível).

Dentro de uma base de dados, base de dados do SQL Azure utiliza um ferro para gerir os recursos. No entanto, em vez de um controlador de ferro, utiliza uma topologia em simultâneo para detetar falhas. Cada réplica num cluster tem dois vizinhos e é responsável por detetar quando acederem para baixo. Quando uma réplica vai para baixo, o respetivos vizinhos accionam um agente do reconfiguração voltar a criá-lo noutro computador. Optimização do motor é fornecido para garantir que um servidor lógico não utilize recursos demasiadas num computador ou exceder limites física a máquina.

###<a name="elasticity"></a>Elasticidade

Se a aplicação exigir mais do que o limite de base de dados 1 TB, tem de implementar uma abordagem de escala de saída. Dimensionar saída com a base de dados do SQL Azure por manualmente criar partições, também conhecido como sharding, dados em várias bases de dados do SQL. Esta abordagem de escala out fornece a oportunidade de atingir o crescimento de custo quase linear com escala. Crescimento flexível ou capacidade a pedido pode crescer com custos elementares, conforme necessário porque bases de dados são faturadas com base em média tamanho real utilizado por dia, não baseado no tamanho máximo possível.

##<a name="sql-server-on-virtual-machines"></a>SQL Server em máquinas virtuais

Instalando (versão 2014 ou posterior) do SQL Server em máquinas virtuais do Azure, pode tirar partido das funcionalidades de disponibilidade tradicional do SQL Server. Estas funcionalidades incluem grupos de Disponibilidade AlwaysOn e espelhando as bases de dados. Note que Azure VMs, armazenamento e redes tem características operacionais diferentes de no local, não foi virtualizado infraestrutura de TI. Uma implementação com êxito de uma recuperação de alta disponibilidade/falhas solução (HA/DR) SQL Server no Azure requer que compreender estes diferenças e estruturar a solução para acomodá-los.

###<a name="high-availability-nodes-in-an-availability-set"></a>Disponibilidade de alta nós num conjunto de disponibilidade

Quando implementar uma solução de disponibilidade como sendo de alta no Azure, pode utilizar a disponibilidade definir no Azure para colocar os nós de alta disponibilidade domínios falhas separadas e actualizar domínios. Para limpar, o conjunto de disponibilidade é um conceito Azure. É aconselhável que deve seguir para se certificar de que as bases de dados estão facto altamente disponíveis, se estiver a utilizar grupos de Disponibilidade AlwaysOn espelhando as bases de dados de outra coisa. Se não siga esta prática recomendada, poderá ser em falso pressuposto de que o seu sistema está altamente disponível. Mas na realidade, os nós de todas as falha em simultâneo porque foram efetuadas seja colocado no mesmo domínio falhas na região Azure.

Esta recomendação não é o envio de registo, conforme aplicável. Como uma funcionalidade de recuperação de falhas, deverá Certifique-se de que os servidores de estão a ser executada em separado regiões Azure. Por definição, estas regiões são domínios falhas em separado.

Para Azure nuvem serviços VMs implementado através do portal do clássico estar no mesmo conjunto de disponibilidade, tem de ser implementar no serviço de nuvem do mesmo. VMs implementados através do Azure Gestor de recursos (o portal atual) não têm esta limitação. Para o portal clássica implementado VMs num serviço de nuvem Azure, apenas os nós no serviço na nuvem mesmo podem participar no mesmo conjunto de disponibilidade. Além disso, deve ser o VMs de serviços na nuvem na mesma rede virtual para se certificar de que mantêm os respetivos IPs do mesmo após a reparação de serviço. Isto evita interrupções de atualização DNS.

###<a name="azure-only-high-availability-solutions"></a>Só Azure: soluções de elevada disponibilidade

Pode ter uma solução de alta disponibilidade para as bases de dados do SQL Server no Azure ao utilizar grupos de Disponibilidade AlwaysOn ou espelhando as bases de dados.

O diagrama seguinte demonstra a arquitetura de grupos de Disponibilidade AlwaysOn em execução no máquinas virtuais do Azure. Este diagrama foi tirado do artigo aprofundado sobre este assunto, [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Grupos de Disponibilidade AlwaysOn no Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

Pode também automaticamente aprovisionar um grupos de Disponibilidade AlwaysOn implementação fim-a-ponto no Azure VMs utilizando o modelo de AlwaysOn no portal do Azure. Para mais informações, consulte o artigo [SQL Server AlwaysOn perguntar se na Galeria de Portal do Microsoft Azure](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

O diagrama seguinte demonstra a utilização da base de dados espelhando as em máquinas virtuais do Azure. -Lo também foi tirado do tópico aprofundado [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Base de dados espelhando as no Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

>[AZURE.NOTE] Ambas as arquitecturas necessitam de um controlador de domínio. No entanto, com espelhando as bases de dados, é possível utilizar os certificados de servidor para eliminar a necessidade de um controlador de domínio.

##<a name="other-azure-platform-services"></a>Outros serviços de plataforma Azure

As aplicações que foram criadas no Azure beneficiar de capacidades de plataforma para recuperar a partir de falhas locais. Em alguns casos, pode tomar ações específicas para aumentar a disponibilidade para o seu cenário específico.

###<a name="service-bus"></a>Serviço Bus

A atenuar contra uma falha de temporária do Azure Service Bus, considere criar uma fila resistente do lado do cliente. Esta opção utiliza temporariamente um dispositivo de armazenamento alternativo, local para armazenar as mensagens que não podem ser adicionadas para a fila Bus de serviço. A aplicação pode decidir como lidar com as mensagens temporariamente armazenadas depois do serviço é restaurado. Para mais informações, consulte o artigo [melhores práticas para melhorias de desempenho utilizando o serviço Bus controlada mensagens](../service-bus-messaging/service-bus-performance-improvements.md) e [Bus de serviço (recuperação de falhas)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

###<a name="hdinsight"></a>HDInsight

Os dados que está associado ao Azure HDInsight são armazenados por predefinição no armazenamento de Blobs do Azure. Armazenamento Azure Especifica as propriedades de armazenamento de BLOBs alta disponibilidade e durabilidade. O processamento de vários nós associada Hadoop MapReduce tarefas ocorre num breves distribuído ficheiro Hadoop sistema (HDFS) que está aprovisionado quando necessita de HDInsight-lo. Resultados de uma tarefa MapReduce também são armazenados por predefinição no armazenamento de Blobs do Azure, para que os dados processados dure e permanecem altamente disponíveis depois do cluster Hadoop será descontinuado. Para mais informações, consulte o artigo [HDInsight (recuperação de falhas)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

##<a name="checklists-for-local-failures"></a>Listas de verificação para falhas locais

###<a name="cloud-services"></a>Serviços em nuvem

  1. Reveja a secção serviços em nuvem deste documento.
  2. Configure, pelo menos, duas instâncias para cada função.
  3. Manter o estado no armazenamento resistente, não no instâncias de função.
  4. Processe corretamente o evento de StatusCheck.
  5. Molde alterações relacionadas com as transações sempre que possível.
  6. Verifique se as tarefas da função trabalhador estão idempotent e reinício.
  7. Continue a invocar operações até que teve êxito.
  8. Considere autoscaling estratégias.

###<a name="virtual-machines"></a>Máquinas virtuais

  1. Reveja a secção máquinas virtuais deste documento.
  2. Não utilize unidade D para armazenamento persistente.
  3. Agrupar máquinas de uma camada de serviço para um conjunto de disponibilidade.
  4. Configure balanceamento de carga e sondas opcionais.

###<a name="storage"></a>Armazenamento

  1. Reveja a secção de armazenamento deste documento.
  2. Utilize várias contas de armazenamento quando dados ou a largura de banda excede quotas.

###<a name="sql-database"></a>Base de dados SQL

  1. Reveja a secção de base de dados SQL deste documento.
  2. Implemente uma política de repetir para processar erros breves.
  3. Utilize a partições/sharding como uma estratégia de escala de saída.

###<a name="sql-server-on-virtual-machines"></a>SQL Server em máquinas virtuais

  1. Reveja o SQL Server em máquinas virtuais secção deste documento.
  2. Siga as recomendações para máquinas virtuais anterior.
  3. Utilize funcionalidades de elevada disponibilidade do SQL Server, tal como AlwaysOn.

###<a name="service-bus"></a>Serviço Bus

  1. Reveja a secção serviço Bus deste documento.
  2. Considere criar uma fila do lado do cliente resistente como uma cópia de segurança.

###<a name="hdinsight"></a>HDInsight

  1. Reveja a secção HDInsight deste documento.
  2. Não disponibilidade adicionais são passos necessários para falhas locais.

##<a name="next-steps"></a>Próximos passos

Este artigo faz parte de uma série com o foco nas [orientações técnico RDP Azure](./resiliency-technical-guidance.md). O seguinte artigo nesta série é [recuperação a partir de uma interrupção de serviço de toda a região](./resiliency-technical-guidance-recovery-loss-azure-region.md).
