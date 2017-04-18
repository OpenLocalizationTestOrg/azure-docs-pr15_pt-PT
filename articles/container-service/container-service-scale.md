<properties
   pageTitle="Dimensionar o seu cluster ACS com o clip do Azure | Microsoft Azure"
   description="Como dimensionar cluster o serviço de contentor Azure utilizando o clip do Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contentores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="timlt"/>

# <a name="scale-an-azure-container-service"></a>Escala de um serviço de contentor Azure

É possível dimensionar o número de nós do seu serviço de contentor Azure (ACS) tem utilizando a ferramenta de clip Azure. Quando utiliza o clip do Azure para dimensionar, a ferramenta devolve um novo ficheiro de configuração que representa a alteração aplicada o contentor.

## <a name="about-the-command"></a>Sobre o comando

O clip do Azure tem de ser no modo de Gestor de recursos do Azure para interagir com contentores Azure. Pode mudar para modo de Gestor de recursos ao contactar o suporte `azure config mode arm`. O `acs` comando tem um comando subordinado chamado `scale` que é que todas as operações de escala para um serviço de contentor. Pode obter ajuda sobre os vários parâmetros utilizados no comando escala ao executar `azure acs scale --help`, que produz saída algo semelhante ao seguinte:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Utilize o comando para dimensionar

Para dimensionar um serviço de contentor, tem primeiro de saber o **grupo de recursos** e o **nome do serviço de contentor Azure (ACS)**bem como especificar a contagem de agentes de nova. Ao utilizar um valor mais pequeno ou superior, é possível dimensionar para baixo ou para cima respetivamente.

Pretende saber que a contagem de agentes para um serviço de contentor atual antes que dimensionar. Utilizar o `azure acs show <resource group> <ACS name>` comando para devolver o ACS config. Tenha em atenção o resultado de <mark>contagem</mark> .

#### <a name="see-current-count"></a>Consulte o artigo contar atual

```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Dimensionar a contagem de nova

Como é provavelmente já óbvias, pode dimensionar o serviço de contentor ao contactar o suporte `azure acs scale` e fornecer o **grupo de recursos**, o **nome de ACS**e **contar agente**. Quando dimensionar um serviço de contentor, Azure clip devolve uma cadeia JSON que representa a nova configuração do serviço contentor, incluindo a contagem de agente de novo.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Próximos passos

- [Implementar um cluster de](container-service-deployment.md)