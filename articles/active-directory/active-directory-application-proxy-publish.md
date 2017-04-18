<properties
    pageTitle="Publicar aplicações com o Proxy de aplicação do Azure AD | Microsoft Azure"
    description="Publica aplicações no local para a nuvem com o Proxy de aplicação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicar aplicações utilizando o Proxy de aplicação do Azure AD

Proxy de aplicação do Azure AD ajuda-o suporte trabalhadores remotos publicando aplicações no local para ser acedido através da internet. Por esta altura, já deve ter [ativado o Proxy de aplicação no portal do Azure clássico](active-directory-application-proxy-enable.md). Este artigo explica os passos para publicar aplicações que estiver a executar na sua rede local e fornecem acesso remoto seguro a partir de fora da sua rede. Depois de concluir este artigo, estará pronto para configurar a aplicação com informações personalizadas ou os requisitos de segurança.

> [AZURE.NOTE] Proxy de aplicação é uma funcionalidade que está disponível apenas se atualizado para a Premium ou edição básica do Azure Active Directory. Para mais informações, consulte o artigo [edições do Azure Active Directory](active-directory-editions.md).

## <a name="publish-an-app-using-the-wizard"></a>Publicar uma aplicação utilizando o Assistente

1. Inicie sessão como administrador no [portal clássica Azure](https://manage.windowsazure.com/).
2. Aceda ao Active Directory e selecione o diretório de ter ativado o Proxy de aplicação.

    ![Do Active Directory - ícone](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Clique no separador **aplicações** e, em seguida, clique no botão **Adicionar** na parte inferior do ecrã

    ![Adicionar aplicação](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Selecione **publicar uma aplicação que será acessível a partir do fora da sua rede**.

    ![Publicar uma aplicação que será acessível a partir do fora da sua rede](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Forneça as seguintes informações sobre a aplicação:

    - **Nome**: O nome amigável para a sua aplicação. Tem de ser exclusivo no diretório da sua.
    - **URL interno**: O endereço que utiliza o conector de Proxy de aplicação para aceder à aplicação a partir de dentro da sua rede privada. Pode fornecer um caminho específico no servidor back-end para publicar, enquanto o resto do servidor é não publicado. Desta forma, pode publicar sites diferentes no mesmo servidor e atribua cada as suas próprias regras de acesso e o nome.

        > [AZURE.TIP] Se publicar um caminho, certifique-se de que inclui todas as imagens for necessárias, scripts e folhas de estilo para a sua aplicação. Por exemplo, se a sua aplicação está na https://yourapp/app e utiliza imagens que se encontra no https://yourapp/media, deverá publicar https://yourapp/ como o caminho.

    - **Método de pré-autenticação**: como o Proxy de aplicação verifica os utilizadores antes de conceder-lhes acesso à sua aplicação. Selecione uma das opções a partir do menu pendente.

        - Azure Active Directory: Proxy de aplicação redireciona utilizadores para iniciar sessão com o Azure AD autentica as respetivas permissões para o diretório e a aplicação.
        - Pass-through: Os utilizadores não têm que autenticar para aceder à aplicação.

    ![Propriedades da aplicação](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Para concluir o assistente, clique na marca de verificação na parte inferior do ecrã. A aplicação está agora definida no Azure AD.


## <a name="assign-users-and-groups-to-the-application"></a>Atribuir utilizadores e grupos para a aplicação

Para os seus utilizadores aceder à sua aplicação publicada, é necessário atribuir-lhes individualmente ou em grupos. (Não se esqueça de atribuir a mesmo acesso, demasiado.) Isto requer que cada utilizador tem uma licença para Azure básicas ou superior. Pode atribuir licenças, individualmente ou aos grupos. Consulte o artigo [atribuir aos utilizadores uma aplicação](active-directory-applications-guiding-developers-assigning-users.md) para obter mais detalhes. 

Para as aplicações que requerem pré-autenticação, isto concede permissões para utilizar a aplicação. Para as aplicações que não exigem pré-autenticação, os utilizadores ainda podem ser atribuídos à aplicação para que seja apresentada na sua lista de aplicação, tal como Asminhasaplicações.

1. Depois de concluir o Assistente Adicionar aplicação, consulte a página de início rápido para a sua aplicação. Para gerir a quem tem acesso à aplicação, selecione **utilizadores e grupos**.

    ![Guia de introdução do Proxy de aplicação atribuir utilizadores - captura de ecrã](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Procurar grupos específicos no seu diretório ou mostrar todos os seus utilizadores. Para apresentar os resultados da pesquisa, clique na marca de verificação.

    ![Procurar grupos ou utilizadores - captura de ecrã](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Selecione cada utilizador ou grupo que pretende atribuir a esta aplicação e clique em **atribuir**. São-lhe pedido para confirmar esta ação.

> [AZURE.NOTE] Para aplicações integradas autenticação do Windows, pode atribuir apenas os utilizadores e grupos que são sincronizados a partir do seu Active Directory no local. Não podem ser atribuída aos utilizadores que inicie sessão com uma conta Microsoft e os convidados para as aplicações publicadas com Proxy de aplicação do Azure Active Directory. Certifique-se os seus utilizadores, inicie sessão com as credenciais que fazem parte do mesmo domínio como a aplicação que esteja a publicar.

## <a name="test-your-published-application"></a>Teste a sua aplicação publicada

Assim que tiver publicado a aplicação, pode testá-la ao navegar para o URL que publicado. Certifique-se de que pode ser acedido, que compõe corretamente e que tudo está a funcionar como esperado. Se tiver problemas ao ou obter uma mensagem de erro, tente o [Guia de resolução de problemas](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Configurar a aplicação

Pode modificar aplicações publicadas ou configurar opções avançadas na página configurar. Nesta página, pode personalizar a sua aplicação alterando o nome ou carregar um logótipo. Também pode gerir regras de acesso, como o método pré-autenticação ou a autenticação multifator.

![Configuração avançada](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Depois de publicar aplicações com o Azure Active Directory aplicação Proxy, são apresentados na lista de aplicações no Azure AD e pode gerir aí.

Se desativar a serviços de Proxy de aplicação depois de ter publicado aplicações, já não são acessíveis a partir do fora da sua rede privada. Isto não elimina as aplicações.

Para ver uma aplicação e certifique-se de que é acessível, faça duplo clique no nome da aplicação. Se o serviço de Proxy de aplicação está desativado e a aplicação não está disponível, é apresentada uma mensagem de aviso na parte superior do ecrã.

Para eliminar uma aplicação, selecione uma aplicação na lista e, em seguida, clique em **Eliminar**.

## <a name="next-steps"></a>Próximos passos

- [Publicar aplicações utilizando o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabalhar com aplicações deverá ter em consideração em afirmações](active-directory-application-proxy-claims-aware-apps.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)
