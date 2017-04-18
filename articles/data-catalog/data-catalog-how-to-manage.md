<properties
   pageTitle="Como gerir elementos de dados | Microsoft Azure"
   description="Artigo sobre como utilizar realce como controlar a visibilidade e a propriedade dos dados de activos registado no catálogo de dados do Azure."
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


# <a name="how-to-manage-data-assets"></a>Como gerir elementos de dados

## <a name="introduction"></a>Introdução

**Catálogo de dados do Azure** fornece capacidades de deteção de origem de dados, permitindo aos utilizadores descobrir e compreender as origens de dados que necessitam para efetuar uma análise e tomar decisões facilmente. Estas funcionalidades de deteção de efetuar o impacto maior quando todos os utilizadores podem localizar e compreender vasta gama de origens de dados disponíveis. Com esta situação deve ter em conta, é o comportamento predefinido do catálogo de dados para todas as origens de dados registadas sejam visíveis para – e detetável por – todos os utilizadores de catálogo.

Catálogo de dados não dar aos utilizadores acesso aos dados própria. Acesso a dados é controlado pelo proprietário da origem de dados. Catálogo de dados permite aos utilizadores para detetar origens de dados e para ver os metadados relacionados com as origens de registadas no catálogo de.

Poderão existir situações, no entanto, onde as origens de dados deverão estar apenas visíveis para utilizadores específicos ou para membros de grupos específicos. Para estes cenários, catálogo de dados permite aos utilizadores obter a propriedade de elementos de dados registadas dentro do catálogo de e para o controlo, em seguida, a visibilidade dos ativos o proprietário.

> [AZURE.NOTE] A funcionalidade é descrita neste artigo apenas estão disponíveis no Standard Edition do Azure catálogo de dados. A edição gratuito não fornece capacidades de para a propriedade e restringir visibilidade de elementos de dados.

## <a name="managing-ownership-of-data-assets"></a>Gerir a propriedade dos dados de activos
Por predefinição, os elementos de dados registados no catálogo de dados são sem proprietário; qualquer utilizador com permissão para aceder ao catálogo pode descobrir e anotar estes elementos. Os utilizadores podem obter a propriedade de elementos de dados sem proprietário e, em seguida, podem limitar a visibilidade do recursos pertencem.

Quando pertence um recurso de dados no catálogo de dados, apenas os utilizadores autorizados pelos proprietários podem descobrir o elemento e ver os seus metadados e apenas os proprietários podem eliminar o elemento do catálogo.

> [AZURE.NOTE] Propriedade no catálogo de dados afeta apenas os metadados armazenados no catálogo. Não confere quaisquer permissões na origem de dados subjacentes.

### <a name="taking-ownership"></a>Propriedade de escrita
Os utilizadores podem demorar propriedade dos dados de activos ao selecionar a opção de "Ter que é o proprietário" no portal do catálogo de dados. Não existem permissões especial necessárias para obter a propriedade de um ativo de dados sem proprietário; qualquer utilizador pode demorar que é o proprietário de um ativo de dados sem proprietário.

### <a name="adding-owners-and-co-owners"></a>Adicionar os proprietários e proprietários de cocriação
Se já pertence um recurso de dados, os utilizadores não é possível obter simplesmente a propriedade – têm de ser adicionados como proprietários cocriação por um proprietário existente. Qualquer proprietário pode adicionar utilizadores ou grupos de segurança adicionais, como os proprietários de cocriação.

> [AZURE.NOTE] É uma melhor prática ter, pelo menos, duas indivíduos como proprietários para qualquer activo de propriedade dados.

### <a name="removing-owners"></a>Remover os proprietários de
Tal como qualquer proprietário de elementos, pode adicionar os proprietários de cocriação, qualquer proprietário de elementos pode remover qualquer coproprietário.

Se um proprietário de elementos remove o próprio como um proprietário, já não ele pode gerir os ativos. Se um proprietário de elementos remove próprio como um proprietário e não existem sem outros proprietários de cocriação, elemento voltará num estado sem proprietário.

## <a name="visibility"></a>Visibilidade
Os proprietários de elementos de dados podem controlar a visibilidade dos ativos de dados que o proprietário. Para impedir que visibilidade predefinida – onde todos os utilizadores de catálogo de dados podem descobrir e visualizar elementos de dados – o proprietário de elementos pode mostrar ou ocultar a definição de visibilidade de "Todos" para "Proprietários & estes utilizadores" nas propriedades para o ativo. Os proprietários, em seguida, podem adicionar utilizadores e grupos de segurança específicos.

> [AZURE.NOTE] Sempre que possível, devem ser atribuídas permissões de elementos que é o proprietário e visibilidade para grupos de segurança e não para utilizadores individuais.

## <a name="catalog-administrators"></a>Os administradores de catálogo
Os administradores do catálogo de dados são implicitamente cocriação proprietários de todos os elementos no catálogo. Os proprietários de elementos não é possível remover visibilidade de administradores de catálogo e os administradores podem gerir a propriedade e visibilidade para todos os elementos de dados no catálogo.

## <a name="summary"></a>Resumo
Modelo de crowdsourcing do catálogo de dados para deteção de elementos de metadados e dados permite todos os utilizadores de catálogo contribuir e descobrir. Edition padrão do catálogo de dados fornece capacidades para a propriedade e gestão de para limitar a visibilidade e a utilização de activos dados específicos.
