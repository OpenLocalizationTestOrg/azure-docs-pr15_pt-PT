<properties
   pageTitle="Recuperação de falhas para aplicações do Azure | Microsoft Azure"
   description="Descrição geral técnica e informações detalhadas acerca da criação de aplicações para o recuperação de falhas no Microsoft Azure."
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

#<a name="disaster-recovery-for-applications-built-on-microsoft-azure"></a>Recuperação de falhas para aplicações criadas no Microsoft Azure

Considerando que é elevada disponibilidade sobre a gestão de falha temporária, recuperação de falhas (DR) é de cerca de grave perda de funcionalidade da aplicação. Por exemplo, considere o cenário em que uma região vai para baixo. Neste caso, tem de ter um plano para executar a sua aplicação ou aceder aos dados fora da região Azure. Execução deste plano envolve pessoas, processos e as aplicações de suporte que permitem o sistema de função. Os proprietários de negócio e tecnologia quem definem o modo de operacional do sistema para um falhas também determinam o nível de funcionalidade para o serviço durante uma falhas. O nível de funcionalidade pode demorar alguns formulários: indisponível totalmente, parcialmente disponíveis (degradado funcionalidade ou atrasada processamento), ou completamente disponível.

##<a name="azure-disaster-recovery-features"></a>Funcionalidades de recuperação de falhas Azure

Tal como acontece com considerações de disponibilidade, Azure tem [orientações técnicas RDP](./resiliency-technical-guidance.md) concebida para recuperação de falhas de suporte. Também existe uma relação entre algumas das funcionalidades de disponibilidade do Azure e falhas a recuperação. Por exemplo, a gestão de funções em vários domínios falhas aumenta a disponibilidade de uma aplicação. Sem essa gestão, uma falha do hardware não processada seria tornam-se num cenário de "falhas". Para que a aplicação correta das funcionalidades de disponibilidade e estratégias é uma parte importante de verificação linguística falhas a aplicação. No entanto, este artigo vai para além dos problemas de disponibilidade geral para eventos de falhas grave (e mais raros).

##<a name="multiple-datacenter-regions"></a>Várias regiões do Centro de dados

Azure mantém centros de dados em muitas regiões do mundo. Este infraestrutura suporta vários cenários de recuperação de falhas, tal como a fornecidos pelo sistema geo-replicação de armazenamento do Windows Azure para regiões secundários. Também significa que pode facilmente e pouco dispendiosa implementar um serviço na nuvem para várias localizações em todo o mundo. Compare esta com o custo e dificuldade de executar o seus próprio centros de dados em várias regiões. Implementar e serviços de dados em várias regiões ajuda a proteger a aplicação a partir de corrente principais numa única região.

##<a name="azure-traffic-manager"></a>Gestor de tráfego Azure

Quando ocorre uma falha de específicos da região, tem de redirecionar o tráfego para implementações no outro região ou serviços. Pode fazer este encaminhamento manualmente, mas é mais eficiente para utilizar um processo automatizado. Gestor de tráfego Azure foi concebido para esta tarefa. Pode utilizá-lo para gerir automaticamente a activação pós-falha de tráfego de utilizador para outro região no caso de falha na região principal. Uma vez que a gestão de tráfego é uma parte importante da estratégia global, é importante compreender as noções básicas do Gestor de tráfego.

