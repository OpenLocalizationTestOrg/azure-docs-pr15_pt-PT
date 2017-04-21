#<a name="data-management-and-business-analytics"></a>Gestão de dados e análises de empresas

Gerir e analisar dados na nuvem são tão importante tal como está em qualquer outro local. Para permitir que efetuar o seguinte, o Azure fornece um intervalo de tecnologias para trabalhar com dados não relacionais e. Este artigo apresenta cada uma das opções. 

##<a name="table-of-contents"></a>Índice      

- [Armazenamento de BLOBs](#blob)
- [Executar um DBMS numa máquina Virtual](#dbinvm)
- [Base de dados SQL](#sqldb)
    - [Sincronização de dados SQL](#datasync)
    - [Relatórios de dados de SQL utilizando máquinas virtuais](#datarpt)
- [Armazenamento de tabela](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Armazenamento de BLOBs

A palavra "blob" é abreviatura para "grandes objecto binário" e descreve exatamente o que um blob é: um conjunto de informações binários. Ainda apesar de se simples, blobs são bastante úteis. [Figura 1](#Fig1) ilustra os princípios básicos de armazenamento de Blobs do Azure.

<a name="Fig1"></a>![Diagrama de Blobs][blobs]
 
**Figura 1: O armazenamento de Blobs do Azure armazena dados binários - blobs - em contentores.**

Para utilizar blobs, crie primeiro uma *conta de armazenamento*do Azure. Como parte desta, especifique o Centro de dados Azure que irá guardar os objetos que cria utilizando esta conta. Onde encontra-se, cada blob que criar pertence a algumas contentor na sua conta de armazenamento. Para aceder a um blob, uma aplicação fornece um URL com o formulário:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*contentor*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount* &gt; é um identificador exclusivo atribuído quando é criada uma nova conta de armazenamento, enquanto &lt; *contentor* &gt; e &lt; *BlobName* &gt; são os nomes de um contentor específico e um blob dentro desse contentor. 

Azure fornece dois tipos diferentes de blobs. As opções são:

- *Bloquear* blobs, cada uma das quais pode conter até 200 gigabytes de dados. Como o nome sugere, um blob bloco é dividido algumas número de blocos. Se ocorre uma falha ao transferir um blob bloco, retransmissão pode currículo com o bloco mais recente em vez de enviar o blob inteiro novamente. Bloco blobs são uma abordagem bastante geral para o armazenamento e, se o tipo de BLOBs utilizadas com mais frequência hoje.

- *Página* blobs, que podem ser tão grandes num terabyte. Página blobs foram concebidos para acesso aleatório e, por isso, cada um deles estiver dividido em algumas número de páginas. Uma aplicação é gratuita ler e escrever aleatoriamente páginas individuais no blob. No Azure máquinas virtuais por exemplo, VMs criar utilizam blobs de página como armazenamento persistente para o sistema operativo discos e dados.

Se optar por bloquear blobs ou blobs de página, aplicações podem aceder blob dados de várias formas diferentes. As opções incluem o seguinte:

- Diretamente através de um RESTful (por exemplo, com base em HTTP) aceder protocolo. As aplicações do Azure e aplicações externas, incluindo aplicações em execução no local, podem utilizar esta opção.
- Utilizar a biblioteca do cliente de armazenamento do Azure, que fornece uma interface de amigável de programador mais na parte superior o protocolo de acesso não processado blob RESTful. Mais uma vez, aplicações Azure e aplicações externas podem aceder a blobs utilizando esta biblioteca.
- Utilizar unidades Azure, uma opção que permite uma aplicação do Azure tratar blob uma página como uma unidade local com um sistema de ficheiros NTFS. A aplicação, o blob página aspeto de um sistema de ficheiros Windows normal aceder às mesmas através de e/s de ficheiro padrão. Na verdade, lê e escreve são enviadas para o blob de página subjacente que implementa a unidade de Azure. 

Para proteger-se contra falhas de hardware e melhorar a disponibilidade, cada blob é replicada nos três computadores num centro de dados Azure. Escrever um blob atualiza três cópias, para que lê posterior não ver resultados inconsistentes. Também pode especificar que os dados de um BLOBs devem ser copiados para outra Centro de dados Azure na mesma geo mas, pelo menos, 500 milhas ausente. A copiar, denominado *geo replicação*, acontece dentro de alguns minutos de uma atualização para o blob e é útil para recuperação de falhas.

Dados blobs também podem ser disponibilizados através da Azure *Rede de entrega de conteúdos (CDN)*. Por colocação em cache cópias dos dados de BLOBs a dezenas de servidores em todo o mundo, a CDN pode acelerar o acesso às informações de que são acedidas repetidamente. 

Simples como estão, blobs são a opção acertada em muitas situações. Armazenar e transmissão de vídeo e áudio são exemplos óbvios, as cópias de segurança e outros tipos de arquivo de dados. Os programadores também podem utilizar blobs para qualquer tipo de dados não estruturados que pretenderem colocar em espera. Pode ser útil surpreendentemente ter uma forma simples para armazenar e aceder aos dados binários.


## <a name="dbinvm"></a>Executar um DBMS numa máquina Virtual

Muitas aplicações dependem hoje qualquer tipo de sistema de gestão de base de dados (DBMS). Os sistemas relacionais como SQL Server estão o mais frequentemente utilizado escolha, mas não relacionais abordagens, geralmente denominadas *NoSQL* tecnologias, obtenha mais populares diariamente. Para permitir que as aplicações de nuvem Utilize estas opções de gestão de dados, o máquinas virtuais do Azure permite-lhe executar um DBMS (relacional ou NoSQL) numa VM. [Figura 2](#Fig2) mostra este aspeto com o SQL Server.

<a name="Fig2"></a>![Diagrama de SQL Server em máquinas uma Máquina Virtual][SQLSvr-vm]
 
**Figura 2: Azure máquinas virtuais permite a execução de um DBMS numa VM com persistente fornecida pela blobs.**

Para os programadores e administradores de base de dados, este cenário aspeto muito a executar o mesmo software na sua própria Centro de dados. No exemplo mostrado aqui, por exemplo, podem ser utilizadas quase todas as funcionalidades do SQL Server e tiver total acesso administrativo ao sistema. Também tem a responsabilidade de gestão do servidor de base de dados, claro, tal como se estivesse em execução localmente.

Como [figura 2](#Fig2) mostra, as bases de dados são apresentados a ser armazenado no disco local da VM o servidor é executado no. Em folhas de rosto, no entanto, cada um desses discos é escrita um BLOBs do Azure. (É semelhante à utilização de uma SAN no seu próprio Centro de dados, com um blob muito serve como um LUN.) À medida com qualquer BLOBs do Azure, os dados que contém são replicados três vezes dentro de um centro de dados e, se o pedido, o geo replicadas para outro centro de dados na mesma região. Também é possível utilizar as opções como base de dados do SQL Server espelhando as para Fiabilidade melhorada. 

Outra forma de utilizar o SQL Server uma VM é criar uma aplicação híbrida, onde os dados encontra-se no Azure enquanto lógica da aplicação é executada no local. Por exemplo, este poderá fazer sentido quando aplicações em execução em várias localizações ou em vários dispositivos móveis têm de partilhar os mesmos dados. Para tornar a comunicação entre a nuvem da base de dados e no local lógica mais simples, uma organização pode utilizar rede Virtual Azure para criar uma ligação de rede privada virtual (VPN) entre um centro de dados do Azure e os suas próprias Centro de dados no local.


## <a name="sqldb"></a>Base de dados SQL

Para muitas pessoas, a execução de um DBMS numa VM é a primeira opção que chegar ao Lembre-se para a gestão de dados estruturados na nuvem. Ela é não a escolha apenas, no entanto, nem é sempre a melhor escolha. Em alguns casos, a gestão de dados utilizando uma plataforma como uma abordagem de serviço (PaaS) torna mais sentido. Azure fornece uma tecnologia de PaaS denominada base de dados SQL permite-lhe fazê-lo para dados relacionais. [Figura 3](#Fig3) ilustra esta opção. 

<a name="Fig3"></a>![Diagrama de base de dados SQL][SQL-db]
 
**Figura 3: Base de dados SQL fornece um serviço de armazenamento relacionais PaaS partilhado.**

Base de dados SQL não dá a cada cliente as suas próprias física instância do SQL Server. Em vez disso, fornece um serviço de inquilino com várias, com um servidor de base de dados SQL lógico de cada cliente. Todos os clientes partilham a capacidade de armazenamento e cluster que fornece o serviço. E tal como acontece com armazenamento de Blobs do, todos os dados numa base de dados SQL está armazenado no três computadores separados dentro de um centro de dados Azure, que lhe dá uma as bases de dados incorporada elevada disponibilidade (HA).

Para uma aplicação, base de dados SQL aspeto muito do SQL Server. Aplicações podem emitir consultas SQL contra tabelas relacionais, utilize procedimentos armazenados T SQL e executar operações em várias tabelas. E porque aplicações aceder a base de dados SQL utilizando o protocolo de fluxo de dados de tabela (TDS), o mesmo protocolo utilizado para aceder ao SQL Server, pode trabalhar com os dados utilizando a arquitetura de entidade, ADO.NET, JDBC e outras interfaces de acesso de dados familiares. 

Mas uma vez que a base de dados SQL é um serviço de nuvem a ser executada em centros de dados Azure, não precisa de gerir qualquer um dos aspetos física do sistema, tal como a utilização do disco. Também não precisa de se preocupar atualização do software ou outras tarefas administrativas feixe de processamento. Cada organização do cliente ainda controla as suas próprias bases de dados, claro, incluindo os seus esquemas e inícios de sessão do utilizador, mas muitas das tarefas administrativas mundano são feitas por si. 

Enquanto a base de dados SQL aspeto muito do SQL Server para aplicações,-não comportam-se exatamente o mesmo que um DBMS em execução num física ou máquina virtual. Porque é executada no hardware partilhada, o seu desempenho variam com a carga colocada nesse hardware por todos os dos seus clientes. Isto significa que o desempenho do, diga um procedimento armazenado na base de dados SQL pode variar a partir de um dia para o próximo. 

Hoje em dia, base de dados SQL permite-lhe criar uma base de dados mantendo até 150 gigabytes. Se necessitar de trabalhar com bases de dados maiores, o serviço fornece uma opção denominada *Federação*. Para fazer isto, um administrador de base de dados cria duas ou mais *Membros de Federação*, cada um dos quais é uma base de dados separado com o seu próprio esquema. Dados são distribuídos ao longo estes membros, algo que é, muitas vezes, *sharding*, com cada membro atribuído uma *chave de Federação*de exclusiva. Um consultas SQL de problemas de aplicação contra estes dados ao especificar a chave de federação que identifica o membro Federação a consulta devem de destino. Esta opção permite-utilizando uma abordagem de relacional tradicional com grandes quantidades de dados. Como sempre, existem compromissos; nem consultas nem transações podem ocupar membros de Federação, por exemplo. Mas quando um serviço de PaaS relacional é a melhor escolha e estes compromissos são aceites, utilizando a Federação SQL pode ser uma boa solução.

Base de dados SQL pode ser utilizado por aplicações em execução no Azure ou noutro local, tal como no seu centro de dados no local. Isto torna útil para aplicações de nuvem que precisam de dados relacionais, bem como as aplicações que pode beneficiar de armazenar dados na nuvem no local. Uma aplicação móvel poderão depender de base de dados do SQL para gerir partilhados dados relacionais, por exemplo, como poderá ser uma aplicação do inventário que é executada em vários distribuidores em todo o mundo.

Pensar em base de dados SQL eleva um problema óbvio (e importante): quando deve executar do SQL Server numa VM e quando é uma boa opção de base de dados SQL? Como habitualmente, existem compromissos e, por isso, qual das abordagens é melhor depende da seus requisitos. 

Uma forma simple a ter em conta sobre a mesma é ver a base de dados SQL como sendo para as novas aplicações, enquanto o SQL Server em máquinas uma VM é uma boa opção quando está a mover um existente no local aplicação para a nuvem. Também pode ser útil ver esta decisão de uma forma mais detalhada, no entanto. Por exemplo, a base de dados SQL é mais fácil de utilizar, uma vez que existe mínimas configuração e administração. Mas com o SQL Server numa VM pode ter mais previsível desempenho - não é um serviço partilhado - e -lo também suporta não federado bases de dados maiores que a base de dados SQL. Ainda assim, base de dados SQL fornece a replicação incorporada de dados e processamento, eficazmente que lhe dá um DBMS alta disponibilidade com o trabalho muito pequeno. Enquanto o SQL Server fornece-lhe mais controlo e um pouco mais amplo conjunto de opções, base de dados SQL é mais simples definir cópias de segurança e significativamente menos trabalho para gerir.

Por fim, é importante destacar que base de dados SQL não se encontra o serviço de dados de PaaS apenas disponível no Azure. Parceiros da Microsoft fornecem também outras opções. Por exemplo, ClearDB oferece uma MySQL PaaS perguntar se, enquanto Cloudant vende uma opção de NoSQL. Serviços de dados PaaS são a solução direita em muitos casos e, por isso, esta abordagem para gestão de dados é uma parte importante do Azure.


### <a name="datasync"></a>Sincronização de dados SQL

Enquanto a base de dados SQL manter três cópias de cada base de dados dentro de um centro de dados do Azure única,-não automaticamente criar uma réplica dados entre o Azure centros de dados. Em vez disso, fornece sincronização de dados SQL, um serviço que pode utilizar para o fazer. [Figura 4](#Fig4) mostra este aspeto.

<a name="Fig4"></a>![Diagrama de sincronização de dados SQL][SQL-datasync]
 
**Figura 4: Sincronizar de dados do SQL sincroniza os dados na base de dados SQL com dados em outras Azure e no local centros de dados.**

Como o diagrama mostra, a sincronização de dados do SQL pode sincronizar dados em diferentes localizações. Suponha que está a executar uma aplicação no centros de dados Azure múltiplos, por exemplo, com dados armazenados numa base de dados SQL. Pode utilizar a sincronização de dados do SQL para manter os dados sincronizados. Sincronização de dados do SQL também pode sincronizar dados entre um centro de dados do Azure e uma instância do SQL Server em execução num centro de dados no local. Poderá ser útil para manter uma cópia local da dados utilizados por aplicações no local e uma cópia na nuvem utilizado por aplicações em execução no Azure. E, apesar de não estiver apresentado na figura, a sincronização de dados do SQL podem também ser utilizada para sincronizar dados entre a base de dados SQL e o SQL Server em execução numa VM no Azure ou em outro local.

A sincronização pode ser bidirecional e determinar exatamente que dados são sincronizados e com que frequência é feito. (Sincronização entre bases de dados não é atómica; no entanto - há sempre pelo menos algumas atraso.) E no entanto, é utilizado, configurar a sincronização com a sincronização de dados do SQL é completamente configuração orientadas; não existe nenhum código para escrever.


### <a name="datarpt"></a>Relatórios de dados de SQL utilizando máquinas virtuais

Assim que uma base de dados contiver dados, alguém irá provavelmente pretende criar relatórios com esses dados. Azure pode executar SQL Server Reporting Services (SSRS) no Azure máquinas virtuais, que equivale funcional para executar o SQL Server Reporting Services no local. Em seguida, pode utilizar SSRS para executar relatórios sobre a dados armazenados numa base de dados SQL Azure.  [Figura 5](#Fig5) mostra como funciona o processo.

<a name="Fig5"></a>![Diagrama de elaboração de relatórios do SQL][SQL-report]
 
**Figura 5: SQL Server Reporting Services em execução num máquinas virtuais do Azure fornece serviços de elaboração de relatórios para os dados na base de dados SQL. .**

Antes de um utilizador pode ver um relatório, alguém define esse relatório deve aspeto (passo 1). Com SSRS numa VM, pode fazê-lo com qualquer uma das duas ferramentas: ferramentas de dados do SQL Server, parte do SQL Server 2012 ou predecessora, Studio de desenvolvimento de Business Intelligence (BI). Tal como acontece com SSRS, estas definições de relatório são representadas no relatório RDL (Definition Language). Depois de tem sido criados os ficheiros RDL para um relatório, estes são carregados para uma VM na nuvem (passo 2). A definição de relatório está agora pronta para utilizar.

Em seguida, um utilizador da aplicação acede o relatório (passo 3). A aplicação transmite este pedido para a VM de SSRS (passo 4), base de dados SQL ou outras origens de dados para obter os dados que necessita de contactos (passo 5). SSRS utiliza estes dados e os ficheiros RDL relevantes para compor o relatório (passo 6), em seguida, devolve o relatório para a aplicação (passo 7), que apresenta-o para o utilizador (passo 8).

Incorporar um relatório numa aplicação, o cenário aqui apresentado, não está a única opção. Também é possível ver relatórios no Gestor de relatórios no VM, SharePoint na VM ou de outras formas. Relatórios podem também ser combinados com um relatório que contém uma ligação para outra.

SSRS numa VM Azure dá-lhe todas as funcionalidades como uma solução de elaboração de relatórios na nuvem. Relatórios podem utilizar uma origem de dados suportada pelo SSRS. Aplicações e relatórios podem incluir código incorporado ou conjuntos para suportar comportamentos personalizados. Execução do relatório e composição são rapidamente uma vez que o conteúdo de servidor do relatório e o motor executadas em conjunto no mesmo servidor virtual.



## <a name="tblstor"></a>Armazenamento de tabela

Dados relacionais são útil em muitos casos, mas não é sempre a escolha correta. Se a aplicação necessitar de acesso rápido e simple para muito grandes quantidades de dados estruturados vagamente, por exemplo, uma base de dados relacional poderá não funcionar corretamente. Uma tecnologia NoSQL é provável que seja a melhor opção.

Armazenamento de tabela do Azure é um exemplo deste tipo de NoSQL abordagem. Apesar do nome, o armazenamento de tabela não suporta tabelas relacionais padrão. Em vez disso, fornece o que é conhecido como um *valor/chave armazenar*, associando um conjunto de dados com uma chave de específica, em seguida, permitindo que o acesso de uma aplicação que os dados ao fornecer a chave. [Figura 6](#Fig6) ilustra informações básicas.

<a name="Fig6"></a>![Diagrama de armazenamento de tabela][SQL-tblstor]
 
**Figura 6: Armazenamento de tabela do Azure é um arquivo de valor/chave que fornece rápida e simple acesso a grandes quantidades de dados.**

Como blobs, cada tabela está associada uma conta de armazenamento Azure. Tabelas são também denominadas muito como blobs, com um URL do formulário

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*tabela*&gt;

Como na figura mostra, cada tabela é dividida em algumas número de partições, cada uma das quais pode ser armazenada num computador em separado. (Isto é um formulário de sharding, tal como acontece com a Federação de SQL.) As aplicações do Azure e aplicações em execução noutro lugar, podem aceder a uma tabela utilizando o protocolo de RESTful OData ou a biblioteca do cliente de armazenamento do Azure.

Cada partição numa tabela mantém algumas número de *entidades*, cada que contenha até 255 *Propriedades*. Todas as propriedades tem um nome, um tipo (tal como binário, booleano, DateTime, Int ou cadeia) e um valor. Ao contrário de armazenamento relacional, estas tabelas tem sem esquema fixa e entidades tão diferentes na mesma tabela podem conter propriedades com tipos diferentes. Uma entidade poderá ter apenas uma propriedade de cadeia que contém um nome, por exemplo, enquanto outra entidade na mesma tabela tem duas Int propriedades que contém um número de ID de cliente e uma classificação de crédito.

Para identificar uma determinada entidade dentro de uma tabela, uma aplicação fornece chave essa entidade. A tecla tem duas partes: uma *chave de partição* que identifica uma partição específica e uma *tecla de linha* que identifica uma entidade dentro desse partição. [Figura 6](#Fig6), por exemplo, o cliente pede a entidade com chave partição respostas e linha 3 e armazenamento de tabela devolve essa entidade, incluindo todas as propriedades contém.

Esta estrutura permite tabelas ser grande - numa única tabela pode conter até 100 terabytes de dados - e -permite que um acesso rápido para os dados que contêm. Também coloca limitações, no entanto. Por exemplo, não existe suporte para atualizações transaccionais que abrangem a partições pares numa única tabela ou tabelas. Um conjunto de atualizações a uma tabela apenas pode ser agrupado numa transação Atómica se todas as entidades envolvidas estiverem na mesma partição. Não existem também nenhuma forma de uma tabela de consulta baseia-se no valor das respetivas propriedades, nem existe suporte para associações entre várias tabelas. E, ao contrário das bases de dados relacionais, tabelas não têm suporte para procedimentos armazenados.

Armazenamento de tabela do Azure é uma boa escolha para aplicações que precisam de acesso rápido e tarifa reduzido para grandes quantidades de dados estruturados vagamente. Por exemplo, uma aplicação da Internet que armazena informações de perfil para vários utilizadores pode utilizar tabelas. Acesso rápido, é importante esta situação e a aplicação provavelmente não necessita de potência completa de SQL. Que lhe dá uma cópia esta funcionalidade para obter a velocidade e o tamanho, por vezes, pode fazer sentido e, por isso, o armazenamento de tabela é apenas a solução direita para alguns problemas.


## <a name="hadoop"></a>Hadoop

Organizações tem sido construir armazém de dados, há décadas. Estas colecções de informação, com mais frequência armazenados em tabelas relacionais, informar as pessoas a trabalhar com e saiba a partir de dados de várias maneiras. Por exemplo, com o SQL Server, é comuns a utilizar ferramentas como SQL Server Analysis Services para o fazer.

Mas, imaginemos que pretende fazer análises de dados que não sejam relacionais. Os seus dados poderão demorar vários formulários: a informação de sensores ou RFID etiquetas, ficheiros de registo nos farms, dados de clickstream produzidos por aplicações web, as imagens a partir de dispositivos de diagnóstico médicos e muito mais. Estes dados também poderão ser demasiado grande para ser utilizado de forma eficaz com um armazém de dados tradicional, na verdade grande. Problemas de dados grande como esta, raros apenas alguns anos atrás, tem agora tornam-se bastante comum.

Para analisar este tipo de dados grandes, os nossos indústria em grande medida convergiu numa única solução: a tecnologia de abrir origem Hadoop. Hadoop é executado um cluster de física ou máquinas virtuais, distribuição de dados funciona ao longo do tempo que essas máquinas e processá-la em paralelo. As mais máquinas Hadoop tem de utilizar, mais rápido pode concluir o trabalho-está a fazer.

Este tipo de problema é uma opção singular para a nuvem pública. Em vez de manutenção apenas quando deles necessita de um VMs exército dos servidores que poderão sentar idle maior parte das vezes, a executar o Hadoop na nuvem permite-lhe criar de no local (e pagamento pelo). Melhor ainda mais os dados grandes que pretende analisar com Hadoop é criada na nuvem, poupando-lhe os problemas de movê-lo. Para ajudar a explorar estes sinergias, a Microsoft fornece um serviço de Hadoop no Azure. [Figura 7](#Fig7) mostra os componentes mais importantes deste serviço.

<a name="Fig7"></a>![Diagrama de hadoop][hadoop]

**Figura 7: Hadoop no Azure executa tarefas de MapReduce dados em paralelo utilizando várias máquinas virtuais do processo.**

Para utilizar Hadoop no Azure, pela primeira vez pedir nesta plataforma na nuvem para criar um cluster de Hadoop, especificando o número de VMs precisar. Configurar um cluster de Hadoop si próprio é uma tarefa que não sejam comum e, por isso, permitindo que Azure fazê-lo faz sentido. Quando tiver terminado utilizando o cluster, encerrá-lo. Não é necessário para pagar a recursos de cluster que não estiver a utilizar.

Uma aplicação de Hadoop, geralmente denominada uma *tarefa*, que utiliza um modelo de programação conhecido como *MapReduce*. Como na figura mostra, a lógica para uma tarefa de MapReduce é executado em simultâneo ao longo VMs muitos. Através do processamento de dados em paralelo, Hadoop pode analisar dados muito mais rapidamente único máquina soluções.

No Azure, os dados de um MapReduce tarefa funciona num é normalmente a par de armazenamento de Blobs. No entanto, no Hadoop, trabalhos MapReduce pretender dados serão armazenados na *Distribuído ficheiro Hadoop sistema (HDFS)*. HDFS é semelhante ao armazenamento Blob em algumas formas; -replica os dados por vários servidores físicos, por exemplo. Em vez de duplicar esta funcionalidade, Hadoop no Azure expõe em vez disso, o armazenamento de BLOBs através da API HDFS, conforme mostrado figura. Enquanto a lógica numa tarefa MapReduce pensa que está a aceder a ficheiros HDFS normais, a tarefa na verdade está a trabalhar com dados transmitido em sequência pela partir do blobs. E para suportar as maiúsculas/minúsculas onde várias tarefas são executadas sobre os mesmos dados, Hadoop no Azure também permitir copiar dados a partir de blobs para HDFS total em execução nas VMs. 

Tarefas de MapReduce frequentemente estão escritas em Java hoje em dia, uma abordagem que suporte Hadoop no Azure. A Microsoft adicionou também suporte para criar tarefas de MapReduce noutros idiomas, incluindo c#, F # e JavaScript. O objetivo é tornar esta tecnologia de grande dados mais facilmente acessíveis para um grupo grande a partir de programadores.

Juntamente com HDFS e MapReduce, Hadoop inclui outras tecnologias de que permitir que as pessoas a analisar dados sem ter de escrever uma tarefa MapReduce próprios. Por exemplo, porco é uma linguagem de alto nível concebida para analisar dados grandes, enquanto ramo oferece uma linguagem de SQL gosto denominada HiveQL. Porco e ramo realmente geram MapReduce tarefas que processam HDFS dados, mas eles ocultar esta complexidade dos seus utilizadores. Ambos são fornecidos com Hadoop no Azure.

A Microsoft fornece também um controlador de HiveQL para o Excel. Utilizar um suplemento do Excel, analistas de negócio podem criar consultas HiveQL (e, por conseguinte, trabalhos MapReduce) diretamente a partir do Excel, em seguida, processar e visualizar os resultados através de PowerPivot e outras ferramentas do Excel. Hadoop no Azure inclui outras tecnologias, assim, tal como a bibliotecas Mahout, o sistema de extração de graph Pegasus e mais de aprendizagem automática.

Análise de dados grande é importante e, por isso, Hadoop é igualmente importante. Ao fornecer Hadoop como um serviço gerido no Azure, juntamente com ligações para ferramentas familiares como o Excel, Microsoft destina-se a efetuar esta tecnologia acessível a um conjunto mais vasto de utilizadores.

Mais amplamente, dados de todos os tipos são importantes. Este é o motivo pelo qual Azure inclui um intervalo de opções para análise de gestão e empresas de dados. Independentemente da sua aplicação que está a tentar criar, é provável que encontrará algo nesta plataforma na nuvem que irá funcionar para si.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png
