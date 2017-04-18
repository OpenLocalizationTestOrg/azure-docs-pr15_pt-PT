<properties
   pageTitle="Ferro e contentores implementar o serviço de | Microsoft Azure"
   description="Ferro e a utilização de contentores para implementar microservice aplicações de serviço. Este artigo descreve as funcionalidades que fornece ferro de serviço de membros em contentores e como implementar uma imagem de contentor do Windows para um cluster de"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Pré-visualização: Implementar um contentor Windows ferro de serviço

> [AZURE.SELECTOR]
- [Implementar o contentor Windows](service-fabric-deploy-container.md)
- [Implementar Docker contentor](service-fabric-deploy-container-linux.md)

Este artigo explica o criação de serviços em contentores em contentores do Windows. 

>[AZURE.NOTE] Esta funcionalidade está na pré-visualização para Linux e não se encontra disponível para utilização com o Windows Server 2016. Este estará disponível na pré-visualização do Windows Server 2016 na próxima versão do serviço ferro e suportadas na versão subsequente depois que.

Serviço ferro tem várias funcionalidades de contentor que ajudarão-lo com a criação de aplicações que são compostos por de microservices que estão em contentores. Estes são chamados em contentores de serviços. 

As capacidades de incluem;

- Implementação da imagem contentor e ativação
- Governação de recursos
- Autenticação do repositório
- Porta de contentor para o mapeamento de porta do anfitrião
- Contentor para outro deteção e avaliação de comunicação
- Capacidade de configurar e definir variáveis de ambiente

Permite-Observe cada uma das capacidades sucessivamente quando embalagem um serviço em contentores que devem ser incluídas na sua aplicação.

## <a name="packaging-a-windows-container"></a>Embalagem um contentor do Windows

