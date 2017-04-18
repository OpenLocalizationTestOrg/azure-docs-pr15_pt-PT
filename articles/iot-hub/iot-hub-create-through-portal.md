<properties
     pageTitle="Utilizar o portal do Azure para criar um concentrador de IoT | Microsoft Azure"
     description="Uma descrição geral sobre como criar e gerir concentradores Azure IoT através do portal do Azure"
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

# <a name="create-an-iot-hub-using-the-azure-portal"></a>Criar um concentrador de IoT através do portal Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]


## <a name="introduction"></a>Introdução

Este artigo descreve como localizar o serviço de IoT concentrador no portal do Azure e como criar e gerir IoT concentradores.

## <a name="where-to-find-iot-hubs"></a>Onde localizar IoT concentradores

Existem vários locais onde pode encontrar IoT concentradores.

1. **+ Novo**: **Azure IoT concentrador** é um serviço de IoT e pode ser encontrado na categoria **Internet das coisas**, em **+ Novo**, semelhante a outros serviços.

2. Também podem ser acedidos IoT concentradores através de mercado como o serviço de herói **Da Internet de coisas**.

## <a name="create-an-iot-hub"></a>Criar um concentrador de IoT

Pode criar um concentrador de IoT utilizando os seguintes métodos:

- Criar um concentrador de IoT através da opção **+ Novo** leva a pá apresentado na captura de ecrã seguinte. Os passos para criar o concentrador IoT através deste método e através de mercado são idênticos.

- Criar um concentrador de IoT através de mercado: clicando em **Criar** é aberta uma pá que é idêntico à pá anterior para a experiência **+ Novo** . As secções seguintes listam os vários passos envolvidos na criação de um concentrador de IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Selecione o nome do centro do IoT

Para criar um concentrador de IoT, é necessário nomear o concentrador. Este nome tem de ser exclusivo entre os controladores. Sem duplicação de concentradores é permitida no back-end, pelo que é recomendado que este concentrador chama-se exclusivamente possível.

### <a name="choose-the-pricing-tier"></a>Selecione a camada comparar

Pode escolher entre quatro camadas: **livre**, **padrão 1** e **2 padrão**e **S3 padrão**. A camada gratuita permite apenas 500 dispositivos de estar ligado ao centro do IoT e a até 8000 mensagens por dia.

**S1 padrão**: IoT concentradores S1 edition foi concebido para IoT soluções que tenham um grande número de dispositivos gerar relativamente pequenas quantidades de dados por dispositivo. Cada unidade da edição de S1 permite até 400.000 mensagens por dia em todos os dispositivos ligados.

**S2 padrão**: IoT concentrador S2 edition foi concebido para soluções IoT na qual dispositivos geram grandes quantidades de dados. Cada unidade da edição de S2 permite até 6 milhões de mensagens por dia entre todos os dispositivos ligados.

**S3 padrão**: IoT concentrador S3 edition foi concebido para IoT soluções que geram grandes quantidades de dados. Cada unidade da edição de S3 permite até 300 milhões de mensagens por dia entre todos os dispositivos ligados.

![][4]

> [AZURE.NOTE] Concentrador IoT permite apenas um concentrador gratuito por subscrição Azure.

### <a name="iot-hub-units"></a>Unidades de concentrador IoT

Uma unidade de concentrador IoT inclui um determinado número de mensagens por dia. O número total de mensagens suportadas para este concentrador é o número de unidades multiplicada pelo número de mensagens por dia para essa camada. Por exemplo, se pretender que o concentrador IoT para suportar penetração de 700,000 mensagens, selecione as duas unidades de camada S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo a partições de nuvem e grupo de recursos

Pode alterar o número de partições para um concentrador de IoT. Partições predefinidas estão definidas para 4; No entanto, pode escolher um número diferente de partições a partir de uma lista pendente.

Grupos de recursos, não terá explicitamente criar um grupo de recursos vazia. Ao criar um recurso, pode optar por criar um novo grupo de recursos ou utilizar um grupo de recursos existente.

![][5]

### <a name="choose-subscriptions"></a>Selecione subscrições

Azure IoT concentrador automaticamente mostra a lista de subscrições Azure aos quais está ligada a conta de utilizador. Pode escolher uma das opções aqui para associar o concentrador IoT esse subscrição Azure.

### <a name="choose-the-location"></a>Escolha a localização

A opção de localização fornece uma lista das regiões na qual é oferecido IoT concentrador. Concentrador IoT está disponível para implementar nas seguintes localizações: Austrália leste, Austrália Sudeste, asiático, Sudeste asiático, Europa Norte, Europa Oeste, Japão Oriente, oeste, Japão-nos Oriente, oeste-nos.

### <a name="create-the-iot-hub"></a>Criar o concentrador IoT

Quando todos os passos anteriores estiverem concluídos, o concentrador IoT está pronto para ser criado. Clique em **Criar** para iniciar o processo de back-end da criação deste concentrador IoT com as opções específicas e implementá-lo para a localização especificada.

Pode demorar alguns minutos para que o concentrador IoT seja criado como demora tempo para a implementação de back-end a ocorrer nos servidores localização apropriada.

## <a name="change-the-settings-of-the-iot-hub"></a>Alterar as definições do concentrador IoT

