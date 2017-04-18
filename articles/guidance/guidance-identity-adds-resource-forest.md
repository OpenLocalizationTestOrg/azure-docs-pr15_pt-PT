<properties
   pageTitle="Referência arquitetura Azure - IaaS: Criar uma floresta de recursos do Active Directory no Azure | Microsoft Azure"
   description="Como criar um domínio do Active Directory fidedigno no Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="creating-a-active-directory-directory-services-adds-resource-forest-in-azure"></a>Criar uma floresta de recursos de serviços de diretório do Active Directory (adiciona) no Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve como criar um domínio do Active Directory no Azure que separada do, mas fidedigna por, domínios na sua floresta no local.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Uma organização que executa o Active Directory (AD) no local, poderá ter uma floresta que incluam muitos domínios diferentes. Por exemplo, poderá criar domínios individuais para diferentes departamentos ou suborganizations ou novos domínios poderão ter sido adicionados estatístico como resultado da aquisição ou fusão de outras organizações. Pode utilizar domínios para fornecer isolamento entre as áreas funcionais que devem ser mantidos em separado, possivelmente por motivos de segurança, mas pode partilhar informações entre domínios estabelecendo relações de fidedignidade.

Uma organização que utiliza domínios separados pode tirar partido do Azure por reposicionar um ou mais destes domínios na nuvem. Em alternativa, uma organização poderá optar por manter todos os recursos de nuvem logicamente distintas desse mantido no local e armazenar informações sobre recursos da nuvem no seu próprio diretório, num domínio também contidos na nuvem.

Pode implementar a do Active Directory no Azure de várias formas diferentes, tal como descrito nos artigos [Prolongamento do Active Directory para Azure] [ extending-ad-to-azure] e [Execução do Azure Active Directory][implementing-aad]. Este documento foca um cenário específico: criar um domínio na nuvem que são distinta a partir de qualquer domínios contidos no local, mas que têm uma relação de confiança com domínios no local. 

Casos de utilização típica para esta arquitectura incluem:

- Manutenção de separação de segurança para objetos e identidades contidas na nuvem.

