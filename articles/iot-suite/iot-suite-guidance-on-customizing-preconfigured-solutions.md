<properties
    pageTitle="Personalizar soluções pré-configuradas | Microsoft Azure"
    description="Fornece orientações sobre como personalizar as soluções do conjunto de aplicações do IoT Azure pré-configuradas automaticamente."
    services=""
    suite="iot-suite"
    documentationCenter=".net"
    authors="aguilaaj"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/11/2016"
     ms.author="aguilaaj"/>

# <a name="customize-a-preconfigured-solution"></a>Personalizar uma solução pré-configurado

As soluções pré-configurado fornecidas com o conjunto de aplicações do Azure IoT demonstram os serviços incluído no conjunto de trabalhar em conjunto para fornecer uma solução de fim para fim. A partir do ponto de partida, existem várias localizações na qual pode expandir e personalizar a solução para cenários específicos. As seguintes secções descrevem estes pontos de personalização comuns.

## <a name="finding-the-source-code"></a>Localizar o código de origem

O código fonte para as soluções pré-configurado está disponível no GitHub nos seguintes repositórios:

- Monitorização remota: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Manutenção de aspeto do Office: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

O código fonte para as soluções pré-configurado é fornecido para demonstrar padrões e práticas utilizadas para implementar a funcionalidade de fim para fim de uma solução de IoT utilizando o conjunto de aplicações do Azure IoT. Pode encontrar mais informações sobre como criar e implementar soluções nos repositórios GitHub.

## <a name="changing-the-preconfigured-rules"></a>Alterar as regras pré-configurado

