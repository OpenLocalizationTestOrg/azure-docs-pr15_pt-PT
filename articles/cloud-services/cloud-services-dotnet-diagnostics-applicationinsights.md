<properties
   pageTitle="Resolver problemas de serviços em nuvem com informações de aplicação | Microsoft Azure"
   description="Saiba como resolver problemas de serviço de nuvem ao utilizar a aplicação informações aos dados do processo de diagnósticos do Azure."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# <a name="troubleshoot-cloud-services-using-application-insights"></a>Resolver problemas de serviços em nuvem com informações de aplicação

Com as extensões de diagnósticos do [Azure SDK 2,8](https://azure.microsoft.com/downloads/) e Azure 1,5 pode agora enviar os seus dados Azure diagnósticos do seu serviço de nuvem diretamente para informações de aplicação. Os vários tipos de registos recolhidos pelo diagnósticos do Azure incluindo registos da aplicação, registos de eventos do windows, ETW inicia e contadores de desempenho agora podem ser enviados para informações de aplicação e visualizar os no portal do aplicação informações IU. Quando utilizado juntamente com o SDK de informações da aplicação agora pode obter informações para registos proveniente da sua aplicação bem como os dados de nível sistema e infraestrutura provenientes do Azure diagnóstico e métricas.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurar o Azure diagnóstico para enviar dados para informações de aplicação

Siga estes passos para configurar o seu projeto do serviço de nuvem para enviar dados Azure diagnósticos para informações de aplicação.

1) No Visual Studio solução Explorer com o botão direito numa função e selecione **Propriedades** para abrir o estruturador de função

![Propriedades da função Explorer solução][1]

2) No designer de funções na secção Diagnóstico, selecione a caixa de verificação para **Enviar dados diagnóstico para informações de aplicação**

![Estruturador de fluxos de função enviar dados diagnóstico para informações de aplicação][2]

3) Na caixa de diálogo que aparece, selecione o recurso de informações de aplicação que pretende enviar os dados do Azure diagnóstico para. A caixa de diálogo permite-lhe selecionar um recurso de informações de aplicação existente da sua subscrição ou especificar manualmente uma tecla instrumentação para um recurso de informações da aplicação. Se não tiver um recurso de informações de aplicação existente, em seguida, pode criar ao clicar na ligação **criar um novo recurso** que irão abrir uma janela do browser portal clássica do Azure onde pode criar um recurso de informações da aplicação. Para obter mais informações sobre a criação de um recurso de aplicação informações consulte o artigo [criar um novo recurso de informações de aplicação](../application-insights/app-insights-create-new-resource.md)

![Selecione o recurso de informações da aplicação][3]

4) Assim que tiver adicionado o recurso de informações da aplicação, a tecla instrumentação para esse recurso é armazenada como uma definição de configuração do serviço com o nome **APPINSIGHTS_INSTRUMENTATIONKEY**. Pode alterar esta definição de configuração para cada configuração do serviço ou ambiente ao selecionar uma configuração diferente da lista pendente de configuração de serviço para baixo e especificar uma nova chave de instrumentação para que a configuração.

![Selecione a configuração do serviço][4]

A definição de configuração de **APPINSIGHTS_INSTRUMENTATIONKEY** é utilizada pelo Visual Studio para configurar a extensão de diagnóstico com a informação de recurso de informações da aplicação apropriada durante a publicação. A definição de configuração é uma forma conveniente de definir as teclas de instrumentação diferentes para configurações de serviços diferente. Visual Studio irá traduzir essa definição e inseri-lo para a configuração do público extensão diagnósticos quando de publicação. Para simplificar o processo de configurar a extensão de diagnóstico com o PowerShell, o pacote de saída do Visual Studio também contém a configuração pública XML com a adequado instrumentação de aplicação informações chave incluído. Os ficheiros de config público são criados na pasta extensões e siga o padrão de PaaSDiagnostics. <RoleName>. PubConfig.xml. Qualquer implementações do PowerShell com base podem utilizar este padrão para mapear cada configuração para uma função.

5) Ativar a **Enviar dados diagnóstico para informações de aplicação** irá configurar automaticamente o Azure diagnóstico para enviar todas as contadores de desempenho e os registos de nível de erros que estão a ser recolhidos pelo agente do Azure diagnóstico para informações de aplicação. Se pretender continuar a configurar dados que é enviada para informações de aplicação, em seguida, precisa de editar manualmente o ficheiro *diagnostics.wadcfgx* para cada função. Consulte o artigo [Configurar o Azure diagnóstico para enviar dados para informações de aplicação](../azure-diagnostics-configure-applicationinsights.md) para saber mais sobre atualizar manualmente a configuração.

Certificar-se de que a extensão de diagnóstico Azure está ativada assim que o serviço de nuvem está configurado para enviar dados Azure diagnósticos para informações de aplicação que pode implementá-lo para Azure, tal como faria normalmente. Consulte [publicar um serviço na nuvem utilizando o Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Visualizar dados de diagnóstico Azure em informações de aplicação
A Azure telemetria diagnóstico irá aparecer no recurso de informações da aplicação configurado para o seu serviço de nuvem.

Segue-se como o vários Azure de registo de diagnóstico tipos mapa os conceitos de informações da aplicação:  

-  Contadores de desempenho são apresentados como métricas personalizada na aplicação de informações
-  Registos de eventos do Windows são apresentados como traços e eventos de personalizada no informações de aplicação
-  Registos de aplicação, ETW registos e quaisquer registos de diagnóstico infraestrutura são apresentados como rastreios na aplicação de informações.

Para ver dados de diagnósticos Azure na aplicação de informações:

- Utilize o [Explorador de métricas](../application-insights/app-insights-metrics-explorer.md) para visualizar qualquer contadores de desempenho personalizado ou contagens de diferentes tipos de eventos de registo de eventos do windows.

![Métricas personalizadas no Explorador de métricas][5]

- Utilize a [pesquisa](../application-insights/app-insights-diagnostic-search.md) para pesquisar os vários registos de rastreio que foi enviados por diagnósticos do Azure. Por exemplo, se tiver uma exceção não processada numa função que a função para uma falha de sistema e reciclagem essas informações de causaram seria aparecer no canal na *aplicação* de *registo de eventos do Windows*. Pode utilizar a funcionalidade de pesquisa para analisar o erro de registo de eventos do Windows e obter o rastreio da pilha completo para a exceção, permitindo-lhe localizar a causa de raiz do problema.

![Rastreios de pesquisa][6]

## <a name="next-steps"></a>Próximos passos

- [Adicionar o SDK de informações de aplicação no seu serviço de nuvem](../application-insights/app-insights-cloudservices.md) para enviar dados sobre pedidos, exceções, dependências e qualquer telemetria personalizada a partir da sua aplicação. Combinado com diagnósticos Azure dados, pode obter uma vista completa das suas aplicações e sistema tudo no mesmo recurso conhecimentos aprofundados de aplicação.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
