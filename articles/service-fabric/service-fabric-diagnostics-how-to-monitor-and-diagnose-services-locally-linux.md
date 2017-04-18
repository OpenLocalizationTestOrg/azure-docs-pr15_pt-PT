<properties
   pageTitle="Localmente monitorizar e diagnosticar serviços escritos com Azure Service ferro | Microsoft Azure"
   description="Saiba como monitorizar e diagnosticar os seus serviços escritos utilizando ferro de serviço do Microsoft Azure num computador desenvolvimento local."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorizar e diagnosticar serviços numa configuração de desenvolvimento do computador local


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Permitem a monitorização, detetar, diagnóstico e resolução de problemas para serviços continuar com uma interrupção mínima para a experiência do utilizador. Monitorização e diagnóstico é crítico num ambiente de produção implementada real. Aprovar num modelo semelhante durante o desenvolvimento de serviços assegura que pipeline de diagnóstico funciona quando mover para um ambiente de produção. Serviço ferro torna mais fácil para os programadores de serviço implementar o diagnósticos de forma totalmente integrada podem trabalhar ao longo das configurações de desenvolvimento local máquina único e configurações de cluster de produção reais.


## <a name="debugging-service-fabric-java-applications"></a>Depuração de aplicações do serviço ferro Java

Para as aplicações de Java, [vários registo quadros](http://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Uma vez que `java.util.logging` é a opção predefinida com JRE, também é utilizado para os [exemplos de código no github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  O debate seguinte explica como configurar o `java.util.logging` framework. 
 
Utilizar java.util.logging redirecionar os registos de aplicação para memória, as sequências de saída, ficheiros de consola ou sockets. Para cada uma das seguintes opções, existem processadores predefinido fornecidos no quadro. Pode criar um `app.properties` ficheiro para configurar a alça de ficheiro para a sua aplicação redirecionar todos os registos para um ficheiro local. 

O fragmento de código seguinte contém um exemplo de configuração: 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

A pasta apontado pela `app.properties` ficheiro tem de existir. Depois do `app.properties` ficheiro é criado, também modificar o script do ponto de entrada, ao `entrypoint.sh` na `<applicationfolder>/<servicePkg>/Code/` pasta para definir a propriedade `java.util.logging.config.file` para `app.propertes` ficheiro. A entrada deve aspeto fragmento que se segue:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Esta configuração resultados nos registos que está a ser recolhidos de uma forma de rotação na `/tmp/servicefabric/logs/`. A **%u** **demasiado** permitem para criar mais ficheiros, com nomes de ficheiro mysfapp0.log, mysfapp1.log e assim sucessivamente. Por predefinição se sem processador explicitamente estiver configurado, o processador de consola está registado. Um pode visualizar os registos do sistema em /var/log/syslog.
 
Para mais informações, consulte os [exemplos de código no github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  



## <a name="next-steps"></a>Próximos passos
O mesmo código rastreio adicionado à sua aplicação também funciona com o diagnóstico da sua aplicação num Azure cluster. Consulte estes artigos que descrevem as diferentes opções para as ferramentas e descrevem como os pode configurar o.
* [Como recolher registos de diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-lad.md)
