<properties
     pageTitle="Utilizar o portal do Azure para configurar o carregamento de ficheiro | Microsoft Azure"
     description="Uma descrição geral de como configurar o carregamento de ficheiro através do portal Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="configure-file-uploads-using-the-azure-portal"></a>Configurar carregamentos de ficheiros através do portal Azure

## <a name="file-upload"></a>Carregamento de ficheiro

Para utilizar a [funcionalidade no Centro de IoT de envio de ficheiros][lnk-upload], tem primeiro de associar uma conta de armazenamento do Windows Azure seu centro. Selecione as definições de **envio de ficheiros** para apresentar uma lista das propriedades de carregamento de ficheiro para o concentrador de IoT que está a ser modificada.

![][13]

**O contentor de armazenamento**: utilizar o portal do Azure para selecionar um contentor blob numa conta de armazenamento do Windows Azure na sua subscrição atual do Azure para associar o seu centro IoT. Se for necessário, pode criar uma conta de armazenamento do Windows Azure no contentor pá e blob de **contas de armazenamento** na pá **contentores** . Concentrador IoT gera automaticamente SA URIs com permissões de escrita a este contentor blob para dispositivos utilizar quando estes carregar ficheiros.

![][14]

**Receber notificações para os ficheiros carregados**: Ativar ou desativar as notificações de carregamento de ficheiros através do botão de alternar.

**TTL SA**: esta definição está a hora-to-live dos URI SA devolvidos ao dispositivo por IoT concentrador. Definido para uma hora por predefinição, mas pode ser personalizado para outros valores utilizando o controlo de deslize.

**Notificação do ficheiro definições default TTL**: O tempo de vida de um ficheiro de notificações de carregamento antes de tiver expirado. Definido para um dia por predefinição, mas pode ser personalizado para outros valores utilizando o controlo de deslize.

**Contagem de entrega máximo de notificação de ficheiros**: O número de vezes que o concentrador IoT tenta entregar um ficheiro notificações de carregamento. Configurar para 10 por predefinição, mas pode ser personalizado para outros valores utilizando o controlo de deslize.

![][15]

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre as capacidades de carregamento de ficheiro do concentrador de IoT, consulte o artigo [carregar ficheiros a partir de um dispositivo] [ lnk-upload] no Guia do programador.

Siga estas ligações para saber mais sobre a gestão de Azure IoT concentrador:

- [Em volume gerir IoT dispositivos][lnk-bulk]
- [Métricas de utilização][lnk-metrics]
- [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]
- [Proteger a sua solução IoT desde o início para cima][lnk-securing]


  [13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
  [14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
  [15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md