<properties
   pageTitle="O que é o catálogo de dados do Azure? | Microsoft Azure"
   description="Este artigo fornece uma descrição geral do catálogo de dados do Azure Microsoft, incluindo as suas funcionalidades e os problemas destina-se para o endereço. Catálogo de dados fornece capacidades de que ativar todos os utilizadores – de analistas de cientistas de dados para os programadores – para registar, descobrir, entender e consumam origens de dados."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="what-is-azure-data-catalog"></a>O que é o catálogo de dados do Azure?

Azure catálogo de dados é um serviço de nuvem totalmente geridas que permite aos utilizadores para detetar origens de dados necessitam de e para compreender as origens de dados localizar, ajudando organizações obter maior valor a partir dos seus investimentos existentes. Catálogo de dados fornece capacidades de que ativar todos os utilizadores – de analistas de cientistas de dados para os programadores – para detetar, compreender e consumam origens de dados. Catálogo de dados inclui um modelo de crowdsourcing de metadados e anotações e permite todos os utilizadores contribuir dos seus conhecimentos para criar uma Comunidade e uma cultura de dados.

## <a name="discovery-challenges-for-data-consumers"></a>Deteção são os desafios mais para consumidores de dados

Normalmente, a descobrir origens de dados empresariais foi um processo orgânico com base em dados de conhecimento tribais. Isto apresenta desafios inúmeros para as empresas que pretendem Tire o máximo a partir do seu património informações.

-   Os utilizadores não estão em atenção que as origens de dados existem, a menos que eles entrarem em contacto com-lo como parte do processo outra; não existe nenhuma localização central onde estão registadas origens de dados.
-   A menos que um utilizador saiba a localização de uma origem de dados, não é possível ligar aos dados através de uma aplicação de cliente; dados consumo experiências exigir que os utilizadores saber a cadeia de ligação ou o caminho.
-   A menos que um utilizador saiba a localização da documentação de uma origem de dados, ele não é possível compreender que utilizações dos dados; origens de dados e documentação live em locais diferentes e são consumidas através de diferentes experiências.
-   Se um utilizador tiver perguntas acerca de um ativo de informações, ele tem localize o especialista ou a equipa responsável pelos dados e contrate esses especialistas offline; não existe nenhuma explícita ligação entre os dados e aqueles com perspetivas especialistas na sua utilização.
-  A menos que um utilizador compreende o processo de pedido de acesso à origem de dados, a descobrir a origem de dados e a respectiva documentação ainda não permitir-lhe aceder aos dados que ele necessita.

## <a name="discovery-challenges-for-data-producers"></a>Deteção são os desafios mais para produtor de dados

Enquanto consumidores de dados de enfrentar estes desafios, os utilizadores responsáveis pela que produza e manutenção de activos informações cara desafios da sua própria.

-   Anotar origens de dados com metadados descritivo é frequentemente um esforço de perdidas aplicações de cliente normalmente ignoram descrições armazenadas na origem de dados.
-   Criar a documentação para origens de dados é frequentemente um esforço de perdidas; manter a documentação sincronizado com a origem de dados é uma responsabilidade em curso e utilizadores faltam fidedignidade na documentação como-lo com frequência é considerada a ser desatualizada.
- Restringir o acesso à origem de dados e garantir que os consumidores de dados sabem como pedi access é um desafio em curso.

Criar e manter a documentação para uma origem de dados são complexo e moroso. O desafio da disponibilização dessa documentação facilmente para todos os que utilizam a origem de dados é frequentemente assim ainda mais.

Quando o combinado, estes são os desafios mais apresentam uma barreiras significativa para as empresas que pretende para encorajar e promover a utilização e compreensão dos dados de empresa.

## <a name="azure-data-catalog-can-help"></a>Pode ajudar a Azure catálogo de dados

Catálogo de dados foi concebido para resolver estes problemas e para activar empresas obter o valor a maior parte da partir do seu património informações existentes. Ajuda do catálogo de dados criando as origens de dados fáceis e compreensíveis pelos utilizadores que precisam os dados que gerir.

Catálogo de dados, fornece um serviço baseado na nuvem para os dados que pode ser registada origem. Os dados permanecem na sua localização existente, mas uma cópia dos metadados é adicionada ao catálogo de dados, juntamente com uma referência para a localização de origem de dados. Este metadados também são indexado para tornar a cada origem de dados facilmente detetável através da pesquisa e compreensíveis aos utilizadores que descobri-lo.

Assim que tiver sido registada uma origem de dados, os metadados podem, em seguida, ser enriquecimento, pelo utilizador que executou o registo ou por outros utilizadores da empresa. Qualquer utilizador pode anotar uma origem de dados ao fornecer descrições, etiquetas ou outros metadados, como documentação e processos para pedir acesso à origem de dados. Este metadados descritivo complementam os metadados estruturais (tais como nomes de colunas e tipos de dados) registado da origem de dados.

Descobrir e compreender as origens de dados e a sua utilização são o objetivo principal de registar as origens. Quando os utilizadores empresariais precisa de dados para os respetivos esforços (que podem ser de business intelligence, desenvolvimento de aplicações, ciência de dados ou qualquer outra tarefa onde os dados corretos são necessários) podem utilizar a experiência de deteção de catálogo de dados para rapidamente localizar dados que corresponde às suas necessidades, compreender os dados a avaliar o respetiva manutenção física para efeito e consuma esses dados ao abrir a origem de dados na sua ferramenta à escolha. Ao mesmo tempo, o catálogo de dados permite aos utilizadores de contribuição para o catálogo, por etiquetagem, documentar e anotar origens de dados que já foram registadas e por novas origens de dados que podem, em seguida, ser descobertas, compreensão e média consumidas por à Comunidade de utilizadores do catálogo a registar.

![Funcionalidades do catálogo de dados](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>Introdução ao catálogo de dados

Para começar a utilizar com o catálogo de dados hoje em dia, visite [www.azuredatacatalog.com](https://www.azuredatacatalog.com).

Um guia de introdução ao obter está disponível [aqui](data-catalog-get-started.md).

## <a name="learn-more-about-data-catalog"></a>Mais informações sobre o catálogo de dados

Para saber mais sobre as funcionalidades do catálogo de dados, consulte o artigo:

* [Como registar origens de dados](data-catalog-how-to-register.md)
* [Como detetar origens de dados](data-catalog-how-to-discover.md)
* [Como anotar as origens de dados](data-catalog-how-to-annotate.md)
* [Como origens de dados do documento](data-catalog-how-to-documentation.md)
* [Como ligar a origens de dados](data-catalog-how-to-connect.md)
* [Como trabalhar com dados grandes](data-catalog-how-to-big-data.md)
* [Como gerir elementos de dados](data-catalog-how-to-manage.md)
* [Como configurar o glossário de empresas](data-catalog-how-to-business-glossary.md)
* [Perguntas mais frequentes](data-catalog-frequently-asked-questions.md)
