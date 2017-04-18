<properties 
    pageTitle="Máquina recomendações de formação: Integração de JavaScript | Microsoft Azure" 
    description="Documentação de máquina aprendizagem recomendações - integração com o JavaScript - Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Azure máquina recomendações - JavaScript integração de formação

>[AZURE.NOTE]Deverá começar a utilizar o serviço de disfunção API recomendações em vez de nesta versão. O serviço de disfunção recomendações irá substituição deste serviço e todas as novas funcionalidades, serão desenvolvidas aí. Tem novas funcionalidades como lotes suporte, uma melhor API Explorer, uma experiência de início de sessão no/faturação superfície, mais consistente API mais limpeza, etc.
> Saiba mais sobre como [Migrar para o novo serviço disfunção](http://aka.ms/recomigrate)


Este documento representar como integrar o seu site com JavaScript. O JavaScript permite-lhe para enviar dados aquisição eventos e consumir recomendações depois de criar um modelo de recomendação. Todas as operações concluídas através do JS podem ser processadas também a partir do lado do servidor.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1. Descrição geral
Integrar o seu site com o Azure ML recomendações são compostos por 2 fases:

1.  Envie eventos para Azure ML recomendações. Isto permitirá construa um modelo de recomendação.
2.  Consuma as recomendações. Depois do modelo é criado pode consumir as recomendações. (Este documento não explicam como criar um modelo, leia o guia de introdução para obter mais informações sobre como).


<ins>Fase posso</ins>

Na primeira fase inserir nas suas páginas de html numa biblioteca de JavaScript pequenas que permite a página para enviar eventos à medida que estes ocorrem na página html para os servidores de Azure ML recomendações (através do Data Market):

![Drawing1][1]

<ins>Fase II</ins>

Na segunda fase quando pretende mostrar as recomendações na página selecionar uma das seguintes opções:

1. a sua server (na fase de composição da página) chamadas Azure ML recomendações Server (através do Data Market) para obter recomendações. Os resultados incluir uma lista de itens id. O servidor tem de enriquecer os resultados com os itens Meta dados (por exemplo, imagens, descrição) e enviar a página criada para o browser.

![Drawing2][2]

2. a outra opção é utilizar o ficheiro JavaScript pequenas da fase um para obter uma lista de itens recomendadas simple. Os dados recebidos aqui serão leaner na primeira opção.

![Drawing3][3]

##<a name="2-prerequisites"></a>2. Pré-requisitos de

1. Crie um novo modelo utilizando as APIs. Consulte o guia de introdução sobre como fazê-lo.
2. Codificar seu &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; com base64. (Isto vai ser utilizado para a autenticação básica para activar o código JS ligar as APIs).


##<a name="3-send-data-acquisition-events-using-javascript"></a>3. enviar eventos de aquisição de dados utilizando JavaScript
Os passos seguintes facilitar a enviar eventos:

1.  Inclua JQuery biblioteca no seu código. Pode transferi-la a partir do nuget no URL seguinte.

        http://www.nuget.org/packages/jQuery/1.8.2
2.  Incluir a biblioteca de recomendações Java Script a partir do seguinte URL: http://aka.ms/RecoJSLib1

3.  Iniciar a biblioteca do Azure ML recomendações com os parâmetros adequados.

        <script>
            AzureMLRecommendationsStart("<base64encoding of username:key>",
            "<model_id>");
        </script>

4.  Envie o evento adequado. Consulte a secção detalhada abaixo em todos os tipos de eventos (exemplo de clique em evento)

        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") {      
                        AzureMLRecommendationsEvent = [];
                    }
            AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
        </script>


###<a name="31-limitations-and-browser-support"></a>3.1. Limitações e suporte de Browser
Esta é uma implementação de referência e é dado tal como está. Este deverá suportar todos os browsers principais.

###<a name="32-type-of-events"></a>3,2. Tipo de eventos
Existem 5 tipos de evento que suporte a biblioteca: clique em, clique em recomendação, adicionar ao carrinho de compras, remover a partir do Centro de cópias carrinho e compra. Existe um evento adicional que é utilizado para definir o contexto de utilizador denominado de início de sessão.

####<a name="321-click-event"></a>3.2.1. Clique em evento
Este evento deve ser utilizado a qualquer altura, um utilizador clicada num item. Normalmente, quando o utilizador clica num item uma nova página é aberta com os detalhes do item; nesta página deve ser acionou este evento.

Parâmetros:
- evento (cadeia, obrigatória) - "clique"
- item (cadeia, obrigatória) - Identificador exclusivo do item
- itemName (cadeia, opcional) - o nome do item
- itemDescription (cadeia, opcional) - a descrição do item
- itemCategory (cadeia, opcional) - a categoria do produto
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

Ou com os dados opcionais:

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


####<a name="322-recommendation-click-event"></a>3.2.2. Recomendação clique em evento
Este evento deve ser utilizado a qualquer altura, um utilizador clicado num item que foi recebido a partir do Azure ML recomendações como um item recomendado. Normalmente, quando o utilizador clica num item uma nova página é aberta com os detalhes do item; nesta página deve ser acionou este evento.

