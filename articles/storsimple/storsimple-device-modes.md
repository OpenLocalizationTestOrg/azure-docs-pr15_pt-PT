<properties 
   pageTitle="Alterar o modo de dispositivo StorSimple | Microsoft Azure"
   description="Descreve os modos de dispositivo StorSimple e explica como utilizar o Windows PowerShell para StorSimple para alterar o modo de dispositivo."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-device-mode-on-your-storsimple-device"></a>Alterar o modo de dispositivo no seu dispositivo StorSimple

Este artigo fornece uma descrição breve dos modos de vários na qual o seu dispositivo StorSimple pode funcionar. Dispositivo StorSimple pode funcionar em três modos: normal, manutenção e recuperação. 

Depois de ler este artigo, saberá:

- Quais os modos de dispositivo StorSimple são
- Como descobrir o modo em que o dispositivo StorSimple está no
- Como alterar a partir do normal em modo de manutenção e *vice-versa*


As tarefas de gestão acima só podem ser executadas através da interface do Windows PowerShell do seu dispositivo StorSimple.

## <a name="about-storsimple-device-modes"></a>Sobre os modos de dispositivo StorSimple

Dispositivo StorSimple pode funcionar no modo normal, manutenção ou recuperação. Cada um destes modos brevemente é descrita abaixo.

### <a name="normal-mode"></a>Modo normal

Isto é definido como o modo normal operacional para um dispositivo StorSimple completamente configurado. Por predefinição, o seu dispositivo deve ser no modo normal.

### <a name="maintenance-mode"></a>Modo de manutenção

Por vezes, poderá ter o dispositivo StorSimple seja colocado em modo de manutenção. Neste modo permite-lhe efetuar manutenção no dispositivo e instalar atualizações desorganização, como aqueles relacionados com firmware de disco.

Pode colocar o sistema no modo de manutenção apenas através do Windows PowerShell para StorSimple. Todos os pedidos e/s são colocados em pausa neste modo. Serviços como o não voláteis memória de acesso aleatório (NVRAM) ou o serviço de cluster também são parados. Ambos os controladores são reiniciados quando introduz ou sair do modo de presente. Quando sair do modo de manutenção, todos os serviços serão retomada e devem ser saudáveis. Isto pode demorar alguns minutos.

>[AZURE.NOTE]Modo de manutenção **só é suportado num dispositivo funcionar corretamente. Não é suportada num dispositivo no qual uma ou ambas as os controladores não estão a funcionar.**
</br>

### <a name="recovery-mode"></a>Modo de recuperação

Modo de recuperação pode ser definido como "Windows o modo de segurança com o suporte de rede". Modo de recuperação realiza a equipa do Microsoft Support e lhes permite executar diagnósticos do sistema. É o objetivo principal do modo de recuperação obter os registos do sistema.

Se o seu sistema de entrar em modo de recuperação, deverá contactar a Microsoft Support para os passos seguintes. Para obter mais informações, aceda ao [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md).

>[AZURE.NOTE] **Não pode colocar o dispositivo no modo de recuperação. Se o dispositivo está em mau estado, o modo de recuperação tentará obter o dispositivo para o estado no qual pessoal da Microsoft Support pode examiná-lo.**

## <a name="determine-storsimple-device-mode"></a>Determinar o modo de dispositivo StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Para determinar o modo de dispositivo atual

1. Inicie sessão na consola de série do dispositivo ao seguir os passos na [Utilização betumes para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Observe a mensagem de faixa no menu Acções em série de consola do dispositivo. Esta mensagem explicitamente indica se o dispositivo está no modo de manutenção ou recuperação. Se a mensagem não contiver qualquer informações específicas sobre no modo do sistema, o dispositivo está no modo normal.

## <a name="change-the-storsimple-device-mode"></a>Alterar o modo de dispositivo StorSimple 

Pode colocar o dispositivo StorSimple no modo de manutenção (a partir do modo normal) para executar a manutenção ou instalar as atualizações do modo de manutenção. Execute os seguintes procedimentos para introduzir ou sair do modo de manutenção.

> [AZURE.IMPORTANT] Antes de introduzir o modo de manutenção, verifique se ambos os controladores do dispositivo estão saudáveis acedendo o **Estado do Hardware** na página **Manutenção** no portal do Azure clássica. Se um ou ambos os controladores de não estiverem saudáveis, contacte o Microsoft Support para os passos seguintes. Para obter mais informações, aceda ao [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md).

#### <a name="to-enter-maintenance-mode"></a>Para introduzir o modo de manutenção

1. Inicie sessão na consola de série do dispositivo ao seguir os passos na [Utilização betumes para ligar à consola de série do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. No menu de consola série, selecione a opção 1, **Iniciar sessão com acesso total**. Quando lhe for pedido, forneça a **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é: `Password1`.

3. Na linha de comandos, escreva 

    `Enter-HcsMaintenanceMode`

4. Verá uma mensagem de aviso a informar que o modo de manutenção irá interromper a todos os pedidos e/s e Server a ligação ao portal clássica do Azure e ser-lhe-á pedido confirmação. Escreva **Y** para entrar no modo de manutenção.

5. Ambos os controladores irão reiniciar. Quando o reinício estiver concluído, aparecerá outra mensagem que indica que o dispositivo está no modo de manutenção.


#### <a name="to-exit-maintenance-mode"></a>Para sair do modo de manutenção

1. Inicie sessão na consola de série do dispositivo. Verifique se a partir da mensagem de faixa que o dispositivo está no modo de manutenção.

2. Na linha de comandos, escreva:

    `Exit-HcsMaintenanceMode`

3. Aparecerão uma mensagem de aviso e uma mensagem de confirmação. Escreva **Y** para sair do modo de manutenção.

4. Ambos os controladores irão reiniciar. Quando o reinício estiver concluído, aparecerá outra mensagem que indica que o dispositivo está no modo normal.


## <a name="next-steps"></a>Próximos passos

Saiba como [Aplicar atualizações de modo normal e manutenção](storsimple-update-device.md) no seu dispositivo StorSimple.

