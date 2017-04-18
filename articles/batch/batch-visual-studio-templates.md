<properties
    pageTitle="Visuais Studio modelos para de Azure lote | Microsoft Azure"
    description="Saiba como estes modelos de projeto do Visual Studio podem ajudar a implementar e executar as cargas de trabalho com muitos cluster no lote Azure"
    services="batch"
    documentationCenter=".net"
    authors="fayora"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="visual-studio-project-templates-for-azure-batch"></a>Modelos de projecto do visuais Studio para lote Azure

O **Gestor de tarefa** e **modelos de tarefa processador Visual Studio** para lote fornecem código para ajudá-lo a implementar e executar as cargas de trabalho com muitos cluster no lote com o mínimo de esforço. Este documento descreve estes modelos e fornece orientação sobre como utilizá-las.

>[AZURE.IMPORTANT] Este artigo aborda informações apenas aplicáveis a estes dois modelos e assume que está familiarizado com o serviço de lote e conceitos chave relacionadas com a mesma: conjuntos de dados, calcular nós, tarefas e tarefas, Gestor de tarefas, variáveis de ambiente e outras informações relevantes. Pode encontrar mais informações no [Lote de noções básicas do Azure](batch-technical-overview.md), [Descrição geral da funcionalidade do lote para os programadores](batch-api-basics.md)e [começar a trabalhar com a biblioteca do Azure lote para .NET](batch-dotnet-get-started.md).

## <a name="high-level-overview"></a>Descrição geral

Os modelos de Gestor de tarefa e processador de tarefas podem ser utilizados para criar os dois componentes útil:

* Uma tarefa de Gestor de projecto implementa uma divisão de tarefa que pode dividir uma tarefa para baixo em várias tarefas que podem ser executada de forma independente, paralelas.

* Processador de tarefas que pode ser utilizado para desempenhar pré-processamento e pós-processamento à volta de uma linha de comandos de aplicação.

Por exemplo, num cenário de composição filme, o divisor tarefa seria transformar uma tarefa de filme única centenas ou milhares de tarefas separadas que iria processar frames individuais em separado. Modo correspondente, o processador de tarefa seria invocar a aplicação de composição e todos os processos dependentes que são necessários para compor cada moldura, bem como realizar ações adicionais (por exemplo, copiar a moldura composta para uma localização de armazenamento).

>[AZURE.NOTE] Os modelos de Gestor de tarefa e processador de tarefa são independentes uns dos outros, para que possa escolher utilizar ambas ou apenas um deles, consoante as necessidades da sua tarefa de cluster de e as suas preferências.

Como é mostrado no diagrama abaixo, uma tarefa de cluster que utiliza estes modelos subscreve as três fases:

1. O código do cliente (por exemplo, aplicação, serviço web, etc.) submete uma tarefa ao serviço de lote no Azure, especificar como o programa de Gestor de tarefa de tarefas do seu Gestor de tarefa.

2. O serviço de lote a tarefa de Gestor de trabalho é executado num cluster nó e o divisor tarefa inicia o número especificado de tarefas de processador de tarefa, no como calcular muitas nós conforme necessário, com base nos parâmetros e especificações no código de divisão de tarefa.

3. As tarefas de processador tarefa executada de forma independente, em paralelo, para os dados de entrada do processo e gerar os dados de saída.

![Diagrama que mostra como código do cliente interage com o serviço do lote][diagram01]

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os modelos de lote, terá o seguinte procedimento:

* Um computador com o Visual Studio 2015, ou mais recente, já instalado no mesmo.

* Os modelos de lote, estão disponíveis a partir da [Galeria do Visual Studio] [ vs_gallery] como extensões do Visual Studio. Existem duas formas de colocar os modelos:

  * Instalar os modelos utilizando a caixa de diálogo **extensões e atualizações** no Visual Studio (para obter mais informações, consulte o artigo [Localizar e utilizar extensões de Visual Studio][vs_find_use_ext]). Na caixa de diálogo **Extensions e atualizações** , procurar e transferir as extensões de duas seguintes:

    * Azure lote o Gestor de tarefa com tarefa divisor
    * Processador de tarefas do lote Azure

  * Transferir os modelos a partir da Galeria online para o Visual Studio: [Microsoft Azure lote de modelos de projecto][vs_gallery_templates]

