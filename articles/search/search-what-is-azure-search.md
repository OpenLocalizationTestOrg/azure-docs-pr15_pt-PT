<properties
    pageTitle="O que é o Azure pesquisa | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Pesquisa Azure é um serviço de pesquisa com gestão alojado nuvem. Saiba mais nesta descrição geral de funcionalidade."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="what-is-azure-search"></a>O que é o Azure pesquisa?

Procura Azure é uma solução de pesquisa-como-a-service na nuvem que delegados management server e infraestrutura para a Microsoft, deixando-o com um serviço de prontos a utilizar que pode preencher com os seus dados e, em seguida, utilize para adicionar pesquisa ao seu web ou aplicação móvel. Pesquisa Azure permite-lhe adicionar facilmente uma experiência de pesquisa robustas para as suas aplicações utilizando um simples [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ou [.NET SDK](search-howto-dotnet-sdk.md) sem gerir a infraestrutura de pesquisa ou tornar-se um especialista na pesquisa.

## <a name="give-your-users-a-powerful-search-experience"></a>Dar-lhe os seus utilizadores uma experiência de pesquisa avançada

**Consultas poderosas** pode ser elaborado utilizando a [sintaxe da consulta simples](https://msdn.microsoft.com/library/azure/dn798920.aspx), que disponibiliza os operadores lógicos, operadores de pesquisa de expressão, operadores sufixo, operadores precedência. Para além disso, a [sintaxe da consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) pode ativar a pesquisa nítida, pesquisa de proximidade, aumentar a termos e expressões normais. Pesquisa Azure também suporta analisadores lexicais personalizados para permitir que a sua aplicação para processar consultas de pesquisa complexa utilizando a correspondência fonética e expressões normais.

**Suporte para o idioma** é [incluído para 56 idiomas diferentes](https://msdn.microsoft.com/library/azure/dn879793.aspx). Utilizar Lucene analisadores e analisadores Microsoft (refinados por anos de linguagem natural processamento no Office e o Bing), Azure pesquisa pode analisar o texto na caixa de pesquisa da sua aplicação para processar inteligente linguística específicas do idioma incluindo tempos verbais, género, substantivos plural irregulares (por exemplo, ' rato' vs. 'rato'), word retirar composição, word recentes (para os idiomas sem espaços) e mais.

**Sugestões de pesquisa** pode ser ativado para barras de pesquisa de preenchimento automático e consultas limitam. [Real documentos no seu índice são sugeridos](https://msdn.microsoft.com/library/azure/dn798936.aspx) como utilizadores introduza pesquisa parcial de entrada.

**Acertar realce** [permite que](https://msdn.microsoft.com/library/azure/dn798927.aspx) os utilizadores vejam o fragmento do texto em cada resultado que contém as correspondências para a sua consulta. Qual pode escolher quais os campos devolvem fragmentos realçados.

**Navegação por facetas** facilmente é adicionado à sua página de resultados de pesquisa com a pesquisa do Azure. Utilizar [apenas um parâmetro de consulta única](https://msdn.microsoft.com/library/azure/dn798927.aspx), Azure pesquisa irá devolver todas as informações necessárias para construir uma experiência de pesquisa por facetas na IU sua aplicação para permitir que os utilizadores para desagregação e filtrar os resultados da pesquisa (por exemplo, filtro catálogo itens por intervalo de preços ou marca corporativa).

Suporte **geo espacial** permite-lhe processar inteligente, filtrar e apresentar localizações geográficas. Pesquisa Azure permite aos utilizadores explorar dados com base em proximidade de um resultado de pesquisa para uma localização específica ou com base numa região geográfica específica. Este vídeo explica como funciona: [9 de canal: os dados de pesquisa do Azure e Geoespaciais](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtros** podem ser utilizados para facilmente incorporar navegação por facetas na IU sua aplicação, melhorar a composição de consulta e filtrar com base no especificadas pelo utilizador ou programador-critérios. Crie filtros eficazes utilizando a [sintaxe de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Adquira os programadores com um serviço de fácil utilização

**Disponibilidade elevada** assegura uma experiência de serviço de pesquisa extremamente fiável. Quando dimensionada corretamente, [pesquisa Azure oferece um SLA 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

**Completamente geridas** como uma solução de ponto a ponto, pesquisa Azure requer realmente sem gestão de infraestrutura. O serviço pode ser facilmente adaptado para as suas necessidades por dimensionamento em duas dimensões lidar mais armazenamento do documento, superior carregamentos de consultas ou ambas.

**Integração de dados** utilizando [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) permite Azure a pesquisa para pesquisar automaticamente a base de dados do SQL Azure, Azure DocumentDB ou [armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) para sincronizar conteúdo do seu índice de pesquisa com o arquivo de dados principal.

**Violação de palavras do documento** está disponível (atualmente em pré-visualização) [para ler e indexar formatos de ficheiro principais](search-howto-indexing-azure-blob-storage.md) incluindo Microsoft Office, bem como os documentos PDF e HTML.

**Análise de tráfego de pesquisa** são [recolhidas e analisadas facilmente](search-traffic-analytics.md) para desbloquear informações a partir do que os utilizadores estiver a escrever na caixa de pesquisa.

**Pontuação simples** é dos principais benefícios da pesquisa do Azure. [Perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) são utilizadas para permitir que as organizações à relevância modelo como uma função de valores nos próprios documentos. Por exemplo, poderá querer produtos mais recentes ou descontado produtos que seja apresentado mais elevados nos resultados da pesquisa. Também pode criar perfis pontuação utilizar etiquetas para pontuação personalizada com base nas preferências de pesquisa do cliente que registados e armazenados em separado.

**Ordenar** é oferecidos para vários campos através do esquema de índice remissivo e, em seguida, alternada no momento da consulta com um parâmetro única pesquisa.

**Paginação** e limitação os resultados da pesquisa é [simples com o controlo levemente bonita](search-pagination-page-layout.md) que procura Azure oferece sobre os resultados da pesquisa.  

**Explorador de procura** permite-lhe emitir consultas relativamente a todos os índices direita a partir do portal Azure da sua conta para que possa facilmente testar consultas e otimizar os perfis de pontuação.

## <a name="how-it-works"></a>Como funciona

### <a name="1-provision-service"></a>1. Serviço de aprovisionar
Pode rodar o num serviço de pesquisa do Azure utilizando o [Portal do Azure](https://portal.azure.com/) ou a [API de gestão de recursos do Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

Dependendo de como configurar o serviço de pesquisa, irá utilizar na camada gratuita do serviço que é partilhada com outros subscritores de pesquisa do Azure, ou uma [paga camada](https://azure.microsoft.com/pricing/details/search/) que dedica recursos para ser utilizado apenas pelo seu serviço. Quando aprovisiona o seu serviço, também escolher a região do Centro de dados que aloja o seu serviço.

Dependendo de qual camada de serviço que escolher, pode dimensionar o seu serviço em duas dimensões: 1) adicionar réplicas para aumentar a sua capacidade para processar carregamentos de consultas frequente e 2) adicionar a partições a adicionar armazenamento para documentos mais. Processamento de débito de armazenamento e consulta do documento em separado, pode personalizar o seu serviço de pesquisa para as suas necessidades específicas.

### <a name="2-create-index"></a>2. Criar índice
Antes de pode carregar o conteúdo para o seu serviço de pesquisa do Azure, primeiro tem de definir um índice de pesquisa do Azure. Um índice remissivo é como uma tabela de base de dados que contém os dados e pode aceitar consultas de pesquisa. Definir o esquema de índice remissivo para mapear para a estrutura dos documentos que pretende procurar, semelhante a campos numa base de dados.

O esquema dos índices pode ser criado no Portal do Azure ou através de programação [utilizando o SDK .NET](search-howto-dotnet-sdk.md) ou [REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx). Assim que o índice está definido, em seguida, pode carregar os seus dados para o serviço de pesquisa do Azure onde é indexado posteriormente.

### <a name="3-index-data"></a>3. dados de índice remissivo
Assim que tiver definido os campos e atributos do seu índice, está pronto para carregar o seu conteúdo para o índice. Pode utilizar um modelo push ou solicitação para carregar os dados para o índice.

O modelo recolher é fornecido através de indexadores que podem ser configurados para num pedido ou agendadas atualizações (consulte [as operações de indexador Azure pesquisa serviço REST API ()](https://msdn.microsoft.com/library/azure/dn946891.aspx)), permitindo-lhe para facilmente ingerir esta última dados e as alterações de dados a partir de um Azure DocumentDB, base de dados do SQL Azure, armazenamento de Blobs do Azure ou do SQL Server alojado numa VM Azure.

O modelo de push é fornecido através do SDK ou REST APIs utilizado para enviar documentos atualizados para um índice remissivo. Pode emissão dados a partir de praticamente qualquer conjunto de dados utilizando o formato JSON. Consulte o artigo [Adicionar, atualizar, ou eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [como utilizar o .NET SDK)](search-howto-dotnet-sdk.md) para obter orientações sobre o carregamento de dados.

### <a name="4-search"></a>4. pesquisa
Assim que tiver povoada índice de pesquisa do Azure, pode agora [consultas de pesquisa de problema](https://msdn.microsoft.com/library/azure/dn798927.aspx) para o ponto final de serviço através de pedidos de HTTP simples com REST API ou o .NET SDK.

## <a name="try-it-now-for-free"></a>Experimente agora (gratuitamente!)
Pesquisa do Azure que pode experimentar hoje! Se já tiver uma conta Azure, pode [aprovisionar um serviço na camada gratuito](search-create-service-portal.md).

Se não tiver uma conta Azure que pode experimentar uma sessão de gratuita, 60 minutos com não inscrever-se necessário. Aceda ao [Tentar Azure aplicação de serviço de](http://go.microsoft.com/fwlink/p/?LinkId=618214) e selecione "Web App." Em seguida, selecione o modelo de "ASP.NET + Azure Search" para começar a utilizar.
