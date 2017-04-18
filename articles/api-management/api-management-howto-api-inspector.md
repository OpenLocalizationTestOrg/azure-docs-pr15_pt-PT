<properties 
    pageTitle="Como utilizar o Inspector de API para rastreio chama-se de gestão de API do Azure" 
    description="Saiba como analisar chamadas utilizando o Inspector de API na gestão de API do Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Como utilizar o Inspector de API para rastreio chama-se de gestão de API do Azure

Gestão de API fornece uma ferramenta de Inspector de API para ajudá-lo com o seu APIs de resolução de problemas e depuração. O Inspector de API podem ser utilizado através de programação e também podem ser utilizado diretamente a partir do portal de programador. 

Para além de operações de rastreio, API Inspector controla também avaliações [expressão de política](https://msdn.microsoft.com/library/azure/dn910913.aspx) . Para uma demonstração sobre, consulte o artigo [nuvem de folha de rosto episódio 177: mais funcionalidades de gestão de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) e para a frente rápido para 21:00.

Este guia fornece uma guia passo a passo de utilização de API Inspetor.

>[AZURE.NOTE] API Inspector rastreios apenas são gerados e disponibilizados para pedidos de teclas de subscrição que pertencem à conta de [administrador](api-management-howto-create-groups.md) .

## <a name="trace-call"></a> Utilização Inspector de API para rastrear uma chamada

Para utilizar a API do Inspector, adicione uma **rastreio de apim ocp: VERDADEIRO** pedir cabeçalho para a chamada de operação, em seguida, transfira-o e inspecionar o rastreio de utilizar o URL indicado pelo cabeçalho de resposta **ocp apim-rastreio localização** . Pode ser efetuado forma programática e também pode ser efetuado diretamente a partir do portal de programador.

Este tutorial mostra como utilizar o Inspector de API para utilizar a API Calculadora básicas que esteja configurado no tutorial introdução ao obter [Gerir o seu primeiro API](api-management-get-started.md) de operações de rastreio. Se ainda não concluídas desse tutorial apenas bastam alguns minutos para importar a API Calculadora básicas ou pode utilizar outra API da sua escolha, tal como a API eco. Cada instância do serviço de gestão de API vem com uma API eco que podem ser utilizados para experimentar e saiba mais sobre a gestão de API pré-configurada. API eco devolve novamente qualquer entrada é enviada para o mesmo. Para utilizá-la, pode invocar qualquer verbo HTTP e o valor de retorno simplesmente será que enviou. 



Para começar a, clique em **developer portal para** no Portal clássica do seu serviço de gestão de API do Azure. Operações podem ser chamadas diretamente a partir do portal de programador que fornece uma forma conveniente para ver e testar a operações de uma API.

>Se ainda não tiver criado uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

![Portal do Programador de gestão de API][api-management-developer-portal-menu]

Clique **APIs** a partir do menu superior e, em seguida, clique em **Calculadora básica**.

![API eco][api-management-api]

Clique em **experimentar** para tentar a operação de **Adicionar dois números inteiros** .

![Experimente][api-management-open-console]

Manter a predefinição valores de parâmetros e selecione a chave de subscrição para o produto que pretende utilizar a partir da **chave de subscrição** pendente.

Por predefinição no portal do programador o **Rastreio de Apim Ocp** cabeçalho já está definido como **Verdadeiro**. Este cabeçalho configura se ou não é gerado um rastreio.

![Enviar][api-management-http-get]

Clique em **Enviar** para invocar a operação.

![Enviar][api-management-send-results]

Na resposta cabeçalhos será um **ocp apim-rastreio localização** com um valor semelhante ao exemplo seguinte.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

O rastreio pode ser transferido a partir da localização especificada e revisto conforme demonstrado no próximo passo.

## <a name="inspect-trace"> </a>Inspecionar o rastreio

Para rever os valores no rastreio, transfira o ficheiro de rastreio a partir do URL da **ocp apim-rastreio de localização** . É um ficheiro de texto no formato JSON e contém entradas semelhantes ao exemplo seguinte.

    {
        "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
        "traceEntries": {
            "inbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0725926",
                    "data": {
                        "request": {
                            "method": "GET",
                            "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "Connection",
                                    "value": "Keep-Alive"
                                },
                                {
                                    "name": "Host",
                                    "value": "contoso5.azure-api.net"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "mapper",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0726213",
                    "data": {
                        "configuration": {
                            "api": {
                                "from": "/calc",
                                "to": {
                                    "scheme": "http",
                                    "host": "calcapi.cloudapp.net",
                                    "port": 80,
                                    "path": "/api",
                                    "queryString": "",
                                    "query": {},
                                    "isDefaultPort": true
                                }
                            },
                            "operation": {
                                "method": "GET",
                                "uriTemplate": "/add?a={a}&b={b}"
                            },
                            "user": {
                                "id": 1,
                                "groups": [
                                    "Administrators",
                                    "Developers"
                                ]
                            },
                            "product": {
                                "id": 1
                            }
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0727522",
                    "data": {
                        "message": "Request is being forwarded to the backend service.",
                        "request": {
                            "method": "GET",
                            "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "X-Forwarded-For",
                                    "value": "33.52.215.35"
                                }
                            ]
                        }
                    }
                }
            ],
            "outbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1960601",
                    "data": {
                        "response": {
                            "status": {
                                "code": 200,
                                "reason": "OK"
                            },
                            "headers": [
                                {
                                    "name": "Pragma",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Length",
                                    "value": "124"
                                },
                                {
                                    "name": "Cache-Control",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Type",
                                    "value": "application/xml; charset=utf-8"
                                },
                                {
                                    "name": "Date",
                                    "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                                },
                                {
                                    "name": "Expires",
                                    "value": "-1"
                                },
                                {
                                    "name": "Server",
                                    "value": "Microsoft-IIS/8.5"
                                },
                                {
                                    "name": "X-AspNet-Version",
                                    "value": "4.0.30319"
                                },
                                {
                                    "name": "X-Powered-By",
                                    "value": "ASP.NET"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1961112",
                    "data": {
                        "message": "Response headers have been sent to the caller. Starting to stream the response body."
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1963155",
                    "data": {
                        "message": "Response body streaming to the caller is complete."
                    }
                }
            ]
        }
    }

## <a name="next-steps"> </a>Passos seguintes

-   Ver uma demonstração da rastreio expressões política [nuvem de folha de rosto episódio 177: mais funcionalidades de gestão de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Avançar para 21:00 para ver a demonstração.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Introdução ao Azure API gestão]: api-management-get-started.md
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 