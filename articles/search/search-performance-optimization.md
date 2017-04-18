<properties 
    pageTitle="Considerações Azure de desempenho e otimização de pesquisa | Microsoft Azure" 
    description="Ajustar o desempenho de pesquisa do Azure e configurar escala ideal" 
    services="search" 
    documentationCenter="" 
    authors="LiamCavanagh" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="liamca"/>

# <a name="azure-search-performance-and-optimization-considerations"></a>Considerações Azure de desempenho e otimização de pesquisa

Uma experiência de pesquisa excelente é uma chave para o sucesso para muitos dispositivos móveis e aplicações web. A partir de imobiliário, para utilizado mercados carro aos onlinehttps catálogos, pesquisa rápida e resultados relevantes irão afetar a experiência do cliente. Este documento destina-se a ajuda-o a descobrir melhores práticas para saber como tirar o máximo partido pesquisa Azure, especialmente para obter cenários avançados com os requisitos de sofisticadas para escalabilidade, multilingue de suporte ou classificação personalizada.  Além disso, este documento destaca internos e abrange abordagens que funcionam de forma eficaz nas aplicações de cliente do mundo real.

## <a name="performance-and-scale-tuning-for-search-services"></a>Desempenho e escala optimização para serviços de pesquisa

São todas utilizámos para procurar motores de como o Bing e Google e alto desempenho oferecem.  Como resultado, quando utilizarem os clientes web com capacidade de pesquisa ou aplicação móvel, irá esperar características semelhantes de desempenho.  Quando otimizar para o desempenho da procura, uma das melhores abordagens é focar-se em latência, que é o tempo que demora uma consulta para concluir e devolver os resultados.  Quando otimizar para latência da pesquisa é importante para:

1. Selecione uma latência de destino (ou período de tempo máximo) que pedem uma pesquisa típica ações deverá permitir a concluir.

2. Criar e testar uma carga de trabalho real contra o serviço de pesquisa com um conjunto de dados real para medir estas taxas de latência.

3. Comece com um número mínimo de consultas por segundo (QPS) e continuar aumentar o número executado durante o ensaio até a latência da consulta desce abaixo a latência destino definido.  Esta é uma referência importante para ajudar a planear a escala à medida que aumenta a aplicação em utilização.

4. Sempre que possível, reutilize ligações HTTP.  Se estiver a utilizar o Azure pesquisa .NET SDK, isto significa que deve reutilizar uma instância ou instância de [SearchIndexClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx) e, se estiver a utilizar a API REST, deve reutilizar uma única HttpClient.
 
Ao criar estes testar das cargas de trabalho, existem algumas características de pesquisa do Azure a ter em conta:

1. É possível push para que pesquisa muitos consultas ao mesmo tempo que vai ser sise os recursos disponíveis no seu serviço de pesquisa do Azure.  Quando isto acontecer, verá HTTP 503 códigos de resposta.  Por este motivo, é melhor iniciar com vários intervalos de pedidos de pesquisa para ver as diferenças entre as taxas de latência à medida que adiciona mais pedidos de pesquisa.

2. Carregar de conteúdo a pesquisa do Azure irá afetar o desempenho geral e a latência do serviço de pesquisa do Azure.  Se pretender enviar dados enquanto os utilizadores estão a executar pesquisas, é importante tirar este carga de trabalho para a conta no seu testes.

3. Nem todas as consultas de pesquisa irão efetuar aos mesmos níveis de desempenho.  Por exemplo, uma sugestão de pesquisa ou procurar documento normalmente irá efetuar mais depressa do que uma consulta com um número significativo de facetas e filtros.  É melhor tirar as várias consultas que esperava ver em consideração quando construir testes.  

4. Variação de pedidos de pesquisa é importante porque caso continuamente a executar os mesmo pedidos de pesquisa, colocação em cache de dados iniciará tornar o desempenho aspeto melhor do que esta poderá com uma consulta mais diversificada definida.

> [AZURE.NOTE] [Testes do Visual Studio carga](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) é uma forma muito bom para desempenhar seu prévia à medida permite-lhe executar pedidos de HTTP à medida que precisa para executar consultas de pesquisa do Azure e permite-parallelization de pedidos de.

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Dimensionamento Azure procurar taxas de consulta alta e limitada pedidos

Quando está a receber demasiados pedidos desacelerados ou exceder as taxas de latência de destino a partir de uma carga da consulta maior, pode ver para diminuir taxas de latência numa de duas formas:

1. **Aumentar réplicas:**  Uma réplica é como uma cópia dos seus dados permitindo Azure a pesquisa para carregar os pedidos de saldo contra várias cópias.  Todos os balanceamento de carga e replicação de dados ao longo réplicas é gerida pela pesquisa do Azure e pode alterar o número de réplicas atribuída do seu serviço em qualquer altura.  Pode alocar até 12 réplicas num serviço de pesquisa padrão e 3 réplicas num serviço de pesquisa básica.  Podem ser ajustadas réplicas quer a partir do [Portal do Azure](search-create-service-portal.md) ou utilizar a [API de gestão de pesquisa do Azure](search-get-started-management-api.md).

