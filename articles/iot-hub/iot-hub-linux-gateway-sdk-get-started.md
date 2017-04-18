<properties
    pageTitle="Introdução ao SDK IoT concentrador Gateway | Microsoft Azure"
    description="Este tutorial Azure IoT Gateway SDK utiliza Linux para ilustrar conceitos chave que deve compreender ao utilizar o SDK do Azure IoT Gateway."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-linux"></a>Azure IoT Gateway SDK (beta) - começar a utilizar Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como construir a amostra

Antes de começar, tem de [configurar o seu ambiente de desenvolvimento] [ lnk-setupdevbox] para trabalhar com o SDK no Linux.

1. Abra uma shell.
2. Navegue para a pasta de raiz na sua cópia local do repositório **azure-iot gateway sdk** .
3. Execute o script **tools/build.sh** . Este script utiliza o utilitário **cmake** para criar uma pasta denominada **construir** na pasta raiz da sua cópia local do repositório **azure-iot gateway sdk** e gerar um makefile. O script, em seguida, constrói a solução e executa os testes.

> [AZURE.NOTE]  Sempre que executar o script **build.sh** , elimina e, em seguida, recria a pasta **construir** na pasta raiz da sua cópia local do repositório **azure-iot gateway sdk** .

## <a name="how-to-run-the-sample"></a>Como executar a amostra

1. O script **build.sh** gera respectiva saída na pasta **construir** na sua cópia local do repositório **azure-iot gateway sdk** . Isto inclui dois módulos utilizados neste exemplo.

    O script de compilação coloca **liblogger_hl.so** na **módulos/compilação/registo/** pasta e **libhello_world_hl.so** na **módulos/compilação/hello_world/** pasta. Utilize estes caminhos para o valor de **caminho de módulo** conforme mostrado no ficheiro de definições JSON abaixo.

2. O ficheiro **hello_world_lin.json** na pasta **hello_world/amostras/src** é um exemplo de ficheiro de definições JSON para Linux que pode utilizar para executar o exemplo. As definições de JSON de exemplo apresentadas abaixo assume que será executada a amostra na raiz da sua cópia local do repositório **azure-iot gateway sdk** .

3. Para o módulo **logger_hl** , na secção de **argumentos** , defina o valor de **nome de ficheiro** para o nome e o caminho do ficheiro que irá conter os dados do registo.

    Este é um exemplo de um ficheiro de definições de JSON para Linux escreverá a **txt** para a pasta onde executar da amostra.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
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

3. Na sua shell, navegue para a pasta do **azure-iot gateway sdk** .
4. Execute o seguinte comando:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
