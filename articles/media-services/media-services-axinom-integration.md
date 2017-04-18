<properties 
    pageTitle="Utilizar Axinom a Widevine licenças para dos serviços de multimédia do Azure | Microsoft Azure" 
    description="Este artigo descreve como pode utilizar serviços de multimédia do Azure (MGA) para a prestação uma sequência de que está encriptada dinamicamente pela AMS com PlayReady e Widevine DRMs. A licença PlayReady provêm de servidor de licenças PlayReady de serviços de multimédia e Widevine licença é entregue ao servidor de licenças Axinom." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"   
    ms.author="willzhan;Mingfeiy;rajputam;Juliako"/>

#<a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilizar Axinom a Widevine licenças para dos serviços de multimédia do Azure  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##<a name="overview"></a>Descrição geral

Serviços de multimédia Azure (MGA) adicionou protecção dinâmica Google Widevine (consulte o [blogue do Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) para obter mais detalhes). Além disso, o Azure Media Player (AMP) adicionou também suporte Widevine (consulte [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) para obter mais detalhes). Este é um principal específico no conteúdo de TRAÇO protegido por CENC com multi-native-DRM (PlayReady e Widevine) de transmissão em browsers modernos equipados com MSE e EME.

Começar com o suporte de dados serviços .NET SDK versão 3.5.2, dos serviços de multimédia permite-lhe configurar Widevine licença modelo e obter Widevine licenças. Também pode utilizar os parceiros de AMS seguintes para ajudá-lo a entregar Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Este artigo descreve como integrar e testar o servidor de licenças de Widevine gerida pelo Axinom. Especificamente, abrange:  

- Configurar encriptação comuns dinâmico com multi-DRM (PlayReady e Widevine) com URLs de aquisição licença correspondente;
- Gerar um token JWT para cumprir os requisitos do servidor de licença;
- Desenvolver a aplicação, Azure Media Player que trata aquisição licença com a autenticação do token JWT;

O sistema concluído e o fluxo de conteúdos que chave, chave ID, propagação chave, token JTW e em suas afirmações pode ser melhor descrito pelo diagrama seguinte.

![TRAÇO e CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##<a name="content-protection"></a>Proteção de conteúdo

Para configurar protecção dinâmica e a política de entrega chave, consulte o artigo blogue do Mingfei: [como configurar Widevine embalagem com os serviços de multimédia do Azure](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Pode configurar protecção CENC dinâmica com multi DRM para TRAÇO transmissão ter ambas as seguintes opções:

1. Proteção de PlayReady para MS Edge e IE11, que poderia ter uma restrições de autorização token. A política de restrita token tem de ser acompanhar por um token emitido por uma seguro Token serviço (STS), tal como o Azure Active Directory;
1. Proteção de Widevine para o Chrome,-pode exigir autenticação token com token emitido por outro STS. 

Consulte o artigo [Geração Token JWT](media-services-axinom-integration.md#jwt-token-generation) secção para por que motivo Azure Active Directory não podem ser utilizado como um STS Widevine servidor do Axinom de licenças.

###<a name="considerations"></a>Considerações sobre

1. Tem de utilizar Axinom especificado ID para gerar a chave de conteúdo para configurar o serviço de entrega de chave de chave de propagação chave (8888000000000000000000000000000000000000) e os seus gerado ou selecionado. Servidor de licenças Axinom irá emitir todas as licenças que contém as teclas conteúdas com base na mesmo propagação chave, que é válida para testar e produção.
1. O URL de aquisição Widevine licença para testar a ligação: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP e HTTS são permitidos.

##<a name="azure-media-player-preparation"></a>Preparação do leitor de multimédia do Microsoft Azure

AMP v1.4.0 suporta a reprodução de conteúdo de AMS dinamicamente compactado com PlayReady e Widevine DRM.
Se o servidor de licenças Widevine não requer autenticação de token, não existe nada adicionais que precisa para testar conteúdo um TRAÇO protegida por Widevine. Por exemplo, a equipa de AMP fornece uma simples [exemplo](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html), onde pode ver isto funcione no limite e IE11 com PlayReady e no Chrome com Widevine.
O servidor de licença Widevine fornecido pelo Axinom requer autenticação token de JWT. O token JWT tem de ser apresentado com pedido de licença através de um cabeçalho de HTTP "X AxDRM-mensagens". Para o efeito, terá de adicionar o javascript seguinte na página web que aloja AMP antes de definir a origem:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

O resto do código AMP é padrão AMP API, tal como no documento AMP [aqui](http://amp.azure.net/libs/amp/latest/docs/).

Note que a javascript acima de um cabeçalho de autorização personalizado definição ainda é uma abordagem de curto prazo antes do funcionário abordagem de longo prazo em AMP é disponibilizada.

##<a name="jwt-token-generation"></a>JWT Token. ª geração

Servidor de licenças Axinom Widevine para testar a ligação requer autenticação token de JWT. Além disso, uma das afirmações no token de JWT é de um tipo de objeto complexa em vez de tipo de dados primitivos.

Infelizmente, Azure AD apenas pode emitir JWT tokens com tipos primitivos. Da mesma forma, .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler e JwtPayload) apenas permite-lhe introduzir o tipo de objeto complexa como em afirmações. No entanto, as afirmações ainda são serializadas como cadeia. Por conseguinte, não é possível utilizamos qualquer uma das duas para gerar o token de JWT para pedido de licença Widevine.


De João Sheehan [JWT Nuget pacote](https://www.nuget.org/packages/JWT) cumpre as necessidades, de modo que vamos utilizar este pacote Nuget.

Segue-se o código para gerar token JWT com as afirmações conforme seja necessárias conforme necessário pelo servidor de licenças Axinom Widevine para testar a ligação:

    
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;
    
    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
    
                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };
    
                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
    
                return token;
            }
    
            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }
    
                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }
    
                return HexAsBytes;
            }
    
        }  
    
    }  

