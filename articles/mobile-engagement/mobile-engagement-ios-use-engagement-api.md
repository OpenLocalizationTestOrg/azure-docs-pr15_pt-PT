<properties
    pageTitle="Como utilizar a API Cativação no iOS"
    description="IOS mais recente SDK - como utilizar a API Cativação no iOS"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-use-the-engagement-api-on-ios"></a>Como utilizar a API Cativação no iOS

Este documento é um suplemento para o documento como integrar Cativação IOS: que proporciona profundidade obter detalhes sobre como utilizar a API de Cativação para comunicar as estatísticas de aplicação.

Tenha em atenção que se apenas quiser Cativação para comunicar a sua aplicação sessões, atividades, falhas e informações técnicas, em seguida, a forma mais simples é criar todos os seus personalizada `UIViewController` objetos herdam a correspondente `EngagementViewController` escolares.

Se pretender fazer mais, por exemplo, se precisar de um relatório eventos específicos da aplicação, erros e tarefas, ou se tiver de atividades da sua aplicação um relatório de forma diferente do que aquele implementada a `EngagementViewController` classes, em seguida, tem de utilizar a API Cativação.

API Cativação é fornecido pela `EngagementAgent` escolares. Uma instância desta classe pode ser obtida ao contactar o `[EngagementAgent shared]` método estático (tenha em atenção que a `EngagementAgent` objecto devolvido é uma singleton).

Antes das chamadas API, o `EngagementAgent` objeto tem de ser inicializado ao contactar o método`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##<a name="engagement-concepts"></a>Conceitos de Cativação

As seguintes partes Refina os comuns [Mobile Cativação conceitos](mobile-engagement-concepts.md) para a plataforma do iOS.

### <a name="session-and-activity"></a>`Session`e`Activity`

Uma *atividade* está normalmente associado um ecrã da aplicação, ou seja, a *atividade* é iniciado quando o ecrã é apresentado e deixa de quando o ecrã estiver fechado: Este é o caso quando o SDK Cativação está integrado utilizando o `EngagementViewController` aulas.

Mas *atividades* também pode ser controlado manualmente ao utilizar a API Cativação. Esta opção permite-para dividir um determinado ecrã em várias partes de sub para obter mais detalhes sobre a utilização deste ecrã (por exemplo para conhecidos com que frequência e quanto tempo as caixas de diálogo são utilizadas dentro este ecrã).

##<a name="reporting-activities"></a>Atividades de elaboração de relatórios

### <a name="user-starts-a-new-activity"></a>Utilizador inicia uma nova actividade

            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Precisa de telefonar `startActivity()` cada vez que as alterações de atividade de utilizador. A primeira chamada para esta função inicia uma nova sessão do utilizador.

### <a name="user-ends-his-current-activity"></a>Utilizador termina a sua atividade atual

            [[EngagementAgent shared] endActivity];

> [AZURE.WARNING] Deve **nunca** esta função chamar por si próprio, exceto se pretende dividir uma utilização da sua aplicação para várias sessões: uma chamada para esta função seria terminar a sessão actual imediatamente, por isso, chamadas subsequentes para `startActivity()` seria iniciar uma nova sessão. Esta função é automaticamente chamada pelo SDK quando a aplicação estiver fechada.

##<a name="reporting-events"></a>Eventos de elaboração de relatórios

### <a name="session-events"></a>Eventos de sessão

Eventos de sessão são normalmente utilizados para as ações realizadas por um utilizador durante a sua sessão de um relatório.

**Exemplo sem dados adicionais:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Exemplo com os dados adicionais:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Eventos de autónomo

Contrariamente ao eventos de sessão, eventos de autónomo podem ser utilizados fora do contexto de uma sessão.

**Exemplo:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##<a name="reporting-errors"></a>Relatório de erros

### <a name="session-errors"></a>Erros de sessão

Erros de sessão são normalmente utilizados para relatar erros que afetam o utilizador durante a sua sessão.

**Exemplo:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Erros de autónomo

Contrariamente ao erros de sessão, erros autónomo podem ser utilizados fora do contexto de uma sessão.

**Exemplo:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##<a name="reporting-jobs"></a>Tarefas de elaboração de relatórios

**Exemplo:**

Imaginemos que pretende reportar a duração do processo de início de sessão:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Relatório de erros durante a uma tarefa

Erros podem estar relacionado com uma tarefa em execução em vez de a ser relacionado com a sessão do utilizador actual.

**Exemplo:**

Imaginemos que pretende reportar um erro durante o processo de início de sessão:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Eventos durante uma tarefa

Eventos podem estar relacionado com uma tarefa em execução em vez de a ser relacionado com a sessão do utilizador actual.

**Exemplo:**

Suponha que temos uma rede social e utilizamos uma tarefa ao relatório o tempo total durante o qual o utilizador está ligado ao servidor. O utilizador possa receber mensagens dele amigos, este é um evento de tarefa.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

##<a name="extra-parameters"></a>Parâmetros adicionais

Dados arbitrários podem ser anexados eventos, erros, atividades e tarefas.

Estes dados podem ser estruturados, que utiliza a classe de NSDictionary do iOS.

Tenha em atenção que podem conter extras `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` ou outro `NSDictionary` instâncias.

> [AZURE.NOTE] O parâmetro extra é serializado em JSON. Se pretender passar objetos diferentes dos descritos acima, tem de implementar o método que se segue na sua aula:
>
             -(NSString*)JSONRepresentation;
>
> O método deve devolver uma representação JSON do objeto.

### <a name="example"></a>Exemplo

    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Limites

#### <a name="keys"></a>Teclas

Cada chave na `NSDictionary` tem de corresponder a seguinte expressão normal:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que teclas tem de iniciar com, pelo menos, uma letra, seguida de letras, dígitos ou sublinhado (\_).

#### <a name="size"></a>Tamanho

Extras são limitadas ao carateres de **1024** por chamada (uma vez codificado em JSON pelo agente de Cativação).

No exemplo anterior, JSON enviado para o servidor é 58 carateres de comprimento:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Informações sobre a aplicação de elaboração de relatórios

Pode comunicar manualmente controlar informações (ou outras informações específicas aplicação) utilizando o `sendAppInfo:` função.

Tenha em atenção que podem ser enviadas incrementada estas informações: o valor mais recente para uma determinada chave será mantido para um determinado dispositivo.

Como extras do evento, o `NSDictionary` classe é utilizada para abstract informações sobre a aplicação, tenha em atenção que matrizes ou sub-dicionários serão tratados como cadeias simples (utilizando serialização JSON).

**Exemplo:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Limites

#### <a name="keys"></a>Teclas

Cada chave na `NSDictionary` tem de corresponder a seguinte expressão normal:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que teclas tem de iniciar com, pelo menos, uma letra, seguida de letras, dígitos ou sublinhado (\_).

#### <a name="size"></a>Tamanho

Informações da aplicação estão limitados a carateres de **1024** por chamada (uma vez codificado em JSON pelo agente de Cativação).

No exemplo anterior, JSON enviado para o servidor é 44 carateres de comprimento:

    {"birthdate":"1983-12-07","gender":"female"}
