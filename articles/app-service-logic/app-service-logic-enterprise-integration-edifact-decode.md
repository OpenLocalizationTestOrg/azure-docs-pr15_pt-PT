<properties 
    pageTitle="Saiba mais sobre Enterprise Integration Pack codificar EDIFACT mensagem conexão | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
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

# <a name="get-started-with-decode-edifact-message"></a>Introdução ao codificar mensagem EDIFACT

Validar editar e propriedades específicas do parceiro, gera documento XML para cada conjunto da transação e gera confirmação para transação transformada.

## <a name="create-the-connection"></a>Criar a ligação

### <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)

* É necessária uma conta de integração para utilizar codificar EDIFACT mensagem conector. Ver detalhes sobre como criar uma [Conta de integração](./app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](./app-service-logic-enterprise-integration-partners.md) e [contrato EDIFACT](./app-service-logic-enterprise-integration-edifact.md)

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Ligar a mensagem EDIFACT codificar através dos seguintes passos:

1. [Criar uma aplicação de lógica](./app-service-logic-create-a-logic-app.md) fornece um exemplo.

2. Este conector não tem qualquer accionadores. Utilize os outros accionadores para iniciar a aplicação de lógica, tal como um accionador pedido.  No estruturador de lógica aplicação, adicione um accionador e adicione uma ação.  Selecione Mostrar Microsoft API gerida no menu pendente lista e, em seguida, introduza "EDIFACT" na caixa de pesquisa.  Selecione descodificar EDIFACT mensagem

    ![pesquisa EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
    
3. Se ainda não tiver criado quaisquer ligações a conta de integração, lhe for pedido para os detalhes de ligação

    ![criar a conta de integração](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage2.png)  

4. Introduza os detalhes de integração de conta.  Propriedades com um asterisco são necessárias

  	| Propriedade | Detalhes |
  	| -------- | ------- |
  	| Nome da ligação * | Introduza um nome para a sua ligação |
  	| Integração conta * | Introduza o nome da conta de integração. Certifique-se de que a conta de integração e a aplicação de lógica estão na mesma localização Azure |

    Depois de concluído, os detalhes do seu ligação têm um aspeto semelhantes ao seguinte

    ![conta de integração criada](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage3.png)  

5. Selecione **Criar**

6. Repare que a ligação foi criada

    ![detalhes de ligação de conta de integração](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

7. Selecione EDIFACT ficheiro simples mensagem para descodificar

    ![Forneça os campos obrigatórios](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

## <a name="edifact-decode-does-following"></a>Codificar EDIFACT é que a seguir

* Resolver o contrato ao correspondência a qualificador remetente & identificador e qualificador recetor & Identificador
* Divide intercâmbio vários numa única mensagem em separado.
* Validar o envelope contra negociação acordo de parceiro
* Desassembla o intercâmbio.
* Validar editar e propriedades específicas do parceiro inclui
    * Validação da estrutura do envelope de intercâmbio.
    * Validação de esquema do envelope face ao esquema de controlo.
    * Validação de esquema dos elementos de dados do conjunto da transação face ao esquema de mensagem.
    * Validação de editar executado nas elementos da transação conjunto de dados
* Verifica se de que os números de controlo de conjunto de intercâmbio, de grupo e da transação não são duplicados (se configurado) 
    * Verifica se o número de controlo de intercâmbio contra intercâmbio recebidos anteriormente. 
    * Verifica se o número de controlo do grupo contra outros números de controlo grupo no intercâmbio. 
    * Verifica que a transação definir o número de controlo contra outros números de controlo do conjunto da transação nesse grupo.
* Gera um documento XML para cada conjunto da transação.
* Converte o intercâmbio inteiro XML 
    * Dividir intercâmbio como da transação conjuntos - suspender da transação conjuntos no erro: analisa cada transação definir um intercâmbio num documento XML separado. Se uma ou mais transação conjuntos no intercâmbio validação falhe, em seguida, EDIFACT codificar suspender apenas esses conjuntos da transação. 
    * Dividir intercâmbio como da transação conjuntos - suspender intercâmbio no erro: analisa cada transação definir um intercâmbio num documento XML separado.  Se uma ou mais transação conjuntos no intercâmbio validação falhe, em seguida, EDIFACT codificar suspender o intercâmbio inteiro.
    * Preservar intercâmbio - suspender da transação conjuntos no erro: cria um documento XML para o intercâmbio por lotes inteiro. Codificar EDIFACT suspender apenas esses conjuntos da transação falham de validação, enquanto continuar a processar todos os outros conjuntos da transação
    * Preservar intercâmbio - suspender intercâmbio no erro: cria um documento XML para o intercâmbio por lotes inteiro. Se uma ou mais transação conjuntos no intercâmbio validação falhe, em seguida, EDIFACT codificar suspender o intercâmbio inteiro, 
* Gera uma técnica (controlo) e/ou confirmação funcional (se configurado).
    * Uma confirmação técnico ou o ACK CONTRL reporta os resultados de uma verificação sintácticas do intercâmbio recebido concluída.
    * Uma confirmação funcional reconhece aceitar ou rejeitar um intercâmbio recebido ou um grupo

## <a name="next-steps"></a>Próximos passos

[Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa") 