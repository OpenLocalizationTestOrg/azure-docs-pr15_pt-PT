<properties
   pageTitle="Recomendado convenções de nomenclatura para recursos Azure | Orientações | Microsoft Azure"
   description="Convenções de nomenclatura recomendadas para recursos Azure. Como atribuir um nome máquinas virtuais, contas de armazenamento, redes, redes virtuais, sub-redes e outras entidades Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/27/2016"
   ms.author="christb"/>
   
# <a name="recommended-naming-conventions-for-azure-resources"></a>Convenções de nomenclatura recomendadas para recursos Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

A escolha de um nome para qualquer recurso no Microsoft Azure é importante porque:

- É difícil alterar um nome mais tarde.
- Nomes tem de cumprir os requisitos do respetivo tipo de recurso específico.

Convenções de nomenclatura consistentes tornar recursos mais fáceis de localizar. Pode também indicar a função de um recurso numa solução.

Este artigo é um resumo das regras de nomenclatura e restrições para recursos Azure e um conjunto de linha de base de recomendações para convenções de nomenclatura.  Pode utilizar estas recomendações como ponto de partida para o seus próprio convenções específicas para as suas necessidades.  

A chave para o sucesso com convenções de nomenclatura é estabelecer e segui-los entre as suas organizações e aplicações. 

## <a name="naming-subscriptions"></a>Atribuição de nomes de subscrições

Quando nomear subscrições Azure, nomes verbosas certifique Noções sobre o contexto e a finalidade de cada subscrição limpar.  Quando trabalha num ambiente com muitas subscrições, a seguir uma convenção de nomenclatura partilhada pode melhorar clareza.

Um padrão recomendado para atribuição de nomes de subscrições é:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Empresa, normalmente, serão os mesmos para cada subscrição. No entanto, algumas empresas poderão ter empresas subordinado dentro da estrutura organizacional. Essas empresas podem ser geridas por um grupo de TI central. Nestes casos, poderá diferenciar por ter o nome da empresa de elemento principal (*Contoso*) e o nome da empresa subordinado (*Vento América do Norte*).

- Departamento é um nome dentro da empresa onde um grupo de indivíduos trabalhar. Este item dentro do espaço de nomes como opcionais.

- Linha de produto é um nome para um produto ou a função que é executada a partir do departamento de específico.
Isto é geralmente opcional para direcionados serviços e aplicações. No entanto, recomenda-se vivamente para utilizar para serviços de direccionada que requerem separação fácil e identificação (tal como limpar separação da faturação registos).

- Ambiente é o nome que descreva o ciclo de vida de implementação de aplicações ou serviços, como Dev Center, as perguntas e respostas ou ordem de produção.

| Empresa | Departamento | Linha de produto ou serviço | Ambiente | Nome completo  |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Produção | Produção de AwesomeService contoso SocialGaming |
| Contoso | SocialGaming | AwesomeService | Dev Center | Dev Center do contoso SocialGaming AwesomeService |
| Contoso | -LO | InternalApps | Produção | Contoso IT InternalApps produção |
| Contoso | -LO | InternalApps | Dev Center | Contoso IT Dev Center do InternalApps |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc. -->

## <a name="use-affixes-to-avoid-ambiguity"></a>Utilizar apõe para evitar a ambiguidade

Quando nomear recursos no Azure, é recomendável utilizar comuns prefixos ou sufixos para identificar o tipo e o contexto do recurso.  Enquanto todas as informações sobre o tipo, metadados, contexto, está disponível através de programação, aplicar apõe comuns simplifica identificação visual.  Quando incorporar apõe na sua Convenção de nomenclatura, é importante claramente Especifique se a apor é no início do nome (prefixo) ou no final (sufixo).  

Por exemplo, aqui estão dois nomes possíveis para um serviço de alojamento um motor de cálculo:

- SvcCalculationEngine (prefixo)
- CalculationEngineSvc (sufixo)

Apõe pode consultar a aspetos diferentes que descrevem os recursos específicos. A tabela seguinte mostra alguns exemplos normalmente utilizados.

| Altura | Exemplo | Notas |
| ------ | ------- | ----- |
| Ambiente | Dev Center, ordem de produção, as perguntas e respostas | Identifica o ambiente do recurso |
| Localização | UW (-nos Ocidental), ue (Oriente-nos) | Identifica a região no qual o recurso é implementado |
| Instância | 01, 02 | Para recursos que têm mais do que uma instância com nome (servidores web, etc.). |
| Produto ou serviço | serviço | Identifica o produto, aplicação ou serviço que suporta o recurso |
| Função | SQL, da web, mensagens | Identifica a função do recurso associado |

