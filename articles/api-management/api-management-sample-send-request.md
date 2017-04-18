<properties
    pageTitle="Utilizar o serviço de gestão de API para gerar pedidos de HTTP"
    description="Saiba como utilizar as políticas de pedidos e respostas na gestão de API para telefonar serviços externos a partir do seu API"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="using-external-services-from-the-azure-api-management-service"></a>Utilizar serviços externos a partir do serviço de gestão de API do Azure

As políticas que foram disponíveis no serviço de gestão de API do Azure podem fazer uma vasta gama de trabalho útil baseada puramente o pedido de entrada, a resposta de saída e informações de configuração básica. No entanto, ser capaz de interagir com os serviços externos da gestão de API políticas abre-se para cima inúmeras oportunidades mais.

Anteriormente já viu a como podemos pode interagir com o [serviço de Azure concentrador de evento para o registo, monitorização e análise](api-management-log-to-eventhub-sample.md). Neste artigo podemos vai demonstrar políticas que permitem-lhe interagir com qualquer HTTP externo com base no serviço. Estas políticas podem ser utilizadas para acionar eventos remotos ou para obter informações que serão utilizadas para manipular o pedido e a resposta a algumas forma original.

## <a name="send-one-way-request"></a>Forma de pedido de envio-um
Possivelmente a interação externa mais simples é o estilo fire e esquecer do pedido que permite a um serviço externo ser notificado de qualquer tipo de eventos importantes. Vamos pode utilizar a política de fluxo de controlo `choose` para detetar qualquer tipo de condição que recomendamos estiver interessados em e, em seguida, se a condição é cumprida, vamos fazer um pedido HTTP externo utilizando a política de [forma-pedido de envio de um](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Isto pode ser um pedido para um sistema de mensagens como Hipchat ou margem ou uma mensagem de correio API como SendGrid ou MailChimp ou para incidentes de suporte crítico algo com PagerDuty. Todos estes sistemas de mensagens tem APIs HTTP simples que recomendamos pode facilmente invocar.

### <a name="alerting-with-slack"></a>Alertar com margem
O exemplo seguinte demonstra como enviar uma mensagem para uma sala de chat folga se o código de estado de resposta HTTP for maior ou igual a 500. Um erro de 500 intervalo indica um problema com os nossos back-end API que o cliente da nossa API não é possível resolver próprios. Requer normalmente qualquer tipo de intervenção da nossa parte.  

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Folga tem o conceito de entrada web anzóis. Quando configurar uma rotina web entrada, a folga gera um URL especial que permite-lhe para fazer um pedido de mensagem simple e para passar de uma mensagem para o canal folga. O corpo JSON criamos é baseado num formato definido pela margem.

![Folga Web rotina](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>É fire e esquecer suficientemente bom?
Existem certos concessões ao utilizar um estilo fire e esquecer do pedido. Se, por alguma razão, o pedido irá falhar, em seguida, não vai ser comunicada a falha. Esta situação específica, a complexidade de ter uma falha de secundária elaboração de relatórios de sistema e o custo de desempenho adicionais de aguardar a resposta não se justifica. Para obter cenários onde é essencial para verificar a resposta, em seguida, a política de [Enviar pedido](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) é a melhor opção.

## <a name="send-request"></a>Pedido de envio
O `send-request` política permite utilizando um serviço externo para executar funções de processamento complexa e devolver dados para a gestão de API do serviço que pode ser utilizado para subsequente transformação de política.

### <a name="authorizing-reference-tokens"></a>Autorizar tokens de referência
Uma função principal da gestão de API está a proteger os recursos de back-end. Se o servidor de autorização utilizado pelo seu API cria [JWT tokens](http://jwt.io/) como parte do seu fluxo de oauth2 ainda, tal como é que o [Azure Active Directory](../active-directory/active-directory-aadconnect.md) , em seguida, pode utilizar o `validate-jwt` política para verificar a validade do token de. No entanto, alguns servidores de autorização criam o que é chamado [tokens de referência](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) que não podem ser verificadas sem fazer uma chamada de volta para o servidor de autorização.

### <a name="standardized-introspection"></a>Introspection normalizado
No passado não tiver havido nenhuma forma normalizada de um token de referência com um servidor de autorização a verificar. No entanto, um padrão recentemente proposta [RFC 7662](https://tools.ietf.org/html/rfc7662) foi publicado pela IETF que define como um servidor de recursos pode verificar a validade de um token.

### <a name="extracting-the-token"></a>Extrair do token
O primeiro passo é extrair o token de cabeçalho de autorização. O valor de cabeçalho que deve ser formatado com o `Bearer` esquema de autorização, um único espaço e, em seguida, o token de autorização de acordo com o [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Infelizmente, existem casos onde o esquema de autorização é omitido. Para ter em conta isto ao analisar, dividir o valor do cabeçalho num espaço e selecione a última cadeia à matriz devolvida de cadeias. Isto fornece uma solução para os cabeçalhos de autorização mal formatada.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### <a name="making-the-validation-request"></a>Tornar o pedido de validação
Assim que temos o token de autorização, iremos pode fazer o pedido para validar o token. RFC 7662 liga introspection este processo e requer que lhe `POST` um formulário HTML para o recurso introspection. O formulário HTML tem de conter pelo menos um par de valor/chave com a tecla `token`. Também deve estar autenticado este pedido para o servidor de autorização para se certificar de que não é possível aceda arrasto maliciosos clientes para tokens válidas.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### <a name="checking-the-response"></a>Verificar a resposta
O `response-variable-name` atributo é utilizado para conceder acesso a resposta devolvida. O nome definido nesta propriedade pode ser utilizado como uma chave para o `context.Variables` dicionário para aceder a `IResponse` objeto.

A partir do objeto de resposta podemos pode obter o corpo da e RFC 7622 indica-nos em que a resposta tem de ser um objeto JSON e tem de conter, pelo menos, uma propriedade denominada `active` isto é um valor booleano. Quando `active` é verdadeira, em seguida, o token é considerado válido.

### <a name="reporting-failure"></a>Falha de elaboração de relatórios
Utilizamos uma `<choose>` política para detetar se o token é inválido e em caso afirmativo, devolver uma resposta 401.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

Por [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) descreve como `bearer` tokens deverão ser utilizados, recomendamos também devolver uma `WWW-Authenticate` cabeçalho com a resposta 401. O autenticar WWW destina-se para indicar um cliente sobre como construir um pedido de corretamente autorizado. Devido ao grande variedade de abordagens possíveis com o quadro oauth2 ainda, é difícil comunicar todas as informações necessárias. Felizmente, existem esforços decorrer para ajudar [os clientes de detetar como corretamente autorizar pedidos para um servidor de recursos](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solução final
Colocar em conjunto em todas as peças, podemos obter a política seguinte:

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
            <!-- Check active property in response -->
            <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                <!-- Return 401 Unauthorized with http-problem payload -->
                <return-response response-variable-name="existing response variable">
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="override">
                        <value>Bearer error="invalid_token"</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
      <base />
    </inbound>

Este é apenas um dos muitos exemplos de como o `send-request` política pode ser utilizada para integrar serviços externos úteis para o processo de pedidos e respostas a fluir através do serviço de gestão de API.

## <a name="response-composition"></a>Composição de resposta
O `send-request` política pode ser utilizada para melhorar um pedido de principal para um sistema de back-end, tal como é mostrado no exemplo anterior, ou pode ser utilizado como uma substituir completa da chamada back-end. Utilizando esta técnica podemos facilmente criar compostos recursos que são agregados de vários sistemas diferentes.

### <a name="building-a-dashboard"></a>Criar um dashboard   
Por vezes, pretende conseguir para expor informações existentes nos vários sistemas de back-end, por exemplo, a unidade de um dashboard. Os KPIs provêm de todos os diferentes back-end, mas que prefere não fornecer acesso direto aos mesmos e seria conveniente se foi possível obter todas as informações num único pedido. Talvez algumas das informações de back-end precisa de algumas cortar e interfolhados e um pouco sanitizing pela primeira vez! Ser capaz de cache esse recurso composto seria útil reduzir a carga de back-end como sabe que os utilizadores têm um hábito de martelar a tecla F5 para poder ver se os respetivos métricas underperforming podem ser alterados.    

### <a name="faking-the-resource"></a>Faking o recurso
É o primeiro passo para construir o nosso recurso de dashboard configurar uma nova operação no portal do publisher de gestão de API. Esta será uma operação de marcador de posição utilizada para configurar a nossa política de composição para construir o nosso recurso dinâmico.

![Operação do dashboard](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Tornar os pedidos de
Uma vez a `dashboard` operação foi criada podemos podem configurar uma política especificamente para essa operação. 

![Operação do dashboard](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

O primeiro passo consiste em extrair quaisquer parâmetros de consulta a partir do pedido de entrada, para que pode reencaminhar a nossa back-end. Neste exemplo nosso dashboard apresenta as informações com base num período de tempo uma, por isso, tem uma `fromDate` e `toDate` parâmetro. Pode utilizamos a `set-variable` política extrair as informações de URL pedido.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Assim que temos estas informações iremos pode fazer pedidos a todos os sistemas de back-end. Cada pedido constrói um novo URL com as informações do parâmetro e chamadas do seu servidor respetivos e armazena a resposta numa variável de contexto.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Estes pedidos serão executadas em sequência, que não seja ideal. Uma versão futuras podemos vai ser introdução a uma nova política denominada `wait` que irá permitir que todos estes pedidos para executar em paralelo.

### <a name="responding"></a>Responder

Construir a resposta composta utilizamos a política de [resposta de retorno](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . O `set-body` elemento pode utilizar uma expressão para construir um novo `JObject` com todas as representações de componente incorporadas como propriedades.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

A política completa será apresentada da seguinte forma:

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

A configuração do marcador de posição operação que podemos pode configurar o recurso de dashboard para ser colocadas em cache para pelo menos uma hora porque compreendemos natureza dos dados significa que o mesmo se for uma hora desatualizada, ainda estará suficientemente eficaz transmitir informações útil para os utilizadores.

## <a name="summary"></a>Resumo
Serviço de gestão de API Azure disponibiliza políticas flexíveis que podem ser aplicadas seletivamente para o tráfego de HTTP e permite-composição dos serviços de back-end. Se pretende melhorar o gateway API com funções, verificação, as capacidades de validação de alerta ou criar novos recursos compostos com base em vários serviços de back-end, o `send-request` e políticas relacionadas abra um mundo de possibilidades.

## <a name="watch-a-video-overview-of-these-policies"></a>Ver uma descrição geral em vídeo estas políticas
Para mais informações sobre a [forma de pedido de envio de um](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [Enviar pedido](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)e políticas de [resposta de retorno](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) abordadas neste artigo, contacte o veja o vídeo seguinte.

> [AZURE.VIDEO send-request-and-return-response-policies]
