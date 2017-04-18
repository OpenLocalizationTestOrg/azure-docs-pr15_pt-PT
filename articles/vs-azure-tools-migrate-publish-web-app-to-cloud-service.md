<properties
   pageTitle="Como migrar e publicar uma aplicação Web para um serviço em nuvem Azure do Visual Studio | Microsoft Azure"
   description="Saiba como migrar e publicar a sua aplicação web para um serviço em nuvem Azure utilizando o Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Como: migrar e publicar uma aplicação Web para um serviço em nuvem Azure do Visual Studio

Para tirar partido dos serviços de alojamento e escalabilidade do Azure, poderá pretender migrar e publicar a sua aplicação web para um serviço em nuvem Azure. Pode executar uma aplicação web no Azure com mínimas alterações à aplicação existente.

>[AZURE.NOTE] Este tópico é sobre como implementar aos serviços em nuvem, não a web sites. Para obter informações sobre como implementar a web sites, consulte o artigo [Implementar uma aplicação web na aplicação de serviço de Azure](./app-service-web/web-sites-deploy.md).

Para obter uma lista de modelos específicos que são suportadas para Visual c# tanto do Visual Basic, consulte a secção **Suportadas modelos do Project** , mais adiante.

Tem de ativar primeiro a aplicação web do Azure a partir do Visual Studio. A ilustração seguinte mostra os passos principais para publicar a sua aplicação web existente ao adicionar um projeto Azure a utilizar para implementação. Este processo adiciona um projeto Azure com a função de web necessária a sua solução. Com base no tipo de projecto da web que possui, as propriedades do projeto para montagens também são atualizadas se o pacote de serviço requer conjuntos adicionais para implementação.

