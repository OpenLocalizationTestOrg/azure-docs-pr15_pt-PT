<properties
    pageTitle="Procedimentos de actualização Mobile Cativação Web SDK Azure | Microsoft Azure"
    description="As atualizações mais recentes e os procedimentos para o Web SDK para Azure Mobile Cativação"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procedimentos de actualização Mobile Cativação Web SDK Azure

Se já tiver integrado numa versão anterior do Azure Mobile Cativação Web SDK para a aplicação web, terá da ter em conta os seguintes pontos quando atualizar o SDK.

Se ignorou várias versões do Mobile Cativação Web SDK, poderá ter de concluir várias procedimentos durante o processo de atualização. Por exemplo, se migrar a partir do 1.4.0 para 1.6.0, pela primeira vez siga os procedimentos para atualizar a partir do 1.4.0 para 1.5.0. Em seguida, siga os procedimentos para atualizar a partir do 1.5.0 para 1.6.0.

Independentemente versão atualizar a partir de, substituir qualquer versão anterior do azure-engagement.js ficheiro com a versão mais recente do ficheiro.

## <a name="upgrade-from-121-to-200"></a>Atualizar a partir do 1.2.1 para 2.0.0

Esta secção descreve como migrar a integração do Mobile Cativação Web SDK a partir do serviço de Capptain, oferecido pelo Capptain SA, a uma aplicação do Azure Mobile Cativação. Se estiver a migrar de uma versão anterior, consulte o Web site Capptain para migrar pela primeira vez para 1.2.1 e, em seguida, aplique os seguintes procedimentos.

Esta versão do SDK Mobile Cativação Web não suporta TV inteligente Samsung, Opera TV, webOS ou a funcionalidade atingir.

>[AZURE.IMPORTANT] Capptain e Azure Mobile Cativação não são o mesmo serviço. O procedimento seguinte realça apenas como migrar a aplicação de cliente. Migrar o Mobile Cativação Web SDK na aplicação não será migrado os seus dados a partir de um servidor de Capptain para um servidor de Cativação Mobile.

### <a name="javascript-files"></a>Ficheiros de JavaScript

Substitua o valor de erro ficheiro capptain-sdk.js azure-engagement.js o ficheiro e, em seguida, atualize o seu importações de script em conformidade.

### <a name="remove-capptain-reach"></a>Remover Capptain alcance

Esta versão do SDK Mobile Cativação Web não suporta a funcionalidade atingir. Se integrados Capptain atingir a sua aplicação, é necessário para removê-la.

Remover a importação de CSS chegar a partir da página e elimine o ficheiro. css relacionados (capptain reach.css, por predefinição).

Eliminar os seguintes recursos de atingir: a imagem Fechar (capptain close.png, por predefinição) e o ícone de marca corporativa (capptain--ícone de notificação, por predefinição).

Remova a IU atinja para notificações de na aplicação. O esquema predefinido tem o seguinte aspeto:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Remova a IU atinja para texto e anúncios de web e inquéritos. O esquema predefinido tem o seguinte aspeto:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Remover o `reach` objeto a partir da sua configuração, se existir. Será apresentada da seguinte forma:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Remova quaisquer outros atingir a personalização, tal como categorias.

### <a name="remove-deprecated-apis"></a>Remover APIs preteridos

Algumas APIs da Capptain são preteridos no Mobile Cativação Web SDK.

Remover todas as chamadas para as seguintes APIs: `agent.connect`, `agent.disconnect`, `agent.pause`, e `agent.sendMessageToDevice`.

Remover as ocorrências das chamadas de retorno seguintes da sua configuração Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, e `onPushMessageReceived`.

### <a name="configuration"></a>Configuração

Mobile Cativação utiliza uma cadeia de ligação para configurar identificadores SDK, por exemplo, o identificador de aplicação.

Substitua o ID da aplicação com a cadeia de ligação. Tenha em atenção que o objeto global para a configuração de SDK alterações da `capptain` para `azureEngagement`.

Antes da migração:

    window.capptain = {
      appId: ...,
      [...]
    };

Após a migração:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

A cadeia de ligação para a aplicação é apresentada no Portal do Azure.

### <a name="javascript-apis"></a>API JavaScript

O objeto JavaScript global `window.capptain` tiver sido mudado `window.azureEngagement` mas pode utilizar o `window.engagement` alias para chamadas de API. Não é possível utilizar o alias para definir a configuração de SDK.

Por exemplo, `capptain.deviceId` torna-se `engagement.deviceId`, `capptain.agent.startActivity` torna-se `engagement.agent.startActivity`, e assim sucessivamente.
