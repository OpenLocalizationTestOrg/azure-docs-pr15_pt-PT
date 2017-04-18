<properties
   pageTitle="Gráfico do Azure Active Directory API | Microsoft Azure"
   description="Um guia de descrição geral e guia de introdução para que lhe permite o acesso de programação ao Azure AD através de pontos finais de REST API API Graph."
   services="active-directory"
   documentationCenter=""
   authors="PatAltimore"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-graph-api"></a>Gráfico do Azure Active Directory API

> [AZURE.IMPORTANT] Funcionalidade do Azure AD Graph API também está disponível através de do [Microsoft Graph](https://graph.microsoft.io/), um API unificada, que inclui APIs de outros serviços da Microsoft como o Outlook, OneDrive, o OneNote, Planeador e grafo do Office, acessível através de um ponto final de único e com um token de acesso único.

Azure Active Directory Graph API fornece acesso de programação para Azure AD através de pontos finais de REST API. Aplicações podem utilizar a API do gráfico para efetuar criar, ler, atualizar e eliminar (CRUD) operações no diretório de dados e objetos. Por exemplo, a API Graph suporta as seguintes operações comuns de um objeto de utilizador:

- Criar um novo utilizador num diretório

- Obter as propriedades de detalhada de um utilizador, tais como os respetivos grupos

- Atualizar propriedades de um utilizador, tais como a sua localização e o número de telefone, ou alterar a respetiva palavra-passe

- Verificar a associação a grupos de um utilizador de acesso baseado em funções

- Desativar uma conta de utilizador ou eliminá-lo completamente

Para além de objetos de utilizador, pode executar operações semelhantes em outros objetos como grupos e aplicações. Para ligar para a API do gráfico de um diretório, a aplicação deverá estar registado junto com Azure AD e estar configurada para permitir o acesso ao diretório. Isso é normalmente obtido através de um fluxo de consentimento do utilizador ou administrador.

Para começar a utilizar a API Azure Active Directory Graph, consulte o [Guia de guia de introdução de API do gráfico](active-directory-graph-api-quickstart.md)ou veja a [documentação de referência interactiva API do gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## <a name="features"></a>Funcionalidades

Graph API fornece as seguintes funcionalidades:

- **Pontos finais de API REST**: A API do gráfico é um serviço RESTful composto por pontos finais que são acedidos através de pedidos de HTTP padrão. Graph API suporta XML ou Javascript objeto notação (JSON) tipos de conteúdo para pedidos e respostas. Para mais informações, consulte o artigo [referência da API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Autenticação com Azure AD**: todos os pedidos para a API Graph têm de ser autenticados por um JSON Web Token (JWT) no cabeçalho do pedido de autorização de acrescentar. Este token é adquirido ao efetuar um pedido para o ponto final token do Azure AD e fornecer credenciais válidas. Pode utilizar o fluxo de credenciais de cliente OAuth 2.0 ou o código de autorização conceder fluxo adquirir um token para ligar para o gráfico. Para mais informações, [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Baseado em funções autorização RBCA ()**: os grupos de segurança são utilizados para executar RBAC na API do gráfico. Por exemplo, se pretender determinar se um utilizador tem acesso a um recurso específico, a aplicação pode ligar a operação de [Verificar a associação a grupos (verbos)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , que devolve true ou false.

- **Consulta diferença**: Se pretender verificar a existência de alterações num diretório entre dois períodos de tempo sem ter de tornar as consultas frequentes à API do gráfico, pode fazer um pedido de diferença de consulta. Este tipo de pedido irá devolver apenas as alterações efetuadas entre o pedido de consulta diferença anterior e o pedido atual. Para mais informações, consulte o artigo [Azure AD Graph API diferença consulta](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Extensões Directory**: Se estiver a desenvolver uma aplicação que necessita de leitura ou escrita exclusivas propriedades para objetos de diretório, pode registar e utilizar valores extensão ao utilizar a API do gráfico. Por exemplo, se a sua aplicação requer uma propriedade de ID do Skype para cada utilizador, pode registar a nova propriedade no diretório e estará disponível em cada objeto do utilizador. Para mais informações, consulte o artigo [extensões de esquema do Azure AD Graph API diretório](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

- **Protegido por âmbitos de permissão**: AAD Graph API expõe âmbitos de permissão que ativar o acesso seguro/manifestado acordo aos dados AAD e suportam uma variedade de tipos de aplicação de cliente, incluindo:
 - aqueles com uma interface de utilizador que são dadas delegado acesso aos dados através de autorização do utilizador tiver sessão iniciada (delegado)
  - as que utilizam aplicação-definem controlo de acesso baseado em funções como clientes de serviço/daemon (funções de aplicação)

    Delegada tanto âmbitos de permissão de função aplicação representam um privilégio exposto pela API do gráfico e podem ser pedidos por aplicações de cliente através de da aplicação registo permissões [funcionalidades no portal do Azure clássica](https://manage.windowsazure.com). Os clientes podem verificar os âmbitos de permissão concedido às mesmas através da inspeção da afirmação âmbito ("scp") recebida no token de acesso delegado permissões e funções ("funções") afirmação para as permissões da função app. Saiba mais sobre [âmbitos de permissão de API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).


## <a name="scenarios"></a>Cenários

Graph API permite muitos cenários de aplicação. Os cenários seguintes são mais comuns:

- **Aplicação de linha de negócio (única inquilino)**: neste cenário, um programador enterprise funciona para uma organização que tenha uma subscrição do Office 365. O programador está a criar uma aplicação web que interage com o Azure AD para executar tarefas como atribuir uma licença a um utilizador. Esta tarefa necessita de acesso à Graph API, para que os registos de programador único inquilinos aplicação no Azure AD e configura ler em escrever permissões para a API do gráfico. Em seguida, a aplicação está configurada para utilizar as suas próprias credenciais ou as do utilizador atualmente iniciar sessão para adquirir um token para chamar a API do gráfico.

- **Software como uma aplicação de serviço (com várias inquilino)**: neste cenário, um fornecedor de software independente (ISV) está a desenvolver aplicação web de inquilino com várias alojado que disponibiliza funcionalidades de gestão de utilizador para outras organizações que utilizem o Azure AD. Estas funcionalidades necessitam de acesso a objetos de diretório e por isso, a aplicação chamar a API do gráfico. O programador regista a aplicação no Azure AD, configura-o para exigir ler e escrever as permissões para a API do gráfico e, em seguida, ativa o acesso externo para que outras organizações podem consentimento para utilizar a aplicação no seu diretório. Quando um utilizador no outra organização autentica a aplicação pela primeira vez, que sejam apresentados uma caixa de diálogo de acordo com as permissões que está a pedir a aplicação.  Conceder consentimento atribuirá, em seguida, a aplicação aqueles pedida permissões à API Graph no diretório do utilizador. Para mais informações sobre a arquitetura de consentimento, consulte o artigo [Descrição geral do quadro consentimento](active-directory-integrating-applications.md).

## <a name="see-also"></a>Consulte também

[Guia de guia de introdução do Azure AD Graph API](active-directory-graph-api-quickstart.md)

[Documentação do resto do gráfico de AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guia do Azure Active Directory para programadores](active-directory-developers-guide.md)
