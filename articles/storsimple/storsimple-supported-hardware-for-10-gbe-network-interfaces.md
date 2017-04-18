<properties 
   pageTitle="Interfaces de hardware para StorSimple 10 GbE | Microsoft Azure"
   description="Descreve a distância de (SFP) incorporável suportados pequenas-factor da forma, cabos e parâmetros de 10 GbE para interfaces de rede no seu dispositivo StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Suportada hardware para as 10 interfaces de rede GbE no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo fornece informações sobre o hardware suplementar que funciona com o seu dispositivo do Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos testadas pela Microsoft

Microsoft tem testado os seguintes máximo (SFP) incorporável pequenas-factor da forma, cabos e muda para garantir que funcionam melhor com dispositivos. (As tabelas seguintes serão atualizadas como novo hardware é testado.)

### <a name="sfp-transceivers"></a>SFP + máximo

|Fazer|Modelo|
|---|---|
|Cisco|SFP-10G-SR|

### <a name="cables"></a>Cabos

|S. Não. |Fazer|Modelo|
|---|---|---|
| 1.|Cisco|SFP-H10GB-CU1M|
| 2.|Cisco|SFP-H10GB-CU2M|
| 3.|Cisco|SFP-H10GB-CU3M|
| 4.|Tripp Lite|N820 - 05M (OM3)|

### <a name="switches"></a>Parâmetros

|S. Não.|Fazer|Modelo|
|---|---|---|
| 1. |Cisco|N3K-C3172PQ-10GE|
| 2. |Cisco|N3K-C3048-ZM-F|
| 3. |Cisco|N5K-C5596UP-FA|

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos de testado no campo

Esta secção contém a lista de dispositivos que foram com êxito implementados no campo por StorSimple clientes. Estes não foram testadas pela Microsoft, mas provavelmente trabalhar com o seu dispositivo StorSimple.
 
| Parâmetro                         | Valor                                    |
|-----------------------------------|------------------------------------------|
| Mudar a fazer                     | Zimbro                                  |
| Modelo de parâmetro                    | ex4550 32F                               |
| Versão do sistema operativo mudar | JunOS 12.3R9.4                           |
| Modelo de pá                     | Portas bordo (PIC 0)                    |
| Tornar transceptor                  | Zimbro                                  |
| Modelo de transceptor               | Número de peça 740 021308 <br></br> Número de peça 740 030658                   |
| Versão do firmware transceptor    | Revisão 01 versão 0,0 (reportados)            |
| Modelo de cabo                     | Em frente e verso jumper LC/LC 50/125µ, OM3, LSZH |
| Modelo de StorSimple                | 8600                                     |
| Versão do software StorSimple     | 6.3.9600.17491                           |


## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos testado pelo fornecedor OEM (Mellanox)  

Mellanox tem testado os seguintes máximo (SFP) incorporável pequenas-factor da forma, cabos e muda para garantir que funcionam melhor com interfaces de rede Mellanox como as 10 interfaces de rede GbE no seu dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Os cabos e módulos suportados pelo Mellanox 

