<properties
   pageTitle="Recuperação de falhas e elevada disponibilidade para aplicações do Azure | Microsoft Azure"
   description="Descrições gerais técnicos e informações de profundidade sobre conceber aplicações para alta recuperação de disponibilidade e falhas de aplicações criadas no Microsoft Azure."
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

#<a name="disaster-recovery-and-high-availability-for-applications-built-on-microsoft-azure"></a>Recuperação de falhas e elevada disponibilidade para aplicações criadas no Microsoft Azure

##<a name="introduction"></a>Introdução

Este artigo foca-se em elevada disponibilidade para aplicações em execução no Azure. Uma estratégia global para elevada disponibilidade também inclui o aspecto de recuperação de falhas. Planear e desastres na nuvem requer que a reconhecer as falhas rapidamente. Em seguida, a implementar uma estratégia que corresponde ao seu tolerância para tempo de inatividade da aplicação. Para além disso, tem de ter em conta a extensão de perda de dados que a aplicação pode tolerar sem causar consequências empresas nocivos, tal como é restaurado.

A maioria das empresas diga que estão preparados para falhas temporárias e em grande escala. No entanto, antes de atender essa pergunta para si próprio, sua empresa ensaiar estas falhas? Pode testar a recuperação de bases de dados para se certificar de que tem os processos corretos no local? Provavelmente, não. Se ao facto de recuperação de falhas efetuada com êxito começa com muitas planeamento e criação para implementar estes processos. Tal como faria com muitos outros requisitos não funcional, tal como segurança, recuperação de falhas obtém raramente da análise inicial e da atribuição de tempo, é necessário. Além disso, a maior parte das empresas não tem o orçamento geograficamente distribuídos regiões com capacidade de redundante. Por conseguinte, aplicações críticas missão par frequentemente são excluídas do planeamento de recuperação de falhas adequada.

Plataformas de nuvem, como Azure, fornecem regiões geograficamente dispersos em todo o mundo. Estas plataformas também fornecem capacidades que suportam a disponibilidade e uma variedade de cenários de recuperação de falhas. Agora, todas as aplicações de nuvem crítico missão podem ser fornecidas para conclusão consideração para falhas de verificação linguística do sistema. Azure tem RDP e recuperação de falhas integrado para-muitos dos seus serviços. Tem de estudo estas funcionalidades de plataforma cuidadosamente e o suplemento com estratégias de aplicação.

