<properties
   pageTitle="Guia de introdução para o grafo do Azure AD API | Microsoft Aure"
   description="Azure Active Directory Graph API fornece acesso de programação para Azure AD através de pontos finais de OData REST API. Aplicações podem utilizar a API do gráfico para efetuar criar, ler, atualizar e eliminar (CRUD) operações no diretório de dados e objetos."
   services="active-directory"
   documentationCenter="n/a"
   authors="PatAltimore"
   manager="mbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="patricka"/>

# <a name="quickstart-for-the-azure-ad-graph-api"></a>Guia de introdução para o grafo do Azure AD API

A API do Azure Active Directory (AD) Graph oferece um acesso de programação às Azure AD através de pontos finais de OData REST API. Aplicações podem utilizar a API do gráfico para efetuar criar, ler, atualizar e eliminar (CRUD) operações no diretório de dados e objetos. Por exemplo, pode utilizar a API do gráfico para criar um novo utilizador, ver ou atualizar as propriedades do utilizador, alterar a palavra-passe do utilizador, verifique a associação a grupos de acesso baseado em funções, desativar ou eliminar o utilizador. Para mais informações sobre as funcionalidades de gráfico API e cenários de aplicações, consulte o artigo [API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e a [Pré-requisitos API do Azure AD Graph](https://msdn.microsoft.com/library/hh974476.aspx). 

> [AZURE.IMPORTANT] Funcionalidade do Azure AD Graph API também está disponível através de do [Microsoft Graph](https://graph.microsoft.io/), um API unificada, que inclui APIs de outros serviços da Microsoft como o Outlook, OneDrive, o OneNote, Planeador e grafo do Office, acessível através de um ponto final de único e com um token de acesso único.

## <a name="how-to-construct-a-graph-api-url"></a>Como construir um URL de API do gráfico

No gráfico API, para poder aceder a dados de diretório e objetos (por outras palavras, recursos ou entidades) contra a qual pretende efectuar operações CRUD, pode utilizar URLs com base no protocolo de dados abertas (OData). Os URLs utilizados no gráfico API de consistir em quatro partes principais: raiz, identificador de inquilino, caminho do recurso e opções de cadeia de consulta de serviço: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Tomar o exemplo que o seguinte URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **Raiz do serviço**: Azure AD Graph API, a raiz de serviço está sempre https://graph.windows.net.
- **Identificador do inquilino**: este pode ser um nome de domínio (registado) verificado, no exemplo acima, contoso.com. Pode também ser um ID de objeto do inquilino ou "myorganiztion" ou "eu" alias. Para obter mais informações, consulte o artigo [endereçar entidades e operações na API do gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
- **Caminho do recurso**: Esta secção de um URL identifica o recurso a ser seus serviços contactou com (utilizadores, grupos, um utilizador específico, ou um grupo específico, etc.) No exemplo acima, é os nível superiores "grupos de" para o endereço recurso definido. Também pode endereçar uma entidade específica, por exemplo "os utilizadores / {ID de objecto}" ou "utilizadores/userPrincipalName".
- **Parâmetros de consulta**:? separa a secção de caminho de recursos a partir da secção de parâmetros de consulta. O parâmetro de consulta "api versão" é necessário em todos os pedidos de na API do gráfico. A API do gráfico também suporta as seguintes opções de consulta de OData: **$filter**, **$orderby**, **expanda $**, **$top**e **$format**. As seguintes opções de consulta não são atualmente suportadas: **$count**, **$inlinecount**e **$skip**. Para mais informações, consulte o artigo [consultas suportadas, filtros e opções de paginação na API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Graph API versões

Especifique a versão para um pedido de Graph API no parâmetro de consulta "api versão". Para obter a versão 1.5 e posterior, utilizar um valor numérico versão; versão da API = 1.6. Para versões anteriores, é utilizar uma cadeia de data que adira para o formato AAAA-MM-DD; Por exemplo, de api versão = 2013-11-08. Para funcionalidades da pré-visualização, utilize a cadeia "beta"; Por exemplo, de api versão = beta. Para mais informações acerca das diferenças entre as versões API do gráfico, consulte o artigo [o controlo de versões do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadados Graph API

Para devolver o ficheiro de metadados Graph API, adicione o segmento "$metadata" após o identificador de inquilino de URL por exemplo, o seguinte URL devolve metadados para a empresa de demonstração utilizada pelo Explorador de gráfico: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Pode introduzir este URL na barra de endereço de um browser para ver os metadados. O documento de metadados CSDL devolvido descreve as entidades e tipos complexos, respectivas propriedades e funções e ações expostas pela versão do Graph API pedida. Omissão do parâmetro da versão da api irá devolver metadados para a versão mais recente.

## <a name="common-queries"></a>Consultas comuns

[Consultas comuns do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) lista consultas comuns que podem ser utilizadas com o gráfico de AD Azure, incluindo consultas que podem ser utilizadas para aceder a recursos de nível superiores no seu diretório e consultas para efetuar operações no seu diretório.

Por exemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` devolve informações para contoso.com diretório da empresa.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` lista todos os objetos de utilizador no diretório contoso.com.

## <a name="using-the-graph-explorer"></a>Utilizando o Explorador do gráfico

Pode utilizar o Explorador de Graph para a API do Azure AD Graph para consultar os dados do directório à medida que cria a sua aplicação.

> [AZURE.IMPORTANT] O Explorador de gráfico não suporta a escrever ou eliminar os dados de um diretório. Só pode executar operações de leitura no seu diretório do Azure AD com o Explorador de gráfico.

Segue-se o resultado seria ver se fosse navegar para o Explorador do gráfico, selecione a empresa de demonstração de utilização e introduza `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` para apresentar todos os utilizadores no diretório demonstração:

![Explorador api do Azure AD graph](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Carregar o Explorador de gráfico**: para a ferramenta de carregar, navegue para [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Clique em **Utilizar empresa de demonstração** para executar o Explorador de Graph contra dados a partir de um inquilino de exemplo. Não necessita de credenciais para utilizar a empresa de demonstração. Em alternativa, pode clique em **Iniciar sessão** e inicie sessão com as credenciais da conta Azure AD para executar o Explorador de Graph contra seu inquilino. Se executar Graph Explorer contra a sua própria inquilino, utilizador ou o administrador terá consentimento durante a iniciar sessão. Se tiver uma subscrição do Office 365, tem automaticamente um inquilino do Azure AD. As credenciais que utiliza para iniciar sessão Office 365 são, na verdade, contas do Azure AD, e pode utilizar estas credenciais com o Explorador de gráfico.

**Executar uma consulta**: para executar uma consulta, escreva a sua consulta na caixa de texto de pedido e clique em **obter** ou clique em **introduzir** chave. Os resultados são apresentados na caixa resposta. Por exemplo, `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` irá listar todos os objetos de grupo no diretório demonstração.

Tenha em atenção as seguintes funcionalidades e limitações do Explorador de gráfico:
- Capacidade de conclusão automática no conjuntos de recursos. Para ver isto, clique em **Utilizar demonstração empresa** e, em seguida, clique na caixa de texto de pedido (onde aparece o URL da empresa). Pode selecionar um recurso definir na lista pendente.

- Suporta "eu" e "myorganization" endereçamento aliases. Por exemplo, pode utilizar `https://graph.windows.net/me?api-version=1.6` para devolver o objeto de utilizador do utilizador tiver sessão iniciada no ou `https://graph.windows.net/myorganization/users?api-version=1.6` para devolver todos os utilizadores no diretório atual. Note que utilizar "eu" alias devolve um erro para a empresa de demonstração porque não existe nenhum utilizador tiver sessão iniciada no fazer o pedido.

- Uma secção de cabeçalhos de resposta. Isto pode ser utilizado para ajudar a resolver problemas que ocorrem quando executar consultas.

- Visualizador de JSON para a resposta com capacidades de expandir e fechar.

- Não existe suporte para apresentar uma miniatura da fotografia.

## <a name="using-fiddler-to-write-to-the-directory"></a>Utilizar Fiddler para escrita ao diretório

Para efeitos deste guia de guia de introdução, pode de utilizar o depurador de Web Fiddler para praticar a execução de operações 'escrever' contra diretório do Azure AD. Para obter mais informações e instalar Fiddler, consulte o artigo [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

No exemplo abaixo, irá utilizar Fiddler Web depurador para criar um novo grupo de segurança 'MyTestGroup' no seu diretório do Azure AD.

**Obter um token de acesso**: para aceder ao grafo do Azure AD, clientes são necessários para autenticar com êxito para Azure AD pela primeira vez. Para mais informações, consulte o artigo [Cenários de autenticação de Azure AD](active-directory-authentication-scenarios.md).

**Compor e executar uma consulta**: concluir os passos seguintes.

1. Abra Fiddler Web depurador e mude para o separador **Compositor** .
2. Uma vez que pretende criar um novo grupo de segurança, selecione a **mensagem** como o método de HTTP a partir do menu de lista pendente. Para mais informações sobre permissões e operações de um objeto do grupo, consulte o artigo [grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) dentro da [referência da API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. No campo junto à **mensagem**, escreva o seguinte como o URL do pedido: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] Tem de substituir mytenantdomain com o nome de domínio do diretório da sua própria Azure AD.

4. No campo diretamente abaixo pendente mensagem, escreva o seguinte:

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] SUBST seu &lt;token de acesso&gt; com token de acesso para o seu diretório do Azure AD.

5. No campo **pedido corpo** , escreva o seguinte:

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Para mais informações sobre a criação de grupos, consulte o artigo [Criar grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para obter mais informações sobre tipos de que são expostos pelo Graph e entidades do Azure AD e informações sobre as operações que pode ser executada nos mesmos com Graph, consulte o artigo [referência da API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a [API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- Saiba mais sobre [âmbitos de permissão de API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
