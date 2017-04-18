<properties
    pageTitle="Introdução ao soluções pré-configurado | Microsoft Azure"
    description="Siga este tutorial para saber como implementar uma solução de conjunto de aplicações do IoT Azure pré-configuradas automaticamente."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Tutorial: Começar a trabalhar com as soluções pré-configurado

## <a name="introduction"></a>Introdução

Azure IoT Suite [soluções pré-configurado] [ lnk-preconfigured-solutions] combinar múltiplos Azure IoT serviços para fornecer soluções de ponto a ponto implementam cenários de negócio comuns IoT. A solução de *monitorização remota* pré-configurado liga-se ao e monitoriza o seus dispositivos. Pode utilizar a solução para analisar o fluxo de dados a partir do seu dispositivos e para melhorar os resultados de negócio ao tornar processos responder automaticamente a que fluem de dados.

Este tutorial mostra-lhe como pode aprovisionar a solução pré-configurado monitorização remota. -Lo também orienta as funcionalidades básicas de solução de controlo remota. Pode aceder muitas destas funcionalidades através do dashboard de solução que implementa juntamente com a solução pré-configurado:

![Monitorização remota pré-configurado dashboard solução][img-dashboard]

Para concluir este tutorial, precisa de uma subscrição ativa do Azure.

> [AZURE.NOTE]  Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter mais detalhes, consulte o artigo [Versão de avaliação gratuita do Azure][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Visualizar o dashboard de solução

Dashboard de solução permite-lhe gerir a solução implementada. Por exemplo, pode ver telemetria, adicionar dispositivos e configurar regras.

1.  Quando o aprovisionamento está concluído e o mosaico para a sua solução pré-configurado indica **pronto**, clique em **Iniciar** para abrir o seu portal de solução monitorização remoto num novo separador.

    ![Iniciar a solução pré-configurado][img-launch-solution]

2.  Por predefinição, o portal de solução mostra o *dashboard de solução*. Pode selecionar outras vistas utilizando o menu à esquerda.

    ![Monitorização remota pré-configurado dashboard solução][img-dashboard]

O dashboard apresenta as seguintes informações:

- O mapa apresenta a localização de cada dispositivo ligado à solução. Quando executa a solução pela primeira vez, existem quatro dispositivos simulados. Os dispositivos simulados são implementados como Azure WebJobs e a solução utiliza a API do mapas Bing para desenhar informações no mapa.
- O painel de **Histórico de telemetria** desenha humidade e temperatura telemetria num dispositivo selecionado no perto em tempo real e apresenta agregar dados tal como humidade média, mínimo e máximo.
- Painel **Histórico alarme** mostra eventos alarme recentes quando um valor de telemetria excedeu um determinado limiar. Pode definir o seus próprio alarmes para além dos exemplos criados pela solução pré-configurado.

## <a name="view-the-device-list"></a>Ver a lista de dispositivo

A lista de dispositivo apresenta todos os dispositivos registados na solução. Ver e editar os metadados de dispositivo, adicione ou remova os dispositivos e enviar comandos para dispositivos.

1.  Clique em **dispositivos** no menu do lado esquerdo para mostrar a *lista de dispositivos* para esta solução.

    ![Lista de dispositivos no dashboard][img-devicelist]

2.  A lista de dispositivo mostra que não existem quatro dispositivos simulados criados pelo processo de aprovisionamento.

3.  Clique num dispositivo na lista de dispositivos para ver os detalhes do dispositivo.

    ![Detalhes do dispositivo no dashboard][img-devicedetails]

O painel de **Detalhes do dispositivo** contém três secções:

- A secção **ações** lista as ações que pode efetuar no dispositivo. Se desativar o dispositivo, já não é permitida de telemetria de enviar ou receber comandos. Se desativar um dispositivo, pode, em seguida, voltar a ativá-lo. Pode adicionar uma regra associada com o dispositivo que accionadores um alarme quando um valor de telemetria excede um determinado limiar. Pode também enviar um comando para um dispositivo. Quando um dispositivo se liga pela primeira vez, indica que a solução os comandos-pode responder a.
- A secção **Propriedades do dispositivo** lista os metadados de dispositivo. Alguns deste metadados vem a partir do dispositivo (por exemplo, o fabricante) e algumas é gerado pela solução (tal como a hora de criação). Pode editar os metadados de dispositivo a partir daqui.
- A secção de **Chaves de autenticação** listas as teclas que pode utilizar o dispositivo para autenticar com a solução.

## <a name="send-a-command-to-a-device"></a>Enviar um comando para um dispositivo

O painel de detalhes do dispositivo mostra todos os comandos que suporta um dispositivo específico e permite para enviar o comandos para um dispositivo. Quando um dispositivo inicia pela primeira vez, envia informações sobre os comandos suporta a solução.

1.  Clique em **comandos** no painel de detalhes de dispositivo para o dispositivo seleccionado.

    ![Comandos de dispositivo no dashboard][img-devicecommands]

2.  Selecione **PingDevice** a partir da lista de comandos.

3.  Clique em **Enviar comando**.

4.  Pode ver o estado do comando no histórico de comandos.

    ![Comando de estado no dashboard][img-pingcommand]

A solução controla o estado de cada comando envia. Inicialmente o resultado é **pendentes**. Quando o dispositivo de relatórios que executou o comando, o resultado é definido para **Success**.

## <a name="add-a-new-simulated-device"></a>Adicionar um novo dispositivo simulado

Quando implementar a solução pré-configurado, automaticamente aprovisionar quatro dispositivos de exemplo que pode ver na lista de dispositivos. Estes dispositivos são *simulados dispositivos* a ser executada numa WebJob Azure. Dispositivos simulados facilitam experimentar com a solução pré-configurado sem ser necessário para implementar reais, físicos dispositivos. Se pretender ligar um dispositivo real para a solução, consulte o artigo [ligar o dispositivo para o controlo remoto monitorização solução pré-configurado] [ lnk-connect-rm] tutorial.

Os seguintes passos mostram como adicionar um dispositivo simulado para a solução:

1.  Navegar para a lista de dispositivos.

2.  Clique em **+ Adicionar um dispositivo** no canto inferior esquerdo para adicionar um dispositivo.

    ![Adicionar um dispositivo a solução pré-configurado][img-adddevice]

3.  Clique em **Adicionar novo** no mosaico **Simulado dispositivo** .

    ![Definir os novos detalhes de dispositivo no dashboard][img-addnew]
    
    Para além de criar um novo dispositivo simulado, também pode adicionar um dispositivo físico se optar por criar um **Dispositivo personalizada**. Para saber mais sobre como ligar dispositivos físicos para a solução, consulte o artigo [ligar o dispositivo para o conjunto de aplicações IoT monitorização solução pré-configurado remota][lnk-connect-rm].

4.  Selecione **Deixar-me a definir o meu próprio ID do dispositivo**e, introduza um nome de ID de dispositivo exclusivo tal como **mydevice_01**.

5.  Clique em **Criar**.

    ![Guardar um novo dispositivo][img-definedevice]

6. No passo 3 de **Adicionar um dispositivo simulado**, clique em **Concluir** para regressar à lista de dispositivos.

7. Pode ver o seu dispositivo **a executar** na lista de dispositivos.

    ![Novo dispositivo de vista na lista de dispositivos][img-runningnew]

8. Também pode ver a telemetria simulada a partir do seu novo dispositivo no dashboard de:

    ![Vista de telemetria a partir do novo dispositivo][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Editar os metadados de dispositivo

Quando um dispositivo pela primeira vez se liga para a solução, envia os seus metadados para a solução. Quando edita os metadados de dispositivo através do dashboard de solução, envia os novos valores de metadados para o dispositivo e armazena os novos valores na base de dados de DocumentDB solução. Para obter mais informações, consulte o artigo [registo de identidade do dispositivo e DocumentDB][lnk-devicemetadata].

1.  Navegar para a lista de dispositivos.

2.  Selecione o seu novo dispositivo na **Lista de dispositivos**e, em seguida, clique em **Editar** para editar as **Propriedades do dispositivo**:

    ![Editar metadados de dispositivo][img-editdevice]

3. Desloque para baixo e efetua uma alteração de valores de latitude e longitude. Em seguida, clique em **Guardar alterações feitas ao registo de dispositivo**.

    ![Editar metadados de dispositivo][img-editdevice2]

4. Navegar de volta para o dashboard, a localização do dispositivo mudou no mapa:

    ![Editar metadados de dispositivo][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Adicionar uma regra para o novo dispositivo

Não existem regras para o novo dispositivo que acabou de adicionar. Nesta secção, adicione uma regra que accionadores um alarme quando a temperatura comunicada pelo novo dispositivo excede 47 graus. Antes de começar, repare que o histórico de telemetria para o novo dispositivo no dashboard de mostra que a temperatura de dispositivo nunca excedem os 45 graus.

1.  Navegar para a lista de dispositivos.

2.  Selecione o novo dispositivo na **Lista de dispositivos**e, em seguida, clique em **Adicionar regra** para adicionar uma regra para o dispositivo.

3. Crie uma regra que utiliza a **temperatura** como o campo de dados e utiliza **AlarmTemp** como o resultado quando a temperatura excede graus 47:

    ![Adicionar uma regra de dispositivo][img-adddevicerule]

4. Clique em **regras de vista e guardar** para guardar as alterações.

5.  Clique em **comandos** no painel de detalhes de dispositivo para o novo dispositivo.

    ![Adicionar uma regra de dispositivo][img-adddevicerule2]

6.  Selecione **ChangeSetPointTemp** a partir da lista de comandos e defina **SetPointTemp** para 45. Em seguida, clique em **Enviar comando**:

    ![Adicionar uma regra de dispositivo][img-adddevicerule3]

7.  Navegar de volta para o dashboard de solução. Após um breve momento, irá ver uma nova entrada no painel do **Histórico de alarme** quando a temperatura comunicada pelo seu novo dispositivo excede o limite de grau 47:

    ![Adicionar uma regra de dispositivo][img-adddevicerule4]

8. Pode rever e editar todas as regras na página do dashboard **regras** :

    ![Regras de lista de dispositivos][img-rules]

9. Pode rever e editar todas as ações que podem ser tidos em resposta a uma regra na página do dashboard **ações** :

    ![Ações de dispositivo de lista][img-actions]

> [AZURE.NOTE] É possível definir as acções que podem enviar uma mensagem de correio eletrónico ou SMS em resposta a uma regra ou integrar com um sistema de linha de negócio através de uma [Aplicação de lógica][lnk-logic-apps]. Para obter mais informações, consulte o artigo [ligar a aplicação de lógica para monitorização o Azure IoT Suite remota pré-configurado solução][lnk-logicapptutorial].

## <a name="other-features"></a>Outras funcionalidades

Utilizando o portal de solução que pode procurar para dispositivos com características específicas, tal como um número de modelo:

![Procurar um dispositivo][img-search]

Pode desativar um dispositivo e, depois de ser desactivado pode removê-lo:

![Desactivar e remover um dispositivo][img-disable]

## <a name="behind-the-scenes"></a>Nos bastidores

Quando implementar uma solução pré-configurado, o processo de implementação cria vários recursos na subscrição Azure que selecionou. Pode ver estes recursos no Azure [portal][lnk-portal]. O processo de implementação cria um **grupo de recursos** com um nome com base no nome que escolher para a sua solução pré-configurado:

![Solução pré-configurado no portal do Azure][img-portal]

Pode ver as definições de cada recurso ao selecioná-lo na lista de recursos no grupo de recursos.

Também pode ver o código de origem para a solução pré-configurado. O código de origem solução pré-configurado monitorização remoto é a [monitorização de azure iot remote] [ lnk-rmgithub] GitHub repositório:

- A pasta **DeviceAdministration** contém o código de origem para o dashboard.
- A pasta **Simulator** contém o código de origem para o dispositivo simulado.
- A pasta **EventProcessor** contém o código de origem para o processo de back-end que se trata de telemetria recebida.

Quando tiver terminado, pode eliminar a solução pré-configurado da sua subscrição Azure na [azureiotsuite.com] [ lnk-azureiotsuite] site. Este site permite-lhe facilmente eliminar todos os recursos que foram aprovisionados quando criou a solução pré-configurado.

> [AZURE.NOTE] Para se certificar de que elimine tudo relacionados com a solução pré-configurado, elimine-a [azureiotsuite.com] [ lnk-azureiotsuite] do site e não simplesmente eliminar o grupo de recursos no portal.

## <a name="next-steps"></a>Próximos passos

Agora que já implementou uma solução de trabalho pré-configuradas automaticamente, pode continuar a introdução ao conjunto de aplicações do IoT lendo os artigos seguintes:

- [Monitorização remota pré-configurado solução guiadas][lnk-rm-walkthrough]
- [Ligar o seu dispositivo para a solução pré-configurado monitorização remota][lnk-connect-rm]
- [Permissões no site de azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
