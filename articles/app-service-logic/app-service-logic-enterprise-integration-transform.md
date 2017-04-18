<properties 
    pageTitle="Descrição geral do pacote de integração de Enterprise | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
    description="Utilizar as funcionalidades do Enterprise Integration Pack para ativar o processo e integração cenários para empresas com o serviço de aplicação do Microsoft Azure" 
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

# <a name="enterprise-integration-with-xml-transforms"></a>Integração da empresa com transformações XML

## <a name="overview"></a>Descrição geral
O conector de transformação de integração de empresa converte dados de um formato noutro formato. Por exemplo, poderá ter uma mensagem a receber que contém a data atual no formato de YearMonthDay. Pode utilizar uma transformação reformatar uma data no formato de MonthDayYear.

## <a name="what-does-a-transform-do"></a>O que faz uma transformação?
Uma transformação, que é também conhecido como um mapa, é constituída por um esquema XML de origem (a entrada) e um esquema XML de destino (a saída). Pode utilizar funções incorporadas diferentes para o ajudar a manipular ou controlar os dados, incluindo manipulações cadeia, atribuições condicional, expressões aritméticos, formatters de tempo de data e, até mesmo ciclo construções.

## <a name="how-to-create-a-transform"></a>Como criar uma transformação?
Pode criar um mapa/transformação utilizando o Visual Studio [SDK de integração do Enterprise](https://aka.ms/vsmapsandschemas). Quando terminar de criar e testar a transformação, carregue a transformação para a sua conta de integração. 

## <a name="how-to-use-a-transform"></a>Como utilizar uma transformação
Depois de carregar a transformação para a sua conta de integração, pode utilizá-lo para criar uma aplicação de lógica. A aplicação de lógica, em seguida, será executado nas suas transformações sempre que a aplicação de lógica é acionada (e não existe conteúdo entrado que precisa de ser transformados).

**Eis os passos para utilizar uma transformação**:

### <a name="prerequisites"></a>Pré-requisitos 
Na pré-visualização, terá:  

-  [Criar um contentor de funções do Azure] (https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Criar um contentor de funções do Azure")  
-  [Adicionar uma função para o contentor de funções do Azure] (https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "Este modelo cria uma função c# azure webhook com base com capacidades de transformação utilizar em cenários de integração de aplicações de lógica")    
-  Criar uma conta de integração e adicionar-lhe um mapa  

>[AZURE.TIP] Tome nota da do nome do contentor de funções do Azure e a função Azure, terá de-los no próximo passo.  

Agora que tenha o prestado cuidado os pré-requisitos, é altura de criar a sua aplicação de lógica:  

1. Crie uma aplicação de lógica e, em seguida, [ligá-lo à sua conta de integração](./app-service-logic-enterprise-integration-accounts.md "aprender a associar uma conta de integração para uma aplicação de lógica") que contém o mapa.
2. Adicionar um accionador **pedido - pedido de HTTP an quando for recebido** para a sua aplicação de lógica  
![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)    
3. Adicionar a ação **Transformar XML** ao primeiro seleccionando **Adicionar uma ação**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)   
4. Introduza a palavra *transformar* na caixa de pesquisa para filtrar todas as ações ao que pretende utilizar  
![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)  
5. Selecione a ação **Transformar XML**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)  
6. Selecione a **Função contentor** que contém a função que irá utilizar. Este é o nome do contentor Azure funções que criou anteriormente nestes passos.
7. Selecione a **função** que pretende utilizar. Este é o nome da função Azure que criou anteriormente.
8. Adicione **conteúdo** que o utilizador será transformar XML. Tenha em atenção que pode utilizar quaisquer dados XML que recebe no pedido de HTTP, como o **conteúdo**. Neste exemplo, selecione o corpo do pedido de HTTP que o acionou a aplicação de lógica.
9. Selecione o nome do **mapa** que pretende utilizar para efetuar a transformação. Já tem de ser o mapa na sua conta de integração. Um passo anterior, que já lhe atribuiu o acesso à aplicação de lógica à sua conta de integração que contém o seu mapa.
10. Guardar o seu trabalho  
![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png) 

Neste momento, terminar configurar o seu mapa. Numa aplicação do mundo real, pretende armazenar os dados transformados com logaritmos numa aplicação LOB como SalesForce. Pode facilmente como uma ação para enviar a saída da transformação a equipa de vendas. 

Agora pode testar o seu transformação ao efetuar um pedido para o ponto final HTTP.  

## <a name="features-and-use-cases"></a>Funcionalidades e casos de utilização

- Transformação criada num mapa pode ser simple, tais como copiar um nome e endereço de um documento para outro. Em alternativa, pode criar transformações mais complexas utilizando as operações de mapa out of box.  
- Várias operações de mapa ou funções estão facilmente disponíveis, incluindo cadeias, funções de data de tempo e assim sucessivamente.  
- O que pode fazer uma cópia de dados direta entre os esquemas. O mapeamento de pontos incluído no SDK, este é tão simple como desenhar uma linha que liga os elementos no esquema de origem com os respetivos homólogos no esquema de destino.  
- Quando criar um mapa, pode visualizar uma representação gráfica do mapa, que mostram a todas as relações e ligações que cria.
- Utilize a funcionalidade de mapa de teste para adicionar uma mensagem XML de exemplo. Com um clique simple, pode testar o mapa que criou e ver os resultados gerado.  
- Carregar mapas existentes  
- Inclui suporte para o formato XML.


## <a name="learn-more"></a>Saiba mais
- [Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa")  
- [Saiba mais acerca de mapas] (./app-service-logic-enterprise-integration-maps.md "Saiba mais sobre a integração da empresa de mapas")  
 