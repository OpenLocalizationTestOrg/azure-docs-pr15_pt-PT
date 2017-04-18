<properties
   pageTitle="Configurar as funções para um serviço de nuvem Azure com o Visual Studio | Microsoft Azure"
   description="Saiba como instalar e configurar funções para serviços em nuvem Azure utilizando o Visual Studio."
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

# <a name="configure-the-roles-for-an-azure-cloud-service-with-visual-studio"></a>Configurar as funções para um serviço de nuvem Azure com o Visual Studio

Um serviço em nuvem Azure pode ter uma ou mais trabalho ou funções da web. Para cada função tem de definir como essa função está configurada e também de configurar como essa função é executado. Para saber mais sobre as funções de serviços em nuvem, consulte o vídeo [Introdução aos serviços em nuvem Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). As informações do seu serviço de nuvem são armazenadas nos seguintes ficheiros:

- **ServiceDefinition.csdef**

    O ficheiro de definição de serviço define as definições de tempo de execução do seu serviço na nuvem, incluindo funções de que são necessárias, pontos finais e tamanho de máquina virtual. Nenhum dos dados armazenados neste ficheiro podem ser alteradas quando ao seu cargo está em execução.

- **ServiceConfiguration.cscfg**

    O ficheiro de configuração do serviço configura quantas instâncias de uma função são executadas e os valores das definições definidas para uma função. Os dados armazenados neste ficheiro podem ser alterados enquanto ao seu cargo estiver em execução.

Para poder armazenar diferentes valores para estas definições para a forma como o seu perfil é executado, pode ter vários configurações do serviço. Pode utilizar uma configuração de serviços diferente para cada ambiente de implementação. Por exemplo, pode definir a cadeia de ligação de conta de armazenamento para utilizar o emulador armazenamento Azure local numa configuração serviço local e criar outro serviço configuração para utilizar o Azure armazenamento na nuvem.

Quando cria um novo serviço de nuvem Azure no Visual Studio, duas configurações de serviço são criadas por predefinição. Estas configurações são adicionadas ao projeto Azure. As configurações são com nome:

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## <a name="configure-an-azure-cloud-service"></a>Configurar um serviço em nuvem Azure

Pode configurar um serviço em nuvem Azure a partir do Explorador de solução no Visual Studio, conforme apresentado na seguinte ilustração.

