<properties
 pageTitle="Compreender e resolver erros de WebHCat no HDInsight"
 description="Saiba como para informações sobre erros comuns devolvido pela WebHCat no HDInsight e como para os resolver."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/27/2016"
 ms.author="larryfr"/>

#<a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>Compreender e resolver erros recebidos do WebHCat (Templeton), no HDInsight

Quando utilizar WebHCat (anteriormente conhecido como Templeton,) para trabalhar com HDInsight, poderá receber erros. Este artigo fornece orientações sobre erros comuns – porque ocorrem e o que pode fazer para resolvê-los.

##<a name="what-is-webhcat"></a>O que é WebHCat?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) é uma REST API para [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), uma tabela e armazenamento camada de gestão para Hadoop. WebHCat está ativada por predefinição nos HDInsight clusters e é utilizada por várias ferramentas para submeter tarefas, obter o estado da tarefa, etc., sem iniciar sessão no cluster.

##<a name="modifying-configuration"></a>Modificar a configuração

> [AZURE.IMPORTANT] Várias dos erros listados neste documento ocorrerem porque a um máximo de configurado tenha sido ultrapassado. Quando o passo de resolução menções que pode alterar um valor, tem de utilizar um dos seguintes para efetuar a alteração:

* Para **Windows** clusters: utilizar uma ação de script para configurar o valor de durante a criação de cluster. Para mais informações, consulte o artigo [desenvolver acções de script](hdinsight-hadoop-script-actions.md).

* Para **Linux** clusters: utilizar Ambari (web ou REST API) para modificar o valor. Para mais informações, consulte o artigo [Gerir HDInsight utilizar Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>Configuração predefinida

Seguem-se os valores de configuração de predefinidos que podem afetar o desempenho WebHCat ou causar erros se estes valores são excedidos:

| Definição | O que faz | Valor predefinido |
| ------- | ------------ | ------------- |
| [yarn.Scheduler.CAPACITY.Maximum aplicações][maximum-applications] | O número máximo de tarefas que pode ser ativos em simultâneo (pendente ou em execução) | 10.000 |
| [templeton.exec.Max-procs][max-procs] | O número máximo de pedidos de que pode ser fornecido em simultâneo | 20 |
| [mapreduce.jobhistory.Max-idade-ms][max-age-ms] | O número de dias que será retido histórico da tarefa | 7 dias |

##<a name="too-many-requests"></a>Demasiados pedidos

**Código de estado HTTP**: 429

| Causa | Resolução |
| ----- | ---------- |
| Se tiver excedido os máximos pedidos em simultâneo fornecidos pelo WebHCat por minuto (predefinição 20) | Reduza a carga de trabalho para se certificar de que não apresente mais do que o número máximo de pedidos em simultâneo ou aumente o limite de pedidos em simultâneo modificando `templeton.exec.max-procs`. Para mais informações, consulte [configuração de modificar](#modifying-configuration) |

##<a name="server-unavailable"></a>Servidor não está disponível

**Código de estado HTTP**: 503

| Causa | Resolução |
| ---------------- | ------------------- |
| Normalmente, isto ocorre durante activação pós-falha entre o HeadNode principal e secundário para o cluster | Aguarde dois minutos, em seguida, repita a operação |

##<a name="bad-request-content-could-not-find-job"></a>Pedido inválido conteúdo: não foi possível encontrar o projecto

**Código de estado HTTP**: 400

| Causa | Resolução |
| ---------------- | ------------------- |
| Detalhes sobre o trabalho ter sido limpo pelo histórico de tarefa Limpeza | O período de retenção predefinido para histórico da tarefa é 7 dias. Isto pode ser alterado modificando `mapreduce.jobhistory.max-age-ms`. Para mais informações, consulte [configuração de modificar](#modifying-configuration) |
| Tarefa foi terminada devido a um activação pós-falha | Volte a tentar submissão de tarefa de dois minutos |
| Foi utilizado um id da tarefa inválido | Verificar se o id da tarefa está correto |

##<a name="bad-gateway"></a>Gateway inválido

**Código de estado HTTP**: 502

| Causa | Resolução |
| ---------------- | ------------------- |
| Recolha de lixo interno está a ocorrer no âmbito do processo de WebHCat | Aguardar de recolha de lixo concluir ou reinicie o serviço de WebHCat |
| Limite de tempo a aguardar uma resposta de serviço ResourceManager. Isto pode ocorrer quando o número de aplicações activas acede ao máximo configurado (predefinição 10.000) | Aguardar atualmente em execução tarefas para concluir ou aumentar o limite de tarefa simultâneo modificando `yarn.scheduler.capacity.maximum-applications`. Para mais informações, consulte [configuração de modificar](#modifying-configuration)  |
| Quando tentar obter todas as tarefas através da chamada [obter /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) enquanto `Fields` está definido para`*` | Fazer obter *todos os* detalhes de tarefa, em vez disso utilize `jobid` para obter detalhes para trabalhos de maiores apenas a determinados id da tarefa. Em alternativa, não utilize`Fields` |
| O serviço de WebHCat é premida durante HeadNode activação pós-falha | Aguardar de dois minutos e tente novamente a operação |
| Existem mais de 500 tarefas pendentes submetidas através de WebHCat | Aguarde até que trabalhos atualmente pendentes terminem antes de a submeter mais tarefas |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 