Quando desenvolver uma convenção de nomenclatura específica para a sua empresa ou projetos, é importante escolher um conjunto comum de apõe e respetiva posição (sufixo ou prefixo).

## <a name="naming-rules-and-restrictions"></a>Regras de nomenclatura e restrições

Cada tipo de recurso ou serviço no Azure impõe um conjunto de nomenclatura restrições e âmbito; qualquer convenção de nomenclatura ou um padrão tem de aderir a regras de nomenclatura necessárias e âmbito.  Por exemplo, enquanto o nome de uma VM mapas a um nome DNS (e, por conseguinte, é necessário para ser exclusivo em todos Azure), o nome de um VNET está confinado ao grupo de recursos ter sido criada dentro.

Em geral, não ter quaisquer caracteres especiais (`-` ou `_`) como o primeiro ou último caráter em qualquer nome. Destes carateres causará a maioria das regras de validação a falha.

| Categoria | Serviço ou entidade | Âmbito | Comprimento | Invólucro | Carateres válidos | Padrão sugerida | Exemplo |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Grupo de recursos | Grupo de recursos | Global | 1-64 | Maiúsculas e minúsculas | Alfanumérico, sublinhado e hífen | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Grupo de recursos | Conjunto de disponibilidade | Grupo de recursos | 1-80 | Maiúsculas e minúsculas | Alfanumérico, sublinhado e hífen | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Geral | Etiqueta | Entidade associada | 512 (nome), 256 (valor) | Maiúsculas e minúsculas | Alfanuméricos | `"key" : "value"` | `"department" : "Central IT"` |
| Cluster | Máquina virtual | Grupo de recursos | 1-15 | Maiúsculas e minúsculas | Alfanumérico, sublinhado e hífen | `<name>-<role>-<instance>` | `profx-sql-001` |
| Armazenamento | Nome de conta de armazenamento (dados) | Global | 3-24 | Minúsculas | Alfanuméricos | `<service short name><type><number>` | `profxdata001` |
| Armazenamento | Nome de conta de armazenamento (discos) | Global | 3-24 | Minúsculas | Alfanuméricos | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Armazenamento | Nome do contentor | Conta de armazenamento | 3-63 |   Minúsculas | Alfanumérico e travessão | `<context>` | `logs` |
| Armazenamento | Nome de BLOBs | Contentor | 1-1024 | Maiúsculas e minúsculas | Qualquer CARÁCT de URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Armazenamento | Nome da fila | Conta de armazenamento | 3-63 | Minúsculas | Alfanumérico e travessão | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Armazenamento | Nome da tabela | Conta de armazenamento | 3-63 |Maiúsculas e minúsculas | Alfanuméricos | `<service short name>-<context>` | `awesomeservice-logs` |
| Armazenamento | Nome do ficheiro | Conta de armazenamento | 3-63 | Minúsculas | Alfanuméricos | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Funcionamento em rede | Rede virtual (VNet) | Grupo de recursos | 2-64 | Maiúsculas e minúsculas | Alfanumérico, traço, sublinhado e período | `<service short name>-[section]-vnet` | `profx-vnet` |
| Funcionamento em rede | Sub-rede | VNet principal | 2-80 | Maiúsculas e minúsculas | Alfanumérico, sublinhado, traço e período | `<role>-subnet` | `gateway-subnet` |
| Funcionamento em rede | Interface da rede | Grupo de recursos | 1-80 | Maiúsculas e minúsculas | Alfanumérico, traço, sublinhado e período | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Funcionamento em rede | Grupo de segurança de rede | Grupo de recursos | 1-80 | Maiúsculas e minúsculas | Alfanumérico, traço, sublinhado e período | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Funcionamento em rede | Regra de grupo de segurança de rede | Grupo de recursos | 1-80 | Maiúsculas e minúsculas | Alfanumérico, traço, sublinhado e período | `<descriptive context>` | `sql-allow` |
| Funcionamento em rede | Endereço IP público | Grupo de recursos | 1-80 | Maiúsculas e minúsculas | Alfanumérico, traço, sublinhado e período | `<vm or service name>-pip` | `profx-sql1-pip` |
| Funcionamento em rede | Balanceador de carga | Grupo de recursos | 1-80 | Maiúsculas e minúsculas | Alfanumérico, traço, sublinhado e período | `<service or role>-lb` | `profx-lb` |
| Funcionamento em rede | Carregar Config regras desequilibradas | Balanceador de carga | 1-80 | Maiúsculas e minúsculas | Alfanumérico, traço, sublinhado e período | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | TODO | TODO
-->