Este artigo destaques os passos de arquitectura necessárias que deve ter-falhas prova uma implementação do Azure. Em seguida, pode implementar o processo de continuidade do negócio maior. Um plano de continuidade do negócio é um guia para continuar operações em condições negativos. Isto pode ser uma falha com a tecnologia, tal como um serviço inactivo ou uma falhas natural, tal como uma falha de energia ou tempestade. RDP de aplicação para catástrofes é apenas um subconjunto do processo de recuperação de maior falhas, tal como descrito neste documento NIST: [Guia de planeamento de contingência para sistemas de tecnologias de informação](https://www.fismacenter.com/sp800-34.pdf).

As secções seguintes definem níveis diferentes de falhas, técnicas para lidar com-los e arquitecturas que suportam estas técnicas. Esta informação fornece uma entrada para os processos de recuperação de falhas e procedimentos, para garantir a que sua estratégia de recuperação de falhas funciona corretamente e eficiente.

##<a name="characteristics-of-resilient-cloud-applications"></a>Características de aplicações e são na nuvem

Uma aplicação bem concepção pode suportar falhas de capacidade num nível de táctico e também tolerar estratégicas falhas de todo o sistema ao nível de região. As secções seguintes definem a terminologia referenciada ao longo do documento para descrever vários aspetos das serviços em nuvem e são.

###<a name="high-availability"></a>Disponibilidade de alta

Uma aplicação de nuvem altamente disponível implementa estratégias para absorve indisponibilidade de dependências, como o serviços geridos disponibilizadas pela plataforma na nuvem. Apesar de possíveis falhas de capacidades a plataforma na nuvem, esta abordagem permite a aplicação para continuar a apresentam as características sistémicas esperadas funcionais e não funcional. Isto é abrangido aprofundada na série de 9 de canal de vídeo [isenta de falhas: seta de quatro pontas e são nuvem arquitecturas](https://channel9.msdn.com/Series/FailSafe).

Quando implementar a aplicação, tem de considerar a probabilidade de uma falha de funcionalidade. Para além disso, considere o impacto que vai ter uma falha na aplicação da perspetiva empresas, antes de consultar profundas estratégias de implementação. Sem conclusão considerações para o impacto de negócio e a probabilidade de atingir a condição de risco, a aplicação pode ser dispendioso e potencialmente desnecessários.

Considere era semelhante para elevada disponibilidade. Mesmo peças de qualidade e engenharia superior não impede que falhas ocasionais. Por exemplo, quando o seu carro obtém um pneus simples, o carro ainda é executado, mas está a funcionar com a funcionalidade degradada. Se planeado para esta ocorrência potencial, pode utilizar uma desses pneus reserva com margens de espessura fina e até chegar um centro de cópias de reparação. Apesar da reserva pneus não lhe permite velocidades rápidas, pode continuar a funcionar veículo até que substitui a pneus. Da mesma forma, um serviço na nuvem que potenciais perda de funcionalidades de planos pode impedir que um problema relativamente secundário colocá-se para baixo a aplicação completa. Este é verdadeiro, mesmo se o serviço de nuvem tem de executar com a funcionalidade degradada.

Existem alguns características principais dos serviços em nuvem altamente disponíveis: disponibilidade, escalabilidade e tolerância a falhas. Apesar destas características estão interligadas, é importante compreender a cada e, como se contribuírem para a disponibilidade geral da solução.

###<a name="availability"></a>Disponibilidade

Uma aplicação disponível considera a disponibilidade dos seus infraestrutura subjacente e serviços dependentes. Aplicações disponíveis remover pontos de falha através de redundância e estrutura e são únicos. Quando alargar o âmbito a ter em consideração disponibilidade no Azure, é importante compreender o conceito da disponibilidade da plataforma eficaz. Disponibilidade eficaz considera acordos de nível de serviço (SLA) de cada serviço dependente e o respetivo efeito cumulativo sobre a disponibilidade de sistema total.

Disponibilidade do sistema é a medida da percentagem de uma janela de tempo que o sistema poderão funcionar. Por exemplo, a disponibilidade SLA de pelo menos mais duas instâncias de uma função web ou de trabalho no Azure é 99.95 percentagem (fora 100 por cento). Não-medir o desempenho ou a funcionalidade dos serviços em execução dessas funções. No entanto, a disponibilidade do seu serviço de nuvem eficaz também é afetada pela vários SLA de outros serviços dependentes. As partes mais mover dentro do sistema, o mais cuidado que deve seguir para garantir que a aplicação resiliently pode cumprir os requisitos de disponibilidade dos seus utilizadores finais.

Considere a seguinte SLA para um serviço Azure que utiliza o Azure serviços: cluster, base de dados do SQL Azure e armazenamento do Windows Azure.

|Serviço Azure|SLA   |Potenciais minutos tempo de inatividade/mês (30 dias)|
|:------------|:-----|:----------------------------------------:|
|Cluster      |99.95%|minutos 21,6                              |
|Base de dados SQL |99,99%|minutos 4.3                              |
|Armazenamento      |99.90%|minutos 43.2                              |

Tem de planear para todos os serviços para potencialmente Ir para baixo em alturas diferentes. Neste exemplo simplificado, o número total de minutos por mês que a aplicação dever-se para baixo é 108 minutos. Num mês de 30 dias tem um total de 43,200 minutos. minutos 108 é.25 percentagem do número total de minutos num mês de 30 dias (43,200 minutos). Isto dá-lhe uma disponibilidade eficaz de percentagem 99.75 para o serviço de nuvem.

No entanto, utilizar técnicas de disponibilidade descritas deste documento pode melhorar isto. Por exemplo, se conceber a sua aplicação para continuar a ser executado quando a base de dados do SQL não estiver disponível, pode remover que da equação. Isto poderá significa que a aplicação é executada com capacidades reduzidas, por isso, também são os requisitos de negócio a ter em conta. Para uma lista completa dos SLA Azure, consulte [Os acordos do nível de serviço](https://azure.microsoft.com/support/legal/sla/).

###<a name="scalability"></a>Escalabilidade

Escalabilidade diretamente afeta disponibilidade. Uma aplicação que falha em caso de carga maior já não se encontra disponível. Aplicações dimensionáveis que conseguem cumprir o maior procura com resultados consistentes, no windows aceitável de tempo. Quando um sistema estiver dimensionável, escalas horizontal ou verticalmente para gerir aumentos carga mantendo desempenho consistente. Em termos básicos, dimensionamento horizontal adiciona mais máquinas do mesmo tamanho (processador, memória e largura de banda), enquanto verticais aumentos dimensionamento o tamanho das máquinas existentes. Para Azure, tem de opções de dimensionamento verticais para selecionar vários tamanhos de máquina para cluster. No entanto, alterar o tamanho do computador necessita de uma implementação nova. Por conseguinte, as soluções mais flexíveis foram concebidas para o dimensionamento horizontal. Este é especialmente verdadeiro para cluster, uma vez que pode facilmente aumentar o número de instâncias de qualquer função web ou de trabalho em execução. Estas instâncias adicionais processam maior tráfego através do portal do Azure Web, scripts de PowerShell ou código. Base a esta decisão nos aumentos métricas monitorizadas específicos. Neste cenário, o desempenho do utilizador ou métricas não sofrem uma descida evidente em caso de carga. Normalmente, as funções web e trabalhador armazenam qualquer Estado externamente. Isto permite balanceamento de carga flexível e com êxito processamento de quaisquer alterações a instância contagens. Dimensionamento horizontal também funciona bem com serviços, como o armazenamento do Windows Azure, que não fornecem opções em camadas para dimensionamento vertical.

Nuvem implementações devem ser visualizadas como uma coleção de unidades da escala. Esta opção permite-a aplicação esteja flexível na manutenção as necessidades de débito dos utilizadores finais. As unidades de escala são mais fácil de visualizar ao nível do servidor web e de aplicação. Esta é uma vez que Azure já fornece nós de cluster sem estado através de funções web e trabalhador. Adicionar que mais unidades de escala para a implementação de calcular não irão causar efeitos lado da gestão de estado qualquer aplicação, porque são sem estado cluster unidades da escala. Uma unidade de escala de armazenamento é responsável pela gestão de uma partição de dados (structured ou não estruturados). Exemplos de armazenamento unidades da escala incluem partição de tabela do Azure, contentor de Blobs do Azure e base de dados do SQL Azure. Mesmo a utilização de várias contas de armazenamento do Windows Azure tem um impacto directo escalabilidade de aplicação. É necessário estruturar um serviço de nuvem altamente dimensionáveis para incorporar vários armazenamento escala unidades. Por exemplo, se uma aplicação utiliza dados relacionais, partição os dados em várias bases de dados do SQL. Fazê-lo permite que o armazenamento para acompanhar o modelo de unidade de escala cluster flexível. Da mesma forma, armazenamento do Windows Azure permite criar a partições esquemas que requerem deliberadas designs para satisfazer as necessidades de débito da camada cluster de dados. Para obter uma lista das melhores práticas para criar serviços em nuvem dimensionáveis, consulte o artigo [Melhores práticas para os serviços de estrutura de em larga escala no Azure serviços em nuvem](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

###<a name="fault-tolerance"></a>Tolerância a falhas

Aplicações devem partem do princípio de que cada funcionalidade nuvem dependentes possa e irão para baixo em algumas ponto na hora. Uma aplicação de tolerância a falhas de falhas Deteta e manobras à volta de falhados elementos, para continuar e devolver os resultados corretos dentro de um período definido específico. Breves condições de erro, um sistema de tolerância a falhas de falhas irá empregam uma política de repetir. Para mais graves falhas, a aplicação pode detetar problemas e sobre a falhar hardware alternativo ou planos até que seja corrigida a falha. Uma aplicação fiável corretamente pode gerir a falha de uma ou mais peças e continuar a funcionar corretamente. Aplicações de tolerância a falhas de falhas podem utilizar estratégias de estrutura de uma ou mais, tal como redundância, replicação ou uma funcionalidade degradada.

##<a name="disaster-recovery"></a>Recuperação de falhas

Uma implementação de nuvem poderá deixa de funcionar devido a uma falha de sistémica dos serviços dependentes ou a infraestrutura de subjacente. Em tais condições, de um plano de continuidade do negócio accionadores o processo de recuperação de falhas. Este processo envolve normalmente pessoal operações e procedimentos automatizados para reativar a aplicação numa região disponível. Isto requer que a transferência dos utilizadores da aplicação, dados e serviços à região de novo. Envolva também a utilização de multimédia de cópia de segurança ou replicação em curso.

Considere a analogia anterior que comparado com elevada disponibilidade para a capacidade de recuperar a partir de uma simples pneus através da utilização de reserva. Em contrapartida, recuperação de falhas envolve os passos tomados após uma falha de carro, onde o carro já não se encontra operacional. Nesse caso, a melhor solução é localizar uma forma eficaz para alterar carros, ao contactar um serviço de viagem ou um amigo. Neste cenário, existem provável que vai ser um atraso mais longo em introdução novamente em viagem. Também existe mais complexidade no reparar e voltar à veículo original. Da mesma forma, recuperação de falhas para outra região é uma tarefa complexa que normalmente envolve algum tempo de inatividade e a possível perda de dados. Para compreender e avaliar estratégias de recuperação de falhas melhor, é importante definir dois termos: objectivo de tempo de recuperação (RTO) e recuperação do ponto de objectivo (RPO).

###<a name="recovery-time-objective"></a>Objectivo de tempo de recuperação

O RTO é a quantidade máxima de tempo atribuído para restaurar a funcionalidade da aplicação. Isto é baseado no necessidades de negócio e está relacionada com a importância da aplicação. As aplicações comerciais críticas requerem uma RTO baixa.

###<a name="recovery-point-objective"></a>Objectivo de ponto de recuperação

O RPO é a janela de tempo aceitável de dados perdidas devido o processo de recuperação. Por exemplo, se o RPO for uma hora, tem completamente fazer cópia de segurança ou criar uma réplica os dados, pelo menos, cada hora. Uma vez que ativar a aplicação numa região alternativa, os dados de cópia de segurança podem estar em falta até uma hora de dados. Como RTO, aplicações críticas assinale uma quantidade RPO mais pequeno.

##<a name="checklist"></a>Lista de verificação

Vamos resumir os pontos-chave que tenham sido abrangidos neste artigo (e o respetivos artigos relacionados no [elevada disponibilidade](./resiliency-high-availability-azure-applications.md) e [recuperação de falhas](./resiliency-disaster-recovery-azure-applications.md) para aplicações do Azure). Este resumo irá funcionar como uma lista de verificação dos itens, que deverá tomar em consideração para o seu próprio disponibilidade e planeamento de recuperação de falhas. Estas são as práticas recomendadas que tenham sido útil para clientes que procuram obter grave sobre como implementar uma solução efetuada com êxito.

1. Realize uma avaliação de riscos para cada aplicação, uma vez que cada pode têm requisitos diferentes. Algumas aplicações são críticas mais do que as outras e seriam justificar extra para arquitectá-los para recuperação de falhas de custo.
1. Utilize estas informações para definir a RTO e RPO para cada aplicação.
1. Design de falha, começando a arquitetura de aplicação.
1. Implementar melhores práticas para elevada disponibilidade, enquanto balanceamento de custo, a complexidade e risco.
1. Implementar os planos de recuperação falhas e processos.
  * Considere falhas que aparecem em nível do módulo tudo para uma falha na nuvem concluída.
  * Estabelece estratégias de cópia de segurança para todos os dados transaccionais e de referência.
  * Selecione uma arquitetura de recuperação de falhas de múltiplos sites.
1. Defina um proprietário específico para processos de recuperação de falhas, automatização e testar. O proprietário deve gerir e o proprietário do todo o processo.
1. Os processos do documento para que fiquem facilmente possa ser repetidas. Apesar de existir um proprietário, várias pessoas deverá conseguir compreender e siga os processos em caso de emergência.
1. Prepare o pessoal para implementar o processo.
1. Utilize simulações falhas normal para a formação e validação do processo.

##<a name="summary"></a>Resumo

Quando o hardware ou aplicações não dentro Azure, são diferentes das quando ocorre falha em sistemas no local a técnicas e estratégias para geri-los. O principal motivo para isto é que as soluções de nuvem normalmente têm mais dependências no infraestrutura distribuída ao longo de uma região Azure e que gerido como serviços separados. Tem mais fácil lidar com falhas parciais utilizando técnicas de elevada disponibilidade. Para gerir falhas mais extremas, possivelmente devido a um evento de falhas, utilize estratégias de recuperação de falhas.

Azure Deteta e processa falhas muitos, mas existem vários tipos de falhas que requerem estratégias específicos da aplicação. Ativamente tem de se preparar para e gerir as falhas de aplicações, serviços e dados.

Ao criar a sua aplicação disponibilidade e plano de recuperação de falhas, considere as consequências empresas de falha da aplicação. Definir os processos, políticas e procedimentos para restaurar os sistemas críticos depois de um evento grave demora tempo, planeamento e compromisso. E depois de estabelecer os planos, não é possível impedir aí. Tem de analisar regularmente, testar e continuamente a melhorar os planos baseados no seu portefólio de aplicação, as necessidades da empresa e as tecnologias disponíveis para si. Azure Deteta novos desafios para a criação de desenvolver aplicações que suportar falhas e fornece capacidades de novas.

##<a name="additional-resources"></a>Recursos adicionais

[Disponibilidade elevada para as aplicações criadas no Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Recuperação de falhas para aplicações criadas no Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Orientação técnica do Azure RDP](resiliency-technical-guidance.md)

[Descrição geral: Na nuvem recuperação de falhas de bases de dados e continuidade empresas com base de dados SQL](../sql-database/sql-database-business-continuity.md)

[Recuperação de disponibilidade e falhas alta para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[Isenta de falhas: Seta de quatro pontas arquitecturas de nuvem e são](https://channel9.msdn.com/Series/FailSafe)

[Melhores práticas para a estrutura de serviços em grande escala no Azure serviços em nuvem](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

##<a name="next-steps"></a>Próximos passos

Este artigo faz parte de uma série de artigos com o foco nas recuperação de falhas e elevada disponibilidade para aplicações do Azure. O seguinte artigo nesta série é [elevada disponibilidade para as aplicações criadas no Microsoft Azure](resiliency-high-availability-azure-applications.md).
