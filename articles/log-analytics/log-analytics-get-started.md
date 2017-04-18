<properties
    pageTitle="Introdução ao registo de análise | Microsoft Azure"
    description="Pode obter a trabalhar com a análise de registo no Microsoft operações de gestão de conjunto de aplicações (OMS) em minutos."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="get-started-with-log-analytics"></a>Começar a trabalhar com a análise de registo

Pode obter a trabalhar com a análise de registo no Microsoft operações de gestão de conjunto de aplicações (OMS) em minutos. Tem duas opções quando escolher como criar uma área de trabalho OMS, que é semelhante a uma conta:

- Web site do conjunto de aplicações do Microsoft operações gestão
- Subscrição do Microsoft Azure

Pode criar uma área de trabalho do OMS gratuita utilizando o Web site OMS. Ou, pode utilizar uma subscrição do Microsoft Azure para criar uma área de trabalho OMS. Ambas as áreas de trabalho são funcional equivalentes, exceto que uma área de trabalho OMS gratuita só pode enviar 500 MB de dados diariamente para o serviço OMS. Se utilizar uma subscrição do Azure, também pode utilizar esse subscrição para aceder a outros serviços do Azure. Independentemente do método que utilizar para criar a área de trabalho, irá criar a área de trabalho com uma conta Microsoft ou institucional.

Eis um olhar sobre o processo:

![Diagrama de ativação](./media/log-analytics-get-started/oms-onboard-diagram.png)

## <a name="log-analytics-prerequisites-and-deployment-considerations"></a>Pré-requisitos de análise de registo e considerações de implementação

