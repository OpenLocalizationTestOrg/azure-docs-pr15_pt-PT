<properties
   pageTitle="Criar um cluster de autónomo com VMs Azure a executar o Windows | Microsoft Azure"
   description="Saiba como criar e gerir um cluster de Azure Service ferro no Azure máquinas virtuais executar o Windows Server."
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
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Criar um cluster de ferro serviço autónomo três nó com Azure máquinas virtuais executar o Windows Server

Este artigo descreve como criar um cluster em baseados no Windows Azure máquinas virtuais (VMs), utilizando o instalador do serviço ferro autónomo para o Windows Server. Este é um caso especial de [criar e gerir um cluster de executar o Windows Server](service-fabric-cluster-creation-for-windows-server.md) onde os VMs estão [VMs Azure executar o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), no entanto, não está a criar [um cluster de ferro serviço baseado na nuvem Azure](service-fabric-cluster-creation-via-portal.md). A diferença é que cluster ferro serviço autónomo criado através dos seguintes passos totalmente é gerido por si, enquanto o Azure clusters de ferro serviço baseado na nuvem são geridos e atualizados pelo fornecedor de recursos do serviço ferro.


## <a name="steps-to-create-the-standalone-cluster"></a>Passos para criar o cluster autónomo

1. Inicie sessão portal do Azure e crie uma nova Windows Server 2012 R2 Centro de dados VM num grupo de recursos. Leia o artigo [criar uma VM do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter mais detalhes.
2. Adicione alguns mais Windows Server 2012 R2 Centro de dados VMs ao mesmo grupo de recursos. Certifique-se de que cada as VMs tem o mesmo nome de utilizador do administrador e palavra-passe quando criou. Uma vez criado, deverá ver todos os três VMs na mesma rede virtual.
3. Ligar a cada um das VMs e desativar a Firewall do Windows utilizando o [Gestor de servidor, o dashboard do servidor Local](https://technet.microsoft.com/library/jj134147.aspx). Este procedimento garante que pode comunicar o tráfego de rede entre as máquinas. Enquanto estiver ligado a cada máquina, obter o endereço IP abrindo uma linha de comandos e escrevendo `ipconfig`. Em alternativa pode ver o endereço IP do cada máquina selecionando o recurso de rede virtual para o grupo de recursos no portal do Azure.
4. Ligar a um das VMs e testar as outros duas VMs pode ping com êxito.
5. Ligar a um dos VMs e [Transferir o pacote de ferro serviço autónomo para o Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) para uma nova pasta no computador e extrair o pacote.
6. Abra o ficheiro *ClusterConfig.Unsecure.MultiMachine.json* no bloco de notas e edite cada nó com os endereços IP três das máquinas. Alterar o nome do cluster na parte superior e guarde o ficheiro.  Um exemplo parcial do manifesto cluster é apresentado abaixo.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Abra uma [janela de ISE do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Navegue para a pasta onde extraídas o pacote de instalação transferido autónomo e guardado o ficheiro de manifesto cluster. Execute o seguinte comando do PowerShell.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Deverá visualizar o PowerShell para executar, ligue para cada máquina e criar um cluster. Depois de cerca de um minuto, pode verificar se o cluster está operacional ligando-se para o Explorador de ferro serviço do endereço IP do computador, por exemplo, utilizando `http://10.7.0.5:19080/Explorer/index.html`. Uma vez que este é um cluster de autónomo utilizando Azure VMs, para que seja seguro irá têm de [Implementar certificados para o Azure VMs](service-fabric-windows-cluster-x509-security.md) ou configurar um das máquinas como um [controlador do Windows Server Active Directory (AD) para a autenticação do Windows](service-fabric-windows-cluster-windows-security.md), tal como faria no local.


## <a name="next-steps"></a>Próximos passos
- [Criar clusters de ferro serviço autónomo no Windows Server ou Linux](service-fabric-deploy-anywhere.md)
- [Adicionar ou remover nós a um cluster de ferro serviço autónomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Definições de configuração para cluster de autónomo do Windows](service-fabric-cluster-manifest.md)
- [Proteger um cluster de autónomo no Windows com segurança do Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteger um cluster de autónomo num Windows utilizando X509 certificados](service-fabric-windows-cluster-x509-security.md)
