<properties
    pageTitle="Gerir armazenamento de Blobs do Azure recursos com o Explorador de armazenamento (pré-visualização) | Microsoft Azure"
    description="Gerir contentores de Blobs do Azure e Blobs com o Explorador de armazenamento (pré-visualização)"
    services="storage"
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
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Gerir armazenamento de Blobs do Azure recursos com o Explorador de armazenamento (pré-visualização)

## <a name="overview"></a>Descrição geral

[Armazenamento de Blobs do Azure](./storage/storage-dotnet-how-to-use-blobs.md) é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS.
Pode utilizar o armazenamento de BLOBs para expor dados publicamente a parte do mundo, ou para armazenar os dados da aplicação em privado. Neste artigo, irá obter informações sobre como utilizar o Explorador de armazenamento (pré-visualização) para trabalhar com contentores blob e blobs.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

- [Transferir e instalar o Explorador de armazenamento (pré-visualização)](http://www.storageexplorer.com)
- [Ligar a uma conta de armazenamento Azure ou serviço](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Criar um contentor blob

Todos os blobs tem residem num contentor BLOBs, que é simplesmente um agrupamento lógico blobs. Uma conta pode conter um número ilimitado de contentores e cada contentor pode armazenar um número ilimitado de blobs.

Os passos seguintes ilustram como criar um contentor de Blobs do Explorador de armazenamento (pré-visualização).

1.  Abra o Explorador de armazenamento (pré-visualização).
1.  No painel da esquerda, expanda a conta de armazenamento dentro do qual pretende criar o contentor blob.
1.  Com o botão direito **Blob contentores**e - no menu de contexto - selecione **Criar contentor Blob**.

    ![Criar o menu de contexto do blob contentores][0]

1.  Será apresentada uma caixa de texto abaixo da pasta **Blob contentores** . Introduza o nome para o contentor blob. Consulte a secção de [regras de nomenclatura de contentor](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) para obter uma lista de restrições e regras no blob contentores de atribuição de nomes.

    ![Criar a caixa de texto Blob contentores][1]

1.  Prima **Enter** quando tiver concluído para criar o contentor blob ou **Esc** para cancelar. Assim que o contentor BLOBs foi criado com êxito, serão apresentado na pasta **BLOBs contentores** para a conta de armazenamento selecionado.

    ![Contentor de BLOBs criado][2]

## <a name="view-a-blob-containers-contents"></a>Ver o conteúdo de um contentor BLOBs

Blob contentores contêm blobs e pastas (que também podem conter blobs).

Os passos seguintes ilustram como ver os conteúdos de um contentor de Blobs do Explorador de armazenamento (pré-visualização):

1.  Abra o Explorador de armazenamento (pré-visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contentor blob que pretende ver.
1.  Expanda a conta de armazenamento **Blob contentores**.
1.  Botão direito do rato no contentor de BLOBs que pretende ver e, - no menu de contexto - selecione **Abrir o Editor de contentor Blob**.
Também pode fazer duplo clique no contentor BLOBs que pretende ver.

    ![Menu de contexto editor de contentor blob aberta][19]

1.  O painel principal irá apresentar conteúdo o contentor blob.

    ![Editor de contentor blob][3]

## <a name="delete-a-blob-container"></a>Eliminar um contentor blob

BLOBs contentores podem ser facilmente criados e eliminados conforme necessário. (Para ver como eliminar blobs individuais, consulte a secção, [Gerir blobs num contentor de BLOBs](./#managing-blobs-in-a-blob-container).)

Os passos seguintes ilustram como eliminar um contentor de Blobs do Explorador de armazenamento (pré-visualização):

1.  Abra o Explorador de armazenamento (pré-visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contentor blob que pretende ver.
1.  Expanda a conta de armazenamento **Blob contentores**.
1.  Botão direito do rato no contentor de BLOBs que pretende eliminar e, - no menu de contexto - selecione **Eliminar**.
Também pode premir **Eliminar** para eliminar o contentor blob atualmente selecionada.

    ![Eliminar o menu de contexto do contentor de BLOBs][4]

1.  Selecione **Sim** para a caixa de diálogo de confirmação.

    ![Eliminar blob confirmação contentor][5]

## <a name="copy-a-blob-container"></a>Copiar um contentor blob

Explorador de armazenamento (pré-visualização) permite-lhe copiar um contentor BLOBs para a área de transferência e, em seguida, cole desse contentor BLOBs outra conta de armazenamento. (Para ver como copiar blobs individuais, consulte a secção, [Gerir blobs num contentor de BLOBs](./#managing-blobs-in-a-blob-container).)

Os passos seguintes ilustram como copiar um contentor BLOBs de conta de armazenamento para outro.

1.  Abra o Explorador de armazenamento (pré-visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contentor blob que pretende copiar.
1.  Expanda a conta de armazenamento **Blob contentores**.
1.  Botão direito do rato no contentor de BLOBs que pretende copiar e, - no menu de contexto - selecione **Copiar Blob contentor**.

    ![Menu de contexto da cópia BLOBs contentor][6]

1.  Botão direito do rato a conta de armazenamento de "destino" pretendido no qual pretende colar o contentor blob e - no menu de contexto - selecione **Colar contentor Blob**.

    ![Menu de contexto do colar blob contentor][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obter as associações de segurança para um contentor blob

Uma [assinatura de acesso partilhado (SA)](./storage/storage-dotnet-shared-access-signature-part-1.md) oferece um acesso delegado para recursos na sua conta de armazenamento.
Isto significa que o utilizador pode conceder que um cliente limitado permissões para objetos na sua conta de armazenamento para um determinado período de tempo e com um conjunto específico de permissões, sem ter de partilhar o seu teclas de acesso de conta.

Os seguintes passos mostram como criar uma SA para um contentor blob:

1.  Abra o Explorador de armazenamento (pré-visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs para o qual pretende obter uma SA.
1.  Expanda a conta de armazenamento **Blob contentores**.
1.  Botão direito do rato no contentor de BLOBs pretendido e, - no menu de contexto - selecione **Obter assinatura acesso partilhado**.

    ![Obter o menu de contexto SA][8]

1.  Na caixa de diálogo **Partilhado assinatura do Access** , especifique a política, datas de início e de expiração, fuso horário e os níveis que pretende para o recurso de acesso.

    ![Obter SA opções][9]

1.  Quando tiver terminado de especificar as opções de SA, selecione **Criar**.

1.  Uma caixa de diálogo **Assinatura de acesso partilhado** segunda, em seguida, irá apresentar que lista o contentor blob juntamente com o URL e QueryStrings pode utilizar para aceder ao recurso de armazenamento.
Selecione **Copiar** junto ao URL que pretende copiar para a área de transferência.

    ![Copiar URLs SA][10]

1.  Quando terminar, selecione **Fechar**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gerir políticas de acesso de um contentor blob

Os passos seguintes ilustram como gerir (adicionar e remover) aceder às políticas para um contentor blob:

1.  Abra o Explorador de armazenamento (pré-visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contentor BLOBs cujas políticas de acesso que pretende gerir.
1.  Expanda a conta de armazenamento **Blob contentores**.
1.  Selecione o contentor blob pretendido e, - no menu de contexto - selecione **Gerir políticas de acesso**.

    ![Gerir o menu de contexto de políticas de acesso][11]

1.  A caixa de diálogo de **Políticas de acesso** irá listar quaisquer políticas de acesso já criadas o contentor de BLOBs selecionado.

    ![Opções de política de acesso][12]        

1.  Siga estes passos dependendo a tarefa de gestão de política do access:

    - **Adicionar uma nova política de acesso** - selecione **Adicionar**. Depois de gerado, a caixa de diálogo de **Políticas de acesso** irá apresentar a política de acesso recentemente adicionado (com as predefinições).
    - **Editar uma política de acesso** - Efetue qualquer edições pretendidas e, selecione **Guardar**.
    - **Remover uma política de acesso** - selecionar **Remover** junto a política de acesso que pretende remover.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Definir o nível de acesso do público para um contentor blob

Por predefinição, todas as contentor blob está definido para "Sem acesso do público".

Os passos seguintes ilustram como especificar um nível de acesso do público para um contentor blob.

1.  Abra o Explorador de armazenamento (pré-visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contentor de BLOBs cujas políticas de acesso que pretende gerir.
1.  Expanda a conta de armazenamento **Blob contentores**.
1.  Selecione o contentor BLOBs pretendido e, - no menu de contexto - selecione **Definir nível de acesso do público**.

    ![Definir o menu de contexto de nível de acesso do público][13]

1.  Na caixa de diálogo **Definir nível de acesso do público contentor** , especifique o nível de acesso pretendido.

    ![Definir opções de nível de acesso do público][14]

1.  Selecione **Aplicar**.

## <a name="managing-blobs-in-a-blob-container"></a>Gerir blobs num contentor de BLOBs

Depois de criar um contentor blob, pode carregar um blob para desse contentor blob, transfira um blob para o computador local, abra um blob no seu computador local e muito mais.

Os passos seguintes ilustram como gerir blobs (e pastas) dentro de um contentor blob.

1.  Abra o Explorador de armazenamento (pré-visualização).
1.  No painel esquerdo, expanda a conta de armazenamento que contém o contentor blob que pretende gerir.
1.  Expanda a conta de armazenamento **Blob contentores**.
1.  Faça duplo clique no contentor de BLOBs que pretende ver.
1.  O painel principal irá apresentar conteúdo o contentor blob.

    ![Contentor de Blobs do Vista][3]

1.  O painel principal irá apresentar conteúdo o contentor blob.

1.  Siga estes passos dependendo da tarefa que pretende executar:

    - **Carregar ficheiros para um contentor blob**

        1.  Na barra de ferramentas do painel principal, selecione **carregar**e, em seguida, **Carregar ficheiros** a partir do menu pendente.

            ![Ficheiros menu carregar][15]

        1.  Na caixa de diálogo **carregar ficheiros** , selecione o botão de reticências (…****) no lado direito da caixa de texto de **ficheiros** para selecionar os ficheiros que pretende carregar.

            ![Carregar ficheiros de opções][16]

        1.  Especifique o tipo do **tipo de BLOBs**. O artigo [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica as diferenças entre os vários tipos de Blobs.

        1.  Opcionalmente, especifique uma pasta de destino nas quais os ficheiros seleccionados serão carregados. Se a pasta de destino não existir, será criado.

        1.  Selecione **carregar**.

    - **Carregar uma pasta para um contentor blob**

        1.  Na barra de ferramentas do painel principal, selecione **carregar**e, em seguida, **Carregar pastas** a partir do menu pendente.

            ![Pasta menu carregar][17]

        1.  Na caixa de diálogo **carregar pastas** , selecione o botão de reticências (…****) no lado direito da caixa de texto de **pasta** para selecionar a pasta cujo conteúdo que pretende carregar.

            ![Carregar opções de pastas][18]

        1.  Especifique o tipo do **tipo de BLOBs**. O artigo [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explica as diferenças entre os vários tipos de blob.

        1.  Opcionalmente, especifique uma pasta de destino nas quais vai ser carregado conteúdo da pasta selecionada. Se a pasta de destino não existir, será criado.

        1.  Selecione **carregar**.

    - **Transfira um blob para o computador local**

        1.  Selecione o blob que pretende transferir.

        1.  Na barra de ferramentas do painel principal, selecione **Transferir**.

        1.  Na caixa de diálogo **especificar onde pretende guardar o blob transferido** , especifique a localização onde pretende que o blob transferido e o nome que pretende dar-lhe.  

        1.  Selecione **Guardar**.

    - **Abrir um blob no seu computador local**

        1.  Selecione o blob que pretende abrir.

        1.  Na barra de ferramentas do painel principal, selecione **Abrir**.

        1.  O blob será transferido e abertos utilizando a aplicação associada ao tipo de ficheiro subjacente a BLOBs.

    - **Copiar um blob para a área de transferência**

        1.  Selecione o blob que pretende copiar.

        1.  Na barra de ferramentas do painel principal, selecione **uma cópia**.

        1.  No painel esquerdo, navegue para outro contentor blob e faça duplo clique para visualizá-lo no painel principal.

        1.  Na barra de ferramentas do painel principal, selecione **Colar** para criar uma cópia do blob.

    - **Eliminar um blob**

        1.  Selecione o blob que pretende eliminar.

        1.  Na barra de ferramentas do painel principal, selecione **Eliminar**.

        1.  Selecione **Sim** para a caixa de diálogo de confirmação.

## <a name="next-steps"></a>Próximos passos

- Ver o [Explorador de armazenamento mais recente (pré-visualização) notas de lançamento e vídeos](http://www.storageexplorer.com).
- Saiba como [criar aplicações utilizando blobs do Azure, tabelas, filas e ficheiros](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png