<properties
    pageTitle="Configuração avançada para Azure Mobile Cativação Android SDK"
    description="Descreve as opções de configuração avançadas incluindo manifesto Android com o Azure Mobile Cativação Android SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configuração avançada para Azure Mobile Cativação Android SDK

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Este procedimento descreve como configurar várias opções de configuração para as aplicações do Azure Mobile Cativação Android.

## <a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Requisitos de permissões
Algumas opções requerem permissões específicas, as quais estão listadas aqui para referência e na linha na funcionalidade específica. Adicionar estas permissões a AndroidManifest.xml do seu projeto imediatamente antes ou depois do `<application>` etiqueta.

O código de permissão tem de ter o seguinte aspeto o seguinte, onde preencha a permissão adequada da tabela que se segue.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Permissão | Quando utilizado |
| ---------- | --------- |
| INTERNET | Obrigatório. Para elaboração de relatórios básicos |
| ACCESS_NETWORK_STATE | Obrigatório. Para elaboração de relatórios básicos |
| RECEIVE_BOOT_COMPLETED | Obrigatório. Para mostrar o Centro de notificações após o reinício do dispositivo |
| WAKE_LOCK | Recomendado. Permite a recolha de dados ao utilizar a conta ou quando o ecrã está desligado |
| VIBRAÇÃO | Opcional. Permite vibração quando são recebidas notificações |
| DOWNLOAD_WITHOUT_NOTIFICATION | Opcional. Permite a notificação de conceito Android |
| WRITE_EXTERNAL_STORAGE | Opcional. Permite a notificação de conceito Android |
| ACCESS_COARSE_LOCATION | Opcional. Ativa a localização em tempo real elaboração de relatórios |
| ACCESS_FINE_LOCATION | Opcional. Permite aos relatórios com base em GPS localização |

Começar com Android M, [algumas permissões são geridas ao tempo de execução](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Se já estiver a utilizar ``ACCESS_FINE_LOCATION``, em seguida, não precisa de também utilizar ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Opções de configuração de manifesto Android

### <a name="crash-report"></a>Relatório de falha de sistema

Para desativar os relatórios de falha de sistema, adicione este código entre o `<application>` e `</application>` etiquetas:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Limiar de rajada

Por predefinição, os relatórios de serviço de Cativação inicia em tempo real. Se os registos de relatório de aplicação variam frequentemente, é melhor para os registos de memória intermédia e para um relatório-las todas ao mesmo tempo quando uma base de tempo regular (denominados "rajada modo"). Para fazê-lo, adicione este código entre o `<application>` e `</application>` etiquetas:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

O modo de rajada ligeiramente aumenta a vida de baterias, mas tem um impacto no Monitor Cativação: todos os duração sessões e tarefas de implementação são arredondados para o limiar de rajada (portanto, sessões e tarefas mais pequena do que o limiar de rajada poderão não estar visível). O limiar de rajada deve ser não pode ultrapassar 30000 (30s).

### <a name="session-timeout"></a>Tempo limite de sessão

 É possível terminar uma atividade ao premir a tecla **Home** ou para **trás** , definindo o telefone idle ou ao ir para outra aplicação. Por predefinição, uma sessão é terminada dez segundos após o fim da sua última actividade. Isto evita uma divisão sessão sempre que o utilizador sai e devolve à aplicação rapidamente, pode acontecer quando o utilizador escolhe uma imagem, verifica uma notificação, etc. Pretende modificar este parâmetro. Para fazê-lo, adicione este código entre o `<application>` e `</application>` etiquetas:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Desactivar o relatório de registo

### <a name="using-a-method-call"></a>Utilizar uma chamada de método

Se pretender Cativação para parar o envio de registos, pode ligar:

    EngagementAgent.getInstance(context).setEnabled(false);

Esta chamada é persistente: utiliza um ficheiro de preferências partilhada.

Se Cativação está ativa ao ligar para esta função, poderá demorar um minuto para parar o serviço. No entanto,-não iniciar o serviço de todo da próxima vez que iniciar a aplicação.

Pode ativar o registo de elaboração de relatórios novamente ao contactar o suporte a mesma função com `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integração no seu próprio`PreferenceActivity`

Em vez de chamar esta função, também pode integrar o esta definição diretamente no seu existentes `PreferenceActivity`.

Pode configurar Cativação para utilizar o seu ficheiro de preferências (com o modo de pretendido) na `AndroidManifest.xml` ficheiro com `application meta-data`:

-   O `engagement:agent:settings:name` chave é utilizada para definir o nome do ficheiro partilhado preferências.
-   O `engagement:agent:settings:mode` chave é utilizada para definir o modo do ficheiro de preferências partilhada. Utilizar o mesmo modo como no seu `PreferenceActivity`. Tem de ser transmitido o modo como um número: Se estiver a utilizar uma combinação de sinalizadores constantes no seu código, selecione o valor total.

Utiliza sempre Cativação a `engagement:key` chave booleana dentro o ficheiro de preferências para gerir esta definição.

O exemplo seguinte de `AndroidManifest.xml` mostra os valores predefinidos:

    <application>
        [...]
        <meta-data
          android:name="engagement:agent:settings:name"
          android:value="engagement.agent" />
        <meta-data
          android:name="engagement:agent:settings:mode"
          android:value="0" />

Em seguida, pode adicionar um `CheckBoxPreference` no seu esquema preferência tal como o seguinte:

    <CheckBoxPreference
      android:key="engagement:enabled"
      android:defaultValue="true"
      android:title="Use Engagement"
      android:summaryOn="Engagement is enabled."
      android:summaryOff="Engagement is disabled." />
