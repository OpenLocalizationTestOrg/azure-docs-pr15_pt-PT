<properties
   pageTitle="Ligar à base de dados SQL ou armazém de dados SQL ao utilizando a autenticação do Azure Active Directory | Microsoft Azure"
   description="Saiba como se ligar à base de dados SQL utilizando a autenticação do Azure Active Directory."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>Ligar a base de dados SQL ou armazém de dados SQL utilizando a autenticação do Azure Active Directory

Autenticação do Azure Active Directory é um mecanismo de ligar à base de dados do Microsoft Azure SQL e [Armazém de dados SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) através de identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure Active Directory, pode gerir centralmente as identidades dos utilizadores de base de dados e outros serviços da Microsoft numa localização central. Gestão central de ID proporciona um só local para gerir os utilizadores de base de dados e simplifica a gestão de permissões. Benefícios incluem o seguinte:

- Fornece uma alternativa a autenticação do SQL Server.
- Ajuda a impedir que o multiplicação de identidades de utilizador por servidores de base de dados.
- Permite a rotação de palavra-passe num único local
- Os clientes podem gerir permissões de base de dados utilizando externos (AAD) grupos.
- -Pode eliminar armazenar palavras-passe ao ativar integrada autenticação do Windows e outras formas de autenticação suportados pelo Azure Active Directory.
- Utilizações de autenticação do Azure Active Directory continham utilizadores de base de dados para autenticar identidades ao nível da base de dados.
- Azure Active Directory suporta autenticação baseada em token para aplicações ligar à base de dados SQL.
- Autenticação do Active Directory Azure suporta ADFS (Federação de domínios) ou a autenticação de palavra-passe nativas utilizador para um Azure Active Directory local sem sincronização de domínio.  
- Azure Active Directory suporta ligações a partir do SQL Server Management Studio que utilizam Universal autenticação do Active Directory, que inclui a autenticação Multifator (MFA).  MFA inclui uma autenticação forte com um intervalo de opções de verificação fácil — chamada telefónica, mensagem de texto, cartões de smart card com o pin ou notificação de aplicação móvel. Para mais informações, consulte o artigo [SSMS suporte para o Azure AD MFA com base de dados SQL e armazém de dados SQL](sql-database-ssms-mfa-authentication.md).

Os passos de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Azure Active Directory.