Solução de controlo remota inclui três tarefas [Azure da cadeia Analytics](https://azure.microsoft.com/services/stream-analytics/) para implementar o lógica de informações, telemetria e as regras do dispositivo apresentada para a solução.

As tarefas de análise da três cadeia e a respectiva sintaxe é descrita em profundidade na [monitorização de solução pré-configurado guiadas remota](iot-suite-remote-monitoring-sample-walkthrough.md). 

Pode editar estas tarefas diretamente para alterar a lógica ou adicionar lógica específica ao seu cenário. Pode encontrar as tarefas de análise da cadeia da seguinte forma:
 
1. Aceda ao [Azure portal](https://portal.azure.com).
2. Navegue para o grupo de recursos com o mesmo nome que a sua solução IoT. 
3. Selecione a tarefa de análise do Azure da cadeia que pretende modificar. 
4. Pare a tarefa selecionando **Parar**o conjunto de comandos. 
5. Edite as entradas, consultas e saídas.

    Uma simples modificação é alterar a consulta para a tarefa de **regras** utilizar um **"<"** em vez de um **">"**. O portal de solução ainda mostrará **">"** ao editar uma regra, mas irá reparar o comportamento é invertido devido a alteração da tarefa de subjacente.

6. Iniciar a tarefa

> [AZURE.NOTE] Dashboard de monitorização remoto depende dados específicos, para que alterar as tarefas pode causar o dashboard falha.

## <a name="adding-your-own-rules"></a>Adicionar as suas próprias regras

Além da alteração as tarefas de análise da cadeia de Azure pré-configurado, pode utilizar o portal do Azure para adicionar novas tarefas ou adicionar novas consultas para tarefas já existentes.

## <a name="customizing-devices"></a>Personalização de dispositivos

Uma das atividades de extensão mais comuns está a trabalhar com dispositivos específicos do seu cenário. Existem vários métodos para trabalhar com dispositivos. Estes métodos incluem alterar um dispositivo simulado para corresponder ao seu cenário ou utilizando o [SDK do dispositivo de IoT][] para ligar o seu dispositivo físico para a solução.

Para um guia passo a passo para adicionar dispositivos para a solução pré-configurado monitorização remota, consulte o artigo [Ligar dispositivos do conjunto de aplicações Iot](iot-suite-connecting-devices.md) e [Remoto monitorização C SDK exemplo](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) que foi concebido para funcionar com a solução pré-configurado monitorização remota.

### <a name="creating-your-own-simulated-device"></a>Criar a sua simulado dispositivo

Incluído no remoto monitorização solução código fonte (acima referenciado), é uma simulator do .NET. Este simulator é uma aprovisionado como parte da solução e pode ser alterada para enviar metadados diferentes, telemetria ou responder a comandos diferentes.

O simulator pré-configurado da solução pré-configurado monitorização remoto é um dispositivo arrefecedor que emite telemetria temperatura e humidade, pode modificar simulator no projeto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) quando tiver forked do repositório de GitHub.

### <a name="available-locations-for-simulated-devices"></a>Localizações disponíveis para dispositivos simuladas

É o conjunto predefinido de localizações no Seattle/Redmond, Washington, Estados Unidos da América. Pode alterar estes locais no [SampleDeviceFactory.cs][lnk-sample-device-factory].


### <a name="building-and-using-your-own-physical-device"></a>Criar e através do dispositivo (físico)

O [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) fornecem bibliotecas para ligar vários tipos de dispositivos (idiomas e sistemas operativos) para IoT soluções.

## <a name="modifying-dashboard-limits"></a>Modificar limites de dashboard

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Número de dispositivos apresentados na lista pendente de dashboard

A predefinição é 200. Pode alterar este número na [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Número de PIN para apresentar no controlo de mapa do Bing

A predefinição é 200. Pode alterar este número na [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Período de tempo do gráfico de telemetria

A predefinição é 10 minutos. Pode alterar no [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configurar manualmente o funções da aplicação

O procedimento seguinte descreve como adicionar as funções de **administrador** e **só de leitura** da aplicação a uma solução pré-configurado. Tenha em atenção que as soluções pré-configurado aprovisionadas a partir do site azureiotsuite.com já incluem as funções de **administrador** e **só de leitura** .

Membros da função **só de leitura** podem ver o dashboard e da lista de dispositivos, mas não são permitidos para dispositivos de adicionar, alterar os atributos do dispositivo ou enviar comandos.  Os membros a função de **administrador** têm acesso total a todas as funcionalidades da solução.

1. Aceda ao [portal clássica Azure][lnk-classic-portal].

2. Selecione **do Active Directory**.

3. Clique no nome do inquilino AAD que utilizou quando a sua solução aprovisionado.

4. Clique em **aplicações**.

5. Clique no nome da aplicação que corresponde ao seu nome de solução pré-configurado. Se não vir a aplicação na lista, selecione **aplicações proprietário da minha empresa** em **Mostrar** pendente e clique na marca de verificação.

6.  Na parte inferior da página, clique em **Gerir manifesto** e, em seguida, **Transferir manifesto**.

7. Isto transfere um ficheiro de .json para o seu computador local.  Abra este ficheiro para edição num editor de texto da sua escolha.

8. Na terceira linha do ficheiro .json, irá encontrar:

  ```
  "appRoles" : [],
  ```
  Substitua-o seguinte procedimento:

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. Guarde o ficheiro atualizado .json (pode substituir o ficheiro existente).

10.  No Portal de gestão do Azure, na parte inferior da página, selecione **Gerir manifesto** , em seguida, **Carregar manifesto** para carregar o ficheiro de .json que guardou no passo anterior.

11. Agora ter adicionado as funções de **administrador** e **só de leitura** para a sua aplicação.

12. Para atribuir uma das seguintes funções a um utilizador no seu diretório, consulte o artigo [permissões no azureiotsuite.com site][lnk-permissions].

## <a name="feedback"></a>Comentários

Possui uma personalização que pretende ver abrangidas neste documento? Adicione as sugestões de funcionalidade para [Chamadas de voz do utilizador](https://feedback.azure.com/forums/321918-azure-iot)ou comentar abaixo, neste artigo. 

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as opções para personalizar as soluções pré-configurado, consulte o artigo:

- [Ligar a aplicação de lógica a sua solução Azure IoT Suite monitorização remota pré-configurado][lnk-logicapp]
- [Utilizar telemetria dinâmica com a solução pré-configurado monitorização remota][lnk-dynamic]
- [Dispositivo informações metadados da solução pré-configurado monitorização remoto][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[Dispositivo IoT SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