* Se planeia utilizar a funcionalidade de [Pacotes de aplicações](batch-application-packages.md) para implementar o Gestor de tarefa e processador de tarefa para o lote de calcular nós, terá de ligar uma conta de armazenamento à sua conta do lote.

## <a name="preparation"></a>Preparação

Recomendamos que a criação de uma solução que pode conter o seu Gestor de tarefas, bem como o processador de tarefa, uma vez que este pode facilitar a partilhar código entre o seu Gestor de trabalho e programas de processador de tarefa. Para criar esta solução, siga estes passos:

1. Abra o Visual Studio 2015 e selecione **ficheiro** > **Novo** > **projeto**.

2. Em **modelos**, expanda **Outros tipos de projeto**, clique em **Soluções do Visual Studio**e, em seguida, selecione **Solução em branco**.

3. Escreva um nome que descreva a aplicação e o objetivo desta solução (por exemplo, "LitwareBatchTaskPrograms").

4. Para criar a nova solução, clique em **OK**.

## <a name="job-manager-template"></a>Modelo de Gestor de tarefa

O modelo de Gestor de tarefas ajuda-o a implementar uma tarefa de Gestor de tarefa que pode executar as seguintes ações:

* Divida uma tarefa em várias tarefas.
* Submeta essas tarefas para executar no lote.

>[AZURE.NOTE] Para mais informações sobre o Gestor de tarefas, consulte o artigo [Descrição geral da funcionalidade do lote para programadores](batch-api-basics.md#job-manager-task).

### <a name="create-a-job-manager-using-the-template"></a>Criar um Gestor de tarefas utilizando o modelo

Para adicionar um Gestor de tarefa para a solução que criou anteriormente, siga estes passos:

1. Abra a sua solução existente no Visual Studio 2015.

2. No Explorador de solução, o botão direito do rato a solução, clique em **Adicionar** > **Novo projeto**.

3. Em **Visual c#**, clique em **nuvem**e, em seguida, clique em **Gestor de tarefa de lote Azure com divisão de tarefa**.

4. Escreva um nome que descreve as aplicações e identifica este projeto como o Gestor da tarefa (por exemplo, "LitwareJobManager").

5. Para criar o projeto, clique em **OK**.

6. Por fim, a criar o projeto para forçar o Visual Studio para carregar todos os pacotes de NuGet referenciados e para verificar se o projeto é válido antes de começar modificá-la.

### <a name="job-manager-template-files-and-their-purpose"></a>Ficheiros de modelos de Gestor de tarefa e a sua finalidade

Quando cria um projecto utilizando o modelo de Gestor de tarefa, gera três grupos de ficheiros de código:

* O ficheiro de programa principal (Program.cs). Este contém o ponto de entrada do programa e processamento de exceção de nível superior. Não deva normalmente precisa modificar este valor.

* Diretório do quadro. Este contém os ficheiros responsáveis pelo trabalho de 'automático' efetuado pelo programa de Gestor de tarefas – descompactar parâmetros, adicionar tarefas à rotina, etc. Não deva normalmente precisa modificar estes ficheiros.

* O ficheiro de divisão de tarefa (JobSplitter.cs). Este é o local onde irá coloca a lógica específicos da aplicação para dividir uma tarefa em tarefas.

Obviamente pode adicionar ficheiros adicionais conforme necessário para suportar o código de divisão de tarefa, consoante a complexidade da tarefa dividir lógica de.

O modelo também gera padrão ficheiros de projeto .NET como um ficheiro de csproj, App, packages.config, etc.

Os restantes esta secção descreve os ficheiros de diferentes e a sua estrutura de código e explica o que faz cada categoria.

![Visual Studio solução Explorer que mostra o Gestor da tarefa de solução de modelo][solution_explorer01]

**Ficheiros de quadro**

* `Configuration.cs`: Contém o carregamento dos dados de configuração de tarefa como os detalhes da conta lote, as credenciais de conta de armazenamento ligadas, tarefa e informação de tarefa e parâmetros da tarefa. Também fornece acesso ao variáveis de ambiente de definidos pelo lote (consulte o artigo definições de ambiente de tarefas, na documentação lote) através da classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Abstracts a execução da classe de configuração, para que possa teste de unidade o divisor da tarefa utilizando um objeto de configuração de hiperligações falsas ou fictício.

* `JobManager.cs`: Orquestra os componentes do programa de Gestor de tarefa. É responsável pela inicialização a divisão de tarefa, invocar a divisão de tarefa e as tarefas devolvidas pela divisão de tarefa para Submissores a tarefa de distribuição.

* `JobManagerException.cs`: Representa um erro que exige o Gestor de tarefas terminar. JobManagerException é utilizada para moldar erros 'esperados' onde informações específicas de diagnóstico podem ser fornecidas como parte de taxas de cessação.

* `TaskSubmitter.cs`: Esta classe é responsável para adicionar tarefas devolvidas pela divisão de tarefa para o processo. Os agregados de classe JobManager a sequência de tarefas em lotes para eficiente mas atempada adição ao projecto, em seguida, as chamadas TaskSubmitter.SubmitTasks num tópico de fundo para cada secção.

**Divisor de tarefa**

`JobSplitter.cs`: Esta classe contém lógica específicos da aplicação para dividir a tarefa em tarefas. A arquitetura invoca o método de JobSplitter.Split para obter uma sequência de tarefas, que adiciona a tarefa de como o método devolve-los. Esta é a classe onde irá inserção a lógica do seu trabalho. Implemente o método de divisão para devolver uma sequência de instâncias CloudTask que representa as tarefas no qual pretende criar a partições a tarefa.

**Ficheiros de projeto da linha de comandos de .NET padrão**

* `App.config`: Ficheiro de configuração de aplicação .NET padrão.

* `Packages.config`: Ficheiro de dependência do pacote de NuGet padrão.

* `Program.cs`: Contém o ponto de entrada do programa e o processamento de exceção de nível superior.

### <a name="implementing-the-job-splitter"></a>Implementar o divisor da tarefa

Quando abre o projeto de modelo de Gestor de tarefa, o projeto terá o ficheiro de JobSplitter.cs aberto por predefinição. Pode implementar a lógica de divisão para as tarefas na sua carga de trabalho utilizando a apresentação de método Split() abaixo:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] A secção anotada de `Split()` método é a secção apenas o código de modelo de Gestor de tarefa que lhe é destinado modificar adicionando a lógica para dividir as tarefas em várias tarefas. Se pretender modificar uma secção diferente do modelo, certifique-se de que é familiarized com como funciona o lote e experimentar algumas das [exemplos de código do lote][github_samples].

