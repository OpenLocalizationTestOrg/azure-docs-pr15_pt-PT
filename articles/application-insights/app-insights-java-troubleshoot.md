<properties 
    pageTitle="Resolver problemas de aplicação informações num projeto de web Java" 
    description="Guia de resolução de problemas - direto Java aplicações, com informações de aplicação de monitorização." 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/01/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Resolução de problemas e perguntas e respostas para as informações de aplicação para Java

Perguntas ou problemas com o [Visual Studio, conhecimentos aprofundados de aplicação, em Java][java]? Eis algumas sugestões.


## <a name="build-errors"></a>Criar erros

*Eclipse, ao adicionar o SDK de informações da aplicação através de Maven ou Gradle, posso obter erros de validação de compilação ou soma.*

* Se a dependência <version> elemento está a utilizar um padrão com carateres universais (por exemplo, (Maven) `<version>[1.0,)</version>` ou (Gradle) `version:'1.0.+'`), tente especificar uma versão específica em vez disso, como `1.0.2`. Consulte as [notas de lançamento](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) para a versão mais recente.

## <a name="no-data"></a>Sem dados 

*Posso adicionada com êxito informações de aplicação e executou a minha aplicação, mas nunca visualizou dados no portal.*

* Aguarde um minuto e clique em Atualizar. Os gráficos periodicamente atualizar eles próprios, mas também pode atualizar manualmente. O intervalo de atualização depende o intervalo de tempo do gráfico.
* Verifique se tem uma chave de instrumentação definida no ficheiro ApplicationInsights.xml (na pasta de recursos no projeto)
* Certifique-se de que existe nenhum `<DisableTelemetry>true</DisableTelemetry>` nó no ficheiro xml.
* Na sua firewall, poderá ter de abrir portas TCP 80 e 443 tráfego de saída para dc.services.visualstudio.com. Consulte a [lista completa de exceções da firewall](app-insights-ip-addresses.md)
* No Microsoft Azure Iniciar quadro, observe o mapa de estado do serviço. Se existirem algumas indicações alertas, aguarde até que tenham sido devolvidos ao OK e, em seguida, feche e abra novamente o seu pá da aplicação de informações da aplicação.
* Ativar o registo na janela da consola IDE, adicionando um `<SDKLogger />` elemento sob o nó de raiz no ficheiro ApplicationInsights.xml (na pasta de recursos no projeto) e verificar existência de entradas precedido com [erro].
* Certifique-se de que o ficheiro ApplicationInsights.xml correcto tiver sido com êxito carregado pelo Java SDK, verificando as mensagens de saída da consola para uma instrução "ficheiro de configuração de tenha sido com êxito encontrado".
* Se o ficheiro de configuração não for encontrado, verifique as mensagens de saída para ver onde está a ser procurado o ficheiro de configuração e certifique-se de que o ApplicationInsights.xml está localizado dessas localizações de pesquisa. Como uma regra útil, pode colocar o ficheiro de configuração junto a aplicação informações SDK e vasos. Por exemplo: no Tomcat, isto seria significa a pasta WEB-INF/biblioteca.



#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Posso utilizada para ver dados, mas deixou de

* Verificar o [Estado de blogue](http://blogs.msdn.com/b/applicationinsights-status/).
* Acertar sua quota mensal de pontos de dados? Abra Definições/Quota e preços para descobrir. Se Sim, pode atualizar o plano ou pagar a capacidade adicional. Consulte o artigo [preços esquema](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Não consigo ver todos os dados que estou a esperar

* Abra as Quotas e preços pá e verifique se [amostragem](app-insights-sampling.md) é numa operação. (100% de transmissão significa que não está amostragem numa operação de.) O serviço de informações da aplicação pode ser definido para aceitar apenas uma fração de telemetria que chega da sua aplicação. Isto ajuda a manter na sua quota mensal de telemetria. 

## <a name="no-usage-data"></a>Sem dados de utilização

*Vejo dados sobre os pedidos e tempos de resposta, mas não vista de página, browser ou os dados de utilizador.*

Com êxito configura sua aplicação para enviar telemetria do servidor. Agora o próximo passo é configurar [as suas páginas web para enviar telemetria a partir do browser][usage].

Em alternativa, se o seu cliente é uma aplicação num [telemóvel ou outro dispositivo][platforms], pode enviar telemetria a partir daí. 

Utilize a mesma chave instrumentação para configurar o seu cliente e o servidor de telemetria. Os dados serão apresentados no mesmo recurso de informações da aplicação e poderá correlacionar eventos a partir do cliente e servidor.



## <a name="disabling-telemetry"></a>Desativar telemetria

*Como posso desativar coleção de telemetria?*

No código:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Ou** 

Atualize ApplicationInsights.xml (na pasta de recursos no projeto). Adicione o seguinte sob o nó de raiz:

    <DisableTelemetry>true</DisableTelemetry>

Utilizar o método XML, tem de reiniciar a aplicação quando altera o valor.

## <a name="changing-the-target"></a>Alterar o destino

*Como posso alterar qual meu projecto envia dados para o recurso Azure?*

* [Obter a chave de instrumentação do novo recurso.][java]
* Se adicionar informações da aplicação ao projeto utilizando o Toolkit de Azure para Eclipse, botão direito do rato clique em projeto web, selecione **Azure**, **Configurar informações de aplicação**e alterar a chave.
* Caso contrário, atualize a chave no ApplicationInsights.xml na pasta recursos no projeto.


## <a name="the-azure-start-screen"></a>O ecrã inicial do Azure

*Estou à procura no [portal do Azure](https://portal.azure.com). Que o mapa me diz algo sobre a minha aplicação?*

Não, mostra o estado de funcionamento dos servidores Azure em todo o mundo.

*A partir do quadro iniciar Azure (ecrã principal), como posso encontrar dados sobre a minha aplicação?*

Partindo do princípio que [Configurar a sua aplicação para informações de aplicação][java], clique em Procurar, selecione a aplicação de informações e selecione o recurso de aplicação que criou para a sua aplicação. Para obter existem mais rápido no futuro, pode afixar a aplicação ao quadro iniciar.

## <a name="intranet-servers"></a>Servidores da intranet

*Pode monitorizar um servidor na minha intranet?*

Sim, desde o que seu servidor pode enviar telemetria para o portal de informações da aplicação através da internet público. 

Na sua firewall, poderá ter de abrir portas TCP 80 e 443 tráfego de saída para dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Retenção de dados 

*Quanto tempo são guardados os dados no portal do? É segura?*

Consulte o artigo [retenção de dados e privacidade][data].

## <a name="next-steps"></a>Próximos passos

*Posso configurar as informações de aplicação para a minha aplicação do servidor de Java. Que mais posso fazer?*

* [Monitorizar a disponibilidade das suas páginas web][availability]
* [Monitorizar a utilização da página web][usage]
* [Controlar a utilização e diagnosticar problemas nas aplicações do seu dispositivo][platforms]
* [Escrever código para controlar a utilização da sua aplicação][track]
* [Capturar registos de diagnóstico][javalogs]


## <a name="get-help"></a>Obter ajuda

* [Texto em excesso de pilha](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 