## <a name="organizing-resources-with-tags"></a>Organizar recursos com etiquetas

O Gestor de recursos do Azure suporta as entidades com cadeias de texto arbitrário para identificar o contexto e simplificar a automatização.  Por exemplo, a etiqueta `"sqlVersion: "sql2014ee"` poderia identificar VMs numa implementação a executar o SQL Server 2014 Enterprise Edition para executar um script automatizado contra-los.  Etiquetas devem ser utilizadas para aumentar e melhorar o contexto ao longo do lado das convenções de nomenclatura escolhido.

> [AZURE.TIP]Uma outra vantagem de etiquetas é que etiquetas abrangem grupos de recursos, permitindo-lhe ligar e correlacionar entidades através de implementações diferentes.

Cada recurso ou grupo de recursos pode ter um máximo de etiquetas de **15** . O nome da etiqueta está limitado à 512 carateres e o valor de etiqueta é limitado a 256 carateres.

Para mais informações sobre recursos etiquetagem, consulte [utilizar etiquetas para organizar os recursos do Azure](../resource-group-using-tags.md).

Alguns dos casos de utilização de etiquetas de rede comuns são:

- **Faturação**; Agrupamento de recursos e associando-os com faturação ou o encargo de trás códigos.
- **Serviço contexto identificação**; Identificar os grupos de recursos ao longo de grupos de recursos para operações de comuns e agrupamento
- **Controlo de acesso e contexto de segurança**; Identificação de função administrativa com base no portefólio, sistema, serviço, aplicação, instância, etc.

> [AZURE.TIP]Marca mais cedo - etiqueta com frequência.  Melhor ter um plano base etiquetagem de esquema no local e ajustar ao longo do tempo em vez de ter de actualizar após o facto de.  

Um exemplo de algumas abordagens de etiquetas de rede comuns:

| Nome de etiqueta | Chave | Exemplo | Comentário |
| -------- | --- | ------- | ------- |
| Fatura para / interno encargos ID | CobrarA:  | `IT-Chargeback-1234` | Uma interno e/s ou código de faturação |
| Operador ou diretamente responsável individuais (DRI) | managedBy | `joe@contoso.com`  | Alias ou endereço de e-mail |
| Nome do projeto | nome do projeto | `myproject`  | Nome da linha de projeto ou de produto |
| Versão do Project | versão do Project | `3.4`  | Versão da linha de projeto ou produto |
| Ambiente | ambiente | `<Production, Staging, QA >` | Identificador ambiental | 
| Camada | camada | `Front End, Back End, Data` | Identificação camada ou função/contexto |
| Perfil de dados | dataProfile | `Public, Confidential, Restricted, Internal` | Sensibilidade dos dados armazenados num recurso |
 
## <a name="tips-and-tricks"></a>Sugestões e truques

Alguns tipos de recursos podem exigir cuidados adicionais no convenções de nomenclatura e.

### <a name="virtual-machines"></a>Máquinas virtuais

Sobretudo no maiores topologias, cuidadosamente nomenclatura máquinas virtuais simplifica o identificar o papel e a finalidade de cada máquina e ativar o scripting mais previsíveis.

> [AZURE.WARNING]Cada máquina virtual no Azure tem um nome de recurso Azure e um nome de anfitrião do sistema operativo.  
> Se o nome do recurso e o nome de anfitrião forem diferentes, gerir os VMs poderá ser um desafio e deve ser evitada.
> Por exemplo, se uma máquina virtual criada a partir de um VHD que já contém um sistema operativo configurado com um nome de anfitrião.

