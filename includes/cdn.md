# <a name="using-cdn-for-azure"></a>Utilizar o CDN para Azure

Rede de entrega de conteúdos (CDN) o Azure oferece os programadores uma solução global para fornecer o conteúdo de largura de banda elevada por colocação em cache blobs e em conteúdo estático de instâncias de cluster na físicas nós nos Estados Unidos, Europa, países da Ásia, Austrália e América do Sul. Para obter uma lista de localizações de nó CDN atual, consulte o artigo [Azure CDN nó localizações].

Esta tarefa inclui os seguintes passos:

* [Passo 1: Criar uma conta de armazenamento](#Step1)
* [Passo 2: Criar um novo ponto final de CDN para a sua conta de armazenamento](#Step2)
* [Passo 3: Aceder aos seus conteúdos CDN](#Step3)
* [Passo 4: Remover o seu conteúdo CDN](#Step4)

Os benefícios da utilização CDN para colocar em cache Azure dados incluem:

-   Melhor desempenho e utilizador experiência utilizadores finais que são longe de ser uma origem de conteúdo e estiver a utilizar aplicações onde muitos 'viagens de internet' são necessários para carregar conteúdo
-   Grande distribuído Dimensionar para melhor gerir caso de carga elevado instantâneo, diga no início de um evento como um lançamento do produto

Clientes CDN existentes agora podem utilizar a CDN Azure no [Azure portal clássica]. A CDN é uma funcionalidade do suplemento à sua subscrição e tem um [plano de faturação]de separada.

<a id="Step1"> </a>
<h2>Passo 1: Criar uma conta de armazenamento</h2>

Utilize o procedimento seguinte para criar uma nova conta de armazenamento para uma subscrição Azure. Uma conta de armazenamento proporciona acesso a serviços de armazenamento Azure. A conta de armazenamento representa o mais alto nível do espaço de nomes para aceder a cada um dos componentes de serviço de armazenamento Azure: Blob serviços, serviços de fila e serviços de tabela. Para mais informações sobre os serviços de armazenamento Azure, consulte o artigo [utilizar os serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Para criar uma conta de armazenamento, tem de ser o administrador do serviço ou um administrador de cocriação para a subscrição associada.

> [AZURE.NOTE] Para obter informações acerca da realização esta operação ao utilizar a API de gestão do Azure serviço, consulte o tópico de referência de [Criar a conta de armazenamento](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) .

**Para criar uma conta de armazenamento para uma subscrição do Azure**

1.  Inicie sessão no [portal clássica Azure].
2.  No canto inferior esquerdo, clique em **Novo**. Na caixa de diálogo **Novo** , selecione os **Serviços de dados**, em seguida, clique em **armazenamento**, em seguida, **Criar rápida**.

    É apresentada a caixa de diálogo **Criar conta de armazenamento** .

    ![Criar a conta de armazenamento][create-new-storage-account]

4. No campo **URL** , escreva um nome de subdomínio. Esta entrada pode conter a partir de 3-24 letras em minúsculas e números.

    Este valor torna-se o nome do anfitrião o URI que é utilizado para endereçar recursos Blob, fila ou tabela para a subscrição. Para um recurso de contentor no serviço BLOBs de endereço, teria de utilizar um URI no seguinte formato, onde * &lt;StorageAccountLabel&gt; * refere-se para o valor que escreveu em **Introduza um URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importantes:** A etiqueta de URL o subdomínio da conta de armazenamento URI de formulários e têm de ser exclusiva entre todos os serviços alojados no Azure.

    Este valor também é utilizado como o nome desta conta de armazenamento no portal do ou quando aceder a esta conta através de programação.

5.  A partir da lista pendente de **Região/afinidade do grupo** , selecione uma região ou grupo de afinidade para a conta de armazenamento. Selecione um grupo de afinidade em vez de uma região se pretender que os seus serviços de armazenamento para ser no Centro de dados do mesmo com outros serviços do Windows Azure que está a utilizar. Isto pode melhorar o desempenho e taxas de não são suportadas pelo saída.  

    **Nota:** Para criar um grupo de afinidade, abrir a área de **Definições** do Portal de gestão, clique em **grupos de afinidade**e, em seguida, clique em **Adicionar um grupo de afinidade** ou **Adicionar**. Também pode criar e gerir grupos de afinidade utilizar a API de gestão de serviço do Windows Azure. Para mais informações, consulte o artigo [operações em grupos de afinidade].

6. A partir da lista pendente de **subscrição** , selecione a subscrição que será utilizada com a conta de armazenamento.
7.  Clique em **criar a conta de armazenamento**. O processo de criação da conta de armazenamento poderá demorar vários minutos a concluir.
8.  Para verificar que a conta de armazenamento foi criada com êxito, certifique-se de que a conta é apresentada na itens listados para **armazenamento** com o estado **Online**.

<a id="Step2"> </a>
<h2>Passo 2: Criar um novo ponto final de CDN para a sua conta de armazenamento</h2>

Depois de ativar o acesso CDN a uma conta de armazenamento ou alojado serviço, todos os objetos disponíveis ao público são elegíveis para CDN colocação em cache do limite. Se modificar um objeto que está atualmente em cache na CDN, os novos conteúdos não estarão disponíveis através da CDN até a CDN atualiza o respetivo conteúdo quando expira em cache conteúdo time to live período de tempo.

**Para criar um novo ponto final de CDN para a sua conta de armazenamento**

1. No [portal clássica Azure], no painel de navegação, clique em **CDN**.

2. No Friso, clique em **Novo**. Na caixa de diálogo **Novo** , selecione **Os serviços de aplicação**, **CDN**e, em seguida **Criação rápida**.

3. Na lista pendente **Origem domínio** , selecione a conta de armazenamento que criou na secção anterior na lista das suas contas de armazenamento disponível. 

4. Clique no botão **Criar** para criar o novo ponto final.

5. Quando o ponto final estiver criado, aparece uma lista de pontos finais para a subscrição. A vista de lista mostra o URL para o utilizar para aceder a conteúdo em cache, bem como o domínio de origem. 

    O domínio de origem é a localização a partir do qual a CDN coloca em cache conteúdo. O domínio de origem pode ser uma conta de armazenamento ou num serviço na nuvem; uma conta de armazenamento é utilizada para fins de neste exemplo. Conteúdo de armazenamento é colocada em cache para servidores edge acordo com para uma definição de controlo de cache que especificar, ou para a heurística predefinido da rede colocação em cache. 


    > [AZURE.NOTE] A configuração criada para o ponto final não imediatamente estará disponível; pode demorar até 60 minutos para o registo a serem propagadas através da rede CDN. Os utilizadores que tentarem para utilizar o nome de domínio CDN imediatamente poderão receber código de estado 400 (pedido incorrecto) até que o conteúdo está disponível através de CDN.

<a id="Step3"> </a>
<h2>Passo 3: Acesso CDN conteúdo</h2> 

Para aceder ao conteúdo em cache na CDN, utilize o URL de CDN fornecido no portal. O endereço para um blob em cache será semelhante ao seguinte:

http://<*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Passo 4: Remover conteúdo da CDN</h2>

Se já não pretender um objeto na rede Azure de conteúdo de entrega (CDN) em cache, pode tirar um dos seguintes passos:

-   Para um BLOBs do Azure, pode eliminar o BLOBs do contentor público.
-   Pode tornar o contentor privado em vez de público. Para mais informações, consulte [Restringir o acesso a contentores e Blobs](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) .
-   Pode desativar ou eliminar o ponto final de CDN utilizando o Portal de gestão.
-   Pode modificar o seu serviço alojado já não responder aos pedidos para o objeto.

Um objeto já em cache na CDN permanecerá em cache até o período de time to live para o objeto expirar. Quando o período de time to live expirar, a CDN irá Verifique se o ponto final de CDN ainda estiver válido e o objeto de forma anónima continuam a estar acessível. Se não for, em seguida, o objeto será já não ser colocada em cache.

A capacidade de limpar imediatamente conteúdo atualmente não é suportada no Portal de gestão do Azure. Contacte o [suporte do Azure](https://azure.microsoft.com/support/options/) se precisar de imediatamente Limpar conteúdo. 

## <a name="additional-resources"></a>Recursos adicionais

-   [Como criar um grupo de afinidade no Azure]
-   [Como: Gerir contas de armazenamento para uma subscrição do Azure]
-   [Sobre a API de gestão de serviço]
-   [Como mapear CDN conteúdo para um domínio personalizado]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Azure CDN nó localizações]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Azure portal clássico]: https://manage.windowsazure.com/
  [plano de faturação]: /pricing/calculator/?scenario=full
  [Como criar um grupo de afinidade no Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Sobre a API de gestão de serviço]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Como mapear CDN conteúdo para um domínio personalizado]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
