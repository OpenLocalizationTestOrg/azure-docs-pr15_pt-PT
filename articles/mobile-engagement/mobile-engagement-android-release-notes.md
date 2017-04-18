<properties
    pageTitle="Integração do Azure Cativação móvel Android SDK"
    description="Atualizações e procedimentos para Android SDK para Azure Mobile Cativação mais recentes"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo" />

# <a name="release-notes"></a>Notas de lançamento

## <a name="423-08102016"></a>4.2.3 (10/08/2016)

- Sem mais bloqueio de conta.
- Corrigir um bloqueio ao chamar getDeviceId antes de inicialização (erros introduzida numa 4.2.0).

## <a name="422-05172016"></a>4.2.2 (17/05/2016)

- Melhorias de estabilidade.

## <a name="421-05102016"></a>4.2.1 (10/05/2016)

- Segurança: desative web vista ficheiro local do access.
- Segurança: remover `EngagementPreferenceActivity` escolares que se expande não seguras e obsoleto `PreferenceActivity` escolares.
- Segurança: alcance atividades agora encontram-se documentados para utilizar `exported="false"`, este sinalizador pode também ser utilizado em versões anteriores do SDK.

## <a name="420-03112016"></a>4.2.0 (11/03/2016)

- O SDK agora está licenciado sob MIT.
- Permitir que especificar um identificador de dispositivo personalizado ao tempo de inicialização SDK.

## <a name="415-02012016"></a>4.1.5 (01/02/2016)

- Melhorias de estabilidade.

## <a name="414-01262016"></a>4.1.4 (26/01/2016)

- Melhorias de estabilidade.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)

- Melhorias de estabilidade.

## <a name="412-11252015"></a>4.1.2 (25/11/2015)

- Melhorias de estabilidade.

## <a name="411-11042015"></a>4.1.1 (04/11/2015)

- Melhorias de estabilidade.

## <a name="410-08252015"></a>4.1.0 (25/08/2015)

- Processe o novo modelo de permissões para Android M.
- Agora pode configurar funcionalidades de localização o tempo de execução em vez de utilizar `AndroidManifest.xml`.
- Corrigir um erro de permissão: Se utilizar `ACCESS_FINE_LOCATION`, em seguida, `ACCESS_COARSE_LOCATION` não é necessária deixem.
- Melhorias de estabilidade.

## <a name="400-07062015"></a>4.0.0 (06/07/2015)

-   Alterações de protocolo interno para tornar mais fiável push e de análise.
-   Push nativo (GCM/ADM) agora também é utilizado no notificações da aplicação para que tem de configurar as credenciais de push nativo para qualquer tipo de campanha push.
-   Corrigir notificação conceito: terem sido apresentado 10s apenas depois de a ser enviados.
-   Corrigir um erro na vista de web: clicar numa hiperligação também foi a executar o URL da ação predefinida.
-   Corrigir uma falha de sistema rara relacionadas com a gestão de armazenamento local.
-   Corrigir gestão da cadeia de configuração dinâmicos.
-   Atualize EULA.

## <a name="300-02172015"></a>3.0.0 (02/17/2015)

-   Lançamento inicial do Azure Cativação móvel
-   configuração de appId é substituída por uma configuração de cadeia de ligação.
-   Removido API para enviar e receber mensagens XMPP arbitrários de entidades XMPP arbitrários.
-   Removido API para enviar e receber mensagens entre dispositivos.
-   Melhoramentos de segurança.
-   Controlo da Google Play e SmartAd removidos.
