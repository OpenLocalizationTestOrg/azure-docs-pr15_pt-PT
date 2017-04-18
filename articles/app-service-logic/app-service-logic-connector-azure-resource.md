<properties
   pageTitle="Utilizando o conector de recurso Azure nas aplicações de lógica | Aplicação de serviço do Microsoft Azure"
   description="Como criar e configurar a aplicação de conector de recursos do Azure ou API e utilizá-la numa aplicação lógica na aplicação de serviço do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>

# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Começar a trabalhar com o conector de recursos do Azure e adicioná-lo para a sua aplicação de lógica
>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica.

Utilize o conector de recursos do Azure para gerir facilmente Azure recursos dentro da sua aplicação de lógica.

## <a name="create-the-azure-resource-connector"></a>Criar o conector de recurso Azure
Para utilizar a aplicação de API do Azure recurso conexão, é necessário criar primeiro uma instância do mesmo. Isto pode ser feito quer inline ao criar uma aplicação de lógica ou ao selecionar a aplicação do Azure recurso Gestor conexão API do Azure Marketplace.

Configurá-lo, tem de que tem de definir o capital um serviço com permissões para fazer tudo o que é que pretende fazer no Azure. Serão feitas todas as chamadas em nome de capital este serviço que configurou. Esta opção permite-lhe a conexão para utilizar apenas exatamente o que pretende que seja fazer o âmbito e nada mais.

David Ebbo tem escrito [uma mensagem no blogue excelente](http://blog.davidebbo.com/2014/12/azure-service-principal.html) sobre como configurar esta tarefa. Fórum siga todas as instruções de não existem e obter o seu **ID de inquilino**, o **ID de cliente** e a **palavra-passe**. Estes três campos, juntamente com o **ID da subscrição**, são o que são necessário para configurar o conector.

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>Utilizar o conector de recursos do Azure no designer de aplicações de lógica
### <a name="trigger"></a>Accionador
Existem duas accionadores que são suportadas no Connector:

Nome | Descrição
---- | -----------
Evento ocorre | Accionador quando ocorre um evento a um recurso na sua subscrição.
Métrica cruza limiar |  Accionador quando uma métrica cumpre um determinado limiar.

### <a name="action"></a>Ação

Da mesma forma, pode fornecer um grande número de ações dentro da sua subscrição do Azure:

Para **grupos de recursos** , pode:

Nome | Descrição
---- | -----------
Lista os grupos de recursos | Liste todos os grupos de recursos na subscrição.
Obter o grupo de recursos | Obter um grupo de recursos pelo respectivo id.
Criar grupo de recursos | Criar ou actualizar um grupo de recursos.
Eliminar o grupo de recursos | Elimine um grupo de recursos.

Para **recursos** , pode:

Nome | Descrição
---- | -----------
Recursos da lista | Recursos da lista na sua subscrição por diferentes tipos de filtros.
Obter recursos | Obter um único recurso pelo seu recurso ID.
Criar ou actualizar o recurso | Criar um recurso ou atualizar um recurso existente. Tem de fornecer todas as propriedades para esse recurso.
Ação de recursos |  Execute qualquer outra ação num recurso. Tem de saber o nome de ação e a carga de útil que esta ação leva-o até (se existirem).
Eliminar recurso | Elimine um recurso.

Para **Fornecedores de recursos** , pode:

Nome | Descrição
---- | -----------
Fornecedores de recursos de lista | Liste todos os fornecedores de recursos disponíveis na subscrição.

Para **Implementações do grupo de recursos** , pode:

Nome | Descrição
---- | -----------
Lista híbridas | Liste todos os de implementações num grupo de recursos.
Obter implementação | Obter uma implementação do modelo pelo respectivo id.
Criar implementação | Crie uma nova implementação de grupo de recursos ao fornecer um modelo.

Para **eventos** sobre recursos, pode:

Nome | Descrição
---- | -----------
Obter eventos | Obter eventos de uma subscrição ou para um recurso.

Para **métricas** sobre recursos, pode:

Nome | Descrição
---- | -----------
Obter métricas | Obter uma métrica para um recurso ID.

## <a name="do-more-with-your-connector"></a>Fazer mais com a conexão
Agora que a conexão for criada, pode adicioná-la para um fluxo de empresas com uma aplicação de lógica. Consulte o artigo [quais são as aplicações de lógica?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Se pretender começar com aplicações do Azure lógica antes de inscrever-se para uma conta do Azure, aceda a [lógica Experimente a aplicação](https://tryappservice.azure.com/?appservice=logic), onde imediatamente pode criar uma aplicação de lógica starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

Ver a referência Swagger REST API em [API e conectores aplicações referência](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md
