<properties 
    pageTitle="Utilizar o Gestor de ligação híbrido | Microsoft Azure" 
    description="Instalar e configurar o Gestor de ligação híbrido e ligue aos conectores no local em aplicações de lógica" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>Ligue aos conectores no local através do Gestor de ligação híbrido

>[AZURE.NOTE] Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica. Disponibilidade geral de lógica aplicações (das versões DG) utiliza um gateway para a conectividade no local. Leia mais sobre o novo [gateway](app-service-logic-gateway-connection.md) e [Apps de lógica das versões DG](https://azure.microsoft.com/documentation/services/logic-apps/).

Para utilizar um sistema no local, aplicações de lógica utiliza o Gestor de ligação híbrido. Algumas conexões podem ligar a um sistema no local, como o SQL Server, SAP, SharePoint e assim sucessivamente. 

O Gestor de ligação de híbrido (HCM) é um clique-installer uma vez que está instalado num servidor IIS na rede da sua, atrás da firewall. Utilizar um reencaminhamento Azure Service Bus, HCM autentica sistema no local com o conector no Azure. 

> [AZURE.NOTE] Gestor de ligação de híbrido é necessário apenas se estiver a ligar a um recurso no local atrás da firewall. Se não estiver a ligar a um sistema no local, em seguida, não terá o Gestor de ligação híbrido.

Para começar, é necessário:

- Azure Service Bus reencaminhamento espaço de nomes SA cadeia de ligação. Consulte o artigo [Preços do serviço Bus](https://azure.microsoft.com/pricing/details/service-bus/) para determinar qual camada inclui estações de retransmissão.
- No local início de sessão no informações de sistema, incluindo o nome de utilizador e palavra-passe. Por exemplo, se estiver a estabelecer ligação a um SQL Server no local, terá a conta de início de sessão do SQL Server e a palavra-passe.
- No local informações do servidor, incluindo porta número e nome de servidor. Por exemplo, se estiver a estabelecer ligação a um SQL Server no local, terá do nome do SQL Server e o número de porta do TCP.

## <a name="get-the-service-bus-connection-string"></a>Obter a cadeia de ligação de Bus de serviço

No portal do Azure, copie a cadeia de ligação SA de raiz Bus de serviço. Nesta cadeia de ligação liga-se a conexão Azure ao seu sistema no local. 

1. No [portal clássica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885), selecione o seu espaço de nomes de serviço Bus e selecione **Informações de ligação**:

    ![][SB_ConnectInfo]

2. Copie a cadeia de ligação SA:

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>Instalar o Gestor de ligação híbrido

1. No [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), selecione a conexão que criou. Para o abrir, pode selecionar **Procurar**, selecione **Aplicações API**e, em seguida, selecione do conector ou API aplicação. 
<br/><br/>
Em **Ligação híbrido**, a configuração está **incompleto**:
<br/>
![][2] 

2. Selecione **híbrido ligação**. A cadeia de ligação de serviço Bus que introduziu anteriormente está listada.
3. Copie a **cadeia de configuração principal**:
<br/>
![][PrimaryConfigString]

4. Em **Gestor de ligação de híbrido no local**, pode transferir o Gestor de ligação híbrido ou instalá-lo diretamente a partir do portal. 
<br/><br/>
Para instalar diretamente a partir do portal, aceda ao seu servidor IIS no local, navegue para o portal e selecione **Transferir e configurar**.
<br/><br/>
Para transferir o Gestor de ligação híbrido, aceda ao seu servidor IIS no local e ir para a **aplicação ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). A instalação é iniciado automaticamente para que possa executar.

5. Na janela do **Programa de configuração do escuta** , introduza a **Cadeia de configuração primária** anteriormente colado (no passo 3) e selecione **instalar**.

Quando a configuração está concluída, apresenta a seguinte:
<br/>
![][3] 

Agora quando navega ao conector novamente, o estado da ligação híbrida está **ligado**. Poderá ter de fechar a conexão e abri-lo novamente:
<br/>
![][4] 

> [AZURE.NOTE] Para mudar para a cadeia de ligação secundário, execute novamente a configuração híbrida ligação e introduza a **Cadeia de configuração secundário**.


## <a name="tcp-ports-and-security"></a>Portas TCP e segurança

Quando cria uma ligação de híbrido, um Web site é criado no servidor IIS local no local. O servidor do IIS pode estar numa DMZ. Requisitos de portas TCP no servidor do IIS incluem:

Portas TCP | Por que motivo
--- | ---
 | Sem as portas TCP recebidas são necessárias.
9350 - 9354 | Estas portas são utilizadas para transmissão de dados. O Gestor de reencaminhamento de serviço Bus sondas porta 9350 para determinar se TCP está disponível. Se estiver disponível, em seguida, assume que porta 9352 também está disponível. Vai tráfego de dados através da porta 9352. <br/><br/>Permita ligações de saída para estas portas.
5671 | Quando a porta 9352 é utilizada para o tráfego de dados, porta 5671 é utilizada como o o canal de controlo. <br/><br/>Permitir que as ligações de saída para esta porta. 
80, 443 | Se as portas 9352 e 5671 não são utilizáveis, *em seguida,* portas 80 e 443 estão as portas contingência utilizadas para transmissão de dados e o canal de controlo.<br/><br/>Permita ligações de saída para estas portas.
Porta do sistema no local | No sistema no local, abra a porta utilizada pelo sistema. Por exemplo, SQL Server normalmente utiliza porta 1433. Abra esta porta TCP.

[Protegido por uma Firewall com Bus de serviço de alojamento](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>Resolução de problemas

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>Resolução de problemas no local

1. No servidor do IIS, confirme a função de web do IIS está instalada e todos os serviços do IIS são iniciados.
2. No servidor do IIS, confirme o Gestor de ligação híbrido é instalada e em execução:
 - No Gestor de IIS (inetmgr), o Web site ***MicrosoftAzureBizTalkHybridListener*** deve ser indicado e estar em execução. 
 - Este Web site utiliza ***HybridListenerAppPool*** que é executada como a conta de utilizador incorporados local do *serviço de rede* . Também deve ser iniciado este AppPool.
3. No servidor do IIS, confirme que a ligação é instalada e em execução: 
 - É criado um Web site para a conexão. Por exemplo, se tiver criado uma conexão SQL, existe um Web site ***MicrosoftSqlConnector_nnn*** . No Gestor de IIS (inetmgr), confirme este Web site está listado e iniciado. 
 - Este Web site utiliza o seu próprio conjunto aplicacional do IIS denominado ***HybridAppPoolnnn***. Este AppPool é executado como a conta de utilizador incorporadas local de *serviço de rede* . Este Web site e AppPool devem ambos ser iniciado. 
 - Procure o conector local. Por exemplo, se utiliza o seu Web site conexão porta 6569, navegue para http://localhost:6569. Um documento predefinido não estiver configurado para uma `HTTP Error 403.14 - Forbidden error` é esperado.
4. Na sua firewall, confirme as portas TCP listados neste tópico estão abertas.
5. Veja o sistema de origem ou de destino:
 - Alguns sistemas no local necessitam de ficheiros de dependência adicionais. Por exemplo, se estiver a estabelecer ligação SAP no local, alguns ficheiros SAP adicionais tem de estar instalados no servidor do IIS.
 - Verifique a conectividade do sistema com a conta de início de sessão. Por exemplo, a porta TCP utilizada pelo sistema tem de ser aberta, como porta 1433 para SQL Server. A conta de início de sessão que introduziu no portal do Azure tem de ter acesso ao sistema.
6. No servidor do IIS, verifique os registos de evento para todos os erros. 
7. Limpeza e reinstalar o Gestor de ligação híbrido: 
 - No IIS, elimine manualmente o Web site conexão e respectivo conjunto aplicacional. 
 - Volte a executar o Gestor de ligação híbrido e confirme que estiver a introduzir a correto **Primária cadeia de configuração** para a conexão.



### <a name="in-the-azure-classic-portal"></a>No portal do Azure clássico

1. Confirme que o espaço de nomes de serviço Bus tem um estado **activo** .
2. Quando cria a conexão, introduza a cadeia de ligação de serviço Bus SA. Não introduza a cadeia de ligação ACS.


## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES

**PERGUNTA**: Existem dois gestores de ligação híbrido. O que é a diferença? 

**Resposta**: existe a tecnologia de [Híbrido ligações](../biztalk-services/integration-hybrid-connection-overview.md) que é utilizada principalmente por Web Apps (anteriormente Web sites) e Apps Mobile (anteriormente móveis serviços) para ligar para o local. Este Gestor de ligações híbrido é a sua própria [configuração](../biztalk-services/integration-hybrid-connection-create-manage.md) e utiliza um serviço de BizTalk Azure (nos bastidores). Suporta protocolos TCP e HTTP.

Com conexões de aplicação de serviço do Azure, também temos um Gestor de ligação híbrido.  Este Gestor de ligação híbrido é que *não* utilize uma BizTalk Azure Service (nos bastidores) e suporta mais do que os protocolos TCP e HTTP. Consulte a [lista de aplicações API e conectores](app-service-logic-connectors-list.md).

Azure Service Bus ambos utilizam para ligar ao sistema no local.

**PERGUNTA**: Quando posso criar uma aplicação de API personalizada, posso utilizar o Gestor de ligação de implementações do aplicação do serviço para ligar a no local? 

**Resposta**: não no sentido tradicional. Pode utilizar um conector incorporado, configurar o Gestor da aplicação de serviço híbrido ligação para ligar ao sistema no local. Em seguida, utilize este conector com a aplicação de API personalizado, possivelmente utilizar uma aplicação de lógica. Atualmente, não pode desenvolver ou criar o seu próprio híbrido API aplicação (como o SQL conector ou ficheiro).

Se a sua API personalizada utiliza uma porta TCP ou HTTP, pode utilizar [Ligações de híbrido](../biztalk-services/integration-hybrid-connection-overview.md) e o seu Gestor de ligação híbrido. Neste cenário, é utilizado num serviço de BizTalk Azure. [Ligar ao servidor do SQL no local a partir de uma aplicação web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) poderão ajudar.  


## <a name="read-more"></a>Saiba mais

[Monitorize as suas aplicações de lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Bus preços de serviço](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
