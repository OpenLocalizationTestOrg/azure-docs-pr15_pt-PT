<properties
    pageTitle="Lista de conta de armazenamento Azure"
    description="Gerir as definições de conta de armazenamento utilizando o Toolkit de Azure para Eclipse"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# <a name="azure-storage-account-list"></a>Lista de conta de armazenamento Azure #

Contas do Azure armazenamento ativar localizações de transferência ser utilizado para sua JDK, servidor de aplicações e componentes arbitrários, bem como para armazenar estado ao utilizar a colocação em cache. Eclipse mantém uma lista de contas de armazenamento conhecidos que estão disponíveis para os seus projetos na área de trabalho Eclipse. Para abrir a caixa de diálogo **Contas de armazenamento** , que é utilizada para gerir nessa lista, dentro de Eclipse, clique em **janela**, clique em **Preferências**, expanda **Azure**e, em seguida, clique em **Contas de armazenamento**.

A imagem seguinte mostra a caixa de diálogo **Contas de armazenamento** .

![][ic719496]

Esta caixa de diálogo também pode ser aberta a partir de uma ligação de **contas** nas caixas de diálogo utilizar contas de armazenamento, como as seguintes:

* O separador **JDK** da caixa de diálogo de **Configuração do servidor** .
* No separador **servidor** da caixa de diálogo de **Configuração do servidor** .
* Caixa de diálogo **Adicionar um componente** .
* A caixa de diálogo Propriedades de **Colocação em cache** .

## <a name="to-import-your-storage-accounts-using-a-publish-settings-file"></a>Para importar o seu armazenamento contas com um ficheiro de definições de publicar ##

1. Na caixa de diálogo **Contas de armazenamento** , clique em **Importar a partir de ficheiro de definições de publicar**.
2. (Ignorar este passo se já tiver guardado um ficheiro de definições de publicar para o seu computador local.) Na caixa de diálogo **Importar as informações da subscrição** , clique em **Transferir o ficheiro de definições de publicar**. Se ainda não está iniciou para a sua conta Azure, vai ser-lhe para iniciar sessão. Em seguida, será solicitado que guardar um Azure publicar o ficheiro de definições. (Pode ignorar as instruções resultantes apresentadas nas páginas de início de sessão - são fornecidos pelo portal do Azure e destinam-se para utilizadores do Visual Studio.) Guardá-lo para o seu computador local.
3. Ainda na caixa de diálogo **Importar as informações da subscrição** , clique no botão **Procurar** , selecione o ficheiro de definições de publicar que guardou localmente anteriormente e, em seguida, clique em **Abrir**.
4. Clique em **OK** para fechar a caixa de diálogo **Importar as informações da subscrição** .

## <a name="to-create-a-new-storage-account"></a>Para criar uma nova conta de armazenamento ##

1. Na caixa de diálogo **Contas de armazenamento** , clique em **Adicionar**.
2. Na caixa de diálogo **Adicionar conta de armazenamento** , clique em **Novo**.
3. Na caixa de diálogo **Nova conta de armazenamento** , especifique valores para o seguinte:
    * Nome da conta de armazenamento.
    * Localização da conta de armazenamento.
    * Descrição da conta de armazenamento.
    * A subscrição à qual pertence a conta de armazenamento.
4. Clique em **OK** para fechar a caixa de diálogo **Nova conta de armazenamento** .

Poderá demorar vários minutos até que a sua conta de armazenamento para ser criado. Após ter sido criada, clique em **OK** para fechar a caixa de diálogo **Adicionar conta de armazenamento** , e a nova conta de armazenamento será adicionada à lista de contas de armazenamento disponível.

## <a name="to-add-an-existing-storage-account-to-the-list"></a>Para adicionar uma conta existente do armazenamento à lista ##

1. Se ainda não tiver uma conta de armazenamento Azure, criar uma ao seguir os passos listados na **para criar uma nova secção de conta de armazenamento** acima. (Em alternativa, pode criar uma nova conta de armazenamento no [Portal de gestão do Azure][]).
2. Na caixa de diálogo **Contas de armazenamento** , clique em **Adicionar**.
3. Na caixa de diálogo **Adicionar conta de armazenamento** , introduza valores para o **nome** e a **Chave de acesso**. A chave de acesso e nome da conta tem de ser para uma conta de armazenamento Azure existente. Utilize a secção de **armazenamento** do [Portal de gestão do Azure][] para ver os seus nomes de conta de armazenamento e chaves. A caixa de diálogo **Adicionar conta de armazenamento** terá uma aspeto semelhante ao seguinte.

    ![][ic719497]

4. Clique em **OK** para fechar a caixa de diálogo **Adicionar conta de armazenamento** .

## <a name="to-modify-a-storage-account-to-use-a-new-access-key"></a>Para modificar uma conta de armazenamento para utilizar uma nova chave de acesso ##

1. Na caixa de diálogo **Contas de armazenamento** , clique na conta de armazenamento que pretende editar e, em seguida, clique em **Editar**.
2. Na caixa de diálogo **Editar tecla de acesso de conta de armazenamento** , modifique o valor de **Chave de acesso** .
3. Clique em **OK** para fechar a caixa de diálogo **Editar tecla de acesso de conta de armazenamento** .

## <a name="to-remove-a-storage-account-from-the-list-maintained-in-eclipse"></a>Para remover uma conta de armazenamento a partir da lista mantida nas Eclipse ##

1. Na caixa de diálogo **Contas de armazenamento** , clique na conta de armazenamento que pretende editar e, em seguida, clique em **Remover**.
2. Clique em **OK** quando lhe for pedido para remover a conta de armazenamento.

>[AZURE.NOTE] Remover a conta de armazenamento através da caixa de diálogo **Contas de armazenamento** só remove-a partir da lista de contas de armazenamento visualizáveis dentro Eclipse. Não remove a conta de armazenamento da sua subscrição Azure. Para além disso, a conta de armazenamento pode aparecer novamente na sua lista depois de Eclipse recarrega os detalhes da sua subscrição.

## <a name="see-also"></a>Consulte também ##

[Toolkit Azure para Eclipse][]

[Instalar o Toolkit Azure para Eclipse][] 

[Criar uma aplicação do mundo Olá Azure no Eclipse][]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Portal de gestão do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png
