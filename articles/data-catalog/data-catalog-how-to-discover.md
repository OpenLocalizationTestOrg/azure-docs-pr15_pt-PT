<properties
   pageTitle="Como detetar origens de dados | Microsoft Azure"
   description="Artigo sobre como utilizar realce como descobrir dados registadas ativos com o catálogo de dados do Azure, incluindo procurar e filtrar e utilizar as funcionalidades do portal do catálogo de dados do Azure de ocorrências."
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

# <a name="how-to-discover-data-sources"></a>Como detetar origens de dados

## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente geridas que serve de um sistema de registo e sistema de deteção para origens de dados da empresa. Por outras palavras, o **Catálogo de dados do Azure** é tudo sobre ajudando às pessoas descobrir, compreender e utilizar origens de dados e organizações de ajuda para obter maior valor a partir dos seus dados existentes. Assim que uma origem de dados foi registada com o **Catálogo de dados do Azure**, respectivos metadados são indexado pelo serviço, para que os utilizadores podem procurar facilmente para descobrir os dados que necessitam.

## <a name="searching-and-filtering"></a>Procurar e filtrar

Deteção no **Catálogo de dados do Azure** utiliza dois mecanismos primários: Procurar e filtrar.

Procurar foi concebida para ser intuitiva e poderosas – por predefinição, os termos de pesquisa são comparados com qualquer propriedade no catálogo, incluindo anotações fornecidos pelo utilizador.

A filtragem foi concebida para completar a procurar. Os utilizadores possam selecionar características específicas como especialistas, o tipo de origem de dados, o tipo de objeto e o etiquetas, para ver apenas elementos de dados correspondente e para restringir resultados da pesquisa para a correspondência de activos também.

Ao utilizar uma combinação de pesquisa e filtragem, os utilizadores podem navegar rapidamente as origens de dados que foi registadas com o **Catálogo de dados do Azure** para detetar origens de dados que necessitam.

## <a name="search-syntax"></a>Sintaxe de procura

Apesar da procura de texto livre predefinida é simples e intuitivo, os utilizadores também podem utilizar sintaxe de pesquisa do **Catálogo de dados do Azure**do tenham maior controlo sobre os resultados da pesquisa. Pesquisa do **Catálogo de dados do Azure** suporta as seguintes técnicas:

| Técnica                 | Utilizar                                                                                                                                     | Exemplo                                                   |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Pesquisa básica              | Pesquisa básica com um ou mais termos de pesquisa. Os resultados são quaisquer activos que correspondem aos qualquer propriedade com um ou mais os termos especificados. | dados de vendas                                                |
| Propriedade controlo do âmbito          | Só devolver origens de dados onde o termo de pesquisa é correspondido com a propriedade especificada                                                   | nome: juros                                              |
| Operadores booleanos         | Alargar ou restringir uma procura utilizando operações booleanas                                                                                     | juros não empresarial                                     |
| Agrupar com parênteses | Utilizar parênteses para peças de grupo da consulta para alcançar isolamento lógico, especialmente em conjunto com operadores booleanos              | nome: Finanças AND (etiquetas: Q1 ou etiquetas: Q2) |
| Operadores de comparação      | Utilizar as comparações que não seja igualdade para as propriedades que tiverem os tipos de dados numéricos e de data                                                | modifiedTime > "11/05/2014"                                 |

Para mais informações sobre pesquisa no **Catálogo de dados do Azure** , consulte o artigo [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="hit-highlighting"></a>Acertar realce
Quando visualizar os resultados da pesquisa, qualquer propriedades apresentadas que correspondem aos termos de pesquisa especificados – como o nome de elementos de dados, descrição e etiquetas – serão realçadas para facilitar a identificar a razão pela qual um recurso de dados determinado foi devolvido por um determinado de pesquisa.

> [AZURE.NOTE] Os utilizadores podem ativar visitas realce desativar caso deseje, utilizando o parâmetro de "Realce" no portal do **Catálogo de dados do Azure** .

Quando visualizar os resultados da pesquisa, poderá não sempre ser óbvio razão pela qual um recurso de dados é incluído, mesmo com realce visitas ativado. Uma vez que todas as propriedades são procuradas por predefinição, um recurso de dados pode ser devolvido devido a uma correspondência numa propriedade de nível de coluna. E uma vez que vários utilizadores podem anotar elementos de dados registadas com os seus próprios etiquetas e descrições, nem todos os metadados poderão ser apresentada na lista de resultados da pesquisa.

Na predefinida dispor em mosaico vista, cada mosaico apresentado nos resultados da pesquisa irá incluir um ícone de "termo de pesquisa da vista corresponde ao", que permite ao utilizador para ver rapidamente o número de correspondências e a sua localização e para ir para-las, se pretender.

 ![Acertar realce e corresponde a pesquisa no portal do catálogo de dados do Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Resumo
Registar uma origem de dados com o **Catálogo de dados do Azure** facilita que origem de dados detetar e compreender, copiando estrutural e descritivo metadados da origem de dados para o serviço de catálogo. Assim que tiver sido registada uma origem de dados, os utilizadores possam detetá-lo utilizando a filtragem e procurar a partir do portal do **Catálogo de dados do Azure** .

## <a name="see-also"></a>Consulte também
- [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial para obter mais detalhes passo a passo sobre como detetar origens de dados.
