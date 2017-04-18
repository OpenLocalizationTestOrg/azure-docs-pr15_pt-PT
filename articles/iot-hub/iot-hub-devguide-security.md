<properties
 pageTitle="Guia do programador - controlar o acesso a IoT concentrador | Microsoft Azure"
 description="Guia de programador do Azure IoT concentrador - como controlar o acesso a IoT concentrador e gerir a segurança"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="control-access-to-iot-hub"></a>Controlar o acesso a IoT concentrador

## <a name="overview"></a>Descrição geral

Este artigo descreve as opções para proteger o seu centro IoT. Concentrador IoT utiliza *permissões* para conceder acesso a cada os pontos finais do IoT concentrador. Permissões de limitam o acesso a um concentrador de IoT com base na funcionalidade.

Este artigo descreve:

- As permissões diferentes que pode conceder a uma aplicação do dispositivo ou back-end para aceder ao seu centro IoT.
- O processo de autenticação e os tokens que utiliza para verificar permissões.
- Como criar o âmbito de credenciais para limitar o acesso aos recursos específicos.
- Suporte de IoT concentrador para certificados x. 509.
- Mecanismos de autenticação de dispositivo personalizado que utilizam existente registos de identidade do dispositivo ou esquemas de autenticação.

### <a name="when-to-use"></a>Quando utilizar

Tem de ter permissões adequadas para aceder a qualquer os pontos finais IoT concentrador. Por exemplo, um dispositivo tem de incluir um token que contém as credenciais de segurança juntamente com todas as mensagens que envia a IoT concentrador.

## <a name="access-control-and-permissions"></a>Controlo de acesso e permissões

