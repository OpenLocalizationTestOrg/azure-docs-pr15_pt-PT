<properties
   pageTitle="Noções sobre a aplicação de serviço ferro e políticas de segurança do serviço | Microsoft Azure"
   description="Uma descrição geral sobre como executar uma aplicação de serviço ferro em sistema e contas de segurança local, incluindo o ponto de SetupEntry onde precisa de uma aplicação para executar alguns ação privilegiada antes de ser iniciado"
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
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# <a name="configure-security-policies-for-your-application"></a>Configurar políticas de segurança para a sua aplicação
Azure Service ferro fornece a capacidade de seguro aplicações em execução no cluster em diferentes contas de utilizador. Serviço ferro protege também os recursos utilizados pelas aplicações no momento da implementação na conta de utilizador como ficheiros, diretórios e certificados. Isto faz com que aplicações em execução, mesmo num ambiente alojado partilhada, seguro uns dos outros. 

Por predefinição, executar aplicações de serviço ferro sob a conta que executa o processo de Fabric.exe em. Serviço ferro também fornece a capacidade de executar aplicações em conta de utilizador local sistema local, uma conta ou especificado dentro manifesto a aplicação. Tipos de conta de sistema local suportado para são **UtilizadorLocal**, o **serviço de rede**, o **serviço local**e o **sistema local**.

 Quando executar ferro de serviço no Windows Server no Centro de dados utilizando o instalador do autónomo, pode utilizar contas de domínio do Active Directory (AD).

Grupos de utilizadores, podem ser definidos e criou, para que podem ser adicionados um ou mais utilizadores para cada grupo para ser geridos em conjunto. Isto é útil quando existem vários utilizadores para pontos de entrada de serviços diferente e que necessitam de ter determinados privilégios comuns que estão disponíveis ao nível do grupo.

## <a name="configure-the-policy-for-service-setupentrypoint"></a>Configurar a política de serviço SetupEntryPoint

Tal como descrito no [modelo de aplicação](service-fabric-application-model.md) a **SetupEntryPoint** é um ponto de entrada privilegiados que é executada com as mesmas credenciais como ferro de serviço (normalmente, a conta de *serviço de rede* ) antes de qualquer outro ponto de entrada. O ficheiro executável especificado por **ponto de entrada** é normalmente o anfitrião do serviço de execução longa, para que está a ter uma ponto de entrada separadas configuração evita ter de executar o anfitrião do serviço executável com privilégios de alto para adicional períodos de tempo. O ficheiro executável especificado por **ponto de entrada** é executado depois **SetupEntryPoint** sai com êxito. O processo de resultante é monitorizado e reiniciado, começando novamente **SetupEntryPoint**, se alguma vez termina ou falha.

Segue-se um exemplo de manifesto serviço simples que mostra o SetupEntryPoint e o ponto de entrada principal para o serviço.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### <a name="configure-the-policy-using-a-local-account"></a>Configurar a política de utilizando uma conta local

Depois de configurar o serviço de ter uma ponto de entrada de configuração, pode alterar as permissões de segurança que é executada no manifesto da aplicação. O exemplo followin mostra como configurar o serviço de ser executadas com privilégios de conta de administrador do utilizador.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Em primeiro lugar, crie uma secção de **principais** com um nome de utilizador, tal como SetupAdminUser. Isto indica que o utilizador é um membro do grupo Administradores sistema.

Em seguida, na secção **ServiceManifestImport** , configure uma política para aplicar esta capital a **SetupEntryPoint**. Isto indica que ferro de serviço que quando o ficheiro **MySetup.bat** é executado, deverá ser RunAs com privilégios de administrador. Dado que tiver *não* aplicada uma política para o ponto de entrada principal, o código no **MyServiceHost.exe** é executado no sistema de conta de **serviço de rede** . Este é a conta predefinida que todos os pontos de entrada do serviço são executados como.

Vamos agora adicione o ficheiro MySetup.bat o projeto do Visual Studio para testar os privilégios de administrador. No Visual Studio, com o botão direito no projeto serviço e adicione um novo ficheiro denominado MySetup.bat.

Em seguida, certifique-se de que o ficheiro MySetup.bat está incluído no pacote do serviço. Por predefinição, não é. Selecione o ficheiro, com o botão direito para obter o menu de contexto e selecione **Propriedades**. Na caixa de diálogo Propriedades, certifique-se de que a **Copiar para directório de saída** está definido para **copiar se mais recente**. Consulte a seguinte captura de ecrã.

![Visual Studio CopyToOutput para o ficheiro de batch SetupEntryPoint][image1]

