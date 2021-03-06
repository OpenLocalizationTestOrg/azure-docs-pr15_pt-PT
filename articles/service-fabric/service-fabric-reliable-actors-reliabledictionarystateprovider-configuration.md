<properties
   pageTitle="Descrição geral da configuração do Azure Service ferro fiável intervenientes ReliableDictionaryActorStateProvider | Microsoft Azure"
   description="Saiba mais sobre configurar intervenientes com estado do Azure ferro de serviço do tipo ReliableDictionaryActorStateProvider."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/18/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Configurar intervenientes fiáveis – ReliableDictionaryActorStateProvider
Pode modificar a configuração predefinida do ReliableDictionaryActorStateProvider alterando o ficheiro de settings.xml gerado na raiz de pacote Visual Studio na pasta de configuração para o ator especificado.

O tempo de execução do Azure Service ferro procura nomes de secção predefinido no ficheiro settings.xml e consome os valores de configuração enquanto cria os componentes de runtime subjacente.

>[AZURE.NOTE] Efetue **não** elimine ou modifique os nomes de secção das seguintes configurações no ficheiro settings.xml que é gerado da solução do Visual Studio.

Também existem definições globais que afetam a configuração do ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Configuração global

A configuração global está especificada no manifesto cluster para cluster na secção KtlLogger. -Permite a configuração da localização do registo partilhado e tamanho juntamente com os limites de memória global utilizados pelo registo. Tenha em atenção que as alterações no manifesto cluster afectar todos os serviços que utilizam ReliableDictionaryActorStateProvider e serviços com estado fiáveis.

O manifesto cluster é um único ficheiro XML que detém definições e configurações que se aplicam a todos os nós e serviços no cluster. O ficheiro é normalmente chamado ClusterManifest.xml. Pode ver o cluster manifesto para o seu cluster utilizando o comando do powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Nomes de configuração

|Nome|Unidade|Valor predefinido|Observações|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Quilobytes|8388608|Número mínimo de KB para atribuir no modo de núcleo para o conjunto de memória de memória intermédia de escrita do registo. Este conjunto de memória é utilizado para informações de estado antes de escrever no disco de colocação em cache.|
|WriteBufferMemoryPoolMaximumInKB|Quilobytes|Sem limite|Pode aumentar o tamanho máximo para o qual o registo escrever um conjunto de memória memória intermédia.|
|SharedLogId|GUID|""|Especifica um exclusivo GUID a utilizar para identificar o ficheiro de registo partilhada predefinido utilizado por todos os serviços fiáveis em todos os nós no cluster não especificar a SharedLogId na sua configuração específicas do serviço. Se não for especificado SharedLogId, SharedLogPath também tem especificado.|
|SharedLogPath|Nome do caminho completamente qualificado|""|Especifica o caminho completamente qualificado onde o ficheiro de registo partilhada utilizado por todos os serviços fiáveis em todos os nós no cluster não especificar a SharedLogPath na sua configuração específicas do serviço. No entanto, se for especificado SharedLogPath, em seguida, SharedLogId também tem de ser especificado.|
|SharedLogSizeInMB|Megabytes|8192|Especifica o número de MB de espaço em disco para estática alocar para o registo de partilhada. O valor tem de ser 2048 ou maior.|

### <a name="sample-cluster-manifest-section"></a>Secção de manifesto de cluster de exemplo
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Observações
O registo tem um conjunto de memória atribuída a partir de memória kernel paginada que está disponível para todos os serviços fiáveis num nó para colocação em cache de dados de estado antes de a ser escrito no registo dedicado associada a réplica serviço fiável global. O tamanho do conjunto de dados é controlado através das definições WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Especifica o tamanho inicial deste conjunto de memória e o tamanho mais baixo ao qual poderá encolher o conjunto de memória. WriteBufferMemoryPoolMaximumInKB é o tamanho mais alto ao qual pode aumentar o conjunto de memória. Cada réplica de um serviço fiável é aberta pode aumentar o tamanho do conjunto de memória de um montante de sistema determinado até WriteBufferMemoryPoolMaximumInKB. Se existir mais pedido de memória a partir do conjunto de memória que está disponível, pedidos de memória ser adiados até memória está disponível. Por isso, se o conjunto de memória de memória intermédia de escrever for demasiado pequeno para uma determinada configuração desempenho sofrer.

As definições de SharedLogId e SharedLogPath sempre são utilizadas em conjunto para definir o GUID e a localização para o registo partilhada predefinido para todos os nós no cluster. O registo de partilhada predefinido é utilizado para todos os serviços de fiáveis não especificar as definições no settings.xml para o serviço específico. Para um melhor desempenho, ficheiros de registo partilhados devem ser colocados em discos que são utilizados apenas para o ficheiro de registo partilhada para reduzir contenção.

SharedLogSizeInMB Especifica a quantidade de espaço em disco a pré-alocar para o registo partilhada predefinido em todos os nós.  SharedLogId e SharedLogPath não necessita de ser especificado na ordem de SharedLogSizeInMB ser.

## <a name="replicator-security-configuration"></a>Configuração de segurança de replicação
Configurações de segurança de replicação são utilizadas para proteger o canal de comunicação que é utilizado durante a replicação. Isto significa que serviços não é possível ver uns dos outros tráfego de replicação, garantindo que os dados que são disponibilizados altamente também são seguros.
Por predefinição, uma secção de configuração de segurança vazios impede a segurança de replicação.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração de replicação
Configurações de replicação são utilizadas para configurar a replicação que é o responsável pela fazer com que o estado do fornecedor de estado do Ator altamente fiáveis por replicadas e persistência localmente o estado.
A configuração predefinida é gerada pelo modelo Visual Studio e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para a replicação o gráfico.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração

|Nome|Unidade|Valor predefinido|Observações|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0.015|Período de tempo para o qual a replicação no aguarda secundário depois de receber uma operação antes de enviar novamente uma confirmação para a página principal. Quaisquer outros confirmações sejam enviadas para operações de processadas dentro deste intervalo são enviadas como uma resposta.||
|ReplicatorEndpoint|N/D|Nenhum predefinido – parâmetro necessário|Endereço IP e portas a replicação principal/secundário irá utilizar para comunicar com outros replicadores na réplica definidos. Isto deve fazer referência a um ponto final de recurso TCP no manifesto do serviço. Consulte os [recursos do serviço de manifesto](service-fabric-service-manifest-resources.md) para ler mais sobre a definição de recursos de ponto final no manifesto de serviço. |
|MaxReplicationMessageSize|Bytes|50 MB|Tamanho máximo de dados de replicação que podem ser transmitida numa única mensagem.|
|MaxPrimaryReplicationQueueSize|Número de operações|8192|Número máximo de operações na fila de espera principal. Uma operação é disponibilizada depois da replicação principal recebe um aviso de todos os replicadores secundários. Este valor tem de ser maior do que 64 e uma potência de 2.|
|MaxSecondaryReplicationQueueSize|Número de operações|16384|Número máximo de operações na fila de espera secundária. Uma operação é disponibilizada após fazer o seu estado altamente disponíveis através do persistente. Este valor tem de ser maior do que 64 e uma potência de 2.|
|CheckpointThresholdInMB|MB|200|Quantidade de espaço de ficheiro de registo após o qual o estado é verificado.|
|MaxRecordSizeInKB|KB|1024|Tamanho registo maior a replicação pode escrever no registo de. Este valor tem de ser um múltiplo de 4 e superiores a 16.|
|OptimizeLogForLowerDiskUsage|Booleano|VERDADEIRO|Quando for VERDADEIRO, o registo é configurado para que o ficheiro de registo dedicada a réplica é criado utilizando um ficheiro disperso NTFS. Isto diminui a utilização do espaço de disco real para o ficheiro. Quando falso, o ficheiro é criado com distribuições fixas, que disponibiliza que a melhor desempenho de escrita.|
|SharedLogId|GUID|""|Especifica um exclusivo guid a utilizar para identificar o ficheiro de registo partilhada utilizado com esta réplica. Normalmente, serviços não devem utilizar esta definição. No entanto, se for especificado SharedLogId, em seguida, SharedLogPath também tem de ser especificado.|
|SharedLogPath|Nome do caminho completamente qualificado|""|Especifica o caminho completamente qualificado onde o ficheiro de registo partilhada para esta réplica será criado. Normalmente, serviços não devem utilizar esta definição. No entanto, se for especificado SharedLogPath, em seguida, SharedLogId também tem de ser especificado.|


## <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>Observações
O parâmetro BatchAcknowledgementInterval controla latência de replicação. Um valor de "0" resulta na latência possível mais baixa, pelo valor débito (tal como mais mensagens de confirmação devem ser enviadas e processadas, cada que contém as confirmações menos).
Maior será o valor para BatchAcknowledgementInterval, quanto maior o replicação débito global, pelo valor mais elevado latência de operação. Isto diretamente corresponde a função a latência da transação consolidadas.

O parâmetro CheckpointThresholdInMB controla a quantidade de espaço em disco que pode utilizar a replicação para armazenar informações de estado no ficheiro de registo dedicada a réplica. Aumentar esse para um valor maior que o predefinido poderá resultar em reconfiguração mais rápidos quando uma nova réplica é adicionada ao conjunto. Este é devido a transferência de estado parcial que ocorre devido a disponibilidade de histórico mais de operações no registo de. Isto potencialmente pode aumentar o tempo de recuperação de uma réplica após uma falha de sistema.

Se definir OptimizeForLowerDiskUsage como VERDADEIRO, o espaço de ficheiro de registo ser indevidamente aprovisionado para que réplicas activas podem armazenar informações de estado mais nos seus ficheiros de registo, enquanto réplicas Inativas irão utilizar menos espaço em disco. Isto torna possível réplicas mais num nó do anfitrião. Se definir OptimizeForLowerDiskUsage FALSO, as informações de estado são escritas mais rapidamente os ficheiros de registo.

A definição de MaxRecordSizeInKB define o tamanho máximo de um registo que pode ser escrito pela replicação para o ficheiro de registo. Na maioria dos casos, o tamanho de registo de 1024 KB predefinido é ideal. No entanto, se o serviço está a causar o maiores dados itens a fazer parte das informações de estado, em seguida, este valor poderá ter de ser aumentada. Há pouco benefício ao fazer MaxRecordSizeInKB mais pequeno do que 1024, tal como registos mais pequenos utilizam apenas o espaço necessário para o registo mais pequeno. Esperar que que este valor seria têm de ser alterados apenas em alguns casos raros.

As definições de SharedLogId e SharedLogPath sempre são utilizadas em conjunto para tornar um serviço utilizar um registo em separado partilhado a partir do registo de partilhada predefinido para o nó. Para obter melhores resultados, serviços maior número possível deverão especificar o mesmo registo partilhado. Ficheiros de registo partilhados devem ser colocados em discos que são utilizados apenas para o ficheiro de registo partilhada para reduzir contenção de cabeça movimento. Esperar que que estes valores seriam têm de ser alterados apenas em alguns casos raros.
