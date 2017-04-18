<properties
   pageTitle="Como guardar procuras e afixar elementos de dados | Microsoft Azure"
   description="Artigo sobre como utilizar realce capacidades no catálogo de dados do Azure para guardar as origens de dados e elementos de dados para reutilizar posteriormente."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/10/2016"
   ms.author="maroche"/>

# <a name="how-to-save-searches-and-pin-data-assets"></a>Como guardar procuras e afixar elementos de dados

## <a name="introduction"></a>Introdução

Catálogo de dados do Microsoft Azure fornece capacidades de deteção de origem de dados. Os utilizadores podem rapidamente procurar e filtrar no catálogo de localize as origens de dados e compreender a sua finalidade, tornando mais fácil encontrar os dados corretos para a tarefa em questão.

Mas e quanto quando os utilizadores têm de trabalhar regularmente com os mesmos dados? E quando os utilizadores contribuir regularmente dos seus conhecimentos às mesmas origens de dados no catálogo de? Nas seguintes situações, ter de emitir repetidamente as pesquisas mesmo pode ser ineficaz – este é sempre que procura guardada e afixados dados podem ajudar a recursos.

## <a name="saved-searches"></a>Pesquisas guardadas

Uma pesquisa guardada no catálogo de dados do Azure é uma definição de pesquisa de por utilizador reutilizável. Assim que um utilizador tenha definido uma pesquisa – incluindo termos de pesquisa, etiquetas e outros filtros – ele pode guardá-lo para utilizar posteriormente. A definição de procura guardada, em seguida, pode executar novamente numa data posterior, para devolver qualquer elementos de dados que correspondem aos seus critérios de pesquisa.

### <a name="creating-a-saved-search"></a>Criar uma procura guardada

Para criar uma procura guardada, introduza pela primeira vez os critérios de pesquisa para ser reutilizado. Em seguida, clique na ligação de "Guardar" na caixa de "Pesquisa atual" no portal do catálogo de dados do Azure.

 ![Selecionar 'Guardar' para guardar as definições de pesquisa atual](./media/data-catalog-how-to-save-pin/01-save-option.png)

Quando lhe for pedido, introduza um nome para a procura guardada. Selecione um nome que seja significativo e descritivo dos activos dados que vão ser devolvidos pela pesquisa.

 ![Forneça um nome para a procura guardada](./media/data-catalog-how-to-save-pin/02-name.png)

### <a name="managing-saved-searches"></a>Gerir pesquisas guardadas

Assim que um utilizador tem uma ou mais pesquisas guardadas, uma opção de "Procuras guardadas" irá aparecer no portal do catálogo de dados do Azure na caixa "Procurar atual". Quando expandido e a lista completa de pesquisas guarda será apresentada.

 ![Lista de pesquisas guardadas](./media/data-catalog-how-to-save-pin/03-list.png)

Selecionar uma procura guardada a partir da lista causará a pesquisa a ser executada.

Selecionar o menu pendente irá fornecer um conjunto de opções de gestão de:

 ![Opções para gerir pesquisas guardadas](./media/data-catalog-how-to-save-pin/04-managing.png)

Selecionar "Mudar o nome" irá pedir ao utilizador que introduza um novo nome para a procura guardada. A definição de pesquisa não será alterada.

Selecionar "Eliminar" irá pedir a confirmação do utilizador e, em seguida, irá remover a procura guardada a partir da lista do utilizador.

Selecionar "Guardar como predefinição" irá marcar escolhido procura guardado como a procura predefinida para o utilizador. Se o utilizador fará uma pesquisa "vazia" na home page do catálogo de dados do Azure, procura de predefinida do utilizador será executada. Além disso, a pesquisa marcada como predefinição será apresentada na parte superior da lista de procura guardada.

### <a name="organizational-saved-searches"></a>Pesquisas guardadas organizacionais

Cada utilizador pode guardar pesquisas para utilização própria. Os administradores do catálogo de dados também podem guardar pesquisas para todos os utilizadores dentro da empresa. Ao guardar uma pesquisa, os administradores são apresentados com uma opção para partilhar a procura guardada dentro da empresa. Se esta opção estiver selecionada, a procura guardada será incluída na lista de pesquisas disponíveis para todos os utilizadores.

 ![Pesquisas guardadas organizacionais](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)


## <a name="pinned-data-assets"></a>Elementos de dados afixada

Pesquisas guardadas permitir que os utilizadores guardar e reutilizar definições de pesquisa; Os ativos dados devolvidos pelas pesquisas podem ser alteradas ao longo do tempo, como o conteúdo da alteração de catálogo. Afixar elementos de dados permite aos utilizadores identifique explicitamente elementos de dados específicos para facilitar a acesso sem necessitar de utilizar uma pesquisa.

Afixar um elementos de dados é simples – utilizadores podem simplesmente clicar no ícone de "pin" para o ativo de dados para adicioná-lo à respetiva lista afixada. Este ícone aparece no canto do mosaico elementos na vista de mosaico e, na coluna mais à esquerda na vista de lista no portal do catálogo de dados do Azure.

![Afixar um elementos de dados](./media/data-catalog-how-to-save-pin/05-pinning.png)

Unpinning de um ativo é igualmente simples – utilizadores basta clicam no ícone de "alfinete" novamente para ativar/desativar a definição para o item selecionado.

![Unpinning um elementos de dados](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="my-assets"></a>"Os meus elementos"
A home page do portal Azure catálogo de dados inclui uma secção de "Os meus recursos" apresenta ativos de interesse para o utilizador actual. Esta secção inclui ambos os elementos afixados e pesquisas guardadas.

![«Meu activos» na home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumo
Catálogo de dados Azure fornece as funcionalidades que tornam mais fácil para os utilizadores descobrir as origens de dados que necessitam e para que podem dedicar menos tempo está à procura de dados e mais tempo a trabalhar com o mesmo. Pesquisas guardadas e afixados dados de activos construir nestas capacidades core para que os utilizadores lhe identificar facilmente origens de dados com o qual irá funcionar repetidamente.
