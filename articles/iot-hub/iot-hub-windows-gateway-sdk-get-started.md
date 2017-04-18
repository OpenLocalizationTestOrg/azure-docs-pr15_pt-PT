<properties
    pageTitle="Introdução ao SDK IoT concentrador Gateway | Microsoft Azure"
    description="Azure IoT Gateway SDK instruções passo a passo utilizando o Windows para ilustrar conceitos chave que deve compreender quando utiliza o SDK do Azure IoT Gateway."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-windows"></a>Azure IoT Gateway SDK (beta) - começar a utilizar o Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como construir a amostra

Antes de começar, tem de [configurar o seu ambiente de desenvolvimento] [ lnk-setupdevbox] para trabalhar com o SDK no Windows.

1. Abra uma linha de comandos de **linha de comandos para programadores para VS2015** .
2. Navegue para a pasta de raiz na sua cópia local do repositório **azure-iot gateway sdk** .
3. Executar o **ferramentas\\build.cmd** script. Este script cria um ficheiro de solução do Visual Studio, constrói a solução e executa os testes. Pode encontrar a solução do Visual Studio na pasta **construir** na sua cópia local do repositório **azure-iot gateway sdk** .

## <a name="how-to-run-the-sample"></a>Como executar a amostra

1. O script **build.cmd** cria uma pasta denominada **construir** na sua cópia local do repositório. Esta pasta contém dois módulos utilizados neste exemplo.

    O script de compilação coloca **logger_hl.dll** na **construir\\módulos\\registo\\depurar** pasta e **hello_world_hl.dll** na **construir\\módulos\\hello_world\\depurar** pasta. Utilize estes caminhos para o valor de **caminho de módulo** conforme mostrado no ficheiro de definições JSON abaixo.

2. O ficheiro **hello_world_win.json** na **amostras\\hello_world\\src** pasta é um ficheiro de definições de JSON de exemplo para o Windows que pode utilizar para executar o exemplo. As definições de JSON de exemplo apresentadas abaixo assume que clonar do repositório de IoT Gateway SDK na raiz da sua unidade **c:** . Se tiver transferido para outra localização, terá de ajustar os valores de **caminho de módulo** na **amostras\\hello_world\\src\\hello_world_win.json** ficheiro em conformidade.

3. Para o módulo **logger_hl** , na secção de **argumentos** , defina o valor de **nome de ficheiro** para o nome e o caminho do ficheiro que irá conter os dados do registo.

    Este é um exemplo de um ficheiro de definições de JSON para o Windows que vai escrever para o ficheiro **txt** na raiz da sua unidade **c:** .

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. Numa linha de comandos, navegue para a pasta raiz da sua cópia local do repositório **azure-iot gateway sdk** .
4. Execute o seguinte comando:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md