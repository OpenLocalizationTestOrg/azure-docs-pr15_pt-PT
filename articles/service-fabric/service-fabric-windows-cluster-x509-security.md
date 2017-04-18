<properties
   pageTitle="Ligar a um cluster de privado seguro | Microsoft Azure"
   description="Este artigo descreve como proteger as comunicações dentro do programa autónomo ou cluster privada, bem como entre os clientes e o cluster."
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
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Proteger um cluster de autónomo num Windows utilizando certificados x. 509

Este artigo descreve como seguro a comunicação entre os vários nós do seu cluster de autónomo do Windows, bem como autenticar os clientes estabelecer ligação a este cluster, utilizando o x. 509 certificados. Isto garante que apenas os utilizadores autorizados, podem aceder ao cluster, as aplicações implementados e efetuar tarefas de gestão.  Certificado de segurança deve estar ativada no cluster quando o cluster é criado.  

Para mais informações sobre a segurança de cluster como nó a nó segurança, segurança nó de cliente e controlo de acesso baseado em funções, consulte o artigo [cenários de segurança de Cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>Quais os certificados que irá precisar?

Para iniciar com, [Transfira o pacote de cluster autónomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) para um de nós no seu cluster. No pacote transferido, irá encontrar um ficheiro de **ClusterConfig.X509.MultiMachine.json** . Abra o ficheiro e reveja a secção de **segurança** na secção **Propriedades** :

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

Esta secção descreve os certificados que precisa para proteger o seu cluster de autónomo do Windows. Para ativar a segurança baseada em certificados defina os valores de **ClusterCredentialType** e **ServerCredentialType** para *X509*.

>[AZURE.NOTE] Uma [impressão digital](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a identidade principal de um certificado. Leia [como obter impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx) para descobrir a impressão digital dos certificados que criar.

A tabela seguinte lista os certificados que terá de na configuração do seu cluster:

|**Definição de CertificateInformation**|**Descrição**|
|-----------------------|--------------------------|
|ClusterCertificate|Este certificado é necessário para a comunicação entre os nós num cluster segura. Pode utilizar dois certificados diferentes, um principal e um secundário para a atualização. Configurar a impressão digital do certificado principal na secção **impressão digital** e que secundária nas variáveis **ThumbprintSecondary** .|
|ServerCertificate|Este certificado é apresentado para o cliente ao tentar ligar a este cluster. Para sua comodidade, pode optar por utilizar o mesmo certificado para *ClusterCertificate* e *ServerCertificate*. Pode utilizar dois certificados de servidor diferente, um principal e um secundário para a atualização. Configurar a impressão digital do certificado principal na secção **impressão digital** e que secundária nas variáveis **ThumbprintSecondary** . |
|ClientCertificateThumbprints|Este é um conjunto de certificados que pretende instalar a clientes do autenticados. Pode ter um número diferente de certificados de cliente instaladas nos computadores que pretende permitir acesso ao cluster. Configurar a impressão de cada certificado digital na variável **CertificateThumbprint** . Se definir o **IsAdmin** *Verdadeiro*, em seguida, o cliente com este certificado instalado pode realizar administrador atividades de gestão no cluster. Se o **IsAdmin** for *Falso*, o cliente com este certificado só pode realizar as ações permitidas para direitos de acesso do utilizador, normalmente só de leitura. Para mais informações sobre funções ler [(RBCA) o controlo de acesso baseado em funções](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|Defina o nome comuns do certificado de cliente primeiro para a **CertificateCommonName**. O **CertificateIssuerThumbprint** é a impressão digital para o emissor deste certificado. [Trabalhar com certificados](https://msdn.microsoft.com/library/ms731899.aspx) para saber mais sobre nomes de comuns e o emissor de leitura.|
|HttpApplicationGatewayCertificate|Este é um certificado opcional que pode ser especificado se que pretende proteger o seu Gateway de Http de aplicação. Certifique-se de reverseProxyEndpointPort é definida no nodeTypes se estiver a utilizar este certificado.|

Segue-se a configuração do exemplo cluster onde os certificados de Cluster, Server e o cliente foi fornecidos.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x509-certificates"></a>Adquirir o x. 509 certificados
Para proteger as comunicações dentro do cluster, terá primeiro obter os certificados de x. 509 para os nós de cluster. Para além disso, para limitar a ligação para este cluster a máquinas/utilizadores autorizados, terá de obter e instalar certificados para computadores cliente.

Para clusters que estiver a executar o cargas de trabalho de produção, deve utilizar uma [Autoridade de certificação (AC)](https://en.wikipedia.org/wiki/Certificate_authority) assinado certificado x. 509 para proteger o cluster. Para obter detalhes sobre como obter estes certificados, aceda a [como: obter um certificado](http://msdn.microsoft.com/library/aa702761.aspx).

Para clusters que utiliza para fins de teste, pode optar por utilizar um certificado autoassinado.

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: Criar um certificado autoassinado
Uma forma de criar um certificado autoassinado que pode ser protegido corretamente é utilizar o script *CertSetup.ps1* na pasta serviço ferro SDK no diretório *C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Editar este ficheiro e utilize esta opção para criar um certificado com um nome adequado.

Agora, exporte o certificado para um ficheiro PFX com uma palavra-passe protegida. Pela primeira vez que precisa para obter a impressão do certificado digital. Execute a aplicação certmgr.exe. Navegue para a pasta **Local\Pessoal.** e localize o certificado que acabou de criar. Faça duplo clique no certificado para abri-lo, selecione o separador *Detalhes* e desloque-se para baixo para o campo de *impressão digital* . Copie o valor de impressão digital para o comando do PowerShell abaixo, remover os espaços.  Altere o valor de *$pswd* para uma conformidade palavra-passe segura protegê-lo e executar o PowerShell:

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Para ver os detalhes de um certificado instalado no computador pode executar o seguinte comando PowerShell:

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

Em alternativa, se tiver uma subscrição do Azure, siga a secção [certificados de adicionar a chave cofre](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Instale os certificados
Assim que tiver certificados, pode instalá-las em nós de cluster. Os nós têm de ter o mais recente do Windows PowerShell 3. x instalado nos mesmos. Terá de repetir estes passos em cada nó, para Cluster e servidor e os certificados qualquer secundário.

1. Copie ficheiros. pfx para o nó.
2. Abra uma janela do PowerShell como administrador e introduza os seguintes comandos. Substitua o *$pswd* a palavra-passe que utilizou para criar este certificado. Substitua o *$PfxFilePath* o caminho completo da PFX copiados para este nó.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. Seguinte tem de configurar o controlo de acesso neste certificado para que o processo de ferro de serviço, que é executado em conta de serviço de rede, pode utilizá-lo executando o seguinte script. Forneça a impressão digital do certificado e "Serviço de rede" para a conta de serviço. Pode verificar se as ACL no certificado estão corretas utilizando a ferramenta de certmgr.exe e consultar as chaves privadas gerir na orientação do diapositivo notas.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab - AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
ServiceFabricCluster ligar - ConnectionEndpoint 10.7.0.4:19000 - KeepAliveIntervalInSec 10 - X509Credential - ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 - FindType FindByThumbprint - FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 - StoreLocation UtilizadorActual - StoreName meu
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
ServiceFabricCluster ligar Get-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab
```
