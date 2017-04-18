<properties
  pageTitle="Conjunto de aplicações do Azure IoT e Apps de lógica | Microsoft Azure"
  description="Um tutorial sobre como ligar lógica aplicações para o conjunto de aplicações do Azure IoT para o processo de negócio."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="08/16/2016"
  ms.author="araguila"/>
  
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Tutorial: Ligar a aplicação de lógica a sua solução Azure IoT Suite monitorização remota pré-configurado

O [Conjunto de aplicações do Microsoft Azure IoT] [ lnk-internetofthings] monitorização solução pré-configurado remota é uma ótima forma para começar a trabalhar rapidamente com um conjunto de ponto a ponto funcionalidade exemplifies uma solução IoT. Neste tutorial explica como adicionar lógica de aplicação para o Microsoft Azure IoT Suite monitorização solução pré-configurado remota. Estes passos demonstram como que pode tomar a sua solução IoT ainda mais ligando-lo para um processo empresarial.

_Se procura instruções sobre como aprovisionar uma monitorização remota pré-configurado solução, consulte o artigo [Tutorial: começar a trabalhar com as soluções IoT pré-configurado][lnk-getstarted]._

Antes de iniciar este tutorial, deve:

- Aprovisionar monitorização remota pré-configurado solução na sua subscrição do Azure.

