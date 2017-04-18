<properties
   pageTitle="Configurar o seu cluster autónomo | Microsoft Azure"
   description="Este artigo descreve como configurar o seu programa autónomo ou cluster de serviço ferro privada."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="dkshir"/>


# <a name="configuration-settings-for-standalone-windows-cluster"></a>Definições de configuração para cluster de autónomo do Windows

Este artigo descreve como configurar um cluster de ferro serviço autónomo utilizando o ficheiro _**ClusterConfig.JSON**_ . Pode utilizar este ficheiro para especificar informações como os nós ferro de serviço e os respetivos endereços IP, diferentes tipos de nós num cluster, as configurações de segurança, bem como a topologia da rede em termos de falhas/atualização domínios, para o seu cluster autónomo.

Quando a [Transferir o pacote de ferro serviço autónomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), alguns exemplos do ficheiro ClusterConfig.JSON são transferidas para o seu computador de trabalho. Os exemplos experienciar *DevCluster* nos seus nomes irão ajudá-lo a criar um cluster com todos os três nós no mesmo computador, como nós lógicos. Terminar estas, pelo menos um nó deve ser marcado como um nó principal. Este cluster é útil para um ambiente de desenvolvimento ou de teste e não é suportado como um cluster de produção. Os exemplos experienciar *MultiMachine* nos seus nomes, irá ajudá-lo a criar um cluster de qualidade de produção, com cada nó num computador em separado. O número de nós principais para estes cluster será baseado no [nível de fiabilidade](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* e *ClusterConfig.Unsecure.MultiMachine.JSON* mostram como criar um cluster de teste ou de produção não seguro respetivamente. 
    
2. *ClusterConfig.Windows.DevCluster.JSON* e *ClusterConfig.Windows.MultiMachine.JSON* mostram como criar cluster de teste ou de produção, protegido utilizando a [segurança do Windows](service-fabric-windows-cluster-windows-security.md).

3. *ClusterConfig.X509.DevCluster.JSON* e *ClusterConfig.X509.MultiMachine.JSON* mostram como criar cluster de teste ou de produção, protegido utilizando [X509 segurança baseada em certificados](service-fabric-windows-cluster-x509-security.md). 


Agora iremos examinar as várias secções de um ficheiro de _**ClusterConfig.JSON**_ como abaixo.

## <a name="general-cluster-configurations"></a>Configurações de cluster gerais
Isto abrange as configurações de específico de cluster abrangentes, conforme apresentado no fragmento de JSON abaixo.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

Pode dar qualquer nome amigável para o seu cluster de serviço ferro atribuindo-lo para a variável de **nome** . O **clusterConfigurationVersion** é o número de versão do seu cluster; deverá aumentá-la sempre que atualizar o seu cluster de serviço ferro. No entanto, deve deixar a **apiVersion** para o valor predefinido.


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>Nós no cluster
Pode configurar os nós no seu cluster ferro de serviço, utilizando a secção **nós** , como a seguinte mostra o fragmento.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Um cluster de serviço ferro tem de conter pelo menos 3 nós. Pode adicionar mais nós para esta secção de acordo com a configuração. A seguinte tabela explica as definições de configuração para cada nó.

|**Configuração de nó**|**Descrição**|
|-----------------------|--------------------------|
|NomeNó|Pode dar qualquer nome amigável para o nó.|
|endereço IP|Descobrir o endereço IP do seu nó ao abrir uma janela de comando e escrever `ipconfig`. Tenha em atenção o endereço IPV4 e atribuí-la para a variável de **endereço IP** .|
|nodeTypeRef|Cada nó pode ser atribuído um tipo de nó diferente. Os [tipos de nó](#nodetypes) são definidas na secção abaixo.|
|faultDomain|Domínios falhas permitem aos administradores cluster definir os nós físicos que poderão falhar ao mesmo tempo definitiva devido às dependências físicas partilhadas.|
|upgradeDomain|Domínios atualização descrevem conjuntos de nós encerrar para actualizações de serviço ferro na sobre ao mesmo tempo. Pode escolher quais os nós para atribuir a que a atualização domínios, à medida que não são limitados pelo quaisquer requisitos físicos.| 


## <a name="cluster-properties"></a>Propriedades do cluster

Secção **Propriedades** na ClusterConfig.JSON é utilizada para configurar o cluster da seguinte forma.

<a id="reliability"></a>
### <a name="reliability"></a>Fiabilidade 
A secção **reliabilityLevel** define o número de cópias dos serviços de sistema que podem ser executados nos nós principais do cluster. Isto aumenta a fiabilidade destes serviços e, consequentemente, cluster. Pode definir esta variável para *Bronze*, *prata*, *ouro* ou *Platinum* para cópias de 3-5, 7 ou 9 um destes serviços respetivamente. Ver um exemplo abaixo.

    "reliabilityLevel": "Bronze",
    
Note que uma vez que um nó primário executa uma única cópia dos serviços do sistema, seria necessário um mínimo de 3 nós principais para *Bronze*, 5 para *prata*, 7 para *ouro* e 9 para níveis de fiabilidade *Platinum* .


### <a name="diagnostics"></a>Diagnósticos
A secção **diagnosticsStore** permite-lhe configurar parâmetros para ativar de diagnóstico e resolução de problemas falhas nó ou cluster, conforme mostrado no seguinte fragmento. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Os **metadados** são uma descrição do seu diagnósticos cluster e podem ser definido de acordo com o seu programa de configuração. Estas variáveis ajudam a recolher ETW registos de rastreio, informações de estado da falha de sistema, bem como contadores de desempenho. Para mais informações sobre registos de rastreio ETW de leitura [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [ETW Tracing](https://msdn.microsoft.com/library/ms751538.aspx) . Todos os registos, incluindo [uma falha de sistema informações de estado](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contadores de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) podem ser direcionados para a pasta **connectionString** no seu computador. Também pode utilizar *AzureStorage* para armazenar diagnósticos. Consulte abaixo para um fragmento de exemplo.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Segurança 
A secção de **segurança** é necessária para um cluster de ferro serviço autónomo seguro. O fragmento que se segue mostra uma parte desta secção.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Os **metadados** são uma descrição do seu cluster seguro e podem ser definido de acordo com o seu programa de configuração. A **ClusterCredentialType** e **ServerCredentialType** determinam o tipo de título que serão implementar o cluster e os nós. Podem ser definidas para um dos *X509* de um título com base no certificado ou o *Windows* para um segurança baseada em Azure Active Directory. O resto da secção **segurança** será baseado no tipo de segurança. Ler [segurança certificados baseia-se num cluster autónomo](service-fabric-windows-cluster-x509-security.md) ou de [segurança do Windows num cluster autónomo](service-fabric-windows-cluster-windows-security.md) para obter informações sobre como preencha o resto da secção **segurança** .


<a id="nodetypes"></a>
### <a name="node-types"></a>Tipos de nó
A secção **nodeTypes** descreve o tipo dos nós que tem o seu cluster. Tipo de pelo menos um nó tem de ser especificado para um cluster, conforme apresentado no fragmento de abaixo. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

O **nome** é o nome amigável para este tipo de nó específico. Para criar um nó deste tipo de nó, atribuir o seu nome amigável para a variável de **nodeTypeRef** para esse nó, como mencionado [acima](#clusternodes). Para cada tipo de nó, defina os pontos finais de ligação que serão utilizados. Pode escolher qualquer número de porta para estes pontos finais de ligação, desde que não entram em conflito com os pontos finais deste cluster. Num cluster de nó com várias, haverá um ou mais nós principais (ou seja, **isPrimary** definido como *Verdadeiro*), consoante o [**reliabilityLevel**](#reliability). Leia [Considerações de planeamento do serviço ferro cluster capacidade](service-fabric-cluster-capacity.md) para informações sobre os valores **nodeTypes** e **reliabilityLevel** e saber o que são principal e os tipos de nó não principal. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Pontos finais utilizados para configurar os tipos de nó

- *clientConnectionEndpointPort* é a porta utilizada pelo cliente para ligar ao cluster, ao utilizar o cliente APIs. 
- *clusterConnectionEndpointPort* é a porta no qual os nós de comunicarem com os outros.
- *leaseDriverEndpointPort* é a porta utilizada pelo controlador de locação financeira cluster para saber se os nós ainda estão ativos. 
- *serviceConnectionEndpointPort* é a porta utilizada pelas aplicações e serviços implementados num nó, para comunicar com o cliente de serviço ferro nesse nó específico.
- *httpGatewayEndpointPort* é a porta utilizada pelo Explorador de ferro do serviço para ligar ao cluster.
- *ephemeralPorts* são as [portas dinâmicas utilizadas pelo SO](https://support.microsoft.com/kb/929851). Serviço ferro irá utilizar uma parte destas como portas de aplicações e o restante estará disponível para o sistema operativo. Também-serão mapeadas este intervalo para o intervalo existente presente no sistema operativo, para que para todos os efeitos, pode utilizar os intervalos dadas em ficheiros de JSON de exemplo. É necessário para se certificar de que a diferença entre o início e as portas final é, pelo menos, 255. 
- *applicationPorts* são as portas que serão utilizadas por aplicações de serviço ferro. Estes devem ser um subconjunto do *ephemeralPorts*, suficiente para cobrir o requisito de ponto final das suas aplicações. Serviço ferro utilizará estas sempre que novas portas necessários, bem como encarregam-se de abertura de firewall para estas portas. 
- *reverseProxyEndpointPort* é um ponto final de proxy inverso opcional. Para mais detalhes, consulte [Serviço ferro inverter Proxy](service-fabric-reverseproxy.md) . 


### <a name="other-settings"></a>Outras definições
A secção **fabricSettings** permite-lhe definir os directórios de raiz para os dados de serviço ferro e os registos. Pode personalizar estes apenas durante a criação de cluster inicial. Consulte abaixo para exemplo fragmento de código nesta secção.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Recomendamos utilizando uma unidade de que não sejam SO como a FabricDataRoot e FabricLogRoot à medida que fornece que mais fiabilidade contra SO falha. Tenha em atenção que se personalizar só a raiz de dados, em seguida, a raiz de registo será colocada um nível abaixo da raiz de dados.


## <a name="next-steps"></a>Próximos passos

Assim que tiver um ficheiro ClusterConfig.JSON completo configurado de acordo com o seu programa de configuração de cluster autónomo, pode implementar o seu cluster seguindo o artigo [criar um cluster de Azure Service ferro no local ou na nuvem](service-fabric-cluster-creation-for-windows-server.md) e, em seguida, avance para [visualizar o seu cluster com o Explorador de ferro de serviço](service-fabric-visualizing-your-cluster.md).


