<properties 
    pageTitle="Criar aplicações de lógica no Visual Studio | Microsoft Azure" 
    description="Crie um projeto do Visual Studio para criar e implementar a aplicação de lógica." 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/> 
    
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Criar e implementar lógica aplicações no Visual Studio

Apesar do [Portal do Azure](https://portal.azure.com/) fornece-lhe uma excelente forma de estruturar e gerir as suas aplicações de lógica, também poderá querer estruturar e implementar em vez disso, a aplicação do Visual Studio lógica.  Lógica aplicações vem com um conjunto de ferramentas rich Visual Studio que lhe permite criar uma aplicação de lógica utilizando o designer, configurar modelos de implementação e automatização e implementar em qualquer ambiente.  

## <a name="installation-steps"></a>Passos de instalação

Seguem-se os passos para instalar e configurar as ferramentas do Visual Studio para as aplicações de lógica.

### <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [SDK Azure mais recente](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
- [Azure PowerShell] (https://github.com/Azure/azure-powershell#installation)
- Acesso à web ao utilizar o estruturador incorporado

### <a name="install-visual-studio-tools-for-logic-apps"></a>Instalar ferramentas do Visual Studio para as aplicações de lógica

Assim que tiver os pré-requisitos instalados, 

1. Abra o Visual Studio 2015 ao menu **Ferramentas** e selecione **Extensions e atualizações**
1. Selecione a categoria de **Online** para procurar online
1. Procurar **Lógica aplicações** apresentar as **Ferramentas de aplicações do Azure lógica para Visual Studio**
1. Clique no botão **Transferir** para transferir e instalar a extensão
1. Reinicie o Visual Studio após a instalação

> [AZURE.NOTE] Também pode transferir a extensão diretamente a partir desta [ligação](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)

Depois de instalado que poderá utilizar o projeto de grupo de recursos do Azure com o Designer de aplicação lógica.

## <a name="create-a-project"></a>Criar um projeto

1. Aceda ao menu **ficheiro** e selecione **Novo** >  **projeto** (ou, pode vá a **Adicionar** e, em seguida, selecione **novo projeto** de adicioná-la a uma solução existente):  ![menu ficheiro](./media/app-service-logic-deploy-from-vs/filemenu.png)

1. Na caixa de diálogo, localize a **nuvem**e, em seguida, selecione o **Grupo de recursos do Azure**. Escreva um **nome** e, em seguida, clique em **OK**.
    ![Adicionar novo projeto](./media/app-service-logic-deploy-from-vs/addnewproject.png)

1. Selecione o modelo de **aplicação de lógica** . Isto irá criar um modelo de implementação de aplicação de lógica em branco para começar.
    ![Selecione o modelo do Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

1. Assim que tiver seleccionado o seu **modelo**, clique em **OK**.

    Agora o projeto de aplicação lógica é adicionado à sua solução. Deverá visualizar o ficheiro de implementação no Explorador de solução:  

    ![Implementação](./media/app-service-logic-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>Utilizar o estruturador de aplicação de lógica

Assim que tiver um projeto de grupo de recursos do Azure que contém uma aplicação de lógica, pode abrir o estruturador do Visual Studio para ajudá-lo a criar o fluxo de trabalho.  O estruturador requer uma ligação à internet para os conectores para propriedades disponíveis e dados da consulta (por exemplo, se utilizar o conector do Dynamics CRM Online, o estruturador irá consultar a instância CRM para listar personalizada disponível e propriedades predefinidas).

1. Botão direito do rato sobre o `<template>.json` de ficheiros e selecione **Abrir com o Designer de aplicação de lógica** (ou `Ctrl+L`)
1. Selecione a subscrição, grupo de recursos e a localização para o modelo de implementação
    - É importante ter em atenção que a estruturar uma aplicação de lógica irá criar recursos de **Ligação de API** para consultar propriedades durante a estruturação.  O grupo de recursos selecionado será o grupo de recursos utilizado para criar as ligações durante o tempo de estrutura.  Pode ver ou modificar quaisquer ligações de API ao aceder ao Portal do Azure e para **API ligações**de navegação.
    ![Seleccionador de subscrição](./media/app-service-logic-deploy-from-vs/designer_picker.png)
1. O estruturador deve compor com base na definição na `<template>.json` ficheiro.
1. Agora pode criar e a aplicação de lógica de estrutura e as alterações serão atualizadas no modelo de implementação.
    ![Designer no Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

Também verá `Microsoft.Web/connections` recursos ser adicionados ao seu ficheiro de recurso para todas as ligações necessário para a aplicação de lógica a função.  Estas propriedades de ligação podem ser definidas quando implementar e geridas depois de implementar em **API ligações** no Portal do Azure.

### <a name="switching-to-the-json-code-view"></a>Mudar para a vista de código JSON

Pode selecionar no separador **Vista código** na parte inferior do estruturador de fluxos para mudar para a representação de JSON da aplicação lógica.  Para mudar para o recurso completo JSON, faça duplo clique a `<template>.json` de ficheiros e selecione **Abrir**.

### <a name="saving-the-logic-app"></a>Guardar a aplicação de lógica

Pode guardar a aplicação de lógica em qualquer altura através do botão **Guardar** ou `Ctrl+S`.  Se existirem algum erro com a sua aplicação de lógica no momento em que guarda, serão apresentados em janela de **resultados** do Visual Studio.

## <a name="deploying-your-logic-app"></a>Implementar a sua aplicação de lógica

Por fim, depois de ter configurado a sua aplicação, pode implementar diretamente a partir do Visual Studio em apenas alguns passos. 

1. Com o botão direito no projeto no Explorador de solução e aceda a **Implementar** > **Nova implementação...** 
     ![Nova implementação](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Lhe for pedido para iniciar sessão para as suas subscrições Azure. 

3. Agora tem de escolher os detalhes do grupo de recursos que pretende implementar a aplicação lógica. 
    ![Implementar a grupo de recursos](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    Certifique-se de que selecione os ficheiros de modelo e parâmetros à direita do grupo de recursos (por exemplo se estiver a implementar num ambiente de produção que pretende escolher o ficheiro de parâmetros de produção). 
4. Selecione o botão de implementar
 
    
6. O estado da implementação aparece na janela de **saída** (poderá ter de escolher **Azure aprovisionamento**. 
    ![Saída](./media/app-service-logic-deploy-from-vs/output.png)

No futuro, pode rever a sua aplicação de lógica no controlo de origem e utilizar o Visual Studio para implementar novas versões. 

> [AZURE.NOTE] Se modificar a definição no Portal do Azure diretamente, em seguida, da próxima vez que implementa a partir do Visual Studio essas alterações será substituída.

## <a name="next-steps"></a>Próximos passos

- Para começar a lógica de aplicações, siga o tutorial de [criar uma aplicação de lógica](app-service-logic-create-a-logic-app.md) .  
- [Ver exemplos e cenários comuns](app-service-logic-examples-and-scenarios.md)
- [Pode automatizar processos de negócio com aplicações de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Saiba como integrar o seu sistemas com aplicações de lógica](http://channel9.msdn.com/Events/Build/2016/P462)