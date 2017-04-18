<properties
   pageTitle="Cenários comuns de catálogo de dados Azure | Microsoft Azure"
   description="Uma descrição geral de cenários comuns de Azure catálogo de dados, incluindo o registo e a deteção de origens de dados do valor máximo, ativar-service business intelligence e capturar dados de conhecimento Tribais existente sobre origens de dados e processos."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/03/2016"
   ms.author="maroche"/>


# <a name="azure-data-catalog-common-scenarios"></a>Cenários comuns de catálogo de dados Azure

Este artigo apresenta cenários comuns onde Azure catálogo de dados pode ajudar as organizações a obter maior valor a partir de origens de dados existente.

## <a name="scenario-1---registration-of-central-data-sources"></a>Cenário #1 - registo de origens de dados central

Organizações com frequência tiverem um número de origens de dados do valor máximo. Estas origens de dados incluem a linha de sistemas OLTP empresariais, armazém de dados e de business intelligence / bases de dados de análise. Muitas vezes o número de sistemas e a sobreposição entre os sistemas, cresce ao longo do tempo, como as necessidades da evolve negócio e, como o negócio propriamente dito evolução deste através de fusões e aquisições.

Muitas vezes é difícil para os utilizadores saber onde localizar dados dentro estas origens de dados. Perguntas como estas são todas as demasiado comuns:

- Dos três HR sistemas utilizados dentro da empresa, que devo utilizar para criar este tipo de relatório?
- Onde devem ir para obter os números das vendas certificados para ano fiscal que acabou de terminar?
- Quem deverá posso pedir a ou o que é o processo que devo utilizar para obter acesso ao armazém de dados?
- Não sei se estes números são adequados – quem pode posso pedir para conhecimentos aprofundados no como estes dados são suposto para ser utilizado antes deste dashboard posso partilhar com a minha equipa?

Neste cenário, pode ajudar a Azure catálogo de dados. As origens de dados central, valor alto, gerido IT que são utilizadas em toda a organização são, muitas vezes, o ponto de partida lógico para preencher o catálogo. Embora qualquer utilizador pode registar uma origem de dados, está a ter o catálogo kick-started com as origens de dados que são mais provável fornecer valor para o maior número de utilizadores ajudará a adoção unidade e a utilização do sistema. Para clientes introdução ao Azure catálogo de dados, identificar e registar as origens de dados de chave utilizadas pela muitas equipas diferentes dos consumidores de dados podem ser o primeiro passo para o sucesso das.

Este cenário também apresenta uma oportunidade para anotar as origens de dados do valor máximo para facilitar a compreender e aceder aos mesmos. Um aspecto chave deste esforço é incluem informações sobre como os utilizadores podem pedir acesso à origem de dados. Catálogo de dados Azure permite aos utilizadores fornecer o endereço de e-mail do utilizador ou equipa responsável para controlar o acesso à origem de dados, ligações para ferramentas existentes ou documentação ou texto livre que descreve o processo de pedido de acesso. Com esta informação incluída no catálogo, os utilizadores que detetar origens de dados registadas, mas que ainda não tem permissões para aceder aos dados facilmente podem pedir acesso utilizando os processos definidos e controladas pelos proprietários de origem de dados.

## <a name="scenario-2---self-service-business-intelligence"></a>Cenário #2 --service business intelligence

Apesar de soluções de informações da empresa de tradicional empresariais empresarial continuam a ser uma parte inestimável paisagens de dados de muitas organizações, a alteração ritmo de negócio fez BI de gestão personalizada mais importantes. BI de gestão personalizada permite que os técnicos de informação e analistas para criar os seus próprios relatórios, livros e dashboards sem depender de uma equipa IT central – ou a ser restringida por agenda e disponibilidade desse departamento de TI.