No diagrama seguinte, os utilizadores ligar a um URL que tiver especificado para o Gestor de tráfego (__http://myATMURL.trafficmanager.net__) e que resumos os URLs de site real (__http://app1URL.cloudapp.net__ e __http://app2URL.cloudapp.net__). Com base no como configurar os critérios de quando para encaminhar utilizadores, os utilizadores vão ser direcionados para o site correto real quando a política dita. As opções de política são round robin, desempenho ou activação pós-falha. Por razões de neste artigo, iremos serão preocupem com a opção de activação pós-falha.

![Encaminhamento através do Gestor de tráfego Azure](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Quando estiver a configurar o Gestor de tráfego, fornece um novo prefixo de tráfego de Gestor de DNS. Este é o prefixo do URL que irá indicar aos seus utilizadores para aceder ao serviço. Gestor de tráfego abstracts agora um nível para cima e não ao nível de região de balanceamento de carga. Conjunto mapas do tráfego de Gestor de DNS para um CNAME para todas as implementações que gere o-lo.

Dentro do Gestor de tráfego, pode especificar a prioridade de implementações do que os utilizadores irão ser encaminhados para quando ocorre falha. Gestor de tráfego monitoriza os pontos finais da implementações e notas quando a implementação primária falha. Na linha falha, Gestor de tráfego analisa a lista de implementações prioridades e encaminha os utilizadores para a seguinte na lista.

Apesar de Gestor de tráfego decida onde ir numa activação pós-falha, pode decidir se o domínio de activação pós-falha é ativos ou inativos, enquanto não estiver no modo de activação pós-falha. Essa funcionalidade não tem nada fazer com o Gestor de tráfego Azure. Gestor de tráfego Deteta uma falha no site principal e descer ao longo para o site de activação pós-falha. Gestor de tráfego descer, independentemente de se esse site está atualmente a servir utilizadores ou não.

Para mais informações sobre como funciona o Gestor de tráfego Azure, consulte:

 * [Descrição geral do Gestor de tráfego](../traffic-manager/traffic-manager-overview.md)
 * [Configurar o método de encaminhamento de activação pós-falha](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##<a name="azure-disaster-scenarios"></a>Cenários de falhas Azure

As secções seguintes abrangem vários tipos de cenários de falhas. Interrupções de serviço de toda a região não estão a causa apenas de falhas de toda a aplicação. Erros de estrutura ou administração fraco também podem conduzir a corrente. É importante ter em consideração as causas possíveis de uma falha durante a estrutura e o testes fases do seu plano de recuperação. Um bom plano tira partido das funcionalidades Azure e aumenta-los com estratégias específicos da aplicação. A resposta que selecionou é ditada pela importância da aplicação, o objectivo de ponto de recuperação (RPO) bem como o objectivo de tempo de recuperação (RTO).

###<a name="application-failure"></a>Falha de aplicação

Gestor de tráfego Azure trata automaticamente falhas resultantes o software de hardware ou sistema operativo subjacente na máquina virtual anfitrião. Azure cria uma nova instância da função num servidor funcionar e adiciona-a para a rotação do Balanceador de carga. Se o número de ocorrências de função for maior do que um, Azure desloca processamento para as outras instâncias de funções em execução enquanto substituir o nó falhado.

Existem erros da aplicação grave que ocorrem independentemente de quaisquer falhas de hardware ou sistema operativo. A aplicação poderá falhar devido a uma as exceções grave causadas por lógica incorretas ou problemas de integridade dos dados. Tem de incorporar suficiente telemetria para o código para que um sistema de monitorização possa condições de falha de detetar e notifique o administrador de aplicações. Um administrador que tenha completo dados de conhecimento dos processos de recuperação falhas pode tomar uma decisão para invocar um processo de activação pós-falha. Em alternativa, um administrador pode simplesmente aceitar uma falha de disponibilidade para resolver erros críticos.

###<a name="data-corruption"></a>Danos nos dados

Azure armazena automaticamente os seus dados de base de dados do SQL Azure e armazenamento do Windows Azure três vezes forma redundante dentro domains falha diferentes na mesma região. Se utilizar a replicação geo, os dados são armazenados três vezes adicionais numa região diferente. No entanto, se os seus utilizadores ou a sua aplicação danificar esses dados na cópia principal, os dados replica rapidamente para as outras cópias. Infelizmente, isto resulta em três cópias dos dados danificados.

Para gerir danos potenciais dos seus dados, tem duas opções. Em primeiro lugar, pode gerir uma estratégia de cópia de segurança personalizada. Pode armazenar as cópias de segurança no Azure ou no local, dependendo do seu necessidades de negócio ou regulamentos de governação. Outra opção é utilizar a opção restaurar no momento novo para recuperar uma base de dados do SQL. Para mais informações, consulte a secção sobre [estratégias de dados para recuperação de falhas](#data-strategies-for-disaster-recovery).

###<a name="network-outage"></a>Falha na rede

Quando estão inacessíveis partes da rede Azure, não poderá aceder à sua aplicação ou dados. Se uma ou mais ocorrências de função sejam encontram disponíveis devido a problemas de rede, Azure utiliza as instâncias disponíveis restantes da sua aplicação. Se a aplicação não é possível aceder os respetivos dados devido a uma falha na rede Azure, potencialmente pode executar no modo degradado localmente utilizando dados em cache. Tem de Arquitectar a estratégia de recuperação de falhas para executar no modo degradado na sua aplicação. Para algumas aplicações, isto poderá não ser prático.

Outra opção é armazenar dados numa localização alternativa até que seja restaurada conectividade. Se modo degradado não for uma opção, as restantes opções são tempo de inatividade da aplicação ou activação pós-falha para uma região alternativa. A estrutura de uma aplicação a ser executada em modo degradado é quanto uma decisão de negócio como um técnico. Isto é abordado adicional na secção [degradado funcionalidade da aplicação](#degraded-application-functionality).

###<a name="failure-of-a-dependent-service"></a>Falha de um serviço dependente

Azure fornece muitos serviços que podem experimentar o tempo de inatividade periódico. Considere [Azure Redis Cache](https://azure.microsoft.com/services/cache/) de exemplo. Este serviço do inquilino com várias fornece capacidades de colocação em cache para a sua aplicação. É importante ter em consideração o que acontece na sua aplicação se o serviço dependente está indisponível. De várias formas, este cenário é semelhante ao cenário de falha de rede. No entanto, considerar cada serviço de forma independente resulta no potenciais melhorias para o seu plano geral.

Azure Redis Cache fornece colocação em cache para a aplicação a partir da implementação do serviço na nuvem, que fornece falhas benefícios de recuperação. Em primeiro lugar, o serviço agora é executado em funções que são locais para a sua implementação. Por conseguinte, não melhor conseguir monitorizar e gerir o estado da cache como parte dos seus processos de gestão global para o serviço de nuvem. Este tipo de colocação em cache também expõe novas funcionalidades. Uma das novas funcionalidades é elevada disponibilidade para os dados em cache. Isto ajuda a manter os dados em cache se num único nó falhar, mantendo as cópias duplicadas em outros nós.

Note que elevada disponibilidade diminui débito e aumenta latência devido a atualizar a cópia secundária no escritas. -Lo também é duplicado a quantidade de memória que é utilizada para cada item, por isso planeie para esse. Este exemplo específico demonstra que cada serviço dependente pode ter capacidades melhorar a sua disponibilidade geral e resistência a falhas grave.

Com cada serviço dependente, deve compreender as implicações de uma interrupção de serviço. No exemplo colocação em cache, poderá ser possível aceder aos dados diretamente a partir de uma base de dados até restaurar a cache. Isto seria um modo degradado em termos de desempenho mas seria fornece a funcionalidade completa no que respeita aos dados.

###<a name="region-wide-service-disruption"></a>Interrupção de serviço de toda a região

As falhas anterior principalmente tem sido falhas que podem ser geridas dentro da mesma região Azure. No entanto, também tem de preparar para a possibilidade de que existe uma interrupção de serviço de toda a região. Caso ocorra uma interrupção de serviço de toda a região, as cópias localmente redundantes dos seus dados não estão disponíveis. Se ativou o geo replicação, existem três cópias adicionais das suas blobs e tabelas numa região diferente. Se Microsoft declara região perdido, Azure reatribui todas as entradas DNS à região de replicadas geo.

>[AZURE.NOTE] Tenha em atenção que não tem qualquer controlo sobre este processo e -lo irá ocorrer apenas para interrupção de serviço de toda a região. Por esta razão, tem de depender outras estratégias de cópia de segurança específicos da aplicação para alcançar o nível mais alto de disponibilidade. Para mais informações, consulte a secção sobre [estratégias de dados para recuperação de falhas](#data-strategies-for-disaster-recovery).

###<a name="azure-wide-service-disruption"></a>Interrupção de serviço de toda a Azure

Em falhas de planeamento, tem de considerar o intervalo de catástrofes possíveis inteiro. Uma das mais graves interrupções de serviço implicar todas as regiões Azure em simultâneo. Tal como acontece com outras interrupções de serviço, pode decidir que irá demorar nesse caso o risco de tempo de inatividade temporário. Interrupções de serviço executarão que aparecem em regiões devem ser muito raros que interrupções de serviço isoladas que envolvam serviços dependentes ou regiões únicos.

No entanto, para algumas aplicações críticos, pode decidir que devem ser um plano de cópia de segurança para este cenário também. O plano para este evento pode incluir a falhar ao longo aos serviços numa [nuvem alternativa](#alternative-cloud) ou um [híbrido no local e na nuvem solução](#hybrid-on-premises-and-cloud-solution).

###<a name="degraded-application-functionality"></a>Funcionalidade da aplicação degradado

Uma aplicação bem concebida normalmente utiliza uma coleção de módulos comunicar com os outros embora a implementação de padrões de agregamento de intercâmbio de informações. Uma aplicação de DR amigável requer separação de tarefas ao nível do módulo. Este é para impedir que a interrupção de um serviço dependente colocá-se para baixo a aplicação completa. Por exemplo, considere uma aplicação de commerce web para Y da empresa. Os seguintes módulos podem constituir a aplicação:

 * __Catálogo de produtos__ permite aos utilizadores procurar produtos.
 * __Carrinho de compras__ permite aos utilizadores adicionar/remover produtos no seu carrinho de compras.
 * __Estado da ordem de__ mostra o estado de envio das encomendas de utilizador.
 * __Ordem de submissão__ finaliza sido a sessão de compras, apresentando a encomenda com pagamento.
 * __Ordem de processamento__ valida a ordem de integridade dos dados e executa uma verificação de disponibilidade quantidade.

Quando um dependente de um módulo nesta aplicação entra para baixo, como é que o módulo funcionar até essa parte recupera? Um sistema bem concepção implementa limites de isolamento através de separação de tarefas na altura da estruturação e o tempo de execução. Pode categorizar cada falha como recuperáveis e que não sejam recuperáveis. Erros não recuperável irão demorar para baixo o módulo, mas pode mitigar um erro recuperável através de alternativas. Conforme descrito na secção disponibilidade de alta, pode ocultar alguns problemas de utilizadores ao processamento falhas e executa ações alternativas. Durante uma interrupção de serviço mais grave, a aplicação poderá ser completamente indisponível. No entanto, uma terceira opção é prestado utilizadores no modo degradado.

Por exemplo, se a base de dados que aloja encomendas vai para baixo, o módulo processamento encomendas perde a sua capacidade para processar as transações de vendas. Consoante a arquitetura, poderá ser disco rígido ou impossibilita para as peças de submissão da encomenda e a ordem de processamento do pedido para continuar. Se a aplicação não está concebida para processar neste cenário, a aplicação completa poderá ficar offline.

No entanto, este cenário mesmo, é possível que os dados de produto estão armazenados numa localização diferente. Nesse caso, o módulo do catálogo de produtos ainda pode ser utilizado para a visualização de produtos. No modo de degradado, a aplicação continua a estar disponível para utilizadores para as funcionalidades disponíveis como ver o catálogo de produtos. No entanto, não outras peças da aplicação, estão disponíveis, tal como as consultas de ordenação ou de inventário.

Centros de variação de outra modo degradado no desempenho em vez de funcionalidades. Por exemplo, considere um cenário onde o catálogo de produtos está a ser colocadas em cache através do Azure Redis Cache. Se a colocação em cache ficar disponível, a aplicação poderá ir diretamente para o armazenamento de servidor para obter informações do catálogo de produtos. Mas este acesso poderá ser mais lento do que a versão em cache. Por esta razão, o desempenho da aplicação é degradado até que o serviço de cache é completamente restaurado.

Decidir quanto de uma aplicação do irão continuar a função no modo degradado é uma decisões empresariais e uma decisão técnica. A aplicação também tem de decidir como informar os utilizadores dos problemas temporários. Neste exemplo, a aplicação poderão permitir que os produtos de visualização e até mesmo adicioná-los ao carrinho de compras. No entanto, quando o utilizador tenta efectuar uma compra, a aplicação notifica o utilizador que está inacessível temporariamente o módulo de vendas. Não é ideal para o cliente, mas o impede uma interrupção de serviço de toda a aplicação.

##<a name="data-strategies-for-disaster-recovery"></a>Estratégias de dados para recuperação de falhas

Processamento dados corretamente é a área as para aceder à direita no plano de recuperação qualquer falhas. Restaurar dados também é a parte do processo de recuperação que normalmente demora mais tempo. Opções diferentes nos modos de degradação resultam em desafios difícil para recuperação de dados a partir de falha e consistência após falha.

Uma dos fatores é a necessidade de restaurar ou manter uma cópia dos dados da aplicação. Irá utilizar estes dados para uma referência e efeitos transaccionais num site secundário. No local a definição requer um processo de planeamento dispendioso e comprido para implementar uma estratégia de recuperação de falhas de região de múltiplo. Convenientemente, a maior parte dos fornecedores de nuvem, incluindo Azure, permitem facilmente a implementação de aplicações para várias regiões. Estas regiões são distribuídas geograficamente forma a que interrupção de serviço de região múltiplo deve ser extremamente rara. A estratégia para processamento de dados através de regiões é um dos factores que contribuem para o sucesso das qualquer plano de recuperação de falhas.

As secções seguintes abordam técnicas de recuperação de falhas relacionadas com cópias de segurança de dados, dados de referência e dados transaccionais.

###<a name="backup-and-restore"></a>Cópia de segurança e restauro

Cópias de segurança regulares dos dados da aplicação podem suporta alguns cenários de recuperação de falhas. Recursos de armazenamento diferentes requerem técnicas diferentes.

Para as camadas Basic, padrão e base de dados do SQL Premium, pode tirar partido de restaurar em qualquer altura para recuperar a sua base de dados. Para obter mais informações, consulte o artigo [Descrição geral: na nuvem recuperação de falhas de bases de dados e continuidade empresas com base de dados SQL](../sql-database/sql-database-business-continuity.md). Outra opção é utilizar a replicação de Geo ativa para a base de dados SQL. Isto replica automaticamente alterações de base de dados para bases de dados secundárias na mesma região Azure ou até mesmo numa região Azure diferente. Este procedimento fornece uma alternativa potencial a algumas técnicas de sincronização de dados mais manuais apresentadas neste artigo. Para obter mais informações, consulte o artigo [Descrição geral: da base de dados ativo Geo-a replicação SQL](../sql-database/sql-database-geo-replication-overview.md).

Também pode utilizar uma abordagem mais manual para cópia de segurança e restauro. Utilize o comando Copiar de base de dados para criar uma cópia da base de dados. Tem de utilizar este comando para obter uma cópia de segurança com consistência transaccional. Também pode utilizar o serviço de importação/exportação de base de dados do SQL Azure. Esta opção suporta a exportação bases de dados aos ficheiros BACPAC que estão armazenados no armazenamento de Blobs do Azure.

A redundância incorporada de armazenamento do Windows Azure cria duas réplicas do ficheiro de cópia de segurança na mesma região. No entanto, a frequência de executar o processo de cópia de segurança determina o RPO, qual é a quantidade de dados que podem perder em cenários de falhas. Por exemplo, imagine que efetuar uma cópia de segurança na parte superior da hora e uma falhas ocorre dois minutos antes do início da hora. Irá perder 58 minutos de dados que aconteceu após a última cópia de segurança foi executada. Além disso, para proteger contra uma interrupção de serviço de toda a região, deverá copiar os ficheiros BACPAC para uma região alternativa. Em seguida, tem a opção de restaurar as cópias de segurança na região alternativo. Para obter mais detalhes, consulte o artigo [Descrição geral: na nuvem recuperação de falhas de bases de dados e continuidade empresas com base de dados SQL](../sql-database/sql-database-business-continuity.md).

Para o armazenamento do Windows Azure, pode desenvolver o seu próprio processo de cópia de segurança personalizado ou utilizar uma das várias ferramentas de cópia de segurança de terceiros. Note que a maioria das estruturas de aplicação tem complexidade de adicional onde recursos de armazenamento de referência entre si. Por exemplo, considere uma base de dados do SQL que tenha uma coluna com ligações para um blob de armazenamento do Windows Azure. Se as cópias de segurança não ocorrer em simultâneo, a base de dados poderá ter o ponteiro para um blob que não foi cópia de segurança antes da falha. A aplicação ou o plano de recuperação de falhas tem de implementar processos para processar esta inconsistência após uma recuperação.

###<a name="reference-data-pattern-for-disaster-recovery"></a>Padrão de dados de referência para recuperação de falhas

Dados de referência são só de leitura dados que suporta a funcionalidade de aplicação. Normalmente, não é alterado com frequência. Apesar de cópia de segurança e restauro ser um método para processar interrupções de serviço de toda a região, o RTO é comprida. Quando implementar a aplicação para uma região secundária, algumas estratégias podem melhorar RTO para os dados de referência.

Uma vez que as alterações de dados de referência com pouca frequência, pode melhorar o RTO mantendo uma cópia permanente dos dados de referência na região secundário. Isto elimina o tempo necessário para restaurar as cópias de segurança eventualidade de um. Para cumprir os requisitos de recuperação de falhas de múltiplos região, tem de implementar a aplicação e os dados de referência em conjunto em várias regiões. Tal como mencionado no [padrão de dados de referência para elevada disponibilidade](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), pode implementar os dados de referência para a função propriamente dito, para armazenamento externo ou para uma combinação de ambos.

O modelo de implementação de dados de referência dentro de cluster nós implicitamente satisfaz os requisitos de recuperação de falhas. Implementação de dados de referência para a base de dados SQL requer que implementar uma cópia dos dados de referência para cada região. A mesma estratégia aplica-se ao armazenamento do Windows Azure. Tem de implementar uma cópia de quaisquer dados de referência que estão armazenados no Azure armazenamento para as regiões principais e secundárias.

![Publicação de dados de referência para as regiões principais e secundárias](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

Tem de implementar o seus próprio rotinas de cópia de segurança específicos da aplicação para todos os dados, incluindo os dados de referência. Cópias de replicadas geo entre as regiões são utilizadas apenas numa interrupção de serviço de toda a região. Para impedir que o tempo de inatividade adicional, implemente as partes críticos dos dados da aplicação à região de secundário. Para um exemplo sobre esta topologia, consulte o [modelo de activo passivo](#active-passive).

###<a name="transactional-data-pattern-for-disaster-recovery"></a>Padrão de dados transaccionais para recuperação de falhas

Implementação de uma estratégia de modo falhas totalmente funcional exige replicação assíncrona dos dados transaccionais à região de secundário. As janelas de tempo prático dentro do qual pode ocorrer a replicação vai determinar as características RPO da aplicação. Ainda poderá recuperar os dados que foram perdidos da região do principal durante a janela de replicação. Poderá também conseguir unir com a região secundária mais tarde.

Os exemplos de arquitectura seguintes fornecem algumas ideias sobre as diferentes maneiras de processamento de dados transaccionais num cenário de activação pós-falha. É importante ter em atenção que estes exemplos não são exaustivos. Por exemplo, poderão ser substituídas localizações de armazenamento intermédio como filas com base de dados do SQL Azure. As filas próprios poderá filas de armazenamento do Windows Azure ou Azure Service Bus (consulte [filas Azure e filas de serviço Bus – comparado com e contraste](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Destinos de armazenamento do servidor também podem variar, tais como tabelas Azure em vez de base de dados SQL. Além disso, funções de trabalho podem ser inseridas como intermediários em vários passos. O importante não é emular estes arquitecturas exatamente, mas a ter em consideração diversos alternativas na recuperação de dados transaccionais e módulos relacionados.

####<a name="replication-of-transactional-data-in-preparation-for-disaster-recovery"></a>Replicação de dados transaccionais preparação para a recuperação de falhas

Considere a hipótese de uma aplicação que utiliza filas de armazenamento do Windows Azure para armazenarem transaccionais dados. Esta opção permite-funções de trabalho processar os dados para a base de dados do servidor numa arquitetura de desacoplados transaccionais. Isto requer que a conclusão das transações para utilizar alguma forma de temporário colocação em cache se as funções de front-end exigem a consulta imediata desses dados. Dependendo do nível de tolerância a perda de dados, pode optar por criar uma réplica as filas, a base de dados ou todos os recursos de armazenamento. Com apenas replicação de bases de dados, se acede a região principal para baixo, pode ainda recuperar os dados nas filas quando a região primária surgir novamente.

O diagrama seguinte mostra uma arquitetura de onde a base de dados do servidor é sincronizada através de regiões.

![Replicação de dados transaccionais preparação para a recuperação de falhas](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

O desafio maior esta arquitetura de execução é a estratégia de replicação entre regiões. O serviço de sincronização de dados do SQL Azure permite este tipo de replicação. No entanto, o serviço ainda se encontra na pré-visualizar e não é recomendado para os ambientes de produção. Para obter mais informações, consulte o artigo [Descrição geral: na nuvem recuperação de falhas de bases de dados e continuidade empresas com base de dados SQL](../sql-database/sql-database-business-continuity.md). Para as aplicações de produção, tem de investir numa solução de terceiros ou criar o seu próprio lógica de replicação no código. Dependendo a arquitetura, a replicação poderão não estar bidireccional, que é mais complexo.

Uma implementação potencial pode fazer a utilização da fila de espera intermédia no exemplo anterior. A função de trabalho que processa dados para o destino de armazenamento final poderá ser fazer a alteração na região de principal e a região secundária. Estes não são tarefas comum e orientações completa para o código de replicação está fora do âmbito deste artigo. O ponto de importante é o seu tempo muitas e testes devem concentrar-se como pode criar uma réplica os seus dados à região de secundário. Processar e testes adicionais podem ajudar a garantir que os processos activação e recuperação corretamente processam quaisquer inconsistências de dados possíveis ou transações duplicadas.

>[AZURE.NOTE] A maior parte deste artigo foca-se em plataforma como um serviço (PaaS). No entanto, opções de replicação e disponibilidade adicionais para aplicações híbridas utilizam máquinas virtuais do Azure. Estas aplicações híbridas utilizam infraestrutura como um serviço (IaaS) para alojar o SQL Server em máquinas virtuais no Azure. Esta opção permite-tradicional disponibilidade abordagens no SQL Server, tal como grupos de Disponibilidade AlwaysOn ou endereço de envio, o registo. Algumas técnicas, tal como AlwaysOn, funcionam apenas entre instâncias do SQL Server no local e máquinas virtuais Azure. Para mais informações, consulte o artigo [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####<a name="degraded-application-mode-for-transaction-capture"></a>Modo de aplicação degradado para captura da transação

Considere uma segunda arquitectura que opera no modo degradado. A aplicação na região do secundário desativa todas as funcionalidades, como relatórios, informações da empresa (BI), ou gastar filas. Este aceita apenas os tipos de mais importantes de transaccionais fluxos de trabalho, conforme definido pelo necessidades de negócio. O sistema a conclusão das transações para capturar e escreve-as filas. O sistema poderá adiar o processamento dos dados durante a fase inicial de interrupção do serviço. Se o sistema na região do principal é reactivar dentro da janela de tempo esperado, as funções de trabalho na região primária podem descarregar as filas. Este processo elimina a necessidade de intercalação de base de dados. Se entrar em modo de interrupção de serviço de região principal para além da janela máximo, pode iniciar a aplicação processar as filas.

Neste cenário, a base de dados secundária contém dados transaccionais utilizarão que têm de ser intercalados depois da página principal é reactivar. O diagrama seguinte mostra esta estratégia para armazenar temporariamente dados transaccionais até que a região primária é restaurada.

![Modo de aplicação degradado para captura da transação](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Para obter mais debate de técnicas de gestão de dados para aplicações do Azure e são, consulte o artigo [isenta de falhas: seta de quatro pontas e são nuvem arquitecturas](https://channel9.msdn.com/Series/FailSafe).

##<a name="deployment-topologies-for-disaster-recovery"></a>Topologias de implementação para recuperação de falhas

Tem de preparar críticos aplicações a possibilidade de uma interrupção de serviço de toda a região. Pode fazê-lo por incorporar uma estratégia de implementação de região de múltiplos no planeamento operacional.

Região de múltiplos implementações poderão envolvam processos IT pro para publicar os dados da aplicação e referência à região de secundário após uma falhas. Se a aplicação requerer activação pós-falha instantânea, o processo de implementação poderá implicar um programa de configuração do activo/passivo ou um activo/activo. Este tipo de implementação tem existentes instâncias da aplicação executar na região alternativo. Uma ferramenta de encaminhamento como Gestor de tráfego do Azure fornece serviços de balanceamento de carga ao nível de DNS. Pode detetar interrupções de serviço e encaminha os utilizadores para regiões diferentes quando for necessário.

Parte de uma recuperação de falhas Azure bem sucedida é criação esse recuperação solução desde o início. Nuvem fornece opções adicionais para recuperar de falhas durante uma falhas que não estão disponíveis num fornecedor de alojamento tradicional. Especificamente, pode rapidamente e dinamicamente atribuir recursos para uma região diferente. Por conseguinte, não pagar muitas para recursos idle enquanto está à espera para uma falha a ocorrer.

As secções seguintes abrangem topologias de implementação diferentes para recuperação de falhas. Normalmente, existe uma solução de compromisso aumento do custo ou complexidade para disponibilidade adicional.

###<a name="single-region-deployment"></a>Implementação de região único

Uma implementação única região não é realmente uma topologia de recuperação de falhas, mas se destinar a contraste com as outras arquiteturas de. Implementações única região são comuns nas aplicações do Azure. No entanto, este tipo de implementação não é uma desafiador grave para um plano de recuperação de falhas.

O diagrama seguinte ilustra uma aplicação em execução numa única região Azure. Gestor de tráfego Azure e a utilização de domínios falhas e atualização de aumentam a disponibilidade da aplicação dentro de uma região.

![Implementação de região único](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

Aqui, é evidente-se de que a base de dados é um ponto de falha único. Apesar de Azure replica os dados em vários domínios falha diferentes para réplicas internos, tudo isto ocorre na mesma região. A aplicação não é possível suportar uma falha grave. Se a região vai para baixo, para todos os domínios de falhas descer – incluindo todas as instâncias do serviço e recursos de armazenamento.

Para todas as mas as aplicações menos críticas, tem de conceber um plano para implementar a suas aplicações em toda a várias regiões. Também deve ter em consideração RTO e custo restrições de considerar qual topologia de implementação para utilizar.

Vamos dar uma olhadela agora na padrões específicos para suportar activação pós-falha ao longo de regiões diferentes. Estes exemplos todas as utilizam duas regiões para descrever o processo.

###<a name="redeployment-to-a-secondary-azure-region"></a>Nova implementação para uma região Azure secundária

O padrão de nova implementação para uma região secundária, apenas a região primária contém aplicações e bases de dados em execução. Região de secundário não está configurado para uma activação automática pós-falha. Por isso, quando ocorre uma falhas, tem giratório até todas as partes do serviço na nova região. Isto inclui carregar um serviço na nuvem para Azure, implementar o serviço de nuvem, restaurar os dados e alterar o DNS para o tráfego de reencaminhar.

Apesar de esta é mais acessível das opções de região de múltiplos, possui as características RTO pior. Neste modelo, as cópias de segurança do pacote e base de dados do serviço são armazenadas quer no local ou na instância de armazenamento de Blobs do Azure da região secundário. No entanto, tem de implementar um novo serviço e restaurar os dados antes de que seja com essa operação. Mesmo se automatizar completamente a transferência de dados a partir do armazenamento cópia de segurança, girar o ambiente de base de dados do novo consome muito tempo. Mover dados a partir do armazenamento disco de cópia de segurança para a base de dados vazia na região do secundário é o mais dispendioso parte do processo de restaurar. Deve fazê-lo, no entanto, para trazer nova base de dados para um Estado de funcionamento de uma vez que não está replicada.

É a melhor abordagem armazenar os pacotes de serviço de armazenamento de BLOBs na região secundário. Isto elimina a necessidade para carregar o pacote de atualizações para o Azure, que é o que acontece quando implementar a partir de uma máquina de desenvolvimento no local. Pode implementar rapidamente os pacotes de serviço num serviço na nuvem novo a partir do armazenamento de BLOBs utilizando scripts de PowerShell.

Esta opção é prática apenas para não críticos aplicações que podem tolerar um RTO alta. Por exemplo, pode funcionar para uma aplicação que pode ser para baixo para várias horas, mas deve estar em execução novamente dentro de 24 horas.

![Nova implementação para uma região Azure secundária](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###<a name="active-passive"></a>Activo passivo

O padrão de activo passivo é na opção que preferir muitas empresas. Este padrão fornece melhoramentos para RTO com um aumento relativamente pequeno custos sobre o padrão de nova implementação.
Neste cenário, existe novamente uma principal e uma região Azure secundária. Todo o tráfego vai para a implementação do active na região do principal. Região de secundário melhor está preparado para recuperação de falhas porque a base de dados está em execução no ambas as regiões. Para além disso, um mecanismo de sincronização é num local entre elas. Esta abordagem espera pode envolvam duas variações: uma abordagem só de base de dados ou uma implementação concluída na região secundário.

####<a name="database-only"></a>Apenas a base de dados

Na primeira variante do padrão activo passivo, apenas a região primária tem uma aplicação de serviço de nuvem implementada. No entanto, ao contrário padrão nova implementação, ambas as regiões são sincronizadas com o conteúdo da base de dados. (Para obter mais informações, consulte a secção no [padrão de dados transaccionais para recuperação de falhas](#transactional-data-pattern-for-disaster-recovery).) Quando ocorre uma falhas, existem menos requisitos de ativação. Iniciar a aplicação na região secundário, altere as cadeias de ligação para a nova base de dados e alterar as entradas de DNS para reencaminhar o tráfego.

Como o padrão de nova implementação, deverá já tiver armazenado os pacotes de serviço de armazenamento de Blobs do Azure na região secundário para implementação mais rápida. Ao contrário padrão nova implementação, não assumir a maioria do overhead que requer a operação de restauro de base de dados. A base de dados está pronto e em execução. Este procedimento guarda uma quantidade de tempo, fazer isto um padrão de DR acessível significativa. Também é o padrão de DR mais popular.

![Apenas base de dados de activo-passivo](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####<a name="full-replica"></a>Réplica completa

A segunda variante do padrão activo passivo, a região principal e a região secundária a ter uma implementação completa. Esta implementação inclui serviços na nuvem e uma base de dados sincronizada. No entanto, apenas a região primária ativamente é processamento de pedidos de rede dos utilizadores. Região de secundário fica ativo apenas quando a região primária tem uma interrupção de serviço. Nesse caso, todos os novos pedidos de rede encaminha-se à região de secundário. Gestor de tráfego Azure pode gerir automaticamente este activação pós-falha.

Activação pós-falha mais depressa do que a variação de base de dados só ocorre porque já estão implementados os serviços. Este padrão fornece uma RTO muito baixo. Região de activação pós-falha secundário tem de ser pronta a ser enviada imediatamente após a falha da região principal.

Juntamente com um tempo de resposta mais rápido, este padrão tem a vantagem de previamente alocar e implementação dos serviços de cópia de segurança. Não tem de se preocupar numa região não ter espaço para atribuir uma falhas as novas instâncias. Isto é importante se a sua região Azure secundário está a chegar capacidade. Há garantias (contrato de nível de serviço) que instantaneamente ser capaz de implementar um número de novos serviços em nuvem qualquer região.

Para o tempo de resposta mais rápido com este modelo, tem de ter escala semelhante (número de ocorrências de função) nas regiões principais e secundárias. Apesar das vantagens, paga por instâncias de cluster não utilizadas é dispendioso e este poderão não estar a escolha financeira mais prudente. Por esta razão, é mais comuns para utilizar uma versão de ligeiramente dimensionada descendente dos serviços em nuvem na região do secundário. Em seguida, pode rapidamente falhar ao longo do e dimensionar saída de implementação secundária, se necessário. Deverá automatizar o processo de activação pós-falha para que depois da região primária for inacessível, ativar ocorrências adicionais, consoante a carregar. Isto poderá implicar a utilização de um dispositivo autoscaling como [conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

O diagrama seguinte mostra o modelo de onde as regiões principais e secundárias contêm um serviço em nuvem totalmente implementado num padrão activo passivo.

![Réplica activo passivo, completa](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###<a name="active-active"></a>Activo activo

Por agora, está a provavelmente perceber a evolução dos padrões: diminuir o RTO aumenta os custos e complexidade. A solução activo activo realmente quebras este tendência no que diz respeito à custo.

Num padrão activo activo, a serviços em nuvem e a base de dados são totalmente implementados em ambas as regiões. Ao contrário de modelo activo passivo, ambas as regiões recebem tráfego de utilizador. Esta opção origina a hora de recuperação mais rápida. Os serviços já são dimensionados para processar uma parte da carga cada região. DNS já está ativado para utilizar a região secundária. Existe complexidade adicional para determinar como encaminhar os utilizadores para a região apropriada. Poderá ser possível round robin agendamento. É mais provável que a determinados utilizadores utilizaria uma determinada região onde reside a cópia primária dos seus dados.

Em caso de falha na ligação, basta desative DNS à região de principal. Isto encaminha todo o tráfego para a região secundária.

Mesmo neste modelo, existem algumas variações. Por exemplo, o diagrama seguinte mostra um modelo de onde a região principal pertence a cópia da base de dados principal. Escreverem os serviços em nuvem de ambas as regiões para essa base de dados principal. A implementação secundária pode ler a partir da base de dados principal ou de replicadas. Replicação neste exemplo acontece uma forma.

![Activo activo](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

Existe uma desvantagem para a arquitetura de activo activo no diagrama anterior. Segundo região tem de aceder a base de dados na região primeiro uma vez que a cópia principal reside aí. Desempenho significativamente desce desativar quando aceder a dados fora de uma região. Em chamadas de publicação em região base de dados, deverá tomar em consideração algum tipo de lotes estratégia para melhorar o desempenho destas chamadas. Para mais informações, consulte o artigo [como utilizar lotes para melhorar o desempenho da aplicação de base de dados SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Uma arquitetura alternativa poderá implicar cada região aceder diretamente a sua própria base de dados. Nesse modelo algum tipo de replicação bidireccional é necessária para sincronizar as bases de dados em cada região.

Em padrão activo activo, poderá não ter quantas instâncias na região do principal como faria no padrão de activo passivo. Se tiver 10 instâncias da região primária de uma arquitetura de activo passivo, poderá ter apenas 5 em cada uma das regiões numa arquitetura de activo activo. Ambas as regiões agora partilham a carregar. Isto pode ser poupanças de custos ao longo do padrão de activo passivo se mantiver um quente espera na região do passiva com 10 instâncias aguardar activação pós-falha.

Aperceba-se de que até restaurar a região primária, secundário região poderá receber um picos repentina sobre de novos utilizadores. Se existirem 10.000 utilizadores em cada servidor quando a região primária tem uma interrupção de serviço, a região secundária tem inesperadamente processar 20.000 utilizadores. Monitorização regras na região do secundário tem de detectar este aumentar e duplo as instâncias na região secundário. Para obter mais informações, consulte a secção no [detecção de falha](#failure-detection).

##<a name="hybrid-on-premises-and-cloud-solution"></a>Híbrido no local e na nuvem solução

Uma estratégia adicional de recuperação de falhas é Arquitectar uma aplicação híbrida que é executada no local e na nuvem. Dependendo da aplicação, a região primária poderão não estar qualquer localização. Considere as arquitecturas anterior e imagine a região principal ou secundária como uma localização no local.

Existem alguns desafios estes arquitecturas híbrido. Em primeiro lugar, a maior parte deste artigo tem endereçadas PaaS padrões de arquitectura. As aplicações PaaS típicas no Azure dependem construções Azure específicos como Gestor de tráfego, serviços em nuvem e funções. Para criar uma solução no local para este tipo de aplicação PaaS seria necessário uma arquitetura de significativamente diferente. Poderá não ser viável de uma gestão ou perspetiva de custo.

No entanto, uma solução híbrido para recuperação de falhas tem menos desafios para arquitecturas tradicionais que tenham simplesmente movidas para a nuvem. Este é verdadeira relativamente a arquitecturas que utilizam IaaS. Aplicações de IaaS utilizam máquinas virtuais na nuvem que pode ter equivalentes direta no local. Também pode utilizar redes virtuais para se ligar máquinas na nuvem a recursos de rede no local. Esta ação abre o várias possibilidades que não são possíveis com aplicações só de PaaS. Por exemplo, SQL Server pode tirar partido das soluções de recuperação de falhas como grupos de Disponibilidade AlwaysOn e espelhando as bases de dados. Para obter detalhes, consulte o artigo [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Soluções de IaaS também fornecem um caminho mais fácil para as aplicações no local utilizar o Azure como a opção de activação pós-falha. Poderá ter uma aplicação totalidade numa região existente no local. No entanto, o que acontece se possui os recursos para manter uma região centrados em separado para activação pós-falha? Pode decidir utilizar máquinas virtuais e redes virtuais para obter a aplicação em execução no Azure. Nesse caso, defina processos sincronizar dados para a nuvem. Em seguida, a implementação Azure torna-se a região secundária a utilizar para activação pós-falha. Região de primária mantém-se a aplicação no local. Para mais informações sobre IaaS arquitecturas e as funcionalidades, consulte a [documentação de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/).

##<a name="alternative-cloud"></a>Nuvem alternativa

Existem situações onde mesmo a robustez do Microsoft Cloud poderá não cumprir regras de conformidade interno ou políticas que requer a sua organização. Ainda melhor preparação e estrutura para implementar sistemas de cópia de segurança durante uma falhas de se situar breve se existir uma interrupção de serviço global de um fornecedor de serviços na nuvem.

Irá pretende comparar os requisitos de disponibilidade com o custo e complexidade da maior disponibilidade. Efetuar uma análise de risco e definir as RTO e RPO para a sua solução. Se a sua aplicação não poder permitir qualquer tempo de inatividade, poderá fazer adequado para a ter em conta a utilizar outra solução na nuvem. A menos que acede a totalidade da Internet para baixo, outra solução nuvem poderão ainda estar disponível se Azure ficar globalmente inacessível.

Tal como acontece com o cenário de implementações híbridas a activação pós-falha nas arquitecturas de recuperação de falhas anterior também podem existir dentro de outra solução na nuvem. Sites de nuvem alternativo DR devem ser utilizadas apenas para soluções cuja RTO permite muito pequeno, se existirem, tempo de inatividade. Note que numa solução que utiliza um site de DR fora Azure vai requerer mais trabalho a configurar, desenvolver, implementar e manter. Também é mais difícil implementar o melhores práticas numa arquitetura de publicação em nuvem. Apesar de nuvem plataformas tem os conceitos de alto nível semelhantes, APIs e arquitecturas são diferentes.

Se decidir dividir o DR entre diferentes plataformas, faria sentido Arquitectar camadas de produção na estrutura da solução. Se efetuar o seguinte, não é necessário desenvolver e manter as duas versões diferentes do mesmo pedido de plataformas de nuvem diferente em caso de falhas. Como o cenário híbrido, a utilização de máquinas virtuais do Azure ou o serviço de contentor Azure poderá nestes casos ainda mais fácil a utilização de estruturas de PaaS específicas na nuvem.

##<a name="automation"></a>Automatização

Alguns dos padrões de debatido apenas requerem a ativação rápida implementações offline e a restauração de partes específicas de um sistema. Automatização ou scripting, suporta a capacidade de ativar recursos a pedido e implementar soluções rapidamente. Neste artigo, relacionados com DR automatização equivale com [PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx), mas a [API do serviço de gestão de resto](https://msdn.microsoft.com/library/azure/ee460799.aspx) também é uma opção.

Desenvolver scripts ajuda a gerir as partes de DR Azure não processar transparente. Isto tem o benefício de produzir resultados consistentes sempre que minimiza a probabilidade de erro humano. Também predefinidos de scripts de DR reduz o tempo a recompilar um sistema e as suas partes constituintes ao meio de uma falhas. Não pretende tente descobrir manualmente como restaurar o seu site enquanto está dinheiro para baixo e preterido minuto.

Depois de criar os scripts, testá-las a repetidamente do princípio ao fim. Depois de verificar a respetiva funcionalidade básica, certifique-se de que testá-las no [simulação falhas](#disaster-simulation). Isto ajuda-o a descobrir falhas de scripts ou processos.

É aconselhável com a automatização criar um repositório de scripts de PowerShell ou scripts de interface de comandos (CLI) para recuperação de falhas Azure. Claramente marcar e categorizá-los para pesquisa fácil. Designe uma pessoa para gerir as repositório e controlo de versões dos scripts. Documento-los bem com explicações dos parâmetros e exemplos de utilização de script. Também Certifique-se de que mantenha esta documentação sincronizado com o seu implementações Azure. Deste modo, o objetivo de ter uma pessoa responsável pelo todas as partes do repositório.

##<a name="failure-detection"></a>Deteção de falha

Para processar corretamente os problemas com a disponibilidade e recuperação de falhas, tem de ser conseguir detetar e diagnosticar falhas. O que deve fazer avançadas do servidor e implementação de monitorização para rapidamente pode saber quando estão inesperadamente um sistema ou partes da mesma para baixo. Ferramentas observe o estado de funcionamento geral do serviço na nuvem e respectivas dependências de monitorização pode executar parte deste trabalho. Uma ferramenta de Microsoft é [2016 do Centro de sistema](https://www.microsoft.com/en-us/server-cloud/products/system-center-2016/). Ferramentas de terceiros também podem fornecer as capacidades de monitorização. A maioria das soluções de monitorização registar contadores de chave de desempenho e disponibilidade do serviço.

Embora estas ferramentas sejam crucial, não substituem a necessidade de planear a deteção de falhas e relatórios dentro de um serviço na nuvem. Tem planeia utilizar corretamente os diagnósticos do Azure. Contadores de desempenho personalizado ou entradas de registo de eventos também podem fazer parte da estratégia global. Este procedimento fornece mais dados durante falhas para diagnosticar o problema e restaurar as capacidades completas rapidamente. Também fornece métricas adicionais que podem utilizar as ferramentas de monitorização para determinar a saúde de aplicação. Para mais informações, consulte o artigo [Ativar o Azure diagnósticos no Azure serviços em nuvem](../cloud-services/cloud-services-dotnet-diagnostics.md). Para um debate de como planear uma global "modelo de estado de funcionamento", consulte o artigo [isenta de falhas: seta de quatro pontas e são nuvem arquitecturas](https://channel9.msdn.com/Series/FailSafe).

##<a name="disaster-simulation"></a>Simulação de falhas

Simulação testes envolve criar pequenas situações de vida real no piso trabalho observar como reagirem os membros da equipa. Simulações também mostram como eficaz as soluções estão no plano de recuperação. Realizar simulações de forma a que os cenários criados não perturbar negócio reais enquanto ainda dar como situações reais.

Considere a criação de um tipo de "painel de navegação" na aplicação para simular manualmente os problemas de disponibilidade. Por exemplo, através de um parâmetro contornos, acionar exceções de acesso de base de dados para um módulo de ordenação fazendo duplo clique na mesma para mau funcionamento. Pode tirar semelhantes abordagens lightweight para outros módulos ao nível da interface de rede.

Simulação realça quaisquer problemas que recebeu foram resolvidos. Os cenários simulados tem de ser completamente controláveis. Isto significa que, mesmo se o plano de recuperação parece estar a falhar, é possível restaurar a situação ao normal sem causar prejuízos importantes. Também é importante que informem gestão complicado sobre quando e como os exercícios simulação serão executados. Este plano deverá incluir informações sobre o tempo ou recursos que poderão ficar unproductive enquanto o teste de simulação estiver em execução. Quando estiver a submeter o seu plano de recuperação de falhas para um teste, também é importante definir como sucesso vai ser medido.

Existem várias técnicas que pode utilizar para testar os planos de recuperação falhas. No entanto, a maior parte dos mesmos é alterados simplesmente versões destes técnicas básicas. Se o principal motive atrás este teste a avaliar como viável e o plano de recuperação como funcional é. Testes de recuperação de falhas foca-se nos detalhes para descobrir buracos no plano de recuperação básica.

##<a name="next-steps"></a>Próximos passos

Este artigo faz parte de uma série de artigos com o foco nas [recuperação de falhas e elevada disponibilidade para aplicações criadas no Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). O artigo anterior nesta série é [elevada disponibilidade para as aplicações criadas no Microsoft Azure](./resiliency-high-availability-azure-applications.md).
