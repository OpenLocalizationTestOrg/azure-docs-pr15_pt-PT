<properties
   pageTitle="Criar o serviço principal no portal | Microsoft Azure"
   description="Descreve como criar uma nova aplicação do Active Directory e principal do serviço que pode ser utilizado com o controlo de acesso baseado em funções no Gestor de recursos do Azure para gerir o acesso aos recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Utilizar o portal para criar a aplicação do Active Directory e principal do serviço que pode aceder a recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Clip Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Quando tiver uma aplicação que precisa de aceder ou modificar recursos, tem de configurar uma aplicação do Active Directory (AD) e atribuir-lhe as permissões necessárias. Este tópico mostra-lhe como efetuar esses passos através do portal. Atualmente, tem de utilizar o portal clássico para criar uma nova aplicação do Active Directory e, em seguida, mude para o portal do Azure para atribuir uma função para a aplicação. 

> [AZURE.NOTE] Os passos neste tópico aplicam-se apenas ao utilizar o **portal clássico** para criar a aplicação de AD. **Se utilizar o portal do Azure para criar a aplicação de AD, estes passos não serão bem sucedida.** 
>
> Talvez seja mais fácil de configurar a sua aplicação de AD e o serviço principal através do [PowerShell](resource-group-authenticate-service-principal.md) ou [Clip de Azure](resource-group-authenticate-service-principal-cli.md), especialmente se quiser utilizar um certificado para autenticação. Este tópico não mostrar como utilizar um certificado.

