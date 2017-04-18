<properties 
   pageTitle="Interface de utilizador do Azure Cativação móvel - alcance campanha" 
   description="Laern como criar e gerir notificações push campanhas utilizando Azure Mobile Cativação" 
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


# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Como criar e gerir campanhas de notificações push
Pode utilizar a secção alcance da IU para criar uma nova campanha Push com uma fórmula complexa, fornecendo todas as informações que necessita para enviar uma notificação de emissão. As opções de uma campanha de Push variar ligeiramente consoante os tipos de quatro campanha: anúncios, inquéritos, dados emite e mosaicos (apenas para o Windows Phone).

### <a name="option-applies-to"></a>Opção aplica-se ao:
- Idiomas: Todos os (anúncios, inquéritos, dados puxa, mosaicos)
- Campanha: Todos os (anúncios, inquéritos, dados puxa, mosaicos)
- Notificação: Anúncios, inquéritos
- Content: Exclusiva para cada tipo de campanha
- Audiência: Todos os (anúncios, inquéritos, dados puxa, mosaicos)
- Período de tempo: anúncios, inquéritos, mosaicos
- Teste: Todos os (anúncios, inquéritos, dados puxa, mosaicos)
 
![Alcance Campaign1][20]

## <a name="languages"></a>Idiomas
Pode utilizar o menu pendente de idiomas para enviar uma versão diferente do seu Push para dispositivos que estão definidos para utilizar idiomas diferentes. Por predefinição, todos os dispositivos irão receber o mesmo Push independentemente que idioma estão definidos para utilizar. Os utilizadores com o seu dispositivo definido para um idioma diferente irão receber a versão de idioma predefinido da Push. Muitas das opções de campanha push permitem-lhe especificar o conteúdo alternativo para cada um dos idiomas adicionais que selecionar. 
 
![Alcance Campaign2][21]

### <a name="language-differences-apply-to"></a>As diferenças entre idiomas aplicam-se para:
- Idiomas: Idiomas exclusivos podem ser selecionados para além do idioma predefinido
- Campanha: Da mesma forma para todos os idiomas
- Notificação: Exclusiva para cada idioma além do idioma predefinido
- Content: Exclusiva para cada idioma além do idioma predefinido
- Audiência: Podem ser filtrados por um critério de idioma em separado
- Período de tempo: mesmo para todos os idiomas
- Teste: Poderá ser enviada para cada idioma cada vez
 
### <a name="supported-languages"></a>Idiomas suportados:
- Árabe (ar) 
- Búlgaras (bg) 
- Catalão (AC) 
- Chinês (zh) 
- Croata (hr) 
- Czech (CS) 
- Dinamarquês (the) 
- Neerlandês (ln das) 
- Inglês (en) 
- Finlandês (fi) 
- Francês (f) 
- Alemão (de) 
- Grego (el) 
- Hebraico (ele) 
- Hindi (Olá) 
- Húngaro (Hungria) 
- Indonésio (id) 
- Italiano () 
- Japonês (ja) 
- Coreano (ko) 
- Letão (lv) 
- Lituano (lt) 
- Malaio (macrolanguage) (ms) 
- Norueguês (Bokmål) (n) (b) 
- Polaco (pl) 
- Português (pt) 
- Romeno (ro) 
- Russo (ru) 
- Sérvio (sr) 
- Eslovaco (discos) 
- Esloveno (sl) 
- Espanhol (es) 
- Sueco (sv) 
- Tagalog (tl) 
- Tailandês (ésimo) 
- Turco (tr) 
- Ucraniano (Reino Unido) 
- Vietnamita (vi) 
 
## <a name="campaign"></a>Campanha
Pode utilizar a secção de campanha para definir o nome e a categoria da campanha, assim como se planeia ignorar secção audiência de uma campanha de Push e enviar desta campanha através da API atinja (e alguns elementos com o nível de baixo emissão API) em vez disso. Categorias podem ser utilizadas com um modelo de notificação personalizada para notificações de na aplicação de controlo com base nas definições predefinidas. Pode obter uma lista das existentes "categorias" através da API atinja.

