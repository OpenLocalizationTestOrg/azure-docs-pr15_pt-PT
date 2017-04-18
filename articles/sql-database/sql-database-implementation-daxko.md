<properties
   pageTitle="Base de dados do Azure SQL Azure caso prático da Microsoft - Daxko/CSI | Microsoft Azure"
   description="Saiba mais sobre como Daxko/CSI utiliza a base de dados SQL para acelerar o ciclo de desenvolvimento e para melhorar os serviços de cliente e o desempenho"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>
   
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI utilizado Azure para acelerar o ciclo de desenvolvimento e para melhorar os serviços de cliente e o desempenho

![Logótipo de Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Software Daxko/CSI são confrontadas um desafio: sua base de cliente de manutenção física e recreio centros de foi crescimento rapidamente, graças ao sucesso das sua solução enterprise-software abrangente, mas manter com as necessidades de infraestrutura de TI para esse cliente crescente base foi testes da empresa a equipa de TI. Na empresa foi cada vez mais limitada pelo crescente overhead de operações, especialmente para gerir o respetivos crescentes bases de dados. Pior, esse sobrecarga operações foi cortar para recursos de desenvolvimento para novas iniciativas, como novas funcionalidades de mobilidade para software da empresa.

De acordo com David Molina, Director de desenvolvimento de produtos na Daxko/CSI, Azure CSI Software fornecido com o modelo de (PaaS) de plataforma-como-a-service-lo conforme necessário para simplificar a gestão de base de dados, aumentar a escalabilidade e libertar recursos focar-se em software em vez de ops. "Base de dados SQL azure foi uma ótima opção para-nos. Não ter de se preocupar manutenção de SQL Server, um cluster de activação pós-falha e todas as necessidades de outros infraestrutura do não foi ideal para-nos."

Desde a migrar para o Azure, Software de CSI necessita de um pessoal de operações de apenas dois para gerir mais de 600 bases de dados do cliente. A empresa utiliza agrupamentos flexível de base de dados do Azure SQL para mover bases de dados do cliente com base no tamanho e precisa de.

Molina persistir, "dos nossos clientes sentir a alteração imediatamente. Antes de agrupamentos flexível, ocasionalmente tinham tempos limite e outros problemas durante a períodos de rajada. Com os agrupamentos flexível Azure, podem rajada conforme necessário e utilizar o software sem problemas."

Para além de melhorar o desempenho de clientes, elásticos Azure da base de dados agrupamentos de recursos de CSI Software focar-se em desenvolver novos serviços e funcionalidades, em vez de lidar com a gestão e operações disponibilizados. Os recursos de TI contribuiu CSI Software melhorar o seu software de empresa oferece o, SpectrumNG, para ajudar a contrate membros ginásio, melhorar a eficiência de docentes e fornecer docentes e membros acesso móvel para tarefas interativas e notificações em tempo real.

Azure também contribuiu CSI Software acelerar e melhorar o desenvolvimento e o ciclo de qualidade (das perguntas e respostas) ao ativar opções de automatização. Com a implementação Azure da empresa, os gestores de compilação poderá compactar o componentes com o clique de um botão. Como Molina descreve, "como parte do ciclo de lançamento, as perguntas e respostas são agora possível implementar um ambiente de teste no Azure, que consiste imite nossa pilha de produção. Vamos pode implementar compilações imediatamente nosso ambiente do Dev Center alterações a uma análise aprofundada. É um grande win para os casos, porque não temos paridade para testes antes que."

## <a name="offloading-to-the-cloud"></a>Descarregar na nuvem

Antes de passar para a nuvem, CSI Software tinha criado com êxito o seu próprio infraestrutura multi-inquilino num centro de dados local no guarda. Tal como a empresa expandido e o são confrontadas crescente em crescimento u.s. c do comprar, aprovisionamento e manter todos os hardware e software necessário para suportar aos seus clientes. IT pessoal para processar operações ficou outra fonte congestionamento que levaram num abrandamento do aprovisionamento novos recursos e gradual saída novos serviços aos clientes.

CSI Software procurado para opções de nuvem para eliminando esse overhead, para que poderia focar-se no seu código, em vez das suas operações. A empresa descobriu muitos dos fornecedores de nuvem superior apenas oferecem soluções do infraestrutura-como-a-service (IaaS) que ainda requerem uma grande a equipa de TI para gerir a pilha de IaaS. No final, CSI Software determinado que a solução Azure PaaS foi os mais adequados para as suas necessidades. Explica Molina, "O Azure obtém o software de hardware e de sistema para fora da área de trabalho, para que recomendamos pode concentrar-se no nossas ofertas de software, enquanto reduzir o overhead nosso IT."

## <a name="making-the-transition-to-azure"></a>Fazer a transição para Azure

Após selecionar Azure para a sua solução PaaS, CSI Software começou migrar a sua infraestrutura de back-end e bases de dados para a nuvem. Antes de Azure, SpectrumNG clientes necessários para instalar uma aplicação de cliente comunicadas com um serviço Windows Communication Foundation (WCF) na back-end. De acordo com Molina, "Apesar de alguns clientes alojado tudo na suas própria centros de dados, criámos saída do produto ser multi-inquilino. Vamos alojado tudo num centro de dados no guarda, utilizando o SQL Server como o arquivo de dados.

"O nosso produto perguntar se também incluído um membro destinado web portal (escrito no ASP.net), que foi concebido para ser denominada branco para corresponder aos presença na web do cliente e uma API SOAP para suportar as páginas online e qualquer integração de terceiros."

A migração para a nuvem não teve longa para a arquitetura. De acordo com Molina, "A maioria dos esforço tratada modificar a forma como o podemos ler informações de ficheiro de configuração, modificação uma cadeia de ligação centralizada e automatizar da embalagem, carregar e implementação do nossos lançamentos."

Desenvolver a automatização de compilação, engenheiros CSI Software utilizado PowerShell do Azure e os REST APIs para criar pacotes e carregue-os para ambiente de teste para cada uma das noites de lançamento.
A transição geral para uma implementação baseada na nuvem Azure Ocorreu um bom e rapidamente para a equipa de TI de Software CSI. Explica Molina, "em todas as, tivemos um ambiente do beta na nuvem no prazo de três ou quatro semanas demorar no projeto. Que foi uma win surpreendente para dos e.u.a."

Depois de configurar e testar o ambiente, CSI Software começou migrar clientes. Para clientes que já utiliza o alojamento CSI Software, a transição foi quase totalmente integrada. Para clientes migrar a partir de uma implementação no local, a migração para a nuvem demorou algum tempo adicional, mas foi ainda principalmente cómodos para clientes e CSI Software.

Para do novos clientes, CSI Software a equipa de TI utilize o seguinte processo para bordo-los para Azure:

1.  Azure scripts de PowerShell são utilizadas para giratório para cima uma nova base de dados para o cliente; todos os clientes iniciar numa camada premium para se certificar de suficiente débito inicial para a transição.
2.  Sempre que possível, o Software de CSI utiliza o Assistente de migração do SQL Azure para mover dados existentes para uma instância de base de dados do Azure SQL.
3.  Por fim, Microsoft SQL Server Integration Services (SSIS) são utilizados para reconciliar quaisquer discrepâncias nos dados ou para executar qualquer limpeza de dados conforme necessário.

Hoje em dia, cerca de 99 por cento de clientes do Software de CSI estão alojados no Azure, através de quatro centros de dados em regionais (América do Norte Central, Sul Central, leste e oeste). Ao ter centros de dados na região geográfica cada cliente, latência é mantida para um mínimo.

## <a name="azure-elastic-database-pools-free-up-it-resources"></a>Agrupamentos de base de dados flexível Azure libertam recursos de TI

Várias funcionalidades do Azure tem Contribuiu CSI Software shift sejam infraestrutura e operações com foco para a ser funcionalidade e desenvolvimento com foco nos. Talvez o benefício maior foi a partir de agrupamentos de base de dados flexível.

CSI Software fornece atualmente cerca 550 bases de dados para os clientes. Antes de agrupamentos flexível, foi de difícil acesso gerir bases de dados que muitas dentro de uma estrutura de camadas. Gestores de OPS tinham que atribuir camadas de desempenho com base nas necessidades de clientes, que necessárias significativa IT-recurso sobrecarga rajada. Com os agrupamentos de base de dados flexível, os gestores de podem atribuir inquilinos uma premium ou um conjunto padrão, conforme adequado e, em seguida, mover clientes com base no tamanho e precisa de. Os clientes sentir os efeitos dos agrupamentos de base de dados flexível quase de imediato; antes de agrupamentos flexível, clientes tinham tempos limite e outros problemas durante a utilização de rajada períodos, mas com agrupamentos flexível, os clientes podem experimentar atividade rajada conforme necessário e podem continuar a utilizar SpectrumNG sem problemas.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure Active Geo-replicação acelera elaboração de relatórios

Vários clientes CSI Software também são tirar partido do Azure Active Geo-replicação. Com o Active Geo replicação, até quatro legíveis secundários bases de dados podem ser configuradas das regiões do Centro de dados do mesmo ou diferente. CSI Software faz com que utilize do Active Geo replicação de duas maneiras: em primeiro lugar, estão disponíveis no caso de uma falha de centro de dados ou a impossibilidade de ligar à base de dados principal; as bases de dados secundárias e em segundo lugar, as bases de dados secundárias são legíveis e podem ser utilizados para descarregar cargas de trabalho só de leitura como tarefas de elaboração de relatórios. Alguns clientes CSI Software utilizam este benefício para acelerar fluxos de trabalho de elaboração de relatórios.

## <a name="csi-software-application-logic-and-architecture"></a>Lógica de aplicação de CSI Software e de arquitectura

SpectrumNG utiliza funções da web. Porque a aplicação está com várias inquilino, um serviço WCF é utilizado para processar o pedido de ligação inicial de clientes. Como Molina diz, "o pedido identifica cada cliente, que permite-na criar uma cadeia de ligação de saída da suas bases de dados fazer tudo o que é necessário, em seguida,."

Para a camada de web do seu serviço, CSI Software tira partido Azure automática do dimensionamento da, com base no dia e a hora. Recursos disponíveis são automaticamente aumentados para acomodar o maior utilização durante o horário de negócio, de acordo com o fuso horário do cada centro de dados regional. Recursos também estão definidos para dimensionar para baixo no fins de semana, quando são inferiores às necessidades de cliente.

     
![Arquitetura de Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figura 1. Uma função de trabalho de serviços na nuvem desenha dados estruturados da base de dados do SQL Azure e semiestruturados dados a partir do armazenamento de tabela. Utilizadores SpectrumNG interagem com dados através de uma nuvem função web de serviços.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Utilizar aplicações web e uma camada web plano para aplicações móveis

Utilizando a base de dados do SQL Azure disponibilizado recursos para CSI Software para activar novas iniciativas, incluindo uma plataforma móvel completa com base numa API personalizada alojada no Azure web apps. A plataforma permite membros ginásio e docentes utilizar dispositivos móveis para verificar a agendas, classes do livro e receber mensagens.

A plataforma utiliza arquitetura orientados para serviços (SOA) para tirar um único componente — como um sistema de pontos de venda (posição) ou um sistema de vendas — movê-la no momento para outro plano de web e, em seguida, giratório para cima um serviço para suportar desse componente, deixando tudo sobre o plano de web original. Esta capacidade flexibilidade CSI Software imenso e ajuda a manter os custos para baixo.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure permite CSI Software os programadores concentrar-se nas aplicações e serviços

Base de dados SQL Azure não apenas um benefício aos clientes SpectrumNG, quem desfrutar o serviço rápido e fiável, também é uma grande win para Software de CSI a equipa de TI e os programadores. Ao descarregar ops para Azure na nuvem, CSI Software reduzido o overhead de recursos e infraestrutura, substancialmente, a aceleração respetivo ciclos de desenvolvimento e já não necessidades a micromanage bases de dados para otimizar o desempenho aos seus inquilinos.

## <a name="more-information"></a>Obter mais informações

- Para saber mais acerca de agrupamentos de base de dados flexível Azure, consulte o artigo [agrupamentos de base de dados flexível](sql-database-elastic-pool.md).

- Para saber mais sobre as ferramentas de base de dados e o dimensionamento flexível, consulte o artigo [dimensionamento flexível e de ferramentas da base de dados flexível](sql-database-elastic-scale-get-started.md).

- Para saber mais sobre como migrar uma base de dados do SQL Server, consulte o artigo [Assistente de migração do SQL Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).

- Para saber mais sobre Geo-replicação do Active, consulte o artigo [Geo-a replicação do Active](sql-database-geo-replication-overview.md).

- Para saber mais sobre as funções de Web e funções de trabalho, consulte [funções de trabalho](../fundamentals-introduction-to-azure.md#compute). 

- Para saber mais sobre Azure Service Bus, consulte o artigo [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).

- Para saber mais sobre Dimensionar automaticamente, consulte o artigo [dimensionamento serviços em nuvem](../cloud-services/cloud-services-how-to-scale.md).