![Configurar o serviço na nuvem](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### <a name="to-configure-an-azure-cloud-service"></a>Para configurar um serviço em nuvem Azure

1. Para configurar cada função no Azure projeto a partir do **Explorador de solução**, abrir o menu de atalho para a função no projeto Azure e, em seguida, selecione **Propriedades**.

    No editor do Visual Studio, é apresentada uma página com o nome da função. A página apresenta os campos para o separador **configuração** .

1. Na lista de **Configuração do serviço** , selecione o nome da configuração do serviço que pretende editar.

    Se pretender efetuar alterações a todas as configurações de serviço para esta função do, pode escolher **Todas as configurações**.

    >[AZURE.IMPORTANT] Se optar por uma configuração de serviço específicos, algumas propriedades estão desactivadas porque só pode ser definidas para todas as configurações. Para editar estas propriedades, tem de escolher todas as configurações.

    Agora pode escolher um separador para atualizar as propriedades ativadas essa vista.

## <a name="change-the-number-of-role-instances"></a>Alterar o número de ocorrências de função

Para melhorar o desempenho do seu serviço de nuvem, pode alterar o número de ocorrências de uma função que estiver a executar, com base no número de utilizadores ou a carga esperado para uma determinada função. Uma máquina virtual separada é criada para cada instância de um perfil quando o serviço de nuvem é executado no Azure. Isto afeta a faturação para a implementação deste serviço na nuvem. Para mais informações sobre faturação, consulte o artigo [entender a minha conta do Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-number-of-instances-for-a-role"></a>Para alterar o número de ocorrências de uma função

1. Selecione o separador de **configuração** .

1. Na lista de **Configuração do serviço** , selecione a configuração do serviço que pretende atualizar.

    >[AZURE.NOTE] Pode definir a contagem de instância para a configuração de um serviço específico ou para todas as configurações de serviço.

1. Na caixa de texto **instância contagem** , introduza o número de instâncias ao qual pretende iniciar para esta função.

    >[AZURE.NOTE] Cada instância é executada numa máquina virtual separada quando publicar o seu serviço de nuvem no Azure.

1. Selecione o botão **Guardar** na barra de ferramentas para guardar estas alterações ao ficheiro de configuração de serviço.

## <a name="manage-connection-strings-for-storage-accounts"></a>Gerir as cadeias de ligação para contas de armazenamento

Pode adicionar, remover ou modificar as cadeias de ligação para as configurações de serviço. Poderá querer diferentes cadeias de ligação para as configurações de serviços diferente. Por exemplo, poderá querer uma cadeia de ligação local para uma configuração de serviço local que tenha um valor `UseDevelopmentStorage=true`. Também poderá pretender configurar um serviço na nuvem que utiliza uma conta de armazenamento no Azure.

>[AZURE.WARNING] Ao introduzir informação de chave da conta Azure armazenamento para uma cadeia de ligação de conta de armazenamento, estas informações são armazenadas localmente no ficheiro de configuração de serviço. No entanto, esta informação não atualmente está armazenada como texto encriptado.

Ao utilizar um valor diferente para cada configuração de serviço, não tem de utilizar diferentes cadeias de ligação no seu serviço de nuvem ou modificar o seu código quando publicar o seu serviço de nuvem no Azure. Pode utilizar o mesmo nome para a cadeia de ligação no seu código e o valor serão diferente, com base na configuração do serviço que selecionar quando criar o seu serviço de nuvem ou quando publicá-la.

### <a name="to-manage-connection-strings-for-storage-accounts"></a>Para gerir as cadeias de ligação para contas de armazenamento

1. Selecione o separador **Definições** .

1. Na lista de **Configuração do serviço** , selecione a configuração do serviço que pretende atualizar.

    >[AZURE.NOTE] Pode atualizar as cadeias de ligação para a configuração de um serviço específico, mas se precisar de adicionar ou eliminar uma cadeia de ligação tem de selecionar todas as configurações.

1. Para adicionar uma cadeia de ligação, escolha o botão **Adicionar definição** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , escreva o nome que pretende utilizar para a cadeia de ligação.

1. Na lista pendente **tipo** , escolha a **Cadeia de ligação**.

1. Para alterar o valor de cadeia de ligação, escolha o botão de reticências (…). É apresentada a caixa de diálogo **Criar cadeia de ligação de armazenamento** .

1. Para utilizar o emulador de conta de armazenamento local, selecione o botão de opção **emulador de armazenamento do Microsoft Azure** e, em seguida, selecione o botão **OK** .

1. Para utilizar uma conta de armazenamento no Azure, selecione o botão de opção da **sua subscrição** e selecione a conta de armazenamento pretendida.

1. Para utilizar credenciais personalizadas, selecione o botão de opções **introduzidos manualmente as credenciais** . Introduza o nome da conta de armazenamento e a chave primária ou ao segunda. Para obter informações sobre como criar uma conta de armazenamento e como introduzir os detalhes da conta de armazenamento na caixa de diálogo **Criar cadeia de ligação de armazenamento** , consulte o artigo [preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Para eliminar uma cadeia de ligação, selecione a cadeia de ligação e, em seguida, selecione o botão de **Definição de remover** .

1. Selecione o ícone **Guardar** na barra de ferramentas para guardar estas alterações ao ficheiro de configuração de serviço.

1. Para aceder a cadeia de ligação no ficheiro de configuração de serviço, terá de obter o valor da definição de configuração. O código seguinte mostra um exemplo onde o armazenamento de BLOBs é criado e carregados utilizando uma cadeia de ligação de dados `MyConnectionString` a partir do ficheiro de configuração de serviço quando um utilizador escolhe **Button1** na página default. aspx na função web para um serviço em nuvem Azure. Adicione o seguinte utilizando instruções para Default.aspx.cs:

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Abra Default.aspx.cs na vista de estrutura e adicionar um botão da caixa de ferramentas. Adicione o seguinte código para o `Button1_Click` método. Este código utiliza `GetConfigurationSettingValue` para obter o valor a partir do ficheiro de configuração do serviço para a cadeia de ligação. Em seguida, é criado um blob na conta de armazenamento que é referenciada na cadeia de ligação `MyConnectionString` e finalmente o programa adiciona o texto para o blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Adicionar as definições personalizadas para utilizar no seu serviço de nuvem Azure

As definições personalizadas no ficheiro de configuração de serviço permitem-lhe adicionar um nome e o valor de uma cadeia de configuração de um serviço específico. Poderá optar por utilizar esta definição para configurar uma funcionalidade no seu serviço de nuvem, lendo o valor da definição e utilizar este valor para controlar a lógica no seu código. Pode alterar estes valores de configuração do serviço sem ser necessário que recriar o pacote de serviço ou quando o serviço de nuvem está em execução. Pode verificar o código de notificações de quando uma aplicação. Consulte o artigo [RoleEnvironment.Changing evento](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Pode adicionar, remover ou modificar as definições personalizadas para as configurações de serviço. Poderá querer diferentes valores para estas cadeias para as configurações de serviços diferente.

Ao utilizar um valor diferente para cada configuração de serviço, não tem de utilizar cadeias diferentes no seu serviço de nuvem ou modificar o seu código quando publicar o seu serviço de nuvem no Azure. Pode utilizar o mesmo nome para a cadeia no seu código e o valor serão diferente, com base na configuração do serviço que selecionar quando criar o seu serviço de nuvem ou quando publicá-la.

### <a name="to-add-custom-settings-to-use-in-your-azure-cloud-service"></a>Para adicionar as definições personalizadas para utilizar no seu serviço de nuvem Azure

1. Selecione o separador **Definições** .

1. Na lista de **Configuração do serviço** , selecione a configuração do serviço que pretende atualizar.

    >[AZURE.NOTE] Pode atualizar cadeias para a configuração de um serviço específico, mas se precisar de adicionar ou eliminar uma cadeia, tem de selecionar **Todas as configurações**.

1. Para adicionar uma cadeia de, selecione o botão **Adicionar definição** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , escreva o nome que pretende utilizar para a cadeia.

1. Na lista pendente **tipo** , escolha **cadeia**.

1. Para adicionar ou alterar o valor para a cadeia, na caixa de texto de **valor** , escreva o novo valor.

1. Para eliminar uma cadeia de, selecione a cadeia e, em seguida, selecione o botão de **Definição de remover** .

1. Selecione o botão **Guardar** na barra de ferramentas para guardar estas alterações ao ficheiro de configuração de serviço.

1. Para aceder a cadeia no ficheiro de configuração de serviço, terá de obter o valor da definição de configuração.

    Tem de se certificar de que o utilizando seguintes declarações de já são adicionadas à Default.aspx.cs, tal como fez no procedimento anterior.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Adicione o seguinte código para o `Button1_Click` método para aceder a esta cadeia da mesma forma que aceder a uma cadeia de ligação. O código, em seguida, pode executar algumas código específico com base em valores de uma cadeia definições para o ficheiro de configuração do serviço é utilizado.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Gerir armazenamento local para cada instância de função

Pode adicionar armazenamento de sistema do ficheiro local para cada instância de uma função. Pode armazenar dados locais aqui que não têm de ser acedido por outras funções. Todos os dados que não é necessário guardar numa tabela, blob ou armazenamento de base de dados SQL podem ser armazenados aqui. Por exemplo, pode utilizar este armazenamento local para dados em cache que poderão ter de ser utilizada novamente. Estes dados armazenados não podem ser acedidos por outras instâncias de uma função. 

Definições do armazenamento local aplicadas a todas as configurações de serviço. Só pode adicionar, remover ou modificar armazenamento local para todas as configurações de serviço.

### <a name="to-manage-local-storage-for-each-role-instance"></a>Para gerir o armazenamento local para cada instância de função

1. Selecione o separador **Armazenamento Local** .

1. Na lista de **Configuração do serviço** , selecione **Todas as configurações**.

1. Para adicionar uma entrada de armazenamento local, selecione o botão **Adicionar armazenamento Local** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , escreva o nome que pretende utilizar para este armazenamento local.

1. Na caixa **tamanho** do texto, escreva o tamanho em MB que precisa para esta armazenamento local.

1. Para remover os dados neste armazenamento local quando a máquina virtual para esta função é reciclada, selecione a caixa de verificação **Limpar numa função de reciclagem** .

1. Para editar uma entrada de armazenamento local existente, selecione a linha que necessita de atualizar. Em seguida, pode editar os campos, tal como descrito nos passos anteriores.

1. Para eliminar uma entrada de armazenamento local, selecione a entrada de armazenamento na lista e, em seguida, selecione o botão de **Remover armazenamento Local** .

1. Para guardar estas alterações a ficheiros de configuração do serviço, selecione o ícone **Guardar** na barra de ferramentas.

1. Para aceder ao armazenamento local que tenha adicionado no ficheiro de configuração de serviço, terá de obter o valor da definição de configuração de recursos locais. Utilize as seguintes linhas de código para aceder a este valor e criar um ficheiro denominado **MyStorageTest.txt** e escrever uma linha de dados de teste para esse ficheiro. Pode adicionar este código para o `Button_Click` método que utilizou nos procedimentos anterior:

1. Tem de se certificar de que o utilizando seguintes declarações são adicionadas à Default.aspx.cs:

    ```
    using System.IO;
    using System.Text;
    ```

1. Adicione o seguinte código para o `Button1_Click` método. Este cria o ficheiro no armazenamento local e escreve dados de teste para esse ficheiro.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Opcional) Para ver este ficheiro que criou quando executa o seu serviço de nuvem localmente, utilize os passos seguintes:

  1. Executar a função de web e selecione **Button1** para se certificar de que o código dentro `Button1_Click` obtém denominado.

  1. Na área de notificação, abra o menu de atalho para o ícone do Azure e selecione **Mostrar IU emulador calcular**. É apresentada a caixa de diálogo **Azure calcular emulador** .

  1. Selecione a função da web.

  1. Na barra de menus, selecione **Ferramentas**, **arquivo local abrir**. Aparece uma janela do Explorador do Windows.

  1. Na barra de menus, introduza **MyStorageTest.txt** na caixa de texto de **pesquisa** e, em seguida, selecione **Enter** para iniciar a pesquisa.

    O ficheiro é apresentado nos resultados da pesquisa.

  1. Para ver o conteúdo do ficheiro, abra o menu de atalho para o ficheiro e selecione **Abrir**.

## <a name="collect-cloud-service-diagnostics"></a>Recolher diagnósticos do serviço na nuvem

É possível recolher dados de diagnósticos do seu serviço de nuvem Azure. Estes dados são adicionados a uma conta de armazenamento. Poderá querer diferentes cadeias de ligação para as configurações de serviços diferente. Por exemplo, poderá querer uma conta de armazenamento local para uma configuração de serviço local que tenha um valor de UseDevelopmentStorage = true. Também poderá pretender configurar um serviço na nuvem que utiliza uma conta de armazenamento no Azure. Para mais informações sobre diagnósticos do Azure, consulte o artigo recolher dados de registo por utilizar diagnósticos do Azure.

>[AZURE.NOTE] A configuração do serviço local já está configurada para utilizar recursos locais. Se utilizar a configuração do serviço na nuvem para publicar o seu serviço de nuvem Azure, a cadeia de ligação que especificou quando publicar também é utilizada para a cadeia de ligação de diagnóstico, a menos que especificou uma cadeia de ligação. Se compactar seu serviço de nuvem utilizando o Visual Studio, a cadeia de ligação a configuração do serviço não é alterada.

### <a name="to-collect-cloud-service-diagnostics"></a>Para recolher diagnósticos do serviço na nuvem

1. Selecione o separador de **configuração** .

1. Na lista de **Configuração do serviço** , selecione a configuração do serviço que pretende atualizar ou selecione **Todas as configurações**.

    >[AZURE.NOTE] Pode atualizar a conta de armazenamento para uma configuração de serviço específico, mas se pretende ativar ou desativar diagnósticos tem de escolher todas as configurações.

1. Para ativar diagnósticos, selecione a caixa de verificação **Ativar diagnósticos** .

1. Para alterar o valor para a conta de armazenamento, selecione o botão de reticências (…).

    É apresentada a caixa de diálogo **Criar cadeia de ligação de armazenamento** .

1. Para utilizar uma cadeia de ligação local, selecione a opção de emulador de armazenamento Azure e, em seguida, selecione o botão **OK** .

1. Para utilizar uma conta de armazenamento associada à sua subscrição Azure, selecione a opção da **sua subscrição** .

1. Para utilizar uma conta de armazenamento para a cadeia de ligação local, selecione a opção **introduzidos manualmente as credenciais** .

    Para obter mais informações sobre como criar uma conta de armazenamento e como introduzir os detalhes da conta de armazenamento na caixa de diálogo **Criar cadeia de ligação de armazenamento** , consulte o artigo [preparar para publicar ou implementar uma aplicação do Azure a partir do Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Selecione a conta de armazenamento que pretende utilizar no **nome da conta**.

    Se estiver manualmente introduzir as credenciais da conta de armazenamento, copie ou escreva a sua chave primária na **chave da conta**. Esta chave pode ser copiada a partir do [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885). Para copiar esta tecla, seguindo estes passos a partir da vista de **Contas de armazenamento** no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885):
    
  1. Selecione a conta de armazenamento que pretende utilizar para o seu serviço de nuvem.

  1. Selecione o botão de **Gerir as teclas de acesso** localizado na parte inferior do ecrã. É apresentada a caixa de diálogo **Gerir as teclas de acesso** .

  1. Para copiar a tecla de acesso, selecione o botão **Copiar para área de transferência** . Agora pode colar esta chave para o campo de **chave da conta** .

1. Para utilizar a conta de armazenamento fornecidos, como a cadeia de ligação para diagnósticos (e colocação em cache) durante a publicação seu serviço de nuvem Azure, selecione a caixa de verificação **Atualizar desenvolvimento armazenamento cadeias de ligação de diagnóstico e de colocação em cache com armazenamento Azure credenciais quando publicar Azure de conta** .

1. Selecione o botão **Guardar** na barra de ferramentas para guardar estas alterações ao ficheiro de configuração de serviço.

## <a name="change-the-size-of-the-virtual-machine-used-for-each-role"></a>Alterar o tamanho da máquina virtual utilizado para cada função

Pode definir o tamanho de máquina virtual para cada função. Só podem definir este tamanho para todas as configurações de serviço. Se selecionar um tamanho mais pequeno de máquina, em seguida, menos CPU núcleos, memória e disco local armazenamento é atribuído. Largura de banda alocada também é mais pequena. Para mais informações sobre estes tamanhos e os recursos atribuídos, consulte o artigo [tamanhos dos serviços em nuvem](cloud-services/cloud-services-sizes-specs.md).

Os recursos necessários para cada máquina virtual no Azure afeta o custo de executar o seu serviço de nuvem no Azure. Para mais informações sobre Azure faturação, consulte o artigo [entender a minha conta do Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-size-of-the-virtual-machine"></a>Para alterar o tamanho da máquina virtual

1. Selecione o separador de **configuração** .

1. Na lista de **Configuração do serviço** , selecione **Todas as configurações**.

1. Para selecionar o tamanho para a máquina virtual para esta função, escolha um tamanho adequado a partir da lista de **tamanho da memória virtual** .

1. Selecione o botão **Guardar** na barra de ferramentas para guardar estas alterações ao ficheiro de configuração de serviço.

## <a name="manage-endpoints-and-certificates-for-your-roles"></a>Gerir os pontos finais e certificados para o seu funções

Configurar os pontos finais à rede ao especificar o protocolo, o número da porta e, por HTTPS, as informações do certificado SSL. Lançamentos antes de Junho de 2012 suportam HTTP, HTTPS e TCP. A versão de Junho de 2012 suporta esses protocolos e UDP. Não pode utilizar UDP para entrada pontos finais o emulador cluster. Pode utilizar esse protocolo apenas para os pontos finais internos.

Para melhorar a segurança do seu serviço de nuvem Azure, pode criar os pontos finais que utilizam o protocolo HTTPS. Por exemplo, se tiver um serviço na nuvem que é utilizado pelos clientes para ordens de compra, que pretende Certifique-se de que as respetivas informações são seguras utilizando SSL.

Também pode adicionar os pontos finais que pode ser utilizado ou externamente. Os pontos finais externos são designadas por pontos finais de entrada. Um ponto final entrado permite noutro ponto de acesso aos utilizadores para o seu serviço de nuvem. Se tiver um serviço WCF, poderá pretender expor um ponto final interno para uma função de web Utilize para aceder este serviço.

>[AZURE.IMPORTANT] Só pode atualizar os pontos finais para todas as configurações de serviço.

Se adicionar pontos finais de HTTPS, tem de utilizar um certificado SSL. Para fazer isto pode associar certificados ao seu cargo para todas as configurações de serviço e utilizar estas opções para os pontos finais.

>[AZURE.IMPORTANT] Estes certificados não são fornecidos com o seu serviço. Tem carregue os seus certificados separadamente para Azure através do [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Qualquer certificados de gestão associar com as suas configurações de serviço aplicam-se apenas quando o seu serviço de nuvem é executado no Azure. Quando o seu serviço de nuvem é executado no ambiente de desenvolvimento local, é utilizado um certificado padrão que é gerido pelo emulador cluster Azure.

### <a name="to-add-a-certificate-to-a-role"></a>Para adicionar um certificado para uma função

1. Selecione o separador **certificados** .

1. Na lista de **Configuração do serviço** , selecione **Todas as configurações**.

    >[AZURE.NOTE] Para adicionar ou remover certificados, tem de selecionar todas as configurações. Pode atualizar o nome e a impressão digital para a configuração de um serviço específico se for necessário.

1. Para adicionar um certificado para esta função, selecione o botão **Adicionar certificado** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , introduza o nome para o certificado.

1. Na lista **Localização do arquivo** , escolha a localização para o certificado que pretende adicionar.

1. Na lista **Nome de arquivo** , selecione o arquivo de que pretende utilizar para selecionar o certificado.

1. Para adicionar o certificado, selecione o botão de reticências (…). É apresentada a caixa de diálogo **Segurança do Windows** .

1. Selecione o certificado que pretende utilizar a partir da lista e, em seguida, selecione o botão **OK** .

    >[AZURE.NOTE] Quando adiciona um certificado do arquivo de certificados, quaisquer certificados intermédios são automaticamente adicionados às definições de configuração para si. Estes certificados intermédios também tem de ser carregados para Azure para configurar o seu serviço de SSL corretamente.

1. Para eliminar um certificado, selecione o certificado e, em seguida, selecione o botão **Remover certificado** .

1. Selecione o ícone **Guardar** na barra de ferramentas para guardar estas alterações aos ficheiros de configuração de serviço.

### <a name="to-manage-endpoints-for-a-role"></a>Para gerir os pontos finais para uma função

1. Selecione o separador **pontos finais** .

1. Na lista de **Configuração do serviço** , selecione **Todas as configurações**.

1. Para adicionar um ponto final, selecione o botão **Adicionar ponto final** . Uma nova entrada é adicionada à lista.

1. Na caixa de texto **nome** , escreva o nome que pretende utilizar para este ponto final.

1. Selecione o tipo de ponto final que precisa da partir da lista **tipo** .

1. Escolha o protocolo para o ponto final que é necessário a partir da lista de **protocolo** .

1. Se for um ponto final entrado, na caixa de texto **Porta pública** , introduza a porta pública a utilizar.

1. Na caixa de texto **Porta privado** , escreva a porta privada a utilizar.

1. Se o ponto final exigir o protocolo https, selecione um certificado para utilizar na lista **Nome do certificado SSL** .

    >[AZURE.NOTE] Esta lista mostra os certificados que adicionou para esta função no separador **certificados** .

1. Selecione o botão **Guardar** na barra de ferramentas para guardar estas alterações aos ficheiros de configuração de serviço.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre Azure projetos no Visual Studio ao ler o artigo [configurar um projecto do Azure](vs-azure-tools-configuring-an-azure-project.md). Saiba mais acerca do esquema de serviço de nuvem ao ler o artigo [Referência de esquema](https://msdn.microsoft.com/library/azure/dd179398).
