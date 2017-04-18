
<properties 
    pageTitle="Proteger o acesso RemoteApp do Azure e, para além das | Microsoft Azure"
    description="Saiba mais acesso seguro como a RemoteApp do Azure ao utilizar o access condicional no Azure Active Directory"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Proteger o acesso RemoteApp do Azure e, para além das

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Neste artigo iremos fornecer uma descrição geral de como um administrador pode configurar um canal de acesso seguro a partir do utilizador final, através de RemoteApp do Azure e terminando num recurso seguro, tal como uma base de dados do SQL ou outra aplicação back-end. O objetivo é para se certificar de que apenas os utilizadores autorizados cumpram as condições pretendidas têm acesso remotas aplicações e que back-end seguro só pode ser acedido a partir do ambiente controlado RemoteApp do Azure e não a partir de outras localizações.

Existem 3 áreas principais que o administrador tem de observar:

![Considerações de acesso condicional RemoteApp Azure](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Leia sobre para obter informações e respostas a estas questões.

## <a name="who-can-access-the-collection"></a>Quem pode aceder a coleção de?
O administrador seleciona os utilizadores que podem aceder ao aplicações remotas na coleção de. Pode utilizar o trabalho do Azure Active Directory (Azure AD) ou contas escolares (anteriormente denominadas, "contas organizacionais") ou contas Microsoft (por exemplo, @outlook.com). A maior parte dos cenários empresariais utilizam contas do Azure AD; Estes permitem-lhe utilizar o access condicional funcionalidades abordadas mais tarde e também são a opção apenas para as coleções de domínio. O resto do artigo assume que está a utilizar contas do Azure AD com RemoteApp do Azure.

**O que recomendamos ter feito:**

Utilizar contas do Azure AD para controlar o acesso ao Azure RemoteApp dá-nos duas coisas:

1.  Estamos sempre saber quem pode aceder as aplicações que tenham publicados e aceder a qualquer anterior extremidades nessas aplicações ligar a.
2.  Vamos controlar o Azure AD subjacente para podemos criar e eliminar contas de utilizador, definir políticas de palavra-passe, utilizam a autenticação multifator, etc. 

## <a name="how-is-the-collection-accessed-from-where"></a>Como é acedida a coleção de? A partir da qual?
Normalmente, os administradores pretendem definir políticas para aceder a um público ambiente de acesso à Internet, como RemoteApp do Azure. Por exemplo, que pretendem para se certificar de que os utilizadores a aceder ao ambiente de fora à rede empresarial tenham que utilizar a autenticação multifator (MFA) para obter acesso; ou talvez devem ser bloqueados completamente.

Azure RemoteApp os administradores podem utilizar a funcionalidade disponível através do Azure AD Premium para definir as políticas de acesso condicional para o seu ambiente RemoteApp do Azure. Pode também utilizam rich elaboração de relatórios e alerta de funcionalidades para monitorizar a forma como o ambiente está a ser acedido.

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Como configurar o acesso condicional para RemoteApp do Azure
Vamos guiá-lo através de um cenário de exemplo – RemoteApp Azure administrador pretende bloquear o acesso ao ambiente quando os utilizadores estiverem fora à rede empresarial.

>[AZURE.NOTE] Vamos assumir tiver atualizado o Azure AD para a camada Premium e tiver criado pelo menos uma coleção de RemoteApp do Azure.

1.  No Azure portal clique no separador **Do Active Directory** . Em seguida, clique no directório que pretende configurar.

    Lembre-se: Acesso condicional é uma propriedade do seu diretório e não do Azure RemoteApp, para que todos os configuração está concluída ao nível do diretório. Isto significa também que tem de ser o administrador do diretório para efetuar estas alterações.

2.  Clique em **aplicações**e, em seguida, clique em **Microsoft Azure RemoteApp** para configurar o acesso condicional. Tenha em atenção que pode configurar o acesso condicional para cada aplicação "software como um serviço" no seu diretório separadamente.
![Configurar o acesso condicional para RemoteApp do Azure](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.  No separador **Configurar** , defina **Regras de acesso a ativar** para ON.
![Ativar as regras de acesso para RemoteApp do Azure](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.  Agora pode configurar regras de vários e escolher quem para aplicá-los para:

    1. Selecione **Bloquear o acesso do quando não estiver no trabalho** completamente impedir que os utilizadores acedam RemoteApp do Azure fora do ambiente de rede que especificar.
    2. Clique na opção abaixo para definir os intervalos de endereços IP coletivamente a sua rede"fidedigno". Tudo fora aqueles será rejeitado.

5.  Teste a configuração, iniciando o cliente do Azure RemoteApp a partir de um endereço IP fora do intervalo que especificou. Depois de iniciar sessão com as suas credenciais do Azure AD deverá ver uma mensagem como esta:

![Negado o acesso RemoteApp do Azure](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### <a name="future-conditional-access-features"></a>Funcionalidades de acesso condicional futuro 
A equipa do Azure Active Directory está a trabalhar no novas capacidades no Access condicional. Os administradores poderão criar novos tipos de regras para além de rede regras de localização com base. Uma pré-visualização pública da nova funcionalidade deve estar disponível mais rapidamente.

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Como monitorizar o acesso ao RemoteApp do Azure
Uma funcionalidade ótima para utilizar ao lado do access condicional é a funcionalidade de elaboração de relatórios do Azure Active Directory Premium. Pode utilizar relatórios para monitorizar quem está a aceder ao seu ambiente e detetar quaisquer actividades suspeitas.

Por exemplo, pode ver os nomes dos utilizadores que pode ser consultada RemoteApp do Azure, número de vezes que tinham-lo e quando.

1.  No Azure portal, clique em **Active Directory**e, em seguida, clique no diretório da sua.

2.  Aceda ao separador **relatórios** .

3.  A partir da lista de relatórios, selecione a **utilização da aplicação** em **aplicações integradas**.

    Verá algumas estatísticas agregadas para RemoteApp do Azure. 
![Agregado estatística de acesso de RemoteApp do Azure](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.  Clique na aplicação para revelar informações sobre utilizadores a aceder a RemoteApp do Azure.
![Estado de acesso do utilizador para RemoteApp do Azure](./media/remoteapp-secureaccess/ra-userstats.png)
 
### <a name="summary"></a>Resumo
Com o Azure Active Directory Premium pode configurar regras de acesso para RemoteApp do Azure (e outro software como um aplicações de serviço disponível através do Azure AD). As regras de estão atualmente limitadas a políticas de localização com base rede mas serão no futuro expandidas para outros aspetos da gestão de empresa.

Azure AD Premium também oferece elaboração de relatórios e monitorizar as funcionalidades que ainda mais alargar o controlo o administrador tem ao longo do seu ambiente de trabalho do Azure RemoteApp.

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>Como posso garantir que a minha recurso seguro é acessível apenas a partir do meu ambiente de RemoteApp do Azure?
Nas secções anteriores deste artigo podemos com o foco nas proteger o acesso ao ambiente do Azure RemoteApp. Vamos concluiu que ao escolher os utilizadores que têm permissão de acesso e configurar regras de acesso para controlar melhor como podem utilizar o serviço.

Um cenário comum para implementações RemoteApp do Azure é que as aplicações remotas necessitam comunicar com um recurso de back-end, por exemplo uma base de dados do SQL. Este recurso está alojado quer no local (por exemplo, numa rede da empresa) ou na nuvem (por exemplo, no Azure IaaS). Os administradores pretender com frequência para se certificar de que o recurso de back-end só pode ser acedido por aplicações implementadas através do RemoteApp do Azure e não por exemplo, por uma aplicação a executar o diretamente no PC de um utilizador e aceder a através da Internet pública. Azure RemoteApp é frequentemente visualizada como o ambiente geridos centralmente e protegido e, consequentemente, o caminho apenas através da qual os utilizadores devem interagir com o recurso de back-end.

A solução é colocar o ambiente de RemoteApp do Azure e o recurso seguro no mesmo Azure Virtual rede (VNET). Se o recurso está num site diferente, pode estabelecer uma ligação VPN do site para o site, por exemplo para criar um VNet abrangendo o Centro de dados Azure e o ambiente do cliente no local.

Azure RemoteApp suporta dois tipos de implementações de coleções de sites onde pode fornecer o seu próprio VNET:

-   Não-associado ao domínio: as aplicações terão "linha de visão" dos outros recursos que o VNET. Por exemplo, isto pode ser utilizado para ligar a aplicações para uma base de dados do SQL que utiliza a autenticação do SQL (aplicações autenticar o utilizador diretamente através da base de dados)

-   Domínio: as máquinas virtuais utilizadas pelo RemoteApp do Azure são associadas a um controlador de domínio a VNET. Isto é útil quando precisam das aplicações autenticar um controlador de domínio do Windows para obter acesso a um recurso de back-end.
![Uma coleção de domínio no RemoteApp do Azure](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Como criar uma ligação segura entre o Azure e o meu ambiente no local
Existem várias opções de configuração para ligar os ambientes Azure e no local. Está disponível uma boa visão geral das opções.

Com o Azure RemoteApp tem de configurar o seu VNet pela primeira vez e, em seguida, utilizá-lo durante o processo de criação da sua coleção de. 

## <a name="the-complete-solution"></a>A solução completa
O diagrama abaixo mostra a solução completa sempre que criámos um canal acesso seguro a partir do utilizador final, através de RemoteApp Azure (ARA), para o recurso de back-end.
![Seguro RemoteApp do Azure](./media/remoteapp-secureaccess/ra-secureoverview.png) na fase 1 estamos os utilizadores seleccionados e criado regras de acesso que governem como ARA pode ser acedida. No exemplo abaixo estamos apenas permitir o acesso de utilizadores a trabalhar a partir da rede da empresa. Compatível com não utilizadores não poderão aceder de todo o ambiente de ARA.
No "Fase 2" Pedimos ter expostos o recurso de back-end apenas através de configuração da VNet/VPN que recomendamos controlam. Azure RemoteApp foi colocada em VNet o mesmo. O resultado final é que o recurso só pode ser acedido através do ambiente de ARA.


