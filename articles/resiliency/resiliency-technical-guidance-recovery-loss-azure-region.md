<properties
   pageTitle="RDP para a recuperação de perda de orientação técnica do Azure região | Microsoft Azure"
   description="Artigo no compreender e conceber aplicações tolerância a falhas de falhas e são, altamente disponível, bem como o planeamento de recuperação de falhas"
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

#<a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Orientação técnica do Azure RDP: recuperação a partir de uma interrupção de serviço de toda a região

Azure é dividido física e logicamente unidades denominadas regiões. Uma região é composta por uma ou mais centros de dados na proximidade. No momento deste escrita, Azure tem vinte e quatro regiões do mundo.

Em circunstâncias raras, é possível que instalações numa região toda podem tornar-se inacessível, por exemplo devido a falhas de rede. Ou instalações podem perder-completamente, por exemplo devido a uma falhas natural. Esta secção explica as funcionalidades do Azure para criação de aplicações que são distribuídas regiões. Essa distribuição ajuda para minimizar a possibilidade de que uma falha numa região pode afetar outras regiões.

##<a name="cloud-services"></a>Serviços em nuvem

###<a name="resource-management"></a>Gestão de recursos

Pode distribuir cluster instâncias entre as regiões ao criar um serviço na nuvem separada em cada região de destino e, em seguida, publicar o pacote de implementação para cada serviço na nuvem. No entanto, tenha em atenção que a distribuição tráfego entre os serviços em nuvem de diferentes regiões tem de ser implementada pelo programador da aplicação ou com um serviço de gestão de tráfego.

Para determinar o número de ocorrências de função reserva para implementar com antecedência para a recuperação de falhas é um aspecto importante de planeamento de capacidade. Está a ter uma implementação secundária à escala total garante que capacidade já está disponível quando for necessário; No entanto, esta forma eficaz é duplicado o custo. É um padrão comuns de ter uma pequena, secundária implementação, basta grande o suficiente para executar serviços críticos. Esta implementação secundária pequenas é uma boa ideia, tanto para reservar capacidade e para testar a configuração do ambiente secundário.

>[AZURE.NOTE]A quota de subscrição não é uma garantia capacidade. A quota é simplesmente um limite de crédito. Para garantir a capacidade, tem de ser definido o número necessário de funções no modelo de serviço e as funções tem de ser implementadas.

###<a name="load-balancing"></a>Balanceamento de carga

