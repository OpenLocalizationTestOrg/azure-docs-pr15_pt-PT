## <a name="view-device-telemetry-in-the-dashboard"></a>Ver dispositivo telemetria no dashboard

O dashboard na solução de controlo remoto permite-lhe visualizar a telemetria que os dispositivos de enviar a IoT concentrador.

1. No browser, regresse ao dashboard de solução de controlo remoto, clique em **dispositivos** no painel do lado esquerdo para navegar para a **lista de dispositivos**.

2. Na **lista de dispositivos**, deverá ver o estado do dispositivo está agora **em execução**.

    ![][18]

3. Clique em **Dashboard** para voltar ao dashboard, seleccione o dispositivo no **dispositivo para a vista de** pendente para visualizar o respectiva telemetria. Telemetria da aplicação de exemplo é 50 unidades de temperatura interna, 55 unidades externas temperatura e 50 unidades para a humidade. Tenha em atenção que, por predefinição, o dashboard apresenta apenas os valores de temperatura e humidade.

    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Enviar um comando para o dispositivo

O dashboard na solução de controlo remoto permite-lhe enviar comandos para os dispositivos através de IoT concentrador. Por exemplo, na solução de controlo remota pode enviar um comando para definir a temperatura interna de um dispositivo.

1. No dashboard solução monitorização remota, clique em **dispositivos** no painel do lado esquerdo para navegar para a **lista de dispositivos**.

2. Clique em **ID de dispositivo** para o dispositivo na **lista de dispositivos**.

3. No painel de **Detalhes do dispositivo** , clique em **comandos**.

    ![][13]

4. Na pendente **comando** , seleccione **SetTemperature**e, em seguida, introduza um novo valor de temperatura de **temperatura** . Clique em **Enviar comando** para enviar o comando para o dispositivo.

    ![][14]

    > [AZURE.NOTE] O histórico de comandos mostra inicialmente o estado de comando como **pendentes**. Quando o dispositivo reconhece o comando, o estado muda para o **êxito**.

5. No dashboard, certifique-se de que o dispositivo está a enviar agora 75 como o novo valor de temperatura.

## <a name="next-steps"></a>Passos seguintes

O artigo [Personalizar soluções pré-configuradas] [ lnk-customize] descreve alguns modos como pode expandir este exemplo. Extensões possíveis incluem a utilização reais sensores e execução de comandos adicionais.

Pode obter mais informações sobre as [permissões no azureiotsuite.com site][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
