<properties
    pageTitle="Partilhado descrição geral do Access assinaturas | Microsoft Azure"
    description="O que são partilhados assinaturas do Access, como é que funcionam e como utilizá-las do nó, PHP e c#."
    services="service-bus"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/02/2016"
    ms.author="darosa;sethm"/>

# <a name="shared-access-signatures"></a>Acesso partilhado assinaturas

*Partilhado assinaturas de acesso* (SA) estão dispositivo principal de segurança de serviço Bus, incluindo o evento concentradores, controlada mensagens (filas e tópicos) e reencaminhado mensagens. Este artigo aborda partilhado assinaturas do Access, como funcionam e como utilizá-las de forma a plataforma desconhecido.

## <a name="overview-of-sas"></a>Descrição geral de SA

Partilhada assinaturas de acesso estão um dispositivo de autenticação com base no hashes seguros SHA 256 ou URIs. SA são um dispositivo muito poderoso que é utilizado por todos os serviços de Bus de serviço. Em utilizar real, SA têm dois componentes: uma *Política de acesso partilhado* e uma *Assinatura de acesso partilhados* (denominadas frequentemente um *token*).

Pode encontrar informações mais detalhadas sobre assinaturas de acesso partilhado com o serviço Bus em [autenticação de assinatura de acesso partilhado com Bus de serviço](service-bus-shared-access-signature-authentication.md).

## <a name="shared-access-policy"></a>Política de acesso partilhado

Um dado importante para compreender sobre SA é que todos os desde o início com uma política. Para cada política, optar por três partes de informações: **nome**, **âmbito**e **permissões**. O **nome** é apenas esse; um nome exclusivo dentro desse âmbito. O âmbito for suficientemente fácil: é o URI do recurso em questão. Um espaço de nomes do serviço Bus o âmbito for o nome de domínio completamente qualificado (FQDN), tal como `https://<yournamespace>.servicebus.windows.net/`.

As permissões disponíveis para uma política de são em grande medida facilmente compreensíveis:

  + Enviar
  + Ouvir
  + Gerir

Depois de criar a política, é atribuída uma *Chave primária* e uma *Tecla secundário*. Estes são teclas com encriptação fortes. Não perca-los ou fugas-los - sempre estará disponível no [portal do Azure][]. Pode utilizar qualquer uma das chaves geradas e pode voltar a gerá-los em qualquer altura. No entanto, se gerar ou alterar a chave primária na política, irá invalidadas quaisquer assinaturas de acesso partilhado criado a partir dos mesmos.

Quando cria um espaço de nomes de serviço Bus, uma política é automaticamente criada para o espaço de nomes inteiro denominado **RootManageSharedAccessKey**e esta política tem todas as permissões. Não inicie sessão como **raiz**, para que não utilize esta política a não ser que exista uma muito bom a razão. Pode criar políticas adicionais na caixa de diálogo **Configurar** para o espaço de nomes no portal. É importante nota que um único nível no serviço Bus em árvore (espaço de nomes, fila de espera, Hub do evento, etc.) só pode ter até 12 políticas anexadas.

## <a name="shared-access-signature-token"></a>Assinatura de acesso partilhado (token)

A política propriamente dito não é o token de acesso para Bus de serviço. É o objeto a partir do qual o token de acesso é gerado - utilizando uma a chave primária ou secundário. O token é gerado pelo cuidadosamente intencionada uma cadeia no seguinte formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Onde `signature-string` é o hash SHA-256 do âmbito do token (**âmbito** , tal como descrito na secção anterior) com um CRLF acrescentado e uma hora de termo (em segundos, desde a época: `00:00:00 UTC` no 1 de Janeiro de 1970).

O hash ter um aspeto semelhante ao seguinte código de pseudo e devolve 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Os valores que não sejam atribuídos são na cadeia de **SharedAccessSignature** para que o destinatário pode calcular o hash com os mesmos parâmetros, para se certificar de que devolve o mesmo resultado. O URI Especifica o âmbito e o nome da chave identifica a política para ser utilizado para calcular o hash. Isto é importante a partir de um ponto de vista de segurança. Se a assinatura não corresponderem ao que calcula o destinatário (serviço Bus), em seguida, acesso negado. Neste momento pode não se esqueça de que o remetente tinha acesso à chave e deve ser concedido os direitos especificados na política.

## <a name="generating-a-signature-from-a-policy"></a>Gerar uma assinatura de uma política de

Como que realmente fazer isto no código? Vamos dar uma olhadela a alguns dos seguintes procedimentos.

### <a name="nodejs"></a>NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### <a name="c35"></a>C & #35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-at-http-level"></a>Utilizar a assinatura de acesso partilhados (ao nível HTTP)
 
Agora que sabe como criar assinaturas de acesso partilhado para quaisquer entidades no serviço Bus, está pronto para efetuar uma mensagem de HTTP:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
Lembre-se de que isto funciona para tudo. Pode criar SA para uma fila de espera, tópico, subscrição, concentrador de evento ou reencaminhamento. Se utilizar identidade por publisher para evento concentradores, simplesmente acrescentar `/publishers/< publisherid>`.

