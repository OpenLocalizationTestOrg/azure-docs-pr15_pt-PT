<properties 
    pageTitle="Casos de utilização comum DocumentDB | Microsoft Azure" 
    description="Saiba mais sobre o início cinco casos de utilização para DocumentDB: gerado pelo utilizador conteúdo, registo de eventos, dados do catálogo, dados de preferências de utilizador e Internet coisas (IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="hawong"/>

# <a name="common-documentdb-use-cases"></a>Casos de utilização de DocumentDB comuns
Este artigo fornece uma descrição geral de vários casos de utilização comum para DocumentDB.  As recomendações neste artigo age como ponto de partida à medida que desenvolve a sua aplicação com DocumentDB.   

Depois de ler este artigo, poderá atender as seguintes questões: 
 
- Quais são os casos de utilização comum para DocumentDB?
- Quais são as vantagens da utilização DocumentDB como um utilizador gerada arquivo de conteúdo?
- Quais são as vantagens da utilização DocumentDB como de dados de catálogo armazena?
- Quais são as vantagens da utilização DocumentDB como um registo de eventos armazena?
- Quais são as vantagens da utilização DocumentDB como de dados de preferências de utilizador armazena?
- Quais são as vantagens de utilizar DocumentDB como um arquivo de dados para sistemas de Internet coisas (IoT)?

## <a name="common-use-cases-for-documentdb"></a>Comuns casos de utilização para DocumentDB
Azure DocumentDB é uma finalidade gerais NoSQL base de dados é utilizada numa vasta gama de aplicações e casos de utilização. É uma boa escolha para qualquer aplicação que necessita de tempos de baixa ordem de milissegundos resposta e dimensionar rapidamente. Seguem-se alguns atributos do DocumentDB que a tornam bem adequado para aplicações de alto desempenho.

- DocumentDB partições vierem os seus dados para elevada disponibilidade e escalabilidade.
- DocumentDB tem SSD cópia armazenamento com os tempos de baixa latência ordem de milissegundos resposta.
- Suporte do DocumentDB para níveis de consistência como eventual, sessão e staleness delimitada permite para baixo para custo desempenho-relação. 
- DocumentDB tem um modelo de preços do dados relevantes flexível que contadores armazenamento e débito de forma independente.
- Modelo de débito reservadas do DocumentDB permite-lhe a pensar em termos de número de lê/escritas em vez de memória/CPU/IOPs do hardware subjacente.
- Estrutura permite-lhe do DocumentDB que dimensionar a volumes de grandes pedido pela ordem milhares de pedidos de por dia.

Estes atributos são particularmente úteis quando chegar Web, móvel, jogos e aplicações de IoT que precisa de tempos de resposta baixa e precisa de processar grandes quantidades de operações de leitura e escrita. 

## <a name="user-generated-content"></a>Conteúdo de utilizador gerado
Um caso de utilização comum para DocumentDB é para armazenar e gerado pelo utilizador de consulta conteúdo (UGC) para a web e aplicações móveis, aplicações de redes sociais, sobretudo.  Alguns exemplos de UGC são sessões de conversação, tweets, mensagens no blogue, classificações e comentários.  Muitas vezes, UGC nas aplicações de redes sociais é uma mistura de texto de forma livre, propriedades, etiquetas e relações que não são delimitadas pela estrutura rígida.   

O conteúdo tal como chats, comentários e mensagens podem ser armazenados numa DocumentDB sem necessidade de transformações ou complexo objeto a camadas de mapeamento relacional.  Propriedades de dados podem ser adicionadas ou modificadas facilmente para que correspondam aos requisitos, tal como os programadores iteramos sobre o código da aplicação, assim promover desenvolvimento rápido.  

Para alterar os esquemas destes redes as aplicações que se integram com várias redes sociais tem responder.  Como dados são indexados automaticamente por predefinição no DocumentDB, dados estão prontos para ser consultados em qualquer altura.  Por conseguinte, estas aplicações tem a flexibilidade de poder obter projecções de acordo com as suas necessidades respetivas.       

