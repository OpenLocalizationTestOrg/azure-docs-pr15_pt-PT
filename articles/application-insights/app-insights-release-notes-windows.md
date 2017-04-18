<properties 
    pageTitle="Notas de lançamento do aplicação informações para Windows" 
    description="As atualizações mais recentes para SDK da loja Windows." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2016" 
    ms.author="joshweb"/>
 
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Notas de lançamento do aplicação informações SDK para Windows Phone e armazenar

O SDK de informações da aplicação envia telemetria sobre a sua aplicação direto para de [Informações da aplicação](https://azure.microsoft.com/services/application-insights/), onde pode analisar a utilização e o desempenho.


## <a name="version-111"></a>Versão 1.1.1

### <a name="windows-sdk"></a>Windows SDK

- Corrigir um desligar o telefone durante Falha ao utilizar o Windows Phone Silverlight SDK. Após esta alteração, qualquer falha de sistema que acontece posteriormente ~ 2 segundos depois da chamada para WindowsAppInitialier.InitializeAsync(...) será mantida no disco e serão enviadas da próxima vez que a aplicação for iniciada. Se uma falha de sistema acontece antes ~ 2 segundos depois da chamada, será ignorada.  
- Defina dependências de NuGet para uma versão específica da Core e Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### <a name="core-sdk"></a>Principais SDK

- Principais são gerido no github. Notas de lançamento futuro do Core SDK podem ser encontradas [na github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>Versão 1.1

### <a name="core-sdk"></a>Principais SDK

- SDK apresenta agora novo tipo de telemetria ```DependencyTelemetry``` que contém informações sobre a chamada de dependência a partir da aplicação
- Novo método ```TelemetryClient.TrackDependency``` permite-lhe para enviar informações relativas a chamadas de dependência a partir da aplicação

## <a name="version-100"></a>Versão 1.0.0

### <a name="windows-app-sdk"></a>Windows SDK de aplicação

- Nova inicialização para as aplicações do Windows. Novo `WindowsAppInitializer` classe com `InitializeAsync()` método permite para iniciar a inicialização da coleção de SDK. Esta alteração permitir que um controlo mais preciso e melhorias de desempenho de inicialização aplicação significativa sobre técnica de ApplicationInsights.config anterior.
- DeveloperMode automaticamente já não está definida. Para alterar o comportamento de DeveloperMode tem de especificar no código.
- Já não o pacote de NuGet injete ApplicationInsights.config. Recomendamos para utilizar o novo WindowsAppInitializer quando adicionar manualmente NuGet pacote.
- Apenas lê ApplicationInsights.config `<InstrumentationKey>`, todas as outras definições são ignoradas num preferência para WindowsAppInitializer definições.
- Loja mercado serão automaticamente recolhido pelo SDK.
- Muitas correções de erros, melhorias de estabilidade e melhorias no desempenho.

### <a name="core-sdk"></a>Principais SDK

- Ficheiro de ApplicationInsights.config já não se encontra requiered. E não adicionado pelo pacote NuGet. Configuração pode ser totalmente especificada no código.
- Pacote NuGet já não irá adicionar um ficheiro de destinos a sua solução. Esta ação remove a definição automática do DeveloperMode durante uma compilação de depuração. DeveloperMode deve ser definida manualmente no código.

## <a name="version-017"></a>Versão 0.17

### <a name="windows-app-sdk"></a>Windows SDK de aplicação

- Windows SDK aplicação suporta agora Universal aplicações do Windows criados contra a pré-visualização técnica Windows 10 e com RC de 2015 VS.

### <a name="core-sdk"></a>Principais SDK

- Predefinições de TelemetryClient a inicialização com o InMemoryChannel.
- Nova API adicionado, `TelemetryClient.Flush()`. Este método esvaziar irá acionar um carregamento de bloqueio de imediato de telemetria todos os tem sessão iniciada que o cliente. Isto permite acionar manual de carregamento antes do encerramento do processo.
- O pacote de NuGet adicionado um alvo de .net 4,5. Este destino não tem externas dependências (removidas BCL e Origemdoevento dependências).

## <a name="version-016"></a>Versão 0,16 

pré-visualização de 2015-04-28

- SDK suporta agora a plataforma de destino DNX para ativar a monitorização de [.NET Core framework](http://www.dotnetfoundation.org/NETCore5) aplicações.
- Instâncias de ```TelemetryClient``` não colocar em cache instrumentação chave deixem. Agora se instrumentação chave não foi definida no ```TelemetryClient``` explicitamente ```InstrumentationKey``` irá devolver nulo. Corrige um problema, quando definir ```TelemetryConfiguration.Active.InstrumentationKey``` depois de alguns telemetria já foram recolhida, módulos de telemetria como Recolectores dependência, web pedidos recolha e desempenho contadores recolha irá utilizar a nova chave de instrumentação.

## <a name="version-015"></a>Versão 0,15

- Nova propriedade ```Operation.SyntheticSource``` agora disponível em ```TelemetryContext```. Agora pode marcar os itens de telemetria como "não utilizador real de tráfego de" e especificar a forma como este tráfego foi criado. Por exemplo ao definir esta propriedade, é possível distinguir tráfego a partir do seu automatização de teste de tráfego de teste de carregamento.
- Lógica de canal foi movida para a NuGet separada denominada Microsoft.ApplicationInsights.PersistenceChannel. Canal predefinido é agora designado InMemoryChannel
- Novo método ```TelemetryClient.Flush``` permite-lhe para esvaziar os itens de telemetria modo síncrono da memória intermédia

## <a name="version-013"></a>Versão 0.13

Sem notas de lançamento do versões mais antigas disponíveis. 
