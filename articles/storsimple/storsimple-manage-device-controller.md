<properties
   pageTitle="Gerir controladores do dispositivo de StorSimple | Microsoft Azure"
   description="Saiba como parar, reinicie, encerrar ou repor os controladores do dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="manage-your-storsimple-device-controllers"></a>Gerir os controladores do dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial descreve as operações diferentes que podem ser executadas no seu controladores do dispositivo StorSimple. Os controladores no seu dispositivo StorSimple são controladores de redundantes (peer) numa configuração activo passivo. Num dado momento, apenas um controlador está ativo e está a processar todas as operações de disco e de rede. O controlador de outros está em modo de passivo. Se o controlador de ativo falhar, o controlador de passivo fica ativo automaticamente.

Neste tutorial inclui instruções passo a passo para gerir os controladores do dispositivo utilizando o:

- **Controladores de** secção da página de **Manutenção** no serviço StorSimple Gestor
- Windows PowerShell para StorSimple.

Recomendamos que gerir os controladores do dispositivo através do serviço de Gestor de StorSimple. Se só pode ser executada uma ação ao utilizar o Windows PowerShell para StorSimple, o tutorial faz com que uma nota do mesmo.

Depois de ler neste tutorial, irá poderá:

- Reiniciar ou encerrar um controlador de dispositivo StorSimple
- Encerrar um dispositivo StorSimple
- Repor o seu dispositivo StorSimple para as predefinições de fábrica


## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou encerrar um único controlador

Um controlador reinício ou encerramento não é necessário como parte do funcionamento do sistema normal. Operações de encerramento para um controlador de dispositivo único são comuns apenas nos casos em que um componente de hardware do dispositivo falhado necessita de substituição. Também poderá ser necessário reiniciar o computador controlador uma situação na qual o desempenho é afetado pela utilização de memória excessiva ou um controlador de funcionar corretamente. Também poderá ter de reiniciar um controlador após a substituição controlador efetuada com êxito, se pretender ativar e testar o controlador de substituído.

Reiniciar um dispositivo não é desorganização para iniciadores ligadas, partindo do princípio de que o controlador passivo está disponível. Se um controlador passivo não está disponível ou invertida desativado, em seguida, reiniciar o controlador de ativo pode resultar em interrupção do serviço e o tempo de inatividade.

> [AZURE.IMPORTANT]

> - **Nunca deve ser removido física um controlador em execução como o resultado seria uma perda de redundância e aumento do risco de tempo de inatividade.**

> - O procedimento seguinte só se aplica o dispositivo físico StorSimple. Para obter informações sobre como iniciar, parar e reinicie o dispositivo virtual, consulte o artigo [trabalhar com o dispositivo virtual](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).

Pode reiniciar ou encerrar um controlador de dispositivo único utilizando o portal do Azure clássico do serviço do Gestor de StorSimple ou o Windows PowerShell para StorSimple

Para gerir os controladores do dispositivo a partir do portal clássico Azure, execute os passos seguintes.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Para reiniciar ou encerrar um controlador no portal clássico

1. Navegue para **dispositivos > manutenção**.

1. Aceda a **Estado de Hardware** e certifique-se de que o estado dos controladores no seu dispositivo é **Saudável**.

    ![Verifique se controladores do dispositivo de StorSimple estão saudáveis](./media/storsimple-manage-device-controller/IC766017.png)

1. A partir da parte inferior da página de **Manutenção** , clique em **Gerir controladores**.

    ![Gerir controladores do dispositivo de StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>

    >[AZURE.NOTE] Se não conseguir ver **Gerir controladores**, em seguida, terá de instalar as atualizações. Para mais informações, consulte o artigo [atualizar o seu dispositivo StorSimple](storsimple-update-device.md).

1. Na caixa de diálogo **Alterar controlador de definições** , faça o seguinte:
    1. A partir da lista pendente **Selecione controlador** , selecione o controlador de que pretende gerir. As opções estão controlador de 0 e 1 de controlador. Estes controladores também são identificados como ativo ou passiva.

        >[AZURE.NOTE] Não pode ser gerido um controlador se estiver indisponível ou invertida desativado e não aparecerá na lista pendente.

    2. A partir da lista pendente **Selecionar ação** , selecione **reiniciar controlador** ou **Encerrar controlador**.

        ![Reinicie o controlador de passiva StorSimple dispositivo](./media/storsimple-manage-device-controller/IC766020.png)
    3. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-device-controller/IC740895.png).

Isto irá reiniciar ou encerrar o controlador de. A tabela abaixo resume os detalhes do que acontece consoante as seleções que tenha efetuado na caixa de diálogo **Alterar controlador de definições** .  


|Seleção #|Se optar por...|Isto irá ocorrer.|
|---|---|---|
|1.|Reinicie o controlador passivo.|Será criada uma tarefa para reiniciar o controlador de e será notificado depois da tarefa é criada com êxito. Isto irá iniciar o reinício controlador. Pode monitorizar o processo de reinício acedendo **serviço > dashboards > Ver registos de operação** e, em seguida, filtrar por parâmetros específicos no seu serviço.|
|2.|Reinicie o controlador ativo.|Verá o seguinte aviso: "se reiniciar o controlador de ativo, o dispositivo irá falhar ao longo para o controlador de passivo. Pretende continuar?" </br>Se optar por continuar com esta operação, os passos será idênticos aos utilizados para reiniciar o controlador de passivo (consulte seleção 1).|
|3.|Encerre o controlador de passivo.|Irá ver a seguinte mensagem: "após encerramento está concluído, terá de premir o botão do power no seu comando a ativá-la. Tem a certeza que pretende encerrar este controlador?" </br>Se optar por continuar com esta operação, os passos será idênticos aos utilizados para reiniciar o controlador de passivo (consulte seleção 1).|
|4.|Encerre o controlador de ativo.|Irá ver a seguinte mensagem: "após encerramento está concluído, terá de premir o botão do power no seu comando a ativá-la. Tem a certeza que pretende encerrar este controlador?" </br>Se optar por continuar com esta operação, os passos será idênticos aos utilizados para reiniciar o controlador de passivo (consulte seleção 1).|


#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou encerrar um controlador no Windows PowerShell para StorSimple
Execute os passos seguintes para encerrar ou reiniciar um único controlador no seu dispositivo StorSimple a partir do Azure portal clássico.


