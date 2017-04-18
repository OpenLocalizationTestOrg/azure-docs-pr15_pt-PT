<properties
    pageTitle="Notificação Azure concentradores - perguntas mais frequentes (FAQ)"
    description="Perguntas mais frequentes sobre como estruturar/implementar soluções no concentradores de notificação"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="ysxu"
    manager="erikre"
    keywords="notificação de emissão, as notificações push, as notificações push iOS, as notificações android push, ios push, android push"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu" />

#<a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notificações push notificações com notificação Azure concentradores - perguntas mais frequentes

##<a name="general"></a>Geral
###<a name="1---what-is-the-price-model-for-notification-hubs"></a>1. o que é o modelo de preço para notificação concentradores?
Notificação concentradores é-lhe fornecidos em três camadas:

* **Free** - obter até 1 milhão puxa por subscrição por mês.
* **Básicas** - obter 10 milhões puxa por subscrição por mês como um plano base, com as opções de crescimento de quota.
* **Padrão** - obter milhões de 10 puxa por subscrição por mês como um plano base, com quota aumentar opções, assim como capabilties de telemetria avançada.

Podem encontrar os detalhes mais recentes na página de [Notificação concentradores preços] . O preço é estabelecida ao nível de subscrição e é baseado no número de inícios de notificações push, por isso não interessa quantos concentradores espaços de nomes ou notificação de ter criado na sua subscrição do Azure.

Camadas de **Free** são oferecida para finalidade development com nenhuma garantia SLA. Enquanto este camada poderá ser um bom ponto de partida para aqueles que pretende explorar as capacidades de notificações push através do Azure notificação concentradores, não poderá a melhor escolha para médio das aplicações do grande escala.

**Básicas** & camadas**padrão** são-lhe fornecidas para a utilização de produção com as seguintes funcionalidades chaves activado *apenas para o padrão de camada*:

- *Telemetria Rich* - oferta de notificação concentradores um número das capacidades de exportar os dados de telemetria, bem como informações de registo de notificação para visualização offline e a análise de emissão.
- *Multitenancy* - Ideal se estiver a criar uma aplicação móvel utilizar concentradores de notificação para suportar a múltiplos inquilinos do. Esta opção permite-lhe definir credenciais de serviços de notificações de emissão (PNS) ao nível de espaço de nomes do concentrador de notificação para a aplicação e, em seguida, pode isolamento inquilinos fornecendo-lhes concentradores individuais neste espaço de nomes comuns. Isto permite facilidade de manutenção enquanto mantém as teclas de SA para enviar e receber notificações push dos controladores de notificação separados para cada inquilino garantir que não sejam de inquilino de publicação em sobreposição.
- *Agendado Push* - permite-lhe agendar as notificações push, que será subsequentemente na fila para cima e enviadas.
- *Importar em volume* - permite-lhe importar registos em massa.

###<a name="2---what-is-the-notification-hubs-sla"></a>2. o que é o SLA concentradores notificação?
Para camadas **básicos** e concentradores de notificação **padrão** , podemos garantir que pelo menos 99,9% das vezes, aplicações adequadamente configuradas será possível enviar notificações push ou executar operações de gestão de registo relativamente a um concentrador de notificação implementado dentro de uma camada suportada. Para saber mais sobre os nossos SLA, visite a página de [Notificação concentradores SLA] .

> [AZURE.NOTE] Não existem sem garantias SLA para ramificação entre o serviço de notificação de plataforma e o dispositivo, uma vez que notificação concentradores dependem fornecedores externos plataforma a notificação de emissão para o dispositivo.

###<a name="3---which-customers-are-using-notification-hubs"></a>3. quais os clientes que estão a utilizar notificação concentradores?
Temos um grande número de clientes que utilizam notificação concentradores com alguns dos apresentadas abaixo:

* Sochi 2014 – 100s dos grupos de interesse, milhões de 3 + dispositivos, notificação de 150 + milhões distribuído nas 2 semanas. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Horas de Seattle - [CaseStudy - horas de Seattle]
* Mural.LY - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing aplicações – 10s de milhões de dispositivos, enviar 3 milhões notificações/dia.

