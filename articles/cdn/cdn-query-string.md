<properties
    pageTitle="Controlar o Azure CDN colocação em cache comportamento de pedidos de cadeias de consulta | Microsoft Azure"
    description="Cadeia de consulta do Azure CDN colocação em cache controlos, como os ficheiros devem ser colocadas em cache quando contêm cadeias de consulta."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings"></a>Controlar o comportamento de colocação em cache de pedidos de CDN com cadeias de consulta

> [AZURE.SELECTOR]
- [Padrão](cdn-query-string.md)
- [Premium Azure CDN a partir do Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Descrição geral

Cadeia de consulta colocação em cache controlos, como os ficheiros devem ser colocadas em cache quando contêm cadeias de consulta.

> [AZURE.IMPORTANT] Os produtos padrão e Premium CDN fornecem a mesma cadeia de consulta colocação em cache funcionalidade, mas a interface de utilizador é diferente.  Este documento descreve a interface para **Azure CDN padrão de Akamai** e **Azure CDN padrão de Verizon**.  Para na cache cadeia de consulta com o **Azure CDN Premium a partir do Verizon**, consulte o artigo [controlar o comportamento colocação em cache de pedidos de CDN com cadeias de consulta - Premium](cdn-query-string-premium.md).

Estão disponíveis três modos:

- **Ignorar cadeias de consulta**: Este é o modo de predefinido.  O nó do limite CDN irá transmitir a cadeia de consulta a partir do autor do pedido para a origem do primeiro pedido e a cache de elemento.  Todos os pedidos subsequentes para esse activo são servidos a partir do nó limite irão ignorar a cadeia de consulta até expira elemento em cache.
- **Ignorar a colocação em cache para o URL com cadeias de consulta**: neste modo, pedidos de cadeias de consulta não são colocadas em cache no nó de limite de CDN.  O nó do limite obtém o activo diretamente a partir da origem e transmite-lo para o autor do pedido com cada pedido.
- **Todos os URL exclusivo em cache**: deste modo trata cada pedido por uma cadeia de consulta, como um activo exclusivo com a sua própria cache.  Por exemplo, a resposta a partir da origem para um pedido de *foo.ashx?q=bar* seria em cache no nó de limite e devolvida para caches subsequentes com essa mesma cadeia de consulta.  Um pedido para *foo.ashx?q=somethingelse* seria colocadas em cache como um activo separado com as suas próprias hora a dinâmicos.

##<a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Alterar definições para os perfis de CDN padrão de cache de cadeia de consulta

1. Pá de perfil CDN, clique no ponto final de CDN que pretende gerir.

    ![Pontos finais pá de perfil CDN](./media/cdn-query-string/cdn-endpoints.png)

    É aberta a pá de ponto final CDN.

2. Clique no botão de **Configurar** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-query-string/cdn-config-btn.png)

    É aberta a pá CDN configuração.

3. Selecione uma definição do menu pendente de **Colocação em cache comportamento de cadeia de consulta** .

    ![Cadeia de consulta CDN opções de colocação em cache](./media/cdn-query-string/cdn-query-string.png)

4. Depois de efetuar a sua seleção, clique no botão **Guardar** .

> [AZURE.IMPORTANT] As alterações de definições não podem ser imediatamente visíveis, tal como demora tempo para o registo a serem propagadas através de CDN.  Perfis de <b>CDN Azure a partir do Akamai</b> , de propagação normalmente irá concluir dentro de um minuto.  Perfis de <b>CDN Azure a partir do Verizon</b> , de propagação normalmente irá concluir no prazo de 90 minutos, mas em alguns casos, pode demorar mais tempo.