A implementação de Split() tem acesso ao:

* Os parâmetros de tarefa, através de `_parameters` campo.
* O objeto de CloudJob que representa a tarefa, através de `_job` campo.
* O objeto de CloudTask que representa a tarefa de Gestor de tarefas, através de `_jobManagerTask` campo.

O `Split()` aplicação não é necessário adicionar tarefas à tarefa diretamente. Em vez disso, o código deve devolver uma sequência de CloudTask objetos e, estes serão adicionados à tarefa automaticamente pelas classes framework que é apresentado o divisor da tarefa. É comuns a iteração da utilizar # (`yield return`) funcionalidade para implementar o divisores tarefa esta opção permite-as tarefas iniciar a executar o mais cedo possível em vez de à espera de todas as tarefas ser calculadas.

**Falha de divisão de tarefa**

Se o seu divisor tarefa encontrar um erro, quer deverá:

* Terminar a sequência de utilizando o c# `yield break` instrução, na qual caso o Gestor da tarefa será tratado como êxito; ou

* Gerar uma exceção, caso que o Gestor da tarefa será tratado como Ocorreu uma falha e que poderá ser tentada novamente dependendo de como o cliente tem configurado--).

Em ambos os casos, todas as tarefas já devolvido pela divisão de tarefa e adicionada à rotina será elegíveis para ser executado. Se não quiser que este deverá acontecer, em seguida, poderá:

* Terminar a tarefa antes de devolver a partir do divisor da tarefa