> Aviso: Se utilizar a opção "Ignorar audiência, push será enviado para utilizadores através da API" na secção "Campanha" de uma campanha de alcance, não irá enviar campanha automaticamente, terá de enviá-lo manualmente através da API atinja.
 
![Alcance Campaign3][22]
 
### <a name="option-applies-to"></a>Opção aplica-se ao:
- Nome: todos os
- Categoria: Anúncios, inquéritos
- Ignorar audiência, push será enviado para utilizadores através da API: todos os
 
## <a name="notification"></a>Notificação
Pode utilizar a secção de notificação para definir as definições básicas de inclusão de push: O título da instalação autónoma, a mensagem, uma imagem na aplicação, ou se for dismissible. Muitas definições de notificação são específicas para a plataforma do seu dispositivo. Pode selecionar se o seu push será enviado "na aplicação" ou "fora do aplicação" ou ambos. (Não se esqueça de que os utilizadores podem "optar ativamente por participar no" ou "optar ativamente" de "fora do aplicação" emite no sistema operativo nível nos seus dispositivos e Azure Mobile Cativação não conseguir substituir esta definição. Também não se esqueça de que a API atinja processa "na aplicação" e "fora da aplicação" emite. Push API pode ser utilizado para processar "fora do aplicação" puxa demasiado.) Puxa pode ser personalizada com imagens ou conteúdo HTML, incluindo ligações abrangente para ligação fora da sua aplicação ou para outra localização na sua aplicação (Android SDK 2.1.0 ou posteriores categorias intenção necessárias). Pode alterar o ícone ou iOS distintivo e enviar conteúdo de texto ou web (um menu de contexto com ligação de conteúdo, URL html para outra localização dentro ou fora da aplicação). Também pode fazer simultaneamente dispositivos Android ou vibração com a instalação autónoma. (Não se esqueça de que terá do correto SDK as permissões no seu Android manifesto do ficheiro para simultaneamente ou vibração um dispositivo.) Não existe atualmente não indústria padrão para tamanhos de Android "conceito", uma vez que tamanhos de ecrã são diferentes em todos os dispositivos, mas 400 x 100 imagens trabalhar em praticamente qualquer tamanho de ecrã.

### <a name="delivery-types"></a>Tipos de entrega:
-    Terminar apenas a aplicação: será enviada a notificação quando o utilizador não utilizar a aplicação.
- Fora de notificação da aplicação apenas requer um certificado a partir da Apple ou Google (certificado APNS ou GCM).
- No-app apenas: A notificação é apresentada apenas quando a aplicação estiver em execução.
- A notificação de utiliza o sistema de entrega de Capptain contactar o utilizador. Pode personalizar completamente a esquema/apresentação visual do seu push.
- Em qualquer altura: Esta opção garante que envia uma notificação que ou a aplicação está em execução ou não.

 
![Alcance Campaign4][23]

### <a name="option-applies-to"></a>Opção aplica-se ao:
- Notificação: Anúncios, inquéritos
 
## <a name="content"></a>Conteúdo
Pode utilizar a secção Conteúda para modificar o conteúdo do seu anúncios, inquéritos, dados emite e mosaicos (apenas para o Windows Phone). A definição de conteúdo de campanhas de Push é específica para o tipo de campanha. 

### <a name="see-also"></a>Consulte também
- [IU documentação - atinja - conteúdo de emissão][Link 29]
 
![Alcance Campaign5][24]

## <a name="audience"></a>Audiência
Pode utilizar a secção audiência para definir uma lista de itens para limitar a sua campanha ou limites de campanha com base em critérios personalizados padrão. O conjunto de opções para limitar a sua audiência padrão permite-lhe transmitir para utilizadores de novos ou antigos ou apenas para utilizadores do push nativa. Também pode definir uma quota para limitar o número de utilizadores que receber o push. Pode editar manualmente a expressão para como a sua campanha é filtrada para incluir um ou mais critério aos utilizadores de destino. Pode escrever manualmente uma expressão de audiência. Como uma expressão tem de definir explicitamente a relação entre critérios. Um critério é descrito por um identificador de que tem de iniciar com uma letra maiúscula e não pode conter espaços. A relação entre os critérios pode ser descrita utilizando 'e', 'ou', 'não' operadores, bem como '(',')'. Exemplo: "Criterion1 ou (Criterion1 e não Criterion2)".