A tabela seguinte lista os cabos e módulos suportados pelo Mellanox. Estes não foram testadas pela Microsoft, mas provavelmente trabalhar com o seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelo                 | Descrição                                            | Fazer                |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1.     | 10 GbE| CAB-SFP-SFP - 1 MILHÃO        | cabo cobre passivo SFP + 10 Gb/s 1 milhão                   | Arista                |
| 2.     | 10 GbE| CAB-SFP-SFP - 2M        | cabo cobre passivo SFP + 10 Gb/s 2m                   | Arista                |
| 3.     | 10 GbE| CAB-SFP-SFP - 3M        | cabo cobre passivo SFP + 10 Gb/s 3M                   | Arista                |
| 4.     | 10 GbE| CAB-SFP-SFP - 5M        | cabo cobre passivo SFP + 10 Gb/s 5m                   | Arista                |
| 5.     | 10 GbE| Cisco SFP-H10GBCU1M   | Cabo SFP + Cisco                                       | Cisco                 |
| 6.     | 10 GbE| Cisco SFP-H10GBCU3M   | Cabo SFP + Cisco                                       | Cisco                 |
| 7.     |10 GbE | Cisco SFP-H10GBCU5M   | Cabo SFP + Cisco                                       | Cisco                 |
| 8.     | 10 GbE| J9281B HP X242 10 G    | SFP + para SFP + 1 milhão Direct anexar cabo cobre             | HP                    |
| 9.     | 10 GbE| 455883 B21 HP BLc     | 10Gb SR SFP + optar ativamente por participar                                       | HP                    |
| 10.    | 10 GbE| 455886 B21 HP BLc     | 10Gb LR SFP + optar ativamente por participar                                       | HP                    |
| 11.    |10 GbE | 487649 B21 HP BLc     | Cabo de cobre SFP + m 0,5 10GbE                           | HP                    |
| 12.    |10 GbE | 487652 B21 HP BLc     | Cabo de cobre SFP + 1 milhão 10GbE                             | HP                    |
| 13.    |10 GbE | 487655 B21 HP BLc     | Cabo de cobre SFP + 3M 10GbE                             | HP                    |
| 14.    |10 GbE | 487658 B21 HP BLc     | Cabo de cobre SFP + 7m 10GbE                             | HP                    |
| 15.    |10 GbE | 537963 B21 HP BLc     | Cabo de cobre SFP + 5m 10GbE                             | HP                    |
| 16.    |10 GbE | AP784A HP             | Cabo série C passivas cobre SFP + 3M                  | HP                    |
| 17.    |10 GbE | AP785A HP             | Cabo série C passivas cobre SFP + 5m                  | HP                    |
| 18.    |10 GbE | AP818A HP             | 1 milhão B série SFP ativo cobre + cabo                   | HP                    |
| 19.    |10 GbE | AP819A HP             | 3M B série SFP cobre ativo + cabo                   | HP                    |
| 20.    |10 GbE | J9150A HP             | X132 10 G SFP + LC SR transceptor                        | HP                    |
| 21.    |10 GbE | J9151A HP             | X132 10 G SFP + LC LR transceptor                        | HP                    |
| 22.    |10 GbE | J9283B HP             | X242 10 G SFP + SFP + 3M CAD cabo                        | HP                    |
| 23.    |10 GbE | J9285B HP             | X242 10 G SFP + SFP + 7 m CAD cabo                        | HP                    |
| 24.    | 10 GbE| JD095B HP             | X240 10 G SFP + SFP + 0.65 m CAD cabo                     | HP                    |
| 25.    | 10 GbE| JD096B HP             | X240 10 G SFP + SFP + 1.2 m CAD cabo                      | HP                    |
| 26 de.    | 10 GbE| JD097B HP             | X240 10 G SFP + SFP + 3M pai cabo                        | HP                    |
| 27.    | 10 GbE| MAM1Q00A QSA Mellanox | QSFP para adaptador SFP +                                   | Tecnologias Mellanox |
| 28.    | 10 GbE| MC2309124 006 Mt      | Cabo cobre passivas 1 x SFP+ para QSFP 10 Gb/s 24awg 7 m   | Tecnologias Mellanox |
| 29.    | 10 GbE| MC2309124 007 Mt      | Cabo cobre passivas 1 x SFP+ para QSFP 10 Gb/s 24awg 7 m   | Tecnologias Mellanox |
| 30.    | 10 GbE| MC2309130 003 Mt      | Cabo cobre passivas 1 x SFP+ para QSFP 10 Gb/s 30awg 3M   | Tecnologias Mellanox |
| 31.    | 10 GbE| MC2309130 00A Mt      | Cabo cobre passivas 1 x SFP+ para QSFP 10 Gb/s 30awg 0,5 m | Tecnologias Mellanox |
| 32.    | 10 GbE| MC3309124 005 Mt      | Bobinas passivas cabo 1 24awg de 10 Gb/s x SFP+ 5 m           | Tecnologias Mellanox |
| 33.    | 10 GbE| MC3309124 007 Mt      | Bobinas passivas cabo 1 24awg de 10 Gb/s x SFP+ 7 m           | Tecnologias Mellanox |
| 34.    | 10 GbE| MC3309130 003 Mt      | Bobinas passivas cabo 1 30awg de 10 Gb/s x SFP+ 3M           | Tecnologias Mellanox |
| 35.    | 10 GbE| MC3309130 00A Mt      | Bobinas passivas cabo 1 30awg de 10 Gb/s x SFP+ 0,5 m         | Tecnologias Mellanox |

### <a name="switches-supported-by-mellanox"></a>Parâmetros de suportados pelo Mellanox 

A tabela seguinte lista os parâmetros suportados pelo Mellanox. Estes não foram testadas pela Microsoft, mas provavelmente trabalhar com o seu dispositivo StorSimple.

| S. Não. | Velocidade | Modelo | Descrição                                                         | Fazer              |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1.     | 10GbE | 516733 B21  | HP ProCurve 6120XG 10GbE Ethernet pá mudar                      | HP    |
| 2.     | 10GbE | 538113 B21  | HP 10GbE pass-through módulo (PTM)                                  | HP    |
| 3.     | 10GbE | EN4093      | Módulo de dimensionáveis mudar do IBM PureFlex sistema ferro EN4093 10 Gigabit | IBM   |
| 4.     | 1GbE  | 3020        | Pá mudar do Cisco catalisador 3020 1GbE                               | Cisco |
| 5.     | 1GbE  | 3020 X       | Pá de mudar de catalisador 3020 X 1GbE Cisco                              | Cisco |
| 6.     | 1GbE  | 438030 B21  | Módulo de mudar de 1GbE HP - GbE2c camada 2/3 Ethernet pá mudar       | HP    |
| 7.     | 1GbE  | 6120G       | Pá de mudar de 1GbE HP ProCurve 6120G/XG                              | HP    |

## <a name="next-steps"></a>Próximos passos

[Saiba mais sobre os componentes de hardware StorSimple e de estado](storsimple-monitor-hardware-status.md).
