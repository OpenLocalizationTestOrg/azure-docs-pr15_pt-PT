<properties
    pageTitle="Gestão de Azure API FAQ | Microsoft Azure"
    description="Saiba as respostas a perguntas comuns, padrões e as melhores práticas na gestão de API do Azure."
    services="api-management"
    documentationCenter=""
    authors="miaojiang"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="mijiang"/>

# <a name="azure-api-management-faqs"></a>Perguntas mais frequentes do Azure API gestão

Obter as respostas a perguntas comuns, padrões e as melhores práticas para gestão de API do Azure.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

-   [Como pode posso fazer a equipa do Microsoft Azure API gestão uma pergunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)
-   [O que significa quando uma funcionalidade estiver na pré-visualização?](#what-does-it-mean-when-a-feature-is-in-preview)
-   [Como posso proteger a ligação entre o API o Data Management gateway e os meus serviços de back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
-   [Como copio meu instância do serviço de gestão de API para uma nova instância?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
-   [Posso gerir meu instância de gestão de API através de programação?](#can-i-manage-my-api-management-instance-programmatically)
-   [Como posso adicionar um utilizador ao grupo de administradores?](#how-do-i-add-a-user-to-the-administrators-group)
-   [Porque é que a política que pretendo adicionar disponível no editor de políticas de?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
-   [Como utilizar o controlo de versões API na gestão de API?](#how-do-i-use-api-versioning-in-api-management)
-   [Como configurar ambientes de múltiplos numa única API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
-   [Pode utilizar SOAP com a gestão de API?](#can-i-use-soap-with-api-management)
-   [É a constante de endereço IP de gateway de gestão de API? Posso utilizá-lo em regras de firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-   [Pode configurar um servidor de autorização OAuth 2.0 com segurança do AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-   [Que método de encaminhamento de gestão de API utilizar em implementações para várias localizações geográficas?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
-   [Pode utilizar um modelo de Gestor de recursos do Azure para criar uma instância do serviço de gestão de API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
-   [Pode utilizar um certificado SSL autoassinado para back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
-   [Por que motivo recebo uma falha de autenticação quando tento clonar um repositório GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
-   [Gestão de API funciona com o Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
-   [Posso mover um serviço de gestão de API a partir de uma subscrição para outro?](#can-i-move-an-api-management-service-from-one-subscription-to-another)


### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Como pode posso fazer a equipa do Microsoft Azure API gestão uma pergunta?

Pode contactar-nos utilizando uma das seguintes opções:

-   Publique as suas perguntas no nosso [Fórum MSDN de gestão de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-   Enviar um e-mail para <apimgmt@microsoft.com>.
-   Envie um pedido de funcionalidade no [Fórum de comentários Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>O que significa quando uma funcionalidade estiver na pré-visualização?

Quando uma funcionalidade estiver na pré-visualização, significa que recomendamos estiver ativamente à procura de comentários no modo como a funcionalidade está a trabalhar para si. Uma funcionalidade na pré-visualização é funcional completa, mas é possível que faremos uma quebra de alterar em resposta aos comentários dos clientes. Recomendamos que não dependem uma funcionalidade que está na pré-visualização no seu ambiente de produção. Se tiver quaisquer comentários sobre as funcionalidades de pré-visualização, consulte diga-nos saber através de uma das opções de contactos no [como pode posso fazer a equipa de gestão de API do Microsoft Azure uma pergunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Como posso proteger a ligação entre o API o Data Management gateway e os meus serviços de back-end?

Tem várias opções para proteger a ligação entre o API o Data Management gateway e os seus serviços de back-end. Pode:

-   Utilize a autenticação básica HTTP. Para mais informações, consulte o artigo [Configurar API definições](api-management-howto-create-apis.md#configure-api-settings).
- Utilize a autenticação de comum de SSL conforme descrito no [artigo como seguro serviços de back-end utilizando a autenticação de certificado de cliente na gestão de API do Azure](api-management-howto-mutual-certificates.md).
- Utilize IP whitelisting no seu serviço de back-end. Se tiver uma instância de gestão de API camada padrão ou Premium, o endereço IP do gateway permanece constante. Pode definir a sua lista de branca para permitir que este endereço IP. Pode obter o endereço IP do seu instância de gestão de API no Dashboard de no portal do Azure.
- Ligar a instância de gestão de API a uma rede Virtual Azure. Para mais informações, consulte o artigo [como configurar ligações de VPN na gestão de API do Azure](api-management-howto-setup-vpn.md).

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Como copio meu instância do serviço de gestão de API para uma nova instância?

Tem várias opções se pretender copiar uma instância de gestão de API para uma nova instância. Pode:

-   Utilizar a cópia de segurança e restaurar a função na gestão de API. Para mais informações, consulte o artigo [como implementar recuperação de falhas utilizando serviço cópia de segurança e restaurar na gestão de API do Azure](api-management-howto-disaster-recovery-backup-restore.md).
-   Criar o seu próprio cópia de segurança e restaurar a funcionalidade utilizando a [Gestão de API REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Utilize a API REST para guardar e restaurar as entidades da instância de serviço que pretende.
-   Transfira a configuração do serviço utilizando Git e, em seguida, carregue-o para uma nova instância. Para mais informações, consulte o artigo [como guardar e configurar a configuração do serviço de gestão de API utilizando Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Posso gerir meu instância de gestão de API através de programação?

Sim, pode gerir API gestão através de programação, utilizando:

-   A [Gestão de API REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
-   A [biblioteca de gestão do Microsoft Azure ApiManagement Service SDK](http://aka.ms/apimsdk).
-   Os cmdlets de [implementação do serviço](https://msdn.microsoft.com/library/mt619282.aspx) e [Gestão de serviços de](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Como posso adicionar um utilizador ao grupo de administradores?

Eis como pode adicionar um utilizador ao grupo de administradores:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Vá para o grupo de recursos que tem a instância de gestão de API que pretende atualizar.
3. Em gestão de API, atribua a função **Contribuinte de gestão de Api** ao utilizador.

Agora o recentemente adicionado contribuinte pode utilizar [cmdlets](https://msdn.microsoft.com/library/mt613507.aspx)do Azure PowerShell. Eis como iniciar sessão como administrador:

1. Utilizar o `Login-AzureRmAccount` cmdlet para iniciar sessão.
2. Definir o contexto para a subscrição que tenha o serviço utilizando `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Obter um URL de início de sessão único utilizando `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Utilize o URL para aceder ao portal de administração.


### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Porque é que a política que pretendo adicionar disponível no editor de políticas de?

Se a política ao qual pretende adicionar aparece esbatido ou sombreadas no editor de política, certifique-se que se encontra no âmbito correto para a política. Cada declaração da política destina-se para utilizar nas secções de política e âmbitos específicos. Para rever as secções de política e âmbitos para uma política, consulte a secção a utilização da política [políticas de gestão de API](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### <a name="how-do-i-use-api-versioning-in-api-management"></a>Como utilizar o controlo de versões API na gestão de API?

Tem algumas opções para utilizar o controlo de versões API na gestão de API:

-   Em gestão de API, pode configurar APIs para representar diferentes versões. Por exemplo, poderá ter dois APIs diferentes, MyAPIv1 e MyAPIv2. Um programador pode escolher a versão que quer utilizar o programador.
-   Também pode configurar o seu API com um URL do serviço que não inclui um segmento de versão, por exemplo, https://my.api. Em seguida, configure um segmento de versão no modelo de [URL reescrita de](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) cada operação. Por exemplo, pode ter uma operação com um [modelo de URL](api-management-howto-add-operations.md#url-template) denominado /resource e um modelo de [URL reescrever](api-management-howto-add-operations.md#rewrite-url-template) chamado /v1/Resource. Pode alterar o valor de segmento versão separadamente para cada operação.
-   Se pretender manter um segmento de versão "predefinido" no URL do serviço a API, no operações selecionadas, defina uma política que utiliza a política de [configurar o serviço de back-end](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) para alterar o caminho de pedido de back-end.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Como configurar ambientes de múltiplos numa única API?

Para configurar vários ambientes, por exemplo, num ambiente de teste e num ambiente de produção numa única API, tem duas opções. Pode:

-   Anfitrião APIs diferentes ao mesmo inquilino.
-   API do mesmo no diferentes inquilinos do anfitrião.

### <a name="can-i-use-soap-with-api-management"></a>Pode utilizar SOAP com a gestão de API?

[Pass-through SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) suporte está agora disponível. Os administradores podem importar WSDL dos seus serviços SOAP e gestão de API do Azure irá criar um front-end SOAP. Documentação portal para programadores, consola de teste, políticas e análise está ambas disponível para os serviços SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>É a constante de endereço IP de gateway de gestão de API? Posso utilizá-lo em regras de firewall?

Em camadas Standard e Premium, o endereço IP público (VIP) do inquilino API gestão é estático para o tempo de vida do inquilino, com algumas exceções. As alterações de endereço IP nas seguintes circunstâncias:

-   O serviço é eliminado e, em seguida, recriado.
-   A subscrição do serviço está suspensa (por exemplo, para nonpayment) e, em seguida, restabelecida.
-   Adicionar ou remover Azure rede Virtual (que pode utilizar rede Virtual apenas na camada Premium).

Para implementações da região com várias, o endereço regional for alterado se a região for tiverem vagado devida e, em seguida, restabelecida (pode utilizar com várias região implementação apenas na camada Premium).

Inquilinos do Premium camada que estão configurados para implementação da região com várias são atribuídos a um endereço IP público por região.

Pode obter o seu endereço IP (ou endereços, numa implementação com várias região) na página de inquilino no portal do Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Pode configurar um servidor de autorização OAuth 2.0 com segurança do AD FS?

Para saber como configurar um servidor de autorização de OAuth 2.0 com a segurança de serviços de Federação do Active Directory (AD FS), consulte o artigo [Utilizar o ADFS na gestão de API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Que método de encaminhamento de gestão de API utilizar em implementações para várias localizações geográficas?

Gestão de API utiliza o [método de encaminhamento de tráfego de desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method) em implementações para várias localizações geográficas. O tráfego de entrada é encaminhado para o gateway API mais próximo. Se uma região ficar offline, o tráfego de entrada é automaticamente encaminhado para o próximo gateway mais próximo. Saiba mais sobre os métodos de encaminhamento no [Gestor de tráfego de encaminhamento de métodos](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Pode utilizar um modelo de Gestor de recursos do Azure para criar uma instância do serviço de gestão de API?

Sim. Ver os modelos de guia de introdução do [Serviço de gestão de API do Azure](http://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Pode utilizar um certificado SSL autoassinado para back-end?

Sim. Eis como utilizar um certificado autoassinado do Secure Sockets Layer (SSL) para um back-end:

1. Crie uma entidade de [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) utilizando a gestão de API.
2. Defina a propriedade **skipCertificateChainValidation** **Verdadeiro**.
3. Se já não quiser permitir que os certificados autoassinados, eliminar a entidade de back-end ou defina a propriedade **skipCertificateChainValidation** para **Falso**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Por que motivo recebo uma falha de autenticação quando tento clonar um repositório de Git?

Se utilizar o Gestor de credenciais Git ou se estiver a tentar clonar um repositório de Git utilizando o Visual Studio, poderão ocorrer um problema conhecido com a caixa de diálogo de credenciais do Windows. Caixa de diálogo de limites de comprimento de palavra-passe para 127 caracteres e trunca-lo a palavra-passe gerados pelo Microsoft. Estamos a trabalhar no encurtar a palavra-passe. Por agora, utilize para clonar do repositório de Git Git festa.

### <a name="does-api-management-work-with-azure-expressroute"></a>Gestão de API funciona com o Azure ExpressRoute?

Sim. Gestão de API funciona com Azure ExpressRoute.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Posso mover um serviço de gestão de API a partir de uma subscrição para outro?

Sim. Para saber mais, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../resource-group-move-resources.md).
