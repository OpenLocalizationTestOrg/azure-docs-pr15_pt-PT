<properties
    pageTitle="Automaticamente escala calcular nós num conjunto de dados Azure lote | Microsoft Azure"
    description="Ative a escala automática num agrupamento de nuvem para ajustar dinamicamente o número de nós de cluster no conjunto."
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="batch"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="multiple"
    ms.date="10/14/2016"
    ms.author="marsma"/>

# <a name="automatically-scale-compute-nodes-in-an-azure-batch-pool"></a>Dimensionar automaticamente cluster nós num conjunto de dados lote Azure

Com uma escala automática, o serviço do Azure lote dinamicamente pode adicionar ou remover cluster nós num conjunto de dados com base nos parâmetros definidos por si. Potencialmente pode poupar tempo e dinheiro ao ajustar automaticamente a quantidade de energia cluster utilizada pela sua aplicação – adicionar nós como aumento de pedidos de tarefas do seu trabalho e removê-los quando estes diminuem.

Ativar a escala automática num conjunto de nós de cluster associando com o mesmo uma *fórmula autoscale* definidos por si, tais como com a [PoolOperations.EnableAutoScale] [ net_enableautoscale] método na biblioteca do [Lote .NET](batch-dotnet-get-started.md) . Em seguida, o serviço do lote utiliza esta fórmula para determinar o número de nós de cluster que são necessários para executar a sua carga de trabalho. Lote responde à métricas de serviço amostras de dados que são recolhidas periodicamente e ajusta o número de nós de cluster no conjunto de um intervalo de configuráveis com base na sua fórmula.

Pode ativar a escala automática quando é criado um conjunto de dados, ou um conjunto existente. Também pode alterar uma fórmula existente num agrupamento de que é "autoscale" ativado. Lote fornece a capacidade de avaliar fórmulas antes atribuindo-lhes para conjuntos de dados, bem como monitorizar o estado de será executado dimensionamento automático.

## <a name="automatic-scaling-formulas"></a>Automático as fórmulas de dimensionamento

Uma fórmula de dimensionamento automática for um valor de cadeia que pode definir que contém uma ou mais instruções e está atribuído a um agrupamento [autoScaleFormula] [ rest_autoscaleformula] elemento (lote resto) ou [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] propriedade (lote .NET). Quando atribuída a um conjunto de dados, o serviço de lote utiliza a fórmula para determinar o número de destino de nós de cluster no conjunto de para o intervalo seguinte de processamento (mais informações sobre intervalos mais tarde). A cadeia de fórmula não pode exceder 8 KB de tamanho, pode incluir até 100 declarações que são separadas por ponto e vírgula e podem incluir as quebras de linha e os comentários.

