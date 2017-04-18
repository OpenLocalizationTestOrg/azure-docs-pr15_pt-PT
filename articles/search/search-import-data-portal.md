<properties
    pageTitle="Importar dados para pesquisa Azure utilizando indexadores no Portal do Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Utilize o Assistente de dados de importação de pesquisa do Azure no Portal do Azure aos dados de pesquisa a partir do armazenamento de Blobs do Azure, stroage de tabela, base de dados SQL e do SQL Server no Azure VMs."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>

# <a name="import-data-to-azure-search-using-the-portal"></a>Importar dados para pesquisa Azure através do portal

Portal do Azure fornece um Assistente de **Importação de dados** no dashboard de pesquisa do Azure para carregar os dados para um índice remissivo. 

  ![Importar dados, na barra de comando][1]

Interior, o assistente configura e invoca um *indexador*automatizar vários passos do processo de indexação: 

- Ligar a uma origem de dados externos na subscrição atual do Azure
- Gerar automaticamente um esquema de índice remissivo baseado na estrutura de dados de origem
- Criar documentos baseados num conjunto de linhas obtido a partir da origem de dados
- Carregar documentos para o índice no seu serviço de pesquisa

Pode experimentar este fluxo de trabalho utilizando dados de exemplo no DocumentDB. Visite o [artigo Introdução ao Azure pesquisa no Portal do Azure](search-get-started-portal.md) para obter instruções.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Origens de dados suportadas pelo Assistente de importação de dados

O Assistente Importar dados suporta seguintes origens de dados: 

- Base de dados Azure SQL
- Dados relacionais do SQL Server numa VM Azure
- Azure DocumentDB
- Armazenamento Blob do Azure (na pré-visualização)
- Armazenamento de tabela do Azure (na pré-visualização)

Um conjunto de dados plana é uma entrada necessária. Só pode importar a partir de uma única tabela, vista de base de dados ou estrutura de dados equivalente. Deve criar esta estrutura de dados antes de executar o assistente.

Tenha em atenção que algumas as indexadores ainda sejam encontram na pré-visualizar, que significa que a definição do indexador é cópias pela versão de pré-visualização da API do. Consulte o artigo [Descrição geral do indexador](search-indexer-overview.md) para obter mais informações e hiperligações.

## <a name="connect-to-your-data"></a>Ligar a dados

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) e os dashboards serviços aberta. Pode clicar em **Serviços de pesquisa** na barra de atalhos para mostrar os serviços existentes na subscrição atual. 

2. Na barra de comando para abrir de diapositivo a pá importar dados, clique em **Importar dados** .  

3. Clique em **ligar aos seus dados** para especificar uma definição de origem de dados utilizada por um indexador. Para origens de dados de subscrição dentro, o assistente, normalmente, pode detetar e ler informações de ligação, minimizar geral requisitos de configuração.

| | |
|--------|------------|
|**Origem de dados existente** | Se já tiver indexadores definidos no seu serviço de pesquisa, pode selecionar uma definição de origem de dados existente para outra importação.|
|**Base de dados Azure SQL** | Nome do serviço, as credenciais para um utilizador de base de dados com permissão de leitura e um nome de base de dados podem ser especificado na página ou através de uma cadeia de ligação ADO.NET. Selecione a opção de cadeia de ligação para ver ou personalizar as propriedades. <br/><br/>A tabela ou vista que fornece o conjunto de linhas tem de ser especificada na página. Esta opção é apresentada depois da ligação ser bem sucedida, que lhe dá uma uma lista pendente, para que pode fazer uma seleção.|
|**SQL Server no Azure VM** | Especifique um nome de serviço totalmente qualificado, ID de utilizador e palavra-passe e base de dados como uma cadeia de ligação. Para utilizar esta origem de dados, tem de ter anteriormente instalado um certificado no arquivo local que encripta a ligação. <br/><br/>A tabela ou vista que fornece o conjunto de linhas tem de ser especificada na página. Esta opção é apresentada depois da ligação ser bem sucedida, que lhe dá uma uma lista pendente, para que pode fazer uma seleção.
|**DocumentDB** |Requisitos de incluem a conta, base de dados e coleções de sites. Todos os documentos na coleção de serão incluídos no índice remissivo. Pode definir uma consulta para aplanar ou filtrar o conjunto de linhas, ou para detetar documentos alterados para operações de atualização de dados subsequentes.|
|**Armazenamento de Blobs do Azure** | Requisitos de incluem a conta de armazenamento e um contentor. Opcionalmente, se os nomes de BLOBs seguem uma convenção de nomenclatura virtual para fins de agrupamento, pode especificar o directório virtual parte do nome como uma pasta em contentor. Para mais informações, consulte o [Armazenamento de BLOBs indexação (pré-visualização)](search-howto-indexing-azure-blob-storage.md) . |
|**Armazenamento de tabela do Azure** | Requisitos de incluem a conta de armazenamento e nome de uma tabela. Opcionalmente, pode especificar uma consulta para obter um subconjunto dos quadros. Para mais informações, consulte o [Armazenamento de tabela de indexação (pré-visualização)](search-howto-indexing-azure-tables.md) . |

