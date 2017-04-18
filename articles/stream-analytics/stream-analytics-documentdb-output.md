<properties
    pageTitle="Saída JSON para análise da cadeia | Microsoft Azure"
    description="Saiba como a análise da cadeia pode alvo Azure DocumentDB para saída JSON, para o arquivo de dados e consultas de baixa latência no dados JSON não estruturados."
    keywords="JSON saída"
    documentationCenter=""
    services="stream-analytics,documentdb"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>Destino Azure DocumentDB para saída JSON de análise da cadeia

Análise da cadeia pode direccionar [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) para JSON de saída, permitindo-consultas de arquivo e baixa latência de dados no dados JSON não estruturados. Saiba como implementar o melhor esta integração.

Para aqueles que não estiver familiarizado com DocumentDB, veja o [caminho de formação do DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para começar a utilizar.

## <a name="basics-of-documentdb-as-an-output-target"></a>Noções básicas de DocumentDB como um alvo de saída
O resultado do Azure DocumentDB em sequência Analytics permite escrita sua sequência de processamento resultados como saída JSON para a coleção ou coleções de DocumentDB específicas. Análise da cadeia não criar coleções de sites na base de dados, em vez disso, que seja necessário criá-los upfront. Esta é para que os custos de DocumentDB coleções de faturaçãohttps são transparentes para si e para que pode ajustar o desempenho, a consistência e a capacidade das suas coleções de diretamente utilizando as [DocumentDB APIs](https://msdn.microsoft.com/library/azure/dn781481.aspx). Recomendamos que utilize uma base de dados de DocumentDB por transmissão de tarefa para separar logicamente os seus coleções de sites para uma tarefa de transmissão.

Algumas das opções de coleção de DocumentDB são detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar o consistência, disponibilidade e latência

Para que correspondam aos requisitos de aplicação, DocumentDB permite-lhe ajustar e ajustar a base de dados e coleções de compromissos entre consistência, a disponibilidade e a latência. Dependendo do que níveis de consistência leia o às suas necessidades cenário contra a leitura e escrita latência, que pode escolher um nível de consistência na sua conta de base de dados. Também por predefinição, DocumentDB ativa a indexação síncrono em cada operação CRUD para a coleção. Esta é outra opção útil para controlar o desempenho de escrita/leitura no DocumentDB. Para obter mais informações sobre este tópico, consulte o artigo [alterar a sua base de dados e consulta consistência os níveis](../documentdb/documentdb-consistency-levels.md) .

## <a name="choose-a-performance-level"></a>Escolher um nível de desempenho

DocumentDB coleções de sites podem ser criadas no 3 diferentes níveis de desempenho (S1, S2 ou S3), que determinam o débito disponível para CRUDs a essa coleção. Para além disso, o desempenho é afectado pelos níveis de indexação/consistência na sua coleção. Consulte [Este artigo](../documentdb/documentdb-performance-levels.md) para compreender estes níveis de desempenho em detalhe.

## <a name="upserts-from-stream-analytics"></a>Upserts a partir da cadeia Analytics

Integração de análise da cadeia com DocumentDB permite-lhe inserir ou atualizar registos na sua coleção de DocumentDB baseada numa coluna ID do documento determinada. Isto é também conhecido como um *Upsert*.

Análise da cadeia utiliza uma abordagem de Upsert optimista, onde atualizações apenas são feitas ao inserir falha devido a um conflito de ID do documento. Esta atualização é executada por análise da cadeia como uma correcção, para que permite-atualizações parciais para o documento, ou seja, a adição de novas propriedades ou substituir que uma propriedade existente é efetuada incrementada. Tenha em atenção que as alterações nos valores das propriedades de matriz nos seus resultados de documento JSON na matriz inteira introdução substituído, ou seja, a matriz não foram intercaladas.

## <a name="data-partitioning-in-documentdb"></a>Criar a partições no DocumentDB de dados

Coleções de DocumentDB permitem-lhe a partições os seus dados com base no padrões de consulta e necessidades de desempenho da aplicação. Cada uma das coleções pode conter até 10GB de dados (máximo) e neste momento não existe nenhuma forma de uma coleção de dimensionar para cima (ou em conteúdo adicional). Para escalar saída, o sequência Analytics permite-lhe escrever em várias coleções de sites com um prefixo determinado (ver detalhes de utilização abaixo). Análise da cadeia utiliza a estratégia [Hash partição solucionador](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) consistente com base no utilizador fornecido PartitionKey coluna para criar a partições seus registos de saída. O número de colecções com o prefixo determinado na hora de início de transmissão da tarefa é utilizado como a contagem de partição de saída, na qual a tarefa escreve em paralelo (DocumentDB coleções = a partições saída). Para uma única S3 coleções de sites com preguiça indexação ao fazê-lo só insere, sobre 0,4 débito de escrita MB/s pode ser esperado. Utilizar várias coleções de sites pode permitem-lhe alcançar débito superior e a capacidade aumentada.

Se tencionar aumentar a contagem de partição no futuro, poderá ter de parar a tarefa, criar a partições os dados a partir do seu coleções de sites existentes para novas colecções de e, em seguida, reinicie a tarefa de análise da cadeia. Obter mais detalhes sobre como utilizar PartitionResolver e voltar a partições juntamente com exemplos de código, será incluído numa mensagem de seguimento. Também o artigo [criar a partições no DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) fornece detalhes sobre este assunto.

## <a name="documentdb-settings-for-json-output"></a>Definições de DocumentDB para JSON saída

Criar DocumentDB como uma saída em sequência Analytics gera um pedido para informações conforme visto abaixo. Esta secção fornece uma explicação sobre a definição de propriedades.

![ecrã de saída do documentdb da cadeia analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **Alias de saída** – um alias para consultar este resultado na sua consulta ASA  
-   **Nome da conta** – o nome ou URI da conta DocumentDB de ponto final.  
-   **Chave de conta** – a tecla de acesso partilhado para a conta DocumentDB.  
-   **Base de dados** – DocumentDB o nome de base de dados.  
-   **Colecção de nome padrão** – o padrão de nome de coleções de sites para as coleções de ficheiros ser utilizado. O formato de nome de coleções de sites pode ser construído utilizando o token de opcional {partição}, onde a partições iniciar a partir de 0. Seguem-se entradas válidas exemplo:  
   1\) conjunto – um conjunto com o nome "Conjunto" tem de existir previamente.  
   2\) conjunto {partição} – essas coleções tem de existir previamente – "MyCollection0", "MyCollection1", "MyCollection2" e assim sucessivamente.  
-   **Partição chave** – o nome do campo em eventos de saída utilizado para especificar a chave para criar a partições saída ao longo de coleções de sites. Resultado da coleção única, qualquer coluna saída arbitrário pode ser utilizado por exemplo, PartitionId.  
-   **ID do documento** – opcional. O nome do campo em eventos de saída utilizado para especificar a chave primária no qual a inserir ou actualização sejam baseiam operações.  
