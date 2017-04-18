<properties
   pageTitle="Proteger um cluster de executar o Windows com segurança do Windows | Microsoft Azure"
   description="Saiba como configurar a segurança nó a nó e nó de cliente num cluster autónomo em execução no Windows com segurança do Windows."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Proteger um cluster de autónomo no Windows com segurança do Windows

Para evitar acesso não autorizado a um cluster de serviço ferro que tem seguro, especialmente quando tiver das cargas de trabalho de produção em execução no mesmo. Este artigo descreve como configurar a segurança de nó a nó e nó de cliente com segurança do Windows no ficheiro *ClusterConfig.JSON* e corresponde para o passo de segurança de configurar de [criar um cluster de autónomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para mais informações sobre como o serviço ferro utiliza a segurança do Windows, consulte o artigo [cenários de segurança de Cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE]
Pondere a seleção de segurança para nó a nó segurança cuidadosamente, uma vez que não existe nenhuma actualização de cluster de escolha de segurança de uma para outra. Alterar a seleção de segurança seria necessário reconstruir um cluster completa.

## <a name="configure-windows-security"></a>Configurar a segurança do Windows
O ficheiro de configuração do exemplo *ClusterConfig.Windows.JSON* são transferidos com o [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. Postal](http://go.microsoft.com/fwlink/?LinkId=730690) pacote da versão autónoma cluster contém um modelo para configurar a segurança do Windows.  Segurança do Windows é configurada na secção **Propriedades** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**Definição de configuração**|**Descrição**|
|-----------------------|--------------------------|
|ClusterCredentialType|Segurança do Windows é activada por definição do parâmetro **ClusterCredentialType** para *Windows*.|
|ServerCredentialType|Segurança do Windows para clientes é activada por definição do parâmetro **ServerCredentialType** para *Windows*. Isto indica que os clientes do cluster e o próprio cluster, estiver a executar o dentro de um domínio do Active Directory.|
|WindowsIdentities|Contém as identidades cluster e o cliente.|
|ClusterIdentity|Configura nó a nó segurança. Uma lista separados por vírgulas do grupo geridas contas de serviço ou nomes de máquina.|
|ClientIdentities|Configura a segurança de nó de cliente. Uma matriz de contas de utilizador do cliente.|
|Identidade|A identidade do cliente, um utilizador do domínio.|
|IsAdmin|VERDADEIRO Especifica que o utilizador de domínio tem o acesso de cliente administrador falso para acesso de cliente do utilizador.|

[Nó à segurança nó](service-fabric-cluster-security.md#node-to-node-security) está configurado definindo utilizando **ClusterIdentity**. Para poder criar relações de confiança entre nós, que devem ser efetuadas deverá ter em consideração uns dos outros. Isto pode ser feito de duas formas diferentes: Especifique o grupo de conta de serviço gerida que inclui todos os nós no cluster ou especifique as identidades de nó de domínio de todos os nós no cluster. Recomendamos vivamente utilizando a abordagem de [Grupo gerido conta do serviço (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) , especialmente para clusters maiores (mais de 10 nós) ou para clusters que provavelmente aumentar ou encolher.
Esta abordagem permite que os nós ser adicionados ou removidos da gMSA, sem exigir alterações à manifesto cluster. Esta abordagem não requer a criação de um grupo de domínio para que os administradores de cluster foi concedidos acesso direitos para adicionar e remover membros. Para mais informações, consulte o artigo [Introdução ao grupo de contas de serviço geridas](http://technet.microsoft.com/library/jj128431.aspx).

[Cliente para segurança nó](service-fabric-cluster-security.md#client-to-node-security) está configurado com **ClientIdentities**. Para estabelecer fidedignidade entre um cliente e o cluster, tem de configurar o cluster saber qual cliente identidades que pode confiar. Isto pode ser feito de duas formas diferentes: Especifique os utilizadores do grupo de domínio que podem ligar ou especificar os utilizadores de nó do domínio que podem ligar-se. Serviço ferro suporta dois tipos de controlo de acesso diferente para clientes que estão ligados a um cluster de serviço ferro: administrador e utilizador. Controlo de acesso fornece a capacidade para o administrador de cluster limitar o acesso a determinados tipos de operações de cluster para diferentes grupos de utilizadores, tornar o cluster mais segura.  Os administradores têm acesso total a capacidades da gestão (incluindo as capacidades de leitura/escrita). Por predefinição, os utilizadores, têm apenas acesso de leitura para as capacidades da gestão (por exemplo, as capacidades de consulta) e a capacidade de resolver aplicações e serviços.

A seguinte secção **segurança** de exemplo configura segurança do Windows e especifica que máquinas no *ServiceFabric/clusterA.contoso.com* fazem parte da cluster e que *CONTOSO\usera* tem acesso de cliente de administrador:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Próximos passos

Depois de configurar a segurança do Windows no ficheiro *ClusterConfig.JSON* , retoma o processo de criação de cluster no [criar um cluster de autónomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para obter mais informações sobre como nó a nó segurança, segurança nó de cliente e controlo de acesso baseado em funções, consulte o artigo [cenários de segurança de Cluster](service-fabric-cluster-security.md).

Consulte o artigo [ligar a um cluster de seguro](service-fabric-connect-to-secure-cluster.md) para obter exemplos da ligação utilizando o PowerShell ou FabricClient.
