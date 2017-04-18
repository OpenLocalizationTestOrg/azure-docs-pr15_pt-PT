<properties
    pageTitle="A colocação em cache personalizado na gestão de API do Azure"
    description="Saiba como itens em cache pela chave na gestão de API do Azure"
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

# <a name="custom-caching-in-azure-api-management"></a>A colocação em cache personalizado na gestão de API do Azure
Serviço de gestão de API Azure tem suporte incorporado para de [Colocação em cache de resposta HTTP](api-management-howto-cache.md) utilizando o URL do recurso como chave. A tecla pode ser modificada por cabeçalhos pedido ao utilizar o `vary-by` propriedades. Isto é útil para colocação em cache inteiras respostas HTTP (também conhecidos por representações), mas, por vezes, é útil para cache apenas uma parte de uma representação. As novas políticas de [valor de procura de cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) e [o valor de arquivo de cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) fornecem a capacidade para armazenar e obter arbitrários blocos de dados a partir do definições de política. Esta capacidade também soma o valor para a política de anteriormente introduzidos [pedido de envio](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) porque agora na cache respostas a partir de serviços externos.

## <a name="architecture"></a>Arquitetura  
Serviço de gestão de API utiliza uma cache de dados partilhada por inquilino de modo a que, à medida que dimensionar até múltiplas unidades ainda irá obter acesso ao mesmo dados em cache. No entanto, quando trabalhar com uma implementação da região com várias existem caches independentes dentro de cada uma das regiões. Devido ao seguinte, é importante não tratar a cache como um arquivo de dados, onde está a única origem de informação. Se fez e, mais tarde, decidir tirar partido da implementação com várias região, em seguida, clientes com utilizadores que viajam poderão perder o acesso com os dados em cache.

## <a name="fragment-caching"></a>Fragmento colocação em cache
Existem certos casos onde as respostas a ser devolvidas contêm parte dos dados que são dispendioso determinar e ainda permanecem fresca durante um período de tempo razoável. Por exemplo, considere um serviço criado por uma companhia aérea que fornece informações relativas reservas de voos, estado de voo, etc. Se o utilizador é um membro do programa de pontos de companhias aéreas, estes também teria informações relativas ao seu estado atual e quilometragem acumulado ao. Estas informações relacionadas com o utilizador podem estar armazenadas num sistema de diferentes, mas pode ser conveniente para os incluir nas respostas devolvidas sobre o estado de voo e reservas. Isto pode ser feito utilizando um processo chamado fragmento colocação em cache. Pode ser devolvida a representação principal a partir do servidor de origem utilizando qualquer tipo de token para indicar onde as informações relacionadas com o utilizador estão a ser inserida. 

Considere a seguinte resposta JSON a partir de uma API de back-end.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

E de recursos secundário na `/userprofile/{userid}` que é semelhante,

     { "username" : "Bob Smith", "Status" : "Gold" }

Para determinar as informações de utilizador apropriadas para incluir, é necessário identificar quem é o utilizador final. Este mecanismo é dependente de implementação. Por exemplo, estou a utilizar o `Subject` reclamar de um `JWT` token. 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Vamos armazenar este `enduserid` valor numa variável de contexto para utilizar posteriormente. O passo seguinte é determinar se um pedido de anterior já obtidos as informações de utilizador e armazenou-o na cache. Para este utilizamos a `cache-lookup-value` política.

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Se não existe nenhuma entrada na cache que corresponde ao valor de chave, em seguida, n `userprofile` variável de contexto será criado. Vamos dar o sucesso da pesquisa utilizando o `choose` política de fluxo de controlo.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Se o `userprofile` não existe a variável de contexto, em seguida, vamos tem de fazer um pedido de HTTP para obtê-la.

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

Utilizamos os `enduserid` para construir o URL para o recurso de perfil de utilizador. Assim que tiver a resposta, que possamos separar o corpo de texto terminar a resposta e armazená-lo novamente numa variável de contexto.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Para evitar-na ter de fazer este pedido HTTP novamente, quando o mesmo utilizador faz com que o outro pedido, podemos armazenar o perfil de utilizador na cache.

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Vamos armazenar o valor na cache utilizando a mesma chave exata que recomendamos originalmente tentou recuperá-la com. A duração que optamos para armazenar o valor deve ser baseada em como muitas vezes as alterações de informações e os utilizadores como tolerância a falhas são informações para desatualizada. 