Servidor de licenças Axinom Widevine

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###<a name="considerations"></a>Considerações sobre

1.  Apesar de serviço de entrega de licença AMS PlayReady requer "portadores =" que antecede um token de autenticação, servidor de licenças Axinom Widevine não utilizá-lo.
2.  A chave de comunicação Axinom é utilizada como chave de assinatura. Tenha em atenção que a chave é uma cadeia de hex, no entanto, deve ser tratada como uma série de bytes não uma cadeia, quando a codificação. Pode fazê-lo o método ConvertHexStringToByteArray.

##<a name="retrieving-key-id"></a>Obter o ID da chave

É provavelmente, reparou que o código para gerar um JWT ID token, chave é necessária. Uma vez que as necessidades tokens JWT esteja pronta antes do carregamento AMP player, tecla ID tem de ser obtidos para gerar JWT token.

Existem várias formas de obter mantenha a tecla de chave do curso ID. Por exemplo, pode armazenar um ID de chave juntamente com o conteúdo metadados numa base de dados. Ou pode obter ID a partir de ficheiro MPD travessão (descrição de apresentação de multimédia) chave. O código abaixo encontra-se para o último.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }
    
        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();
    
        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
    
        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }
    
        return key_id;
    }

##<a name="summary"></a>Resumo

Com adição mais recente de suporte Widevine no protecção de conteúdos do serviços de multimédia do Azure e Azure Media Player, estamos a conseguir implementar a transmissão de TRAÇO + Multi-native-DRM (PlayReady + Widevine) com o serviço de licença ambas as PlayReady no AMS e Widevine servidor de licenças de Axinom para modernas seguintes browsers:

- Chrome
- Limite da Microsoft no Windows 10
- IE 11 no Windows 8.1 e Windows 10
- Firefox (ambiente de trabalho) e Safari no Mac (não iOS) também são suportados através do Silverlight e o mesmo URL com o Azure Media Player

Os seguintes parâmetros são necessários no servidor de licenças de Axinom Widevine tirar partido da solução clique em Guardar. Exceto chave ID, o resto dos parâmetros são fornecidos por Axinom com base no seu programa de configuração do Widevine server.


Parâmetro|Como são utilizadas
---|---
ID de chave de comunicação|Tem de ser incluídos como valor de afirmação "com_key_id" no token de JWT (consulte [nesta](media-services-axinom-integration.md#jwt-token-generation) secção).
Tecla de comunicação|Tem de ser utilizado como a chave de assinatura do token JWT (consulte [nesta](media-services-axinom-integration.md#jwt-token-generation) secção).
Chave propagação|Tem de ser utilizada para gerar a chave de conteúdo com qualquer conteúdo determinado ID de chave (consulte [nesta](media-services-axinom-integration.md#content-protection) secção).
URL de aquisição Widevine licença|Deve ser utilizado em configurar a política de entrega de elementos para TRAÇO transmissão (consulte o artigo [nesta](media-services-axinom-integration.md#content-protection) secção).
ID da chave do conteúdo|Tem de ser incluídos como parte do valor do afirmação direito mensagem do token JWT (consulte [nesta](media-services-axinom-integration.md#jwt-token-generation) secção). 


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Confirmações 

Gostaríamos de reconhecermos as seguintes pessoas a quem contribuíram concreto este documento: Kristjan Jõgi de Axinom, Mingfei Yan e Amit Rajput.
