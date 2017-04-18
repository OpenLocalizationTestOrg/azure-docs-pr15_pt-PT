<properties
    pageTitle="Azure Mobile Cativação iOS notas de lançamento SDK | Microsoft Azure"
    description="Atualizações e procedimentos para iOS SDK para Azure Mobile Cativação mais recentes"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="piyushjo" />

#<a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Notas de lançamento do Azure Mobile Cativação iOS SDK

##<a name="400-09122016"></a>4.0.0 (09/12/2016)

-   Notificação de fixa não actioned em dispositivos iOS 10.
-   Preterir XCode 7.

##<a name="324-06302016"></a>3.2.4 (30/06/2016)

-   Agregação fixa entre registos técnicos e outros registos.

##<a name="323-06072016"></a>3.2.3 (06/07/2016)

-   Corrigido o erro onde comentários de entrega não são comunicado quando a aplicação estiver em segundo plano.
-   Otimizado o envio de registos técnicos.

##<a name="322-04072016"></a>3.2.2 (04/07/2016)

-   Erro corrigido no cancelamento de pedido de HTTP, por vezes, oportunidades potenciais falhar.

##<a name="321-12112015"></a>3.2.1 (12/11/2015)

-   Fixa o atraso quando uma nova instância de aplicação é acionada por uma notificação com ligações de profundidade

##<a name="320-10082015"></a>3.2.0 (10/08/2015)

-   Ativada Bitcode no SDK para torná-lo a trabalhar com **Xcode 7**.
-   Fixos erros relacionados com as notificações de na aplicação.
-   As notificações de na aplicação efetuadas mais fiável em caso de baterias baixa e outros destes cenários.
-   Remover registos de consola extra gerados pelo 3º biblioteca de terceiros.

##<a name="310-08262015"></a>3.1.0 (26/08/2015)

-   Corrigir erros de compatibilidade do iOS 9 com uma biblioteca de terceiros. Enquanto enviar consulta resultados, informações sobre a aplicação ou dados extra-estava a causar falhas.

##<a name="300-06192015"></a>3.0.0 (19/06/2015)

-   Mobile Cativação utiliza as notificações de emissão silenciosa.
-   Largadas suporte para iOS 4. x. Iniciar a partir desta versão o destino da implementação da sua aplicação tem de ser, pelo menos, iOS 6.

##<a name="220-05212015"></a>2.2.0 (21/05/2015)

-   O id do dispositivo móvel Cativação para dispositivos < iOS 6 baseia-se agora num GUID gerado no momento da instalação.

##<a name="210-04242015"></a>2.1.0 (04/24/2015)

-   Compatibilidade rápida adicionada.
-   Quando clicar numa notificação, a ação que URL é agora executada direita após a aplicação é aberta.
-   Ficheiro de cabeçalho em falta adicionados no pacote SDK.
-   Fixo um problema quando o autor de falha de sistema Mobile Cativação foi desativado.

##<a name="200-02172015"></a>2.0.0 (02/17/2015)

-   Lançamento inicial do Azure Cativação móvel
-   configuração de appId/sdkKey é substituída por uma configuração de cadeia de ligação.
-   Removido API para enviar e receber mensagens XMPP arbitrários de entidades XMPP arbitrários.
-   Removido API para enviar e receber mensagens entre dispositivos.
-   Melhoramentos de segurança.
-   Controlo de SmartAd removidos.
