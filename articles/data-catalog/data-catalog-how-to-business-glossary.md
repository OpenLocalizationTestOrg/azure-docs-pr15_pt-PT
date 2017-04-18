<properties
    pageTitle="Como configurar o glossário de negócio para regida etiquetagem | Microsoft Azure"
    description="Artigo sobre como utilizar realce glossário empresas no catálogo de dados do Azure para definir e utilizar um vocabulário de negócio comum para colocar uma etiqueta registados elementos de dados."
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
    ms.date="09/21/2016"
    ms.author="maroche"/>

# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>Como configurar o glossário de negócio para regida etiquetas de rede

## <a name="introduction"></a>Introdução

Catálogo de dados Azure fornece capacidades de deteção de origem de dados, permitindo aos utilizadores descobrir e compreender as origens de dados que necessitam para efetuar uma análise e tomar decisões facilmente. Estas funcionalidades de deteção de efetuar o impacto maior quando os utilizadores podem encontrar e compreender vasta gama de origens de dados disponíveis.

Etiquetagem de uma funcionalidade de catálogo de dados que promove maior compreensão dos dados de activos. Etiquetagem permite aos utilizadores associar palavras-chave de um ativo ou uma coluna, que por sua vez, é mais fácil descobrir o activo através de procurar ou navegar, e permite aos utilizadores compreender mais facilmente o contexto e intenção do elemento.

No entanto, etiquetagem, por vezes, pode causar problemas da sua própria. Alguns exemplos de problemas que podem ser introduzidos pela etiquetagem são:

1.  Utilizar o abreviaturas em alguns elementos de texto expandido no outras pessoas enquanto etiquetagem de utilizadores. Esta inconsistência impede a deteção de activos apesar da intenção foi para marcar os ativos com a mesma etiqueta.
2.  Etiquetas qual o significado das coisas em contextos diferentes. Por exemplo, uma etiqueta designado por um conjunto de dados do cliente "Receita" poderá vermelha receita pelo cliente, mas a mesma etiqueta num conjunto de dados de vendas trimestral poderia vermelha receita trimestral para a empresa.  

Para ajudar a solucionar estas e outras desafios semelhantes, o catálogo de dados inclui um glossário de empresas.

Glossário de negócio do catálogo de dados permite que as organizações para os termos de negócio cruciais do documento e as respetivas definições para criar um vocabulário de negócio comuns. Este governação permite consistência na utilização de dados em toda a organização. Assim que termos são definidos no glossário empresas, pode ser atribuídos para elementos de dados no catálogo, utilizando a mesma abordagem como etiquetagem, permitindo assim _regida etiquetagem_.

> [AZURE.NOTE] A funcionalidade é descrita neste artigo apenas estão disponíveis no Standard Edition do Azure catálogo de dados. A edição gratuito não fornecem capacidades para etiquetagem regulado ou um glossário de empresas.

## <a name="glossary-availability-and-privileges"></a>Disponibilidade de glossário e privilégios

*Glossário empresas está disponível no Standard Edition do Azure catálogo de dados. Edition gratuito do catálogo de dados não inclui um glossário.*

Glossário empresas pode ser acedido através da opção de "Glossário" no menu de navegação do portal de catálogo de dados.  

![Aceder a glossário empresas](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)


Os administradores do catálogo de dados e membros da função glossário administradores de podem criar, editar e eliminar termos glossário no glossário empresas. Todos os utilizadores de catálogo de dados podem ver as definições de termo e podem marcar ativos com os termos de glossário.

![Adicionar um novo termo glossário](./media/data-catalog-how-to-business-glossary/02-new-term.png)


## <a name="creating-glossary-terms"></a>Criar termos glossário

Os administradores do catálogo de dados e glossário pode criar novos termos glossário clicando no novo termo ' botão para criar termos glossário com os seguintes campos:

* Uma definição de negócio para o termo
* Uma descrição que captura a utilização prevista ou regras de negócio para a coluna/activo
* Uma lista dos intervenientes quem saber mais sobre o termo
* O termo principal, que define a hierarquia na qual o termo está organizado


## <a name="glossary-term-hierarchies"></a>Hierarquias de termo glossário

Glossário de negócio do catálogo de dados fornece a capacidade para descrever o vocabulário empresas como uma hierarquia de termos. Isto permite que as organizações criar uma classificação de termos que melhor representa os respetivos taxonomia de empresas.

O nome de um termo tem de ser exclusivo um determinado nível da hierarquia - não são permitidos nomes duplicados. Não existe limite para o número de níveis numa hierarquia, mas uma hierarquia muitas vezes é mais fácil compreensão quando existem três níveis ou menos.

A utilização de hierarquias no glossário empresas é opcional. Sair do principal termos campo em branco para termos de glossário irá criar uma lista de plana (que não sejam hierárquico) de termos no glossário.  

## <a name="tagging-assets-with-glossary-terms"></a>Etiquetagem de activos com os termos de glossário

Assim que tenham sido definidos termos glossário dentro do catálogo, a experiência da etiquetagem de activos está optimizada para procurar o glossário, tal como o utilizador escreve a sua etiqueta. Portal do catálogo de dados apresenta uma lista de correspondência termos glossário para o utilizador à escolha. Se o utilizador seleciona um termo de glossário a partir da lista é adicionado ao activo como uma etiqueta (também conhecido como etiqueta glossário). O utilizador também pode optar por criar uma nova etiqueta, escrevendo um termo que não está no glossário (também conhecido como etiqueta de utilizador).

![Elementos de dados marcada com duas etiquetas glossário e etiquetas de um utilizador](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [AZURE.NOTE] Etiquetas de utilizador são o único tipo de etiqueta suportado no Edition gratuito do catálogo de dados.

### <a name="hover-behavior-on-tags"></a>Paire o comportamento de etiquetas
No portal do catálogo de dados são visualmente distintos, com comportamentos de diferentes hover os dois tipos de etiquetas. Quando o utilizador paira sobre uma etiqueta de utilizador podem ver o texto da etiqueta e o utilizador ou utilizadores que adicionaram a etiqueta. Quando o utilizador paira sobre uma etiqueta de glossário, também ver a definição do termo glossário e uma hiperligação para abrir o glossário de negócio para ver a definição completa do termo.

### <a name="search-filters-for-tags"></a>Filtros de pesquisa de etiquetas
Etiquetas de glossário e etiquetas de utilizador são pesquisáveis e podem ser aplicadas como filtros numa pesquisa.

## <a name="summary"></a>Resumo
Glossário empresas no catálogo de dados do Azure e etiquetagem regulado permite-, permitir que os elementos de dados ser identificados, gerido e descoberta de uma forma consistente. Glossário empresas pode promover a formação do vocabulário empresas entre os utilizadores de uma organização e suporta metadados significativos para ser capturado, tornando deteção de elementos e Noções sobre a opção.

## <a name="see-also"></a>Consulte também

- [Documentação de REST API para operações de glossário de negócio](https://msdn.microsoft.com/library/mt708855.aspx)
