<properties 
    pageTitle="Descrição geral dos modelos Widevine licença | Microsoft Azure" 
    description="Este tópico fornece uma descrição geral de um modelo de licença de Widevine utilizado para configurar Widevine licenças." 
    authors="juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>

#<a name="widevine-license-template-overview"></a>Descrição geral dos modelos Widevine licença

##<a name="overview"></a>Descrição geral

Agora o Azure dos serviços de multimédia permite-lhe configurar e pedir Widevine licenças. Quando tenta o leitor de utilizador final reproduzir o seu conteúdo Widevine protegida, é enviado um pedido para o serviço de entrega de licenças para obter uma licença. Se o serviço de licenças aprovar o pedido, problemas de licença que é enviada para o cliente e pode ser utilizada para desencriptar e reproduzir o conteúdo especificado.

Pedido de licença Widevine está formatado como uma mensagem JSON.  

Note que pode optar por criar uma mensagem de vazia sem valores basta "{}" e será criado um modelo de licença com todas as predefinições.  

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

##<a name="json-message"></a>Mensagem JSON

Nome | Valor | Descrição
---|---|---
carga útil |Cadeia de codificação Base64 |O pedido de licença enviado por um cliente. 
content_id | Cadeia de codificação Base64|Identificador utilizado para deriva KeyId(s) e chaves de conteúdo para cada content_key_specs.track_type.
fornecedor |cadeia |Utilizado para procurar e políticas de teclas de conteúdo. Obrigatório.
nome_política | cadeia |Nome de uma política de registado anteriormente. Opcional
allowed_track_types | enumeração  | SD_ONLY ou SD_HD. Controlos de conteúdo teclas deverão ser incluídos numa licença
content_key_specs | matriz de JSON estruturas, consulte o artigo **Especificações de chave de conteúdo** | Um melhor controlo no qual o conteúdo que as teclas para devolver. Consulte o artigo especificações de chave conteúdo abaixo para obter detalhes.  Apenas uma das allowed_track_types e content_key_specs pode ser especificada. 
use_policy_overrides_exclusively | Booleano. VERDADEIRO ou FALSO | Utilize os atributos de política especificados pelo policy_overrides e omitir todas as política armazenada anteriormente.
policy_overrides | JSON estruturar, consulte o artigo a **Política substitui** abaixo | Definições de política para esta licença.  Na eventualidade deste recurso tem uma política de predefinidos, estes valores especificados serão utilizados. 
session_init | JSON estruturar, consulte o artigo **Iniciar sessão** | Dados opcionais passou à licença.
parse_only | Booleano. VERDADEIRO ou FALSO | O pedido de licença é analisado mas nenhuma licença é emitida. No entanto, os valores de formulário de pedido de licença são devolvidos na resposta.  

##<a name="content-key-specs"></a>Especificações de chaves de conteúdo 

Se existir uma política de pré-existentes, não é necessário para especificar os valores a especificação de chave de conteúdo.  A política de pré-existentes associada este conteúdo será utilizada para determinar a proteção de saída como HDCP e CGMS.  Se uma política de pré-existentes não está registada com o servidor de licença Widevine, o fornecedor de conteúdos pode inserir os valores no pedido de licença.   


Cada content_key_specs tem de ser especificado para todas as pistas, independentemente de use_policy_overrides_exclusively a opção. 


Nome | Valor | Descrição
---|---|---
content_key_specs. track_type | cadeia | Um nome de tipo de registo. Se não for especificado content_key_specs no pedido de licença, certifique-se especificar a que todos os tipos de controlar explicitamente. Falha ao fazê-lo irá resultar numa falha de para reprodução últimos 10 segundos. 
content_key_specs  <br/> security_level | UInt32 | Define cliente robustez requisitos para reprodução. <br/> 1 - baseados em software whitebox crypto é necessário. <br/> 2 - é necessária software crypto e um descodificador oculto. <br/> 3 - as operações crypto e material de chave tem de ser realizadas dentro de um ambiente de execução de confiança com cópia de segurança do hardware. <br/> 4 - o crypto e descodificar de conteúdo devem ser realizados dentro de um ambiente de execução de confiança com cópia de segurança do hardware.  <br/> 5 - os crypto, descodificação e todos os movimentação de ficheiros multimédia (comprimidos e não comprimido) tem de ser tratadas dentro de um ambiente de execução de confiança com cópia de segurança do hardware.  
content_key_specs <br/> required_output_protection.hDC | cadeia - um dos: HDCP_NONE, HDCP_V1, HDCP_V2 | Indica se é requerem HDCP
content_key_specs <br/>chave | Base64 <br/>cadeia codificada|Chave para utilizar para esta faixa de conteúdo. Se especificado, é necessário o track_type ou key_id.  Esta opção permite o fornecedor de conteúdo inserir a chave de conteúdo para esta faixa em vez de permitindo que o servidor de licenças de Widevine gerar ou procurar uma chave.
content_key_specs.key_id| Base64 codificado cadeia binária, 16 bytes | Identificador exclusivo para a chave. 