![Publicar uma aplicação Web do Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE] A **Converter**, comando **Converter para o projeto de serviço de nuvem Azure** é apresentado apenas para o project web na sua solução. Por exemplo, o comando não está disponível para um projeto do Silverlight na sua solução.
Quando criar um pacote de serviço ou publicar a sua aplicação ao Azure, podem ocorrer avisos ou erros. Estes avisos e erros podem ajudar a corrigir problemas antes de implementá para Azure. Por exemplo, poderá receber um aviso sobre uma assemblagem em falta. Para obter mais informações sobre como trata os avisos como erros, consulte [configurar um projeto de serviço de nuvem Azure com o Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Se construir a sua aplicação, executá-la localmente utilizando o emulador cluster ou publique-o Azure, poderá ver a seguinte mensagem de erro na janela da **Lista de erro** : **O caminho especificado, nome de ficheiro ou ambos são demasiado longos**. Este erro ocorre porque o comprimento do nome do projeto Azure completamente qualificado é demasiado longo. O comprimento do nome do projeto, incluindo o caminho completo, não pode ser mais do que 146 carateres. Por exemplo, este é o nome do projeto completo incluindo caminho do ficheiro para um projeto Azure que é criado para uma aplicação do Silverlight: `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Poderá ter que mover a sua solução para um diretório diferente que tem um caminho mais curto para reduzir o comprimento do nome do projeto completamente qualificado.

Para migrar e publicar uma aplicação web Azure a partir do Visual Studio, siga estes passos.

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Ativar uma aplicação Web para a implementação do Azure

### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Para ativar uma aplicação web para a implementação do Azure

1. Para ativar a aplicação web para a implementação do Azure, abra o menu de atalho para um projeto de web na sua solução e selecione Adicionar projeto de implementação do Azure.

    Ocorrem as seguintes ações:

    - Um projeto Azure denominado `<name of the web project>.Azure` é adicionada à solução para a sua aplicação.

    - Uma função de web para o projecto da web é adicionada para este projeto Azure.

    - A propriedade **Cópia Local** está definida como verdadeiro para qualquer montagens que são necessárias para MVC MVC 2, MVC 3, 4 e aplicações de negócio do Silverlight. Esta ação adiciona estes assemblagem para o pacote de serviço que é utilizado para implementação.

  >[AZURE.IMPORTANT] Se tiver outros conjuntos ou ficheiros que são necessários para esta aplicação web, tem de configurar manualmente as propriedades para estes ficheiros. Para obter informações sobre como configurar estas propriedades, consulte a secção **Incluir ficheiros no pacote do serviço** neste artigo.

  >[AZURE.NOTE] Se já existe uma função de web para um projeto de web específica num projeto Azure na solução, **Converter**, a **Converter para o projeto de serviço de nuvem Azure** não é apresentada no menu de atalho para este projeto web.

  Se tiver vários projetos web na sua aplicação web e que pretende criar funções da web para cada projeto web, deve efetuar os passos deste procedimento para cada projeto web. Esta ação cria projetos Azure separados para cada função web. Cada projeto web pode ser publicado separadamente. Em alternativa, pode adicionar manualmente outra função web a um projeto existente Azure na sua aplicação web. Para fazer isto, abrir o menu de atalho para a pasta de **funções** no seu projeto Azure, selecione **Adicionar**, em seguida, **Função projecto numa solução da Web**, selecione o projeto para adicionar como uma função de web e, em seguida, selecione o botão **OK** .

## <a name="use-an-azure-sql-database-for-your-application"></a>Utilizar uma base de dados do Azure SQL para a aplicação

Se tiver uma cadeia de ligação para a sua aplicação web que utiliza uma base de dados do SQL Server que está no local, tem de alterar esta cadeia de ligação para utilizar uma instância da base de dados SQL que aloja Azure em vez disso.

>[AZURE.IMPORTANT] A sua subscrição tem de ativar a utilização de base de dados SQL. Se aceder a sua subscrição a partir do [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), pode determinar que serviços fornece a sua subscrição. As instruções seguintes aplicam-se do lançamento [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885). Se estiver a utilizar o [Azure portal](http://portal.microsoft.com), avance para o procedimento seguinte.

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Para utilizar uma instância de base de dados SQL na sua função web para a cadeia de ligação

1. Para criar uma instância da base de dados SQL no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), siga os passos na seguinte artigo: [criar uma base de dados do SQL Server](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE] Quando configurar regras de firewall para a instância da base de dados SQL, tem de selecionar a caixa de verificação **Permitir que outros serviços Azure aceder a este servidor** .

1. Para criar uma instância da base de dados do SQL para utilizar para a cadeia de ligação, siga os passos na secção seguinte no seguinte artigo: [criar uma base de dados do SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Para copiar a cadeia de ligação ADO.NET a utilizar para a cadeia de ligação, execute os seguintes passos no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).  

  1. Selecione o botão de **base de dados** e, em seguida, abra o nó para a subscrição que utilizou para criar a instância da base de dados SQL.

  1. Para apresentar as instâncias disponíveis da base de dados SQL, escolha o nó de **Bases de dados SQL** .

  1. Para apresentar as propriedades da base de dados, selecione a base de dados. É apresentada a vista de **Propriedades** .

      >[AZURE.NOTE] Se a vista de **Propriedades** não aparecer, poderá ter para o abrir utilizando a barra de divisão.

  1. Para apresentar as cadeias de ligação, escolha o botão de reticências (…) junto a vista.

    É apresentada a caixa de diálogo **Cadeias de ligação** .

  1. Para copiar a cadeia de ligação ADO.NET, realce o texto e escolha as teclas Ctrl + C.

  1. Para fechar a caixa de diálogo, selecione o botão **Fechar** .

1. Para substituir a cadeia de ligação no ficheiro para utilizar esta instância do SQL base de dados Web. config, abra o ficheiro da Web. config, realce a entrada de cadeia de ligação existente e, em seguida, selecione as teclas Ctrl + V. A cadeia de ligação para a instância da base de dados SQL ADO.NET substitui a cadeia de ligação existente.

1. Também tem de adicionar o parâmetro `MultipleActiveResultSets=True` com a cadeia de ligação. A cadeia de ligação deverá ter o seguinte formato:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Opcional) É um método alternativo para alterar a cadeia de ligação diretamente no ficheiro Web. config adicionar uma secção para um dos ficheiros de transformação de Web. config, consoante a configuração da compilação que utilizar para criar o seu pacote de serviço. Abra o ficheiro Web.Debug.Config ou o ficheiro Web.Release.Config. Adicione a secção seguinte para este ficheiro:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Guarde o ficheiro que tenha modificado e voltar a publicar a aplicação.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Para utilizar uma instância da base de dados SQL, utilizando o portal clássico do Azure

1. No [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885), escolha o nó de bases de dados SQL.

  - Se for apresentada a instância da base de dados SQL que pretende utilizar, escolha abri-lo.

  - Se ainda não criou as ocorrências, escolha a ligação adequada e, em seguida, crie uma instância.

1. Depois de abrir ou criar uma instância de base de dados, selecione a ligação de **Cadeias de ligação** .

1. Na parte inferior da página, selecione a ligação para configurar as definições da firewall e aceitar os valores predefinidos ou configurar os valores que necessita.

1. Copie a cadeia de ligação ADO.NET, cole-o para o seu ficheiro Web. config sobre a cadeia de ligação antiga para a base de dados no local e não se esqueça de adicionar `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publicar uma aplicação Web para Azure

### <a name="to-publish-a-web-application-to-azure"></a>Para publicar uma aplicação Web para Azure

1. Para testar a aplicação no desenvolvimento local ambiente utilizando o Azure calcular emulador, abra o menu de atalho para o projeto Azure para a função de web e escolha **Definir como projeto de arranque**. Em seguida, selecione **Depurar**, **Iniciar depuração** (teclado: **F5**).

    A caixa de diálogo **Iniciar o ambiente do Azure depuração** é aberto e a aplicação é iniciada no browser. Para obter detalhes específicos sobre como iniciar cada tipo de aplicação web na emulador cluster, consulte a tabela nesta secção.

1. Para configurar os serviços para a sua aplicação publicar o Azure, tem de ter uma conta Microsoft e uma subscrição do Azure. Utilize os passos no tópico seguinte para configurar os serviços: [preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para publicar a aplicação web às Azure, abra o menu de atalho para o projecto da web e selecione **publicar no Azure**.

    Abre a caixa de diálogo **Publicar aplicações do Azure** e Visual Studio inicia o processo de implementação. Para mais informações sobre como publicar a aplicação, consulte a secção **publicar uma aplicação do Azure a partir do Visual Studio** na [publicação um serviço na nuvem utilizando as ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE] Também pode publicar a aplicação web do projeto Azure. Para fazer isto, abra o menu de atalho para o projeto Azure e selecione **Publicar**.

1. Para ver o progresso da implementação, pode ver a janela de **Registo de atividade Azure** . Este registo é automaticamente apresentado quando inicia o processo de implementação. Pode expandir o item de linha no registo de atividade para mostrar informações detalhadas, conforme apresentado na seguinte ilustração:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Opcional) Para cancelar o processo de implementação, abra o menu de atalho para o item de linha no registo de atividade e selecione **Cancelar e remover**. Isto interrompe o processo de implementação e elimina o ambiente de implementação do Azure.

    >[AZURE.NOTE] Para remover este ambiente implementação depois de ter sido implementado, tem de utilizar o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Depois de tem iniciado seu instâncias de função, o Visual Studio automaticamente mostra o ambiente de implementação no nó **Azure calcular** em **Nuvem Explorer** ou no **Explorador de servidor**. A partir daqui pode ver o estado das instâncias função individuais.

    A ilustração seguinte mostra as instâncias de funções no **Explorador do servidor** enquanto ainda se encontram em estado da inicializar:

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Para aceder à sua aplicação após a implementação, selecione a seta junto a sua implementação quando um Estado de **concluída** é apresentada no de **registo de atividade Azure**. Esta ação apresentará o URL para a aplicação web no Azure. Consulte a tabela seguinte para obter os detalhes sobre como iniciar um tipo específico de aplicação web a partir do Azure.

    A tabela seguinte apresenta os detalhes sobre como iniciar aplicações web específico a partir do Azure ou executar ou depurar uma aplicação web localmente emulador de Azure calcular:

  	|Tipo de aplicação Web|Executar/depuração localmente utilizando o emulador cluster|Em execução no Azure|
  	|---|---|---|
  	|Aplicação Web do ASP.NET|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Selecione a hiperligação de URL apresentada no separador **implementação** para o **registo de atividade Azure** carregar a página de início no browser.|
  	|Aplicação Web do ASP.NET MVC 2|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Selecione a hiperligação de URL apresentada no separador **implementação** para o **registo de atividade Azure** carregar a página de início no browser.|
  	|Aplicação Web do ASP.NET MVC 3|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Selecione a hiperligação de URL apresentada no separador **implementação** para o **registo de atividade Azure** carregar a página de início no browser.|
  	|Aplicação Web do ASP.NET MVC 4|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Selecione a hiperligação de URL apresentada no separador **implementação** para o **registo de atividade Azure** carregar a página de início no browser.|
  	|Aplicação de Web do ASP.NET vazia|Tem de adicionar uma página. aspx na sua aplicação definida por si como a página de início para o seu projeto web. Em seguida, na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Se tiver uma página. aspx predefinida na sua aplicação, selecione a hiperligação de URL apresentada no separador de **implementação** para o **registo de atividade do Azure** e esta página é carregada no browser. Se tiver uma página. aspx diferente, é necessário navegar para esta página específica utilizando o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx`|
  	|Aplicação do Silverlight|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Tem de navegar para a página específica para a sua aplicação utilizando o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx`|
  	|Aplicação de negócio do Silverlight|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Tem de navegar para a página específica para a sua aplicação utilizando o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx`|
  	|Aplicação de navegação do Silverlight|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Tem de navegar para a página específica para a sua aplicação utilizando o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx`|
  	|Aplicação de serviço WCF|Tem de definir o ficheiro SVC como a página de início para o seu projeto do serviço WCF. Em seguida, na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Tem de navegar para o ficheiro svc para a sua aplicação utilizando o seguinte formato para o seu url:`<url for deployment>/<name of service file>.svc`|
  	|Aplicação de serviço WCF fluxo de trabalho|Tem de definir o ficheiro SVC como a página de início para o seu projeto do serviço WCF. Em seguida, na barra de menus, escolha **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Tem de navegar para o ficheiro svc para a sua aplicação utilizando o seguinte formato para o seu url:`<url for deployment>/<name of service file>.svc`|
  	|Entidades dinâmicos do ASP.NET|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Tem de atualizar a cadeia de ligação (consulte a secção seguinte). Também terá de navegar para a página específica para a sua aplicação utilizando o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx`|
  	|ASP.NET Dynamic dados Linq para SQL|Na barra de menus, selecione **Depurar**, **Iniciar depuração** (teclado: escolher a tecla **F5** .).|Tem de seguir os passos neste procedimento: utilizar uma base de dados do SQL Azure para a sua aplicação (consulte a secção anterior neste tópico). Também terá de navegar para a página específica para a sua aplicação utilizando o seguinte formato para o seu url:`<url for deployment>/<name of page>.aspx`|

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Atualizar uma cadeia de ligação para entidades dinâmicos do ASP.NET

### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Para atualizar uma cadeia de ligação para entidades dinâmicos do ASP.NET

1. Para criar uma base de dados do SQL Azure que pode ser utilizado para uma aplicação web do ASP.NET dinâmico entidades, siga os passos no procedimento **utilizar uma base de dados do SQL Azure para a sua aplicação** anteriormente neste tópico.

1. Adicione as tabelas e campos que precisa para esta base de dados a partir do [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885).

1. A cadeia de ligação para este tipo de aplicação tem o seguinte formato no ficheiro Web. config:  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Atualize o valor de *connectionString* com a cadeia de ligação para a base de dados do SQL Azure ADO.NET da seguinte forma:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

1. Para guardar o ficheiro da Web. config com as alterações que fez ao cadeia de ligação, na barra de menus, escolha **ficheiro**, **Guardar Web. config**.

## <a name="supported-project-templates"></a>Modelos de projecto suportados

Para publicar uma aplicação web para Azure, a aplicação tem de utilizar um dos modelos de projecto para c# ou o Visual Basic que se encontra listado na tabela abaixo.

|Grupo de modelo de projeto|Modelo do Project|
|---|---|
|Web|Aplicação Web do ASP.NET|
|Web|Aplicação Web do ASP.NET MVC 2|
|Web|Aplicação Web do ASP.NET MVC 3|
|Web|Aplicação Web do ASP.NET MVC4|
|Web|Aplicação de Web do ASP.NET vazia|
|Web|Aplicação do ASP.NET MVC 2 Web vazia|
|Web|Aplicação Web do ASP.NET dinâmico entidades de dados|
|Web|ASP.NET Dynamic dados Linq a aplicação Web SQL|
|Silverlight|Aplicação do Silverlight|
|Silverlight|Aplicação de negócio do Silverlight|
|Silverlight|Aplicação de navegação do Silverlight|
|WCF|Aplicação de serviço WCF|
|WCF|Aplicação de serviço WCF fluxo de trabalho|
|Fluxo de trabalho|Aplicação de serviço WCF fluxo de trabalho|

## <a name="next-steps"></a>Próximos passos
Para mais informações sobre a publicação, consulte o artigo [preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Também consulte a [Definição para cima denominada as credenciais de autenticação](vs-azure-tools-setting-up-named-authentication-credentials.md).
