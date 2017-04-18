<properties
    pageTitle="Lista de recursos de armazenamento Azure com a biblioteca de cliente do Microsoft Azure armazenamento para C++ | Microsoft Azure"
    description="Saiba como utilizar a listagem APIs na biblioteca de cliente do Microsoft Azure armazenamento para C++ enumerar contentores, blobs, filas, tabelas e entidades."
    documentationCenter=".net"
    services="storage"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="list-azure-storage-resources-in-c"></a>Recursos da lista de armazenamento Azure no C++

Operações de lista são tecla para muitos cenários de desenvolvimento com armazenamento do Windows Azure. Este artigo descreve como a forma mais eficiente enumerar objetos em armazenamento do Windows Azure utilizando a listagem APIs fornecidos na biblioteca de cliente do Microsoft Azure armazenamento para C++.

>[AZURE.NOTE] Este guia destina-se a biblioteca de cliente de armazenamento do Azure para versão C++ 2. x, que se encontra disponível através de [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

A biblioteca de cliente do armazenamento fornece uma variedade de métodos para objetos de lista ou consulta no armazenamento do Windows Azure. Este artigo aborda os cenários seguintes:

-   Contentores de lista numa conta
-   Lista blobs num contentor ou diretório virtual blob
-   Lista filas numa conta
-   Lista de tabelas numa conta
-   Entidades de consulta numa tabela

Cada um dos seguintes métodos é apresentada com diferentes sobrecargas para cenários diferentes.

## <a name="asynchronous-versus-synchronous"></a>Comportamento assíncrono ou síncrono

Uma vez que a biblioteca de cliente de armazenamento para C++ é incorporada na parte superior da [biblioteca de C++ resto](https://github.com/Microsoft/cpprestsdk), implicitamente suportamos operações assíncronas utilizando [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Por exemplo:

    pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;

Operações síncronas moldagem as operações assíncronas correspondentes:

    list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
    {
        return list_blobs_segmented_async(token).get();
    }

Se estiver a trabalhar com vários serviços ou aplicações módulos, recomendamos que utilize a assíncrona APIs diretamente em vez de criar um tópico para ligar a sincronizar APIs, que impactos significativamente o desempenho do seu.

## <a name="segmented-listing"></a>Lista segmentada

A escala de armazenamento na nuvem requer listagem segmentada. Por exemplo, pode ter sobre um milhão blobs existentes num contentor de Blobs do Azure ou sobre um biliões entidades numa tabela do Azure. Estes não são números teóricos, mas casos de utilização de cliente real.

Por conseguinte, é prático para listar todos os objetos numa única resposta. Em vez disso, pode listar objetos através de paginação. Cada um na lista APIs tem um *segmentado* sobrecarga.

A resposta para uma operação de listagem segmentado inclui:

-   <i>_segment</i>, que contém o conjunto de resultados devolvidos por uma única chamada à API listagem.
-   *continuation_token*, que lhe é transmitido a chamada seguinte para poder obter a próxima página de resultados. Quando não existem mais resultados a devolver, o token de continuação de notas é nulo.

Por exemplo, uma chamada normal para listar todos os blobs num contentor poderão assemelhar-se o fragmento de código seguinte. O código está disponível no nosso [amostras](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

    // List blobs in the blob container
    azure::storage::continuation_token token;
    do
    {
        azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_diretory(it->as_directory());
        }
    }

        token = segment.continuation_token();
    }
    while (!token.empty());

Tenha em atenção que o número de resultados devolvidos numa página pode ser controlado pelo parâmetro *max_results* no sobrecarga de cada API, por exemplo:

    list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
        blob_listing_details::values includes, int max_results, const continuation_token& token,
        const blob_request_options& options, operation_context context)

Se não especificar o parâmetro *max_results* , é devolvido o valor máximo predefinido de até 5000 resultados numa única página.

Tenha em atenção que uma consulta de armazenamento de tabela do Azure pode devolver sem registos, ou menos registos que o valor do parâmetro *max_results* que especificou, mesmo se o token de continuação de notas não está vazio. Um motivo poderá ser que a consulta não foi possível concluir em cinco segundos. Desde que o token de continuação de notas não estiver vazio, deve continuar a consulta e o seu código não deve assumir o tamanho dos resultados de segmento.

O padrão de codificação recomendado para a maioria dos cenários é segmentado listar, que fornece o progresso explícito da lista ou consultar e como o serviço responde a cada pedido. Particularmente para aplicações C++ ou serviços, de níveis inferiores controlo sobre o progresso de listagem poderão ajudá-controlo memória e o desempenho.

