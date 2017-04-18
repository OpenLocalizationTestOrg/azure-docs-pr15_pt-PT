<properties 
    pageTitle="Como utilizar Blitline para a imagem do guia de funcionalidades de processamento - Azure" 
    description="Saiba como utilizar o serviço de Blitline para processar imagens a partir de uma aplicação Azure." 
    services="" 
    documentationCenter=".net" 
    authors="blitline-dev" 
    manager="jason@blitline.com" 
    editor="jason@blitline.com"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2014" 
    ms.author="support@blitline.com"/>
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Como utilizar Blitline com Azure e armazenamento do Windows Azure

Este guia irá explicar como aceder a serviços de Blitline e como submeter tarefas para Blitline.

## <a name="what-is-blitline"></a>O que é Blitline?

Blitline é uma serviço que fornece o processamento de imagem de nível empresarial a uma fracção do preço que iria custa para criar o seu próprio de processamento de imagens baseado na nuvem.

O facto de ser que processamento de imagens tiver sido concluído repetidamente, normalmente recompilado desde o início para cada site. Isto aperceba porque já criámos-los um milhão vezes demasiado. Vamos decidido que talvez é altura de um dia podemos apenas fazê-lo para todas as pessoas. Vamos saber como fazê-lo, para fazê-lo rápida e eficientemente e guardar todas as pessoas escolar entretanto.

Para mais informações, consulte o artigo [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>O que Blitline não é...

Para esclarecer o que é útil para Blitline, geralmente é mais fácil identificar o Blitline não fazer antes de avançar.

- Blitline não tem widgets HTML para carregar imagens. Tem de ter imagens disponíveis publicamente ou com permissões restritas disponíveis para Blitline alcançar.

- Blitline não suporta imagem direto processamento, como Aviary.com

- Blitline não aceita carregamentos de imagem, não é possível transmitir as imagens para Blitline diretamente. Tem de emissão-los para armazenamento do Windows Azure ou outros lugares Blitline suporta e, em seguida, informe Blitline onde obtê-lo.

- Blitline é previstos excederem consideravelmente paralela e não suporta qualquer processamento síncrono. O que significa que deve fornecer uma postback_url e podemos podem indicar-lhe quando podemos terminados processamento.

## <a name="create-a-blitline-account"></a>Criar uma conta de Blitline

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Como criar uma tarefa de Blitline

Blitline utiliza JSON para definir as ações que pretende manter uma imagem. Este JSON é composta por alguns campos simples.

O exemplo mais simples é da seguinte forma:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Aqui temos JSON que o levam uma imagem de "src" "... boys.jpeg" e, em seguida, redimensione essa imagem para 240 x 140.

O ID da aplicação for algo que pode encontrar na sua separador **Informações da ligação** ou **GERIR** no Azure. É o identificador SECRETO, que permite-lhe executar as tarefas no Blitline.

O parâmetro "Guardar" identifica informações sobre onde pretende colocar a imagem, assim que recomendamos o ter processado. Neste caso comum, ainda não o tenha definido uma. Se não está definida nenhuma localização Blitline armazenam-localmente (e temporariamente) numa localização de nuvem exclusivo. Pode poderão obter nessa localização do JSON devolvido pela Blitline quando fizer a Blitline. O identificador de "imagem" é necessário e é devolvido ao utilizador quando identificar este especial guardado imagem.

Pode encontrar mais informações sobre as *funções* suportamos aqui: <http://www.blitline.com/docs/functions>

Também pode encontrar documentação sobre as opções de tarefa aqui: <http://www.blitline.com/docs/api>

Assim que tiver o seu JSON tudo o que precisa de fazer é **POST** -la para`http://api.blitline.com/job`

Irá obter novamente JSON que tenha este aspeto:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Isto indica-lhe que Blitline recebeu o pedido, tem colocá-la na fila de processamento e quando tiver concluído a imagem estarão disponível na: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_aplicação\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Como guardar uma imagem à sua conta de armazenamento do Windows Azure

Se tiver uma conta de armazenamento do Windows Azure, pode facilmente terá Blitline emissão transformadas imagens para o seu contentor Azure. Ao adicionar "azure_destination" definir a localização e as permissões para Blitline transmitir para.

Eis um exemplo:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Por preencher os valores CAPITALIZED pelas suas próprias, pode submeter este JSON para http://api.blitline.com/job e a imagem "src" será processada com um filtro de esbater e, em seguida, seguia destino Azure.

###<a name="please-note"></a>Tenha em atenção:

As associações de segurança tem de conter o url de SA completo, incluindo o nome de ficheiro do ficheiro de destino.

Exemplo:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Também pode ler a edição mais recente do documentos de armazenamento do Windows Azure do Blitline [aqui](http://www.blitline.com/docs/azure_storage).


## <a name="next-steps"></a>Próximos passos

Visite blitline.com para obter mais informações acerca de todas as nossas outras funcionalidades:

* Ponto final de API Blitline documentos <http://www.blitline.com/docs/api>
* Funções de Blitline API <http://www.blitline.com/docs/functions>
* Exemplos de Blitline API <http://www.blitline.com/docs/examples>
* Terceira parte Nuget biblioteca <http://nuget.org/packages/Blitline.Net>
