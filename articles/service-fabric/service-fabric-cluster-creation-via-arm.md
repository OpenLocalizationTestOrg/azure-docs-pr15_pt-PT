
<properties
   pageTitle="Criar um cluster de serviço ferro seguro utilizando o Gestor de recursos do Azure | Microsoft Azure"
   description="Este artigo descreve como configurar um cluster de serviço ferro seguro no Azure através do Gestor de recursos do Azure, Cofre de chave do Azure e Azure Active Directory (AAD) para a autenticação de cliente."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Criar um cluster de serviço ferro no Azure utilizando o Gestor de recursos do Azure

> [AZURE.SELECTOR]
- [Gestor de recursos do Azure](service-fabric-cluster-creation-via-arm.md)
- [Portal do Azure](service-fabric-cluster-creation-via-portal.md)

Este é um guia passo a passo que o orienta-o através dos passos de configuração de um cluster de Azure Service ferro seguro no Azure utilizando o Gestor de recursos do Azure. Este guia orienta-o através dos seguintes passos:

 - Configure o Cofre chave para gerir chaves para cluster e aplicação de segurança.
 - Crie um cluster protegido no Azure com o Gestor de recursos do Azure.
 - Autenticar os utilizadores com o Azure Active Directory (AAD) para a gestão de cluster.

Um cluster seguro é um cluster que impede o acesso não autorizado a operações de gestão, que inclui implementar, atualizar e eliminar aplicações, serviços e os dados que contêm. Um cluster não seguro é um cluster de qualquer pessoa pode ligar a qualquer momento e executar operações de gestão. Apesar de é possível criar um cluster não seguro, é **vivamente recomendado que crie um cluster seguro**. Um cluster não seguro **não podem ser protegidos mais tarde** - um novo cluster têm de ser criado.

