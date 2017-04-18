
<properties
    pageTitle="API DocumentDB Java & SDK | Microsoft Azure"
    description="Saiba tudo sobre o Java API e SDK incluindo lançamento datas, extinção datas e as alterações efetuadas entre cada versão do DocumentDB Java SDK."
    services="documentdb"
    documentationCenter="java"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB APIs e SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [NODE.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTO](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-java-api-and-sdk"></a>API DocumentDB Java e SDK

<table>
<tr><td>**Transferir SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência Java API](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Contribuir para SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Introdução**</td><td>[Começar a trabalhar com o SDK Java](documentdb-java-application.md)</td></tr>
<tr><td>**Tempo de execução suportado atual**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de lançamento

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)

  - Foi adicionado suporte para o nível de consistência BoundedStaleness.
  - Obter suporte adicionada para conectividade direta para operações CRUD para coleções de sites com partições.
  - Fixo um erro no consultar uma base de dados com SQL.
  - Fixo um erro na cache da sessão onde token de sessão pode ser definida incorretamente.

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)

  - Obter suporte adicionada para cruzada partição paralelas consultas.
  - Obter suporte adicionada para consultas início/ORDER BY de colecções com partições de.
  - Obter suporte adicionada para consistência forte.
  - Obter suporte adicionada para pedidos de nomes com base ao utilizar a conectividade direta.
  - Fixo tornar ActivityId permanecem consistentes em todas as repetições de pedido.
  - Fixo um erro relacionado com a cache da sessão quando recriar um conjunto com o mesmo nome.
  - Adicionado polígono LineString tipos de dados e ao especificar coleção de política para consultas espaciais geo vedações de indexação.
  - Fixos problemas com o documento Java para Java 1.8.

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - Fixo um erro na PartitionKeyDefinitionMap para coleções de única partição em cache e não efetuar obtenção extra partição pedidos de chaves.
  - Fixo um erro para não repetir quando é fornecido um valor de chave partição incorretos.

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - Adicionado o suporte para contas com várias região base de dados.
  - Obter suporte adicionada para repetir automática no pedidos desacelerados com opções para personalizar o tempo de espera max repetir e tentativas de repetição máx.  Consulte o artigo RetryOptions e ConnectionPolicy.getRetryOptions().
  - Preterido IPartitionResolver com base no código de criação de partições personalizado. Utilize colecções com partições para mais elevados armazenamento e débito.

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Suporte de política de repetir adicionada para limitação.  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Adicionado tempo para o suporte do live (TTL) para documentos.

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Implementado [coleções de sites com partições](documentdb-partition-data.md) e [níveis de desempenho definidos pelo utilizador](documentdb-performance-levels.md).

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Fixo um erro na HashPartitionResolver para gerar valores hash no pequeno endian que sejam consistentes com outros SDK.

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Adicionar Hash & intervalo partição resoluções para ajudá-lo a aplicações sharding em múltiplas partições.

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implemente Upsert. Novos upsertXXX métodos adicionados para suportar a funcionalidade de Upsert.
- Implemente o encaminhamento de ID com base. Sem alterações da API públicas, todas as alterações internas.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
- Lançamento ignorado para trazer o número da versão no alinhamento com outros SDK

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Suporta Geoespaciais índice
- Validar propriedade id para todos os recursos. IDs para recursos não podem conter ?, /, #, \, carateres ou terminar com um espaço.
- Adiciona o novo cabeçalho "índice transformação progresso" para ResourceResponse.

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- Implementa política de indexação V2

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- DAS VERSÕES DG SDK

## <a name="release--retirement-dates"></a>Libertar o & extinção datas
A Microsoft irá fornecer notificação, pelo menos, **12 meses** antes da disponibilização reforma um SDK para poder lisa a transição para uma versão mais recente/suportada.

Novas funcionalidades e funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal é recomendado que atualizar sempre para a versão mais recente SDK mais cedo possível.

Qualquer pedido DocumentDB utilizando um SDK retirado será recusado pelo serviço.

> [AZURE.WARNING]
Todas as versões do Azure DocumentDB SDK para Java antes de versão **1.0.0** vai ser foi removidas no **29 de Fevereiro de 2016**.

<br/>

| Versão | Data de lançamento | Data da reforma
| ---     | ---          | ---
| [1.9.1](#1.9.1) | 28 de Outubro de 2016 |---
| [1.9.0](#1.9.0) | 03 de Outubro de 2016 |---
| [1.8.1](#1.8.1) | 30 de Junho de 2016 |---
| [1.8.0](#1.8.0) | 14 de Junho de 2016 |---
| [1.7.1](#1.7.1) | 30 de Abril de 2016 |---
| [1.7.0](#1.7.0) | 27 de Abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de Março de 2016 |---
| [1.5.1](#1.5.1) | 31 de Dezembro de 2015 |---
| [1.5.0](#1.5.0) | 04 de Dezembro de 2015 |---
| [1.4.0](#1.4.0) | 05 de Outubro de 2015 |---
| [1.3.0](#1.3.0) | 05 de Outubro de 2015 |---
| [1.2.0](#1.2.0) | 05 de Agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de Julho de 2015 |---
| [1.0.1](#1.0.1) | 12 de Maio de 2015 |---
| [1.0.0](#1.0.0) | 07 de Abril de 2015 |---
| 0.9.5-prelease | 09 de mar de 2015 | 29 de Fevereiro de 2016
| 0.9.4-prelease | 17 de Fevereiro de 2015 | 29 de Fevereiro de 2016
| 0.9.3-prelease | 13 de Janeiro de 2015 | 29 de Fevereiro de 2016
| 0.9.2-prelease | 19 de Dezembro de 2014 | 29 de Fevereiro de 2016
| 0.9.1-prelease | 19 de Dezembro de 2014 | 29 de Fevereiro de 2016
| 0.9.0-prelease | 10 de Dezembro de 2014 | 29 de Fevereiro de 2016

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre DocumentDB, consulte a página de serviço do [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .
