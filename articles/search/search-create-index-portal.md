<properties
    pageTitle="Criar um índice de pesquisa do Azure utilizando o Portal do Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Crie um índice utilizando o Portal do Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Criar um índice de pesquisa do Azure utilizando o Portal do Azure
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTO](search-create-index-rest-api.md)

Este artigo irá guiá-lo durante o processo de criação de um [índice](search-what-is-an-index.md) de pesquisa do Azure utilizando o Portal do Azure.

Antes de seguir este guia e criar um índice remissivo, deverá ter já [criou um serviço de pesquisa do Azure](search-create-service-portal.md).


## <a name="i-go-to-your-azure-search-blade"></a>Posso. Aceda ao seu pá de pesquisa do Azure
1. Clique em "Todos os recursos" no menu no lado esquerdo do [Portal do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selecione o seu serviço de pesquisa do Azure

## <a name="ii-add-and-name-your-index"></a>II. Adicionar e atribuir um nome o índice remissivo
1. Clique no botão "Adicionar index"
2. Dê um nome índice de pesquisa do Azure. Uma vez que estamos a criar um índice remissivo para procurar o alojamento neste guia, que tem chamamos nosso índice "alojamento".
  * O nome do índice tem de iniciar por uma letra e conter apenas letras em minúsculas, dígitos ou traços ("-").
  * Semelhante para o seu nome de serviço, o nome do índice que escolher também estarão parte do URL de ponto final onde irá enviar pedidos de HTTP para a API de pesquisa do Azure
3. Clique na entrada para abrir um novo pá "Campos"

![](./media/search-create-index-portal/add-index.png)


## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Criar e definir os campos do índice
1. Ao selecionar a entrada de "Campos", será aberto um novo pá com um formulário para introduzir a sua definição de índice remissivo.
2. Adicione campos ao seu índice utilizando o formulário.

  * Um campo de *chave* do tipo Edm.String é obrigatório para cada índice de pesquisa do Azure. Este campo de chave é criado por predefinição, com o nome "id do campo". Vamos foram alterados-lo para "hotelId" no nosso índice.
  * Determinadas propriedades do esquema do seu índice só podem ser definidas uma vez e não podem ser atualizadas no futuro. Atualizações de esquema que requerem a reindexação tal como alterar tipos de campo por esta razão, não estão atualmente possíveis após a configuração inicial.
  * Vamos cuidadosamente ter escolhido os valores de propriedade para cada campo com base em como podemos pensa que serão utilizadas numa aplicação. Tenha as pesquisa utilizador experiência empresas suas necessidades e em atenção ao criar o índice remissivo como cada campo tem de ser atribuído as [propriedades adequadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Aplicar estas controlar de propriedades que procura funcionalidades (filtragem, faceting, ordenação, pesquisa de texto completo, etc.) a quais os campos. Por exemplo, é provável que pessoas procurar alojamento irão estar interessadas em correspondências de palavra-chave no campo "Descrição", para que o texto completo procurar esse campo podemos ativar através da propriedade "Pesquisável".
    * Também pode definir o [Analisador de idioma](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) para cada campo ao clicar no separador "Analyzer" no topo da pá. Pode ver abaixo estamos Selecionou um analisador de francesa para um campo no nosso índice destinado a texto em francês.

3. Clique em **OK** na pá "Campos" para confirmar as suas definições de campo
4. Clique em **OK** na pá "Index adicionar" para guardar e criar um índice que acabou de definir.

Nas capturas de ecrã abaixo, pode ver como podemos tem com o nome e os campos definidos para nosso índice "alojamento".

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Seguinte
Depois de criar um índice de pesquisa do Azure, estará pronto para [carregar o seu conteúdo para o índice remissivo](search-what-is-data-import.md) para poder começar a procurar os seus dados.