Muitos das aplicações sociais no escala global e podem apresentam padrões de utilização inesperados.  Flexibilidade na dimensionamento o arquivo de dados é essencial tal como a camada de aplicação escalas para corresponder ao pedido de utilização.  É possível dimensionar saída ao adicionar a partições dados adicionais sob uma conta de DocumentDB.  Além disso, também pode criar contas DocumentDB adicionais entre várias regiões. Para DocumentDB disponibilidade de região do serviço, consulte o artigo [Azure regiões](https://azure.microsoft.com/regions/#services).   

## <a name="catalog-data"></a>Catálogo de dados
Cenários de utilização de dados do catálogo envolvam armazenar e consultar um conjunto de atributos de entidades como as pessoas, locais e produtos.  Alguns exemplos de dados do catálogo são contas de utilizador, catálogos, registos de dispositivo para IoT e fatura dos sistemas de materiais.  Atributos destes dados podem variar e podem alterar ao longo do tempo para a ajustar aos requisitos da aplicação.  

Considere um exemplo de um catálogo de produtos para um fornecedor de partes para. Cada parte poderá ter os suas próprias atributos além dos atributos comuns que todas as peças partilham.  Além disso, atributos de uma parte específica podem alterar o ano seguinte quando é disponibilizado um novo modelo.  Como um arquivo de documentos JSON, DocumentDB suporta flexíveis esquemas e permite-lhe representar dados com as propriedades do aninhadas e, por conseguinte, é adequado para armazenar dados de catálogo de produtos.       

## <a name="logging-and-time-series-data"></a>Registo e dados de séries de tempo
Registo de aplicação for emitido frequentemente em grandes volumes e poderão ter atributos variados com base na versão da aplicação implementado ou os eventos de registo de componente.  Os dados do registo não estão limitados pelo relações complexas ou estruturas rígidas. Cada vez, os dados do registo são mantidos no formato JSON desde que esteja JSON lightweight e mais facilmente Homem a leitura.
   
Normalmente, existem dois casos de utilização principais relacionados com dados de registo de eventos.  É o primeiro caso de utilização para realizar consultas ad-hoc sobre um subconjunto de dados para resolução de problemas.  Durante a resolução de problemas, um subconjunto de dados é pela primeira vez obtido a partir de registos, normalmente por séries de tempo.  Em seguida, é executada uma desagregação filtrando o conjunto de dados com níveis de erro ou mensagens de erro. Este é onde armazenar registos dos eventos DocumentDB é vantajoso. Os dados do registo armazenados no DocumentDB são indexados automaticamente por predefinição e, por conseguinte, está pronto para ser consultados em qualquer altura. Além disso, os dados do registo podem ser persistentes ao longo a partições de dados como uma série de tempo. Registos mais antigos podem ser lançados fria armazenamento pela política de retenção.          

O segundo caso de utilização envolve as tarefas de análise de dados offline executadas através de um grande volume de dados do registo de execução longa.  Neste caso utilize exemplos de análise de disponibilidade do servidor, análise de erro de aplicação e clickstream análise de dados.  Normalmente, Hadoop é utilizado para executar estes tipos de análises.  Com o conector de Hadoop para DocumentDB, DocumentDB bases de dados funcionarem como origens de dados e sumidouros para tarefas porco, ramo e mapa/reduzir. Para obter detalhes sobre o conector de Hadoop para DocumentDB, consulte o artigo [executar uma tarefa Hadoop com DocumentDB e HDInsight](documentdb-run-hadoop-with-hdinsight.md).      

## <a name="gaming"></a>Jogos
A camada de base de dados é um componente crucial das aplicações de jogos. Jogos modernos executar o processamento de gráfico em clientes do consola/mobile, mas dependem na nuvem para fornecer conteúdo personalizado e personalizado como no jogo estatística, integração de redes sociais e classificações de pontuação alta. Jogos exigem latências extremamente baixos para lê e escritas para fornecer uma envolventes no jogo experiência e a camada de base de dados processar os altos e baixos taxas de pedido de durante a novos lançamentos jogos e as atualizações de funcionalidades.

DocumentDB é utilizado pelo jogos grandes escala como [o andar inactivo: terreno sem homem](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) por [Jogos seguinte](http://www.nextgames.com/), e [Halo 5: encarregados de educação](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Em ambas as casos de utilização, as vantagens principais do DocumentDB foram o seguinte procedimento:

- DocumentDB permite que o desempenho da ser dimensionados para cima ou para baixo elastically. Esta opção permite-jogos processar atualizar perfil e coloca as estatísticas de dezenas milhões dos jogadores simultâneos ao efetuar uma chamada de API única.
- DocumentDB suporta milissegundos lê e escreve para ajudar a evitar quaisquer intervalos durante o jogo.
- DocumentDB a indexação automática permite para filtrar contra várias propriedades diferentes em tempo real, por exemplo, localize leitores por sua player interno IDs, ou os respetivos GameCenter, Facebook, Google IDs ou consulta baseada numa associação de leitor numa associação. Isto é possível sem construir indexação complexa ou infraestrutura sharding.
- Funcionalidades sociais incluindo mensagens de chat no jogo, associações a associação player, desafios concluídos, pontuação alta classificações e sociais gráficos estão mais fácil de implementar com um esquema flexível.
- DocumentDB como um geridos plataforma-como-a-service (PaaS) necessários gestão e configuração mínima funcionam para permitir a iteração rápida e reduzir o tempo de mercado.


## <a name="user-preferences-data"></a>Dados de preferências de utilizador
Hoje em dia, web mais moderno e aplicações móveis vêm com vistas complexas e experiências. Estas vistas e experiências são normalmente dinâmicas, restauração preferências de utilizador ou Estados de espírito e imagem corporativa suas necessidades.  Por conseguinte, aplicações precisam de ser conseguir obter definições personalizadas de forma eficaz para compor elementos da IU e experiências rapidamente. 

JSON é um formato eficaz para representar a dados de esquema da IU, não só é lightweight, mas também pode ser facilmente interpretada por JavaScript.  DocumentDB oferece níveis de consistência ajustável que permitem lê rápida com baixa latência escritas. Por conseguinte, o armazenamento de dados de esquema de IU incluindo definições personalizadas como documentos JSON em DocumentDB constitui um meio eficaz para obter estes dados ao longo o fio.

## <a name="iot-and-device-sensor-data"></a>Dados de sensor IoT e do dispositivo
Casos de utilização IoT frequentemente partilham algumas padrões em como eles ingerir esta última, processam e armazenam dados.  Em primeiro lugar, estes sistemas permitem por incorporação de dados que pode ingerir esta última rajada de dados a partir de sensores dispositivo dos vários idiomas.  Em seguida, estes sistemas processam e analisar dados em tempo real para obter informações em tempo real. E última mas não menos importante, a maior parte se não estiverem todos os dados são eventualmente serão direcionado num arquivo de dados para análise de ad hoc offline e consultar.    

Microsoft Azure oferece casos de utilização de serviços formatados que podem ser utilizados para IoT.  Serviços de IoT Azure são um conjunto de serviços, incluindo Azure evento concentradores, Azure DocumentDB, Azure da cadeia Analytics, concentrador de notificação do Azure, formação de máquina Azure, Azure HDInsight e obter. 

Pode ser penetração rajada de dados ao Azure evento concentradores à medida que este oferece ingestão de dados de débito alta com a latência baixa. Dados penetração que precisa de ser processada por conhecimentos aprofundados de tempo real podem ser canalizados para Azure da cadeia Analytics para análise de tempo real. Dados podem ser carregados num DocumentDB para consultar ad hoc. Assim que os dados são carregados para DocumentDB, os dados estão prontos para ser consultados.  Os dados no DocumentDB podem ser utilizados como dados de referência como parte de análise de tempo real. Além disso, dados podem ainda ser refinados e processados pelos ligar dados DocumentDB a HDInsight para tarefas porco, ramo ou mapa/reduzir.  Refinado dados são carregados, em seguida, voltar a DocumentDB para elaboração de relatórios.   

Para obter uma solução de IoT de exemplo com DocumentDB, EventHubs e tempestade, consulte o artigo o [repositório de exemplos de tempestade hdinsight no GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para mais informações sobre ofertas Azure para IoT, consulte o artigo [Criar Internet do seu coisas](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx).

## <a name="next-steps"></a>Próximos passos
 
Para começar com DocumentDB, pode criar uma [conta](https://azure.microsoft.com/pricing/free-trial/) e, em seguida, siga os nossos [caminho de formação](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obter informações sobre DocumentDB e localizar as informações que necessárias. 

Ou, se gostaria de ler mais sobre os clientes que utilizam DocumentDB, estão disponíveis as seguintes histórias de cliente:

- [Jogos seguintes](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Inativas suas: sem homem Popular terreno jogo soars # 1 suportados pelo Azure DocumentDB.
- [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Como Halo 5 implementada jogo social utilizando Azure DocumentDB.
- [Galeria de análise de Cortana](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galeria de análise de Cortana - um site de Comunidade dimensionáveis criada com base em Azure DocumentDB.
- [A opção](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). À esquerda integrador dá conhecimentos aprofundados Global de empresas multinacionais em minutos com tecnologias de nuvem flexíveis.
- [República de notícias](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Adicionar informações da empresa para obter as notícias para fornecer informações com objetivo para cidadãos ocupados. 
- [SGS internacional](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Cores consistentes em todo o mundo para marcas principais ativar para SGS. E SGS transforma-se Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Caráter de preenchimento global Telenor utiliza na nuvem para deslocar-se com a velocidade de um arranque. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Na loja do futuro é executado pesquisa rápida e fácil fluxo de dados.
