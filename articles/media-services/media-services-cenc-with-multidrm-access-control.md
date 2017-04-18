<properties 
    pageTitle="CENC com o controlo de acesso e Multi DRM: uma estrutura de referência e implementação no Azure e Azure dos serviços de multimédia | Microsoft Azure" 
    description="Saiba mais sobre como para o licenciamento da Microsoft® suaves transmissão cliente migrar Kit." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>

#<a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC com o controlo de acesso e Multi DRM: uma estrutura de referência e implementação no Azure e Azure dos serviços de multimédia

##<a name="key-words"></a>Palavras-chave
 
Azure Active Directory, dos serviços de multimédia do Azure, o Azure Media Player, dinâmicos encriptação, licença entrega, PlayReady, Widevine, FairPlay, Encryption(CENC) comuns, Multi DRM, Axinom, travessão, EME, MSE, JSON Web Token (JWT), em afirmações, moderna Browsers, sobreposição de chave, chave simétrica, chave assimétricos, OpenID ligar, X509 certificado. 

##<a name="in-this-article"></a>Neste artigo

Os tópicos seguintes são abrangidos neste artigo:

- [Introdução](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [Descrição geral deste artigo](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Uma estrutura de referência](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Estrutura de mapeamento para tecnologia de apoio para implementação](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Pós-implementação](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [Procedimentos de implementação](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [Algumas contras em execução](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Tópicos adicionais para implementação](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP ou HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [Azure Active Directory iniciar chave passagem com o rato](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [Onde está o Token de acesso?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [E quanto Live transmissão?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [E acerca dos servidores de licença fora dos serviços de multimédia do Azure?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [E se quiser utilizar um STS personalizado?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [O sistema concluídas e teste](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [Início de sessão do utilizador](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [Utilizar extensões de multimédia encriptada para PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [Utilizar o EME para Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [Não tem o direito de utilizadores](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [Executar seguro Token serviço personalizado](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Resumo](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>Introdução

É conhecidos que é uma tarefa complexa para estruturar e construir um subsystem DRM para um OTT ou transmissão solução online. E é uma prática comum para fornecedores de vídeo operadores online utilizar esta peça para fornecedores de serviços DRM especializados. O objetivo deste documento é apresentar um design de referência e a execução do subsystem DRM de ponto a ponto no OTT ou solução de transmissão online.

Os leitores alvo deste documento são engenheiros a trabalhar no subsystem DRM de OTT ou transmissão/multi-screen soluções online ou qualquer leitores interessados em DRM subsystem. Partem do princípio de é que os leitores estiver familiarizados com pelo menos um das tecnologias DRM no mercado, tal como PlayReady, Widevine, FairPlay ou Adobe acesso.

Por DRM, recomendamos também incluir CENC (encriptação comuns) multi DRM. Uma tendência principal no transmissão online e no setor OTT é utilizar CENC com multi-native-DRM vários plataformas de cliente, que é uma mudança da tendência anterior da utilização de uma única DRM e o seu cliente SDK para várias plataformas de cliente. Quando utilizar CENC com multi native DRM, PlayReady e Widevine são encriptados pela especificação de [Encriptação comum (CENC ISO/CEI 23001-7)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Os benefícios da CENC com multi DRM são os seguintes:

1. Reduz o custo de uma vez que uma transformação de encriptação única é utilizada a filtragem de diferentes plataformas com o seus DRMs nativos; a encriptação
1. Reduz o custo de gestão de activos encriptados uma vez que é necessária uma única cópia de activos encriptadas;
1. Elimina cliente DRM custo de licenciamento, uma vez que o native client DRM é normalmente gratuito na sua plataforma nativa.

Microsoft foi uma factor ativo de TRAÇO e CENC juntamente com alguns leitores de indústria principais. Dos serviços de multimédia do Microsoft Azure tenha sido fornecer suporte de TRAÇO e CENC. Para anúncios recentes, consulte o artigo blogues do Mingfei: [Serviços de entrega de licença de anunciar o Google Widevine nos serviços de multimédia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)e [dos serviços de multimédia Azure adiciona embalagem do Google Widevine para fornecer multi DRM sequência](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Descrição geral deste artigo

O objetivo deste artigo inclui o seguinte:

1. Fornece uma estrutura de referência de subsystem DRM utilizar CENC com multi-DRM;
1. Fornece uma implementação de referência no plataforma dos serviços de multimédia do Microsoft Azure/Azure;
1. Aborda alguns tópicos de estrutura e implementação.

No artigo, "multi-DRM" aborda os seguintes tópicos:

1. Microsoft PlayReady
1. Google Widevine
1. Apple FairPlay (ainda não é suportada dos serviços de multimédia do Azure)

A tabela seguinte resume a aplicação nativa plataforma/nativo e browsers suportados por cada DRM.

**Plataforma do cliente**|**Suporte de DRM nativo**|**Aplicação/browser**|**Formatos de transmissão**
----|------|----|----
**TVs inteligentes, o operador STBs, OTT STBs**|PlayReady principalmente, e/ou Widevine e/ou outros|Linux, Opera, WebKit, outros|Vários formatos
**Dispositivos Windows 10 (Windows PC, Windows Tablets, Windows Phone, Xbox)**|PlayReady|MS limite/IE11/EME<br/><br/><br/>UWP|TRAÇO (para HLS, PlayReady não é suportado)<br/><br/>TRAÇO suaves em sequência, (para HLS, PlayReady não é suportado) 
**Dispositivos Android (telemóvel, Tablet, TV)**|Widevine|Chrome/EME|TRAÇO
**iOS (iPhone, iPad), de clientes dos X e de TV da Apple**|FairPlay|Safari 8 + / EME|HLS
**Plug-in: Quando for necessária Adobe**|Quando for necessária Access|Plug-in de browser|HDS, HLS

Tendo em conta o estado atual de implementação para cada DRM, um serviço, normalmente, vai querer implementar DRMs 2 ou 3 para se certificar de que todos os tipos de pontos finais de endereços na melhor forma.

Existe um compromisso entre a complexidade da lógica do serviço e complexidade do lado do cliente para alcançar um determinado nível da experiência do utilizador nos vários clientes.

Para tornar a sua seleção, tenha em atenção estes factos:

- PlayReady vierem é implementada em cada dispositivo Windows, alguns dispositivos Android e disponível através do software SDK praticamente qualquer plataforma
- Widevine vierem é implementada no todos os dispositivos Android, no Chrome e algumas noutros dispositivos
- FairPlay está disponível apenas no iOS e clientes do sistema operativo Mac ou através do iTunes.

Para que um multi-DRM típica seria:

- Opção 1: PlayReady e Widevine
- Opção 2: PlayReady, Widevine e FairPlay


## <a name="a-reference-design"></a>Uma estrutura de referência

Nesta secção, podemos irá apresentar uma estrutura de referência que é desconhecida para tecnologias utilizadas para implementá-lo.

Um subsystem DRM pode conter os seguintes componentes:

1. Gestão de chaves
1. Embalagem de DRM
1. Entrega de licença DRM
1. Verificação de direito
1. Autenticação/autorização
1. Player
1. Origem/CDN

O diagrama seguinte ilustra a interação entre os componentes num subsystem DRM alto nível.

![Subsystem DRM com CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Existem três básicas "camadas" na estrutura:

1. Camada de back-office (no preto), que não são expostos externamente;
1. Camadas de "DMZ" (azul) que contém todos os pontos finais opostas pública;
1. Camada da Internet de pública (azul claro) que contém CDN e leitores de tráfego de Internet público.
 
Deverá haver uma ferramenta de gestão de conteúdo para controlar protecção DRM, independentemente é estática ou dinâmica encriptação. As entradas para encriptação DRM devem incluir:

1. Conteúdos vídeo MBR;
1. Conteúdo chave;
1. URLs de aquisição licença.

Durante o tempo de reprodução, o fluxo de alto nível é:

1. Utilizador é autenticado;
1. É criada token de autorização para o utilizador;
1. Conteúdo protegido por DRM (manifest) é transferido para o leitor;
1. Leitor submete o pedido de aquisição de licença para os servidores de licença juntamente com o ID de chave e autorização token.

Antes de passar para o tópico seguinte, algumas palavras sobre a estrutura de gestão de chave.

**ContentKey – activo**|**Cenário**
------|---------------------------
1-para-1|As maiúsculas/minúsculas mais simples. Fornece o controlo pormenorizado. Mas isto geralmente resulta no custo de entrega de licença mais alto. No mínima licença de um pedido de é necessário para cada imobilizado protegido.
1-para-muitos|Pode utilizar a mesma chave conteúda para vários elementos. Por exemplo, para os ativos num grupo lógicos como um género ou um subconjunto de género (ou filme Gene), poderia utilize uma única chave conteúda.
Muitos-para-1|Múltiplas teclas de conteúdo são necessários para cada item. <br/><br/>Por exemplo, se necessitar de aplicar protecção CENC dinâmica com multi-DRM para MPEG-TRAÇO e encriptação AES de 128 dinâmica para HLS, tem duas separadas conteúdas chaves, cada uma com as suas próprias ContentKeyType. (Para a chave de conteúdos utilizada para protecção CENC dinâmica, ContentKeyType.CommonEncryption deve ser utilizado, enquanto para a chave de conteúdo dinâmico da encriptação AES de 128, deverá ser utilizado ContentKeyType.EnvelopeEncryption.)<br/><br/>Outro exemplo, em proteção CENC TRAÇO de conteúdo de, em teoria, uma chave de conteúdo pode ser utilizado para proteger a sequência de vídeo e outra tecla conteúda para proteger a sequência de áudio. 
Muitos-para - muitos|Combinação dos dois cenários acima: um conjunto de teclas de conteúdo são utilizadas para cada um dos vários activos no mesmo activo "grupo".


Outro factor importante a ter em conta é a utilização de licenças que não sejam persistentes e persistentes.

Por que razão são importantes as seguintes considerações? 

Possuem direto impacto e custo de entrega de licença se utilizar pública nuvem para entrega de licença. Vamos considere dois estrutura diferente casos seguintes para ilustrar:



1. Subscrição mensal: utilizar licença persistente e chave para elementos mapeamento de conteúdo-1-para-muitos. Por exemplo para todos os filmes infantil, utilizamos uma única conteúda chave para encriptação. Neste caso: 

    Total de licenças de # solicitadas para todos os seus filhos filmes/dispositivo = 1

1. Subscrição mensal: utilizar licença não persistentes e mapeamento de 1 para 1 entre a chave de conteúdo e ativos. Neste caso:

    Total de licenças de # solicitadas para todos os seus filhos filmes/dispositivo = [# filmes observadas] x [sessões #]

Como pode ver facilmente, os desenhos diferentes duas resultam em padrões de pedido de licença muito diferente, por isso, se o serviço de entrega de licença for fornecido por uma nuvem pública como dos serviços de multimédia do Azure de custo de entrega de licença.

## <a name="mapping-design-to-technology-for-implementation"></a>Estrutura de mapeamento para tecnologia de apoio para implementação

Em seguida, podemos mapear os nossos estrutura genérica para tecnologias numa plataforma dos serviços de multimédia do Microsoft Azure/Azure, especificando a tecnologia a utilizar para cada bloco modular.

A tabela seguinte mostra o mapeamento:

**Blocos Modulares**|**Tecnologia de apoio**
------|-------
**Player**|[Reprodução de multimédia do Microsoft Azure](https://azure.microsoft.com/services/media-services/media-player/)
**Fornecedor de identidades (IDP)**|Azure Active Directory
**Serviço de tokens seguro (STS)**|Azure Active Directory
**Fluxo de trabalho de proteção de DRM**|Serviços de multimédia do Azure protecção dinâmica
**DRM licença entrega**|1. serviços de multimédia do azure licença entrega (PlayReady, Widevine, FairPlay), ou <br/>2. servidor de licenças de Axinom, <br/>3. servidor de licenças PlayReady personalizado
**Origem**|Ponto final de transmissão de serviços de multimédia do Azure
**Gestão de chaves**|Não é necessária para implementação de referência
**Gestão de conteúdo**|Uma aplicação de consola do c#

Por outras palavras, fornecedor de identidade (IDP) e seguro Token serviço (STS) serão Azure AD. Para player, utilizamos [API do Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Serviços de multimédia do Azure e Azure Media Player suportam travessão e CENC com multi DRM.

O diagrama seguinte mostra a estrutura e fluxo com o mapeamento de tecnologia acima geral.

![CENC no AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar a encriptação CENC dinâmica, a ferramenta de gestão de conteúdo irá utilizar as seguintes entradas:

1. Abrir conteúdo;
1. Chave de conteúdo da chave geração/gestão;
1. URLs de aquisição licença;
1. Uma lista de informações a partir do Azure AD.

O resultado da ferramenta de gestão de conteúdo será:

1. ContentKeyAuthorizationPolicy que contém a especificação de no como entrega licença verifica se um token de JWT e especificações de licença DRM;
1. AssetDeliveryPolicy que contém especificações em sequência de formato, protecção DRM e URLs de aquisição licença.

Durante a runtime, o fluxo é como abaixo:

1. Relativamente a autenticação de utilizador, é gerado um token JWT;
1. Uma das afirmações contidas no JWT token é afirmação "grupos de" que contém o ID de objeto do grupo de "EntitledUserGroup". Esta afirmação será utilizada para transmitir "verificação direito".
1. Leitor transferências cliente manifesto de um CENC conteúdo protegido e "vê" o seguinte:
    1. ID, chave 
    1. o conteúdo é CENC protegido,
    1. URLs de aquisição licença.

1. Leitor torna um pedido de aquisição licença com base no browser/DRM suportado. No pedido de aquisição de licença, ID de chave e o token JWT também será apresentado. Serviço de entrega de licença verificará o token JWT e as afirmações contidas antes da licença conforme seja necessária.

##<a name="implementation"></a>Pós-implementação

###<a name="implementation-procedures"></a>Procedimentos de implementação

A aplicação irá incluir os seguintes passos:

1. Preparar contrário teste: codificar/pacote um vídeo de teste para multi velocidade fragmentado MP4 nos serviços de multimédia do Azure. Este elementos não está protegido por DRM. Proteção de DRM vai ser efetuada ao protecção dinâmica mais tarde.
1. Criar chave ID e conteúdos chave (opcionalmente a partir de propagação chave). Para o nosso objetivo, sistema de gestão de chave não é necessária uma vez que recomendamos está a lidar com apenas um conjunto único de ID e chave de conteúdo para algumas teste activos; chave
1. Utilize AMS API para configurar os serviços de entrega multi DRM licença para o teste ativo. Se estiver a utilizar os servidores de licença personalizado pela sua empresa ou fornecedores da sua empresa em vez de serviços de licenças nos serviços de multimédia do Azure, pode ignorar este passo e especificar licença aquisição URLs no passo para configurar a entrega de licença. AMS API é necessário para especificar a que algumas detalhadas configurações, tais como restrição de política de autorização, modelos de resposta de licença para diferentes serviços de licença DRM, etc. Neste momento, o portal do Azure não ainda fornece a IU conforme seja necessária para esta configuração. Pode encontrar informações de nível de API e código no documento de Ana Kornich de exemplo: [utilizar PlayReady e/ou Widevine encriptação comuns dinâmica](media-services-protect-with-drm.md). 
1. Utilize AMS API para configurar a política de entrega de elementos para o teste ativo. Pode encontrar informações de nível de API e código no documento de Ana Kornich de exemplo: [utilizar PlayReady e/ou Widevine encriptação comuns dinâmica](media-services-protect-with-drm.md).
1. Criar e configurar um inquilino do Azure Active Directory no Azure;
1. Criar algumas contas de utilizador e grupos no seu inquilino do Azure Active Directory: deve criar, pelo menos, "EntitledUser" Agrupar e adicionar um utilizador para este grupo. Utilizadores neste grupo passará verificação direito no aquisição de licenças e não os utilizadores neste grupo irão falhar transmitir a verificação de autenticação e não conseguir adquirir qualquer licença. Ser um membro deste grupo de "EntitledUser" é uma afirmação necessários "grupos de" no token de JWT emitido por Azure AD. Este requisito afirmação deve ser especificado na configuração passo de serviços de entrega de licença multi DRM.
1. Crie uma aplicação do ASP.NET MVC qual vai ser alojamento seu leitor de vídeo. Esta aplicação ASP.NET será protegida com autenticação de utilizador contra o inquilino do Azure Active Directory. Inicial maiúscula em afirmações serão incluídas no tokens de acesso obtidos após a autenticação de utilizador. API de ligar OpenID é recomendado para este passo. Tem de instalar os pacotes de NuGet seguintes:
    - Microsoft.Azure.ActiveDirectory.GraphClient do pacote de instalação
    - Microsoft.Owin.Security.OpenIdConnect do pacote de instalação
    - Microsoft.Owin.Security.Cookies do pacote de instalação
    - Microsoft.Owin.Host.SystemWeb do pacote de instalação
    - Microsoft.IdentityModel.Clients.ActiveDirectory do pacote de instalação
1. Crie um leitor de utilizar a [API do Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). [Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) permite-lhe especificar a tecnologia DRM a utilizar no plataforma DRM diferente.
1. Matriz de teste:

**DRM**|**Browser**|**Resultado de utilizador tem direito**|**Resultado de utilizador não tem direito**
---|---|-----|---------
**PlayReady**|MS limite ou IE11 no Windows 10|Teve êxito|Falhar
**Widevine**|Chrome no Windows 10|Teve êxito|Falhar
**FairPlay** |TBD||

Jorge Trifonov da equipa de serviços de multimédia do Azure tiver escrito um blogue que fornece os passos detalhados em configurar o Azure Active Directory para uma aplicação do leitor ASP.NET MVC: [integrar o Azure multimédia serviços OWIN MVC com base aplicação com o Azure Active Directory e restringir o conteúdo entrega chave com base em afirmações JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Jorge também tem escrito um blogue sobre a [autenticação token de JWT dos serviços de multimédia do Azure e encriptação dinâmicos](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). E Eis dele [exemplo sobre a integração do Azure AD com entrega chave dos serviços de multimédia do Azure](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Para obter informações sobre o Azure Active Directory:

- Pode encontrar informações de programador do [Azure Active Directory para programadores do](../active-directory/active-directory-developers-guide.md)guia.
- Pode encontrar informações de administrador no [Administrar o Azure AD diretório](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Algumas contras em execução

Existem algumas "dilemas" na aplicação. Começadas a seguinte lista de "dilemas" pode ajudar a resolução de problemas no caso de se depare com problemas.

1. **Emissor** URL deve terminar com **"/"**.  

    **Audiência** deve ser o ID do leitor aplicação cliente e deve adicionar também **"/"** no final do URL emissor.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    No [JWT descodificador](http://jwt.calebb.net/), deverá ver **sociedade mista** e **iss** , como de abaixo no token de JWT:
    
    ![1. º gotcha](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Adicione permissões para a aplicação no AAD (no separador Configurar da aplicação). Isto é necessário para cada aplicação (versões locais e implementadas).

    ![gotcha 2.](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Utilize o emissor direita em configurar o protecção CENC dinâmica:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    As seguintes não funcionarão:
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    GUID é o ID do inquilino AAD. Pode encontrar o GUID no pop-up de pontos finais no Azure portal.

4. Membros do grupo de conceder afirmações privilégios. Certifique-se AAD manifesto num ficheiro de aplicação, temos o seguinte

    "groupMembershipClaims": "Todos" (o valor predefinido é nulo)

5. Quando criar os requisitos de restrição a definição TokenType inicial maiúscula.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Desde a adição de suporte da JWT (AAD) para além de SWT ACS (), a predefinição TokenType é TokenType.JWT. Se utilizar SWT/ACS, tem de definir TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Tópicos adicionais para implementação
Seguinte podemos irá disco uss alguns tópicos adicionais no nosso estrutura e a aplicação.

###<a name="http-or-https"></a>HTTP ou HTTPS?

A aplicação de leitor de ASP.NET MVC criámos têm de suportar o seguinte procedimento:

1. Autenticação do utilizador através do Azure AD que tem de estar em HTTPS;
1. Exchange token de JWT entre o cliente e do Azure AD que tem de estar em HTTPS;
1. Aquisição de licença DRM pelo cliente que é necessário para estar em HTTPS se entrega licença é fornecida dos serviços de multimédia do Azure. Obviamente, conjunto de aplicações do produto PlayReady não define a HTTPS para entrega de licença. Se o servidor de licenças PlayReady estiver fora dos serviços de multimédia do Azure, podem ser utilizadas HTTP ou HTTPS.

Por conseguinte, a aplicação leitor do ASP.NET irá utilizar HTTPS como prática recomendada. Isto significa que o leitor de multimédia do Azure será uma página em HTTPS. No entanto, para transmissão podemos preferir HTTP, por conseguinte, precisamos de considerar o problema de conteúdo misto.

1. Browser não permite conteúdo misto. Mas permitem o plug-ins, como o Silverlight e OSMF Plug-in para suaves e de TRAÇO. Conteúdo misto é uma questão de segurança - é devido a ameaça da capacidade de inserção JS maliciosos que podem causar os dados do cliente de estar risco.  Browsers bloquear este por predefinição e até ao momento a única forma para contornar esta situação é do lado do servidor (origem), para permitir que todos os domínios (https independentemente ou http). Este provavelmente não é uma boa ideia quer.
1. Vamos deve evitar conteúdo misto: utilizam ambas HTTP ou ambos utilizam HTTPS. Quando a reproduzir conteúdo misto, silverlightSS tech requer limpar um aviso de conteúdo misto. flashSS tech trata conteúdo misto sem qualquer aviso conteúdo misto.
1. Se o seu ponto final de transmissão foi criado antes de Agosto de 2014, não suporta HTTPS. Neste caso, consulte Criar e utilizar um novo ponto final transmissão para HTTPS.

Na implementação referência, para conteúdo protegido por DRM, aplicação e transmissão estarão em HTTTPS. Para abrir índice, o leitor não precisar de autenticação ou licença, pelo que tem a liberdade para utilizar HTTP ou HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory iniciar chave passagem com o rato

Este é um ponto importante a ter em consideração da sua implementação. Se não considerar esta na sua implementação, o sistema concluídas eventualmente deixarão de funcionar completamente dentro pelo mais de 6 semanas.

Azure AD utiliza norma da indústria para estabelecer fidedignidade entre si próprio e aplicações utilizando o Azure AD. Especificamente, o Azure AD utiliza uma chave de assinatura é constituído por um par de chaves público e privado. Quando o Azure AD cria um token de segurança que contém informações sobre o utilizador, este token está assinado pelo Azure AD utilizando a sua chave privada antes de ter sido enviada para a aplicação. Para verificar o token é válida e realmente originadas a partir do Azure AD, a aplicação tem de validar assinatura do token utilizando a chave pública exposta pela Azure AD está contida num documento de metadados de Federação do inquilino. Esta chave pública – e a chave de assinatura a partir do qual deriva – são a mesma outra utilizada para todos os inquilinos no Azure AD.

Informações detalhadas sobre sobreposição de chave do Azure AD podem ser encontradas no documento: [Informações importantes sobre assinatura sobreposição de tecla no Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Entre a [par de chaves privadas público](https://login.windows.net/common/discovery/keys/), 

- A chave privada é utilizada pelo Azure Active Directory para gerar um token JWT;
- A chave pública é utilizada por uma aplicação como o serviços de entrega de licença DRM no AMS para confirmar o token JWT;
 
Para o efeito de segurança, o Azure Active Directory roda este certificado periodicamente (todas as semanas 6). Em caso de violações de segurança, a sobreposição de chave pode ocorrer a qualquer altura. Por isso, os serviços de entrega de licença no AMS necessita de atualizar a chave pública utilizada como Azure AD roda o par de chaves, caso contrário, irá falhar autenticação de token AMS e não será emitida nenhuma licença. 

Pode fazê-lo a definição de TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument quando configurar os serviços de entrega de licença DRM.

O fluxo de token de JWT é como abaixo:

1.  Azure AD irá emitir o token JWT com a chave privada atual para um utilizador autenticado;
2.  Quando um leitor de vê um CENC com conteúdo protegido por multi DRM, pela primeira vez-lo irá localizar o token JWT emitido por Azure AD.
3.  O leitor envia o pedido de aquisição de licença com o token de JWT para serviços de entrega de licença no AMS;
4.  Os serviços de entrega de licença no AMS irão utilizar a chave pública atual válido, a partir do Azure AD para verificar o token de JWT, antes de licenças.

Serviços de entrega de licença DRM serão sempre estar a verificar e para a chave pública atual válido, a partir do Azure AD. A chave pública apresentada por Azure AD estará a chave utilizada para verificar um token JWT emitido por Azure AD.

O que acontece se a chave sobreposição acontece após AAD gera um token de JWT mas antes do JWT token é enviada por jogadores para serviços de entrega de licença do DRM no AMS para verificação? 

Uma vez que pode ser apresentada uma chave em qualquer momento, há sempre mais do que uma chave pública válida disponíveis no documento de metadados Federação. Entrega de licença do Azure dos serviços de multimédia pode utilizar qualquer uma das chaves especificadas no documento, uma vez que uma tecla pode ser apresentada mais cedo, outro pode ser sua substituição e assim sucessivamente.

### <a name="where-is-the-access-token"></a>Onde está o Token de acesso?

Caso veja como uma aplicação web liga uma aplicação de API em [Identidade da aplicação com OAuth 2.0 cliente credenciais conceder](active-directory-authentication-scenarios.md#web-application-to-web-api), o fluxo de autenticação é como abaixo:

1.  Um utilizador tiver sessão iniciado Azure AD na aplicação web (consulte o artigo o [Browser para a aplicação Web](active-directory-authentication-scenarios.md#web-browser-to-web-application).
2.  O ponto final de autorização de Azure AD redireciona agente do utilizador voltar para a aplicação de cliente com um código de autorização. Agente do utilizador devolve o código de autorização para redirecionar a aplicação de cliente URI.
3.  A aplicação web tem de adquirir um token de acesso para que possa autenticar para a web API e obter o recurso pretendido. Torna um pedido para o ponto final de token Azure AD, fornecer a credencial, ID de cliente e de aplicação web do API ID URI. Apresenta o código de autorização para revelar-se de que o utilizador deu.
4.  Azure AD autentica a aplicação e devolve um token de acesso JWT que é utilizado para ligar a web API.
5.  Por HTTPS, a aplicação web utiliza o token de acesso JWT devolvido para adicionar a cadeia JWT com uma designação "Ligação" no cabeçalho do pedido de autorização para a web API. Em seguida, web API valida o token JWT e se validação for concluído com êxito, devolve o recurso pretendido.

Este fluxo de "identidade da aplicação" web API fidedignidades de que o utilizador autenticado, a aplicação web. Por este motivo, este padrão chama um subsystem fidedigna. O [diagrama nesta página](http://msdn.microsoft.com/library/azure/dn645542.aspx/) descreve como código de autorização conceder fluxo funciona.

Na aquisição licença com restrição token, podemos está a seguir o mesmo padrão subsystem fidedigna. E o serviço de entrega de licença dos serviços de multimédia do Azure é o recurso de API, o "recurso de back-end" necessita de uma aplicação web para aceder a web. Por isso, onde está o token de acesso?

Na verdade, podemos estão a obter token de acesso a partir do Azure AD. Após a autenticação de utilizador com êxito, é devolvido o código de autorização. O código de autorização é, em seguida, utilizado juntamente com a chave de ID e a aplicação de cliente, para token de acesso do exchange. E o token de acesso é para aceder a uma aplicação de "ponteiro" apontar ou que representa o serviço de entrega de licença dos serviços de multimédia do Azure.

Precisamos registar e configurar a aplicação "ponteiro" no Azure AD ao seguir os passos abaixo:

1.  No inquilino Azure AD

    - Adicione uma aplicação (recurso) com o URL de início de sessão: 

    https://[resource_name].azurewebsites.NET/ e 

    - ID de URL da aplicação: 
    
    https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.  Adicionar uma nova chave para a aplicação de recurso;
3.  Atualizar o ficheiro de manifesto de aplicação para que a propriedade groupMembershipClaims tem o seguinte valor: "groupMembershipClaims": "Todos"  
4.  Na aplicação do Azure AD apontar para a aplicação web do Media player, na secção "permissões para outras aplicações", adicione a aplicação de recursos que foi adicionada no passo 1 acima. Em "permissões de delegado" selecione "Access [resource_name]" marca de verificação. Isto dá a permissão de aplicação web para criar tokens de acesso para aceder a aplicação de recursos. Deve fazer isto para local e implementada versão da aplicação web se estiver a desenvolver com o Visual Studio e Azure web app.
    
Por conseguinte, o token JWT emitido por Azure AD é de facto token de acesso para aceder a este recurso "ponteiro".

### <a name="what-about-live-streaming"></a>E quanto Live transmissão?

Acima, no nosso debate tem sido centrando-se em elementos a pedido. E quanto live transmissão?

As boas notícias são que pode utilizar exatamente a mesma estrutura e implementação para proteger a transmissão direto nos serviços de multimédia do Azure, por treating elementos associados com um programa, como "activo VOD".

Especificamente, é conhecido que para fazer direto transmissão em serviços de multimédia do Azure, tem de criar um canal, em seguida, um programa no canal. Para criar o programa, é necessário criar um ativo que irá conter o arquivo direto para o programa. Para fornecer CENC com proteção multi DRM do conteúdo direto, tudo o que precisa de fazer, é aplicar o mesma configuração/processamento ao activo como se fosse "elementos VOD" antes de iniciar o programa.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>E acerca dos servidores de licença fora dos serviços de multimédia do Azure?

Muitas vezes, poderão ter investido clientes licença farm de servidores nos seus próprios dados centrar ou alojado pelos fornecedores de serviços DRM. Felizmente, proteção do Azure multimédia dos serviços de conteúdo permite-lhe funcionar no modo de híbrido: conteúdos alojado e dinamicamente protegidos dos serviços de multimédia do Azure, enquanto licenças DRM são entregues por servidores fora dos serviços de multimédia do Azure. Neste caso, existem as seguintes considerações de alterações:

1. O serviço de tokens seguro tem de emitir tokens que são aceitável e que podem ser verificadas por farm de servidores de licença. Por exemplo, os servidores de licenças Widevine fornecidos pela Axinom requer um token JWT específico que contém "direito mensagem". Por isso, tem de ter um STS emitir o token essas JWT. Os autores tem concluído essa uma implementação e pode encontrar os detalhes do documento seguinte no [Centro de documentação do Azure](https://azure.microsoft.com/documentation/): [Utilizar Axinom para a prestação Widevine licenças dos serviços de multimédia do Azure](media-services-axinom-integration.md). 
1. Já não tem de configurar o serviço de entrega de licença (ContentKeyAuthorizationPolicy) dos serviços de multimédia do Azure. O que precisa de fazer consiste em fornecer a licença URLs de aquisição (para PlayReady, Widevine e FairPlay) quando configura AssetDeliveryPolicy em configurar o CENC com multi DRM.
 
### <a name="what-if-i-want-to-use-a-custom-sts"></a>E se quiser utilizar um STS personalizado?

Podem existir algumas razões que um cliente poderá optar por utilizar um STS personalizado (seguro serviço de tokens) para fornecer JWT tokens. Alguns dos mesmos são:

1.  Fornecedor de ponto a identidade (IDP) utilizado pelo cliente não suporta STS. Neste caso, uma STS personalizado podem ser uma opção.
2.  O cliente poderá ter um controlo numa integrar STS com subscritor do cliente faturação sistema mais flexível ou maior. Por exemplo, pode oferecer um operador MVPD vários pacotes de subscritor OTT como premium, básica, desportos, etc. O operador poderá querer corresponder ao afirmações num token com o pacote de um subscritor, para que apenas o conteúdo no pacote direita é disponibilizado. Neste caso, uma STS personalizado fornece a flexibilidade conforme seja necessária e o controlo.

Duas alterações tem de ser efetuadas ao utilizar um STS personalizado:

1.  Quando configurar o serviço de entrega de licença de um ativo, tem de especificar a chave de segurança utilizada para verificação pelo STS personalizado (mais detalhes abaixo) em vez de à chave atual a partir do Azure Active Directory.
2.  Quando é gerado um token JTW, uma chave de segurança é especificada em vez da chave privada do X509 atual certificado no Azure Active Directory.

Existem dois tipos de chaves de segurança:

1.  Chave simétrica: a mesma chave é utilizada para gerar e verificar um token JWT;
2.  Chave assimétrico: um par de chaves públicas privadas num X509 certificado é utilizado com a chave privada para encriptar/gerar um token JWT e a chave pública para verificar o token.

####<a name="tech-note"></a>Nota técnicos

Se utiliza o .NET Framework / c# como a plataforma de desenvolvimento, o X509 certificado utilizado para a chave de segurança assimétricos tem de ter, pelo menos, 2048 o comprimento da chave. Este é um requisito a classe System.IdentityModel.Tokens.X509AsymmetricSecurityKey no .NET Framework de. Caso contrário, vai ser iniciada a seguinte exceção:

IDX10630: Não podem ser mais pequeno do que bits '2048' 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para iniciar sessão. 

## <a name="the-completed-system-and-test"></a>O sistema concluídas e teste

Vamos irá guiá-lo através de alguns cenários no sistema de ponto a ponto concluído para que os leitores podem ter uma basic "imagem" do comportamento antes de obter uma conta de início de sessão.

A aplicação web do leitor e o início de sessão podem ser encontradas [aqui](https://openidconnectweb.azurewebsites.net/).

Se o que precisa de "não integradas" cenário: vídeos activos alojados no Azure dos serviços de multimédia que não estão quer protegidas ou protegido por DRM mas sem autenticação token (emissão uma licença para quem pedi-lo), pode testar sem início de sessão (ao mudar para HTTP se a transmissão de vídeo através de HTTP).

Se o que precisa de cenário integrado do ponto a ponto: elementos de vídeos está sob dinâmica DRM protecção nos serviços de multimédia do Azure, com autenticação de token e token JWT a ser gerados pelo Azure AD, tenha de iniciar sessão.

### <a name="user-login"></a>Início de sessão do utilizador

Para testar o sistema DRM integrado do fim para fim, tem de ter uma "conta" criado ou adicionada. 

Que conta?

Apesar do Azure originalmente permitido acesso apenas por utilizadores da conta Microsoft, permite agora acesso por utilizadores a partir de ambos os sistemas. Isto foi feito por ter todas as fidedignidade de propriedades Azure Azure AD para autenticação, tendo Azure AD autenticar utilizadores organizacionais e por criar uma relação de federação onde Azure AD fidedignidades e o sistema de identidade do consumidor de conta Microsoft para autenticar os utilizadores do consumidor. Como resultado, Azure AD é possível autenticar contas do Microsoft "convidado", bem como contas do Azure AD "nativas".

Uma vez que o Azure AD confia no domínio de conta Microsoft (MSA), pode adicionar qualquer contas a partir de qualquer um dos seguintes domínios para o Azure AD personalizado inquilino e utilizam a conta de início de sessão:

**Nome de domínio**|**Domínio**
-----------|----------
**Domínio de inquilino personalizada Azure AD**|somename.onmicrosoft.com
**Domínio empresarial**|Microsoft.com
**Domínio de conta Microsoft (MSA)**|Outlook.com, live.com, hotmail.com

Pode contactar qualquer um dos autores tenho uma conta criado ou adicionada por si. 

Seguem-se as capturas de ecrã de início de sessão diferentes páginas utilizadas por contas de domínio diferente.

**Azure personalizado conta de domínio do inquilino AD**: neste caso, irá ver a página de início de sessão personalizada da personalizada domínio de inquilino do Azure AD.

![Conta de inquilino do domínio personalizado Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Conta de domínio do Microsoft com smart card**: neste caso irá ver a página de início de sessão personalizada pela Microsoft empresarial IT com autenticação de dois fatores.

![Conta de inquilino do domínio personalizado Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Conta Microsoft (MSA)**: neste caso, irá ver a página de início de sessão da Account Microsoft para os consumidores.

![Conta de inquilino do domínio personalizado Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Utilizar extensões de multimédia encriptada para PlayReady

Num browser com as extensões de multimédia encriptados (EME) moderno para obter suporte PlayReady, tais como IE 11 no Windows 8.1 e e para cima e browser Microsoft Edge no Windows 10, PlayReady estará a DRM subjacente para EME.

![Utilizar o EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A área de leitor escuro é que a protecção PlayReady impede que um façam captura de ecrã do vídeo protegida. 

O ecrã seguinte apresenta o plug-ins do leitor e o suporte MSE/EME.

![Utilizar o EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME no Microsoft Edge e IE 11 no Windows 10 permite invocar de [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) em dispositivos Windows 10 que suporta-lo. PlayReady SL3000 desbloqueia o fluxo de conteúdo premium avançado (4K, INVT, etc.) e os modelos de entrega de conteúdos novo (janela antecipada para conteúdo avançado).

Concentre-se nos dispositivos Windows: PlayReady é a DRM apenas na HW disponível em dispositivos Windows (PlayReady SL3000). Um serviço de transmissão pode utilizar PlayReady através de EME ou através de uma aplicação de UWP e oferecem uma qualidade superior vídeo utilizando PlayReady SL3000 que DRM outra. Normalmente, 2K conteúdo será fluxo através do Chrome ou o Firefox e o conteúdo de 4K através do Microsoft Edge/IE11 ou uma aplicação de UWP no mesmo dispositivo (dependendo das definições do serviço e pós-implementação).


#### <a name="using-eme-for-widevine"></a>Utilizar o EME para Widevine

Num browser moderno com o suporte EME/Widevine, tal como Chrome 41 + no Windows 10, Windows 8.1, Mac os x Yosemite e Chrome num Android 4.4.4, Google Widevine é DRM atrás EME.

![Utilizar o EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Repare que Widevine não impeça a um façam captura de ecrã do vídeo protegida.

![Utilizar o EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Não tem o direito de utilizadores

Se um utilizador não é um membro do grupo "O direito de utilizadores", o utilizador não conseguir passar "direito verificação" e o serviço de licenças multi DRM irá recusar emitir a licença pedida, conforme apresentado abaixo. A descrição detalhada é "adquirir licenças falhou", que é como planeado.

![Utilizadores não elegíveis](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Executar seguro Token serviço personalizado

Para o cenário básicos para realizar personalizado seguro Token serviço (STS), o token JWT será emitido pelo STS personalizado utilizando chave simétrica ou assimétrica. 

Maiúsculas/minúsculas com chave simétrica (utilizando o Chrome):

![Executar STS personalizados](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Para maiúsculas/minúsculas com chave assimétrico através de um X509 de certificados (utilizando o browser moderna Microsoft).

![Executar STS personalizados](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Em ambos os casos acima, a autenticação de utilizador permanecerá a mesma – através do Azure AD. A única diferença é que os JWT tokens são emitidos por o STS personalizado em vez do Azure AD. Obviamente, quando configurar protecção CENC dinâmica, a restrição de serviço de entrega de licença Especifica o tipo de token de JWT, chave simétrica ou assimétrica.

## <a name="summary"></a>Resumo

Neste documento, debatido CENC com multi native DRM e controlo de acesso através de autenticação de token: sua estrutura e da sua aplicação através do Azure, dos serviços de multimédia do Azure e Azure Media Player.

- Uma estrutura de referência é apresentada que contém todos os componentes necessários no subsystem DRM/CENC;
- Implementação de referência no Azure, dos serviços de multimédia do Azure e Azure Media Player.
- Alguns tópicos diretamente envolvidos na estrutura e implementação são também debatidos.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Confirmações 

Guilherme Zhang, Mingfei Yan, Roland Le franco, Kilroy Hughes, Paula Kornich
