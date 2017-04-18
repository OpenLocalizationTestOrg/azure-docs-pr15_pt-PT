<properties
  pageTitle="Cliente e servidor SDK controlo de versões em Mobile aplicações e serviços de Mobile | Azure de aplicação de serviço"
  description="Lista de cliente SDK e compatibilidade com versões SDK do servidor para serviços Mobile e as aplicações móveis do Azure"
  services="app-service\mobile"
  documentationCenter=""
  authors="adrianhall"
  manager="erikre"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="10/01/2016"
  ms.author="adrianha"/>

# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controlo de versões de cliente e servidor em Mobile aplicações e serviços de Mobile

A versão mais recente do Azure Mobile serviços é a funcionalidade de **Aplicações móveis** do serviço de aplicação do Azure.

Os SDK Mobile aplicações de cliente e servidor originalmente são baseados nos serviços do telemóvel, mas são *não* é compatível com os outros.
Isto é, tem de utilizar um cliente de *Aplicações Mobile* SDK com um servidor de *Aplicações Mobile* SDK e da mesma forma para *Serviços Mobile*. Este contrato é aplicado através de um valor de cabeçalho especial utilizado pelo cliente e servidor SDK, `ZUMO-API-VERSION`.

Nota: sempre que este documento refere-se a *Serviços Mobile* back-end,-não é necessariamente necessário ser alojados em serviços Mobile. É agora possível migrar um serviço móvel para executar o serviço de aplicação sem alterações código, mas o serviço seria ainda estar a utilizar versões SDK *Serviços Mobile* .

Para saber mais sobre como migrar a aplicação de serviço sem alterações código, consulte o artigo [migrar um serviço móvel do serviço de aplicação do Azure].

## <a name="header-specification"></a>Especificação de cabeçalho

A tecla `ZUMO-API-VERSION` pode ser especificado no cabeçalho de HTTP ou a cadeia de consulta. O valor for uma cadeia de versão o formulário **x.y.z**.

Por exemplo:

OBTER https://service.azurewebsites.net/tables/TodoItem

CABEÇALHOS: ZUMO-API-VERSÃO: 2.0.0

PUBLICAR https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Ativamente por não verifica a versão

Pode optar por permitir Terminar verificação através da definição de um valor **Verdadeiro** para a aplicação **MS_SkipVersionCheck**a definição de versão. Especifique-o no seu Web. config ou na secção definições da aplicação do portal do Azure.

> [AZURE.NOTE] Existem várias das alterações de comportamento entre os serviços de Mobile e Apps Mobile, particularmente nas áreas de sincronização offline, autenticação e as notificações push. Só deverá optar por terminar versão dar depois de concluída testado para se certificar de que estas alterações de comportamentais não interromper funcionalidade da sua aplicação.

## <a name="summary-of-compatibility-for-all-versions"></a>Resumo de compatibilidade para todas as versões

O gráfico abaixo mostra a compatibilidade entre todos os tipos de cliente e servidor. Back-end é classificada como móveis **Serviços** ou móveis **aplicações** com base no servidor SDK, que utiliza.

|                           | **Serviços móveis** NODE.js ou .NET | **Aplicações móveis** NODE.js ou .NET |
| ----------                | -----------------------             |   ----------------              |
| [Clientes de serviços móveis] | OK                                  | Erro\*                         |
| [Clientes de aplicações Mobile]     | Erro\*                             | OK                              |

\*Isto pode ser controlado ao especificar **MS_SkipVersionCheck**.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Serviços Mobile cliente e servidor

O cliente SDK na tabela abaixo são compatíveis com **Serviços Mobile**.

Nota: o cliente de serviços móveis SDK *não* enviar um valor de cabeçalho `ZUMO-API-VERSION`. Se o serviço recebe este cabeçalho ou o valor de cadeia de consulta, será devolvido um erro, a menos que explicitamente optado reduzir, tal como descrito acima.

### <a name="MobileServicesClients"></a>Cliente de *Serviços* móvel SDK

| Plataforma do cliente                   | Versão                                                                   | Valor do cabeçalho da versão |
| -------------------               | ------------------------                                                  | -------------------  |
| Cliente gerido (Windows, Xamarin) | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | n/d                  |
| iOS                               | [2.2.2](http://aka.ms/gc6fex)                                             | n/d                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | n/d                  |
| HTML                              | [1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | n/d     |

### <a name="mobile-services-server-sdks"></a>Servidor de *Serviços* móvel SDK

| Plataforma do servidor  | Versão                                                                                                        | Cabeçalho de versão aceites |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Versão WindowsAzure.MobileServices.Backend.* 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Sem cabeçalho de versão** |
| NODE.js          | (brevemente)                        | **Sem cabeçalho de versão** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportamento da back-ends de serviços móveis

| VERSÃO DA API ZUMO | Valor de MS_SkipVersionCheck | Resposta |
| ---------------- | ---------------------------- | -------- |
| Não especificado    | Qualquer                          | 200 - OK |
| Qualquer valor        | VERDADEIRO                         | 200 - OK |
| Qualquer valor        | FALSO/não especificado          | 400 - pedido inválido |

## <a name="2.0.0"></a>Azure Mobile aplicações cliente e servidor

### <a name="MobileAppsClients"></a>Cliente móvel do *aplicações* SDK

Verificação da versão foi introduzida começando com as seguintes versões do cliente SDK para **Aplicações do Azure Mobile**:

| Plataforma do cliente                   | Versão                   | Valor do cabeçalho da versão |
| -------------------               | ------------------------  | -----------------    |
| Cliente gerido (Windows, Xamarin) | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| iOS                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Servidor de *aplicações* móvel SDK

Verificação da versão está incluído nos seguintes versões do SDK server:

| Plataforma do servidor  | SDK                                                                                                        | Cabeçalho de versão aceites |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| NODE.js          | [Azure--aplicações mobile)](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento da back-ends de aplicações Mobile

| VERSÃO DA API ZUMO | Valor de MS_SkipVersionCheck | Resposta |
| ---------------- | ---------------------------- | -------- |
| x.y.z ou de valores nulos    | VERDADEIRO                         | 200 - OK |
| Nulo             | FALSO/não especificado          | 400 - pedido inválido |
| 1.x.y            | FALSO/não especificado          | 400 - pedido inválido |
| 2.0.0-2.x.y      | FALSO/não especificado          | 200 - OK |
| 3.0.0-3.x.y      | FALSO/não especificado          | 400 - pedido inválido |


## <a name="next-steps"></a>Próximos passos

- [Migrar um serviço móvel do Azure de aplicação de serviço]


[Clientes de serviços móveis]: #MobileServicesClients
[Clientes de aplicações Mobile]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrar um serviço móvel do Azure de aplicação de serviço]: app-service-mobile-migrating-from-mobile-services.md

