<properties 
    pageTitle="Como utilizar a API Cativação no Windows Phone Silverlight" 
    description="Como utilizar a API Cativação no Windows Phone Silverlight"    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" /> 

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Como utilizar a API Cativação no Windows Phone Silverlight

Este documento é um suplemento para o documento [como integrar o Mobile Cativação na sua aplicação do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Fornece detalhes de profundidade sobre como utilizar a API de Cativação para comunicar as estatísticas de aplicação.

Se apenas quiser Cativação para comunicar a sua aplicação sessões, atividades, falhas e informações técnicas, em seguida, a forma mais simples é tornar todos os seu `PhoneApplicationPage` sub-classes herdam a `EngagementPage` escolares.

Se pretender fazer mais, por exemplo, se precisar de um relatório eventos específicos da aplicação, erros e tarefas, ou se tiver de atividades da sua aplicação um relatório de forma diferente do que aquele implementada a `EngagementPage` classes, em seguida, tem de utilizar a API Cativação.

API Cativação é fornecido pela `EngagementAgent` escolares. Pode aceder a essas métodos através do `EngagementAgent.Instance`.

Mesmo que não tenha sido inicializado o módulo agente, cada chamada à API é adiada e será executada novamente quando o agente está disponível.

##<a name="engagement-concepts"></a>Conceitos de Cativação

As seguintes partes Refina os conceitos de Cativação móvel para a plataforma Windows Phone.

### <a name="session-and-activity"></a>`Session`e`Activity`

Uma *atividade* está normalmente associado uma página da aplicação, ou seja, a *atividade* é iniciado quando a página é apresentada e deixa de quando a página estiver fechada: Este é o caso quando o SDK Cativação está integrado utilizando o `EngagementPage` escolares.

Mas *atividades* também pode ser controlado manualmente ao utilizar a API Cativação. Esta opção permite-para dividir uma determinada página em várias partes de sub para obter mais detalhes sobre a utilização desta página (por exemplo para conhecidos com que frequência e quanto tempo as caixas de diálogo são utilizadas dentro esta página).

##<a name="reporting-activities"></a>Atividades de elaboração de relatórios

### <a name="user-starts-a-new-activity"></a>Utilizador inicia uma nova actividade

#### <a name="reference"></a>Referência

            void StartActivity(string name, Dictionary<object, object> extras = null)

Precisa de telefonar `StartActivity()` cada vez que as alterações de atividade de utilizador. A primeira chamada para esta função inicia uma nova sessão do utilizador.

> [AZURE.IMPORTANT] O SDK chamar automaticamente o método de EndActivity quando a aplicação está fechada. Assim sendo, recomenda-se vivamente para chamar o método de StartActivity sempre que a atividade da alteração de utilizador e para nunca chamar o método de EndActivity, uma vez que chamar este método força a sessão actual para ser terminado.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Utilizador termina a sua atividade atual

#### <a name="reference"></a>Referência

            void EndActivity()

Precisa de telefonar `EndActivity()` pelo menos uma vez quando o utilizador concluir a sua última actividade. Isto informa o SDK Cativação que o utilizador está atualmente inactivo e que a sessão do utilizador precisa de ser uma vez fechado o limite de sessão expirará (se chamar `StartActivity()` antes que expire o limite de sessão, a sessão é simplesmente continuação).

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>Tarefas de elaboração de relatórios

### <a name="start-a-job"></a>Iniciar uma tarefa

#### <a name="reference"></a>Referência

            void StartJob(string name, Dictionary<object, object> extras = null)

Pode utilizar a tarefa para monitorizar certos tarefas ao longo de um período de tempo.

#### <a name="example"></a>Exemplo

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Terminar uma tarefa

#### <a name="reference"></a>Referência

            void EndJob(string name)

Assim que uma tarefa controlada por uma tarefa foi terminada, deverá contactar o método de EndJob para esta tarefa, fornecendo o nome da tarefa.

#### <a name="example"></a>Exemplo

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>Eventos de elaboração de relatórios

Há três tipos de eventos:

-   Eventos de autónomo
-   Eventos de sessão
-   Eventos de tarefa

### <a name="standalone-events"></a>Eventos de autónomo

#### <a name="reference"></a>Referência

            void SendEvent(string name, Dictionary<object, object> extras = null)

Eventos de autónomo podem ocorrer fora do contexto de uma sessão.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Eventos de sessão

#### <a name="reference"></a>Referência

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Eventos de sessão são normalmente utilizados para as ações realizadas por um utilizador durante a sua sessão de um relatório.

#### <a name="example"></a>Exemplo

**Sem dados:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Com dados:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Eventos de tarefa

#### <a name="reference"></a>Referência

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Eventos de tarefa são normalmente utilizados para as ações realizadas por um utilizador durante uma tarefa de um relatório.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>Relatório de erros

Há três tipos de erros:

-   Erros de autónomo
-   Erros de sessão
-   Erros de tarefa

### <a name="standalone-errors"></a>Erros de autónomo

#### <a name="reference"></a>Referência

            void SendError(string name, Dictionary<object, object> extras = null)

Contrariamente ao erros de sessão, podem ocorrer erros de autónomo fora do contexto de uma sessão.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Erros de sessão

#### <a name="reference"></a>Referência

            void SendSessionError(string name, Dictionary<object, object> extras = null)

Erros de sessão são normalmente utilizados para relatar erros que afetam o utilizador durante a sua sessão.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Erros de tarefa

#### <a name="reference"></a>Referência

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Erros podem estar relacionado com uma tarefa em execução em vez de a ser relacionado com a sessão do utilizador actual.

#### <a name="example"></a>Exemplo

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>Causa uma falha de elaboração de relatórios

O agente fornece dois métodos para lidar com causa uma falha.

### <a name="send-an-exception"></a>Enviar uma exceção

#### <a name="reference"></a>Referência

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Exemplo

Pode enviar uma exceção em qualquer altura ao contactar o suporte:

            EngagementAgent.Instance.SendCrash(aCatchedException);

Também pode utilizar um parâmetro opcional para terminar a sessão de Cativação ao mesmo tempo que enviar a falha de sistema. Para fazê-lo, contacte:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Se fazê-lo, a sessão e tarefas de implementação serão fechadas apenas depois de enviar a falha de sistema.

### <a name="send-an-unhandled-exception"></a>Enviar uma exceção não processada

#### <a name="reference"></a>Referência

            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Cativação também fornece um método para enviar exceções não processadas. Este é particularmente útil quando utilizada dentro de processador de eventos de UnhandledException silverlight.

Isto irá método **sempre** terminar a sessão de Cativação e tarefas de implementação após a ser denominado.

#### <a name="example"></a>Exemplo

Pode utilizá-lo para implementar o seu próprio processador UnhandledException (especialmente se tiver desativado a falha automática relatórios a funcionalidade de Cativação). Por exemplo, na `Application_UnhandledException` método a `App.xaml.cs` ficheiro:

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code
            
              EngagementAgent.Instance.SendCrash(e);
            }