Pode alterar as definições de um concentrador de IoT existente após ter sido criada a partir do pá IoT concentrador.

![][8]

**Políticas de acesso partilhadas**: estas políticas definem as permissões para dispositivos e serviços para ligar a IoT concentrador. Pode aceder a estas políticas ao clicar em **Políticas de acesso partilhado** em **Geral**. Neste pá, pode modificar políticas existentes ou adicionar uma nova política.

### <a name="create-a-policy"></a>Criar uma política

- Clique em **Adicionar** para abrir uma pá. Aqui pode introduzir o novo nome da política e as permissões que pretende associar esta política, conforme apresentado na figura seguinte.

    Existem vários níveis de permissões que podem ser associados estas políticas partilhadas. As duas primeiras políticas, **registo ler** e **escrever registo**, conceder ler e direitos de acesso de escrita para o arquivo de identidade do dispositivo ou o registo de identidade. Escolher a opção de escrita automaticamente escolhe a opção de leitura.

    A política de **serviço de ligar** concede permissão para aceder aos pontos finais lado na nuvem como o grupo do consumidor para estabelecer ligação ao centro do IoT de serviços. A política de **dispositivo ligar-se** concede permissões para enviar e receber mensagens nos pontos finais lado do dispositivo do concentrador IoT.

- Clique em **Criar** para adicionar este recentemente criado política à lista existente.

![][10]

## <a name="messaging"></a>Mensagens

Clique em **serviço de mensagens** para apresentar uma lista de mensagens propriedades para o concentrador de IoT que está a ser modificada. Existem dois tipos principais de propriedades que pode modificar ou copiar: **na nuvem para o dispositivo** e o **dispositivo na nuvem**.

- Definições de **nuvem ao dispositivo** : esta definição tem dois subsettings: **na nuvem para dispositivo TTL** (time-para-live) e a **hora de retenção** para as mensagens. Quando o concentrador IoT é criado pela primeira vez, ambos os estas definições são criadas com um valor predefinido de uma hora. Para ajustar estes valores, utilize os controlos de deslize ou introduza os valores.

- Definições de **dispositivo nuvem** : esta definição tem várias subsettings, algumas das quais são com o nome/atribuídos quando o concentrador IoT é criado e pode ser copiado apenas para outros subsettings que são personalizáveis. Estas definições estão listadas na secção seguinte.

**Partições**: este valor é definido quando o concentrador IoT é criado e pode ser alterado através desta definição.

**Compatível com o evento concentrador nome e o ponto final**: quando IoT o concentrador é criado, um concentrador de evento é criado internamente que poderá ter acesso em determinadas circunstâncias. Este nome compatível com o evento concentrador e o ponto final não é possível mas está disponíveis para utilização através do botão **Copiar** .

**Tempo de retenção**: definido para um dia por predefinição, mas pode ser personalizado para outros valores utilizando a lista pendente. Este valor é em dias para o dispositivo na nuvem e não em horas, tal como a definição semelhante para a nuvem para o dispositivo.

**Grupos de consumidores**: consumidor grupos são uma definição de semelhante a outros sistemas de mensagens que podem ser utilizados para recuperar dados de formas específicas para ligar a outras aplicações ou serviços a IoT concentrador. Cada concentrador IoT é criado com um grupo do consumidor predefinido. No entanto, pode adicionar ou eliminar grupos do consumidor a sua concentradores IoT.

> [AZURE.NOTE] O grupo do consumidor predefinido não pode ser editado ou eliminado.

![][11]

## <a name="pricing-and-scale"></a>Preços e a escala

Os preços de um concentrador de IoT existente podem ser alterados através das definições de **preços** , com as seguintes exceções:

- Na implementação atual, um concentrador de IoT com um SKU gratuito não é possível alterar camadas para um das SKUs pagas, ou vice versa.
- Só pode haver um concentrador de IoT camada gratuito na subscrição do Azure.

![][12]

Mover a partir de uma camada superior (S2 ou S3) para o campo inferior camada (S1 ou S2) é permitida apenas quando o número de mensagens enviadas para esse dia não está em conflito. Por exemplo, se o número de mensagens por dia exceder 400.000, em seguida, a camada para o concentrador IoT pode ser alterada. No entanto, se mudar para a camada S1 concentrador está limitado para esse dia.

## <a name="delete-the-iot-hub"></a>Eliminar o concentrador IoT

Pode navegar para o concentrador de IoT que pretende eliminar ao clicar em **Procurar**e, em seguida, escolhendo o concentrador adequado para eliminar. Clique no botão **Eliminar** abaixo do nome do concentrador para eliminar o concentrador.

## <a name="next-steps"></a>Próximos passos

Siga estas ligações para saber mais sobre a gestão de Azure IoT concentrador:

- [Em volume gerir IoT dispositivos][lnk-bulk]
- [Métricas de utilização][lnk-metrics]
- [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]
- [Proteger a sua solução IoT desde o início para cima][lnk-securing]


  [4]: ./media/iot-hub-create-through-portal/create-iothub.png
  [5]: ./media/iot-hub-create-through-portal/location1.png
  [8]: ./media/iot-hub-create-through-portal/portal-settings.png
  [10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-create-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md