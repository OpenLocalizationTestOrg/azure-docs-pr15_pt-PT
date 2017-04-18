<properties
    pageTitle="Solução de contentores no registo de análise | Microsoft Azure"
    description="A solução contentores no registo de análise ajuda-o a ver e gerir os anfitriões de contentor Docker num único local."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>



# <a name="containers-preview-solution-log-analytics"></a>Solução contentores (pré-visualização) a análise de registo

Este artigo descreve como configurar e utilizar a solução contentores na análise de registo, que o ajuda a ver e gerir os anfitriões de contentor Docker num único local. Docker é um sistema de Virtualização do software utilizado para criar contentores automatizar a implementação de software para sua infraestrutura de TI.

Com a solução, pode ver os contentores estiver a executar no seu anfitriões de contentor e o que imagens estão a ser executada em contentores. Pode ver informações detalhadas de auditoria a mostrar os comandos utilizados com contentores. E, pode resolver problemas de contentores ao visualizar e procurar registos centralizados sem ter de visualizar remotamente anfitriões Docker. Pode encontrar contentores que podem ser ruídos e consome recursos excesso num sistema anfitrião. E, pode ver centralizada CPU, memória, armazenamento e informações de utilização e desempenho de rede de membros em contentores.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

Utilize as seguintes informações para instalar e configurar a solução.

Adicione a solução contentores à área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).

Existem duas formas de instalar e utilizar Docker com OMS:

- Em sistemas operativos Linux suportados, instalar e executar Docker e, em seguida, instalar e configurar OMS agente para Linux
- No CoreOS, instalar e executar Docker e, em seguida, configurar OMSAgent para executados no interior de um contentor

