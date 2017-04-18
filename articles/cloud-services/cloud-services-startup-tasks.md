<properties 
pageTitle="Executar tarefas de arranque no serviços em nuvem Azure | Microsoft Azure" 
description="Tarefas de arranque ajudam a preparar o seu ambiente de serviço de nuvem para a sua aplicação. Isto ensina como funcionam tarefas de arranque e como torná-las" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Como configurar e executar tarefas de arranque para um serviço na nuvem

Pode utilizar tarefas de arranque para efetuar operações antes do início de uma função. Operações que poderá pretender executar incluem instalar um componente de, a registar componentes COM, a definição de chaves de registo ou iniciar um processo longo em execução.

>[AZURE.NOTE] Tarefas de arranque não são aplicáveis a máquinas virtuais, apenas a nuvem serviço Web e funções de trabalho.

## <a name="how-startup-tasks-work"></a>Como funcionam tarefas de arranque

Tarefas de arranque são ações que são tomadas antes das funções começarem e são definidas no ficheiro [ServiceDefinition.csdef] utilizando o elemento de [tarefa] dentro do elemento de [arranque] . Frequentemente tarefas de arranque são ficheiros de comandos, mas também pode ser aplicações da consola ou lote ficheiros que comecem scripts de PowerShell.

Variáveis de ambiente passam informações para uma tarefa de arranque e armazenamento local pode ser utilizado para transmitir informações de uma tarefa de arranque. Por exemplo, uma variável de ambiente pode especificar o caminho para um programa que pretende instalar e podem ser escritos ficheiros para o armazenamento de local, em seguida, pode ser lido posteriormente pelo seu funções.

A tarefa de arranque pode iniciar sessão informações e erros do directório especificado pela variável de ambiente **TEMP** . Durante a tarefa de arranque, a variável de ambiente de **TEMP** é resolvido para o *c:\\recursos\\temp\\[guid]. [ nome da função]\\RoleTemp* diretório quando a ser executado na nuvem.

Tarefas de arranque podem também ser executadas várias vezes entre ser reiniciado. Por exemplo, a tarefa de arranque será executada sempre que a função Reciclagens da e função Reciclagens não podem incluir sempre um reinício. Tarefas de arranque devem ser escritas de uma forma que permite-lhes executar várias vezes sem problemas.

Tarefas de arranque tem de terminar com um **errorlevel** (ou o código de saída) de zero para o processo de arranque para concluir. Se uma tarefa de arranque termina com um diferente de zero **errorlevel**, a função não será iniciado.


## <a name="role-startup-order"></a>Ordem de arranque de função

A seguinte lista apresenta o procedimento de arranque de funções no Azure:

1. A instância está marcada como **inicial** e não receber tráfego.

2. Todas as tarefas de arranque são executadas de acordo com o respectivo atributo **taskType** .
    - As tarefas **simples** são executadas modo síncrono, uma de cada vez.
    - As tarefas de **fundo** e de **primeiro plano** são iniciadas modo assíncrono, paralelo à tarefa de arranque.  
       
    > [AZURE.WARNING] IIS pode não estar totalmente configurado durante a fase de tarefa de arranque no processo de arranque, para que os dados específicos da função poderão não estar disponíveis. Tarefas de arranque que requerem dados específicos de funções devem utilizar [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. O processo de anfitrião da função é iniciado e o site é criado no IIS.

4. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) é chamado.

5. A instância está marcada como **pronto** e tráfego é encaminhado para a instância.

6. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) é chamado.


## <a name="example-of-a-startup-task"></a>Exemplo de uma tarefa de arranque

Tarefas de arranque são definidas no ficheiro de [ServiceDefinition.csdef] , no elemento de **tarefa** . O atributo de **linha de comandos** Especifica o nome e parâmetros do ficheiro de comandos de arranque ou comando da consola, o atributo **executionContext** Especifica o nível de privilégio da tarefa de arranque e o atributo **taskType** Especifica como será executada a tarefa.

Neste exemplo, uma variável de ambiente **MyVersionNumber**, é criada para a tarefa de arranque e defina o valor "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

No exemplo seguinte, o ficheiro de comandos **Startup.cmd** escreve na linha "a versão atual é 1.0.0.0" para o ficheiro StartupLog.txt no diretório especificado pela variável de ambiente de TEMP. O `EXIT /B 0` linha garante que a tarefa de arranque termina com um **errorlevel** de zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] No Visual Studio, a propriedade **Copiar para directório de saída** para o seu ficheiro de comandos de arranque deve ser definida para **Copiar sempre** para se certificar de que o seu ficheiro de comandos de arranque está corretamente implementado ao seu projeto no Azure (**approot\\posição** para funções de Web e **approot** para funções de trabalho).

## <a name="description-of-task-attributes"></a>Descrição dos atributos da tarefa

