<properties
   pageTitle="Catálogo de dados Azure perguntas mais frequentes | Microsoft Azure"
   description="Perguntas mais frequentes sobre o catálogo de dados do Azure, incluindo as capacidades de deteção de origem de dados, anotações e gestão."
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
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure catálogo de dados de perguntas mais frequentes

Este artigo fornece respostas para perguntas mais frequentes relacionadas com o serviço do Microsoft **Azure catálogo de dados** .

## <a name="q-what-is-azure-data-catalog"></a>P: qual é o catálogo de dados do Azure?

R: catálogo de dados Microsoft Azure é um serviço totalmente gerido alojado na nuvem Microsoft Azure que serve de um sistema de registo e sistema de deteção para origens de dados da empresa. Catálogo de dados Azure fornece capacidades de que ativar todos os utilizadores – de analistas de cientistas de dados para os programadores – para registar, descobrir, entender e consumam origens de dados.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>P: que cliente pede o catálogo de dados do Azure resolver?

Catálogo de dados Azure endereços o desafio da deteção de origem de dados e "dados escuros" permitindo aos utilizadores detetar e compreender as origens de dados empresariais.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>P: quem são as audiências alvo para o catálogo de dados do Azure?

Catálogo de dados Azure fornece capacidades para técnicos e utilizadores, incluindo:

- Os programadores de dados, BI e profissionais Analytics: quem é o responsável por produzir dados e a análise de conteúdo para que outras pessoas consumir
-   Responsáveis pelos dados: Aqueles que têm o conhecimento sobre os dados, o que significa e como se destina a ser utilizado e para o efeito
- Consumidores de dados: Pessoas que precisam de ser possível descobrir, compreender e ligar facilmente a dados se necessário, para fazer a sua tarefa utilizando a ferramenta da sua escolha
- Central IT: aqueles que necessitam tornar o centenas de origens de dados detetável para os utilizadores empresariais e que necessitam de manter supervisão sobre como os dados está a ser utilizados e por quem

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>P: qual é a disponibilidade de região do catálogo de dados do Azure?

Azure serviços de catálogo de dados estão atualmente disponíveis nos seguintes centros de dados:

- Ocidental dos e.u.a.
- Leste dos EUA
- Europa Ocidental
- Europa Norte
- Leste Austrália
- Sudeste asiático

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>P: quais são os limites no número de elementos de dados no catálogo de dados do Azure?

O gratuito Edition do Azure catálogo de dados está limitado a elementos de dados registadas 5.000.

O Standard Edition do Azure catálogo de dados suporta até 100.000 elementos de dados registadas.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>P: quais são os tipos de origem e elementos de dados suportadas?

Consulte [DSR de catálogo de dados](data-catalog-dsr.md) para a lista de origens de dados atualmente suportados.

## <a name="q-how-do-i-request-support-for-another-data-source"></a>P: como pedido de suporte para outra origem de dados?