1. Criar e povoar um Azure Active Directory.
2. Certifique-se de que a base de dados está no V12 de base de dados do SQL Azure. (Não é necessário para armazém de dados SQL.)
3. Opcional: Associar ou altere o active directory que esteja atualmente associado com a sua subscrição do Azure.
4. Crie um administrador do Azure Active Directory para Azure SQL Server ou [Armazém de dados do SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configure os computadores cliente.
6. Crie utilizadores de base de dados contidas na base de dados mapeado para identidades do Azure AD.
7. Ligar à sua base de dados com identidades do Azure AD.


## <a name="trust-architecture"></a>Arquitetura de fidedignidade

O diagrama de alto nível seguinte resume a arquitetura de solução do utilizando a autenticação do Azure AD com base de dados do SQL Azure. Aplicam os mesmos conceitos ao armazém de dados SQL. Para suportar a palavra-passe de utilizador nativas do Azure Active Directory, é considerada apenas a parte da nuvem e a base de dados do Azure AD/Azure SQL. Para suportar a autenticação de Federado (ou utilizador/palavra-passe para as credenciais do Windows), é necessária a comunicação com ADFS bloco. As setas indicam percursos de comunicação.

![Diagrama de auth aad][1]

O diagrama seguinte indica a Federação, fidedignidade e alojamento relações que permitem um cliente ligar a uma base de dados ao submeter um token. O token é autenticado por um Azure AD e é fidedigno para a base de dados. O cliente 1 pode representar um Azure Active Directory com utilizadores nativos ou um Azure Active Directory com utilizadores federados. Cliente 2 representa uma possível solução, incluindo os utilizadores importados; Neste exemplo provenientes de uma federados de Azure Active Directory com o ADFS a ser sincronizada com o Azure Active Directory. É importante compreender que o acesso a uma base de dados utilizando a autenticação do Azure AD requer que a subscrição alojamento está associada ao Azure Active Directory. Mesma subscrição tem de ser utilizada para criar o SQL Server que aloja a base de dados do SQL Azure ou armazém de dados SQL.

![relação de subscrição][2]

## <a name="administrator-structure"></a>Estrutura de administrador

Quando utilizando a autenticação do Azure AD, existem duas contas de administrador para o servidor de base de dados SQL o administrador do SQL Server original e o administrador do Azure AD. Aplicam os mesmos conceitos ao armazém de dados SQL. Apenas o administrador baseado numa conta Azure AD pode criar o primeiro utilizador de base de dados do Azure AD contida numa base de dados do utilizador. O início de sessão do Azure AD administrador pode ser um utilizador do Azure AD ou um grupo do Azure AD. Quando o administrador é uma conta de grupo, pode ser utilizado por membros do grupo, permitindo-múltiplos administradores do Azure AD para a instância do SQL Server. Utilizar a conta de grupo como administrador melhora a capacidade de gestão, permitindo-lhe adicionar e remover membros do grupo sem alterar os utilizadores ou permissões de base de dados SQL Azure AD centralmente. Apenas um administrador do Azure AD (um utilizador ou grupo) pode ser configurado em qualquer altura.

![estrutura de administrador][3]

## <a name="permissions"></a>Permissões

Para criar novos utilizadores, tem de ter o `ALTER ANY USER` permissão da base de dados. O `ALTER ANY USER` pode ser concedida permissão para qualquer utilizador de base de dados. O `ALTER ANY USER` permissão também é mantida contas de administrador do servidor e, os utilizadores de base de dados com a `CONTROL ON DATABASE` ou `ALTER ON DATABASE` permissão para essa base de dados e pelos membros da `db_owner` funções de base de dados.

Para criar um utilizador de base de dados contidas na base de dados do SQL Azure ou armazém de dados SQL, tem de ligar à base de dados utilizando uma identidade do Azure AD. Para criar o primeiro utilizador de base de dados contidos, tem de ligar à base de dados utilizando um administrador do Azure Active Directory (que é o proprietário da base de dados). Isso é demonstrado nos passos 4 e 5 abaixo. Qualquer autenticação do Azure Active Directory só é possível se o administrador do Azure Active Directory tiver sido criado para base de dados do SQL Azure ou o armazém de dados do SQL server. Se o administrador do Azure Active Directory tiver sido removido do servidor, utilizadores existentes do Azure Active Directory criados anteriormente no interior do SQL Server podem já não ligar à base de dados utilizando as respetivas credenciais do Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Azure AD funcionalidades e limitações

Os membros seguintes do Azure Active Directory podem ser aprovisionados no Azure SQL Server ou armazém de dados SQL:

- Membros nativos: um membro criado no Azure AD no domínio gerido ou num domínio de cliente. Para mais informações, consulte o artigo [Adicionar o seu próprio nome de domínio para Azure AD](../active-directory/active-directory-add-domain.md).

- Federado membros do domínio: um membro criado no Azure AD num domínio federado. Para mais informações, consulte o artigo [do Microsoft Azure agora suporta a Federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).

- Importados os membros a partir de outros directórios ativo Azure que são membros de domínio nativo ou federados.

- Grupos do Active Directory criados como grupos de segurança.

Contas Microsoft (por exemplo, outlook.com, hotmail.com, live.com) ou a outras contas de convidado (por exemplo gmail.com, yahoo.com) não são suportadas. Se pode iniciar sessão no [https://login.live.com](https://login.live.com) utilizando a conta e a palavra-passe, em seguida, estiver a utilizar uma conta Microsoft, que não é suportada para a autenticação do Azure AD para base de dados do SQL Azure ou armazém de dados do SQL Azure.

### <a name="additional-considerations"></a>Considerações adicionais

- Para melhorar a capacidade de gestão, recomendamos que aprovisionar um grupo do Azure Active Directory dedicado como administrador.
- Apenas um administrador do Azure AD (um utilizador ou grupo) pode ser configurado para uma Azure SQL Server ou armazém de dados do SQL Azure em qualquer altura.
- Apenas um administrador do Azure Active Directory para SQL Server inicialmente pode ligar para o Azure SQL Server ou armazém de dados SQL Azure com uma conta do Azure Active Directory. O administrador do Active Directory pode configurar os utilizadores da base de dados do Azure Active Directory subsequentes.
- Recomendamos que definir o tempo limite da ligação para 30 segundos.
- SQL Server 2016 Management Studio e as ferramentas de dados do SQL Server para Visual Studio 2015 (versão 14.0.60311.1April 2016 ou posterior) suportam uma autenticação Azure Active Directory. (A autenticação do azure Active Directory é suportada pelo **fornecedor de dados do .NET Framework para SqlServer**; na versão, pelo menos, .NET Framework 4.6). Por conseguinte, as versões mais recentes destas ferramentas e aplicações de camada de dados (CAD e .bacpac) podem utilizar a autenticação do Azure Active Directory.
- [Versão do ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339) suporta a autenticação do Azure Active Directory no entanto `bcp.exe` não é possível ligar utilizando a autenticação do Azure Active Directory porque utiliza um fornecedor ODBC mais antigo.
- `sqlcmd`suporta início de autenticação do Azure Active Directory com a versão 13.1 disponível a partir do [Centro de transferências](http://go.microsoft.com/fwlink/?LinkID=825643).  
- Ferramentas de dados do SQL Server para Visual Studio 2015, pelo menos, requer a versão de Abril de 2016 das ferramentas de dados (versão 14.0.60311.1). Atualmente, os utilizadores do Azure Active Directory não são apresentados no Explorador do objeto SSDT. Como solução, veja os utilizadores na [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Microsoft JDBC controlador 6.0 para SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=11774) suporta a autenticação do Azure Active Directory. Além disso, consulte [definir as propriedades da ligação](https://msdn.microsoft.com/library/ms378988.aspx).
- Não é possível autenticar PolyBase utilizando a autenticação do Azure Active Directory.
- Algumas ferramentas como BI e o Excel não são suportadas.
- Autenticação do Azure Active Directory é suportada para a base de dados SQL as lâminas Azure portais de **Base de dados de importação** e **Exportação** . Importar e exportar utilizando a autenticação do Azure Active Directory também é suportada a partir do comando PowerShell.


## <a name="1-create-and-populate-an-azure-ad"></a>1. a criar e preencher um Azure AD

Criar um Azure Active Directory e preencha-a com utilizadores e grupos. O Azure Active Directory pode ser o domínio gerido domínio inicial Azure AD. O Azure Active Directory também pode ser no local Active Directory Domain Services que esteja Federado com o Azure Active Directory.

Para mais informações, consulte o artigo [integrar a suas identidades no local com o Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Adicionar o seu nome de domínio para Azure AD](../active-directory/active-directory-add-domain.md), [Microsoft Azure agora suporta a Federação com o Active Directory do Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrar no diretório da sua Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)e [Gerir Azure AD através do Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## <a name="2-ensure-your-sql-database-is-version-12"></a>2. Certifique-se da que base de dados do SQL é versão 12

Autenticação do Azure Active Directory é suportada na mais recente da base de dados V12 SQL. Para obter informações sobre V12 de base de dados do SQL e para saber se está disponível na sua região, consulte o artigo [o que é novo no V12 de atualização de base de dados de SQL mais recente](sql-database-v12-whats-new.md). Este passo não é necessário para armazém de dados do SQL Azure porque armazém de dados SQL só está disponível no V12.

Se tiver uma base de dados existente, certifique-se de que está alojado no SQL V12 de base de dados ligando-se à base de dados (por exemplo utilizando o SQL Server Management Studio) e executar `SELECT @@VERSION;`. O resultado esperado para uma base de dados V12 de base de dados do SQL, pelo menos, é **Azure de SQL Microsoft (RTM) - 12.0**. Se a base de dados não estiver alojado no V12 de base de dados do SQL, consulte o artigo [Planear e preparar para atualizar para o V12 de base de dados do SQL](sql-database-v12-plan-prepare-upgrade.md)e, em seguida, visite o Portal de clássica do Azure para migrar a base de dados para V12 de base de dados do SQL.

Em alternativa, pode criar uma nova base de dados no V12 de base de dados do SQL ao seguir os passos indicados no [artigo criar a primeira base de dados do Azure SQL](sql-database-get-started.md). **Sugestão**: ler o passo seguinte antes de selecionar uma subscrição para a nova base de dados.

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3. opcional: Associar ou alterar o active directory que esteja atualmente associado com a sua subscrição do Azure

Para associar a base de dados de diretório do Azure AD para a sua organização, faça o diretório de um diretório de confiança para a subscrição Azure que aloja a base de dados. Para mais informações, consulte o artigo [como Azure subscrições estão associadas a Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Informações adicionais:** Cada subscrição Azure tem uma relação de confiança com uma instância do Azure AD. Isto significa que confia-esse directório para autenticar utilizadores, serviços e dispositivos. Múltiplas subscrições podem confiar do diretório do mesmo, mas apenas um diretório fidedignidades de uma subscrição. Pode ver qual diretório é considerado fidedigno pela sua subscrição no separador **Definições** na [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Esta relação de confiança que tenha uma subscrição com um diretório é ao contrário de relação que tenha uma subscrição com todos os outros recursos no Azure (sites públicos, bases de dados e assim sucessivamente), que são mais semelhante a recursos de subordinado de uma subscrição. Se a uma subscrição expirar, em seguida, acesso aos outros recursos associados à subscrição também será interrompida. Mas diretório permanece no Azure e pode associar a esse directório outra subscrição e continuar a gerir os utilizadores de diretório. Para mais informações sobre recursos, consulte [Noções sobre o acesso de recursos no Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Os procedimentos seguintes fornecem instruções passo a passo sobre como alterar o directório associado para uma subscrição determinado.

1. Ligue-se para o seu [Portal de clássico de Azure](https://manage.windowsazure.com/) utilizando um administrador de subscrição Azure.
2. Na faixa para a esquerda, selecione **Definições**.
3. As suas subscrições aparecem no ecrã definições. Se a subscrição pretendida não aparecer, clique em **subscrições** na parte superior, a caixa de **Filtro ao directório** de lista pendente e selecione a pasta que contém as suas subscrições e, em seguida, clique em **Aplicar**.

    ![Selecione a subscrição][4]
4. Na área **Definições** , clique na sua subscrição e, em seguida, clique em **Editar DIRETÓRIO** na parte inferior da página.

    ![portal de definições de AD][5]
5. Na caixa **Editar DIRETÓRIO** , selecione o Azure Active Directory que está associado com o SQL Server ou armazém de dados SQL e, em seguida, clique na seta para a seguinte.

    ![Editar pressionar diretório seleccionar][6]
6. Na caixa de diálogo de mapeamento do diretório de **Confirmar** , confirme se "**serão removidos todos os administradores de cocriação.**"

    ![Editar diretório confirmar][7]
7. Clique em verificar para recarregar o portal.

> [AZURE.NOTE] Quando altera o diretório, acesso a todos os coadministradores, Azure AD utilizadores e grupos e os utilizadores dos recursos de cópias de diretório são removidos e já não tiverem acesso a esta subscrição ou seus recursos. Apenas, como um administrador do serviço, pode configurar o acesso para os principais com base no novo directório. Esta alteração pode demorar um substancial período de tempo a serem propagadas para todos os recursos. Também alterando o diretório, altera o administrador do Azure AD para base de dados SQL e armazém de dados SQL e não permitir o acesso de base de dados para qualquer utilizadores existentes do Azure AD. O administrador do Azure AD tem de ser repor (conforme descrito abaixo) e Azure novo utilizadores AD tem de ser criados.

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4. Crie um administrador do Azure AD para Azure SQL Server

Inicia cada Azure SQL Server (que aloja uma base de dados SQL ou armazém de dados SQL) com uma conta de administrador do servidor único que é o administrador do SQL Server Azure inteira. Um administrador do SQL Server segundo deve ser criado, que é uma conta do Azure AD. Este principal é criado como um utilizador de base de dados contidas na base de dados principal. Como os administradores, as contas de administrador do servidor são membros da função **proprietário** cada base de dados de utilizador e introduzir cada base de dados de utilizador como o utilizador **dbo** . Para mais informações sobre as contas de administrador do servidor, consulte o artigo [Gerir bases de dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md) e da secção **utilizadores e inícios de sessão** do [Azure SQL directrizes de segurança da base de dados e limitações](sql-database-security-guidelines.md).

Quando utiliza o Azure Active Directory com replicação Geo, o administrador do Azure Active Directory tem de ser configurado para a página principal e os servidores secundários. Se um servidor não tiver um administrador do Azure Active Directory, em seguida, inícios de sessão do Azure Active Directory e utilizadores recebem um "não é possível ligar" erro de servidor.

> [AZURE.NOTE] Os utilizadores que não são baseados numa conta Azure AD (incluindo a conta de administrador do Azure SQL Server), não é possível criar Azure AD os utilizadores baseados, uma vez que não possui permissão para validar os utilizadores de proposta de base de dados com o Azure AD.

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>Aprovisionar um administrador do Azure Active Directory para o seu Azure SQL Server utilizando o portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, clique na ligação para uma lista de possíveis directórios ativo de lista pendente. Selecione o Active Directory correto como predefinição Azure AD. Este passo ligações a associação de subscrição com o Active Directory com o Azure SQL Server, certificando-se de que a mesma subscrição é utilizada para ambos Azure AD e o SQL Server. (Pode estar alojar o SQL Server Azure base de dados do SQL Azure ou armazém de dados do SQL Azure.)

    ![Selecione ad][8]
2. Na faixa para a esquerda selecione **SQL servers**, selecione o seu **do SQL server**e, em seguida, no pá **Do SQL Server** , na parte superior clique em **Definições**.

    ![definições de AD][9]
3. No pá **Definições** , clique em * * administrador do Active Directory.
4. Na pá **administrador do Active Directory** , clique em **admin do Active Directory**e, em seguida, na parte superior, clique em **conjunto de administrador**.
5. Na pá a **Administração de adicionar** , pesquisa para um utilizador, selecione o utilizador ou grupo de ser um administrador e, em seguida, clique em **Selecionar**. (O pá do administrador do Active Directory mostra todos os membros e grupos do Active Directory. Não não possível selecionar utilizadores ou grupos que estão a cinzento porque não são suportadas como administradores do Azure AD. (Consulte a lista de administradores suportados no **Azure AD funcionalidades e limitações** acima). Controlo de acesso baseado em funções (RBCA) só se aplica ao portal e não é propagado do SQL Server.
6. Na parte superior da pá **administrador do Active Directory** , clique em **Guardar**.
    ![Selecione admin][10]

    O processo de alterar o administrador poderá demorar vários minutos. Em seguida, o novo administrador é apresentada na caixa de **Administração do Active Directory** .

> [AZURE.NOTE] Quando a configurar o administrador do Azure AD, o novo nome de administrador (utilizador ou grupo) não pode já estar presente na base de dados principal virtual como um utilizador de autenticação do SQL Server. Se existir, o programa de configuração de administração do Azure AD irá falhar; anular a sua criação e que indica que essa um administrador (nome) já existentes. Uma vez que essas um utilizador de autenticação do SQL Server não estiver parte do Azure AD, qualquer esforço para ligar ao servidor utilizando a autenticação do Azure AD falha.

Para mais tarde, remover um administrador, no topo da pá **administrador do Active Directory** , clique em **Remover admin**e, em seguida, clique em **Guardar**.

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>Aprovisionar um administrador do Azure AD para Azure SQL Server utilizando o PowerShell

Para executar os cmdlets do PowerShell, tem de ter Azure PowerShell instalada e em execução. Para obter informações detalhadas, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Aprovisionar um administrador do Azure AD, execute os seguintes comandos do Azure PowerShell:

- AzureRmAccount adicionar
- Selecione AzureRmSubscription


Cmdlets utilizados para aprovisionar e gerir Azure AD administrador:

| Nome do cmdlet                                       | Descrição                                                                                                     |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Definir AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)    | Aprovisiona um administrador do Azure Active Directory para Azure SQL Server ou armazém de dados do SQL Azure. (Tem de ser da subscrição atual.) |
| [Remover AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Remove um administrador do Azure Active Directory para Azure SQL Server ou armazém de dados do SQL Azure. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)    | Devolve informações sobre um administrador do Azure Active Directory atualmente configurado para o Azure SQL Server ou armazém de dados do SQL Azure. |

Utilizar o comando get-ajuda do PowerShell para ver mais detalhes para cada um destes comandos, por exemplo ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

As seguintes disposições de script um grupo de administrador do Azure AD com o nome **DBA_Group** (id de objecto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o **demo_server** servidor num grupo de recursos denominada **grupo 23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome a apresentar Azure AD ou o nome de utilizador Principal. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do Azure AD o Azure AD nome a apresentar é suportada.

> [AZURE.NOTE] O comando Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` não impedi-lo de aprovisionamento do Azure AD administradores para os utilizadores não suportados. Um utilizador não suportado pode ser aprovisionado, mas não pode ligar a uma base de dados. (Consulte a lista de administradores suportados no **Azure AD funcionalidades e limitações** acima).

O exemplo seguinte utiliza o **ID de objecto**opcional:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Azure AD **ID de objecto** é necessária quando o **DisplayName** não é exclusivo. Para obter os valores de **ID de objecto** e **DisplayName** , utilize a secção do Active Directory do Portal clássica do Azure e ver as propriedades de um utilizador ou grupo.

O exemplo seguinte devolve informações sobre atuais admin do Azure AD para Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

O exemplo seguinte remove um administrador do Azure AD:
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

Também pode aprovisionar um administrador do Azure Active Directory utilizando as API REST. Para mais informações, consulte o artigo [referência da API do serviço de gestão de resto e operações para operações de bases de dados SQL Azure para bases de dados do SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5. configurar os computadores de cliente

Em todos os computadores cliente, a partir do qual aplicações ou utilizadores ligar à base de dados do SQL Azure ou armazém de dados do SQL Azure que utilizar identidades do Azure AD, tem de instalar o seguinte software:

- .NET framework 4.6 ou posterior do [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Biblioteca de autenticação do Azure Active Directory para SQL Server (**ADALSQL. DLL**) está disponível em vários idiomas (x86 e amd64) a partir do Centro de transferências da livraria [Microsoft Active Directory autenticação para o Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### <a name="tools"></a>Ferramentas

- Instalar o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [Ferramentas de dados do SQL Server para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) satisfaz os requisitos do .NET Framework 4.6.
- SSMS instalações a x86 a versão do **ADALSQL. DLL**.
- SSDT instala a versão amd64 do **ADALSQL. DLL**.
- O Visual Studio mais recente do [Visual Studio transferências](https://www.visualstudio.com/downloads/download-visual-studio-vs) satisfaz os requisitos do .NET Framework 4.6, mas não instalar a versão amd64 necessários do **ADALSQL. DLL**.

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6. criar utilizadores de base de dados contidas na base de dados mapeado para identidades do Azure AD

### <a name="about-contained-database-users"></a>Acerca dos utilizadores de base de dados contidas

Autenticação do Active Directory Azure requer que os utilizadores de base de dados criada como utilizadores de base de dados contidas. Um utilizador de base de dados que não tem um início de sessão da base de dados principal não é um utilizador de base de dados contidas com base numa identidade do Azure AD, e que mapas para uma identidade no diretório Azure AD que está associado a base de dados. A identidade do Azure AD pode ser uma conta de utilizador individual ou um grupo. Para mais informações sobre utilizadores contidas da base de dados, consulte o artigo [Efetuar os utilizadores de base de dados contidos Your da base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [AZURE.NOTE] Os utilizadores de base de dados (, à exceção dos administradores) não não possível criar através do portal. Funções RBAC não são propagadas para SQL Server, base de dados SQL ou armazém de dados SQL. Funções RBAC Azure são usadas para gerir os recursos do Azure e não são aplicadas às permissões da base de dados. Por exemplo, a função **Contribuinte do SQL Server** não conceder acesso ao ligar à base de dados SQL ou armazém de dados SQL. Tem de conceder a permissão de acesso diretamente na base de dados utilizando instruções Transact-SQL.

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Ligar para o armazém de base de dados ou dados de utilizador com SQL Server Management Studio ou ferramentas de dados do SQL Server

Para confirmar o administrador do Azure AD está corretamente configurada, ligar à base de dados **principal** com a conta de administrador do Azure AD.
Aprovisionar um Azure AD-utilizador com base em contidas da base de dados (que não seja o administrador do servidor que é o proprietário da base de dados), ligar-se a base de dados com uma identidade do Azure AD que tenha acesso à base de dados.

> [AZURE.IMPORTANT] Suporte para autenticação do Azure Active Directory está disponível com o [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [Ferramentas de dados do SQL Server](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. A versão de Agosto de 2016 do SSMS também inclui suporte para Universal autenticação do Active Directory, que permite aos administradores exigir autenticação Multifator utilizando uma chamada telefónica, a mensagem de texto, cartões de smart card com o pin ou notificação de aplicação móvel.

#### <a name="connect-using-active-directory-integrated-authentication"></a>Ligar utilizando a autenticação integrada do Active Directory

Utilize este método se tiver sessão iniciada Windows com as suas credenciais do Azure Active Directory num domínio federado.

1. Inicie o Management Studio ou ferramentas de dados e na caixa de diálogo **ligar ao servidor** (ou **ligar ao motor de base de dados**), na caixa de **autenticação** , selecione **Autenticação integrado do Active Directory**. Nenhuma palavra-passe é necessária ou pode ser introduzido uma vez que as suas credenciais existentes serão apresentadas para a ligação.
    ![Selecione a autenticação integrada do AD][11]

2. Clique no botão **Opções** e, na página **Propriedades da ligação** , na caixa **ligar à base de dados** , escreva o nome da base de dados de utilizador que pretende ligar.
    ![Selecione o nome da base de dados][13]


#### <a name="connect-using-active-directory-password-authentication"></a>Ligar utilizando a autenticação de palavra-passe do Active Directory

Utilize este método quando ligar-se com um nome principal do Azure AD utilizando o Azure AD gerido domínio. Também pode utilizar para a conta federada sem acesso ao domínio, por exemplo, quando trabalhar de forma remota.

Utilize este método se tiver sessão iniciada Windows com as credenciais a partir de um domínio que não é Federado com Azure ou quando utilizando a autenticação do Azure AD utilizando Azure AD com base no inicial ou o domínio de cliente.

1. Inicie o Management Studio ou ferramentas de dados e na caixa de diálogo **ligar ao servidor** (ou **ligar ao motor de base de dados**), na caixa de **autenticação** , selecione **A autenticação de palavra-passe do Active Directory**.
2. Na caixa **nome de utilizador** , escreva o seu nome de utilizador do Azure Active Directory no formato **username@domain.com**. Tem de ser uma conta a partir do Azure Active Directory ou uma conta a partir de um domínio federar com o Azure Active Directory.
3. Na caixa **palavra-passe** , escreva a palavra-passe de utilizador da conta do Azure Active Directory ou Federado com a conta de domínio.
    ![Seleccionar a autenticação de palavra-passe AD][12]

4. Clique no botão **Opções** e, na página **Propriedades da ligação** , na caixa **ligar à base de dados** , escreva o nome da base de dados de utilizador que pretende ligar. (Consulte o gráfico a opção anterior).


### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>Criar um utilizador de base de dados do Azure AD contida numa base de dados de utilizador

Para criar um Azure AD-utilizador com base em contidas da base de dados (que não seja o administrador do servidor que é o proprietário da base de dados), ligar à base de dados com uma identidade do Azure AD, como um utilizador com, pelo menos, a permissão de **Alterar qualquer utilizador** . Em seguida, utilize a seguinte sintaxe Transact-SQL:

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name* pode ser nome principal de utilizador de um utilizador do Azure AD ou o nome a apresentar de um grupo do Azure AD.

**Exemplos:** Para criar uma base de dados contida utilizador que representa um Azure AD federado ou gerido utilizador do domínio:

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Para criar um utilizador de base de dados contidas que representa um Azure AD ou federado grupo de domínio, fornece o nome de apresentação de um grupo de segurança:

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Para criar um utilizador de base de dados contidas que representa uma aplicação que liga-se através de um token de Azure AD:

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

Para mais informações sobre a criação de utilizadores de base de dados contidas com base em identidades do Azure Active Directory, consulte o artigo [Criar utilizador (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).


> [AZURE.NOTE] Remover o administrador do Azure Active Directory para Azure SQL Server impede que qualquer utilizador de autenticação do Azure AD estabelecer ligação ao servidor. Se necessário, inutilizável utilizadores do Azure AD podem ser largados manualmente por um administrador da base de dados SQL.

Quando cria um utilizador de base de dados, esse utilizador recebe a permissão de **Ligar** e pode ligar-se à base de dados como um membro da função **público** . Inicialmente as permissões apenas disponíveis para o utilizador são todas as permissões atribuídas à função **público** ou quaisquer permissões concedidas a qualquer grupos do Windows que estão membro. Depois de aprovisionar um utilizador de base de dados contidos com base em AD Azure, pode conceder ao utilizador permissões adicionais, da mesma forma como conceder permissão para qualquer outro tipo de utilizador. Normalmente, conceder permissões para funções de base de dados e adicionar utilizadores a funções. Para mais informações, consulte o artigo [Noções básicas de permissão do motor da base de dados](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para mais informações sobre as funções de base de dados SQL especiais, consulte o artigo [Gerir bases de dados e inícios de sessão de base de dados do SQL Azure](sql-database-manage-logins.md).
Um utilizador de domínio federado é importado para um domínio de gerir, tem de utilizar a identidade do domínio gerido.

> [AZURE.NOTE] Utilizadores do Azure AD são marcados nos metadados da base de dados com o tipo E (EXTERNAL_USER) e para grupos com o tipo de X (EXTERNAL_GROUPS). Para mais informações, consulte o artigo [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## <a name="7-connect-by-using-azure-ad-identities"></a>7. ligar-se ao utilizar identidades do Azure AD

Autenticação do Active Directory Azure suporta os seguintes métodos de ligar a uma base de dados utilizando identidades do Azure AD:

- Utilizar a autenticação do Windows integrada
- Utilizar um nome principal do Azure AD e uma palavra-passe
- Utilizando a autenticação do token de aplicação

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1. Ligar utilizando a autenticação integrada (Windows)

Para utilizar a autenticação do Windows integrada, tem de ser federado do Active Directory do seu domínio com o Azure Active Directory. Tem de executar a aplicação de cliente (ou um serviço) que liga à base de dados num computador associado ao domínio em credenciais de domínio de um utilizador.

Para ligar a uma base de dados utilizando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação na cadeia de ligação de base de dados tem de estar definida para integradas do Active Directory. Exemplo de código c# seguinte utiliza ADO .NET.

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Nota a ligação de palavra-chave de cadeia ``Integrated Security=True`` não é suportada para ligar à base de dados do SQL Azure.
Tenha em atenção que, quando efetuar uma ligação ODBC será necessário remover espaços e definir a autenticação para 'ActiveDirectoryIntegrated'.

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2. Estabelecer ligação com um nome principal do Azure AD e uma palavra-passe
Para ligar a uma base de dados utilizando a autenticação integrada e uma identidade do Azure AD, tem de estar definida para Active Directory palavra-passe a palavra-chave de autenticação. Tem de conter o ID de utilizador/UID e palavras-chave de palavra-passe/PWD e os valores a cadeia de ligação. Exemplo de código c# seguinte utiliza ADO .NET.

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Saiba mais sobre métodos de autenticação do Azure AD utilizar os exemplos de código de demonstração disponíveis na [Demonstração de GitHub de autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).


### <a name="73-connecting-with-an-azure-ad-token"></a>7.3 estabelecer ligação com um token de Azure AD
Este método de autenticação permite que os serviços de camada ligar à base de dados do SQL Azure ou armazém de dados do SQL Azure através da obtenção um token a partir do Azure Active Directory (AAD). Permite-sofisticados cenários, incluindo a autenticação baseada em certificados. Tem de concluir quatro passos básicos para utilizar a autenticação do token Azure AD:

1. Registe-se a aplicação com o Azure Active Directory e obter o id do cliente para o seu código. 
2. Crie um utilizador de base de dados que representa a aplicação. (Concluídos anteriormente no passo 6).
3. Crie um certificado sobre o computador de cliente será executado a aplicação.
4. Adicione o certificado como uma chave para a sua aplicação.

Cadeia de ligação de exemplo:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, consulte o [Blogue de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/).

### <a name="connecting-with-sqlcmd"></a>Ligar-se com sqlcmd  
As seguintes instruções, ligar utilizando a versão 13.1 do sqlcmd, que se encontra disponível a partir do [Centro de transferências](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>Consulte também

[Gerir bases de dados e inícios de sessão numa base de dados do Azure SQL](sql-database-manage-logins.md)

[Utilizadores de base de dados contidas](https://msdn.microsoft.com/library/ff929071.aspx)

[Criar utilizador (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