Os conceitos são os mesmos para criar clusters seguros, se o clusters são Linux clusters ou clusters do Windows. Para obter mais informações e helper scripts para criar seguros Linux clusters, consulte [criar clusters seguros no Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Este guia utiliza [Azure PowerShell][azure-powershell]. Ao iniciar uma nova sessão do PowerShell, inicie sessão na sua conta Azure e selecione a sua subscrição antes de executar o Azure comandos.

Inicie sessão na sua conta azure:

```powershell
Login-AzureRmAccount
```

Selecione a sua subscrição:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurar o Cofre chave

Esta secção explica criar cofre chave para um cluster de serviço ferro no Azure e para as aplicações de serviço ferro. Para um guia concluído na chave cofre, referir-se para o [Guia de introdução do cofre chave ao][key-vault-get-started].

Serviço ferro utiliza certificados x. 509 para proteger um cluster e fornecer funcionalidades de segurança da aplicação. Azure chave Cofre é utilizado para gerir certificados para clusters de serviço ferro no Azure. Quando um cluster é implementado no Azure, o fornecedor de recurso Azure responsável pela criação clusters de serviço ferro obtém certificados do cofre chave e instala-los num cluster VMs.

O diagrama seguinte ilustra a relação entre chave cofre, um cluster de serviço ferro e o fornecedor de recurso Azure que utiliza certificados armazenados num cofre de chave quando cria um cluster de:

![Instalação do certificado][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O primeiro passo é criar um grupo de recursos especificamente para a chave cofre. Colocar em Cofre chave para o seu próprio grupo de recursos é recomendado. Esta opção permite-lhe remover os grupos de recursos cluster e armazenamento, incluindo o grupo de recursos que tem o seu cluster de serviço ferro sem perder a sua teclas e segredos. O grupo de recursos que tenha Cofre de chave tem de ser na mesma região como cluster de que está a utilizar.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Criar cofre chave 

Crie um cofre chave no novo grupo de recursos. A chave cofre **tem de estar ativada para implementação** para permitir que o fornecedor de recurso ferro de serviço obter certificados do mesmo e instalar em nós de cluster:

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Se tiver um cofre chave existente, pode ativá-lo para implementação do Azure clip de utilizando:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>Adicione os certificados à chave Cofre

Certificados são utilizados em ferro de serviço para fornecer autenticação e encriptação para proteger vários aspetos das suas aplicações e um cluster. Para mais informações sobre como os certificados são utilizados no ferro de serviço, consulte o artigo [cenários de segurança do serviço ferro cluster][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e servidor (obrigatório) 

É necessário este certificado para um cluster de proteger e evitar acesso não autorizado ao mesmo. Fornece-lhe segurança cluster de algumas formas:
 
 - **Autenticação cluster:** Autentica nó a nó comunicação para a Federação cluster. Apenas os nós que podem revelar-se a sua identidade com este certificado podem aderir ao cluster.
 - **Autenticação de servidor:** Autentica os pontos finais da gestão de computação para um cliente de gestão, para que o cliente de gestão sabe que está a falar ao cluster real. Este certificado também fornece SSL para a gestão de HTTPS API e para o serviço ferro Explorer por HTTPS.

Para atender a estes objectivos, o certificado tem de cumprir os seguintes requisitos:

 - O certificado tem de conter uma chave privada.
 - O certificado deve ser criado para troca de chaves, exportável para um ficheiro de intercâmbio de informações pessoais (. pfx).
 - Nome de assunto o certificado tem de corresponder o domínio utilizado para aceder ao serviço ferro cluster. Este matchng é necessária para fornecer SSL para o cluster pontos finais de gestão de HTTPS e serviço ferro Explorer. Não consegue obter um certificado SSL de uma autoridade de certificação (AC) para o `.cloudapp.azure.com` domínio. É necessário adquirir um nome de domínio personalizado para o seu cluster. Quando pedir um certificado a partir de uma AC, nome de assunto o certificado tem de corresponder o nome de domínio personalizado utilizado para o seu cluster.

### <a name="application-certificates-optional"></a>Certificados de aplicação (opcionais)

Qualquer número de certificados adicionais pode ser instalado num cluster de aplicação por motivos de segurança. Antes de criar o seu cluster, considere os cenários de segurança de aplicação que requerem um certificado de ser instalada em nós, tais como:

 - Encriptação e desencriptar de valores de configuração da aplicação
 - Encriptação de dados em nós durante a replicação 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Certificados para o recurso Azure fornecedor utilizar a formatação

Ficheiros de chave privados (. pfx) podem ser adicionados e utilizados diretamente através da tecla cofre. No entanto, o fornecedor do recurso Azure requer chaves a ser armazenado num formato de JSON especial que inclui PFX como base-64 codificado cadeia e a palavra-passe da chave privada. Para acomodar estes requisitos, teclas devem ser colocadas numa cadeia JSON e, em seguida, armazenadas como *segredos* na chave cofre.

Para facilitar este processo, um módulo de PowerShell está [disponível no GitHub][service-fabric-rp-helpers]. Siga estes passos para utilizar o módulo:

  1. Transfira todo o conteúdo da repo para um diretório local. 
  2. Importe o módulo na janela do PowerShell:

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
O `Invoke-AddCertToKeyVault` comando neste módulo PowerShell automaticamente formata uma chave privada de certificado para uma cadeia JSON e carrega-lo de chave do cofre. Utilizá-lo para adicionar o certificado de cluster e quaisquer certificados de aplicação adicional à chave cofre. Repita este passo para qualquer certificados adicionais que pretende instalar no seu cluster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

As cadeias anteriores são todos os pré-requisitos cofre chave para configurar um modelo de Gestor de recursos de cluster de serviço ferro que instala certificados para autenticação nó, de segurança de ponto final de gestão e de autenticação e funcionalidades de segurança de qualquer aplicação adicional que utilizam x. 509 certificados. Neste momento, deverá ter agora a configuração seguinte no Azure:

 - Grupo de recursos do cofre chave
   - Cofre chave
     - Certificado de autenticação de servidor de cluster
     - Certificados de aplicação

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

AAD permite às organizações (conhecidas como inquilinos) para gerir o acesso de utilizador para aplicações, que estão divididas em aplicações com um login baseada na web IU e aplicações uma experiência de cliente nativa. Neste documento, podemos partem do princípio de que já criou um inquilino. Caso não esteja, comece por ler [como obter um inquilino do Azure Active Directory][active-directory-howto-tenant].

Um cluster de serviço ferro oferece vários pontos de entrada para a funcionalidade de gestão, incluindo o baseados na web e [Serviço ferro Explorer] [ service-fabric-visualizing-your-cluster] e o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, criar duas aplicações AAD para controlar o acesso ao cluster, uma aplicação web e uma aplicação nativa.

Para simplificar a alguns dos passos envolvidos na configuração AAD com um cluster de serviço ferro, criámos um conjunto de scripts do Windows PowerShell.

>[AZURE.NOTE] Tem de executar estes passos *antes de* criar o clustê-lo em casos onde os scripts esperar cluster nomes e os pontos finais, estas páginas devem ser os valores planeados, não fases, que já criadas.

1. [Transferir os scripts] [ sf-aad-ps-script-download] para o seu computador.

2. O ficheiro zip de contexto, selecione **Propriedades**, em seguida, selecione a caixa de verificação de **Desbloquear** e aplicar.

3. Extrai o ficheiro zip.

4. Executar `SetupApplications.ps1`, fornecendo-lhe o TenantId, nome de cluster e WebApplicationReplyUrl tão parâmetros. Por exemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Pode encontrar o seu **TenantId** executando o comando PowerShell ' Get-AzureSubscription ' '. Esta ação irá apresentar a **TenantId** para cada subscrição.

    O **nome de cluster** é utilizada para as aplicações de AAD criadas pelo script do prefixo. Não é necessário corresponder ao nome real do cluster exatamente como destina-se apenas para facilitar a mapear artefactos AAD para cluster ferro de serviço que está a ser utilizadas com.

    O **WebApplicationReplyUrl** é o ponto final predefinido que AAD devolve aos seus utilizadores depois de concluir o processo de início de sessão. Deve definir este para o ponto final de serviço ferro Explorer para o seu cluster, que, por predefinição, é:

    https://&lt;cluster_domain&gt;: 19080/Explorer

    Lhe for pedido para iniciar sessão para uma conta com privilégios administrativos para o inquilino AAD. Depois de o fazer, o script avança para criar a web e aplicações nativas para representar o seu cluster de serviço ferro. Se observe as aplicações do inquilino no [portal clássica Azure][azure-classic-portal], deverá ver duas novas entradas:

    - *Nome de cluster*\_Cluster
    - *Nome de cluster*\_cliente

    Imprime o script Json necessário pelo modelo de Gestor de recursos do Azure quando criar o cluster na secção seguinte por isso, manter o PowerShell abrir a janela.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Criar um modelo do serviço ferro cluster Gestor de recursos

Nesta secção, o resultado dos comandos do PowerShell anteriores são utilizados num modelo de Gestor de recursos de cluster ferro de serviço.

Modelos de exemplo Gestor de recursos estão disponíveis na [Galeria de modelos de guia Azure no GitHub][azure-quickstart-templates]. Estes modelos podem ser utilizados como ponto de partida para o modelo de cluster. 

### <a name="create-the-resource-manager-template"></a>Criar o modelo de Gestor de recursos

Este guia utiliza o [cluster seguro 5-node] [ service-fabric-secure-cluster-5-node-1-nodetype-wad] modelo de exemplo e parâmetros de modelo. Transferir `azuredeploy.json` e `azuredeploy.parameters.json` para o seu computador e abra ambos os ficheiros no seu editor de texto favorito.

### <a name="add-certificates"></a>Adicionar certificados

Certificados são adicionados a um modelo de Gestor de recursos de cluster ao referenciar no Cofre de chave que contém as teclas de certificado. Recomenda-se que estes valores chave cofre são colocados num ficheiro de parâmetros de modelo de Gestor de recursos para manter o Gestor de recursos reutilizável de ficheiro de modelo e libertar de valores específicos para uma implementação.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Adicionar todos os certificados para o osProfile VMSS

Todos os certificados que precisa de estar instalado no cluster tem de ser configurado na secção osProfile do recurso VMSS (Microsoft.Compute/virtualMachineScaleSets). Isto indica o fornecedor de recursos para instalar o certificado nas VMs. Isto inclui o certificado de computação, bem como qualquer certificados de segurança de aplicação que pretenda utilizar para as suas aplicações:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>Configurar um certificado cluster ferro de serviço

O certificado de autenticação cluster tem também de ser configurado no recurso de cluster de serviço ferro (Microsoft.ServiceFabric/clusters) e na extensão de serviço ferro para VMSS no recurso VMSS. Isto permite que o fornecedor de recursos do serviço ferro configurá-lo para ser utilizado para cluster de autenticação e server para os pontos finais de gestão.

##### <a name="vmss-resource"></a>Recurso de VMSS:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Recurso de ferro de serviço:

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>Inserir AAD config

A configuração de AAD criada anteriormente pode ser inserida diretamente no seu modelo de Gestor de recursos, no entanto recomenda-se para extrair os valores para parâmetros pela primeira vez para um ficheiro de parâmetros para manter o modelo de Gestor de recursos reutilizável e gratuito de valores específicos para uma implementação.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < um "Configurar-braço" ></a>parâmetros de modelo de configurar o Gestor de recursos

Por fim, utilize os valores de saída a partir dos comandos de chave do cofre e AAD PowerShell para preencher o ficheiro de parâmetros:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
Neste momento, deverá agora tem o seguinte procedimento:

 - Grupo de recursos do cofre chave
    - Cofre chave
    - Certificado de autenticação de servidor de cluster
    - Certificado de cifragem de dados
 - Inquilino do Azure Active Directory 
    - Aplicação AAD gestão baseada na web e serviço ferro Explorer
    - Aplicação AAD para gestão de clientes nativo
    - Utilizadores com funções atribuídas 
 - Modelo do serviço ferro cluster Gestor de recursos
    - Certificados configurada através da tecla Cofre
    - Azure Active Directory configurado 

O diagrama seguinte ilustra onde configuração cofre chave e AAD ajustar para o modelo de Gestor de recursos.

![Mapa de dependência de Gestor de recursos][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Criar o cluster

Está agora pronto para criar o cluster utilizando a [implementação de processador][resource-group-template-deploy].

#### <a name="test-it"></a>Testá-la

Utilize o seguinte comando do PowerShell para testar o seu modelo de Gestor de recursos com um ficheiro de parâmetros:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Implementá-lo

Se passar o teste de modelo de Gestor de recursos, utilize o seguinte comando do PowerShell para implementar o modelo de Gestor de recursos com um ficheiro de parâmetros:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>Atribuir utilizadores a funções

Depois de ter criado as aplicações para representar o seu cluster, é necessário atribuir os seus utilizadores para as funções suportadas pelo serviço ferro: só de leitura e de administrador. Pode fazê-lo utilizando o [portal clássica Azure][azure-classic-portal].

1. Navegue até ao seu inquilino e selecione aplicações.
2. Selecione a aplicação web, que tem um nome como `myTestCluster_Cluster`.
3. Clique no separador de utilizadores.
4. Selecione um utilizador para atribuir e clique no botão **atribuir** na parte inferior do ecrã.

    ![Atribuir aos utilizadores para o botão de funções][assign-users-to-roles-button]

5. Selecione a função para atribuir ao utilizador.

    ![Atribuir utilizadores a funções][assign-users-to-roles-dialog]

>[AZURE.NOTE] Para mais informações sobre funções no ferro de serviço, consulte o artigo [o controlo de acesso baseado em funções para clientes do serviço ferro](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>Criar clusters seguros no Linux

Para facilitar o processo, tem foi fornecido um script de ajuda [aqui](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Para utilizar este script helper, é considerada que já tiver clip Azure instalada e é no caminho. Certifique-se de que o script tem permissões para executar executando `chmod +x cert_helper.py` após transferi-lo. O primeiro passo consiste em inicie sessão na sua conta Azure utilizando o clip com o `azure login` comando. Depois de iniciar sessão na sua conta Azure, utilize a auxiliar juntamente com o certificado com a sessão iniciada AC, como mostra o comando seguinte:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Este comando devolve as seguintes três cadeias como o resultado: 

1. Um SourceVaultID, que é o ID para o novo KeyVault ResourceGroup-criado para si. 

2. Um CertificateUrl para aceder ao certificado.

3. Um CertificateThumbprint, que é utilizado para autenticação.


O exemplo seguinte mostra como utilizar o comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Executar o comando anterior fornece-lhe com as três cadeias da seguinte forma:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Nome de assunto o certificado tem de corresponder o domínio utilizado para aceder ao serviço ferro cluster. Isto é necessário para fornecer SSL para o cluster pontos finais de gestão de HTTPS e serviço ferro Explorer. Não consegue obter um certificado SSL de uma autoridade de certificação (AC) para o `.cloudapp.azure.com` domínio. É necessário adquirir um nome de domínio personalizado para o seu cluster. Quando pedir um certificado de uma AC nome de assunto o certificado tem de corresponder o nome de domínio personalizado utilizado para o seu cluster.

Estas são as entradas de necessárias para criar um cluster de ferro de serviço seguro (sem AAD), tal como descrito em [Gestor de recursos do configurar parâmetros de modelo](#configure-arm). Pode ligar ao cluster seguro através de instruções na [acesso de cliente para um cluster de autenticação](service-fabric-connect-to-secure-cluster.md). Clusters de pré-visualização de Linux não suportam a autenticação AAD. Pode atribuir funções de administrador e o cliente, tal como descrito na secção [atribuir funções de administrador a utilizadores](#assign-roles). Quando especificar funções de administrador e o cliente para um cluster de pré-visualização de Linux, tem de fornecer as miniaturas de certificado para autenticação (por oposição a nome de assunto, uma vez que está a ser executada sem validação de cadeia ou revogação desta versão da pré-visualização).


Se pretender utilizar um certificado autoassinado para testes, pode utilizar o mesmo script para gerar um certificado autoassinado e carregue-o para KeyVault, fornecendo o sinalizador `ss` em vez de fornecer o caminho de certificados e o nome do certificado. Por exemplo, consulte o seguinte comando para criar e carregar um certificado autoassinado:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Este comando devolve as três cadeias mesmo, SourceVault, CertificateUrl e CertificateThumbprint, que é utilizado para criar um cluster de Linux seguro, juntamente com a localização onde o certificado autoassinado foi colocado. Terá do certificado autoassinado para ligar ao cluster.  Pode ligar ao cluster seguro através de instruções na [acesso de cliente para um cluster de autenticação](service-fabric-connect-to-secure-cluster.md). Nome de assunto o certificado tem de corresponder o domínio utilizado para aceder ao serviço ferro cluster. Isto é necessário para fornecer SSL para o cluster pontos finais de gestão de HTTPS e serviço ferro Explorer. Não consegue obter um certificado SSL de uma autoridade de certificação (AC) para o `.cloudapp.azure.com` domínio. É necessário adquirir um nome de domínio personalizado para o seu cluster. Quando pedir um certificado de uma AC nome de assunto o certificado tem de corresponder o nome de domínio personalizado utilizado para o seu cluster.

Os parâmetros fornecidos pelo helper script podem ser preenchidos no portal do conforme descrito na secção [criar um cluster no portal do Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Próximos passos

Neste momento, que tem um cluster seguro com autenticação de gestão de fornecer Azure Active Directory. A seguinte, [ligar ao seu cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [Gerir segredos de aplicação](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Resolver problemas de configurar o Azure Active Directory para autenticação de cliente

Se ocorrer um problema ao configurar o Azure Active Directory para autenticação de cliente, reveja a seguinte suggestings para potenciais soluções.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Pedidos de serviço ferro Explorer para selecionar o certificado

#### <a name="problem"></a>Problema

Depois de início de sessão com êxito na página de início de sessão do AAD no serviço ferro Explorer, browser devolve à home page mas pede-lhe uma caixa de diálogo para selecionar um certificado.

![Caixa de diálogo do SFX certificado select][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo

O utilizador não foi atribuído uma função na aplicação de cluster AAD. Por conseguinte, a autenticação de AAD falha num cluster de serviço ferro. Serviço ferro Explorer reverte para autenticação de certificados.

#### <a name="solution"></a>Solução

Siga as instruções de configuração de AAD e atribuir funções de utilizador. Além disso, "Utilizador atribuição obrigatório a aplicação do ACCESS" é recomendada para ativado como `SetupApplications.ps1` faz.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>Ligar-se a falha do PowerShell com o erro: as credenciais especificadas são inválidas

#### <a name="problem"></a>Problema

Quando utilizar o PowerShell para ligar a cluster utilizar o modo de segurança "AzureActiveDirectory", após o início de sessão com êxito na página de início de sessão do AAD, ligação falhar com o erro: as credenciais especificadas são inválidas apresentado.

#### <a name="solution"></a>Solução

Mesmo que acima.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Serviço ferro Explorer iniciar sessão em troca de falha: AADSTS50011

#### <a name="problem"></a>Problema

Depois de início de sessão no AAD página Iniciar sessão no serviço ferro Explorer, o página devolve o início de sessão no falha - AADSTS50011: O endereço de resposta &lt;url&gt; não corresponder os endereços de resposta configurados para a aplicação: &lt;guid&gt;. 

![Endereço de resposta SFX não corresponder][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo

A aplicação de cluster(web) que representa o serviço ferro Explorer tentativas para autenticar AAD, como parte do pedido de fornece o URL de retorno redirecionar. Mas não estiver listado na lista AAD aplicação 'Resposta URL'.

#### <a name="solution"></a>Solução

Adicionar o url do serviço ferro Explorer para 'Resposta URL' no separador Configurar de aplicação cluster(web) ou substituir dos itens na lista. Em seguida, guarde.

![Resposta url da aplicação Web][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>Pode reutilizar ao mesmo inquilino AAD para vários clusters?

#### <a name="answer"></a>Resposta

Sim. Mas não se esqueça de adicionar o URL do serviço ferro Explorer à aplicação cluster(web) que contrário serviço ferro Explorer não irá funcionar.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>Por que motivo ainda preciso certificado de servidor enquanto AAD ativado?

#### <a name="answer"></a>Resposta

FabricClient e FabricGateway executar autenticação comum. Em caso de autenticação de AAD, a integração de AAD fornece a identidade do cliente para o servidor e o certificado de servidor é utilizada para confirmar a identidade do servidor. Para mais informações sobre como funciona o certificado no serviço ferro, verificar [certificados x. 509 e ferro de serviço][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png