- [Convenções de nomenclatura para o Windows Server VMs](https://support.microsoft.com/en-us/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

### <a name="storage-accounts-and-storage-entities"></a>Contas de armazenamento e entidades de armazenamento

Existem dois casos de utilização principal para contas de armazenamento - efetuar cópias de segurança discos para VMs e armazenar os dados em tabelas, filas e blobs.  Contas de armazenamento utilizadas para discos VM devem seguir a Convenção de nomenclatura de associá-los com o nome VM principal (e com a necessidade de potencial para múltiplas contas de armazenamento de alto nível VM SKUs, também se aplica um sufixo do número).

> [AZURE.TIP]Contas de armazenamento - para dados ou discos - devem seguir Convenção de nomenclatura que permite a várias contas de armazenamento ser utilizadas (ou seja, sempre utilizando um sufixo numérico).

É possível configurar um nome de domínio personalizado para aceder a dados de BLOBs na sua conta de armazenamento do Windows Azure.
O ponto final predefinido para o serviço de BLOBs é `https://mystorage.blob.core.windows.net`.

Mas se mapear um domínio personalizado (por exemplo, www.contoso.com) para o ponto final blob para a sua conta de armazenamento, também pode aceder a dados na sua conta de armazenamento de blob ao utilizar esse domínio. Por exemplo, com um nome de domínio personalizado, `http://mystorage.blob.core.windows.net/mycontainer/myblob` pôde ser acedido como `http://www.contoso.com/mycontainer/myblob`.

Para obter mais informações sobre como configurar esta funcionalidade, consulte [configurar um nome de domínio personalizado para o ponto final de armazenamento de BLOBs](../storage/storage-custom-domain-name.md).

Para mais informações sobre como atribuir nomes a blobs, contentores e tabelas:

- [Atribuição de nomes e referenciar contentores, Blobs e metadados](https://msdn.microsoft.com/library/dd135715.aspx)
- [Atribuir nomes a filas e metadados](https://msdn.microsoft.com/library/dd179349.aspx)
- [Atribuição de nomes de tabelas](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Um nome de BLOBs pode conter uma combinação de carateres, mas caracteres de URL reservados tem corretamente escape. Evitar nomes de BLOBs que terminam com um ponto (.), uma barra (/), ou uma sequência ou uma combinação das duas. Por convenção, a barra é o separador de diretório **virtual** . Não utilize uma barra invertida (\) num nome de Blobs. O cliente APIs poderá permiti-lo, mas a hash corretamente, em seguida, falhar, e as assinaturas não correspondem.

Não é possível modificar o nome de uma conta de armazenamento ou um contentor após ter sido criada.
Se quiser utilizar um novo nome, tem de eliminá-la e crie um novo.

> [AZURE.TIP] Recomendamos que estabelecer uma convenção de nomenclatura para todas as contas de armazenamento e tipos de antes de embarque no desenvolvimento de um novo serviço ou aplicação.

## <a name="example---deploying-an-n-tier-service"></a>Exemplo - implementar um serviço de camadas

Neste exemplo, vamos definir uma configuração de serviço de camadas, que consiste servidores front-end IIS (hospedados no Windows Server VMs), com o SQL Server (hospedados na duas VMs de servidor do Windows), um cluster de ElasticSearch (hospedado na 6 Linux VMs) e associadas de armazenamento contas, redes virtuais, recursos de grupo e Balanceador de carga.

Vamos começar ao definir as convenções contextuais para esta aplicação:

| Entidade | Convenção | Descrição  |
| ------ | ---------- | ------------ |  
| Nome do serviço | `profx` | O nome abreviado da aplicação ou serviço que está a ser implementada |
| Ambiente | `prod` | Destina-se para a implementação de produção (por oposição a das perguntas e respostas, teste, etc.) |

A partir do plano base podemos pode, em seguida, mapear saída convenções para cada um dos tipos de recursos:

| Tipo de recurso | Base da Convenção | Exemplo |
| ------------- | --------------- | ------- |
| Subscrição | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Grupo de recursos | `servicename-rg` | `profx-rg` |
| Rede virtual | `servicename-vnet` | `profx-vnet` |
| Sub-rede | `role-subnet` | `sql-vnet` |
| Balanceador de carga | `servicename-lb` | `profx-lb` |
| Máquina virtual | `servicename-role[number]` | `profx-sql0` |
| Conta de armazenamento | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Conforme visto no seguinte diagrama:

![diagrama de topologia da aplicação] (media/guidance-naming-conventions/guidance-naming-convention-example.png "Topologia da aplicação de exemplo")

## <a name="sample---azure-cli-script-for-deploying-the-sample-above"></a>Exemplo - script clip Azure para implementar o exemplo acima

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

    # Create the network interface card for this VM
    azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

    # Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
    azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
        --boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
    create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```
