<properties
   pageTitle="Adicionar ou remover nós a um cluster de ferro serviço autónomo | Microsoft Azure"
   description="Saiba como adicionar ou remover nós a um cluster de Azure Service ferro num física ou máquina virtual com o Windows Server, que pode ser no local ou em qualquer na nuvem."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós a um cluster de ferro serviço autónomo em execução no Windows Server

Depois de ter [criado o cluster de ferro serviço autónomo no Windows Server máquinas](service-fabric-cluster-creation-for-windows-server.md), podem alterar as suas necessidades de negócio para que poderá ter de adicionar ou remover vários nós ao seu cluster. Este artigo fornece os passos detalhados para atingir este objectivo.


## <a name="add-nodes-to-your-cluster"></a>Adicionar nós ao seu cluster

1. Preparar a VM/máquina que pretende adicionar ao seu cluster ao seguir os passos mencionados na secção [preparar máquinas para cumprir os pré-requisitos para a implementação de cluster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Planear quais domínio falhas e atualização domínio passar para adicionar esta VM máquina para.
3. Ambiente de trabalho remoto (RDP) para a VM/máquina que pretende adicionar ao cluster.
4. Copiar ou [Transferir o pacote de autónoma para ferro de serviço para o Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) para esta VM/máquina e deszipe o pacote.
5. Execute o Powershell como administrador e navegue para a localização do pacote deszipado.
6. Execute *AddNode.ps1* Powershell com os parâmetros que descreve o nó novo para adicionar. O exemplo abaixo adiciona um novo nó denominado VM5, com o tipo de NodeType0, o endereço IP 182.17.34.52 em UD1 e FD1. O *ExistingClusterConnectionEndPoint* é um ponto final de ligação para um nó já no cluster existente. Para este ponto final, pode escolher o endereço IP do *qualquer* nó no cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Remover nós do seu cluster

1. Dependendo do nível de Reliablity escolhido para cluster, não é possível remover os nós de n a primeira (3/5/7/9) nó primário do tipo de
2. Executar o comando RemoveNode num cluster Dev Center não é suportado.
2. Ambiente de trabalho remoto (RDP) para a VM/máquina que pretende remover do cluster.
2. Copiar ou [Transferir o pacote de autónomo ferro de serviço para o Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e deszipe o pacote para esta VM/máquina.
3. Execute o Powershell como administrador e navegue para a localização do pacote deszipado.
4. Execute *RemoveNode.ps1* no PowerShell. O exemplo abaixo remove o nó atual do cluster. O *ExistingClientConnectionEndpoint* é um ponto final de ligação cliente para qualquer nó que vai permanecer no cluster. Selecione o endereço IP e a porta de ponto final de *qualquer* **outro nó** no cluster. Este **outro nó** sucessivamente irão atualizar a configuração do cluster para o nó removido. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

Tenha em atenção que mesmo depois de remover um nó, este poderá aparecer como sendo para baixo em consultas e SFX. Este é um defeito conhecido e será corrigida uma versão futuras. 


## <a name="next-steps"></a>Próximos passos
- [Definições de configuração para cluster de autónomo do Windows](service-fabric-cluster-manifest.md)
- [Proteger um cluster de autónomo no Windows com segurança do Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteger um cluster de autónomo num Windows utilizando X509 certificados](service-fabric-windows-cluster-x509-security.md)
- [Criar um cluster de ferro serviço autónomo com VMs Azure a executar o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
