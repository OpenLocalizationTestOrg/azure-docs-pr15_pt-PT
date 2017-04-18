<properties
   pageTitle="Noções sobre microservices | Microsoft Azure"
   description="Uma descrição geral do porque a criação de aplicações na nuvem com uma abordagem de microservices é importante para o desenvolvimento de aplicações modernos e como Azure Service ferro fornece uma plataforma para alcançar Isto"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="mfussell"/>

# <a name="why-a-microservices-approach-to-building-applications"></a>Por que motivo um microservices abordagem à criação de aplicações?
Como os programadores de software, não existe nada novo no como podemos pense factoring uma aplicação em partes de componente. É o paradigma central de orientação de objeto, captações de software e sistema de componentes. Hoje em dia, este factorization tenda a assumir a forma de classes e as interfaces entre bibliotecas partilhadas e camadas de tecnologia de apoio. Normalmente, uma abordagem por níveis é disponibilizada com um arquivo de back-end, lógica empresarial camada e uma IU front-end. O que *tem* alterado ao longo dos últimos anos é que recomendamos, como os programadores, está a criar aplicações distribuídas para a nuvem, condicionada pelo negócio.

As alteração as necessidades da empresa são:

- Criar e trabalhar com um serviço na escala para poder atingir os clientes no novos regiões geográficas (por exemplo).
- Entrega mais rápida das funcionalidades e capacidades para conseguir responder a pedidos de cliente de uma forma ágil.
- Utilização de recursos melhoradas para reduzir os custos.

Estes necessidades da empresa os estão a afetar *como* podemos criar aplicações.