Para carregar o saldo tráfego através de regiões requer uma solução de gestão de tráfego. Azure fornece [Azure tráfego Gestor](https://azure.microsoft.com/services/traffic-manager/). Também pode tirar partido dos serviços de terceiros que fornecem as capacidades da gestão de tráfego semelhante.

###<a name="strategies"></a>Estratégias

Muitas estratégias alternativas estão disponíveis para a implementação cluster distribuído ao longo de regiões. Estes devem ser adaptados para os requisitos específicos empresariais e as circunstâncias da aplicação. De alto nível, as abordagens podem ser divididas em categorias seguintes:

  * __Implementar novamente no falhas__: nesta abordagem a aplicação novamente implementada de raiz no momento da falhas. Isto é adequado para as aplicações que não sejam críticas que não exigem uma hora de recuperação garantia.

  * __Reserva quente (ativo/passiva)__: é criado um serviço secundário alojado numa região alternativa e funções são implementadas para garantir a capacidade de mínima; No entanto, as funções não recebem o tráfego de produção. Esta abordagem é útil para as aplicações que não foram concebidas para distribuir o tráfego entre regiões.

  * __Reserva quente (ativo/activo)__: A aplicação foi concebida para receber carga de produção em várias regiões. Serviços na nuvem em cada região podem estar configurados para capacidade mais elevada que necessários para fins de recuperação de falhas. Em alternativa, poderão dimensionar os serviços em nuvem iniciativas conforme necessário, no momento de um falhas e activação pós-falha. Esta abordagem requer investimento substancial na estrutura da aplicação, mas tiver benefícios significativos relacionados. Estas incluem mínimo e garantia o tempo de recuperação, testes de todas as localizações de recuperação e a utilização eficiente da capacidade contínuo.

Um debate completo da estrutura distribuído está fora do âmbito deste documento. Para obter mais informações, consulte o artigo [recuperação de falhas e elevada disponibilidade para aplicações do Azure](https://aka.ms/drtechguide).

##<a name="virtual-machines"></a>Máquinas virtuais

Recuperação de infraestrutura como um máquinas virtuais de serviço (IaaS) (VMs) é semelhante a plataforma, tal como um serviço (PaaS) calcular recuperação em muitos aspectos. Existem diferenças importantes, no entanto, que uma VM IaaS consiste na VM e o disco VM.

  * __Utilizar Azure cópias de segurança para criar cópias de segurança de região cruzada que são aplicação consistente__.
  [Cópia de segurança do Azure](https://azure.microsoft.com/services/backup/) permite que os clientes criar cópias de segurança consistentes aplicação por vários discos VM e suportar a replicação de cópias de segurança ao longo de regiões. Pode fazê-lo ao selecionar a replicação geo cofre cópia de segurança no momento da criação. Tenha em atenção que a replicação da cópia de segurança cofre tem de ser configurado no momento da criação. Não é possível definir mais tarde. Se uma região for perdida, Microsoft irá tornar as cópias de segurança disponível aos clientes. Os clientes poderão restaurar a qualquer uma das suas pontos de restauro configurado.

  * __Separar o disco de dados a partir do disco do sistema operativo__. Uma consideração importante para IaaS VMs é que não é possível alterar o disco do sistema operativo sem voltar a criar a VM. Não é um problema se a sua estratégia de recuperação é implementá após falhas. No entanto, poderá um problema se estiver a utilizar a abordagem quente reserva para reservar capacidade. Para implementar este processo corretamente, tem de ter o disco de sistema operativo correto implementado ambas as localizações principais e secundárias e os dados da aplicação devem ser armazenados numa unidade separada. Se possível, utilize uma configuração do sistema operativo padrão que pode ser fornecida em ambas as localizações. Após uma falha na ligação, em seguida, tem de anexar a unidade de dados ao seu VMs IaaS existente no Centro de dados secundário. Utilize AzCopy para copiar instantâneos dos discos dados para um site remoto.

  * __Tenha em atenção os potenciais problemas de consistência após geo com falha de vários VM discos__. VM discos são implementados como blobs do Azure armazenamento e de ter a mesmo característica geo replicação. A menos que a [Cópia de segurança do Azure](https://azure.microsoft.com/services/backup/) for utilizado, não existem sem garantias de consistência através de discos, porque geo replicação é assíncrona e replica de forma independente. Discos VM individuais estão garante numa falha consistentes Estado após geo com falha, mas não está consistente ao longo de discos. Isto pode causar problemas em alguns casos (por exemplo, no caso de repartição do disco).

##<a name="storage"></a>Armazenamento

###<a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Recuperação utilizando Geo redundantes armazenamento de BLOBs, tabela, fila de espera e armazenamento em disco VM

No Azure, blobs, tabelas, filas e VM discos são todas as geo replicada por predefinição. Isto é referido como Geo redundantes armazenamento (GRS). GRS replica os dados de armazenamento para um emparelhado Centro de dados centenas de milhas centímetros dentro de uma região geográfica específica. GRS foi concebido para fornecer durabilidade adicional caso exista uma falhas principais do Centro de dados. Controlos da Microsoft quando ocorre activação pós-falha e mudança de recurso está limitado a catástrofes principais em que a localização principal original é considerada irrecuperável num razoável período de tempo. Em alguns cenários, este pode ser vários dias. Dados são normalmente replicados dentro de alguns minutos, apesar do intervalo de sincronização não está ainda abrangido por um contrato de nível de serviço.

Trabalho um geo-falha na ligação, haverá não altera a forma como é acedida a conta (não irá alterar a chave de URL e a conta). A conta de armazenamento, no entanto, será numa região diferente após activação pós-falha. Isto pode afetar as aplicações que requerem afinidade regional com a sua conta de armazenamento. Mesmo para serviços e aplicações que não necessitam de uma conta de armazenamento no Centro de dados a mesma, o Centro de dados em várias latência e taxas de largura de banda poderão apelativos motivos para mover o tráfego para a região de activação pós-falha temporariamente. Isto poderia factor para uma estratégia de recuperação de falhas global.

Para além de falha na ligação automática fornecida pela GRS, Azure tenha introduzido um serviço que fornece-lhe acesso de leitura para a cópia dos seus dados na localização de armazenamento secundário. Esta opção é denominada armazenamento Geo redundantes acesso de leitura (GRS RT).

Para mais informações sobre o armazenamento GRS e GRS RT, consulte o artigo [replicação de armazenamento do Windows Azure](../storage/storage-redundancy.md).

###<a name="geo-replication-region-mappings"></a>Mapeamentos de região de replicação Geo:

É importante saber onde os dados estiverem geo replicada, para saber onde implementar as outras ocorrências dos seus dados que requerem afinidade regional com o seu armazenamento. A tabela seguinte mostra os pares de localização principais e secundários:

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing"></a>Replicação geo preços:

Replicação GEO é incluída no preços atual do armazenamento do Windows Azure. Esta opção é denominada armazenamento Geo redundantes (GRS). Se não pretender que os seus dados replicada geo pode desativar geo replicação para a sua conta. Esta opção é denominada localmente armazenamento redundante e é cobrada de um preço com desconto em comparação comparado GRS.

###<a name="determining-if-a-geo-failover-has-occurred"></a>Para determinar se uma geo com falha ocorreu

Se ocorrer uma geo com falha, este será registado ao [Dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/). Aplicações podem implementar um meios automatizados de detetar esta, no entanto, através da região de geo para a sua conta de armazenamento de monitorização. Isto pode ser utilizado para acionar outras operações de recuperação, tal como recursos de cluster na região geo onde o seu armazenamento movido para a ativação. Pode executar uma consulta para que esta partir da gestão de serviço API, utilizando [Obter propriedades da conta de armazenamento](https://msdn.microsoft.com/library/ee460802.aspx). As propriedades relevantes são:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>Discos VM e geo com falha

Conforme descrito na secção em discos VM, não existem sem garantias para consistência dos dados ao longo de discos VM após uma activação pós-falha. Para assegurar que regularidade de cópias de segurança, um produto cópia de segurança, tal como Gestor de proteção de dados deverá ser utilizado para fazer cópia de segurança e restaurar os dados da aplicação.

##<a name="database"></a>Base de dados

###<a name="sql-database"></a>Base de dados SQL

Base de dados SQL Azure fornece dois tipos de recuperação: Geo restauro e Geo-a replicação do Active.

####<a name="geo-restore"></a>Restaurar geo

[Restaurar geo](../sql-database/sql-database-recovery-using-backups.md#geo-restore) também está disponível com bases de dados Basic, padrão e Premium. Fornece a opção de recuperação predefinida quando a base de dados está disponível devido a um incidente na região onde a base de dados está alojado. Semelhante ao ponto-In-Time restaurar, Geo restaurar depende de cópias de segurança da base de dados no armazenamento Azure geo redundantes. Restaura a partir de replicadas geo cópia de segurança e, por isso é flexível para as falhas de armazenamento na região principal. Para obter mais detalhes, consulte o artigo [Restaurar uma base de dados do SQL Azure ou activação pós-falha para um secundário](../sql-database/sql-database-disaster-recovery.md).

####<a name="active-geo-replication"></a>Replicação de Geo ativa

[Replicação do Geo Active](../sql-database/sql-database-geo-replication-overview.md) está disponível para todas as camadas de base de dados. Destina-se para as aplicações que têm requisitos de recuperação agressivos mais do que pode oferecer Geo restaurar. Replicação do Geo Active pode criar até quatro secundários legíveis em servidores no regiões diferentes. Pode iniciar activação pós-falha a qualquer uma dos secundários. Além disso, Geo-a replicação do Active pode ser utilizada para suportar os cenários de atualização ou mudança de aplicação, bem como balanceamento de carga para só de leitura das cargas de trabalho. Para obter detalhes, consulte o artigo [Configurar a replicação Geo](../sql-database/sql-database-geo-replication-portal.md) e a [Falha ao longo à base de dados secundária](../sql-database/sql-database-geo-replication-failover-portal.md). Referir-se a [estrutura de um pedido de recuperação de falhas de nuvem utilizando Geo-replicação do Active na base de dados SQL](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [actualizações de ficha técnica em gestão de aplicações na nuvem utilizando da base de dados ativo Geo-a replicação SQL](../sql-database/sql-database-manage-application-rolling-upgrade.md) para obter detalhes sobre a estrutura e implementar aplicações e atualização de aplicações sem tempo de inatividade.

###<a name="sql-server-on-virtual-machines"></a>SQL Server em máquinas virtuais

Várias opções estão disponíveis para recuperação e elevada disponibilidade para SQL Server 2012 (e posterior) em execução no máquinas virtuais do Azure. Para mais informações, consulte o artigo [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##<a name="other-azure-platform-services"></a>Outros serviços de plataforma Azure

Ao tentar executar o serviço de nuvem em várias regiões Azure, tem de considerar as implicações para cada um dos seus dependências. Nas secções seguintes, as orientações específico do serviço assume que tem de utilizar o mesmo serviço Azure um centro de dados do Azure alternativo. Isso envolve configuração e tarefas de replicação de dados.

>[AZURE.NOTE]Em alguns casos, estes passos podem ajudar a atenuar uma falha de específico do serviço em vez de um evento do Centro de dados inteira. Da perspetiva de aplicação, poderá ser uma falha de específico do serviço tal como limitar e seria necessário migrar temporariamente o serviço para uma região Azure alternativa.

###<a name="service-bus"></a>Serviço Bus

Azure Service Bus utiliza um espaço de nomes exclusivo que não aparecem em regiões Azure. Por isso, ao primeiro requisito é configurar os espaços de nomes do serviço necessário bus na região alternativo. No entanto, também existem considerações para a durabilidade das mensagens em fila de espera. Existem várias estratégias para a replicação de mensagens entre as regiões Azure. Para obter detalhes sobre estas estratégias de replicação e outras estratégias de recuperação de falhas, consulte o artigo [melhores práticas para aplicações isolamento contra Bus de serviço de corrente e catástrofes](../service-bus-messaging/service-bus-outages-disasters.md). Para outras considerações de disponibilidade, consulte o artigo [Bus de serviço (disponibilidade)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="app-service"></a>Aplicação de serviço

Para migrar uma aplicação do serviço de aplicação do Azure, tal como Web Apps ou aplicações móveis, para uma região Azure secundária, tem de ter uma cópia de segurança do Web site disponível para publicação. Se a indisponibilidade não envolvem o Centro de dados Azure inteiro, poderá possível utilizar o FTP para transferir uma cópia de segurança recente conteúdo do site. Em seguida, crie uma nova aplicação na região alternativo, a menos que anteriormente efetuou esta opção para reservar capacidade. Publicar o site à região de novo e faça as alterações de configuração necessárias. Estas alterações poderá incluir cadeias de ligação de base de dados ou outras definições específicas do região. Se for necessário, adicione um certificado SSL o site e alterar o registo CNAME de DNS para que o nome de domínio personalizado aponta para o URL da aplicação Web Azure redeployed.

###<a name="hdinsight"></a>HDInsight

Os dados associados HDInsight são armazenados por predefinição no armazenamento de Blobs do Azure. HDInsight requer que um cluster de Hadoop processar tarefas MapReduce tem de estar cocriação localizado na mesma região com a conta de armazenamento que contém os dados a ser analisados. Desde que utilize a funcionalidade de replicação geo disponível para o armazenamento do Windows Azure, pode aceder aos dados na região secundário onde os dados foram replicados se por algum motivo a região primária já não se encontra disponível. Pode criar um novo cluster de Hadoop na região onde os dados tem sido replicados e continuar a processá-la. Para outras considerações de disponibilidade, consulte o artigo [HDInsight (disponibilidade)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="sql-reporting"></a>Relatório de SQL

Neste momento, recuperar a perda de uma região Azure requer várias instâncias de diferentes regiões Azure SQL relatórios. Os mesmos dados deverão aceder a estas instâncias de elaboração de relatórios do SQL e esses dados devem ter as suas próprias recuperação planear eventualidade de um. Também pode manter cópias de segurança externas do ficheiro RDL para cada relatório.

###<a name="media-services"></a>Serviços de multimédia

Azure dos serviços de multimédia tem uma abordagem de recuperação diferentes para codificação e transmissão. Normalmente, transmissão é mais crítica durante uma falha de regional. Para preparar para esta situação, deve ter uma conta dos serviços de multimédia em dois diferentes regiões Azure. O conteúdo codificado deverá estar localizado em ambas as regiões. Durante uma falha, pode redirecionar o tráfego de transmissão à região de alternativo. Codificação pode ser realizado na região qualquer Azure. Se a codificação é sensível ao tempo, por exemplo durante o processamento de evento em directo, tem de ser preparado para submeter tarefas para um centro de dados alternada durante falhas.

###<a name="virtual-network"></a>Rede virtual

Ficheiros de configuração de fornecem a forma mais rápida para configurar uma rede virtual numa região Azure alternativa. Depois de configurar a rede virtual na região Azure principal, [exportar as definições de rede virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) para a rede atual para um ficheiro de configuração de rede. Em caso de uma falha na região principal, [restaurar a rede virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) a partir do ficheiro de configuração armazenada. Em seguida, configure outros serviços em nuvem, máquinas virtuais ou definições de publicação em local para trabalhar com a nova rede virtual.

##<a name="checklists-for-disaster-recovery"></a>Listas de verificação para recuperação de falhas

##<a name="cloud-services-checklist"></a>Lista de verificação de serviços de nuvem

  1. Reveja a secção serviços em nuvem deste documento.
  2. Crie uma estratégia de recuperação de falhas de publicação em região.
  3. Compreenda compromissos no reserva de capacidade em regiões alternativos.
  4. Utilize ferramentas de encaminhar tráfego, tal como o Azure tráfego Manager.

##<a name="virtual-machines-checklist"></a>Lista de verificação máquinas virtuais

  1. Reveja a secção máquinas virtuais deste documento.
  2. Utilize o [Azure cópia de segurança](https://azure.microsoft.com/services/backup/) para criar cópias de segurança consistentes aplicação entre regiões.

##<a name="storage-checklist"></a>Lista de verificação de armazenamento

  1. Reveja a secção de armazenamento deste documento.
  2. Não desative o geo-replicação de recursos de armazenamento.
  3. Compreenda a região alternativo para a replicação geo trabalho activação pós-falha.
  4. Crie personalizadas estratégias de cópia de segurança para estratégias activação pós-falha controlado pelo utilizador.

##<a name="sql-database-checklist"></a>Lista de verificação de base de dados SQL

  1. Reveja a secção de base de dados SQL deste documento.
  2. Utilize [Geo restaurar](../sql-database/sql-database-recovery-using-backups.md#geo-restore) ou [Geo replicação](../sql-database/sql-database-geo-replication-overview.md) conforme adequado.

##<a name="sql-server-on-virtual-machines-checklist"></a>SQL Server em máquinas virtuais lista de verificação

  1. Reveja o SQL Server em máquinas virtuais secção deste documento.
  2. Utilize grupos de Disponibilidade AlwaysOn cruzada região ou espelhando as bases de dados.
  3. Em alternativa, utilize cópia de segurança e restaurar para blob armazenamento.

##<a name="service-bus-checklist"></a>Lista de verificação Bus de serviço

  1. Reveja a secção serviço Bus deste documento.
  2. Configure um espaço de nomes de serviço Bus numa região alternativa.
  3. Considere estratégias de replicação personalizada para mensagens entre as regiões.

##<a name="app-service-checklist"></a>Lista de verificação da aplicação de serviço

  1. Reveja a secção de aplicação de serviço deste documento.
  2. Manter cópias de segurança do site fora da região principal.
  3. Se for parcial indisponibilidade, tentativa obter o site atual com FTP.
  4. Plano para implementar o site novo ou já existente site numa região alternativa.
  5. Planear alterações na configuração para aplicação e registos CNAME de DNS.

##<a name="hdinsight-checklist"></a>Lista de verificação HDInsight

  1. Reveja a secção HDInsight deste documento.
  2. Crie um novo cluster de Hadoop na região com dados replicados.

##<a name="sql-reporting-checklist"></a>Lista de verificação de elaboração de relatórios do SQL

  1. Reveja a secção relatórios SQL deste documento.
  2. Manter uma instância de elaboração de relatórios do SQL alternativa numa região diferente.
  3. Manter um plano para criar uma réplica de destino para essa região em separado.

##<a name="media-services-checklist"></a>Lista de verificação dos serviços de multimédia

  1. Reveja a secção dos serviços de multimédia deste documento.
  2. Crie uma conta dos serviços de multimédia numa região alternativa.
  3. Codificar o mesmo conteúdo de ambas as regiões para suportar a transmissão activação pós-falha.
  4. Submeta codificação tarefas para uma região alternativa trabalho uma interrupção de serviço.

##<a name="virtual-network-checklist"></a>Lista de verificação de rede virtual

  1. Reveja a secção de rede Virtual deste documento.
  2. Utilizar exportados as definições de rede virtual voltar a criá-lo noutra região.

##<a name="next-steps"></a>Próximos passos

Este artigo faz parte de uma série com o foco nas [orientações técnico RDP Azure](./resiliency-technical-guidance.md). O seguinte artigo nesta série foca-se em [recuperação a partir de um centro de dados no local para Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).