É importante para aperceba que retrieving da cache de ainda é uma fora do processo, o pedido de rede e potencialmente pode ainda adicionar dezenas de milissegundos para o pedido. Os benefícios vir quando para determinar que as informações de perfil de utilizador demoram significativamente mais tempo do que a devido necessário especificá-las de base de dados consultas ou agregadas informações a partir de várias back-end.

O passo final do processo é atualizar a resposta devolvida com os nossos informações de perfil de utilizador.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

Posso optado por incluir as aspas como parte do token de, para que o mesmo quando não ocorre substituir, a resposta foi JSON ainda válida. Para tornar mais fácil de depuração era este principalmente.

Depois de combinar todos estes passos em conjunto, o resultado final é uma política com um aspeto semelhante a que segue.

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

Esta abordagem de colocação em cache é principalmente utilizada em web sites onde HTML é composta por no lado do servidor para que pode ser composto como uma única página. No entanto, também pode ser útil em APIs onde os clientes não é possível fazer cliente laterais HTTP colocação em cache ou é conveniente não para colocar essa responsabilidade no cliente.

Este mesmo tipo de colocação em cache de fragmento também pode ser efetuado nos servidores de web back-end utilizando um Redis colocação em cache server, no entanto, utilizando o serviço de gestão de API para efetuar este trabalho útil quando os fragmentos em cache sejam provenientes de extremidades de back diferentes que as respostas principais.

## <a name="transparent-versioning"></a>Controlo de versões transparente
É prática comum para várias versões de implementação diferente de uma API serão suportadas em qualquer momento. Isto é talvez para suportar ambientes diferentes, como Dev Center, teste, produção, etc., ou pode ser suportar versões mais antigas, de API para dar tempo para os consumidores de API migrar para as versões mais recentes. 

Uma abordagem para processamento isto em vez de exigir os programadores de cliente alterar os URLs de `/v1/customers` para `/v2/customers` é armazenar dados de perfil do consumidor qual a versão da API do pretendem atualmente utilizar e chamar o URL de back-end adequado. Para determinar o URL correto back-end para ligar para um cliente em particular, é necessário alguns dados de configuração da consulta. Por colocação em cache estes dados de configuração, podemos pode minimizar a sanções de desempenho de efetuar esta pesquisa.

O primeiro passo é determinar o identificador utilizado para configurar a versão pretendida. Neste exemplo, que escolhi associar a versão para a chave de produto de subscrição. 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

Vamos, em seguida, faça uma pesquisa de cache para ver se podemos já ter obtidos a versão do cliente pretendido.

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Em seguida, podemos Verifique se não encontrámos-lo na cache.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

Se o podemos não, em seguida, podemos aceda e recuperá-la.

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

Extrai texto do corpo de resposta da resposta.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Guardá-lo novamente na cache para utilização futura.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

E por fim, atualize o URL de back-end para selecionar a versão do serviço pretendido pelo cliente.

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

A política completamente é da seguinte forma.

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


Ativar os consumidores de API para transparente controlar qual a versão back-end está a ser acedida por clientes sem ter de atualizar e implementar clientes é uma solução elegante que corrige muitos preocupações de controlo de versões de API.

## <a name="tenant-isolation"></a>Isolamento de inquilino

Na maiores e inquilinos com várias implementações algumas empresas criar grupos de inquilinos separados no distintas implementações do hardware de back-end. Isto minimiza o número de clientes que são afetada por um problema de hardware no back-end. Também lhe permite novas versões de software ser lançadas fases. Idealmente esta arquitetura de back-end deve ser transparente para consumidores API. Isto pode ser excedido em forma semelhante para controlo de versões transparente porque baseia-se a mesma técnica de manipulação o URL de back-end utilizando o estado da configuração por chave API.  

Em vez de voltar uma versão preferida da API para cada chave da subscrição, deve devolver um identificador de que está relacionada com um inquilino para o grupo de hardware atribuídas. Esse identificador pode ser utilizado para construir o URL de back-end adequado.

## <a name="summary"></a>Resumo
Liberdade para utilizar a cache de gestão do Azure API para armazenar qualquer tipo de dados permite eficiente acesso aos dados de configuração que podem afetar a forma como um pedido de entrada é processado. Podem também ser utilizado para armazenar segmentos de dados que podem aumentar as respostas, devolvidas por um API de back-end.

## <a name="next-steps"></a>Próximos passos
Fórum nos dar feedback no tópico Disqus para este tópico se existem outras situações que estas políticas tem permissão para utilizar ou, se existirem cenários gostaria de atingir mas não sentir não são atualmente possíveis.
