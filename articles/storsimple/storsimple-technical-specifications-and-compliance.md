<properties 
   pageTitle="Especificações técnicas StorSimple | Microsoft Azure"
   description="Descreve as especificações técnicas e informações de conformidade de normas de regulamentação para os componentes de hardware StorSimple."
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

# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Especificações e conformidade para o dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Os componentes de hardware do seu dispositivo do Microsoft Azure StorSimple adiram as especificações técnicas e normas de regulamentação descritas neste artigo. Especificações técnicas descrevem o Power e módulos refrigeração (PCMs), unidades de disco, capacidade de armazenamento e anexos. As informações de conformidade abrangem itens, tais como as normas internacionais, segurança e emissão e cablagem.


## <a name="power-and-cooling-module-specifications"></a>Especificações Power e refrigeração módulo  

O dispositivo StorSimple tem dois 100 240V ventoinha duplo, compatível com SB Power refrigeração módulos (PCMs). Este procedimento fornece uma configuração power redundantes. Se um PCM falhar, o dispositivo continua a funcionar normalmente no outros PCM até o módulo falhado é substituído.  

A inclusão EBOD utiliza um PCM W 580 e inclusão primária utiliza um PCM W 764. As tabelas seguintes listam as especificações técnicas associadas as PCMs.

| Especificação           | 580 W PCM (EBOD)                                    | 764 W PCM (principal)                                |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| Potência máxima de saída    | 580 W                                               | 764                                                |
| Frequência               | 50/60Hz                                            | 50/60Hz                                           |
| Seleção de intervalo tensão | Automática que vão: 90 – 264 V AC, Hz 47/63               | Automática que vão: 90-264 V AC, Hz 47/63               |
| Máxima irrupção atual  | 20 A                                                | 20 A                                               |
| Correção de fator Power | > 95% nominal tensão de entrada                          | > 95% nominal tensão de entrada                         |
| Harmónicas               | Cumpre EN61000-3-2                                   | Cumpre EN61000-3-2                                  |
| Saída                  | Tensão de espera 5V @ 2.0 A                          | Tensão de espera 5V @ 2.7 A                         |
|                         | + 5V @ 42 A                                          | + 5V @ 40 A                                         |
|                         | + 12V @ 38 A                                         | + 12V @ 38 A                                        |
| Quente incorporável           |  Sim                                                | Sim                                                |
| Parâmetros e LEDs       | Mudar do segundo AC e indicador de estado quatro LEDs     | Mudar do segundo AC e indicador de estado de seis LEDs     |
| Inclusão de refrigeração       | Axial refrigeração ventoinhas com o controlo de velocidade ventoinha variável  | Axial refrigeração ventoinhas com o controlo de velocidade ventoinha variável |

 
## <a name="power-consumption-statistics"></a>Estatísticas de consumo de energia  

A tabela seguinte lista os dados de consumo power típica (valores reais podem variar a partir do publicados) para vários modelos de StorSimple dispositivo. 
 
 Condições | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 Unidades lentas, ventoinhas inactivas | 1,45 A  |0.31 kW | 1057.76 BTU/hr | 3.19 A | 0.34 kW | 1160.13 BTU/hr 
 Unidades lentas, ventoinhas aceder | 1,54 A | 0.33 kW | 1126.01 BTU/hr | 3.27 A | 0.36 kW | 1228.37 BTU/hr 
 Ventoinhas rápidas, unidades inactivas, dois PSUs powered | 2.14 A | 0.49 kW  | 1671.95 BTU/hr | 4,99 A | 0.54 kW | 1842.56 BTU/hr 
 Ventoinhas rápidas, unidades idle, um UAP powered um inactivo | 2.05 A | 0.48 kW | 1637.83 BTU/hr | 4.58 A | 0,50 kW | 1706.07 BTU/hr 
 Unidades rápidas, ventoinhas aceder à, dois PSUs powered | 2.26 A | 0.51 kW | 1740.19 BTU/hr | 4.95 A | 0.54 kW | 1842.56 BTU/hr 
 Ventoinhas rápidas, unidades de aceder à, um UAP powered um inactivo | 2.14 A |0.49 kW | 1671.95 BTU/hr | 4.81 A  | 0.53 kW | 1808.44 BTU/hr 

