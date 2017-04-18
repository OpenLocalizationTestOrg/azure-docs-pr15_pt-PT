<properties
    pageTitle="Adicionar um atraso nas aplicações de lógica | Microsoft Azure"
    description="Descrição geral de atraso e atraso-até ações e como utilizá-los com uma aplicação do Azure lógica."
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
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Começar a trabalhar com o atraso e atraso-até ações

Ao utilizar o atraso e "atraso-até" ações, pode concluir cenários de fluxo de trabalho.

Por exemplo, pode:

- Aguarde até um dia da semana para enviar uma atualização de estado através de correio eletrónico.
- Atrasar o fluxo de trabalho até que tenha de uma chamada de HTTP tempo para concluir antes de retomar e obter o resultado.

Para começar a utilizar a ação de atraso numa aplicação lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Utilizar as ações de atraso

Uma ação é uma operação que é realizada pelo fluxo de trabalho que está definido numa aplicação de lógica. [Saber mais sobre ações](connectors-overview.md).

Eis uma sequência de exemplo de como utilizar um passo de atraso numa aplicação de lógica:

1. Depois de adicionar um accionador, clique em **Novo passo** para adicionar uma ação.
2. Procure **atraso** ativar as ações de atraso. Neste exemplo, vamos irá selecionar **atraso**.

    ![Ações de atraso](./media/connectors-native-delay/using-action-1.png)

3. Conclua qualquer uma das propriedades para configurar o atraso de ação.

    ![Configuração de atraso](./media/connectors-native-delay/using-action-2.png)

4. Clique em **Guardar** para publicar e ativar a aplicação de lógica.


## <a name="action-details"></a>Detalhes de ação

O accionador periodicidade tem as seguintes propriedades que podem ser configuradas.

### <a name="delay-action"></a>Ação de atraso

Esta ação atrasa a executar para um determinado intervalo de tempo.
A * significa que é um campo obrigatório.

|Nome a apresentar|Nome da propriedade|Descrição|
|---|---|---|
|Contar *|contar|O número de unidades de tempo para atrasar|
|Unidade *|unidade|A unidade de tempo: `Second`, `Minute`, `Hour`, ou`Day`|
<br>

### <a name="delay-until-action"></a>Atraso-até que ação

Esta ação atrasa a executar até à data/hora especificada.
A * significa que é um campo obrigatório.

|Nome a apresentar|Nome da propriedade|Descrição|
|---|---|---|
|Ano *|data/hora|O ano até à atrasar até (GMT)|
|Mês *|data/hora|Mês até à atrasar até (GMT)|
|Dia *|data/hora|O dia para atrasar até (GMT)|
<br>


## <a name="next-steps"></a>Próximos passos

Agora, experimente a plataforma e, em seguida, [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Pode explorar as outras conexões disponíveis nas aplicações de lógica verificando nossa [lista APIs](apis-list.md).
