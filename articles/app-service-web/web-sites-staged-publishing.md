<properties
    pageTitle="Configurar o teste ambientes para web apps no serviço de aplicação do Azure"
    description="Saiba como utilizar a publicação faseada para web apps no Azure aplicação de serviço."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/09/2016"
    ms.author="cephalin"/>

# <a name="set-up-staging-environments-for-web-apps-in-azure-app-service"></a>Configurar o teste ambientes para web apps no serviço de aplicação do Azure
<a name="Overview"></a>

Quando implementar a aplicação web do [Serviço de aplicação](http://go.microsoft.com/fwlink/?LinkId=529714), pode implementar para uma ranhura em separado implementação em vez da ranhura de produção predefinido ao executar no modo de plano **padrão** ou a aplicação de serviço de **Premium** . Faixas de implementação são aplicações web ao vivo realmente com os seus próprios nomes de anfitriões. Elementos de conteúdo e as configurações da aplicação Web podem ser trocados entre duas faixas de implementação, incluindo a ranhura de produção. Implementar a aplicação para uma ranhura implementação tem os seguintes benefícios:

- Pode validar alterações da aplicação web de uma transição ranhura de implementação antes de trocá-lo com a ranhura de produção.

- Implementar uma aplicação web para uma ranhura em primeiro lugar e trocá-lo para produção assegura que todas as instâncias da ranhura são aquece antes de a ser convertido em produção. Isto elimina o tempo de inatividade quando implementar a aplicação web. O redirecionamento de tráfego está totalmente integrado e não existem pedidos desaparecem como resultado de operações de trocar. Este fluxo de trabalho todo pode ser automatizado através da configuração [Automática trocar](#configure-auto-swap-for-your-web-app) quando não é necessária uma pré-trocar validação.

- Depois de um trocar, ranhura com a aplicação web anteriormente faseada tem agora o anterior produção web app. Se as alterações trocadas na ranhura produção não como esperado, pode efetuar o mesmo trocar imediatamente para obter o seu "último site conhecido" novamente.

Cada modo de plano de serviço de aplicação suporta um número diferente de faixas de implementação. Para saber o número de faixas suporta de modo a aplicação web, consulte o artigo [Preços de serviço de aplicação](/pricing/details/app-service/).

- Quando a aplicação web do tem vários faixas, não é possível alterar o modo de.

- Dimensionamento não está disponível para não produção faixas.

- Gestão de recursos ligadas não é suportada para não produção faixas. No [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) apenas, pode evitar este potencial impacto numa ranhura de produção movendo temporariamente a ranhura que não sejam de produção para um modo de plano de serviço de aplicação diferente. Tenha em atenção que a ranhura não produção tem mais uma vez para partilhar o mesmo modo com a ranhura de produção antes de pode trocar duas fendas.


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

<a name="Add"></a>
## <a name="add-a-deployment-slot-to-a-web-app"></a>Adicionar uma ranhura de implementação para uma aplicação web ##

A aplicação web tem de executar no modo **padrão** ou **Premium** por ordem para activar várias faixas de implementação.

1. No [Portal do Azure](https://portal.azure.com/), abra pá da sua aplicação web.
2. Clique em **Definições**e, em seguida, clique em **faixas de implementação**. Em seguida, na pá **faixas de implementação** , clique em **Adicionar ranhura**.

    ![Adicionar uma nova ranhura de implementação][QGAddNewDeploymentSlot]

    > [AZURE.NOTE]
    > Se a aplicação web ainda não estiver no modo **padrão** ou **Premium** , receberá uma mensagem a indicar os modos de suportados para ativar a publicação faseada. Neste momento, tem a opção para selecionar a **Atualizar** e navegue para o separador **escala** da sua aplicação web antes de continuar.

2. Na pá **Adicionar uma ranhura** , dê um nome ao ranhura e selecionar se pretende clonar configuração da aplicação web a partir de outra ranhura implementação existente. Clique na marca de verificação para continuar.

    ![Origem de configuração][ConfigurationSource1]

    A primeira vez que adiciona uma ranhura, apenas terá duas opções: configuração clonar da ranhura predefinido na produção ou não de todo.

    Depois de ter criado várias faixas, vai conseguir clonar configuração a partir de uma ranhura que não seja aquele em produção:

    ![Origens de configuração][MultipleConfigurationSources]

5. Na pá **faixas de implementação** , clique em ranhura implementação para abrir uma pá para ranhura, com um conjunto de métricas e configuração, tal como faria com qualquer outra aplicação web. **your-Web-App-Name-Deployment-Slot-Name** aparecerá no topo da pá a lembrá-lo de que está a ver a ranhura implementação.

    ![Implementação ranhura título][StagingTitle]

5. Clique no URL de aplicação no pá a ranhura. Aviso da ranhura implementação tem as suas próprias hostname e também é uma aplicação direto. Para limitar o acesso do público para a ranhura de implementação, consulte o artigo [Aplicação de serviço Web App – bloquear o acesso de web ao faixas de implementação de que não sejam de produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Não existe conteúdo após a criação de ranhura implementação. Pode implementar a ranhura a partir de um ramo de repositório diferente ou um repositório de completamente diferente. Também pode alterar a configuração da ranhura. Utilize as credenciais de perfil ou implementação de publicar associadas a ranhura de implementação para atualizações de conteúdos.  Por exemplo, pode [publicar para esta ranhura com git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>
## <a name="configuration-for-deployment-slots"></a>Configuração de faixas de implementação ##
Quando clonar configuração a partir de outra ranhura de implementação, a configuração clonada é editável. Além disso, alguns elementos de configuração vai ter de seguir o conteúdo ao longo de um trocar (não ranhura específica) enquanto outros elementos de configuração irão permanecer na mesma ranhura após um trocar (ranhura específica). As listas seguintes mostram a configuração que mudam quando trocar o faixas.

**As definições que são trocadas**:

- Definições gerais - como sockets do quadro versão, 32/64 bits, Web
- Definições da aplicação (pode ser configurado para continuar a uma ranhura)
- Cadeias de ligação (pode ser configurado para continuar a uma ranhura)
- Mapeamentos de processador
- Definições de monitorização e diagnóstico
- WebJobs conteúdo

**Definições de que não são trocadas**:

- Pontos finais de publicação
- Nomes de domínio personalizado
- Os certificados SSL e enlaces
- Definições de escala
- Responsáveis pelo agendamento de WebJobs

Para configurar uma cadeia de ligação ou da definição de aplicação para continuar a uma ranhura (não trocada), aceder a pá **Definições da aplicação** para uma ranhura específica, em seguida, selecione a caixa **Definição do intervalo livre** para os elementos de configuração que deve continuar a ranhura. Tenha em atenção que um elemento de configuração marcar como ranhura específica tem o efeito de estabelecimento esse elemento como não mudança ao longo de todas as faixas de implementação associadas com a aplicação web.

![Definições de ranhura][SlotSettings]

<a name="Swap"></a>
## <a name="to-swap-deployment-slots"></a>Alternar faixas de implementação ##

>[AZURE.IMPORTANT] Antes de trocar uma aplicação web a partir de uma ranhura de implementação para produção, certifique-se de que todas as definições de específicas que não sejam ranhura estão configuradas exatamente como pretende tê-lo em destino trocar.

1. Para trocar faixas de implementação, clique no botão de **trocar** na barra de comandos da aplicação web ou na barra de comando de uma ranhura implementação. Certifique-se de que trocar origem e de destino de trocar estão definidos corretamente. Normalmente, o destino de trocar seria a ranhura de produção.  

    ![Trocar botão][SwapButtonBar]

3. Clique em **OK** para concluir a operação. Quando termina a operação, tem sido trocadas as faixas de implementação.

## <a name="configure-auto-swap-for-your-web-app"></a>Configurar trocar automática para a sua aplicação web ##

Trocar automática simplifica DevOps cenários onde pretende continuamente implementar a aplicação web com zero frio início e de zero tempo de inatividade para os clientes do fim da aplicação web. Quando uma ranhura implementação está configurada para trocar automática para produção, sempre que a atualização da código notificações push para essa ranhura, o serviço de aplicação serão automaticamente a trocar do web app para produção, depois de já tem aquece na ranhura.

>[AZURE.IMPORTANT] Quando ativa trocar automática para uma ranhura, certifique-se a configuração de ranhura exatamente a configuração destinada a ranhura de destino (normalmente a ranhura de produção).

Configurar o trocar automática para uma ranhura é fácil. Siga os passos abaixo:

1. Na pá **Faixas de implementação** , selecione uma ranhura não produção, clique em **Todas as definições** para pá essa ranhura.  

    ![][Autoswap1]

2. Clique em **definições da aplicação**. Selecione **** para **Trocar automática**, selecione a ranhura destino pretendido na **Automática trocar ranhura**e clique em **Guardar** na barra de comandos. Certifique-se de configuração da ranhura está exatamente na configuração destinada a ranhura de destino.

    No separador **notificações** serão flash verde **SUCESSO** , uma vez concluída a operação.

    ![][Autoswap2]

    >[AZURE.NOTE] Para testar trocar automática para a sua aplicação web, pode selecionar primeiro uma ranhura de destino não produção na **Automática trocar ranhura** para se familiarizar com a funcionalidade.  

3. Execute uma push de código para essa ranhura implementação. Trocar automática que irá acontecer após um breve momento e a atualização será refletida no URL do seu ranhura de destino.

<a name="Multi-Phase"></a>
## <a name="use-multi-phase-swap-for-your-web-app"></a>Utilizar várias fase trocar para a sua aplicação web ##

Fase com várias trocar está disponível para simplificar a validação no contexto de elementos de configuração concebido para continuar a uma ranhura como cadeias de ligação. Nestes casos, poderá ser útil aplicar esses elementos de configuração do destino trocar à origem de trocar e validar antes de trocar realmente entra em vigor. Uma vez trocar elementos são aplicados à origem de trocar as ações disponíveis são concluir a trocar ou reverter para a configuração original para a origem de trocar que também tem o efeito de cancelar a trocar de configuração de destino. Amostras para os cmdlets do Azure PowerShell disponíveis para a fase com várias trocar estão incluídas nos cmdlets do Azure PowerShell para a secção de faixas de implementação.

<a name="Rollback"></a>
## <a name="to-rollback-a-production-app-after-swap"></a>Anular uma aplicação de produção após trocar ##

Se algum erro é identificado no rendimento após um trocar ranhura, recuperar as faixas aos seus membros de pré-lançamento trocar por trocar mesmo duas fendas imediatamente.

<a name="Warm-up"></a>
## <a name="custom-warm-up-before-swap"></a>Aquecimento personalizado antes de trocar ##

Algumas aplicações precisem de ações de aquecimento personalizado. O elemento de configuração de applicationInitialization na Web. config permite-lhe especificar acções de inicialização personalizada para ser executado antes de um pedido de for recebido. A operação de trocar vai aguardar este aquecimento personalizado concluir. Eis um fragmento da Web. config de exemplo.

    <applicationInitialization>
        <add initializationPage="/" hostName="[web app hostname]" />
        <add initializationPage="/Home/About" hostname="[web app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>
## <a name="to-delete-a-deployment-slot"></a>Para eliminar uma ranhura de implementação##

No pá para uma ranhura de implementação, clique em **Eliminar** na barra de comandos.  

![Eliminar uma ranhura de implementação][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##Azure os cmdlets do PowerShell de faixas de implementação

Azure PowerShell é um módulo que fornece cmdlets para gerir Azure através do Windows PowerShell, incluindo o suporte para gestão de faixas de implementação de aplicação web na aplicação de serviço de Azure.

- Para obter informações sobre como instalar e configurar o PowerShell do Azure e, no autenticar Azure PowerShell com a sua subscrição Azure, consulte o artigo [como instalar e configurar o Microsoft Azure PowerShell](../powershell-install-configure.md).  

----------

### <a name="create-web-app"></a>Criar a aplicação web

```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [web app name] -Location [location] -AppServicePlan [app service plan name]
```

----------

### <a name="create-a-deployment-slot-for-a-web-app"></a>Criar uma ranhura de implementação para uma aplicação web

```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [web app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

----------

### <a name="initiate-multi-phase-swap-and-apply-target-slot-configuration-to-source-slot"></a>Iniciar uma fase com várias trocar e aplicar configuração ranhura de destino para ranhura de origem

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="revert-the-first-phase-of-multi-phase-swap-and-restore-source-slot-configuration"></a>Reverter a primeira fase da fase com várias trocar e restaurar a configuração da origem de ranhura

```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

----------

### <a name="swap-deployment-slots"></a>Trocar faixas de implementação

```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [web app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

----------

### <a name="delete-deployment-slot"></a>Eliminar ranhura de implementação

```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [web app name]/[slot name] -ApiVersion 2015-07-01
```

----------

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>
##Comandos de Interface de comandos (Azure CLI) Azure de faixas de implementação

O clip do Azure fornece em diferentes plataformas comandos para trabalhar com o Azure, incluindo o suporte para gestão de faixas de implementação do Web App.

- Para obter instruções sobre como instalar e configurar o clip do Azure, incluindo informações sobre como ligar o clip do Azure à sua subscrição do Azure, consulte o artigo [instalar e configurar o clip do Azure](../xplat-cli-install.md).

-  Para listar os comandos disponíveis para o serviço de aplicação do Azure no clip do Azure, contacte o `azure site -h`.

----------
### <a name="azure-site-list"></a>lista de sites Azure
Para obter informações sobre as aplicações web na subscrição atual, **lista de sites azure**, tal como no exemplo seguinte de chamadas.

`azure site list webappslotstest`

----------
### <a name="azure-site-create"></a>Criar Azure site
Para criar uma ranhura de implementação, **Criar azure site** de chamadas e especifique o nome da aplicação web do existente e o nome da ranhura para criar, tal como no exemplo seguinte.

`azure site create webappslotstest --slot staging`

Para ativar o controlo de origem para a nova ranhura, utilize a opção de **– git** , tal como no exemplo seguinte.

`azure site create --git webappslotstest --slot staging`

----------
### <a name="azure-site-swap"></a>trocar Azure site
Para tornar a implementação atualizada ranhura a aplicação de produção, utilize o comando **azure site trocar** para executar uma operação de trocar, tal como no exemplo seguinte. A aplicação de produção não irá deparar qualquer uma vez, nem irá-sofrer um início frio.

`azure site swap webappslotstest`

----------
### <a name="azure-site-delete"></a>Eliminar Azure site
Para eliminar uma ranhura de implementação que já não é necessária, utilize o comando **Eliminar azure site** , tal como no exemplo seguinte.

`azure site delete webappslotstest --slot staging`

----------

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="next-steps"></a>Próximos passos ##
[Azure aplicação de serviço Web App – bloquear o acesso de web ao faixas de implementação de que não sejam de produção](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Versão de avaliação gratuita do Microsoft Azure](/pricing/free-trial/)

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png
 
