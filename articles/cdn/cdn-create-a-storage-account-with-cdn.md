<properties
    pageTitle="Integrar uma conta de armazenamento CDN | Microsoft Azure"
    description="Saiba como utilizar a rede de entrega de conteúdos de Azure (CDN) ao executar a largura de banda elevada conteúdo por blobs do Azure armazenamento de colocação em cache."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="integrate-a-storage-account-with-cdn"></a>Integrar uma conta de armazenamento CDN

CDN pode ser ativado para o conteúdo da cache a partir do seu armazenamento Azure. Os programadores é oferece uma solução global para fornecer o conteúdo de largura de banda elevada por colocação em cache blobs e em conteúdo estático de instâncias de cluster na físicas nós nos Estados Unidos, Europa, países da Ásia, Austrália e América do Sul.


## <a name="step-1-create-a-storage-account"></a>Passo 1: Criar uma conta de armazenamento

Utilize o procedimento seguinte para criar uma nova conta de armazenamento para uma subscrição Azure. Uma conta de armazenamento proporciona acesso a serviços de armazenamento Azure. A conta de armazenamento representa o mais alto nível do espaço de nomes para aceder a cada um dos componentes de serviço de armazenamento Azure: Blob serviços, serviços de fila e serviços de tabela. Para obter mais informações, consulte a [Introdução ao Microsoft Azure armazenamento](../storage/storage-introduction.md).

Para criar uma conta de armazenamento, tem de ser o administrador do serviço ou um administrador de cocriação para a subscrição associada.

> [AZURE.NOTE] Existem vários métodos que pode utilizar para criar uma conta de armazenamento, incluindo o Portal do Azure e Powershell.  Para este tutorial, iremos utilizar o Portal do Azure.  

**Para criar uma conta de armazenamento para uma subscrição do Azure**

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).
2.  No canto superior esquerdo, selecione **Novo**. Na caixa de diálogo **Novo** , selecione **dados + armazenamento**, em seguida, clique em **conta de armazenamento**.

    É apresentada a pá de **criar a conta de armazenamento** .

    ![Criar a conta de armazenamento][create-new-storage-account]

4. No campo **nome** , escreva um nome de subdomínio. Esta entrada pode conter 3-24 letras em minúsculas e números.

    Este valor torna-se o nome do anfitrião o URI que é utilizado para endereçar recursos Blob, fila ou tabela para a subscrição. Para um recurso de contentor no serviço Blob de endereço, teria de utilizar um URI no seguinte formato, onde * &lt;StorageAccountLabel&gt; * refere-se para o valor que escreveu em **Introduza um URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importantes:** A etiqueta de URL o subdomínio da conta de armazenamento URI de formulários e têm de ser exclusiva entre todos os serviços alojados no Azure.

    Este valor também é utilizado como o nome desta conta de armazenamento no portal do ou quando aceder a esta conta através de programação.

5. Deixe as predefinições para o **modelo de implementação**, **tipo de conta**, **Desempenho**e **replicação**. 

6. Selecione a **subscrição** que será utilizada com a conta de armazenamento.

7. Selecione ou criar um **Grupo de recursos**.  Para mais informações sobre os grupos de recursos, consulte o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

8. Selecione uma localização para a sua conta de armazenamento.

8. Clique em **Criar**. O processo de criação da conta de armazenamento poderá demorar vários minutos a concluir.


## <a name="step-2-create-a-new-cdn-profile"></a>Passo 2: Criar um novo perfil CDN

Um perfil CDN é uma coleção de pontos finais de CDN.  Cada perfil contém um ou mais pontos finais CDN.  Pode optar por utilizar vários perfis para organizar os pontos finais de CDN ao domínio da internet, aplicação web ou por outros critérios algumas.