A seguinte tabela descreve os atributos do elemento **tarefa** no ficheiro [ServiceDefinition.csdef] :

**linha de comandos** - Especifica a linha de comandos para a tarefa de arranque:

- O comando, com parâmetros de linha de comandos opcional, que começa a tarefa de arranque.
- Perguntas, este é o nome do ficheiro de um ficheiro batch cmd ou. bat.
- A tarefa é relativo a AppRoot\\pasta Bin para a implementação. Variáveis de ambiente não são expandidas para determinar o caminho e o ficheiro da tarefa. Se for necessária uma expansão ambiente, pode criar um script de cmd pequenas que liga para a tarefa de arranque.
- Pode ser uma aplicação de consola ou um ficheiro batch que inicia um [PowerShell script](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - Especifica o nível de privilégio para a tarefa de arranque. O nível de privilégio pode estar limitado ou elevado:

- **limitado**  
A tarefa de arranque será executado com os mesmos privilégios do que a função. Quando o atributo **executionContext** para o elemento [Runtime] também é **limitado**, são utilizados os privilégios de utilizador.

- **elevados**  
A tarefa de arranque será executado com privilégios de administrador. Esta opção permite-tarefas de arranque para instalar os programas, fazer alterações à configuração do IIS, efetuar alterações de registo e outras tarefas de nível de administrador, sem aumentar o nível de privilégio da função própria.  

> [AZURE.NOTE] O nível de privilégio de uma tarefa de arranque não precisam de ser igual a função própria.

**taskType** - Especifica a forma como uma tarefa de arranque é executada.

- **simples**  
Tarefas são executadas modo síncrono, uma de cada vez, pela ordem especificado no ficheiro [ServiceDefinition.csdef] . Quando termina uma tarefa de arranque **simples** com um **errorlevel** de zero, a próxima tarefa de arranque **simples** é executada. Se não existem tarefas mais **simples** arranque executar, em seguida, a função propriamente dito será iniciada.   

    > [AZURE.NOTE] Se a tarefa **simples** termina com um diferente de zero **errorlevel**, a instância será bloqueada. Tarefas de arranque subsequentes **simples** e a função propriamente dito, não serão iniciado.

    Para se certificar de que o seu ficheiro batch termina com um **errorlevel** de zero, execute o comando `EXIT /B 0` no final do processo ficheiro batch.

- **fundo**  
Tarefas são executadas forma assíncrona, paralelas com o arranque da função.

- **primeiro plano**  
Tarefas são executadas forma assíncrona, paralelas com o arranque da função. A chave diferença entre um **primeiro plano** e de uma tarefa de **fundo** é que uma tarefa de **primeiro plano** impede que a função a partir da Reciclagem ou encerrar até a tarefa foi terminado. As tarefas em **segundo plano** não têm esta restrição.

## <a name="environment-variables"></a>Variáveis de ambiente

Variáveis de ambiente são uma forma para transmitir informações para uma tarefa de arranque. Por exemplo, pode colocar o caminho para uma BLOBs que contém um programa para instalar, ou números de porta que irá utilizar a sua função ou definições para funcionalidades de controlo da sua tarefa de arranque.

Existem dois tipos de variáveis de ambiente de tarefas de arranque; variáveis de ambiente estático e variáveis de ambiente com base em membros da classe [RoleEnvironment] . Ambos estão na secção [ambiente] do ficheiro [ServiceDefinition.csdef] e utilizam ambos o atributo elemento e o **nome da** [variável] .

Variáveis de ambiente estático utiliza o atributo **valor** do elemento [variável] . O exemplo acima cria a variável de ambiente **MyVersionNumber** que tem um valor estático de "**1.0.0.0**". Outro exemplo seria criar uma variável de ambiente de **StagingOrProduction** que pode definir manualmente a valores de "**teste**" ou "**produção**" para executar ações de arranque diferente com base no valor da variável de ambiente **StagingOrProduction** .

Variáveis de ambiente com base em membros da classe RoleEnvironment não utilize o atributo **valor** do elemento [variável] . Em vez disso, o elemento subordinado [RoleInstanceValue] , com o valor do atributo **XPath** adequado, são utilizados para criar uma variável de ambiente com base num membro específico a classe de [RoleEnvironment] de. Valores para o atributo **XPath** aceder a vários valores de [RoleEnvironment] podem ser encontrados [aqui](cloud-services-role-config-xpath.md).



Por exemplo, para criar uma variável de ambiente que é "**true**" quando a instância está em execução no cluster emulador e "**Falso**" quando a ser executado na nuvem, utilize os seguintes elementos de [variável] e [RoleInstanceValue] :

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Próximos passos
Saiba como efetuar algumas [tarefas comuns de arranque](cloud-services-startup-tasks-common.md) com o seu serviço de nuvem.

[Pacote](cloud-services-model-and-package.md) seu serviço de nuvem.  


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Arranque]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Tempo de execução]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variável]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx