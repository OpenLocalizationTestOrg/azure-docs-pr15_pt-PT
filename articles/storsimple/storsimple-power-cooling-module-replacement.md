<properties 
   pageTitle="Substituir um PCM no seu dispositivo StorSimple | Microsoft Azure"
   description="Explica como remover e substituir a Power e o módulo refrigeração (PCM) no seu dispositivo StorSimple"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Substituir uma Power e refrigeração módulo no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

O Power e refrigeração módulo (PCM) no seu dispositivo do Microsoft Azure StorSimple é composta por uma fonte de alimentação e de refrigeração ventoinhas que são controladas através do principal e anexos EBOD. Existe apenas um modelo de PCM está certificado para cada inclusão. A inclusão primária está certificada para um PCM W 764 e a inclusão EBOD está certificada para um PCM W 580. Apesar de PCMs para a inclusão de principal e a inclusão EBOD são diferentes, o procedimento de substituição é idêntico.

Este tutorial explica como:

- Remover um PCM
- Instalar substituição PCM

>[AZURE.IMPORTANT] Antes de remover e substituir um PCM, reveja as informações de segurança no [StorSimple hardware de substituição do componente](storsimple-hardware-component-replacement.md).

## <a name="before-you-replace-a-pcm"></a>Antes de substituir um PCM

Tenha em atenção os seguintes problemas de importantes antes de substituir o PCM:

- Se a fonte de alimentação de PCM falhar, deixe o módulo cablagem instalado, mas remover o cabo de alimentação. A ventoinha irão continuar a receber power da inclusão e continuar fornecer refrigeração inicial maiúscula. Se a ventoinha falhar, o PCM tem de ser substituído imediatamente.

- Antes de remover o PCM, desligue ao PCM ao desativar as principal (se existir) ou removendo física o cabo de alimentação. Este procedimento fornece um aviso ao seu sistema de que está iminente um encerramento de energia.

- Certifique-se de que as outras PCM está funcional para o funcionamento do sistema continuação antes de substituir o PCM cablagem. Um PCM cablagem deve ser substituído por um PCM completamente operacional mais cedo possível.

- Substituição de módulo PCM demora apenas alguns minutos a concluir, mas têm de ser concluído 10 minutos depois de remover o PCM falhado para impedir que ao aquecimento.

- Tenha em atenção que substituição 764 W PCM módulos enviados a partir da fábrica não contenham o módulo carregador de cópia de segurança. Terá de remover o carregador do seu PCM cablagem e, em seguida, insira-o no módulo substituição antes de efetuar a substituição. Para obter mais informações, consulte o artigo como [remover e inserir um módulo de baterias cópia de segurança](storsimple-battery-replacement.md).


## <a name="remove-a-pcm"></a>Remover um PCM

Siga estas instruções quando estiver pronto para remover um Power e módulo refrigeração (PCM) a partir do seu dispositivo do Microsoft Azure StorSimple.

>[AZURE.NOTE] Antes de remover o PCM, certifique-se de que tem substituição correta (764 W para a inclusão principal) ou 580 W para a inclusão EBOD.

#### <a name="to-remove-a-pcm"></a>Para remover um PCM

1. No portal do Azure clássico, clique em **dispositivos** > **Manutenção** > **Hardware Estado**. Verificar o estado dos componentes PCM em **Componentes partilhados** para identificar qual PCM falhou:

     - Se tiver falhado uma fonte de alimentação no PCM 0, o estado de **Alimentação no PCM 0** estará a vermelho.

     - Se tiver falhado uma fonte de alimentação no PCM 1, o estado de **Alimentação no PCM 1** estará a vermelho.

     - Se tiver falhado ventoinha no PCM 1, o estado de **refrigeração 0 para PCM 0** ou **1 de refrigeração para PCM 0** estará a vermelho.

2. Localize o PCM falhado na parte detrás da inclusão principal. Se estiver a utilizar um modelo de 8600, identificar a inclusão primária verificando o número de identificação sistema de unidade no painel frontal apresentação LED. A predefinição que ID da unidade apresentada na inclusão primária é **00**, Considerando que a predefinição que ID da unidade apresentada na inclusão EBOD é **01**. O diagrama e a tabela seguinte explicam painel frontal da apresentação LED do.

    ![ID do sistema no painel OPS frontal](./media/storsimple-power-cooling-module-replacement/IC740991.png)

     **Figura 1** Painel frontal do dispositivo  

  	|Etiqueta|Descrição|
  	|:---|:-----------|
  	|1|Botão Desativar som|
  	|2|Power de sistema|
  	|3|Falhas de módulo|
  	|4|Falhas de lógica|
  	|5|Visualização de ID de unidade|