- Crie uma conta de SendGrid para permitem-lhe enviar um e-mail que accionadores o processo de negócio. Pode inscrever-se para uma conta de avaliação gratuita na [SendGrid](https://sendgrid.com/) ao clicar em **experimentar gratuitamente**. Depois de ter registado para a sua conta de avaliação gratuita, tem de criar uma [chave de API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) no SendGrid que concede permissões para enviar e-mails. Precisa desta tecla API mais tarde no tutorial.

Partindo do princípio de que já tenha aprovisionado monitorização remota pré-configurado solução, navegue para o grupo de recursos para essa solução no [portal do Azure][lnk-azureportal]. O grupo de recursos tem o mesmo nome que o nome da solução que escolheu quando a solução de monitorização remota aprovisionado. No grupo de recursos, pode ver todos os recursos Azure aprovisionados para a sua solução, à exceção da aplicação do Azure Active Directory que pode encontrar no Portal clássica do Azure. A captura de ecrã seguinte mostra uma pá de **grupo de recursos** de exemplo para uma solução de pré-configurado controlo remota:

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

Para começar, configurar a aplicação de lógica para utilizar com a solução pré-configurado.

## <a name="set-up-the-logic-app"></a>Configurar a aplicação de lógica

1. Clique em __Adicionar__ no topo da sua pá de grupo de recursos no portal do Azure.

2. Procurar __Lógica de aplicação__, selecione-lo e, em seguida, clique em **Criar**.

3. Preencha o __nome__ e utilize a mesma **subscrição** e **grupo de recursos** que utilizou quando a sua solução de controlo remota aprovisionado. Clique em __Criar__.

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. Quando concluir a sua implementação, pode ver que a aplicação de lógica está listada como um recurso no seu grupo de recursos.

5. Clique na aplicação de lógica para navegar para o pá lógica aplicação, selecione o modelo de **Aplicação de lógica vazia** para abrir o **Estruturador de fluxos de lógica de aplicações**.

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. Selecione o __pedido__. Esta ação Especifica que um pedido de HTTP recebido com um JSON específico formatados carga útil actos como um accionador.

7. Cole o seguinte para o esquema de JSON corpo pedido:

    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
    
    > [AZURE.NOTE] Pode copiar o URL para a mensagem HTTP depois de guardar a aplicação de lógica, mas primeiro tem de adicionar uma ação.

8. Clique em __+ novo passo__ em seu accionador manual. Em seguida, clique em **Adicionar uma ação**.

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. Procurar **SendGrid - enviar mensagens de e-mail** e clique na mesma.

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. Introduza um nome para a ligação, tais como **SendGridConnection**, introduza a **Chave de API SendGrid** que criou quando configurar a conta SendGrid e clique em **Criar**.

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. Adicione endereços de e-mail que é o proprietário para o **partir de** e **para** campos. Adicione **monitorização alerta [DeviceId] remota** para o campo **Assunto** . No campo **Corpo da mensagem de correio electrónico** , adicione **dispositivo [DeviceId] comunicou [measurementName] com valor [measuredValue].** Pode adicionar **[DeviceId]**, **[measurementName]**e **[measuredValue]** clicando na secção **pode inserir dados a partir de passos anteriores** .

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. Clique em __Guardar__ no menu superior.

13. Clique em acionador o **pedido** e copie o valor de __Http Post para este URL__ . Este URL é necessário posteriormente neste tutorial.

> [AZURE.NOTE] Aplicações de lógica permitem-lhe executar [vários tipos diferentes de ação] [ lnk-logic-apps-actions] incluindo ações no Office 365. 

## <a name="set-up-the-eventprocessor-web-job"></a>Configurar a tarefa de Web EventProcessor

Nesta secção, ligue-se a solução pré-configurado para a aplicação de lógica que criou. Para concluir esta tarefa, adicione o URL para acionar a aplicação de lógica para a ação que é acionada quando um valor de sensor dispositivo excede um determinado limiar.

1. Utilizar o cliente git para clonar a versão mais recente da [monitorização de azure iot remote github repositório][lnk-rmgithub]. Por exemplo:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. No Visual Studio, abra o __RemoteMonitoring.sln__ da cópia local do repositório.

3. Abra o ficheiro __ActionRepository.cs__ na **infraestrutura\\repositório** pasta.

4. Atualize o dicionário de **actionIds** com a __Mensagem de Http para este URL__ anotou da sua aplicação de lógica da seguinte forma:

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. Guardar as alterações na solução e saia do Visual Studio.

## <a name="deploy-from-the-command-line"></a>Implementar a partir da linha de comandos

Nesta secção, implementar a versão actualizada da solução de controlo remota para substituir a versão atualmente em execução no Azure.

1. Seguir a [configuração da compilação de Dev Center] [ lnk-devsetup] instruções para configurar o seu ambiente para implementação.

2.  Para implementar localmente, siga a [implementação local] [ lnk-localdeploy] instruções.

3.  Para implementar na nuvem e atualizar a sua implementação nuvem existente, siga a [implementação de nuvem] [ lnk-clouddeploy] instruções. Utilize o nome da sua implementação original como o nome de implementação. Por exemplo, se a implementação original foi chamada **demologicapp**, utilize o seguinte comando:

    ``
    build.cmd cloud release demologicapp
    ``
    
    Quando executa o script de compilação, certifique-se de que utiliza a mesma conta Azure, subscrição, região e instância do Active Directory que utilizou quando a solução aprovisionado.

## <a name="see-your-logic-app-in-action"></a>Ver a sua aplicação de lógica em ação

A solução pré-configurado monitorização remota tem duas regras de configurar por predefinição, quando aprovisionar o uma solução. Ambas as regras são no dispositivo **SampleDevice001** :

* Temperatura > 38.00
* Humidade > 48,00

A regra de temperatura accionadores a ação **Elevar alarme** e a regra humidade accionadores a ação **EnviarMensagem** . Partindo do princípio que utilizou o mesmo URL para ambas as ações a classe de **ActionRepository** , a sua aplicação de lógica accionadores para um dos regra. Ambas as regras utilizam SendGrid para enviar uma mensagem de e-mail para **o endereço com detalhes do alerta** .

> [AZURE.NOTE] A aplicação de lógica continua a acionar sempre que o limiar de que existe correspondência. Para evitar e-mails desnecessários, pode desativar as regras no seu portal de solução ou desativar a aplicação de lógica no [portal do Azure][lnk-azureportal].

Para além de receber mensagens de e-mail, também pode ver quando a aplicação de lógica é executado no portal:

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Próximos passos

Agora que tenha utilizado uma aplicação de lógica para ligar a solução pré-configurado a um processo empresarial, pode obter mais informações sobre as opções para personalizar as soluções pré-configurado:

- [Utilizar telemetria dinâmica com a solução pré-configurado monitorização remota][lnk-dynamic]
- [Dispositivo informações metadados da solução pré-configurado monitorização remoto][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
