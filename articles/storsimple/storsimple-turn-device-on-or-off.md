<properties 
   pageTitle="Ativar ou desativar o seu dispositivo StorSimple | Microsoft Azure"
   description="Explica como ativar um novo dispositivo StorSimple, ativar um dispositivo que foi encerrar ou perdeu energia e desligar um dispositivo em execução."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/23/2016"
   ms.author="alkohli" />

# <a name="turn-your-storsimple-device-on-or-off"></a>Ativar ou desativar o seu dispositivo StorSimple 

## <a name="overview"></a>Descrição geral

Encerrar um dispositivo Microsoft Azure StorSimple não é necessária como parte do funcionamento do sistema normal. No entanto, poderá ter de ativar um novo dispositivo ou um dispositivo que tinha para ser encerrar. Em geral, é necessário um encerramento em casos em que tem de substituir falha hardware, física mover uma unidade ou tirar um dispositivo de serviço. Neste tutorial descreve o procedimento necessário para ativar o registo e encerrar o seu dispositivo StorSimple em cenários diferentes.

A tabela seguinte apresenta vários cenários para ativar o registo e encerrar o seu dispositivo StorSimple e fornece ligações para os procedimentos adequados.

|Cenário|Tópicos de referência|
|:-------|:---------------|
|Ativar um novo dispositivo|[Ativar um novo dispositivo](#turn-on-a-new-device)<ul><li>[Novo dispositivo com apenas inclusão principal](#new-device-with-primary-enclosure-only)</li><li>[Novo dispositivo com inclusão EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Ativar um dispositivo após o encerramento|[Ativar um dispositivo após o encerramento](#turn-on-a-device-after-shutdown)<ul><li>[Dispositivo com apenas inclusão principal](#device-with-primary-enclosure-only)</li><li>[Dispositivo com inclusão EBOD](#device-with-ebod-enclosure)</li></ul>|
|Ativar um dispositivo após uma perda power|[Ativar um dispositivo após uma perda power](#turn-on-a-device-after-a-power-loss)<ul><li>[Dispositivo com apenas inclusão principal](#8100)</li><li>[Dispositivo com inclusão EBOD](#8600)</li></ul>|
|Ativar um dispositivo depois da inclusão de principal e EBOD ligação for perdida|[Ativar um dispositivo depois da página principal e ligação de inclusão EBOD é perdida](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Encerrar um dispositivo em execução|[Desligar um dispositivo em execução](#turn-off-a-running-device)<ul><li>[Dispositivo com apenas inclusão principal](#8100a)</li><li>[Dispositivo com inclusão EBOD](#8600a)</li></ul>|

## <a name="turn-on-a-new-device"></a>Ativar um novo dispositivo

Os passos para ativar um dispositivo StorSimple pela primeira vez diferem dependendo se o dispositivo é uma 8100 ou de um modelo de 8600. O 8100 tem uma única inclusão principal, Considerando que o 8600 é um dispositivo de inclusão de dupla com uma inclusão principal e uma inclusão EBOD. Os passos detalhados para ambos os modelos são abrangidos nas secções seguintes.

- [Novo dispositivo com apenas inclusão principal](#new-device-with-primary-enclosure-only)

- [Novo dispositivo com inclusão EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Novo dispositivo com apenas inclusão principal

O modelo de StorSimple 8100 é um dispositivo de inclusão único. O dispositivo inclui redundantes Power e módulos refrigeração (PCMs). Ambos os PCMs tem de estar instalados e ligados a origens de energia diferente para garantir a disponibilidade de alta.

Execute os passos seguintes para cabo o seu dispositivo para o power.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Para a configuração de dispositivo completo e cablagem instruções, aceda ao [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Certifique-se de que siga as instruções exatamente.

### <a name="new-device-with-ebod-enclosure"></a>Novo dispositivo com inclusão EBOD

O modelo de StorSimple 8600 tem uma inclusão principal e uma inclusão EBOD. Isto requer que as unidades de estar ligado em conjunto para conectividade de série anexado SCSI (SA) e power.

Quando configurar este dispositivo pela primeira vez, execute os passos para SA cablagem pela primeira vez e, em seguida, conclua os passos para power cablagem.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Para a configuração de dispositivo completo e cablagem instruções, aceda ao [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Certifique-se de que siga as instruções exatamente.

## <a name="turn-on-a-device-after-shutdown"></a>Ativar um dispositivo após o encerramento

Os passos para ativar um dispositivo StorSimple depois de ter sido encerrar são diferentes consoante o dispositivo está a um 8100 ou um modelo de 8600. O 8100 tem uma única inclusão principal, Considerando que o 8600 é um dispositivo de inclusão de dupla com uma inclusão principal e uma inclusão EBOD.

- [Dispositivo com apenas inclusão principal](#device-with-primary-enclosure-only)

- [Dispositivo com inclusão EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivo com apenas inclusão principal

Depois de um encerramento, utilize o seguinte procedimento para ativar num dispositivo com uma inclusão principal e sem inclusão EBOD StorSimple.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Para ativar um dispositivo com apenas uma inclusão principal

1. Certifique-se de que a potência muda em ambos os Power e módulos refrigeração (PCMs) estão na posição DESATIVADO. Se os parâmetros não estiverem a ser a posição DESATIVADO, invertê-las para a posição DESATIVADO e aguarde que luzes aceder a.

2. Ative o dispositivo por inversão os parâmetros de energia em ambos os PCMs para a posição ON. O dispositivo deverá ativar.

3. Verifique o seguinte para confirmar que o dispositivo está totalmente no:

    1. Os LEDs OK em ambas as módulos PCM são verdes.

    2. O estado LEDs em ambos os controladores são verde sólida.

    3. O LED azul dos controladores está a piscar, que indica que o controlador está ativo.

    Se alguma destas condições não forem cumpridas, em seguida, o dispositivo não estiver saudável. Utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Dispositivo com inclusão EBOD

Depois de um encerramento, utilize o seguinte procedimento para ativar num dispositivo com uma inclusão principal e uma inclusão EBOD StorSimple. Execute cada passo sequência exatamente conforme descrito. Se não conseguir fazê-lo poderá resultar na perda de dados.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Para ativar um dispositivo com um principal e uma inclusão EBOD

1. Certifique-se de que a inclusão EBOD está ligado a inclusão principal. Para mais informações, consulte o artigo [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Certifique-se de que o Power e refrigeração módulos (PCMs) no EBOD e anexos primários se encontram na posição DESATIVADO. Se os parâmetros não estiverem a ser a posição DESATIVADO, invertê-las para a posição DESATIVADO e aguarde que luzes aceder a.

3. Ativar a inclusão EBOD pela primeira vez por inversão potência muda em ambos os PCMs para a posição ON. Os LEDs PCM deve ser verdes. Um controlador EBOD LED verde esta unidade indica que a inclusão EBOD está ativada.

4. Ative a inclusão primária por inversão os parâmetros de energia em ambos os PCMs para a posição ON. Todo o sistema deverá agora estar no.

5. Verifique se os LEDs SA verdes, que garantem que a ligação entre a inclusão EBOD e a inclusão primária é boa.

## <a name="turn-on-a-device-after-a-power-loss"></a>Ativar um dispositivo após uma perda power

Uma falha de energia ou interrupções, podem encerrar um dispositivo StorSimple. Falha de energia pode ocorrer uma das fontes de alimentação ou ambas as fontes de alimentação. Os passos de recuperação são diferentes consoante o dispositivo está a um modelo de 8100 ou um 8600. O 8100 tem uma única inclusão principal, Considerando que o 8600 é um dispositivo de inclusão de dupla com uma inclusão principal e uma inclusão EBOD. Esta secção descreve o procedimento de recuperação para cada cenário.

- [Dispositivo com apenas inclusão principal](#8100)

- [Dispositivo com inclusão EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivo com apenas inclusão principal<a name="8100">

O sistema pode continuar a sua operação normal se existir perda de energia para uma das suas fontes de alimentação. No entanto, para assegurar que elevada disponibilidade do dispositivo, restaure a power alimentação mais cedo possível.

Se existir uma falha de energia ou interrupções power em ambas as fontes de alimentação, o sistema irá encerrar de uma forma ordenada e controlada. Quando for restaurada potência, o sistema irá ativar automaticamente.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo com inclusão EBOD<a name="8600">

#### <a name="power-loss-on-one-power-supply"></a>Perda de energia no um power fornecer

O sistema pode continuar a sua operação normal se existir perda de energia para uma das suas fontes de alimentação na inclusão principal ou a inclusão EBOD. No entanto, para assegurar que elevada disponibilidade do dispositivo, restaure power para a fonte de alimentação mais cedo possível.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Perda de energia em ambas as fontes de alimentação no principal e anexos EBOD

Se existir uma falha de energia ou interrupções power em ambas as fontes de alimentação, a inclusão EBOD irá encerrar imediatamente e uma forma ordenada e controlada irá encerrar a inclusão principal. Quando for restaurado power, aparelho será iniciado automaticamente.

Se a potência estiver desligada manualmente, em seguida, siga os passos seguintes para restaurar power para o sistema.

1. Ative a inclusão EBOD.

2. Após a inclusão EBOD está na, ative a inclusão principal.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Perda de energia em ambas as fontes de alimentação no inclusão EBOD

Quando configurar o seu cabos, certifique-se de que o EBOD nunca está ligado individualmente um PDU em separado. Se a EBOD e inclusão primária falharem ao mesmo tempo, o sistema irá recuperar.

Se apenas a inclusão EBOD falha em ambas as fontes de alimentação, não conseguirá recuperar automaticamente o sistema de segurança. Siga os passos seguintes para ativar o sistema e restaurá-la para um estado Saudável:

1. Se a inclusão principal estiver ativada, mude desativar Power e módulos refrigeração (PCMs).

2. Aguarde alguns minutos encerrar o sistema.

3. Ative a inclusão EBOD.

4. Após a inclusão EBOD está na, ative a inclusão principal.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Ativar um dispositivo depois da página principal e ligação de inclusão EBOD é perdida

Se a ligação é perdida entre o controlador de espera e o controlador EBOD correspondente, o dispositivo continua a funcionar. Se se perder a ligação entre o controlador de active system e o controlador EBOD correspondente, deve ocorrer activação pós-falha e o dispositivo deve continuar a trabalhar como normal.

Quando são removidos ambos os cabos de série anexado SCSI (SA) ou a ligação entre a inclusão EBOD e a inclusão primária é cortada, o dispositivo para de funcionar. Neste momento, execute os passos seguintes.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Para ativar o dispositivo após ligação for perdida

1. Aceda a parte detrás do dispositivo.

2. Se a ligação de cabo SA entre a inclusão EBOD e a inclusão primária for interrompida, todos os Gonçalves SA LEDs na inclusão EBOD deixará de estar ligado.

3. Encerre Power e módulos refrigeração (PCMs) a inclusão EBOD e a inclusão principal.

4. Aguarde até que todas as luzes na parte detrás do ambos os anexos a desativar.

5. Voltar a inseri os cabos de SA e certifique-se de que existe uma boa ligação entre a inclusão EBOD e a inclusão principal.

6. Ativar a inclusão EBOD pela primeira vez por inversão ambas as PCM muda para a posição ON.

7. Certifique-se de que a inclusão EBOD está ativada, verificando que o LED verde está Ativada.

8. Ative a inclusão principal.

9. Certifique-se de que a inclusão primária está ativada, verificando que o controlador verde LED está Ativada.

10. Verifique se a ligação de inclusão EBOD com a inclusão primária é bom, verificando que a pista de SA LEDs (quatro por controlador de EBOD) são todas as ON.

>[AZURE.IMPORTANT] Se os cabos de SA estão danificados ou a ligação entre a inclusão EBOD e a inclusão primária é não boa quando ativar o sistema, irá vá para o modo de recuperação. Inicie o [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) , se tal acontecer.

## <a name="turn-off-a-running-device"></a>Desligar um dispositivo em execução

Um dispositivo StorSimple em execução poderá ter de encerrar se está a ser movido, tomadas fora do serviço, ou se tem um componente de funcionar corretamente que precisa de ser substituído. Os passos diferem se o dispositivo StorSimple é uma 8100 ou de um modelo de 8600. O 8100 tem uma única inclusão principal, Considerando que o 8600 é um dispositivo de inclusão de dupla com uma inclusão principal e uma inclusão EBOD. Esta secção fornece detalhes sobre os passos para encerrar um dispositivo em execução.

- [Dispositivo com inclusão principal](#8100a)

- [Dispositivo com inclusão EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo com inclusão principal<a name="8100a"> 

Para encerrar o dispositivo de uma forma ordenada e controlada, pode fazê-lo através do portal do Azure clássico ou através do Windows PowerShell para StorSimple. 

>[AZURE.IMPORTANT] Não encerre um dispositivo em execução, utilizando o botão de energia na parte detrás do dispositivo.
>
>Antes de encerrar o dispositivo, certifique-se de que todos os componentes de dispositivo estão em bom Estados. No portal do Azure clássico, navegue para **dispositivos** > **Manutenção** > **Estado do Hardware**e verifique se o estado de todos os componentes encontra verde. Esta situação for verdadeira apenas para um sistema saudável. Se está a ser encerrar o sistema para substituir um componente de funcionar corretamente, irá ver um falhado (vermelho) ou degradado Estado (amarelo) para o componente respetivos o **Estado do Hardware**.

Depois de aceder ao Windows PowerShell para StorSimple ou portal clássico do Azure, siga os passos no [encerrar um dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo com inclusão EBOD<a name="8600a">

>[AZURE.IMPORTANT] Antes de encerrar a inclusão de principal e a inclusão EBOD, certifique-se de que todos os componentes de dispositivo estão em bom Estados. No portal do Azure clássico, navegue para **dispositivos** > **Manutenção** > **Hardware Estado**e certifique-se de que todos os componentes são saudáveis.

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Para encerrar um dispositivo em execução com inclusão EBOD

1. Siga todos os passos indicados no [encerrar um dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) para a inclusão principal.

2. Depois da inclusão primária é encerrar, encerre o EBOD inversão desativar parâmetros Power e módulo refrigeração (PCM).

3. Para verificar que tem encerrar o EBOD, verifique se todas as luzes na parte detrás da inclusão EBOD estão desativar.

>[AZURE.NOTE] Os cabos de SA são utilizados para ligar a inclusão EBOD para a inclusão principal não devem ser removidos até depois do sistema é encerrar.

## <a name="next-steps"></a>Próximos passos

[Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) se encontrar problemas ao ativar o registo ou encerrar um dispositivo StorSimple.

