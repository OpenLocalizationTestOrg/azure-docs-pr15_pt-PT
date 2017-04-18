<properties
 pageTitle="Introdução ao Pi morango 3 | Microsoft Azure"
 description="Introdução ao framboesa Pi 3, criar o seu centro de IoT Azure e ligar o Pi ao centro do IoT"
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="get-started-with-raspberry-pi-3"></a>Introdução ao Pi morango 3

Neste tutorial, comece pelos princípios básicos para trabalhar com framboesa Pi 3 esse Raspbian a execução de formação. Em seguida, a Saiba como forma totalmente integrada ligar os seus dispositivos na nuvem com [Azure IoT concentrador](iot-hub-what-is-iot-hub.md). Para obter exemplos do Windows 10 IoT Core, visite [windowsondevices.com](http://www.windowsondevices.com/).

## <a name="lesson-1-configure-your-device"></a>Lição 1: Configurar o seu dispositivo

![Diagrama de E2E Lição 1](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

Esta lição, configurar o seu dispositivo framboesa Pi 3 com um sistema operativo, configurar o seu ambiente de desenvolvimento e implementar uma aplicação para o Pi.

### <a name="configure-your-device"></a>Configurar o seu dispositivo

Configurar o seu 3 de Pi framboesa para a primeira utilização e instale o SO Raspbian, um sistema operativo gratuito que está optimizado para o hardware framboesa Pi.

*Tempo para concluir estimado: 30 minutos* 

[Vá para 'Configurar o seu dispositivo'](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)

### <a name="get-the-tools"></a>Obter as ferramentas de
Transfira as ferramentas e software criem e implementem a aplicação para o 3 de Pi framboesa primeira.

*Tempo para concluir estimado: 20 minutos* 

[Aceda a 'Obter as ferramentas de'](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

### <a name="create-and-deploy-the-blink-application"></a>Criar e implementar a aplicação de intermitência

Clonar a aplicação de Node.js exemplo a partir do Github e gulp para implementar esta aplicação para o seu quadro framboesa Pi 3. Esta aplicação exemplo intermitência LED ligado ao quadro em dois segundos.

*Tempo para concluir estimado: 5 minutos* 

[Aceda a ' criar e implementar a aplicação de intermitência '](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)

## <a name="lesson-2-create-your-iot-hub"></a>Lição 2: Criar o seu centro IoT

![Diagrama de E2E lição 2](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

Esta lição, criar a sua conta Azure gratuita, aprovisionar o seu centro Azure IoT e criar o primeiro dispositivo no Centro de Azure IoT.

Conclua Lição 1 antes de começar esta lição.

### <a name="get-the-azure-tools"></a>Obter as ferramentas de Azure

Instalar o Azure Interface de comandos (Azure CLI).

*Tempo para concluir estimado: 10 minutos* 

[Aceda a 'Azure obter ferramentas'](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)

### <a name="create-your-iot-hub-and-register-your-raspberry-pi-3"></a>Criar o seu centro IoT e registar a sua framboesa Pi 3

Criar o seu grupo de recursos, aprovisionar o seu centro Azure IoT primeiro e adicionar o primeiro dispositivo para o concentrador de IoT Azure utilizando o clip do Azure. 

*Tempo para concluir estimado: 10 minutos* 

[Ir para 'Criar o seu centro IoT e registar a sua framboesa Pi 3'](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


## <a name="lesson-3-send-device-to-cloud-messages"></a>Lição 3: Enviar mensagens de nuvem de dispositivo

![Diagrama de E2E lição 3](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

Esta lição, enviar mensagens a partir do seu Pi para o seu centro IoT. Também pode criar uma aplicação de função Azure que escolhe a partir do seu centro IoT as mensagens a receber e escreve-los ao armazenamento de tabela do Azure.

Conclua lições 1 e 2 antes de começar esta lição.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Criar uma aplicação de função Azure e a conta de armazenamento do Windows Azure

Utilize um modelo de Gestor de recursos do Azure para criar uma aplicação de função Azure e uma conta de armazenamento do Windows Azure.

*Tempo para concluir estimado: 10 minutos* 

[Aceda a 'Criar uma aplicação de função Azure e a conta de armazenamento do Windows Azure'](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)

### <a name="run-sample-application-to-send-device-to-cloud-messages"></a>Executar a aplicação de exemplo para enviar mensagens de nuvem de dispositivo

Implementar e execute uma aplicação de exemplo para o seu dispositivo framboesa Pi 3 que envia mensagens a IoT concentrador.

*Tempo para concluir estimado: 10 minutos* 

[Ir para 'Executar a aplicação de exemplo para enviar mensagens de nuvem de dispositivo'](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

### <a name="read-messages-persisted-in-azure-storage"></a>Ler mensagens de persiste no armazenamento do Windows Azure
Monitorize as mensagens de dispositivo para nuvem, tal como sejam escritos nos seu armazenamento do Windows Azure.

*Tempo para concluir estimado: 5 minutos* 

[Aceda a 'ler mensagens de persiste no armazenamento do Windows Azure'](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)


## <a name="lesson-4-send-cloud-to-device-messages"></a>Lição 4: Enviar mensagens de dispositivo de nuvem

![Diagrama de E2E Lição 4](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

Esta lição demonstrações como enviar mensagens a partir do seu centro Azure IoT para sua framboesa Pi 3. Controlam as mensagens de ativar e desativar o comportamento do LED que está ligado à sua Pi. Uma aplicação de exemplo está preparada para lhe atingir esta tarefa.

Preencha lições 1, 2 e 3 antes de começar esta lição.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Executar a aplicação de exemplo para receber mensagens de dispositivo de nuvem

A aplicação de exemplo na Lição 4 é executada no seu Pi e monitoriza as mensagens recebidas a partir do seu centro IoT. Uma nova tarefa gulp envia mensagens para o Pi a partir do seu centro IoT para intermitência o LED.

*Tempo para concluir estimado: 10 minutos* 

[Ir para 'Executar a aplicação de exemplo para receber mensagens de dispositivo de nuvem'](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Secção opcional: alterar ativar e desativar o comportamento do LED

Personalize as mensagens para alterar o LED e desativar o comportamento.

*Tempo para concluir estimado: 10 minutos* 

[Aceda a ' secção opcional: alterar ativar e desativar o comportamento do LED'](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


## <a name="troubleshooting"></a>Resolução de problemas

Se cumprir qualquer presença durante as lições, pode procurar soluções nesta página.

[Aceda a 'Resolução de problemas'](iot-hub-raspberry-pi-kit-node-troubleshooting.md)
