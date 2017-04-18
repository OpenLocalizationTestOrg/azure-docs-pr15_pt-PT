<properties
   pageTitle="Elevada disponibilidade para aplicações do Azure | Microsoft Azure"
   description="Descrição geral técnica e informações detalhadas sobre a estrutura e criação de aplicações para elevada disponibilidade no Microsoft Azure."
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

#<a name="high-availability-for-applications-built-on-microsoft-azure"></a>Disponibilidade elevada para as aplicações criadas no Microsoft Azure

Uma aplicação altamente disponível absorve os variações de disponibilidade, carregamento e temporárias falhas nos serviços dependentes e hardware. A aplicação continua a trabalhar com um utilizador do aceitável e o nível de resposta sistémicas, conforme definido pelo necessidades de negócio ou os acordos do nível de serviço de aplicação (SLA).

##<a name="azure-high-availability-features"></a>Funcionalidades de alta disponibilidade Azure

Azure tem muitas funcionalidades de plataforma incorporados que suportam aplicações altamente disponíveis. Esta secção descreve alguns dessas funcionalidades chaves. Para uma análise mais abrangente da plataforma, consulte [orientações técnicas RDP Azure](./resiliency-technical-guidance.md).

###<a name="fabric-controller"></a>Controlador de ferro

O controlador de ferro Azure disposições e monitoriza a condição das instâncias cluster Azure. O controlador de ferro verifica o estado do hardware e software de instâncias de máquina anfitrião e como convidado. Quando Deteta uma falha de, impõe SLA ao reposicionar automaticamente as instâncias VM. O conceito de falhas e atualização de domínios ainda mais suporta a SLA cluster.

Quando são implementadas várias instâncias de função de serviço na nuvem, Azure implementa estas instâncias domínios falha diferentes. Um limite de domínio de falhas é basicamente bastidor hardware diferentes na mesma região. Domínios falhas reduzem a probabilidade que uma falha de hardware localizados irá interromper o serviço de uma aplicação. Não pode gerir o número de falhas domínios que estão atribuídos para o seu trabalho ou funções da web. O controlador de ferro utiliza dedicados recursos que são separados a partir de aplicações alojado no Azure. Tempo de utilização de 100 por cento-possui uma vez que serve de núcleo do sistema Azure. Monitoriza e gere instâncias de funções em vários domínios falhas.

O diagrama seguinte mostra os recursos partilhados Azure que o controlador de ferro ser implementada e gere em vários domínios falha diferentes.

