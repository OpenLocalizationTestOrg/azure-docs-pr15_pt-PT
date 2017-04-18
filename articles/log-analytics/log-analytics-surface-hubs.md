<properties
    pageTitle="Monitorizar concentradores superfície com a análise de registo | Microsoft Azure"
    description="Utilize a solução de superfície concentrador para controlar o estado de funcionamento do seu concentradores superfície e compreender como estão a ser utilizados."
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
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="monitor-surface-hubs-with-log-analytics"></a>Monitor concentradores superfície com a análise de registo

Este artigo descreve como pode utilizar a solução concentrador de superfície na análise de registo para monitorizar a dispositivos Microsoft superfície concentrador com Microsoft operações de gestão de conjunto de aplicações (OMS). Inicie sessão Analytics ajuda-o a que controlar o estado de funcionamento do seu concentradores superfície, bem como a compreender como estão a ser utilizados.

Cada concentrador de superfície tem o Microsoft monitorização Agent instalado. Respetivo através do agente de que pode enviar dados a partir do seu centro de superfície ao OMS. Ficheiros de registo são lidos a partir do concentradores superfície e são, em seguida, são enviados para o serviço OMS. Problemas por servidores a ser offline, o calendário não sincronizar, ou se a conta de dispositivo não conseguir que inicie sessão no Skype são apresentadas na OMS no dashboard de superfície concentrador. Ao utilizar os dados no dashboard, é possível identificar dispositivos que não estão a ser executado, ou que estão com outros problemas e aplicar potencialmente correções para problemas que detectado.


## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

Utilize as seguintes informações para instalar e configurar a solução. Para gerir o seu concentradores superfície a partir do Microsoft operações de gestão de conjunto de aplicações (OMS), terá o seguinte procedimento:

- Uma subscrição válida [OMS](http://www.microsoft.com/oms).
- Nível de [subscrição OMS](https://azure.microsoft.com/pricing/details/log-analytics/) que irá suportar o número de dispositivos que pretende monitorizar. Preços OMS variam consoante estiverem registados como vários dispositivos e a quantidade de dados-processos. Deverá tenha isto em consideração ao planear a sua implementação superfície concentrador.

Em seguida, irá adicionar uma subscrição do OMS à sua subscrição do Microsoft Azure existente ou criar uma nova área de trabalho diretamente através do portal do OMS. Instruções detalhadas sobre como utilizar qualquer um dos métodos encontra-se no [começar a trabalhar com a análise de registo](log-analytics-get-started.md). Assim que a subscrição OMS está configurada, existem duas formas para se inscrever os seus dispositivos Surface concentrador:

- Automaticamente através de InTune
- Manualmente através das **Definições** no seu dispositivo de superfície concentrador.

## <a name="set-up-monitoring"></a>Configurar o controlo

Pode monitorizar o estado de funcionamento e atividade do seu centro de superfície utilizando a análise de registo no OMS. Pode inscrever o Centro de superfície no OMS utilizando InTune ou localmente utilizando **as definições** no centro do superfície.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Ligar concentradores superfície a OMS através do InTune

Terá do ID da área de trabalho e a chave de área de trabalho para a área de trabalho OMS que irá gerir a sua concentradores superfície. Pode obter aqueles partir do portal OMS.

InTune é um produto da Microsoft que permite-lhe gerir centralmente as definições de configuração de OMS que são aplicadas a um ou mais dos seus dispositivos. Siga estes passos para configurar os seus dispositivos através do InTune:

1. Inicie sessão no InTune.
2. Navegue até **Definições** > **ligado origens**.
3. Criar ou editar uma política de baseado no modelo superfície concentrador.
4. Navegue para a secção OMS (Azure operacionais informações) da política e adicionar o *ID da área de trabalho* e a *Chave de área de trabalho* para a política.
5. Guarde a política.
6. Associe a política de grupo apropriado dos dispositivos.

  ![Política de InTune](./media/log-analytics-surface-hubs/intune.png)

Em seguida, o InTune sincroniza as definições de OMS com dispositivos no grupo de destino, inscrevê-los na área de trabalho OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Ligar superfície concentradores a OMS com a aplicação de definições

Terá do ID da área de trabalho e a chave de área de trabalho para a área de trabalho OMS que irá gerir a sua concentradores superfície. Pode obter aqueles partir do portal OMS.

Se não utiliza o InTune para gerir o seu ambiente, pode inscrever-se dispositivos manualmente através das **Definições** de cada concentrador de superfície:

1. A partir do seu centro de superfície, abra **Definições**.
2. Introduza as credenciais de administrador de dispositivo, quando lhe for pedido.
3. Clique em **este dispositivo**e em **monitorização**, clique em **Configurar definições de OMS**.
4. Selecione **Ativar a monitorização**.
6. Na caixa de diálogo de definições de OMS, escreva o **ID da área de trabalho** e introduza a **Chave de área de trabalho**.  
  ![definições](./media/log-analytics-surface-hubs/settings.png)
7. Clique em **OK** para concluir a configuração.

É apresentada uma confirmação informar ou não a configuração de OMS com êxito foi aplicada ao dispositivo. Se tiver sido, é apresentada uma mensagem a informar que o agente ligado com êxito o serviço OMS. O dispositivo inicia, em seguida, enviar dados para OMS onde pode ver e agir em-lo.

## <a name="monitor-surface-hubs"></a>Concentradores superfície monitor

Monitorizar os seus concentradores superfície utilizar OMS é muito como monitorização quaisquer outros dispositivos inscrito.

1. Inicie sessão no portal do OMS.
2. Navegue até ao dashboard de pacote de solução de superfície concentrador.
3. Estado de funcionamento do seu dispositivo é apresentado.

  ![Dashboard do Centro de superfície](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Pode criar [alertas](log-analytics-alerts.md) com base em pesquisas de registo existente ou personalizada. Utilizar os dados que a OMS recolhe do seu concentradores superfície, pode procurar problemas e um alerta as condições que pode definir para os seus dispositivos.


## <a name="next-steps"></a>Próximos passos

- Utilize [pesquisas de registo no registo de análise](log-analytics-log-searches.md) para ver dados detalhados de superfície concentrador.
- Crie [alertas](log-analytics-alerts.md) para notificá-lo quando ocorrem problemas com o seu concentradores superfície.