Agora, abra o ficheiro MySetup.bat e adicione os seguintes comandos:

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Em seguida, criar e implementar a solução para um cluster de desenvolvimento local.  Assim que o serviço foi iniciado, conforme mostrado no serviço ferro Explorer, pode ver que a MySetup.bat foi bem sucedida de uma de duas maneiras. Abra uma linha de comandos do PowerShell e escrever:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Em seguida, tome nota do nome do nó onde o serviço foi implementado e a trabalhar no Explorador de ferro do serviço, por exemplo, nó 2. Em seguida, navegue para a pasta de trabalho de instância de aplicação para localizar o ficheiro de out.txt que mostra o valor de **TestVariable**. Por exemplo, caso este serviço foi implementado nó 2, em seguida, que pode aceder a este caminho para o **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  <a name="configure-the-policy-using-local-system-accounts"></a>Configurar a política de utilizando as contas do sistema local
Muitas vezes é preferível para executar o script de arranque utilizando uma conta de sistema local em vez de uma conta de administradores, conforme mostrado anterior. A executar o RunAs política como administradores normalmente não funciona bem uma vez que máquinas têm utilizador Access (User Control) ativado por predefinição. Nestes casos, **a recomendação é executar o SetupEntryPoint como sistema local em vez de um utilizador local adicionado ao grupo de administradores**. O exemplo seguinte mostra a definição SetupEntryPoint para executar o sistema local.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  <a name="launch-powershell-commands-from-a-setupentrypoint"></a>Comandos do PowerShell a partir de um SetupEntryPoint de iniciação
Para executar o PowerShell do ponto de **SetupEntryPoint** , pode executar **PowerShell.exe** num ficheiro batch que aponta para um ficheiro do PowerShell. Em primeiro lugar, adicione um ficheiro do PowerShell para o projeto de serviço, tal como **MySetup.ps1**. Lembre-se definir a propriedade *copiar se mais recente* para que o ficheiro também é incluído no pacote do serviço. O exemplo seguinte mostra um ficheiro do lote de exemplo para iniciar um ficheiro de PowerShell denominado MySetup.ps1, que define uma variável de ambiente de sistema denominada **TestVariable**.


MySetup.bat à iniciação PowerShell ficheiro.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

No ficheiro do PowerShell, adicione o seguinte para definir uma variável de ambiente de sistema:

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Nota:** Por predefinição quando executa o ficheiro batch-analisa denominada **trabalhar** para os ficheiros na pasta da aplicação. Neste caso, quando executa MySetup.bat queremos esta opção para localizar o MySetup.ps1 na mesma pasta, que é a pasta do **pacote de código** de aplicação. Para alterar esta pasta, defina a pasta de trabalho, conforme mostrado no seguinte.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## <a name="using-console-redirection-for-local-debugging"></a>Utilização do redirecionamento de consola para depuração local
Ocasionalmente é útil ver o resultado da consola de execução de um script para fins de depuração. Para poder fazer isto pode definir uma política de redirecionamento de consola, o que escreve a saída de um ficheiro. O resultado do ficheiro é escrito para a pasta de aplicação denominada **registo** no nó onde a aplicação é implementada e executar (consulte o artigo onde localizar isto no exemplo anterior).

**Nota: nunca** utilizar a política de redirecionamento de consola numa aplicação implementada na produção, uma vez que este pode afetar a aplicação activação pós-falha. **Apenas** Utilize esta opção para fins de depuração e desenvolvimento local.  

O exemplo seguinte mostra a configurar o redirecionamento de consola com um valor de FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

Se alterar o ficheiro MySetup.ps1 para escrever um comando **eco** agora, isto vai escrever para o ficheiro de exportação para fins de depuração.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**Depois de ter depuração o script, imediatamente, remova esta política de redirecionamento de consola**

## <a name="configure-policy-for-service-code-packages"></a>Configurar a política para os pacotes de código do serviço 
Nos passos anteriores, viu como aplicar RunAs política a SetupEntryPoint. Vamos analisar um pouco mais aprofundada como criar principais diferentes que podem ser aplicados como políticas de serviço.

