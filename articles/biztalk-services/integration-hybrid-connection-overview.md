<properties
    pageTitle="Descrição geral das ligações de híbrido | Microsoft Azure"
    description="Saiba mais sobre ligações híbrido, segurança, portas TCP e configurações suportadas. MAK, MAB, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>Descrição geral das ligações de híbrido
Introdução às ligações híbrido, apresenta as configurações suportadas e apresenta as portas TCP necessárias.


## <a name="what-is-a-hybrid-connection"></a>O que é uma ligação de híbrido

Híbrido ligações são uma funcionalidade dos serviços de BizTalk Azure. Híbrido ligações fornecem uma forma fácil e conveniente para ligar a funcionalidade de Web Apps no Azure aplicação de serviço (anteriormente sites públicos) e a funcionalidade de aplicações móveis no Azure aplicação de serviço (anteriormente Mobile serviços) para recursos no local atrás da firewall.

![Ligações de híbrido][HCImage]

Os benefícios de ligações de híbrido incluem:

- Web Apps e Mobile Apps podem aceder a dados no local e serviços existentes em segurança.
- Várias aplicações Web ou aplicações Mobile, pode partilhar uma ligação de híbrido para aceder um recurso no local.
- Portas TCP mínimas necessárias para aceder à sua rede.
- Aplicações utilizando ligações de híbrido aceder apenas o recurso específico no local que é publicado através da ligação híbrido.
- Pode ligar a qualquer outro recurso no local que utiliza uma porta TCP estática, tal como SQL Server, MySQL, HTTP Web API e a maioria dos serviços Web personalizados.

    > [AZURE.NOTE] Atualmente, serviços baseados na TCP que utilizam portas dinâmicas (como modo passivo FTP ou modo passivas expandido) não são suportados. LDAP também não é suportada. LDAP utiliza uma porta TCP estática mas -lo também pode utilizar UDP. Como resultado, não é suportada.

- Podem ser utilizados com todos os quadros suportados pelo Web Apps (.NET, PHP, Java, Python, Node.js) e Apps de Mobile (Node.js, .NET).
- Web Apps e Mobile Apps podem aceder a no local recursos exatamente da mesma forma como se na Web ou a aplicação Mobile está localizada na sua rede local. Por exemplo, o mesma ligação cadeia utilizada no local pode também ser utilizado no Azure.


Ligações de híbrido também fornecem administradores da empresa com o controlo e visibilidade em relação as recursos da empresa pode ser consultada pela aplicações híbridas, incluindo:

- Definições de política de grupo de utilizar, os administradores podem permitir ligações de implementações da rede e também designar recursos que podem ser acedidos por aplicações híbridas.
- Registos de eventos e auditoria na rede da empresa fornecem visibilidade para os recursos que pode ser consultada pela híbrido ligações.


## <a name="example-scenarios"></a>Cenários de exemplo

Ligações de híbrido suportam as seguintes combinações de quadro e de aplicação:

- Acesso .NET framework do SQL Server
- Acesso .NET framework serviços HTTP/HTTPS com WebClient
- Acesso PHP ao SQL Server, MySQL
- Acesso Java para SQL Server, MySQL e Oracle
- Acesso de Java a serviços HTTP/HTTPS

Ao utilizar híbrido ligações para aceder a no local SQL Server, considere o seguinte:

- SQL Express instâncias com nome tem de ser configuradas para utilizar portas estáticas. Por predefinição, SQL Express com o nome instâncias utilizam portas dinâmicos.
- SQL Express instâncias predefinido utiliza uma porta estática, mas tem de estar ativada TCP. Por predefinição, o TCP não estiver ativado.
- Quando utilizar clusters ou grupos de disponibilidade, o `MultiSubnetFailover=true` modo não é atualmente suportado.
- O `ApplicationIntent=ReadOnly` não são atualmente suportadas.
- Autenticação do SQL que devem ser seguida como o método de autorização de ponto a ponto suportado pela aplicação Azure e o SQL server no local.


## <a name="security-and-ports"></a>Todas as portas e segurança

Híbrido ligações utilizam autorização de assinatura partilhadas do Access (SA) para proteger as ligações a partir de aplicações do Azure e o Gestor de ligação de híbrido no local para a ligação híbrida. Teclas de ligação em separado são criadas para a aplicação e o Gestor de ligação de híbrido no local. Estas teclas ligação podem ser apresentadas ao longo e revogadas de forma independente.

Ligações de híbrido fornecem para distribuição de forma totalmente integrada e segura de teclas para as aplicações e o Gestor de ligação de híbrido no local.

Consulte o artigo [criar e gerir ligações de híbrido](integration-hybrid-connection-create-manage.md).

*Autorização de aplicação está separada da ligação híbrido*. Pode ser utilizado qualquer método de autorização apropriada. O método de autorização depende métodos de autorização de ponto a ponto suportados ao longo da nuvem Azure e os componentes no local. Por exemplo, as aplicações do Azure acede a uma SQL Server no local. Neste cenário, autorização de SQL pode ser o método de autorização que é suportado para fim.

#### <a name="tcp-ports"></a>Portas TCP
Ligações de híbrido necessitam de conectividade de TCP ou HTTP apenas saída da sua rede privada. Não tem de abrir qualquer portas da firewall ou alterar a configuração de perímetro de rede para permitir a qualquer conectividade de entrada na sua rede.

As seguintes portas TCP são utilizadas pelo híbrido ligações:

Porta | Por que motivo precisa
--- | ---
9350 - 9354 | Estas portas são utilizadas para transmissão de dados. O Gestor de reencaminhamento de serviço Bus sondas porta 9350 para determinar se TCP está disponível. Se estiver disponível, em seguida, assume que porta 9352 também está disponível. Vai tráfego de dados através da porta 9352. <br/><br/>Permita ligações de saída para estas portas.
5671 | Quando porta 9352 é utilizada para o tráfego de dados, porta 5671 é utilizada como o canal de controlo. <br/><br/>Permitir que as ligações de saída para esta porta.
80, 443 | Estas portas são utilizadas para alguns pedidos de dados para Azure. Além disso, se não estiverem utilizáveis portas 9352 e 5671, *em seguida,* portas 80 e 443 estão as portas contingência utilizadas para transmissão de dados e o canal de controlo.<br/><br/>Permita ligações de saída para estas portas. <br/><br/>**Nota** Não é recomendado para utilizá-los como as portas de contingência em vez das outras portas TCP. HTTP/WebSocket é utilizado como o protocolo em vez de TCP nativo para os canais de dados. Poderá resultar a no desempenho inferior.



## <a name="next-steps"></a>Próximos passos

[Criar e gerir ligações de híbrido](integration-hybrid-connection-create-manage.md)<br/>
[Ligar a aplicações Azure Web a um recurso de no local](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Ligar no local SQL Server a partir de uma aplicação web do Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>Consulte também

[API REST para gerir os serviços de BizTalk no Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[BizTalk serviços: edições gráfico](biztalk-editions-feature-chart.md)<br/>
[Criar um serviço de BizTalk através do portal Azure](biztalk-provision-services.md)<br/>
[BizTalk serviços: Separadores de Dashboard, Monitor e escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
