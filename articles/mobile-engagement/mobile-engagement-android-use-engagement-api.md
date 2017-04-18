<properties
    pageTitle="Como utilizar a API Cativação no Android"
    description="SDK Android mais recente - como utilizar a API Cativação no Android"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="piyushjo;ricksal" />

#<a name="how-to-use-the-engagement-api-on-android"></a>Como utilizar a API Cativação no Android

Este documento é um suplemento para o documento [Opções avançadas relatórios para Android Mobile Cativação SDK](mobile-engagement-android-advanced-reporting.md). Fornece detalhes de profundidade sobre como utilizar a API de Cativação para comunicar as estatísticas de aplicação.

Tenha em atenção que se apenas quiser Cativação para comunicar a sua aplicação sessões, atividades, falhas e informações técnicas, em seguida, a forma mais simples é tornar todos os seus `Activity` sub-classes herdam a correspondente `EngagementActivity` classe.

Se pretender fazer mais, por exemplo, se precisar de um relatório eventos específicos da aplicação, erros e tarefas, ou se tiver de atividades da sua aplicação um relatório de forma diferente do que aquele implementada a `EngagementActivity` classes, em seguida, tem de utilizar a API Cativação.

API Cativação é fornecido pela `EngagementAgent` escolares. Uma instância desta classe pode ser obtida ao contactar o `EngagementAgent.getInstance(Context)` método estático (tenha em atenção que a `EngagementAgent` objecto devolvido é uma singleton).

##<a name="engagement-concepts"></a>Conceitos de Cativação

As seguintes partes Refina os comuns [Mobile Cativação conceitos](mobile-engagement-concepts.md), para a plataforma Android.

### <a name="session-and-activity"></a>`Session`e`Activity`

Se o utilizador permanece mais do que de aguardar alguns segundos idle entre duas *atividades*, em seguida, sua sequência de *atividades* é dividida em duas distintos *sessões*. Estes alguns segundos chamam o "limite de sessão".

Uma *atividade* está normalmente associado um ecrã da aplicação, ou seja, a *atividade* é iniciado quando o ecrã é apresentado e deixa de quando o ecrã estiver fechado: Este é o caso quando o SDK Cativação está integrado utilizando o `EngagementActivity` aulas.

Mas *atividades* também pode ser controlado manualmente ao utilizar a API Cativação. Esta opção permite-para dividir um determinado ecrã em várias partes de sub para obter mais detalhes sobre a utilização deste ecrã (por exemplo para conhecidos com que frequência e quanto tempo as caixas de diálogo são utilizadas dentro este ecrã).

##<a name="reporting-activities"></a>Atividades de elaboração de relatórios

> [AZURE.IMPORTANT] Não precisa de relatório Atividades como descrito nesta secção, se estiver a utilizar o `EngagementActivity` escolares e respectivas variantes tal como é explicado a como integrar Cativação no documento Android.

### <a name="user-starts-a-new-activity"></a>Utilizador inicia uma nova actividade

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Precisa de telefonar `startActivity()` cada vez que as alterações de atividade de utilizador. A primeira chamada para esta função inicia uma nova sessão do utilizador.

O melhor local para ligar a esta função é na cada atividade `onResume` chamada de retorno.

### <a name="user-ends-his-current-activity"></a>Utilizador termina a sua atividade atual

            EngagementAgent.getInstance(this).endActivity();

Precisa de telefonar `endActivity()` pelo menos uma vez quando o utilizador concluir a sua última actividade. Isto informa o SDK Cativação que o utilizador está atualmente inactivo e que a sessão do utilizador precisa de ser uma vez fechado o limite de sessão expirará (se chamar `startActivity()` antes que expire o limite de sessão, a sessão é simplesmente retomada).

O melhor local para ligar a esta função é na cada atividade `onPause` chamada de retorno.

##<a name="reporting-events"></a>Eventos de elaboração de relatórios

### <a name="session-events"></a>Eventos de sessão

Eventos de sessão são normalmente utilizados para as ações realizadas por um utilizador durante a sua sessão de um relatório.

**Exemplo sem dados adicionais:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Exemplo com os dados adicionais:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Eventos de autónomo

Contrariamente ao eventos de sessão, podem ocorrer autónomo eventos fora do contexto de uma sessão.

**Exemplo:**