* Elaborar a coleção de tarefa inteira antes de a devolver (ou seja, é devolvido um `ICollection<CloudTask>` ou `IList<CloudTask>` em vez de execução do seu divisor da tarefa utilizando uma iteração c#)

* Utilizar dependências de tarefas para efetuar todas as tarefas que dependem da conclusão de Gestor de tarefa

**Número de tentativas de Gestor de tarefa**

Se o Gestor da tarefa falhar, poderá ser repetida pelo serviço lote dependendo das definições de repetição do cliente. Em geral, este é seguro, porque quando a arquitetura adiciona tarefas para a tarefa, ignora quaisquer tarefas que já existem. No entanto, se for dispendioso calcular tarefas, poderá não pretender implicam o custo de recalcular tarefas que já foram adicionadas à tarefa; por outro lado, se executar vários não há garantia para gerar a mesma tarefa IDs, em seguida, o comportamento de 'Ignorar duplicados' irá não funcionam como ricochete. Nestes casos devo estruturar o divisor da tarefa para detetar o trabalho que já foi feito e não repeti-la, por exemplo, executando um CloudJob.ListTasks antes de começar a lucro tarefas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de saída e exceções no modelo de Gestor de tarefa

Códigos de saída e exceções fornecem um mecanismos para determinar o resultado da execução de um programa e podem ajudar a identificar algum problema com a execução do programa. O modelo de Gestor de tarefa implementa os códigos de sair e exceções descritas nesta secção.

Uma tarefa de Gestor de projecto que é implementada com o modelo de Gestor de tarefa pode devolver três códigos de saída possíveis:

| Código | Descrição |
|------|-------------|
| 0    | O Gestor de tarefa foi concluído com êxito. Executou o seu código de divisão de tarefa para a conclusão e todas as tarefas que foram adicionadas à tarefa. |
| 1    | A tarefa de Gestor de projecto Ocorreu uma falha com uma exceção numa peça 'esperada' do programa. A exceção foi convertida para um JobManagerException com informações de diagnóstico e, se possível, sugestões para resolver a falha. |
| 2    | A tarefa de Gestor de projecto Ocorreu uma falha com uma exceção 'inesperada'. A exceção foi tem sessão iniciada saída padrão, mas o Gestor de tarefa não foi possível adicionar quaisquer informações de diagnóstico ou remediação adicionais. |

No caso de falha de Gestor de tarefas da tarefa, algumas tarefas podem ainda foram adicionadas ao serviço antes do erro. Estas tarefas serão executadas como normais. Consulte o artigo "Falha de divisão de tarefa" acima para debate deste caminho de código.

Todas as informações devolvidas pela exceções são escritas na ficheiros stdout.txt e stderr.txt. Para mais informações, consulte o artigo [Processamento de erros](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerações de clientes

Esta secção descreve alguns requisitos da aplicação de cliente ao invocar um Gestor de trabalho baseado neste modelo. Veja [como pode passar parâmetros e variáveis de ambiente do código do cliente](#pass-environment-settings) para obter detalhes sobre prisma parâmetros e definições de ambiente.

**Credenciais obrigatórias**

Para poder adicionar tarefas à Azure rotina, a tarefa de Gestor de projecto requer o Azure lote conta URL e a chave. Tem de passar estes itens no variáveis de ambiente com o nome YOUR_BATCH_URL e YOUR_BATCH_KEY. Pode definir no Gestor de tarefa estas definições de ambiente de tarefa. Por exemplo, de um cliente c#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciais de armazenamento**

Normalmente, o cliente não é necessário fornecer as credenciais da conta ligada de armazenamento para a tarefa de Gestor de projecto porque (um) mais os gestores de projecto não necessita de explicitamente acesso à conta de armazenamento ligadas e (b) a conta de armazenamento ligada é fornecida com frequência para todas as tarefas como uma definição de ambiente comuns para a tarefa. Se não estão a fornecer a conta de armazenamento ligada através das definições de ambiente comuns e o Gestor de tarefa requer o acesso ao armazenamento ligado, deve fornecer as credenciais de armazenamento ligadas da seguinte forma:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Definições de tarefa de Gestor de tarefas**

O cliente deve definir o sinalizador de *killJobOnCompletion* do Gestor de tarefa para **Falso**.

É normalmente seguro para o cliente definir *runExclusive* para **Falso**.

O cliente deve utilizar a coleção de *resourceFiles* ou *applicationPackageReferences* para que a tarefa Gestor executável (e suas DLLs necessários) implementado a nó cluster.

Por predefinição, o Gestor de tarefa não vai ser repetido se falhar. Dependendo do seu lógica de Gestor de tarefa, o cliente poderá pretender ativar tentativas através *das restrições*/*maxTaskRetryCount*.

**Definições da tarefa**

Se o divisor tarefa emite tarefas com dependências, o cliente tem de definir usesTaskDependencies a tarefa como verdadeiro.

No modelo de divisão de tarefa, é invulgar para que os clientes pretende adicionar tarefas à tarefas excede o que cria o divisor da tarefa. O cliente normalmente, por conseguinte, deverá definido a tarefa *onAllTasksComplete* para **terminatejob**.

## <a name="task-processor-template"></a>Modelo de processador de tarefas

Um modelo de processador de tarefas ajuda-o a implementar um processador de tarefas que pode executar as seguintes ações:

* Configure as informações necessárias por cada tarefa lote para executar.
* Execute todas as ações necessárias por cada tarefa lote.
* Guarde tarefa saídas armazenamento persistente.

Apesar de não é necessário um processador de tarefa para executar as tarefas no lote, a chave vantagem de utilizar um processador de tarefa é que fornece um espacial para implementar todas as ações de execução de tarefa uma localização. Por exemplo, se de que precisa para executar várias aplicações no contexto de cada tarefa ou se for necessário copiar dados para o armazenamento persistente depois de concluir cada tarefa.

As ações executadas pelo processador de tarefa podem ser como simples ou complexo e tantas ou poucas, conforme necessário pela sua carga de trabalho. Para além disso, através da implementação de todas as ações de tarefa para um processador de tarefa, pode facilmente atualizar ou adicionar ações com base em alterações aos requisitos de carga de trabalho ou aplicações. No entanto, em alguns casos um processador de tarefa poderá não ser a solução ideal para a sua implementação como pode adicionar a complexidade desnecessária, por exemplo quando a ser executado tarefas que podem ser iniciadas rapidamente a partir de uma linha de comandos simple.

### <a name="create-a-task-processor-using-the-template"></a>Criar um processador de tarefas utilizando o modelo

Para adicionar um processador de tarefa para a solução que criou anteriormente, siga estes passos:

1. Abra a sua solução existente no Visual Studio 2015.

2. No Explorador de solução, com o botão direito a solução, clique em **Adicionar**e, em seguida, clique em **Novo projeto**.

3. Em **Visual c#**, clique em **nuvem**e, em seguida, clique em **Processador de tarefas do Azure lote**.

4. Escreva um nome que descreve as aplicações e identifica este projeto como o processador de tarefa (por exemplo, "LitwareTaskProcessor").

5. Para criar o projeto, clique em **OK**.

6. Por fim, a criar o projeto para forçar o Visual Studio para carregar todos os pacotes de NuGet referenciados e para verificar se o projeto é válido antes de começar modificá-la.

### <a name="task-processor-template-files-and-their-purpose"></a>Ficheiros de modelos de processador de tarefa e a sua finalidade

Quando cria um projecto utilizando o modelo de processador de tarefa, gera três grupos de ficheiros de código:

* O ficheiro de programa principal (Program.cs). Este contém o ponto de entrada do programa e processamento de exceção de nível superior. Não deva normalmente precisa modificar este valor.

* Diretório do quadro. Este contém os ficheiros responsáveis pelo trabalho de 'automático' efetuado pelo programa de Gestor de tarefas – descompactar parâmetros, adicionar tarefas à rotina, etc. Não deva normalmente precisa modificar estes ficheiros.

* O ficheiro de processador de tarefa (TaskProcessor.cs). Este é o local onde irá coloca a lógica específicos da aplicação para executar uma tarefa (normalmente ao ligar para um ficheiro executável existente). Pré e pós-processamento código, tal como transferir dados adicionais ou carregar ficheiros de resultado, também vai aqui.

Obviamente pode adicionar ficheiros adicionais conforme necessário para suportar o código de processador de tarefa, consoante a complexidade da tarefa dividir lógica.

O modelo também gera padrão ficheiros de projeto .NET como um ficheiro de csproj, App, packages.config, etc.

Os restantes esta secção descreve os ficheiros de diferentes e a sua estrutura de código e explica o que faz cada categoria.

![Visual Studio solução Explorer que mostra a solução de modelo de processador de tarefa][solution_explorer02]

**Ficheiros de quadro**

* `Configuration.cs`: Contém o carregamento dos dados de configuração de tarefa como os detalhes da conta lote, as credenciais de conta de armazenamento ligadas, tarefa e informação de tarefa e parâmetros da tarefa. Também fornece acesso ao variáveis de ambiente de definidos pelo lote (consulte o artigo definições de ambiente de tarefas, na documentação lote) através da classe Configuration.EnvironmentVariable.

* `IConfiguration.cs`: Abstracts a execução da classe de configuração, para que possa teste de unidade o divisor da tarefa utilizando um objeto de configuração de hiperligações falsas ou fictício.

* `TaskProcessorException.cs`: Representa um erro que exige o Gestor de tarefas terminar. TaskProcessorException é utilizada para moldar erros 'esperados' onde informações específicas de diagnóstico podem ser fornecidas como parte de taxas de cessação.

**Processador de tarefa**

* `TaskProcessor.cs`: Executa a tarefa. A arquitetura invoca o método de TaskProcessor.Run. Esta é a classe onde irá inserção a lógica específicos da aplicação da sua tarefa. Implementa o método de executar a:
  * Analisar e validar quaisquer parâmetros de tarefa
  * Componha a linha de comandos para qualquer programa externo que pretende invocar
  * Registar quaisquer informações de diagnóstico que poderá necessitar de para fins de depuração
  * Iniciar um processo utilizando nessa linha de comandos
  * Aguardar que o processo para sair
  * Capturar o código de saída do processo para determinar se este foi concluída com êxito ou falha
  * Guarde os ficheiros de saída que pretende manter-se ao armazenamento persistente

**Ficheiros de projeto da linha de comandos de .NET padrão**

* `App.config`: Ficheiro de configuração de aplicação .NET padrão.
* `Packages.config`: Ficheiro de dependência do pacote de NuGet padrão.
* `Program.cs`: Contém o ponto de entrada do programa e o processamento de exceção de nível superior.

## <a name="implementing-the-task-processor"></a>Implementar o processador de tarefa

Quando abre o projecto do modelo processador de tarefa, o projeto terá o ficheiro de TaskProcessor.cs aberto por predefinição. Pode implementar a lógica executar para as tarefas na sua carga de trabalho utilizando o método também é mostrado abaixo:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] A secção anotada o método também é a secção apenas o código de modelo de processador de tarefa que lhe é destinado modificar ao adicionar a lógica executar para as tarefas na sua carga de trabalho. Se pretender modificar uma secção diferente do modelo, consulte pela primeira vez familiarizar-se com como o lote funciona ao rever a documentação do lote e experimentar algumas os lote de exemplos de código.

O método também é responsável por iniciar a linha de comandos, iniciar uma ou mais processos, a aguardar que todos os processos concluir, guardar os resultados e, por fim devolver com um código de saída. O método de também é onde implementar a lógica de processamento para as suas tarefas. A arquitetura de processador tarefa invoca o método (Long Directory) não necessita de ligar o seu próprio.

A implementação de também tem acesso ao:

* Os parâmetros de tarefas, através de `_parameters` campo.
* Os ids de projecto e a tarefa através de `_jobId` e `_taskId` campos.
* A configuração da tarefa, através de `_configuration` campo.

**Falha na tarefa**

Em caso de falha, pode sair o método também por deitar uma exceção, mas isto deixa o processador de exceção de nível superior controlam o código de saída da tarefa. Se precisar de controlar o código de saída para que possa distinguir diferentes tipos de falha, por exemplo para fins de diagnóstico ou porque algumas modos de falha devem terminar a tarefa e outras pessoas não devem, em seguida, deve saia o método (Long Directory), devolvendo um código de saída não zero. Isto torna-se o código de saída da tarefa.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos de saída e exceções no modelo de processador de tarefa

Códigos de saída e exceções fornecem um mecanismos para determinar o resultado da execução de um programa e podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo de tarefa processador implementa os códigos de sair e exceções descritas nesta secção.

Uma tarefa de processador de tarefa que é implementada com o modelo de processador de tarefa pode devolver três códigos de saída possíveis:

| Código | Descrição |
|------|-------------|
|  [Process.ExitCode][process_exitcode] | Executou o processador de tarefa para a conclusão. Tenha em atenção que isto não implica que o programa chamado que foi efetuada com êxito – apenas que o processador de tarefa invocá-lo com êxito e executado qualquer processamento pós sem exceções. O significado do código de saída depende do programa de invocado – normalmente código de saída 0 significa que o programa foi concluída com êxito e outro código de sair significa que o programa falha. |
| 1    | Processador a tarefa Ocorreu uma falha com uma exceção numa peça 'esperada' do programa. A exceção foi convertida para um `TaskProcessorException` com informações de diagnóstico e, se possível, sugestões para resolver a falha. |
| 2    | Processador a tarefa Ocorreu uma falha com uma exceção 'inesperada'. A exceção foi tem sessão iniciada saída padrão, mas o processador de tarefa não foi possível adicionar quaisquer informações de diagnóstico ou remediação adicionais. |

>[AZURE.NOTE] Se o programa que invoca utiliza códigos de saída 1 e 2 para indicar os modos de falha específico, em seguida, utilizar códigos de saída 1 e 2 para erros de processador de tarefa é ambíguo. Pode alterar estes códigos de erro de processador de tarefa para códigos de saída distintivo ao editar os casos exceção no ficheiro Program.cs.

Todas as informações devolvidas pela exceções são escritas na ficheiros stdout.txt e stderr.txt. Para mais informações, consulte o artigo processamento de erros, na documentação lote.

### <a name="client-considerations"></a>Considerações de clientes

**Credenciais de armazenamento**

Se o processador de tarefa utiliza o armazenamento de Blobs do Azure para persistirem saídas, por exemplo, utilizando a biblioteca de ajuda de convenções de ficheiro, em seguida, necessita de acesso a *um da* nuvem armazenamento conta credenciais *ou* um URL de contentor blob que inclui uma assinatura de acesso partilhado (SA). O modelo inclui suporte para fornecer credenciais através de variáveis de ambiente comuns. O cliente pode ser efetuada com as credenciais de armazenamento da seguinte forma:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A conta de armazenamento, em seguida, está disponível na classe TaskProcessor através de `_configuration.StorageAccount` propriedade.

Se preferir utilizar um URL de contentor com SA, também pode passar isto através de uma definição de ambiente de comuns de tarefa, mas o modelo de processador tarefa atualmente não inclui suporte incorporado para esta.

**Configuração do armazenamento**

Recomenda-se que a tarefa de Gestor de cliente ou de tarefa de criar quaisquer contentores necessários tarefas antes de adicionar as tarefas para a tarefa. Este é obrigatória que se utilizar um URL de contentor com SA, tal como um URL não inclui permissão para criar o contentor. Recomenda-se, mesmo se a verificação ser efetuada com as credenciais de conta de armazenamento, tal como guarda todas as tarefas ter de contactar o CloudBlobContainer.CreateIfNotExistsAsync no contentor.

## <a name="pass-parameters-and-environment-variables"></a>Pass parâmetros e variáveis de ambiente

### <a name="pass-environment-settings"></a>Pass ambiente definições

Um cliente pode passar informações para a tarefa de Gestor de projecto sob a forma de definições de ambiente. Estas informações, em seguida, podem ser utilizadas pela tarefa de Gestor de projecto quando as tarefas de processador de tarefa que serão executada como parte da tarefa cluster de a gerar. Exemplos das informações que pode passar como definições de ambiente são:

* Teclas de nome e a conta de conta de armazenamento
* URL do lote conta
* Chave da conta batch

O serviço de lote tem um mecanismos simples para transmitir as definições de ambiente para uma tarefa de Gestor de projecto utilizando o `EnvironmentSettings` propriedade na [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Por exemplo, para obter o `BatchClient` instância de uma conta do lote, pode ser efetuada com como o URL e partilhadas credenciais da chave da conta do lote o código de variáveis de cliente do ambiente. Da mesma forma, para aceder à conta de armazenamento que está ligada à conta do lote, pode ser efetuada com o nome da conta de armazenamento e a chave de conta de armazenamento como variáveis de ambiente.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passar parâmetros para o modelo de Gestor de tarefa

Em muitos casos, é útil para passar por tarefa parâmetros para a tarefa de Gestor de tarefa, para controlar o trabalho dividir processo ou para configurar as tarefas para a tarefa. Pode fazê-lo ao carregar um ficheiro JSON nomeado parameters.json como um ficheiro de recursos para a tarefa de Gestor de projecto. Os parâmetros podem, em seguida, tornar-se disponível na `JobSplitter._parameters` campo no modelo de Gestor de tarefa.

>[AZURE.NOTE] Processador de parâmetro incorporados suporta apenas a cadeia de cadeia dicionários. Se pretender passar valores JSON complexas como valores de parâmetros, terá de ser efetuada com estas como cadeias e analisá-los na divisão de tarefa ou modificar a arquitetura `Configuration.GetJobParameters` método.

### <a name="pass-parameters-to-the-task-processor-template"></a>Passar parâmetros para o modelo de processador de tarefa

Também pode passar parâmetros para tarefas individuais implementadas utilizando o modelo de processador de tarefa. Tal como com o modelo de Gestor de tarefa, o modelo de processador tarefa procura um ficheiro de recursos com nome

Parameters.JSON e se verificou que carrega-lo como o dicionário de parâmetros. Existem duas opções como passar parâmetros para as tarefas de processador tarefa:

* Reutilize os parâmetros de tarefa JSON. Isto funciona bem se os parâmetros apenas são aqueles toda a tarefa (por exemplo, uma altura de composição e a largura). Para efetuar esta ação, criar uma CloudTask no divisor da tarefa, adicione uma referência ao objeto parameters.json recurso ficheiro a partir ResourceFiles da tarefa de Gestor de projecto (`JobSplitter._jobManagerTask.ResourceFiles`) à coleção de ResourceFiles a CloudTask.

* Gerar e carregar um documento de tarefas específicas parameters.json como parte de execução de divisão de tarefas e fazer referência esse blob na coleção de ficheiros de recursos da tarefa. Isto é necessário se diferentes tarefas tiverem diferentes parâmetros. Um exemplo poderá ser um cenário de composição de 3D onde o índice de moldura lhe é transmitido a tarefa como um parâmetro.

>[AZURE.NOTE] Processador de parâmetro incorporados suporta apenas a cadeia de cadeia dicionários. Se pretender passar valores JSON complexas como valores de parâmetros, terá de ser efetuada com estas como cadeias e analisá-los no processador de tarefa ou modificar a arquitetura `Configuration.GetTaskParameters` método.

## <a name="next-steps"></a>Próximos passos

### <a name="persist-job-and-task-output-to-azure-storage"></a>Manter o projecto e a tarefa saída ao armazenamento do Azure

Outra ferramenta útil no desenvolvimento de soluções para lote é [Convenções de ficheiro do Azure lote][nuget_package]. Utilize esta biblioteca de classes .NET (atualmente em pré-visualização) nas aplicações do lote .NET para facilmente armazenar e obter resultados de tarefa de armazenamento do Windows Azure e para. [Rotina Azure persistirem e tarefas de saída](batch-task-output.md) contém um debate completo da biblioteca e a sua utilização.

### <a name="batch-forum"></a>Fórum do lote

[Fórum do Azure lote] [ forum] no MSDN é um ótimo local para discutir lote e perguntas sobre o serviço. Cabeça no sobre para mensagens "autocolantes" úteis e publique as suas perguntas que possam ocorrer enquanto que cria as soluções do lote.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
