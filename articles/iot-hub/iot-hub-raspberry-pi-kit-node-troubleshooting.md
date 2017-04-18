<properties
 pageTitle="Resolução de problemas | Microsoft Azure"
 description="Resolução de problemas de página para framboesa Pi Node.js experiência"
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

# <a name="troubleshooting"></a>Resolução de problemas

## <a name="hardware-issues"></a>Problemas de hardware

### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>A aplicação é executada bem, mas o LED não está a piscar

Este problema está sempre relacionado com a conectividade de circuito hardware. Utilize os passos seguintes para identificar problemas.

1. Verifique se tem de escolher a **GPIO** correta na sua área. As duas portas nesta lição devem ser **GPIO terra (afixar 6)** e **GPIO 04 (afixar 7)**.
2. Verifique se polaridade da sua LED está correta. A mais longa ramificação deverá indicar pin ânodos, **positivo**.
3. Utilize o **Afixar 3.3 v** e **Pin terra** no seu Pi morango 3. Trata o Pi como potência Cc. Verifique se o LED funciona ajustar.

![Especificação LED](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Outros problemas de hardware

Para obter informações sobre como resolver problemas comuns sobre a 3 de Pi framboesa, consulte a [página de resolução de problemas oficial](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Problemas de pacote node.js

### <a name="no-response-during-gulp-tasks"></a>Sem resposta durante gulp tarefas

Se tiver problemas gulp tarefas em execução, pode adicionar a `--verbose` opção para depuração. Experimente terminar as tarefas atuais de gulp com `Ctrl + C` e, em seguida, execute o seguinte comando na sua janela consola para ver depurar mensagens. Poderá ver mensagens de erro detalhadas impressas na sua saída da consola. 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemas de deteção de dispositivo

Para obter ajuda de resolução de problemas comuns com a `devdisco` comando, selecione o [ficheiro Leia-me](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="other-npm-issues"></a>Outros problemas NPM

Experimente atualizar o seu pacote NPM com o seguinte comando:

```bash
npm install -g npm
```

Se o problema persistir, deixe os seus comentários no final deste artigo ou criar um problema de Github no nosso [Exemplo repositório](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)

## <a name="remote-debugging"></a>Depuração remota

### <a name="run-the-sample-application-in-debug-mode"></a>Executar a aplicação de exemplo no modo de depuração

```bash
gulp run --debug
```

Quando o motor de depuração estiver pronto, deverá ver ```Debugger listening on port 5858``` no resultado da consola.

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>Configurar o código de VS para ligar para o dispositivo remoto

Abra o painel **Depurar** no lado esquerdo.

Clique no botão **Começar a depuração** (F5) verde. Código VS abre um ficheiro de **launch.json** , necessita de atualizar.

Actualize o ficheiro **launch.json** com o seguinte conteúdo. Substituir `[device hostname or IP address]` com o endereço IP do dispositivo real ou o nome do anfitrião.   

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![Configuração de depuração remota](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Anexar à aplicação remota

Clique no botão **Começar a depuração** (F5) para iniciar a depuração verde. 

Leia o [JavaScript no código VS](https://code.visualstudio.com/docs/languages/javascript#_debugging) para saber mais sobre o depurador.

![Remote depuração interativos](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Problemas de clip Azure

Clip Azure é uma compilação de pré-visualização. Pode referir-se para o [Guia de instalação da pré-visualização](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md) para procurar soluções.

Se encontrar todos os erros com a ferramenta, ficheiro um [problema](https://github.com/Azure/azure-cli/issues) na secção de **problemas** da repo GitHub.

Para obter ajuda de resolução de problemas comuns, selecione o [ficheiro Leia-me](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Problemas de instalação Python

### <a name="legacy-installation-issues-macos"></a>Problemas de instalação legado (macOS)

Ao instalar o **pip**, é devolvido um erro de permissão quando existem pacotes legados que são instaladas com permissões de **DOM** . Esta situação ocorre porque instalação anterior do Python utilizando brew (macOS) não é desinstalada. Alguns pacotes **pip** a partir de uma instalação anterior foram criadas ao raiz, o que faz com que o erro de permissão. A solução é remover os pacotes instalados pelo raiz. Utilize os seguintes passos para concluir esta tarefa:

1. Aceda a /usr/local/lib/python2.7/site-packages
2. Criar lista pacotes por raiz:`ls -l | grep root`
3. Desinstale pacotes de passo2 para:`sudo rm -rf {package name}`
4. Reinstale o Python.

## <a name="azure-iot-hub-issues"></a>Problemas de concentrador IoT Azure

Se tiver aprovisionado Azure IoT concentrador com `azure-cli`, e precisar de uma ferramenta para gerir os dispositivos estabelecer ligação ao seu centro IoT, experimente as seguintes ferramentas:

### <a name="device-explorer"></a>Explorador de dispositivo

[Dispositivo Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) é executada no seu computador local do Windows e liga-se ao seu centro IoT no Azure. Comunica com [os pontos finais de IoT concentrador](iot-hub-devguide.md)com a seguir:

- *Gestão de identidades do dispositivo* para aprovisionar e gerir dispositivos registado com o seu centro IoT.
- *Receber dispositivo à nuvem* para permitem-lhe monitorizar as mensagens enviadas a partir do seu dispositivo para o seu centro IoT.
- *Enviar na nuvem-a-dispositivo* para que possa enviar mensagens para os seus dispositivos do seu centro IoT.

Configurar o seu `IoT hub connection string` dentro esta ferramenta para utilizar todas as suas capacidades.

### <a name="iot-hub-explorer"></a>Concentrador IoT Explorer

[Concentrador IoT Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) é uma ferramenta de clip múltiplas plataformas da amostra para gerir clientes do dispositivo. A ferramenta de permite-lhe gerir os dispositivos no registo identidade, monitorizar mensagens de dispositivo para nuvem e enviar comandos de dispositivo de nuvem.

Para instalar a versão mais recente do (pré-lançamento) da ferramenta do iothub explorer, execute o seguinte comando no seu ambiente da linha de comandos:

```
npm install -g iothub-explorer@latest
```

Pode utilizar o seguinte comando para obter ajuda adicional sobre todos os comandos do iothub explorer e os respetivos parâmetros:

```bash
iothub-explorer help
```

### <a name="use-azure-portal-to-manage-your-resources"></a>Utilizar o portal do Azure para gerir os seus recursos

Nestas lições uma experiência de clip completa é fornecida para criar e gerir os seus recursos Azure. Também poderá pretender utilizar o [Azure portal](../azure-portal-overview.md) para ajudar a aprovisionar, gerir e depurar os recursos do Azure.

## <a name="azure-storage-issues"></a>Problemas de armazenamento Azure

[Explorador de armazenamento do Microsoft Azure (pré-visualização)](http://storageexplorer.com) é uma aplicação autónoma da Microsoft que lhe permite trabalhar com dados de armazenamento do Windows Azure no Windows, macOS e Linux. Esta ferramenta permite-lhe ligar à sua tabela e ver os dados. Pode utilizar esta ferramenta para resolver os problemas de armazenamento do Windows Azure.
