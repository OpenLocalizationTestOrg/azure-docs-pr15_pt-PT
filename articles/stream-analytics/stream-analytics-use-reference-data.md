<properties
    pageTitle="Utilizar tabelas de dados e pesquisa de referência na análise da cadeia | Microsoft Azure"
    description="Utilizar uma consulta de análise da cadeia os dados de referência"
    keywords="tabela de referência, dados de referência"
    services="stream-analytics"
    documentationCenter=""
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

# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Utilizar tabelas de dados ou de pesquisa de referência numa sequência de entrada da cadeia Analytics

Dados de referência (também conhecido como uma tabela de procura) são um conjunto de dados finito que é estático ou abrandar a alterar no natureza, utilizado para efetuar uma pesquisa ou se ligam às sua sequência de dados. Para tornar a utilização de dados de referência no seu trabalho Azure a análise da cadeia será geralmente utilizar uma [Referência dados participar](https://msdn.microsoft.com/library/azure/dn949258.aspx) na sua consulta. Análise da cadeia utiliza armazenamento de Blobs do Azure como a camada de armazenamento para os dados de referência e com referência Azure dados fábrica dados podem ser transformados e/ou copiados para o armazenamento de Blobs do Azure, para utilizar como dados de referência, a partir de [qualquer número de nuvem com base e no local arquivos de dados](../data-factory/data-factory-data-movement-activities.md). Dados de referência são considerados uma sequência de blobs (definido na configuração do teclado) por ordem ascendente de data/hora especificada no nome do blob. -Lo **só** suporta a adição para o fim da sequência utilizando uma data/hora **maior do** que aquele especificado pelo último blob na sequência.

## <a name="configuring-reference-data"></a>Dados de referência de configuração

Para configurar os seus dados de referência, primeiro tem de criar uma entrada do tipo de **Dados de referência**. A tabela abaixo explica cada propriedade que terá de fornecer ao criar os dados de referência de entrada com a respetiva descrição:

<table>
<tbody>
<tr>
<td>Nome da propriedade</td>
<td>Descrição</td>
</tr>
<tr>
<td>Alias de entrada</td>
<td>Um nome amigável que será utilizado na consulta tarefa para fazer referência a esta entrada.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento onde se encontram os seus ficheiros de Blobs. Se estiver na mesma subscrição do seu trabalho de análise da cadeia, pode selecioná-lo a partir da lista pendente para baixo.</td>
</tr>
<tr>
<td>Chave de conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento. Isto é preenchido automaticamente se a conta de armazenamento é na mesma subscrição do seu trabalho de análise da cadeia.</td>
</tr>
<tr>
<td>Contentor de armazenamento</td>
<td>Contentores fornecem um agrupamento lógico para blobs armazenados no serviço de Blobs do Microsoft Azure. Quando carregar um blob para o serviço de BLOBs, tem de especificar um contentor para esse blob.</td>
</tr>
<tr>
<td>Caminho padrão</td>
<td>O caminho do ficheiro utilizado para localizar o seu blobs dentro do contentor especificado. Dentro do caminho, pode optar por especificar um ou mais ocorrências de 2 variáveis que se seguem:<BR>{date}, {tempo}<BR>Exemplo 1: products/{date}/{time}/product-list.csv<BR>Exemplo 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se tiver utilizado {date} dentro o padrão de caminho que especificou, pode selecionar o formato de data na qual os seus ficheiros estão organizados no pendente formatos suportados. Exemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se tiver utilizado {tempo} dentro o padrão de caminho que especificou, pode selecionar o formato da hora em que os seus ficheiros estão organizados no pendente formatos suportados. Exemplo: HH</td>
</tr>
<tr>
<td>Formato de serialização de evento</td>
<td>Para se certificar de que as suas consultas funcionam da forma que esperava, precisa de análise da cadeia saber qual o formato de serialização estiver a utilizar o recebidas sequências de dados. Para os dados de referência, os formatos suportados são CSV e JSON.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Neste momento, o formato de codificação suportado apenas é UTF-8</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Geração de dados de referência com base numa agenda

Se os dados de referência forem um conjunto de dados lentamente alteração, em seguida, suportam de atualização de dados estão ativados ao especificar um padrão de caminho na configuração do teclado utilizando a {data} de referência e {tempo} tokens de substituição. Análise da cadeia vai selecionar as definições de dados de referência atualizada com base neste padrão do caminho. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data de **"aaaa-MM-DD"** e um tempo de formato de **"Hh: mm"** instrui da cadeia Analytics para continuar o blob atualizado `sample/2015-04-16/17:30/products.csv` em 5:30 PM de Abril de 2015 16 UTC tempo zona.

> [AZURE.NOTE] Atualmente trabalhos de análise da cadeia procure a atualização de BLOBs apenas quando a hora do computador avança para a hora codificada no nome do blob. Por exemplo a tarefa irá procurar `sample/2015-04-16/17:30/products.csv` assim possível mas não anterior que 5:30 PM de Abril de 2015 16 UTC tempo zona. É provável que *nunca* aspeto para um ficheiro com um período de tempo anterior ao último aquele que seja descoberta codificado.
> 
> Por exemplo Assim que a tarefa encontra o blob `sample/2015-04-16/17:30/products.csv` ignora quaisquer ficheiros com uma data codificado anteriores ao 5:30 PM 16 de Abril de 2015 pelo que, se um entregue atrasada `sample/2015-04-16/17:25/products.csv` blob é criado no mesmo contentor a tarefa irá não utilizá-la.
> 
> Da mesma forma se `sample/2015-04-16/17:30/products.csv` apenas é produzidos às 10:03 PM 16 de Abril de 2015 mas sem blob com uma data anterior é presente no contentor, a tarefa irá utilizar este ficheiro começando na 10:03 PM 16 de Abril de 2015 e usar os dados de referência anterior até, em seguida.
> 
> Uma exceção é iniciada de quando a tarefa tem de voltar a processar dados trás no tempo ou quando a tarefa estiver em primeiro lugar. Na hora de início que a tarefa está à procura o mais recente blob produzido antes da tarefa comece a hora indicada. Isto é feito para se certificar de que existe um conjunto de dados de referência **não vazios** quando a tarefa é iniciado. Se não for encontrada, a tarefa será apresentada o seguinte diagnóstico: `Initializing input without a valid reference data blob for UTC time <start time>`.


[Azure dados fábrica](https://azure.microsoft.com/documentation/services/data-factory/) podem ser utilizados para orquestrar a tarefa da criação de blobs atualizadas necessários da cadeia Analytics para atualizar as definições de dados de referência. Fábrica de dados é um serviço de integração de dados baseadas na nuvem que orquestra e automatiza o movimento e transformação de dados. Dados fábrica suporta a [ligar-se a um grande número de baseada na nuvem e arquivos de dados no local](../data-factory/data-factory-data-movement-activities.md) e mover dados facilmente uma agenda normal que especificar. Para obter mais informações e orientações passo a passo sobre como configurar uma tubagem fábrica de dados para gerar a dados de referência para a análise da cadeia que atualiza uma agenda predefinidos, consulte o artigo este [exemplo GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Sugestões sobre como atualizar os dados de referência ##

1. Substituir blobs de dados de referência não irão causar a análise da cadeia recarregar o blob e em alguns casos pode causar a tarefa falhar. O caminho recomendado para alterar os dados de referência é adicionar um novo blob com o mesmo contentor e padrão do caminho definido na entrada de tarefa e utilizar uma data/hora **maior do** que aquele especificado pelo último blob na sequência.
2.  Dados de referência blobs não são ordenadas por hora de "Última modificação" o blob, mas apenas pela hora e data especificada no blob atribuir um nome utilizando a {data} e {tempo} substituições.
3.  Em algumas ocasiões que uma tarefa deve voltar atrás tempo, por conseguinte, referência dados blobs tem não ser alteradas ou eliminadas.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum Azure da cadeia Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximos passos
Tenha de foram introduzidas para a análise da cadeia, um serviço gerido de transmissão de análise de dados a partir da Internet de coisas. Para saber mais sobre este serviço, consulte o artigo:

- [Começar a utilizar o Azure da cadeia Analytics](stream-analytics-get-started.md)
- [Tarefas de análise do Azure da cadeia de escala](stream-analytics-scale-jobs.md)
- [Azure sequência Analytics referência da linguagem de consulta](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da sequência Azure Analytics gestão REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
