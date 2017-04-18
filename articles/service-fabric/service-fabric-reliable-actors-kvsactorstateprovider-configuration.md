<properties
   pageTitle="Descrição geral da configuração do Azure Service ferro fiável intervenientes KVSActorStateProvider | Microsoft Azure"
   description="Saiba mais sobre configurar intervenientes com estado do Azure ferro de serviço do tipo KVSActorStateProvider."
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
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurar intervenientes fiáveis – KVSActorStateProvider
Pode modificar a configuração predefinida do KVSActorStateProvider alterando o ficheiro de settings.xml é gerado na raiz do pacote da Microsoft Visual Studio na pasta de configuração para o ator especificado.

O tempo de execução do Azure Service ferro procura nomes de secção predefinido no ficheiro settings.xml e consome os valores de configuração enquanto cria os componentes de runtime subjacente.

>[AZURE.NOTE] Efetue **não** elimine ou modifique os nomes de secção das seguintes configurações no ficheiro settings.xml que é gerado da solução do Visual Studio.

## <a name="replicator-security-configuration"></a>Configuração de segurança de replicação
Configurações de segurança de replicação são utilizadas para proteger o canal de comunicação que é utilizado durante a replicação. Isto significa que serviços não é possível ver uns dos outros tráfego de replicação, garantindo que os dados que são disponibilizados altamente também são seguros.
Por predefinição, uma secção de configuração de segurança vazios impede a segurança de replicação.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configuração de replicação
Configurações de replicação configurar a replicação que é o responsável pela efetuar o estado do fornecedor de estado do Ator altamente fiáveis.
A configuração predefinida é gerada pelo modelo Visual Studio e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para a replicação o gráfico.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomes de configuração

|Nome|Unidade|Valor predefinido|Observações|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0.015|Período de tempo para o qual a replicação no aguarda secundário depois de receber uma operação antes de enviar novamente uma confirmação para a página principal. Quaisquer outros confirmações sejam enviadas para operações de processadas dentro deste intervalo são enviadas como uma resposta.|
|ReplicatorEndpoint|N/D|Nenhum predefinido – parâmetro necessário|Endereço IP e portas a replicação principal/secundário irá utilizar para comunicar com outros replicadores na réplica definidos. Isto deve fazer referência a um ponto final de recurso TCP no manifesto do serviço. Consulte os [recursos do serviço de manifesto](service-fabric-service-manifest-resources.md) para ler mais sobre a definição de recursos de ponto final do manifesto de serviço. |
|RetryInterval|Segundos|5|Período de tempo após o qual a replicação novamente transmite uma mensagem se não receber a confirmação de uma operação.|
|MaxReplicationMessageSize|Bytes|50 MB|Tamanho máximo de dados de replicação que podem ser transmitida numa única mensagem.|
|MaxPrimaryReplicationQueueSize|Número de operações|1024|Número máximo de operações na fila de espera principal. Uma operação é disponibilizada depois da replicação principal recebe um aviso de todos os replicadores secundários. Este valor tem de ser maior do que 64 e uma potência de 2.|
|MaxSecondaryReplicationQueueSize|Número de operações|2048|Número máximo de operações na fila de espera secundária. Uma operação é disponibilizada após fazer o seu estado altamente disponíveis através do persistente. Este valor tem de ser maior do que 64 e uma potência de 2.|

## <a name="store-configuration"></a>Configuração do arquivo
Configurações de arquivo são utilizadas para configurar o arquivo local que é utilizado para persistir o estado que está a ser replicado.
A configuração predefinida é gerada pelo modelo Visual Studio e deve ser suficiente. Esta secção fala sobre configurações adicionais que estão disponíveis para o arquivo local o gráfico.

### <a name="section-name"></a>Nome da secção
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nomes de configuração

|Nome|Unidade|Valor predefinido|Observações|
|----|----|-------------|-------|
|MaxAsyncCommitDelayInMilliseconds|Milissegundos|200|Define o número máximo de lotes intervalo para o arquivo local resistentes consolidadas.|
|MaxVerPages|Número de páginas|16384|O número máximo de páginas de versão no local armazena base de dados. Determina o número máximo de transações pendentes.|

## <a name="sample-configuration-file"></a>Ficheiro de configuração de exemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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
