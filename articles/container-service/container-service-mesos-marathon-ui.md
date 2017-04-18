<properties
   pageTitle="Gestão de contentor de serviço de contentor Azure através da IU da web | Microsoft Azure"
   description="Implemente contentores num serviço de cluster de serviço de contentor Azure utilizando web Marathon IU."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contentores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/19/2016"
   ms.author="timlt"/>

# <a name="container-management-through-the-web-ui"></a>Gestão de contentor através da IU da web

Cc/SO fornece um ambiente para implementar e dimensionamento das cargas de trabalho agrupadas, enquanto abstracting o hardware subjacente. Na parte superior de CC sistema operativo, existe um quadro que gere o agendamento e cluster cargas de trabalho em execução.

Quadros estão disponíveis para muitas das cargas de trabalho mais utilizadas, este documento irá descrevem como pode criar e dimensionar implementações do contentor com Marathon. Antes de trabalhar através destes exemplos, terá um cluster de CC/SO está configurado no Azure contentor de serviço. Também tem de ter conectividade remota a este cluster. Para mais informações sobre estes itens, consulte os artigos seguintes:

- [Implementar um cluster de serviço de contentor do Azure](container-service-deployment.md)
- [Ligar a um cluster de serviço de contentor do Azure](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Explore o Centro de dados/SO IU

Com um túnel Shell seguro (SSH) estabelecido, navegue até à http://localhost/. Isto carrega web CC/SO IU e mostra informações sobre o cluster, como recursos utilizados, agentes ativos e serviços em execução.

![CC/SO IU](media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Explorar o Marathon IU

Para ver a IU Marathon, navegue para http://localhost/Marathon. A partir deste ecrã, pode iniciar um novo contentor ou para outra aplicação no cluster de serviço de contentor Azure CC/SO. Também pode ver informações sobre como executar contentores e aplicações.  

![Marathon IU](media/dcos/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Implementar um contentor formatados como Docker

Para implementar um novo contentor utilizando Marathon, clique no botão **Criar aplicação** e introduza as seguintes informações no formulário:

Campo           | Valor
----------------|-----------
ID              | nginx
Imagem           | nginx
Rede         | Pontes estabelecidas
Porta do anfitrião       | 80
Protocolo        | TCP

![Nova aplicação IU – geral](media/dcos/dcos4.png)

![Nova aplicação IU – Docker contentor](media/dcos/dcos5.png)

![Nova aplicação IU – portas e deteção de serviço](media/dcos/dcos6.png)

Se pretender estática mapear a porta de contentor para uma porta no agente do, terá de utilizar o modo de JSON. Para fazê-lo, mude o Assistente da nova aplicação para o **Modo de JSON** utilizando o botão de alternar. Em seguida, introduza o seguinte no `portMappings` secção a definição de aplicação. Este exemplo liga porta 80 do contentor à porta 80 do agente CC/SO. Pode alternar este assistente fora do modo de JSON depois de efetuar esta alteração.

```none
"hostPort": 80,
```

![Nova aplicação IU – exemplo porta 80](media/dcos/dcos13.png)

Cluster CC/SO é implementado com conjunto de agentes públicos e privados. Para o cluster possam aceder a aplicações da Internet, tem de implementação de aplicações para um agente do público. Para fazê-lo, selecione o separador **opcional** do Assistente nova aplicação e introduza **slave_public** para as **Funções de recurso aceites**.

![Nova aplicação IU – agente público definição](media/dcos/dcos14.png)

Novamente na página principal Marathon, pode ver o estado da implementação para o contentor.

![Página principal do Marathon IU – estado de implementação do contentor](media/dcos/dcos7.png)

Ao mudar para a web CC/SO IU (http://localhost/), irá ver que uma tarefa (neste caso, um contentor formatados como Docker) está em execução no cluster CC/SO.

![Cc/SO IU – tarefa em execução no cluster da web](media/dcos/dcos8.png)

Também pode ver o nó do cluster que a tarefa está em execução no.

![Cc/SO IU da web – nó de cluster de tarefa](media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Dimensionar os contentores

Pode utilizar a IU Marathon para dimensionar a contagem de instância de um contentor. Para fazê-lo, navegue para a página de **Marathon** , selecione o contentor que pretende dimensionar e clique no botão de **escala** . Na caixa de diálogo **Escala de aplicação** , introduza o número de ocorrências de contentor que pretende e selecione **Escala de aplicação**.

![Marathon IU – caixa de diálogo Escala aplicação](media/dcos/dcos10.png)

Depois de concluir a operação de escala, irá ver várias instâncias da mesma tarefa distribuir por agentes de CC/SO.

![Dashboard de IU do Centro de dados/SO web-- propagação de tarefas ao longo de agentes](media/dcos/dcos11.png)

![Cc/SO IU – nós da web](media/dcos/dcos12.png)

## <a name="next-steps"></a>Próximos passos

- [Trabalhar com CC/SO e Marathon API](container-service-mesos-marathon-rest.md)

Vôo picado abrangente o serviço de contentor Azure com Mesos

> [AZURE. Azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos vídeo]]
