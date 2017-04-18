## <a name="create-an-iot-hub"></a>Criar um concentrador de IoT

Crie um concentrador de IoT para o seu dispositivo ligar à simulada. Os passos seguintes mostram-lhe como concluir esta tarefa, utilizando o portal do Azure.

1. Inicie sessão no [portal do Azure][lnk-portal].

2. Na Jumpbar, clique em **Novo** > **Internet das coisas** > **Azure IoT concentrador**.

    ![Azure portal Jumpbar][1]

3. Na pá **IoT concentrador** , selecione a configuração para o seu centro IoT.

    ![Pá concentrador de IoT][2]

    * Na caixa **nome** , introduza um nome para o seu centro IoT. Se o **nome** for válidas e disponíveis, aparece uma marca de verificação verde na caixa **nome** .
    * Selecione uma [camada de preços e a escala][lnk-pricing]. Neste tutorial não requer uma camada específica. Neste tutorial, utilize a camada F1 gratuita.
    * No **grupo de recursos**, crie um novo grupo de recursos ou selecione uma existente. Para obter mais informações, consulte o artigo [utilizar grupos de recursos para gerir os recursos do Azure][lnk-resource-groups].
    * Numa **localização**, selecione a localização para alojar o seu centro IoT. Para este tutorial, escolha a sua localização mais próxima.

4. Quando tiver escolhido seu centro IoT opções de configuração, clique em **Criar**.  Pode demorar alguns minutos para Azure criar o seu centro IoT. Para verificar o estado, pode monitorizar o progresso de Startboard ou no painel de notificações.

    ![Novo estado de concentrador IoT][3]

5. Quando o concentrador IoT ter sido criado com êxito, clique em novo mosaico do seu centro IoT no portal do Azure para abrir o pá para o concentrador IoT novo. Anote o **nome do anfitrião**e, em seguida, clique em **partilhado as políticas de acesso**.

    ![Nova Pá de concentrador IoT][4]

6. Na pá **políticas de acesso partilhado** , clique na política de **iothubowner** e, em seguida, copie e tome nota da cadeia de ligação na pá **iothubowner** . Para obter mais informações, consulte o artigo [o controlo de acesso] [ lnk-access-control] no "manual de programador Azure IoT concentrador".

    ![Pá de políticas de acesso partilhado][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