Quando embalagem um contentor, pode escolher quer ao utilizar um modelo de projeto do Visual Studio ou [criar manualmente o pacote de aplicação](#manually). Utilizar o Visual Studio, a estrutura de pacote de aplicações e ficheiros manifest são criados pelo Assistente do project novo para si (Isto está a chegar na próxima versão).

## <a name="using-visual-studio-to-package-an-existing-container-image"></a>Utilizando o Visual Studio para compactar uma imagem de contentor existente

>[AZURE.NOTE] Num lançamento futuro do Visual Studio ferramenta SDK, irá poderá adicionar um contentor para uma aplicação de forma semelhante que pode adicionar convidado executável hoje. Consulte o tópico [Implementar convidado executável para ferro de serviço](service-fabric-deploy-existing-app.md) . Atualmente tem de efetuar embalagem manual, conforme descrito abaixo.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-a-container"></a>Embalagem manualmente e implementar um contentor
O processo de compactação manualmente um serviço em contentores baseia-se os seguintes passos:

1. Publicado os contentores para o seu repositório.
2. Crie a estrutura de diretório do pacote.
3. Edite o ficheiro de manifesto de serviço.
4. Edite o ficheiro manifesto da aplicação.

## <a name="container-image-deployment-and-activation"></a>Implementação da imagem contentor e ativação.
No [modelo de aplicação](service-fabric-application-model.md)de serviço ferro, um contentor representa um anfitrião da aplicação na qual são colocadas várias réplicas do serviço. Para implementar e ativar um contentor, coloque o nome da imagem contentor para um `ContainerHost` elemento no manifesto do serviço.

No manifesto serviço adicionar um `ContainerHost` para a entrada aponte e configurar o `ImageName` para ser o nome do repositório de contentor e imagem. O manifesto parcial seguinte mostra um exemplo de implementação do contentor chamado *myimage:v1* a partir de um repositório denominado *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Pode fornecer comandos de entrada para a imagem de contentor ao especificar opcional `Commands` elemento com uma vírgula delimitado por conjunto de comandos para executar no interior do contentor. 

## <a name="resource-governance"></a>Governação de recursos
Governação recurso é uma funcionalidade do contentor e restringe os recursos que pode utilizar o contentor no anfitrião do. O `ResourceGovernancePolicy`, especificado no manifesto da aplicação, que fornece a capacidade para declarar limites de recursos para um pacote de código do serviço. Limites do recurso podem ser definidos para;

- Memória
- MemorySwap
- CpuShares (CPU relativo de peso)
- MemoryReservationInMB  
- BlkioWeight (BlockIO relativo peso). 

>[AZURE.NOTE] Num lançamento futuro, o suporte para especificar os limites de IO bloco específico como IOPs, leitura/escrita BPS e outros será possíveis.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Autenticação do repositório
Para transferir um contentor que poderá ter de fornecer as credenciais de início de sessão para o repositório de contentor. As credenciais de início de sessão, especificadas no manifesto da *aplicação* são utilizadas para especificar as informações de início de sessão, ou tecla SSH, para transferir a imagem de contentor a partir do repositório de imagem.  O exemplo seguinte mostra uma conta denominada *utilizadorteste* juntamente com a palavra-passe em texto simples. Isto **não** é recomendado.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

A palavra-passe pode e deve ser encriptada utilizando um certificado implementado a máquina.

O exemplo seguinte mostra uma conta chamada *utilizadorteste* com a palavra-passe encriptada utilizando um certificado denominado *MyCert*. Pode utilizar o `Invoke-ServiceFabricEncryptText` comando do Powershell para criar o texto de codificação secreta para a palavra-passe. Consulte este artigo [Gerir segredos nas aplicações de serviço ferro](service-fabric-application-secret-management.md) para obter detalhes sobre como. Tem de ser implementada a chave privada do certificado para desencriptar a palavra-passe para o computador local num método de fora da banda (no Azure este é através do processador). Em seguida, ao serviço ferro implementa o pacote de serviço para o computador, é possível desencriptar o segredo e juntamente com o nome da conta, autenticar-se com o repositório de contentor utilizando estas credenciais.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Porta de contentor para o mapeamento de porta do anfitrião
Pode configurar uma porta de anfitrião utilizada para comunicar com o contentor ao especificar um `PortBinding` no manifesto da aplicação. O enlace porta mapas a porta que o serviço está a escutar no interior do contentor para uma porta no anfitrião do.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Contentor para outro deteção e avaliação de comunicação
Utilizar o `PortBinding` política pode mapear uma porta de contentor para um `Endpoint` no manifesto do serviço, conforme mostrado no seguinte exemplo. O ponto final `Endpoint1` pode especificar uma porta fixa, por exemplo porta 80 ou não especificar nenhuma porta de todo, caso em que uma porta aleatória a partir do intervalo de portas de aplicação dos clusters é escolhida por si.

De membros em contentores de convidado, especificando uma `Endpoint` como no manifesto serviço permite que o serviço ferro publicar automaticamente este ponto final para o serviço de nomenclatura para que outros serviços em execução no cluster podem descobrir neste contentor utilizando as consultas de resto do serviço de resolver. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Através do registo com o serviço de atribuição de nomes, que pode fazer facilmente comunicação de contentor para outro no código de dentro o contentor a utilizar o [proxy inverso](service-fabric-reverseproxy.md). Tudo o que precisa de fazer é fornecer a porta de monitorização do proxy inverso http e o nome dos serviços que pretende comunicar com definindo estas como variáveis de ambiente. Consulte a secção seguinte sobre como fazer isto.  

## <a name="configure-and-set-environment-variables"></a>Configurar e definir variáveis de ambiente
Variáveis de ambiente podem ser especificado foe cada pacote de código no serviço manifesto em ambos os serviços implementados contentores ou como executáveis processos/convidado. Estes valores de variáveis ambiente podem ser substituídos especificamente no manifesto da aplicação ou especificados durante a implementação como parâmetros de aplicação.

O manifesto do serviço fragmento XML seguinte mostra um exemplo de como especificar variáveis de ambiente para um pacote de código. 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

Estas variáveis de ambiente podem ser substituídas ao nível manifesto da aplicação:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

No exemplo acima, podemos tiver especificado um valor explícito para a `HttpGateway` variável de ambiente (19000) enquanto o valor de `BackendServiceName` parâmetro estiver definido através de `[BackendSvc]` parâmetro de aplicação. Isto permite-lhe especificar o valor para `BackendServiceName`valor no momento da implementar a aplicação e não tem um valor fixo no manifesto. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Concluir exemplos para a aplicação e manifesto de serviço
Segue-se uma manifestos de aplicação de exemplo que mostra as funcionalidades do contentor.


    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


A seguinte é um exemplo manifestos de serviço (especificado no manifesto da aplicação anterior), que mostra as funcionalidades do contentor

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>