Parâmetros:
- evento (cadeia, obrigatória) - "recommendationclick"
- item (cadeia, obrigatória) - Identificador exclusivo do item
- itemName (cadeia, opcional) - o nome do item
- itemDescription (cadeia, opcional) - a descrição do item
- itemCategory (cadeia, opcional) - a categoria do produto
- plantas (cadeia matriz, opcional) - o que gerou a consulta de recomendação.
- recoList (cadeia matriz, opcional) - o resultado de pedido de recomendação que gerou o item que foi clicado.
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

Ou com os dados opcionais:

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]               });
        </script>


####<a name="323-add-shopping-cart-event"></a>3.2.3. Adicionar compras de carrinho de evento
Este evento deve ser utilizado quando o utilizador adicionar um item ao carrinho de compras.
Parâmetros:
* evento (cadeia, obrigatória) - "addshopcart"
* item (cadeia, obrigatória) - Identificador exclusivo do item
* itemName (cadeia, opcional) - o nome do item
* itemDescription (cadeia, opcional) - a descrição do item
* itemCategory (cadeia, opcional) - a categoria do produto
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

####<a name="324-remove-shopping-cart-event"></a>3.2.4. Remover o evento carrinho de compras
Este evento deve ser utilizado quando o utilizador remove um item ao carrinho de compras.

Parâmetros:
* evento (cadeia, obrigatória) - "removeshopcart"
* item (cadeia, obrigatória) - Identificador exclusivo do item
* itemName (cadeia, opcional) - o nome do item
* itemDescription (cadeia, opcional) - a descrição do item
* itemCategory (cadeia, opcional) - a categoria do produto
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

####<a name="325-purchase-event"></a>3.2.5. Comprar pacote de evento
Este evento deve ser utilizado quando o utilizador adquirido o seu carrinho de compras.

Parâmetros:
* evento (cadeia) - "comprar"
* itens (adquiridos []) - matriz mantém uma entrada para cada item adquirido.<br><br>
Formato de comprado:
    * item (cadeia) - Identificador exclusivo do item.
    * contar (int ou cadeia) - número de itens que foram compradas.
    * preço (flutuante ou cadeia) - campo opcional - o preço do item.

O exemplo abaixo apresenta compra de 3 itens (33, 34, 35), dois com todos os campos de povoada (item, contagem, preço) e outra (item 34) sem um preço.

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

####<a name="326-user-login-event"></a>3.2.6. Eventos de início de sessão do utilizador
Biblioteca de ML recomendações evento Azure cria e utilizar um cookie para identificar eventos provenientes do mesmo browser. Para melhorar os resultados do modelo Azure ML recomendações permite para definir uma identificação exclusivo do utilizador que irá substituir a utilização de cookies.

Este evento deve ser utilizado após o início de sessão do utilizador para o seu site.

Parâmetros:
* evento (cadeia) - "userlogin"
* utilizador (cadeia) - identificação exclusivo do utilizador.
        <script>
  Se (typeof AzureMLRecommendationsEvent = = "não definido") {AzureMLRecommendationsEvent = [];} AzureMLRecommendationsEvent.push ({evento: "userlogin", utilizador: "ABCD10AA"});      </script>

##<a name="4-consume-recommendations-via-javascript"></a>4. consumir recomendações através de JavaScript
O código que consome recomendação é acionado por algum evento JavaScript por página Web do cliente. A resposta de recomendação inclui os Ids de itens recomendados, os seus nomes e os respetivos classificações. É melhor utilizar esta opção apenas para exibir uma lista dos itens recomendados - processamento mais complexo (como adicionar metadados do item), deve ser feito sobre a integração do lado do servidor.

###<a name="41-consume-recommendations"></a>4.1 consumir recomendações
Consumir recomendações que terá de incluem as bibliotecas de JavaScript necessárias na sua página de e para chamar AzureMLRecommendationsStart. Consulte a secção 2.

Consumir recomendações para um ou mais itens que precisa de telefonar um método chamado: AzureMLRecommendationsGetI2IRecommendation.

Parâmetros:
* itens (matriz de cadeias) - um ou mais itens para obter recomendações para. Se consumir uma compilação Fbt, em seguida, pode definir aqui apenas um item.
* numberOfResults (int) - número de resultados necessários.
* includeMetadata (booleano, opcional) - se definida como 'true' indica que o campo de metadados tem de ser preenchido no resultado.
* Função - uma função que irá processar as recomendações de processamento devolvido. Os dados são devolvidos como uma matriz de:
    * Id exclusivo item - item
    * name - item nome (se existir no catálogo)
    * classificação - classificação de recomendação
    * metadados - uma cadeia que representa os metadados do item

Exemplo: O seguinte código de pedidos de 8 recomendações para o item "64f6eb0d-947a-4c18-a16c-888da9e228ba" (e especificando não includeMetadata - implicitamente indica que não são necessário nenhuma metadados),-em seguida, concatenar os resultados para um intervalo de tempo.

        <script>
            var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                var buff = "";
                for (var ii = 0; ii < reco.length; ii++) {
                    buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                }
                alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 
