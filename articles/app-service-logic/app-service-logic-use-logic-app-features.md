<properties 
    pageTitle="Utilizar funcionalidades de aplicação de lógica | Microsoft Azure" 
    description="Saiba como utilizar as funcionalidades avançadas de lógica aplicações." 
    authors="stepsic-microsoft-com" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/28/2016"
    ms.author="stepsic"/> 
    
# <a name="use-logic-apps-features"></a>Utilizar funcionalidades de aplicações de lógica

No [tópico anterior](app-service-logic-create-a-logic-app.md), que criou a aplicação de lógica primeira. Agora iremos mostrar-lhe como criar um processo mais completo utilizando o iniciador de aplicações de lógica de serviços. Este tópico apresenta os conceitos de lógica aplicações novos seguintes:

- Lógica condicional, executa uma ação apenas quando numa determinada condição é cumprida.
- Vista de código para editar uma aplicação de lógica existente.
- Opções para iniciar um fluxo de trabalho.

Antes de concluir este tópico, devem concluir os passos no [artigo criar uma nova aplicação de lógica](app-service-logic-create-a-logic-app.md). No [portal do Azure], navegue para a sua aplicação de lógica e clique em **Accionadores e acções** num resumo para editar a definição de aplicação lógica.

## <a name="reference-material"></a>Material de referência

Pode encontrar os seguintes documentos útil:

- [Gestão e runtime REST APIs](https://msdn.microsoft.com/library/azure/mt643787.aspx) - incluindo como invocar diretamente aplicações de lógica
- [Referência da linguagem](https://msdn.microsoft.com/library/azure/mt643789.aspx) - uma lista completa de suportadas todas as funções de expressões
- [Tipos de accionador e ação](https://msdn.microsoft.com/library/azure/mt643939.aspx) : os diferentes tipos de ações e as entradas tomem
- [Descrição geral da aplicação de serviço de](../app-service/app-service-value-prop-what-is.md) -descrição dos quais os componentes a escolher quando criar uma solução

## <a name="adding-conditional-logic"></a>Adicionar lógica condicional

Apesar do fluxo original funciona, existem algumas áreas que podem ser melhoradas. 


### <a name="conditional"></a>Condicional
Esta aplicação lógica pode resultar em que receber muitas mensagens de correio eletrónico. Os passos seguintes adicionem lógica para se certificar de que apenas recebe uma mensagem de e-mail quando o tweet enviado por alguém com um determinado número de seguidores. 

1. Clique no sinal de adição e localize a ação *Obter utilizador* do Twitter.

2. Passar no campo **Tweeted por** de accionador para obter informações sobre o utilizador Twitter.

    ![Obter o utilizador](./media/app-service-logic-use-logic-app-features/getuser.png)

3. Clique no sinal de adição novamente, mas desta vez selecionar **Adicionar condição**

4. Na primeira caixa, clique em **…** abaixo do **Utilizador obter** para localizar o campo de **contagem de seguidores** .

5. Na lista pendente, selecione **maior do que**

6. Na segunda caixa, escreva o número de seguidores pretende que os utilizadores tenham.

    ![Condicional](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  Por fim, arrastar e largar o e-mail caixa para a caixa **Se Sim** . Isto significa que o irá obter apenas mensagens de correio eletrónico quando a contagem de Seguidor for cumprida.

## <a name="repeating-over-a-list-with-foreach"></a>Ao longo de uma lista com forEach de repetição

O ciclo de forEach Especifica uma matriz a repetir uma ação ao longo do. Se não se trata de uma matriz o fluxo de falha. Por exemplo, se tiver action1 exporta uma matriz de mensagens, e que pretende enviar a cada mensagem pode incluir este instrução forEach nas propriedades da sua ação: forEach:"@action('action1').outputs.messages"
 

## <a name="using-the-code-view-to-edit-a-logic-app"></a>Utilizar a vista de código para editar uma aplicação de lógica

Para além do estruturador de, diretamente pode editar o código que define uma aplicação de lógica, da seguinte forma. 

1. Clique no botão **da Vista código** na barra de comandos. 

    Esta ação abre um editor de completo que mostra a definição que acabou de editar.

    ![Vista de código](./media/app-service-logic-use-logic-app-features/codeview.png)

    Ao utilizar o editor de texto, pode copiar e colar qualquer número de ações da aplicação de lógica mesmo ou entre as aplicações de lógica. Pode adicionar ou remover secções inteiras da definição do também facilmente e definições também pode partilhar com outras pessoas.

2. Depois de efetuar as suas alterações na vista de código, clique simplesmente **Guardar**. 

### <a name="parameters"></a>Parâmetros
Existem algumas funcionalidades de lógica das aplicações do que só pode ser utilizado na vista de código. Exemplo de um dos seguintes procedimentos é parâmetros. Parâmetros facilitam a reutilização valores ao longo da sua aplicação de lógica. Por exemplo, se tiver um endereço de e-mail que pretende utilizar no várias ações, deverá defini-la como um parâmetro.

A seguinte atualiza a aplicação de lógica existente para utilizar parâmetros para o termo de consulta.

1. Na vista de código, localize o `parameters : {}` objeto e inserir o objeto de tópico seguintes:

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
    
2. Desloque-se para o `twitterconnector` ação, localize o valor de consulta e substituí-la com `#@{parameters('topic')}`.
    Também pode utilizar a função **concat** para associar duas ou mais cadeias, por exemplo: `@concat('#',parameters('topic'))` é idêntico ao que precede. 
 
Parâmetros são uma boa forma de separar valores que provavelmente alterar um lote. São particularmente útil quando precisar de substituem parâmetros em ambientes diferentes. Para mais informações sobre como substituir parâmetros com base no ambiente, consulte a nossa [documentação REST API](https://msdn.microsoft.com/library/mt643787.aspx).

Agora, quando clica em **Guardar**, cada hora receberá qualquer tweets novas que tenham mais de 5 retweets entregues numa pasta denominada **tweets** no seu Dropbox.

Para saber mais sobre as definições de aplicação de lógica, consulte o artigo [definições de aplicação de lógica de autor](app-service-logic-author-definitions.md).

## <a name="starting-a-logic-app-workflow"></a>Iniciar um fluxo de trabalho de aplicação de lógica
Existem várias opções diferentes para iniciar o fluxo de trabalho definido no app lógica. Um fluxo de trabalho sempre pode ser iniciado a pedido no [portal do Azure].

### <a name="recurrence-triggers"></a>Accionadores periodicidade
Um acionador de periodicidade é executado num intervalo de tempo que especificar. Quando o accionador tem lógica condicional, o accionador determina se ou não o fluxo de trabalho precisa para executar. Um accionador indica-deve ser executada, devolvendo um `200` código de estado. Quando não precisar executar, devolve um `202` código de estado.

### <a name="callback-using-rest-apis"></a>Chamada de retorno utilizando REST APIs
Serviços de podem ligar a um ponto final de aplicação lógica para iniciar um fluxo de trabalho. Para mais informações, consulte [aplicações lógica como passível pontos finais](app-service-logic-connector-http.md) . Para iniciar esse tipo de lógica aplicação a pedido, clique no botão **Executar agora** na barra de comandos. 

<!-- Shared links -->
[Portal do Azure]: https://portal.azure.com 