Para mais informações sobre abordagem instrumental do Azure ao microservices, leia o artigo [Microservices: uma rotação de aplicação recorrendo a nuvem](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolítico vs. microservice abordagem de design
Todas as aplicações evoluem ao longo do tempo. Aplicações com êxito evoluem por a ser úteis para as pessoas. Aplicações sem êxito não evoluir e eventualmente são preteridas. A pergunta torna-se: quantidade sabe sobre os requisitos de hoje e o que vão estar no futuro? Por exemplo, vamos supor que está a criar uma aplicação para um departamento de elaboração de relatórios. Tiver a certeza de que a aplicação vai permanecer no âmbito da sua empresa e que os relatórios serão curto. Escolher abordagem é diferente, diga construir um serviço para fornecer o conteúdo de vídeo a dezenas de milhões de clientes. Por vezes, a introdução algo fora da porta como prova de conceito é fator de condução, com o conhecimento de que a aplicação pode ser recriada mais tarde. Existe pequeno ponto de excesso de engenharia algo que nunca é utilizado. É o compromisso engenharia habitual. Por outro lado, quando empresas falar sobre modular para o cloud, a expetativa é crescimento e a utilização. O problema é que o crescimento e a escala são inesperados. Gostaríamos possam protótipo rapidamente apesar de também saber que temos num caminho para lidar com sucesso futuro. Esta é a abordagem de arranque lean: construir, medir, saiba, iteração.

Durante a ARA de cliente / servidor, podemos procurava concentrar-se na criação de aplicações em camadas utilizando tecnologias específicas em cada camada. O termo de aplicação "monolítico" tem eclodidas para estas abordagens. As interfaces procurava estar entre as camadas e, mais intimamente ao forma estrutura utilizada entre componentes dentro de cada camada. Os programadores concebido e estimadas são aplicadas classes compiladas num bibliotecas, interligadas em alguns executáveis e DLLs. Existem vantagens para uma abordagem de estrutura monolítico. Muitas vezes é mais simples estruturar o e tem mais rápidas chamadas entre componentes, uma vez que estas chamadas são geralmente sobre IPC. Além disso, todos os testes de um único produto, tem tendência para ser mais pessoas-recurso eficiente. A desvantagem é que uma ligação justa entre resultados de camadas em camadas e não consigo dimensionar componentes individuais. Se necessitar de efetuar correções ou atualizações, terá de esperar que as outras pessoas concluir a sua testes. É mais difícil ser ágil.

Microservices endereço estes downsides e mais atentamente alinharem com os requisitos de empresas anterior, mas também têm upsides e downsides. Os benefícios da microservices são que cada um deles normalmente contém mais simples da funcionalidade de negócio, que Dimensionar para cima ou para baixo, teste, implementar e gerir de forma independente. Uma das vantagens importante de uma abordagem de microservice é que as equipas condicionadas mais por cenários para empresas que por tecnologia que essa abordagem encoraje. Na prática, o mais pequenas equipas desenvolvem um microservice com base num cenário de um cliente, utilizar tecnologias de optarem. Por outras palavras, não precisa de organização normalizar tech para manter monolíticos aplicações. Equipas individuais que serviços próprios podem fazer o que faz sentido para os mesmos com base em conhecimentos de equipa ou o que é mais adequado para o problema ser resolvido. Na prática, está a ter um conjunto de tecnologias recomendadas, tal como uma determinada NoSQL loja ou web quadro de aplicação, é preferível.

A desvantagem de microservices é recebida gerir o maior número de entidades separadas e trabalhar com implementações mais complexas e controlo de versões. Aumenta o tráfego de rede entre o microservices assim como as correspondente latências da rede. Ter muitas chatty, serviços granulares é uma receitas para um pesadelo em termos de desempenho. Sem necessidade de ferramentas para o ajudar a ver estas dependências, é difícil de todo o sistema de "ver". Os padrões de são o que fazer a abordagem microservice escolar ao concordar sobre como comunicar e a ser tolerância a falhas de apenas coisas que precisa de um serviço em vez de rígido contratos. É importante definir adiantado estes contactos na estrutura da, uma vez que serviços atualizar independentemente umas das outras. Descrição: outra criada para estruturar com uma abordagem de microservices é "SOA extensivamente."


***Na sua forma mais simples, a abordagem de estrutura de microservices é sobre uma desacoplados Federação dos serviços, com alterações independentes a cada e aceitou após padrões para a comunicação.***


Como são produzidas mais aplicações na nuvem, pessoas verificou que este decomposição da aplicação geral para serviços independentes, com foco nos cenário uma abordagem melhor longo prazo.

## <a name="comparison-between-application-development-approaches"></a>Comparação entre abordagens de desenvolvimento de aplicações

![Desenvolvimento de aplicações do serviço ferro plataforma][Image1]

1. Uma aplicação monolítico contém funcionalidades específicas do domínio e normalmente é dividida por camadas funcionais, como web, empresas e dados.

2. Dimensionar uma aplicação monolítico por cloná-lo em servidores/VMs/contentores de múltiplos.

3. Uma aplicação microservice separa funcionalidade serviços mais pequenos separados.

4. Esta abordagem escalas saída ao implementar cada serviço de forma independente, criação de instâncias de um destes serviços nos servidores/VMs/contentores.


Estruturar com um microservice abordagem não é uma panacea para todos os projetos, mas mais atentamente alinhar com os objectivos empresas descritos anteriormente. Começar com uma abordagem monolítico poderão ser aceite se souber que mais tarde não terá a oportunidade de Reformule as o código para uma estrutura de microservice se for necessário. Mais frequentemente, que começam com uma aplicação monolítico e lentamente interrompê-la para cima fases, começando com as áreas funcionais que tem de ser mais dimensionáveis ou ágil.

Resumindo, a abordagem de microservice é para compor a aplicação de muitos serviços mais pequenos.  Os serviços executados em contentores implementados através de um cluster de máquinas. Mais pequenas equipas desenvolver um serviço que foca-se num cenário e testar a forma independente, versão, implementar e dimensionar cada serviço para que a aplicação como um todo pode evoluir.

## <a name="what-is-a-microservice"></a>O que é um microservice?

Existem diferentes definições do microservices e procurar na Internet fornece muitos recursos de boas fornecem os seus próprios pontos de vista e definições. No entanto, a maior parte das seguintes características do microservices amplamente é aceitou relativamente a:

- Incorporar um cenário de negócio ou de cliente. O que é o que é a resolução de problemas?
- Desenvolvido por uma equipa de engenharia pequenas.
- Escrita em qualquer linguagem de programação e utilizar qualquer quadro.
- Consistem código e (opcionalmente) Estado, as duas situações que forem independentemente versões, implementado e dimensionada.
- Interagir com outros microservices sobre interfaces bem definidas e protocolos.
- Têm nomes exclusivos (URLs) utilizados para resolver a sua localização.
- Manter-se disponível na presença de falhas e consistentes.

Pode resumindo estas características para:

***Aplicações de Microservice são compostos por pequenas, independentemente com versões dimensionáveis com foco nos cliente dos serviços e que comunicarem com os outros sobre protocolos padrão com interfaces bem definidos.***


Vamos coberta os dois primeiros pontos na secção anterior e agora podemos expandir no e clarificar as outras.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Escrita em qualquer linguagem de programação e utilizar qualquer quadro
Como os programadores, podemos deve ser gratuitos escolher o idioma ou framework queremos, dependendo do nossos aptidões ou as necessidades do serviço. Em alguns serviços, poderá valor as vantagens de desempenho da C++ acima de todos os pessoa. Em outros serviços, a facilidade de desenvolvimento gerido no c# ou Java poderá ser mais importante. Em alguns casos, poderá ter utilizar uma biblioteca de terceiros específica, a tecnologia de armazenamento de dados ou a meio de expor o serviço de clientes.

Depois de ter escolhido uma tecnologia que entram para a gestão operacional ou ciclo de vida e de dimensionamento do serviço.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que o código e de estado para ser independentemente versões, implementado e dimensionada  

No entanto escolher escrever o seu microservices, o código e, opcionalmente, o estado deve independentemente implementar, atualizar e dimensionar. Este realmente é um dos problemas mais difícil para resolver, uma vez que é fornecido para baixo para a sua escolha de tecnologias. Para compreender o dimensionamento, como a partições (ou shard) o código e o estado é um desafio. Quando o código e o estado utilizarem tecnologias distintas (que hoje estas opções têm tendência para fazer), os scripts de implementação para sua microservice tem de ser conseguir face dimensionamento-los ambos. Esta será também sobre agilidade e flexibilidade, pelo que pode atualizar algumas da microservices sem ter de atualizá-las todas ao mesmo tempo.
Voltar à monolítico versus microservice abordagem para um pouco a incorporá, o diagrama seguinte mostra as diferenças entre a abordagem para armazenar estado.

#### <a name="state-storage-between-application-styles"></a>Armazenamento de estado entre os estilos de aplicação
![Armazenamento de estado do serviço ferro plataforma][Image2]

***No lado esquerdo é a abordagem monolítico, com uma única base de dados e camadas de tecnologias específicas.***

***No lado direito é a abordagem microservices, um gráfico de microservices interligados onde Estado normalmente está confinado ao microservice e várias tecnologias são utilizadas.***

Numa abordagem monolítico, normalmente, há uma base de dados única utilizado pela aplicação. A vantagem é que é uma única localização, tornando mais fácil implementar. Cada componente pode ter uma única tabela para armazenar o seu estado. Equipas precisam de ser estritamente no separar Estado, que é um desafio. Existem inevitavelmente temptations para adicionar uma nova coluna a uma tabela existente do cliente, efetue uma associação entre tabelas e criar dependências na camada de armazenamento. Assim que isto acontece, não consigo dimensionar componentes individuais. Na abordagem microservices, cada serviço gere e armazena os suas próprias estado. Cada serviço é responsável por dimensionamento código e Estado em conjunto para satisfazer os pedidos de serviço. Uma desvantagem é que quando existe uma necessidade de criar vistas ou consultas, dos dados da sua aplicação será necessário consulta em estado diversificado stores. Normalmente, isto é resolvido por ter um microservice separada que cria uma vista através de uma coleção de microservices. Se necessitar de efetuar várias consultas ad-hoc nos dados, o cada microservice deverá tomar em consideração ao escrever os respetivos dados para um armazenamento serviço para offline análise de dados.

Controlo de versões é específico para a versão de um microservice implementada por isso, esse múltiplas versões diferentes implementar e executar lado a lado. Controlo de versões aborda os cenários onde uma versão mais recente de um microservice falha durante a atualização e precisa de recuperar uma versão anterior. O outro cenário para controlo de versões está a executar A/B-estilo testes, onde diferentes utilizadores uma experiência de versões diferentes do serviço. Por exemplo, é comum para atualizar um microservice para um conjunto específico de clientes para testar a nova funcionalidade antes de gradual muito mais. Depois de gestão de ciclo de vida de microservices, este agora dá-ao comunicação entre elas.


### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interage com outros microservices sobre interfaces bem definidas e protocolos

Este tópico necessita de atenção pequeno aqui, uma vez que existe Bibliografia extensa no serviço orientados arquitetura publicada dos últimos 10 anos que descreve os padrões de comunicação. Em geral, comunicação do serviço de utiliza uma abordagem de resto com protocolos HTTP e TCP e XML ou JSON como o formato de serialização. Da perspetiva interface, é sobre cubra a abordagem de estrutura da web. Mas não há nada impedi-lo de utilizar protocolos binários ou o seus próprio formatos de dados. Ser preparado para pessoas tenham uma hora mais difícil através do seu microservices se estes estão disponíveis aberta.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tem um nome exclusivo utilizado para resolver a sua localização (URL)

Lembre-se como podemos manter a dizer que a abordagem microservice é como web? Tal como na web, o seu microservice precisa de ser endereçáveis esteja onde estiver em execução. Se estiver a pensar sobre máquinas e qual deles está a ser executado um nomeadamente microservice, coisas subscreve incorretas rapidamente. Da mesma forma que o DNS resolve um URL específico para uma determinada máquina, o seu microservice tem de ter um nome exclusivo para que a sua localização atual é detetável. Microservices necessitam de ter endereçáveis nomes que torná-los independentemente da infraestrutura de que estão a ser executados no. Isto implica que existe uma interação entre como o seu serviço é implementado e como como é descoberta, uma vez que precisa de ser um registo de serviço. Igualmente, quando uma máquina de falha do registo serviço tem indicar-lhe onde o serviço está a ser executado. Isto dá-ao tópico seguinte: recuperação e consistência.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Permanece consistente e disponível na presença de falhas

Lidar com falhas inesperadas é um das problemas a resolver, sobretudo num sistema distribuído. Muitas do código que vamos escrever como os programadores está a processar exceções e isto também é onde mais tempo despendido a testar. Mas o problema é mais complexo que escrever código para processar falhas. O que acontece quando o computador onde está a ser executado o microservice falha? Não só precisa de detetar esta falha microservice (um problema de disco rígido na sua própria), mas também precisa de algo para reiniciar o seu microservice. Um microservice tem de ser e são a falhas e reinicie frequentemente noutro computador por razões de disponibilidade. Isto também é fornecido para baixo para a qual o estado que foi guardado em nome de microservice, onde pode-recuperar este estado a partir de e, se for capaz de reiniciar com êxito. Por outras palavras, existem tem de ser resistência no cluster (o processo for reiniciado), bem como resistência no Estado ou dados (sem perda de dados e os dados permanece consistente).

Os problemas de RDP são adicionados durante a outros cenários, tal como quando falhas ocorreram durante uma atualização de aplicação. Microservice, trabalhar com o sistema de implementação, não é necessário recuperar. Também precisa de, em seguida, decida se pode continuar avançar para a versão mais recente ou em vez disso reverter para uma versão anterior para manter um estado consistente. A perguntas como se existem suficiente máquinas disponíveis para manter mover reencaminhar e como recuperar versões anteriores da necessidade de microservice tidos em conta. Isto requer que microservice a emitir informações de estado de funcionamento possam efetuar estas decisões.

### <a name="reports-health-and-diagnostics"></a>Estado de funcionamento de relatórios e diagnósticos de

Parecer óbvia e frequentemente está a ser negligenciada, mas é que um microservice relatórios do seu estado de funcionamento e diagnósticos de essencial. Caso contrário, é pouco conhecimentos aprofundados uma perspetiva de operações. É um desafio correlacionar eventos de diagnóstico ao longo de um conjunto de serviços independentes e trabalhar com máquina relógio Torce para fazer sentido da ordem de evento. Da mesma forma que interagem com um microservice sobre aceitou após protocolos e formatos de dados, existem conclui uma necessidade de normalização no como iniciar sessão Estado de funcionamento e diagnóstico eventos que terminem finalmente para cima uma loja de evento para consultar e visualizar. Numa abordagem de microservices, é chave que as equipas de diferentes concordem sobre um formato de registo único.  Não existem tem de estar uma abordagem consistente para visualizar os eventos de diagnóstico na aplicação como um todo.

Estado de funcionamento é diferente do diagnóstico. Estado de funcionamento é de cerca microservice reportar o estado atual para efetuar ações adequadas. Um bom exemplo está a trabalhar com mecanismos de atualização e implementação para manter a disponibilidade. Um serviço poderá estar atualmente danificado devido a uma falha de sistema do processo ou reiniciar o computador, mas ainda operacionais de computador. É a última coisa que precisa para torná-piores ao efetuar uma atualização. É a melhor abordagem fazer um inquérito em primeiro lugar, ou permitir tempo para microservice recuperar. Eventos de estado de funcionamento de uma microservice permitem-nos tomar decisões informadas e, na verdade, ajudar a criar serviços de reparação personalizada.

## <a name="service-fabric-as-a-microservices-platform"></a>Serviço ferro como uma plataforma microservices

Azure Service ferro eclodidas terminar transição da Microsoft a partir de fornecer produtos de caixa, que foram normalmente monolíticos no estilo, a entrega de serviços. A experiência de criação e de funcionamento serviços grandes, como bases de dados Azure SQL e DocumentDB, em forma de serviço ferro. A plataforma evoluiu um ao longo do tempo, tal como mais serviços aprovadas-lo. Importante, o serviço ferro tinha que executar qualquer lugar: não apenas no Azure, mas também em implementações do Windows Server autónomo.

***O serviço ferro objectivo resolver os problemas de disco rígidos de criação e execução de um serviço e utilizam os recursos de infraestrutura de forma eficaz, para que as equipas podem resolver problemas de empresas utilizando uma abordagem de microservices.***

Serviço ferro fornece duas áreas abrangentes para ajudar a criar aplicações com uma abordagem de microservices:

- Ocorreu uma falha com uma plataforma que fornece serviços do sistema para implementar, atualizar, detetar e reinicie o serviços, descobrir a localização do serviço, gerir o estado e monitorizar o estado de funcionamento. Estes serviços do sistema em efeito ativar muitos das características da microservices descrito anteriormente.

-  APIs de programação ou quadros, para ajudar a criar aplicações como microservices: [intervenientes fiáveis e serviços fiáveis](service-fabric-choose-framework.md). Obviamente, pode utilizar qualquer código da sua escolha para criar o seu microservice. Mas estas APIs tornar a tarefa mais simples e elas integram com a plataforma num nível mais aprofundada. Desta forma, por exemplo, poderá obter informações de estado de funcionamento e diagnósticos de ou pode tirar partido de elevada disponibilidade incorporado.

***Serviço ferro é desconhecido sobre como criar o seu serviço e pode utilizar qualquer tecnologia. No entanto, fornecida APIs programação incorporados que tornam mais fácil criar microservices.***

### <a name="are-microservices-right-for-my-application"></a>São microservices para a direita para a minha aplicação?

Talvez. O que recomendamos ocorreu ao foi que como mais equipas no Microsoft começaram construir para o cloud por razões de negócio, muitos deles realizados os benefícios da demorar uma abordagem de microservice semelhante. Bing, por exemplo, tenha sido desenvolver microservices na pesquisa de anos. Para outras equipas a abordagem microservices era nova. Estes encontram que não existem foram problemas de disco rígidos para resolver fora respectivas áreas principais de rutura. Este é o motivo pelo qual o serviço ferro adquirida motoras como a tecnologia de seleção para a criação de serviços.

Objectivo de serviço ferro é reduzir a complexidade da criação de aplicações com uma abordagem de microservice, para que não tenha seguir como muitos dispendioso redesigns. Inicie o pequeno, dimensionar quando for necessário, preterir serviços, adicione novos e evoluir com a utilização de cliente – que é a abordagem. Recomendamos também saber que na realidade não existem muitos outros problemas ainda para ser resolvido para tornar o microservices mais acessível para os programadores a maior parte dos. Contentores e o modelo de programação do ator alguns exemplos de passos pequenos nessa direcção e estamos a certeza de que inovações mais surgirá para facilitar a esta.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

* Para obter mais informações:
    * [Descrição geral de terminologia ferro de serviço](service-fabric-technical-overview.md)
    * [Microservices: Uma aplicação rotação recorrendo a nuvem](https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/)


[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