- Precisa de uma subscrição paga do Microsoft Azure para utilizar totalmente a análise de registo. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) que permite-lhe aceder a qualquer serviço Azure. Ou pode criar uma conta OMS gratuita no Web site da [Conjunto de aplicações de gestão de operações](http://microsoft.com/oms) e clique em **experimentar gratuitamente**.
- Uma área de trabalho OMS
- Cada computador com o Windows que pretende recolher dados de tem de executar o Windows Server 2008 SP1 ou posterior
- Acesso de [Firewall](log-analytics-proxy-firewall.md) para o OMS endereços do serviço da web
- Um servidor de [OMS registo Analytics reencaminhador](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) (o Gateway) para encaminhar o tráfego de servidores para OMS, se não estiver disponível de computadores acesso à Internet
- Se utilizar o Gestor de operações, suporta registo Analytics Gestor de operações 2012 SP1 UR6 e acima e 2012 de Gestor de operações R2 UR2 e superior. Suporte de proxy foi adicionado no Gestor de operações 2012 SP1 UR7 e 2012 de Gestor de operações R2 UR3. Determine como serão integrada com OMS.
- Determine se os computadores têm acesso à Internet direto. Caso não esteja, necessitam de um servidor de gateway para aceder aos sites de serviço web OMS. Acesso a todos os é através de HTTPS.
- Determine quais tecnologias e servidores irão enviar dados para OMS. Por exemplo, controladores de domínio, SQL Server, etc.
- Conceder permissões aos utilizadores no OMS e Azure.
- Se estiver preocupado com a utilização de dados, implementar individualmente cada solução e teste o impacto no desempenho antes de adicionar soluções adicionais.
- Rever a sua utilização de dados e o desempenho à medida que adiciona funcionalidades e soluções para a análise de registo. Isto inclui recolha de eventos, colecção de registos, recolha de dados de desempenho, etc. Melhor consiste em começar com coleção mínima até que a utilização de dados ou identificou impacto no desempenho.
- Certifique-se de que agentes do Windows não são também geridos através do Gestor de operações, caso contrário, irão resultar dados duplicados. Isto também se aplica ao Azure--agentes de base que tenham diagnósticos do Azure ativado.
- Depois de instalar agentes, verifique se o agente está a funcionar corretamente. Se não, verificação para garantir que criptografia API: isolamento de chave geração CNG (Next) não está desativado utilizando a política de grupo.
- Algumas soluções de análise de registo têm requisitos adicionais



## <a name="sign-up-in-3-steps-using-the-operations-management-suite"></a>Inscrever-se nos 3 passos utilizando o conjunto de gestão de operações

1. Aceder ao site do [Conjunto de aplicações de gestão de operações](http://microsoft.com/oms) e clique em **experimentar gratuitamente**. Inicie sessão com a sua conta Microsoft, tal como Outlook.com ou com uma conta institucional fornecida pela sua empresa ou instituição educacional para utilizar com o Office 365 ou outros serviços da Microsoft.
2. Forneça um nome exclusivo da área de trabalho. Uma área de trabalho é um contentor lógico onde os seus dados de gestão estão armazenados. Fornece uma maneira de dados de partição entre diferentes equipas na sua organização, tal como os dados estão em modo exclusivo à sua área de trabalho. Especificar um endereço de e-mail e a região em que pretende ter dados armazenados.  
    ![criar a área de trabalho e ligar subscrição](./media/log-analytics-get-started/oms-onboard-create-workspace-link01.png)
3. Em seguida, pode criar uma nova subscrição Azure ou ligar a uma subscrição existente do Azure. Se pretender para continuar a utilizar a versão de avaliação gratuita, clique em **Não agora**.  
  ![criar a área de trabalho e ligar subscrição](./media/log-analytics-get-started/oms-onboard-create-workspace-link02.png)

Está pronto para começar a utilizar o portal do conjunto de aplicações de gestão de operações.

Pode obter mais informações sobre como configurar a área de trabalho e ligar contas de Azure existente para áreas de trabalho criadas com o conjunto de gestão de operações de [Gerir](log-analytics-manage-access.md)o acesso ao registo de análise.

## <a name="sign-up-quickly-using-microsoft-azure"></a>Inscrever-se rapidamente utilizando o Microsoft Azure

1. Aceda ao [portal do Azure](https://portal.azure.com) e iniciar sessão, procure a lista de serviços e, em seguida, selecione o **Registo de análise (OMS)**.  
    ![Portal do Azure](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. Clique em **Adicionar**e, em seguida, selecione opções para os seguintes itens:
    - Nome **OMS área de trabalho**
    - **Subscrição** - se de que tem múltiplas subscrições, escolha o dispositivo que pretende associar a nova área de trabalho.
    - **Grupo de recursos**
    - **Localização**
    - **Preços de camadas**  
        ![criação rápida](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Clique em **Criar** e verá os detalhes de área de trabalho no portal do Azure.       
    ![detalhes de área de trabalho](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
4. Clique na ligação **OMS Portal** para abrir o Web site de conjunto de aplicações de gestão de operações com a sua nova área de trabalho.

Está pronto para começar a utilizar o portal do conjunto de aplicações de gestão de operações.

Pode saber mais sobre como configurar a área de trabalho e ligar as áreas de trabalho existentes que criou com o conjunto de gestão de operações para subscrições do Azure [Gerir](log-analytics-manage-access.md)o acesso ao registo de análise.

## <a name="get-started-with-the-operations-management-suite-portal"></a>Introdução ao portal do conjunto de aplicações de gestão de operações
Para escolher soluções e ligue os servidores que pretende gerir, clique no mosaico de **Definições** e siga os passos nesta secção.  

![introdução](./media/log-analytics-get-started/oms-onboard-get-started.png)  

1. **Adicionar soluções** - ver as soluções instaladas.  
    ![soluções](./media/log-analytics-get-started/oms-onboard-solutions.png)  
    Clique em **visitar a Galeria** para adicionar mais soluções.  
    ![soluções](./media/log-analytics-get-started/oms-onboard-solutions02.png)  
    Selecione uma solução e, em seguida, clique em **Adicionar**.
2. **Ligar uma origem de** - escolher como pretende estabelecer ligação com o seu ambiente do servidor para recolher dados:
    - Ligar a qualquer Windows Server ou cliente diretamente ao instalar um agente.
    - Ligar os servidores de Linux com o agente de OMS para Linux.
    - Utilize uma conta de armazenamento Azure configurada com a extensão VM Diagnóstico Windows ou Linux Azure.
    - Utilize o Gestor de operações do Centro de sistema para anexar seus grupos de gestão ou a sua implementação completa do Gestor de operações.
    - Ative telemetria do Windows utilizar a análise de atualizar.
        ![origens ligadas](./media/log-analytics-get-started/oms-onboard-data-sources.png)    

3. **Recolher dados** Configure pelo menos uma origem de dados para preencher dados para a área de trabalho. Quando terminar, clique em **Guardar**.    

    ![recolher dados](./media/log-analytics-get-started/oms-onboard-logs.png)    


## <a name="optionally-connect-servers-directly-to-the-operations-management-suite-by-installing-an-agent"></a>Opcionalmente, ligue servidores diretamente para o conjunto de gestão de operações instalando um agente

O exemplo seguinte mostra-lhe como instalar um agente do Windows.

1. Clique no mosaico de **Definições** , clique no separador de **Origens ligadas** , clique num separador para o tipo de origem que pretende adicionar e, em transferir um agente ou Saiba mais sobre como ativar um agente. Por exemplo, clique em **Transferir agente do Windows (64 bits)**. Para agentes do Windows, só pode instalar o agente no Windows Server 2008 SP 1 ou mais tarde ou no Windows 7 SP1 ou posterior.
2. Instale o agente em servidores de uma ou mais. Pode instalar o agentes um a um ou utilizando um método mais automatizado com um [script personalizado](log-analytics-windows-agents.md), ou pode utilizar uma solução de distribuição de software existente que pode ter.
3. Depois de aceitar o contrato de licença e escolher a sua pasta de instalação, selecione **ligar o agente de análise de registo para Azure (OMS)**.   
    ![programa de configuração do agente](./media/log-analytics-get-started/oms-onboard-agent.png)

4. Na página seguinte, são-lhe pedido para o seu ID de área de trabalho e chave de área de trabalho. O ID da área de trabalho e a chave, são apresentadas no ecrã onde tenha transferido o ficheiro de agente.  
    ![teclas do agente](./media/log-analytics-get-started/oms-onboard-mma-keys.png)  

    ![anexar servidores](./media/log-analytics-get-started/oms-onboard-key.png)
5. Durante a instalação, pode clicar em **Avançadas** para opcionalmente configurar o seu servidor proxy e forneça informações de autenticação. Clique no botão **seguinte** para regressar ao ecrã de informações da área de trabalho.
6. Clique em **seguinte** para validar o seu ID de área de trabalho e chave. Se forem encontrados erros, pode clicar em **voltar** para efetuar correções. Quando o seu ID de área de trabalho e chave são validados, clique em **instalar** para concluir a instalação do agente.
7. No painel de controlo, clique em Microsoft Agent monitorização > separador Azure registo Analytics (OMS). Um ícone de marca de verificação verde é apresentada quando os agentes de comunicam com o serviço de conjunto de aplicações de gestão de operações. Inicialmente, este botão leva cerca de 5 a 10 minutos.

>[AZURE.NOTE] As soluções de avaliação capacidade de gestão e configuração não são atualmente suportadas pelo servidores ligado diretamente para o conjunto de gestão de operações.


Também pode ligar o agente para o Gestor de operações do sistema Centro 2012 SP1 e versões posteriores. Para fazê-lo, selecione **ligar o agente de Gestor de operações do Centro de sistema**. Quando escolher que opção, enviar dados para o serviço sem necessidade de hardware adicional ou carregar no seus grupos de gestão.

Pode ler mais sobre como ligar agentes para o conjunto de gestão de operações em [computadores com o Windows ligar-se para a análise de registo](log-analytics-windows-agents.md).

## <a name="optionally-connect-servers-using-system-center-operations-manager"></a>Opcionalmente, ligue servidores utilizando o Gestor de operações do Centro de sistema

1. Na consola do Gestor de operações de, selecione **Administração**.
2. Expanda o nó **Operacionais informações** e selecione **Operacionais de informações de ligação**.

  >[AZURE.NOTE] Dependendo do que Update Rollup de SCOM estiver a utilizar, poderá ver um nó de *Classificação do Centro de sistema*, *Informações operacionais*ou *Conjunto de aplicações de gestão de operações*.

3. Clique na ligação **Registe-se para informações de operacionais** no canto superior direito e siga as instruções.
4. Depois de concluir o Assistente de registo, clique na ligação **Adicionar um computador/grupo** .
5. Na caixa de diálogo **Pesquisa de computador** pode procurar computadores ou grupos controlados pelo Gestor de operações. Selecione computadores ou grupos para incorporada-los ao registo de análise, clique em **Adicionar**e, em seguida, clique em **OK**. Pode verificar se o serviço OMS está a receber dados ao aceder ao mosaico de **utilização** no portal do conjunto de aplicações de gestão de operações. Dados deverão aparecer em cerca de 5 a 10 minutos.

Pode ler mais sobre como ligar o Gestor de operações para o conjunto de gestão de operações em [Gestor de operações de ligar para a análise de registo](log-analytics-om-agents.md).

## <a name="optionally-analyze-data-from-cloud-services-in-microsoft-azure"></a>Opcionalmente, analisar os dados dos serviços em nuvem no Microsoft Azure

Com o conjunto de gestão de operações, pode procurar rapidamente IIS registos para serviços em nuvem e máquinas virtuais de eventos e ao ativar diagnósticos para serviços em nuvem Azure. Pode também recebe informações adicionais para máquinas virtuais Azure instalando o agente de monitorização da Microsoft. Pode ler mais sobre como configurar o seu ambiente do Azure para utilizar o conjunto de gestão de operações em [armazenamento Azure ligar-se para a análise de registo](log-analytics-azure-storage.md).


## <a name="next-steps"></a>Próximos passos

- [Soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md) para adicionar a funcionalidade e reunir dados.
- Se familiarizar com [as pesquisas de registo](log-analytics-log-searches.md) ver informações detalhadas recolhidas pela soluções.
- Utilize [dashboards](log-analytics-dashboards.md) para guardar e apresentar os seus próprios procuras personalizadas.
