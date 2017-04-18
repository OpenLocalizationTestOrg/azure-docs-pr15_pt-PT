<properties 
    pageTitle="Descrição geral do conteúdo de proteger | Microsoft Azure" 
    description="Este artigo dar uma descrição geral da proteção de conteúdo com dos serviços de multimédia." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="juliako"/>

#<a name="protecting-content-overview"></a>Proteger a descrição geral do conteúdo


Serviços de multimédia do Microsoft Azure permite-lhe proteger os ficheiros de multimédia da hora deixa o seu computador através do armazenamento, transformação e entrega de. Dos serviços de multimédia permite-lhe distribuir o conteúdo direto e a pedido encriptado dinamicamente com encriptação AES (Advanced Standard) (utilizando as chaves de encriptação de 128-bit) ou qualquer um das DRMs principais: Microsoft PlayReady, Google Widevine e FairPlay da Apple. Dos serviços de multimédia também fornece um serviço para fornecer chaves AES e DRM licenças (PlayReady, Widevine e FairPlay) para clientes autorizados. 

A seguinte imagem demonstra protecção de conteúdos dos fluxos de trabalho que suporte AMS. 

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Para poder utilizar a encriptação dinâmica, terá de obter, pelo menos, uma unidade reservada transmissão no ponto final transmissão a partir do qual pretende transmitir em fluxo conteúdo encriptado.