##<a name="onactivated"></a>OnActivated

### <a name="reference"></a>Referência

            void OnActivated(ActivatedEventArgs e)

Quando o utilizador navega reencaminhar, afastando-o de uma aplicação, depois do evento Deactivated é elevado, o sistema operativo tentará colocar a aplicação para o estado inativos. Em seguida, a aplicação é desactivar. Este processo é terminada uma aplicação mas alguns dados sobre o estado da aplicação e as páginas individuais dentro da aplicação são preservados.

Tem de inserir `EngagementAgent.Instance.OnActivated(e)` na `Application_Activated` método do ficheiro App.xaml.cs para repor o agente de Cativação quando a aplicação foi desactivado.

### <a name="example"></a>Exemplo

            // Inside your App.xaml.cs file
            
            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

##<a name="device-id"></a>Id do dispositivo

            String GetDeviceId()

Pode obter o id do dispositivo Cativação ao contactar o suporte este método.

##<a name="extras-parameters"></a>Parâmetros de Extras

Dados arbitrários podem ser anexados um evento, um erro, uma actividade ou uma tarefa. Podem ser estruturados esses dados utilizando um dicionário. Chaves e valores podem ser de qualquer tipo.

Dados de Extras são serializados pelo se pretende inserir o seu próprio tipo no extras que tem de adicionar um contrato para este tipo de dados.

### <a name="example"></a>Exemplo

Vamos criar uma nova classe "Pessoa".

            using System.Runtime.Serialization;
            
            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Em seguida, vamos adicionar um `Person` instância para um ficheiro extra.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Se colocar outros tipos de objetos, certifique-se dos respetivos método ToString () é implementado para devolver uma cadeia de legível humana.

### <a name="limits"></a>Limites

#### <a name="keys"></a>Teclas

Cada chave no objeto tem de corresponder a seguinte expressão normal:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Significa que teclas tem de iniciar com, pelo menos, uma letra, seguida de letras, dígitos ou sublinhado (\_).

#### <a name="size"></a>Tamanho

Extras são limitadas ao carateres de **1024** por chamada.

##<a name="reporting-application-information"></a>Informações sobre a aplicação de elaboração de relatórios

### <a name="reference"></a>Referência

            void SendAppInfo(Dictionary<object, object> appInfos)

Pode comunicar função Rastreio informações (ou outras informações específicas aplicação) utilizando o SendAppInfo() manualmente.

Tenha em atenção que podem ser enviadas incrementada estas informações: o valor mais recente para uma determinada chave será mantido para um determinado dispositivo. Como extras do evento, utilizar um dicionário\<objecto, objeto\> para anexar informações.

### <a name="example"></a>Exemplo

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Teclas

Cada chave no objeto tem de corresponder a seguinte expressão normal:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Significa que teclas tem de iniciar com, pelo menos, uma letra, seguida de letras, dígitos ou sublinhado (\_).

#### <a name="size"></a>Tamanho

Informações da aplicação estão limitados a carateres de **1024** por chamada.

No exemplo anterior, JSON enviado para o servidor é 44 carateres de comprimento:

            {"subscription":"2013-12-07","premium":"true"}
 
##<a name="logging"></a>Funcionalidade de registo
###<a name="enable-logging"></a>Ativar o registo

O SDK pode ser configurado para produzir os registos de teste na consola do IDE.
Estes registos não são ativados por predefinição. Para personalizar isto, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um do valor disponível a partir de `EngagementTestLogLevel` enumeração, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