### <a name="create-local-user-groups"></a>Criar grupos de utilizadores local
Grupos de utilizadores podem ser definidos e criados que permitir que os utilizadores de uma ou mais seja adicionada a um grupo. Isto é particularmente útil se existem vários utilizadores para pontos de entrada de serviços diferente e que necessitam de ter determinados privilégios comuns que estão disponíveis ao nível do grupo. O exemplo seguinte mostra um grupo local denominado **LocalAdminGroup** com privilégios de administrador. Dois utilizadores, Customer1 e Customer2, são efectuados os membros deste grupo local.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### <a name="create-local-users"></a>Criar utilizadores locais
Pode criar um utilizador local que pode ser utilizado para proteger um serviço dentro da aplicação. Quando um tipo de conta **UtilizadorLocal** é especificado na secção principais do manifesto da aplicação, serviço ferro cria contas de utilizador locais máquinas onde a aplicação é implementada. Por predefinição, nestas contas não tem os mesmos nomes que as especificadas no manifesto da aplicação (por exemplo, Customer3 no seguinte exemplo). Em vez disso, são dinamicamente geradas e ter aleatórias palavras-passe.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### <a name="assign-policies-to-the-service-code-packages"></a>Atribuir políticas para os pacotes de código do serviço
A secção **RunAsPolicy** para um **ServiceManifestImport** Especifica a conta a partir da secção principais que deve ser utilizada para executar um pacote de código. Também associa pacotes de código do serviço manifesto contas de utilizador, na secção principais. Pode especificá-lo para a configuração ou pontos de entrada principal ou pode especificar `All` aplicá-lo a ambos. O exemplo seguinte mostra diferentes políticas é aplicadas:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Se não for especificado **EntryPointType** , a predefinição é definida como EntryPointType = "Principal". Especificar **SetupEntryPoint** é particularmente útil quando pretende executar a operação de configuração determinadas privilégio alta numa conta de sistema. Pode executar o código de serviço real numa conta de privilégio canto inferior.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política predefinida para todos os pacotes de código do serviço
A secção de **DefaultRunAsPolicy** é utilizada para especificar uma conta de utilizador predefinida para todos os pacotes de código que não têm um específico **RunAsPolicy** definidos. Se precisar da maior parte dos pacotes de código especificados no serviço manifesto utilizado por uma aplicação executar sob o utilizador do mesmo, a aplicação apenas pode definir uma política de RunAs predefinida com essa conta de utilizador. O exemplo seguinte especifica que se um pacote de código não tiver um **RunAsPolicy** especificado, o pacote de código deve ser executado **MyDefaultAccount** especificado na secção principais.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### <a name="using-an-active-directory-domain-group-or-user"></a>Utilizar um utilizador ou grupo de domínio do Active Directory
Para o serviço ferro instalado no Windows Server com o instalador do autónomo, pode executar o serviço sob as credenciais para uma conta de grupo ou utilizador do Active Directory (AD). Nota: Este é o Active Directory no local dentro do seu domínio e não é com o Azure Active Directory (AAD). Ao utilizar um domínio de utilizador ou grupo, em seguida, pode aceder outros recursos no domínio (por exemplo, partilhas de ficheiros) que tenham sido concedidos permissões.

O exemplo seguinte mostra um utilizador do AD chamado *utilizadorteste* com a respetiva palavra-passe de domínio encriptado utilizando um certificado denominado *MyCert*. Pode utilizar o `Invoke-ServiceFabricEncryptText` comando do Powershell para criar o texto de codificação secreta. Consulte este artigo [Gerir segredos nas aplicações de serviço ferro](service-fabric-application-secret-management.md) para obter detalhes sobre como. Tem de ser implementada a chave privada do certificado para desencriptar a palavra-passe para o computador local num método de fora da banda (no Azure este é através do Gestor de recurso). Em seguida, ao serviço ferro implementa o pacote de serviço para o computador, é possível desencriptar o segredo e juntamente com o nome de utilizador, autenticar com AD para ser executado nessas credenciais.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## <a name="assign-securityaccesspolicy-for-http-and-https-endpoints"></a>Atribuir SecurityAccessPolicy para os pontos finais HTTP e HTTPS
Se aplicar uma política de RunAs para um serviço e manifesto serviço declara recursos de ponto final com o protocolo HTTP, tem de especificar uma **SecurityAccessPolicy** para assegurar que portas atribuídas para estes pontos finais corretamente são listado para a conta de utilizador RunAs que o serviço é executado em controlo de acesso. Caso contrário, **sys** não tem acesso ao serviço e obtenha falhas com chamadas a partir do cliente. O exemplo followin aplica-se a conta Customer3 para um ponto final denominado **ServiceEndpointName**, que lhe dá uma para direitos de acesso completo.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Para o ponto final HTTPS, também tem de indicar o nome do certificado para voltar para o cliente. Pode fazê-lo utilizando **EndpointBindingPolicy**, com o certificado definido numa secção de certificados no manifesto da aplicação.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## <a name="a-complete-application-manifest-example"></a>Um exemplo de manifesto aplicação completo
Manifesto da aplicação seguinte mostra muitas das definições do diferentes:

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

* [Compreender o modelo de aplicação](service-fabric-application-model.md)
* [Especificar recursos do manifesto serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