Pedidos de funcionalidades e outros comentários podem ser apresentados no [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>P: como posso começar a com o catálogo de dados do Azure?

É o melhor local para começar a utilizar ao seguir as instruções em [Introdução ao catálogo de dados](data-catalog-get-started.md). Este artigo é uma descrição geral de fim para fim das capacidades de no serviço.

## <a name="q-how-do-i-register-my-data"></a>P: como registar aos meus dados?

Para registar os seus dados no catálogo de dados do Azure, inicie a ferramenta de registo de catálogo de dados do Azure da área do portal de catálogo de dados do Azure "Publicar". Na aplicação de publicação do catálogo de dados do Azure, inicie sessão com as mesmas credenciais para aceder ao portal do catálogo de dados do Azure e, em seguida, selecione a origem de dados e os ativos específicos que deseja registar.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>P: que propriedades são extraídas para elementos de dados que estão registados?

As propriedades específicas serão diferentes de origem de dados a origem de dados, mas em geral o serviço de publicação do catálogo de dados do Azure irá extrair as seguintes informações:

- Nome do activo
- Tipo de elementos
- Descrição do activo
- Nomes de atributo/colunas
- Tipos de dados de atributo/coluna
- Descrição do atributo/coluna

> [AZURE.IMPORTANT] Registar elementos de dados com o catálogo de dados do Azure não mover ou copiar dados para a nuvem. Registar elementos a partir de uma origem de dados irá copiar metadados dos recursos para Azure, mas os dados permanecem na localização de origem de dados existente. A única exceção esta regra é se um utilizador escolhe carregar registos de pré-visualizar ou um perfil de dados quando o registo de recursos. Quando incluindo uma pré-visualização, até 20 registos serão copiados a partir de cada imobilizado e são armazenadas como um instantâneo no catálogo de dados do Azure. Quando incluindo um perfil de dados, informações de agregação (como o tamanho de tabelas, os valores nulos percentagem por coluna e os valores mínimos, máximos e média para colunas) será calculada e incluído nos metadados armazenados no catálogo.

<br/>

> [AZURE.NOTE] Para origens de dados como SQL Server Analysis Services que têm uma propriedade de **Descrição** primeira classe, o catálogo de dados do Azure publicação de aplicação irá extrair esse valor da propriedade. Para SQL Server bases de dados relacionais, que possuem uma propriedade de **Descrição** primeira classe, a aplicação de publicação do catálogo de dados do Azure irá extrair o valor de ms_description propriedade para objetos e colunas expandida. Para mais informações, consulte o artigo da TechNet [Utilizando propriedades expandidas objetos de base de dados](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>P: quanto tempo devo demorar de activos recentemente registados seja apresentado no catálogo de dados do Azure?

Depois de registar ativos com o catálogo de dados do Azure poderão existir um período de 5 a 10 segundos antes de serem apresentados no portal do catálogo de dados do Azure.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>P: como anotar e enriquecer os metadados para os meus elementos de dados registadas?

É a forma mais simples para fornecer metadados de activos registados selecionar o elemento no portal do catálogo de dados do Azure e, em seguida, introduza os valores de metadados no painel de propriedades ou no painel de esquema para o objeto selecionado.

Pode também fornecem alguns metadados, como especialistas e etiquetas, durante o processo de registo. Os valores fornecidos no serviço de publicação do catálogo de dados do Azure serão aplicada a todos os elementos que está a ser registados nesse momento. Para ver os objetos registado recentemente no portal para anotação adicional, selecione o botão de **Vista Portal** no último ecrã da aplicação de publicação do catálogo de dados do Azure.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>P: como posso eliminar a minha objetos de dados registadas?

Pode eliminar um objeto de catálogo de dados do Azure selecionando o objeto no portal do e, em seguida, clicando no botão **Eliminar** . Isto irá remover os metadados para o objeto do catálogo de dados do Azure mas não afeta a origem de dados subjacente real.

## <a name="q-what-is-an-expert"></a>P: o que é um especialista?

Um especialista é uma pessoa que tenha uma perspetiva informada sobre um objeto de dados. Um objeto pode ter vários especialistas. Um especialista não necessitam de ser o "proprietário" para um objeto; o especialista é simplesmente alguém que sabe como podem os dados e deve ser utilizado.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>P: como posso partilhar informações com a equipa de catálogo de dados do Azure se deparar com problemas?

Utilizar o Fórum do catálogo de dados do Azure de comunicar problemas, partilhar informações e fazer perguntas. Fórum da pode ser encontrado em http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##<a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>P: Azure catálogo de dados funciona com esta origem de dados que estiver interessado em?
Ativamente estamos a trabalhar sobre como adicionar mais origens de dados para o catálogo de dados do Azure. Se existir uma origem de dados que pretende Consulte suportados, consulte sugeri-la (ou o suporte de voz, se já foi sugerida) no [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>P: como está Azure catálogo de dados relacionada com o catálogo de dados no Power BI para Office 365?

Poderá pensar Azure catálogo de dados como uma evolução do catálogo de dados. Catálogo de dados Azure fornece capacidades semelhantes para publicação de origem de dados e deteção, mas é com o foco no cenários mais amplas e não depende do Office 365. Pouco depois do catálogo de dados do Azure fica disponível geralmente os catálogos de duas impressão em série para um serviço único.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>P: que permissões é que um utilizador precisa registar ativos com o catálogo de dados do Azure?

O utilizador a executar a ferramenta de registo do catálogo de dados do Azure tem permissões na origem de dados que irá permitir-lhe ler os metadados da origem. Se o utilizador também seleciona para incluir uma pré-visualização, em seguida, o utilizador tem também de ter permissões que permitem-lhe a leitura dos dados de objetos que está a ser registados.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>P: Azure catálogo de dados estarão disponível para também a implementação no local?

Azure catálogo de dados é um serviço na nuvem que pode trabalhar com origens de dados na nuvem e no local, faz uma solução de deteção de origem de dados híbrido. Neste momento não existem planos para a versão do serviço de catálogo de dados do Azure que será executada no local.

##<a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>P: Recomendamos extrair metadados mais / mais completo das origens de dados, que podemos registar?

Ativamente estamos a trabalhar para expandir as funcionalidades do catálogo de dados do Azure. Se existir metadados adicionais que pretender para ver extraída da origem de dados durante o registo, Sugerir fórum (ou voto para o mesmo se já foi sugerida) no [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Vamos permitirá no futuro terceiros adicionar novos tipos de origem de dados através de uma expansão API.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>P: como restringir a visibilidade de elementos de dados registadas, para que apenas algumas pessoas possam detetá-los?

R: selecione os elementos de dados no catálogo de dados do Azure e clique no botão "Obter propriedade". Os proprietários dos recursos de dados no catálogo de dados do Azure podem alterar as definições de visibilidade, para permitir que todos os utilizadores de catálogo para descobrir os ativos propriedade ou para restringir a visibilidade para utilizadores específicos.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>P: como atualizar o registo para um item de dados para que muda na origem de dados serão refletidas no catálogo de?

R: para atualizar os metadados para elementos de dados que já estão registados no catálogo, basta voltar a registar a origem de dados que contém os ativos. Quaisquer alterações da origem de dados, tais como colunas a ser adicionadas ou removidas de tabelas ou vistas, serão atualizadas no catálogo, mas qualquer anotações fornecidas pelos utilizadores serão mantidas.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>P: o meu não responder à pergunta aqui – o que devo fazer?

Cabeça ao longo para o [Fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Não existem perguntas irão encontrar a sua forma aqui.
