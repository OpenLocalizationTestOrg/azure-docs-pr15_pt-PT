<properties 
   pageTitle="Azure Cativação Mobile guia - SDK de resolução de problemas" 
   description="Resolver problemas de integração de SDK no Azure Mobile Cativação" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Guia de resolução de problemas para problemas de integração de SDK

Seguem-se possíveis problemas que poderá encontrar com como o Azure Mobile Cativação integra na sua aplicação.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problemas SDK descobertos por uma falha na outra área da sua aplicação

### <a name="issue"></a>Problema
- Falha de coleção de dados de IU (na análise, monitorização, segmentação ou Dashboards).
- Notificações push falhas (puxa não funciona na aplicação, fora da aplicação ou ambas).
- Avançadas falhas de funcionalidade (controlo, Geolocalização ou plataforma que não funciona puxa específica).
- API falhas (APIs falhas frequentemente em silêncio sem mensagens de erro).
- Falhas de serviço (nenhuma das Azure Mobile Cativação funciona para a sua aplicação).

### <a name="causes"></a>Faz com que

- A maior parte dos problemas que precisam de ser resolvidos com o SDK do Azure Mobile Cativação serão identificados por uma falha na sua aplicação (tal como uma falha de recolha de dados de IU, falha push, falha de funcionalidade avançada, falha API, falhas de aplicações ou indisponibilidade do serviço evidente).  
- Se uma determinada funcionalidade do Azure Mobile Cativação nunca trabalhou na sua aplicação antes, terá de concluir a integração. 
- Se uma determinada funcionalidade do Azure Mobile Cativação estava a trabalhar e parado, poderá ter de atualizar para a última versão com o SDK do Azure Mobile Cativação. Lembre-se de que existe uma versão diferente do Azure Mobile Cativação SDK para cada plataforma suportada pelo Azure Mobile Cativação (Android, iOS, Windows e Windows Phone).

#### <a name="sdk-integration"></a>Integração de SDK

- Azure Mobile Cativação não corretamente integrado no SDK (Analytics).
- Chegar não corretamente integrado no SDK (na aplicação e saída da aplicação emite).
- Certificado expirada ou incorreto ordem de produção vs. Dev Center (apenas para iOS).
- GCM ou ADM não corretamente integrado no SDK (Android apenas - serviço específico emite).
- Controlo não corretamente integrado no SDK (store de instalação de controlo).
- Preguiça ou à localização GPS não corretamente integrado no SDK (filtragem por geo localização).


**Consulte também:**

- [Documentação do SDK - guias de integração][Link 5] 
- [Resolução de problemas de guia - Push][Link 23]

#### <a name="sdk-upgrade"></a>Atualização SDK

- Tem de atualizar SDK para resolver problemas com as versões mais antigas do SDK (muitas vezes relacionadas com as versões mais recentes do dispositivo SO).
- Desinstalar todas as versões anteriores da sua aplicação a partir do seu dispositivo e reinstalar a versão mais recente da sua aplicação, registe novamente o seu ID de dispositivo a partir do Azure Mobile Cativação da IU para confirmar que o seu dispositivo está a utilizar a versão mais recente da sua aplicação.

**Consulte também:**

- [Documentação do SDK - notas de lançamento](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Documentação do SDK - guias de atualização](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>SDK outros

- Erros no manifesto da aplicação "AndroidManifest.xml" podem causar Azure Mobile Cativação não trabalhar (apenas para Android).
- É um problema comuns de integração de SDK e a utilização de API confundir a chave de SDK e a API.

**Consulte também:**

- [Conceitos - glossário][Link 6]

## <a name="advanced-coding-issues"></a>Advanced codificação de problemas

### <a name="issue"></a>Problema
-  Código específico da plataforma não diretamente relacionados com a Azure Mobile Cativação pode causar problemas no iOS, Android e Windows Phone.

### <a name="causes"></a>Faz com que

- Muitas funções codificação com Azure Mobile Cativação avançadas são causadas por código específico da plataforma indevidamente escritas não diretamente relacionados com a Azure Mobile Cativação. Terá de documentação específicos para a plataforma que são desenvolver para além de documentação do Azure Mobile Cativação (Android, iOS, Web, Windows e Windows Phone).
- Configurar não corretamente "categorias", impede a ligação a partir de uma notificação para outra localização dentro ou fora da aplicação (apenas para Android). 
- Não definir um "UIKit.framework" para "opcional" no seu código iOS, mostra um "Symbol não encontrado o erro" e/ou falha em dispositivos iOS mais antigos (apenas para iOS).
- Certificados expirados ou não corretamente utilizando a versão de orientação do diapositivo notas, causas Dev Center ou a ordem de produção push problemas (apenas para iOS).
- Existem algumas limitações inerentes para uma plataforma que Azure Mobile Cativação não é possível controlar (tal como, como funciona o centro do sistema para terminar a aplicação emite no Android e iOS).
- Azure Mobile Cativação publica uma lista completa dos pacotes internos utilizado pelo Azure Mobile Cativação para iOS e Android para referência. Tenha em atenção que algumas funcionalidades do Azure Mobile Cativação são específicas para a plataforma (Android, iOS, Web, Windows e Windows Phone).

### <a name="see-also"></a>Consulte também

 - [Resolução de problemas de guia - Push][Link 23] 
 - [Documentação do SDK - notas de lançamento][Link 5]
 - [Documentação do SDK - guias de atualização][Link 5]

## <a name="application-crashes"></a>Falhas de aplicações

### <a name="issue"></a>Problema
- A aplicação falha dispositivo os utilizadores finais.

### <a name="causes"></a>Faz com que

- Informações de falha de sistema podem ser visualizadas na *Análise de IU* ou a *Analytics API*
- Pode localizar o ID do dispositivo do seu dispositivo de teste e efetuar a ação mesmo que a sua aplicação falhar de um utilizador final para o ajudar a identificar a causa do seu falha de causaram.
- Problemas conhecidos com o SDK do Azure Mobile Cativação que fazem com que aplicações para uma falha de sistema, por vezes, são resolvidos ao atualizar para a versão mais recente do SDK. Certifique-se verificar as notas de lançamento sobre a sua plataforma quando investigar a causa uma falha.

### <a name="see-also"></a>Consulte também

- [Documentação do SDK - notas de lançamento][Link 5]
- [Documentação do SDK - guias de atualização][Link 5]

## <a name="app-store-upload-failures"></a>Falhas de carregamento do arquivo de aplicação

### <a name="issue"></a>Problema
- Erros relacionados com a versão mais recente da sua aplicação a carregar para a loja de aplicações do Windows, Google ou Apple.

### <a name="causes"></a>Faz com que

- Aplicação, por vezes, armazena aplicações bloco com determinadas funcionalidades ativadas (por exemplo, da Apple Store impede a utilização do IDFV nas aplicações na loja e o arquivo de GooglePlay impede a partilha de informações da aplicação entre as aplicações). 
- Certifique-se de que verifica as notas de lançamento sobre as suas plataforma e a versão atual do SDK se tiver dificuldades em carregar uma aplicação para o arquivo.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
