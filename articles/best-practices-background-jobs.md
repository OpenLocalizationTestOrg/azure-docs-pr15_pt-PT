<properties
   pageTitle="Orientação de tarefas do fundo | Microsoft Azure"
   description="Orientações sobre fundo tarefas que executar de forma independente da interface de utilizador."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="masashin"/>

# <a name="background-jobs-guidance"></a>Orientação de tarefas do fundo

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Descrição geral

Muitos tipos de aplicações necessitam de tarefas em segundo plano que são executadas independentemente dos existentes da interface de utilizador (IU). Alguns exemplos incluem trabalhos em lotes, tarefas de processamento intenso e processos de execução longa como fluxos de trabalho. Tarefas em segundo plano podem ser executadas sem necessidade de interação do utilizador – a aplicação pode iniciar a tarefa e, em seguida, continue para processar pedidos interativos dos utilizadores. Isto pode ajudar a minimizar a carga da aplicação IU, que pode melhorar a disponibilidade e reduzir os tempos de resposta interativos.

Por exemplo, se uma aplicação é necessário para gerar miniaturas de imagens que são carregadas pelos utilizadores,-pode fazê-lo como uma tarefa de fundo e guarde a miniatura ao armazenamento de quando estiver concluído – sem que o utilizador necessário especificá-las aguardar que o processo para ser concluída. Da mesma forma, um utilizador colocar uma ordem pode iniciar um fluxo de trabalho fundo processa a ordem, enquanto a IU permite ao utilizador continuar a navegar no web app. Quando a tarefa em segundo plano estiver concluída, pode atualizar os dados armazenados encomendas e enviar uma mensagem de e-mail para o utilizador a confirmar a ordem.

Quando considerar se implementar uma tarefa como uma tarefa em segundo plano, os critérios principais é se a tarefa pode ser executada sem interação de utilizador e sem a IU necessário especificá-las esperar para a tarefa foram concluídas. Tarefas que requerem o utilizador ou a IU de aguardar enquanto a serem concluídas poderão não ser apropriadas como tarefas em segundo plano.

## <a name="types-of-background-jobs"></a>Tipos de tarefas em segundo plano

Tarefas em segundo plano, normalmente, incluem um ou mais dos seguintes tipos de tarefas:

- Trabalhos de CPU-a com um grau elevado, tal como cálculos matemáticos ou análise estruturais modelo.
- Tarefas de posso/the-com um grau elevado, tal como executar uma série de transações do armazenamento ou ficheiros de indexação.
- Trabalhos em lotes, tais como as atualizações de dados efectuadas durante a noite ou processamento agendado.
- Execução longa fluxos de trabalho, tais como satisfação da encomenda, ou o aprovisionamento de serviços e sistemas de.
- Processamento de dados sensíveis onde a tarefa é editora para uma localização mais segura de processamento. Por exemplo, não poderá processar dados sensíveis a maiúsculas e dentro de uma aplicação web. Em vez disso, poderá utilizar um padrão, tais como [chamadas](http://msdn.microsoft.com/library/dn589793.aspx) para transferir os dados para um processo de isolada de fundo que tenha acesso ao armazenamento protegido.

## <a name="triggers"></a>Accionadores

Tarefas em segundo plano podem ser iniciadas de várias maneiras diferentes. Estes abrange uma das categorias seguintes:

- [**Condicionada por eventos accionadores**](#event-driven-triggers). A tarefa é iniciada em resposta a um evento, normalmente uma ação efetuada por um utilizador ou um passo num fluxo de trabalho.
- [**Accionadores orientadas na agenda**](#schedule-driven-triggers). A tarefa é chamada numa agenda com base num temporizador. Isto pode ser uma agenda periódica ou uma invocação pontual especificada para uma hora posterior.

### <a name="event-driven-triggers"></a>Accionadores condicionada por eventos

Condicionada por eventos invocação utiliza um accionador para iniciar a tarefa de fundo. Exemplos de utilização de accionadores condicionada por eventos incluem:

- IU ou outra tarefa coloca uma mensagem numa fila. A mensagem contém dados sobre uma ação que demorou local, como o utilizador colocar uma ordem. A tarefa de fundo recebe nesta fila e Deteta chegada de uma nova mensagem. Lê a mensagem e utiliza os dados como entrada para a tarefa de fundo.
- IU ou outra tarefa guarda ou atualiza um valor no armazenamento. A tarefa de fundo monitoriza o armazenamento e Deteta alterações. Lê os dados e utiliza-a como a entrada para a tarefa de fundo.
- IU ou outra tarefa torna um pedido para um ponto final, tal como um URI HTTPS ou uma API que está exposta como um serviço web. Transmite os dados que são necessário para concluir a tarefa de fundo como parte do pedido. O ponto final ou serviço web invoca a tarefa de fundo, que utiliza os dados como a respectiva entrada.

Típica tarefas que são adequadas para condicionada por eventos invocação exemplos de processamento de fluxos de trabalho, envie informações para serviços de remoto, enviar mensagens de correio eletrónico e novos utilizadores em aplicações multi-inquilino de aprovisionamento de imagens.

### <a name="schedule-driven-triggers"></a>Accionadores orientadas por agenda

Condicionada pelo agenda invocação utiliza um temporizador para iniciar a tarefa de fundo. Exemplos de utilização de orientadas por agenda accionadores incluem:

- Um temporizador que está a ser executado localmente dentro da aplicação ou como parte do sistema operativo da aplicação invoca uma tarefa de fundo regularmente.
- Um temporizador que esteja a executar uma aplicação diferente, ou num serviço de temporizador como o Scheduler do Azure, envia um pedido para um serviço web ou API regularmente. O API ou serviço web invoca a tarefa de fundo.
- Um processo separado ou uma aplicação é iniciada um temporizador que faz com que a tarefa de fundo seja possível invocar uma vez após um atraso de tempo especificado, ou num momento específico.

Típica tarefas que são adequadas para orientadas por agenda invocação exemplos de processamento batch rotinas (como atualizar listas de produtos relacionados com a para os utilizadores com base no seu comportamento recente), tarefas de manutenção de rotina processamento de dados (como atualizar índices ou gerar resultados acumulados), análise de dados para relatórios diários, limpeza de retenção de dados e controlos de consistência de dados.

Se utilizar uma tarefa orientadas por agenda que deve executar como uma única ocorrência, tenha em atenção o seguinte procedimento:

- Se for dimensionada a instância de cluster que está a executar o agendador (tal como uma máquina de virtual utilizar as tarefas agendadas do Windows), tem várias instâncias do scheduler em execução. Estes conseguiu iniciar várias instâncias da tarefa.
- Se executar tarefas durante mais de período de tempo entre eventos do programador, o scheduler poderá ser iniciado outra instância da tarefa enquanto anterior ainda estiver em execução.

## <a name="returning-results"></a>Devolve resultados

Tarefas em segundo plano executar em modo assíncrono num processo separado ou até mesmo numa localização em separado, a partir de IU ou o processo que invocar a tarefa de fundo. Idealmente, tarefas em segundo plano estão operações "fogo e esquecer" e o progresso de execução não tem nenhum impacto no IU ou o processo de chamada. Isto significa que o processo de chamada não aguardar pela conclusão das tarefas. Por conseguinte, não consegue automaticamente detetar quando termina a tarefa.

Se necessitar de uma tarefa de fundo para comunicar com a tarefa de chamada para indicar o progresso da ou de conclusão, tem de implementar um mecanismo para esta situação. Alguns exemplos são:

- Escreva um valor de indicador de estado ao armazenamento de que esteja acessível para a tarefa IU ou autor da chamada, que pode monitorizar ou verificar este valor quando for necessário. Outros dados que a tarefa de fundo tem de devolver o autor da chamada podem ser colocados o mesmo armazenamento.
- Estabelece uma fila de resposta que recebe de IU ou autor da chamada na. A tarefa de fundo pode enviar mensagens para a fila que indicar o estado e de conclusão. As mensagens podem ser colocados dados que a tarefa de fundo tem de devolver o autor da chamada. Se estiver a utilizar o Azure Service Bus, pode utilizar as propriedades **ReplyTo** e **CorrelationId** para implementar esta funcionalidade. Para mais informações, consulte o artigo [correlação no serviço Bus controlada mensagens](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Expor uma API ou ponto final da tarefa de fundo que a IU ou o autor da chamada pode aceder para obter informações de estado. Dados que a tarefa de fundo tem de devolver o autor da chamada podem ser incluídos na resposta.
- Tem a tarefa de fundo de chamada de retorno para a interface de utilizador ou autor através de uma API para indicar o estado em alturas predefinidas ou após a conclusão. Isto pode ser através de eventos elevados localmente ou através de um mecanismo publicar e subscrever. Dados que a tarefa de fundo tem de devolver o autor da chamada podem ser incluídos no carga de útil evento ou pedido.

## <a name="hosting-environment"></a>Ambiente de alojamento

Pode alojar tarefas em segundo plano através de um intervalo de serviços de plataforma Azure diferente:

- [**Azure Web Apps e WebJobs**](#azure-web-apps-and-webjobs). Pode utilizar WebJobs para executar tarefas personalizadas com base num intervalo de diferentes tipos de scripts ou programas executáveis dentro do contexto de uma aplicação web.
- [**Funções de web e trabalhador serviços em nuvem azure**](#azure-cloud-services-web-and-worker-roles). Pode escrever código dentro de uma função que executa como uma tarefa de fundo.
- [**Azure máquinas virtuais**](#azure-virtual-machines). Se tiver um serviço do Windows ou a utilizar o Programador de tarefas do Windows, é comuns para alojar o seu tarefas em segundo plano dentro de uma máquina virtual dedicada.
- [**Azure lote**](./batch/batch-technical-overview.md). É um serviço da plataforma que agenda com muitos cluster de trabalho para ser executado numa coleção de geridos das máquinas virtuais e pode automaticamente escala calcular recursos para satisfazer as necessidades das suas tarefas.

As secções seguintes descrevem cada uma das seguintes opções mais detalhadamente e incluem considerações para o ajudar a escolher a opção adequada.

## <a name="azure-web-apps-and-webjobs"></a>Azure Web Apps e WebJobs

Pode utilizar o Azure WebJobs para executar tarefas personalizadas como tarefas em segundo plano dentro de uma aplicação Web do Azure. Executar WebJobs dentro do contexto da sua aplicação web como um processo contínuo. Também executar WebJobs em resposta a um evento acionador a partir do Azure Scheduler ou factores externos, como as mudanças blobs de armazenamento e filas de mensagens. Tarefas podem ser iniciadas e paradas a pedido e encerrar correctamente. Se um WebJob continuamente a execução falhar, é automaticamente reiniciado. Ações de repetição e erro são configuráveis.

Quando configura um WebJob:

- Se pretender que a tarefa para responder a um acionador de condicionada por eventos, deverá configurá-lo como **executar de forma contínua**. O script ou o programa é armazenado na pasta denominada site/wwwroot/app_data/tarefas/contínuo.
- Se pretender que a tarefa para responder a um accionador orientadas por agenda, deverá configurá-lo como **executar uma agenda**. O script ou o programa é armazenado na pasta denominada site/wwwroot/app_data/tarefas/acionou.
- Se escolher a opção **executar a pedido** quando configura uma tarefa, será executado o mesmo código que a opção **executar com base numa agenda** , quando iniciou.

Azure WebJobs executar sandbox da aplicação web. Isto significa que pode aceder a variáveis de ambiente e partilhar informações, tais como cadeias de ligação, com a aplicação web. A tarefa tem acesso ao identificador exclusivo do computador que está a ser executada a tarefa. A cadeia de ligação com o nome **AzureWebJobsStorage** fornece acesso ao armazenamento Azure filas, blobs e tabelas para dados da aplicação e acesso ao serviço Bus para mensagens e comunicações. A cadeia de ligação com o nome **AzureWebJobsDashboard** oferece um acesso aos ficheiros de registo de acção de tarefa.

Azure WebJobs têm as seguintes características:

- **Segurança**: WebJobs estejam protegidos pelas credenciais de implementação da aplicação web.
- **Tipos de ficheiro suportados**: pode definir WebJobs utilizando scripts de comando (. cmd), ficheiros batch (. bat), scripts de PowerShell (. ps1), festa de scripts da shell (.sh), scripts PHP (.php), scripts Python (.py), código JavaScript (. js) e programas executáveis (.exe, .jar e mais).
- **Implementação**: pode implementar scripts e executáveis utilizando o portal Azure, utilizando o suplemento [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) para Visual Studio ou o [Visual Studio 2013 actualização de 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) (pode criar e implementar com esta opção), utilizando o [Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)ou ao copiá-los diretamente para as seguintes localizações:
  - Para acionou execução: site/wwwroot/app_data/tarefas/acionou / {nome da tarefa}
  - Para execução contínua: site/wwwroot/app_data/tarefas/contínua / {nome da tarefa}
- **Registo**: Console.Out é tratado (marcado) como informações. Console.Error é tratado como erro. Pode aceder a informações de monitorização e diagnósticos de utilizando o portal do Azure. Pode transferir ficheiros de registo diretamente a partir do site. Estes são guardados nas seguintes localizações:
  - Para acionou execução: Vfs/dados/tarefas/acionou/nometarefa
  - Para execução contínua: Vfs/dados/tarefas/contínua/nometarefa
- **Configuração**: pode configurar WebJobs utilizando o portal, de REST API e PowerShell. Pode utilizar um ficheiro de configuração denominado settings.job no mesmo diretório de raiz como o script de tarefa para fornecer informações de configuração de uma tarefa. Por exemplo:
  - {"stopping_wait_time": 60}
  - {"is_singleton": VERDADEIRO}

### <a name="considerations"></a>Considerações sobre

- Por predefinição, WebJobs escala com a aplicação web. No entanto, pode configurar a executar numa única instância através da propriedade **is_singleton** configuração **Verdadeiro**de tarefas. Única ocorrência WebJobs são úteis para tarefas que não pretende dimensionar ou executar como múltiplo simultâneo instâncias, tais como reindexação, análise de dados e tarefas semelhantes.
- Para minimizar o impacto das tarefas no desempenho da aplicação web, considere a criação de uma instância do Azure Web App vazia num novo plano de serviço de aplicação ao anfitrião WebJobs que possam estar em execução longa ou recurso intenso.

### <a name="more-information"></a>Obter mais informações

- [Azure WebJobs recomendado recursos](./app-service-web/websites-webjobs-resources.md) listas de muitos recursos úteis, transferências e exemplos para WebJobs.

## <a name="azure-cloud-services-web-and-worker-roles"></a>Funções Azure de web e trabalhador de serviços em nuvem

Pode executar tarefas em segundo plano dentro de uma função da web ou numa função de trabalho em separado. Quando estiver a decidir se deve utilizar uma função de trabalho, considere escalabilidade e requisitos de elasticidade, duração da tarefa, liberte cadence, segurança, tolerância a falhas, contenção, complexidade e a arquitetura lógica. Para mais informações, consulte o artigo [Calcular padrão do recurso consolidação](http://msdn.microsoft.com/library/dn589778.aspx).

Existem várias formas para implementar o tarefas em segundo plano dentro de uma função de serviços em nuvem:

- Criar uma aplicação a classe de **RoleEntryPoint** de na função e utilizar os seus métodos para executar tarefas em segundo plano. As tarefas são executadas no contexto de WaIISHost.exe. Estes podem utilizar o método de **ObterDefinição** a classe de **CloudConfigurationManager** de para carregar definições de configuração. Para mais informações, consulte o artigo [ciclo de vida (serviços em nuvem)](#lifecycle-cloud-services).
- Utilize as tarefas de arranque para executar tarefas em segundo plano quando a aplicação é iniciada. Para forçar as tarefas que deve continuar a ser executado em segundo plano, defina a propriedade **taskType** ao **fundo** (se fazer isto, o processo de arranque de aplicação irá parar e aguarde que a tarefa concluir a). Para mais informações, consulte o artigo [executar tarefas de arranque no Azure](./cloud-services/cloud-services-startup-tasks.md).
- Utilize o SDK WebJobs para implementar o tarefas em segundo plano como WebJobs que são iniciadas como uma tarefa de arranque. Para mais informações, consulte o artigo [Introdução ao Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Utilize uma tarefa de arranque para instalar um serviço Windows que executa um ou mais tarefas em segundo plano. Tem de definir a propriedade **taskType** ao **fundo** , para que o serviço é executado em segundo plano. Para mais informações, consulte o artigo [executar tarefas de arranque no Azure](./cloud-services/cloud-services-startup-tasks.md).

### <a name="running-background-tasks-in-the-web-role"></a>Executar tarefas em segundo plano na função da web

A vantagem principal básicos para realizar tarefas em segundo plano na função web é a gravação no alojamento custos porque não existe nenhuma requisito para implementar funções adicionais.

### <a name="running-background-tasks-in-a-worker-role"></a>Executar tarefas em segundo plano numa função de trabalho

Executar tarefas em segundo plano numa função de trabalho, tem várias vantagens:

- Permite-lhe gerir dimensionamento separadamente para cada tipo de função. Por exemplo, poderá ter mais ocorrências de uma função de web para suportar a carga atual, mas menos instâncias da função de trabalho que executa tarefas em segundo plano. Por dimensionamento instâncias de cluster fundo tarefa em separado das funções de IU, pode reduzir custos alojamento, enquanto mantém um desempenho aceitável.
- Offloads-lo a sobrecarga de processamento para tarefas em segundo plano da função de web. A função de web que fornece a IU pode permanecer responder e poderá significa que instâncias menos forem necessários para suportar um determinado volume de pedidos de utilizadores.
- Permite-lhe implementar separação de preocupações. Cada tipo de função pode implementar um conjunto específico de tarefas claramente definidos e relacionados. Isto torna estruturar e manter o código mais fácil porque existe menos interdependência de código e funcionalidades entre cada função.
- Pode ajudar a isolar sensíveis a maiúsculas e processos e dados. Por exemplo, funções de web que implementam a IU não necessita de ter acesso aos dados que são geridos e controlados por uma função de trabalho. Isto pode ser útil reforçar a segurança, especialmente quando utiliza um padrão, tais como o [Padrão de chamadas](http://msdn.microsoft.com/library/dn589793.aspx).  

### <a name="considerations"></a>Considerações sobre

Considere os seguintes pontos quando escolher como e onde para implementar tarefas em segundo plano quando utilizar as funções de web e trabalhador serviços em nuvem:

- Alojamento tarefas em segundo plano numa função de web existente, pode guardar o custo básicos para realizar uma função de trabalho em separado apenas para estas tarefas. No entanto, é provável que afetam o desempenho e disponibilidade da aplicação, se existir contenção de processamento e outros recursos. Utilizar uma função de trabalho em separado protege a função de web a partir do impacto das tarefas em segundo plano de execução longa ou consumir muitos recursos.
- Se aloja tarefas em segundo plano, utilizando a classe de **RoleEntryPoint** , pode mover isto facilmente a outra função. Por exemplo, se criar a classe numa função de web e mais tarde, decidir que que precisa para executar as tarefas numa função de trabalho, pode mover a implementação de classe **RoleEntryPoint** para a função de trabalho.
- Tarefas de arranque foram concebidas para executar um programa ou script. Implementar uma tarefa de fundo como um programa executável poderá ser mais difícil, especialmente se requer também de implementação do assemblagem dependentes. Poderá ser mais fácil implementar e utilizar um script para definir uma tarefa em segundo plano quando utilizar tarefas de arranque.
- Exceções que fazem com que uma tarefa de fundo a falha tem um impacto diferente, dependendo da forma que são alojados:
  - Se utilizar a abordagem de classe **RoleEntryPoint** , uma falha na tarefa causará a função por reiniciar para que a tarefa reinicia automaticamente. Isto pode afectar disponibilidade da aplicação. Para evitar esta situação, certifique-se de que incluem exceção robusta processamento dentro a classe de **RoleEntryPoint** e todas as tarefas de fundo. Utilize código para reiniciar a tarefas que falharem onde é adequado e gerar exceção para reiniciar a função apenas se não é possível recuperar correctamente da falha no seu código.
  - Se utilizar tarefas de arranque, for o responsável da gestão a execução de tarefa e de verificação se falhar.
- Gerir e monitorizar tarefas de arranque são mais difícil que utilizando a abordagem de classe **RoleEntryPoint** . No entanto, o SDK do Azure WebJobs inclui um dashboard para que seja mais fácil de gerir WebJobs quando iniciar a tarefas de arranque.

### <a name="more-information"></a>Obter mais informações

- [Calcular o padrão de consolidação de recursos](http://msdn.microsoft.com/library/dn589778.aspx)
- [Introdução ao Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)

## <a name="azure-virtual-machines"></a>Azure máquinas virtuais

Poderão ser implementadas tarefas em segundo plano de uma forma que impede que a ser implementada para aplicações Web do Azure ou os serviços em nuvem ou estas opções poderão não estar convenientes. São exemplos típicos serviços e os utilitários de terceiros e executáveis programas Windows. Outro exemplo poderá ser programas escritos para um ambiente de execução é diferente da que aloja a aplicação. Por exemplo, poderá um programa de Unix ou Linux que pretende executar a partir de uma aplicação do Windows ou .NET. Pode escolher a partir de um intervalo de sistemas operativos para uma máquina virtual Azure e executar o seu serviço ou executável em que a máquina virtual.

Para ajudar a escolher quando utilizar máquinas virtuais, consulte o artigo [Azure serviços de aplicação, serviços em nuvem e máquinas virtuais comparação](./app-service-web/choose-web-site-cloud-service-vm.md). Para obter informações sobre as opções para máquinas virtuais, consulte o artigo [tamanhos Máquina Virtual e serviço na nuvem para Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para mais informações sobre os sistemas operativos e pré-concebidas imagens que estão disponíveis para máquinas virtuais, consulte o artigo [Azure máquinas virtuais Marketplace](https://azure.microsoft.com/gallery/virtual-machines/).

Para iniciar a tarefa de fundo numa máquina virtual separada, tiver um intervalo das opções:

- Pode executar a tarefa a pedido diretamente a partir da sua aplicação ao enviar um pedido para um ponto final que expõe a tarefa. Isto transmite em quaisquer dados que a tarefa necessita. Este ponto final invoca a tarefa.
- Pode configurar a tarefa para executar uma agenda ao utilizar um programador ou temporizador está disponível no seu sistema operativo que selecionou. Por exemplo, no Windows pode utilizar o Programador de tarefas do Windows para executar scripts e as tarefas. Ou, se tiver instalado no computador virtual do SQL Server, pode utilizar o SQL Server Agent para executar scripts e as tarefas.
- Pode utilizar o Azure programador para iniciar a tarefa através da adição de uma mensagem para uma fila que recebe a tarefa ou ao enviar um pedido para uma API que expõe a tarefa.

Consulte a secção anterior [Accionadores](#triggers) para mais informações sobre como pode iniciar tarefas em segundo plano.  

### <a name="considerations"></a>Considerações sobre

Quando estiver a decidir se pretende implementar tarefas em segundo plano numa máquina virtual Azure, tenha em consideração os seguintes pontos:

- Alojamento tarefas em segundo plano numa máquina virtual Azure separada dá flexibilidade e permite precisos sobre iniciação, execução, agendamento e alocação de recursos. No entanto, irá aumentar runtime custo se uma máquina virtual tem de ser implementada apenas para executar tarefas em segundo plano.
- Não existe nenhuma instalações para monitorizar as tarefas no portal do Azure e sem capacidade de reiniciar automatizado para tarefas falhadas – apesar de poder monitorizar o estado da máquina virtual básico e geri-lo ao utilizar os [Cmdlets de gestão de serviço do Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). No entanto, não existem sem instalações para controlar processos e threads em nós de cluster. Normalmente, a utilizar uma máquina virtual requerem esforço adicional para implementar um mecanismo que recolhe dados de instrumentação na tarefa e do sistema operativo na máquina virtual. É uma solução que poderá ser apropriada utilizar o [Pacote de gestão do Centro de sistema para Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Recomendamos criar sondas monitorização que são expostas através de pontos finais de HTTP. O código para estas sondagens conseguiu executar verificações de integridade, recolher informações operacionais e estatísticas – ou agrupar informações de erro e repor para uma aplicação de gestão. Para mais informações, consulte o artigo [Padrão de monitorização do Estado de funcionamento do ponto final](http://msdn.microsoft.com/library/dn589789.aspx).

### <a name="more-information"></a>Obter mais informações

- [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) no Azure
- [Perguntas mais frequentes do Azure máquinas virtuais](virtual-machines/virtual-machines-linux-classic-faq.md)

## <a name="design-considerations"></a>Considerações de estrutura

Existem vários fatores fundamentais a ter em consideração quando estrutura tarefas em segundo plano. As secções seguintes abordam a partições, conflitos e coordená-lo.

## <a name="partitioning"></a>Criação de partições

Se decidir incluir tarefas em segundo plano dentro de uma instância de cluster existente (tal como uma aplicação web, função web, função trabalhador existente ou máquina virtual), tem de considerar como isto vai afectar os atributos de qualidade da instância de cluster e a tarefa de fundo própria. Seguintes fatores irão ajudá-lo a decidir se pretende colocar as tarefas com a instância de cluster existente ou separá-los numa instância de cluster separada:

- **Disponibilidade**: tarefas em segundo plano poderão não necessitam de ter o mesmo nível de disponibilidade como outras partes da aplicação em particular a IU e outras partes que estão, normalmente diretamente interação do utilizador. Tarefas em segundo plano poderão ser mais tolerância a falhas de latência, falhas de ligação repetida, e outros fatores que disponibilidade afeta porque as operações podem ser colocados na fila. No entanto, tem de ter uma capacidade suficiente para impedir que a cópia de segurança dos pedidos de que poderia bloquear filas e afeta a aplicação como um todo.
- **Escalabilidade**: tarefas em segundo plano são que possam ter um requisito escalabilidade diferentes que a IU e as partes interativas da aplicação. Dimensionamento IU poderá ser necessário para cumprir picos no pedido, enquanto tarefas em segundo plano pendentes poderão ser efetuadas durante menos horas ocupadas por um inferior número de ocorrências de cluster.
- **RDP**: falha de uma instância de cluster que aloja apenas tarefas em segundo plano poderá não afeta mortal a aplicação como um todo se os pedidos para estas tarefas podem ser na fila ou adiados até a tarefa está disponível novamente. Se a instância de cluster e/ou tarefas podem ser reiniciadas dentro de um intervalo adequado, utilizadores da aplicação não podem ser afetados.
- **Segurança**: tarefas em segundo plano poderão ter os requisitos de segurança diferente ou restrições que a IU ou outras peças da aplicação. Ao utilizar uma instância de cluster em separado, pode especificar um ambiente de segurança diferente para as tarefas. Também pode utilizar padrões como chamadas para identificar as instâncias de cluster de fundo da IU para poder maximizar a segurança e de separação.
- **Desempenho**: pode escolher o tipo de cluster instância para o fundo de tarefas para especificamente correspondência os requisitos de desempenho das tarefas. Isto poderá significa utilizando uma opção de cluster menos dispendiosa se as tarefas não necessitam das mesmas capacidades de processamento de como a IU ou uma instância maior se necessitam de capacidade adicional e recursos.
- **Capacidade de gestão**: tarefas em segundo plano, poderão ter um ritmo de desenvolvimento e implementação diferente a partir do código da aplicação principal ou a IU. Implementá-los para uma instância de cluster separado pode simplificar atualizações e controlo de versões.
- **Custo**: Adicionar calcular instâncias executar fundo tarefas aumenta os custos de alojamento. Cuidadosamente deverá tomar em consideração o compromisso entre capacidade adicional e estes custos extra.

Para mais informações, consulte o artigo [Padrão de eleições políticas de caráter de preenchimento](http://msdn.microsoft.com/library/dn568104.aspx) e [Competir padrão consumidores](http://msdn.microsoft.com/library/dn568101.aspx).

## <a name="conflicts"></a>Conflitos

Se tiver várias instâncias de uma tarefa de fundo, é possível que irá competir para acesso aos recursos e serviços, como as bases de dados e armazenamento. Este acesso em simultâneo pode resultar em contenção de recursos, poderá causar conflitos de disponibilidade dos serviços e na integridade dos dados no armazenamento. Pode resolver contenção de recursos utilizando uma abordagem de bloqueio pessimista. Isto impede que competir instâncias de uma tarefa a partir em simultâneo aceder a um serviço ou danificar dados.

Outra abordagem para resolver conflitos consiste em definir tarefas em segundo plano como uma singleton, para que existe alguma vez apenas uma instância em execução. No entanto, Isto elimina os benefícios de fiabilidade e desempenho que pode fornecer uma configuração da instância de múltiplo. Este é especialmente verdadeiro se o IU pode fornecer trabalho suficiente para manter mais do que uma tarefa em segundo plano ocupado.

É crucial garantir que a tarefa de fundo pode reiniciar automaticamente e de que tem uma capacidade suficiente para face picos no pedido. É possível alcançar isto atribuindo uma instância de cluster com recursos suficientes, ao implementar um mecanismo de colocação em fila que pode armazenar pedidos para mais tarde quando diminui a pedido, ou ao utilizar uma combinação das seguintes técnicas.

## <a name="coordination"></a>Coordená-lo

As tarefas de fundo poderão ser complexas e pode ser necessário várias tarefas individuais para executar para produzir um resultado ou cumprir todos os requisitos. É comuns nestes cenários para dividir a tarefa em menores discreto passos ou subtarefas que podem ser executadas pelos consumidores múltiplos. Nomes tarefas podem ser mais eficientes e mais flexíveis porque individuais passos poderão ser reutilizáveis em várias tarefas. Também é fácil adicionar, remover ou modificar a ordem dos passos.

Coordenar várias tarefas e os passos que pode ser difícil, mas existem três padrões comuns que pode utilizar para orientar a implementação de uma solução:

- **Permitem uma tarefa para vários passos reutilizáveis**. Uma aplicação poderá ser necessário para executar uma variedade de tarefas de complexidade variada nas informações que processa. Uma simples mas inflexível abordagem para implementar a esta aplicação poderá ser para efetuar este processamento como um módulo monolítico. No entanto, esta abordagem é provável reduzir as possibilidades de refactorização o código, Otimizando-o ou reutilizá-lo se partes do mesmo processamento são necessários noutro local dentro da aplicação. Para mais informações, consulte [em Pipes e filtros padrão](http://msdn.microsoft.com/library/dn568100.aspx).
- **Gerir a execução dos passos para uma tarefa**. Uma aplicação poderá executar tarefas que fazem parte de um número de passos (alguns dos quais poderão invocar serviços remota ou aceder a recursos remotos). Os passos individuais poderão ser independentes entre si, mas estes são orquestradas por lógica da aplicação que implementa a tarefa. Para mais informações, consulte o artigo [Scheduler agente autoridade padrão](http://msdn.microsoft.com/library/dn589780.aspx).
- **Gerir a recuperação para obter os passos de tarefa que falhar**. Uma aplicação poderá ter de anular o trabalho que é executado por uma série de passos (que em conjunto definir uma operação de são eventualmente consistente) se uma ou mais dos passos falharem. Para mais informações, consulte o artigo [Compensadores padrão da transação](http://msdn.microsoft.com/library/dn589804.aspx).

## <a name="lifecycle-cloud-services"></a>Ciclo de vida (serviços em nuvem)

 Se decidir implementar o tarefas em segundo plano para aplicações de serviços em nuvem que utilizam funções web e trabalhador utilizando a classe de **RoleEntryPoint** , é importante compreender o ciclo de vida desta classe para poder utilizá-la corretamente.

Funções Web e trabalhador aceda através de um conjunto de fases distintas como iniciar, executar e parar. A classe de **RoleEntryPoint** expõe uma série de eventos que indicam quando ocorrem estas fases. Utilizar estes para iniciar, executar e parar as tarefas de fundo personalizado. Ciclo de completo é:

- Azure carrega a assemblagem de função e procura-lo uma classe que deriva da **RoleEntryPoint**.
- Se encontrar esta classe, chama **RoleEntryPoint.OnStart()**. Substituir este método para iniciar a sua tarefas em segundo plano.
- Quando tiver concluído o método de **OnStart** , efetuar chamadas Azure **Application_Start()** no ficheiro Global, a aplicação se esta é (por exemplo, asax numa função de web com o ASP.NET).
- Azure chamadas **RoleEntryPoint.Run()** num novo tópico de primeiro plano que executa em paralelo com **OnStart()**. Substituir este método para iniciar a sua tarefas em segundo plano.
- Quando termina o método Run, o Azure chamadas pela primeira vez **Application_End()** no ficheiro Global a aplicação se esta é apresentar e, em seguida, chama **RoleEntryPoint.OnStop()**. Substituir o método de **OnStop** para parar o seu tarefas em segundo plano, limpar recursos, dispor de objetos e fechar ligações que possam ter utilizado as tarefas.
- O processo de anfitrião de função trabalhador Azure está parado. Neste momento, a função será reciclada e irá reiniciar.

Para obter mais detalhes e um exemplo de utilizando os métodos a classe de **RoleEntryPoint** de, consulte o artigo [Calcular padrão do recurso consolidação](http://msdn.microsoft.com/library/dn589778.aspx).

## <a name="considerations"></a>Considerações sobre

Quando estiver a planear como será executado tarefas em segundo plano numa função de web ou de trabalho, tenha em consideração os seguintes pontos:

- **Executar** método implementação predefinida na classe **RoleEntryPoint** contém uma chamada para **Thread.Sleep(Timeout.Infinite)** que mantém a função vivo indefinidamente. Se substituir o método **Executar** (que é normalmente necessário para executar tarefas em segundo plano), não terá de permitir o código sair do método, a menos que pretenda Reciclagem a instância de função.
- Uma implementação típica do método **Executar** inclui código para iniciar cada uma das tarefas em segundo plano e uma construção de ciclo que verifica periodicamente o estado de todas as tarefas de fundo. Pode reiniciar qualquer que falhar ou monitor de tokens de cancelamento que indicam que concluiu trabalhos de.
- Se uma tarefa de fundo inicia uma exceção não processada, essa tarefa deve ser reciclada permitindo outras tarefas de fundo na função para continuar a trabalhar rapidamente. No entanto, se a exceção é causada por danos de objetos fora da tarefa, tal como o armazenamento partilhado, a exceção deve ser processada pela sua aula **RoleEntryPoint** , todas as tarefas devem ser canceladas e o método **Executar** deve ser permitido para terminar. Azure irá, em seguida, reiniciar a função.
- Utilize o método de **OnStop** para colocar em pausa ou eliminar tarefas em segundo plano e limpar recursos. Isto poderá envolver parar a execução longa ou nomes de tarefas. É crucial a considerar como isto pode ser feito para evitar inconsistências de dados. Se uma instância de função parar por qualquer motivo que não seja um encerramento iniciado pelo utilizador, o código a ser executada no método de **OnStop** têm de ser concluído dentro de cinco minutos antes de ser terminada forçar. Certifique-se de que o seu código pode ser concluído nessa altura ou pode tolerar não a ser executado para a conclusão.  
- O Balanceador de carga Azure inicia que encaminha o tráfego para a instância de função quando o método **RoleEntryPoint.OnStart** devolve o valor **Verdadeiro**. Por conseguinte, considere a colocação de todo o código de inicialização o método de **OnStart** para que as instâncias de funções que não inicializado com êxito, não vai receber qualquer tráfego.
- Pode utilizar tarefas de arranque para além dos métodos de classe **RoleEntryPoint** . Deve utilizar tarefas de arranque a inicialização quaisquer definições que precisar de alterar no balanceador de carga Azure, uma vez que estas tarefas serão executadas antes da função recebe pedidos de qualquer. Para mais informações, consulte o artigo [executar tarefas de arranque no Azure](./cloud-services/cloud-services-startup-tasks.md).
- Se existir um erro numa tarefa de arranque, poderá forçar a função de para reiniciar continuamente. Isto pode impedir efetuar uma trocar de endereço IP (VIP) virtual novamente para uma versão anteriormente faseada porque o trocar requer acesso exclusivo à função. Isto não pode ser obtido enquanto a função está a ser reiniciado. Para resolver este problema:
    -  Adicione o seguinte código para o início dos métodos **OnStart** e **Executar** no seu perfil:

    ```C#
    var freeze = CloudConfigurationManager.GetSetting("Freeze");
    if (freeze != null)
    {
        if (Boolean.Parse(freeze))
        {
            Thread.Sleep(System.Threading.Timeout.Infinite);
        }
    }
    ```

   - Adicione a definição da definição **fixar** como um valor booleano à ServiceDefinition.csdef e ServiceConfiguration. *.cscfg ficheiros para a função e defini-lo* *Falso* *. Se a função de informações compõem um modo reiniciar repetida, pode alterar a definição para * *Verdadeiro** para fixar a execução de função e permitir-lhe ser trocadas com uma versão anterior.

## <a name="resiliency-considerations"></a>Considerações de RDP

Tarefas em segundo plano tem de ser e são para fornecer serviços fiáveis para a aplicação. Quando estiver a planear e estruturar tarefas em segundo plano, considere os seguintes pontos:

- Tarefas em segundo plano tem de conseguir processe correctamente reinicia função ou serviço sem danificar dados ou introduzirem inconsistência na aplicação. Tarefas de execução longa ou nomes, considere utilizar _Verificar apontar_ guardando o estado de tarefas de armazenamento persistente ou como mensagens de uma fila se isto é adequado. Por exemplo, pode manter informações de estado numa mensagem de uma fila e actualizar incrementada estas informações de estado com o progresso da tarefa para que a tarefa pode ser processada a partir do último conhecido bom ponto de verificação – em vez de reiniciar desde o início. Quando utilizar filas Azure Service Bus, pode utilizar sessões de mensagens para ativar o mesmo cenário. Sessões permitem-lhe guardar e obter o estado de processamento de aplicação, utilizando os métodos de [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) . Para mais informações acerca da criação de processos nomes fiáveis e fluxos de trabalho, consulte o artigo [Scheduler agente autoridade padrão](http://msdn.microsoft.com/library/dn589780.aspx).
- Quando utiliza as funções web ou de trabalho para alojar várias tarefas em segundo plano, estruture o seu substituição do método **Executar** para monitorizar para as tarefas de falhou ou que está bloqueadas e reinicie-los. Sempre que não é prático e estiver a utilizar uma função de trabalho, força a função de trabalho para reiniciar ao sair do método **Executar** .
- Quando utilizar filas para comunicar com tarefas em segundo plano, as filas podem funcionar como um intervalo de tempo para armazenar pedidos que são enviados para as tarefas enquanto a aplicação está em mais elevados que carga habitual. Esta opção permite-as tarefas que deve acompanhar com a IU menos períodos ocupado. Também significa que a função a Reciclagem não vai bloquear a IU. Para mais informações, consulte o artigo [padrão de nivelamento de carga baseados em filas](http://msdn.microsoft.com/library/dn589783.aspx). Se forem mais importantes do que outras pessoas algumas tarefas, considere a implementação o [Padrão de fila de prioridade](http://msdn.microsoft.com/library/dn589794.aspx) para se certificar de que estas tarefas executadas antes dos menos importantes.
- Tarefas em segundo plano que são iniciadas por mensagens ou processo devem ser concebidas para processar inconsistências, como mensagens que chegam fora de ordem, as mensagens que repetidamente originam um erro (muitas vezes, _mensagens corrompida foi_) e as mensagens que são entregues mais do que uma vez. Considere o seguinte:
  - Mensagens que têm de ser processadas por uma ordem específica, como aqueles que alterar os dados com base no valor de dados existente (por exemplo, adicionar um valor a um valor existente), poderá não chegam são enviadas para a ordenação original na qual foram enviadas. Em alternativa, poderá ser resolvidos por instâncias diferentes de uma tarefa de fundo numa ordem diferente devido a cargas variadas em cada instância. As mensagens que têm de ser processadas por uma ordem específica devem incluir um número de sequência, chave ou outro indicador tarefas em segundo plano, podem utilizar para se certificar de que são processadas na ordem correta. Se estiver a utilizar o Azure Service Bus, pode utilizar sessões de mensagens para garantir a ordem de entrega. No entanto, geralmente é mais eficiente, sempre que possível estruturar o processo para que a ordem de mensagem não é importante.
  - Normalmente, uma tarefa de fundo irá observar mensagens na fila oculta temporariamente a partir de outros consumidores de mensagem. Em seguida, elimina as mensagens depois de terem sido processadas com êxito. Se uma tarefa de fundo falhar quando o processamento de uma mensagem, essa mensagem reaparece na fila de depois de expira o tempo limite da caixa de pré-visualização. Serão processada por outra instância da tarefa, ou durante o ciclo de processamento desta instância seguinte. Se a mensagem de forma consistente provoca um erro do consumidor, irá bloquear a tarefa, fila de espera e eventualmente a própria aplicação quando fila de espera fica completa. Por conseguinte, é crucial para detetar e remover mensagens corrompida foi de fila de espera. Se estiver a utilizar o Azure Service Bus, as mensagens que originam um erro podem ser movidas a automaticamente ou manualmente para uma fila entregues associado.
  - Filas são garante na mecanismos de entrega _pelo menos, uma vez_ , mas poderá entregar a mesma mensagem mais de uma vez. Além disso, se uma tarefa de fundo falhar depois de processamento de uma mensagem, mas antes de o eliminar a partir de fila de espera, a mensagem irão tornar-se disponível para processamento novamente. Tarefas em segundo plano devem ser idempotent, o que significa que a mesma mensagem de processamento mais de uma vez não provocar um erro ou inconsistência nos dados da aplicação. Algumas operações são naturalmente idempotent, tal como a definição de um valor armazenado para um novo valor específico. No entanto, operações tal como adicionar um valor a um valor armazenado existente sem consultar a que o valor armazenado ainda é igual a quando a mensagem foi enviada originalmente causará inconsistências. Azure filas de serviço Bus podem ser configuradas para remover automaticamente as mensagens duplicadas.
  - Alguns sistemas de mensagens, tal como o armazenamento Azure filas e filas de Azure Service Bus, suportar uma propriedade de contagem de de-queue que indica o número de vezes que uma mensagem foi lida da fila. Isto pode ser útil em tratamento de mensagens repetidas e corrompida foi. Para mais informações, consulte o artigo [Introdução assíncrona mensagens](http://msdn.microsoft.com/library/dn589781.aspx) e [Padrões de Idempotency](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## <a name="scaling-and-performance-considerations"></a>Considerações de dimensionamento e desempenho

Tarefas em segundo plano devem oferecer desempenho suficiente para se certificar de que não bloquear a aplicação ou causar inconsistências devido a operação adiada quando o sistema estiver em caso de carga. Normalmente, desempenho melhorado por dimensionamento as instâncias de cluster que alojam as tarefas em segundo plano. Quando estiver a planear e estruturar tarefas em segundo plano, considere os seguintes pontos à volta de desempenho e escalabilidade:

- Suporta Azure autoscaling (escalar para fora e dimensionamento iniciá-la) com base no pedido atual e a carga – ou numa agenda predefinida, para aplicações Web, web de serviços em nuvem e funções de trabalho e máquinas virtuais alojado híbridas. Utilize esta funcionalidade para garantir que a aplicação como um todo tem suficientes capacidades de desempenho durante a minimizar runtime custos.
- Onde tarefas em segundo plano tem uma capacidade de desempenho diferentes a partir de outras partes de uma aplicação de serviços em nuvem (por exemplo, da IU ou componentes como camada de acesso a dados), que aloja as tarefas em segundo plano em conjunto em função trabalhador separada permite a IU e fundo funções de tarefa para independentemente Dimensionar para gerir a carregar. Se várias tarefas em segundo plano tem capacidades de desempenho significativamente diferentes uns dos outros, considere dimensionamento cada tipo de função de forma independente e dividi-los em funções de trabalho em separado. No entanto, tenha em atenção que esta poderá aumentar os custos de runtime comparado comparados combinar funções menos todas as tarefas.
- As funções de dimensionamento simplesmente poderá não ser suficiente para evitar a perda de desempenho em caso de carga. Também poderá ter de dimensionar filas de armazenamento e outros recursos para impedir que um único ponto de global cadeia a partir de um congestionamento a tornar-se de processamento. Além disso, considere outras limitações, tal como o débito máximo de armazenamento e outros serviços de que a aplicação e as tarefas em segundo plano dependem de.
- Tarefas em segundo plano devem ser concebidas para o dimensionamento. Por exemplo, deve ser conseguir detetar dinamicamente o número de armazenamento filas utilizado para poder escutar ou enviar mensagens para a fila adequada.
- Por predefinição, WebJobs escala com a respetiva instância de Azure Web Apps associada. No entanto, se pretender que um WebJob para executar como uma única ocorrência, pode criar um ficheiro de Settings.job que contém os dados JSON **{"is_singleton": VERDADEIRO}**. Este procedimento força Azure para executar apenas uma instância da WebJob, mesmo se existirem várias instâncias da aplicação web associado. Isto pode ser uma técnica útil para tarefas agendadas que deve executar como uma única ocorrência.

## <a name="related-patterns"></a>Padrões relacionados

- [Introdução mensagens assíncrona](http://msdn.microsoft.com/library/dn589781.aspx)
- [Orientações Autoscaling](http://msdn.microsoft.com/library/dn589774.aspx)
- [Compensadores da transação padrão](http://msdn.microsoft.com/library/dn589804.aspx)
- [Concorrentes consumidores padrão](http://msdn.microsoft.com/library/dn568101.aspx)
- [Calcular a partições orientações](http://msdn.microsoft.com/library/dn589773.aspx)
- [Calcular o padrão de consolidação de recursos](http://msdn.microsoft.com/library/dn589778.aspx)
- [Controlador de chamadas padrão](http://msdn.microsoft.com/library/dn589793.aspx)
- [Padrão de eleições políticas de caráter de preenchimento](http://msdn.microsoft.com/library/dn568104.aspx)
- [Tubos e filtros padrão](http://msdn.microsoft.com/library/dn568100.aspx)
- [Prioridade fila padrão](http://msdn.microsoft.com/library/dn589794.aspx)
- [Nivelamento padrão de carga baseada na fila de espera](http://msdn.microsoft.com/library/dn589783.aspx)
- [Scheduler agente autoridade padrão](http://msdn.microsoft.com/library/dn589780.aspx)

## <a name="more-information"></a>Obter mais informações

- [Dimensionamento aplicações Azure com funções de trabalho](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Executar tarefas em segundo plano](http://msdn.microsoft.com/library/ff803365.aspx)
- [Função Azure ciclo de vida de arranque](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (blogue)
- [Ciclo de vida do Azure Cloud Services função](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vídeo)
- [Começar a trabalhar com o SDK WebJobs Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- [Azure filas e serviço Bus filas - comparado com e contraste](./service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Como ativar diagnóstico num serviço de nuvem](./cloud-services/cloud-services-dotnet-diagnostics.md)