Suponha que pretende comunicar eventos que ocorram quando um auscultador difusão é acionada:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##<a name="reporting-errors"></a>Relatório de erros

### <a name="session-errors"></a>Erros de sessão

Erros de sessão são normalmente utilizados para relatar erros que afetam o utilizador durante a sua sessão.

**Exemplo:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Erros de autónomo

Contrariamente ao erros de sessão, podem ocorrer erros de autónomo fora do contexto de uma sessão.

**Exemplo:**

O exemplo seguinte mostra como reportar um erro sempre que a memória torna-se baixa no telemóvel enquanto o processo de aplicação estiver em execução.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##<a name="reporting-jobs"></a>Tarefas de elaboração de relatórios

### <a name="example"></a>Exemplo

Imaginemos que pretende reportar a duração do processo de início de sessão:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Relatório de erros durante a uma tarefa

Erros podem estar relacionado com uma tarefa em execução em vez de a ser relacionado com a sessão do utilizador actual.

**Exemplo:**

Suponha que pretende reportar um erro durante a iniciar sessão processo:

[...] público void início de sessão do (contexto contexto,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Eventos de elaboração de relatórios durante uma tarefa

Eventos podem estar relacionado com uma tarefa em execução em vez de a ser relacionado com a sessão do utilizador actual.

**Exemplo:**

Suponha que temos uma rede social e utilizamos uma tarefa ao relatório o tempo total durante o qual o utilizador está ligado ao servidor. O utilizador pode manter-se em segundo plano mesmo quando ele está a utilizar outra aplicação ou quando o telefone está no modo de suspensão, pelo que não existe nenhuma sessão.

O utilizador possa receber mensagens dele amigos, este é um evento de tarefa.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##<a name="extra-parameters"></a>Parâmetros adicionais

Dados arbitrários podem ser anexados eventos, erros, atividades e tarefas.

Estes dados podem ser estruturados, que utiliza a classe de pacote do Android (na realidade, funciona como parâmetros extra no Android tipos). Tenha em atenção que um pacote pode conter matrizes ou outra instâncias do pacote.

> [AZURE.IMPORTANT] Se coloca na parcelable ou serializáveis parâmetros, certifique-se os respetivos `toString()` método é implementado para devolver uma cadeia legível. Classes serializáveis que contêm os campos que não sejam breves que não irão fazer Android falha quando irá efetuar a chamada`bundle.putSerializable("key",value);`

> [AZURE.WARNING] Não são suportadas matrizes dispersas em parâmetros adicionais, ou seja, não ser serializado como uma matriz. Deverá convertê-las em matrizes padrão antes de o utilizar no parâmetros adicionais.

### <a name="example"></a>Exemplo

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Teclas

Cada chave na `Bundle` tem de corresponder a seguinte expressão normal:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que teclas tem de iniciar com, pelo menos, uma letra, seguida de letras, dígitos ou sublinhado (\_).

#### <a name="size"></a>Tamanho

Extras são limitadas ao carateres de **1024** por chamada (uma vez codificado em JSON pelo serviço Cativação).

No exemplo anterior, JSON enviado para o servidor é 58 carateres de comprimento:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Informações sobre a aplicação de elaboração de relatórios

Pode comunicar manualmente controlar informações (ou outras informações específicas aplicação) utilizando o `sendAppInfo()` função.

Tenha em atenção que podem ser enviadas incrementada estas informações: o valor mais recente para uma determinada chave será mantido para um determinado dispositivo.

Como extras do evento, a classe de pacote é utilizada para abstract informações sobre a aplicação, tenha em atenção que matrizes ou sub-conjuntos de serão tratados como cadeias simples (utilizando serialização JSON).

### <a name="example"></a>Exemplo

Eis uma amostra de código para enviar género de utilizador e a data de nascimento:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Limites

#### <a name="keys"></a>Teclas

Cada chave na `Bundle` tem de corresponder a seguinte expressão normal:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que teclas tem de iniciar com, pelo menos, uma letra, seguida de letras, dígitos ou sublinhado (\_).

#### <a name="size"></a>Tamanho

Informações da aplicação estão limitados a carateres de **1024** por chamada (uma vez codificado em JSON pelo serviço Cativação).

No exemplo anterior, JSON enviado para o servidor é 44 carateres de comprimento:

            {"expiration":"2016-12-07","status":"premium"}
