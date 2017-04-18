<properties 
   pageTitle="Substituição de componente de hardware StorSimple | Microsoft Azure"
   description="Descreve como com segurança substituir o PCMs, baterias, módulos controlador, EBOD controladores, unidades de disco e quadro de um dispositivo StorSimple."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="storsimple-hardware-component-replacement"></a>Substituição de componente de hardware StorSimple

## <a name="overview"></a>Descrição geral

Os tutoriais de substituição de componente de hardware descrevem os componentes de hardware do seu dispositivo de série do Microsoft Azure StorSimple 8000 e os passos necessários para remover e substituí-los. Este artigo descreve os ícones de segurança, fornece ponteiros para os tutoriais detalhados e lista os componentes que são substituíveis.

>[AZURE.IMPORTANT] Antes de tentar remover ou substituir qualquer componente StorSimple, certifique-se de que reveja as [convenções de ícones de segurança](#safety-icon-conventions) e outras [precauções de segurança](storsimple-safety.md).
 
### <a name="safety-icon-conventions"></a>Convenções de ícones de segurança

A tabela seguinte descreve os ícones de segurança utilizados nestes tutoriais. Paga fechar atenção para estes ícones de segurança à medida que avança através dos passos para remover e substituir componentes do dispositivo.

| Ícone | Texto | Informações adicionais |
|:---- |:---- |:-----------|
|![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png)| **PERIGO!** | Indica uma situação perigosa que, se não evitada, irá resultar em morte ou danos graves. Esta palavra sinal está limitada a situações extremas.|
|![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png)| **AVISO!** | Indica uma situação perigosa que, se não evitada, poderá resultar em morte ou danos graves.|
|![Ícone de atenção](./media/storsimple-hardware-component-replacement/Caution.png)| **ATENÇÃO!** |Indica uma situação perigosa que, se não evitada, poderá resultar em prejuízo secundário ou moderado.|
|![Ícone de aviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **AVISO DE:** | Indica informações consideradas importantes, mas não relacionados com o risco.|
|![Ícone de shock eléctricos](./media/storsimple-hardware-component-replacement/Electric.png) | **Shock eléctricos perigo** | Indica alta tensão.|
|![Ícone de espessura muito carregado](./media/storsimple-hardware-component-replacement/Weight.png)| **Peso muito carregado**| |
|![Ícone sem serviceable peças de utilizador](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **Não existem peças Serviceable de utilizador** | Não acedem a menos que formação corretamente.|
|![Ícone de instruções de leitura](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**Ler todas as instruções pela primeira vez**| |
|![Ícone de risco de sugestão](./media/storsimple-hardware-component-replacement/TipHazard.png)|**Sugestão perigo**| |

### <a name="before-you-begin"></a>Antes de começar

Familiarizar-se com as informações de segurança sobre os ícones do dispositivo e segurança utilizados neste tutorial. Aceda a [com segurança instalar e trabalhar com o seu dispositivo StorSimple](storsimple-safety.md) para obter informações completas. Certifique-se de que reveja as [precauções](storsimple-safety.md#handling-precautions) antes de gerir o seu dispositivo StorSimple. 

Antes de tentar substituir um componente, considere as seguintes informações.

![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) ![Shock eléctricos ícone](./media/storsimple-hardware-component-replacement/Electric.png) **aviso!** 

- Ligue terra si próprio corretamente utilizando um descargas electrostáticas ou mat antiestáticos quando tratamento módulos e componentes do seu dispositivo StorSimple.

- Não toque em qualquer circuitos. Utilize as alças fornecidas e guias enquanto processamento componentes que podem ter expostos circuitos.

![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) ![Repare ícone](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **Aviso:**

Quando substitui um módulo, **Nunca deixar uma cais vazia na parte traseira da inclusão**. Obter um módulo em branco ou substituição antes de remover a peça de problema.

## <a name="hardware-component-replacement-procedures"></a>Procedimentos de substituição de componente de hardware

O dispositivo de série StorSimple 8000 é constituída por vários módulos Plug-in no principal e/ou anexos EBOD. O 8100 tem uma única inclusão principal, Considerando que o 8600 é um dispositivo de inclusão dupla com uma inclusão principal e uma inclusão EBOD.

Os componentes de hardware principal no seu dispositivo são resumidos nas seguintes tabelas. Clique na ligação na coluna **processo de substituição** para ir para o tutorial associado.

|Componentes|# Apresentar|Módulo plug-in?|Procedimento de substituição
|:---------|:--------|:--------------|:---------------------|
| Quadro|1|N|[Substituir o quadro no seu dispositivo StorSimple](storsimple-chassis-replacement.md) |
|Controladores principais|2|Sim| [Substituir um módulo de controlador no seu dispositivo StorSimple](storsimple-controller-replacement.md) |
|764W Power e de refrigeração módulos (PCMs)|2|Sim| [Substituir uma Power e refrigeração módulo no seu dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
|Carregador de cópia de segurança|2|Sim| [Substituir o módulo carregador de cópia de segurança no seu dispositivo StorSimple](storsimple-battery-replacement.md) |
|Unidades de disco|12|Sim| [Substituir uma unidade de disco no seu dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabela 1** Componentes de hardware da inclusão principal

A inclusão de principal e a inclusão EBOD diferem nos seus módulos e/s. Além disso, os PCMs tem potência diferente. PCMs na inclusão primária são 764 J, Considerando que são apresentados na inclusão EBOD 580 W. PCMs na inclusão primária também contêm um módulo de cópia de segurança baterias.

|Componentes|# Apresentar|Módulo plug-in?| Procedimento de substituição
|:---------|:--------|:--------------|:---------------------|
|Quadro|1|N| [Substituir o quadro no seu dispositivo StorSimple](storsimple-chassis-replacement.md) |
|Controladores de EBOD|2|Sim| [Substituir um controlador de EBOD no seu dispositivo StorSimple](storsimple-ebod-controller-replacement.md) |
|580W Power e de refrigeração módulos (PCMs)|2|Sim| [Substituir uma Power e refrigeração módulo no seu dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
|Unidades de disco|12|Sim| [Substituir uma unidade de disco no seu dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabela 2** Componentes de hardware da inclusão EBOD

Os módulos Plug-in no dispositivo estão realçados na seguinte frente e diagramas traseiros. Pode utilizar estes diagramas para determinar a localização dos vários Plug-in módulos, se for necessária a substituição. O diagrama frente mostra as unidades de disco e os diagramas da inclusão EBOD e a apresentação de inclusão primária traseiros módulos Plug-in.

![Frontplane do dispositivo com unidades de disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** À frente do dispositivo

|Etiqueta|Descrição|
|:----|:----------|
|0 - 11|Unidades de disco (total de 12)|

A inclusão de principal e a inclusão EBOD tem módulos do unidade carrier. Quadro aloja aos doze 3,5" unidades de disco dispostas num formato de 3 por 4.

![Inserção de módulos de inclusão principal de dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Verso a inclusão principal

|Etiqueta|Descrição|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controlador de 0|
|4|Controlador de 1|

![Inserção de módulos Plug-in do dispositivo EBOD inclusão](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** Verso a inclusão EBOD

|Etiqueta|Descrição|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Controlador de EBOD 0|
|4|Controlador de EBOD 1|

## <a name="field-replaceable-units"></a>Unidades substituíveis em campo

As seguintes unidades de substituíveis campo (FRU) estão disponíveis para o seu dispositivo StorSimple:

- Quadro (incluindo o painel de operações integrada)

- 764 W AC PCM

- 580 W AC PCM

- Unidade de disco rígido com módulo carrier de unidade

- Módulo de controlador

- Módulo de controlador EBOD

- Módulo de baterias cópia de segurança

- Bastidor montagem rail kit

Utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para ordenar qualquer uma destas unidades de substituição.

## <a name="next-steps"></a>Próximos passos

Reveja todas as [informações de segurança](storsimple-safety.md) antes de tentar substituir um componente de hardware StorSimple.