###<a name="4-how-do-i-upgrade-or-downgrade-my-notification-hubs-to-change-my-service-tier"></a>4. como atualizar ou alterar a minha concentradores de notificação para alterar a minha camada de serviços?
Aceda ao [Portal clássica Azure], clique em serviço Bus e em seguida, clique no seu espaço de nomes do seu centro de notificação. No separador escala, será capaz de alterar o seu camada de serviços de notificação concentradores.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##<a name="design--development"></a>Estrutura e desenvolvimento
###<a name="1---which-server-side-platforms-do-you-support"></a>1. as plataformas do lado do servidor que suporta?
Fornecemos SDK e [exemplos de concluída] para .NET, Java, PHP, Python, Node.js para que um back-end aplicação pode ser configuração para comunicar a notificação concentradores utilizar qualquer um dos seguintes plataformas. Notificação concentradores APIs são baseados nas restantes interfaces para que possa escolher diretamente falar com aqueles em vez disso, se não pretender adicionar uma dependência extra. Na página [NH - REST APIs] podem encontrar mais detalhes.

###<a name="2---which-client-platforms-do-you-support"></a>2. as plataformas de cliente que suporta?
Suportamos envio notificações push para o [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Universal do Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [China Android (através do Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [As aplicações do Chrome](notification-hubs-chrome-push-notifications-get-started.md) e [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) plataformas. Para obter uma lista completa de introdução a tutoriais de introdução lidar com as notificações push envio estes plataformas, visite a nossa página de [NH - introdução ao tutoriais] .

###<a name="3---do-you-support-smsemailweb-notifications"></a>3. suporta notificações de correio eletrónico/SMS/web?
Notificação concentradores destina-se principalmente enviar notificações para aplicações móveis utilizando as plataformas listadas em cima. Vamos ainda não fornece a capacidade para enviar correio eletrónico ou alertas SMS; No entanto, podem ser integradas plataformas de terceiros que disponibiliza a estas capacidades juntamente com concentradores de notificação para enviar notificações push nativo ao utilizar [As aplicações móveis do Azure].

Notificação concentradores também não fornecem no browser push notificação entrega serviço out-de-de-box. Os clientes podem escolher implementar este processo utilizando SignalR na parte superior das plataformas suportadas do lado do servidor. Se estiver à procura enviar notificações para aplicações do browser no sandbox do Chrome, consulte o artigo o [tutorial de aplicações do Chrome].

###<a name="4---what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4. o que é a relação entre as aplicações móveis do Azure e concentradores de notificação do Azure e quando utilizar o que?
Se tiver um existente aplicação móvel do back-end e pretende adicionar a capacidade para enviar notificações push, em seguida, pode utilizar concentradores de notificação do Azure. Se pretender configurar o seu back-end da aplicação móvel do zero, em seguida, deve considerar utilizar aplicações do Azure Mobile. Uma aplicação móvel do Azure Aprovisiona automaticamente um concentrador de notificação para que possam enviar notificações push facilmente a partir da aplicação móvel back-end. Preços para as aplicações móveis do Azure incluem os encargos base para um concentrador de notificação e paga apenas quando excede a puxa incluídas. Obter mais detalhes sobre os custos estão disponíveis na página [Preços de serviço de aplicação] .

###<a name="5---how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5. quantos dispositivos posso suportam se enviar notificações push através de notificação concentradores?
Consulte a página de [Notificação concentradores preços] para obter detalhes sobre o número de dispositivos suportados.

Para determinados cenários, se precisar de suporte para mais do que 10.000.000 dispositivos registados, consulte diretamente [Contacte-nos](https://azure.microsoft.com/overview/contact-us/) e podemos irá ajudá-lo dimensionar a solução.

###<a name="6---how-many-push-notifications-can-i-send-out"></a>6. notificações push quantos podem envio?
Consoante a camada selecionada, Azure irá dimensionar automaticamente com base no número de notificações a fluir através do sistema.

>[AZURE.NOTE] O custo de utilização global pode ir para cima com base no número de notificações push servido. Certifique-se de que esteja ciente de limites de camada existente descritos na página de [Notificação concentradores preços] .

Os nossos clientes existentes estiver a utilizar concentradores de notificação para enviar milhões de notificações push diariamente. Não tem de fazer nada especial para dimensionar o push notificações atinja desde que estiver a utilizar o Azure notificação concentradores.

###<a name="7---how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7. quanto tempo demora para enviadas emissão notificações para alcançar o meu dispositivo?
Azure notificação concentradores é possível processar, pelo menos, **notificação de emissão de 1 milhão envia um minuto** no normal utilizar cenário onde a carga recebida é muito consistentes e não é spikey natureza. Esta taxa pode variar consoante o número de etiquetas, natureza do envia recebida e outros fatores externos.

Durante a hora de entrega estimado, o serviço é possível calcular os destinos por mensagens de plataforma e encaminhar os serviços de entrega de notificação de push respetivos com base em expressões a etiqueta/etiquetas registado. A partir daqui no, é a responsabilidade dos serviços de notificações de emissão (PNS) para enviar a notificação para o dispositivo.

Um PNS não garante qualquer SLA para fornecer notificações; No entanto, normalmente uma grande maioria das notificações push são entregues dispositivos alvo dentro de alguns minutos (normalmente dentro dos limites de 10 minutos) a partir do tempo que sejam enviadas para o nossa plataforma. Poderão existir aberrantes alguns que poderão demorar mais tempo.

>[AZURE.NOTE] Azure notificação concentradores tem uma política no local para largar qualquer notificações push que não conseguir ser entregue a PNS em 30 minutos. Este atraso pode ocorrer de um número de razões, mais frequentemente porque o PNS é limitação a sua aplicação.

###<a name="8---is-there-any-latency-guarantee"></a>8. existe qualquer garantia latência?
Devido à natureza notificações push (que forem entregues por um serviço de notificações Push externos, específicos da plataforma), há garantias de latência. Normalmente, a maioria das notificações push obter entregue dentro de alguns minutos.

###<a name="9---what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9. tenha em consideração a que é necessário ter em consideração ao estruturar uma solução com espaços de nomes e notificação concentradores o que são?

####<a name="mobile-appenvironment"></a>Ambiente/aplicação móvel

* Deve ser um concentrador de notificação por aplicação móvel, por ambiente.
* Num cenário de inquilino com várias, cada inquilino deve ter um concentrador em separado.
* Nunca têm de partilhar ao mesmo concentrador de notificação entre os ambientes de teste e de produção como isto pode causar problemas a linha para baixo durante o envio de notificações. Por exemplo, Apple oferece pontos finais Sandbox e produção emissão com cada tendo credenciais separadas.
* Por predefinição, pode enviar notificações de teste para os seus dispositivos registados através do Portal do Azure ou o componente Azure integrado no Visual Studio. O limiar de está definido para 10 dispositivos que estão selecionados aleatoriamente do conjunto de registo.

>[AZURE.NOTE] Se o concentrador foi originalmente configurado com um certificado de sandbox Apple e reconfigurar, em seguida, utilizar um certificado de produção Apple, os tokens de dispositivo antigo seriam tornar-se inválidas com o novo certificado e causar puxa falha. É melhor separar a produção e testar ambientes e utilizar concentradores diferentes para diferentes ambientes.

####<a name="pns-credentials"></a>Credenciais PNS

Quando uma aplicação móvel está registada uma plataforma developer Portal (por exemplo, Apple ou Google etc), em seguida, irá obter uma aplicação do identificador e tokens de segurança que necessita de um back-end de aplicação para fornecer aos serviços de notificação de emissão a plataforma possam enviar notificações push para os dispositivos. Estes tokens de segurança que podem ser sob a forma de certificados (por exemplo, para o Apple iOS ou Windows Phone) ou teclas de segurança (Google Android, Windows etc) tem de ser configuradas de notificação concentradores. Isto é feito normalmente o nível de concentrador de notificação, mas também pode ser feito o nível de espaço de nomes num cenário de inquilino com várias.

####<a name="namespaces"></a>Espaços de nomes

Espaços de nomes podem ser utilizados para agrupamento de implementação.  Também pode ser utilizado para representar todos os concentradores de notificação para todos os inquilinos da aplicação do mesmo no cenário de inquilino com várias.

####<a name="geo-distribution"></a>Distribuição de geo

Distribuição de geo sempre não é crítica em cenários de notificações push. É notar que vários Push notificação serviços (por exemplo, APNS, GCM etc), que finalmente entregar as notificações push para os dispositivos, não são uniformemente distribuído.

Se tiver uma aplicação que é utilizada em todo o mundo, pode criar vários concentradores no diferentes espaços de nomes tomar partido a disponibilidade de serviço de notificação concentradores em diferentes regiões Azure em todo o mundo.

>[AZURE.NOTE] Isto irá aumentar o custo de gestão - bastante à volta de registos, para que esta não é realmente recomendada e só pode ser feita se existir uma necessidade explícita.

###<a name="10--should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10. devo fazer registos a partir da aplicação back-end ou diretamente através de cliente dispositivos?
Registos a partir da aplicação back-end são úteis quando tem de efetuar a autenticação de cliente antes de criar o registo ou quando tiver tags que tem de ser criadas ou modificadas pelo aplicação back-end, com base em algumas lógica de aplicação. Para obter detalhes, pode ler mais nas páginas do [registo de back-end orientações - 2] e [orientações de registo de back-end] .

###<a name="11--what-is-the-push-notification-delivery-security-model"></a>11. o que é o modelo de segurança de entrega de notificações push?
Azure concentradores de notificação de utilizar uma [Assinatura partilhadas do Access (SA)](../storage/storage-dotnet-shared-access-signature-part-1.md)-com base no modelo de segurança. Pode utilizar os tokens SA no nível de espaço de nomes de raiz ou ao nível de notificação concentradores granular. Estes tokens SA pode ser definido com regras de autorização diferente, por exemplo, enviar mensagem permissões, ouvir permissões de notificação etc. Obter mais detalhes estão disponíveis no documento de [modelo de segurança NH] .

###<a name="12--how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12. como posso lidar a carga sensíveis a maiúsculas e útil nas notificações push?
Todas as notificações são entregues dispositivos de destino à plataforma de emissão notificação serviços (PNS). Quando um remetente envia uma notificação a concentradores de notificação do Azure, em seguida, podemos processar e transmitir a notificação para os respetivos PNS.

Todas as ligações do remetente para os controladores de notificações do Azure e, em seguida, para o PNS utilizem HTTPS.

>[AZURE.NOTE] Azure notificações concentradores não regista a carga útil da mensagem de qualquer forma.

Para enviar payloads confidenciais no entanto, recomendamos que um padrão de emissão seguro onde o remetente oferece uma notificação 'ping' com um identificador de mensagem para o dispositivo sem a carga sensíveis a maiúsculas e útil e quando a aplicação no dispositivo recebe este carga útil, é possível ligar uma API segura diretamente para obter os detalhes da mensagem. Um guia sobre como implementar o padrão de descrito acima está disponível na página [NH - tutorial seguro emissão] .

##<a name="operations"></a>Operações
###<a name="1---what-is-the-disaster-recovery-dr-story"></a>1. o que é o bloco de recuperação de falhas (DR)?
Fornecemos cobertura de recuperação de falhas de metadados no nosso fim (nome do concentrador de notificação, cadeia de ligação e outras informações críticas). Quando um cenário de DR é acionado, os dados de registos serão o **segmento apenas** infraestrutura do concentradores de notificação que serão perdidas. Terá de implementar uma solução para voltar preencher estes dados para o seu novo concentrador pós a recuperação.

- *Passo1* - criar um concentrador de notificação secundário num centro de dados diferente. Pode criar isto no momento no momento do evento de DR ou pode criar um partir de introdução ir. Não efetuar muitas de uma diferença opção que escolher porque concentrador de notificação de aprovisionamento é um processo relativamente rápido pela ordem de aguardar alguns segundos. Ter um partir do início irá proteger do evento DR que afetam as capacidades de gestão, pelo que é a opção vivamente recomendado.

- *Passo2 para* - hidrato o concentrador de notificação secundário com os registos a partir do Centro de notificação do principal. Não é recomendado para tentar manter registos do ambos os concentradores e tente mantê-las sincronizado no momento como registos são a chegar no - normalmente que não tiver funcionado bem devido a tendência inerente dos registos a expirar no lado PNS. Notificação concentradores limpá-los como podemos receber comentários PNS sobre registos expirados ou inválidos.  

Recomendação é utilizar uma back-end da aplicação que quer:

- Mantém um determinado conjunto de registos no fim de modo a que pode fazer um inserir em volume ao concentrador de notificação secundário em caso de DR

**OU**

- Obtém normal informação dos registos a partir do centro do principal como uma cópia de segurança e, em seguida, uma em volume inserir o NH secundário.

>[AZURE.NOTE] Funcionalidades de exportação/importação de registos estão disponíveis na camada padrão são descrita no documento [Registos de exportação/importação] .

Se não tiver um back-end, em seguida, quando inicia a aplicação em qualquer um dos dispositivos de destino, irão efetuar um novo registo no Centro de notificação do secundário, e são eventualmente secundária concentrador de notificação terão todos os dispositivos ativos registados.

A desvantagem é que haverá um período de tempo quando dispositivos onde aplicações ainda não tenha aberto o não irão receber notificações.

###<a name="2---is-there-any-audit-log-capability"></a>2. existe qualquer funcionalidade de registo de auditoria?
Todas as operações de gestão de concentradores notificação aceda a operação de registos que são expostos no [Portal clássica Azure].

##<a name="monitoring--troubleshooting"></a>Monitorização e resolução de problemas
###<a name="1---what-troubleshooting-capabilities-are-available"></a>1. que funcionalidades de resolução de problemas estão disponíveis?
Azure notificação concentradores fornecem várias funcionalidades para efetuar comuns resolução de problemas, principalmente no cenário mais comum à volta de notificações ignorados. Ver os detalhes na nossa [NH - resolução de problemas] documento técnico.

###<a name="2---what-telemetry-features-are-available"></a>2. que funcionalidades de telemetria estão disponíveis?
Azure notificação concentradores permite visualizar dados de telemetria no [Azure clássica Portal]. Detalhes de métricas disponíveis estão disponíveis na página [NH - métricas] .

>[AZURE.NOTE] Notificações bem sucedidas apenas vermelha que lhe foram entregues as notificações push para o serviço de notificações Push externos (por exemplo, APNS para Apple, GCM para Google etc). É até PNS para entregar a notificação de dispositivos de destino. Normalmente, a PNS não expõe métricas de entrega para terceiros.  

Fornecemos também a capacidade de exportar os dados de telemetria através de programação (na camada **padrão** ). Consulte o artigo o [NH - exemplo métricas] para obter detalhes.

[Azure Portal clássico]: https://manage.windowsazure.com
[Notificação concentradores preços]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notificação concentradores SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - horas de Seattle]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - REST APIs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - tutoriais de introdução]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Tutorial de aplicações do Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Orientação do registo de back-end]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Orientação do registo de back-end - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modelo de segurança NH]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - tutorial Push seguro]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - resolução de problemas]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - métricas]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - exemplo métricas]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Registos de exportação/importação]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[amostras concluídas]: https://github.com/Azure/azure-notificationhubs-samples
[Aplicações móveis Azure]: https://azure.microsoft.com/en-us/services/app-service/mobile/
[Aplicação de serviço de preços]: https://azure.microsoft.com/en-us/pricing/details/app-service/