2. **Aumentar camada de pesquisa:**  Pesquisa Azure é fornecido um [número de camadas](https://azure.microsoft.com/pricing/details/search/) e cada uma das seguintes camadas oferece diferentes níveis de desempenho.  Em alguns casos, poderá ter tantas consultas que a camada que estiver não pode fornecer taxas de latência suficientemente baixa, mesmo quando réplicas são tirar o máximo partido.  Neste caso, poderá querer ter em consideração tirar partido de uma das camadas de pesquisa mais elevadas tal como a camada de Azure pesquisa S3 é adequado para cenários com grandes quantidades de documentos e consulta extremamente elevado cargas de trabalho.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Dimensionamento procurar Azure lentas consultas individuais

Qualquer outra razão porque é que as taxas de latência podem ser lentas é a partir de uma consulta única demorar demasiado tempo a concluir.  Neste caso, a adicionar réplicas não irão melhorar as taxas de latência.  Para este incidente existem duas opções disponíveis:

1. **Aumentar a partições** Uma partição é um mecanismo para dividir os seus dados através de recursos adicionais.  Por este motivo, quando adiciona uma segunda partição, obtém dividir os seus dados em dois.  Uma partição de terceira divide o índice remissivo em três, etc.  Isto também tem o efeito que em alguns casos, consultas lentas fará mais rápido devido parallelization de cálculo.  Existem alguns exemplos de onde podemos viu este parallelization funcionam extremamente bem com consultas que possuem consultas selectividade baixa.  Isto é composta por consultas que corresponde ao número de documentos ou quando necessita de faceting fornecer contagens sobre grandes quantidades de documentos.  Uma vez que não existem muitas cálculo conforme necessário para pontuação a relevância dos documentos ou para contar o número de documentos, adicionar a partições extra pode ajudar a fornecer cálculo adicional.  

   Pode ser um máximo de 12 partições no serviço de pesquisa padrão e 1 partição no serviço de pesquisa básica.  Podem ser ajustadas a partições quer a partir do [Portal do Azure](search-create-service-portal.md) ou utilizar a [API de gestão de pesquisa do Azure](search-get-started-management-api.md).

2. **Limitar os campos de alta cardinalidade:** Um campo de cardinalidade alta é composta por um facetable ou filtrável campo que tem um número de valores exclusivos significativo e como resultado, retira muito dos recursos para calcular os resultados ao longo do.   Por exemplo, definir um campo ID do produto ou descrição como facetable filtráveis faria para alta cardinalidade uma vez que a maioria dos valores a partir do documento ao documento são exclusivas. Sempre que possível, limite o número de campos de cardinalidade alta.

3. **Aumentar camada de pesquisa:**  Mover até uma camada superior do Azure pesquisa pode ser outra forma para melhorar o desempenho de consultas lentas.  Cada camada superior também fornece CPU mais rápida e mais memória que pode ter um impacto positivo no desempenho da consulta.

## <a name="scaling-for-availability"></a>Dimensionamento para disponibilidade

Réplicas não só ajudam a reduzir a latência da consulta, mas também podem permitir para elevada disponibilidade.  Com uma única réplica, que deve esperar periódico tempo de inatividade devido a ser reiniciado de servidor depois de atualizações de software ou para outros eventos de manutenção que irão ocorrer.  Como resultado, é importante que considere se a sua aplicação requer elevada disponibilidade de pesquisas (consultas) bem como escritas (eventos de indexação).  Pesquisa Azure oferece as opções de SLA em todas as ofertas de pesquisa paga com os seguintes atributos:

- 2 réplicas para elevada disponibilidade de só de leitura das cargas de trabalho (consultas)
- 3 ou mais réplicas para elevada disponibilidade de leitura / escrita cargas de trabalho (consultas e indexação)

Para obter mais detalhes sobre este, visite o [Contrato de nível de serviço de pesquisa Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Uma vez que réplicas são cópias dos seus dados, ter várias réplicas permite Azure pesquisa a um computador for reiniciado e manutenção contra uma réplica uma vez permitindo consultas continuar a ser executada em relação as outras réplicas ao mesmo tempo.  Por que razão, também terá da ter em consideração como este tempo de inatividade pode ter um impacto as consultas que agora tem de ser executada uma menos cópia dos dados.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Dimensionamento das cargas de trabalho distribuído geo e fornecer geo redundância

Para distribuído geo cargas de trabalho, irá encontrar que utilizadores localizados longe de ser o Centro de dados onde o seu serviço de pesquisa do Azure está alojado terão taxas de latência mais elevadas.  Por este motivo, muitas vezes é importante ter vários serviços de pesquisa no regiões que estão no maior proximidade estes utilizadores.  Pesquisa Azure não atualmente fornecem um método automatizado de replicadas geo índices de pesquisa do Azure entre as regiões, mas existem algumas técnicas que podem ser utilizadas que podem tornar este processo simples implementar e gerir. Estas são descritas as secções seguintes alguns.

O objetivo de um conjunto distribuído geo dos serviços de pesquisa é tem dois ou mais índices disponíveis em duas ou mais regiões onde um utilizador será encaminhado para o serviço de pesquisa do Azure que fornece a latência mais baixa conforme visto neste exemplo:

   ![Publicação em-separador dos serviços por região][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Manter os dados sincronizados em vários serviços de pesquisa do Azure

Existem duas opções para manter os seus serviços de procura distribuída sincronizados constituídos por um dos utilizando o [Azure pesquisa indexador](search-indexer-overview.md) ou a API emissão (também conhecida como [Azure pesquisa REST API](https://msdn.microsoft.com/library/dn798935.aspx)).  

### <a name="azure-search-indexers"></a>Pesquisa Azure indexadores

Se estiver a utilizar a pesquisa do Azure indexador, já está a importar as alterações de dados a partir de um arquivo de dados central como DB de SQL Azure ou DocumentDB. Quando cria uma nova pesquisa serviço, pode simplesmente também criar uma nova indexador de procura do Azure desse serviço que aponta para este mesmo arquivo de dados. Desta forma, sempre que novas alterações entram no arquivo de dados, serão, em seguida, indexados pelos vários indexadores.  

Eis um exemplo dessa arquitectura aspeto.

   ![Única origem de dados com indexador distribuído e combinações de serviço][2]


### <a name="push-api"></a>API de emissão 
Se estiver a utilizar a API de emissão de pesquisa para Azure para [actualizar o conteúdo no índice de pesquisa do Azure](https://msdn.microsoft.com/library/dn798930.aspx), pode manter o seu vários serviços de pesquisa sincronizado por conduza alterações a todos os serviços de pesquisa, sempre que é necessária uma atualização.  Quando este procedimento é importante Certifique-se processar casos em que uma atualização para o serviço de pesquisa de uma falha e atualizações de um ou mais bem sucedida.

## <a name="leveraging-azure-traffic-manager"></a>Tirar partido da Gestor de tráfego Azure

[Gestor de tráfego de Azure](../traffic-manager/traffic-manager-overview.md) permite-lhe para encaminhar os pedidos para vários localizado geo Web sites que, em seguida, são cópias por vários serviços de pesquisa do Azure.  Uma vantagem do Gestor de tráfego é-pode a sonda Azure a pesquisa para se certificar de que está disponível e encaminha os utilizadores a serviços de pesquisa alternativo trabalho tempo de inatividade.  Além disso, se o encaminhamento de pedidos de pesquisa de Web Sites através do Azure, Gestor de tráfego Azure permite-lhe carregar saldo casos em que o Web site para cima, mas não Azure pesquisa.  Eis um exemplo de que a arquitetura tira partido de tráfego gestor.

   ![Publicação em-separador dos serviços por região, com o Gestor de tráfego central][3]

## <a name="monitoring-performance"></a>Monitorizar o desempenho

Pesquisa Azure oferece a capacidade de analisar e monitorizar o desempenho do seu serviço através de [Análise de tráfego de pesquisa (STA)](search-traffic-analytics.md). Através do IRRE, pode, opcionalmente, iniciar sessão operações de procura individuais, bem como métricas agregadas uma conta de armazenamento do Windows Azure que, em seguida, pode ser processada para uma análise ou visualizar os no Power BI.  Utilizar métricas IRRE, pode rever as estatísticas de desempenho como o número médio de consultas ou tempos de resposta de consulta.  Além disso, o registo de operação permite-lhe pormenorizar os detalhes de operações de procura específicos.

STA é uma ferramenta útil para compreender as taxas de latência esse perspetiva de pesquisa do Azure.  Uma vez que as métricas de desempenho de consulta com sessão iniciadas sejam baseiam o tempo que demora de uma consulta a ser processado totalmente na pesquisa Azure (a partir do momento em que é solicitada para quando for enviada), que conseguem Utilize esta opção para determinar se os problemas de latência estão a partir do lado do serviço de pesquisa do Azure ou fora do serviço, tais como a partir de latência da rede.  

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre os limites de serviços e camadas comparar para cada um deles, consulte o artigo [limites de serviço do Azure pesquisa](search-limits-quotas-capacity.md).

Visite o [Planeamento da capacidade](search-capacity-planning.md) para saber mais sobre as combinações de partição e réplica.

Para mais análise no desempenho e para ver algumas ao vivo da como implementar o otimizações outros fabricantes referidas neste artigo, veja o vídeo seguinte:

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png