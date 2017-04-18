<properties 
    pageTitle="Criação de soluções de B2B com Enterprise Integration Pack | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
    description="Saiba mais sobre a receber de dados utilizando as funcionalidades de B2B do pacote de integração de empresa" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="learn-about-receiving-data-using-the-b2b-features-of-the-enterprise-integration-pack"></a>Saiba mais sobre a receber de dados utilizando as funcionalidades de B2B do pacote de integração de empresa#

## <a name="overview"></a>Descrição geral ##

Este documento faz parte de lógica aplicações empresariais Integration Pack. Consulte a descrição geral para saber mais sobre as [capacidades do pacote de integração de empresa](./app-service-logic-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Pré-requisitos ##

Para utilizar o AS2 e X12 ações terá uma conta de integração de empresa

[Como criar uma conta de integração de empresa](./app-service-logic-enterprise-integration-accounts.md)

## <a name="how-to-use-the-logic-apps-b2b-connectors"></a>Como utilizar as conexões B2B de aplicações de lógica ##

Quando tiver criado uma conta de integração e adicionado parceiros e os acordos para o mesmo estiver pronto para criar uma aplicação de lógica que implementa um fluxo de trabalho do business para empresas (B2B).

Este visita guiada verá como utilizar o AS2 e X12 ações para criar uma aplicação de lógica para as empresas que receba dados a partir de um parceiro comercial.

1. Crie uma aplicação de lógica novo e [ligá-lo à sua conta de integração](./app-service-logic-enterprise-integration-accounts.md).  
2. Adicionar um accionador **pedido - pedido de HTTP an quando for recebido** para a sua aplicação de lógica  
![](./media/app-service-logic-enterprise-integration-b2b/flatfile-1.png)  
3. Adicionar a ação **Codificar AS2** pelo primeiro seleccionando **Adicionar uma ação**  
![](./media/app-service-logic-enterprise-integration-b2b/transform-2.png)  
4. Introduza a palavra **as2** na caixa de pesquisa para filtrar todas as ações ao que pretende utilizar  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-5.png)  
6. Selecione a ação **AS2 - AS2 codificar mensagem**  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-6.png)  
7. Como apresentado, adicione o **corpo** que vai levar como entrada. Neste exemplo, selecione o corpo do pedido de HTTP que o acionou a aplicação de lógica. Em alternativa pode introduzir uma expressão aos cabeçalhos no campo de**CABEÇALHOS** de entrada:

    @triggerOutputs()['headers']

8. Adicione **cabeçalhos** que são necessários para AS2. Estes irão ser nos cabeçalhos de pedido HTTP. Neste exemplo, selecione os cabeçalhos do pedido de HTTP que o acionou a aplicação de lógica.
9. Agora adicione a ação de mensagem descodificar X12 selecionando **uma ação de adicionar** novamente  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-9.png)   
10. Introduza a palavra **x12** na caixa de pesquisa para filtrar todas as ações ao que pretende utilizar  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-10.png)  
11. Selecione o **X12-codificar X12 mensagem** ação para adicioná-lo para a aplicação de lógica  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-as2message.png)  
12. Agora tem de especificar a entrada para esta ação do qual será o resultado da ação AS2 acima. O conteúdo da mensagem real é num objeto JSON e é codificado base64. Por conseguinte, precisa de especificar uma expressão, tal como a entrada de dados por isso, introduza a seguinte expressão no campo entrado **X12 plano ficheiro mensagem para DESCODIFICAR**  

    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])  

13. Este passo irá descodificar a X12 dados recebidos do parceiro negociação e saída de um número de itens num objeto JSON. Para permitir que o parceiro saber de recepção dos dados pode enviar novamente uma resposta que contém a notificação de eliminação de mensagem (MDN) a AS2 numa ação de resposta HTTP  
14. Adicionar a ação de **resposta** ao selecionar **Adicionar uma ação**   
![](./media/app-service-logic-enterprise-integration-b2b/b2b-14.png)  
15. Introduza a palavra **resposta** na caixa de pesquisa para filtrar todas as ações ao que pretende utilizar  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-15.png)  
16. Selecione a ação de **resposta** para adicioná-lo  
![](./media/app-service-logic-enterprise-integration-b2b/b2b-16.png)  
17. Definir o campo do **corpo** de resposta, utilizando a seguinte expressão para aceder a MDN a partir dos resultados da ação **descodificar X12 mensagem**  

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])  

![](./media/app-service-logic-enterprise-integration-b2b/b2b-17.png)  
18. Guardar o seu trabalho  
![](./media/app-service-logic-enterprise-integration-b2b/transform-5.png)  

Neste momento, terminar a configurar a sua aplicação B2B lógica. Numa aplicação do mundo real, poderá pretende armazenar a X12 descodificada dados num arquivo de dados ou aplicação LOB. Pode adicionar facilmente mais ações para escrever APIs personalizados para ligar a suas própria aplicações LOB e utilizar estas APIs na sua aplicação de lógica ou faça o seguinte.

## <a name="features-and-use-cases"></a>Funcionalidades e casos de utilização ##

- O AS2 e X12 codificar e descodificar ações permitem-lhe receber dados a partir de e enviar dados para os parceiros utilizando protocolos padrão da indústria utilizar lógica aplicações comerciais  
- Pode utilizar AS2 e X12 com ou sem entre si para intercâmbio de dados com parceiros comerciais conforme necessário
- As ações B2B tornam mais fácil criar parceiros e os acordos na conta de integração e consuma-las numa aplicação de lógica  
- Através do prolongamento da sua aplicação de lógica com outras ações para poder enviar e receber dados de outras aplicações e serviços como o SalesForce e para  

## <a name="learn-more"></a>Saiba mais ##

[Saiba mais sobre o pacote de integração de empresa](./app-service-logic-enterprise-integration-overview.md)  