<properties 
    pageTitle="Monitorizar dependências, exceções e tempos de execução no Java web apps" 
    description="Expandido a monitorização do seu Web site Java com informações de aplicação" 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="monitor-dependencies-exceptions-and-execution-times-in-java-web-apps"></a>Monitorizar dependências, exceções e tempos de execução no Java web apps

*Informações de aplicação está na pré-visualização.*

Se tiver [instalado a aplicação web do Java com informações de aplicação][java], pode utilizar o agente de Java para obter informações mais aprofundadas, sem alterações código:


* **Dependências:** Dados sobre as chamadas que faz com a aplicação a outros componentes, incluindo:
 * **Resto chamadas** efetuadas através de HttpClient, OkHttp e RestTemplate (Primavera).
 * **Redis** chamadas efetuadas através do cliente de estação de Jedis. Se a chamada demorar mais de 10s, o agente também obtém os argumentos de chamada.
 * **[Chamadas JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server, PostgreSQL, SQLite, Oracle DB ou Apache Derby DB. "executeBatch" chamadas são suportadas. Para MySQL e PostgreSQL, se a chamada demorar mais de 10s, o agente de relatórios o plano de consulta. 
* **Capturadas exceções:** Dados sobre exceções que são processados pelo seu código.
* **Tempo de execução do método:** Dados sobre o tempo que demora a executar métodos específicos.

Para utilizar o agente de Java, instalá-lo no servidor. As suas aplicações web tem de ser implementadas com a [Aplicação informações Java SDK][java].

## <a name="install-the-application-insights-agent-for-java"></a>Instalar o agente de informações de aplicação para Java

1. No computador a executar o seu servidor de Java, [Transfira o agente de](https://aka.ms/aijavasdk).
2. Editar o script de arranque do servidor de aplicações e adicionar JVM o seguinte:

    `javaagent:`*caminho completo para o ficheiro para caixa agente*

    Por exemplo, num Tomcat num computador Linux:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Reinicie o seu servidor de aplicação.

## <a name="configure-the-agent"></a>Configurar o agente

Criar um ficheiro com o nome `AI-Agent.xml` e colocá-lo na mesma pasta que o ficheiro do agente para caixa.

Defina o conteúdo do ficheiro xml. Edite o exemplo seguinte para incluir ou omitir as funcionalidades que pretende. 

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>
           
           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Tem de ativar exceção de relatórios e temporização do método para os métodos de individuais.

Por predefinição, `reportExecutionTime` for verdadeira e `reportCaughtExceptions` for falsa.

## <a name="view-the-data"></a>Ver os dados

O recurso de informações da aplicação, agregadas remotas dependência método execução horas e é apresentada [em mosaico desempenho][metrics]. 

Para procurar ocorrências individuais de relatórios de dependência, exceção e método, abra a [pesquisa][diagnostic]. 

[Diagnosticar problemas de dependência - Saiba mais](app-insights-dependencies.md#diagnosis).



## <a name="questions-problems"></a>Perguntas? Problemas?

* Sem dados? [Conjunto de firewall exceções](app-insights-ip-addresses.md)
* [Resolução de problemas Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 
