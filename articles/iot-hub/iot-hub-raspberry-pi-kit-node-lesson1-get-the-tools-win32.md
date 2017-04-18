<properties
 pageTitle="Obter as ferramentas (Windows 7 +) | Microsoft Azure"
 description="Transfira e instale as ferramentas e necessárias software para a primeira aplicação de exemplo para o seu Pi no Windows 7 e versões posteriores."
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

# <a name="12-get-the-tools-windows-7-"></a>1.2 obter as ferramentas (Windows 7 +) 

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 o que vai fazer

Transfira as ferramentas de desenvolvimento e do software para a primeira aplicação de exemplo para o seu 3 de Pi framboesa. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="122-what-you-will-learn"></a>1.2.2 o que vai aprender
- Como instalar o Git e Node.js
  - [Git](https://git-scm.com) é um sistema de controlo de versão abrir origem distribuído. A aplicação de exemplo para esta lição está armazenada no Git.
  - [Node.js](https://nodejs.org/en/) é um tempo de execução de JavaScript com um ecossistema pacote avançada.
- Como utilizar NPM para instalar as ferramentas de desenvolvimento Node.js adicionais.
  - O requisito de versão mínima de Node.js é 4,5 LTS.
  - [NPM](https://www.npmjs.com) é um dos gestores de pacote para Node.js.

## <a name="123-what-you-need"></a>1.2.3 o que precisa

- Uma ligação à Internet para transferir o software e as ferramentas de desenvolvimento
- Um computador que esteja a executar o Windows

## <a name="124-install-git-and-nodejs"></a>1.2.4 instalar o Git e Node.js

Clique nas ligações abaixo para transferir e instalar Git e Node.js LTS para Windows.

- [Obter Git para Windows](https://git-scm.com/download/win/)
- [Obter Node.js LTS para Windows](https://nodejs.org/en/)

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 instalar ferramentas de desenvolvimento Node.js adicionais

Utilize [gulp.js](http://gulpjs.com) para automatizar a implementação da aplicação de exemplo para o Pi. Também é utilizar a [deteção de dispositivo-clip](https://github.com/Azure/device-discovery-cli) para obter informações sobre a rede sobre os dispositivos de IoT.

Inicie uma linha de comandos como administrador. Instalar `gulp` e `device-discovery-cli` executando o seguinte comando:

```bash
npm install -g device-discovery-cli gulp
```
    
Se tiver problemas ao instalar Node.js e estas ferramentas adicionais de desenvolvimento Node.js no seu computador, consulte o artigo [Guia de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) para soluções para problemas comuns.

## <a name="126-install-visual-studio-code"></a>1.2.6 instalar o código do Visual Studio

[Transfira](https://code.visualstudio.com/docs/setup/windows) e instale o código do Visual Studio. Código do Visual Studio é um editor de código fonte lightweight mas poderosa para Windows, Linux e macOS. Utilize este editor mais tarde no tutorial para editar o código de exemplo.

## <a name="127-summary"></a>1.2.7 resumo

Tiver instalado as ferramentas de desenvolvimento necessários e software para a primeira aplicação de exemplo. Na secção seguinte, criar, implementar e executar a aplicação de amostra no seu Pi.

## <a name="next-steps"></a>Próximos passos

[1.3 criar e implementar a aplicação de exemplo intermitência](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
