<properties
    pageTitle="Criar efeitos de .NET HDInsight autenticação não interativa | Microsoft Azure"
    description="Saiba como criar autenticação não interativa .NET HDInsight aplicações."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Criar autenticação não interativa .NET HDInsight aplicações

Pode executar a aplicação .NET Azure HDInsight em identidade da aplicação (não interativa) ou sob a identidade do utilizador tiver sessão iniciada-in da aplicação (interativo). Para uma amostra da aplicação interativa, consulte o artigo [tarefas de submeter porco/ramo/Sqoop utilizando HDInsight .NET SDK](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Este artigo mostra-lhe como criar a aplicação do .NET não interativa autenticação para ligar ao Azure HDInsight e submeter uma tarefa de ramo.

A partir da sua aplicação .NET, terá de:

- o ID do inquilino subscrição Azure
- o ID de cliente da aplicação de diretório do Azure
- a Azure Directory secreta tecla de aplicação.  

O processo principal inclui os seguintes passos:

2. Crie uma aplicação do Azure Directory.
2. Atribua funções para a aplicação de AD.
3. Desenvolva a sua aplicação cliente.


##<a name="prerequisites"></a>Pré-requisitos

- Cluster de HDInsight. Pode criar um utilizando as instruções que se encontram no [Tutorial de iniciação](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## <a name="create-azure-directory-application"></a>Criar a aplicação de diretório do Azure 
Quando cria uma aplicação do Active Directory, que realmente cria a aplicação e um serviço principal. Pode executar a aplicação na identidade da aplicação.

Atualmente, tem de utilizar o portal clássico Azure para criar uma nova aplicação do Active Directory. Esta capacidade será adicionada ao portal do Azure com uma versão posterior. Também pode executar estes passos através do Azure PowerShell ou clip de Azure. Para mais informações sobre como utilizar o PowerShell ou clip com o serviço principal, consulte o artigo [autenticar serviço principal com o Gestor de recursos do Azure](../resource-group-authenticate-service-principal.md).

**Para criar uma aplicação do diretório do Azure**

1.  Inicie sessão no [portal clássica Azure]( https://manage.windowsazure.com/).
2.  Selecione **Do Active Directory** a partir do painel esquerdo.

    ![Azure clássico portal do active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.  Selecione a pasta que pretende utilizar para criar a nova aplicação. Deve ser um já existente.
4.  Clique em **aplicações** situado na parte superior para listar as aplicações existentes.
5.  Clique em **Adicionar** a partir da parte inferior para adicionar uma nova aplicação.
6.  Introduza o **nome**, selecione a **aplicação Web e/ou Web API**e, em seguida, clique em **seguinte**.

    ![nova aplicação azure do active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.  Introduza o **URL de início de sessão** e **URI de ID da aplicação**. Para o **Início de sessão no URL**, fornece o URI para um web site que descreva a sua aplicação. Não é validada a existência do web site. Para URI de ID da aplicação, fornece o URI que identifica sua aplicação. E, em seguida, clique em **Concluir**.
Bastam alguns minutos para criar a aplicação.  Quando a aplicação estiver criada, o portal mostra-lhe a página gelo rápida da nova aplicação. Não feche o portal. 

    ![Propriedades de aplicação do novo azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Para aceder à aplicação cliente ID e a chave secreta**

1.  Na página de aplicação AD recentemente criado, clique em **Configurar** a partir do menu superior.
2.  Fazer uma cópia do **ID de cliente**. Terá-lo na sua aplicação .NET.
3.  Em **chaves**, clique em **Selecionar duração** pendente e selecione **1 ano** ou **anos a 2**. O valor de chave não será apresentado até que guarde a configuração.
4.  Clique em **Guardar** na parte inferior da página. Quando for apresentada a chave secreta, fazer uma cópia da chave de. Terá-lo na sua aplicação .NET.

##<a name="assign-ad-application-to-role"></a>Atribuir aplicação AD a função

Tem de atribuir a aplicação a uma [função](../active-directory/role-based-access-built-in-roles.md) para conceder-lhe permissões para realizar ações. Pode definir o âmbito ao nível da subscrição, grupo de recursos ou recurso. As permissões são herdadas inferior níveis de âmbito (por exemplo, adicionar que uma aplicação para a função de leitor para um grupo de recursos significa-pode ler o grupo de recursos e quaisquer recursos contém). Neste tutorial, irá defina o âmbito ao nível do grupo de recursos.  Porque o portal do Azure clássico não suporta grupos de recursos, este peça tem de ser efetuados a partir do portal do Azure. 

**Para adicionar a função de proprietário para a aplicação de AD**

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
2.  Clique em **Grupo de recursos** a partir do painel esquerdo.
3.  Clique no grupo de recursos que contém o cluster HDInsight onde será executada a sua consulta ramo posteriormente neste tutorial. Se existirem demasiadas grupos de recursos, pode utilizar o filtro.
4.  Clique em **acesso** a partir do pá cluster.

    ![ícone na nuvem e thunderbolt = guia de introdução](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.  Clique em **Adicionar** a partir do pá de **utilizadores** .
6.  Siga a instrução para adicionar a função de **proprietário** para a aplicação de AD que criou no último procedimento. Quando concluído com êxito, deve verá a aplicação listada na pá utilizadores com a função de proprietário.


##<a name="develop-hdinsight-client-application"></a>Desenvolver a aplicação de cliente HDInsight

Crie uma aplicação de consola c# .net seguir as instruções que se encontram em [projetos de submeter Hadoop no HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Em seguida, substitua o método de GetTokenCloudCredentials com o seguinte:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Para obter o ID do inquilino através do PowerShell:

    Get-AzureRmSubscription

Em alternativa, clip Azure:

    azure account show --json

      
## <a name="see-also"></a>Consulte também

- [Submeter Hadoop tarefas no HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Criar aplicações do Active Directory e principal de serviço através do portal](../resource-group-create-service-principal-portal.md)
- [Autenticar principal do serviço com o Gestor de recursos do Azure](../resource-group-authenticate-service-principal.md)
- [Controlo de acesso baseado em funções Azure](../active-directory/role-based-access-control-configure.md)
