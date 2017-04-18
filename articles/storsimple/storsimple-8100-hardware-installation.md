<properties
   pageTitle="Instalar o seu dispositivo StorSimple 8100 | Microsoft Azure"
   description="Descreve como descompactar, bastidor montagem e cabo dispositivo StorSimple 8100 antes de implementar e configurar o software."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Descompactar, montagem bastidor e cabo dispositivo StorSimple 8100

## <a name="overview"></a>Descrição geral

O 8100 do Microsoft Azure StorSimple é um único inclusão, dispostos em Bastidor dispositivo. Neste tutorial explica como descompactar, bastidor montagem e cabo a 8100 StorSimple dispositivo hardware antes de configurar e implementar o dispositivo StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Descompactar dispositivo StorSimple 8100

Os passos seguintes fornecem instruções limpar, detalhadas sobre como descompactar o seu dispositivo de armazenamento StorSimple 8100. Este dispositivo é enviado numa única caixa.

### <a name="prepare-to-unpack-your-device"></a>Preparar para descompactar o seu dispositivo

Antes de descompactar seu dispositivo, reveja as seguintes informações.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone espessura grossa](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

1. Certifique-se de que tem duas pessoas disponíveis para gerir a espessura da inclusão se estão processar manualmente. Uma inclusão completamente configurado, pode avaliar 32 kg (kg 70.).
1. Coloque a caixa numa superfície simples e nível.

Em seguida, conclua os passos seguintes para descompactar o seu dispositivo.

#### <a name="to-unpack-your-device"></a>Para descompactar o seu dispositivo

1. Inspecionar a caixa e espuma embalagem para crushes, peças, água danos ou qualquer outro danos óbvios. Se a caixa ou na embalagem extremamente está danificada, não abra a caixa. Utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md) para o ajudar a determinar se o dispositivo está em bom estado de funcionamento.

2. Descompacta a caixa. A imagem seguinte mostra a vista descompactada do seu dispositivo StorSimple.

     ![Descompactar o dispositivo de armazenamento](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Vista descompactada do seu dispositivo de armazenamento**

     Etiqueta | Descrição
     ----- | -------------
     1     | Caixa de embalagem
     2     | Espuma inferior
     3     | Dispositivo
     4     | Espuma superior
     5     | Caixa de acessório


3. Depois de descompactar a caixa de, certifique-se de que tem:

   - dispositivo de inclusão única 1
   - 2 cabos de alimentação
   - 1 cruzado cabo Ethernet
   - 2 cabos de consola em série
   - 1 conversor de série USB para um acesso série
   - 1 fendas de T10 adulteração prova
   - 4 QSFP-para-SFP + placas para utilização com 10 interfaces de rede GbE
   - 1 Bastidor-montagem kit (2 carris lado com montagem hardware)
   - Introdução a documentação de introdução

    Se não receber qualquer um dos itens enumerados acima, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md).

O passo seguinte é bastidor-montagem o seu dispositivo.

## <a name="rack-mount-your-storsimple-8100-device"></a>Montagem de bastidor dispositivo StorSimple 8100

Siga os passos para instalar o seu dispositivo de armazenamento StorSimple 8100 num bastidor 19 polegadas padrão com frente e de trás mensagens. O dispositivo StorSimple 8100 tem uma única inclusão principal.

A instalação é composta por vários passos, cada um dos quais é abordada nos seguintes procedimentos.

> [AZURE.IMPORTANT]
Dispositivos StorSimple tem de ser dispostos em bastidor para uma operação adequada.

### <a name="prepare-the-site"></a>Preparar o site

O dispositivo tem de estar instalado num bastidor 19 polegadas padrão que tenha frente e traseiros mensagens. Utilize o seguinte procedimento para preparar a instalação de bastidor.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Para preparar o site da instalação de bastidor

1. Certifique-se de que o dispositivo fundamentais com segurança um trabalho simples, estável e nível superfície (ou semelhante).

2. Certifique-se de que o site onde pretende configurar tem padrão CA a partir de uma fonte independente ou uma unidade de distribuição de energia (PDU) de bastidor com uma alimentação sem interrupção (UPS).

3. Certifique-se de que uma ranhura 2U está disponível no bastidor na qual pretende montagem do dispositivo.