Poderá pensar automático as fórmulas de dimensionamento que utiliza um autoscale lote "idioma". As instruções fórmulas são expressões formadas livre que podem incluir variáveis definidos pelo serviço (variáveis definidas pelo serviço lote) e variáveis definidas pelo utilizador (variáveis definidos por si). Poderem efetuar várias operações nestes valores utilizando tipos incorporados, operadores e funções. Por exemplo, uma instrução poderá demorar da seguinte forma:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Fórmulas contêm geralmente várias declarações efetuar operações nos valores que são obtidos em demonstrações anteriores. Por exemplo, pela primeira vez podemos obter um valor para `variable1`, em seguida, a passar para uma função para preencher `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Com estas instruções na sua fórmula, o seu objetivo é chegar a um número de nós de cluster que o conjunto deve ser dimensionado para – o número de **destino** de **nós dedicada**. Este número pode ser superior, inferior ou igual ao número atual de nós do grupo. Lote avalia fórmula de autoscale um agrupamento num intervalo específico (os[intervalos automáticos de dimensionamento](#automatic-scaling-interval) são descritos abaixo). Em seguida, ajustará o número de destino de nós no conjunto de para o número que especifica a fórmula autoscale no momento da avaliação.

Como um breve exemplo, esta fórmula duas linhas autoscale Especifica que o número de nós deve ser ajustado de acordo com o número de tarefas ativas, até um máximo de 10 nós de cluster:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

As secções alguns seguintes deste artigo abordam as várias entidades que compõem fórmulas autoscale, incluindo variáveis, operadores, operações e funções. Encontrará informações sobre como obter vários cluster recurso e tarefa métricas dentro de lote. Pode utilizar estes métricas de forma inteligente ajustar contagem de nó do seu conjunto com o estado de tarefas e a utilização de recursos. Em seguida, irá obter informações sobre como construir uma fórmula e ativar a escala automática num conjunto de dados utilizando o resto do lote e o .NET APIs. Vamos irá terminar configuração com alguns exemplos de fórmulas.

> [AZURE.IMPORTANT] Cada conta Azure lote está limitada a um número máximo de núcleos (e, consequentemente, nós de cluster) que pode ser utilizado para processamento. O serviço de lote irá criar nós apenas por excesso para esse limite core. Por conseguinte, não poderá atingir o número de destino de nós de cluster especificado por uma fórmula. Consulte o artigo [Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md) para obter informações sobre a visualização e aumentar as quotas de conta.

## <a name="variables"></a>Variáveis

Pode utilizar variáveis **definidos pelo serviço** e de **definidos pelo utilizador** no autoscale nas suas fórmulas. Variáveis definidos pelo serviço incorporado para o serviço do lote algumas são leitura / escrita e algumas são só de leitura. Variáveis definidas pelo utilizador são as variáveis que *pode* definir. A fórmula de exemplo de duas linhas acima, `$TargetDedicated` é um serviço definidos pelo variável, enquanto `$averageActiveTaskCount` é uma variável definidos pelo utilizador.

As tabelas abaixo mostram variáveis de leitura / escrita e só de leitura que são definidas pelo serviço lote.

Pode **obter** e **defina** os valores destas variáveis definidos pelo serviço para gerir o número de nós de cluster num conjunto de dados:

| Leitura / escrita definidos pelo serviço variáveis | Descrição |
| --- | --- |
| $TargetDedicated | O número de **destino** de **dedicado nós de cluster** para o conjunto. Este é o número de nós de cluster que o conjunto deve ser dimensionado para. É um número de "destino" uma vez que é possível para um conjunto de dados não atingir o número de destino de nós. Isto pode ocorrer se o número de destino de nós é modificado novamente por uma avaliação do autoscale subsequentes antes do conjunto de atingiu o destino inicial. Também pode acontecer se uma quota conta lote nó ou core for atingida antes de atingir o número de destino de nós. |
| $NodeDeallocationOption | A ação que ocorre quando são removidos nós de cluster a partir de um conjunto de dados. Valores possíveis são:<ul><li>**requeue**– termina tarefas imediatamente e coloca-los novamente na fila de tarefas, para que estes são reagendadas.<li>**Terminar**– termina tarefas imediatamente e remove-los de fila de tarefas.<li>**taskcompletion**– aguarda atualmente em execução de tarefas para concluir e, em seguida, remove o nó do conjunto.<li>**retaineddata**– espera para todos os locais retidos tarefa dados no nó para desorganizado antes de remover o nó do conjunto.</ul> |

Pode **obter** o valor destas variáveis definidos pelo serviço para fazer ajustes com baseiam no métricas de serviço lote:

| Variáveis definidos pelo serviço só de leitura | Descrição |
| --- | --- |
| $CPUPercent | A percentagem média de utilização da CPU. |
| $WallClockSeconds | O número de segundos consumidas. |
| $MemoryBytes | O número médio de megabytes utilizado. |
| $DiskBytes | O número médio de gigabytes utilizado no disco local. |
| $DiskReadBytes | O número de bytes lidos. |
| $DiskWriteBytes | O número de bytes escritos. |
| $DiskReadOps | A contagem de leitura das operações do disco executado. |
| $DiskWriteOps | A contagem de escrita disco operações efetuadas. |
| $NetworkInBytes | O número de bytes de entrada. |
| $NetworkOutBytes | O número de bytes de saída. |
| $SampleNodeCount | A contagem de nós de cluster. |
| $ActiveTasks | O número de tarefas num Estado ativo. |
| $RunningTasks | O número de tarefas no estado de execução. |
| $PendingTasks | A soma dos $ActiveTasks e $RunningTasks. |
| $SucceededTasks | O número de tarefas que foi concluída com êxito. |
| $FailedTasks | O número de tarefas que falhou. |
| $CurrentDedicated | O número atual dedicada de calcula nós. |

> [AZURE.TIP] As variáveis só de leitura, definidos pelo serviço que são apresentadas acima são *objetos* que fornecem diferentes métodos para aceder aos dados associados a cada. Para mais informações, consulte [obter dados de exemplo](#getsampledata) abaixo.

## <a name="types"></a>Tipos de

Estes **tipos de** são suportadas numa fórmula.

- dupla
- doubleVec
- doubleVecList
- cadeia
- carimbo de data/hora – carimbo é uma estrutura composta que contém os membros que se seguem:

    - ano
    - mês (1 a 12)
    - dia (1 – 31)
    - dia da semana (em formato de número, por exemplo, 1, segunda-feira)
    - hora (no formato de número de 24 horas, por exemplo, 13 significa 1 PM)
    - minuto (00 a 59)
    - segundo (00 a 59)
- TimeInterval

    - TimeInterval_Zero
    - TimeInterval_100ns
    - TimeInterval_Microsecond
    - TimeInterval_Millisecond
    - TimeInterval_Second
    - TimeInterval_Minute
    - TimeInterval_Hour
    - TimeInterval_Day
    - TimeInterval_Week
    - TimeInterval_Year

## <a name="operations"></a>Operações

Estas **operações** são permitidas tipos de que são listados acima.

| Operação                             | Operadores suportados   | Tipo de resultado   |
| ------------------------------------- | --------------------- | ------------- |
| dupla *operador* duplo              | +, -, *, /            | dupla            |
| dupla *operador* timeinterval        | *                     | TimeInterval      |
| doubleVec *operador* duplo           | +, -, *, /            | doubleVec         |
| doubleVec *operador* doubleVec        | +, -, *, /            | doubleVec         |
| TimeInterval *operador* duplo        | *, /                  | TimeInterval      |
| TimeInterval *operador* timeinterval  | +, -                  | TimeInterval      |
| carimbo de data/hora TimeInterval *operador*     | +                     | data/hora         |
| carimbo *operador* timeinterval     | +                     | data/hora         |
| carimbo de data/hora *operador* de data/hora        | -                     | TimeInterval      |
| *operador*duplo                      | -, !                  | dupla            |
| *operador*timeinterval                | -                     | TimeInterval      |
| dupla *operador* duplo              | <, < =, = =, > =, >,! =  | dupla            |
| cadeia de *operador* de cadeia              | <, < =, = =, > =, >,! =  | dupla            |
| carimbo de data/hora *operador* de data/hora        | <, < =, = =, > =, >,! =  | dupla            |
| TimeInterval *operador* timeinterval  | <, < =, = =, > =, >,! =  | dupla            |
| dupla *operador* duplo              | & &, & #124; & #124;      | dupla            |

Quando testar um valor duplo com um operador Ternário (`double ? statement1 : statement2`), é diferente de zero **verdadeira**e zero for **falsa**.

## <a name="functions"></a>Funções

Estas **funções** de predefinido estão disponíveis para utilizar na definição de uma fórmula de dimensionamento automática.

| Função                          | Tipo de valor devolvido   | Descrição
| --------------------------------- | ------------- | --------- |
| AVG(doubleVecList)                | dupla        | Devolve o valor médio para todos os valores a doubleVecList.
| Len(doubleVecList)                | dupla        | Devolve o comprimento do vector que é criado a partir do doubleVecList.
| LG(Double)                        | dupla        | Devolve o registo de base 2 da faça.
| LG(doubleVecList)                 | doubleVec     | Devolve o registo de componentwise base 2 do doubleVecList. Um vec(double) explicitamente tem de ser transmitido para o parâmetro. Caso contrário, é considerada a versão de lg(double) duplo.
| ln(Double)                        | dupla        | Devolve o registo de natural do faça.
| ln(doubleVecList)                 | doubleVec     | Devolve o registo de componentwise base 2 do doubleVecList. Um vec(double) explicitamente tem de ser transmitido para o parâmetro. Caso contrário, é considerada a versão de lg(double) duplo.
| log(Double)                       | dupla        | Devolve o registo de base 10 da faça.
| log(doubleVecList)                | doubleVec     | Devolve o registo de componentwise base 10 do doubleVecList. Um vec(double) explicitamente tem de ser transmitido para o parâmetro duplo único. Caso contrário, é considerada a versão de log(double) duplo.
| Max(doubleVecList)                | dupla        | Devolve o valor máximo de doubleVecList.
| min(doubleVecList)                | dupla        | Devolve o valor mínimo de doubleVecList.
| NORM(doubleVecList)               | dupla        | Devolve a norma duas do vector que é criado a partir do doubleVecList.
| percentil (v doubleVec, p duplo) | dupla        | Devolve o elemento de percentil do vector v.
| rand)                            | dupla        | Devolve um valor aleatório entre 0,0 e 1,0.
| Range(doubleVecList)              | dupla        | Devolve a diferença entre o mínimo e máximos valores o doubleVecList.
| Std(doubleVecList)                | dupla        | Devolve o desvio-padrão da amostra dos valores de doubleVecList.
| STOP()                            |               | Deixa de avaliação da expressão autoscaling.
| SUM(doubleVecList)                | dupla        | Devolve a soma de todos os componentes da doubleVecList.
| hora (dateTime de cadeia = "")          | data/hora     | Devolve o carimbo de hora da hora atual, se não são transmitidos parâmetros ou o carimbo de data / hora da cadeia de data/hora, se lhe é transmitido. Formatos de data/hora suportados são W3C DTF e RFC 1123.
| VAL (v doubleVec, i duplo)        | dupla        | Devolve o valor do elemento que está na localização i no vector v, com um índice inicial de zero.

Algumas das funções que são descritas na tabela acima podem aceitar uma lista como um argumento. A lista separados por vírgulas é qualquer combinação de *doubleVec*e *dupla* . Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor de *doubleVecList* é convertido para uma única *doubleVec* antes de avaliação. Por exemplo, se `v = [1,2,3]`, em seguida, chamar `avg(v)` é equivalente ao chamar `avg(1,2,3)`. Chamar `avg(v, 7)` é equivalente ao chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados de exemplo

Fórmulas Autoscale agir em dados de métricas (amostras) que são fornecidos pelo serviço lote. Uma fórmula aumenta ou diminui o tamanho do conjunto baseado nos valores que obtém a partir do serviço. As variáveis definidos pelo serviço que são descritas acima são objetos que fornecem diferentes métodos para aceder aos dados que está associados esse objeto. Por exemplo, a expressão seguinte mostra um pedido para obter os últimos cinco minutos de utilização da CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descrição |
| --- | --- |
| GetSample() | O `GetSample()` método devolve um vector de amostras de dados.<br/><br/>Um exemplo é 30 segundos vale de dados de métricas. Por outras palavras, amostras são obtidas a cada 30 segundos. Mas, conforme indicado abaixo, existe um atraso entre quando uma amostra é recolhida e quando está disponível para uma fórmula. Como tal, não todas as amostras por um determinado período de tempo poderão estar disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica o número de amostras, para obter a partir dos exemplos mais recentes que foram recolhidos.<br/><br/>`GetSample(1)`Devolve o último exemplo disponível. Para gostar de métricas `$CPUPercent`, no entanto, esta não deve ser utilizada porque é impossível saber *Quando* a amostra foram recolhida. Poderá ser recente ou, devido a problemas de sistema, poderá ser muito mais antigo. É aconselhável nestes casos para utilizar um intervalo de tempo, conforme apresentado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica um período de tempo para recolher dados de exemplo. Opcionalmente, também especifica a percentagem de amostras de que tem de estar disponível no intervalo de tempo requerido.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`deve devolver 20 amostras se todas as amostras por últimos dez minutos estiverem presentes no histórico de CPUPercent. Se o último minuto do histórico não estava disponível, no entanto, apenas 18 amostras seriam devolvidas. Neste caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`seria falhar porque apenas 90 por cento das amostras estão disponíveis.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`seria teve êxito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica um período de tempo de recolha de dados, com uma hora de início e uma hora de fim.<br/><br/>Tal como mencionado acima, existe um atraso entre quando uma amostra é recolhida e quando está disponível para uma fórmula. Tal deve ser considerado quando utiliza o `GetSample` método. Consulte o artigo `GetSamplePercent` abaixo.|
| GetSamplePeriod() | Devolve o período de amostras que foram tomadas num conjunto de dados de exemplo histórico. |
| Count() | Devolve o número total de amostras no histórico de métrico. |
| HistoryBeginTime() | Devolve o carimbo de hora do exemplo de dados disponíveis mais antigo para a métrica do. |
| GetSamplePercent() |Devolve a percentagem de amostras que estão disponíveis para um intervalo de tempo especificado. Por exemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Uma vez que o `GetSample` método falha se a percentagem de amostras devolvido é menor que o `samplePercent` especificado, pode utilizar o `GetSamplePercent` método para verificar se pela primeira vez. Em seguida, pode executar uma ação alternativa se amostras insuficientes estiverem presentes, sem parar a avaliação de dimensionamento automática.|

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Amostras, percentagem de exemplo e o método de *GetSample()*

