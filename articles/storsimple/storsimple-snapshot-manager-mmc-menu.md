<properties 
   pageTitle="Menu ações do MMC do Gestor de instantâneo StorSimple | Microsoft Azure"
   description="Descreve como utilizar as ações de menu da consola de gestão da Microsoft (MMC) padrão no StorSimple instantâneo Manager."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Utilize as menu ações do MMC StorSimple instantâneo Gestor

## <a name="overview"></a>Descrição geral

No Gestor de StorSimple instantâneo, verá as seguintes ações listadas em todos os menus de ação e todos os variações do painel de **ações** . 

- Vista
- Janela nova a partir daqui 
- Atualizar 
- Lista de exportação 
- Obter ajuda 

Estas ações fazem parte da consola de gestão da Microsoft (MMC) e não são específicas ao StorSimple instantâneo gestor. Neste tutorial descreve estas ações e explica como utilizar cada um no StorSimple instantâneo Manager.

## <a name="view"></a>Vista

Pode utilizar a opção de **visualização** para alterar a vista de painel de **resultados** e para alterar a vista de janela de consola. 

#### <a name="to-change-the-results-pane-view"></a>Para alterar a vista de painel de resultados

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , qualquer nó com o botão direito ou expanda o nó com o botão direito um item no painel de **resultados** e, em seguida, clique na opção de **vista** . 

3. Para adicionar ou remover as colunas que aparecem no painel de **resultados** , clique em **Adicionar/remover colunas**. É apresentada a caixa de diálogo **Adicionar/remover colunas** .

    ![Adicionar ou remover colunas a partir do painel de resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 

4. Preencha o formulário da seguinte forma:

    - Selecione os itens da lista colunas **disponíveis** e clique em **Adicionar** para adicioná-los à lista de **colunas apresentadas** . 

    - Clique em itens na lista **colunas apresentadas** e clique em **Remover** para removê-los a partir da lista. 

    - Selecione um item na lista de colunas **apresentado** e clique em **Mover para cima** ou **Mover para baixo** para mover o item para cima ou para baixo na lista. 

    - Clique em **Restaurar predefinições** para voltar para a configuração de painel de **resultados** predefinida. 

5. Quando terminar com as suas seleções, clique em **OK**. 

#### <a name="to-change-the-console-window-view"></a>Para alterar a vista de janela de consola

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , qualquer nó com o botão direito, clique em **Ver**e, em seguida, clique em **Personalizar**. É apresentada a caixa de diálogo **Personalizar** .

    ![Personalizar a janela da consola](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 

3. Selecione ou desmarque as caixas de verificação para mostrar ou ocultar itens na janela da consola. Quando terminar com as suas seleções, clique em **OK**.

## <a name="new-window-from-here"></a>Janela nova a partir daqui

Pode utilizar a opção de **Janela novo a partir daqui** para abrir numa nova janela de consola.

#### <a name="to-open-a-new-console-window"></a>Para abrir numa nova janela de consola

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , qualquer nó com o botão direito e, em seguida, clique em **Nova janela a partir daqui**. 

    É apresentada uma nova janela, que mostra apenas o âmbito de que selecionou. Por exemplo, se o botão direito do rato o nó **Políticas de cópia de segurança** , a janela de nova Mostrar apenas o nó de **Políticas de cópia de segurança** no painel de **âmbito** e uma lista de políticas de cópia de segurança definidas no painel de **resultados** . Consulte o artigo o exemplo seguinte.

    ![Janela nova a partir daqui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 
 
## <a name="refresh"></a>Atualizar

Pode utilizar a ação **Atualizar** para atualizar a janela da consola.

#### <a name="to-update-the-console-window"></a>Para atualizar a janela da consola

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , qualquer nó com o botão direito ou expanda o nó e com o botão direito um item no painel de **resultados** e, em seguida, clique em **Atualizar**. 

## <a name="export-list"></a>Lista de exportação

Pode utilizar a ação de **Lista de exportação** para guardar uma lista num ficheiro de valores separados por vírgulas (CSV). Por exemplo, pode exportar a lista de políticas de cópia de segurança ou o catálogo de cópia de segurança. Em seguida, pode importar o ficheiro CSV para uma aplicação de folha de cálculo para uma análise.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Para guardar uma lista num ficheiro de valores separados por vírgulas (CSV)

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple. 

2. No painel de **âmbito** , qualquer nó com o botão direito ou expanda o nó com o botão direito um item no painel de **resultados** e, em seguida, clique em **Lista de exportação**. 

3. É apresentada a caixa de diálogo **Lista de exportação** . Preencha o formulário da seguinte forma: 

    1. Na caixa **nome de ficheiro** , escreva um nome para o ficheiro CSV ou clique na seta para selecionar a partir da lista pendente.

    2. Na caixa **Guardar como tipo** , clique na seta e selecione um tipo de ficheiro a partir da lista pendente.

    3. Para guardar apenas os itens selecionados, selecione as linhas e, em seguida, clique na caixa de verificação **Guardar apenas as linhas selecionadas** . Para guardar todas as exportado listas, desmarque a caixa de verificação **Guardar apenas as linhas selecionadas** .

    4. Clique em **Guardar**.

    ![Lista de exportação como um ficheiro de valores separados por ponto e vírgula](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 
 
## <a name="help"></a>Obter ajuda

Pode utilizar o menu **Ajuda** para ver a ajuda online disponível para StorSimple instantâneo gestor e o MMC.

#### <a name="to-view-available-online-help"></a>Para ver a ajuda online disponíveis

1. Clique no ícone de ambiente de trabalho para iniciar o Gestor de instantâneo StorSimple.

2. No painel de **âmbito** , qualquer nó com o botão direito ou expanda o nó com o botão direito um item no painel de **resultados** e, em seguida, clique em **Ajuda**. 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a [interface de utilizador StorSimple instantâneo Gestor](storsimple-use-snapshot-manager.md).
- Saiba mais sobre como [utilizar o Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