Pode conceder [permissões](#iot-hub-permissions) das seguintes formas:

* **Nível de concentrador partilhado políticas de acesso**. Políticas de acesso partilhada podem conceder qualquer combinação de [permissões](#iot-hub-permissions). Pode definir políticas no [portal do Azure][lnk-management-portal], ou através de programação, utilizando o [fornecedor de recursos IoT concentrador REST APIs][lnk-resource-provider-apis]. Um concentrador de IoT recentemente criado tem as seguintes políticas predefinidas:

    - **iothubowner**: a política com todas as permissões.
    - **serviço**: política com permissão de ServiceConnect.
    - **dispositivo**: política com permissão de DeviceConnect.
    - **registryRead**: política com permissão de RegistryRead.
    - **registryReadWrite**: política com permissões de RegistryRead e RegistryWrite.


* **As credenciais de segurança por dispositivo**. Cada concentrador IoT contém um [registo de identidade do dispositivo][lnk-identity-registry]. Para cada dispositivo neste registo, pode configurar as credenciais de segurança conceder permissões de **DeviceConnect** confinadas aos pontos finais da dispositivo correspondente.

Por exemplo, numa solução IoT normal:

- O componente de gestão de dispositivo utiliza a política de *registryReadWrite* .
- O componente de processador de eventos utiliza a política de *serviço* .
- O componente de lógica de negócio de dispositivo de runtime utiliza a política de *serviço* .
- Dispositivos individuais estabelecer ligação com as credenciais armazenadas no registo de identidade do concentrador de IoT.

## <a name="authentication"></a>Autenticação

Azure IoT concentrador concede acesso para os pontos finais, verificando um token das políticas de acesso partilhado e as credenciais de segurança do dispositivo identidade registo.

As credenciais de segurança, tal como simétricas chaves, nunca são enviadas através de fios.

> [AZURE.NOTE] O fornecedor de recursos do Azure IoT concentrador está protegido através da sua subscrição do Azure, como estão todos os fornecedores no [Gestor de recursos do Azure][lnk-azure-resource-manager].

Para mais informações sobre como construir e utilizar tokens de segurança, consulte o artigo [tokens de segurança IoT concentrador][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Detalhes do protocolo

Cada protocolo suportado, como MQTT, AMQP e HTTP, transportes de tokens de formas diferentes.

Ao utilizar MQTT, o pacote de ligar tem o ID de dispositivo como ClientId, {iothubhostname} / {deviceId} no campo nome de utilizador e um token de SA no campo de palavra-passe. {iothubhostname} deve ser o CName completo do concentrador IoT (por exemplo, contoso.azure-devices.net).

Quando utilizar [AMQP][lnk-amqp], IoT concentrador suporta [SASL simples] [ lnk-sasl-plain] e [AMQP violação de segurança com base em -][lnk-cbs].

Se utiliza o AMQP violação de segurança com base em-, o padrão Especifica como transmitir estes tokens.

Para SASL simples, pode ser o **nome de utilizador** :

* `{policyName}@sas.root.{iothubName}`Se utilizar o nível de concentrador tokens.
* `{deviceId}@sas.{iothubname}`Se utilizar tokens confinados dispositivo.

Em ambos os casos, o campo da palavra-passe contém o token, tal como descrito em [tokens de segurança IoT concentrador][lnk-sas-tokens].

HTTP implementa autenticação, incluindo um token válido no cabeçalho do pedido de **autorização** .

#### <a name="example"></a>Exemplo

Nome de utilizador (DeviceId é sensível a maiúsculas e minúsculas):`iothubname.azure-devices.net/DeviceId`

Palavra-passe (gerar SA com o Explorador de dispositivo):`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] O [Azure IoT concentrador SDK] [ lnk-sdks] tokens de verificação gerar automaticamente ao ligar ao serviço. Em alguns casos, os SDK não suportam todos os protocolos ou todos os métodos de autenticação.

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais para SASL simples

Quando utilizar SASL simples com AMQP, um cliente a ligar a um concentrador de IoT pode utilizar um único token para cada ligação de TCP. Quando o token de expira, a ligação TCP desliga de serviço e accionadores uma restabelecer a ligação. Este comportamento, apesar de não problemático para um componente de back-end de aplicação, é muito prejudicial para uma aplicação do lado do dispositivo pelos seguintes motivos:

*  Normalmente, os gateways ligue-se em nome de muitos dispositivos. Ao utilizar SASL simples, têm de criar uma ligação de TCP distinta para cada um dos dispositivos que liga a um concentrador de IoT. Este cenário consideravelmente aumenta o consumo de energia e recursos de redes e aumenta a latência de cada ligação de dispositivo.
* Dispositivos restringido recurso adverso são afetados através da utilização de recursos para voltar a ligar após cada data de expiração token maior.

## <a name="scope-hub-level-credentials"></a>Credenciais de nível de hub do âmbito

Pode reduzir a políticas de segurança de nível de concentrador através da criação de tokens com um recurso restrito URI. Por exemplo, o ponto final para enviar mensagens de dispositivo para nuvem a partir de um dispositivo é **/devices/ {deviceId} / mensagens/eventos**. Também pode utilizar uma política de acesso ao nível do concentrador de partilhado com permissões de **DeviceConnect** para assinar um token cujo URI do recurso é **/devices/ {deviceId}**. Esta abordagem cria um token de que só pode ser utilizado para enviar mensagens em nome do dispositivo **deviceId**.

Este mecanismo é semelhante a [política do fabricante evento concentradores][lnk-event-hubs-publisher-policy]e permite-lhe implementar métodos de autenticação personalizada.

## <a name="security-tokens"></a>Tokens de segurança

Concentrador IoT utiliza tokens de segurança para autenticar dispositivos e serviços para evitar enviar teclas no fio. Para além disso, tokens de segurança são limitados na data de validade e âmbito. [Azure IoT concentrador SDK] [ lnk-sdks] gerar automaticamente tokens sem necessidade de nenhuma configuração especial. No entanto, alguns cenários, requerem que o utilizador gerar e utilizar diretamente tokens de segurança. Estes incluem a utilização das superfícies MQTT, AMQP ou HTTP direta ou a execução do padrão de serviço de tokens, tal como é explicado [autenticação do dispositivo personalizada][lnk-custom-auth].

Concentrador IoT também permite que os dispositivos autenticar com IoT concentrador utilizar [certificados x. 509][lnk-x509]. 

### <a name="security-token-structure"></a>Estrutura do token de segurança
Utilize tokens de segurança para conceder acesso limitado de tempo a dispositivos e serviços para funcionalidades específicas no Centro de IoT. Para se certificar de que apenas autorizados dispositivos e serviços podem ligar-se, tokens de segurança tem de ter sessão iniciadas com uma chave de política de acesso partilhado ou uma chave simétrica armazenados com uma identidade de dispositivo no registo identidade.

Um token sessão iniciada com uma chave concede de política de acesso partilhado acesso a todas as funcionalidades associadas com as permissões de política de acesso partilhado. Por outro lado, um token sessão iniciado com a chave simétrica uma identidade de dispositivo concede apenas a permissão de **DeviceConnect** para a identidade do dispositivo associado.

O token de segurança tem o seguinte formato:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estes são os valores esperados:

| Valor | Descrição |
| ----- | ----------- |
| {assinatura} | Uma cadeia de assinatura HMAC SHA256 do formulário: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: A chave é descodificar a partir do base64 e utilizada como chave para executar o cálculo HMAC SHA256. |
| {URI do recurso} | URI prefixo (por segmento) os pontos finais que possa ser acedida com este token, começando com o nome do anfitrião do concentrador de IoT (sem protocol). Por exemplo,`myHub.azure-devices.net/devices/device1` |
| {termo} | Cadeias de UTF8 para o número de segundos desde o UTC de 00:00:00 época no 1 de Janeiro de 1970. |
| {URL-codificado-URI do recurso} | Quanto mais baixa do incidente codificação de URL do recurso minúsculas URI |
| {Política} | O nome da política de acesso partilhado ao qual se refere este token de. Ausente no caso de tokens que fazem referência a credenciais de registo de dispositivo. |

**Nota sobre prefixo**: prefixo URI de é calculado pelo segmento e não pelo caráter. Por exemplo `/a/b` é um prefixo para `/a/b/c` , mas não para `/a/bc`.

O fragmento de Node.js seguinte mostra uma função denominada **generateSasToken** que calcula o token das entradas `resourceUri, signingKey, policyName, expiresInMins`. As secções seguintes detalhes como a inicialização as entradas diferentes para os casos de utilização token diferente.

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // Use crypto
        var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
        hmac.update(toSign);
        var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

        // Construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        return token;
    };

Como uma comparação, o código de Python equivalente para gerar um token de segurança é:

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from time import time
    from urllib import quote_plus, urlencode
    from hmac import HMAC

    def generate_sas_token(uri, key, policy_name, expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
        print sign_key
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        rawtoken = {
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl))
        }

        if policy_name is not None:
            rawtoken['skn'] = policy_name

        return 'SharedAccessSignature ' + urlencode(rawtoken)

> [AZURE.NOTE] Uma vez que a data de validade do token for validada no máquinas IoT concentrador, é importante que o desvio no relógio do computador que gera o token de ser mínimas.

### <a name="use-sas-tokens-in-a-device-client"></a>Utilizar tokens de SA um cliente de dispositivo

Existem duas formas para obter permissões **DeviceConnect** com IoT concentrador com tokens de segurança: utilizar uma [chave de dispositivo simétrica do registo de identidade do dispositivo](#use-a-symmetric-key-in-the-identity-registry)ou utilizar uma [chave de política de acesso partilhado](#use-a-shared-access-policy).

Lembre-se de que todas as funcionalidades acessíveis a partir de dispositivos são exposta pela estrutura em pontos finais com prefixo `/devices/{deviceId}`.

> [AZURE.IMPORTANT] A única forma que IoT concentrador autentica um dispositivo específico está a utilizar a chave simétrica de identidade do dispositivo. Em casos quando uma política de acesso partilhado é utilizada para aceder a funcionalidade de dispositivo, a solução tem de considerar o componente emitir o token de segurança como um sub componente fidedigna.

Sejam os pontos finais dispositivo destinado (independentemente do protocolo):

| Ponto final | Funcionalidade |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Envie mensagens de dispositivo a nuvem. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Receba mensagens de dispositivo de nuvem. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Utilizar uma chave simétrica no registo identidade

Ao utilizar a chave simétrica uma identidade de dispositivo para gerar um token de política (`skn`) elemento do token é omitido.

Por exemplo, um token criado para aceder a todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: qualquer tecla simétrica para o `{device id}` identidade
* sem nome da política,
* qualquer altura de expiração.

Um exemplo utiliza a função de nó acima seria:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

O resultado, o que concede acesso a todas as funcionalidades para device1, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] É possível gerar um token seguro utilizando a ferramenta de .NET [Dispositivo Explorer][lnk-device-explorer].

### <a name="use-a-shared-access-policy"></a>Utilizar uma política de acesso partilhado

Ao criar um token a partir de uma política de acesso partilhada, a política de nome de campo `skn` tem de estar definida para o nome da política utilizada. Também é necessário que a política concede a permissão de **DeviceConnect** .

Os dois cenários principais para utilizar as políticas de acesso partilhada para aceder à funcionalidade de dispositivo são:

* [na nuvem gateways protocolo][lnk-endpoints],
* [Serviços de token] [ lnk-custom-auth] utilizados para implementar esquemas de autenticação personalizado.

Uma vez que a política de acesso partilhado potencialmente pode conceder acesso a ligar-se como qualquer dispositivo, é importante utilizar o recurso correcto URI quando criar tokens de segurança. Este é particularmente importante para serviços do tokens, tem do token para um dispositivo específico a utilizar o recurso URI do âmbito. Este ponto é menos relevante para os gateways de protocolo à medida que já estão controlando o tráfego para todos os dispositivos.

Por exemplo, um serviço de tokens utilizando a política de acesso partilhado previamente criado denominada **dispositivo** pretende criar um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: um das teclas do `device` política,
* nome da política: `device`,
* qualquer altura de expiração.

Um exemplo utiliza a função de nó acima seria:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

O resultado, o que concede acesso a todas as funcionalidades para device1, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Um gateway protocolo pode utilizar o mesmo token para todos os dispositivos, basta definir URI do recurso para `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Utilizar tokens de segurança a partir de componentes de serviço

Componentes de serviço só podem gerar tokens de segurança utilizando as políticas de acesso partilhado conceder as permissões adequadas, tal como é explicado anteriormente.

Estas são as funções de serviço expostas nos pontos finais:

| Ponto final | Funcionalidade |
| ----- | ----------- |
| `{iot hub host name}/devices` | Criar, atualizar, recuperar e eliminar identidades do dispositivo. |
| `{iot hub host name}/messages/events` | Receba mensagens de dispositivo a nuvem. |
| `{iot hub host name}/servicebound/feedback` | Receba comentários para mensagens de dispositivo de nuvem. |
| `{iot hub host name}/devicebound` | Envie mensagens de dispositivo de nuvem. |

Por exemplo, um serviço gerar utilizando a política de acesso partilhado previamente criado denominada **registryRead** seria criar um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices`,
* chave de assinatura: um das teclas do `registryRead` política,
* nome da política: `registryRead`,
* qualquer altura de expiração.

    ponto final de var = "myhub.azure-devices.net/devices";   var política = 'dispositivo';   var policyKey = '...';

    var token = generateSasToken (ponto final, policyKey, política, 60);

O resultado, o qual pretende conceder acesso ao ler todas as identidades do dispositivo, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>Certificados x. 509 suportados

Pode utilizar qualquer certificado x. 509 para autenticar um dispositivo com IoT concentrador. Isto inclui:

-   **Um certificado x. 509 existente**. Um dispositivo pode já ter um certificado x. 509 associado. O dispositivo pode utilizar este certificado para autenticar com IoT concentrador.

-   **Um certificado de X-509 personalizada gerado e autoassinado**. Um fabricante do dispositivo ou objecto utilização interno pode gerar estes certificados e armazenar a chave privada correspondente (e o certificado) no dispositivo. Pode utilizar ferramentas como [OpenSSL] [ lnk-openssl] e [Windows SelfSignedCertificate] [ lnk-selfsigned] utility para esse efeito.

-   **Certificado x. 509 assinado AC**. Também pode utilizar um certificado x. 509 geradas e assinado por uma autoridade de certificação (AC) para identificar um dispositivo e um dispositivo com IoT concentrador de autenticação.

Um dispositivo pode utilizar um certificado x. 509 ou um token de segurança para autenticação, mas não ambos.

### <a name="register-an-x509-client-certificate-for-a-device"></a>Registar um certificado de cliente x. 509 para um dispositivo

O [Azure IoT serviço SDK para c#] [ lnk-service-sdk] (versão 1.0.8+) suporta registar um dispositivo que utiliza um certificado de cliente x. 509 para autenticação. Outras APIs como importar/exportar dos dispositivos também suportam x. 509 certificados de cliente.

### <a name="c-support"></a>C\# suporte

A classe de **RegistryManager** fornece uma maneira de programação para registar um dispositivo. Em particular, os métodos de **AddDeviceAsync** e **UpdateDeviceAsync** permitem que um utilizador registar e actualizar um dispositivo no registo de identidade do concentrador de Iot dispositivo. Estes dois métodos demorar uma instância de **dispositivo** , como entrada. A classe de **dispositivo** inclui uma propriedade de **autenticação** que permite ao utilizador especificar as miniaturas de certificado x. 509 principais e secundárias. A impressão digital representa um hash SHA-1 do certificado x. 509 (armazenado utilizando a codificação de DER binário). Os utilizadores têm a opção de especificar uma impressão digital principal ou uma impressão digital secundário ou ambos. Principais e secundárias miniaturas são suportadas para processar cenários de sobreposição do certificado.

> [AZURE.NOTE] Concentrador IoT não exigir ou armazenar o certificado de cliente x. 509 inteiro, apenas a impressão digital.

Eis um exemplo C\# fragmento de código para registar um dispositivo com um certificado de cliente x. 509:

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>Utilize um certificado de cliente de x. 509 durante o tempo de execução operações

O [dispositivo Azure IoT SDK para .NET] [ lnk-client-sdk] (versão 1.0.11+) suporta a utilização de x. 509 certificados de cliente.

### <a name="c-support"></a>C\# suporte

A classe **DeviceAuthenticationWithX509Certificate** suporta a criação de instâncias de  **DeviceClient** utilizando um certificado de cliente x. 509.

Eis um fragmento de código de exemplo:

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Autenticação do dispositivo personalizado

Pode utilizar o [registo de identidade do dispositivo] de IoT concentrador[ lnk-identity-registry] para configurar as credenciais de segurança por dispositivo e aceder ao controlo utilizando [tokens][lnk-sas-tokens]. No entanto, se uma solução IoT já tiver um investimento significativo num esquema de registo e/ou a autenticação de identidade do dispositivo personalizado, pode integrar este infraestrutura existente com IoT concentrador através da criação de um *serviço de tokens*. Desta forma, pode utilizar outras funcionalidades IoT na sua solução.

Um serviço de tokens é um serviço de nuvem personalizado. Utiliza uma IoT concentrador *política de acesso partilhado* com permissões de **DeviceConnect** para criar tokens *confinados dispositivo* . Estes tokens de ativar um dispositivo ligar ao seu centro IoT.

  ![Passos para o padrão de serviço de tokens][img-tokenservice]

Estes são os passos principais do padrão token de serviço:

1. Crie uma política de acesso do concentrador de IoT partilhado com permissões de **DeviceConnect** para o seu centro IoT. Pode criar esta política no [portal do Azure] [ lnk-management-portal] ou através de programação. O serviço de tokens utiliza esta política para assinar os tokens que cria.
2. Quando necessita de um dispositivo para aceder ao seu centro IoT, pedidos um token com a sessão iniciada a partir do seu serviço de tokens. O dispositivo pode autenticar com o esquema de registo/autenticação de identidade do dispositivo personalizado para determinar a identidade do dispositivo que o serviço de tokens utiliza para criar o token.
3. O serviço de tokens devolve um token. O token é criado utilizando `/devices/{deviceId}` como `resourceURI`, com `deviceId` como o dispositivo que está a ser autenticado. O serviço de tokens utiliza a política de acesso partilhado para construir o token.
4. O dispositivo utiliza o token diretamente com o concentrador de IoT.

> [AZURE.NOTE] Pode utilizar a classe de .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] ou para a classe Java [IotHubServiceSasToken] [ lnk-java-sas] para criar um token no seu serviço de token.

O serviço de tokens pode definir a data de expiração token conforme pretender. Quando o token de expira, e o concentrador IoT interromper a ligação do dispositivo. Em seguida, o dispositivo terá de pedir um novo token do serviço de tokens. Se utilizar um tempo de expiração breve, isto aumenta a carga o dispositivo e o serviço de tokens.

Para um dispositivo ligar ao seu centro, tem ainda adicioná-lo para o registo de identidade do dispositivo IoT concentrador — apesar do dispositivo está a utilizar um token e não é uma chave de dispositivo para ligar. Por conseguinte, pode continuar a utilizar o controlo de acesso por dispositivo ao ativar ou desativar identidades do dispositivo no [registo de identidade do concentrador de IoT] [ lnk-identity-registry] quando o dispositivo autentica com um token. Isto atenua os riscos de utilização de tokens com os tempos de termo longo.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um gateway personalizado

O padrão de serviço de tokens é o caminho recomendado para implementar um esquema de registo/autenticação de identidade personalizada com IoT concentrador. É recomendável porque IoT concentrador continua a processar a maior parte do tráfego de solução. No entanto, existem casos onde o esquema de autenticação personalizado é por isso, interligado com o protocolo que um serviço de processamento de todo o tráfego (*gateways personalizados*) não é necessário. Um exemplo de isto é [segurança de camada de transporte (TLS) e chaves previamente partilhadas (PSKs)][lnk-tls-psk]. Para obter mais informações, consulte o artigo o [gateway protocolo] [ lnk-protocols] tópico.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre a controlar o acesso ao seu centro IoT.

## <a name="iot-hub-permissions"></a>Permissões de IoT concentrador

A tabela seguinte apresenta as permissões que pode utilizar para controlar o acesso ao seu centro IoT.

| Permissão            | Notas |
| --------------------- | ----- |
| **RegistryRead**      | Concede acesso ao registo de identidade do dispositivo de leitura. Para obter mais informações, consulte o artigo [registo de identidade do dispositivo][lnk-identity-registry]. |
| **RegistryReadWrite** | Concede leitura e escrita acesso ao registo de identidade do dispositivo. Para obter mais informações, consulte o artigo [registo de identidade do dispositivo][lnk-identity-registry]. |
| **ServiceConnect**    | Concede de acesso para a cloud serviço destinado comunicação e monitorizar os pontos finais. Por exemplo,-concede permissão aos serviços em nuvem de back-end para receber mensagens de nuvem de dispositivo, enviar mensagens de dispositivo de nuvem e obter as confirmações de entrega correspondente. |
| **DeviceConnect**     | Concede acesso para os pontos finais de comunicação de dispositivo destinado. Por exemplo,-concede permissão para enviar mensagens de nuvem de dispositivo e receber mensagens de dispositivo de nuvem. Esta permissão é utilizado por dispositivos. |

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador incluem:

- [Os pontos finais de IoT concentrador] [ lnk-endpoints] descreve os vários pontos finais que cada concentrador IoT expõe para operações de runtime e gestão.
- [Limitação e quotas] [ lnk-quotas] descreve as quotas de que se aplicam ao serviço de IoT concentrador e o comportamento optimização esperar quando vai utilizar o serviço.
- [Concentrador IoT dispositivo e serviço SDK] [ lnk-sdks] listas o idioma de vários SDK que uma utilização quando desenvolver o dispositivo e serviço aplicações que interagem com IoT concentrador.
- [Idioma de consulta para gémeos, métodos e tarefas de] [ lnk-query] descreve o idioma de consulta que pode utilizar para recuperar informações a partir do IoT Hub sobre as suas gémeos de dispositivo, métodos e tarefas.
- [Suporte de IoT concentrador MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT concentrador para o protocolo MQTT.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como controlar o acesso IoT concentrador, poderá estar interessado os seguintes tópicos de guia do programador:

- [Utilizar gémeos dispositivo para sincronizar distrito e configurações][lnk-devguide-device-twins]
- [Invocar um método direto num dispositivo][lnk-devguide-directmethods]
- [Agendar tarefas em múltiplos dispositivos][lnk-devguide-jobs]

Se quiser experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado os tutoriais IoT concentrador seguintes:

- [Introdução ao Azure IoT concentrador][lnk-getstarted-tutorial]
- [Como enviar mensagens de dispositivo de nuvem com IoT concentrador][lnk-c2d-tutorial]
- [Como mensagens de processo IoT concentrador dispositivo à nuvem][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
