<properties
    pageTitle="Funções de web e trabalhador Python com o Visual Studio | Microsoft Azure"
    description="Descrição geral da utilização de ferramentas de Python para Visual Studio para criar serviços em nuvem Azure incluindo web funções e funções de trabalho."
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/03/2016"
    ms.author="adegeo"/>


# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Funções de web e trabalhador Python com as ferramentas Python para Visual Studio

Este artigo fornece uma descrição geral da utilização Python web trabalhador as funções e utilizando [Ferramentas Python para Visual Studio][]. Vai aprender a utilizar o Visual Studio para criar e implementar um serviço na nuvem básicas que utiliza Python.

## <a name="prerequisites"></a>Pré-requisitos

 - Visual Studio 2013 ou 2015
 - [Ferramentas de Python para Visual Studio][] (PTVS)
 - [Ferramentas Azure SDK para VS 2013][] ou do [Azure SDK para VS 2015][]
 - [Python 2.7 32 bits][] ou [Python 3.5 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>O que são funções de web e trabalhador Python?

Azure fornece três calcular modelos para executar aplicações: [funcionalidade Web Apps num serviço de aplicação do Azure][execution model-web sites], [máquinas virtuais do Azure][execution model-vms]e [Serviços em nuvem Azure][execution model-cloud services]. Todos os modelos de três suportam Python. Serviços em nuvem, que incluem funções web e trabalhador, fornecem *plataforma como um serviço (PaaS)*. Dentro de um serviço na nuvem, uma função de web fornece um servidor web de serviços de informação Internet (IIS) dedicado para aplicações do anfitrião web front-end, enquanto uma função de trabalho pode executar tarefas assíncronas, execução longa ou perpétuos independentemente da interação do utilizador ou de entrada.

Para obter mais informações, consulte o artigo [o que é um serviço na nuvem?].

> [AZURE.NOTE]*Looking para criar um Web site simple?*
Se o seu cenário envolve apenas um site simple front-end, considere utilizar a funcionalidade de Web Apps lightweight na aplicação de serviço de Azure. Pode atualizar facilmente para um serviço na nuvem como cresce do seu Web site e a alteração dos requisitos. Consulte o <a href="/develop/python/">Centro de programadores do Python</a> para artigos que abrangem desenvolvimento da funcionalidade Web Apps num serviço de aplicação do Azure.
<br />


## <a name="project-creation"></a>Criação de projetos

No Visual Studio, pode selecionar **Serviço em nuvem Azure** na caixa de diálogo **Novo projeto** , em **Python**.

![Caixa de diálogo novo projeto](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

No Assistente de Azure serviço em nuvem, pode criar novas funções web e trabalhador.

![Caixa de diálogo de serviço de nuvem Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

Modelo de função de trabalho é fornecido com o código de texto automático para ligar a uma conta de armazenamento Azure ou Azure Service Bus.

![Solução de serviço de nuvem](./media/cloud-services-python-ptvs/worker.png)

Pode adicionar funções web ou de trabalho para um serviço de nuvem existente em qualquer altura.  Pode optar por adicionar projectos existentes na sua solução ou criar novos.

![Adicionar comando de função](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

O serviço de nuvem pode conter funções implementadas em idiomas diferentes.  Por exemplo, pode ter uma função de web Python implementada utilizando Django, com Python ou com funções de trabalho c#.  Pode comunicar facilmente entre as funções com o serviço Bus filas ou filas de armazenamento.

## <a name="install-python-on-the-cloud-service"></a>Instalar o Python num serviço em nuvem

>[AZURE.WARNING] Os scripts de configuração que são instalados com Visual Studio (ao tempo que este artigo foram atualizados pela última vez) não funcionam. Esta secção descreve uma solução.

O problema principal com os scripts de configuração são não instale python. Em primeiro lugar, defina duas [tarefas de arranque](cloud-services-startup-tasks.md) no ficheiro [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) . A primeira tarefa (**PrepPython.ps1**) transfere e instala o runtime Python. A segunda tarefa (**PipInstaller.ps1**) é executado pip quaisquer dependências, que poderá ter de instalar.

Os scripts abaixo foram escritos filtragem Python 3.5. Se pretender utilizar a versão de 2. x do python, defina o ficheiro de variável **PYTHON2** para **** para as duas tarefas de arranque e a tarefa de runtime: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.


```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
    
  </Task>

</Startup>
```

As variáveis **PYTHON2** e **PYPATH** tem de ser adicionada à tarefa de arranque do trabalho. A variável **PYPATH** é utilizada apenas se a variável de **PYTHON2** estiver definida para **no**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>ServiceDefinition.csdef de exemplo

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Em seguida, crie os ficheiros **PrepPython.ps1** e **PipInstaller.ps1** na **. / posição** pasta do seu perfil.

#### <a name="preppythonps1"></a>PrepPython.ps1

Este script instala python. Se a variável de enviornment **PYTHON2** está definida **no** , em seguida, Python 2.7 vai ser instalado, caso contrário, Python 3.5 será instalado.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }
        
        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1

Este script liga para o pip e instala todas as dependências no ficheiro **requirements.txt** . Se a variável de enviornment **PYTHON2** está definida será utilizada **no** , em seguida, Python 2.7, caso contrário, será utilizada Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Modificar LaunchWorker.ps1

>[AZURE.NOTE] No caso de um projeto de **função de trabalho** , **LauncherWorker.ps1** ficheiro é necessária para executar o ficheiro de arranque. Num projeto de **função web** , o ficheiro de arranque alternativa é definido nas propriedades do projeto.

O **bin\LaunchWorker.ps1** foi criado originalmente para efetuar muitas trabalho preparação mas na verdade não funciona. Substitua o conteúdo desse ficheiro com o seguinte script.

Este script chamadas o ficheiro **worker.py** a partir do seu projeto python. Se a variável de enviornment **PYTHON2** está definida será utilizada **no** , em seguida, Python 2.7, caso contrário, será utilizada Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>PS.cmd

Os modelos do Visual Studio devem ter criado um ficheiro **ps.cmd** a **. / posição** pasta. Este script de shell de chamadas de scripts bombom PowerShell acima e fornece registo com base no nome do bombom PowerShell denominado. Se este ficheiro não foi criado, eis o que devem estar no mesmo. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Executar localmente

Se definir o seu projeto do serviço de nuvem como o projeto de arranque e prima F5, o serviço de nuvem será executada no emulador Azure local.

Apesar de a suportar PTVS lançamento em emulador de, depuração (por exemplo, pontos de interrupção) não irá funcionar.

Para depurar funções de web e trabalhador, pode definir o projeto de função como o projeto de arranque e depurar que em vez disso.  Também pode definir vários projetos de arranque.  A solução com o botão direito e, em seguida, selecione **Configurar projetos de arranque**.

![Solução propriedades do projeto de arranque](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publicar no Azure

Para publicar, o projeto de serviço de nuvem da solução com o botão direito e, em seguida, selecione **Publicar**.

![Publicar o início de sessão do Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Siga o assistente. Se precisar, ativar o ambiente de trabalho remoto. Ambiente de trabalho remoto é útil quando precisar de depurar algo.

Quando tiver terminado de configurar as definições, clique em **Publicar**.

Algumas progresso serão apresentados na janela de saída, em seguida, irá ver a janela de registo de atividade do Microsoft Azure.

![Janela de registo de atividade do Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

Implementação irá demorar vários minutos para concluir e, em seguida, as funções web e/ou trabalho irão estar em execução no Azure!

### <a name="investigate-logs"></a>Investigar registos

Depois da máquina de virtual do serviço de nuvem é iniciado e instala Python, poder ver os registos para localizar todas as mensagens de falha. Estes registos encontram-se na **C:\Resources\Directory\\{função} \LogFiles** pasta. **PrepPython.err.txt** terá pelo menos um erro no mesmo a partir do quando tenta o script detetar se Python está instalada e **PipInstaller.err.txt** poderá reclamação sobre uma versão desatualizada do pip.

## <a name="next-steps"></a>Próximos passos

Para obter informações mais detalhadas sobre como trabalhar com funções web e trabalhador nas ferramentas de Python para Visual Studio, consulte a documentação de PTVS:

- [Projetos de serviço de nuvem][]

Para obter mais detalhes sobre como utilizar serviços Azure a partir do seu funções web e trabalhador, tal como utilizar o armazenamento do Windows Azure ou Bus de serviço, consulte os seguintes artigos.

- [Serviço de BLOBs][]
- [Serviço de tabela][]
- [Serviço de fila de espera][]
- [Serviço Bus filas][]
- [Tópicos de Bus do serviço][]


<!--Link references-->

[O que é um serviço na nuvem?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service-web/app-service-web-overview.md
[execution model-vms]: ../virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Serviço de BLOBs]: ../storage/storage-python-how-to-use-blob-storage.md
[Serviço de fila de espera]: ../storage/storage-python-how-to-use-queue-storage.md
[Serviço de tabela]: ../storage/storage-python-how-to-use-table-storage.md
[Serviço Bus filas]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Tópicos de Bus do serviço]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Ferramentas de Python para Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Projetos de serviço de nuvem]: http://go.microsoft.com/fwlink/?LinkId=624028
[Ferramentas Azure SDK para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas Azure SDK para VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 bits]: https://www.python.org/downloads/
[Python 3.5 32 bits]: https://www.python.org/downloads/