Para obter uma explicação dos conceitos do Active Directory, consulte o artigo [aplicação objectos e Principal de serviço](./active-directory/active-directory-application-objects.md). Para mais informações sobre a autenticação do Active Directory, consulte o artigo [Cenários de autenticação de Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Para obter passos detalhados sobre integrar uma aplicação do Azure para gestão de recursos, consulte o [Guia do programador autorização com a API do Gestor de recursos do Azure](resource-manager-api-authentication.md).

## <a name="create-an-active-directory-application"></a>Criar uma aplicação do Active Directory

1. Inicie sessão na sua conta do Azure através do [portal clássica](https://manage.windowsazure.com/). 

2. Certifique-se de que conhece a predefinição do Active Directory para a sua subscrição. Só pode conceder acesso à aplicações no mesmo directório como a sua subscrição. Selecione **Definições** e procure o nome do directório associado à sua subscrição.  Para mais informações, consulte o artigo [como Azure subscrições estão associadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).
   
     ![localizar directório predefinido](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. Selecione **Do Active Directory** a partir do painel esquerdo.

     ![Selecione do Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Selecione o Active Directory que pretende utilizar para criar a aplicação. Se tiver mais de um Active Directory, criar a aplicação no directório predefinido para a sua subscrição.   

     ![Escolha directório](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. Para ver as aplicações no seu diretório, selecione **aplicações**.

     ![Ver aplicações](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Se não tiver criado uma aplicação nesse directório antes, deverá ver algo semelhante ao seguinte imagem. Selecione **Adicionar uma aplicação**

     ![Adicionar aplicação](./media/resource-group-create-service-principal-portal/create-application.png)

     Em alternativa, clique em **Adicionar** no painel inferior.

     ![Adicionar](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Selecione o tipo de aplicação que pretende criar. Neste tutorial, selecione **Adicionar uma aplicação que está a desenvolver a minha organização**. 

     ![nova aplicação](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Forneça um nome para a aplicação e selecione o tipo de aplicação que pretende criar. Para este tutorial, crie uma **E/ou de aplicações WEB WEB API** e clique no botão seguinte. Se selecionar **NATIVAS aplicação de cliente**, os restantes passos deste artigo não corresponderá a sua experiência.

     ![aplicação nome](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Preencha as propriedades para a sua aplicação. Para o **Início de sessão no URL**, fornece o URI a um web site que descreva a sua aplicação. Não é validada a existência do web site. Para **URI de ID da aplicação**, fornece o URI que identifica sua aplicação.

     ![Propriedades da aplicação](./media/resource-group-create-service-principal-portal/app-properties.png)

Que criou a sua aplicação.

## <a name="get-client-id-and-authentication-key"></a>Obter cliente a chave de id e autenticação

Quando através de programação início de sessão, é necessário o id de para a sua aplicação. Se a aplicação é executada nas suas próprias as credenciais, também precisa de uma chave de autenticação.

1. Selecione o separador **Configurar** para configurar a palavra-passe do seu pedido.

     ![configurar a aplicação](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copie o **código do cliente**.
  
     ![id de cliente](./media/resource-group-create-service-principal-portal/client-id.png)

3. Se a aplicação é executada nas suas próprias as credenciais, desloque para baixo para a secção **chaves** e selecione quanto tempo que pretende utilizar a sua palavra-passe seja válido.

     ![teclas](./media/resource-group-create-service-principal-portal/create-key.png)

4. Selecione **Guardar** para criar a sua chave.

     ![Guardar](./media/resource-group-create-service-principal-portal/save-icon.png)

     A chave guardada é apresentada e pode copiá-lo. Não se conseguir obter a chave mais tarde por isso, copiá-lo agora.

     ![guardado chave](./media/resource-group-create-service-principal-portal/save-key.png)

## <a name="get-tenant-id"></a>Obter id de inquilino

Quando através de programação início de sessão, tem de ser efetuada com o id do inquilino com o seu pedido de autenticação. Para aplicações Web e Web API aplicações, pode obter o id do inquilino ao selecionar **os pontos finais de vista** em parte inferior do ecrã e obter o id como é mostrado na seguinte imagem.  

   ![id de inquilino](./media/resource-group-create-service-principal-portal/save-tenant.png)

Também pode obter o id do inquilino através do PowerShell:

    Get-AzureRmSubscription

Em alternativa, clip Azure:

    azure account show --json

## <a name="set-delegated-permissions"></a>Definir delegada permissões

Se a sua aplicação acede recursos em nome de um utilizador com sessão iniciada, tem de conceder a aplicação obter permissão para aceder às outras aplicações. Conceder acesso a na secção **permissões para outras aplicações** do separador de **Configurar** . Por predefinição, uma permissão delegada já está ativada para o Azure Active Directory. Deixe esta permissão delegada inalterado.

1. Selecione **Adicionar aplicação**.

2. A partir da lista, selecione a **API de gestão de serviço do Windows Azure**. Em seguida, selecione o ícone de concluído.

      ![Selecione aplicação](./media/resource-group-create-service-principal-portal/select-app.png)

3. Na lista pendente para as permissões de delegado, selecione **Gestão de serviços do Access Azure como organização**.

      ![Selecione permissão](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Guarde a alteração.

## <a name="assign-application-to-role"></a>Atribuir a aplicação a função

Se a sua aplicação está a ser executado nas suas próprias as credenciais, tem de atribuir a aplicação a uma função. Decida qual a função representa as permissões corretas para a aplicação. Para saber mais sobre as funções disponíveis, consulte o artigo [RBAC: integrado funções](./active-directory/role-based-access-built-in-roles.md). 

Para atribuir uma função para uma aplicação, tem de ter as permissões corretas. Especificamente, tem de ter `Microsoft.Authorization/*/Write` acesso que é concedido através da função de [proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou a função de [Administrador de acesso do utilizador](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . A função Contribuinte não têm o acesso correto.

Pode definir o âmbito ao nível da subscrição, grupo de recursos ou recurso. Permissões são herdadas inferior níveis de âmbito. Por exemplo, adicionar uma aplicação para a função de leitor para um grupo de recursos significa que pode ler o grupo de recursos e quaisquer recursos contém.

1. Para atribuir a aplicação a uma função, mudar a partir do portal clássico do [Azure portal](https://portal.azure.com).

1. Verifique as permissões para se certificar de que pode atribuir o capital de serviço a uma função. Selecione **as minhas permissões** para a sua conta.

    ![Selecione as minhas permissões](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. Ver as permissões atribuídas para a sua conta. Tal como indicado anteriormente, tem de pertencer a funções de proprietário ou administrador de acesso do utilizador ou de ter uma função personalizada que concede acesso de escrita para Microsoft.Authorization. A imagem seguinte mostra uma conta que é atribuída à função de contribuinte para a subscrição, que não está as permissões adequadas para atribuir uma aplicação a uma função.

    ![Mostrar as minhas permissões](./media/resource-group-create-service-principal-portal/show-permissions.png)

     Se não tem as permissões adequadas para conceder acesso a uma aplicação, que tem um dos pedido que o administrador de subscrição adiciona à função de administrador de acesso do utilizador ou pedir que o administrador concede acesso à aplicação.

1. Navegue para o nível de âmbito que pretende atribuir a aplicação. Para atribuir uma função do âmbito da subscrição, selecione **subscrições**.

     ![Selecione a subscrição](./media/resource-group-create-service-principal-portal/select-subscription.png)

     Selecione a subscrição específica para atribuir a aplicação.

     ![Selecione a subscrição para atribuição](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     Selecione o ícone de **acesso** no canto superior direito.

     ![Selecione acesso](./media/resource-group-create-service-principal-portal/select-access.png)
     
     Em alternativa, para atribuir uma função no âmbito do grupo de recursos, navegue para um grupo de recursos. Pá de grupo de recursos, selecione **o controlo de acesso**.

     ![Selecione utilizadores](./media/resource-group-create-service-principal-portal/select-users.png)

     Os passos seguintes são iguais para qualquer âmbito.

2. Selecione **Adicionar**.

     ![Selecione adicionar](./media/resource-group-create-service-principal-portal/select-add.png)

3. Selecione a função de **leitor** (ou qualquer função que pretende atribuir a aplicação a).

     ![Selecione a função](./media/resource-group-create-service-principal-portal/select-role.png)

4. Quando vir a lista de utilizadores, que pode adicionar à função pela primeira vez, não verá aplicações. Apenas verá o grupo e utilizadores.

     ![Mostrar aos utilizadores](./media/resource-group-create-service-principal-portal/show-users.png)

5. Para localizar as aplicações, tem procurá-lo. Comece a escrever o nome da sua aplicação e irá alterar a lista de opções disponíveis. Quando vir na lista, selecione a aplicação.

     ![atribuir a função](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Selecione **Adicionei** para concluir a atribuir a função. Agora deverá ver a sua aplicação na lista de utilizações atribuídos a uma função para o grupo de recursos.


Para mais informações sobre como atribuir utilizadores e aplicações a funções através do portal do, consulte o artigo [atribuições de funções de utilização para gerir o acesso aos recursos da subscrição Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## <a name="sample-applications"></a>Aplicações de exemplo

As seguintes aplicações de exemplo mostram como iniciar sessão como o capital de serviço.

**.NET**

- [Implementar uma SSH ativado VM com um modelo com o .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gerir grupos de recursos com .NET e recursos Azure](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introdução com recursos - implementar a utilizar o modelo do Gestor de recursos do Azure - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introdução com recursos - gerir o grupo de recursos - Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implementar uma SSH ativado VM com um modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gestão de recursos Azure e grupos de recursos com Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**NODE.js**

- [Implementar uma SSH ativado VM com um modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gerir grupos de recursos com Node.js e recursos Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Rubi**

- [Implementar uma SSH ativado VM com um modelo no Rubi](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gestão de recursos Azure e grupos de recursos com Rubi](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre como especificar políticas de segurança, consulte o artigo [O controlo de acesso baseado em funções Azure](./active-directory/role-based-access-control-configure.md).  
- Para uma demonstração de vídeo sobre estes passos, consulte o artigo [Ativar gestão programação de um recurso de Azure com o Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

