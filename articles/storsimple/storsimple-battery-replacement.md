<properties 
   pageTitle="Substituir baterias num dispositivo StorSimple | Microsoft Azure"
   description="Descreve como remover, substituir e manter o módulo carregador de cópia de segurança no seu dispositivo StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Substituir o módulo carregador de cópia de segurança no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

A inclusão primária Power e refrigeração módulo (PCM) no seu dispositivo do Microsoft Azure StorSimple tem um pacote de baterias adicionais. Este pacote fornece power para que o dispositivo StorSimple pode guardar os dados se existir perda de energia AC a inclusão principal. Este pacote de baterias está designado como o *módulo carregador de cópia de segurança*. Módulo de cópia de segurança baterias existe apenas para a inclusão principal no seu dispositivo StorSimple (a inclusão EBOD não contém um módulo de baterias cópia de segurança). 

Este tutorial explica como:

- Remover o módulo baterias cópia de segurança 
- Instalar um novo módulo de baterias cópia de segurança
- Manter o módulo carregador de cópia de segurança

>[AZURE.IMPORTANT] Antes de remover e substituir um módulo de baterias cópia de segurança, reveja as informações de segurança na [introdução a substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-backup-battery-module"></a>Remover o módulo carregador de cópia de segurança

O módulo carregador de cópia de segurança para o seu dispositivo StorSimple é uma unidade substituíveis em campo. Antes de ser instalada no PCM, o módulo de baterias deve ficar armazenado na sua embalagem original. Execute os seguintes passos para remover a cópia de segurança baterias.

#### <a name="to-remove-the-backup-battery-module"></a>Para remover o módulo carregador de cópia de segurança

1. No portal do Azure clássico, aceda a **dispositivos** > **Manutenção** > **Hardware Estado**. Em **Componentes partilhados**, veja o estado da baterias.

2. Identifique PCM na qual a baterias falhou. Figura 1 mostra parte de trás do dispositivo StorSimple.

    ![Inserção de dispositivo primário inclusão módulos](./media/storsimple-battery-replacement/IC740994.png)

    **Figura 1** Verso mostrando módulos PCM e controlador de dispositivo principal

  	|Etiqueta|Descrição|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

    Como é mostrado pelo número 3 na figura 2, o indicador de monitorização LED no PCM 0, que corresponde a **Falha de baterias** deve feixe.

    ![Inserção de dispositivo PCM monitorização LEDs](./media/storsimple-battery-replacement/IC740992.png)

    **Figura 2** Verso PCM a mostrar o indicador de monitorização LEDs

  	|Etiqueta|Descrição|
  	|:---|:-----------|
  	|1|Falha de energia AC|
  	|2|Falha de ventoinha|
  	|3|Falhas de baterias|
  	|4|PCM OK|
  	|5|Falha de energia do Centro de dados|
  	|6|Carregador Saudável|

3. Para remover PCM com uma falha baterias, siga os passos no [Remover um PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).

4. Com o PCM removido, levantar e rodar a alça de módulo baterias para cima, como indicado na figura seguinte e serem por excesso para remover a baterias.

    ![Remover carregador de PCM](./media/storsimple-battery-replacement/IC741019.png)

    **Figura 3** Remover o carregador do PCM

5. Coloque o módulo na unidade de campo substituíveis embalagem.

6. Regresse a unidade danificada para a Microsoft para servir e que processam inicial maiúscula.

## <a name="install-a-new-backup-battery-module"></a>Instalar um novo módulo de cópia de segurança baterias

Execute os passos seguintes para instalar o módulo de baterias de substituição no PCM na inclusão principal do seu dispositivo StorSimple.

#### <a name="to-install-the-battery-module"></a>Para instalar o módulo de baterias

1. Coloque o módulo baterias cópia de segurança a orientação adequada no PCM.

2. Prima para baixo a alça de módulo baterias tudo para sentar a conexão.

3. Substitua PCM na inclusão principal, seguindo as diretrizes [substituir um Power e refrigeração módulo no seu dispositivo StorSimple](storsimple-power-cooling-module-replacement.md).

4. Depois da substituição estiver concluída, aceda a **dispositivos** > **Manutenção** > **Estado de Hardware** no portal do Azure clássico. Verifique o estado de baterias para se certificar de que a instalação foi efetuada com êxito. Um estado verde indica que o baterias está em bom estada.

## <a name="maintain-the-backup-battery-module"></a>Manter o módulo carregador de cópia de segurança

No seu dispositivo StorSimple, o módulo de cópia de segurança baterias fornece power para o controlador de durante um evento de perda de energia. Permite que o dispositivo StorSimple guardar dados críticos antes de encerrar de uma forma controlada. Com duas baterias completamente carregadas nas PCMs, o sistema pode processar dois eventos de perda consecutivas.

No portal do Azure clássico, o **Estado do Hardware** na página **Manutenção** indica se a baterias está danificada ou o ciclo de vida se aproximação. O estado da pilha é indicado por **pilhas no PCM 0** ou **baterias no PCM 1** em **Componentes partilhados**. Esta página irá mostrar um Estado de **DEGRADADO** para aproximação do ciclo de vida e **falhou** para o fim de vida de instalações atingido. 

>[AZURE.NOTE] A baterias podem comunicar **falhou** quando simplesmente precisa de ser cobrada.
 
Se o estado de **DEGRADADO** for apresentada, recomendamos que o curso de ação seguinte:

- O sistema poderá ter ocorrido uma falha de energia recentes ou as baterias poderão a ser submetido a manutenção periódica. Observe o sistema de 12 horas antes de continuar.

    - Se o estado ainda **DEGRADADO** após 12 horas de ligação contínua a potência AC com os controladores e PCMs em execução, em seguida, a baterias tem de ser substituído. Inicie o [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para um módulo de cópia de segurança carregador de substituição.

    - Se o estado de ficar OK depois de 12 horas, a baterias está operacional e -necessário apenas uma taxa de manutenção.

- Se não tiver havido uma perda associada de corrente e o PCM é ativado e ligado a potência AC, a baterias tem de ser substituído. [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) para encomendar um módulo de cópia de segurança carregador de substituição.

>[AZURE.IMPORTANT] Dispor da baterias falha de acordo com regulamentações nacionais e regionais. 

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).
