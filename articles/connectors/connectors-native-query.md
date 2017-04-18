<properties
    pageTitle="Adicionar a ação de consulta nas aplicações de lógica | Microsoft Azure"
    description="Descrição geral da ação de consulta para realizar ações como matriz de filtro."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-query-action"></a>Começar a trabalhar com a ação de consulta

Ao utilizar a ação de consulta, pode trabalhar com secções e matrizes para executar fluxos de trabalho para:

- Crie uma tarefa para todos os registos de alta prioridade de uma base de dados.
- Guarde todos os anexos de PDF para e-mails para uma BLOBs do Azure.

Para começar a utilizar a ação de consulta numa aplicação lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-query-action"></a>Utilizar a ação de consulta

Uma ação é uma operação que é realizada pelo fluxo de trabalho que está definido numa aplicação de lógica. [Saber mais sobre ações](connectors-overview.md).  

A ação de consulta tem atualmente uma operação, denominada a matriz de filtro, o que é apresentado quando se clica no estruturador de. Esta opção permite-lhe uma matriz de consulta e devolver um conjunto de resultados filtrados.

Eis como pode adicioná-la numa aplicação de lógica:

1. Selecione o botão de **Novo passo** .
2. Selecione **Adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **filtro** para a ação de **matriz de filtro** de lista.

    ![Selecione a ação de consulta](./media/connectors-native-query/using-action-1.png)

4. Selecione uma matriz para filtrar. (A captura de ecrã seguinte apresenta a matriz de resultados de uma pesquisa Twitter do.)
5. Crie uma condição para avaliar em cada item. (A captura de ecrã seguinte filtros tweets de utilizadores que tenham mais de 100 seguidores.)

    ![Concluir a ação de consulta](./media/connectors-native-query/using-action-2.png)

    A ação irá de saída de uma matriz nova que contém apenas os resultados que satisfaçam os requisitos de filtro.
6. Clique no canto superior esquerdo da barra de ferramentas para guardar e, a aplicação de lógica irá guardar e publicar (ativar).

## <a name="query-action"></a>Consulta de ação

Aqui estão os detalhes para a ação que suporta esta conexão. A conexão tem uma ação possível.

|Ação|Descrição|
|---|---|
|Matriz de filtro|Avalia uma condição para cada item numa matriz e devolve o resultado|

## <a name="action-details"></a>Detalhes de ação

A ação de consulta é fornecido com uma ação possível. As tabelas seguintes descrevem os campos de entrada necessários e opcionais para a ação e os detalhes de saída correspondentes que estão associados a utilizar a ação.

### <a name="filter-array"></a>Matriz de filtro
Seguem-se os campos de entrada para a ação, que torna um pedido de HTTP de saída.
A * significa que é um campo obrigatório.

|Nome a apresentar|Nome da propriedade|Descrição|
|---|---|---|
|A partir do *|a partir do|A matriz para filtrar|
|Condição *|onde|A condição para avaliar para cada item|
<br>

### <a name="output-details"></a>Detalhes de saída

Seguem-se detalhes de saída para a resposta de HTTP.

|Nome da propriedade|Tipo de dados|Descrição|
|---|---|---|
|Matriz filtrada|matriz|Uma matriz que contém um objeto para cada resultado filtrado|

## <a name="next-steps"></a>Próximos passos

Agora, experimente a plataforma e, em seguida, [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Pode explorar as outras conexões disponíveis nas aplicações de lógica verificando nossa [lista APIs](apis-list.md).
