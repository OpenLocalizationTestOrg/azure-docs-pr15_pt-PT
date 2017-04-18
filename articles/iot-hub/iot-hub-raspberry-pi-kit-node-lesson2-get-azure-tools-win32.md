<properties
 pageTitle="Obter ferramentas Azure (Windows 7 +) | Microsoft Azure"
 description="Instale o Python e Interface de comandos do Azure (Azure CLI) no Windows 7 e versões posteriores."
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

# <a name="21-get-azure-tools-windows-7-"></a>2.1 obter ferramentas Azure (Windows 7 +)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 o que vai fazer

Instalar Python e o Azure da linha de comandos Interface (Azure CLI). Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 o que vai aprender

- Como instalar o Python.
- Como instalar o clip do Azure.

## <a name="213-what-you-need"></a>2.1.3 o que precisa

- Um computador Windows com ligação à Internet
- Uma subscrição ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) apenas de duas minutos.

## <a name="214-install-python"></a>2.1.4 instalar o Python

Instale Python no computador Windows. Pode instalar o Python 2.7, 3.4 ou 3.5. Neste tutorial é baseado Python 2.7. Se já tiver instalado Python, aceda à secção 2.1.5.

[Obter Python para Windows](https://www.python.org/downloads/)

Também precisa de adicionar o caminho das pastas onde python.exe e pip.exe são instalados para o sistema `PATH` variável de ambiente. Por predefinição, python.exe está instalado no `C:\Python27` e pip.exe está instalado no `C:\Python27\Scripts`.

## <a name="215-install-the-azure-cli"></a>2.1.5 instalar o clip Azure

O clip do Azure fornece uma experiência de linha de comandos múltiplas plataformas para Azure, permitindo-lhe trabalho diretamente da sua linha de comandos para aprovisionar e gestão de recursos.

Para instalar o Azure clip, siga estes passos:

1. Abra uma janela de linha de comandos como administrador.
2. Execute os seguintes comandos:

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```
3. Verifique se a instalação, executando o seguinte comando:

    ```bash
    az iot -h
    ```

Consulte o seguinte resultado se a instalação for bem sucedida.

![pt iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

## <a name="216-summary"></a>2.1.6 resumo

Que instalou o clip do Azure. Continue para secção seguinte para criar a sua identidade Azure IoT concentrador e do dispositivo utilizando o clip do Azure.

## <a name="next-steps"></a>Próximos passos

[2.2 criar o seu centro IoT e registar a sua framboesa Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
