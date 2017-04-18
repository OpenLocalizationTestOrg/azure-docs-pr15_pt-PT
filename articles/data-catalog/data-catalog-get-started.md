<properties
    pageTitle="Introdução ao catálogo de dados | Microsoft Azure"
    description="Tutorial de fim para apresentar as capacidades de catálogo de dados do Azure e cenários."
    documentationCenter=""
    services="data-catalog"
    authors="steelanddata"
    manager="jhubbard"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/20/2016"
    ms.author="spelluru"/>

# <a name="get-started-with-azure-data-catalog"></a>Introdução ao catálogo de dados do Azure
Azure catálogo de dados é um serviço de nuvem totalmente geridas que serve de um sistema de registo e sistema de deteção de activos de dados da empresa. Para obter uma descrição detalhada, consulte o artigo [o que é o Azure catálogo de dados](data-catalog-what-is-data-catalog.md).

Neste tutorial ajuda-o artigo Introdução ao Azure catálogo de dados. Execute os seguintes procedimentos neste tutorial:

| Procedimento | Descrição |
| :--- | :---------- |
| [Catálogo de dados de aprovisionar](#provision-data-catalog) | Este procedimento, pode Aprovisiona ou configurar o catálogo de dados do Azure. O que fazer este passo apenas se não tiver sido configurado o catálogo antes. Pode ter apenas uma catálogo de dados por organização (domínio do Microsoft Azure Active Directory) apesar de existirem múltiplas subscrições associadas à sua conta Azure. |
| [Registe-se a elementos de dados](#register-data-assets) | Este procedimento, registar elementos de dados a partir da base de dados de exemplo AdventureWorks2014 com o catálogo de dados. O registo é o processo de extrair chave metadados estruturais como nomes, tipos e localizações da origem de dados e copiar esse metadados para o catálogo. A origem de dados e elementos de dados permanecem onde estiverem, mas os metadados são utilizado pelo catálogo para torná-las mais facilmente detetável e compreensíveis. |
| [Descobrir o elementos de dados](#discover-data-assets) | Este procedimento, utiliza o portal do catálogo de dados do Azure para descobrir elementos de dados que foram registados no passo anterior. Depois de uma origem de dados foi registada com o catálogo de dados do Azure, respectivos metadados são indexado pelo serviço para que os utilizadores podem procurar facilmente os dados que necessitam. |
| [Anotar elementos de dados](#annotate-data-assets) | Este procedimento, fornecer anotações (informações como descrições, etiquetas, documentação ou especialistas) para os ativos de dados. Estas informações suplementam os metadados extraídos da origem de dados e para efetuar a origem de dados mais compreensíveis a mais pessoas. |
| [Ligar a elementos de dados](#connect-to-data-assets) | Este procedimento, abra elementos de dados nas ferramentas de cliente integrada (como o Excel e as ferramentas de dados do SQL Server) e uma ferramenta que não sejam integrados (SQL Server Management Studio). |
| [Gerir elementos de dados](#manage-data-assets) | Este procedimento, define cópias de segurança para os seus ativos de dados. Catálogo de dados não dar aos utilizadores acesso aos dados própria. O proprietário da origem de dados controla o acesso a dados. <br/><br/> Com o catálogo de dados, pode descobrir origens de dados e ver os **metadados** relacionados com as origens de registadas no catálogo de. Poderão existir situações, no entanto, onde as origens de dados deverão estar visíveis apenas para utilizadores específicos ou para membros de grupos específicos. Para estes cenários, pode utilizar o catálogo de dados para obter a propriedade de elementos de dados registadas no catálogo e controlar a visibilidade de activos que é o proprietário. |
| [Remover elementos de dados](#remove-data-assets) | Este procedimento, saiba como remover elementos de dados a partir do catálogo de dados. |  

## <a name="tutorial-prerequisites"></a>Pré-requisitos de iniciação

### <a name="azure-subscription"></a>Subscrição do Azure
Para configurar o catálogo de dados do Azure, tem de ser o proprietário ou coproprietário de uma subscrição do Azure.

Subscrições do Azure ajudá-lo a organizar o acesso aos recursos de serviço de nuvem como o catálogo de dados do Azure. Também pode controlar como a utilização de recursos é comunicada, de ajudam faturada e paga. Cada subscrição pode têm uma configuração de faturação e de pagamento diferente, pelo que pode ter diferentes subscrições e os diferentes planos por departamento, project, regional office e assim sucessivamente. Cada serviço em nuvem pertence a uma subscrição e, tem de ter uma subscrição antes de configurar o catálogo de dados do Azure. Para saber mais, consulte o artigo [Gerir contas, subscrições e funções administrativas](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Se não tiver uma subscrição, pode criar uma conta de avaliação gratuita apenas de duas minutos. Consulte o artigo [Versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.

### <a name="azure-active-directory"></a>Azure Active Directory
Para configurar o catálogo de dados do Azure, tem de ser assinado com uma conta de utilizador do Azure Active Directory (Azure AD). Tem de ser o proprietário ou coproprietário de uma subscrição do Azure.  

Azure AD fornece uma forma fácil para a sua empresa gerir a identidade e de acesso, tanto na nuvem e no local. Pode utilizar uma única conta escolar ou profissional para iniciar sessão em qualquer aplicação web na nuvem ou no local. Catálogo de dados Azure utiliza Azure AD para autenticar iniciar sessão. Para saber mais, consulte o artigo [o que é o Azure Active Directory](../active-directory/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Configuração de política do Azure Active Directory

Poderá encontrar uma situação onde pode iniciar sessão portal do catálogo de dados do Azure, mas quando tenta iniciar sessão para a ferramenta de registo da origem de dados, encontrar uma mensagem de erro que o impede de iniciar sessão. Este erro pode ocorrer quando estiver a rede da empresa ou quando estiver a ligar a partir de fora da rede da empresa.

A ferramenta de registo utiliza a *autenticação de formulários* para validar suplementos utilizador de início de sessão contra Azure Active Directory. Para o início de sessão efetuada com êxito, um administrador do Azure Active Directory tem de ativar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, pode ativar a autenticação em separado para intranet da extranet ligações e, conforme apresentado na seguinte imagem. Erros de início de sessão no poderão ocorrer se autenticação de formulários não estiver ativada para a rede a partir do qual o utilizador estiver a estabelecer ligação.

 ![Política de autenticação global do Azure Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obter mais informações, consulte [Configurar políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Catálogo de dados de aprovisionar
Pode aprovisionar o catálogo de dados de apenas uma por organização (domínio Azure Active Directory). Por isso, se o proprietário ou coproprietário de uma subscrição do Azure que pertença para este domínio Azure Active Directory já tiver criado um catálogo, não poderá criar um catálogo novamente, mesmo se tiver múltiplas subscrições Azure. Para testar se um catálogo de dados foi criado por um utilizador no seu domínio do Azure Active Directory, aceda à [home page do catálogo de dados do Azure](http://azuredatacatalog.com) e verifique se vir o catálogo. Se já tiver sido criado um catálogo para si, ignore o procedimento seguinte e ir para a secção seguinte.    

1. Aceda à [página de serviço do catálogo de dados](https://azure.microsoft.com/services/data-catalog) e clique em **começar**.

    ![Azure catálogo de dados – página de destino de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Inicie sessão com uma conta de utilizador é o proprietário ou coproprietário de uma subscrição do Azure. Consulte a seguinte página após iniciar sessão.

    ![Azure catálogo de dados – catálogo de dados de aprovisionar](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Especifique um **nome** para o catálogo de dados, a **subscrição** que pretende utilizar e a **localização** para o catálogo.
4. Expanda **os preços** e selecione um catálogo de dados do Azure **edition** (Free ou padrão).
    ![Azure catálogo de dados – selecione edition](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Expanda o **Catálogo de utilizadores** e clique em **Adicionar** para adicionar utilizadores para o catálogo de dados. São automaticamente adicionados a este grupo.
    ![Catálogo de dados Azure – utilizadores](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Expanda o **Catálogo de administradores** e clique em **Adicionar** para adicionar administradores adicionais para o catálogo de dados. São automaticamente adicionados a este grupo.
    ![Catálogo de dados Azure – administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Clique em **Criar Catálogo** para criar o catálogo de dados para a sua organização. Consulte a home page do catálogo de dados após ter sido criada.
    ![Catálogo de dados Azure – criado](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Localizar um catálogo de dados no portal do Azure
1. No separador em separado no browser ou numa janela do browser web em separado, aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a mesma conta que utilizou para criar o catálogo de dados no passo anterior.
2. Selecione **Procurar** e, em seguida, clique em **Catálogo de dados**.

    ![Azure catálogo de dados – procure Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) verá o catálogo de dados que criou.

    ![Azure catálogo de dados – catálogo vista na lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
4.  Clique no catálogo que criou. Consulte pá o **Catálogo de dados** no portal.

    ![Azure catálogo de dados – pá no portal ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
5. Pode ver as propriedades do catálogo de dados e atualizá-las. Por exemplo, clique em **níveis de preços** e altere a edição.

    ![Azure catálogo de dados – preços de camadas](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Base de dados de exemplo da Adventure Works
Neste tutorial, registe o elementos de dados (tabelas) a partir da base de dados de exemplo AdventureWorks2014 para o motor de base de dados do SQL Server, mas pode utilizar uma origem de dados suportados se preferir trabalhar com dados que são familiar e relevantes para a sua função. Para obter uma lista de origens de dados suportados, consulte o artigo [origens de dados suportados](data-catalog-dsr.md).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Instalar a base de dados da Adventure Works 2014 OLTP
A base de dados do Adventure Works suporta padrão cenários de processamento de transações onlinehttps para um fabricante de bicicletas fictício (da Adventure Works ciclos), que inclui produtos, compras e vendas. Neste tutorial, registar informações acerca de produtos para o catálogo de dados do Azure.

Para instalar a base de dados de exemplo de Adventure Works:

1. Transfira [Adventure Works 2014 total da base de dados Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) no CodePlex.
2. Para restaurar a base de dados no seu computador, siga as instruções em [Restaurar uma cópia de segurança da base de dados utilizando o SQL Server Management Studio](http://msdn.microsoft.com/library/ms177429.aspx)ou ao seguir estes passos:
    1. Abra o SQL Server Management Studio e ligue ao motor de base de dados do SQL Server.
    2. **Bases de dados** com o botão direito e clique em **Restaurar a base de dados**.
    3. Em **Restaurar a base de dados**, clique na opção de **dispositivo** de **origem** e clique em **Procurar**.
    4. Em **Selecione dispositivos de cópia de segurança**, clique em **Adicionar**.
    5. Vá para a pasta onde o ficheiro de **AdventureWorks2014.bak** , selecione o ficheiro e clique em **OK** para fechar a caixa de diálogo **Localizar o ficheiro de cópia de segurança** .
    6. Clique em **OK** para fechar a caixa de diálogo **Selecione dispositivos de cópia de segurança** .    
    7. Clique em **OK** para fechar a caixa de diálogo **Restaurar a base de dados** .

Agora pode registar elementos de dados a partir da base de dados de exemplo da Adventure Works utilizando o catálogo de dados do Azure.

## <a name="register-data-assets"></a>Registe-se a elementos de dados

Neste exercício, utilize a ferramenta de registo para registar elementos de dados da base de dados da Adventure Works com o catálogo. O registo é o processo de extrair chave metadados estruturais como nomes, tipos e localizações de origem de dados e os ativos contém e copiar esse metadados para o catálogo. A origem de dados e elementos de dados permanecem onde estiverem, mas os metadados são utilizado pelo catálogo para torná-las mais facilmente detetável e compreensíveis.

### <a name="register-a-data-source"></a>Registar uma origem de dados

1.  Aceda à [home page do catálogo de dados do Azure](https://azuredatacatalog.com) e clique em **Publicar dados**.

    ![Catálogo de dados Azure – publicar dados do botão](media/data-catalog-get-started/data-catalog-publish-data.png)

2.  Clique em **Iniciar aplicação** para transferir, instalar e executar a ferramenta de registo no seu computador.

    ![Catálogo de dados Azure - botão de iniciação](media/data-catalog-get-started/data-catalog-launch-application.png)

3. Na página de **boas-vindas** , clique em **Iniciar sessão** e introduza as suas credenciais.    

    ![Catálogo de dados Azure - página de boas-vindas](media/data-catalog-get-started/data-catalog-welcome-dialog.png)

4. Na página de **Catálogo de dados do Microsoft Azure** , clique em **Do SQL Server** e **seguinte**.

    ![Azure catálogo de dados – origens de dados](media/data-catalog-get-started/data-catalog-data-sources.png)

5.  Introduza as propriedades da ligação do SQL Server para **AdventureWorks2014** (consulte o seguinte exemplo) e clique em **Ligar**.

    ![Azure catálogo de dados – as definições de ligação do SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

6.  Registe-se os metadados da sua elementos de dados. Neste exemplo, registar objetos de **Produção/produto** de espaço de nomes de produção AdventureWorks:

    1. Na árvore da **Hierarquia de servidor** , expanda **AdventureWorks2014** e clique em **produção**.
    2. Selecione **produto**, **ProductCategory**, **ProductDescription**e **ProductPhoto** , utilizando Ctrl + clique.
    3. Clique em **Mover seta selecionada** (**>**). Esta ação move todos os objetos selecionados para a lista de **objetos sejam registados** .

        ![Azure tutorial de catálogo de dados – navegue e selecionar objetos](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
    4. Selecione **incluir uma pré-visualização** para incluir uma pré-visualização de instantâneo dos dados. O instantâneo inclui até 20 registos de cada tabela e -é copiado para o catálogo.
    5. Selecione **Incluir perfil de dados** para incluir um instantâneo das estatísticas do objeto para o perfil de dados (por exemplo: média, mínimos e máximos valores de uma coluna, o número de linhas).
    6. No campo **Adicionar marcas** , introduza **Aventura Lavor, ciclos**. Esta ação adiciona tags de pesquisa para estes elementos de dados. Etiquetas são uma excelente forma para ajudar os utilizadores a encontrar uma origem de dados registadas.
    7. Especifique o nome de um **especialista** nestes dados (opcionais).

        ![Azure tutorial do catálogo de dados – objetos sejam registados](media/data-catalog-get-started/data-catalog-objects-register.png)

    8. Clique em **Registe-se**. Catálogo de dados Azure regista os objetos selecionados. Neste exercício, os objetos selecionados da Adventure Works são registados. A ferramenta de registo extrai metadados do elemento de dados e copia os dados para o serviço de catálogo de dados do Azure. Os dados permanecem onde reside actualmente e permanece sob o controlo dos administradores e políticas do sistema atual.

        ![Azure catálogo de dados – objectos registados](media/data-catalog-get-started/data-catalog-registered-objects.png)

    9. Para ver os objetos de origem de dados registadas, clique em **Vista de Portal**. No portal do catálogo de dados do Azure, confirme que está a ver todas as tabelas de quatro e a base de dados na vista de grelha.

        ![Objetos no portal do catálogo de dados do Azure ](media/data-catalog-get-started/data-catalog-view-portal.png)


Neste exercício, registados objetos de base de dados de exemplo da Adventure Works para que estes podem ser facilmente identificados pelos utilizadores em toda a organização. No seguinte exercício, saiba como descobrir elementos de dados registadas.

## <a name="discover-data-assets"></a>Descobrir o elementos de dados
Deteção no catálogo de dados do Azure utiliza dois mecanismos primários: Procurar e filtrar.

Procurar foi concebida para ser intuitivas e avançadas. Por predefinição, os termos de pesquisa são comparados com qualquer propriedade no catálogo, incluindo anotações fornecidos pelo utilizador.

A filtragem foi concebida para completar a procurar. Pode selecionar características específicas como especialistas, o tipo de origem de dados, o tipo de objeto e o etiquetas para ver a correspondência de dados de activos e para restringir resultados da pesquisa para a correspondência de recursos.

Ao utilizar uma combinação de pesquisa e filtragem, pode navegar rapidamente as origens de dados que foi registadas com Azure catálogo de dados para descobrir os ativos de dados que precisa.

Neste exercício, utiliza o portal do catálogo de dados do Azure para descobrir elementos de dados registadas no exercício anterior. Consulte o artigo [referência de sintaxe de pesquisa do catálogo de dados](https://msdn.microsoft.com/library/azure/mt267594.aspx) para obter detalhes sobre a sintaxe de pesquisa.

Seguem-se alguns exemplos para descobrir elementos de dados no catálogo.  

### <a name="discover-data-assets-with-basic-search"></a>Descobrir o elementos de dados com pesquisa básica
Pesquisa básica ajuda-o a um catálogo de pesquisa ao utilizar um ou mais termos de pesquisa. Os resultados são quaisquer activos que correspondem aos qualquer propriedade com um ou mais os termos especificados.

1. Clique em **base** no portal do catálogo de dados do Azure. Se tiver fechado o browser, aceda à [home page do catálogo de dados do Azure](https://www.azuredatacatalog.com).
2. Na caixa de pesquisa, introduza `cycles` e prima **ENTER**.

    ![Azure catálogo de dados – pesquisa de texto básico](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Confirme que está a ver todas as tabelas de quatro e a base de dados (AdventureWorks2014) nos resultados de. Pode alternar entre a **vista de grelha** e **vista de lista** ao clicar em botões na barra de ferramentas, tal como apresentado na seguinte imagem. Repare que a palavra-chave de pesquisa está realçada nos resultados da pesquisa porque **se encontra a opção de **Realce** **. Também pode especificar o número de **resultados por página** nos resultados da pesquisa.

    ![Azure catálogo de dados – os resultados da pesquisa de texto básico](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)

    O painel de **pesquisas** é à esquerda e o painel de **Propriedades** estiver à direita. No painel de **pesquisas** , pode alterar os critérios de pesquisa e filtrar os resultados. O painel de **Propriedades** apresenta as propriedades de um objeto selecionado na grelha ou lista.

4. Clique em **produto** nos resultados da pesquisa. Clique na **pré-visualização**, **colunas**, **Perfil de dados**e separadores de **documentação** ou clique na seta para expandir o painel inferior.  

    ![Azure catálogo de dados – painel inferior](media/data-catalog-get-started/data-catalog-data-asset-preview.png)

    No separador **pré-visualização** , verá uma pré-visualização dos dados na tabela **Product** .  
5. Clique no separador de **colunas** para localizar os detalhes acerca de colunas (tal como o **nome** e **tipo de dados**) no elemento de dados.
6. Clique no separador de **Perfil de dados** para ver a criação de perfis de dados (por exemplo: número de linhas, tamanho de dados ou o valor mínimo numa coluna) no elemento de dados.
7. Filtre os resultados por através **dos filtros** à esquerda. Por exemplo, clique em **tabela** para o **Tipo de objeto**e, verá apenas as quatro tabelas, não a base de dados.

    ![Azure catálogo de dados – nos resultados de pesquisa de filtro](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Descobrir o ativos de dados com o controlo do âmbito propriedade
Propriedade controlo do âmbito ajuda-o a descobrir elementos de dados onde o termo de pesquisa é correspondido com a propriedade especificada.

1. Limpe o filtro de **tabela** em **Tipo de objeto** em **filtros**.  
2. Na caixa de pesquisa, introduza `tags:cycles` e prima **ENTER**. Consulte o artigo [referência de sintaxe de pesquisa do catálogo de dados](https://msdn.microsoft.com/library/azure/mt267594.aspx) para todas as propriedades que pode utilizar para procurar no catálogo de dados.
3. Confirme que está a ver todas as tabelas de quatro e a base de dados (AdventureWorks2014) nos resultados de.  

    ![Catálogo de dados – propriedade controlo do âmbito os resultados da pesquisa](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Guardar a pesquisa
1. No painel de **pesquisas** na secção **Atual de pesquisa** , introduza um nome para a pesquisa e clique em **Guardar**.

    ![Azure catálogo de dados – guardar pesquisa](media/data-catalog-get-started/data-catalog-save-search.png)
2. Confirme que a procura guardada é apresentada em **Procuras guardadas**.

    ![Azure catálogo de dados – procuras guardado](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Selecione uma das ações que pode realizar na procura guardada (**Mudar o nome**, **Eliminar**, **Guardar como predefinido** procura).

    ![Azure catálogo de dados – guardado opções de pesquisa](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Operadores booleanos
Pode ampliar ou refinar a sua pesquisa com operadores booleanos.

1. Na caixa de pesquisa, introduza `tags:cycles AND objectType:table`, e prima **ENTER**.
2. Confirme que está a ver apenas a tabelas (não a base de dados) nos resultados de.  

    ![Azure catálogo de dados – operador booleano na pesquisa](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Agrupamento entre parênteses
Ao agrupar entre parênteses, pode agrupar partes de consulta para alcançar isolamento lógico, especialmente juntamente com operadores booleanos.

1. Na caixa de pesquisa, introduza `name:product AND (tags:cycles AND objectType:table)` e prima **ENTER**.
2. Confirme que está a ver apenas a tabela **produto** nos resultados da pesquisa.

    ![Azure catálogo de dados – pesquisa de agrupamento](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Operadores de comparação
Com os operadores de comparação, pode utilizar as comparações que não seja igualdade para as propriedades que têm tipos de dados numéricos e de data.

1. Na caixa de pesquisa, introduza `lastRegisteredTime:>"06/09/2016"`.
2. Limpe o filtro de **tabela** em **Tipo de objeto**.
3. Prima **ENTER**.
4. Confirme que está a ver as tabelas de **produto**, **ProductCategory**, **ProductDescription**e **ProductPhoto** e a base de dados AdventureWorks2014 registado nos resultados da pesquisa.

    ![Azure catálogo de dados – os resultados da pesquisa de comparação](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Veja [como descobrir elementos de dados](data-catalog-how-to-discover.md) para obter informações detalhadas sobre a descobrir elementos de dados e de [referência de sintaxe de pesquisa do catálogo de dados](https://msdn.microsoft.com/library/azure/mt267594.aspx) para a sintaxe de pesquisa.

## <a name="annotate-data-assets"></a>Anotar elementos de dados
Neste exercício, utiliza o portal do catálogo de dados do Azure para anotar (adicionar informações como descrições, etiquetas ou especialistas) ter registado anteriormente no catálogo de elementos de dados. As anotações completam e melhoram os metadados estruturais extraídos da origem de dados durante o registo e facilitam os ativos dados muito detetar e compreender.

Neste exercício, anotar um activo dados único (ProductPhoto). Adicionar um nome amigável e uma descrição para o elemento ProductPhoto dados.  

1.  Aceda à [home page do catálogo de dados do Azure](https://www.azuredatacatalog.com) e pesquise com `tags:cycles` para encontrar os ativos dados ter registado.  
2. Clique em **ProductPhoto** nos resultados da pesquisa.  
3. Introduza **imagens do produto** de **Nome amigável** e **fotografias de produto para materiais de marketing** para a **Descrição**.

    ![Azure catálogo de dados – ProductPhoto descrição](media/data-catalog-get-started/data-catalog-productphoto-description.png)

    A **Descrição** ajuda-o a outras pessoas descobrir e compreender o motivo e como utilizar os elementos de dados selecionados. Também pode adicionar mais etiquetas e ver as colunas. Agora que pode experimentar para descobrir elementos de dados utilizando os metadados descritivo que adicionou ao catálogo de filtragem e procura.

Também pode fazer o seguinte nesta página:

- Adicione especialistas para o item de dados. Clique em **Adicionar** na área de **especialistas** .
- Adicione marcas ao nível do conjunto de dados. Clique em **Adicionar** na área de **etiquetas** . Uma etiqueta pode ser uma etiqueta de utilizador ou uma etiqueta de glossário. Edition padrão do catálogo de dados inclui um glossário de empresas que ajuda a administradores de catálogo, definir uma taxonomia negócio central. Utilizadores do catálogo, em seguida, podem anotar elementos de dados com os termos de glossário. Para obter mais informações, consulte o artigo [como configurar o glossário de negócio para regida etiquetas de rede](data-catalog-how-to-business-glossary.md)
- Adicione etiquetas ao nível da coluna. Clique em **Adicionar** em **etiquetas** para a coluna que pretende para fazer anotações.
- Adicione descrição a nível da coluna. Introduza a **Descrição** de uma coluna. Também pode ver os metadados de descrição extraídos de origem de dados.
- Adicione informações de **Pedir acesso** que mostra aos utilizadores como pedir acesso a elementos de dados.

    ![Azure catálogo de dados – adicionar etiquetas, descrições](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)

- Selecione o separador de **documentação** e fornecer documentação para o item de dados. Com a documentação do catálogo de dados do Azure, pode utilizar o catálogo de dados como um repositório de conteúdo para criar uma narração completa dos seus ativos de dados.

    ![Azure catálogo de dados – separador de documentação](media/data-catalog-get-started/data-catalog-documentation.png)


Também pode adicionar uma anotação para vários elementos de dados. Por exemplo, pode selecionar todos os elementos de dados que se registou e especificar um especialista das mesmas.

![Azure catálogo de dados – anotar vários elementos de dados](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Catálogo de dados Azure suporta uma abordagem de abastecimento de muitas pessoas para anotações. Qualquer utilizador do catálogo de dados pode adicionar etiquetas (utilizador ou glossário), descrições e outros metadados, para que qualquer utilizador com uma perspetiva num elementos de dados e a utilização da mesma possa ter essa perspectiva capturada e disponível para outros utilizadores.

Veja [como anotar elementos de dados](data-catalog-how-to-annotate.md) para obter informações detalhadas sobre anotar elementos de dados.

## <a name="connect-to-data-assets"></a>Ligar a elementos de dados
Neste exercício, abra elementos de dados numa ferramenta de cliente integrada (Excel) e uma ferramenta que não sejam integrados (SQL Server Management Studio) utilizando as informações de ligação.

> [AZURE.NOTE] É importante lembrar-se de que o catálogo de dados Azure não dão-lhe acesso à origem de dados reais — simplesmente torna mais fácil para descobrir e compreendê-lo. Ao ligar a uma origem de dados, a aplicação de cliente que escolher utiliza as suas credenciais do Windows ou pede-lhe para introduzir as credenciais conforme necessário. Se não lhe anteriormente foi concedida acesso à origem de dados, tem de ser concedido acesso antes de poder ligar.

### <a name="connect-to-a-data-asset-from-excel"></a>Ligar a um recurso de dados a partir do Excel

1. Selecione o **produto** dos resultados da pesquisa. Clique em **Abrir** na barra de ferramentas e clique em **Excel**.

    ![Azure catálogo de dados – ligar a elementos de dados](media/data-catalog-get-started/data-catalog-connect1.png)
2. Clique em **Abrir** na janela de pop-up de transferência. Esta experiência pode variar consoante o browser.

    ![Azure catálogo de dados – transferido o ficheiro de ligação do Excel](media/data-catalog-get-started/data-catalog-download-open.png)
3. Na janela de **Aviso de segurança do Microsoft Excel** , clique em **Ativar**.

    ![Azure catálogo de dados – popup de segurança do Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Mantenha as predefinições na caixa de diálogo **Importar dados** e clique em **OK**.

    ![Azure catálogo de dados – importar dados do Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Ver a origem de dados no Excel.

    ![Azure catálogo de dados – tabela product no Excel](media/data-catalog-get-started/data-catalog-connect2.png)

Neste exercício, ligado para elementos de dados descobertos ao utilizar o catálogo de dados do Azure. Com o portal de catálogo de dados do Azure, pode ligar diretamente ao utilizar as aplicações de cliente integradas **Abrir no** menu. Também pode ligar com qualquer aplicação que escolher, utilizando as informações de localização da ligação incluídas nos metadados ativo. Por exemplo, pode utilizar o SQL Server Management Studio para se ligar à base de dados AdventureWorks2014 para aceder aos dados nos dados activos registados neste tutorial.

1. Abra o **SQL Server Management Studio**.
2. Na caixa de diálogo **ligar ao servidor** , introduza o nome do servidor a partir de **Propriedades** no portal do catálogo de dados do Azure.
3. Utilize autenticação adequado e as credenciais para aceder o elementos de dados. Se não tiver o access, utilize informações no campo **Pedir acesso** obtê-lo.

    ![Azure catálogo de dados – pedir acesso](media/data-catalog-get-started/data-catalog-request-access.png)

Clique em **Cadeias de ligação da vista** para ver e copiar ADF.NET, ODBC e OLEDB cadeias de ligação para a área de transferência para utilizar na sua aplicação.

## <a name="manage-data-assets"></a>Gerir elementos de dados
Neste passo, consulte como configurar a segurança para os seus ativos de dados. Catálogo de dados não dar aos utilizadores acesso aos dados própria. O proprietário da origem de dados controla o acesso a dados.

Pode utilizar o catálogo de dados para detetar origens de dados e para ver os metadados relacionados com as origens de registadas no catálogo de. Poderão existir situações, no entanto, onde origens de dados deverão estar apenas visíveis para utilizadores específicos ou para membros de grupos específicos. Para estes cenários, pode utilizar o catálogo de dados para obter a propriedade de elementos de dados registadas dentro do catálogo de e para o controlo, em seguida, a visibilidade dos ativos é o proprietário.

> [AZURE.NOTE] As funcionalidades de gestão descritas neste exercício estão disponíveis apenas o Standard Edition do Azure catálogo de dados e não no Free Edition.
No catálogo de dados do Azure, pode obter a propriedade de elementos de dados, adicionar os proprietários de cocriação para elementos de dados e definir a visibilidade de elementos de dados.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Obter a propriedade de elementos de dados e restringir visibilidade

1. Aceda à [home page do catálogo de dados do Azure](https://www.azuredatacatalog.com). Na caixa de texto de **pesquisa** , introduza `tags:cycles` e prima **ENTER**.
2. Clique num item na lista de resultados e clique em **Obter propriedade** na barra de ferramentas.
3. Na secção de **Gestão** do painel **Propriedades** , clique em **Obter propriedade**.

    ![Azure catálogo de dados – obter propriedade](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Para restringir a visibilidade, selecione **os proprietários e estes utilizadores** na secção **visibilidade** e clique em **Adicionar**. Introduza os endereços de e-mail de utilizador na caixa de texto e prima **ENTER**.

    ![Azure catálogo de dados – restringir o acesso](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Remover elementos de dados

Neste exercício, utiliza o portal do catálogo de dados do Azure para remover a pré-visualizar dados de activos dados registadas e eliminar os elementos de dados do catálogo.

No catálogo de dados do Azure, pode eliminar um activo individual ou eliminar vários elementos.

1. Aceda à [home page do catálogo de dados do Azure](https://www.azuredatacatalog.com).
2. Na caixa de texto de **pesquisa** , introduza `tags:cycles` e clique em **ENTER**.
3. Selecione um item na lista de resultados e clique em **Eliminar na barra de ferramentas conforme apresentado na seguinte imagem:**

    ![Azure catálogo de dados – grelha de eliminar item](media/data-catalog-get-started/data-catalog-delete-grid-item.png)

    Se estiver a utilizar a vista de lista, é a caixa de verificação à esquerda do item, conforme apresentado na seguinte imagem:

    ![Azure catálogo de dados – Eliminar item de lista](media/data-catalog-get-started/data-catalog-delete-list-item.png)

    Também pode selecionar vários elementos de dados e elimine-os conforme apresentado na seguinte imagem:

    ![Azure catálogo de dados – eliminar vários elementos de dados](media/data-catalog-get-started/data-catalog-delete-assets.png)


> [AZURE.NOTE] O comportamento predefinido do catálogo é permitir que qualquer utilizador para registar uma origem de dados e para permitir que qualquer utilizador eliminar qualquer elementos de dados que foi registado. As capacidades de gestão incluídas no Standard Edition do Azure catálogo de dados do fornecem opções adicionais para que é o proprietário de activos restringir quem pode descobrir dos recursos, a efetuar e restringir quem pode eliminar elementos.


## <a name="summary"></a>Resumo

Neste tutorial, explorou funcionalidades essenciais do catálogo de dados do Azure, incluindo a registar, anotar, a descobrir e gerir elementos de dados da empresa. Agora que concluiu o tutorial, é hora para começar a utilizar. Pode começar a hoje ao registar as origens de dados que e a sua equipa dependem e ao convidar colegas para utilizar o catálogo.

## <a name="references"></a>Referências

- [Como registar elementos de dados](data-catalog-how-to-register.md)
- [Como descobrir elementos de dados](data-catalog-how-to-discover.md)
- [Como anotar elementos de dados](data-catalog-how-to-annotate.md)
- [Como recursos de dados do documento](data-catalog-how-to-documentation.md)
- [Como ligar aos elementos de dados](data-catalog-how-to-connect.md)
- [Como gerir elementos de dados](data-catalog-how-to-manage.md)
