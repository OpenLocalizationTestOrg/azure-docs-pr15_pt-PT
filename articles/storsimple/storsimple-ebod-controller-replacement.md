<properties 
   pageTitle="Substituir um controlador de StorSimple EBOD | Microsoft Azure"
   description="Explica como remover e substituir ou em ambos os controladores EBOD num dispositivo StorSimple 8600."
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

# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Substituir um controlador de EBOD no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial explica como substituir um módulo de controlador EBOD cablagem no seu dispositivo do Microsoft Azure StorSimple. Para substituir um módulo de controlador EBOD, tem de:

- Remover o controlador de EBOD cablagem
- Instalar um novo controlador de EBOD

Antes de começar, considere as seguintes informações:

- Módulos EBOD em branco tem de ser inseridos no todas as faixas não utilizadas. A inclusão não será arrefecer corretamente se uma ranhura fica aberta.

- O controlador de EBOD está quente mudança e podem ser removidos ou substituída. Não remova um módulo falhado até ter substituição. Quando iniciar o processo de substituição, tem de conclui-la dentro de 10 minutos.

>[AZURE.IMPORTANT] Antes de tentar remover ou substituir qualquer componente StorSimple, certifique-se de que reveja as [convenções de ícones de segurança](storsimple-safety.md#safety-icon-conventions) e outras [precauções de segurança](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Remover um controlador de EBOD

Antes de substituir o módulo de controlador EBOD falhado no seu dispositivo StorSimple, certifique-se de que o outro módulo do controlador EBOD está ativa e em execução. O procedimento seguinte e a tabela explicam como remover o módulo do controlador EBOD.

#### <a name="to-remove-an-ebod-module"></a>Para remover um módulo EBOD

1. Abra o portal do clássico Azure.

2. Navegue para **dispositivos** > **Manutenção** > **Estado do Hardware**e verifique o estado da LED para o módulo do controlador EBOD activo é verde e o LED do módulo de controlador EBOD falhado de fique a vermelho.

3. Localize o módulo de controlador EBOD falhado na parte posterior do dispositivo.

4. Remova os cabos de que se ligam o módulo do controlador EBOD para o controlador de antes de efetuar o módulo EBOD fora do sistema.

5. Anote a porta SA exata do módulo controlador EBOD que foi ligado ao controlador de. Vai ser necessário restaurar o sistema para esta configuração depois de substituir o módulo EBOD. 

    >[AZURE.NOTE] Normalmente, este será A porta que é identificada como **anfitrião** no diagrama seguinte.

    ![Controlador de inserção de EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figura 1** Verso módulo EBOD

  	|Etiqueta|Descrição|
  	|:----|:----------|
  	|1|Falhas LED|
  	|2|Power LED|
  	|3|Conectores de SA|
  	|4|LEDs SA|
  	|5|Portas série fábrica apenas para utilização|
  	|6|Porta (anfitrião no)|
  	|7|Porta B (anfitrião saída)|
  	|8|Porta C (apenas para utilização de fábrica do mesmo)|

## <a name="install-a-new-ebod-controller"></a>Instalar um novo controlador de EBOD

O procedimento seguinte e a tabela explicam como instalar um módulo de controlador EBOD no seu dispositivo StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Para instalar um controlador de EBOD

1. Verifique o dispositivo EBOD para danos, especialmente ao conector de interface. Não instale o novo controlador de EBOD se qualquer PIN é em ângulo.

2. Com fechos na posição aberto, deslize o módulo a inclusão até os fechos participar.

    ![Instalar o controlador de EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figura 2**  Instalar o módulo do controlador EBOD

3. Feche o fecho. Deverá ouvir um clique, tal como o fecho realiza.

    ![Libertar bloqueios EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figura 3**  Os bloqueios de módulo EBOD de fecho

4. Voltar a ligar os cabos de. Utilize a configuração exata que estava a apresentar antes da substituição. Consulte os seguintes diagrama e tabela para obter detalhes sobre como ligar os cabos de.

    ![Cabo dispositivo 4U para o power](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figura 4**. A reestabelecer ligação cabos

  	|Etiqueta|Descrição|
  	|:----|:----------|
  	|1|Inclusão principal|
  	|2|PCM 0|
  	|3|PCM 1|
  	|4|Controlador de 0|
  	|5|Controlador de 1|
  	|6|Controlador de EBOD 0|
  	|7|Controlador de EBOD 1|
  	|8|Inclusão EBOD|
  	|9|Unidades de distribuição Power|

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).
