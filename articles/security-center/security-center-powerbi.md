<properties
   pageTitle="Obter informações a partir de dados do Centro de segurança do Azure com o Power BI | Microsoft Azure"
   description="O pacote de conteúdo do Power BI Azure segurança Centro torna mais fácil encontrar os alertas de segurança, recomendações, ataques de recursos e tendências, com base num conjunto de dados que foi criado para o seu relatório."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Obter informações a partir de dados do Centro de segurança do Azure com o Power BI
O [Dashboard do Power BI](http://aka.ms/azure-security-center-power-bi) para o Centro de segurança do Azure permite-lhe visualizar, analisar e filtrar recomendações e alertas de segurança a partir de qualquer local, incluindo o seu dispositivo móvel. Utilize o dashboard do Power BI para revelar tendências e ataque padrões - alertas de segurança de vista por recurso ou o endereço IP de origem e unaddressed riscos de segurança por recurso ou idade. 

Pode também misturá recomendações de centro de segurança e alertas de segurança com outros dados de formas interessantes, por exemplo, utilizando dados de [Registos de auditoria Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) e [Azure SQL da base de dados de auditoria](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Ambos oferecem Dashboards do Power BI e também pode exportar estes dados para o Excel para elaboração de relatórios mais fácil sobre o estado de segurança dos seus recursos na nuvem.

##<a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Utilizar o dashboard do Centro de segurança do Azure para aceder ao Power BI
Também pode utilizar o dashboard do Centro de segurança do Azure para aceder a relatórios do Power BI. Siga os passos para executar esta tarefa: 

1. No dashboard do **Centro de segurança do Azure** , clique em botão **exploração no Power BI** .

    ![Ligar ao centro de segurança do Azure através do Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. Abre o pá **explorar no Power BI** no lado direito, conforme mostrado no ecrã seguinte:

    ![Ligar ao centro de segurança do Azure através do Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. Se estiver a criar o dashboard do Power BI pela primeira vez, pode escolher uma das seguintes opções na pá a **explorar no Power BI** : 

    - **Dashboard de informações de segurança**: selecione esta opção se pretender criar um dashboard que inclui o estado de segurança, tópicos e DLP. Esta opção é uma mais comum para a função DevOps que é o responsável para analisar o estado de proteção e detetado alertas entre subscrições.
    - **Dashboard de gestão de política**: selecione esta opção se pretender explorar a política de gestão e de imposição.  Esta opção é uma mais comuns para TI Central que são mais focado num governação. Podem utilizar este dashboard para ganhar visibilidade e informações no adesão de política de segurança ao longo da sua organização.
    - Se já tiver um dashboard do Power BI, clique em **Ir para o dashboard atual do Power BI**.

4. Neste exemplo, clique em **dashboard de informações de segurança** opção. Se esta for a primeira vez, está a criar um dashboard do Power BI para o Centro de segurança lhe for pedido para instalar o pacote de conteúdo. Como é mostrado no ecrã seguinte, clique em botão **obter** na janela de **pacotes de conteúdo para o Power BI** :

    ![Dashboard do Azure informações de segurança do Centro de segurança](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. A janela de **ligar para informações de segurança do Azure segurança Centro** apresentada. Certifique-se o método de **autenticação** é **oauth2 ainda** conforme apresentado abaixo e clique em botão de **início de sessão** .
    
    ![Autenticação](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. Poderá ser-lhe pedido para autenticar novamente com as suas credenciais Azure. Depois de autenticar o dashboard será criado. Depois do dashboard é criado ver um relatório com a estrutura semelhante conforme mostrado no ecrã seguinte:

    ![Dashboard do Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] Uma atualização do relatório está agendada para demorar local diariamente. No caso de ocorrerem uma falha nesta atualização, leia [Potenciais problemas de atualização com o Azure segurança Centro Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/), para obter mais informações sobre como resolver problemas.

Aqui pode ver o número de alertas de segurança e recomendações e o número de VMs, bases de dados Azure SQL e recursos de rede que está a ser monitorizados pelo centro de segurança do Azure.

Uma ligação para o Centro de segurança do Azure encaminha-o para o portal do Azure. Os gráficos tornam mais fácil de visualizar informações sobre as recomendações de segurança e alertas, incluindo:

- Estado de segurança do recurso
- Recomendações pendentes
- VM recomendações
- Alertas ao longo do tempo
- Recursos intruso
- IPs intruso

Atrás de cada gráfico, existem informações adicionais. Selecione um mosaico para ver mais informações. Por exemplo, o mosaico do **Estado de segurança do recurso** mostra-lhe detalhes adicionais sobre pendentes recomendações por recursos conforme mostrado no ecrã seguinte:

![Recomendações](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Se clicar em qualquer linha deste gráfico, a outras pessoas estão a passar para cinzento saída e lhe concentrar-se apenas naquele que selecionou. Para regressar ao dashboard de, clique em **Centro de segurança do Azure** sob a opção de **Dashboards** no painel da esquerda desta página.

> [AZURE.NOTE] Se pretender para personalizar os seus relatórios ao adicionar campos adicionais ou alterar os efeitos visuais existentes, pode editar o relatório. Leia [interagir com um relatório na vista de edição no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) para obter mais informações.

Os mosaicos **alertas ao longo do tempo, ataques recursos** e **Intruso IPs** terão o resultado semelhante quando clica em cada um do mesmo. Isto acontece porque o relatório agrega informações em relação a esses três variáveis e chama- **recursos em ataque** conforme mostrado no ecrã seguinte:

![Recursos em ataque](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

Neste momento também pode guardar uma cópia deste relatório, imprimi-lo ou publique-o na web, utilizando as opções disponíveis no menu **ficheiro** .

![Menu ficheiro](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Explorar os dados do Centro de segurança do Azure com serviços do Power BI

Ligar ao [Serviços de pacote de conteúdo do Power BI](https://msit.powerbi.com/groups/me/getdata/services) no Power BI e execute os seguintes passos:

1. Na janela do **Pacote de conteúdo para o Power BI** verá duas opções conforme apresentado abaixo.

    ![Pacote de conteúdo para o Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] Se já executado a primeira parte deste artigo irá ver apenas uma opção, o que é a política de gestão do Azure segurança Centro.

2. Neste exemplo, clique em **obter** o mosaico de **Gestão de política de centro de segurança do Azure** .

3. Na janela **ligar a gestão de política de centro de segurança do Azure** , certifique-se selecionar a **oauth2 ainda** em **Método de autenticação** de lista pendente conforme apresentado abaixo e clique em botão de **início de sessão** .

    ![Janela de gestão de política](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. Serão redirecionados para uma página de autenticação onde deverá escrever as credenciais de que está a utilizar para ligar ao centro de segurança do Azure. Depois do processo de autenticação estiver concluído, do Power BI irá começar a importar dados para criar os seus relatórios. Durante este período de tempo poderá ver a seguinte mensagem no canto direito do seu browser:

    ![Ligar ao centro de segurança do Azure através do Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Quando o dashboard está a ser criado pela primeira vez, pode demorar mais longa do que o habitual, principalmente para cenários onde tem múltiplas subscrições. 

5. Quando o processo de estiver concluído, irá carregar o dashboard do Centro de segurança do Azure Power BI com o relatório de **Gestão de políticas de** semelhante ao mostrado abaixo:

    ![Dashboard de gestão de política](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Consulte também
Neste documento, o que aprendeu como utilizar o Power BI no Centro de segurança do Azure. Para saber mais sobre o Centro de segurança do Azure, consulte o seguinte:

- [Guia de operações de planeamento de centro de segurança do Azure e](security-center-planning-and-operations-guide.md) — obter informações sobre como planear adoção do Centro de segurança do Azure.
- [Definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md) — Saiba como configurar as definições de segurança no Centro de segurança do Azure
- [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md) — Saiba como gerir e responder a alertas de segurança
- [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md) — localizar perguntas mais frequentes sobre como utilizar o serviço
- [Blogue de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) — localizar mensagens de blogue sobre Azure segurança e conformidade
