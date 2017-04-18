<properties
    pageTitle="Introdução ao Azure Active Directory identidade Protection e Microsoft Graph | Microsoft Azure"
    description="Fornece uma introdução à consulta Microsoft Graph para uma lista de eventos de risco e informações associadas a partir do Azure Active Directory."
    services="active-directory"
    keywords="proteção de identidade do Azure do active directory, evento de risco, vulnerabilidade, a política de segurança, o Microsoft Graph"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory identidade Protection e Microsoft Graph

Microsoft Graph é ponto final de API unificada da Microsoft e a página inicial do [Azure Active Directory protecção de identidade do](active-directory-identityprotection.md) APIs. Os nossos API do primeiro, **identityRiskEvents**, permite-lhe a consulta Microsoft Graph para uma lista de [eventos de risco](active-directory-identityprotection-risk-events-types.md) e as informações associadas. Este artigo obtém a começar a consultar esta API. Para uma profundidade em Introdução, a documentação total e acesso ao Graph Explorer, consulte o [site do Microsoft Graph](https://graph.microsoft.io/).

Existem três passos para aceder a dados de proteção de identidade através do Microsoft Graph:

1. Adicione uma aplicação com uma palavra-passe cliente. 

2. Utilize esta palavra-passe e algumas outras partes de informação para autenticar para o Microsoft Graph, qual recebe um token de autenticação. 

3. Utilize este token para fazer pedidos para o ponto final API e voltar a dados de proteção de identidade.

Antes de começar, terá de:

- Privilégios de administrador para criar a aplicação no Azure AD
- O nome do domínio seu inquilino (por exemplo, contoso.onmicrosoft.com)



## <a name="add-an-application-with-a-client-secret"></a>Adicionar uma aplicação com uma palavra-passe cliente


1. [Inicie sessão no](https://manage.windowsazure.com) seu portal clássica Azure como administrador. 

1. No painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. No menu no canto superior, clique em **aplicações**.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. Clique em **Adicionar** na parte inferior da página.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação que está a desenvolver a minha organização**.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. Na caixa de diálogo **Dê-nos sobre a aplicação** , execute os seguintes passos:

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

    um. Na caixa de texto **nome** , escreva um nome para a sua aplicação (por exemplo: AADIP risco evento API aplicação).

    b. Como **tipo**, selecione **Web API aplicação e/ou Web**.

    c. Clique em **seguinte**.


5. Na caixa de diálogo **Propriedades da aplicação** , execute os seguintes passos:

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

    um. Na caixa de texto **Sign-On URL** , escreva `http://localhost`.

    b. Na caixa de texto **URI de ID da aplicação** , escreva `http://localhost`.

    c. Clique em **concluído**.


Agora, pode configurar a aplicação.

![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder a permissão de aplicação para utilizar a API


1. Na página da sua aplicação, no menu na parte superior, clique em **Configurar**. 

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. Na secção **permissões para outras aplicações** , clique em **Adicionar aplicação**.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. Na caixa de diálogo **permissões para outras aplicações** , execute os seguintes passos:

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

    um. Selecione **Microsoft Graph**.

    b. Clique em **concluído**.


1. Clique em **as permissões da aplicação: 0**e, em seguida, selecione **Ler todas as informações de evento de risco de identidade**.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. Clique em **Guardar** na parte inferior da página.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## <a name="get-an-access-key"></a>Obter uma tecla de acesso

1. Na página da sua aplicação, na secção de **teclas** , selecione 1 ano como duração.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. Clique em **Guardar** na parte inferior da página.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. na secção de teclas, copie o valor da chave recentemente criado e, em seguida, colá-la num local seguro.

    ![Criar uma aplicação](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

    > [AZURE.NOTE] Caso a perca esta tecla, terá de regresse a esta secção e criar uma nova chave. Manter esta tecla uma palavra-passe: qualquer pessoa que tenha-acede aos seus dados.


1. Na secção **Propriedades** , copie o **Código do cliente**e, em seguida, colá-la num local seguro. 


## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticar para o Microsoft Graph e a API de eventos de risco de identidade da consulta

Neste momento, que deve ter:

- O ID do cliente que copiou anteriormente

- A chave que copiou anteriormente

- O nome do domínio seu inquilino


Para autenticar, envie um pedido de mensagem para `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: "**client_credentials**"

- recurso: "**https://graph.microsoft.com**"

- client_id:<your client ID>

- client_secret:<your key>


> [AZURE.NOTE] Tem de fornecer valores para o **client_id** e o parâmetro **client_secret** .

Se tiver êxito, esta devolve um token de autenticação.  
Para ligar a API, crie um cabeçalho com o parâmetro seguinte:

    `Authorization`=”<token_type> <access_token>"


Quando a autenticar, pode encontrar o tipo de token e token de acesso no token de devolvidos.

Envie este cabeçalho como um pedido para o seguinte URL API:`https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se tiver êxito, é uma coleção de eventos de risco de identidade e os dados associados no formato JSON de OData, que pode ser analisado e gerido, consulte o artigo ajustar.

Eis exemplos de código para autenticar e chamar a API através do Powershell.  
Basta adicionar o seu ID de cliente, chave e inquilinos domínio.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Próximos passos

Parabéns, que acabou de criar a primeira chamada para o Microsoft Graph!  
Agora pode consultar eventos de risco de identidade e utilizar os dados, no entanto, consulte o artigo ajustar.

Para saber mais sobre o Microsoft Graph e como criar aplicações de utilizar a API do gráfico, dar saída a [documentação](https://graph.microsoft.io/docs) e muito mais no [site do Microsoft Graph](https://graph.microsoft.io/). Além disso, certifique-se para a página de [proteção de identidade do Azure AD API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) que lista todos os das APIs identidade disponíveis no grafo do marcador. À medida que adiciona novas formas de trabalhar com proteção de identidade através da API, irá vê-los nessa página.


## <a name="additional-resources"></a>Recursos adicionais

- [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)

- [Tipos de eventos de risco detectados pelo protecção de identidade do Azure Active Directory](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Descrição geral do Microsoft Graph](https://graph.microsoft.io/docs)

- [Raiz do serviço de proteção de identidade do Azure AD](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)
