## <a name="prerequisites"></a>Pré-requisitos

Antes de podemos pode escrever código de gestão de CDN, precisamos de fazer alguma preparação para ativar o nosso código interagir com o Gestor de recursos do Azure.  Para fazer isto, terá:

* Criar um grupo de recursos para conter o perfil CDN que criamos neste tutorial
* Configure o Azure Active Directory para fornecer autenticação nossa aplicação
* Aplicar permissões para o grupo de recursos para que apenas os utilizadores autorizados a partir do nosso inquilino do Azure AD podem interagir com os nossos perfil CDN

### <a name="creating-the-resource-group"></a>Criar o grupo de recursos

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com).

2. Clique no botão de **Novo** no canto superior esquerdo e, em seguida, **Gestão**e **Grupo de recursos**.
    
    ![Criar um novo grupo de recursos](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)

3. Contacte o seu grupo de recursos *CdnConsoleTutorial*.  Selecione a sua subscrição e escolha uma localização perto de si.  Se pretender, pode clicar na caixa de verificação **Afixar ao dashboard** para afixar o grupo de recursos para o dashboard no portal.  Este procedimento irá tornar mais fáceis de localizar mais tarde.  Depois de efetuar as suas seleções, clique em **Criar**.

    ![O grupo de recursos de nomenclatura](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)

4. Depois de criado o grupo de recursos, se não tiver afixá-lo para o dashboard, pode encontrá-la ao clicar em **Procurar**, em seguida, **Grupos de recursos**.  Clique no grupo de recursos para o abrir.  Tome nota do seu **ID da subscrição**.  Irá precisamos-la mais tarde.

    ![O grupo de recursos de nomenclatura](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Criar a aplicação do Azure AD e aplicar permissões

Existem duas abordagens a autenticação de aplicação com o Azure Active Directory: os utilizadores individuais ou um principal de serviço. Um serviço principal é semelhante a uma conta de serviço no Windows.  Em vez de conceder permissões para interagir com os perfis de CDN de um utilizador específico, recomendamos conceder em vez disso, as permissões para o serviço principal.  Principais de serviço são normalmente utilizados para processos automatizados, não interativa.  Apesar deste tutorial está a escrever uma aplicação de consola interativo, iremos concentrar-na abordagem principal de serviço.

Criar um principal de serviço é composta por vários passos, incluindo a criação de uma aplicação do Azure Active Directory.  Para fazer isto, iremos [siga este tutorial](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Certifique-se de que segue todos os passos no [tutorial ligada](../articles/resource-group-create-service-principal-portal.md).  É *importante extremamente* que executá-la exatamente conforme descrito.  Certifique-se ao seu **inquilino ID**, **nome de domínio do inquilino** da nota (normalmente uma *. onmicrosoft.com* domínio, a menos que especificou um domínio personalizado), **ID de cliente**e a **chave de autenticação de cliente**, tal como precisamos estes posterior.  Cuidado muito proteger o seu **ID de cliente** e **chave de autenticação de cliente**, tal como estas credenciais podem ser utilizados por qualquer pessoa para executar operações de como o capital de serviço. 
>   
> Quando recebe para o passo denominado [Configurar a aplicação de inquilino com várias](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), selecione **não**.
> 
> Quando recebe para o passo [Atribuir aplicação a função](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), utilize o grupo de recursos que criou anteriormente, *CdnConsoleTutorial*, mas em vez da função de **leitor** , atribuir a função **Contribuinte de perfil de CDN** .  Depois de atribuir a aplicação a função **Contribuinte de perfil de CDN** no seu grupo de recursos, regresse a este tutorial. 

Uma vez que criou o serviço de principal e atribuído a função **Contribuinte de perfil de CDN** , pá os **utilizadores** para o seu grupo de recursos deverá ter um aspeto semelhante ao seguinte.

![Pá de utilizadores](./media/cdn-app-dev-prep/cdn-service-principal-include.png)


### <a name="interactive-user-authentication"></a>Autenticação de utilizador interativos

Se, em vez de um capital de serviço, preferia autenticação de utilizador individual interativo, o processo é muito semelhante a essa para um principal de serviço.  Na verdade, terá siga o mesmo procedimento, mas que efetuar algumas alterações secundárias.

> [AZURE.IMPORTANT] Siga estes passos seguintes apenas se estiver a escolher utilizar a autenticação de utilizador individual em vez de um serviço principal.

1. Quando criar a sua aplicação, em vez de **Aplicação Web**, selecione a **aplicação nativa**. 
    
    ![Aplicação nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
    
2. Na página seguinte, será pedido para um **Redirecionar URI**.  O URI não ser validadas, mas lembre-se de que introduziu.  Terá de-la mais tarde. 

3. Não é necessário para criar uma **chave de autenticação de cliente**.

4. Em vez de atribuir um principal de serviço para a função **Contribuinte de perfil de CDN** , iremos atribuir utilizadores individuais ou grupos.  Neste exemplo, pode ver atribuiu *CDN demonstração utilizador* para a função **Contribuinte de perfil de CDN** .  
    
    ![Acesso de utilizador individual](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

