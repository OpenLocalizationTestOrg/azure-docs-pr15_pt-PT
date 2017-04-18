<properties
   pageTitle="Criar clusters de Azure Service ferro no Windows Server e Linux | Microsoft Azure"
   description="Clusters de serviço ferro executados no Windows Server e Linux, que significa que poderá implementar e aplicações de serviço ferro anfitriãs em qualquer lugar pode executar o Windows Server ou Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Criar clusters de serviço ferro no Windows Server ou Linux

Azure Service ferro permite a criação de serviço ferro clusters em qualquer VMs ou em computadores com Windows Server ou Linux. Isto significa que é conseguir implementar e executar as aplicações de serviço ferro em qualquer ambiente onde tem um conjunto de computadores Windows Server ou Linux que estão interligados, quer seja no local, Microsoft Azure ou com qualquer fornecedor de nuvem.

##<a name="create-service-fabric-clusters-on-azure"></a>Criar clusters de serviço ferro no Azure

Criar um cluster no Azure é feito, quer através de um modelo de recursos ou o portal do Azure. Ler [criar um cluster de serviço ferro utilizando um modelo de Gestor de recursos](service-fabric-cluster-creation-via-arm.md) ou [criar um cluster de serviço ferro a partir do Azure portal](service-fabric-cluster-creation-via-portal.md) para obter mais informações.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Sistemas operativos suportados para clusters no Azure

Se conseguir criar clusters no VMs com estes sistemas operativos:

* Windows Server 2012 R2
* Windows Server 2016 (depois de é anunciada como ficará disponível)
* Linux Ubuntu 16.04 (na pré-visualização do público) 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>Criar ferro serviço autónomo clusters no local ou com qualquer fornecedor de nuvem

Serviço ferro fornece um pacote de instalação para criar autónomo serviço ferro clusters no local ou em qualquer fornecedor de nuvem

Para obter mais informações sobre como configurar clusters de ferro serviço autónomo no Windows Server, leia o artigo [criação do cluster ferro de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Qualquer implementações nuvem vs. implementações no local
O processo para criar um cluster de serviço ferro no local é semelhante ao processo de criação de um cluster no qualquer na nuvem da sua preferência com um conjunto de VMs. Os passos iniciais para aprovisionar os VMs são regidos pelo fornecedor de nuvem ou ambiente no local que está a utilizar. Assim que tiver um conjunto de VMs com a conectividade da rede ativada entre eles, em seguida, os passos para configurar o pacote de serviço ferro, edite as definições de cluster e executar a criação de cluster e scripts de gestão de são idênticos. Isto garante que as seus conhecimentos e experiência de exploração e gerir clusters de serviço ferro é transferível quando optar por novas ambientes de alojamento de destino.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Vantagens de criar clusters de ferro serviço autónomo
* Está livre escolher qualquer fornecedor de nuvem para alojar o seu cluster.
* Aplicações de serviço ferro, uma vez por escrito, podem ser executadas em vários ambientes alojamento com mínimas para sem alterações.
* Dados de conhecimento da criação de aplicações do serviço ferro executa ao longo de um ambiente de alojamento para outra.
* Experiência operacional de executar e gerir o serviço ferro clusters cumpram ao longo de um ambiente para outro.
* Alcance do cliente abrangentes é corrigem restrições de ambiente de alojamento.
* Uma camada de fiabilidade e protecção contra falhas executarão adicional existe porque pode mover os serviços ao longo do ambiente de implementação outra se um fornecedor de dados ao centro ou na nuvem tem uma indisponibilidade.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Sistemas operativos suportados para clusters autónomo
Se conseguir criar clusters VMs ou em computadores com estes sistemas operativos:

* Windows Server 2012 R2
* Windows Server 2016 (depois de é anunciada como ficará disponível)
* Linux (brevemente)

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Vantagens de clusters de serviço ferro no Azure sobre autónomo que clusters de serviço ferro criado no local

Executar o serviço ferro clusters no Azure fornece a opção vantagens em relação no local, pelo que, se não tem necessidades específicas para onde executa os clusters, em seguida, sugerimos executá-los no Azure. No Azure, fornecemos integração outras funcionalidades Azure e serviços, o que faz a gestão do cluster e operações mais fácil e mais fiável.

* **Azure portal:** Azure portal torna mais fácil criar e gerir clusters.

* **Gestor de recursos do azure:** Utilização do Gestor de recursos do Azure permite a gestão de fácil de todos os recursos utilizados pelo cluster como uma unidade e simplifica o controlo de custos e faturação.
* **Serviço de Cluster ferro como um recurso Azure** Um cluster de serviço ferro é um recurso de processador, para que possa modelar tal como faria outros recursos de processador no Azure.
* **Integração com o Azure infraestrutura** Serviço ferro coordenadas com infraestrutura do Azure subjacente para SO, rede e outras actualizações melhorar a disponibilidade e fiabilidade das suas aplicações.  
* **Diagnósticos:** No Azure, fornecemos integração Azure diagnóstico e registo de análise.
* **Dimensionamento automática:** Para clusters no Azure, fornecemos-funcionalidade incorporada dimensionamento automática devido a conjuntos de escala de Máquina Virtual. No local e outros ambientes de nuvem, tem de criar o seu próprio funcionalidade ou escala manualmente utilizando as APIs que expõe ferro de serviço para o dimensionamento clusters de dimensionamento automática.

## <a name="next-steps"></a>Próximos passos
Criar um cluster VMs ou em computadores com o Windows Server: [criação do cluster ferro de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Criar um cluster no VMs ou computadores que executam Linux: [Ferro de serviço no Linux](service-fabric-linux-overview.md)
