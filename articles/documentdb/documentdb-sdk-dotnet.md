<properties 
    pageTitle="DocumentDB .NET API & SDK | Microsoft Azure" 
    description="Saiba tudo sobre o .NET API e SDK incluindo lançamento datas, extinção datas e as alterações efetuadas entre cada versão do DocumentDB .NET SDK." 
    services="documentdb" 
    documentationCenter=".net" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB APIs e SDK 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [NODE.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTO](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-net-api-and-sdk"></a>DocumentDB .NET API e SDK

<table>
<tr><td>**Transferir SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência .NET API](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Amostras**</td><td>[Exemplos de código .NET](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Introdução**</td><td>[Começar a trabalhar com o DocumentDB .NET SDK](documentdb-get-started.md)</td></tr>
<tr><td>**Tutorial do Web app**</td><td>[Desenvolvimento de aplicações Web com DocumentDB](documentdb-dotnet-application.md)</td></tr>
<tr><td>**Atual framework suportado**</td><td>[Microsoft .NET Framework 4,5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Notas de lançamento

> [AZURE.IMPORTANT] Começando 1.9.2 versão, poderá receber System.NotSupportedException quando consultar colecções com partições. Para evitar este erro, certifique-se de que o processo do anfitrião é 64 bits. Para os projectos executáveis, pode fazê-lo desmarcando a opção de "Preferir 32 bits" na janela de propriedades do projeto, no separador criar.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)

  - Suporte de conectividade direta adicionada para coleções de sites com partições.
  - Desempenho melhorado para o nível de consistência delimitada Staleness.
  - Adicionado polígono LineString tipos de dados e ao especificar coleção de política para consultas espaciais geo vedações de indexação.
  - LINQ suporte adicionada para StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter ao traduzir predicados.
  - Várias correções de erros SDK.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)

  - Fixa um problema que causaram o NotFoundException seguinte: A sessão de leitura não está disponível para o token de sessão de entrada. Esta exceção ocorreu em alguns casos, quando consultar leitura-se à região de uma conta distribuído geo.
  - Expostos a propriedade ResponseStream na classe ResourceResponse, que permite aos acesso direto para a sequência subjacente a partir de uma resposta.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)

  - Modificado as classes ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse para implementar a interface de pública correspondente para que pode ser mocked para o teste pelo esforço implementação (TDD).
  - Fixo um problema que causado num cabeçalho de chave partição mal ao utilizar um objecto JsonSerializerSettings personalizado para os dados serialização.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)

  - Fixa um problema que causado longa consultas para falhar com o erro: token de autorização não é válida a hora atual.
  - Fixo um problema que removido o SqlParameterCollection original do cruzada partição início/ordenação por consultas.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)

  - Obter suporte adicionada para consultas paralelas para coleções de sites com partições.
  - Obter suporte adicionada para cruzada partição ORDER BY e início consultas de colecções com partições.
  - Fixo as referências em falta para DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll que são necessárias ao referenciar um projeto DocumentDB com uma referência para o pacote de DocumentDB Nuget.
  - Fixo a capacidade de utilizar parâmetros de diferentes tipos quando utilizar as funções definidas pelo utilizador num LINQ. 
  - Fixo um erro para contas de replicadas globalmente onde Upsert chamadas foram a ser direccionadas para ler localizações em vez de escrever localizações.
  - Adicionado métodos para a interface de IDocumentClient que estavam em falta: 
      - Método de UpsertAttachmentAsync leva-o até mediaStream e opções como parâmetros
      - Método de CreateAttachmentAsync leva-o até opções como um parâmetro
      - Método de CreateOfferQuery leva-o até querySpec como um parâmetro.
  - Classes públicas não protegidas que são expostas na interface de IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Adicionado o suporte para contas com várias região base de dados.
  - Obter suporte adicionada para repetir no pedidos desacelerados.  Utilizador pode personalizar o número de tentativas e o tempo de espera max configurando a propriedade ConnectionPolicy.RetryOptions.
  - Adicionar uma nova interface IDocumentClient que define as assinaturas de todas as propriedades de DocumenClient e métodos.  Como parte desta alteração, também alteradas métodos de extensão que criam IQueryable e IOrderedQueryable aos métodos na própria classe DocumentClient.
  - Adicionado a opção de configuração para definir ServicePoint.ConnectionLimit para um determinado DocumentDB Uri de ponto final.  Utilize ConnectionPolicy.MaxConnectionLimit para alterar o valor predefinido, que está definido para 50.
  - Preterido IPartitionResolver e na sua implementação.  Suporte para IPartitionResolver é agora obsoleto. É recomendável que utilize divididos coleções de armazenamento mais elevado e débito.


### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Adicionado que uma sobrecarga para Uri com base no método de ExecuteStoredProcedureAsync leva-o até RequestOptions como um parâmetro.
  
### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Adicionado tempo para o suporte do live (TTL) para documentos.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Fixo um erro na embalagem Nuget do .NET SDK para embalagem-lo como parte de uma solução do serviço em nuvem Azure.
  
### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Implementado [coleções de sites com partições](documentdb-partition-data.md) e [níveis de desempenho definidos pelo utilizador](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Fixo]** Consultar DocumentDB inicia de ponto final: ' System.Net.Http.HttpRequestException: erro ao copiar o conteúdo a um fluxo de.

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - LINQ expandido suportam incluindo operadores novos para expressões condicionais, paginação e intervalo de comparação.
    - Tomar operador para activar o comportamento de SELECIONE início no LINQ
    - Operador de CompareTo para ativar comparações de intervalo de cadeias
    - Condicional (?) e a adesão operadores (?)
  - **[Fixo]** ArgumentOutOfRangeException ao combinar projecções de modelo com onde no linq consulta.  [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Fixo]** Se selecionar não estiver a expressão última o fornecedor de LINQ assumido sem projecções e produzidos SELECIONE * incorretamente.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Upsert implementado, métodos UpsertXXXAsync adicionado
 - Melhorias de desempenho para todos os pedidos de
 - Suporte de LINQ fornecedor para condicional, adesão e métodos de CompareTo para as cadeias
 - **[Fixo]** Fornecedor de LINQ--> Implementar contém método na lista para gerar o mesmo SQL como IEnumerable e matriz
 - **[Fixo]** BackoffRetryUtility utiliza a mesma HttpRequestMessage novamente em vez de criar um novo, repetir
 - **[Obsoleto]** UriFactory.CreateCollection--> agora deve utilizar UriFactory.CreateDocumentCollection
 
### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Fixo]** Problemas de localização ao utilizar as informações de cultura en não como ln das-ln das etc. 
 
### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Encaminhamento de ID com base
    - Helper UriFactory novo para o ajudar com o ID de construção com ligações de recursos base
    - Novos overloads no DocumentClient tirar no URI
  - Adicionado IsValid() e IsValidDetailed() no LINQ para geoespaciais
  - Suporte de fornecedor LINQ avançada
    - **Matemática** - Abs, Acos, ASEN, Atan, Arred. excesso, Cos, Exp, Arred, registo, Log10, p, Round, início de sessão, sen, RAIZQ, Tan, truncar
    - **Cadeia** - Concat, contém, EndsWith, IndexOf, contagem, ToLower, TrimStart, substituir, inverter, TrimEnd, StartsWith, subcadeia, ToUpper
    - **Matriz** - Concat, contiver, contar
    - **No** operador

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Suporte adicionada para modificar políticas de indexação
    - Novo método de ReplaceDocumentCollectionAsync no DocumentClient
    - Nova propriedade IndexTransformationProgress no ResourceResponse<T> para controlar o progresso de percentagem de alterações de política de índice remissivo
    - Está agora mutáveis DocumentCollection.IndexingPolicy
  - Suporte adicionada para espacial indexação e consulta
    - Novo Microsoft.Azure.Documents.Spatial espaço de nomes de serialização/anulação da serialização tipos espaciais como o ponto e polígono
    - Nova classe SpatialIndex GeoJSON dados armazenados num DocumentDB a indexação
  - **[Fixo]** : consulta SQL incorretos gerada a partir de expressão linq [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dependência de Newtonsoft.Json v5.0.7 
- Alterações para suportar Order By
  - Suporte de fornecedor LINQ para OrderBy() ou OrderByDescending()
  - IndexingPolicy para suportar Order By 
  
        **NB: Possible breaking change** 
  
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Suporte para criar a partições dados utilizando as novas classes HashPartitionResolver e RangePartitionResolver e o IPartitionResolver
- DataContract serialização
- Suporte de GUID no fornecedor de LINQ
- Suporte UDF no LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- DAS VERSÕES DG SDK

> [AZURE.NOTE]
Ocorreu uma alteração do nome do pacote NuGet entre pré-visualizar e GA. Vamos movido de **Microsoft.Azure.Documents.Client** para **Microsoft.Azure.DocumentDB**
<br/>


### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-Preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- Pré-visualização SDK [obsoleto]

## <a name="release--retirement-dates"></a>Libertar o & extinção datas
A Microsoft irá fornecer notificação, pelo menos, **12 meses** antes da disponibilização reforma um SDK para poder lisa a transição para uma versão mais recente/suportada.

Novas funcionalidades e funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal é recomendado que atualizar sempre para a versão mais recente SDK mais cedo possível. 

Qualquer pedido DocumentDB utilizando um SDK retirado será recusado pelo serviço.

> [AZURE.WARNING]
Todas as versões do Azure DocumentDB SDK para .NET antes de versão **1.0.0** vai ser foi removidas no **29 de Fevereiro de 2016**. 
 
<br/>
 
| Versão | Data de lançamento | Data da reforma 
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 27 de Setembro de 2016 |---
| [1.9.5](#1.9.5) | 01 de Setembro de 2016 |---
| [1.9.4](#1.9.4) | 24 de Agosto de 2016 |---
| [1.9.3](#1.9.3) | 15 de Agosto de 2016 |---
| [1.9.2](#1.9.2) | 23 de Julho de 2016 |---
| 1.9.1 | Preterido |---
| 1.9.0 | Preterido |---
| [1.8.0](#1.8.0) | 14 de Junho de 2016 |---
| [1.7.1](#1.7.1) | 06 de Maio de 2016 |---
| [1.7.0](#1.7.0) | 26 de Abril de 2016 |---
| [1.6.3](#1.6.3) | 08 de Abril de 2016 |---
| [1.6.2](#1.6.2) | 29 de Março de 2016 |---
| [1.5.3](#1.5.3) | 19 de Fevereiro de 2016 |---
| [1.5.2](#1.5.2) | 14 de Dezembro de 2015 |---
| [1.5.1](#1.5.1) | 23 de Novembro de 2015 |---
| [1.5.0](#1.5.0) | 05 de Outubro de 2015 |---
| [1.4.1](#1.4.1) | 25 de Agosto de 2015 |---
| [1.4.0](#1.4.0) | 13 de Agosto de 2015 |---
| [1.3.0](#1.3.0) | 05 de Agosto de 2015 |---
| [1.2.0](#1.2.0) | 06 de Julho de 2015 |---
| [1.1.0](#1.1.0) | 30 de Abril de 2015 |---
| [1.0.0](#1.0.0) | 08 de Abril de 2015 |---
| [0.9.3-prelease](#0.9.x-preview) | 12 de Março de 2015 | 29 de Fevereiro de 2016
| [0.9.2-prelease](#0.9.x-preview) | Janeiro de 2015 | 29 de Fevereiro de 2016
| [.9.1-prelease](#0.9.x-preview) | 13 de Outubro de 2014 | 29 de Fevereiro de 2016
| [0.9.0-prelease](#0.9.x-preview) | 21 de Agosto de 2014 | 29 de Fevereiro de 2016

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre DocumentDB, consulte a página de serviço do [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 
