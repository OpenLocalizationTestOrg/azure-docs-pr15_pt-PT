<properties 
    pageTitle="Saiba mais sobre Enterprise Integration Pack codificar X12 mensagem Connctor | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
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

# <a name="get-started-with-encode-x12-message"></a>Introdução ao codificar X12 mensagem

Validar editar e propriedades específicas do parceiro, converte codificada XML mensagens Editar da transação conjuntos no intercâmbio e os pedidos de uma confirmação técnicos e/ou funcionais

## <a name="create-the-connection"></a>Criar a ligação

### <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)

* É necessária uma conta de integração para utilizar codificar x12 mensagem conector. Ver detalhes sobre como criar uma [Conta de integração](./app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](./app-service-logic-enterprise-integration-partners.md) e [X12 contrato](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-encode-x12-message-using-the-following-steps"></a>Ligar a mensagem codificar X12 através dos seguintes passos:

1. [Criar uma aplicação de lógica](./app-service-logic-create-a-logic-app.md) fornece um exemplo

2. Este conector não tem qualquer accionadores. Utilize os outros accionadores para iniciar a aplicação de lógica, tal como um accionador pedido.  No estruturador de lógica aplicação, adicione um accionador e adicione uma ação.  Selecione Mostrar Microsoft API gerida no menu pendente lista e, em seguida, introduza "x12" na caixa de pesquisa.  Selecione um dos X12-codificar X12 mensagem por nome de contrato ou X12-descodificar a mensagem de X 12 por identidades.  

    ![Procurar x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png) 

3. Se ainda não tiver criado quaisquer ligações a conta de integração, lhe for pedido para os detalhes de ligação

    ![ligação de conta de integração](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png) 


4. Introduza os detalhes de integração de conta.  Propriedades com um asterisco são necessárias

  	| Propriedade | Detalhes |
  	| -------- | ------- |
  	| Nome da ligação * | Introduza um nome para a sua ligação |
  	| Integração conta * | Introduza o nome da conta de integração. Certifique-se de que a conta de integração e a aplicação de lógica estão na mesma localização Azure |

    Depois de concluído, os detalhes do seu ligação têm um aspeto semelhantes ao seguinte

    ![ligação de conta de integração criada](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png) 


5. Selecione **Criar**

6. Repare que a ligação foi criada.

    ![detalhes de ligação de conta de integração](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png) 

#### <a name="x12---encode-x12-message-by-agreement-name"></a>X12-codificar X12 mensagem por nome de contrato

7. Selecione X12 contrato a partir da mensagem pendente e xml para codificar.

    ![Forneça os campos obrigatórios](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png) 

#### <a name="x12---encode-x12-message-by-identities"></a>X12-codificar X12 mensagem por identidades

7.  Fornecer identificador do remetente, remetente, identificador recetor e qualificador qualificador recetor, tal como está configurado na X12 contrato.  Selecionar mensagem xml para codificar

    ![Forneça os campos obrigatórios](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png) 

## <a name="x12-encode-does-following"></a>Codificar X12 faz a seguir:

* Resolução de contrato através da correspondência de propriedades de contexto do remetente e do destinatário.
* Serializes o intercâmbio de editar, converter um codificada XML mensagens Editar da transação conjuntos no intercâmbio.
* Aplica-se segmentos de cabeçalho e reboque do conjunto da transação
* Gera um número de controlo de intercâmbio, um número de controlo do grupo e um número de controlo da transação definido para cada intercâmbio de saída
* Substitui os separadores nos dados de carga útil
* Validar editar e propriedades específicas do parceiro
    * Validação de esquema dos elementos de dados do conjunto da transação contra a mensagem de esquema
    * Validação de editar executado nas elementos da transação conjunto de dados.
    * Validação expandida executado nas elementos da transação conjunto de dados
* Pede uma confirmação técnicos e/ou funcionais (se configurado).
    * Gera uma confirmação técnico estatístico como resultado da validação de cabeçalho. A confirmação técnica reporta o estado de processamento de um cabeçalho de intercâmbio ou reboque no auscultador de endereço
    * Gera uma confirmação funcional estatístico como resultado da validação de corpo. A confirmação funcional relatórios cada erro encontrado ao processamento do documento recebido

## <a name="next-steps"></a>Próximos passos

[Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa") 

