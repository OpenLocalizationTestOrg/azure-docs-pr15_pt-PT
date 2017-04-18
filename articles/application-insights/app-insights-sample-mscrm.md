<properties 
    pageTitle="Tutorial: Monitorizar Microsoft Dynamics CRM com informações de aplicação" 
    description="Obtenha telemetria do Microsoft Dynamics CRM Online utilizando as informações de aplicação. Instruções passo a passo da configuração, a introdução de dados, visualização e exportar." 
    services="application-insights" 
    documentationCenter=""
    authors="mazharmicrosoft" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="awills"/>
 
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Tutorial: Ativar telemetria para utilizar informações de aplicação do Microsoft Dynamics CRM Online

Este artigo mostra-lhe como obter dados de telemetria do [Microsoft Dynamics CRM Online](https://www.dynamics.com/) utilizando o [Visual Studio aplicação informações](https://azure.microsoft.com/services/application-insights/). Explicaremos durante o processo de adição de script de informações de aplicação à aplicação, concluído capturar dados e a visualização de dados.

>[AZURE.NOTE] [Navegue até a solução da amostra](https://dynamicsandappinsights.codeplex.com/).

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Adicionar informações de aplicação a nova ou existente instância CRM Online 

Para monitorizar a sua aplicação, adicione uma SDK de informações de aplicação para a sua aplicação. O SDK envia telemetria no [portal de informações da aplicação](https://portal.azure.com), onde pode utilizar o nosso uma análise avançada e ferramentas de diagnóstico, ou exportar os dados para armazenamento.

### <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso de informações de aplicação no Azure

1. Obter [uma conta no Microsoft Azure](http://azure.com/pricing). 
2. Inicie sessão no [portal do Azure](https://portal.azure.com) e adicionar um novo recurso de informações da aplicação. Este é onde os seus dados serão processados e apresentados.

    ![Clique em +, serviços de programador, informações de aplicação.](./media/app-insights-sample-mscrm/01.png)

    Selecione ASP.NET como o tipo de aplicação.

3. Abra o separador Quick Start e abra o script de código.

    ![](./media/app-insights-sample-mscrm/03.png)

**Mantenha a página de código aberta** enquanto efetua o próximo passo outra janela do browser. Terá do código mais rapidamente. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Criar um recurso de web JavaScript no Microsoft Dynamics CRM

1. Abra o seu instância CRM Online e inicie sessão com privilégios de administrador.
2. Abrir o Microsoft Dynamics CRM definições, personalizações, personalizar o sistema

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Crie um recurso de JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Atribua um nome, selecione **Script (JScript)** e abra o editor de texto.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Copie o código da aplicação de informações. Ao copiar Certifique-se ignorar etiquetas de script. Referir-se por baixo de captura de ecrã:

    ![](./media/app-insights-sample-mscrm/09.png)

    O código inclui a chave de instrumentação que identifica o seu recurso de informações da aplicação.

5. Guardar e publicar.

    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Formulários de Instrument

1. No Microsoft CRM Online, abra o formulário de conta

    ![](./media/app-insights-sample-mscrm/11.png)

2. Abra o formulário de propriedades

    ![](./media/app-insights-sample-mscrm/12.png)

3. Adicionar o recurso de web JavaScript que criou

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Guardar e publicar as personalizações de formulário.


## <a name="metrics-captured"></a>Métricas capturadas

Configurou captura telemetria para o formulário. Sempre que é utilizado, os dados serão enviados para o seu recurso de informações da aplicação.

Eis as amostras dos dados que irá ver.

#### <a name="application-health"></a>Estado de funcionamento da aplicação

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Exceções de browser:

![](./media/app-insights-sample-mscrm/17.png)

Clique no gráfico para obter mais detalhes:

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Utilização

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Browsers

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolocalização

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Pedido de vista de página internos

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Código de exemplo

[Procure o código de exemplo](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI

Pode efetuar uma análise ainda mais aprofundada se [exportar os dados para o Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Solução da amostra Microsoft Dynamics CRM

[Aqui está a solução da amostra implementada no Microsoft Dynamics CRM] (https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Saiba mais

* [O que é a aplicação informações?](app-insights-overview.md)
* [Informações de aplicação para páginas web](app-insights-javascript.md)
* [Obter mais exemplos e tutoriais](app-insights-code-samples.md)

 
