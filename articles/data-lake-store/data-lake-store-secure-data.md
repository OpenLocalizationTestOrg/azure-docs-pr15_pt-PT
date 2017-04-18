<properties 
   pageTitle="Proteger os dados armazenados no Azure dados Lake arquivo | Microsoft Azure" 
   description="Saiba como proteger dados Azure arquivo de dados Lake utilizar grupos e listas de controlo de acesso" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/29/2016"
   ms.author="nitinme"/>

# <a name="securing-data-stored-in-azure-data-lake-store"></a>Proteger dados armazenados no arquivo de Lake de dados do Azure

Proteger os dados na loja do Azure dados Lake é uma abordagem de três passo.

1. Comece por criar grupos de segurança no Azure Active Directory (AAD). Estes grupos de segurança são utilizados para implementar o controlo de acesso baseado em funções (RBCA) no Portal do Azure. Para obter mais informações, consulte [Controlo de acesso baseado em funções no Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Atribua os grupos de segurança AAD à conta Azure dados Lake loja. Controla o acesso à conta arquivo de Lake dados a partir de operações de portal e gestão do portal ou APIs.

3. Atribua os grupos de segurança AAD como acesso listas de controlo (ACL) no sistema de ficheiros de arquivo de Lake de dados.

4. Para além disso, também pode definir um intervalo de endereços IP para clientes podem aceder aos dados no arquivo de dados de Lake.

Este artigo fornece instruções sobre como utilizar o portal do Azure para executar as tarefas acima. Para obter informações detalhadas sobre como implementa o arquivo de dados de Lake segurança de nível de dados e conta, consulte o artigo [segurança no Azure dados Lake arquivo](data-lake-store-security-overview.md). Para informações de aprofundada sobre como ACL são implementadas na loja do Azure dados Lake, consulte o artigo [Descrição geral do controlo de acesso no arquivo de dados de Lake](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Conta de um arquivo de Lake de dados de Azure**. Para obter instruções sobre como criar uma, consulte o artigo [Introdução ao Azure dados Lake arquivo](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Criar grupos de segurança no Azure Active Directory

Para obter instruções sobre como criar grupos de segurança AAD e como adicionar utilizadores ao grupo, consulte o artigo [Gerir grupos de segurança no Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Atribuir utilizadores ou grupos de segurança para contas do Azure dados Lake arquivo

Ao atribuir utilizadores ou grupos de segurança para contas do Azure dados Lake loja, pode controlar o acesso para operações de gestão da conta utilizando o portal do Azure e APIs de Gestor de recursos do Azure. 

1. Abra uma conta Azure dados Lake loja. A partir do painel esquerdo, clique em **Procurar**, clique em **Arquivo de dados de Lake**e, em seguida, clique no nome da conta à qual pretende atribuir um utilizador ou grupo de segurança de pá dados Lake loja.

2. No seu pá de conta do arquivo de Lake de dados, clique em **Definições**. Pá **Definições** , clique em **utilizadores**.

    ![Atribuir o grupo de segurança para conta Azure dados Lake arquivo] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir o grupo de segurança para conta Azure dados Lake arquivo")

3. O pá **utilizador** por predefinição lista grupo de **administradores de subscrição** como um proprietário. 

    ![Adicionar utilizadores e funções] (./media/data-lake-store-secure-data/adl.add.group.roles.png "Adicionar utilizadores e funções")
 
    Existem duas formas de adicionar um grupo e atribuir funções relevantes.

    * Adicionar um utilizador/grupo à conta e, em seguida, atribuir uma função ou
    * Adicionar uma função e, em seguida, atribuir utilizadores/grupos a função.

    Nesta secção, podemos observe a primeira abordagem, adicionar um grupo e, em seguida, atribuir funções. Pode efetuar passos semelhantes a pela primeira vez, selecione uma função e, em seguida, atribua grupos para essa função.
    
4. No pá **utilizadores** , clique em **Adicionar** para abrir o pá **adicionar acesso** . Na pá **adicionar acesso** , clique em **selecionar uma função**e, em seguida, selecione uma função de utilizador/grupo.

     ![Adicionar uma função para o utilizador] (./media/data-lake-store-secure-data/adl.add.user.1.png "Adicionar uma função para o utilizador")

    O **proprietário** e a função **Contribuinte** fornecem acesso a uma variedade de funções de administração da conta de lake de dados. Para utilizadores que irão interagir com dados no lake dados, pode adicioná-los à função de **leitor **. O âmbito destas funções está limitado a operações de gestão relacionado com a conta Azure dados Lake loja.

    Para dados permissões de sistema de ficheiros individuais de operações definem o que os utilizadores podem fazer. Por conseguinte, um utilizador com uma função de leitor pode ver apenas as definições administrativas associadas a conta, mas pode potencialmente ler e escrever dados com base em permissões do sistema de ficheiro atribuídas. Permissões de sistema de ficheiros de dados Lake arquivo são descritas na [atribuir o grupo de segurança como ACL para o sistema de ficheiros de arquivo de Lake Azure dados](#filepermissions).



5. No pá **adicionar acesso** , clique em **Adicionar utilizadores** para abrir o pá **Adicionar utilizadores** . Neste pá, procure o grupo de segurança que criou anteriormente no Azure Active Directory. Se tiver muitas grupos para procurar a partir de, utilize a caixa de texto na parte superior para filtrar o nome do grupo. Clique em **Selecionar**.

    ![Adicionar um grupo de segurança] (./media/data-lake-store-secure-data/adl.add.user.2.png "Adicionar um grupo de segurança")

    Se pretender adicionar um utilizador/grupo não estiver listado, pode convidá-las utilizando o ícone **Convidar** e especificando o endereço de e-mail para o utilizador/grupo.

6. Clique em **OK**. Deverá ver o grupo de segurança adicionado, conforme apresentado abaixo.

    ![Grupo de segurança adicionado] (./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança adicionado")

7. O grupo de segurança do utilizador agora tem acesso à conta Azure dados Lake loja. Se quiser fornecer acesso aos utilizadores específicos, pode adicioná-los ao grupo de segurança. Da mesma forma, se pretender revogar o acesso para um utilizador, pode removê-los a partir do grupo de segurança. Também pode atribuir vários grupos de segurança para uma conta. 

## <a name="filepermissions"></a>Atribuir utilizadores ou grupo de segurança como ACL ao sistema de ficheiros de arquivo de Lake de dados do Azure

Atribuindo grupos de segurança do utilizador para o sistema de ficheiros do Azure dados Lake, defina o controlo de acesso a dados armazenados na loja do Azure dados Lake.

1. No seu pá de conta do arquivo de Lake de dados, clique em **Explorador de dados**.

    ![Criar directórios na conta do arquivo de dados de Lake] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar directórios na conta Lake de dados")

2. Na pá **Explorador de dados** , clique no ficheiro ou pasta para a qual pretende configurar a ACL e, em seguida, clique em **Access**. Para atribuir ACL para um ficheiro, tem de clicar **Access** a partir do pá a **Pré-visualização do ficheiro** .

    ![Definir ACL no sistema de ficheiros de dados Lake] (./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACL no sistema de ficheiros de dados Lake")

3. O **Access** pá lista os acesso padrão e acesso personalizados que já esteja atribuído na raiz. Clique no ícone **Adicionar** para adicionar ACL de nível personalizado.

    ![Lista padrão e acesso personalizado] (./media/data-lake-store-secure-data/adl.acl.2.png "Lista padrão e acesso personalizado")

    * **Acesso a padrão** é o acesso de estilo de UNIX, onde pode especificar ler, escrever, executar (rwx) para três categorias de utilizador distintas: proprietário, de grupo e outras pessoas.
    * **Access personalizada** corresponde às ACL POSIX que permite-lhe definir permissões para utilizadores com nome específicos ou grupos e não apenas o ficheiro proprietário ou grupo. 
    
    Para mais informações, consulte o artigo [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Para mais informações sobre como ACL são implementadas no arquivo de dados de Lake, consulte o [Controlo de acesso no arquivo de dados de Lake](data-lake-store-access-control.md).

4. Clique no ícone **Adicionar** para abrir o pá **Adicionar Access personalizada** . Neste pá, clique em **Seleccionar utilizador ou grupo**e, em seguida, na pá **Seleccionar utilizador ou grupo** , procure o grupo de segurança que criou anteriormente no Azure Active Directory. Se tiver muitas grupos para procurar a partir de, utilize a caixa de texto na parte superior para filtrar o nome do grupo. Clique no grupo que pretende adicionar e, em seguida, clique em **Selecionar**.

    ![Adicionar um grupo] (./media/data-lake-store-secure-data/adl.acl.3.png "Adicionar um grupo")

5. Clique em **Selecionar permissões**, selecione as permissões e se pretende atribuir as permissões como predefinição ACL, aceder ACL ou ambas. Clique em **OK**.

    ![Atribuir permissões a grupo] (./media/data-lake-store-secure-data/adl.acl.4.png "Atribuir permissões a grupo")

    Para mais informações sobre as permissões no arquivo de dados de Lake e ACL predefinição de acesso, consulte o [Controlo de acesso no arquivo de dados de Lake](data-lake-store-access-control.md).


6. Na pá **Adicionar Access personalizada** , clique em **OK**. O grupo recentemente adicionado, com as permissões associadas, será agora listado no pá **Access** .

    ![Atribuir permissões a grupo] (./media/data-lake-store-secure-data/adl.acl.5.png "Atribuir permissões a grupo")

    > [AZURE.IMPORTANT] Na versão atual, só pode ter 9 entradas em **Personalizados acesso**. Se quiser adicionar mais do que 9 utilizadores, deverá criar grupos de segurança, adicionar utilizadores aos grupos de segurança, adicionar fornecer acesso a esses grupos de segurança da conta do arquivo de Lake de dados.

7. Se necessário, também pode modificar as permissões de acesso depois de ter adicionado o grupo. Desmarque ou selecione a caixa de verificação para cada tipo de permissão (leitura, escrita, Execute) com base em se pretende remover ou atribuir essa permissão ao grupo de segurança. Clique em **Guardar** para guardar as alterações ou **Rejeitar** para anular as alterações.

## <a name="set-ip-address-range-for-data-access"></a>Definir o intervalo de endereços IP para acesso aos dados

Azure dados Lake arquivo permite-lhe mais de bloqueio acesso ao seu arquivo de dados ao nível da rede. Pode ativar firewall, especificar um endereço IP ou definir um intervalo de endereços IP para os seus clientes fidedignos. Quando ativada, apenas os clientes que têm os endereços IP dentro intervalo definido podem ligar-se para o arquivo.

![Acesso IP e as definições da firewall] (./media/data-lake-store-secure-data/firewall-ip-access.png "Definições da firewall e o endereço IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Remover grupos de segurança para uma conta do arquivo de Lake de dados do Azure

Quando remove grupos de segurança a partir de contas do Azure dados Lake loja, que está apenas a alterar access para operações de gestão da conta utilizando o Portal do Azure e APIs de Gestor de recursos do Azure.

1. No seu pá de conta do arquivo de Lake de dados, clique em **Definições**. Pá **Definições** , clique em **utilizadores**.

    ![Atribuir o grupo de segurança para conta Azure dados Lake] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir o grupo de segurança para conta Azure dados Lake")

2. Nos **utilizadores** pá clique no grupo de segurança que pretende remover.

    ![Grupo de segurança para remover] (./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança para remover")

3. No pá para o grupo de segurança, clique em **Remover**.

    ![Grupo de segurança removido] (./media/data-lake-store-secure-data/adl.remove.group.png "Grupo de segurança removido")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Remover o grupo de segurança ACL de sistema de ficheiros de arquivo de Lake de dados do Azure

Quando remover ACL de grupos de segurança do sistema de ficheiros de arquivo do Azure dados Lake, alterar o acesso aos dados do arquivo de dados Lake.

1. No seu pá de conta do arquivo de Lake de dados, clique em **Explorador de dados**.

    ![Criar directórios na conta Lake de dados] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar directórios na conta Lake de dados")

2. No pá **Explorador de dados** , clique no ficheiro ou pasta para a qual pretende remover a ACL e, em seguida, na sua pá de conta, clique no ícone de **acesso** . Para remover ACL para um ficheiro, tem de clicar **Access** a partir do pá a **Pré-visualização do ficheiro** .

    ![Definir ACL no sistema de ficheiros de dados Lake] (./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACL no sistema de ficheiros de dados Lake")

3. Na pá **acesso** , a partir da secção **Personalizados acesso** , clique no grupo de segurança que pretende remover. Na pá **Personalizados acesso** , clique em **Remover** e, em seguida, clique em **OK**.

    ![Atribuir permissões a grupo] (./media/data-lake-store-secure-data/adl.remove.acl.png "Atribuir permissões a grupo")


## <a name="see-also"></a>Consulte também

- [Descrição geral do Azure Lake de arquivo de dados](data-lake-store-overview.md)
- [Copiar dados a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introdução ao arquivo de dados de Lake através do PowerShell](data-lake-store-get-started-powershell.md)
- [Introdução ao arquivo de dados de Lake utilizando .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Registos de diagnóstico do Access para o arquivo de dados de Lake](data-lake-store-diagnostic-logs.md)
