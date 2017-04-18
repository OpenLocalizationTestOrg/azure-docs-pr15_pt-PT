
<properties
    pageTitle="Migrar para o Azure serviços disfunção recomendações API das recomendações DataMarket API | Microsoft Azure"
    description="Azure máquina recomendações – migrar para o serviço disfunção recomendações de formação"
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
    ms.date="09/01/2016"
    ms.author="luisca"/>


# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>Migrar para o Azure serviços disfunção recomendações API das recomendações DataMarket API
Este artigo mostra-lhe como migrar a partir da [Microsoft DataMarket recomendações API](https://datamarket.azure.com/dataset/amla/recommendations) para [Cognitivas serviços recomendações API do Microsoft Azure](https://www.microsoft.com/cognitive-services/en-us/recommendations-api).

A API do recomendações DataMarket deixará de aceitar novos clientes 31 de Dezembro de 2016 e vai ser preterida 28 de Fevereiro de 2017.

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>Como posso começar a utilizar a API de recomendações de serviços disfunção do Azure?

Para migrar para o disfunção recomendações API de serviços, faça o seguinte:

1.  Se ainda não tiver uma subscrição do Azure, [Inscrever-se](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para um. 

1.  Obtenha instruções passo a passo do [Guia de introdução](cognitive-services-recommendations-quick-start.md) para inscrever-se para a disfunção API de recomendações de serviços e utilizá-lo através de programação. 

1.  Vá para a [página de destino disfunção Services recomendações API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) para saber mais sobre o serviço e encontrar documentação.

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>IU de recomendações que habituado a criar os meus modelos. Existe uma ferramenta semelhante para a disfunção API de recomendações de serviços?

Totalmente! O URL para a nova [Recomendações IU](http://recommendations-portal.azurewebsites.net/) é http://recommendations-portal.azurewebsites.net. 

>[AZURE.NOTE] As suas credenciais do DataMarket não irão funcionar aqui. Inscrever-se para uma subscrição no Portal do Azure e obtenha a chave de conta, se necessário, para utilizar a nova [IU recomendações](http://recommendations-portal.azurewebsites.net/).
Se não tiver uma chave de conta, consulte o artigo tarefa 1 do [Guia de introdução](cognitive-services-recommendations-quick-start.md).

##<a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>O novo formato de API é igual a API do recomendações DataMarket?

API suporta os mesmos cenários e fluxos de processo como essas cenários suportados pela versão do DataMarket, mas a interface de API real foi atualizada para se ajustarem às [Diretrizes do Microsoft REST API](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). As APIs são mais consistentes e trabalhar melhor com ferramentas que suportam Swagger.

Para compreender cada uma das APIs, consulte o artigo o [API explorer](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db).
Utilize o *experimentá* -lo do botão para testar uma chamada de API. O formato de ficheiros média consumida por API recomendações (ficheiros de catálogo e a utilização) não foi alterado, por isso pode manter a utilizar o mesmo ficheiros e/ou infraestrutura que criou para gerar os ficheiros.

##<a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>Quais são algumas funcionalidades novas no disfunção API de recomendações de serviços?

Nos últimos dois meses, foram disponibilizadas novas capacidades para a disfunção API de recomendações de serviços:
-   Recomendações IU de formação e testes modelos sem ter de escrever uma única linha de código
-   Lote pontuação para fornecer-lhe milhares de recomendações ao mesmo tempo
-   Construir métricas de suporte a consulta a qualidade dos modelos de recomendações
-   Suporte para as regras de negócio
-   Capacidade de enumerar e transferir ficheiros a utilização e de catálogo
-   Classificação de compilação de suporte para consultar a qualidade das funcionalidades de item num modelo de recomendações
-   Adicionado capacidade para procurar um produto no catálogo de

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Quando é que a Microsoft parar a API do DataMarket recomendações de suporte?

[API de recomendações sobre DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) deixa de aceitar novos clientes após 31 de Dezembro de 2016 e vai ser completamente preterida por 28 de Fevereiro de 2017. 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>O que acontece se não tiver os dados que é necessário para recriar os meus modelos no disfunção API de recomendações de serviços?

Pretendemos efetuar esta transição tão fácil como possíveis para si. Podemos ajudá-lo mover os modelos de antigos da sua conta do DataMarket à sua subscrição do Azure cognitivas serviços recomendações API nova. Contacte-na [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Solicitamos-lhe que forneça os DataMarket ID da subscrição e o seu ID de subscrição de serviços disfunção, antes de podemos associar os modelos com a sua nova conta.
