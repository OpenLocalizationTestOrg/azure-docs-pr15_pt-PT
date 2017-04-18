<properties 
    pageTitle="Configurar a política de autorização de chave de conteúdo utilizando o portal do Azure | Microsoft Azure" 
    description="Saiba como configurar uma política de autorização para uma chave de conteúdo." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="juliako"/>



#<a name="configure-content-key-authorization-policy"></a>Configurar a política de autorização de chave de conteúdo
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##<a name="overview"></a>Descrição geral

Serviços de multimédia do Microsoft Azure permite-lhe entregar sequências de TRAÇO MPEG, transmissão suaves e transmissão HTTP Live (HLS) protegidas com encriptação AES (Advanced Standard) (utilizando as chaves de encriptação de 128-bit) ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS também permite-lhe entregar sequências de TRAÇO encriptadas com Widevine DRM. PlayReady e Widevine são encriptados pela especificação de encriptação comum (ISO/IEC 23001-7 CENC).

Dos serviços de multimédia também fornece um **Serviço de entrega de chave/licenças** a partir do qual os clientes podem obter chaves AES ou PlayReady/Widevine licenças para reproduzir o conteúdo encriptado.

Este tópico mostra como utilizar o portal do Azure para configurar a política de autorização da chave conteúdo. A chave mais tarde pode ser utilizada para encriptar dinamicamente o conteúdo. Nota que atualmente pode encriptar a transmissão seguintes formatos: HLS, MPEG TRAÇO e transmissão suaves. Não é possível encriptar HDS transmissão formato ou progressive transferências.

Quando um leitor de pedidos de uma sequência de que está definida para ser dinamicamente encriptados, dos serviços de multimédia utiliza a chave configurada para encriptar dinamicamente o conteúdo a utilizar encriptação AES ou DRM. Para desencriptar a sequência, o leitor irá pedir a chave de serviço de entrega chave. Para decidir se ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou na chave.


Se planeia ter múltiplas teclas conteúdas ou pretende especificar um URL do **Serviço de entrega de chave/licença** que não seja o serviço de entrega chave dos serviços de multimédia, utilize os serviços de multimédia .NET SDK ou REST APIs.

[Configurar a política de autorização de chave de conteúdo utilizando .NET SDK serviços de multimédia](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurar a política de autorização de chave de conteúdo utilizando serviços de multimédia REST API](media-services-rest-configure-content-key-auth-policy.md)

###<a name="some-considerations-apply"></a>Algumas considerações aplicam-se:

- Para poder utilizar embalagem dinâmica e a encriptação dinâmica, tem de se certificar de ter, pelo menos, uma transmissão unidade reservada. Para mais informações, consulte o artigo [como dimensionar um serviço de multimédia](media-services-portal-manage-streaming-endpoints.md).
- O recurso tem de conter um conjunto de velocidade ajustável MP4s ou velocidade ajustável transmissão suaves ficheiros. Para mais informações, consulte o artigo [codificar um ativo](media-services-encode-asset.md).
- O serviço de entrega de chave coloca em cache ContentKeyAuthorizationPolicy e respectivos relacionados objectos (opções de política e restrições) para 15 minutos.  Se criar uma ContentKeyAuthorizationPolicy e especificar a utilização de uma restrição "Token", testá-lo, em seguida e, em seguida, atualize a política para restrição "Abrir", irá demorar cerca de 15 minutos antes da política muda para a versão da política de "Abrir".


##<a name="how-to-configure-the-key-authorization-policy"></a>Como: configurar a política de autorização da chave

Para configurar a política de autorização principais, selecione a página de **PROTEÇÃO de conteúdo** .

Dos serviços de multimédia suporta várias formas de autenticação de utilizadores que fazem os pedidos de chaves. A política de autorização de chave conteúdo pode ter de **Abrir**, **token**ou restrições de autorização de **IP** (**IP** podem ser configurados com resto ou .NET SDK).

###<a name="open-restriction"></a>Restrição aberta

A restrição **Abrir** significa que o sistema irá entregar a chave para qualquer pessoa que torna um pedido de chave. Esta restrição poderá ser útil para fins de teste.

![OpenPolicy][open_policy]

###<a name="token-restriction"></a>Restrição token

Para escolher a política de restrita token, prima o botão **TOKEN** .

A política **token** restringida tem de ser acompanhar por um token emitido por um **Serviço de tokens de seguro** (STS). Dos serviços de multimédia suporta tokens no formato de **Tokens de Web simples** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e formato **JSON Web Token** (JWT). Para obter informações, consulte o artigo [autenticação token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Dos serviços de multimédia não fornece **Serviços Token seguro**. Pode criar um STS personalizado ou tirar partido do Microsoft Azure ACS aos tokens de problema. O STS tem de ser configurado para criar um token sessão iniciado com as especificado chave e problema em afirmações que especificou na configuração do token de restrição. O serviço de entrega chave dos serviços de multimédia irá devolver a chave de encriptação para o cliente se o token for válido e as afirmações no token de correspondem aos configurados para a chave de conteúdo. Para mais informações, consulte o artigo [Utilizar Azure ACS aos tokens de problema](http://mingfeiy.com/acs-with-key-services).

Quando configurar o **TOKEN** restringido a política, tem de definir valores de **chave de verificação**, **emissor** e **audiência**. A chave primária verificação contém a chave que foi assinado o token com, emissor é o serviço de tokens seguro que o token de problemas. A audiência (por vezes denominada âmbito) descreve à intenção do token ou o recurso autorize o token de acesso. O serviço de entrega chave dos serviços de multimédia valida que estes valores no token de acordo com os valores no modelo.

###<a name="playready"></a>PlayReady

Quando a proteger o seu conteúdo com **PlayReady**, uma das coisas que precisa para especificar a política de autorização é uma cadeia XML que define o modelo de licença PlayReady. Por predefinição, a política seguinte é definida:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

Pode clicar no botão **Importar xml de política** e fornecer um XML diferentes que está em conformidade com para o esquema XML definidos [aqui](https://msdn.microsoft.com/library/azure/dn783459.aspx).


##<a name="next-step"></a>Passo seguinte

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