## <a name="disk-drive-specifications"></a>Especificações de unidade de disco  

Dispositivo StorSimple suporta até 12 unidades de disco do formulário de 3,5 polegadas fator série anexado SCSI (SA). As unidades reais podem ser uma mistura de unidades Estado sólidas (SSDs) ou unidades de disco rígido (HDDs), consoante a configuração de produto. As 12 faixas de unidade de disco encontram-se numa configuração 3 por 4 à frente de inclusão. A inclusão EBOD permite armazenamento adicional para outra 12 unidades de disco. Estes são sempre HDDs.  

## <a name="storage-specifications"></a>Especificações de armazenamento
Os dispositivos de StorSimple tem uma mistura de unidades de disco rígido e unidades Estado sólidas para o 8100 e 8600. A capacidade de utilizável total para a 8100 e 8600 são aproximadamente 15 TB e 38 TB respetivamente. A tabela seguinte documentos os detalhes de SSD, disco e capacidade de nuvem no contexto da capacidade de solução StorSimple.

| Modelo de dispositivo / capacidade                         | 8100                                                    | 8600                                                    |
|------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| Número de unidades de disco rígido (HDDs)              |   8                                                     |  19                                                     |
| Número de unidades Estado sólidos (SSDs)            |   4                                                     |  5                                                      |
| Capacidade de disco única                            |   4 TB                                                  |  4 TB                                                   |
| Capacidade SSD única                            |   400 GB                                                |  800 GB                                                 |
| Capacidade de reserva                                 |   4 TB                                                  | 4 TB                                                    |
| Capacidade de disco utilizável                            | 14 TB                                                   | 36 TB                                                   |
| Capacidade SSD utilizável                            | 800 GB                                                  | 2 TB                                                    |
| Capacidade total utilizáveis *                         | ~ 15 TB                                                 | ~ 38 TB                                                 |
| Capacidade de solução máximo (incluindo na nuvem)    | 200 TB                                                  | 500 TB                                                  |


<sup> * </sup> -  *a capacidade de utilizável total inclui a capacidade de disponível para buffers.*, metadados e dados

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões de inclusão e especificações de espessura  

As tabelas seguintes listam as várias especificações de inclusão de dimensões e de espessura.  

### <a name="enclosure-dimensions"></a>Dimensões de inclusão
A tabela seguinte apresenta as dimensões de inclusão no milímetros e polegadas.

|Inclusão |Milímetros |Polegadas |
|----------|------------|-------| 
| Altura |87.9 | 3.46 |
| Largura através de rebordo montagem | 483 | 19.02 |
| Largura ao longo do corpo de inclusão | 443 | 17.44 |
| Profundidade de rebordo da frente montagem para extremidade do corpo de inclusão | 577 | 22.72 |
| Profundidade a partir do painel de operações para afastada extremidade de inclusão | 630.5 | 24.82 |
| Profundidade de rebordo montagem para afastada extremidade de inclusão |   603 | 23.74 |

### <a name="enclosure-weight"></a>Espessura de inclusão  

Dependendo da configuração, uma inclusão primária totalmente carregado pode avaliar a partir de 21 para 33 kgs e necessita de duas pessoas processar o mesmo. 
 
| Inclusão | Peso |
|-----------|--------| 
| Peso máximo (depende da configuração) |30 kg – 33 kg |
| Esvaziar (sem unidades equipadas) |21 – kg 23 |

## <a name="enclosure-environment-specifications"></a>Especificações de ambiente de inclusão  

Esta secção apresenta as especificações relacionadas com o ambiente de inclusão. A temperatura, humidade, altitude, shock, vibração, orientação, segurança e compatibilidade electromagnética (CEM) são incluídas nesta categoria.  

### <a name="temperature-and-humidity"></a>Temperatura e humidade

| Inclusão        | Intervalo de temperatura  | Ambiente humidade | Lâmpada fresca máxima   |
|------------------|----------------------------|---------------------------|--------------------|
| Operacionais avançados      | 5-35° C (41° F - 95° F)    | 20% - 80% não-condensação- | C 28 (82° F)        |
| Não operacionais  | -40-70° C (40° F - 158° F) | 5-100% sem condensação  | C 29 (84° F)        |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, shock, vibração, orientação, segurança e cem
 
