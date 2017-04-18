<properties 
    pageTitle="Configurar políticas de proteção de conteúdo através do portal Azure | Microsoft Azure" 
    description="Este artigo demonstra como utilizar o portal do Azure para configurar as políticas de proteção de conteúdo. O artigo também mostra como ativar encriptação dinâmica para os seus ativos." 
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
    ms.date="10/24/2016"    
    ms.author="juliako"/>

# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configurar políticas de proteção de conteúdo através do portal Azure

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="overview"></a>Descrição geral

Serviços de multimédia do Azure da Microsoft (MGA) permite-lhe proteger os ficheiros de multimédia da hora deixa o seu computador através do armazenamento, transformação e entrega de. Dos serviços de multimédia permite-lhe entregar o conteúdo encriptados dinamicamente com encriptação AES (Advanced Standard) (utilizando as chaves de encriptação de 128-bit), comuns encriptação (CENC) utilizando PlayReady e/ou Widevine DRM e FairPlay da Apple. 

AMS fornece um serviço para fornecer licenças DRM e AES desmarque Teclas para clientes autorizados. Portal do Azure permite-lhe criar uma **política de autorização de chave/licença** para todos os tipos de encriptação.

Este artigo demonstra como configurar políticas de proteção de conteúdo com o portal do Azure. O artigo também mostra como aplique dinâmica encriptação aos seus ativos.

> [AZURE.NOTE]  Se tiver utilizado o portal clássico Azure para criar políticas de proteção, as políticas que foram podem não aparecer no [portal do Azure](https://portal.azure.com/). No entanto, todas as políticas de antigo ainda existem. Pode examiná-los com o Azure Media serviços .NET SDK ou a ferramenta de [Azure-multimédia serviços Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (para ver as políticas, o botão direito do rato no elemento -> apresentar informações (F4) -> clique no separador conteúdo chaves -> clique sobre a chave). 
> 
> Se pretender encriptar o seu activo utilizando as políticas de novas, configurá-los com o portal do Azure, clique em Guardar e aplicar a encriptação dinâmica. 

## <a name="start-configuring-content-protection"></a>Iniciar a configuração do protecção de conteúdos

Para utilizar o portal para iniciar a configuração do protecção de conteúdos, global à sua conta AMS, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/), selecione a sua conta dos serviços de multimédia do Azure.
2. Selecione **Definições** > **protecção de conteúdos**.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## <a name="keylicense-authorization-policy"></a>Política de autorização de chave/licença

AMS suporta várias formas de autenticação de utilizadores que efectuam a chave de pedidos de licença. A política de autorização de chave conteúdo deve ser configurada por si e correspondida por seu cliente na ordem chave/licença para delived para o cliente. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: **Abrir** ou **token** restrição.

Portal do Azure permite-lhe criar uma **política de autorização de chave/licença** para todos os tipos de encriptação.

###<a name="open"></a>Abrir 

Abrir restrição significa que o sistema irá entregar a tecla a qualquer pessoa que torna um pedido de chave. Esta restrição poderá ser útil para fins de teste. 

### <a name="token"></a>Token

A política de restrita token tem de ser acompanhar por um token emitido por uma seguro Token serviço (STS). Dos serviços de multimédia suporta tokens no formato de Tokens de Web simples (SWT) e o formato de JSON Web Token (JWT). Dos serviços de multimédia não fornece serviços Token seguro. Pode criar um STS personalizado ou tirar partido do Microsoft Azure ACS aos tokens de problema. O STS tem de ser configurado para criar um token sessão iniciado com as especificado chave e problema em afirmações que especificou na configuração do token de restrição. O serviço de entrega chave dos serviços de multimédia irá devolver chave pedida (ou licença) para o cliente se o token é válido e as afirmações na token correspondem àqueles configurado para a chave (ou licença).