> Nota: Com uma audiência maior incluída no campanhas, filtragem de pesquisa do lado do servidor pode ser lento, especialmente se tentar iniciar várias campanhas ao mesmo tempo.

- Se possível, comece apenas uma campanha de cada vez.
- O máximo, apenas ser iniciado quatro campanhas de cada vez.
- Notificações push apenas aos seus utilizadores ativos (caixa de verificação "contrate apenas os utilizadores que podem ser contactados utilizando Push nativo" e "Concluem apenas utilizadores ativos") para que apenas os utilizadores que ainda tem a aplicação instalada e utilização-lo, terá de ser verificado.
Assim que a audiência estiver definida, pode utilizar o botão simulate para saber o número de utilizadores irá receber este Push. Isto irá calcular o número de utilizadores conhecidos potencialmente orientadas desta audiência (isto é uma estimativa com base numa amostra aleatória de utilizadores). Tenha em atenção que os utilizadores que desinstalou a aplicação também fazem parte desta audiência, mas não não possível alcançar.

### <a name="see-also"></a>Consulte também
- [IU documentação - alcance - novo Push critério][Link 28]

![Alcance Campaign6][25]

### <a name="edit-expression"></a>Editar expressão
![Alcance Campaign7][26]
 
### <a name="limit-your-audience-option-applies-to"></a>Limitar a sua opção de audiência aplica-se ao:
- Participar apenas um subconjunto dos utilizadores: todos os (anúncios, inquéritos, dados emite, mosaicos)
- Participar apenas os utilizadores antigos: todos os (anúncios, inquéritos, dados emite, mosaicos)
- Participar apenas novos utilizadores: todos os (anúncios, inquéritos, dados emite, mosaicos)
- Participar apenas os utilizadores idle: anúncios, inquéritos, mosaicos
- Participar apenas utilizadores ativos: todos os (anúncios, inquéritos, dados emite, mosaicos)
- Participar apenas os utilizadores que podem ser contactados utilizando emissão nativo: anúncios, inquéritos
 
## <a name="time-frame"></a>Período de tempo
Pode utilizar a secção do período de tempo para definir quando a operação push será enviada ou pode deixar o período de tempo em branco para iniciar a campanha imediatamente. Lembre-se de que através de fuso horário dos utilizadores finais podem iniciar campanha mais cedo que o que espera para os utilizadores finais na Ásia e envia pequenos lotes de puxa uma vez até que todos os fusos horários no mundo corresponde ao intervalo de tempo definido para sua campanha num dia. Utilizar o fuso horário dos utilizadores finais pode também provocar atrasos no campanhas de uma vez que este tenha que pedir a hora do telemóvel antes de iniciar a operação push.

> Nota: Campanhas sem uma data de fim pode colocar em cache puxa localmente e ainda apresentá-los depois campanhas manualmente concluídas. Para evitar este comportamento, uma hora de fim para campanhas de específicos.

### <a name="see-also"></a>Consulte também
- [Chegar - como apoios – agendamento][Link 3] 
 
![Alcance Campaign8][27]

### <a name="settings-apply-to"></a>Definições aplicam-se para:
- Período de tempo: anúncios, inquéritos, mosaicos
 
## <a name="test"></a>Teste
Pode utilizar a secção de teste para enviar este push para o seu próprio dispositivo de teste antes de guardar a campanha. Se tiver configurado eventuais idiomas personalizados para esta campanha, pode testar o push em cada idioma. Pode configurar um dispositivo de teste de "A minha conta".
> Nota: Sem do lado do servidor, os dados são registados quando utilizar o botão "Testar" emite, apenas os dados são registados para campanhas de real push.

### <a name="see-also"></a>Consulte também
- [IU documentação - a minha conta][Link 14]
 
![Alcance Campaign9][28]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
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
 
