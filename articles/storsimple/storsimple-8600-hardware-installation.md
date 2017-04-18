<properties
   pageTitle="Instalar o seu dispositivo StorSimple 8600 | Microsoft Azure"
   description="Descreve como descompactar, bastidor montagem e cabo dispositivo StorSimple 8600 antes de implementar e configurar o software."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Descompactar, montagem bastidor e cabo dispositivo StorSimple 8600

## <a name="overview"></a>Descrição geral
O 8600 do Microsoft Azure StorSimple é um dispositivo de inclusão dupla e é constituído por uma principal e uma inclusão EBOD. Neste tutorial explica como descompactar, bastidor montagem e hardware do dispositivo cabo a 8600 StorSimple antes de configuram o software de StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Descompactar dispositivo StorSimple 8600

Os passos seguintes fornecem instruções limpar, detalhadas sobre como descompactar o seu dispositivo de armazenamento StorSimple 8600. Este dispositivo é enviado nas duas caixas, um para a inclusão de principal e outro para a inclusão EBOD. Estas duas caixas, em seguida, são colocadas numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para descompactar o seu dispositivo

Antes de descompactar seu dispositivo, reveja as seguintes informações.


![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone espessura grossa](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

1. Certifique-se de que tem duas pessoas disponíveis para gerir a espessura do dispositivo se estão processar manualmente. Uma inclusão completamente configurado, pode avaliar 32 kg (kg 70.).
1. Coloque a caixa numa superfície simples e nível.

Em seguida, conclua os passos seguintes para descompactar o seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para descompactar o seu dispositivo

1. Inspecionar a caixa e espuma embalagem para crushes, peças, água danos ou qualquer outro danos óbvios. Se a caixa ou na embalagem extremamente está danificada, não abra a caixa. Utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para o ajudar a determinar se o dispositivo está em bom estado de funcionamento.

2. Abrir a caixa externa e tome as duas caixas correspondentes principal e anexos EBOD. Agora pode descompactar primário e anexos EBOD. A figura seguinte mostra a vista descompactada de um dos anexos.

    ![Descompactar o dispositivo de armazenamento](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **Vista descompactada do seu dispositivo de armazenamento**

     Etiqueta | Descrição
     ----- | -------------
     1     | Caixa de embalagem
     2     | Cabos SA (no tabuleiro de acessórios e cabos)
     3     | Espuma inferior
     4     | Dispositivo
     5     | Espuma superior
     6     | Caixa de acessório

3. Depois de descompactar as duas caixas, certifique-se de que tem:

  - 1 inclusão principal (a inclusão de principal e a inclusão EBOD estão duas caixas em separado)
  - 1 inclusão EBOD
  - cabos, 2 em cada caixa de alimentação de 4
  - 2 SA cabos (para ligar a inclusão primária a inclusão EBOD)
  - 1 cruzado cabo Ethernet
  - 2 cabos de consola em série
  - 1 conversor de série USB para um acesso série
  - 4 QSFP-para-SFP + placas para utilização com 10 interfaces de rede GbE
  - 2 bastidor montagem kits (carris de lado 4 com montagem hardware, 2 para a inclusão de principal e a inclusão EBOD), 1 em cada caixa
  - Introdução a documentação de introdução

    Se não receber qualquer um dos itens enumerados acima, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md).  

O passo seguinte é bastidor-montagem o seu dispositivo.

## <a name="rack-mount-your-storsimple-8600-device"></a>Montagem de bastidor dispositivo StorSimple 8600

Siga os passos para instalar o seu dispositivo de armazenamento StorSimple 8600 num bastidor 19 polegadas padrão com frente e de trás mensagens. Este dispositivo vem com duas anexos: uma inclusão principal e uma inclusão EBOD. Ambos destes tem de ser bastidor dispostos.

A instalação é composta por vários passos, cada um dos quais é abordada nos seguintes procedimentos.

> [AZURE.IMPORTANT]
Dispositivos StorSimple tem de ser dispostos em bastidor para uma operação adequada.



### <a name="site-preparation"></a>Preparação do site

Os anexos tem de estar instalados num bastidor 19 polegadas padrão que tenha frente e traseiros mensagens. Utilize o seguinte procedimento para preparar a instalação de bastidor.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o site da instalação de bastidor

1. Certifique-se de que a principal e anexos EBOD estão pousio com segurança sobre uma superfície de trabalho simples, estável e nível (ou semelhante).

2. Certifique-se de que o site onde pretende configurar tem padrão CA a partir de uma fonte independente ou um bastidor Power distribuição unidade (PDU) com um alimentação sem interrupção (UPS).

3. Certifique-se de que ranhura (2 X 2U) de um 4U está disponível no bastidor na qual pretende os anexos de montagem.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone espessura grossa](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

 Certifique-se de que tem duas pessoas disponíveis para gerir a espessura se estão a processar a configuração de dispositivo manualmente. Uma inclusão completamente configurado, pode avaliar 32 kg (kg 70.).

### <a name="rack-prerequisites"></a>Pré-requisitos de bastidor

Os anexos foram concebidos para a instalação num bastidor 19 polegadas padrão cab com:

- Profundidade mínima de polegadas 27.84 do bastidor publicar a mensagem
- Peso máximo de 32 kg para o dispositivo
- Pressão anterior máxima de 5 Pascal (indicador de água de 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Kit de rail bastidor montagem

Um conjunto de montagem carris será fornecido para utilização com o cab bastidor 19 polegadas. Os trilhos foram testados para processar a espessura de inclusão máximo. Estes carris também irão permitir a instalação de vários anexos sem perda de espaço dentro de bastidor. Instale a inclusão EBOD pela primeira vez.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Para instalar a inclusão EBOD nos trilhos

2. Execute este passo só se carris internas não são instalados no seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Se não estiverem instalados carris, em seguida, instale os diapositivos rail esquerda e direita rail para os lados do quadro inclusão. Estes anexar utilizando seis parafusos métricos em cada lado. Para ajudar com a orientação, diapositivos rail são marcados **LH – frente** e **RH – em frente**e final que é afixada para trás da inclusão tem das extremidades afunilada.

    ![Como anexar diapositivos rail a quadro de inclusão](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Anexar diapositivos rail para os lados da inclusão**

    Etiqueta | Descrição
    ----- | -----------
    1     | M 3 x 4 parafusos de cabeça de botão
    2     | Diapositivos de quadro

3. Anexe a rail para a esquerda e montagens de rail direita para os membros de vertical armário bastidor. Os parênteses são marcados **LH**, **RH**e **Este lado para cima** para o orientar orientação correta.

4. Localize o PIN rail à frente e atrás da assemblagem rail. Expandir rail para se ajustar entre as mensagens de bastidor e inserir o PIN para a frente e bastidor traseira post buracos de membro vertical. Certifique-se de que a assemblagem rail é nível.

5. Seguro assemblagem rail a bastidor membros verticais utilizando duas dos parafusos métricos fornecidos. Utilize um parafusos no parte da frente e outra na parte traseira.

6. Repita estes passos para outras assemblagem rail.

     ![Como anexar diapositivos rail a bastidor armário](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Como anexar assemblagem rail a bastidor**

     Etiqueta | Descrição
     ----- | -----------
     1     | Parafusos fixação
     2     | Quadrado Furador frente bastidor post parafusos
     3     | PIN de localização rail frontal para a esquerda
     4     | Parafusos fixação
     5     | Esquerda rail traseira localização PIN

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montagem a inclusão EBOD no bastidor

Utilizar os trilhos bastidor que foram instalados apenas, execute os passos seguintes para montagem a inclusão EBOD no bastidor.

#### <a name="to-mount-the-ebod-enclosure"></a>Para montagem a inclusão EBOD

1. Com um assistente levantar o inclusão e alinhá-lo com os trilhos bastidor.

2. Insira cuidadosamente a inclusão os trilhos e, em seguida, enviá-lo completamente para bastidor Cab.

    ![Inserir o dispositivo no bastidor](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montagem inclusão no bastidor**

3. Remova as tecla caps rebordo para a esquerda e direita da frente solicitando as tecla caps gratuitos. As tecla caps rebordo ajustam simplesmente para as flanges.

4. Proteger a inclusão para bastidor instalando um parafusos de cabeça chave fornecido através de cada rebordo, esquerdo e direito.

4. Instale as maiúsculas rebordo premindo-las para a posição e ajuste-los para um local.

     ![Instalar o rebordo maiúsculas](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Instalar as maiúsculas rebordo**

     Etiqueta | Descrição
     ----- | -----------
     1     | Parafusos fixação de inclusão


### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montagem a inclusão principal no bastidor

Depois de terminar de montagem a inclusão EBOD, terá de montagem a inclusão de primária seguindo os mesmos passos.

> [AZURE.NOTE]
>
> - É possível ter alguns faixas vazias no bastidor entre a inclusão de principal e a inclusão EBOD.
> - Utilize o cabo de SA fornecido 2m para ligar a inclusão principal para a inclusão EBOD.
> - Existem restrições na posição relativa da unidade de cabeça para a unidade EBOD. Por conseguinte, a inclusão primária pode ser colocado no ranhura superior e a inclusão EBOD abaixo — ou vice versa.

O passo seguinte é cabo o seu dispositivo, para poder, rede e acesso série.

## <a name="cable-your-storsimple-8600-device"></a>Cabo dispositivo StorSimple 8600

Os procedimentos seguintes explicam como cabo o seu dispositivo, StorSimple 8600 para power, rede e ligações série.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar a cabo o seu dispositivo, terá de:

- A inclusão de principal e inclusão EBOD, completamente descompactados
- 4 os cabos de alimentação (2 cada para a página principal e a inclusão EBOD) que veio incluída com o seu dispositivo
- 2 cabos de SA fornecidos com o dispositivo para ligar a inclusão EBOD a inclusão principal
- Acesso a 2 Power distribuição unidades (PDUs que) (recomendado)
- Cabos de rede
- Fornecidos cabos série
- Conversor de série USB com o controlador adequado instalado no seu PC (se necessário)
- Fornecidos 4 QSFP-para-SFP + placas para utilização com 10 interfaces de rede GbE
- [Suportada hardware para as 10 interfaces de rede GbE no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SA e Power cablagem

O dispositivo tem uma inclusão principal e uma inclusão EBOD. Isto requer que as unidades de estar ligado em conjunto para conectividade de série anexado SCSI (SA) e power.

Quando configurar este dispositivo pela primeira vez, execute os passos para SA cablagem pela primeira vez e, em seguida, conclua os passos para power cablagem.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Cabos de rede

O dispositivo está numa configuração ativo suspensão: em qualquer altura determinada, um módulo de controlador está ativo e todas as operações de disco e de rede enquanto o módulo de controlador de processamento está no modo de espera. Se ocorrer uma falha de controlador, o controlador de espera ativa imediatamente e continua a todas as operações de disco e funcionamento em rede.

Para suportar este activação de pós-falha de controlador redundantes, tem de cabo à rede de dispositivo, conforme mostrado nos passos seguintes.

#### <a name="to-cable-for-network-connection"></a>A cabo para ligação de rede

1. O dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps e duas 10 Gbps Ethernet portas. Consulte a seguinte ilustração para identificar as portas de dados no inserção do seu dispositivo.

     ![Inserção de dispositivo 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Volta do seu dispositivo, mostrando as portas de dados**

     Etiqueta   | Descrição
     ------- | -----------
     0,1,4,5 |  Interfaces de rede GbE 1
     2,3     | 10 interfaces de rede GbE
     6       | Portas série



1. Consulte o artigo o diagrama seguinte para cabos de rede. (A configuração de rede mínima é apresentada por linhas azuis sólidas. Para elevada disponibilidade e desempenho, configuração adicionais necessária é apresentada pelas linhas ponteadas.)

![Cabo dispositivo 4U para a rede](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cablagem para o seu dispositivo de rede**

Etiqueta | Descrição
----- | -----------
A    | LAN com acesso à Internet
B    | Controlador de 0
C    | PCM 0
D    | Controlador de 1
"E"    | PCM 1
F    | Controlador de EBOD 0
G    | Controlador de EBOD 1
H, POSSO  | Anfitriões (por exemplo, servidores de ficheiro)
0-5  | Interfaces de rede
6    | Inclusão principal
7    | Inclusão EBOD

Quando cablagem o dispositivo, a configuração mínima requer:


- Ligado, pelo menos, duas interfaces de rede em cada controlador de com uma para acesso à nuvem e outra para iSCSI. Os dados 0 porta automaticamente está ativada e configurada através da consola de série do dispositivo. Para além dos dados 0, o outro porta de dados também tem de ser configurado através do portal do Azure clássico. Neste caso, ligar dados 0 porta para a LAN principal (rede com acesso à Internet). As outras portas de dados podem ser ligadas ao segmento SAN/iSCSI LAN (VLAN) da rede, dependendo do papel pretendido.

- Interfaces idênticos em cada controlador ligado à rede mesmo para garantir a disponibilidade caso ocorra um controlador activação pós-falha. Por exemplo, se optar por ligar dados 0 e 3 de dados para um dos controladores, terá de ligar os dados correspondentes 0 e os dados 3 no outro controlador de.

Tenha em conta para elevada disponibilidade e desempenho:


- Sempre que possível, configure um par de interface de rede para o acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendado) em cada controlador.

- Sempre que possível, ligue interfaces de rede do controlador de cada aos dois parâmetros diferentes para garantir a disponibilidade contra uma falha de parâmetro. A figura ilustra a duas 10 GbE interfaces de rede, dados 2 e 3 de dados, a partir de cada controlador ligada a duas parâmetros diferentes. Para obter mais informações, consulte as **interfaces de rede** nos [requisitos de elevada disponibilidade para o seu dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Se utilizar SFP + menos com as interfaces de rede GbE 10, utilize o QSFP fornecido-SFP + placas. Para obter mais informações, aceda ao [suportados hardware de 10 GbE para interfaces de rede no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### <a name="serial-port-cabling"></a>Porta série cablagem

Execute os passos seguintes para cabo à porta série.

#### <a name="to-cable-for-serial-connection"></a>A cabo para ligação série

1. O dispositivo tem uma porta série em cada controlador de que está identificado por um ícone de chave inglesa. Para localizar as portas série, consulte a ilustração que apresenta os dados portas na parte detrás do seu dispositivo.

2. Identifique o controlador de ativo no seu dispositivo de inserção. Um LED azul intermitente indica que o controlador de está ativo.

3. Utilize o cabo série fornecido (se necessário, o conversor USB série para o seu portátil) e ligue o seu consola de computador (com emulação de terminal para o dispositivo) ou à porta série do controlador de ativo.

4. Instale os controladores de série USB (fornecidos com o dispositivo) no seu computador.

5. Configure a ligação série da seguinte forma:
   - transmissão 115.200
   - 8 bits de dados
   - 1 bit de paragem
   - Sem paridade
   - Controlo de fluxo de configurada como **nenhuma**

6. Certifique-se de que a ligação está a funcionar, premindo Enter na consola. Deverá aparecer um menu de consola série.

> [AZURE.NOTE] **Lights-Out gestão:** Quando o dispositivo está instalado num centro de dados remoto ou numa sala de computador com acesso limitado, certifique-se de que as ligações em série para ambos os controladores sempre estão ligadas a um comutador da consola série ou equipamento semelhante. Esta opção permite-controlo remoto de fora de banda e operações de suporte em caso de interrupção de rede ou falhas inesperadas.

Concluiu cablagem o seu dispositivo, para poder, acesso de rede e ligação série. O próximo passo é configurar o software no seu dispositivo.

## <a name="next-steps"></a>Próximos passos

Agora está pronto para [Implementar e configurar o seu dispositivo de StorSimple no local](storsimple-deployment-walkthrough-u2.md).
