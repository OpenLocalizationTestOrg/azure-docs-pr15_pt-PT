<properties
    pageTitle="API DocumentDB Node.js & SDK | Microsoft Azure"
    description="Saiba tudo sobre o Node.js API e SDK incluindo lançamento datas, extinção datas e as alterações efetuadas entre cada versão do DocumentDB Node.js SDK."
    services="documentdb"
    documentationCenter="nodejs"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>DocumentDB APIs e SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [NODE.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTO](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##<a name="documentdb-nodejs-api-and-sdk"></a>API DocumentDB Node.js e SDK

<table>
<tr><td>**Transferir SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Documentação da API**</td><td>[Documentação de referência node.js API](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**Instruções de instalação SDK**</td><td>[Instruções de instalação](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Contribuir para SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Amostras**</td><td>[Exemplos de código node.js](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Obter tutorial de introdução**</td><td>[Começar a trabalhar com o SDK Node.js](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Tutorial do Web app**</td><td>[Criar uma Node.js aplicação web DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Plataforma suportada atual**</td><td>[NODE.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[NODE.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[NODE.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##<a name="release-notes"></a>Notas de lançamento

###<a name="1.10.0"/>1.10.0</a>

- Obter suporte adicionada para cruzada partição paralelas consultas.
- Obter suporte adicionada para consultas início/ORDER BY de colecções com partições de.

###<a name="1.9.0"/>1.9.0</a>

- Suporte de política de repetir adicionada para pedidos desacelerados. (Pedidos desacelerados recebem uma pedido taxa demasiado grande exceção, código de erro 429.) Por predefinição, DocumentDB repetições nove horas para cada pedido quando é encontrado o código de erro 429, honrar a hora de retryAfter no cabeçalho da resposta. Um intervalo de tempo de repetir fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se pretende ignorar a hora de retryAfter devolvida pelo servidor entre o número de tentativas. DocumentDB agora aguarda até um máximo de 30 segundos para cada pedido que está a ser limitada (independentemente da contagem de repetir) e devolve a resposta com código de erro 429. Desta vez, também pode ser substituir na propriedade RetryOptions num objeto ConnectionPolicy.

- DocumentDB devolve agora x-ms-borboleta--contagem de tentativas e x-ms-throttle-retry-wait-time-ms como repetir os cabeçalhos de resposta no cada pedido para indicar borboleta contar e a hora de cummulative o pedido aguardado entre o número de tentativas.

- Foi adicionada a classe de RetryOptions, expor a propriedade do RetryOptions a classe de ConnectionPolicy que pode ser utilizada para substituir algumas das opções de repetir predefinido.

###<a name="1.8.0"/>1.8.0</a>

 - Adicionado o suporte para contas com várias região base de dados.

###<a name="1.7.0"/>1.7.0</a>

- Adicionado o suporte para o tempo para Live(TTL) funcionalidade para documentos.

###<a name="1.6.0"/>1.6.0</a>

- Implementado [coleções de sites com partições](documentdb-partition-data.md) e [níveis de desempenho definidos pelo utilizador](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- Erro de RangePartitionResolver.resolveForRead corrigido onde não foi devolver ligações devido a um concat incorretas de resultados.

###<a name="1.5.5"/>1.5.5</a>

- Fixa hashParitionResolver resolveForRead(): quando sem tecla partição fornecida foi deitar exceção, em vez de voltar uma lista de todas as ligações registadas.

###<a name="1.5.4"/>1.5.4</a>

- Correções de problemas de [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - dedicado agente HTTPS: evitar modificar o agente global para fins de DocumentDB. Utilize um agente dedicado de todos os pedidos da biblioteca de.

###<a name="1.5.3"/>1.5.3</a>

- Correções emitem [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - corretamente alça traços na ids de multimédia.

###<a name="1.5.2"/>1.5.2</a>

- Correções emitem [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter escuta fugas aviso.

###<a name="1.5.1"/>1.5.1</a>

- Correções emitem [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - mudar o nome de pasta Hash hash para sistemas de maiúsculas e minúsculas.

### <a name="1.5.0"/>1.5.0</a>

- Suporte de sharding implementar adicionando resoluções de partição hash & intervalo.

### <a name="1.4.0"/>1.4.0</a>

- Implemente Upsert. Novos métodos de upsertXXX no documentClient.

### <a name="1.3.0"/>1.3.0</a>

- Ignorado para cumprir os números de versão alinhamento com outros SDK.

### <a name="1.2.2"/>1.2.2</a>

- Dividir Q promete bombom para o repositório de novo.
- Atualize para o ficheiro de pacote para npm registo.

### <a name="1.2.1"/>1.2.1</a>

- Implementa ID baseada em encaminhamento.
- Corrige o problema [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - atual propriedade está em conflito com current (de método).

### <a name="1.2.0"/>1.2.0</a>

- Obter suporte adicionada para Geoespaciais índice.
- Validar propriedade id para todos os recursos. Não podem conter IDs para recursos?, /, #, #47; & #47; carateres ou terminar com um espaço.
- Adiciona o novo cabeçalho "índice transformação progresso" para ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementa V2 política de indexação.

### <a name="1.0.3"/>1.0.3</a>

- Problema [#40] (https://github.com/Azure/azure-documentdb-node/issues/40) - implementada eslint e roncador configurações na core e promessa SDK.

### <a name="1.0.2"/>1.0.2</a>

- Problema [n. º 45](https://github.com/Azure/azure-documentdb-node/issues/45) - bombom promessas não inclui o cabeçalho com o erro.

### <a name="1.0.1"/>1.0.1</a>

- Implementado capacidade de consulta para conflitos, adicionando readConflicts, readConflictAsync e queryConflicts.
- Documentação da API atualizada.
- Emita [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync erro.

### <a name="1.0.0"/>1.0.0</a>

- DAS VERSÕES DG SDK.

## <a name="release--retirement-dates"></a>Libertar o & extinção datas
A Microsoft irá fornecer notificação, pelo menos, **12 meses** antes da disponibilização reforma um SDK para poder lisa a transição para uma versão mais recente/suportada.

Novas funcionalidades e funcionalidade e otimizações apenas são adicionadas ao SDK atual, como tal é recomendado que atualizar sempre para a versão mais recente SDK mais cedo possível.

Qualquer pedido DocumentDB utilizando um SDK retirado será recusado pelo serviço.

> [AZURE.WARNING]
Todas as versões do Azure DocumentDB SDK para Node.js antes de versão **1.0.0** vai ser foi removidas no **29 de Fevereiro de 2016**.

<br/>

| Versão | Data de lançamento | Data da reforma
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 03 de Outubro de 2016 |---
| [1.9.0](#1.9.0) | 07 de Julho de 2016 |---
| [1.8.0](#1.8.0) | 14 de Junho de 2016 |---
| [1.7.0](#1.7.0) | 26 de Abril de 2016 |---
| [1.6.0](#1.6.0) | 29 de Março de 2016 |---
| [1.5.6](#1.5.6) | 08 de Março de 2016 |---
| [1.5.5](#1.5.5) | 02 de Fevereiro de 2016 |---
| [1.5.4](#1.5.4) | 01 de Fevereiro de 2016 |---
| [1.5.2](#1.5.2) | 26 de Janeiro de 2016 |---
| [1.5.2](#1.5.2) | 22 de Janeiro de 2016 |---
| [1.5.1](#1.5.1) | 4 de Janeiro de 2016 |---
| [1.5.0](#1.5.0) | 31 de Dezembro de 2015 |---
| [1.4.0](#1.4.0) | 06 de Outubro de 2015 |---
| [1.3.0](#1.3.0) | 06 de Outubro de 2015 |---
| [1.2.2](#1.2.2) | 10 de Setembro de 2015 |---
| [1.2.1](#1.2.1) | 15 de Agosto de 2015 |---
| [1.2.0](#1.2.0) | 05 de Agosto de 2015 |---
| [1.1.0](#1.1.0) | 09 de Julho de 2015 |---
| [1.0.3](#1.0.3) | 04 de Junho de 2015 |---
| [1.0.2](#1.0.2) | 23 de Maio de 2015 |---
| [1.0.1](#1.0.1) | 15 de Maio de 2015 |---
| [1.0.0](#1.0.0) | 08 de Abril de 2015 |---
| 0.9.4-prerelease | 06 de Abril de 2015 | 29 de Fevereiro de 2016
| 0.9.3-prerelease | 14 de Janeiro de 2015 | 29 de Fevereiro de 2016
| 0.9.2-prerelease | 18 de Dezembro de 2014 | 29 de Fevereiro de 2016
| 0.9.1-prerelease | 22 de Agosto de 2014 | 29 de Fevereiro de 2016
| 0.9.0-prerelease | 21 de Agosto de 2014 | 29 de Fevereiro de 2016


## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Consulte também

Para saber mais sobre DocumentDB, consulte a página de serviço do [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) .
