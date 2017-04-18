<properties 
    pageTitle="API DocumentDB Python & SDK | Microsoft Azure" 
    description="Saiba tudo sobre o Python API e SDK incluindo lançamento datas, extinção datas e as alterações efetuadas entre cada versão do DocumentDB Python SDK." 
    services="documentdb" 
    documentationCenter="python" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB APIs e SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [NODE.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTO](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-python-api-and-sdk"></a>API DocumentDB Python e SDK

<table>
<tr><td>**Transferir SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência Python API](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Instruções de instalação SDK**</td><td>[Instruções de instalação Python SDK](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Contribuir para SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Introdução**</td><td>[Começar a trabalhar com o SDK Python](documentdb-python-application.md)</td></tr>
<tr><td>**Plataforma suportada atual**</td><td>[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de lançamento

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
- Obter suporte adicionada para Python 3.5.
- Obter suporte adicionada para utilizar um módulo de pedidos de agrupamento de ligações.
- Obter suporte adicionada para consistência sessão.
- Obter suporte adicionada para consultas de início/ORDENARPOR para coleções de sites com partições.


### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Suporte de política de repetir adicionada para pedidos desacelerados. (Pedidos desacelerados recebem uma pedido taxa demasiado grande exceção, código de erro 429.) Por predefinição, DocumentDB repetições nove horas para cada pedido quando é encontrado o código de erro 429, honrar a hora de retryAfter no cabeçalho da resposta. Um intervalo de tempo de repetir fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se pretende ignorar a hora de retryAfter devolvida pelo servidor entre o número de tentativas. DocumentDB agora aguarda até um máximo de 30 segundos para cada pedido que está a ser limitada (independentemente da contagem de repetir) e devolve a resposta com código de erro 429. Desta vez, também pode ser substituir na propriedade RetryOptions num objeto ConnectionPolicy.

- DocumentDB devolve agora x-ms-borboleta--contagem de tentativas e x-ms-throttle-retry-wait-time-ms como repetir os cabeçalhos de resposta no cada pedido para indicar borboleta contar e a hora de cummulative o pedido aguardado entre o número de tentativas.

- Removido a classe de RetryPolicy e a propriedade correspondente apresentado quando se clica na classe document_client (retry_policy) e em vez disso introduziu uma classe RetryOptions expor a propriedade do RetryOptions ConnectionPolicy escolares que podem ser utilizadas para substituir algumas das opções de repetir predefinido.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Adicionado o suporte para contas com várias região base de dados.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Adicionado o suporte para o tempo para Live(TTL) funcionalidade para documentos.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correções de erros relacionados com o servidor de lado partição para permitir que os carateres especiais no caminho partitionkey.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Implementado [coleções de sites com partições](documentdb-partition-data.md) e [níveis de desempenho definidos pelo utilizador](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Adicionar Hash & intervalo partição resoluções para ajudá-lo a aplicações sharding em múltiplas partições.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implemente Upsert. Novos UpsertXXX métodos adicionados para suportar a funcionalidade de Upsert.
- Implemente o encaminhamento de ID com base. Sem alterações da API públicas, todas as alterações internas.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Índice de Geoespaciais suporta.
- Validar propriedade id para todos os recursos. IDs para recursos não podem conter ?, /, #, \, carateres ou terminar com um espaço.
- Adiciona o novo cabeçalho "índice transformação progresso" para ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementa V2 política de indexação.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Suporta a ligação de proxy.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- DAS VERSÕES DG SDK.

## <a name="release--retirement-dates"></a>Libertar o & extinção datas
A Microsoft irá fornecer notificação, pelo menos, **12 meses** antes da disponibilização reforma um SDK para poder lisa a transição para uma versão mais recente/suportada.

Novas funcionalidades e funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal é recomendado que atualizar sempre para a versão mais recente SDK mais cedo possível. 

Qualquer pedido DocumentDB utilizando um SDK retirado será recusado pelo serviço.

> [AZURE.WARNING]
Todas as versões do Azure DocumentDB SDK para Python antes de versão **1.0.0** vai ser foi removidas no **29 de Fevereiro de 2016**. 

<br/>

| Versão | Data de lançamento | Data da reforma 
| ---     | ---          | ---
| [2.0.0](#2.0.0) | 29 de Setembro de 2016 |---
| [1.9.0](#1.9.0) | 07 de Julho de 2016 |---
| [1.8.0](#1.8.0) | 14 de Junho de 2016 |---
| [1.7.0](#1.7.0) | 26 de Abril de 2016 |---
| [1.6.1](#1.6.1) | 08 de Abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de Março de 2016 |---
| [1.5.0](#1.5.0) | 03 de Janeiro de 2016 |---
| [1.4.2](#1.4.2) | 06 de Outubro de 2015 |---
| [1.4.1](#1.4.1) | 06 de Outubro de 2015 |---
| [1.2.0](#1.2.0) | 06 de Agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de Julho de 2015 |---
| [1.0.1](#1.0.1) | 25 de Maio de 2015 |---
| [1.0.0](#1.0.0) | 07 de Abril de 2015 |---
| 0.9.4-prelease | 14 de Janeiro de 2015 | 29 de Fevereiro de 2016
| 0.9.3-prelease | 09 de Dezembro de 2014 | 29 de Fevereiro de 2016
| 0.9.2-prelease | 25 de Novembro de 2014 | 29 de Fevereiro de 2016
| 0.9.1-prelease | 23 de Setembro de 2014 | 29 de Fevereiro de 2016
| 0.9.0-prelease | 21 de Agosto de 2014 | 29 de Fevereiro de 2016

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre DocumentDB, consulte a página de serviço do [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 
