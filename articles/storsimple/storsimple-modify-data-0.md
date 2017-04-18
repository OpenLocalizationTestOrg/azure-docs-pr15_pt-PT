<properties 
   pageTitle="Modificar os dados 0 definições num dispositivo StorSimple | Microsoft Azure"
   description="Saiba como utilizar o Windows PowerShell para StorSimple reconfigurar a interface de 0 rede de dados no seu dispositivo StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-device"></a>Modificar as definições de interface de 0 rede de dados no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Dispositivo Microsoft Azure StorSimple tem seis interfaces de rede, a partir de dados de 0 a 5 de dados. Os dados 0 interface é sempre configurado através da interface do Windows PowerShell ou a série consola e é ativada automaticamente na nuvem. Tenha em atenção que não é possível configurar interface da rede 0 de dados através do portal do Azure clássico. 

Os dados 0 interface pela primeira vez está configurada através do Assistente de configuração durante inicial de implementação do dispositivo StorSimple. Quando o dispositivo está no modo operacional, poderá ter de reconfigurar dados 0 definições. Este tutorial fornece dois métodos para modificar dados 0 definições, ambos os através do Windows PowerShell para StorSimple de rede.

Depois de ler neste tutorial, irá poderá:

- Modificar dados 0 definição através do Assistente de configuração de rede
- Modificar as definições de rede 0 de dados através de `Set-HcsNetInterface` cmdlet



## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modificar as definições de rede 0 de dados através do Assistente de configuração
Pode reconfigurar as definições de rede 0 de dados ligando para a interface do Windows PowerShell do seu dispositivo StorSimple e iniciar uma sessão de Assistente de configuração. Execute os seguintes passos para modificar dados 0 definições:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Para modificar as definições de rede 0 de dados através do Assistente de configuração

1. No menu de consola série, selecione a opção 1, **Iniciar sessão com acesso total**. Quando lhe for pedido fornecer a **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.

2. Na linha de comandos, escreva:

    `Invoke-HcsSetupWizard`

3. Assistente de configuração irão aparecer para o ajudar a configurar os dados 0 interface do seu dispositivo. Forneça novos valores para o endereço de IP, gateway e máscara de rede.

> [AZURE.NOTE] Os controladores fixos IPs serão necessário reconfigurar através da página **Configurar** do dispositivo StorSimple no portal do Azure clássico. Para mais informações, aceda a [Modificar interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces).


## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modificar as definições de rede 0 de dados através do cmdlet Set-HcsNetInterface
Uma forma alternativa para reconfigurar dados 0 interface de rede está utilizou o `Set-HcsNetInterface` cmdlet. O cmdlet é executado a partir da interface do Windows PowerShell do seu dispositivo StorSimple. Quando utilizar este procedimento, o controlador de fixa IPs também pode ser configurado aqui. Execute os seguintes passos para modificar os dados 0 definições: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Para modificar as definições de rede 0 de dados através do cmdlet Set-HcsNetInterface

1. No menu de consola série, selecione a opção 1, **Iniciar sessão com acesso total**. Quando lhe for pedido fornecer a palavra-passe de administrador do dispositivo. A palavra-passe predefinida é `Password1`.

2. Na linha de comandos, escreva:

    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
    
    Do parênteses em ângulo, escreva os seguintes valores para os dados 0:
                                            
    - Endereços IPv4
    
    - IPv4 gateway
    
    - Máscara de sub-rede IPv4
    
    - Endereços IPv4 fixos para controlador de 0

    - Endereços IPv4 fixos para controlador 1

    Para obter mais informações sobre a utilização deste cmdlet, vá para o [Windows PowerShell para referência do cmdlet StorSimple](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Próximos passos

- Para configurar interfaces de rede diferente de 0 de dados, pode utilizar a [página configurar no portal do Azure clássica](storsimple-modify-device-config.md). 

- Se tiver problemas ao configurar as interfaces de rede, consulte a [resolução de problemas de implementação](storsimple-troubleshoot-deployment.md).

