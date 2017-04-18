<properties
   pageTitle="Versões de API do Azure pesquisa | Microsoft Azure | API de procura"
   description="Política de versão para Azure pesquisa REST APIs e a biblioteca do cliente no .NET SDK."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# <a name="api-versions-in-azure-search"></a>Versões de API na pesquisa do Azure

Pesquisa Azure descer saída as atualizações de funcionalidades regularmente. Por vezes, mas não sempre, estas atualizações requerem-nos publicar uma nova versão do nosso API para poder preservar a compatibilidade com versões anteriores. Publicação de uma versão nova permite-lhe controlar quando e como integrar atualizações de serviço de pesquisa no seu código.

Como uma regra, vamos tentar publicar novas versões apenas quando for necessário, uma vez que podem referir-se de algumas esforço para atualizar o seu código para utilizar uma versão nova do API. Vamos apenas publicar uma nova versão se for necessária alterar algum aspecto da API de uma forma que quebras de compatibilidade com versões anteriores. Isto pode acontecer devido a correções para funcionalidades existentes ou devido a funcionalidades novas que alterar existente superfície de API.

Vamos siga a mesma regra existência de actualizações SDK. O SDK de pesquisa do Azure segue as regras de [controlo de versões semântico](http://semver.org/) , o que significa que a sua versão tem três partes: principais, secundária e criar número (por exemplo, 1.1.0). Vamos será disponibilizado uma nova versão principal de SDK apenas em caso de alterações que interromper compatibilidade com versões anteriores. Para as atualizações de funcionalidades não, podemos irão incrementar a versão secundária e para correções de erros podemos apenas aumenta a versão de compilação.

##<a name="snapshot-of-current-versions"></a>Instantâneo de versões atuais 

Abaixo está um instantâneo das versões atuais de todas as interfaces de programação para pesquisa Azure. Guias e outros detalhes podem ser encontrados nas secções subsequentes deste documento.

Interfaces|Mais recente versão principal|Estado
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1.1|Geralmente disponível, disponibilizada de Fevereiro de 2016
[Pré-visualização do .NET SDK](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|pré-visualização do 2.0|Pré-visualizar, disponibilizada Agosto de 2016
[Serviço REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|Ficará disponível
[Pré-visualização do serviço REST API](search-api-2015-02-28-preview.md)|2015-02-28-pré-visualização|Pré-visualização
[Gestão de REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|Ficará disponível

Para API do resto, incluindo o `api-version` numa chamada de cada é necessário. Isto torna mais fácil para uma versão específica, tal como uma pré-visualização API de destino. O exemplo seguinte demonstra como o `api-version` parâmetro for especificado:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Apesar de cada pedido tem um `api-version`, recomendamos que utilize a mesma versão para todos os pedidos de API. Este é especialmente true quando existirem novas versões de API apresentam atributos ou operações que não são reconhecidas por versões anteriores. Mistura de versões de API pode ter consequências inesperadas e deve ser evitada.
> 
O serviço REST API e gestão REST API têm versões umas das outras. Qualquer semelhança na números da versão é cocriação danos acidentais.

Ficará disponível (ou das versões DG) APIs podem ser utilizados na produção e está sujeito aos acordos do nível serviço Azure. Versões de pré-visualização têm a experimentais funcionalidades que não são sempre migradas para uma versão das versões DG. **Recomendamos vivamente contra utilizando a pré-visualização APIs nas aplicações de produção.**

##<a name="sdk-version-roadmap"></a>Informações gerais de versão SDK

Cada versão do .NET SDK destina-se uma versão específica da API do resto do serviço. Funcionalidades são lançadas pela primeira vez no REST API e, em seguida, implementadas no SDK.

O .NET SDK está agora disponibilizado e trabalho já está a decorrer na próxima versão. A tabela seguinte para a frente aspeto para versões futuras do SDK para que tenha uma ideia de como o que está a chegar o seguinte.

Versão .NET SDK|Versão REST API|Funcionalidades|ATE
----------------|----------------|--------|---
1.1|2015-02-28|Sintaxe de consulta Lucene|De Fevereiro de 2016
pré-visualização do 2.0|2015-02-28-pré-visualização|Analisadores personalizados, BLOBs do Azure e indexadores de tabela, mapeamentos de campo, ETags|Agosto de 2016
2. x|Nova versão das versões DG API|Igual a pré-visualização do 2.0|Antecipada Q4 2016

##<a name="about-preview-and-generally-available-versions"></a>Sobre as versões pré-visualizar e ficará disponível

Pesquisa Azure sempre previamente disponibilização de funcionalidades experimentais através da API REST em primeiro lugar, em seguida, através de versões de pré-lançamento do .NET SDK.

Pré-visualização funcionalidades não são garante a ser migrado para uma versão das versões DG. Considerando que funcionalidades numa versão das versões DG são consideradas estável e provavelmente não alterar, à exceção dos melhoramentos e correções do pequenas compatível com versões anteriores, pré-visualização funcionalidades estão disponíveis para testar e pioneira, com o propósito de recolher comentários sobre a estrutura da funcionalidade e implementação. 

No entanto, uma vez que funcionalidades de pré-visualização estão sujeitos a alteração, recomenda-se contra a escrever o código de produção, que assume uma dependência de versões de pré-visualização. Se estiver a utilizar uma versão mais antiga do pré-visualização, recomendamos que migrar para a versão (das versões DG) ficará disponível. 

Para o .NET SDK: orientações para a migração de código podem ser encontrada em [atualizar o .NET SDK](search-dotnet-sdk-migration.md).

Disponibilidade geral significa que Azure pesquisa está agora no contrato de nível de serviço (SLA). O SLA pode ser encontrado em [Acordos do nível de serviço de pesquisa de Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

