<properties
   pageTitle="Criar endereços EAI lógica aplicação utilizando VETR nas aplicações de lógica na aplicação de serviço de Azure | Microsoft Azure"
   description="Validar, codificar e transformar funcionalidades dos serviços de BizTalk XML"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="create-eai-logic-app-using-vetr"></a>Criar a aplicação de lógica de endereços EAI com VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

A maior parte dos cenários de integração de aplicação empresarial (EAI) Media dados entre uma origem e destino. Destes cenários têm frequentemente um conjunto comum de requisitos:

- Certifique-se de que os dados a partir de diferentes sistemas são corretamente formatados.
- Execute "pesquisa" dados recebidas para tomar decisões.
- Converta dados de um formato noutro. Por exemplo, converta dados a partir do formato de dados de um sistema CRM para o formato de dados de um sistema ERP.
- Encaminhar dados para o sistema ou aplicação pretendida.

Este artigo mostra-lhe um padrão de integração comuns: "mensagem unidirecional mediação" ou VETR (validar, Enrich, transformar, encaminhar). O padrão VETR mediador dados entre uma entidade de origem e de uma entidade de destino. Normalmente de origem e destino são origens de dados.

Considere a hipótese de um Web site que aceita encomendas. Os utilizadores publicam encomendas para o sistema através de HTTP. Nos bastidores, o sistema de valida os dados de entrada de regularidade, normaliza-o e persisti-la numa fila Bus de serviço para processamento suplementar. O sistema leva encomendas desativar fila de espera, espera-lo num formato específico. Assim, o fluxo de ponto a ponto é:

**HTTP** → **Validar** → **transformar** → **Bus de serviço**

![Fluxo VETR básicas][1]

As seguintes aplicações de API BizTalk ajudar a criar esta padrão:

* **HTTP accionador** - origem para evento de mensagem de accionador
* **Validar** - valida regularidade de dados de entrada
* **Transformar** - transformações dados de receção formato para o formato de uma ferramenta necessária ao sistema descendentes
* **Conector do serviço Bus** – entidade de destino, onde os dados são enviados


## <a name="constructing-the-basic-vetr-pattern"></a>Construir o padrão VETR básico
### <a name="the-basics"></a>Noções básicas

No portal do Azure, selecione **+ Novo**, selecione **Web + Mobile**e, em seguida, selecione a **Aplicação de lógica**. Selecione um nome, localização, subscrição, grupo de recursos e localização que funciona. Grupos de recursos funcionam como contentores para as suas aplicações; todos os recursos para a sua aplicação aceda ao mesmo grupo de recursos.

Em seguida, vamos adicionar accionadores e ações.


## <a name="add-http-trigger"></a>Adicionar HTTP accionador
1. Em **Lógica os modelos da aplicação**, selecione **criar de raiz**.
1. Selecione **Escuta HTTP** a partir da Galeria para criar uma nova escuta. Atribua- **HTTP1**.
2. Definir o **Enviar automaticamente resposta?** definição para FALSO. Configure a ação de accionador por definição _Método HTTP_ a _mensagem_ e definição de _URL relativo_ para _/OneWayPipeline_:  
    ![HTTP accionador][2]
3. Selecione a marca de verificação verde para concluir o accionador.

## <a name="add-validate-action"></a>Adicionar validar ação

Agora, vamos introduza ações que são executadas sempre que o accionador é acionada, ou seja, sempre que é recebida uma chamada no ponto final de HTTP.

1. Adicionar **Validação de XML BizTalk** a partir da Galeria e o nome _(Validate1)_ para criar uma instância.
2. Configure um esquema XSD para validar as mensagens a receber XML. Selecione a ação _para validar_ e selecione _triggers('httplistener').outputs. Conteúdo_ como o valor para o parâmetro _inputXml_ .

Agora, a ação para validar é a primeira acção após a escuta HTTP: 

![Validação de BizTalk XML][3]

Da mesma forma, vamos adicionar o resto das ações. 

## <a name="add-transform-action"></a>Adicionar ação de transformação
Vamos configurar transformações a normalizar os dados de entrada.

1. Adicione **BizTalk transformar serviço** a partir da galeria.
2. Para configurar uma transformação para transformar as mensagens a receber XML, selecione a ação **transformar** como a ação a executar quando esta API chama. Selecione ```triggers(‘httplistener’).outputs.Content``` como o valor para _inputXml_. *Mapa* é um parâmetro opcional, uma vez que os dados de entrada são correspondidos com todas as transformações configuradas e apenas aqueles que corresponde ao esquema são aplicadas.
3. Por fim, a transformação é executado apenas se validar é concluída com êxito. Para configurar esta condição, selecione o ícone de engrenagem no canto superior direito e selecione _Adicionar uma condição seja cumprida_. Defina a condição para ```equals(actions('xmlvalidator').status,'Succeeded')```:  

![BizTalk transformações][4]


## <a name="add-service-bus-connector"></a>Adicionar o conector Bus do serviço
Em seguida, vamos adicionar o destino — uma fila de Bus serviço — para escrever os dados para.

1. Adicione um **Conector de Bus do serviço** a partir da galeria. Definir o **nome** para _Servicebus1_, definir **Cadeia de ligação** para a cadeia de ligação para a instância do seu serviço bus, defina o **Nome de entidade** como _fila de espera_e ignorar o **nome da subscrição**.
2. Selecione a ação de **Enviar mensagem** e configure o campo de **conteúdo** para a ação para _actions('transformservice').outputs. OutputXml_.
3. Configure o campo de **Tipo de conteúdo** para *aplicação/xml*:  

![Serviço Bus][5]


## <a name="send-http-response"></a>Enviar a resposta de HTTP
Depois de processamento de tubagem estiver concluído, novamente envie uma resposta HTTP para o sucesso e falha com os seguintes passos:

1. Adicione uma **Escuta HTTP** a partir da Galeria e selecione a ação de **Enviar HTTP resposta** .
2. Defina o **ID da resposta** para enviar a *mensagem*.
2. **Conteúdo** do conjunto resposta para *processamento de tubagem concluído*.
3. **Código de estado de resposta** para *200* , para indicar HTTP 200 OK.
4. Selecione o menu no canto superior direito de lista pendente e selecione **Adicionar uma condição seja cumprida**.  Defina a condição com a seguinte expressão:  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. Repita estes passos para enviar uma resposta de HTTP em caso de falha também. Alterar a **condição** com a seguinte expressão:  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Selecione **OK** , em seguida, **Criar**.



## <a name="completion"></a>Conclusão
Sempre que alguém lhe envia uma mensagem para o ponto final HTTP, a aplicação de accionadores e executa ações que acabou de criar. Para gerir as aplicações essas lógica criar, selecione **Procurar** no Portal do Azure e selecione **Aplicações lógica**. Selecione a sua aplicação para ver mais informações.

Alguns tópicos úteis:

[Gerir e monitorizar as suas aplicações de API e conectores](app-service-logic-monitor-your-connectors.md)  <br/>
[Monitorize as suas aplicações de lógica](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG
