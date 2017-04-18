<properties
    pageTitle="Criar Azure BizTalk serviços no portal do Azure | Microsoft Azure"
    description="Saiba como pode aprovisionar ou criar Azure BizTalk serviços no portal do Azure; MAK, MAB, WABS"
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
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>



# <a name="create-biztalk-services-using-the-azure-portal"></a>Criar serviços de BizTalk utilizando o portal do Azure

Crie Azure BizTalk serviços no portal do Azure.

> [AZURE.TIP] Para iniciar sessão no portal do Azure, tem uma conta Azure e subscrição Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita dentro de alguns minutos. Consulte o artigo [versão de avaliação gratuita Azure](http://go.microsoft.com/fwlink/p/?LinkID=239738).

## <a name="create-a-biztalk-service"></a>Criar um serviço de BizTalk
Dependendo da edição que escolher, nem todas as definições de serviço de BizTalk poderão estar disponíveis.

1. Inicie sessão no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação inferior, selecione **Novo**:  
![Selecione o botão novo][NEWButton]

3. Selecione **Serviços de aplicação** > **BIZTALK serviço** > **Criar PERSONALIZADO**:  
![Selecione BizTalk serviço e selecione Criar personalizado][NewBizTalkService]

4. Introduza as definições do serviço de BizTalk:

    <table border="1">
    <tr>
    <td><strong>Nome do serviço BizTalk</strong></td>
    <td>Pode introduzir um nome qualquer mas ser específico. Alguns exemplos incluem:<br/><br/>
    <em>minhaempresa</em>. biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>. biztalk.windows.net<br/>
    <em>MinhaAplicação</em>. biztalk.windows.net<br/><br/>". biztalk.windows.net" são automaticamente adicionados ao nome que introduzir. Esta ação cria um URL que é utilizado para aceder ao seu serviço de BizTalk, como <strong>https://<em>MinhaAplicação</em>. biztalk.windows.net</strong>.
    </td>
    </tr>
    <tr>
    <td><strong>Edition</strong></td>
    <td>Se estiver na fase de testes/desenvolvimento, selecione <strong>Programador</strong>. Se estiver na fase da produção, utilize o <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk serviços: edições gráfico</a> para determinar se <strong>Premium</strong>, <strong>padrão</strong>ou <strong>básicas</strong> está a escolha correta para o seu cenário de negócio.
    </td>
    </tr>
    <tr>
    <td><strong>Região</strong></td>
    <td>Selecione a região geográfica para alojar o seu serviço de BizTalk.</td>
    </tr>
    <tr>
    <td><strong>URL de domínio</strong></td>
    <td><strong>Opcional</strong>. Por predefinição, o URL do domínio é <em>YourBizTalkServiceName</em>. biztalk.windows.net. Também pode ser introduzido um domínio personalizado. Por exemplo, se o seu domínio fizer <em>contoso</em>, pode introduzir: <br/><br/>
    <em>Minhaempresa</em>. contoso.com<br/>
    <em>MyCompanyMyApplication</em>. contoso.com<br/>
    <em>MinhaAplicação</em>. contoso.com<br/>
    <em>YourBizTalkServiceName</em>. contoso.com<br/>
    </td>
    </tr>
    </table>
Selecione a seta seguinte.

5. Introduza o armazenamento e definições de base de dados:

    <table border="1">
    <tr>
    <td><strong>Conta de armazenamento de monitorização/arquivar</strong></td>
    <td>Selecione uma conta de armazenamento existente ou crie uma nova conta de armazenamento. <br/><br/>Se criar uma nova conta de armazenamento, introduza o <strong>Nome da conta de armazenamento</strong>.</td>
    </tr>
    <tr>
    <td><strong>Base de dados de controlo</strong></td>
    <td>Se utilizar uma base de dados do SQL Azure existente, não pode ser utilizado por outro serviço de BizTalk. É necessário o nome de início de sessão e palavra-passe introduzida quando que o servidor de bases de dados do SQL Azure foi criado.<br/><br/><strong>Sugestão:</strong> Crie a base de dados de controlo e a conta de armazenamento de monitorização/arquivar na mesma região como o serviço de BizTalk.</td>
    </tr>
    </table>
Selecione a seta seguinte.

6. Introduza as definições de base de dados:

    <table border="1">
    <tr>
    <td><strong>Nome</strong></td>
    <td>Está disponível quando <strong>criar uma nova instância de base de dados SQL</strong> é selecionada ao ecrã anterior.
    <br/><br/>
   Introduza um nome de base de dados SQL para ser utilizado por seu serviço de BizTalk.</td>
    </tr>
    <tr>
    <td><strong>Servidor</strong></td>
    <td>Está disponível quando <strong>criar uma nova instância de base de dados SQL</strong> é selecionada ao ecrã anterior.
    <br/><br/>
   Selecione um servidor de base de dados SQL existente ou crie um novo servidor de base de dados SQL.</td>
    </tr>
    <tr>
    <td><strong>Nome de início de sessão do servidor</strong></td>
    <td>Introduza o nome de utilizador de início de sessão.</td>
    </tr>
    <tr>
    <td><strong>Palavra-passe de início de sessão do servidor</strong></td>
    <td>Introduza a palavra-passe de início de sessão.</td>
    </tr>
    <tr>
    <td><strong>Região</strong></td>
    <td>Está disponível quando <strong>criar uma nova base de dados SQL instância</strong> está selecionada. Selecione a região geográfica para alojar o seu base de dados do SQL.</td>
    </tr>
    </table>

Selecione a marca de verificação para concluir o assistente. O ícone de progresso é apresentado:  
![Ícone do progresso mostra quando estiver concluído][ProgressComplete]

Quando estiver concluído, o serviço de BizTalk Azure é criado e pronto para as suas aplicações. As definições predefinidas são suficientes. Se quiser alterar as predefinições, selecione **BIZTALK serviços** no painel de navegação do lado esquerdo e, em seguida, selecione o seu serviço de BizTalk. Definições adicionais são apresentadas nos [separadores de Dashboard, Monitor e a escala](biztalk-dashboard-monitor-scale-tabs.md) na parte superior.

Consoante o estado do serviço BizTalk, existem algumas operações que não podem ser concluídas. Para obter uma lista destas operações, aceda a [BizTalk serviços Estado gráfico](biztalk-service-state-chart.md).


## <a name="post-provisioning-steps"></a>Passos pós aprovisionamento

-  [Instalar o certificado num computador local](#InstallCert)
-  [Adicionar um certificado de produção pronto para](#AddCert)
-  [Obter o espaço de nomes do controlo de acesso](#ACS)

#### <a name="InstallCert"></a>Instalar o certificado num computador local
Como parte do serviço de BizTalk aprovisionamento, um certificado autoassinado é criado e associado à sua subscrição BizTalk serviço. Tem de transferir este certificado e instalá-lo em computadores a partir de onde quer implementar aplicações de serviço de BizTalk ou enviar mensagens para um ponto final de serviço de BizTalk.

1. Inicie sessão no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços BIZTALK** no painel de navegação do lado esquerdo e, em seguida, selecione a subscrição do serviço de BizTalk.
3. Selecione o separador **Dashboard** .
4. Selecione **Transferir o certificado SSL**:  
![Modificar um certificado SSL][QuickGlance]
5. Faça duplo clique sobre o certificado e execute o Assistente para instalar o certificado. Certifique-se de que instala o certificado no arquivo **As autoridades de certificação de raiz fidedignos** .

#### <a name="AddCert"></a>Adicionar um certificado de produção pronto para
O certificado autoassinado que é criado automaticamente quando criar serviços de BizTalk destina-se para utilização em ambientes de desenvolvimento apenas. Cenários de produção, substituí-lo com um certificado de produção pronto para.

1. No separador **Dashboard** , selecione **Um certificado SSL atualização**.
2. Navegue até ao seu certificado SSL (*nome do certificado*. pfx) privado que inclui o seu nome de BizTalk serviço, introduza a palavra-passe e, em seguida, clique na marca de verificação.

#### <a name="ACS"></a>Obter o espaço de nomes do controlo de acesso

1. Inicie sessão no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Selecione **Serviços BIZTALK** no painel de navegação do lado esquerdo e, em seguida, selecione o seu serviço de BizTalk.
3. Na barra de tarefas, selecione **Informações de ligação**:  
![Selecione as informações de ligação][ACSConnectInfo]

4. Copie os valores de controlo de acesso.

Quando implementar um projeto de BizTalk serviço a partir do Visual Studio, introduza este espaço de nomes do controlo de acesso. O espaço de nomes do controlo de acesso é criado automaticamente do seu serviço de BizTalk.

Os valores de controlo de acesso podem ser utilizados com qualquer aplicação. Quando é criado Azure BizTalk serviços, este espaço de nomes do controlo de acesso controla a autenticação com a sua implementação BizTalk serviço. Se pretender alterar a subscrição ou gerir o espaço de nomes, selecione **Do ACTIVE DIRECTORY** no painel de navegação do lado esquerdo e, em seguida, selecione o seu espaço de nomes. Barra de tarefas apresenta as opções.

Ao clicar em **Gerir** abre-se o Portal de gestão de controlo de acesso. No Portal de gestão de controlo do Access, o serviço de BizTalk utiliza **identidades de serviço**:  
![Portal de gestão de controlo do serviço ACS identidades do Access][ACSServiceIdentities]

A identidade do serviço de controlo de acesso é um conjunto de credenciais que permitem aplicações ou clientes para autenticar diretamente com o controlo de acesso e receber um token.

> [AZURE.IMPORTANT]O serviço de BizTalk utiliza **proprietário** a identidade do serviço de predefinida e o valor de **palavra-passe** . Se utiliza o valor de chave simétrica em vez do valor de palavra-passe, a seguinte mensagem de erro poderá ocorrer.<br/><br/>*Não foi possível ligar à conta de serviço de gestão de controlo de acesso com as credenciais especificadas*

[Gerir o seu ACS espaço de nomes de](https://msdn.microsoft.com/library/azure/hh674478.aspx) lista algumas orientações e recomendações.

## <a name="requirements-explained"></a>Requisitos de explicadas

Estes requisitos não são aplicadas a edição gratuito.
<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>O que precisa</strong></td>
        <td><strong>Por que motivo precisa</strong></td>
</tr>
<tr>
<td>Subscrição do Azure</td>
<td>A subscrição determina quem pode iniciar sessão no portal do Azure. Titular da conta cria a subscrição no <a HREF="https://account.windowsazure.com/Subscriptions">Azure subscrições</a>.
<br/><br/>
A conta Azure pode tem múltiplas subscrições e pode ser gerida por qualquer pessoa que é permitido. Por exemplo, o seu titular conta Azure cria uma subscrição denominada <em>BizTalkServiceSubscription</em> e fornece-os administradores de BizTalk dentro da sua empresa (por exemplo, ContosoBTSAdmins@live.com) acesso a esta subscrição. Neste cenário, os administradores de BizTalk inicie sessão portal do Azure e ter completos direitos de administrador para todos os serviços alojados da subscrição, incluindo os serviços de BizTalk Azure. Os administradores de BizTalk não são portadores conta Azure e, consequentemente, não tem acesso a quaisquer informações de faturaçãohttps.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Contas de armazenamento no portal do Azure e gerir subscrições</a> fornece mais informações.
</td>
</tr>
<tr>
<td>Base de dados Azure SQL</td>
<td>Armazena as tabelas, vistas e procedimentos armazenados utilizados pelo serviço BizTalk, incluindo os dados de controlo.
<br/><br/>
Quando cria um serviço de BizTalk, pode utilizar um servidor de SQL Azure existente, base de dados SQL Azure, ou criar automaticamente um novo servidor ou base de dados.
<br/><br/>
A escala de base de dados SQL automaticamente está configurada. Normalmente, a escala predefinida é suficiente para um serviço de BizTalk. Alterar a escala impactos preços. Consulte o artigo <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">contas e faturação na base de dados Azure SQL</a>
<br/><br/>
<strong>Notas</strong>
<br/>
<ul>
<li> Quando cria um novo Azure SQL Server e base de dados, dos serviços do Azure está ativado automaticamente. O serviço de BizTalk requer dos serviços do Azure de estar ativada.</li>
<li>Se criar uma nova base de dados do Azure SQL num servidor SQL Azure existente, as regras de firewall do servidor não são alteradas. Como resultado, é possível de que outros serviços do Azure não são permitidos acesso a bases de dados no servidor.</li>
</ul>
</td>
</tr>
<tr>
<td>Espaço de nomes de controlo de acesso Azure</td>
<td>Autentica com os serviços do Azure BizTalk. Quando implementar um projeto de BizTalk serviço a partir do Visual Studio, introduza este espaço de nomes do controlo de acesso. Quando cria um serviço de BizTalk, é criado automaticamente o espaço de nomes do controlo de acesso.</td>
</tr>

<tr>
<td>Conta de armazenamento Azure</td>
<td>Dá acesso a tabelas, blobs e filas utilizadas pelo seu serviço de BizTalk para guardar o seguinte:

<ul>
<li>Ficheiros de registo que monitorize o serviço de BizTalk. O resultado da monitorização também é apresentado no separador **monitorização** no portal do Azure.</li>
<li>Ao criar um X12 AS2 acordo ou entre parceiros, pode ativar a funcionalidade de arquivo para armazenar propriedades da mensagem. Estes dados são guardados na conta de armazenamento.</li>
</ul>
<br/>
Quando cria um serviço de BizTalk, pode utilizar uma conta de armazenamento existente ou criar automaticamente uma nova conta de armazenamento.
<br/><br/>
As predefinições de armazenamento são suficientes para um serviço de BizTalk.
<br/><br/>
Quando cria uma conta de armazenamento, uma chave primária e chave secundário são criados automaticamente. Estas teclas controlam o acesso à sua conta de armazenamento. O serviço de BizTalk utiliza automaticamente a chave primária.
<br/><br/>
Consulte o artigo de <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">armazenamento</a> para obter mais informações.
</td>
</tr>

<tr>
<td>Certificado SSL de privada</td>
<td>
Quando é criado um serviço de BizTalk Azure, também é criado um URL de HTTPS que inclui o seu nome de serviço de BizTalk. Este URL é automaticamente configurado para utilizar um certificado autoassinado só de desenvolvimento. Produção, terá um certificado SSL privado.
<br/><br/>
<strong>Informações sobre o certificado SSL importantes</strong>

<ul>
<li>A data de validade do certificado tem de ser inferiores a 5 anos.</li>
<li>Todos os certificados privados exigem uma palavra-passe. Saber esta palavra-passe e como prática recomendada, partilhe esta palavra-passe com os seus administradores.</li>
<li>Certificados autoassinados são utilizados num ambiente de teste/programação. Ao utilizar certificados autoassinados, importe o certificado para o arquivo de certificados pessoais e arquivo de certificados autoridades de certificação de raiz fidedigna.</li>
</ul>
<br/>Quando enviar o pedido de certificado de produção para sua autoridade de certificação, dê as seguintes propriedades de certificado:
<br/>

<ul>
<li><strong>As funcionalidades de utilização de chaves</strong>: no mínimo, dos serviços do Azure BizTalk requer autenticação de servidor.</li>
<li><strong>Nome comum</strong>: introduza o nome de domínio completamente qualificado (FQDN) do seu URL do serviço de BizTalk Azure. Consulte o artigo <a HREF="#BizTalk">criar um serviço de BizTalk</a> neste artigo.</li>
</ul>
<br/>
Pode ser adicionado um certificado novo ou diferente depois do serviço de BizTalk é criado.
</td>
</tr>
</table>



## <a name="hybrid-connections"></a>Ligações de híbrido

Quando cria um serviço de BizTalk Azure, no separador **Ligações híbrida** está disponível:

![Separador de ligações de híbrido][HybridConnectionTab]

Híbrido ligações são utilizadas para ligar a um Web site Azure ou serviço móvel do Azure a qualquer outro recurso no local que utiliza uma porta TCP estática, como o SQL Server, MySQL, HTTP Web APIs, serviços Mobile e a maioria dos serviços Web personalizados.  Híbrido ligações e o serviço de placa BizTalk são diferentes. O serviço de adaptador BizTalk é utilizado para ligar o Azure BizTalk serviços a um sistema de linha de negócio (LOB) no local.

 Consulte o artigo [Híbrido ligações](integration-hybrid-connection-overview.md) para mais informações, incluindo criar e gerir ligações de híbrido.


## <a name="next-steps"></a>Próximos passos

Agora que é criado um serviço de BizTalk, familiarizar-se com as diferentes [BizTalk serviços: separadores Dashboard, monitorizar e escala](biztalk-dashboard-monitor-scale-tabs.md). O serviço de BizTalk está pronto para as suas aplicações. Para começar a criação de aplicações, aceda a [Azure BizTalk serviços](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consulte também
- [BizTalk serviços: Gráfico de edições](biztalk-editions-feature-chart.md)<br/>
- [BizTalk serviços: Gráfico de estado](biztalk-service-state-chart.md)<br/>
- [BizTalk serviços: Cópia de segurança e restauro](biztalk-backup-restore.md)<br/>
- [Serviços de BizTalk: limitação](biztalk-throttling-thresholds.md)<br/>
- [BizTalk serviços: Nome do emissor e chave emissor](biztalk-issuer-name-issuer-key.md)<br/>
- [Como é que começar a utilizar o SDK do Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
- [Ligações de híbrido](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