1. O dispositivo de acesso através da utilização da consola do série ou uma sessão de telnet a partir de um computador remoto. Ligar a controlador de 0 ou controlador 1 ao seguir os passos na [Utilização betumes para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. No menu de consola série, selecione a opção 1, **Iniciar sessão com acesso total**.

1. Na mensagem faixa, tome nota do controlador de estiver ligado à (controlador de 0 ou controlador 1) e se está activo ou o controlador de (em espera) passivo.
    - Para encerrar um único controlador, na linha de comandos, escreva:

        `Stop-HcsController`

        Isto irá encerrar o controlador de que está ligado. Se deixar do controlador de ativo, em seguida,-falhará ao longo para o controlador de passivo antes de-encerra.
    - Para reiniciar um controlador, na linha de comandos, escreva:

        `Restart-HcsController`

        Isto irá reiniciar o controlador de que está ligado. Se reiniciar o controlador de ativo, irá falhar ao longo para o controlador de passivo antes do reinício.


## <a name="shut-down-a-storsimple-device"></a>Encerrar um dispositivo StorSimple

Esta secção explica como encerrar um corrente ou um dispositivo de StorSimple falhado a partir de um computador remoto. Um dispositivo está desativado depois de encerrar ambos os controladores do dispositivo. Um encerramento do dispositivo é feito quando o dispositivo está a ser movido física ou é disponibilizado fora do serviço.

> [AZURE.IMPORTANT] Antes de encerrar o dispositivo, verifique o estado de funcionamento dos componentes do dispositivo. Navegue para **dispositivos > manutenção > Estado de Hardware** e confirme que o estado de LED de todos os componentes está verde. Apenas um dispositivo Saudável terá um estado verde. Se o seu dispositivo está a ser encerrar para substituir um componente de funcionar corretamente, verá um falhado (vermelho) ou um Estado (amarelo) degradado para os respetivos componentes.

#### <a name="to-shut-down-a-storsimple-device"></a>Para encerrar um dispositivo StorSimple

1. Utilize o procedimento [reiniciar ou encerrar um controlador](#restart-or-shut-down-a-single-controller) para identificar e encerrar o controlador de passivo no seu dispositivo. Pode efetuar esta operação no portal do Azure clássico ou no Windows PowerShell para StorSimple.
2. Repita o passo acima para encerrar o controlador de ativo.
3. Terá agora ver o plano de trás do dispositivo. Depois dos controladores de duas são completamente encerrar, o estado LEDs em ambos os controladores de deve intermitente vermelho. Se precisar de desativar a opção o dispositivo completamente neste momento, inverta os parâmetros de energia no Power e módulos refrigeração (PCMs) para a posição DESATIVADO. Isto deve desativar o dispositivo.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Repor o dispositivo para as predefinições de fábrica

> [AZURE.IMPORTANT] Se precisar de para repor o seu dispositivo para as predefinições de fábrica do mesmo, contacte o Microsoft Support. O procedimento descrito abaixo deve ser utilizado apenas em conjunto com o Microsoft Support.

Este procedimento descreve como repor o seu dispositivo do Microsoft Azure StorSimple para as predefinições de fábrica do mesmo através do Windows PowerShell para StorSimple.
Reposição de um dispositivo remove todos os dados e definições de todo o cluster por predefinição.

Execute os seguintes passos para repor o seu dispositivo do Microsoft Azure StorSimple para as predefinições de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para repor o dispositivo para predefinições no Windows PowerShell para StorSimple

1. O dispositivo de acesso através da respectiva consola série. Verifique a mensagem de faixa para se certificar de que estão ligadas para o controlador de ativo.

1. No menu de consola série, selecione a opção 1, **Iniciar sessão com acesso total**.

1. Na linha de comandos, escreva o seguinte comando para repor a totalidade do cluster, remover todas as definições de dados, metadados e controlador de:

    `Reset-HcsFactoryDefault`

    Para repor a um único controlador em vez disso, utilize o cmdlet [Repor HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) com o `-scope` parâmetro.)

    O sistema será reiniciado várias vezes. Será notificado quando a reposição foi concluída com êxito. Consoante o modelo de sistema, pode demorar 45-60 minutos para um dispositivo 8100 e 60 90 minutos para que um 8600 concluir este processo.

    > [AZURE.TIP]

    > - Se estiver a utilizar a atualização 1.2 ou anterior utilize o `–SkipFirmwareVersionCheck` parâmetro para ignorar a verificação da versão firmware (caso contrário, verá um erro de correspondência firmware: Repor fábrica não é possível continuar devido a um erro de correspondência nas versões firmware. ).

    > - O procedimento de reposição de fábrica do mesmo poderá falhar para dispositivos StorSimple que estiver a executar atualização 1 ou 1.1 no portal de administração pública e tiverem executado a substituição bem sucedida controlador simples ou duplo (com os controladores de substituição que foram enviados com um software de pré-lançamento atualização 1). Isto acontece quando a fábrica de Repor imagem for validada a presença de um ficheiro de SHA1 no controlador de que não existe anterior ao software de actualização 1. Se vir que esta fábrica repor falha, contacte o Microsoft Support para ajudá-lo com os passos seguintes. Este problema não é visualizado com os controladores de substituição que foram enviados a partir da fábrica com o software posterior ou atualização 1.


## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre a gestão de controladores do dispositivo

Nesta secção, podemos ter resumido algumas das perguntas mais frequentes sobre gestão controladores de dispositivo de StorSimple.

**Q.** O que acontece se ambos os controladores no meu dispositivo são saudável e invertida no e posso reiniciar ou encerrar o controlador de ativo?

**RESPOSTAS.** Se ambos os controladores no seu dispositivo são saudável e invertida, vai ser-lhe confirmação. Pode optar por:

- **Reinicie o controlador de ativo** – será notificado reiniciar um controlador ativo originará o dispositivo a falha ao longo para o controlador de passivo. O controlador de irá reiniciar.

- **Encerrar um controlador ativo** – será notificado que encerrar um controlador ativo irá resultar em tempo de inatividade. Também terá do botão de energia de emissão no dispositivo para ativar o controlador de.

**Q.** O que acontece se o controlador de passivo no meu dispositivo não está disponível ou invertida desativado e posso reiniciar ou encerrar o controlador de ativo?

**RESPOSTAS.** Se o controlador de passivo no seu dispositivo está indisponível ou invertida desativado e optar por:

- **Reinicie o controlador de ativo** – será notificado que se continuar a operação irá resultar numa interrupção do serviço de temporária e ser-lhe-á pedido confirmação.

- **Encerrar um controlador ativo** – será notificado que se continuar a operação irá resultar em tempo de inatividade e que terá do botão de energia de emissão em ou em ambos os controladores para ativar o dispositivo. Será pedido de confirmação.

**Q.** Quando é que o controlador reinício ou encerramento falha de progresso?

**RESPOSTAS.** Reiniciar ou encerrar um controlador poderá falhar se:

- Uma atualização de dispositivo está em curso.

- Um reinício controlador já está em curso.

- Um encerramento do controlador já está em curso.

**Q.** Como pode lhe descobriu se um controlador foi reiniciado ou encerrar?

**RESPOSTAS.** Pode verificar o estado do controlador na página Manutenção. O estado do controlador indicam se foi reiniciado ou encerrar um controlador. Para além disso, a página alertas irá conter um alerta informativo se o controlador de foi reiniciado ou encerrar. As operações de reinício e encerramento do controlador também são gravadas nos registos de operação. Para mais informações sobre registos da operação, vá para [ver os registos de operação](storsimple-service-dashboard.md#view-the-operations-logs).

**Q.** Existe qualquer impacto para os/s estatístico como resultado da activação pós-falha de controlador?

**RESPOSTAS.** As ligações de TCP entre iniciadores e controlador de active serão repostas estatístico como resultado da activação pós-falha de controlador, mas serão restabelecidas quando o controlador de passivo assume operação. Poderão existir uma pausa temporário (menos de 30 segundos) na atividade e/s entre iniciadores e o dispositivo durante a execução desta operação.

**Q.** Como posso devolver o meu controlador de após tenham sido encerrar e removida do serviço?

**RESPOSTAS.** Para devolver um controlador de serviço, tem de a inserir no quadro, tal como descrito em [substituir um módulo de controlador no seu dispositivo StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Próximos passos

- Se encontrar problemas com os controladores do dispositivo StorSimple que não é possível resolver utilizando os procedimentos listados neste tutorial, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md).

- Para saber mais sobre como utilizar o serviço do Gestor de StorSimple, aceda a [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
