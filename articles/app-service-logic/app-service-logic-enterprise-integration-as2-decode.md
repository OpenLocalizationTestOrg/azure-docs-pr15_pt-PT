<properties 
    pageTitle="Saiba mais sobre Enterprise Integration Pack codificar AS2 mensagem Connctor | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
    description="Saiba como utilizar parceiros de aplicações do pacote de integração de empresa e lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="padmavc" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="padmavc"/>

# <a name="get-started-with-decode-as2-message"></a>Introdução ao codificar mensagem AS2

Ligar a mensagem de AS2 codificar para estabelecer segurança e fiabilidade ao transmitir mensagens. Fornece assinatura digital, desencriptação e confirmações através de notificações de eliminação da mensagem (MDN).

## <a name="create-the-connection"></a>Criar a ligação

### <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)

* É necessária uma conta de integração para utilizar codificar AS2 mensagem conector. Ver detalhes sobre como criar uma [Conta de integração](./app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](./app-service-logic-enterprise-integration-partners.md) e um [contrato AS2](./app-service-logic-enterprise-integration-as2.md)

### <a name="connect-to-decode-as2-message-using-the-following-steps"></a>Ligar a mensagem AS2 codificar através dos seguintes passos:

1. [Criar uma aplicação de lógica](./app-service-logic-create-a-logic-app.md) fornece um exemplo.

2. Este conector não tem qualquer accionadores. Utilize os outros accionadores para iniciar a aplicação de lógica, tal como um accionador pedido.  No estruturador de lógica aplicação, adicione um accionador e adicione uma ação.  Selecione Mostrar Microsoft API gerida no menu pendente lista e, em seguida, introduza "AS2" na caixa de pesquisa.  Selecione AS2 – descodificar AS2 mensagem

    ![AS2 de pesquisa](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)

3. Se ainda não tiver criado quaisquer ligações a conta de integração, lhe for pedido para os detalhes de ligação

    ![Criar ligação de integração](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)

4. Introduza os detalhes de integração de conta.  Propriedades com um asterisco são necessárias

  	| Propriedade   | Detalhes |
  	| --------   | ------- |
  	| Nome da ligação *    | Introduza um nome para a sua ligação |
  	| Integração conta * | Introduza o nome da conta de integração. Certifique-se de que a conta de integração e a aplicação de lógica estão na mesma localização Azure |

    Depois de concluído, os detalhes do seu ligação têm um aspeto semelhantes ao seguinte

    ![ligação de integração](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)

5. Selecione **Criar**
    
6. Repare que a ligação foi criada.  Agora, avance com os outros passos na sua aplicação de lógica

    ![ligação de integração criada](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png) 

7. Selecione cabeçalhos e corpo do pedido de resultados

    ![Forneça os campos obrigatórios](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png) 

## <a name="the-as2-decode-does-the-following"></a>O AS2 codificar faz o seguinte

* Processa cabeçalhos AS2/HTTP
* Verifica se a assinatura (se configurado)
* Desencripta as mensagens de (se configurado)
* Descomprime a mensagem (se configurado)
* Reconcilia um MDN recebido com a mensagem original de saída
* Atualizações e relacionado com registos na base de dados não repudiação
* Escreve registos para relatórios de estado AS2.
* Os conteúdos de carga útil saída são codificada base64
* Determina se uma MDN é necessário e se o MDN deve ser síncrono ou assíncrona com base na configuração AS2 contrato
* Gera um MDN comportamento assíncrono ou síncrono (com base em configurações do contrato)
* Define as propriedades e tokens de correlação a MDN

##<a name="try-it-for-yourself"></a>Experimente para si próprio

Por que motivo não experimente. Clique [aqui](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) para implementar uma aplicação de lógica completamente operacional da sua própria utilizando as funcionalidades de lógica aplicações AS2 

## <a name="next-steps"></a>Próximos passos

[Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa") 