Se der um remetente ou um cliente um token de SA, não tem a chave de diretamente e não podem reverter o hash obtê-lo. Como tal, tem controlo sobre o que pode aceder e durante quanto tempo. Um dado importante que se deve lembrar é que se alterar a chave primária na política, irá invalidadas quaisquer assinaturas de acesso partilhado criado a partir dos mesmos.

## <a name="using-the-shared-access-signature-at-amqp-level"></a>Utilizar a assinatura de acesso partilhados (ao nível AMQP)

Na secção anterior, viu como utilizar o token de SA com um pedido de HTTP POST para enviar dados para o Bus de serviço. Como sabe, pode aceder a Bus serviço utilizando o avançadas mensagem colocação Protocol (AMQP) que é o protocolo preferido para utilizar por motivos de desempenho em vários cenários. Utilização de token de SA com AMQP é descrita no documento [AMQP Claim-Based segurança versão 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está no trabalho rascunho desde o 2013 mas bem suportado pelo Azure hoje.

Antes de começar a enviar dados para Bus de serviço, o publisher tem de enviar o token de SA dentro de uma mensagem AMQP para um nó AMQP bem definido com o nome **$cbs** (-pode vê-los como uma fila "especial" utilizada pelo serviço para adquirir e validar todos os tokens SA). O publisher tem de especificar o campo **ReplyTo** dentro da mensagem AMQP; Este é o nó na qual o serviço de respostas para o publisher com o resultado da validação token (um padrão de pedido/resposta simples entre o publisher e serviço). É criado neste nó de resposta "no momento," falar sobre "dinâmica criação de nó remoto", tal como descrito a especificação AMQP 1.0. Depois de verificar que o token de SA é válido, o publisher pode avançar e começar a enviar dados para o serviço.

Os seguintes passos mostram como enviar o token de SA com o protocolo AMQP utilizando a biblioteca de [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . Isto é útil se não conseguir utilizar o oficial Bus SDK do serviço (por exemplo, no WinRT, .net Framework compacta, .net Micro Framework e Mono) desenvolver no C\#. Obviamente, esta biblioteca é útil para ajudar a compreender como baseada em afirmações segurança funciona ao nível AMQP, tal como viu funcionamento no nível HTTP (com um pedido de HTTP POST e o token de SA enviado dentro do cabeçalho "Autorização"). Se não precisa de tais dados de conhecimento abrangente sobre AMQP, pode utilizar o oficial Bus SDK do serviço com o .net aplicações do quadro, as quais fazê-lo por si.

### <a name="c35"></a>C & #35;

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

O `PutCbsToken()` método recebe a *ligação* (AMQP ligação classe instância como fornecido pela [biblioteca AMQP .NET Lite](https://github.com/Azure/amqpnetlite)) que representa a ligação TCP e o serviço e o parâmetro de *sasToken* que é o token de SA para enviar. 

> [AZURE.NOTE] É importante que a ligação é criada com **mecanismo de autenticação SASL definido para externo** (e não o predefinido simples com o nome de utilizador e palavra-passe utilizada quando não precisar de enviar o token de SA).

Em seguida, o publisher cria duas ligações AMQP para enviar o token de SA e receber a resposta (o resultado de validação token) de serviço.

A mensagem AMQP contém um conjunto de propriedades e mais informações do que uma mensagem simple. O token de SA é o corpo da mensagem (utilizando o respectivo construtor). A propriedade **"ReplyTo"** está definida para o nome do nó para receber o resultado de validação na ligação recetor (pode mudar o nome do mesmo se pretender que e será criado dinamicamente pelo serviço). As últimas três propriedades de aplicação/personalizadas são utilizadas pelo serviço para indicar que tipo de operação tem executar. Tal como descrito pela especificação de rascunho CBS, devem ser o **nome da operação** ("Colocar-token"), o **tipo de token de** (neste caso, "servicebus.windows.net:sastoken") e o **"nome" da audiência** para o qual o token aplica-se (entidade inteira).

Depois de enviar o token de SA na ligação remetente, o publisher tem de ler a resposta na ligação recetor. A resposta é uma mensagem AMQP simple com uma propriedade da aplicação com o nome **"código de estado"** que pode conter os mesmos valores como um código de estado HTTP. 

## <a name="next-steps"></a>Próximos passos

Consulte a [referência de serviço Bus REST API](https://msdn.microsoft.com/library/azure/hh780717.aspx) para obter mais informações sobre o que pode fazer com estes tokens SA.

Para mais informações sobre a autenticação de Bus de serviço, consulte o artigo [Bus de serviço de autenticação e autorização](service-bus-authentication-and-authorization.md). 

Obter mais exemplos de SA no c# e Java Script estão [Esta mensagem no blogue](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx).

[Portal do Azure]: https://portal.azure.com