A operação principais de uma fórmula autoscale é obter dados de métricos de tarefa e recurso e, em seguida, ajuste o tamanho de conjunto de dados com base em que os dados. Como tal, é importante ter uma limpar compreensão das como fórmulas autoscale interagem com os dados de métricas ou "amostras."

**Amostras**

O serviço do lote periodicamente leva-o até *amostras* de métricas de tarefa e recurso e disponibiliza-los às suas fórmulas autoscale. Estes exemplos são registados a cada 30 segundos pelo serviço lote. No entanto, existe normalmente algumas latência que faz com que um atraso entre quando esses amostras foram registadas e quando são disponibilizados para (e pode ser lido por) autoscale nas suas fórmulas. Para além disso, devido a vários fatores como ou outros problemas de infraestrutura de rede, amostras poderão não ter sido gravadas para um determinado intervalo. Isto resulta em amostras "em falta".

**Percentagem de exemplo**

Quando `samplePercent` lhe é transmitido a `GetSample()` método ou o `GetSamplePercent()` método é chamado, "percentagem" refere-se uma comparação entre o número total *possíveis* de amostras que são registadas pelo serviço lote e o número de amostras realmente *disponíveis* para a fórmula autoscale.

Vamos olhar timespan 10 minutos como um exemplo. Uma vez que exemplos são registados a cada 30 segundos, dentro de timespan 10 minutos, o número máximo de total de amostras são registadas pelo lote seria 20 amostras (2 por minuto). No entanto, devido a latência inerente do dispositivo de elaboração de relatórios, ou algumas outro problema dentro de infraestrutura do Azure, poderão existir apenas 15 amostras que estão disponíveis para a sua fórmula autoscale leitura. Isto significa que, para esse período de 10 minutos, apenas a **percentagem de 75** do número total de amostras gravadas são realmente disponíveis para a sua fórmula.