| Inclusão          | Especificações operacionais avançadas                                                |
|--------------------|---------------------------------------------------------------------------| 
| Fluxo de ar            | Ar sistema é frente para trás. Sistema tem de ser operado com uma instalação de baixa, traseira escape. Retroceder pressão criado pela bastidor portas e obstáculos não deve exceder 5 Pascal (indicador de água de 0,5 mm). |
| Altitude, operacionais avançado  | metros-30 para 3045 metros (-100 para pés 10.000) com uma temperatura de funcionamento máximo retirar classificados 5 ° c acima pés 7000. |
| Altitude, não operacional  | metros-305 para 12,192 metros (-1,000 para pés 40.000) |
| Shock operacionais avançado  | seno de 10 ms ½ 5g |
| Shock, não operacional  | seno de 10 ms ½ 30g |
| Vibração, operacionais avançada  | 0.21g RMS 5-500 Hz aleatório |
| Vibração, não operacional  | 1.04g RMS 2-200 Hz aleatório |
| Vibração, mudança  | seno de-2-200 Hz 3g |
| Orientação e montagem  | 19. o "bastidor montagem (unidades EIA 2) |
| Bastidor carris  | Para se ajustar profundidade 700mm mínimo (31.50 polegadas) de porta-em conformidade com IEC 297 |
| Segurança e das aprovações  |   CE e UL EN 61000-3, CEI 61000-3, UL 61000-3 |
| CEM  | EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Conformidade normas internacionais
Dispositivo Microsoft Azure StorSimple está em conformidade com as normas internacionais seguintes:  

- CE - PT-PT 60950-1  
- Relatório de CB para CEI 60950-1  
- UL e cUL para UL 60950-1  

## <a name="safety-compliance"></a>Conformidade de segurança  

O seu dispositivo do Microsoft Azure StorSimple cumpre as seguintes classificações de segurança:  

- Aprovação de tipo de produto do sistema: UL, cUL, CE  
- Conformidade de segurança: UL 60950 IEC 60950, pt-pt 60950  

## <a name="emc-compliance"></a>Conformidade cem 

O seu dispositivo do Microsoft Azure StorSimple cumpre as classificações de cem seguintes.  

### <a name="emissions"></a>Emissão

O dispositivo está em conformidade cem para níveis de emissão conduzidos e larga.  

- Emissão conduzido limitar níveis: CFR 47 peça 15B classe A EN55022 classe A CISPR classe A  
- Emissão larga limitar níveis: CFR 47 peça 15B classe A EN55022 classe A CISPR classe A   

### <a name="harmonics-and-flicker"></a>Harmónicas e flicker  

O dispositivo está em conformidade com EN61000-3-2/3.  

### <a name="immunity-limit-levels"></a>Níveis de limite imunidade  
O dispositivo está em conformidade com EN55024.  

## <a name="ac-power-cord-compliance"></a>Conformidade do AC power cabo
  
O plug e a assemblagem de cabo power completa tem cumprem as normas adequadas para o país/região em que o dispositivo está a ser utilizado e tem de ter aprovações de segurança que estão aceitáveis nesse país. As tabelas seguintes apresentam padrões Uruguai e Europa.  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cabos de alimentação AC - EUA (tem de ser NRTL listado)

| Componente       | Especificação                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Tipo de cabo       | SV ou SVT, 18 AWG (mínimo), Maestro 3, comprimento máximo metros 2.0 |
| Plug            | Plug de anexo de tipo de grounding NEMA 5-15P classificado 120 V, 10 A; ou IEC 320 C14, 250 V, 10 A |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

### <a name="ac-power-cords---europe"></a>Cabos de alimentação AC - Europa

| Componente       | Especificação                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Tipo de cabo       | Harmonizados, H05-VVF-3G1.0                                         |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

## <a name="supported-network-cables"></a>Cabos de rede suportados  

As 10 GbE para interfaces de rede, dados 2 e 3 de dados, consulte a [lista de cabos de rede suportados e módulos](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Próximos passos

Agora está pronto para implementar um dispositivo StorSimple no seu centro de dados. Para obter mais informações, consulte [Implementar o seu dispositivo no local](storsimple-deployment-walkthrough-u2.md).  