## <a name="customize-target-index"></a>Personalizar o índice de destino

Um índice prévio normalmente é inferido a partir do conjunto de dados. Adicionar, editar ou eliminar campos para concluir o esquema. Para além disso, definir atributos ao nível do campo para determinar os respetivos comportamentos de pesquisa subsequentes.

1. Num **índice de destino de personalizar**, especifique o nome e uma **tecla** utilizado para identificar exclusivamente cada documento. A chave tem de ser uma cadeia. Se os valores de campo incluem espaços ou traços Certifique-se de que definir as opções avançadas do **Importar dados** para suprimir a verificação de validação destes carateres.

2. Reveja e rever os restantes campos. Nome do campo e tipo são normalmente preenchidos por si. Pode alterar o tipo de dados.

3. Definir os atributos de índice remissivo para cada campo:

 - Recuperável devolve o campo nos resultados da pesquisa.
 - Filtráveis permite que o campo a ser referenciada expressões de filtro.
 - Ordenável permite que o campo a ser utilizado numa sequência de ordenação.
 - Facetable permite que o campo para navegação por facetas.
 - Pesquisável permite procurar texto completo.
  
4. Se pretender especificar um analisador de idioma ao nível do campo, clique no separador de **análise** . Analisadores de idioma apenas podem ser especificadas neste momento. Utilizar o analisador de personalizada ou um analisador de idioma em que não sejam como palavras-chave, com padrão e assim sucessivamente, necessitará de código.

 - Clique em **pesquisável** para designar procura no campo de texto completo e ativar a lista pendente Analyzer.
 - Selecione o analisador de que pretende. Consulte o artigo [criar um índice remissivo para documentos em vários idiomas](search-language-support.md) para obter detalhes.

5. Clique na **Suggester** para ativar as sugestões de consulta limitam-no campos selecionados.


## <a name="import-your-data"></a>Importar os seus dados

1. Em **Importar dados**, forneça um nome para o indexador. Recuperar a que o produto do Assistente Importar dados é um indexador. Mais tarde, se pretender ver ou editar, seleciona-lo a partir do portal em vez de voltar a executar o assistente. 

2. Especifique a agenda, qual é baseada no fuso horário regional na qual o serviço está aprovisionado.

3. Definir opções avançadas para especificar limiares se a indexação pode continuar a se um documento é ignorado. Para além disso, pode especificar se os campos de **chave** são permitidos para conter espaços e de barras.  

## <a name="edit-an-existing-indexer"></a>Editar um indexador existente

No dashboard do serviço, faça duplo clique no mosaico do indexador para o diapositivo saída de uma lista de todos os indexadores criado para a sua subscrição. Faça duplo clique das indexadores para executar, editar ou eliminá-la. Pode substituir o índice remissivo por outro existente, alterar a origem de dados e definir opções para limiares de erro durante a indexação.

## <a name="edit-an-existing-index"></a>Editar um índice remissivo existente

Na pesquisa Azure, estruturais actualizações para um índice remissivo necessitarão uma recompilação desse índice, que consiste em eliminar o índice remissivo, recriar o índice e recarregar dados. Actualizações estruturais incluem alterar um tipo de dados e mudar o nome ou eliminar um campo.

Edições que não exigem uma reconstruir incluem a adição de um novo campo, alterar os perfis de pontuação, alterar suggesters, ou alterar analisadores de idioma. Para mais informações, consulte [Actualizar índice](https://msdn.microsoft.com/library/azure/dn800964.aspx) .

## <a name="next-step"></a>Passo seguinte

Reveja estas ligações para saber mais sobre os indexadores:

- [Indexação base de dados Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Indexação DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Armazenamento de Blobs do indexação (pré-visualização)](search-howto-indexing-azure-blob-storage.md)
- [Armazenamento de tabela indexação (pré-visualização)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

