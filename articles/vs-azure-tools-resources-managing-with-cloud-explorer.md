<properties 
   pageTitle="Gerir os recursos Azure com o Explorador de nuvem | Microsoft Azure"
   description="Saiba como utilizar o Explorador de nuvem para procurar e gerir Azure recursos no Visual Studio."
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

# <a name="managing-azure-resources-with-cloud-explorer"></a>Gerir os recursos Azure com o Explorador da nuvem

##<a name="overview"></a>Descrição geral

Nuvem Explorer foi concebido para permitem-lhe mais fácil e rapidamente procurar e gerir os seus recursos Azure dentro IDE do Visual Studio. Pode, por exemplo, utilizá-la para abrir uma aplicação Web no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou num browser ou anexar um depurador à mesma, ou pode ver as propriedades de um contentor blob e abra-o no Editor de contentor Blob.

Nuvem Explorador está incorporado na pilha de Gestor Azure recurso, tal como o [Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040). -Recursos, tais como grupos de recursos Azure compreende e pode utilizar serviços Azure como as aplicações do lógica e API apps e suporta [o controlo de acesso baseado em funções](./active-directory/role-based-access-control-configure.md) (RBCA). Para ver recursos Azure que foram adicionados ou alterados, selecione o botão **Atualizar** na barra de ferramentas do Explorer na nuvem.

Nuvem Explorador está instalado como parte das ferramentas do Visual Studio para Azure SDK 2.7. 

## <a name="prerequisites"></a>Pré-requisitos

- RTM de 2015 do Visual Studio.

- As ferramentas do Visual Studio para Azure SDK. 
- Também tem de ter uma conta do Azure e de ter sessão iniciada na mesma para ver recursos Azure na nuvem Explorer. Se não tiver uma, pode criar uma conta no alguns minutos. Se tiver uma subscrição do MSDN, consulte o artigo [Benefício do Azure para subscritores do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, consulte o artigo [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Se na nuvem Explorer não estiver visível, pode visualizá-la ao selecionar a **vista**, **Windows outras,** **Explorer na nuvem** na barra de menus.

## <a name="manage-azure-accounts-and-subscriptions"></a>Gerir contas do Azure e subscrições

Para ver os recursos Azure no Explorador de nuvem, tem de iniciar sessão numa conta Azure com um ou mais subscrições ativas. Se tiver mais de uma conta Azure, pode adicioná-los na nuvem Explorer e, em seguida, selecione as subscrições que pretende incluir na vista de recursos do Explorador de nuvem.

Se não utilizou o Azure antes ou se ainda não tiver adicionado as contas necessárias para Visual Studio, será solicitado para fazê-lo.

## <a name="to-add-azure-accounts-to-cloud-explorer"></a>Para adicionar contas de Azure para Explorer na nuvem

1. Selecione o ícone de definições na barra de ferramentas do Explorer na nuvem.

1. Selecione a ligação **Adicionar uma conta** . Inicie sessão na conta do Azure cujos recursos que pretende procurar. A conta que acabou de adicionar deverá ser selecionada na lista pendente de selecionador de conta. As subscrições para essa conta aparecem em movimento da conta.

    ![Adicionar subscrições do Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Se escolher a opção subscrições Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. Selecione as caixas de verificação para as subscrições de conta que pretende procurar e, em seguida, selecione o botão **Aplicar** .

    Os recursos Azure para as subscrições seleccionadas aparecem no Explorador de nuvem.

## <a name="to-remove-an-azure-account"></a>Para remover uma conta do Azure

1. Selecione o **ficheiro**, **As definições da conta** na barra de menus.

1. Na secção de **Todas as contas** da caixa de diálogo **Definições de conta** , selecione o comando **Remove** junto à conta que pretende remover. Nota a que este comando só remove a conta a partir do Visual Studio – it não afeta a conta Azure propriamente dita.

## <a name="view-resource-types-or-groups"></a>Tipos de vista de recursos ou grupos

Para ver os recursos do Azure, pode escolher **Tipos de recursos** ou vista de **Grupos de recursos** .

![Lista pendente de vista de recursos](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- Vista de **Tipos de recursos** , que também é a vista comuns utilizada no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), mostra os recursos do Azure categorizados pelo respetivo tipo, tais como aplicações web, contas de armazenamento e máquinas virtuais. Este passo é semelhante a recursos como Azure aparecem no Explorador do servidor.

- Vista de grupos de recursos categoriza Azure recursos pelo grupo de recursos Azure que estão associados.

 
    Um grupo de recursos é um conjunto de recursos Azure, normalmente utilizadas por uma aplicação específica. Para saber mais sobre os grupos de recurso Azure, consulte o artigo [Descrição geral do Gestor de recursos do Azure](./resource-group-overview.md).

## <a name="view-and-navigate-resources"></a>Ver e aceder a recursos

Para navegar para um recurso do Azure e visualizar as informações no Explorador de nuvem, expanda o item tipo ou grupo de recursos associados e, em seguida, selecione o recurso. Quando escolher um recurso, são apresentadas informações no dois separadores na parte inferior do Explorer na nuvem.

![Selecione uma vista de recursos](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- No separador **ações** mostra as ações que pode tomar no Explorador de nuvem para o recurso selecionado. Também pode ver ações disponíveis no menu de atalho do recurso.

- No separador **Propriedades da** mostra as propriedades do recurso, tal como o seu grupo tipo, região e recurso que está associado.

Todos os recursos tem a ação **aberta no portal**. Quando escolher esta ação, nuvem Explorer apresenta o recurso seleccionado no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Esta funcionalidade é particularmente útil para navegar para recursos aninhadas.

Ações adicionais e valores de propriedade podem também são apresentados com base no Azure recurso. Por exemplo, web apps e apps lógica também têm as ações **aberto no browser** e **Anexar Depurador** além **aberta no portal**. Ações para abrir editores aparecem quando escolher um blob de conta de armazenamento, fila ou tabela. Aplicações do Azure têm propriedades de **URL** e o **Estado** , enquanto recursos de armazenamento tem chave e ligação de propriedades da cadeia.

## <a name="search-resources"></a>Recursos de pesquisa

Para localizar recursos com um nome específico no seu subscrições conta Azure, introduza o nome na caixa de pesquisa no Explorador de nuvem.

![Localizar recursos no Explorador de nuvem](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

À medida que introduz carateres na caixa de pesquisa, apenas os recursos que correspondem a esses carateres são apresentados na árvore de recursos.

