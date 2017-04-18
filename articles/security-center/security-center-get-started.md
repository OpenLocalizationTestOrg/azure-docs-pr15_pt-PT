<properties
   pageTitle="Guia de introdução do Centro de segurança do Azure | Microsoft Azure"
   description="Este artigo ajuda-o a começar a trabalhar rapidamente com o Centro de segurança do Azure ajudando-os componentes de gestão de monitorização e a política de segurança e ligando para os passos seguintes."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/28/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-quick-start-guide"></a>Guia de introdução do Centro de segurança do Azure

Este artigo ajuda-o rapidamente introdução ao centro de segurança do Azure, ajudando-os componentes de gestão de segurança de monitorização e a política, do Centro de segurança.

> [AZURE.NOTE] Este artigo apresenta o serviço utilizando uma implementação de exemplo. Este artigo não é um guia passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar com o Centro de segurança, tem de ter uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se para uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

A camada gratuita do Centro de segurança é activada automaticamente com a sua subscrição e fornece visibilidade do Estado de segurança dos seus recursos Azure. Fornece gestão de políticas de segurança básicas, recomendações de segurança e integração com segurança produtos e serviços a partir do Azure parceiros.

Aceder ao centro de segurança a partir do [Azure portal](https://azure.microsoft.com/features/azure-portal/). Para saber mais sobre o portal do Azure, consulte a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="data-collection"></a>Recolha de dados

Centro de segurança recolhe dados a partir do seu máquinas virtuais (VMs) para avaliar o seu estado de segurança, fornecer as recomendações de segurança e alertá-lo para ameaças. Quando pela primeira vez aceder ao centro de segurança, recolha de dados é activada no VMs todos na sua subscrição. Recolha de dados é recomendada, mas pode optar por desativando a recolha de dados na política do Centro de segurança.

Os passos seguintes descrevem como aceder e utilizar os componentes do Centro de segurança. Estes passos, mostramos-lhe como desactivar a recolha de dados se optar por optar ativamente por participar.

## <a name="access-security-center"></a>Centro de segurança do Access

No portal do, siga estes passos para aceder ao centro de segurança:

1. No menu **Do Microsoft Azure** , selecione **Centro de segurança**.
![Azure menu][1]

2. Se está a aceder ao centro de segurança pela primeira vez, é apresentada a pá **Bem-vindo ao** . Selecione Sim **! Pretendo que ao centro de segurança do Azure iniciação** para abrir o **Centro de segurança** pá e para ativar a recolha de dados.
![Ecrã de boas-vindas][10]

3. Depois de iniciar o Centro de segurança a partir da pá bem-vindo ao ou selecione Centro de segurança a partir do menu do Microsoft Azure, é aberta a pá do **Centro de segurança** . Para facilitar o acesso para o **Centro de segurança** pá no futuro, selecione a opção de **pá afixar ao dashboard** (canto superior direito).
![Pá afixar à opção de dashboard][2]

## <a name="use-security-center"></a>Utilize o Centro de segurança

Pode configurar as políticas de segurança do seu subscrições Azure e grupos de recursos. Vamos configurar uma política de segurança para a sua subscrição:

1. No pá **Centro de segurança** , selecione o mosaico de **política** .
![Política de segurança][3]

2. No pá **política de segurança - definir política por subscrição ou grupo de recursos** , selecione uma subscrição.
3. Pá **política de segurança** , **recolha de dados** é ativado automaticamente recolher registos. A extensão de monitorização está aprovisionada em todos os VMs atuais e novos na subscrição. (Pode optar por terminar recolha de dados através da definição de **recolha de dados** para **desativar**, mas isto impede que o Centro de segurança que lhe dá alertas de segurança e recomendações.)
4. No pá **política de segurança** , selecione **Escolher uma conta de armazenamento por região**. Para cada região no qual vai ter VMs em execução, selecione a conta de armazenamento onde os dados recolhidos esses VMs estão armazenados. Se não escolher uma conta de armazenamento para cada região, é criado por si. Os dados que são recolhidos estão logicamente isolados a partir de dados dos outros clientes por motivos de segurança.

     > [AZURE.NOTE] Recomendamos que activar recolha de dados e escolha uma conta de armazenamento ao nível de subscrição pela primeira vez. Políticas de segurança podem ser definidas no nível de subscrição Azure e nível de grupo de recursos, mas a configuração de conta de armazenamento e de recolha de dados ocorre apenas ao nível subscrição.

5. No pá **política de segurança** , selecione a **política de prevenção de**. Esta ação abre a **política de prevenção de** pá.
![Política de prevenção de][4]

6. No pá **política de prevenção** , ative as recomendações de que pretende ver como parte da sua política de segurança. Exemplos:

 - Configurar **atualizações do sistema** **no** analisa todas as máquinas virtuais suportadas SO actualizações em falta.
 - Definir **vulnerabilidades SO** para **no** analisa todas as máquinas virtuais suportadas para identificar as configurações de SO que podem fazer a máquina virtual mais vulnerável a ataques.

### <a name="view-recommendations"></a>Recomendações de vista

1. Regresse ao pá **Centro de segurança** e selecione o mosaico de **recomendações** . Centro de segurança periodicamente analisa o estado de segurança dos seus recursos Azure. Quando o Centro de segurança identifica potenciais vulnerabilidade de segurança, mostra recomendações sobre a pá **recomendações** .
![Recomendações no Centro de segurança do Azure][5]

2.  Selecione uma recomendação sobre a pá **recomendações** para ver mais informações e/ou tomar medidas para resolver o problema.

### <a name="view-the-health-and-security-state-of-your-resources"></a>Ver o estado de funcionamento e segurança dos seus recursos

1.  Regressar ao pá **Centro de segurança** . O mosaico do **Estado de funcionamento de segurança de recursos** contém indicadores de estado de segurança para máquinas virtuais, redes, dados e aplicações.
2.  Selecione **máquinas virtuais** para ver mais informações. O pá **máquinas virtuais** abre e apresenta um resumo de estado dos programas de antimalware, actualizações do sistema, reinicia e vulnerabilidades do sistema operativo do seu VMs.
![O mosaico do Estado de funcionamento de recursos no Centro de segurança do Azure][6]

3.  Selecione uma recomendação em **Máquina VIRTUAL recomendações** para ver mais informações e/ou tomar medidas para configurar controlos necessários.
4.  Selecione uma VM em **máquinas virtuais** para ver os detalhes adicionais.

### <a name="view-security-alerts"></a>Alertas de segurança de vista

1.  Regresse ao pá **Centro de segurança** e selecione o mosaico de **alertas de segurança** . O **alertas de segurança** pá abre e apresenta uma lista de alertas. A análise do Centro de segurança dos seus registos de segurança e actividade de rede gera estes alertas. Alertas de soluções de parceiros integrada são incluídos.
![Alertas de segurança no Centro de segurança do Azure][7]

    > [AZURE.NOTE] Alertas de segurança só estão disponíveis se a camada padrão do Centro de segurança de estiver ativada. Está disponível uma versão de avaliação gratuita de 90 dias da camada padrão. Consulte [os passos seguintes](#next-steps) para obter informações sobre como obter a camada padrão.

2.  Selecione um alerta para ver informações adicionais. Neste exemplo, vamos selecione **sistema modificado binário descobriu**. Esta ação abre pás fornecem detalhes adicionais sobre o alerta.
![Detalhes do alerta de segurança no Centro de segurança do Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Ver o estado de funcionamento do seu soluções de parceiros

1. Regressar ao pá **Centro de segurança** . O mosaico de **soluções de parceiros** permite-lhe monitorizar, de imediato, o estado de estado de funcionamento do seu soluções de parceiros integrada com a sua subscrição Azure.
2. Selecione o mosaico de **soluções de parceiros** . Uma pá abre e apresenta uma lista das suas soluções de parceiros ligado ao centro de segurança.
![Soluções de parceiros][9]

3. Selecione uma solução de parceiro. Neste exemplo, vamos selecione a solução de **F5 WAF** .  Uma pá abre e mostra-lhe o estado da solução de parceiro e recursos associados a solução. Selecione **Consola de solução** para abrir a experiência de gestão de parceiro para esta solução.

## <a name="next-steps"></a>Próximos passos
Este artigo introduzida os componentes de gestão de segurança de monitorização e a política, do Centro de segurança. Agora que já está familiarizado com o Centro de segurança, experimente os seguintes passos:

- Configure uma política de segurança para a sua subscrição Azure. Para saber mais, consulte o artigo [definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md).
- Utilize as recomendações no Centro de segurança para o ajudar a proteger os recursos do Azure. Para saber mais, consulte o artigo [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).
- Reveja e gerir os alertas de segurança atual. Para saber mais, consulte o artigo [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md).
- Saiba mais sobre a [ameaça deteção funcionalidades avançadas](security-center-detection-capabilities.md) que estão incluídos a [camada padrão](security-center-pricing.md) do Centro de segurança. Está disponível uma versão de avaliação gratuita de 90 dias da camada padrão.
- Se tiver questões sobre como utilizar o Centro de segurança, consulte as [Perguntas mais frequentes do Centro de segurança do Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
