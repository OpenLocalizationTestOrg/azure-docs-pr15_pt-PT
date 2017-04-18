<properties
    pageTitle="Posso utilizar serviços Mobile, como aplicação de serviço de ajuda?"
    description="Saiba que vantagens aplicação de serviço de trazer para a sua projectos de serviços móveis existentes."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="getting-started"> </a>Utilizar serviços Mobile, como é que aplicação de serviço de ajuda?

##<a name="overview"></a>Descrição geral
O serviço móvel do existente é seguro e permanecerá suportado. No entanto, existem número de vantagens a *Aplicação de serviço de Azure* plataforma fornece para a sua aplicação móvel que não estão disponíveis hoje com os serviços de Mobile:

- Oferta, mais fácil mais simples e custo eficaz para as aplicações que incluem web e os clientes móveis
- Novas funcionalidades de anfitrião incluindo Web tarefas, CNames personalizado, melhor monitorização
- Chave na mão integração com o Gestor de tráfego
- Conectividade aos seus recursos no local e VPNs utilizando VNet para além de ligações de híbrido
- Monitorização, alertas e resolução de problemas para a sua aplicação utilizando NewRelic ou AppInsights
- O espectro mais diversificado de subjacente calcular recursos e preços
- Dimensionar automaticamente incorporados, balanceamento de carga e monitorização de desempenho.
- Incorporados de transição, as capacidades de cópia de segurança, voltará e testes de produção

## <a name="new-hosting-features"></a>Novas funcionalidades de alojamento
Na *Aplicação de serviço de Azure* a *Aplicação Mobile* o código de back-end é executado no mesmo contentor como Web App e API App. Como tal pode tirar partido das funcionalidades neste contentor, incluindo algumas das pessoas que não estão atualmente presentes nos serviços do Mobile:

- Adicionar lógica continuamente a execução de back-end através de tarefas de Web
- Certifique-se de que o código de back-end sempre está a ser executado
- Utilizar CNames personalizado para fornecer amigáveis e estáveis nomes para os pontos finais de back-end móvel
- Geo escala sua aplicação com o Gestor de tráfego
- Inclua qualquer bibliotecas e pacotes que pretende.
- (Para .NET) Tirar partido de qualquer uma das funções do ASP.NET, incluindo MVC
- (Para Node.js) Tirar partido qualquer biblioteca JavaScript pura do ecossistema nó, incluindo bibliotecas MVC comuns.

##<a name="access-on-premises-data-using-vnet"></a>Dados do Access no local com VNet
Com os serviços de Mobile hoje pode já utilizar híbrido ligações para recursos do access no local. Contudo, existem situações em que uma solução VPN é preferida. Com a *Aplicação de serviço de Azure* pode utilizar Azure VNet para o código de back-end aplicação Mobile.

##<a name="use-your-favorite-backend-language"></a>Utilizar o seu idioma favoritas back-end
*Aplicação de serviço de Azure* oferece mais amplo e mais rica suporte para plataformas ASP.NET e Node.js, incluindo o acesso ao runtimes a mais recente.

##<a name="set-up-automatic-scale"></a>Configurar o escala automática
Com os serviços de Mobile, todas as instâncias do código back-end foram em execução no VMs pequenas. *Aplicação de serviço de Azure* permite-lhe selecionar o tamanho das VMs a partir de um conjunto de opções muito mais rico. Pode também rapidamente Dimensionar para cima ou para fora para processar quaisquer recebidas carga de cliente, com base em várias métricas de desempenho.

##<a name="be-in-the-know"></a>Estar na "saber"
Reajam aos problemas em tempo real a monitorização e alertas para notificar automaticamente o utilizador e a sua equipa. Integre uma funcionalidade de monitorização de novo Relic e AppInsights para obter ainda mais rica visão como está a executar a aplicação móvel e de análise de aplicação avançadas. Com a *Aplicação de serviço de Azure* agora pode configurar alertas baseadas variedade de métricas de desempenho, de forma programática e através do Portal do Azure.

##<a name="keep-your-assets-safe"></a>Manter os seus ativos seguros
Automaticamente uma cópia seu back-end e a base de dados. O código e dados é seguros de falhas e facilmente restaurado, permitindo-lhe executar o seu negócio com confiança.

##<a name="ready-stage-go"></a>Pronto, fase, esteja onde estiver!
Com a *Aplicação de serviço de Azure* agora pode criar testes privado vários e teste ambientes para as aplicações móveis. Utilizá-los para efetuar testes antes de implementar. Trocar produção com sem indisponibilidade. Aplicações Web são carregadas previamente, garantindo a melhor experiência de cliente.

Pode obter iniciar tirando partido da *Aplicação de serviço* do seu serviço de Mobile existente seguindo este [tutorial](app-service-mobile-migrating-from-mobile-services.md).