**Intervalos GetSample() e exemplo**

Nas suas fórmulas autoscale irão ser aumentar e diminuir conjuntos de dados – nós de adicionando ou removendo nós. Uma vez que nós custo dinheiro, que pretende garantir que as suas fórmulas utilizam um método inteligente de análise que é baseada no dados suficientes. Por conseguinte, recomendamos que utilize uma análise de tipo mais populares, nas fórmulas. Este tipo de vai aumentar e diminuir os agrupamentos de dados com base num *intervalo* de amostras recolhidas.

Para fazê-lo, utilize `GetSample(interval look-back start, interval look-back end)` para devolver um **vetor** de amostras:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada por lote, irá devolver um intervalo de amostras como um vector de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Assim que recolhidas vector de amostras, em seguida, pode utilizar funções como `min()`, `max()`, e `avg()` para deriva significativos valores do intervalo recolhido.

Para segurança adicional, pode forçar uma avaliação de fórmulas a *Falha* se menor do que uma determinada percentagem de exemplo está disponível para um determinado período de tempo. Quando forçar uma avaliação de fórmulas para falhar, indique o lote a cessar ainda mais a avaliação da fórmula se não estiver disponível – da percentagem especificada de amostras e não altera o tamanho do conjunto vai ser efectuada. Para especificar uma percentagem necessária de amostras, para a avaliação ser concluída com êxito, especifique-lo como o parâmetro de terceiro para `GetSample()`. Aqui, é especificado um requisito de 75% das amostras:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Também é importante, devido ao anteriormente mencionado atraso na disponibilidade de exemplo, para especificar sempre um intervalo de tempo com uma hora de início de aspeto anterior que seja mais antiga do que um minuto. Isto acontece porque demora cerca de um minuto para as amostras a serem propagadas através do sistema, por isso, amostras no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` frequentemente não estará disponível. Novamente, pode utilizar o parâmetro de percentagem da `GetSample()` para forçar um requisito de percentagem de exemplo específico.

> [AZURE.IMPORTANT] Vamos **Recomendamos vivamente** que *apenas a *evitar confiar ** no `GetSample(1)` no seu autoscale fórmulas * *. Isto acontece porque `GetSample(1)` essencialmente diz ao serviço do lote "também me o último exemplo tiver, independentemente de como há muito tempo tem." Uma vez que é uma única amostra e pode ser uma amostra mais antiga, não pode ser representante da imagem maior de tarefa recente ou estado do recurso. Se utilizar `GetSample(1)`, certifique-se de que faz parte de uma instrução maior e não o ponto de apenas os dados que depende da sua fórmula.

## <a name="metrics"></a>Métricas

Pode utilizar métricas de **recurso** e **tarefa** quando estiver a definir uma fórmula. Ajustar o número de destino de nós dedicada no conjunto baseado nos dados de métricas que obtenha e avaliar. Consulte a secção de [variáveis](#variables) acima para obter mais informações sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p><b>Métricas de recurso</b> são baseados na utilização da CPU, largura de banda e memória de nós de cluster, bem como o número de nós.</p>
        <p> Estas variáveis definidos pelo serviço são úteis para fazer ajustes com base na contagem de nó:</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variáveis definidos pelo serviço são úteis para fazer ajustes com base na utilização de recursos nó:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tarefa</b></td>
    <td><p><b>Métricas de tarefas</b> são com base no estado de tarefas, tais como ativo, pendentes e concluído. As seguintes variáveis definidos pelo serviço são úteis para fazer ajustes de tamanho de agrupamento com base na tarefa métricas:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Escrever uma fórmula de autoscale

Criar uma fórmula de autoscale por formando declarações que utilizem os componentes acima, então combinar essas demonstrações numa fórmula completa. Nesta secção, vamos criar uma fórmula de autoscale de exemplo que pode executar algumas decisões de dimensionamento do mundo real.

Primeiro, vamos definir os requisitos para os nossos autoscale uma nova fórmula. A fórmula deve:

1. **Aumentar** o número de destino de cluster nós num conjunto de dados se a utilização da CPU é elevada.
2. **Diminuir** o número de destino de cluster nós num conjunto de dados quando a utilização da CPU for baixa.
3. Restringir sempre o número **máximo** de nós a 400.

Para *aumentar* o número de nós durante a utilização da CPU alta, podemos definir a declaração de que preenche uma variável definidos pelo utilizador (`$totalNodes`) com um valor que seja 110 por cento do número de destino atual de nós, mas apenas se a utilização da CPU média mínima durante os últimos 10 minutos foi acima 70 por cento. Caso contrário, utilizamos o valor de dedicada atual.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Para *diminuir* o número de nós durante a utilização da CPU baixa, a seguinte instrução no nossa fórmula define o mesmo `$totalNodes` variável para 90 por cento do número de destino actual de nós se a utilização da CPU média nos últimos 60 minutos em foi 20 por cento. Caso contrário, utilize o valor atual da `$totalNodes` que recomendamos preenchida na instrução acima.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Agora limite o número de destino de nós de cluster dedicada a um **máximo** de 400:

```
$TargetDedicated = min(400, $totalNodes)
```

Eis a fórmula completa:

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Criar um conjunto de dados com capacidade de autoscale

Para criar um novo conjunto de dados com autoscaling ativado, pode utilizar uma das seguintes técnicas:

**Lote .NET**

1. Crie o conjunto com [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
1. Defina a propriedade [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) `true`.
1. Defina a propriedade [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) com a sua fórmula autoscale.
1. (Opcional) Defina a propriedade [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (a predefinição é 15 minutos).
1. Consolide o conjunto com o [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) ou [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**Lote REST API**

* [Adicionar um agrupamento de conta](https://msdn.microsoft.com/library/azure/dn820174.aspx): Especifique a `enableAutoScale` e `autoScaleFormula` elementos no seu pedido de REST API para configurar a escala automática para um conjunto de quando o criar.

O fragmento de código seguinte cria um conjunto de dados com capacidade de autoscale utilizando o [Lote .NET] [ net_api] biblioteca. Fórmula de autoscale o agrupamento define o número de destino de nós a 5 no segundas e 1 em todos os outros dias da semana. O [intervalo de dimensionamento automática](#automatic-scaling-interval) está definido para 30 minutos. Neste e os outros c# fragmentos neste artigo, "myBatchClient" é uma instância inicializada correctamente de [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Para além de lote REST API e .NET SDK, pode utilizar qualquer um do [Lote SDK](batch-technical-overview.md#batch-development-apis), [os cmdlets do PowerShell lote](batch-powershell-cmdlets-get-started.md)e o [Clip de lote](batch-cli-get-started.md) para trabalhar com autoscaling.

> [AZURE.IMPORTANT] Quando cria um conjunto de dados com capacidade de autoscale, tem de **não** especificar a `targetDedicated` parâmetro. Além disso, se quiser manualmente redimensionar um conjunto de dados com capacidade de autoscale (por exemplo, com [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), em seguida, tem primeiro **desativar** automático de dimensionamento no agrupamento de, em seguida, redimensione-lo.

### <a name="automatic-scaling-interval"></a>Intervalo de dimensionamento automática

Por predefinição, o serviço do lote ajusta tamanho de um conjunto de acordo com a fórmula autoscale cada **15 minutos**. Este intervalo é configurável, no entanto, utilizando as seguintes propriedades de conjunto de dados:

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (REST API)

O intervalo mínimo é de cinco minutos e o valor máximo é 168 horas. Se não for um intervalo fora deste intervalo especificado, o serviço do lote irá devolver um erro pedido incorrecto (400).

> [AZURE.NOTE] Autoscaling não se destina atualmente para responder a alterações em menos de um minuto, mas preferir destina-se para ajustar o tamanho do seu conjunto de forma gradual como executar uma carga de trabalho.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Ativar autoscaling num conjunto existente

Se já criou um conjunto de dados com um determinado número de nós de cluster utilizando o parâmetro *targetDedicated* , ainda pode ativar a autoscaling no agrupamento. Cada lote SDK fornece uma operação de "ativar autoscale", por exemplo:

* [BatchClient.PoolOperations.EnableAutoScale] [ net_enableautoscale] (Batch .NET)
*  [Ativar a escala automática num agrupamento de] [ rest_enableautoscale] (REST API)

Quando ativa autoscaling num conjunto existente, o seguinte procedimento aplica-se:

* Se escala automática está **desativada** no agrupamento de quando emitir o pedido de "ativar autoscale", *tem* de especificar uma fórmula autoscale válida quando emitir o pedido. Pode *, opcionalmente,* Especifique um intervalo de avaliação autoscale. Se não especificar um intervalo, é utilizado o valor predefinido de 15 minutos.

* Se autoscale atualmente **com permissão para** o conjunto, pode especificar uma fórmula de autoscale, um intervalo de avaliação ou ambas. Não é possível omitir ambas as propriedades.

  * Se especificar um novo intervalo de avaliação autoscale, em seguida, na agenda de avaliação existente está parada e uma nova agenda é iniciada. Hora de início a nova agenda é o tempo no qual o pedido de "ativar autoscale" foi emitido.

  * Se omitir a fórmula autoscale ou o intervalo de avaliação, o serviço do lote continua a utilizar o valor atual dessa definição.

> [AZURE.NOTE] Se foi especificado um valor para o parâmetro *targetDedicated* quando o agrupamento de foi criado, é ignorada quando a fórmula de dimensionamento automática é avaliada.

Este fragmento de código c# utiliza o [Lote .NET] [ net_api] biblioteca para ativar autoscaling num conjunto existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Atualizar uma fórmula de autoscale

Utilizar o mesmo "ativar autoscale" pedido para *Atualizar* a fórmula num conjunto existente com capacidade de autoscale (por exemplo, com [EnableAutoScale] [ net_enableautoscale] no lote .NET). Não existe nenhuma operação "Atualizar autoscale" especial. Por exemplo, se autoscaling já está ativado no "myexistingpool" quando o código seguinte é executado, a fórmula autoscale passa com o conteúdo do `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de autoscale

