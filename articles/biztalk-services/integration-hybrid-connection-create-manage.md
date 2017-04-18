<properties 
    pageTitle="Criar e gerir ligações de híbrido | Microsoft Azure" 
    description="Saiba como criar uma ligação de híbrido, gerir a ligação e instalar o Gestor de ligação híbrido. MAK, MAB, WABS" 
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
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>Criar e gerir ligações de híbrido


## <a name="overview-of-the-steps"></a>Descrição geral dos passos
1. Crie uma ligação de híbrido ao introduzir o **nome do anfitrião** ou o **FQDN** do recurso no local na sua rede privada.
2. Ligação seu aplicações Azure web ou Azure aplicações móveis para a ligação híbrido.
3. Instalar o Gestor de ligação híbrido no seu recurso no local e ligue-se para a ligação híbrido específico. Portal do Azure fornece uma experiência de clique único para instalar e ligar.
4. Gerir ligações de híbrido e as respetivas chaves de ligação.

Este tópico indica estes passos. 

> [AZURE.IMPORTANT] É possível definir um ponto final de ligação de híbrido para um endereço IP. Se utilizar um endereço IP, poderá ou poderão não alcançar o recurso no local, dependendo do seu cliente. A ligação híbrido depende do cliente de estação de efetuar uma pesquisa de DNS. Na maioria dos casos, o __cliente__ é o código da aplicação. Se o cliente não efetua uma pesquisa de DNS, (-não tentar resolver o endereço IP, como se fosse um nome de domínio (x.x.x. x)), em seguida, o tráfego não é enviado através da ligação híbrido.
>
> Por exemplo (pseudocode), pode definir **10.4.5.6** como o seu anfitrião no local:
> 
> **Cenário que se segue funciona:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Cenário que se segue não funciona:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Criar uma ligação de híbrido

Uma ligação de híbrido podem ser criada no portal do Azure utilizando Web Apps **ou** utilizando os serviços de BizTalk. 

**Para criar ligações de híbrido utilizando Web Apps**, consulte o artigo [Ligar o Azure Web Apps para um recurso no local](../app-service-web/web-sites-hybrid-connection-get-started.md). Também pode instalar o Gestor de ligação de híbrido (HCM) a partir do seu web app, que é o método preferencial. 

**Para criar ligações de híbrido no BizTalk serviços**:

1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **BizTalk serviços** e, em seguida, selecione o seu serviço de BizTalk. 

    Se não tiver um serviço de BizTalk existente, pode [criar um serviço de BizTalk](biztalk-provision-services.md).
3. Selecione o separador **Híbrido ligações** :  
![Separador de ligações de híbrido][HybridConnectionTab]

4. Selecione **criar uma ligação de híbrido** ou selecione o botão **Adicionar** na barra de tarefas. Introduza o seguinte:

    Propriedade | Descrição
--- | ---
Nome | O nome da ligação híbrido tem de ser exclusivo e não pode ser o mesmo nome que o serviço de BizTalk. Pode introduzir um nome qualquer mas ser específico com a sua finalidade. Alguns exemplos incluem:<br/><br/>Folha de pagamentos*SQLServer*<br/>SupplyList*SharepointServer*<br/>Os clientes*OracleServer*
Nome do anfitrião | Introduza o nome do anfitrião totalmente qualificado, apenas o nome do anfitrião ou o endereço de IPv4 do recurso no local. Alguns exemplos incluem:<br/><br/>mySQLServer<br/>*mySQLServer*. *Domínio*. .com de*empresa*Corp.<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. .com de *empresa*<br/>10.100.10.10<br/><br/>Se utilizar o endereço IPv4, tenha em atenção que o código de cliente ou aplicação poderá não resolver o endereço IP. Consulte o artigo importante nota na parte superior deste tópico.
Porta | Introduza o número de porta do recurso no local. Por exemplo, se estiver a utilizar aplicações Web, introduza porta 80 ou porta 443. Se estiver a utilizar o SQL Server, introduza porta 1433.

5. Selecione a marca de verificação para concluir a configuração. 

#### <a name="additional"></a>Adicionais

- Múltiplas ligações híbrido pode ser criadas. Consulte o artigo o [BizTalk serviços: edições gráfico](biztalk-editions-feature-chart.md) para o número de ligações permitidas. 
- Cada ligação de híbrido é criada com um par de cadeias de ligação: aplicação chaves esse chaves enviar e no local que OUVIR. Cada par de tem uma principal e uma tecla secundária. 


## <a name="LinkWebSite"></a>Ligar o Azure de aplicação de serviço Web App ou aplicação móvel

Para ligar uma aplicação Web ou a aplicação Mobile num serviço de aplicação do Azure a uma ligação de híbrido existente, selecione **utilizar uma ligação de híbrido existente** na pá híbrido ligações. Consulte o artigo [Access no local recursos utilizando ligações de híbrido no Azure aplicação de serviço](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="InstallHCM"></a>Instalar o Gestor de ligação híbrido no local

Após a criação de uma ligação de híbrido, instale o Gestor de ligação híbrido no recurso no local. Podem ser transferido a partir do seu aplicações Azure web ou do seu serviço de BizTalk. Passos para BizTalk serviços: 

1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **BizTalk serviços** e, em seguida, selecione o seu serviço de BizTalk. 
3. Selecione o separador **Híbrido ligações** :  
![Separador de ligações de híbrido][HybridConnectionTab]
4. Na barra de tarefas, selecione **o programa de configuração no local**:  
![O programa de configuração no local][HCOnPremSetup]
5. Selecione **instalar e configurar** a executar ou a transferir o Gestor de ligação híbrido no sistema no local. 
6. Selecione a marca de verificação para iniciar a instalação. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Adicionais
- Gestor de ligação de híbrido pode ser instalado nos sistemas operativos seguintes:

    - Windows Server 2008 R2 (.NET Framework 4,5 + e Windows Management Framework 4.0 + necessário)
    - Windows Server 2012 (Windows Management Framework 4.0 + necessário)
    - Windows Server 2012 R2


- Depois de instalar o Gestor de ligação híbrido, ocorre o seguinte: 

    - A ligação híbrido à alojado no Azure é automaticamente configurada para utilizar a cadeia de ligação de aplicação principal. 
    - O recurso no local é automaticamente configurado para utilizar a cadeia de ligação no local principal.

- O Gestor de ligação híbrido tem de utilizar uma cadeia de ligação válida no local para autorização. O Azure Web Apps ou aplicações Mobile, tem de utilizar uma cadeia de ligação do pedido válido para autorização.
- É possível dimensionar híbrido ligações instalando outra instância do Gestor de ligações híbrido no outro servidor. Configure o serviço de escuta no local para utilizar o mesmo endereço como a primeira escuta de no local. Neste caso, o tráfego é distribuído aleatoriamente (round robin) entre os listeners ativo no local. 


## <a name="ManageHybridConnection"></a>Gerir ligações de híbrido
Para gerir as suas ligações híbrido, pode:

- Utilizar o portal do Azure e aceda ao seu serviço de BizTalk. 
- Utilize os [REST APIs](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copiar/gerar as cadeias de ligação híbrido

1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **BizTalk serviços** e, em seguida, selecione o seu serviço de BizTalk. 
3. Selecione o separador **Híbrido ligações** :  
![Separador de ligações de híbrido][HybridConnectionTab]
4. Selecione a ligação híbrido. Na barra de tarefas, selecione **Gerir ligação**:  
![Gerir as opções][HCManageConnection]

    **Ligação gerir** lista as cadeias de ligação à aplicação e no local. Pode copiar as cadeias de ligação ou gerar da chave do Access utilizada na cadeia de ligação. 

    **Se selecionar gerar**, a tecla de acesso partilhado utilizados na cadeia de ligação foi alterado. Faça o seguinte:
    - No portal do Azure clássico, selecione **Sincronizar teclas** na aplicação do Azure.
    - Voltar a executar a **Configuração no local**. Quando voltar a executar a configuração no local, o recurso no local é automaticamente configurado para utilizar a cadeia de ligação primária atualizado.


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Política de grupo de utilização para controlar os recursos no local utilizados por uma ligação de híbrido

1. Transferir os [Modelos administrativos híbrido Gestor de ligação](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extrai os ficheiros.
3. No computador que modifica a política de grupo, faça o seguinte:  

    - Copiar o. Ficheiros ADMX para a pasta *%WINROOT%\PolicyDefinitions* .
    - Copiar o. Ficheiros ADML para a pasta de *%WINROOT%\PolicyDefinitions\en-us* .

Assim que copiou, pode utilizar o Editor de política de grupo para alterar a política.




## <a name="next"></a>Seguinte

[Ligar a aplicações Azure Web a um recurso de no local](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Ligar ao servidor do SQL no local a partir das aplicações Azure Web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[Descrição geral das ligações de híbrido](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>Consulte também

[API REST para gerir os serviços de BizTalk no Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk serviços: Gráfico de edições](biztalk-editions-feature-chart.md)  
[Criar um serviço de BizTalk através do portal clássico Azure](biztalk-provision-services.md)  
[BizTalk serviços: Separadores de Dashboard, Monitor e escala](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
