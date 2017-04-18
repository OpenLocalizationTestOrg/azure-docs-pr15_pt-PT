<properties
   pageTitle="Navegação e gerir os recursos de armazenamento com o Explorador de servidor | Microsoft Azure"
   description="Navegação e gerir os recursos de armazenamento com o Explorador de servidor"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="browsing-and-managing-storage-resources-with-server-explorer"></a>Navegação e gerir os recursos de armazenamento com o Explorador de servidor

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Descrição geral
Se tiver instalado as ferramentas do Azure para o Microsoft Visual Studio, pode ver blob, fila de espera e dados da tabela de contas armazenamento para Azure. O nó de armazenamento do Windows Azure no servidor Explorer mostra os dados que se encontra na sua conta de emulador de armazenamento local e outras contas de armazenamento Azure.

Para ver Server Explorer no Visual Studio, na barra de menus, selecione **vista**, **Server Explorer**. O nó de armazenamento mostra todas as contas de armazenamento que existam em cada subscrição/certificado Azure que estiver ligado a. Se a sua conta de armazenamento não aparecer, pode adicioná-la ao seguir as instruções [mais adiante](#add-storage-accounts-by-using-server-explorer).

Iniciar no Azure SDK 2.7, também pode utilizar o Explorador de nuvem novo para ver e gerir os recursos do Azure. Consulte o artigo [Gerir o Azure recursos com o Explorador de nuvem](./vs-azure-tools-resources-managing-with-cloud-explorer.md) para obter mais informações.


## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Ver e gerir recursos de armazenamento no Visual Studio

Servidor Explorer mostra automaticamente uma lista de blobs, filas e tabelas na sua conta de emulador de armazenamento. A conta de emulador armazenamento está listada no Explorador do servidor sob o nó de armazenamento como o nó do **desenvolvimento** .

Para ver recursos a conta de emulador de armazenamento, expanda o nó do **desenvolvimento** . Se o emulador de armazenamento não foi começou quando que expanda o nó do **desenvolvimento** , este iniciará automaticamente. Isto pode demorar alguns segundos. Pode continuar a trabalhar em outras áreas do Visual Studio, enquanto o emulador de armazenamento é iniciado.

Para ver recursos numa conta de armazenamento, expanda o nó da conta de armazenamento no Explorador do servidor. Os seguintes sub-nós aparecem:

- BLOBs

- Filas

- Tabelas

## <a name="work-with-blob-resources"></a>Trabalhar com recursos Blob

O nó Blobs apresenta uma lista de membros em contentores para a conta de armazenamento selecionado. Blob contentores contêm blob ficheiros, e pode organizar estes blobs em pastas e subpastas. Veja [como utilizar o armazenamento de Blobs do .NET](./storage/storage-dotnet-how-to-use-blobs.md) para obter mais informações.

### <a name="to-create-a-blob-container"></a>Para criar um contentor BLOBs

1. Abrir o menu de atalho para o nó **Blobs** e, em seguida, selecione **Criar contentor Blob**.

1. Introduza o nome do novo contentor na caixa de diálogo **Criar contentor Blob** e, em seguida, selecione **Ok**

    >[AZURE.NOTE] O nome do contentor blob tem de começar com um número (0-9) ou a letra minúscula (a-z).

### <a name="to-delete-a-blob-container"></a>Para eliminar um contentor blob

- Abra o menu de atalho para o contentor BLOBs que pretende remover e, em seguida, selecione **Eliminar**.

### <a name="to-display-a-list-of-the-items-contained-in-a-blob-container"></a>Para apresentar uma lista dos itens contidas num contentor BLOBs

- Abrir o menu de atalho para um nome de contentor BLOBs na lista e, em seguida, selecione **Vista BLOBs contentor**.

    Ao visualizar o conteúdo de um contentor blob, este aparece num separador conhecido como a vista de contentor blob.

    ![VST_SE_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)

    Pode efetuar as seguintes operações blobs utilizando os botões no canto superior direito da vista de contentor de BLOBs:

    - Introduza um valor de filtro e aplicá-la

    - Atualizar a lista de blobs no contentor

    - Carregar um ficheiro

    - Eliminar um blob

      >[AZURE.NOTE] Se eliminar um ficheiro a partir de um contentor de BLOBs não elimina o ficheiro subjacente; Remove-a apenas a partir do contentor blob.

    - Abrir um BLOBs

    - Guardar um blob para o computador local

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>Para criar uma pasta ou subpasta num contentor blob

1. Selecione o contentor blob no Explorador do servidor. Na janela do contentor, selecione o botão **Carregar Blob** .

    ![Carregar um ficheiro para uma pasta de BLOBs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)

1. Na caixa de diálogo **Carregar o novo ficheiro** , selecione o botão **Procurar** para especificar o ficheiro que pretende carregar e, em seguida, introduza um nome de pasta na caixa **pasta (opcional)** .

    Pode adicionar subpastas nas pastas do contentor ao seguir o mesmo procedimento. Se não especificar um nome de pasta, irá carregar o ficheiro ao nível superior do contentor blob. O ficheiro aparece na pasta especificada no contentor.

    ![Pasta adicionada a um contentor blob](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)

1. Faça duplo clique na pasta ou prima ENTER para ver os conteúdos da pasta. Quando estiver na pasta o contentor, pode navegar para a raiz do contentor ao selecionar o botão **Abrir directório principal** (para cima seta).

### <a name="to-delete-a-container-folder"></a>Para eliminar uma pasta de contentor

 - Eliminar todos os ficheiros na pasta

    >[AZURE.NOTE] Uma vez que as pastas em contentores de BLOBs são pastas virtuais, não é possível criar uma pasta vazia nem é possível eliminar uma pasta para eliminar os seus conteúdos do ficheiro. Tem de eliminar todo o conteúdo de uma pasta para eliminar a pasta.

### <a name="to-filter-blobs-in-a-container"></a>Para filtrar blobs num contentor

Pode filtrar as blobs que são apresentadas ao especificar um prefixo comuns.

Por exemplo, se introduzir o prefixo `hello` no texto do filtro caixa e, em seguida, selecione **Executar** (****!) botão, aparecem apenas blobs que comecem por "Olá".

![VST_SE_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)


>[AZURE.NOTE] O campo de filtro é sensível às maiúsculas e não suporta a filtragem com carateres universais. Só podem ser filtradas BLOBs pelo prefixo. O prefixo pode incluir delimitador se estiver a utilizar um delimitador para organizar blobs numa hierarquia virtual. Por exemplo, o prefixo HelloFabric de filtragem / devolve todos os blobs começados por essa cadeia.

### <a name="to-download-blob-data"></a>Para transferir dados blob

- No **Explorador do servidor**, abrir o menu de atalho para um ou mais blobs e, em seguida, selecione **Abrir**, ou selecione o nome BLOBs e, em seguida, selecione o botão **Abrir** ou faça duplo clique no nome BLOBs.

    O progresso da transferência de uma blob é apresentada na janela do **Registo de atividade Azure** .

    Abre o BLOBs no editor de predefinido para esse tipo de ficheiro. Se o sistema operativo reconhece o tipo de ficheiro, o ficheiro abre uma aplicação instalada localmente; caso contrário, lhe for pedido para escolher uma aplicação que altura é adequada para o tipo de ficheiro do blob. O ficheiro local que é criado quando transfere um BLOBs está marcado como só de leitura.

    Em cache localmente e comparados com hora da última modificação o blob no serviço Blob blob dados. Se o blob foi atualizado desde a última transferência, serão transferido novamente; caso contrário, o blob será carregado a partir do disco local. Por predefinição, um blob é transferido para um directório temporário. Para transferir blobs para um diretório específico, abra o menu de atalho para os nomes de BLOBs selecionado e selecione **Guardar como**. Quando guarda um blob desta forma, não é possível abrir o ficheiro de BLOBs e o ficheiro local é criado com os atributos de leitura e escrita.

### <a name="to-upload-blobs"></a>Para carregar blobs

- Selecione o botão de **Carregar Blob** quando o contentor estiver aberto para visualização na vista de contentor blob.

    Pode escolher um ou mais ficheiros para carregar e pode carregar ficheiros de qualquer tipo. O **Registo de atividade Azure** mostra o progresso do carregamento. Para obter mais informações sobre como trabalhar com dados de BLOBs, veja [como utilizar o serviço de armazenamento de Blobs do Azure no .NET](http://go.microsoft.com/fwlink/p/?LinkId=267911).

### <a name="to-view-logs-transferred-to-blobs"></a>Para ver registos transferidos para blobs

- Se estiver a utilizar o Azure diagnósticos registar dados a partir da aplicação do Azure e tiver transferido registos à sua conta de armazenamento, verá contentores que foram criadas ao Azure para que estes registos. Ver estes registos no Explorador do servidor é uma forma fácil de identificar problemas com a aplicação, especialmente se é foram implementado para Azure. Para mais informações sobre diagnósticos do Azure, consulte o artigo [Recolher dados de registo por utilizar diagnósticos do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="to-get-the-url-for-a-blob"></a>Para obter o URL para um BLOBs

- Abrir o menu de atalho do blob e, em seguida, selecione **URL da cópia**.

### <a name="to-edit-a-blob"></a>Para editar um blob

- Selecione o blob e, em seguida, selecione o botão de **BLOBs abrir** .

    O ficheiro é transferido para uma localização temporária e aberto no computador local. Terá de carregar o blob novamente depois de efetuar alterações.

## <a name="work-with-queue-resources"></a>Trabalhar com recursos de fila de espera

Filas de serviços de armazenamento estão alojadas numa conta de armazenamento Azure e pode utilizá-los para permitir que o cloud funções de serviço comunicar com os outros e com outros serviços por uma mensagem prisma mecanismo. Pode aceder a fila de espera através de programação através de um serviço na nuvem e, ao longo de um serviço web para clientes externos. Também pode aceder a fila diretamente através do Explorador de servidor no Visual Studio.

Quando desenvolver um serviço na nuvem que utiliza filas, poderá pretender utilizar o Visual Studio para criar filas e trabalhar com os mesmos forma interativa enquanto desenvolver e testar o código.

No Explorador do servidor, pode ver as filas numa conta de armazenamento, criar e eliminar filas, abra uma fila de espera para ver as suas mensagens e adicionar mensagens a uma fila. Quando abre uma fila para visualização, pode ver as mensagens individuais e pode executar as seguintes ações na fila de espera, utilizando os botões no canto superior esquerdo:

- Atualizar a vista de fila de espera

- Adicionar uma mensagem para a fila

- Descarregado a mensagem mais acima.

- Limpar toda fila de espera

A imagem seguinte mostra uma fila que contém duas mensagens.

![Ver uma fila](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

Para obter mais informações sobre o armazenamento dos serviços de filas, consulte o artigo [como: utilizar o serviço de armazenamento de fila](http://go.microsoft.com/fwlink/?LinkID=264702). Para obter informações sobre o serviço web para filas de serviços de armazenamento, consulte o artigo [Conceitos de serviço de fila de espera](http://go.microsoft.com/fwlink/?LinkId=264788). Para obter informações sobre como enviar mensagens para uma fila de serviços de armazenamento utilizando o Visual Studio, consulte o artigo [Enviar mensagens para uma fila de serviços de armazenamento](https://msdn.microsoft.com/library/azure/jj649344.aspx).

>[AZURE.NOTE] Filas de serviços de armazenamento são distintas do filas de bus de serviço. Para mais informações sobre filas de bus de serviço, consulte serviço Bus filas, tópicos e subscrições.

## <a name="work-with-table-resources"></a>Trabalhar com recursos de tabela

O serviço de armazenamento de tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita autenticados chamadas a partir do dentro e fora da nuvem Azure. Tabelas Azure são ideais para armazenar dados estruturados e não relacionais.

### <a name="to-create-a-table"></a>Para criar uma tabela

1. No Explorador do servidor, selecione o nó de **tabelas** da conta de armazenamento e, em seguida, selecione **Create Table**.

1. Na caixa de diálogo **Criar tabela** , introduza um nome para a tabela.

### <a name="to-view-table-data"></a>Para ver os dados de tabela

1. No Explorador do servidor, abra o nó do **Azure** e, em seguida, abra o nó de **armazenamento** .

1. Abra o nó de conta de armazenamento que se estiver interessado em e, em seguida, abra o nó de **tabelas** para ver uma lista de tabelas para a conta de armazenamento.

1. Abrir o menu de atalho de uma tabela e, em seguida, selecione **Vista de tabela**.

    ![Uma tabela do Azure no Explorador de soluções](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

A tabela está organizada por entidades (mostradas nas linhas) e propriedades (mostradas nas colunas). Por exemplo, a seguinte ilustração mostra entidades listadas no **Estruturador de tabelas**:

### <a name="to-edit-table-data"></a>Para editar os dados da tabela

1. No **Estruturador de tabelas**, abra o menu de atalho para uma entidade (uma única linha) ou uma propriedade (uma única célula) e, em seguida, selecione **Editar**.

    ![Adicionar ou editar uma entidade de tabela](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)

    Entidades numa única tabela não são obrigatórios de ter o mesmo conjunto de propriedades (colunas). Tenha em atenção as seguintes restrições quanto ver e editar dados da tabela.
    - Não pode ver ou editar dados binários (byte[]) tipo, mas pode armazená-lo numa tabela.

    - Não pode editar os valores **PartitionKey** ou **RowKey** , porque o armazenamento de tabela no Azure não suporta essa operação.

    - Não é possível criar uma propriedade denominada carimbo data/hora, serviços de armazenamento do Windows Azure utilizam uma propriedade com esse nome.

    - Se introduzir um valor DateTime, tem de seguir um formato que altura é adequado as definições de região e idioma do seu computador (por exemplo, dd/mm/aaaa ss [AM | PM] para o inglês dos e.u.a.).

### <a name="to-add-entities"></a>Para adicionar entidades

1. No **Estruturador de tabelas**, selecione o botão **Adicionar entidade** , que se encontra junto ao canto superior direito da vista de tabela.

    ![Adicionar entidade](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)

1. Na caixa de diálogo **Adicionar entidade** , introduza os valores das propriedades **PartitionKey** e **RowKey** .

    ![Caixa de diálogo entidade adicionar](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)

    Introduza os valores cuidadosamente porque não é possível alterar depois de fechar a caixa de diálogo exceto se eliminar a entidade e adicioná-la novamente.

### <a name="to-filter-entities"></a>Para filtrar entidades

Pode personalizar o conjunto de entidades que aparecem numa tabela, se utilizar o construtor de consultas.

1. Para abrir o construtor de consultas, abra uma tabela para visualização.

1. Selecione o botão mais à direita na barra de ferramentas da vista de tabela.

    É apresentada a caixa de diálogo **Construtor de consultas** . A ilustração seguinte mostra uma consulta que está a ser criada no construtor de consultas.

    ![Construtor de consultas](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)

1. Quando tiver terminado criar a consulta, feche a caixa de diálogo. O formulário de texto resultante da consulta é apresentado numa caixa de texto como um filtro de serviços de dados WCF.

1. Para executar a consulta, selecione o ícone de triângulo verde.

    Também pode filtrar dados de entidade que é apresentado no **Estruturador de tabelas** se introduzir uma cadeia de filtro de serviços de dados WCF diretamente no campo de filtro. Este tipo de cadeia é semelhante a uma cláusula WHERE de SQL, mas é enviado para o servidor como um pedido de HTTP. Para obter informações sobre como construir cadeias de filtro, consulte o artigo [Construir cadeias de filtro para o Estruturador da tabela](https://msdn.microsoft.com/library/azure/ff683669.aspx).

    A ilustração seguinte mostra um exemplo de uma cadeia de filtro válida:

    ![VST_SE_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

### <a name="refresh-storage-data"></a>Atualizar dados de armazenamento

Quando Server Explorer liga-se ao ou obtém dados a partir de uma conta de armazenamento, poderá demorar um minuto concluir a operação. Se não conseguir ligá-lo, a operação poderá tempo limite. Enquanto dados serem recuperados, pode continuar a trabalhar no outras partes do Visual Studio. Para cancelar a operação se está a demorar demasiado tempo, selecione o botão **Parar atualizar** na barra de ferramentas do Explorador de servidor.

#### <a name="to-refresh-blob-container-data"></a>Para atualizar os dados de contentor blob

- Selecione o nó **Blobs** sob o **armazenamento** e selecione o botão **Atualizar** na barra de ferramentas do Explorador de servidor.

- Para atualizar a lista de blobs que é apresentada, selecione o botão **Executar** .

#### <a name="to-refresh-table-data"></a>Para atualizar os dados de tabela

- Selecione o nó **tabelas** abaixo **armazenamento** e selecione o botão **Atualizar** .

- Para atualizar a lista de entidades que é apresentada no **Estruturador de tabelas**, selecione o botão de **Executar** no **Estruturador de tabelas**.

#### <a name="to-refresh-queue-data"></a>Para atualizar os dados de fila de espera

- Selecione o nó **filas** e, em seguida, selecione o botão **Atualizar** .

#### <a name="to-refresh-all-items-in-a-storage-account"></a>Para atualizar todos os itens de uma conta de armazenamento

- Selecione o nome da conta e, em seguida, selecione o botão **Atualizar** na barra de ferramentas para o servidor Explorer.

### <a name="add-storage-accounts-by-using-server-explorer"></a>Adicionar contas de armazenamento através do Explorador de servidor

Existem duas formas de adicionar contas de armazenamento através do Explorador de servidor. Pode criar uma nova conta de armazenamento na sua subscrição do Azure ou pode anexar uma conta de armazenamento existente.

#### <a name="to-create-a-new-storage-account-by-using-server-explorer"></a>Para criar uma nova conta de armazenamento através do Explorador de servidor

1. No Explorador do servidor, abra o menu de atalho para o nó de armazenamento e, em seguida, selecione criar a conta de armazenamento.

    ![Criar uma nova conta de armazenamento Azure](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

1. Selecione ou introduza as seguintes informações para a nova conta de armazenamento na caixa de diálogo **Criar conta de armazenamento** .

    - A subscrição Azure ao qual pretende adicionar a conta de armazenamento.

    - O nome que pretende utilizar para a nova conta de armazenamento.

    - A região ou grupo de afinidade (como EUA ocidental ou Ásia).

    - O tipo de replicação que pretende utilizar para a conta de armazenamento, tal como Geo redundantes.

1. Selecione **Criar**.

    A nova conta de armazenamento é apresentada na lista de **armazenamento** no Explorador de soluções.

#### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>Para anexar uma conta existente do armazenamento através do Explorador de servidor

1. No Explorador do servidor, abrir o menu de atalho para o nó do armazenamento Azure e, em seguida, selecione **Anexar armazenamento externo**.

    ![Adicionar uma conta existente do armazenamento](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)

1. Selecione ou introduza as seguintes informações para a nova conta de armazenamento na caixa de diálogo **Criar conta de armazenamento** .

    - O nome da conta de armazenamento existente que pretende anexar. Pode introduzir um nome ou selecione-a partir da lista.

    - A chave da conta de armazenamento selecionado. Este valor é normalmente fornecido por si quando seleciona uma conta de armazenamento. Se pretender que o Visual Studio para se lembrar da chave de conta de armazenamento, selecione a caixa memorizar de chave de conta.

    - O protocolo a utilizar para ligar para a conta de armazenamento, como HTTP, HTTPS ou um ponto final de personalizado. Veja [como configurar as cadeias de ligação](https://msdn.microsoft.com/library/azure/ee758697.aspx) para obter mais informações sobre os pontos finais personalizados.

### <a name="to-view-the-secondary-endpoints"></a>Para ver os pontos finais secundários

- Se tiver criado uma conta de armazenamento utilizando a opção de replicação **Acesso de leitura Geo redundantes** , pode ver finais secundários. Abrir o menu de atalho para o nome da conta e, em seguida, selecione **Propriedades**.

    ![Pontos finais secundários de armazenamento](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>Para remover uma conta de armazenamento a partir do Explorador de servidor

- No Explorador do servidor, abrir o menu de atalho para o nome da conta e, em seguida, selecione **Eliminar**. Se eliminar uma conta de armazenamento, quaisquer informações da chave guardadas para essa conta também são removidas.

    >[AZURE.NOTE] Se eliminar uma conta de armazenamento a partir do Explorador de servidor, não afeta a conta de armazenamento ou quaisquer dados nela contidos; remove simplesmente a referência a partir do Explorador de servidor. Para eliminar permanentemente uma conta de armazenamento, utilize o [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885).

## <a name="next-steps"></a>Próximos passos

Para saber que mais sobre como utilizam os serviços de armazenamento Azure, consulte o artigo [aceder a serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/ee405490.aspx).
