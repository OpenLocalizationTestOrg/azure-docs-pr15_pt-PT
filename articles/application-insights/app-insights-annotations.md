<properties
    pageTitle="Liberte anotações para informações de aplicação | Microsoft Azure"
    description="Adicionar implementação ou Construa a marcadores para os seus gráficos do Explorador de métricas na aplicação de informações."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="awills"/>

# <a name="release-annotations-in-application-insights"></a>Liberte anotações a informações de aplicação

Liberte anotações no [Explorador de métricas de](app-insights-metrics-explorer.md) gráficos mostram onde implementado que uma nova compilação. Facilitam ver se as suas alterações tinham qualquer efeito no desempenho da sua aplicação. Podem ser criados automaticamente pelo [dos serviços de equipa do Visual Studio construir sistema](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs)e também pode [criá-los a partir do PowerShell](#create-annotations-from-powershell).

![Exemplo de anotações com correlação visível com tempo de resposta do servidor](./media/app-insights-annotations/00.png)

Lançamento anotações são uma funcionalidade da compilação baseada na nuvem e solte o serviço do Visual Studio Team Services. 

## <a name="install-the-annotations-extension-one-time"></a>Instale a extensão de anotações (uma vez)

Para poder criar lançamento anotações, terá de instalar um muitos equipa extensões de serviço disponíveis no Visual Studio mercado.

1. Inicie sessão no seu projeto do [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) .
2. No Visual Studio Marketplace, [obter a extensão do lançamento anotações](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)e adicioná-lo à sua conta de serviços de equipa.

![AT parte superior direita da página web de serviços de equipa, abrir Marketplace. Selecione Visual serviços de equipa e, em seguida, em criação e lançamento, selecione ver mais.](./media/app-insights-annotations/10.png)

Só tem de fazer uma vez para a sua conta do Visual Studio Team Services. Lançamento anotações agora podem ser configuradas para qualquer projeto na sua conta. 

## <a name="get-an-api-key-from-application-insights"></a>Obter uma chave de API de informações de aplicação

Tem de seguir estes passos para cada modelo de versão que pretende criar lançamento anotações.


1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com) e abra o recurso de informações da aplicação monitoriza a sua aplicação. (Ou [criar uma agora](app-insights-overview.md), se ainda não o feito ainda.)
2. Abra o **Acesso à API**e tomar uma cópia do **Id da aplicação de informações**.

    ![No portal.azure.com, abra o seu recurso de informações da aplicação e selecione definições. Abra o acesso à API. Copiar o ID da aplicação](./media/app-insights-annotations/20.png)

2. Numa janela do browser em separado, abra (ou crie) o modelo de lançamento que gere a sua implementações do Visual Studio Team Services. 

    Adicionar uma tarefa e, selecione a tarefa de anotação de lançamento de informações de aplicação a partir do menu.

    Cole o **Id da aplicação** que copiou do pá acesso à API.

    ![Serviços de equipa do Visual Studio, abra a versão, selecione uma definição de lançamento e escolha Editar. Clique em Adicionar tarefa e selecione anotação de lançamento de informações de aplicação. Colar o ID da aplicação informações.](./media/app-insights-annotations/30.png)

3. Defina o campo **APIKey** como uma variável `$(ApiKey)`.

4. Novamente na pá acesso à API, crie uma nova chave de API e tomar uma cópia do mesmo.

    ![Na pá de acesso à API na janela do Azure, clique em criar API chave. Fornecer um comentário, selecione anotações de escrita e clique em Gerar chave. Copie a nova chave.](./media/app-insights-annotations/40.png)

4. Abra o separador de configuração do modelo de lançamento.

    Criar uma definição de variável de `ApiKey`.

    Cole a chave de API para a definição de variável ApiKey.

    ![Na janela do Team Services, selecione o separador de configuração e clique em adicionar variáveis. Definir o nome para ApiKey e no valor, cole a chave que acabou de gerou.](./media/app-insights-annotations/50.png)


5. Por fim, **Guardar** a definição de lançamento.

## <a name="create-annotations-from-powershell"></a>Criar anotações a partir do PowerShell

Também pode criar anotações a partir de qualquer processo de que goste (sem utilizar vs. no sistema de equipa). 

Obter o [script Powershell do GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

Utilize-o da seguinte forma:

    .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }

Obter o `applicationId` e um `apiKey` a partir do seu recurso de informações da aplicação: abrir definições, acesso à API e copiar o ID da aplicação. Em seguida, clique em Criar chave API e copie a chave. 

## <a name="release-annotations"></a>Liberte anotações

Agora, sempre que utilizar o modelo de lançamento para implementar uma nova versão, será enviada uma anotação para informações de aplicação. As anotações irão aparecer em gráficos no Explorador de métricas.

Clique em qualquer marcador de anotação para obter mais detalhes sobre a versão, incluindo o autor do pedido, ramo de controlo da origem de abrir, liberte definição, ambiente e muito mais.


![Clique em qualquer marcador de anotação de lançamento.](./media/app-insights-annotations/60.png)
