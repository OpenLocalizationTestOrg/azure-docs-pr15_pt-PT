<properties 
    pageTitle="Mover dados entre bases de dados de saída dimensionada nuvem | Microsoft Azure" 
    description="Explica como manipular shards e mover dados através de um serviço alojado personalizada utilizando APIs da base de dados flexível." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="moving-data-between-scaled-out-cloud-databases"></a>Mover dados entre bases de dados de saída dimensionada nuvem

Se for um Software de como um programador de serviço e inesperadamente a sua aplicação sofre pedido imenso, é necessário acomodar o crescimento. Para adicionar mais bases de dados (shards). Como é redistribuir os dados para novas bases de dados sem interromper a integridade dos dados? Utilize a **ferramenta de intercalação de divisão** para mover dados a partir de bases de dados restritos para novas bases de dados.  

A ferramenta de divisão em série é executada como um serviço Azure web. Um administrador ou um programador utiliza a ferramenta para mover shardlets (dados a partir de um shard) entre bases de dados diferentes (shards). A ferramenta utiliza gestão de mapa shard para manter a base de dados de metadados do serviço e certifique-se mapeamentos consistentes.

![Descrição geral][1]

## <a name="download"></a>Transferir
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)


## <a name="documentation"></a>Documentação
1. [Tutorial da ferramenta de base de dados flexível dividido em série](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
* [Configuração de segurança de intercalação de divisão](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Considerações de segurança de intercalação de divisão](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Gestão de mapa shard](sql-database-elastic-scale-shard-map-management.md)
* [Migrar bases de dados existentes para fora de escala](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Ferramentas de base de dados flexível](sql-database-elastic-scale-introduction.md)
* [Glossário de ferramentas da base de dados flexível](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Porquê utilizar a ferramenta de divisão em série?

**Flexibilidade**

Precisam de aplicações esticar flexível para além dos limites de uma base de dados do Azure SQL DB único. Utilize a ferramenta para mover dados, conforme necessário para novas bases de dados mantendo a integridade.

**Dividir crescimento** 

Tem de aumentar capacidade global para gerir explosivo crescimento. Para fazê-lo, crie capacidade adicional por sharding os dados e por distribui-lo ao longo incrementada mais bases de dados até que as necessidades da capacidade são preenchidas. Este é um exemplo da funcionalidade 'Dividir'. 

**Impressão em série para encolher**

Capacidade necessita de encolher devido a natureza sazonal de um negócio. A ferramenta de permite-lhe dimensionar para baixo as unidades de escala menos quando empresas diminui. A funcionalidade 'Impressão em série' no serviço de intercalação de divisão de escala flexível abrange este requisito. 

**Gerir pontos movendo shardlets**

Com múltiplos inquilinos por bases de dados, a alocação de shardlets para shards pode conduzir a congestionamentos capacidade no algumas shards. Isto requer voltar a atribuir shardlets ou mover shardlets ocupado para o novos ou menos utilizados shards. 

## <a name="concepts--key-features"></a>Conceitos e funcionalidades-chave

**Serviços de à alojado de cliente**

A intercalação de divisão é entregue como um serviço alojado no cliente. Tem de implementar e o serviço do anfitrião na sua subscrição do Microsoft Azure. O pacote que transferir do NuGet contém um modelo de configuração para concluir com as informações para a sua implementação específica. Consulte o [tutorial dividido em série](sql-database-elastic-scale-configure-deploy-split-and-merge.md) para obter detalhes. Uma vez que o serviço é executado na sua subscrição do Azure, pode controlar e configurar a maior parte dos aspetos de segurança do serviço. O modelo predefinido inclui as opções para configurar SSL, autenticação baseada em certificados cliente, encriptação de credenciais armazenadas, que deve fazer a proteger e as restrições de IP. Pode encontrar mais informações sobre aspectos de segurança no documento que se segue [configuração de segurança dividido em série](sql-database-elastic-scale-split-merge-security-configuration.md).

A predefinição implementado será executado de serviço com um trabalho e uma função de web. Cada utiliza o tamanho da A1 memória-virtual no Azure serviços em nuvem. Enquanto não é possível modificar estas definições quando implementar o pacote, pode alterá-las após uma implementação com êxito no serviço de nuvem em execução, (através do portal do Azure). Tenha em atenção que a função de trabalho não deve ser configurada para mais do que uma única ocorrência, por motivos técnicos. 

**Integração com o mapa shard**

O serviço de intercalação de divisão interage com o mapa shard da aplicação. Quando utilizar o serviço de intercalação de divisão para dividir ou unir intervalos ou para mover shardlets entre shards, o serviço mantém automaticamente o mapa shard atualizados. Para fazê-lo, o serviço se liga à shard mapa Gestor base de dados da aplicação e mantém intervalos e mapeamentos de como o progresso de pedidos de intercalar/dividir/mover. Este procedimento assegura que o mapa shard sempre apresenta uma vista actualizada quando operações dividido em série são o problema?. Impressão em série e shardlet operações de movimento dividir, são implementadas ao mover um lote de shardlets a partir do shard de origem para o shard de destino. Durante a shardlet operação de movimento shardlets sujeito a secção atual são marcadas como offline no mapa do shard e não estão disponível para ligações de encaminhamento dependentes de dados utilizando **OpenConnectionForKey** API. 

**Ligações de shardlet consistentes**

Quando inicia o movimento de dados para um novo lote de shardlets, quaisquer ligações de encaminhamento de dependentes de dados de mapa de shard fornecido ao shard armazenar o shardlet são sacrificadas subsequentes ligações e do mapa de shard APIs estes shardlets estão bloqueados enquanto o movimento de dados está em curso para evitar inconsistências. Ligações para outros shardlets sobre a mesma shard também irá obter mortas, mas vai ter êxito novamente imediato em Repetir. Depois do lote for movido, os shardlets são marcadas online novamente para o shard de destino e a origem de dados é removida o shard de origem. O serviço vai seguir estes passos para cada lote até ter sido movidos shardlets todos os. Isto vai levar a várias operações de kill ligação durante o curso de concluir a operação intercalar/dividir/mover.  

**Gerir shardlet disponibilidade**

Limitar a ligação para o lote atual de shardlets tal como descrito acima para matar restringe o âmbito da indisponibilidade para cada lote de shardlets cada vez. Este é preferido através de uma abordagem de onde a shard completa seria permanecem offline para todos os seus shardlets durante o curso de uma operação de divisão ou em série. O tamanho de um lote, definido como o número de shardlets distintos para mover uma vez, é um parâmetro de configuração. Pode ser definida para cada operação de divisão e intercalar dependendo necessidades de disponibilidade e o desempenho da aplicação. Tenha em atenção que o intervalo que está a ser bloqueado no mapa shard pode ser maior que o tamanho do lote especificado. Isto acontece porque o serviço escolhe o tamanho do intervalo assim que o número de valores de chave sharding nos dados real aproximadamente corresponde ao tamanho lote. Este é importante lembrar-se em particular para chaves sharding distâncias povoada. 

**Armazenamento de metadados**

O serviço de intercalação de divisão utiliza uma base de dados para manter o respetivo estado e manter registos durante o processamento do pedido. O utilizador cria esta base de dados na sua subscrição e fornece a cadeia de ligação para o mesmo no ficheiro de configuração para a implementação de serviço. Os administradores de organização do utilizador também podem ligar a esta base de dados para rever o progresso do pedido e para investigar informações detalhadas acerca potenciais falhas.

**Sharding detecção**

O serviço de intercalação de divisão diferencia entre (1) sharded tabelas, tabelas de referência (2) e tabelas (3) normais. A semântica de uma operação intercalar/dividir/mover variam consoante o tipo de tabela utilizada e é definida da seguinte forma: 

* **Tabelas sharded**: dividido, impressão em série e operações de mudança de mover shardlets de origem para shard de destino. Após a conclusão com êxito do pedido global, esses shardlets já não estão presentes na origem. Tenha em atenção que as tabelas de destino precisar de existir no shard destino e não pode conter dados no intervalo de destino antes de transformação da operação de. 

* **Tabelas de referência**: para tabelas de referência, a divisão, impressão em série e mover operações copiar os dados a partir da origem para o shard de destino. No entanto, tenha em atenção que não ocorrer alterações no shard destino para uma tabela determinada se qualquer linha já se encontra nesta tabela no destino. A tabela tem de estar vazia para qualquer operação de cópia de tabela de referência obter processadas.

* **De outras tabelas**: outras tabelas podem estar presentes na origem ou de destino de uma operação de divisão e enviar em série. O serviço de intercalação de divisão ignora estas tabelas para qualquer movimento de dados ou operações de cópia. No entanto, tenha em atenção que pode interferir com estas operações em caso de restrições.

As informações na referência vs. sharded tabelas são fornecidas por **SchemaInfo** APIs no mapa shard. O exemplo seguinte ilustra a utilização destas APIs sobre um determinado shard mapa Gestor objeto smm: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

As tabelas 'região' e 'nacionais' são definidos como tabelas de referência e onde serão copiados com operações de intercalar/dividir/mover. 'Cliente' e 'Encomendas' sucessivamente são definidas como tabelas sharded. C_CUSTKEY e O_CUSTKEY age como chave sharding. 

**Integridade referencial**

O serviço de intercalação de divisão analisa dependências entre tabelas e utiliza relações de chave primária chave externa para a fase as operações para mover as tabelas de referência e shardlets. Em geral, tabelas de referência são copiadas pela primeira vez na ordem de dependência, em seguida, são copiadas shardlets na ordem dos seus dependências dentro de cada lote. Isto é necessário para que as restrições FK PK shard o destino são respeitadas como chegam os novos dados. 

**Shard mapa consistência e conclusão Eventual**

Em presença falhas, o serviço de intercalação de divisão currículos operações após qualquer indisponibilidade e destina-se para completar quaisquer nos pedidos de progresso. No entanto, poderão existir irrecuperáveis situações, por exemplo, quando a shard de destino é perdida ou comprometida para além da reparação. Nessas circunstâncias, algumas shardlets que foram suposto para ser movida poderão continuar a se encontram o shard de origem. O serviço garante que os mapeamentos de shardlet são actualizados apenas depois dos dados necessários sido copiados com êxito ao destino. Shardlets apenas são eliminados na origem de uma vez todos os seus dados foi copiado para o destino e os mapeamentos correspondentes tiverem sido atualizados com êxito. A operação de eliminação acontece em segundo plano, enquanto o intervalo já está online no shard de destino. O serviço de intercalação de divisão sempre garante regularidade mapeamento armazenados no mapa do shard.


## <a name="the-split-merge-user-interface"></a>A interface de utilizador dividido em série

O pacote de serviço de intercalação de divisão inclui uma função de trabalho e uma função da web. A função de web é utilizada para submeter pedidos de intercalação de divisão de forma interativa. Principais componentes da interface de utilizador são os seguintes:

-    Tipo de operação: O tipo de operação é um botão de opção que controla o tipo de operação executada pelo serviço durante este pedido. Pode escolher entre a divisão, intercalar e mover cenários. Também pode cancelar uma operação de submetidos anteriormente. Pode utilizar dividido, impressão em série e mover pedidos de intervalo shard mapas. Shard lista mapas apenas operações de mudança de suporte.

-    Mapa de shard: A secção seguinte dos parâmetros de pedido de folha de rosto informações sobre o mapa shard e a base de dados que aloja o seu mapa shard. Em particular, tem de fornecer o nome do servidor de base de dados do Azure SQL e alojamento shardmap, credenciais para ligar para a base de dados de mapa shard e, por fim, o nome do mapa shard da base de dados. Atualmente, a operação aceita apenas um único conjunto de credenciais. Estas credenciais têm de ter permissões suficientes para efetuar alterações para o mapa de shard também os dados de utilizador nas shards.

-    Intervalo de origem (dividir e impressão em série): uma operação de divisão e intercalar processa num intervalo utilizando a respectiva chave mínimo e máximo. Para especificar uma operação com um valor de chave alto corrigem, selecione a caixa de verificação "a chave de alta é max" e deixe o campo de chave alto vazia. Os valores de chave do intervalo que especificar não necessita de corresponder exatamente um mapeamento e os limites no seu mapa shard. Se não especificar quaisquer limites de intervalo de todo o serviço irá sugerir o intervalo mais próximo para si automaticamente. Pode utilizar o script do GetMappings.ps1 PowerShell para obter os mapeamentos atuais num mapa shard determinado.

-    O comportamento de origem de divisão (dividida): para operações de divisão, definem o ponto para dividir o intervalo de origem. Pode fazê-lo ao fornecer a chave de sharding onde pretende que a divisão para ocorrer. Utilizar o botão de opção Especifique se pretende que a parte inferior do intervalo (excluindo a chave de divisão) para mover ou, se pretende que a parte superior para mover (incluindo a chave de divisão).

-    Origem Shardlet (mover): mover operações são diferentes das operações de divisão ou em série como não necessitam de um intervalo para descrever a origem. Uma fonte para as mover simplesmente é identificada pelo valor de chave sharding que pretenda mover.

-    Alvo Shard (dividida): uma vez que forneça as informações sobre a origem da operação de divisão, necessita de definir onde pretende que os dados sejam copiados para ao fornecer o nome de servidor e base de dados de Azure SQL Db para o destino.

-    Intervalo de destino (em série): operações de impressão em série mover shardlets para um shard existente. Identifique o shard existente ao fornecer os limites de intervalo do intervalo existente que pretende intercalar com.

-    Tamanho do lote: O tamanho do lote controla o número de shardlets vai ficar offline uma vez durante a deslocação de dados. Este é um valor de número inteiro onde pode utilizar valores inferiores quando estiver sensíveis a maiúsculas e longos períodos de tempo de inatividade para shardlets. Valores maiores irão aumentar o tempo que um determinado shardlet está offline mas podem melhorar o desempenho.

-    Id da operação (Cancelar): Se tiver uma operação em curso que já não é necessária, pode cancelar a operação ao fornecer o seu ID de operação neste campo. Pode obter o ID de operação a partir da tabela de estado do pedido (consulte a secção 8.1) ou a partir de saída no browser web onde que utilizou ao submeter o pedido.


## <a name="requirements-and-limitations"></a>Requisitos e limitações 

A implementação actual do serviço de intercalação de divisão de está sujeito às seguintes requisitos e limitações: 

* Os shards tem de existir e ser registado no mapa shard antes de uma operação de divisão em série nestes shards pode ser executada. 

* O serviço não criar tabelas ou outros objetos de base de dados automaticamente como parte das suas operações. Isto significa que o esquema para sharded todas as tabelas e tabelas de referência necessário existir no shard destino antes de qualquer operação intercalar/dividir/mover. Tabelas sharded em particular são necessários para estar em branco no intervalo onde shardlets novos estão a ser adicionada por uma operação intercalar/dividir/mover. Caso contrário, a operação falhará a verificação de consistência inicial no shard destino. Tenha em atenção que referência dados são copiados apenas se a referência de tabela está vazia e que não existem sem garantias consistência exata relativamente à outras em simultâneo operações de escrita nas tabelas de referência. Recomendamos que isto: quando executar operações de intercalar/dividir, outras operações de escrita efetuar alterações às tabelas de referência.

* O serviço baseia-se sobre a identidade da linha estabelecida por um índice exclusivo ou chave que inclui a chave de sharding para melhorar o desempenho e fiabilidade para shardlets grande. Isto permite que o serviço para se mover dados uma melhor mesmo granularidade que apenas o valor da chave sharding. Isto ajuda a reduzir a quantidade máxima de espaço de registo e bloqueios que são necessários durante a operação. Considere criar um índice exclusivo ou uma chave primária, incluindo a chave de sharding numa tabela determinado se pretender utilizar a tabela com pedidos de intercalar/dividir/mover. Por razões de desempenho, a tecla sharding deve ser a coluna à esquerda na chave ou o índice.

* Durante a processamento do pedido, alguns dados de shardlet podem estar presentes na origem e o shard de destino. Isto é necessário para proteger contra falhas durante a deslocação shardlet. A integração de divisão em série com o mapa shard assegura que ligações através dos dados dependentes encaminhamento APIs utilizando o método de **OpenConnectionForKey** no mapa shard não conseguir ver qualquer inconsistentes Estados-membros intermédios. No entanto, quando ligar para a origem ou de shards destino sem utilizar o método de **OpenConnectionForKey** , inconsistentes Unidos intermédios poderão estar visíveis, quando os pedidos de intercalar/dividir/mover estão a passar. Estas ligações poderão apresentar resultados duplicados ou parciais consoante a temporização ou shard subjacente a ligação. Esta limitação atualmente inclui as ligações efetuadas por escala flexível Multi-Shard-consultas.

* A base de dados de metadados para o serviço de intercalação de divisão não deve ser partilhado entre diferentes funções. Por exemplo, uma função do serviço dividido em série a ser executada em transição precisa apontar para uma base de dados diferentes metadados que a função de produção.
 

## <a name="billing"></a>Faturação 

O serviço de divisão em série é executado como um serviço na nuvem na sua subscrição do Microsoft Azure. Por conseguinte, aplicadas taxas de serviços em nuvem para a instância do serviço. A menos que frequentemente executar operações de intercalar/dividir/mudança, recomendamos que eliminar o seu serviço de nuvem dividido em série. Que guarda os custos de execução ou implementado instâncias do serviço na nuvem. Pode voltar a implementar e iniciar a sua configuração facilmente executáveis sempre necessitar de efetuar operações de divisão ou em série. 
 
## <a name="monitoring"></a>Monitorização 
### <a name="status-tables"></a>Tabelas de estado 

O serviço de intercalação de divisão fornece a tabela **RequestStatus** nos metadados armazenar base de dados para a monitorização de pedidos de concluídas e em curso. A tabela apresenta uma linha para cada pedido de intercalação de divisão foi submetido, a esta instância do serviço de divisão em série. Dá-as seguintes informações para cada pedido:

* **Carimbo**: A hora e data quando o pedido foi iniciado.

* **IDOperação**: um GUID que identifica o pedido. Este pedido pode também ser utilizado para cancelar a operação enquanto a mesma estiver ainda em curso.

* **Estado**: O estado atual do pedido. Para em curso pedidos, também apresenta a fase atual no qual é o pedido.

* **CancelRequest**: um sinalizador que indica se o pedido foi cancelado.

* **Curso**: uma estimativa de percentagem de conclusão para a operação. Um valor de 50 indica que a operação é cerca de 50% concluída.

* **Detalhes**: valor de XML, que fornece um relatório de progresso mais detalhado. O relatório do progresso periodicamente é atualizado, conjuntos de linhas são copiados a partir de origem para o destino. Em caso de falhas ou exceções, esta coluna também inclui informações mais detalhadas sobre a falha.


### <a name="azure-diagnostics"></a>Diagnósticos do Azure

O serviço de intercalação de divisão utiliza diagnósticos do Azure com base no Azure SDK 2,5 para a monitorização e diagnóstico. Controlar a configuração de diagnóstico, tal como é explicado aqui: [Ativar diagnósticos no Azure serviços em nuvem e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). Transferir o pacote inclui duas configurações de diagnóstico – uma função de web e outra para a função de trabalho. Estas configurações de diagnóstico para o serviço siga as orientações da [Conceitos básicos de serviço de nuvem no Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Inclui as definições para iniciar sessão contadores de desempenho, IIS registos, registos de eventos do Windows e registos dos eventos de aplicação dividido em série. 

## <a name="deploy-diagnostics"></a>Implementar diagnósticos 

Para ativar a monitorização e diagnósticos de utilizar a configuração de diagnóstico para as funções web e trabalhador fornecidas pelo pacote NuGet, execute os seguintes comandos através do Azure PowerShell: 

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Pode encontrar mais informações sobre como configurar e implementar definições de diagnósticos aqui: [Ativar diagnósticos no Azure serviços em nuvem e máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Obter diagnósticos 

Pode aceder facilmente diagnósticos do seu a partir do Explorador de servidor do Visual Studio na parte Azure da árvore de Server Explorer. Abra uma instância do Visual Studio e, na barra de menus clique em ver e Server Explorer. Clique no ícone Azure para ligar à sua subscrição do Azure. Em seguida, navegue até ao Azure -> armazenamento -> <your storage account> -> tabelas -> WADLogsTable. Para mais informações, consulte o artigo [Recursos de armazenamento de navegação com o Explorador de servidor](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

WADLogsTable realçada na figura acima contém os eventos detalhados de registo de aplicação do serviço de divisão em série. Tenha em atenção que a configuração de predefinida do pacote transferido é orientada para uma implementação de produção. Por conseguinte, o intervalo no qual os registos e contadores são extraídos das instâncias de serviço é grandes (5 minutos). Para testar e desenvolvimento, Baixe o intervalo ao ajustar as definições de diagnósticos do web ou a função de trabalho para as suas necessidades. Botão direito do rato sobre o papel no Explorador de servidor do Visual Studio (consultar acima) e, em seguida, ajuste o período de transferência na caixa de diálogo para as definições de configuração de diagnóstico: 

![Configuração][3]


## <a name="performance"></a>Desempenho

Em geral, um melhor desempenho é esperado a partir do superior, mais performant serviço camadas na base de dados do SQL Azure. Superiores atribuições IO, memória e CPU para as camadas de serviço superiores beneficiam a cópia em massa e eliminar operações que utiliza o serviço de divisão em série. Por que razão, aumente a camada de serviço apenas para esses bases de dados para um período definido, limitado de tempo.

O serviço também executa consultas de validação como parte das suas operações normais. Estas consultas de validação verificar a existência de presença inesperada de dados no intervalo de destino e certifique-se de que qualquer operação intercalar/dividir/mover começa a partir de um estado consistente. Estas consultas todas as funcionam através de intervalos de chave sharding definidos pelo âmbito da operação de e o tamanho do lote fornecidos como parte da definição do pedido. Estas consultas melhor executam quando um índice remissivo é apresentar que tenha a chave de sharding como a coluna à esquerda. 

Além disso, uma propriedade de exclusividade com a tecla sharding como a coluna à esquerda permite que o serviço para utilizar uma abordagem otimizada que limita consumo de recursos em termos de memória e espaço de registo. Esta propriedade exclusividade é necessária para mover os tamanhos de dados grandes (normalmente superiores a 1GB). 

## <a name="how-to-upgrade"></a>Como atualizar

1. Siga os passos em [Implementar um serviço de divisão em série](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Altere o ficheiro de configuração do serviço de nuvem para a sua implementação dividido em série refletir novos parâmetros de configuração. Um parâmetro obrigatório novo é a informação sobre o certificado utilizado para encriptação. Uma forma fácil para o fazer consiste em comparar o novo ficheiro de modelo de configuração da transferência contra a sua configuração existente. Certifique-se de que adiciona as definições para "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" para a web e a função de trabalho.
3. Antes de implementar a atualização para Azure, certifique-se de que tem concluído a todas as operações de atualmente em execução dividido em série. Pode facilmente fazê-lo ao consultar as tabelas RequestStatus e PendingWorkflows na base de dados dividida intercalação metadados para em curso pedidos de.
4. Atualize a sua implementação de serviço de nuvem existente para dividir em série na sua subscrição do Azure com o novo pacote e ficheiro de configuração de serviço atualizados.

Não tem de aprovisionamento de uma nova base de dados de metadados para dividir em série atualizar. A nova versão será automaticamente atualizado o seu base de dados de metadados existente para a nova versão. 

## <a name="best-practices--troubleshooting"></a>Melhores práticas e resolução de problemas
 
-    Definir um inquilino de teste e exercer as operações de intercalar/dividir/mover mais importantes com o inquilino teste através de vários shards. Certifique-se de que todos os metadados são definida corretamente no seu mapa shard e que as operações não violam constrangimentos ou chaves externas.
-    Manter o inquilino de teste tamanho dos dados acima de tamanho de máxima de dados do seu inquilino do maior para se certificar de que está não se deparar com tamanho de dados relacionados com problemas. Isto ajuda a avaliar um vínculo superior no tempo necessário para mover um único inquilino. 
-    Certifique-se de que o seu esquema permite eliminações. O serviço de intercalação de divisão requer a capacidade de remover dados de shard a origem depois dos dados foram copiados com êxito ao destino. Por exemplo, **elimine accionadores** pode impedir que o serviço de eliminar os dados na origem e pode causar operações a falha.
-    A tecla sharding deve ser a coluna à esquerda no seu chave primária ou a definição de índice exclusivo. Assegura que o melhor desempenho para as consultas de validação dividir ou intercalar e para as operações de movimento e eliminação de dados reais que sempre operam em intervalos de chave sharding.
-    Colocar o seu serviço de intercalação de divisão no Centro de dados e de região onde residem as bases de dados. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 