Cenários de BI de gestão personalizada, é comuns para os utilizadores combinar dados de várias origens, muitas das quais poderão não ter sido utilizadas anteriormente para BI e análise. Apesar de algumas das seguintes origens de dados já podem ser conhecidas, frequentemente existe um processo para descobrir o que deve ter lugar para localizar e avaliar potenciais origens de dados para uma determinada tarefa.

Normalmente, este processo de deteção é um manual: analistas utilizará suas ligações de rede ponto a ponto para identificar a outras pessoas que trabalham com os dados que está a ser procurados. Depois de uma origem de dados é encontrada pode ser utilizada, mas o processo repete-se novamente para cada subsequente personalizado BI esforço, com vários utilizadores efetuar o mesmo processo manual redundante da deteção.

Com o catálogo de dados do Azure, os utilizadores podem interromper este ciclo do esforço redundante. Assim que uma origem de dados foi descobriu através de meios tradicionais, um analista pode registar a origem de dados para o tornar mais facilmente identificável no futuro. Apesar do utilizador pode adicionar mais valor ao anotar os ativos de dados registadas, esta não precisa de tomar local ao mesmo tempo que o registo. Os utilizadores podem contribuir ao longo do tempo, como a sua licença de agendas, adicionar forma gradual valor às origens de dados registadas no catálogo.

Este orgânico crescimento de conteúdo de catálogo é complementar natural ao registo inicial de origens de dados central. Preencher previamente o catálogo de dados que vários utilizadores irão necessitar de pode ser uma motivator para utilização inicial e descoberta. Permitir que os utilizadores registar e anotar fontes adicionais pode ser uma forma para mantê-los e os seus colegas, interessados.

Também é notar que embora este cenário foca-se mais especificamente no BI de gestão personalizada, o mesmo padrões e desafios aplicam a em grande escala empresariais projetos BI também. Qualquer esforço que envolve um processo manual de deteção de origem de dados é um esforço de que pode adicionar o valor para a organização através da utilização de catálogo de dados do Azure.

## <a name="scenario-3---capturing-tribal-knowledge"></a>Cenário #3 - dados de conhecimento Tribais captura

Como saber quais os dados que tem de fazer o seu trabalho e onde encontrar esses dados?

Se tiver sido no seu trabalho para tempo, provavelmente apenas sabe. Que já passaram pelo gradual processo de aprendizagem e, ao longo do tempo aprendeu sobre as origens de dados que são fundamentais para o seu trabalho do dia a dia.

Quando um novo colaborador adere a sua equipa, como é que ele sabe quais os dados que ele precisa de fazer o seu trabalho e onde localizá-los?

São hipóteses, ele pede-lhe.

Este em curso transferência de dados de conhecimento Tribais é parte do processo de deteção de origem de dados em organizações pequenos e grandes. Mais altos e experientes os membros da equipa tem criado o conhecimento ao longo dos anos e os membros da equipa mais recentes aprendeu para peça-lhes quando tiverem dúvidas. As informações essenciais mais frequentemente existem apenas nos chefes de algumas pessoas chaves e, quando essas pessoas de férias ou sair da equipa, a organização sofre.

Por vezes especialistas estes dados irão tornar o esforço para documentar dos seus conhecimentos partilhá-lo por correio eletrónico ou em documentos do Word num site do SharePoint da equipa. Apesar de poder ser útil, que apresenta um problema de deteção novo – como as pessoas saibam que documentação existe e como encontrá-la...

Catálogo de dados Azure fornece uma localização para partilhar este conhecimento Tribais e para torná-lo facilmente identificável. Especialistas dados diretamente podem anotar elementos de dados e também podem incluir ligações para documentos existentes. Não só é que isto capturar dados de conhecimento da própria, mas também coloca o conhecimento na experiência mesmo que é utilizada para deteção de origem de dados. Quando alguém utiliza o catálogo para descobrir da origem de um dados não só será ele localize as origens propriamente dito, ele também irá encontrar dados de conhecimento o especialista que existia anteriormente apenas no espírito do especialista próprio.
