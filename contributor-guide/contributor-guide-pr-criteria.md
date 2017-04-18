# <a name="quality-criteria-for-pull-request-review"></a>Reveja os critérios de qualidade para pedido de solicitação

Estes critérios destinam-se para os autores que criam e mantêm artigos técnicos e para os revisores de pedido de solicitação que rever pedidos de solicitação conteúdo. Se o seu pedido de solicitação não se qualifique para [intercalação automática](contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically), será analisado por um revisor de pedido de solicitação humanos. Solicitação pedido revisores reveja geralmente apenas o que é novas ou alteradas. Extrair pedido revisores avaliar as alterações num pedido por solicitação de acordo com os itens de revisão de qualidade e de bloqueio de bloqueio listados neste artigo.

## <a name="blocking-content-quality-items"></a>Bloqueio de itens de qualidade de conteúdo

As atualizações no pedido de solicitação devem cumprir os seguintes critérios para ser intercalados. Os revisores de pedido de solicitação fornecem comentários nas comentários de pedido de solicitação para estes itens e tipo `#hold-off` no pedido de solicitação para regressar ao que (o autor) com os comentários.

| Categoria | Item de revisão de qualidade |
|----------|---------------------|
|Pré-requisitos| São atribuídas a "pronto-para-intercalação" e as etiquetas "validação foi concluída com êxito" para o pr|
|Pré-requisitos| O pedido de solicitação não pode ser bloqueado por um conflito de impressão em série.|
|Integridade repo|    Solicitação pedido contém sem óbvias regressões conteúdas.|
|Integridade repo|    Solicitação pedido não inclui um repo incorporado ou quaisquer ficheiros invulgares, estranhos.|
|Integridade repo |Pedido de solicitação contém menos de 100 ficheiros alterados, a menos que a PR intencionalmente está a atualizar um ramo de lançamento do modelo global de. (Na verdade, um PR deve conter extremo inferior do que a, mas depois de 100 ficheiros alterados, GitHub não for apresentado o diffs).|
|Atribuição de nomes |Nomes de ficheiro para os novos ficheiros, siga as [diretrizes de atribuição de nomes do ficheiro](file-names-and-locations.md).|
|Atribuição de nomes |Pastas da nova introduzidos o seguimento repo as [diretrizes de nomenclatura de pasta](file-names-and-locations.md#folder-names-in-the-repo).|
|Conteúdo    |O artigo é um documento técnico e, consequentemente, no canal conteúdo correto. Consulte o artigo o [onde o que são inseridos orientações](content-channel-guidance.md).|
|Conteúdo    |O assunto no documento técnico é adequado para um artigo técnico. Consulte o artigo o [onde o que são inseridos orientações](content-channel-guidance.md).|
|Conteúdo    |O artigo contém um parágrafo introdutório e um corpo procedimento ou conceptual do conteúdo. O artigo tem de conter conteúdo suficiente, concluído para realçar sua própria como um artigo. Não deve um pequeno fragmento de informações. (Exceção: um tópico de "Limites" se estiver no contexto de um artigo de grandes dimensões que apresenta todos os limites de um serviço.)|
|Conteúdo| Elementos que devem ser listas numeradas são numerados, elementos que devem ser não ordenados listas com marcas. Este é textuais básica.|
|Conteúdo| Invulgares ou novos gráficos, arquitetura da informação ou estruturas ou normalizadas é óbvio que as estruturas tem de ser verificadas com revisor PR oportunidade potencial. Equipas que são experiências com coisas novas têm de ter um tela problema/solução ou um plano num local para avaliar experiências.|
|/ Estrutura do site funcionalidade| Switchers são utilizadas apenas para alternar entre várias versões do mesmo artigo.|
|/ Estrutura do site funcionalidade| Os títulos dos artigos switchered contêm informações que diferencia cada artigo a partir de outros artigos no conjunto de switchered.|
|/ Estrutura do site funcionalidade| Criou manualmente índices não são permitidos. O artigo tem depender H2s para o índice na página.|
|/ Estrutura do site funcionalidade| Se estiverem presentes H2 cabeçalhos, o artigo contém, pelo menos, dois cabeçalhos de H2. Utilizar um cabeçalho de H2 cria um artigo de item único índice. H2 cabeçalhos tem de ser utilizados antes H3 cabeçalhos para garantir que é criado um índice.|
|Markdown| HTML: Conteúdo de origem não contém HTML ao nível do bloco – inline secundária HTML é permitido – como superior à linha, inferior à linha, carateres especiais e outras coisas secundárias que não é possível fazer com o markdown. Tabelas HTML são permitidas apenas se a tabela que contém listas com marcas ou numerada, mas que é normalmente uma indicação que o conteúdo tem de ser simplificada para a origem pode ser codificada no markdown.|
|Markdown   |Elementos de markdown personalizados são utilizados adequado. Ex: Notas são codificadas utilizando o AZURE. Tenha em atenção extensão, não como texto simples.|
|SEO    |O "& #124; Identificador do site Microsoft Azure"é necessário|
|SEO    |O título H1 contém informações suficientes para descrever o conteúdo do artigo, diferenciá-lo de outros artigos Azure e mapear para palavras-chave do cliente provável. Por exemplo, "Descrição geral de" como título H1 é uma falha.|
|Terminologia| A utilização da processador acrónimo, V1 ou V2 como as referências para a clássica e modelos de implementação do Gestor de recursos é um item de terminologia bloqueio.|
|Imagens |GIF animado não é aceites para o repo.|
|Imagens | Imagens tem limpar resolução, de palavras com erros ortográficos e não contêm privadas informações | 
|Teste|Pré-visualização do artigo tem de ser LIMPARB no teste. Não pode conter qualquer óbvios problemas de formatação: <br><br>-Uma lista numerada ou com marcas que é apresentado como um parágrafo <br> -Código num bloco de código apareça parcialmente no bloco de código e parcialmente fora da <br>-Passos numerados numerados incorretamente devido ao avanço cablagem|

## <a name="non-blocking-content-quality-items"></a>Bloquear não itens de qualidade de conteúdo

Para estes itens, os revisores de pedido de solicitação fornecem comentários e instruções para o autor para dar seguimento com correções num pedido de solicitação posterior. No entanto, esta comentários não bloqueia a decisão de impressão em série. Autores deverão dar seguimento dentro de 3 dias úteis com correções.

| Categoria | Item de revisão de qualidade |
|----------|---------------------|
|Conteúdo|Artigos deverão ter um "os passos seguintes" no final com 1-3 apelativas e relevantes os passos seguintes. Texto breve deve ser incluído que ajuda o compreender por que razão são relevantes os passos de cliente. (Novos artigos apenas). Exemplo: <https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/><br>![](media/contributor-guide-pr-criteria/nextstepsexample.PNG)|
|Conteúdo|Ortografia, gramática e outros problemas de escrita - solicitação pedido os revisores podem fornecer comentários sobre algumas questões secundárias não seja bloqueio comentários. Se existirem mais do que alguns problemas editoriais, revisores iniciar um pedido de edição para o artigo para uma edição de POST publicação.|
|Imagens|Imagens utilizam o estilo de nota de aviso correto e cor e capturas de ecrã utilizam o estilo de limite e o marcador de posição correto. [Ver as orientações da imagem](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Imagens|Imagens incluem texto alternativo. [Ver as orientações da imagem](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|/ Estrutura do site funcionalidade|Os cabeçalhos H2, quando foi processada num índice na página, idealmente devem ser moldado para mais do que 2 linhas. Cabeçalhos mais longo tornar o artigo índice mais difícil digitalizar.|
|Convenções de estilo|Todos os cabeçalhos e títulos são caso frase, por estilo Azure.|
|Processo|Se o pedido de solicitação poderia facilmente ter sido reconfigurar para beneficiar dos PRmerger automatização, os revisores de pedido de solicitação fornecem comentários ao autor sobre como utilizar o ramificações para que as alterações podem ser automaticamente intercaladas. Consulte [o artigo de etiqueta de PR](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically).|
|Processo|Quando eliminar ou mudar o nome de um artigo, certifique-se de que segue o processo. Separar pedido revisores devem adicionar o comentário e a hiperligação seguinte num comentário:<br><br>*Verifique se seguiu o processo de guia dos colaboradores para eliminar os artigos: <https://github.com/Azure/azure-content/blob/master/contributor-guide/retire-or-rename-an-article.md> .*|

## <a name="related"></a>Relacionados com

- [Separar etiqueta do pedido e as melhores práticas para colaboradores da Microsoft](contributor-guide-pull-request-etiquette.md)

- [Separar pedido comentário automatização](contributor-guide-pull-request-comments.md)
