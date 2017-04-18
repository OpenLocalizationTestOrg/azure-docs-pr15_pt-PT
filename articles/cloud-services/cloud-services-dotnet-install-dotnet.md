<properties
   pageTitle="Instale o .NET numa função de serviço de nuvem | Microsoft Azure"
   description="Este artigo descreve como instalar manualmente o .NET framework na Web de serviço de nuvem e funções de trabalho"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>

# <a name="install-net-on-a-cloud-service-role"></a>Instale o .NET numa função de serviço na nuvem 

Este artigo descreve como instalar o .NET framework na Web de serviço de nuvem e funções de trabalho. Pode utilizar estes passos para instalar o .NET 4.6.1 o Azure convidado SO família 4. Para as informações mais recentes no SO convidado lançamentos consulte [disponibilização do Azure convidado SO e da matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

O processo de instalação do .NET no seu funções web e trabalhador envolve incluindo o pacote de instalação .NET como parte do seu projeto na nuvem e iniciar o programa de instalação como parte das tarefas de arranque a função.  

## <a name="add-the-net-installer-to-your-project"></a>Adicionar o instalador do .NET ao seu projeto
- Transferir o o instalador do web para o .NET framework que pretende instalar
    - [.NET 4.6.1 Installer da web](http://go.microsoft.com/fwlink/?LinkId=671729)
- Para uma função da Web
  1. No **Explorador de soluções**, em **funções** do projeto de serviço de nuvem botão direito do rato clique no seu papel e selecione **Adicionar > nova pasta**. Crie uma pasta denominada *Reciclagem*
  2. Clique com o botão direito do rato na pasta **posição** e selecione **Adicionar > Item existente**. Selecione o instalador do .NET e adicioná-lo para a pasta de reciclagem.
- Para uma função de trabalho
  1. Clique com o botão direito do rato no seu papel e selecione **Adicionar > Item existente**. Selecione o instalador do .NET e adicioná-lo para a função. 

Os ficheiros adicionados desta forma ao conteúdo da função pasta serão automaticamente adicionada ao pacote de serviço de nuvem e implementada para uma localização consistente na máquina virtual. Repita este processo para todas as funções web e trabalhador no seu serviço de nuvem para que todas as funções tenham uma cópia do programa de instalação.

> [AZURE.NOTE] Deve instalar .NET 4.6.1 no seu perfil do serviço na nuvem mesmo se destina-se a aplicação .NET 4.6. Azure convidado SO inclui atualizações [3098779](https://support.microsoft.com/kb/3098779) e [3097997](https://support.microsoft.com/kb/3097997). Instalar o .NET 4.6 na parte superior destas atualizações pode causar problemas quando as aplicações do .NET, a ser executado por isso diretamente deve instalar .NET 4.6.1 em vez de .NET 4.6. Para mais informações, consulte o artigo [BDC 3118750](https://support.microsoft.com/kb/3118750).

![Função conteúdos com os ficheiros de instalação][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definir as tarefas de arranque para sua funções
Tarefas de arranque permitem-lhe efetuar operações antes do início de uma função. Instalar o .NET Framework como parte da tarefa de arranque irá garantir que a arquitetura está instalada antes de qualquer um dos seus código da aplicação é executado. Para obter mais informações sobre arranque tarefas Consulte o artigo: [Executar tarefas de arranque no Azure](cloud-services-startup-tasks.md). 

1. Adicione o seguinte para o ficheiro de *ServiceDefinition.csdef* sob o nó **WebRole** ou **WorkerRole** para todas as funções:
    
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    A configuração acima será executado o comando de consola *Install* com privilégios de administrador para que possa instalar o .NET framework. A configuração também cria uma LocalStorage com o nome *NETFXInstall*. Script de arranque irá defina a pasta temp para utilizar este recurso de armazenamento local para que o programa de instalação do .NET framework será transferido e instalado a partir deste recurso. É importante definir o tamanho deste recurso para, pelo menos, 1024MB para garantir que a arquitetura instalará corretamente. Para obter mais informações sobre arranque tarefas Consulte o artigo: [tarefas de arranque do serviço em nuvem comuns](cloud-services-startup-tasks-common.md) 

2. Criar um ficheiro **Install** e adicioná-la a todas as funções, clique em à direita na função e selecionar **Adicionar > Item existente...**. Para que todas as funções agora deverão ter o ficheiro de instalação do .NET, bem como o ficheiro Install.
    
    ![Função conteúdos com todos os ficheiros][2]

    > [AZURE.NOTE] Utilize um editor de texto simples, como o bloco de notas para criar este ficheiro. Se utilizar o Visual Studio para criar um ficheiro de texto e, em seguida, mudar o nome para 'cmd' o ficheiro pode conter ainda uma marca de ordem de Byte UTF-8 e a executar a primeira linha do script irá resultar num erro. Se fosse utilizar o Visual Studio para criar o ficheiro sair adicionar um REM (comentário) para a primeira linha do ficheiro para é ignorada quando executar. 

3. Adicione o seguinte script para o ficheiro **Install** :

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%   
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```
        
    O script de instalação verifica se o .NET framework versão especificado já está instalado no computador consultando o registo. Se a versão de .NET não está instalada, em seguida, o .net Web Installer é iniciada. Para ajudar a resolver quaisquer problemas de início o script registará atividade todos os num ficheiro denominado *startuptasklog-(currentdatetime). txt* armazenados na *InstallLogs* local.

    > [AZURE.NOTE] O script ainda mostra-lhe como instalar o .NET 4.5.2 ou .NET 4.6 para continuidade. Não é necessário para instalar manualmente .NET 4.5.2 como já está disponível no Azure convidado SO. Em vez de instalar .NET 4.6 diretamente deve instalar .NET 4.6.1 devido [KB 3118750](https://support.microsoft.com/kb/3118750).
      

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurar diagnósticos para transferir os registos de tarefa de arranque para blob armazenamento 
Pode configurar Azure diagnósticos para o qual transferir quaisquer ficheiros de registo gerados pelo script de arranque ou o instalador do .NET ao armazenamento blob para simplificar a resolução de problemas de instalação. Com esta abordagem pode ver os registos por simplesmente transferir os ficheiros de registo de armazenamento de BLOBs em vez de ter de ambiente de trabalho remoto para a função.

Para configurar diagnósticos abrir o *diagnostics.wadcfgx* e adicione o seguinte sob o nó de **diretórios** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Isto irá configurar azure diagnósticos para transferir todos os ficheiros no diretório *registo* em *NETFXInstall* recurso para a conta de armazenamento de diagnóstico no contentor de Blobs do *adicionados instalar* .

## <a name="deploying-your-service"></a>Implementar o seu serviço 
Quando implementar o seu serviço das tarefas de arranque serão executada e instalar o .NET framework se não estiver instalado. Funções de será no estado de disponibilidade enquanto o quadro estiver a instalar e poderá reiniciar mesmo se requer a instalação framework-lo. 

## <a name="additional-resources"></a>Recursos adicionais

- [Instalar o .NET Framework][]
- [Como: determinar quais as versões do .NET Framework estão instaladas][]
- [Resolução de problemas .NET Framework instalações][]

[Como: determinar quais as versões do .NET Framework estão instaladas]: https://msdn.microsoft.com/library/hh925568.aspx
[Instalar o .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Resolução de problemas .NET Framework instalações]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 
