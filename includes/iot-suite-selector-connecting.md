> [AZURE.SELECTOR]
- [C no Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C em Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [NODE.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## <a name="scenario-overview"></a>Descrição geral do cenário

Neste cenário, pode criar um dispositivo que envia a telemetria seguinte para o controlo remoto monitorização [solução pré-configurado][lnk-what-are-preconfig-solutions]:

- Temperatura externa
- Temperatura interna
- Humidade

Por simplicidade, valores de exemplo de gera o código no dispositivo, mas Encorajamo-lo para expandir a amostra ligando sensores reais para o dispositivo e envio de telemetria real.

Para concluir este tutorial, é necessário uma conta Azure activa. Se não tiver uma conta, pode criar uma conta de demonstração gratuita em apenas alguns minutos. Para obter detalhes, consulte a [Versão de avaliação gratuita do Azure][lnk-free-trial].

## <a name="before-you-start"></a>Antes de iniciar

Antes de escrever qualquer código para o dispositivo, tem de aprovisionar a solução pré-configurado monitorização remota e, em seguida, fornecer um novo dispositivo personalizado essa solução.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Aprovisionar Solução pré-configurado monitorização remota

O dispositivo que criar neste tutorial envia dados para uma instância da [monitorização remota] [ lnk-remote-monitoring] pré-configurados solução. Se já não tiver aprovisionado monitorização remota pré-configurados solução na sua conta Azure, siga os passos abaixo:

1. Na página <https://www.azureiotsuite.com/> , clique em **+** para criar uma nova solução.

2. Clique em **Seleccionar** no painel de **controlo remoto** para criar a nova solução.

3. Na página **Criar remoto solução de controlo** , introduza um **nome da solução** da sua escolha, seleccione a **região** que pretende implementar para e seleccione a subscrição Azure pretende utilizar. Em seguida, clique em **Criar solução**.

4. Aguarde até concluir o processo de aprovisionamento.

> [AZURE.WARNING] As soluções pré-configuradas utilizam serviços Azure facturáveis. Certifique-se de que remover a solução pré-configurado da sua subscrição quando tiver terminado com o mesmo para evitar quaisquer cobranças desnecessárias. Pode remover completamente uma solução pré-configurado da subscrição visitando a página de <https://www.azureiotsuite.com/> .

Quando terminar o processo de aprovisionamento da solução de controlo remoto, clique em **Iniciar** para abrir o dashboard de solução no browser.

![][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Aprovisionar o dispositivo na solução de controlo remoto

> [AZURE.NOTE] Se já tiver aprovisionado um dispositivo na sua solução, pode ignorar este passo. Terá de conhecer as credenciais de dispositivo ao criar a aplicação cliente.

Para um dispositivo estabelecer ligação com a solução pré-configurado, tem que identificar-se ao concentrador IoT utilizando credenciais válidas. Pode obter as credenciais de dispositivo do dashboard solução. Incluir as credenciais do dispositivo na aplicação cliente mais tarde nesta iniciação. 

Para adicionar um novo dispositivo à sua solução de controlo remota, efectue os seguintes passos no dashboard solução:

1.  No canto inferior esquerdo do dashboard, clique em **Adicionar um dispositivo**.

    ![][1]

2.  No painel de **Dispositivo personalizado** , clique em **Adicionar novo**.

    ![][2]

3.  Escolher **Deixar-me a definir o meu próprio ID de dispositivo**, introduza um ID de dispositivo, tal como **mydevice**, clique em **Verificar ID** para verificar que esse nome ainda não estiver em utilização e, em seguida, clique em **Criar** para aprovisionar o dispositivo.

    ![][3]

5. Anote as credenciais de dispositivo (ID de dispositivo, o nome de anfitrião do IoT concentrador e chave de dispositivo), a aplicação cliente necessita deles para estabelecer ligação com a solução de controlo remota. Em seguida, clique em **concluído**.

    ![][4]

6. Certifique-se que o dispositivo apresenta na secção de dispositivos. O estado do dispositivo está **pendente** até que o dispositivo estabelece uma ligação para a solução de controlo remota.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/