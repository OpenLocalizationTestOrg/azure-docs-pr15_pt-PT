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

# <a name="get-started-with-decode-x12-message"></a>Introdução ao descodificar X12 mensagem

Validar editar e propriedades específicas do parceiro, gera documento XML para cada conjunto da transação e gera confirmação para transação transformada.

## <a name="create-the-connection"></a>Criar a ligação

### <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure; Pode criar uma [conta gratuita](https://azure.microsoft.com/free)

* É necessária uma conta de integração para utilizar descodificar X12 mensagem conector. Ver detalhes sobre como criar uma [Conta de integração](./app-service-logic-enterprise-integration-create-integration-account.md), [parceiros](./app-service-logic-enterprise-integration-partners.md) e [X12 contrato](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-decode-x12-message-using-the-following-steps"></a>Ligar a mensagem descodificar X12 através dos seguintes passos:

1. [Criar uma aplicação de lógica](./app-service-logic-create-a-logic-app.md) fornece um exemplo

2. Este conector não tem qualquer accionadores. Utilize os outros accionadores para iniciar a aplicação de lógica, tal como um accionador pedido.  No estruturador de lógica aplicação, adicione um accionador e adicione uma ação.  Selecione Mostrar Microsoft API gerida no menu pendente lista e, em seguida, introduza "x12" na caixa de pesquisa.  Selecione X12 – codificar X12 mensagem

    ![Procurar x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png)  

3. Se ainda não tiver criado quaisquer ligações a conta de integração, lhe for pedido para os detalhes de ligação

    ![ligação de conta de integração](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage4.png)    

4. Introduza os detalhes de integração de conta.  Propriedades com um asterisco são necessárias

  	| Propriedade | Detalhes |
  	| -------- | ------- |
  	| Nome da ligação * | Introduza um nome para a sua ligação |
  	| Integração conta * | Introduza o nome da conta de integração. Certifique-se de que a conta de integração e a aplicação de lógica estão na mesma localização Azure |

    Depois de concluído, os detalhes do seu ligação têm um aspeto semelhantes ao seguinte
    
    ![ligação de conta de integração criada](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage5.png) 

5. Selecione **Criar**
    
6. Repare que a ligação foi criada.

    ![detalhes de ligação de conta de integração](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage6.png) 

7. Selecione X12 plano mensagem ficheiro para descodificar

    ![Forneça os campos obrigatórios](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage7.png) 

## <a name="x12-decode-does-following"></a>Codificar X12 faz a seguir

* Validar o envelope contra negociação acordo de parceiro
* Gera um documento XML para cada conjunto da transação.
* Validar editar e propriedades específicas do parceiro
    * Validação estrutural editar e, a validação de esquema expandida
    * Validação da estrutura do envelope de intercâmbio.
    * Validação de esquema do envelope face ao esquema de controlo.
    * Validação de esquema dos elementos de dados do conjunto da transação face ao esquema de mensagem.
    * Validação de editar executado nas elementos da transação conjunto de dados 
* Verifica se os números de controlo de conjunto de intercâmbio, de grupo e da transação não estão duplicados
    * Verifica se o número de controlo de intercâmbio contra intercâmbio recebidos anteriormente.
    * Verifica se o número de controlo do grupo contra outros números de controlo grupo no intercâmbio.
    * Verifica que a transação definir o número de controlo contra outros números de controlo do conjunto da transação nesse grupo.
* Converte o intercâmbio inteiro XML 
    * Dividir intercâmbio como da transação conjuntos - suspender da transação conjuntos no erro: analisa cada transação definir um intercâmbio num documento XML separado. Se uma ou mais transação conjuntos no intercâmbio falhar validação, X12 descodificar suspende apenas esses conjuntos da transação.
    * Dividir intercâmbio como da transação conjuntos - suspender intercâmbio no erro: analisa cada transação definir um intercâmbio num documento XML separado.  Se uma ou mais transação conjuntos no intercâmbio falhar validação, X12 descodificar suspende o intercâmbio inteiro.
    * Preservar intercâmbio - suspender da transação conjuntos no erro: cria um documento XML para o intercâmbio por lotes inteiro. X12 descodificar suspende apenas esses conjuntos da transação cuja validação falhem, enquanto continua a processar todos os outros da transação conjuntos
    * Preservar intercâmbio - suspender intercâmbio no erro: cria um documento XML para o intercâmbio por lotes inteiro. Se uma ou mais transação conjuntos no intercâmbio falhar validação, X12 descodificar suspende o intercâmbio inteiro, 
* Gera uma confirmação técnicos e/ou funcionais (se configurado).
    * Gera uma confirmação técnico estatístico como resultado da validação de cabeçalho. A confirmação técnica relatórios o estado de processamento de um cabeçalho de intercâmbio ou reboque no auscultador de endereço.
    * Gera uma confirmação funcional estatístico como resultado da validação de corpo. A confirmação funcional relatórios cada erro encontrado ao processamento do documento recebido

## <a name="next-steps"></a>Próximos passos

[Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa") 


