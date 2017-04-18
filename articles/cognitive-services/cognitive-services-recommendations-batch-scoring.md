
<properties
    pageTitle="Obter recomendações em lotes: API recomendações de formação de máquina | Microsoft Azure"
    description="Azure máquina recomendações – obter recomendações em lotes de formação"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="luisca"/>

# <a name="get-recommendations-in-batches"></a>Obter recomendações em lotes

>[AZURE.NOTE] Obter recomendações em lotes é mais complicada que introdução recomendações um de cada vez. Verificar as APIs para obter informações sobre como obter recomendações para um único pedido:

> [Recomendações de Item de produto](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [Recomendações de Item de utilizador](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> Só lote pontuação funciona para compilações que foram criadas após 21 de Julho de 2016.


Existem situações em que que precisa para obter recomendações para a mais do que um item de cada vez. Por exemplo, poderá interessar na criação de uma cache de recomendações ou analisar os tipos de recomendações de que está a obter o mesmo.

Operações de pontuação batch, tal como chamamos-las, são operações assíncronas. Tem de submeter o pedido, aguarde concluir a operação e, em seguida, reunir os seus resultados.  

Para ter mais preciso, estes são os passos a seguir:

1.  Crie um contentor de armazenamento do Windows Azure, se ainda não tiver um.
2.  Carregar um ficheiro de entrada que descreve cada um dos pedidos de recomendação ao armazenamento de Blobs do Azure.
3.  Kick-Start a pontuação rotina.
4.  Aguarde que a operação assíncrona concluir.
5.  Quando tiver terminado a operação, reunir os resultados do armazenamento de Blobs.

Vamos guiá-lo através de cada um dos seguintes passos.

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>Criar um contentor de armazenamento, se ainda não tiver um

Aceda ao [portal do Azure](https://portal.azure.com) e criar uma nova conta de armazenamento, se ainda não tiver um. Para executar esta tarefa, navegue para o **Novo** > **dados** + **armazenamento** > **Conta de armazenamento**.

Depois de ter uma conta de armazenamento, tem de criar os contentores de BLOBs onde irá guardar a entrada e saída da execução lote.

Carregar um ficheiro de entrada que descreve cada um dos seus recomendação pedidos ao armazenamento Blob do – vamos chamar aqui input.json o ficheiro.
Depois de ter um contentor, precisar de carregar um ficheiro que descreve cada um dos pedidos de que precisa para executar a partir do serviço de recomendações.

Um lote pode efetuar apenas um tipo de pedido a partir de uma compilação específica. Vamos irá explicam como definir estas informações na secção seguinte. Por agora, vamos assumir que recomendamos irá efetuar recomendações de item terminar uma compilação específica. O ficheiro de entrada, em seguida, contém as informações de entrada (neste caso, os itens de propagação) para cada um dos pedidos.

Este é um exemplo de que o ficheiro input.json tem a seguinte apresentação:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Como pode ver, o ficheiro é um ficheiro JSON, onde cada os pedidos de tem as informações que são necessárias para enviar um pedido de recomendações. Criar um ficheiro JSON semelhante para os pedidos de que precisa para satisfazer e copiá-lo para o contentor que acabou de criar no armazenamento Blob do.

## <a name="kick-start-the-batch-job"></a>O processo de kick-Start

O passo seguinte é submeter uma nova rotina. Para mais informações, consulte a [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

O corpo do pedido da API do tem de definir as localizações onde a entrada, saída e ficheiros de erro têm de ser armazenado. Também precisa definir as credenciais que são necessárias para aceder a essas localizações. Além disso, precisa de especificar alguns parâmetros que se aplicam a todo o lote (o tipo de recomendações para pedir, a modelo de compilação para utilizar, o número de resultados por chamada etc.)

Este é um exemplo do corpo do pedido deve aspeto:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Aqui alguns aspetos importantes a tenha em atenção:

-   Atualmente, **authenticationType** deve ser sempre definido para **PublicOrSas**.

-   Precisa de obter um token de assinatura partilhadas do Access (SA) para permitir a API recomendações ler e escrever ou para a conta de armazenamento de Blobs. Podem encontrar mais informações sobre como gerar tokens SA na [página API recomendações](../storage/storage-dotnet-shared-access-signature-part-1.md).

-   Apenas **apiName** que é atualmente suportadas é **ItemRecommend**, que é utilizado para recomendações de Item para o Item. Lotes atualmente não suportam recomendações de Item de utilizador.

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>Aguarde que a operação assíncrona concluir a

Quando iniciar a operação batch, a resposta devolve o cabeçalho de localização de operação que dá-lhe as informações que são necessárias para controlar a operação.
Controlar a operação ao utilizar a [API do Estado de funcionamento obter]( https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da), tal como faria para controlar a operação de uma operação de compilação.

## <a name="get-the-results"></a>Obter os resultados

Depois da operação for concluída, partindo do princípio que não ocorreram erros, pode reunir os resultados da sua saída Blob armazenamento.

Exemplo que se segue mostram o aspeto que a saída poderá. Neste exemplo, podemos apresentar os resultados para um lote com apenas dois pedidos de (para uma questão de brevidade).

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>Saiba mais sobre as limitações

-   Apenas uma rotina pode ser chamada por subscrição cada vez.
-   Um ficheiro de entrada do lote tarefa não pode ser mais do que 2 MB.
