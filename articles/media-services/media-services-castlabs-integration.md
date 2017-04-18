<properties 
    pageTitle="Utilizar castLabs a Widevine licenças para dos serviços de multimédia do Azure | Microsoft Azure" 
    description="Este artigo descreve como pode utilizar serviços de multimédia do Azure (MGA) para a prestação uma sequência de que está encriptada dinamicamente pela AMS com PlayReady e Widevine DRMs. A licença PlayReady provêm de servidor de licenças PlayReady de serviços de multimédia e Widevine licença é entregue ao servidor de licenças castLabs." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="Mingfeiy;willzhan;Juliako"/>


#<a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Utilizar castLabs a Widevine licenças para dos serviços de multimédia do Azure

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##<a name="overview"></a>Descrição geral

Este artigo descreve como pode utilizar serviços de multimédia do Azure (MGA) para a prestação uma sequência de que está encriptada dinamicamente pela AMS com PlayReady e Widevine DRMs. A licença PlayReady provêm de servidor de licenças PlayReady de serviços de multimédia e Widevine licença é entregue ao servidor de licenças **castLabs** .

Para reproduzir transmissão conteúdo protegido por CENC (PlayReady e/ou Widevine), pode utilizar [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Consulte o artigo [documento AMP](http://amp.azure.net/libs/amp/latest/docs/) para obter detalhes.

O diagrama seguinte demonstra dos serviços de multimédia de alto nível Azure e arquitetura de integração de castLabs.

![integração](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##<a name="typical-system-set-up"></a>Configurar o sistema de típico

- Conteúdo multimédia é armazenado numa AMS.
- IDs de chave de teclas de conteúdo são armazenados no castLabs e AMS.
- castLabs e AMS possuem a autenticação token incorporada. As secções seguintes abordam tokens de autenticação. 
- Quando um pedidos de cliente da cadeia o vídeo, o conteúdo é dinamicamente encriptado com **Encriptação comuns** (CENC) e dinamicamente empacotado por AMS a transmissão suaves e de TRAÇO. Recomendamos também entregar encriptação do ensino básico da cadeia PlayReady m2t para o protocolo de transmissão de HLS.
- Licença PlayReady é obtida a partir do servidor de licenças AMS e Widevine licença é obtida a partir do servidor de licenças castLabs. 
- Media Player automaticamente decide qual licença para obter com base na capacidade de plataforma do cliente. 

##<a name="authentication-token-generation-for-getting-a-license"></a>Geração de token de autenticação para obter uma licença

CastLabs e AMS suporta o formato de token JWT (JSON Web Token) utilizado para autorizar uma licença. 

###<a name="jwt-token-in-ams"></a>Token JWT na AMS 

A tabela seguinte descreve token JWT na AMS. 

Emissor|Cadeia emissor a partir do escolhido seguro Token serviço (STS)
---|---
Audiência|Cadeia de audiência a partir de o STS utilizado
Em afirmações|Um conjunto de afirmações
Não antes de|Começar a validade do token
Expirar|Fim validade do token
SigningCredentials|A chave que é partilhada entre PlayReady servidor de licenças, castLabs servidor de licenças e STS, dever-se simétrica ou assimétricos chave.

###<a name="jwt-token-in-castlabs"></a>Token JWT na castLabs

A tabela seguinte descreve token JWT na castLabs. 

Nome|Descrição
---|---
optData|Uma cadeia JSON que contém informações sobre si. 
CRT|Uma cadeia JSON que contém informações sobre o imobilizado seus direitos de informação e reproduzir licença.
iat|A data/hora atual no época.
jti|Um identificador exclusivo sobre este token (todos os tokens só podem ser utilizado uma vez no sistema de castLabs).

##<a name="sample-solution-set-up"></a>Solução de amostra configurar 

A [solução de amostra](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) é constituído por dois projetos:

-   Uma aplicação de consola que pode ser utilizada para definir restrições de DRM sobre um activo já aspirado, para PlayReady e Widevine.
-   Uma aplicação Web que mãos saída tokens, que podem ser visualizadas como uma versão muito simplificado um STS.


Para utilizar a aplicação da consola:

1.  Altere a App configurar credenciais AMS, castLabs credenciais, STS configuração e chave partilhada.
2.  Carregar um ativo para AMS.
3.  Obtenha o UUID do elemento carregado e alterar 32 de linha no ficheiro Program.cs:

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  Utilize um iddoactivo para nomear elemento no sistema de castLabs (linha 44 no ficheiro Program.cs).

    Tem de definir iddoactivo para **castLabs**; necessita de ser uma cadeia de alfanumérica exclusiva.

5.  Execute o programa.


Para utilizar a aplicação Web (STS):

1.  Alterar a Web. config para comerciante de castlabs configuração ID, a configuração de STS e a chave partilhada.
2.  Implemente a Web sites Azure.
3.  Navegue para o Web site.

##<a name="playing-back-a-video"></a>Reproduzir um vídeo

Para reproduzir um vídeo encriptado com encriptação comuns (PlayReady e/ou Widevine), pode utilizar o [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Quando executar a aplicação de consola, o ID da chave de conteúdos e o URL manifesto são enviados.

1.  Abrir um novo separador e inicie o seu STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.  Aceda ao [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.  Copie o URL de transmissão.
4.  Clique na caixa de verificação **Opções avançadas** .
5.  Na lista pendente **proteção** , selecione PlayReady e/ou Widevine.
6.  Cole o token que recebeu do seu STS na caixa de texto Token. 
    
    Não necessita do servidor de licenças castLab a "portadores =" prefixo à frente do token. Por isso, remova que antes de a submeter o token.
7.  Atualize o leitor.
8.  Deverá ser reproduzir o vídeo.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