##<a name="policy-overrides"></a>Substituições de política 

Nome | Valor | Descrição
---|---|---
policy_overrides. can_play | Booleano. VERDADEIRO ou FALSO | Indica que reprodução do conteúdo é permitida. Predefinição é falso.
policy_overrides. can_persist | Booleano. VERDADEIRO ou FALSO |Indica que a licença pode ser persistente ao armazenamento de não voláteis para utilização offline. Predefinição é falso.
policy_overrides. can_renew | booleano verdadeiro ou FALSO |Indica que a renovação desta licença é permitida. Se for VERDADEIRO, a duração da licença pode ser prolongada por heartbeat. Predefinição é falso. 
policy_overrides. license_duration_seconds | Int64 | Indica a janela de tempo para esta licença específica. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. 
policy_overrides. rental_duration_seconds | Int64 | Indica a janela de tempo enquanto reprodução é permitida. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. 
policy_overrides. playback_duration_seconds | Int64 | Na janela de visualização de tempo depois de reprodução é iniciada dentro a duração de licença. Um valor de 0 indica que não existe nenhum limite para a duração. A predefinição é 0. 
policy_overrides. renewal_server_url |cadeia | Todos os pedidos de heartbeat (de renovação) para esta licença devem ser direcionados para o URL especificado. Este campo é utilizado apenas se can_renew for VERDADEIRO.
policy_overrides. renewal_delay_seconds |Int64 |Quantos segundos após license_start_time, antes de renovação é tentada pela primeira vez. Este campo é utilizado apenas se can_renew for VERDADEIRO. A predefinição é 0 
policy_overrides. renewal_retry_interval_seconds | Int64 | Especifica o atraso em segundos entre pedidos de renovação de licença subsequentes, em caso de falha. Este campo é utilizado apenas se can_renew for VERDADEIRO. 
policy_overrides. renewal_recovery_duration_seconds | Int64 | A janela de tempo, em que é permitida reprodução para continuar a enquanto renovação é tentada, ainda pode ter êxito devido a problemas de back-end com o servidor de licenças. Um valor de 0 indica que não existe nenhum limite para a duração. Este campo é utilizado apenas se can_renew for VERDADEIRO.
policy_overrides. renew_with_usage | booleano verdadeiro ou FALSO |Indica que a licença deve ser enviada para renovação quando for iniciada a utilização. Este campo é utilizado apenas se can_renew for VERDADEIRO. 

##<a name="session-initialization"></a>Iniciar sessão

Nome | Valor | Descrição
---|---|---
provider_session_token | Cadeia de codificação Base64 |Este token de sessão é transmitido novamente na licença do e existirão no renovação subsequentes.  O token de sessão não vai permanecer para além das sessões. 
provider_client_token | Cadeia de codificação Base64 | Token de cliente para enviar novamente na resposta à licença.  Se o pedido de licença contiver um token de cliente, este valor é ignorado. O token de cliente vai permanecer para além de sessões de licença.
override_provider_client_token | Booleano. VERDADEIRO ou FALSO |Se FALSO e o pedido de licença contém um token de cliente, utilize o token do pedido de mesmo se um token de cliente foi especificado nesta estrutura.  Se for VERDADEIRO, utilize sempre o token especificado nesta estrutura.

##<a name="configure-your-widevine-licenses-using-net-types"></a>Configurar as licenças de Widevine utilizar tipos de .NET

Dos serviços de multimédia fornece APIs .NET, que permitem-lhe configurar as licenças de Widevine. 

###<a name="classes-as-defined-in-the-media-services-net-sdk"></a>Classes tal como foi definido no SDK de .NET de serviços de multimédia

Seguem-se as definições dos seguintes tipos de.

    public class WidevineMessage
    {
        public WidevineMessage();
    
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

###<a name="example"></a>Exemplo

O exemplo seguinte mostra como utilizar o .NET APIs para configurar uma licença de Widevine simple.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Consulte também

[Utilizar a encriptação comuns PlayReady e/ou Widevine dinâmicos](media-services-protect-with-drm.md)