Este tópico explica [conceitos e terminologia](media-services-content-protection-overview.md) relevantes para Noções sobre a proteção de conteúdo com AMS. O tópico também contém [ligações](media-services-content-protection-overview.md#common-scenarios) para tópicos que mostram como realizar tarefas de proteção de conteúdo. 

##<a name="dynamic-encryption"></a>Encriptação dinâmica

Serviços de multimédia do Microsoft Azure permite-lhe fornecer o seu conteúdo encriptado dinamicamente com AES limpar chave ou encriptação DRM: Microsoft PlayReady, Google Widevine e FairPlay da Apple.

Atualmente, pode encriptar os seguintes formatos de transmissão: HLS, MPEG TRAÇO e transmissão suaves. Não é possível encriptar HDS transmissão formato ou progressive transferências.

Se pretender para serviços de multimédia encriptar um ativo, tem de associar uma chave de encriptação (CommonEncryption ou EnvelopeEncryption) ao seu ativo e também de configurar políticas de autorização para a chave.

Também precisa de configurar a política de entrega a elementos. Se pretender transmitir em fluxo um activo encriptada do armazenamento, certifique-se especificar como pretende entregá-lo ao configurar a política de entrega de elementos.

Quando uma sequência é pedida por um leitor de, dos serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o seu conteúdo utilizando AES limpar chave ou encriptação DRM. Para desencriptar a sequência, o leitor irá pedir a chave de serviço de entrega chave. Para decidir se ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou na chave.

>[AZURE.NOTE]Para tirar partido de encriptação dinâmico, terá de obter, pelo menos, uma unidade de transmissão da pedido para o ponto final de transmissão a partir do qual planeia entrega seu conteúdo encriptado. Para mais informações, consulte o artigo [como escala dos serviços de multimédia](media-services-portal-manage-streaming-endpoints.md).

##<a name="storage-encryption"></a>Encriptação de armazenamento

Utilize a encriptação de armazenamento para encriptar o seu conteúdo limpar localmente utilizar encriptação AES 256 a bits e, em seguida, carregue-o para o armazenamento do Windows Azure onde foi armazenado encriptados em repouso. Elementos protegidos com encriptação de armazenamento são automaticamente não encriptados colocados num sistema de ficheiros encriptados antes de codificação e, opcionalmente, encriptar novamente antes de carregar novamente como um novo activo de saída. No caso de utilização principal para encriptação de armazenamento é quando que pretende proteger os seus ficheiros de suporte de entrada de alta qualidade com encriptação forte em repouso no disco.

Para poder entregar um activo encriptada do armazenamento, tem de configurar políticas de entrega de elementos para que dos serviços de multimédia saibam como pretende distribuir o conteúdo. Antes do recurso pode ser transmitido em sequência, o servidor de transmissão remove a encriptação de armazenamento e transmite em fluxo conteúdo utilizando a política de entrega especificada (por exemplo, AES, comuns ou a encriptação nenhum).

## <a name="common-encryption-cenc"></a>Encriptação comuns (CENC)

É utilizada a encriptação comuns ao encriptar o seu conteúdo com PlayReady ou / e Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Utilizar a encriptação cbcs aapl

Cbcs aapl é utilizado quando encriptar o seu conteúdo com FairPlay.

## <a name="envelope-encryption"></a>Encriptação de envelope 

Utilize esta opção se quiser proteger o seu conteúdo com a chave de limpar AES de 128. Se pretender que uma opção mais segura, escolha uma das DRMs listados neste tópico. 

##<a name="licenses-and-keys-delivery-service"></a>Serviço de entrega de licenças e chaves

Dos serviços de multimédia fornece um serviço para fornecer licenças DRM (PlayReady, Widevine, FairPlay) e AES desmarque Teclas para clientes autorizados. Pode utilizar [o portal do Azure](media-services-portal-protect-content.md), REST API ou SDK de serviços de multimédia para .NET para configurar as políticas de autorização e autenticação para o seu licenças e chaves.

##<a name="token-restriction"></a>Restrição token

A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir ou token restrição. A política de restrita token tem de ser acompanhar por um token emitido por uma seguro Token serviço (STS). Dos serviços de multimédia suporta tokens no formato de Tokens de Web simples (SWT) e o formato de JSON Web Token (JWT). Dos serviços de multimédia não fornece serviços Token seguro. Pode criar um STS personalizado ou tirar partido do Microsoft Azure ACS aos tokens de problema. O STS tem de ser configurado para criar um token sessão iniciado com as especificado chave e problema em afirmações que especificou na configuração do token de restrição. O serviço de entrega chave dos serviços de multimédia irá devolver chave pedida (ou licença) para o cliente se o token é válido e as afirmações na token correspondem àqueles configurado para a chave (ou licença).

Quando configurar o token restringido a política, tem de especificar a chave primária de verificação, emissor e os parâmetros de audiência. A chave primária verificação contém a chave que foi assinado o token com, emissor é o serviço de tokens seguro que o token de problemas. A audiência (por vezes denominada âmbito) descreve à intenção do token ou o recurso autorize o token de acesso. O serviço de entrega chave dos serviços de multimédia valida que estes valores no token de acordo com os valores no modelo.

##<a name="streaming-urls"></a>URLs transmissão

Se o recurso foi encriptado com mais do que um DRM, deve utilizar uma tag de encriptação no URL transmissão: (formato = 'm3u8 aapl' encriptação = 'xxx').

As seguintes considerações aplicam-se:

- Pode ser especificado apenas zero ou um tipo de encriptação.
- Tipo de encriptação não tem de ser especificado no url se apenas uma encriptação foi aplicada aos elemento.
- Tipo de encriptação é maiúsculas e minúsculas.
- Podem ser especificados os seguintes tipos de encriptação:  
    - **cenc**: comuns encriptação (Playready ou Widevine)
    - **cbcs aapl**: Fairplay
    - **CBC**: encriptação AES de envelope.

##<a name="common-scenarios"></a>Cenários comuns

Os tópicos seguintes demonstram como proteger conteúdo no armazenamento, entregar dinamicamente encriptada transmissão de multimédia, utilizar o serviço de entrega de chave/licença AMS

- [Proteger com AES](media-services-protect-with-aes128.md) 
- [Proteger com PlayReady e/ou Widevine](media-services-protect-with-drm.md)
- [Transmitir em fluxo seu protegido de conteúdo HLS com Apple FairPlay e/ou PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Cenários adicionais

- [Como integrar o serviço de Azure PlayReady licença com o seu próprio server EncDec/transmissão](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [Utilizar castLabs a licenças DRM para dos serviços de multimédia do Azure](media-services-castlabs-integration.md)
 
##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>Ligações relacionadas

[Anunciar PlayReady como um serviço e encriptação AES de dinâmica com os serviços de multimédia do Azure](http://mingfeiy.com/playready)

[Azure preços de entrega de multimédia serviços PlayReady licença explicadas](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Como depurar para a sequência encriptada AES nos serviços de multimédia do Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT token authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integrar o Azure multimédia serviços OWIN MVC com base aplicação com o Azure Active Directory e restringir o conteúdo entrega chave com base em afirmações JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Utilizar Azure ACS aos tokens de problema](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