Reveja as versões de sistemas operativos suportadas Docker e Linux do seu anfitrião de contentor no [GitHub](https://github.com/Microsoft/OMS-docker).

>[AZURE.IMPORTANT] Docker tem de ser a execução **antes de** instalar o [Agente de OMS para Linux](log-analytics-linux-agents.md) no seu anfitriões de contentor. Se já tiver instalado o agente antes de instalar Docker, terá de reinstalar o agente OMS para Linux. Para mais informações sobre Docker, consulte o [Web site de Docker](https://www.docker.com).

Tem as seguintes definições que configurou no seu anfitriões contentor antes de poder monitorizar contentores.

## <a name="configure-settings-for-the-linux-container-host"></a>Configurar definições para o anfitrião o contentor Linux

Após instalar Docker, utilize as seguintes definições do seu anfitrião de contentor para configurar o agente para utilização com Docker. CoreOS não suportam este método de configuração.

### <a name="to-configure-settings-for-the-container-host---systemd-suse-opensuse-centos-7x-rhel-7x-and-ubuntu-15x-and-higher"></a>Para configurar definições para o anfitrião do contentor - systemd (SUSE openSUSE, CentOS 7. x, RHEL 7. x e Ubuntu 15.x e superior)

1. Edite docker.service para adicionar o seguinte procedimento:

    ```
    [Service]
    ...
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ...
    ```

2. Adicionar $DOCKER\_OPTE no &quot;daemon de = / usr/Reciclagem/docker ExecStart&quot; no seu ficheiro docker.service. Utilizar o exemplo seguinte.

    ```
    [Service]
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
    ```

3. Reinicie o serviço de Docker. Por exemplo:

    ```
    sudo systemctl restart docker.service
    ```

### <a name="to-configure-settings-for-the-container-host---upstart-ubuntu-14x"></a>Para configurar definições para o anfitrião do contentor - Upstart (Ubuntu 14.x)

1. Editar /etc/default/docker e adicione o seguinte:

    ```
    DOCKER_OPTS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Guardar o ficheiro e, em seguida, reinicie o Docker e OMS os serviços.

    ```
    sudo service docker restart
    ```

### <a name="to-configure-settings-for-the-container-host---amazon-linux"></a>Para configurar definições para o anfitrião do contentor - Amazon Linux

1. Editar /etc/sysconfig/docker e adicione o seguinte:

    ```
    OPTIONS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Guardar o ficheiro e, em seguida, reinicie o serviço de Docker.

    ```
    sudo service docker restart
    ```

## <a name="configure-settings-for-coreos-containers"></a>Configurar definições para CoreOS contentores

Após instalar Docker, utilize as seguintes definições para CoreOS para executar Docker e criar um contentor. Pode utilizar qualquer versão suportada do Linux — incluindo CoreOS, com este método de configuração. Terá o seu [ID de área de trabalho OMS e chave](log-analytics-linux-agents.md).

### <a name="to-use-oms-for-all-containers-with-coreos"></a>Para utilizar OMS para todos os contentores com CoreOS

- Inicie o contentor OMS ao qual pretende monitorizar. Modificar e utilizar o exemplo seguinte.

  ```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25224:25224/udp -p 127.0.0.1:25225:25225 --name="omsagent" --log-driver=none --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Mudar do utilizando um agente instalado para um num contentor

Se anteriormente utilizado o agente directamente instalado e pretende em vez disso, utilize um agente a ser executada num contentor, primeiro tem de remover OMSAgent. Consulte [os passos para instalar o agente OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md).

## <a name="containers-data-collection-details"></a>Detalhes de recolha de dados de contentores

A solução contentores recolhe vários desempenho métricas e registo de dados de anfitriões de contentor e contentores com os agentes OMS para Linux activou e de OMSAgent a ser executada em contentores.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos de membros em contentores.

| plataforma | Agente OMS para Linux | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Linux|![Sim](./media/log-analytics-containers/oms-bullet-green.png)|![N](./media/log-analytics-containers/oms-bullet-red.png)|![N](./media/log-analytics-containers/oms-bullet-red.png)|            ![N](./media/log-analytics-containers/oms-bullet-red.png)|![N](./media/log-analytics-containers/oms-bullet-red.png)| cada 3 minutos|


A tabela que se segue mostram exemplos dos tipos de dados recolhidos pela solução contentores:

| Tipo de dados | Campos |
| --- | --- |
| Desempenho para anfitriões e contentores | Computador, nome de objecto, CounterName e #40; tempo de processador %, disco lê MB, escritas no disco MB, a utilização de memória MB, rede receber Bytes, rede enviar Bytes, processador sec de utilização, n. º 41 & rede; contravalor, TimeGenerated, CaminhoContador, SourceSystem |
| Inventário de contentor | TimeGenerated, o computador, o nome do contentor, ContainerHostname, imagem, ImageTag, ContinerState, ExitCode, EnvironmentVar, comando, CreatedTime, StartedTime, FinishedTime, SourceSystem, IDContentor, ImageID |
| Inventário de imagem do contentor | TimeGenerated, computador, imagem, ImageTag, ImageSize, VirtualSize, em execução, colocados em pausa, parado, falhou, SourceSystem, ImageID, TotalContainer |
| Registo de contentor | TimeGenerated, o computador, o ID de imagem, o nome do contentor, LogEntrySource, LogEntry, SourceSystem, IDContentor |
| Registo de serviço do contentor | TimeGenerated, computador, TimeOfCommand, imagem, comando, SourceSystem, IDContentor |

## <a name="monitor-containers"></a>Monitor contentores

Depois de ter a solução ativada no portal do OMS, verá o mosaico de **contentores** a mostrar as informações de resumo sobre os anfitriões de contentor e os contentores a ser executada em anfitriões.

![Mosaico de membros em contentores](./media/log-analytics-containers/containers-title.png)

O mosaico mostra uma descrição geral de quantas contentores tiver no ambiente, e se estiver a falhou, em execução ou parados.

### <a name="using-the-containers-dashboard"></a>Com o dashboard contentores

Clique no mosaico de **contentores** . A partir desse local verá vistas organizadas por:

- Eventos de contentor
- Erros
- Estado de membros em contentores
- Inventário de imagem do contentor
- Desempenho de memória e CPU

Cada painel no dashboard de é uma representação visual de uma pesquisa que é executada no dados recolhidos.

![Dashboard de membros em contentores](./media/log-analytics-containers/containers-dash01.png)

![Dashboard de membros em contentores](./media/log-analytics-containers/containers-dash02.png)

No pá **Contentor Estado** , clique para a área superior, conforme apresentado abaixo.

![Estado de membros em contentores](./media/log-analytics-containers/containers-status.png)

É aberta a pesquisa de registo e apresenta informações sobre os anfitriões e contentores a ser executada em-los.

![Registo de pesquisa de membros em contentores](./media/log-analytics-containers/containers-log-search.png)

A partir daqui, pode editar a consulta de pesquisa para modificá-lo para encontrar as informações específicas que lhe interessa. Para mais informações sobre o registo de pesquisas, consulte [pesquisas de registo no registo de análise](log-analytics-log-searches.md).

Por exemplo, pode modificar a consulta de pesquisa para que mostra todos os contentores parados em vez dos contentores em execução alterando **a executar** para **parado** na consulta de pesquisa.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Resolver ao encontrar um contentor falhado

OMS marca um contentor como **falhou** se tem saiu com um código de saída não zero. Pode ver uma descrição geral dos erros e falhas no ambiente em pá os **Contentores falhou** .

### <a name="to-find-failed-containers"></a>Para localizar falhou contentores

1. Clique em pá o **Contentor eventos** .  
  ![eventos de membros em contentores](./media/log-analytics-containers/containers-events.png)
2. É aberta a pesquisa de registo e apresenta o estado de contentores, semelhantes ao seguinte.  
  ![Estado de membros em contentores](./media/log-analytics-containers/containers-container-state.png)
3. Em seguida, clique no valor falhado para ver informações adicionais, tal como o tamanho da imagem e o número de imagens parados e falhadas. Expanda **Mostrar mais** para ver o ID da imagem.  
  ![falhadas contentores](./media/log-analytics-containers/containers-state-failed.png)
4. Em seguida, localize o contentor que está a ser executada esta imagem. Escreva o seguinte para a consulta de pesquisa.
  `Type=ContainerInventory <ImageID>`Isto apresenta os registos. Pode deslocar-se para ver o contentor falhado.  
  ![falhadas contentores](./media/log-analytics-containers/containers-failed04.png)


## <a name="search-logs-for-container-data"></a>Registos de pesquisa para os dados de contentor

Quando estiver a resolver um erro específico, pode ajudar para ver onde está a ocorrer no seu ambiente. Os seguintes tipos de registo irão ajudá-lo a criar consultas para devolver as informações que pretende.

- **ContainerInventory** – utilizar este tipo de quando pretende obter informações sobre a localização do contentor, quais são os seus nomes e imagens do que estiver a executar.
- **ContainerImageInventory** – utilizar este tipo de que está a tentar encontrar informações organizadas por imagem e para ver informações sobre a imagem como imagem IDs ou tamanhos.
- **ContainerLog** – Utilize este tipo quando pretender localizar entradas e informações de registo de erros específicos.
- **ContainerServiceLog** – utilizar este tipo de que está a tentar encontrar informações de pista de auditoria para daemon Docker, tal como iniciar, parar, eliminar ou solicitação comandos.

### <a name="to-search-logs-for-container-data"></a>Para procurar registos para os dados de contentor

- Escolha uma imagem que sabe falhou recentemente e encontrar os registos de erro para a mesma. Comece por localizar um nome de contentor que esteja a executar essa imagem com uma pesquisa **ContainerInventory** . Por exemplo, procurar`Type=ContainerInventory ubuntu Failed`  
    ![Procurar Ubuntu contentores](./media/log-analytics-containers/search-ubuntu.png)

  Tome nota do nome do contentor junto ao **nome**e pesquise por esses registos. Neste exemplo, é `Type=ContainerLog adoring_meitner`.

**Ver informações do desempenho**

Quando estiver a começando construir consultas, pode ajudar para ver o que é possível pela primeira vez. Por exemplo, para ver todos os dados de desempenho, experimente uma consulta abrangente ao escrever a seguinte consulta de pesquisa.

```
Type=Perf
```

![desempenho contentores](./media/log-analytics-containers/containers-perf01.png)



Pode ver isto num formulário mais gráfica quando clica na palavra **métricas** nos resultados de.

![desempenho contentores](./media/log-analytics-containers/containers-perf02.png)



Pode reduzir os dados de desempenho que está a ver a um contentor específico ao escrever o nome do mesmo à direita da sua consulta.

```
Type=Perf <containerName>
```

Que mostra a lista de métricas de desempenho que são recolhidas para um contentor individual.

![desempenho contentores](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Consultas de pesquisa de registo de exemplo

Muitas vezes é útil para construir consultas começar com um exemplo ou dois e, em seguida, modificá-los para ajustar o seu ambiente. Como ponto de partida, pode experimentar a **Consultas dos** pá para ajudar a criar consultas mais avançadas.

![Consultas de membros em contentores](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Guardar registo consultas de pesquisa

Guardar consultas é uma funcionalidade padrão no registo de análise. Ao guardá-los, terá de aqueles que ter encontrado útil útil para utilização futura.

Depois de criar uma consulta que achar útil, guarde-o ao clicar em **Favoritos** na parte superior da página da pesquisa de registo. Em seguida, pode facilmente acedê-la mais tarde a partir da página de **Dashboard meus** .

## <a name="next-steps"></a>Próximos passos

- [Registos de pesquisa](log-analytics-log-searches.md) para ver registos de dados de contentor detalhadas.
