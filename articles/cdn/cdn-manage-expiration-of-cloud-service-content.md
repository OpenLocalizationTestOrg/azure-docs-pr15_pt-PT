<properties
 pageTitle="Como gerir expiração de conteúdo Azure Web Apps/Cloud Services, ASP.NET e IIS no Azure CDN | Microsoft Azure"
 description="Descreve como gerir a expiração de conteúdo de serviço de nuvem no Azure CDN"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Como gerir expiração de conteúdo Azure Web Apps/Cloud Services, ASP.NET ou IIS no Azure CDN

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Serviço de armazenamento de BLOBs Azure](cdn-manage-expiration-of-blob-content.md)

Ficheiros a partir de qualquer servidor web, origem acessível publicamente podem ser colocadas em cache no Azure CDN até decorrida respetivo time to live (TTL).  O valor TTL é determinado pela [cabeçalho *Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP a partir do servidor de origem.  Este artigo descreve como configurar `Cache-Control` cabeçalhos para Azure Web Apps, serviços em nuvem Azure, aplicações do ASP.NET e sites de serviços de informação Internet, as quais estão configuradas de forma semelhante.

>[AZURE.TIP] Pode optar por definir sem TTL num ficheiro.  Neste caso, Azure CDN aplica automaticamente um TTL predefinido de sete dias.
>
>Para mais informações sobre como funciona o Azure CDN para acelerar o acesso aos ficheiros e outros recursos, consulte o artigo [Descrição geral de CDN Azure](./cdn-overview.md).

## <a name="setting-cache-control-headers-in-configuration"></a>Cabeçalhos de Cache-Control definição configuração

Para em conteúdo estático, tais como imagens e folhas de estilo, pode controlar a frequência de actualização modificando os ficheiros **applicationHost** . **config** ou para a aplicação web.  O elemento **system.webServer\staticContent\clientCache** no ficheiro de configuração irá definir o `Cache-Control` cabeçalho para o seu conteúdo. Para a **Web. config**, as definições de configuração irão afetar tudo na pasta e todas as subpastas, a menos que sobreposto ao nível do subpasta.  Por exemplo, pode predefinir uma time to live na raiz de ter todo o conteúdo estático colocadas em cache para 3 dias, mas tem uma subpasta que tenha mais variável conteúdo com uma definição de cache de 6 horas.  Para **config**, todas as aplicações no site, serão afectadas, mas podem ser substituídas na **Web. config** ficheiros as aplicações.

As seguintes mostra XML e exemplo de definição **clientCache** para especificar uma idade máxima de 3 dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Especificar **UseMaxAge** adiciona uma `Cache-Control: max-age=<nnn>` cabeçalho para a resposta com base no valor especificado no atributo **CacheControlMaxAge** . É o formato de timespan para o atributo **cacheControlMaxAge** <days>. <hours>:<min>:<sec>. Para mais informações sobre o nó **clientCache** , consulte o artigo [Cache de cliente <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Cabeçalhos de Cache-Control definição no código

Para aplicações do ASP.NET, pode definir a CDN colocação em cache comportamento através de programação, definindo a propriedade **HttpResponse.Cache** . Para mais informações sobre a propriedade **HttpResponse.Cache** , consulte o artigo [HttpResponse.Cache propriedade](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [HttpCachePolicy escolares](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Se pretender através de programação de conteúdo de aplicação no ASP.NET em cache, certifique-se de que o conteúdo está marcado como para armazenamento em cache, definindo HttpCacheability para o *público*. Além disso, certifique-se de que a validação de cache está definida. A validação de cache pode ser uma última modificação carimbo de data/hora definido pelo chamar SetLastModified ou um valor de etag definir ao contactar o suporte SetETag. Opcionalmente, também pode especificar um período de expiração de cache ao contactar o suporte SetExpires ou pode depender a heurística de cache predefinido já descrita neste documento.  

Por exemplo, a cache de conteúdo de uma hora, adicione o seguinte:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Próximos passos

- [Leia detalhes sobre o elemento **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [Leia a documentação para a propriedade **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
- [Ler a documentação para a **Classe de HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  