3. O indicador de monitorização LEDs no fundo de inclusão primária pode também ser utilizado para identificar o PCM cablagem. Consulte os seguintes diagrama e tabela para compreender como utilizar os LEDs para localizar o PCM cablagem. Por exemplo, se o LED correspondente a **Falhar ventoinha** é feixe, a ventoinha falhou. Da mesma forma, se o LED correspondente à **AC falhas** é feixe, alimentação falhou. 

    ![Inserção de indicador de monitorização do dispositivo PCM LEDs](./media/storsimple-power-cooling-module-replacement/IC740992.png)

     **Figura 2** Verso PCM com indicador de LEDs

  	|Etiqueta|Descrição|
  	|:---|:-----------|
  	|1|Falha de energia AC|
  	|2|Falha de ventoinha|
  	|3|Falhas de baterias|
  	|4|PCM OK|
  	|5|Falha de energia do Centro de dados|
  	|6|Carregador Saudável|

4. Referem-se para o diagrama seguinte da parte posterior do dispositivo StorSimple para localizar o módulo PCM falhado. PCM 0 é à esquerda e 1 de PCM é à direita. A tabela que se segue explica os módulos.

     ![Inserção de módulos de inclusão principal de dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)

     **Figura 3** Verso dispositivo com módulos Plug-in 

  	|Etiqueta|Descrição|
  	|:---|:-----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

5. Desative o PCM cablagem e desligar o cabo de aprovisionamento do power. Agora pode remover o PCM.

6. Perceber o fecho e o lado da alça de PCM entre o seu miniatura e indicador e encaixá-las em conjunto para abrir a alça.

    ![Abrir o identificador PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)

    **Figura 4** Abrir a alça de PCM

7. Agarre a alça e remova o PCM.

    ![Remover o dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)

    **Figura 5** Remover o PCM

## <a name="install-a-replacement-pcm"></a>Instalar substituição PCM

Siga estas instruções para instalar um PCM no seu dispositivo StorSimple. Certifique-se de que inseriu o módulo carregador de cópia de segurança antes de instalar a substituição PCM (aplicável ao 764 PCMs W apenas). Para obter mais informações, consulte o artigo como [remover e inserir um módulo de baterias cópia de segurança](storsimple-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Para instalar um PCM

1. Certifique-se de que tem a substituição correta PCM para este inclusão. A inclusão primária tem um PCM W 764 e a inclusão EBOD necessita de um PCM W 580. Não deve tentar utilizar o PCM W 580 na inclusão principal ou o PCM W 764 na inclusão EBOD. A imagem seguinte mostra onde identificar estas informações sobre a etiqueta que se encontra afixada para o PCM.

    ![Etiqueta do dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740973.png)

    **Figura 6** Etiqueta PCM

2. Verificar existência de danos para inclusão, com especial atenção para os conectores. 
                                        
    >[AZURE.NOTE] **Não instale o módulo se qualquer PIN de conexão é em ângulo.**

3. Com a alça PCM na posição aberta, deslize o módulo para a inclusão.

    ![Instalar o dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)

    **Figura 7** Instalar o PCM

4. Feche manualmente a alça de PCM. Deverá ouvir um clique como realiza o alça de fecho. 
                                        
    >[AZURE.NOTE] Para se certificar de que tem interessada o PIN de conexão, pode tug levemente a alça sem soltar o fecho. Se o PCM diapositivos de saída, implica que o fecho foi fechado antes das conexões interessada.

5. Ligue os cabos de energia para a fonte de alimentação e para o PCM.

6. Proteger o ocular fardos isenção. 

7. Ative o PCM.

8. Certifique-se de que a substituição foi efetuada com êxito: no portal Azure clássico do seu serviço de Gestor de StorSimple, navegue para **dispositivos** > **Manutenção** > **Hardware Estado**. Em **Componentes partilhados**, o estado do PCM deve ser verde. 
                                        
    >[AZURE.NOTE] Poderá demorar alguns minutos para a substituição PCM a inicialização completamente.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).
