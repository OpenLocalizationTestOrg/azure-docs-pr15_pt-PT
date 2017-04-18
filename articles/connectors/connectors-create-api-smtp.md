<properties
pageTitle="SMTP | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. Ligar a SMTP para enviar e-mail."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-smtp-connector"></a>Começar a trabalhar com o conector SMTP

Ligar a SMTP para enviar e-mail.

Para utilizar [qualquer conexão](./apis-list.md), tem primeiro criar uma aplicação de lógica. Pode começar por [criar uma aplicação de lógica agora](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>Ligar a SMTP

Antes de pode aceder a sua aplicação de lógica qualquer serviço, primeiro tem de criar uma *ligação* ao serviço. Uma [ligação](./connectors-overview.md) fornece conectividade entre uma aplicação de lógica e outro serviço. Por exemplo, para poder ligar à SMTP, tem primeiro uma *ligação*de SMTP. Para criar uma ligação, terá de fornecer as credenciais que normalmente utiliza para aceder ao serviço que pretende ligar. Sim, no exemplo SMTP, precisa as credenciais para a sua ligação nome, endereço do servidor SMTP e informações de início de sessão do utilizador para poder criar a ligação ao SMTP. [Saiba mais acerca das ligações]()  

### <a name="create-a-connection-to-smtp"></a>Criar uma ligação a SMTP

>[AZURE.INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]

## <a name="use-an-smtp-trigger"></a>Utilize um acionador de SMTP

Um accionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação de lógica. [Saiba mais sobre os accionadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, uma vez que SMTP não ter um accionador da sua própria, vamos utilizar o accionador **Salesforce - quando é criado um objeto** . Este accionador ativará quando é criado um novo objeto no Salesforce. Para o nosso exemplo, vamos irá configure-o tal que sempre que é criada uma nova oportunidade potencial no Salesforce, ocorre uma ação *Enviar e-mail* através do conector de SMTP com uma notificação da nova oportunidade potencial a ser criada.

1. Introduza *salesforce* na caixa de pesquisa no estruturador de aplicações de lógica, em seguida, selecione o accionador **Salesforce - quando é criado um objeto** .  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  

2. É apresentado o controlo **quando é criado um objeto** .
 ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  

3. Selecione o **Tipo de objeto** , em seguida, selecione *conduzir* a partir da lista de objetos. Neste passo são indicando que está a criar um accionador que informa a sua aplicação de lógica sempre que a equipa de vendas. é criada uma nova oportunidade potencial.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  

4. O accionador foi criado.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Utilizar uma ação SMTP

Uma ação é uma operação realizada pelo fluxo de trabalho definido numa aplicação de lógica. [Saber mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Agora que foi adicionado ao accionador, siga estes passos para adicionar uma ação de SMTP irá ocorrer quando Salesforce. é criada uma nova oportunidade potencial.

1. Selecione **+ novo passo** para adicionar a ação que gostaria de tomar quando é criada uma nova oportunidade potencial.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  

2. Selecione **Adicionar uma ação**. Esta abre-se a caixa de pesquisa onde pode procurar por qualquer ação pretende utilizar para tirar.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  

3. Introduza *smtp* para procurar acções relacionadas com a SMTP.  

4. Selecione **SMTP - enviar correio eletrónico** como a ação a tomar quando a nova oportunidade potencial é criada. Abre o bloco de controlo de ação. Terá de estabelecer a ligação smtp no bloco de Estruturador, se ainda não feito anteriormente.  
 ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    

5. Informações do seu e-mail pretendida no bloco de **SMTP - enviar correio eletrónico** de entrada.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  

6. Guarde o seu trabalho para ativar o fluxo de trabalho.  

## <a name="technical-details"></a>Detalhes técnicos

Aqui estão os detalhes sobre accionadores, ações e as respostas que suporta esta ligação:

## <a name="smtp-triggers"></a>SMTP accionadores

SMTP não tem accionadores. 

## <a name="smtp-actions"></a>Ações de SMTP

SMTP tem a seguinte ação:


|Ação|Descrição|
|--- | ---|
|[Enviar mensagens de E-Mail](connectors-create-api-smtp.md#send-email)|Esta operação envia uma mensagem de e-mail para um ou mais destinatários.|

### <a name="action-details"></a>Detalhes de ação

Aqui estão os detalhes para a ação deste conexão, juntamente com as suas respostas:


### <a name="send-email"></a>Enviar mensagens de E-Mail
Esta operação envia uma mensagem de e-mail para um ou mais destinatários. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|Para|Para|Especificar endereços de e-mail separados por ponto e vírgula comorecipient1@domain.com;recipient2@domain.com|
|CC|Cc|Especificar endereços de e-mail separados por ponto e vírgula comorecipient1@domain.com;recipient2@domain.com|
|Assunto|Assunto|Assunto da mensagem de correio electrónico|
|Corpo|Corpo|Corpo da mensagem de correio electrónico|
|A partir do|A partir do|Endereço de e-mail do remetente comosender@domain.com|
|IsHtml|É Html|Envie o e-mail como HTML (verdadeiro/falso)|
|Bcc|Bcc|Especificar endereços de e-mail separados por ponto e vírgula comorecipient1@domain.com;recipient2@domain.com|
|Importância|Importância|Importância da mensagem de correio electrónico (alta, Normal ou mínimo)|
|ContentData|Dados de conteúdo de anexos|Dados de conteúdos (codificado em base64 para sequências e como-destina-se cadeia)|
|ContentType|Tipo de conteúdo de anexos|Tipo de conteúdo|
|ContentTransferEncoding|Codificação de transferência conteúdo anexos|Transferir codificação de conteúdo (base64 ou nenhum)|
|Nome de ficheiro|Nome de ficheiro de anexos|Nome do ficheiro|
|ContentId|ID de conteúdo de anexos|Id de conteúdo|

Um * indica que não é necessária uma propriedade


## <a name="http-responses"></a>Respostas HTTP

Ações e accionadores acima, poderá regressar um ou mais dos seguintes códigos de estado HTTP: 

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido.|
|predefinido|A operação falhou.|

## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)