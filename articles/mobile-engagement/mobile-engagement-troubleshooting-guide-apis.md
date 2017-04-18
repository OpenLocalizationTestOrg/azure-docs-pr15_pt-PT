<properties 
   pageTitle="Azure Cativação Mobile guia - APIs de resolução de problemas" 
   description="Guias para o Azure Cativação móvel - APIs de resolução de problemas" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="10/04/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-api-issues"></a>Guia de resolução de problemas para problemas de API

Seguem-se possíveis problemas que poderá encontrar com o modo como os administradores interagem com Azure Mobile Cativação através de APIs.

## <a name="syntax-issues"></a>Problemas de sintaxe

### <a name="issue"></a>Problema
- Erros de sintaxe utilizar a API (ou comportamentos inesperados).

### <a name="causes"></a>Faz com que

- Problemas de sintaxe:
    - Certifique-se verificar a sintaxe da API do específica que estiver a utilizar para confirmar que a opção está disponível.
    - É um problema de comuns com a utilização de API confundir alcançar API e API emissão (a maioria das tarefas que deve ser realizada com a API atinja em vez da API emissão). 
    - Outro problema comuns com integração SDK e a utilização de API é confunda as teclas de SDK e a API.
    - Precisam de scripts que se ligam às APIs enviar dados pelo menos a cada 10 minutos ou a ligação será limite de tempo (especialmente comuns em scripts Monitor API listening para os dados). Para impedir que os tempos limite, tem o script envie um ping XMPP cada 10 minutos para manter a sessão vivo com o servidor.

### <a name="see-also"></a>Consulte também
 
- [Documentação da API][Link 4]
- [Informações de protocolo XMPP]( http://xmpp.org/extensions/xep-0199.html)
 
## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Não é possível utilizar a API para executar a ação mesmo disponível na IU Azure Mobile Cativação

### <a name="issue"></a>Problema
- Uma ação que funciona a partir do Azure Mobile Cativação IU não funciona a partir da Cativação API relacionados do Azure Mobile.

### <a name="causes"></a>Faz com que

- Confirmar a que pode executar a ação mesmo a partir do Azure Mobile Cativação IU mostra que esta funcionalidade do Azure Mobile Cativação corretamente tiver integrado com o SDK.

### <a name="see-also"></a>Consulte também
 
- [Documentação da IU][Link 1]
 
## <a name="error-messages"></a>Mensagens de erro

### <a name="issue"></a>Problema
- Códigos de erro de utilizar a API apresentada o tempo de execução ou nos registos.

### <a name="causes"></a>Faz com que

- Aqui está uma lista composta de comuns números de códigos de estado de API para uma referência e prévia de resolução de problemas:

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Consulte também

- [Documentação da API - erros detalhadas sobre cada API específico][Link 4]
 
## <a name="silent-failures"></a>Falhas silenciosos

### <a name="issue"></a>Problema
- Acção de API falhar sem mensagem de erro apresentado o tempo de execução ou nos registos.

### <a name="causes"></a>Faz com que

- Muitos itens serão desativados na IU Azure Mobile Cativação se eles não são integrados corretamente, mas irão falhar silenciosamente a partir da API, por isso, lembre-se testar a mesma funcionalidade da IU para ver se funciona.
- Azure Mobile Cativação e muitas das funcionalidades avançadas do Azure Mobile Cativação estiver a tentar utilizar, tem de ser individualmente integradas na sua aplicação com o SDK como quatro passos distintos antes de poder utilizá-los.

### <a name="see-also"></a>Consulte também

- [Resolução de problemas de guia - SDK][Link 25]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
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
 
