<properties
 pageTitle="Obter ferramentas Azure (Ubuntu 16.04) | Microsoft Azure"
 description="Instale o Python e Azure Interface da linha de comandos (Azure CLI) no Ubuntu."
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

# <a name="21-get-azure-tools-ubuntu-1604"></a>2.1 obter ferramentas Azure (Ubuntu 16.04)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 o que vai fazer

Instalar o Azure Interface de comandos (Azure CLI). Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 o que vai aprender

- Como instalar o clip do Azure.
- Como adicionar IoT subgrupos do clip Azure.

## <a name="213-what-you-need"></a>2.1.3 o que precisa

- Um computador Ubuntu com ligação à Internet
- Uma subscrição ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) apenas de duas minutos.

## <a name="214-install-the-azure-cli"></a>2.1.4 instalar o clip Azure

O clip do Azure fornece uma experiência de linha de comandos múltiplas plataformas para Azure, permitindo-lhe trabalho diretamente da sua linha de comandos para aprovisionar e gestão de recursos. 

Para instalar o clip de Azure mais recente, siga estes passos:

1. Execute os seguintes comandos numa janela do Terminal. Pode demorar cinco minutos para instalar o clip do Azure.

    ```bash
    sudo apt-get update
    sudo apt-get install -y libssl-dev libffi-dev
    sudo apt-get install -y python-dev
    sudo apt-get install -y build-essential
    sudo apt-get install -y python-pip
    sudo pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```

2. Verifique se a instalação, executando o seguinte comando:

    ```bash
    az iot -h
    ```

Deverá ver o resultado seguinte se a instalação for bem sucedida.

![pt iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_ubuntu.png)

## <a name="215-summary"></a>2.1.5 resumo

Que instalou o clip do Azure. Continue para secção seguinte para criar a sua identidade Azure IoT concentrador e do dispositivo utilizando o clip do Azure.

## <a name="next-steps"></a>Próximos passos

[2.2 criar o seu centro IoT e registar a sua framboesa Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
