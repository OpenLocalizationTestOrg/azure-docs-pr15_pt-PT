<properties
   pageTitle="Incorporados do Microsoft Power BI - estabelecer ligação a uma origem de dados"
   description="Power BI incorporado, ligar a origens de dados"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="connect-to-a-data-source"></a>Ligar a uma origem de dados

Com o **Power BI incorporado**, pode incorporar relatórios na sua própria aplicação. Quando incorporar um relatório do Power BI a sua aplicação, o relatório liga aos dados subjacentes **importando** uma cópia dos dados ou, **diretamente a ligar** à origem de dados utilizando **DirectQuery**.

Estas são as diferenças entre utilizar de **importação** e **DirectQuery**.

|Importar | DirectQuery
|---|---
|As tabelas, colunas *e os dados* são importadas ou copiadas para o conjunto de dados do relatório. Para ver as alterações que ocorreram os dados subjacentes, tem de atualizar ou importar um concluída, atual conjunto de dados novamente.|Apenas a *tabelas e colunas* são importados ou copiado para o conjunto de dados do relatório. Vê sempre os dados mais recentes.
Com o Power BI incorporado, pode utilizar o DirectQuery com origens de dados da nuvem mas não no local origens de dados neste momento.

## <a name="benefits-of-using-directquery"></a>Benefícios da utilização DirectQuery

Existem duas vantagens principais quando utilizar **DirectQuery**:

   -    **DirectQuery** permite-lhe criar visualizações sobre muito grandes conjuntos de dados, onde, caso contrário, seria unfeasible a primeira importação todos os dados.
   -    Subjacentes alterações aos dados, podem requerer uma atualização de dados e para alguns relatórios, a necessidade de apresentar dados atuais pode exigir transferências de dados grandes, efetuar novamente a importação de dados unfeasible. Por outro lado, **DirectQuery** sempre relatórios utilizar dados atual.

## <a name="limitations-of-directquery"></a>Limitações da DirectQuery

   Existem algumas limitações deve utilizar **DirectQuery**:

   -    Todas as tabelas têm de surgir a partir de uma base de dados simples.
   -    Se a consulta for demasiado complexa, irá ocorrer um erro. Resolver o erro tem de reestruturar a consulta para que fique menos complexo. Se o quuery tem de ser complexa, terá de importar os dados em vez de utilizar **DirectQuery**.
   -    A filtragem de relação é limitado num único sentido em vez de ambas as direções.
   -    Não é possível alterar o tipo de dados de uma coluna.
   -    Por predefinição, limitações são colocadas em expressões do DAX permitidos na medidas. Consulte o artigo [DirectQuery e medidas](#measures).

<a name="measures"/>
## <a name="directquery-and-measures"></a>DirectQuery e medidas

Para se certificar consultas enviadas para a origem de dados subjacentes têm um desempenho aceitável, limitações são impostas no medidas. Quando utilizar o **Power BI Desktop**, os utilizadores avançados pode optar por ignorar esta limitação ao selecionar **ficheiro > Opções e definições > Opções**. Na caixa de diálogo **Opções** , escolha **DirectQuery**e selecione a opção **Permitir ilimitadas medidas no modo DirectQuery**. Quando essa opção está selecionada, qualquer expressão DAX que seja válido para uma medida pode ser utilizado. Utilizadores devem ter em mente; No entanto, que algumas expressões que desempenhar muito bem quando os dados são importados pode resultar em consultas muito lentas à origem de back-end quando está no modo **DirectQuery** . 

## <a name="see-also"></a>Consulte também
- [Introdução ao Microsoft Power BI incorporado](power-bi-embedded-get-started.md)
- [Ambiente de trabalho do Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