![Ícone de aviso](./media/storsimple-safety/IC740879.png)![ícone espessura grossa](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **aviso!**

Certifique-se de que tem duas pessoas disponíveis para gerir a espessura se estão a processar a configuração de dispositivo manualmente. Uma inclusão completamente configurado, pode avaliar 32 kg (kg 70.).

### <a name="rack-prerequisites"></a>Pré-requisitos de bastidor

A inclusão de 8100 destina-se para a instalação num bastidor 19 polegadas padrão cab com:

- Profundidade mínima de polegadas 27.84 do bastidor publicar a mensagem.
- Peso máximo de 32 kg para o dispositivo
- Pressão anterior máxima de 5 Pascal (indicador de água de 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Kit de rail bastidor montagem

Um conjunto de montagem carris é fornecido para utilização com o cab bastidor 19 polegadas. Os trilhos foram testados para processar a espessura de inclusão máximo. Estes carris também irão permitir a instalação de vários anexos sem qualquer perda de espaço dentro de bastidor.


#### <a name="to-install-the-device-on-the-rails"></a>Para instalar o dispositivo nos trilhos

2. Execute este passo só se carris internas não são instalados no seu dispositivo. Normalmente, os trilhos internos são instalados na fábrica. Se não estiverem instalados carris, em seguida, instale os diapositivos rail esquerda e direita rail para os lados do quadro inclusão. Estes anexar utilizando seis parafusos métricos em cada lado. Para ajudar com a orientação, diapositivos rail são marcados **LH – frente** e **RH – em frente**e final que é afixada para trás da inclusão tem das extremidades afunilada.<br/>

    ![Como anexar diapositivos rail a quadro de inclusão](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
    **diapositivos de rail interna Attaching para os lados da inclusão**

       Etiqueta | Descrição
       ----- | -----------
       1     | M 3 x 4 parafusos de cabeça de botão
       2     | Diapositivos de quadro

3. Anexe a externa rail para a esquerda e montagens de rail direita externa para os membros de vertical armário bastidor. Os parênteses são marcados **LH**, **RH**e **Este lado para cima** para orientá-lo durante a orientação correta.

4. Localize o PIN rail à frente e atrás da assemblagem rail. Expandir rail para se ajustar entre as mensagens de bastidor e inserir o PIN para a frente e bastidor traseira post membro vertical buracos. Certifique-se de que a assemblagem rail é nível.

5. Utilize dois dos parafusos métricos fornecidos para proteger a assemblagem rail bastidor membros verticais. Utilize um parafusos no parte da frente e outra na parte traseira.

6. Repita estes passos para outras assemblagem rail.<br/>

     ![Como anexar diapositivos rail a bastidor armário](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Como anexar assemblagem externa rail a bastidor**

     Etiqueta | Descrição
     ----- | -----------
     1     | Parafusos fixação
     2     | Quadrado Furador frente bastidor post parafusos
     3     | PIN de localização da frente rail para a esquerda
     4     | Parafusos fixação
     5     | Esquerda rail localização traseira PIN


### <a name="mounting-the-device-in-the-rack"></a>Montagem o dispositivo na bastidor

Utilizar os trilhos bastidor que foram instalados apenas, execute os seguintes passos para o dispositivo na bastidor de montagem.

#### <a name="to-mount-the-device"></a>Para o dispositivo de montagem

1. Com um assistente levantar o inclusão e alinhá-lo com os trilhos bastidor.

2. Insira o dispositivo cuidadosamente os trilhos e, em seguida, emissão o dispositivo completamente para bastidor Cab.<br/>

    ![Inserir o dispositivo no bastidor](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montagem o dispositivo na bastidor**


3. Remova as tecla caps rebordo para a esquerda e direita da frente solicitando as tecla caps gratuitos. As tecla caps rebordo ajustam simplesmente para as flanges.

5. Seguro inclusão no bastidor instalando um parafusos de cabeça chave fornecido através de cada rebordo, esquerdo e direito.

4. Instale as maiúsculas rebordo premindo-las para a posição e ajuste-los no local.<br/>

     ![Instalar o rebordo maiúsculas](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)

    **Instalar as maiúsculas rebordo**

     Etiqueta | Descrição
     ----- | -----------
     1     | Parafusos fixação de inclusão

O passo seguinte é cabo o seu dispositivo, para poder, rede e acesso série.

## <a name="cable-your-storsimple-8100-device"></a>Cabo dispositivo StorSimple 8100

Os procedimentos seguintes explicam como cabo o seu dispositivo, StorSimple 8100 para power, rede e ligações série.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar os cabos do seu dispositivo, terá de:

- O dispositivo de armazenamento, completamente descompactados e bastidor instalados.

- 2 cabos power que veio incluída com o seu dispositivo

- Acesso a 2 unidades de distribuição Power (recomendado).

- Cabos de rede

- Fornecidos cabos série

- Conversor USB série com o controlador adequado instalado no seu PC (se necessário)

- Fornecidos 4 QSFP-para-SFP + placas para utilização com 10 interfaces de rede GbE

- [Suportada hardware para as 10 interfaces de rede GbE no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### <a name="power-cabling"></a>Power cablagem

O dispositivo inclui redundantes Power e módulos refrigeração (PCMs). Ambos os PCMs tem de estar instalados e ligados a origens de energia diferente para garantir a disponibilidade de alta.

Execute os passos seguintes para cabo o seu dispositivo para o power.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Cabos de rede

O dispositivo é uma configuração ativo suspensão: em qualquer altura determinada, um módulo de controlador está ativo e todas as operações de disco e de rede enquanto o módulo de controlador de processamento está no modo de espera. Se um controlador falhar, o controlador de espera é activado imediatamente e continua o disco e operações de rede.

Para suportar este activação de pós-falha de controlador redundantes, tem de cabo a sua rede do dispositivo, tal como descrito nos passos seguintes.

#### <a name="to-cable-for-network-connection"></a>A cabo para ligação de rede

1. O dispositivo tem seis interfaces de rede em cada controlador: quatro 1 Gbps, e duas 10 Gbps Ethernet portas. Identifica as portas de dados diferentes no inserção do seu dispositivo.

    ![Inserção de dispositivo 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Volta do dispositivo mostrando portas de dados**

     Etiqueta   | Descrição
     ------- | -----------
     0,1,4,5 |  Interfaces de rede GbE 1
     2,3     | 10 interfaces de rede GbE
     6       | Portas série

2. Consulte o artigo o diagrama seguinte para cabos de rede. (A configuração de rede mínima é apresentada por linhas azuis sólidas. Configuração adicionais necessária para o elevada disponibilidade e o desempenho é apresentada pelas linhas ponteadas.)


    ![Cabo dispositivo 2U para a rede](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cablagem para o seu dispositivo de rede**


  	|Etiqueta | Descrição |
  	|----- | ----------- |
  	| A    | LAN com acesso à Internet |
  	| B    | Controlador de 0 |
  	| C    | PCM 0 |
  	| D    | Controlador de 1 |
  	| "E"    | PCM 1 |
  	| F, G | Anfitriões |
  	| 0-5  | Interfaces de rede |



Quando cablagem o dispositivo, a configuração mínima requer:


- Ligado, pelo menos, duas interfaces de rede em cada controlador de com uma para acesso à nuvem e outra para iSCSI. Os dados 0 porta automaticamente está ativada e configurada através da consola de série do dispositivo. Para além dos dados 0, o outro porta de dados também tem de ser configurado através do portal do Azure clássico. Neste caso, ligar dados 0 porta para a LAN principal (rede com acesso à Internet). As outras portas de dados podem ser ligadas ao segmento SAN/iSCSI LAN (VLAN) da rede, dependendo do papel pretendido.

- Interfaces idênticos em cada controlador ligado à rede mesmo para garantir a disponibilidade caso ocorra um controlador activação pós-falha. Por exemplo, se optar por ligar dados 0 e 3 de dados para um dos controladores, terá de ligar os dados correspondentes 0 e os dados 3 no outro controlador de.

Tenha em conta para elevada disponibilidade e desempenho:


- Sempre que possível, configure um par de interface de rede para o acesso à nuvem (1 GbE) e outro par para iSCSI (10 GbE recomendado) em cada controlador.

- Sempre que possível, ligue interfaces de rede do controlador de cada aos dois parâmetros diferentes para garantir a disponibilidade contra uma falha de parâmetro. A figura ilustra a duas 10 GbE interfaces de rede, dados 2 e 3 de dados, a partir de cada controlador ligada a duas parâmetros diferentes.

Para obter mais informações, consulte as **interfaces de rede** nos [requisitos de elevada disponibilidade para o seu dispositivo StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Se estiver a utilizar menos SFP + com as interfaces de rede GbE 10, utilize o QSFP fornecido-SFP + placas. Para obter mais informações, aceda ao [suportados hardware de 10 GbE para interfaces de rede no seu dispositivo StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).



### <a name="serial-port-cabling"></a>Porta série cablagem

Execute os passos seguintes para cabo à porta série.

#### <a name="to-cable-for-serial-connection"></a>A cabo para ligação série

1. O dispositivo tem uma porta série em cada controlador de que está identificado por um ícone de chave inglesa. Referenciar a ilustração na secção [cabos de rede](#network-cabling) para localizar as portas série sobre a inserção do seu dispositivo.

2. Identifique o controlador de ativo no seu dispositivo de inserção. Um LED azul intermitente indica que o controlador de está ativo.

3. Utilize os cabos de série fornecidos (se necessário, o conversor USB série para o seu portátil) e ligue o seu consola de computador (com emulação de terminal para o dispositivo) ou à porta série do controlador de ativo.

4. Instale os controladores de série USB (fornecidos com o dispositivo) no seu computador.

5. Configurar a ligação série da seguinte forma: transmissão 115.200, 8 bits de dados, 1 bit de paragem, sem paridade e controlo de fluxo de configurada como nenhuma.

6. Certifique-se de que a ligação está a funcionar, premindo Enter na consola. Deverá aparecer um menu de consola série.

>[AZURE.NOTE] **Gestão de lights-Out**: quando o dispositivo está instalado num centro de dados remoto ou numa sala de computador com acesso limitado, certifique-se de que as ligações em série para ambos os controladores sempre estão ligadas a um comutador da consola série ou equipamento semelhante. Esta opção permite-controlo remoto de fora de banda e operações de suporte se existirem falhas inesperadas ou interrupções de rede.

Dispositivo agora está ligado para power, acesso de rede e conectividade série. O próximo passo é configurar o software e implementar o seu dispositivo.

## <a name="next-steps"></a>Próximos passos

Saiba como [Implementar e configurar o seu dispositivo de StorSimple no local](storsimple-deployment-walkthrough.md).
