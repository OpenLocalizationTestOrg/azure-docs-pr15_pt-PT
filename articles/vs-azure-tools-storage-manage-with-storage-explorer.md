<properties
    pageTitle="Introdução ao Explorador de armazenamento (pré-visualização) | Microsoft Azure"
    description="Gerir Armazenamento Azure recursos com o Explorador de armazenamento (pré-visualização)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="getting-started-with-storage-explorer-preview"></a>Introdução ao Explorador de armazenamento (pré-visualização)

## <a name="overview"></a>Descrição geral 

Explorador de armazenamento do Microsoft Azure (pré-visualização) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Windows Azure no Windows, OS X e Linux. Neste artigo, irá aprender as várias formas de ligar à e gestão das suas contas de armazenamento Azure.

![Explorador de armazenamento do Microsoft Azure (pré-visualização)][15]

## <a name="prerequisites"></a>Pré-requisitos

- [Transferir e instalar o Explorador de armazenamento (pré-visualização)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a uma conta de armazenamento ou serviço

Explorador de armazenamento (pré-visualização) fornece uma número infindável formas para ligar a contas de armazenamento. Isto inclui ligar a contas de armazenamento associadas a suas subscrições Azure, ligar a contas de armazenamento e de serviços partilhados a partir de outras subscrições Azure e ainda estabelecer ligação ao e gerir armazenamento local utilizando o emulador de armazenamento do Azure:

- [Ligar a uma subscrição do Azure](#connect-to-an-azure-subscription) - gerir recursos de armazenamento que pertencem à sua subscrição do Azure.
- [Trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage) - gerir armazenamento local utilizando o emulador de armazenamento do Azure. 
- [Anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account) - gerir recursos de armazenamento que pertencem para outra subscrição Azure utilizando a conta de armazenamento nome da conta e chave.
- [Conta de armazenamento de anexar utilizando SA](#attach-storage-account-using-sas) - gerir recursos de armazenamento que pertencem para outra subscrição Azure utilizando uma SA.
- [Utilizar o serviço de anexar SA](#attach-service-using-sas) - gerir um serviço de armazenamento específico (contentor blob, fila ou tabela) que pertencem para outra subscrição Azure utilizando uma SA.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

> [AZURE.NOTE] Se não tiver uma conta Azure, pode [Inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar os benefícios da sua Visual Studio subscritor](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. No Explorador de armazenamento (pré-visualização), selecione **definições de conta do Azure**. 

    ![Definições da conta Azure][0]

1. Painel do lado esquerdo agora irá apresentar todas as contas do Microsoft que tenha sessão iniciada. Para ligar a outra conta, selecione **Adicionar uma conta**e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que está associada com pelo menos uma subscrição ativa do Azure.

    ![Adicionar uma conta][1]

1. Assim que com êxito, iniciar sessão com uma conta Microsoft, irá preencher o painel do lado esquerdo com as subscrições Azure associadas a essa conta. Selecione as subscrições Azure com o qual pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **todas as subscrições** alterna selecionar todos ou nenhum das subscrições Azure listadas.)

    ![Selecione subscrições do Azure][3]

1. Painel do lado esquerdo agora irá apresentar as contas de armazenamento associadas as subscrições Azure selecionadas.

    ![Subscrições Azure seleccionadas][4]

## <a name="work-with-local-development-storage"></a>Trabalhar com o armazenamento de desenvolvimento local

Explorador de armazenamento (pré-visualização) permite-lhe trabalhar contra armazenamento local utilizando o emulador de armazenamento do Azure. Esta opção permite-lhe escrever código contra e testar o armazenamento sem necessariamente ter uma conta de armazenamento implementada no Azure (uma vez que a conta de armazenamento é a ser emulada pelo emulador armazenamento Azure).

>[AZURE.NOTE] O emulador de armazenamento do Azure atualmente só é suportado para Windows. 

1. No painel esquerdo do Explorador de armazenamento (pré-visualização), expanda o **(Local e anexadas** > **Armazenamento contas** > nó**(desenvolvimento)** .

    ![Nó de desenvolvimento local][21]

1. Se ainda não tiver instalado o emulador de armazenamento do Azure, será solicitado para fazê-lo através de uma barra de informações. Se for apresentada a barra de informações, selecione **Transferir a versão mais recente**e instalar o emulador. 

    ![Transferir o pedido de emulador de armazenamento do Azure][22]

1. Quando o emulador estiver instalado, terá a capacidade de criar e trabalhar com tabelas, filas e blobs locais. Para saber como trabalhar com cada tipo de conta de armazenamento, selecione na ligação adequada abaixo:

    - [Gerir recursos de armazenamento de Blobs do Azure](./vs-azure-tools-storage-explorer-blobs.md)
    - Gerir recursos de armazenamento de partilha de ficheiro Azure - *brevemente*
    - Gerir os recursos de armazenamento do Azure fila - *brevemente*
    - Gerir os recursos de armazenamento de tabela do Azure - *brevemente*

## <a name="attach-or-detach-an-external-storage-account"></a>Anexar ou desanexar uma conta de armazenamento externo

Explorador de armazenamento (pré-visualização) fornece a capacidade de anexar a contas de armazenamento externo para que as contas de armazenamento podem ser facilmente partilhadas. Esta secção explica como anexar a (e desanexar do) contas de armazenamento externo.

### <a name="get-the-storage-account-credentials"></a>Obter as credenciais de conta de armazenamento

Para partilhar uma conta de armazenamento externo, o proprietário dessa conta tem primeiro obter as credenciais - nome da conta e chave - a para a conta e, em seguida, partilhe essas informações com a pessoa que pretendem anexar a essa conta (externa). Obter as credenciais da conta de armazenamento pode ser feito através do portal do Azure seguindo estes passos: 

1.  Inicie sessão no [portal do Azure](https://portal.azure.com).
1.  Selecione **Procurar**.
1.  Selecione **contas de armazenamento**.
1.  No pá **Armazenamento contas** , selecione a conta de armazenamento pretendida.
1.  Na pá **Definições** da conta de armazenamento selecionado, selecione **as teclas de acesso**.

    ![Opção de teclas de acesso][5]
    
1.  Na pá **teclas de acesso** , copie os valores de **Nome de conta de armazenamento** e **chave 1** para utilizar quando ligar para a conta de armazenamento. 

    ![Teclas de acesso][6]

### <a name="attach-to-an-external-storage-account"></a>Ligar a uma conta de armazenamento externo
Para anexar a uma conta de armazenamento externo, terá de nome e a chave de conta. A secção *obter as credenciais de conta de armazenamento* explica como obter estes valores a partir do portal Azure. No entanto, tenha em atenção que no portal do, a chave de conta chama-se "chave 1" por isso, onde o Explorador de armazenamento (pré-visualização) pedir uma chave de conta, irá introduzir (ou colar) o valor de "chave 1". 
 
1.  No Explorador de armazenamento (pré-visualização), selecione **ligar ao armazenamento Azure**.

    ![Ligar a opção de armazenamento Azure][23]

1.  Na caixa de diálogo **ligar ao armazenamento do Windows Azure** , especifique a chave da conta ("chave 1" valor a partir do portal Azure) e, em seguida, selecione **seguinte**.

    ![Ligar à caixa de diálogo armazenamento Azure][24] 

1.  Na caixa de diálogo **Anexar externos armazenamento** , introduza o nome de conta de armazenamento na caixa **nome da conta** , especificar quaisquer outras definições desejadas e selecione **seguinte** quando terminar. 

    ![Anexar a caixa de diálogo armazenamento externo][8]

1.  Na caixa de diálogo **Resumo da ligação** , verifique se as informações. Se quiser alterar alguma coisa, selecione **novamente** e volte a introduzir as definições pretendidas. Assim que tiver terminado, selecione **Ligar**.

1.  Quando estiver ligado, será apresentada a conta de armazenamento externo com o texto **(externo)** acrescentados ao nome da conta de armazenamento. 

    ![Resultado de ligar a uma conta de armazenamento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desligar a partir de uma conta de armazenamento externo

1.  Botão direito do rato na conta de armazenamento externo que pretende desanexar e - no menu de contexto - selecione **Desanexar**.

    ![Desligar a opção de armazenamento][10]

1.  Quando for apresentada a caixa de mensagem de confirmação, selecione **Sim** para confirmar o destacados junto da conta de armazenamento externo.

## <a name="attach-storage-account-using-sas"></a>Anexar a conta de armazenamento utilizando SA

Uma [SA (partilhados assinatura do Access)](storage/storage-dotnet-shared-access-signature-part-1.md) dá o administrador de uma subscrição do Azure a capacidade de conceder acesso a uma conta de armazenamento numa base temporária sem ter de fornecer as respetivas credenciais de subscrição Azure. 

Para ilustrar isto, imaginemos utilizadora é um administrador de uma subscrição do Azure e utilizadora pretende permitir que UtilizadorB aceder a uma conta de armazenamento para um período de tempo limitado com determinadas permissões:

1. Utilizadora gera uma SA (que consiste a cadeia de ligação para a conta de armazenamento) para um determinado período de tempo e com as permissões pretendidas.
1. Utilizadora partilha as associações de segurança com a pessoa procurem acesso à conta de armazenamento - UtilizadorB, no nosso exemplo.  
1. UtilizadorB utiliza o Explorador de armazenamento (pré-visualização) para anexar a conta que pertencem a utilizadora utilizando as SA fornecidas. 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obter uma SA para a conta que pretende partilhar

1.  No Explorador de armazenamento (pré-visualização), com o botão direito na conta de armazenamento que pretende partilhar e, - no menu de contexto - selecione **Obter assinatura acesso partilhado**.

    ![Obter a opção de menu de contexto SA][13]

1. Na caixa de diálogo **Partilhado assinatura do Access** , especifique o período de tempo e as permissões que pretende para a conta e selecione **Criar**.

    ![Obter a caixa de diálogo SA][14]
 
1. Uma caixa de diálogo **Assinatura de acesso partilhado** segunda irão aparecer a apresentar as associações de segurança. Selecione **Copiar** junto a **Cadeia de ligação** para copiá-lo à área de transferência. Selecione **Fechar** para fechar a caixa de diálogo.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Anexar para a conta partilhada utilizando as associações de segurança

1.  No Explorador de armazenamento (pré-visualização), selecione **ligar ao armazenamento Azure**.

    ![Ligar a opção de armazenamento Azure][23]

1.  Na caixa de diálogo **ligar ao armazenamento do Windows Azure** , especifique a cadeia de ligação e, em seguida, selecione **seguinte**.

    ![Ligar à caixa de diálogo armazenamento Azure][24] 

1.  Na caixa de diálogo **Resumo da ligação** , verifique se as informações. Se quiser alterar alguma coisa, selecione **novamente** e volte a introduzir as definições pretendidas. Assim que tiver terminado, selecione **Ligar**.

1.  Uma vez anexada, a conta de armazenamento será apresentada com o texto (SA) acrescentado ao nome da conta que fornecido.

    ![Resultado de anexado a uma conta utilizando SA][17]

## <a name="attach-service-using-sas"></a>Anexar serviço utilizando SA

Secção de [conta de armazenamento de anexar utilizando SA](#attach-storage-account-using-sas) ilustra como um administrador de subscrição Azure pode conceder acesso temporário a uma conta de armazenamento gerando (e partilha de) uma SA para a conta de armazenamento. Do mesmo modo, uma SA podem ser gerada para um serviço específico (contentor blob, fila ou tabela) dentro de uma conta de armazenamento.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Gerar uma SA no serviço que pretende partilhar

Neste contexto, um serviço pode ser um contentor de BLOBs, fila ou tabela. As secções seguintes explicam como gerar as associações de segurança para o serviço listado:

- [Obter as associações de segurança para um contentor BLOBs](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Obter as associações de segurança para uma partilha de ficheiros - *brevemente*
- Obter as associações de segurança para uma fila - *brevemente*
- Obter as associações de segurança de uma tabela - *brevemente*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Anexar ao serviço de conta partilhada utilizando as associações de segurança

1.  No Explorador de armazenamento (pré-visualização), selecione **ligar ao armazenamento Azure**.

    ![Ligar a opção de armazenamento Azure][23]

1.  Na caixa de diálogo **ligar ao armazenamento do Windows Azure** , especifique o URI SA e, em seguida, selecione **seguinte**.

    ![Ligar à caixa de diálogo armazenamento Azure][24] 

1.  Na caixa de diálogo **Resumo da ligação** , verifique se as informações. Se quiser alterar alguma coisa, selecione **novamente** e volte a introduzir as definições pretendidas. Assim que tiver terminado, selecione **Ligar**.

1.  Uma vez anexada, o serviço recentemente anexado será apresentado sob o nó **(Serviço SA)** .

    ![Resultado de como anexar a um serviço partilhado utilizando SA][20]

## <a name="search-for-storage-accounts"></a>Pesquisa para contas de armazenamento

Se tiver uma longa lista de contas de armazenamento, uma forma rápida para localizar uma conta de armazenamento particular é utilizar a caixa de pesquisa na parte superior do painel da esquerda. 

Tal como estiver a escrever na caixa de pesquisa, o painel do lado esquerdo irá apresentar apenas as armazenamento contas que correspondem ao valor de procura que introduziu até que apontam. Captura de ecrã seguinte ilustra um exemplo onde posso tenha pesquisou todas as contas de armazenamento onde o nome da conta de armazenamento contém o texto "tarcher".

![Pesquisa de conta de armazenamento][11]
    
Para limpar a pesquisa, selecione o botão **x** na caixa de pesquisa.

## <a name="next-steps"></a>Próximos passos
- [Gerir recursos de armazenamento de Blobs do Azure com o Explorador de armazenamento (pré-visualização)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