![Vista simplificada do isolamento de domínio de falhas](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Atualização domínios são semelhantes aos domínios falhas na função, mas que suportam atualiza os em vez de falhas. Um domínio para a atualização é uma unidade lógica de separação de instância que determina as ocorrências de um serviço específico serão atualizadas num ponto no tempo. Por predefinição, para a implementação de serviço alojado, são definidos cinco domínios de atualização. No entanto, pode alterar esse valor no ficheiro de definição de serviço. Por exemplo, suponha que tem oito instâncias da sua função web. Haverá duas instâncias em três domínios atualização e duas instâncias de um domínio de atualização. Azure define a sequência de atualização, mas baseia-se o número de domínios para a atualização. Para mais informações sobre domínios atualização, consulte o artigo [atualizar um serviço na nuvem](../cloud-services/cloud-services-update-azure-service.md).

###<a name="features-in-other-services"></a>Funcionalidades de outros serviços

Para além de funcionalidades plataforma que suportam a disponibilidade de cluster alta, Azure incorpora alta disponibilidade funcionalidades no respetivos outros serviços. Por exemplo, o armazenamento do Windows Azure mantém três réplicas de todos os BLOBs, tabela e dados de fila de espera. Também lhe permite a opção de replicação geo para armazenar cópias de segurança das tabelas e blobs numa região secundário. A rede de entrega de conteúdos do Azure permite blobs ser colocada em cache em todo o mundo para redundância e escalabilidade. Base de dados SQL Azure mantém várias réplicas também.

Para além da série de [orientação técnica RDP](https://aka.ms/bctechguide) de artigos, consulte o artigo [Melhores práticas para os serviços de estrutura de em larga escala no Azure serviços em nuvem](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) papel. Estes trabalhos fornecem um debate mais aprofundado das funcionalidades de disponibilidade de plataforma Azure.

Apesar do Azure fornece várias funcionalidades que suportam a disponibilidade de alta, é importante compreender as limitações:

- Para cluster, Azure garante que as funções estão disponíveis e em execução, mas não sabe se a sua aplicação está em execução ou sobrecarregado.
- Para a base de dados SQL Azure, dados são replicados modo síncrono dentro de uma região. Pode escolher geo-replicação do active, que lhe permite até quatro cópias de base de dados adicionais na mesma região (ou regiões diferentes). Estas réplicas de base de dados não estão em qualquer altura cópias de segurança. Bases de dados SQL fornecem capacidades de cópia de segurança de em qualquer altura. Para saber mais sobre as capacidades de base de dados de SQL ponto-in-time, leia o [Ponto de bases de dados do SQL Azure em vez de restaurar](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
- Para o armazenamento do Windows Azure, dados de tabela e blob são replicados por predefinição, para uma região alternativa. No entanto, não consegue aceder as réplicas até que a Microsoft escolhe a falha ao longo para o site alternativo. Ocorre uma região activação pós-falha apenas no caso de uma interrupção de serviço de toda a região prolongada e não existe nenhuma SLA para tempo de geo com falha. Também é importante ter em atenção que qualquer danos nos dados rapidamente numa ou páginas para as réplicas.

Por estas razões, tem de completar as funcionalidades de disponibilidade de plataforma com as funcionalidades de disponibilidade específicos da aplicação. Funcionalidades específicas da aplicação disponibilidade incluem a funcionalidade de instantâneo blob para criar cópias de segurança no momento de dados de Blobs.

###<a name="availability-sets-for-azure-virtual-machines"></a>Disponibilidade define para máquinas virtuais do Azure

A maioria deste artigo foca-se a serviços em nuvem, que utilizar uma plataforma como um modelo de serviço (PaaS). No entanto, também existem funcionalidades de disponibilidade específico para máquinas virtuais do Azure, que utiliza uma infraestrutura como um modelo de serviço (IaaS). Para alcançar elevada disponibilidade com máquinas virtuais, tem de utilizar conjuntos de disponibilidade. Um conjunto de disponibilidade serve de uma função semelhante de falhas e atualização de domínios. Dentro de um conjunto de disponibilidade, Azure posiciona as máquinas virtuais de uma forma que impede o hardware localizados falhas e atividades de manutenção de colocá-se para baixo de todas as máquinas nesse grupo. Conjuntos de disponibilidade são necessários para alcançar a SLA Azure para a disponibilidade de máquinas virtuais.

O diagrama seguinte fornece uma representação de dois conjuntos de disponibilidade esse web de grupo e máquinas virtuais de SQL Server, respetivamente.

![Disponibilidade define para máquinas virtuais do Azure](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] No diagrama anterior, SQL Server é instalada e em execução em máquinas virtuais. Este é diferente do debate anterior da base de dados de SQL Azure, que fornece uma base de dados como um serviço gerido.

##<a name="application-strategies-for-high-availability"></a>Estratégias de aplicação para elevada disponibilidade

A maior parte dos estratégias de aplicação para elevada disponibilidade envolvam redundância ou a remoção do disco rígidas dependências entre componentes da aplicação. Estrutura da aplicação deverá suportar tolerância a falhas durante o tempo de inatividade esporádico do Azure ou os serviços de terceiros. As secções seguintes descrevem os padrões de aplicação para aumentar a disponibilidade dos seus serviços na nuvem.

###<a name="asynchronous-communication-and-durable-queues"></a>Comunicação assíncrona e filas resistentes

Considere a hipótese de comunicação assíncrona entre serviços agregamento para aumentar a disponibilidade nas aplicações Azure. Neste padrão, escreva mensagens filas de armazenamento ou filas Azure Service Bus para processamento posterior. Quando escrever a mensagem para a fila, controlo devolve imediatamente ao remetente da mensagem. Outra camada da aplicação trata a mensagem de processamento, normalmente implementada como uma função de trabalho. Se a função de trabalho vai para baixo, as mensagens se acumulem na fila de espera até que o serviço de processamento é restaurado. Desde que a fila está disponível, não existe nenhuma direta dependência entre o remetente front-end e o processador de mensagens. Isto elimina o requisito de chamadas do serviço síncrono que pode ser um congestionamento débito em aplicações distribuídas.

Uma variação de isto utiliza armazenamento do Windows Azure (blobs, tabelas, filas) ou filas de serviço Bus como uma localização de activação pós-falha para chamadas de base de dados falhou. Por exemplo, uma chamada síncrona dentro de uma aplicação para outro serviço (como base de dados SQL Azure) falha repetidamente. Poderá conseguir serializar esses dados para o armazenamento resistente. Em algumas ponto posterior quando o serviço ou base de dados estiver novamente online, a aplicação pode voltar a submeter o pedido a partir do armazenamento. A diferença neste modelo é que a localização intermédia não é uma constante peça do fluxo de trabalho de aplicação. É utilizada apenas em cenários de falha.

Em ambos os cenários, comunicação assíncrona e o armazenamento intermédio impedem que um serviço de back-end inactivo colocá-se para baixo a aplicação completa. Filas servem um intermédio lógico. Para obter mais orientações sobre como escolher o serviço de colocação correto, consulte o artigo [filas Azure e filas do Azure Service Bus – comparado com e em contraste com](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###<a name="fault-detection-and-retry-logic"></a>Lógica de deteção e volte a tentar de falhas

Um ponto de chave na estrutura da aplicação altamente disponível é utilizar a lógica de repetição dentro de código processe correctamente um serviço que está temporariamente para baixo. O [Bloco de aplicações processamento breves falhas](https://msdn.microsoft.com/library/hh680934.aspx), desenvolvida pela equipa do Microsoft Patterns e práticas, ajuda os programadores de aplicações neste processo. A palavra "transitória" significa que uma condição temporária que dura apenas para uma altura relativamente curta. No contexto deste artigo, processar falhas breves faz parte de programação de uma aplicação altamente disponível. Exemplos de condições breves incluem os erros de rede intermitentes e ligações de base de dados perdidas.

O bloco de aplicação de processamento de falhas breves é uma forma simplificada para si lidar com falhas no seu código de uma forma com êxito. Pode utilizá-lo para melhorar a disponibilidade das suas aplicações ao adicionar lógica de processamento de falhas breves robusta. Na maioria dos casos, lógica de repetição processa a interrupção breve e reestabelece ligação ao remetente e destinatário depois de um ou mais tentativas falhadas. Uma tentativa de repetir efetuada com êxito, normalmente, é enviada não ser para os utilizadores da aplicação.

Os programadores tem três opções para gerir os seu lógica de repetição: intervalo utilizarão, fixo e exponencial. Aguarda utilizarão já antes cada repetir de uma forma linear crescente (por exemplo, 1, 2, 3 e 4 segundos). Intervalo fixo aguarda a mesma quantidade de tempo entre cada tentativa (por exemplo, 2 segundos). Para uma opção mais aleatória, exponencial back-off espera já entre tentativas. No entanto, utiliza o comportamento exponencial (por exemplo, 2, 4, 8 e 16 segundos).

A estratégia de alto nível no seu código é:

1. Defina a sua estratégia de repetir e política.
1. Experimente a operação que poderá ocorrer uma falha de breves.
1. Se ocorrer falhas breves, invocar a política de repetir.
1. Se todas as repetições falharem, captura uma exceção final.

Teste a lógica de repetição num falhas simuladas para se certificar de que repetições de operações sucessivas não resultar num atraso comprido imprevisto. Fazê-lo antes de decidir provocam a falha da tarefa geral.

###<a name="reference-data-pattern-for-high-availability"></a>Padrão de dados de referência para elevada disponibilidade

Referência dados se os dados só de leitura de uma aplicação. Estes dados fornecem o contexto de empresas dentro do qual a aplicação gera dados transaccionais durante o curso de uma operação de empresas. Dados transaccionais são uma função de ponto-in-time os dados de referência. Por conseguinte, sua integridade depende instantâneo dos dados de referência no momento da transação. Esta é uma definição de um pouco lata, mas -deve ser suficiente para o nosso objetivo aqui.

Dados de referência no contexto de uma aplicação são necessários para o funcionamento da aplicação. As aplicações respetivas criar e mantém os dados de referência; sistemas de gestão (MDM) do modelo global de dados com frequência executam esta função. Estes sistemas são responsáveis pelo ciclo de vida dos dados de referência. Exemplos de dados de referência incluem o catálogo de produtos, modelo global de empregado, modelo global de peças e modelo global de equipamento. Dados de referência podem também têm origem a partir de fora da sua organização, por exemplo, códigos postais ou taxas de impostos. Estratégias para aumentar a disponibilidade de dados de referência são normalmente menos difíceis daqueles para os dados transaccionais. Dados de referência com a vantagem de que está a ser principalmente imutáveis.

Pode tornar Azure funções web e trabalhador consumam autónomos o tempo de execução de dados de referência ao implementar os dados de referência juntamente com a aplicação. Se o tamanho do armazenamento local permite que essas uma implementação, este é um estado ideal. Bases de dados incorporados (SQL, NoSQL) ou ficheiros XML implementados um sistema de ficheiros local irão ajudá-lo a autonomia de unidades de escala cluster Azure. No entanto, deve ter um mecanismos para atualizar os dados em cada função sem necessidade de nova implementação. Para fazer isto, coloque quaisquer atualizações os dados de referência a um ponto final armazenamento de nuvem (por exemplo, armazenamento de Blobs do Azure ou base de dados SQL). Adicione código para cada função de que transfere as atualizações de dados para os nós de cluster no arranque de função. Em alternativa, adicione o código que permite que um administrador efetuar uma transferência forçada para as instâncias de função.

Para aumentar a disponibilidade, as funções também devem conter um conjunto de dados de referência no caso de armazenamento é premida. Permite que as funções iniciar com um conjunto de base de dados de referência até que o recurso de armazenamento fica disponível para as atualizações.

![Disponibilidade da aplicação através de nós de cluster autónomo](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Uma consideração para este padrão é a implementação e velocidade de arranque para sua funções. Se estiver a implementar ou transferir grandes quantidades de dados de referência no arranque, isto pode aumentar a quantidade de tempo que demora a giratório o novas implementações ou instâncias de função. Isto pode ser uma variação aceitável para a autonomia de ter os dados de referência imediatamente disponíveis na cada função em vez de cálculo consoante a forma dos serviços de armazenamento externo.

###<a name="transactional-data-pattern-for-high-availability"></a>Padrão de dados transaccionais para elevada disponibilidade

Dados transaccionais se os dados que a aplicação gera num contexto de empresas. Dados transaccionais são uma combinação de um conjunto de processos de negócio que a aplicação implementa e os dados de referência que estes processos de suporte. Exemplos de dados transaccionais podem incluir encomendas, avisos de envio avançados, faturas e oportunidades de gestão (CRM) de relação de cliente. Os dados transaccionais assim gerados vai ser alimentados para sistemas externos para arquivamento ou para processamento suplementar.

Tenha em atenção que podem alterar dados nos sistemas que são responsáveis por estes dados de referência. Por este motivo, dados transaccionais tem de guardar o contexto de dados no momento referência para que este tenha mínimas dependências externas para a sua consistência semântica. Por exemplo, considere a remoção de um produto do catálogo de alguns meses após uma ordem for cumprida. É a melhor prática incorporar quanto contexto de dados de referência como viável a transação. Isto preserva a semântica associada a transação, mesmo se os dados de referência são alterados depois da transação é capturada.

Tal como mencionado anteriormente, arquitecturas que utilizam lato e comunicação assíncrona conduzem a níveis de disponibilidade superiores. Isto mantém-se para dados transaccionais, assim, mas a aplicação é mais complexa. Noções transaccionais tradicionais dependem normalmente a base de dados para garantir a transação. Quando apresentar intermédias camadas, o código da aplicação corretamente deve processar os dados na vista às várias camadas para se certificar de consistência e durabilidade suficientes.

A sequência que se segue descreve um fluxo de trabalho que separa a captura do transaccionais dados a partir do seu processamento:

1. Nó de cluster Web: apresentar dados de referência.
1. Armazenamento externo: guardar dados transaccionais intermédios.
1. Nó de cluster Web: concluir a operação de utilizador final.
1. Nó de cluster Web: enviar os dados transaccionais concluídos, juntamente com o contexto de dados de referência para o armazenamento resistente temporário que garante que dê uma resposta previsível.
1. Nó de cluster Web: assinalar a conclusão do utilizador final da transação.
1. Fundo calcular nó: extrair os dados transaccionais, pós processá-lo, se for necessário e enviá-lo para a sua localização de armazenamento final do sistema atual.

O diagrama seguinte mostra uma implementação possível desta concepção num serviço na nuvem alojado no Azure.

![Disponibilidade através de lato](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

As setas tracejadas no diagrama anterior indicam processamento assíncrono. A função web front-end não está em mente este processamento assíncrono. Isto leva-se ao armazenamento da transação ao seu destino final tendo em conta o sistema atual. Devido a latência que lhe apresenta este modelo assíncrono, os dados transaccionais não não imediatamente disponíveis para a consulta. Por conseguinte, cada unidade dos dados transaccionais tem de ser guardado numa cache ou precisa de uma sessão de utilizador para cumprir IU de imediata.

A função web é autónoma do resto da infraestrutura. Perfil respectiva disponibilidade é uma combinação do papel de web e Azure fila de espera e não a infraestrutura de toda. Para além de elevada disponibilidade, esta abordagem permite que o papel de web Dimensionar horizontalmente, independentemente dos existentes do armazenamento de back-end. Este modelo de alta disponibilidade pode ter um impacto no economics de operações. Componentes adicionais, como o Azure filas e funções de trabalho podem afetar mensal custos de utilização.

Note que o diagrama anterior mostra uma implementação desta abordagem desacoplados aos dados transaccionais. Existem muitas outras implementações possíveis. A lista seguinte fornece algumas alternativas:

 * Uma função de trabalho pode ser colocada entre o papel de web e a fila de armazenamento.
 * Uma fila de serviço Bus pode ser utilizada em vez de uma fila de armazenamento do Windows Azure.
 * O destino final poderá ser armazenamento do Windows Azure ou um fornecedor de base de dados diferente.
 * Azure Cache pode ser utilizado na camada web para fornecer os requisitos de colocação em cache imediatos após a transação.

###<a name="scalability-patterns"></a>Padrões de escalabilidade

É importante ter em atenção que a escalabilidade do serviço de nuvem afeta diretamente disponibilidade. Se um aumento da carga faz com que o seu serviço de ser não responde, a impressão de utilizador é que a aplicação para baixo. Siga as melhores práticas para escalabilidade com base na sua carga esperado da aplicação e as expectativas futuras. A escala mais alta envolve considerações muitos, tal como a utilização de únicos versus várias contas do armazenamento, partilhar em várias bases de dados e, em cache estratégias. Para um aprofundada sobre estes padrões, consulte o artigo [Melhores práticas para os serviços de estrutura de em larga escala no Azure serviços em nuvem](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

##<a name="next-steps"></a>Próximos passos

Este artigo faz parte de uma série de artigos com o foco nas [recuperação de falhas e elevada disponibilidade para aplicações criadas no Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). O seguinte artigo nesta série é a [recuperação de falhas para aplicações criadas no Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).
