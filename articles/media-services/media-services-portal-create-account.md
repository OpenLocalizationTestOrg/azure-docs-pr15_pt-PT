<properties
    pageTitle=" Criar uma conta de serviços de multimédia do Azure com o portal do Azure | Microsoft Azure"
    description="Neste tutorial explica os passos da criação de uma conta de serviços de multimédia do Azure com o portal do Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Criar uma conta dos serviços de multimédia do Azure utilizando o portal do Azure

> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTO](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

Portal do Azure fornece uma forma para criar rapidamente uma conta de serviços de multimédia do Azure (MGA). Pode utilizar a sua conta para aceder a serviços de multimédia que permitem-lhe armazenar, encriptar, codificar, gerir e transmitir em fluxo conteúdo de multimédia no Azure. Ao tempo que cria uma conta dos serviços de multimédia, também criar uma conta de armazenamento associado (ou utilize um já existente) na mesma região geográfica como a conta dos serviços de multimédia.

Este artigo explica alguns conceitos comuns e mostra como criar uma conta dos serviços de multimédia com o portal do Azure.

## <a name="concepts"></a>Conceitos

Aceder a serviços de multimédia necessita de duas contas associadas:

- Uma conta dos serviços de multimédia. A conta dá-lhe acesso a um conjunto de baseado na nuvem dos serviços de multimédia que estão disponíveis no Azure. Uma conta dos serviços de multimédia não armazenar conteúdo multimédia real. Em vez disso, armazena metadados sobre o conteúdo de multimédia e as tarefas de processamento de multimédia na sua conta. Ao tempo que cria a conta, selecione uma região dos serviços de multimédia disponíveis. A região que selecionar é um centro de dados que armazena os registos de metadados para a sua conta.

    Regiões de serviços de multimédia (MGA) disponíveis incluem o seguinte: Europa Norte, Europa Ocidental, EUA Ocidental, Leste dos EUA, Sudeste asiático, Ásia, oeste, Japão Leste Japão. Serviços de multimédia não utilizar afinidade grupos.
    
    AMS agora também está disponível nos seguintes centros de dados: sul do Brasil, Índia oeste, Sul Índia e Índia Central. Agora pode utilizar o portal do Azure para criar contas de serviço de multimédia e realizar várias tarefas descritas aqui. No entanto, o Live codificação não estiver ativado nestes centros de dados. Além disso, nem todos os tipos de codificação de unidades reservadas estão disponíveis nestes centros de dados.
    
    - Sul Brasil: Padrão e de codificação reservadas unidades básicas estão apenas disponíveis.
    - Índia oeste, Sul Índia: 

- Uma conta de armazenamento Azure. Contas de armazenamento têm de estar localizadas na mesma região geográfica como a conta dos serviços de multimédia. Quando cria uma conta dos serviços de multimédia, pode escolher uma conta de armazenamento existente na mesma região ou pode criar uma nova conta de armazenamento na mesma região. Se eliminar uma conta dos serviços de multimédia, blobs na sua conta de armazenamento relacionados não são eliminadas.

## <a name="create-an-ams-account"></a>Criar uma conta de AMS

Os passos nesta secção Mostrar como criar uma conta de AMS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** > **Web + Mobile** > **dos serviços de multimédia**.

    ![Criar dos serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. **Criar conta de serviços de multimédia** introduza valores necessários.

    ![Criar dos serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Em **Nome da conta**, introduza o nome da nova conta de AMS. Um nome de conta dos serviços de multimédia é todos os números em minúsculas ou letras sem espaços e é 3 para 24 carateres de comprimento.
    2. Na sua subscrição, selecione entre as diferentes subscrições Azure que tiver acesso a.
    
    2. No **Grupo de recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é um conjunto de recursos que partilham o ciclo de vida, permissões e políticas de. Saiba mais [aqui](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Na **localização**, selecione a região geográfica que será utilizada para armazenar os registos de multimédia e de metadados para a sua conta dos serviços de multimédia. Esta região será utilizada para processar e transmitir em fluxo os ficheiros de multimédia. Apenas as regiões dos serviços de multimédia disponíveis aparecem na caixa de lista pendente. 
    
    3. Na **Conta de armazenamento**, selecione uma conta de armazenamento para fornecer o armazenamento de Blobs do conteúdo multimédia da sua conta dos serviços de multimédia. Pode selecionar uma conta de armazenamento existente na mesma região geográfica como a sua conta dos serviços de multimédia, ou pode criar uma conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras de nomes de conta de armazenamento são iguais para contas dos serviços de multimédia.

        Saiba mais sobre o armazenamento [aqui](storage-introduction.md).

    4. Selecione **Afixar ao dashboard** para ver o progresso de implementação de conta.
    
7. Clique em **Criar** na parte inferior do formulário.

    Assim que a conta estiver criada com êxito, o estado muda para **a executar**. 

    ![Definições de serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para gerir a sua conta AMS (por exemplo, carregar vídeos, codificar elementos, monitorizar o progresso da tarefa) utilize a janela **Definições** .

## <a name="manage-keys"></a>Gerir chaves

Precisa do nome da conta e as informações da chave primárias através de programação acesso à conta dos serviços de multimédia.

1. No portal do Azure, selecione a sua conta. 

    Na janela **Definições** que é apresentado à direita. 

2. Na janela **Definições** , selecione **teclas**. 

    As janelas de **Gerir chaves** mostra o nome da conta e as teclas principais e secundárias é apresentada. 
3. Prima o botão Copiar para copiar os valores.
    
    ![Teclas de serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Próximos passos

Agora pode carregar ficheiros para a sua conta AMS. Para mais informações, consulte o artigo [carregar ficheiros](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