Quando configurar o token restringido a política, tem de especificar a chave primária de verificação, emissor e parâmetros de audiência. A chave primária verificação contém a chave que foi assinado o token com, emissor é o serviço de tokens seguro que o token de problemas. A audiência (por vezes denominada âmbito) descreve à intenção do token ou o recurso autorize o token de acesso. O serviço de entrega chave dos serviços de multimédia valida que estes valores no token de acordo com os valores no modelo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>Modelo de direitos de PlayReady

Para obter informações detalhadas sobre o modelo de direitos de PlayReady, consulte o artigo [Multimédia serviços PlayReady licença descrição geral dos modelos](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Não persistente

Se configurar licença como não persistente, é mantida na memória apenas quando o leitor está a utilizar a licença.  

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente

Se configurar a licença como persistente, este será guardado no armazenamento persistente no cliente.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Modelo de direitos de Widevine

Para obter informações detalhadas sobre o modelo de direitos de Widevine, consulte o artigo [Descrição geral dos modelos Widevine licença](media-services-widevine-license-template-overview.md).

### <a name="basic"></a>Básicas

Quando seleciona **básicas**, o modelo será criado com todos os valores de predefinições.

### <a name="advanced"></a>Avançadas

Para obter explicação detalhada sobre a opção de avançar das configurações de Widevine, consulte o artigo [neste](media-services-widevine-license-template-overview.md) tópico.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuração de FairPlay

Para ativar FairPlay encriptação, tem de fornecer o certificado de aplicação e a chave de palavra-passe de aplicação (ASK) através da opção de configuração de FairPlay. Para obter informações detalhadas sobre a configuração de FairPlay e requisitos, consulte [Este](media-services-protect-hls-with-fairplay.md) artigo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplique dinâmica encriptação aos seus ativos

Para tirar partido de encriptação dinâmico, terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro de origem para o conjunto de ficheiros de MP4 ajustável velocidade.
- Obter, pelo menos, uma unidade de transmissão da pedido para o ponto final de transmissão a partir do qual pretende distribuir o conteúdo. Para mais informações, consulte o artigo [como dimensionar a pedido transmissão reservadas unidades](media-services-portal-manage-streaming-endpoints.md).

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Selecione um recurso que pretende encriptar

Para ver todos os seus ativos, selecione **Definições** > **elementos**.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Encriptar com AES ou DRM

Depois de premir **encriptar** num ativo, são apresentadas com duas opções: **AES** ou **DRM**. 

#### <a name="aes"></a>AES

Limpar a encriptação de chave irão estar ativada em todos os protocolos de transmissão de AES: transmissão suaves, HLS e MPEG-TRAÇO.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM

Quando selecionar o separador DRM, são apresentadas com opções diferentes de políticas de proteção de conteúdo de (o que tem de ter configurado por agora) + um conjunto de transmissão protocolos.

- **PlayReady e Widevine com travessão MPEG** - dinamicamente irá encriptar a sua sequência de TRAÇO MPEG com PlayReady e Widevine DRMs.
- **PlayReady e Widevine com MPEG-TRAÇO + FairPlay com HLS** - irá dinamicamente encriptar lhe da cadeia de TRAÇO MPEG com PlayReady e Widevine DRMs. Também irá encriptar o seu sequências HLS com FairPlay.
- **PlayReady apenas com transmissão suaves, HLS e TRAÇO MPEG** - irá dinamicamente encriptar Streaming suaves, HLS, sequências de TRAÇO MPEG com PlayReady DRM.
- **Widevine apenas com travessão MPEG** - dinamicamente irá encriptar lhe MPEG-TRAÇO com Widevine DRM.
- **FairPlay apenas com HLS** - dinamicamente irá encriptar o seu fluxo HLS com FairPlay.

Para ativar FairPlay encriptação, tem de fornecer o certificado de aplicação e a chave de palavra-passe de aplicação (ASK) através da opção de configuração de FairPlay de pá de definições de proteção de conteúdo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Depois de efetuar a seleção de encriptação, prima **Aplicar**.

##<a name="next-steps"></a>Próximos passos

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