## <a name="greedy-listing"></a>Lista melão

Versões anteriores da biblioteca de cliente de armazenamento para C++ (versões 0.5.0 pré-visualizar e versões anteriores) incluído não segmentado listagem APIs tabelas e filas, tal como no exemplo seguinte:

    std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
    std::vector<table_entity> execute_query(const table_query& query) const;
    std::vector<cloud_queue> list_queues() const;

Estes métodos foram implementados como wrappers das APIs segmentados. Para cada resposta da listagem segmentada, o código acrescentado os resultados por vetor e devolvido todos os resultados após os contentores completos foram pesquisados.

Esta abordagem poderão funcionar quando a conta de armazenamento ou tabela contém um pequeno número de objetos. No entanto, com um aumento no número de objetos, a necessidade de memória poderia aumentar sem limite, porque permaneceram todos os resultados na memória. Uma operação de listagem pode demorar muito tempo, durante o qual o autor da chamada tinha não são registadas informações sobre o progresso.

Estes listagem melão APIs no SDK não existam na c#, Java ou o ambiente de JavaScript Node.js. Para evitar potenciais problemas de utilização destas APIs melão, podemos removeu na versão 0.6.0 pré-visualização.

Se o seu código está a chamar estas APIs melão:

    std::vector<azure::storage::table_entity> entities = table.execute_query(query);
    for (auto it = entities.cbegin(); it != entities.cend(); ++it)
    {
        process_entity(*it);
    }

Em seguida, deverá modificar o seu código para utilizar na lista segmentada APIs:

    azure::storage::continuation_token token;
    do
    {
        azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
        {
            process_entity(*it);
        }

        token = segment.continuation_token();
    } while (!token.empty());

Ao especificar o parâmetro *max_results* do segmento, pode equilibrar entre os números de pedidos e a utilização de memória para cumprir considerações sobre o desempenho para a sua aplicação.

Para além disso, se estiver a utilizar a lista segmentada APIs, mas armazenar os dados numa coleção local num estilo "melão", também recomendamos vivamente que reestruturar o seu código para processar armazenar os dados numa coleção local cuidadosamente à escala.

## <a name="lazy-listing"></a>Lista de entrada

Apesar de listagem melão elevado potenciais problemas, é conveniente se não existirem demasiados objectos no contentor.

Se também estiver a utilizar c# ou Oracle Java SDK, deve estar familiarizado com o modelo de programação vasto, que disponibiliza um entrada de estilo listar, onde os dados num determinado desfasamento seja apenas obtidos a se for necessário. Em C++, o modelo com base em iteração também fornece uma abordagem semelhante.

Uma listagem preguiça típica API, utilizando **list_blobs** de exemplo, tem o seguinte aspeto:

    list_blob_item_iterator list_blobs() const;

Um fragmento de código típicas que utiliza o padrão de listagem preguiça poderá ter este aspeto:

    // List blobs in the blob container
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_directory(it->as_directory());
        }
    }

Tenha em atenção que listagem preguiça só está disponível no modo síncrono.

Em comparação com listagem melão, listagem preguiça obtém dados apenas quando for necessário. Em folhas de rosto,-obtém dados a partir do armazenamento Azure apenas quando a iteração seguinte move o cursor para o segmento seguinte. Por conseguinte, a utilização de memória é controlada com um tamanho de delimitada e, a operação é rápida.

Lista preguiça APIs estão incluídas na biblioteca de cliente de armazenamento para C++ na versão 2.2.0.

## <a name="conclusion"></a>Conclusão

Neste artigo, iremos abordado diferentes sobrecargas para indicar APIs para vários objetos na biblioteca de cliente de armazenamento para C++. Resumindo:

-   APIs assíncrona recomenda-se vivamente em vários cenários módulos.
-   Lista segmentada recomenda-se para a maioria dos cenários.
-   Lista preguiça é fornecida na biblioteca de um dispositivo de moldagem conveniente em cenários síncronos.
-   Lista melão não é recomendada e foi removida a partir da biblioteca.

##<a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o armazenamento do Azure e a biblioteca do cliente para C++, consulte os seguintes recursos.

-   [Como utilizar o armazenamento de Blobs do C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Como utilizar o armazenamento de tabela a partir do C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Como utilizar o armazenamento de fila de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Biblioteca de cliente de armazenamento Azure para documentação da C++ API.](http://azure.github.io/azure-storage-cpp/)
-   [Blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   [Documentação do armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