- Migrar domínios individuais a partir no local para a nuvem.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte realça os componentes importantes nesta arquitectura (*clique para ampliar*). Para obter mais informações sobre os elementos a cinzento, leia o artigo [Implementar uma arquitetura de rede seguro híbrido no Azure] [ implementing-a-secure-hybrid-network-architecture] e [Implementar uma arquitetura de rede híbrido seguro com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Rede no local.** A rede no local contém as suas próprias floresta de AD e os domínios.

- **Servidores de AD.** Estes são os controladores de domínio implementar os serviços de diretório (AD DS) em execução como VMs na nuvem. Nestes servidores alojam uma floresta que contém uma ou mais domínios, separados desse localizado no local.

- **Relação de confiança unidirecional.** O exemplo no diagrama mostra uma fidedignidade unidirecional a partir do domínio na nuvem para o domínio no local. Desta relação permite que os utilizadores no local para aceder aos recursos no domínio na nuvem, mas não no sentido inverso. Este é um caso comuns. No entanto, pode criar uma fidedignidade bidireccional se os utilizadores na nuvem também requerem acesso aos recursos de no local.

- **Sub-rede diretório ativo.** Os servidores de AD DS estão alojados numa sub-rede separada. Regras NSG protegem os servidores de AD DS e podem fornecer uma firewall contra tráfego a partir de origens inesperados.

- **Sub-rede de camada Web**, empresas camada de **sub-rede**e **dados camada sub-rede**. Estas sub-redes alojam os servidores e componentes executam aplicações na nuvem. Para obter mais informações, consulte o artigo [Executar VMs para uma arquitetura de camadas no Azure][running-VMs-for-an-N-tier-architecture-on-Azure]. Os recursos e VMs nesta sub-rede estão contidas dentro do domínio na nuvem.

- **Azure Gateway**. O gateway Azure fornece uma ligação entre a rede no local e o Azure VNet. Isto pode ser uma [ligação VPN] [ azure-vpn-gateway] ou [Azure ExpressRoute][azure-expressroute]. Para obter mais informações, consulte o artigo [Implementar uma arquitetura de rede seguro híbrido no Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recomendações

Esta secção fornece uma lista de recomendações com base nos componentes essenciais necessários para implementar a arquitetura básica. Estas recomendações abrangem:

- Definições de adiciona, e

- Configuração de relação de confiança.

Poderá ter de requisitos adicionais ou diferentes dos descritos aqui. Pode utilizar os itens nesta secção como ponto de partida para considerar como personalizar a arquitetura para o seu próprio sistema.

### <a name="adds-recommendations"></a>Adiciona recomendações

Para obter recomendações específicas sobre a implementação do Active Directory na nuvem, consulte o documento [Prolongamento do Active Directory para Azure][extending-ad-to-azure]. O artigo [diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais do Azure] [ ad-azure-guidelines] contém informações detalhadas adicionais.

### <a name="trust-recommendations"></a>Recomendações de fidedignidade

Os domínios no local estão contidos dentro de uma floresta diferente de domínios na nuvem. Para ativar a autenticação de utilizadores no local na nuvem, os domínios na nuvem devem confiar no domínio de início de sessão na floresta no local. Da mesma forma, se na nuvem fornece um domínio de início de sessão para utilizadores externos, poderá ser necessário para a floresta no local confiar no domínio na nuvem.

Pode estabelecer fidedignidades ao nível da floresta criando [fidedignidades de floresta][creating-forest-trusts], ou ao nível do domínio através da [criação de fidedignidades externas][creating-external-trusts]. Uma fidedignidade nível de floresta cria uma relação entre duas florestas todos os domínios. Uma nível de fidedignidade para domínios externos só cria uma relação entre dois domínios especificados. Só deverá criar fidedignidades nível de domínio externo entre domínios em florestas diferentes.

Fidedignidades podem ser unidireccionais (unidirecional) ou bidireccional (bidireccional):

- Uma fidedignidade unidirecional permite aos utilizadores de um domínio ou floresta (conhecido como domínio *recebidas* ou floresta) para aceder aos recursos contidos em outra (o *envio* de domínio ou floresta). 

- Uma fidedignidade bidireccional permite aos utilizadores no domínio ou floresta aceder a recursos contidos no outro.

A tabela seguinte resume as configurações de confiança para alguns cenários de simples:

| Cenário | Confia no local | Nuvem de fidedignidade |
|----------|-------------------|-------------|
| No local utilizadores requerem acesso aos recursos na nuvem, mas não vice-versa | Entrada, sentidos | Unidirecional, envio |
| Os utilizadores na nuvem requerem acesso para recursos localizados no local, mas não vice-versa | Unidirecional, envio | Entrada, sentidos |
| Os utilizadores na nuvem e no local requer o acesso aos recursos contidos em nuvem e no local | Bidirecional, recebidas e enviadas | Bidirecional, recebidas e enviadas |

## <a name="security-considerations"></a>Considerações de segurança

Fidedignidades de nível de floresta são verbos. Se tiver estabelecer uma fidedignidade nível de floresta entre uma floresta no local e uma floresta na nuvem, esta fidedignidade é expandida para outros novos domínios criados numa quer floresta. Se utilizar domínios para fornecer separação por motivos de segurança, considere criar fidedignidades ao nível do domínio apenas. Fidedignidades de nível de domínio são não transitiva.

Considerações de segurança AD específicas, consulte a secção *Considerações de segurança* no [Prolongamento Active Directory para Azure][extending-ad-to-azure].

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Implemente, pelo menos, dois controladores de domínio para cada domínio. Isto permite a replicação automática entre servidores. Crie uma disponibilidade configurar para VMs serve como os servidores de AD processamento cada domínio. Certifique-se de que não existem, pelo menos, dois servidores do conjunto. 

Além disso, considere designar um ou mais servidores em cada domínio como [modelos globais de operações inactivo] [ standby-operations-masters] no caso de falha de conectividade a um servidor de uma função FSMO.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

AD é automaticamente dimensionável controladores de domínio que fazem parte do mesmo domínio. Pedidos de são distribuídos por todos os controladores dentro de um domínio. Pode adicionar outro controlador de domínio e sincroniza automaticamente com o domínio. Não configure um balanceador de carga separada para direcionar tráfego para controladores dentro do domínio. Certifique-se de que todos os controladores de domínio têm recursos de memória e armazenamento suficientes para gerir a base de dados do domínio. Tornar o controlador de domínio todos os VMs do mesmo tamanho.

## <a name="management-and-monitoring-considerations"></a>Considerações de monitorização e gestão

Para obter informações sobre considerações de monitorização e gestão, consulte as secções equivalentes no [Prolongamento Active Directory para Azure][extending-ad-to-azure]. 

Para obter informações adicionais, consulte o artigo [Monitoring Active Directory][monitoring_ad]. Pode instalar ferramentas, como o [Centro de sistemas da Microsoft] [ microsoft_systems_center] num servidor de monitorização na sub-rede management para o ajudar a efetuar as seguintes tarefas. 

## <a name="solution-components"></a>Componentes da solução

Um script de solução de exemplo, [Implementar ReferenceArchitecture.ps1][solution-script], está disponível que pode utilizar para implementar a arquitetura que se segue as recomendações descritas neste artigo. Este script utiliza modelos de Gestor de recursos do Azure. Os modelos estão disponíveis como um conjunto de blocos modulares fundamentais, cada um dos quais executa uma ação específica como criar um VNet ou configurar uma NSG. O objetivo do script é orquestrar implementação do modelo.

Os modelos são parametrizados, com os parâmetros contidos em ficheiros JSON separados. Pode modificar os parâmetros nestes ficheiros para configurar a implementação para cumprir os seus próprios requisitos. Não necessita de alterar os modelos de si. Não tem de alterar os esquemas dos objetos nos ficheiros do parâmetro.

Quando edita os modelos, criar objetos que se seguem as convenções de nomenclatura descritas na [Recomendado convenções de nomenclatura de para recursos de Azure][naming-conventions].

A solução de exemplo cria e configura um ambiente na nuvem que implementa um domínio com o nome *treyresearch.com*. Este ambiente inclui o gateway de VPN sub-rede e servidores, DMZ, camada web, camada de negócio e componentes de níveis de acesso de dados, adiciona e gestão camada. Solução da amostra também inclui uma configuração opcional para criar um simulada ambiente no local com o seu próprio domínio, *contoso.com*. A solução inclui scripts estabelecer uma relação de confiança através destes domínios que permite aos utilizadores no local aceder a objectos no domínio de *treyresearch.com* na nuvem.

As secções seguintes descrevem os elementos no local e na nuvem configurações.

### <a name="on-premises-components"></a>Componentes no local

>[AZURE.NOTE] Estes componentes não são o foco principal de arquitectura descrita neste documento e são fornecidos simplesmente para lhe dar a oportunidade de ambiente de teste o nuvem em segurança, em vez de utilizar um ambiente de produção real. Por este motivo, esta secção resume apenas os ficheiros de parâmetro de chave. Pode modificar as definições de como os endereços IP ou os tamanhos das VMs, mas é aconselhável para deixar muitos dos outros parâmetros inalterados.

Este ambiente inclui uma floresta AD o domínio *contoso.com* . O domínio contém dois adiciona servidores com os endereços IP 192.168.0.4 e 192.168.0.5. Estes dois servidores também executar o serviço de DNS. Chama-se na conta de administrador local no ambas as VMs `testuser` com palavra-passe `AweS0me@PW`. Para além disso, a configuração configura um gateway VPN para ligar à VNet na nuvem. Pode modificar a configuração ao editar os seguintes ficheiros JSON localizados nos [**parâmetros/onpremise**] [ on-premises-folder] pasta:

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Este ficheiro define o espaço de endereços de rede para o ambiente no local.

- ** [virtualMachines adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Este ficheiro contém a configuração para os VMs no local adiciona serviços de alojamento. Por predefinição, são criados dois *padrão-DS3-v2* VMs.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** e ** [connection.parameters.json][on-premises-connection-parameters]**. Estes ficheiros mantenha as definições para a ligação de VPN para o gateway Azure VPN na nuvem, incluindo a chave partilhada para ser utilizado para proteger o tráfego percorrer o gateway.

Os ficheiros na pasta restantes contêm as informações de configuração utilizadas para criar o domínio no local (*contoso.com*) utilizando este infraestrutura. Utilizá-los para instalar o adiciona, configuração DNS e criar floresta no local.

A solução também utiliza o script seguinte, com o nome [receção trust.ps1][incoming-trust], que é executada numa máquina de no domínio no local:

```Powershell
# Run the following powershell script in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)

$TrustedDomainName = "contoso.com"
#$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

$TrustingDomainName = "treyresearch.com"
$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustingDomainDnsIpAddresses
$ForwardDomainName = $TrustingDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Este script adiciona os endereços IP dos servidores do AD DS na nuvem (consulte a secção seguinte) para o serviço DNS local e, em seguida, utiliza o [netdom] [ netdom] comando para criar uma fidedignidade unidirecional entrada a partir do domínio na nuvem (*treyresearch.com*).

### <a name="cloud-components"></a>Componentes de nuvem

O essencial esta arquitetura de formulário estes componentes. Se a infraestrutura do domínio *treyresearch.com* do programa de configuração e de criar as relações de fidedignidade com o domínio de *contoso.com* no local. Os [**parâmetros/azure**] [ azure-folder] pasta contém os seguintes ficheiros de parâmetro para configurar estes componentes:

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Este ficheiro define a estrutura do VNet para os VMs e outros componentes na nuvem. Inclui definições, tais como o nome, espaço de endereços, sub-redes e os endereços de todos os servidores DNS necessários. Os endereços de DNS apresentada esta referência exemplo os endereços IP dos servidores DNS no local e o servidor Azure DNS predefinido. Modificar estes endereços para fazer referência a sua própria configuração DNS se não estiver a utilizar o exemplo-ambiente no local:
    
    ```json
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg",
        "addressPrefixes": [
          "10.0.0.0/16"
        ],
        "subnets": [
          {
            "name": "dmz-private-in",
            "addressPrefix": "10.0.0.0/27"
          },
          {
            "name": "dmz-private-out",
            "addressPrefix": "10.0.0.32/27"
          },
          {
            "name": "dmz-public-in",
            "addressPrefix": "10.0.0.64/27"
          },
          {
            "name": "dmz-public-out",
            "addressPrefix": "10.0.0.96/27"
          },
          {
            "name": "mgmt",
            "addressPrefix": "10.0.0.128/25"
          },
          {
            "name": "GatewaySubnet",
            "addressPrefix": "10.0.255.224/27"
          },
          {
            "name": "web",
            "addressPrefix": "10.0.1.0/24"
          },
          {
            "name": "biz",
            "addressPrefix": "10.0.2.0/24"
          },
          {
            "name": "data",
            "addressPrefix": "10.0.3.0/24"
          },
          {
            "name": "adds",
            "addressPrefix": "10.0.4.0/27"
          }
        ],
        "dnsServers": [
          "10.0.4.4",
          "10.0.4.5",
          "168.63.129.16"
        ]
      }
    }
    ```

- ** [virtualMachines adds.parameters.json ] [ virtualmachines-adds-parameters] **. Este ficheiro configura VMs executar adiciona na nuvem. A configuração consiste em duas VMs. Alterar o nome de utilizador de administração e a palavra-passe na `virtualMachineSettings` secção e, opcionalmente, pode modificar o tamanho da memória virtual para que correspondam aos requisitos do domínio:

    Para obter mais informações, consulte o artigo [Prolongamento do Active Directory para Azure][extending-ad-to-azure].
    
    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-ad",
        "computerNamePrefix": "aad",
        "size": "Standard_DS3_v2",
        "osType": "Windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "adds",
            "privateIPAllocationMethod": "Static",
            "startingIPAddress": "10.0.4.4",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 127,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [
        ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-as"
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-adtrust-vnet",
        "resourceGroup": "ra-adtrust-network-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ```

- ** [Adicionar-adiciona-domínio-controller.parameters.json][add-adds-domain-controller-parameters]**. Este ficheiro contém as definições para criar o domínio de *treyresearch.com* que abrangem os servidores de adiciona. Utiliza as extensões personalizadas que estabelecer o domínio e adicionar os servidores de adiciona à mesma. A não ser que crie servidores adiciona adicionais (caso em deverá adicioná-los para o `vms` matriz), altere os seus nomes de predefinido ou pretende criar um domínio com um nome diferente, não precisa de modificar este ficheiro.

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Este ficheiro contém as definições utilizadas para criar o gateway Azure VPN na nuvem utilizada para ligar à rede no local. Deverá modificar o `sharedKey` valor na `connectionsSettings` secção para que correspondam às que o dispositivo VPN no local. Para obter mais informações, consulte o artigo [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN][hybrid-azure-on-prem-vpn].

- ** [dmz private.parameters.json] [ dmz-private-parameters] ** e ** [dmz public.parameters.json ] [ dmz-public-parameters] **. Configurar a estes ficheiros (público) entrado e saídos lados (privados) dos VMs que compõem a DMZ, proteger os servidores na nuvem. Para mais informações sobre estes elementos e a respectiva configuração, consulte o artigo [Implementar uma DMZ entre Azure e a Internet][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer web.parameters.json][loadBalancer-web-parameters]**, ** [loadBalancer biz.parameters.json][loadBalancer-biz-parameters]**, e ** [loadBalancer data.parameters.json][loadBalancer-data-parameters]**. Estes ficheiros de parâmetros contêm as especificações VM para as camadas de acesso web, empresas e dados e configure balanceadores de carga para cada camada. Estes são os VMs que alojam aplicações web e bases de dados e executar as cargas de trabalho de negócio para a organização. VMs na camada web são adicionados ao domínio na nuvem ao utilizar as definições especificadas na ** [web-vm domínio join.parameters.json] [ web-vm-domain-join-parameters] ** ficheiro.

    Cada ficheiro contém dois conjuntos de parâmetros de configuração. O `virtualMachineSettings` secção define os VMs que alojam o serviço ADFS na nuvem. Por predefinição, o script cria ao dois destes VMs no mesmo conjunto de disponibilidade. Os seguintes fragmentos mostram as partes relevantes do ficheiro *loadBalancer web.parameters.json* :

    ```json
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-adtrust-web",
        "computerNamePrefix": "web",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "false",
            "subnetName": "web",
            "privateIPAllocationMethod": "Dynamic",
            "isPrimary": "true",
            "enableIPForwarding": false,
            "dnsServers": [ ]
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 1,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": "ra-adtrust-web-vm-as"
        }
      }
    },
    ...
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 2,
        "vmCount": 2,
        "vmStartIndex": 1
      }
    }
    ````
    Pode modificar os tamanhos e o número de VMs em cada camada de acordo com os requisitos de.

    O `loadBalancerSettings` secção fornece a descrição do Balanceador de carga para estas VMs. O Balanceador de carga transmite tráfego que aparece no porta 80 (HTTP) e a porta 443 (HTTPS) para um ou outro as VMs. 

    >[AZURE.NOTE] A regra para a porta 80 utiliza uma ligação TCP em vez de HTTP. Isto acontece porque a instalação do IIS na camada web está configurada para suportar uma autenticação do Windows apenas. Autenticação anónima é desativada. Tentar *ping* porta 80 através de uma ligação de HTTP falha com um erro 401 (não autorizada), Considerando que apenas a utilizar uma ligação de TCP Deteta se a porta está activa:

    ```json
    "loadBalancerSettings": {
      "value": {
        "name": "ra-adtrust-web-lb",
        "frontendIPConfigurations": [
          {
            "name": "ra-adtrust-web-lb-fe",
            "loadBalancerType": "internal",
            "internalLoadBalancerSettings": {
              "privateIPAddress": "10.0.1.254",
              "subnetName": "web"
            }
          }
        ],
        "backendPools": [
          {
            "name": "ra-adtrust-web-lb-bep",
            "nicIndex": 0
          }
        ],
        "loadBalancingRules": [
          {
            "name": "http-rule",
            "frontendPort": 80,
            "backendPort": 80,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "http-probe",
            "enableFloatingIP": false
          },
          {
            "name": "https-rule",
            "frontendPort": 443,
            "backendPort": 443,
            "protocol": "Tcp",
            "backendPoolName": "ra-adtrust-web-lb-bep",
            "frontendIPConfigurationName": "ra-adtrust-web-lb-fe",
            "probeName": "https-probe",
            "enableFloatingIP": false
          }
        ],
        "probes": [
          {
            "name": "http-probe",
            "port": 80,
            "protocol": "Tcp",
            "requestPath": null
          },
          {
            "name": "https-probe",
            "port": 443,
            "protocol": "Tcp",
            "requestPath": null
          }
        ],
        "inboundNatRules": [ ]
      }
    }
    ```

- ** [virtualMachines mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Este ficheiro contém a configuração de salto caixa/gestão VMs. Só é possível conseguir início de sessão e acesso administrativo ao VMs na web, empresas e camadas de dados a partir da caixa de hiperligação. Por predefinição, o script cria uma única *Standard_DS1_v2* VM, mas pode modificar este ficheiro para criar VMs maiores ou adicionais se a carga de trabalho gestão é provável que seja significativo.

A configuração também utiliza o [trust.ps1 de envio de] [ outgoing-trust] script mostrado abaixo para criar uma fidedignidade de saída unidirecional com o domínio *contoso.com* :

```powershell
# prerequiste: 
# You need to first run incoming-trust.ps1 in ra-adtrust-onpremise-ad-vm1 (ip 192.168.0.4)
# Then,
# Run the following powershell script in ra-adtrust-ad-vm1 (ip 10.0.4.4)

$TrustedDomainName = "contoso.com"
$TrustedDomainDnsIpAddresses = "192.168.0.4,192.168.0.5"

#$TrustingDomainName = "treyresearch.com"
#$TrustingDomainDnsIpAddresses = "10.0.4.4,10.0.4.5"

$ForwardIpAddress  = $TrustedDomainDnsIpAddresses
$ForwardDomainName = $TrustedDomainName

$IpAddresses = @()
foreach($address in $ForwardIpAddress.Split(',')){
    $IpAddresses += [IPAddress]$address.Trim()
}
Add-DnsServerConditionalForwarderZone -Name "$ForwardDomainName" -ReplicationScope "Domain" -MasterServers $IpAddresses

#netdom trust $TrustingDomainName /d:$TrustedDomainName /add
```

Este script é semelhante ao script de *receção trust.ps1* descrito anteriormente. Adiciona os endereços IP da no local servidores AD DS para o serviço DNS local e, em seguida, utiliza o [netdom] [ netdom] comando para criar a relação de confiança envio.

## <a name="solution-deployment"></a>Implementação da solução

A solução pressupõe os pré-requisitos seguintes:

- Tem uma subscrição existente do Azure na qual pode criar grupos de recursos.

- Ter transferiu e instalou a compilação mais recente do Azure Powershell. Consulte o artigo [aqui] [ azure-powershell-download] para obter instruções.

Para executar o script que implementar a solução:

1. Mover para uma pasta conveniente no seu computador local e crie as seguintes subpastas:

    - Scripts

    - Scripts/parâmetros

    - Parâmetros/scripts/Onpremise

    - Parâmetros/scripts/Azure

2. Transferir o [Implementar ReferenceArchitecture.ps1] [ solution-script] ficheiro para a pasta de Scripts

3. Transferir o conteúdo da [parâmetros/onpremise] [ on-premises-folder] pasta para a pasta de Scripts/parâmetros/Onpremise:

4. Transferir o conteúdo da [parâmetros/azure] [ azure-folder] pasta para a pasta de Scripts/parâmetros/Azure.

5. Editar o ficheiro de implementar ReferenceArchitecture.ps1 na pasta Scripts e alterar as linhas seguintes para especificar os grupos de recursos que devem ser criados ou utilizados para manter os recursos criados pelo script:

    ```powershell
    # Azure Onpremise Deployments
    $onpremiseNetworkResourceGroupName = "ra-adtrust-onpremise-rg"

    # Azure ADDS Deployments
    $azureNetworkResourceGroupName = "ra-adtrust-network-rg"
    $workloadResourceGroupName = "ra-adtrust-workload-rg"
    $securityResourceGroupName = "ra-adtrust-security-rg"
    $addsResourceGroupName = "ra-adtrust-adds-rg"
    ```

6. Edite os ficheiros de parâmetro nos parâmetros/Scripts/Onpremise e as pastas de parâmetros/Scripts/Azure. Atualize as referências de grupo de recursos nestes ficheiros para corresponder aos nomes dos grupos de recursos atribuídos a variáveis no ficheiro ReferenceArchitecture.ps1 implementar. A tabela seguinte mostra os ficheiros que parâmetro referenciam o grupo de recursos. Os grupos de recursos *RT-adfs carga de trabalho rg*, *RT adfs-segurança rg*, *RT-adfs-adiciona-rg*, *RT-adfs adfs rg*e *RT-adfs proxy rg* só são utilizados no script do PowerShell e não ocorre nos ficheiros do parâmetro.

  	|Grupo de recursos|Ficheiros de parâmetro|
  	|--------------|--------------|
  	|RT-adtrust onpremise rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RT adtrust-rede rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-Biz.Parameters.JSON<br />parameters\azure\loadBalancer-data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*duas ocorrências*)

    Para além disso, definir a configuração para no local e na nuvem componentes, tal como descrito em [Componentes da solução] [ solution-components] secção.

7. Abrir uma janela do Azure PowerShell, mover para a pasta de Scripts e execute o seguinte comando:

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Substituir `<subscription id>` com o seu ID de subscrição Azure.

    Para `<location>`, especifique uma região Azure, tal como `eastus` ou `westus`.

    O `<mode>` parâmetro pode ter um dos seguintes valores:

    - `Onpremise`, para criar o simulada ambiente no local.

    - `Infrastructure`, para criar a infraestrutura de VNet e saltar caixa na nuvem.

    - `CreateVpn`, para criar o gateway de rede virtual Azure e ligá-lo à rede no local.

    - `AzureADDS`, para construir VMs serve como adiciona servidores, implementar o Active Directory para estes VMs e criar o domínio na nuvem.

    - `WebTier`, que cria web camada VMs e Balanceador de carga.

    - `Prepare`, executa a todas as tarefas anteriores. **Este é a opção recomendada se estiver a criar uma implementação completamente nova e não tiver uma infraestrutura no local existente.**

    - `Workload`Para criar a camada de negócio e dados VMs e carregar balanceadores. Estes VMs não estão incluídos como parte da `Prepare` opção.

    >[AZURE.NOTE] Se utilizar o `Prepare` opção, o script leva-o até várias horas para concluir.

8.  Se estiver a utilizar a configuração do exemplo no local:

    1. Ligar para a caixa de hiperligação (*RT adtrust-gestão vm1* no grupo de recursos *RT adtrust-segurança rg* ). Inicie sessão como *utilizadorteste* com palavra-passe *AweS0me@PW*.

    2.  Na caixa de salto de abrir uma sessão de RDP na primeira VM no domínio de *contoso.com* (o domínio no local). Este VM tem o endereço IP 192.168.0.4. O nome de utilizador é *contoso\testuser* com palavra-passe *AweS0me@PW*.

    3. Transferir o [receção trust.ps1] [ incoming-trust] script e executá-la para criar a fidedignidade de entrada do domínio *treyresearch.com* .

9. Se estiver a utilizar o seu próprio infraestrutura no local:

    1. Transferir o [receção trust.ps1] [ incoming-trust] script.

    2. Editar o script e substitua o valor da `$TrustedDomainName` variável com o nome do seu próprio domínio.

    3. Execute o script.

10. A partir da caixa de hiperligação, ligue para a primeira VM no domínio de *treyresearch.com* (o domínio na nuvem). Este VM tem o endereço IP 10.0.4.4. O nome de utilizador é *treyresearch\testuser* com palavra-passe *AweS0me@PW*.

11. Transferir o [trust.ps1 de envio de] [ outgoing-trust] script e executá-la para criar a fidedignidade de entrada do domínio *treyresearch.com* . Se estiver a utilizar o seus próprio máquinas no local, em seguida, edite o script pela primeira vez. Definir o `$TrustedDomainName` variável para o nome do seu domínio no local e especifique os endereços IP dos servidores de AD DS para este domínio na `$TrustedDomainDnsIpAddresses` variável.

12. Num computador no local, execute os passos descritos no artigo [verificar uma confiança] [ verify-a-trust] para determinar se tem corretamente configurada a relação de confiança entre os domínios *contoso.com* e *treyresearch.com* . Poderá ter de aguardar alguns minutos depois de concluir os passos anteriores antes da fidedignidade pode ser validados face.

Os passos opcionais restantes mostram como determinar se a fidedignidade domínio está a funcionar como esperado. Estes passos exigem que tem acesso a um computador de desenvolvimento com Visual Studio instalada.

1.  A partir da janela do Azure PowerShell, execute o seguinte comando para criar a camada web se não configurou-anteriormente (utilizando o `Prepare` opção):

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> WebTier
    ```

    Este comando cria a camada de web e adiciona as VMs aos *treyresearch.com* do domínio.

2. Utilizar o Visual Studio no computador desenvolvimento, crie uma aplicação Web do ASP.NET denominada *TreyResearchWebApp*. Utilize o .NET Framework 4.5.2.

3. Selecione o modelo *MVC* e alterar a autenticação para *A autenticação do Windows*. Não crie uma aplicação de serviço na nuvem.

3. Criar e executar a aplicação para testar a autenticação. Certifique-se de que o seu nome de utilizador do Windows atual é apresentado na barra de menus na parte superior da página, para a direita.

4. Feche o Internet Explorer.

5. Na janela do *Explorer solução* , com o botão direito do projecto TreyResearchWebApp, clique em *Publicar*.

6. Na janela de *Publicar Web* , clique em *personalizado*. Crie um perfil personalizado com o nome *TreyResearchWebApp*.

7. Na página da *ligação* , defina o *método de publicar* para *Sistema de ficheiros* e especifique uma pasta denominada *TreyResearchWebApp*, numa localização conveniente no seu computador de desenvolvimento.

8. Na página *Definições* , defina a *configuração* para *edição*.

9. Na página de *pré-visualização* , clique em *Publicar*.

10. Ligar a cada VM na camada web sucessivamente (através da caixa de hiperligação) e efetuar as seguintes tarefas. Os endereços IP dos web camada VMs são 10.0.1.4 e 10.0.1.5. O nome de utilizador para ambas as VMs é *treyresearch\testuser* com palavra-passe *AweS0me@PW*:

    1. Copie a pasta *TreyResearchWebApp* e os seus conteúdos a partir do computador de desenvolvimento para a pasta *C:\inetpub* .

    2. Utilizar a consola do Gestor de serviços de informação Internet (IIS), navegue até ao *Web Site da Sites\Web* no computador.

    3. No painel de *ações* , clique em *Definições básicas*e altere o caminho físico do web site para *%SystemDrive%\inetpub\TreyResearchWebApp*.

    4. No painel de *Vista de funcionalidades* , faça duplo clique em *autenticação*. Verifique se *A autenticação do Windows* está ativado e *Autenticação anónima* está desativado.

11. a partir de um computador no local, abra o Internet Explorer e navegue para o web site em http://10.0.1.254 (este é o endereço do Balanceador de carga camada web).

12. Na caixa de diálogo *Segurança do Windows* , introduza as credenciais de um utilizador no domínio no local. Especifique um nome de utilizador que não existe no domínio de *treyresearch* . Se estiver a utilizar o simulada ambiente no local criar primeiro um utilizador no domínio *contoso* e especifique as credenciais deste utilizador.

13. Quando for apresentada a home page, certifique-se de que o domínio correto e o nome de utilizador aparecem na barra de menus na parte superior da página, para a direita.

## <a name="next-steps"></a>Próximos passos

- Aprender as melhores práticas para [Expandir o seu domínio de adiciona no local ao Azure][adds-extend-domain]

- Aprender as melhores práticas para [criar uma infraestrutura ADFS] [ adfs] no Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[implementing-aad]: ./guidance-identity-aad.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[running-VMs-for-an-N-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[naming-conventions]: ./guidance-naming-conventions.md
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[netdom]: https://technet.microsoft.com/library/cc835085.aspx
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-identity-adds-trust/parameters/onpremise/connection.parameters.json
[incoming-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/incoming-trust.ps1
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/extensions/outgoing-trust.ps1
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adds-trust/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-adds.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/add-adds-domain-controller.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/dmz-public.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/virtualMachines-mgmt.parameters.json
[web-vm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adds-trust/parameters/azure/web-vm-domain-join.parameters.json
[solution-components]: [#solution_components]
[0]: ./media/guidance-identity-aad-resource-forest/figure1.png "Seguro arquitetura de rede híbrido com os domínios do AD separadas"