> [AZURE.TIP] Se já tiver um perfil CDN que pretende utilizar para este tutorial, avance para o [passo 3](#step-3-create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="step-3-create-a-new-cdn-endpoint"></a>Passo 3: Criar um novo ponto final CDN

**Para criar um novo ponto final de CDN para a sua conta de armazenamento**

1. No [Portal de gestão do Azure](https://portal.azure.com), navegue até ao seu perfil CDN.  Pode pode ter afixada-lo para o dashboard no passo anterior.  Se não, pode encontrá-lo clicando em **Procurar**, em seguida, **perfis CDN**e clicar no perfil que pretende adicionar o seu ponto final para.

    É apresentada a pá de perfil CDN.

    ![Perfil CDN][cdn-profile-settings]

2. Clique no botão **Adicionar ponto final** .

    ![Adicionar botão de ponto final][cdn-new-endpoint-button]

    É apresentada a pá de **Adicionar um ponto final** .

    ![Adicionar pá de ponto final][cdn-add-endpoint]

3. Introduza um **nome** para este ponto final CDN.  Este nome será utilizada para aceder aos seus recursos em cache no domínio `<endpointname>.azureedge.net`.

4. Na lista pendente **tipo de origem** , selecione *armazenamento*.  

5. Na lista pendente **origem hostname** , selecione a sua conta de armazenamento.

6. Deixe as predefinições para o **caminho de origem**, **cabeçalho de anfitrião de origem**e **porta de protocolo/origem**.  Tem de especificar pelo menos uma protocol (HTTP ou HTTPS).

    > [AZURE.NOTE] Esta configuração permite que todos os contentores publicamente visíveis na sua conta de armazenamento para colocação em cache na CDN.  Se pretende limitar o âmbito para um único contentor, utilize o **caminho de origem**.  Tenha em atenção que o contentor tem de ter a sua visibilidade definir ao pública.

7. Clique no botão **Adicionar** para criar o novo ponto final.

8. Quando o ponto final estiver criado, aparece uma lista de pontos finais para o perfil. A vista de lista mostra o URL para o utilizar para aceder a conteúdo em cache, bem como o domínio de origem.

    ![Ponto final CDN][cdn-endpoint-success]

    > [AZURE.NOTE] O ponto final não imediatamente estará disponível para utilização.  Pode demorar até 90 minutos para o registo a serem propagadas através da rede CDN. Os utilizadores que tentarem para utilizar o nome de domínio CDN imediatamente poderão receber o código de estado 404 até que o conteúdo está disponível através de CDN.


## <a name="step-4-access-cdn-content"></a>Passo 4: Acesso CDN conteúdo

Para aceder ao conteúdo em cache na CDN, utilize o URL de CDN fornecido no portal. O endereço para um blob em cache será semelhante ao seguinte:

http://<*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [AZURE.NOTE] Depois de ativar o acesso CDN a uma conta de armazenamento ou alojado serviço, todos os objetos disponíveis ao público são elegíveis para CDN colocação em cache do limite. Se modificar um objeto que está atualmente em cache na CDN, os novos conteúdos não estarão disponíveis através da CDN até a CDN atualiza o respetivo conteúdo quando expira em cache conteúdo time to live período de tempo.

## <a name="step-5-remove-content-from-the-cdn"></a>Passo 5: Remover conteúdo da CDN

Se já não pretender um objeto na rede Azure de conteúdo de entrega (CDN) em cache, pode tirar um dos seguintes passos:

-   Pode tornar o contentor privado em vez de público. Para mais informações, consulte [Gerir anónimo acesso de leitura contentores e blobs](../storage/storage-manage-access-to-resources.md) .
-   Pode desativar ou eliminar o ponto final de CDN utilizando o Portal de gestão.
-   Pode modificar o seu serviço alojado já não responder aos pedidos para o objeto.

Um objeto já em cache na CDN permanecerão em cache até o período de time to live para o objeto expirar ou até o ponto final é eliminado. Quando o período de time to live expirar, a CDN irá Verifique se o ponto final de CDN ainda estiver válido e o objeto de forma anónima continuam a estar acessível. Se não for, em seguida, o objeto será já não ser colocada em cache.


## <a name="additional-resources"></a>Recursos adicionais

-   [Como mapear CDN conteúdo para um domínio personalizado](cdn-map-content-to-custom-domain.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png

[cdn-profile-settings]: ./media/cdn-create-a-storage-account-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-a-storage-account-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-a-storage-account-with-cdn/cdn-endpoint-success.png
