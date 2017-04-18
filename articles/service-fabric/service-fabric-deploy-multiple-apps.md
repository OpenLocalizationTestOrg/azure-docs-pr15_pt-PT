<properties
   pageTitle="Implementar uma aplicação de Node.js que utiliza MongoDB | Microsoft Azure"
   description="Instruções passo a passo sobre como compactar várias convidado executáveis para implementar a um cluster de ferro de serviço do Azure"
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
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-multiple-guest-executables"></a>Implementar várias convidado executáveis

Este artigo mostra como compactar e implementar várias convidado executáveis para Azure Service ferro. Para criar e implementar um único pacote de serviço ferro leia o artigo como [Implementar convidado executável para ferro de serviço](service-fabric-deploy-existing-app.md).

Enquanto este tutorial mostra como implementar uma aplicação com um Node.js front-end que utiliza MongoDB como o arquivo de dados, pode aplicar os passos para qualquer aplicação que tem dependências em outra aplicação.   

Pode utilizar o Visual Studio para produzir o pacote de aplicação que contém vários executáveis de convidado. Consulte o artigo [Utilizar o Visual Studio para compactar uma aplicação existente](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-executable). Depois de ter adicionado o primeiro executável de convidado, clique com o botão direito do rato no projeto aplicação e selecione **Adicionar -> novo serviço ferro serviço** para adicionar o segundo projecto executável convidado a solução. Nota: Se optar por ligar a origem do projeto do Visual Studio, criar solução Visual Studio, irá Certifique-se de que o seu pacote de aplicação está atualizado com as alterações à origem. 

## <a name="manually-package-the-multiple-guest-executable-application"></a>Compactar manualmente a aplicação executável do convidado múltiplos
Em alternativa poderá compactar manualmente o convidado executável. Para a embalagem manual, este artigo utiliza a ferramenta de embalagem ferro de serviço, que está disponível em [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Embalagem a aplicação Node.js
Este artigo assume que Node.js não está instalado em nós no cluster ferro de serviço. Consequentemente, tem de adicionar Node.exe para o diretório de raiz da sua aplicação de nó antes da embalagem. A estrutura do directório da aplicação Node.js (utilizando Express framework de web e motor de modelo Jade) deverá ter um aspeto semelhante ao abaixo:

```
|-- NodeApplication
  	|-- bin
        |-- www
  	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
  	|-- public
        |-- images
        |-- etc.
  	|-- routes
        |-- index.js
        |-- users.js
  	|-- views
        |-- index.jade
        |-- etc.
  	|-- app.js
  	|-- package.json
  	|-- node.exe
```

Como um passo seguinte, crie um pacote de aplicações para a aplicação de Node.js. O código abaixo cria um pacote de aplicação de serviço ferro que contém a aplicação Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Segue-se uma descrição dos parâmetros que estão a ser utilizados:

- **/Source** aponta para o diretório da aplicação que deve ser compactado.
- **/Target** define o diretório na qual deve ser criado o pacote. Este diretório tem de ser diferente do directório de origem.
- **/appname** define o nome da aplicação da aplicação existente. É importante compreender que isto traduz-se para o nome do serviço no manifesto e não para o nome da aplicação de serviço ferro.
- **/exe** define o ficheiro executável que é suposto ferro de serviço de iniciação, neste caso `node.exe`.
- **/ma** define o argumento que está a ser utilizado para iniciar o ficheiro executável. Como Node.js não estiver instalado, serviço ferro tem de iniciar o servidor de web Node.js através da execução `node.exe bin/www`.  `/ma:'bin/www'`Indica a ferramenta de embalagem para utilização `bin/ma` como argumento para node.exe.
- **/AppType** define o nome de tipo de aplicação de serviço ferro.

Se procurar ao diretório que foi especificado no início de sessão, pode ver que a ferramenta de tenha criado um pacote de serviço ferro totalidade conforme apresentado abaixo:

```
|--[yourtargetdirectory]
  	|-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
O ServiceManifest.xml gerado tem agora uma secção que descreve como o servidor de web Node.js deve ser iniciado, conforme apresentado no fragmento de código abaixo:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Neste exemplo, o servidor de web Node.js aguarda à porta 3000, pelo que necessita de atualizar as informações de ponto final no ficheiro ServiceManifest.xml conforme apresentado abaixo.   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Embalagem a aplicação MongoDB
Agora que tenha compactado a aplicação de Node.js, pode prosseguir e compactar MongoDB. Tal como mencionado antes, os passos que atravessam agora não são específicos Node.js e MongoDB. Na verdade, também se aplicam a todas as aplicações que foram concebidas para empacotados em conjunto como uma aplicação de serviço ferro.  

Para compactar MongoDB, que pretende Certifique-se de que o pacote Mongod.exe e Mongo.exe. Ambas as binários estão localizados na `bin` directório do seu diretório de instalação MongoDB. A estrutura do directório aspeto semelhante ao abaixo.

```
|-- MongoDB
  	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Serviço ferro precisa de iniciar MongoDB com um comando semelhante ao abaixo, pelo que necessita para utilizar o `/ma` parâmetro quando embalagem MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] Os dados não estão a ser preservados no caso de falha de um nó se coloca o diretório de dados MongoDB no diretório local do nó. Deve utilizar o armazenamento resistente ou implementar uma réplica MongoDB definida para evitar a perda de dados.  

Na ou a shell de comandos do PowerShell, vamos executar a ferramenta de embalagem com os seguintes parâmetros:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para poder adicionar MongoDB ao seu pacote de aplicação de serviço ferro, é necessário para se certificar de que os pontos de parâmetro /target para o mesmo directório que já contenha a aplicação manifesto juntamente com a aplicação Node.js. Também precisa para se certificar de que está a utilizar o mesmo nome de ApplicationType.

Vamos navegue até ao diretório e examinar o que criou a ferramenta.

```
|--[yourtargetdirectory]
  	|-- MyNodeApplication
  	|-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Como pode ver, a ferramenta de adicionada uma nova pasta, MongoDB, ao diretório que contém os binários MongoDB. Se abrir o `ApplicationManifest.xml` ficheiro, pode ver que o pacote contém agora a aplicação de Node.js e o MongoDB. O código abaixo mostra o conteúdo do manifesto da aplicação.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>A aplicação de publicação
O último passo é publicar a aplicação para o local cluster de serviço ferro utilizando os scripts de PowerShell abaixo:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Assim que a aplicação com êxito for publicada no cluster local, pode aceder à aplicação de Node.js na porta que introduzidas no manifesto do serviço da aplicação Node.js – por exemplo http://localhost:3000.

Neste tutorial, viu como compactar facilmente as duas aplicações existentes como uma aplicação de serviço ferro. Também aprendeu como implementar ferro de serviço para que-beneficiar algumas das funcionalidades ferro de serviço, tais como elevada disponibilidade e integração com o sistema do Estado de funcionamento.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre como implementar contentores com [Descrição geral de serviço ferro e contentores](service-fabric-containers-overview.md)
