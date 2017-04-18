<properties
    pageTitle="Referência para navegar portal do Azure"
    description="Obter informações sobre as experiências de utilizador diferentes para a aplicação de serviço Web entre o portal de gestão e o Portal do Azure"
    services="app-service"
    documentationCenter=""
    authors="jaime-espinosa"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/26/2016"
    ms.author="jaime-espinosa"/>

# <a name="reference-for-navigating-the-azure-portal"></a>Referência para navegar portal do Azure

Sites públicos do Azure agora chamam a [Aplicação de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Atualizamos todas nossa documentação para refletir esta alteração de nome e para fornecem instruções para o Portal do Azure. Até processo estiver concluído, pode utilizar este documento como um guia para trabalhar com Web Apps no portal do Azure.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 
 
## <a name="the-future-of-the-azure-classic-portal"></a>O futuro da Portal clássica do Azure

Enquanto irá reparar as alterações de imagem corporativa no Portal clássica do Azure, esse portal está a ser substituída por Portal do Azure. Como o portal clássico está a ser gradualmente, o foco para o desenvolvimento de novo é deslocar ao Portal do Azure. Todas as futuras novas funcionalidades para Web Apps seja reencaminhado no Portal do Azure. Começar a utilizar o Portal do Azure para tirar partido das mais recentes e maior que Web Apps tem que oferecer.

## <a name="layout-differences-between-the-azure-classic-portal-and-azure-portal"></a>Esquema as diferenças entre o Azure clássica Portal e Portal do Azure

No portal do clássico, todos os serviços do Azure são apresentados do lado esquerdo. Navegação no portal do clássico segue uma estrutura de árvore, onde iniciar a partir do serviço e navegue para cada elemento. Esta estrutura funciona bem quando gerir componentes independentes. No entanto, as aplicações criadas no Azure são uma coleção de serviços interligados e estrutura da árvore deste não é ideal para trabalhar com conjuntos de serviços. 

Portal do Azure torna mais fácil criar aplicações fim-a-ponto com componentes de vários serviços. O portal está organizado como *deslocações*. Uma *viagem* é uma série de *pás*, que são contentores para os diferentes componentes. Por exemplo, a configurar a escala automática para uma aplicação web é um *percurso* que o irá guiar pás várias conforme mostrado no seguinte exemplo: pá o **web site** (que título pá não ainda foi atualizado para utilizar a nova terminologia), o pá **Definições** e pá a **escala de saída** . No exemplo, escala automática está a ser configurado para dependem de utilização da CPU, pelo que não existe uma **Percentagem de CPU** pá. Os componentes dentro de *pás* chamam *peças*, que aspeto mosaicos. 

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## <a name="navigation-example-create-a-web-app"></a>Exemplo de navegação: criar uma aplicação web

Criar novas aplicações web é ainda é tão fácil como 1-2-3. A imagem seguinte mostra o portal clássico e o portal lado a lado para demonstrar muito não foi alterado do número dos passos necessários para obter uma aplicação web para cima e a executar. 

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

No portal do pode escolher a partir dos tipos de aplicações web, incluindo as aplicações de galeria populares como WordPress mais comuns. Para obter uma lista completa de aplicações disponíveis, visite o [Azure Marketplace].

Quando cria uma aplicação web, especificar o URL, plano de serviço de aplicação e a localização no portal do tal como faz no portal do clássico. 

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

Além disso, o portal permite-lhe definir outras definições comuns. Por exemplo, [grupos de recursos](../azure-resource-manager/resource-group-overview.md) facilitam a ver e gerir recursos Azure relacionados. 

## <a name="navigation-example-settings-and-features"></a>Exemplo de navegação: as funcionalidades e definições

Todas as definições e funcionalidades estão agora logicamente agrupadas numa única pá, a partir do qual pode navegar.

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

Por exemplo, pode criar domínios personalizados ao clicar em **domínios personalizados e SSL** no pá **Definições** .

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

Para configurar um alerta de monitorização, clique em **pedidos e erros** e, em seguida, **Adicionar alerta**.

![](./media/app-service-web-app-azure-portal/Monitoring.png)

Para ativar diagnósticos, clique em **registos de diagnóstico** no pá **Definições** .

![](./media/app-service-web-app-azure-portal/Diagnostics.png)
 
Para configurar definições da aplicação, clique em **definições da aplicação** no pá **Definições** . 

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

Que não seja o nome da marca, algumas coisas no portal do foram cujo nome foi mudadas ou agrupadas de forma diferente para facilitar a localizá-los. Por exemplo, segue-se uma captura de ecrã da página correspondente para a aplicação definições (**Configurar**) no portal do clássico.

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## <a name="more-resources"></a>Mais recursos

[Azure Portal]: https://portal.azure.com
[Azure Marketplace]: /marketplace/

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
 
