## <a name="create-a-device-management-enabled-iot-hub"></a>Criar um dispositivo IoT concentrador com capacidade de gestão

Desde que esteja gestão de dispositivos do concentrador de IoT na pré-visualização, tem de criar um concentrador de IoT de gestão de activada dispositivo. Quando a gestão de dispositivos do concentrador de IoT atinge disponibilidade geral, este tutorial será atualizado. Os passos seguintes mostram-lhe como concluir esta tarefa através do portal Azure.

1.  Inicie sessão no [portal do Azure].
2.  Na Jumpbar, clique em **Novo**, em seguida, clique em **Da Internet de ações**e, em seguida, clique em **Azure IoT concentrador**.

    ![][img-new-hub]

3.  Na pá **IoT concentrador** , selecione a configuração para o seu centro IoT.

    ![][img-configure-hub]

  -   Na caixa **nome** , introduza um nome para o seu centro IoT. Se o **nome** for válidas e disponíveis, aparece uma marca de verificação verde na caixa **nome** .
  -   Selecione uma **camada de preços e a escala**. Neste tutorial não requer uma camada específica.
  -   No **grupo de recursos**, crie um novo grupo de recursos ou selecione uma existente. Para mais informações, consulte o artigo [utilizar grupos de recursos para gerir os recursos do Azure].
  -   Selecione a caixa para **Ativar a gestão de dispositivos - pré-visualização**.
  -   Numa **localização**, selecione a localização para alojar o seu centro IoT. Gestão de dispositivos do concentrador de IoT só está disponível no Leste dos EUA, Europa Norte e Ásia durante a pré-visualização pública.

    > [AZURE.NOTE]Se não selecionar a caixa para **Ativar a gestão de dispositivos**, as amostras não funcionam.<br/>Ao selecionar **Ativar a gestão de dispositivos**, crie uma pré-visualização IoT concentrador suportado apenas em Leste dos EUA, Europa Norte e Ásia e não se destina cenários de produção. Não consegue migrar dispositivos para e terminar concentradores com capacidade de gestão de dispositivo.

4.  Quando tiver escolhido seu centro IoT opções de configuração, clique em **Criar**. Pode demorar alguns minutos para Azure criar o seu centro IoT. Para verificar o estado, pode monitorizar o progresso de **Startboard** ou no painel de **notificações** .

    ![][img-monitor]

5.  Quando o concentrador IoT ter sido criado com êxito, pá para o seu Centro serão abertos automaticamente. Anote o **nome do anfitrião**e, em seguida, clique em **partilhado as políticas de acesso**.

    ![][img-keys]

6.  Clique na política de **iothubowner** , em seguida, copiar e tome nota da cadeia de ligação na pá **iothubowner** . Copie-o para uma localização que pode aceder a mais tarde, porque tem de concluir este tutorial.

    > [AZURE.NOTE] Em cenários de produção, certifique-se de que evite utilizar as credenciais de **iothubowner** .

    ![][img-connection]

Agora ter criado um dispositivo com capacidade de gestão de IoT concentrador. Tem a cadeia de ligação para concluir este tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta secção, pode utiliza uma ferramenta de nó chamada [IoT concentrador Explorer] [ iot-hub-explorer] para criar uma identidade de dispositivo para este tutorial.

1. Execute o seguinte no seu ambiente da linha de comandos:

    npm instalar -giothub-explorer@latest

2. Em seguida, execute o seguinte comando para iniciar sessão no seu centro, renovados substituí-los `{service connection string}` com a cadeia de ligação IoT concentrador que copiou anteriormente:

    início de sessão do iothub explorer "{cadeia de ligação serviço}"

3. Por fim, criar uma nova identidade de dispositivo denominada `myDeviceId` com o comando:

    iothub explorer criar myDeviceId – cadeia de ligação

Anote a cadeia de ligação de dispositivo a partir do resultado. Nesta cadeia de ligação é utilizada pela aplicação do dispositivo para ligar ao seu centro IoT como um dispositivo.

![][img-identity]

Consulte a [introdução com IoT concentrador] [ lnk-getstarted] para uma forma de criar identidades do dispositivo através de programação.

<!-- images and links -->
[img-new-hub]: media/iot-hub-get-started-create-hub-pp/image1.png
[img-configure-hub]: media/iot-hub-get-started-create-hub-pp/image2.png
[img-monitor]: media/iot-hub-get-started-create-hub-pp/image3.png
[img-keys]: media/iot-hub-get-started-create-hub-pp/image4.png
[img-connection]: media/iot-hub-get-started-create-hub-pp/image5.png
[img-identity]: media/iot-hub-get-started-create-hub-pp/devidentity.png

[Portal do Azure]: https://portal.azure.com/
[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[Utilizar grupos de recursos para gerir os recursos do Azure]: ../articles/azure-portal/resource-group-portal.md