Com a atualização de uma fórmula de autoscale, ao utilizar o mesmo [EnableAutoScale] [ net_enableautoscale] método para alterar o intervalo de avaliação autoscale de um conjunto existente com capacidade de autoscale. Por exemplo, para definir o intervalo de avaliação autoscale para 60 minutos de um conjunto de que já se encontra ativada autoscale:

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula autoscale

Pode avaliar uma fórmula antes de aplicá-lo a um conjunto de dados. Desta forma, pode executar um "teste executar" da fórmula para ver como as suas demonstrações avaliar antes de colocar a fórmula para produção.

Para avaliar uma fórmula de autoscale, tem primeiro o **Ativar autoscaling** o conjunto de com uma **fórmula válida**. Se pretende testar uma fórmula num agrupamento de que ainda não tenha autoscaling ativado, pode utilizar a fórmula de uma linha `$TargetDedicated = 0` quando ativar primeiro o autoscaling. Em seguida, utilize um dos seguintes para avaliar a fórmula que pretende testar:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    Estes métodos lote .NET requerem o ID de um conjunto existente e uma cadeia que contém a fórmula autoscale avaliar. Os resultados de avaliação estão contidos na instância [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) devolvida.

* [Avaliar uma fórmula de dimensionamento automática](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    No pedido REST API, especifique o ID do agrupamento no URI e a fórmula autoscale no *autoScaleFormula* elemento do corpo do pedido. A resposta da operação de contém as informações de erro que poderão estar relacionado com a fórmula.

Neste [Lote .NET] [ net_api] fragmento de código, podemos avaliar uma fórmula antes de aplicá-lo para a [CloudPool][net_cloudpool]. Se o conjunto de não tiver autoscaling ativado, podemos ativá-la pela primeira vez.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Avaliação bem sucedida da fórmula neste fragmento irá resultar no resultado semelhante ao seguinte:

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obter informações sobre autoscale execuções

Para garantir a que sua fórmula está a executar como esperado, recomendamos que os resultados do autoscaling "será executado" lote executa no seu conjunto verifica periodicamente. Para obter por isso, (ou atualizar) uma referência para o conjunto de dados e examinar as propriedades da sua última autoscale executar.

No lote .NET, a propriedade [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) tem várias propriedades fornecendo informações sobre as mais recentes escala automática executar executado no agrupamento pelo serviço lote.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

Na API do resto, o pedido de [obter informações sobre um conjunto de dados](https://msdn.microsoft.com/library/dn820165.aspx) devolve informações sobre o conjunto de, que inclui o mais recente automática dimensionamento executar informações no [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun).

Fragmento de código c# seguinte utiliza a biblioteca de lote .NET para imprimir informações sobre o último autoscaling executar no agrupamento "myPool":

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemplo de resultado do fragmento de anterior:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Exemplos de fórmulas autoscale

Vamos ver algumas fórmulas que mostram formas diferentes para ajustar a quantidade de recursos de cluster num conjunto de dados.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: Baseados no tempo ajuste

Talvez pretenda ajustar o tamanho do conjunto de dados com base no dia da semana e a hora do dia, para aumentar ou diminuir o número de nós no conjunto, consoante adequado.

Esta fórmula obtém pela primeira vez a hora atual. Se for um dia da semana (1-5) e dentro de horas de trabalho (8: 00 para 6 PM), o tamanho do conjunto de destino está definido para 20 nós. Caso contrário, está definido para 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: Baseado em tarefas ajuste

Neste exemplo, o tamanho do agrupamento é ajustado com base no número de tarefas na fila de espera. Note que os comentários e as quebras de linha são aceitáveis em cadeias de fórmulas.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: Gestão de contas para paralelo de duas tarefas

Este é outro exemplo ajusta o tamanho do conjunto de dados com base no número de tarefas. Esta fórmula também leva em consideração o [MaxTasksPerComputeNode] [ net_maxtasks] valor que definiu para o conjunto. Isto é particularmente útil em situações onde foi ativada [execução paralelo de duas tarefas](batch-parallel-node-tasks.md) no seu conjunto.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: Definir um tamanho de conjunto de dados inicial

Este exemplo mostra um fragmento de código c# com uma fórmula de autoscale que define o tamanho do conjunto de dados para um determinado número de nós por um período de tempo inicial. Em seguida, ajusta-se com base no número de execução e ativo o tamanho do conjunto de tarefas decorrido o período de tempo inicial.

A fórmula no fragmento de código seguinte:

- Define o tamanho do conjunto inicial para quatro nós.
- Não ajustar o tamanho do conjunto dentro os primeiros 10 minutos do ciclo de vida do conjunto.
- Após 10 minutos, obtém o valor máximo do número de tarefas em execução e ativas dentro os últimos 60 minutos.
  - Se ambos os valores forem 0 (indicando que não existem tarefas estavam em execução ou ativo nos últimos 60 minutos), o tamanho do agrupamento está definido como 0.
  - Se qualquer valor for maior que zero, sem alteração for feita.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Próximos passos

* [Maximizar o Azure lote calcular utilização de recursos com tarefas de nó concorrentes](batch-parallel-node-tasks.md) contém detalhes sobre como pode executar várias tarefas simultaneamente em nós cluster no seu conjunto. Para além de autoscaling, esta funcionalidade pode ajudar a reduzir a duração da tarefa para algumas das cargas de trabalho, poupando-lhe dinheiro.

* Para outra booster de eficiência, certifique-se de que as aplicações do lote consulta o serviço de lote da forma mais ideal. Na [consulta o serviço do Azure lote eficientemente](batch-efficient-list-queries.md), vai aprender a limitar a quantidade de dados que este se cruza o fio quando consulta o estado de potencialmente milhares de nós de cluster ou tarefas.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx
