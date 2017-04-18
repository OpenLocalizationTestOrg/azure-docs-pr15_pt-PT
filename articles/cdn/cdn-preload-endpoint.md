<properties
    pageTitle="Carregar previamente elementos de um ponto final Azure CDN | Microsoft Azure"
    description="Saiba como carregar conteúdo em cache num ponto final de CDN previamente."
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

# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Carregar previamente elementos de um ponto final Azure CDN

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Por predefinição, elementos são pela primeira vez em cache como forem pedidas. Isto significa que o primeiro pedido a partir de cada região poderá demorar mais tempo, uma vez que os servidores edge não terão o conteúdo em cache e será necessário reencaminhar o pedido ao servidor de origem. Pré-carregar conteúdo evita esta latência visitas primeira.

Para além de fornecer uma melhor experiência de cliente, a ser carregados previamente seus ativos em cache também podem reduzir o tráfego de rede no servidor de origem.

> [AZURE.NOTE] Elementos a ser carregados previamente são útil para eventos grandes ou conteúdo que torna-se em simultâneo disponível para um grande número de utilizadores, tal como uma nova versão de filme ou uma atualização de software.

Neste tutorial orienta previamente carregar conteúdo em cache em todos os nós de limite de Azure CDN.

## <a name="walkthrough"></a>Instruções passo a passo

1. No [Portal do Azure](https://portal.azure.com), navegue para o perfil CDN que contém o ponto final que pretende carregar previamente.  É aberta a pá de perfil.

2. Clique no ponto final da lista.  É aberta a pá de ponto final.

3. A partir do pá de ponto final CDN, clique no botão carregar.

    ![Pá de ponto final CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)

    É aberta a pá de carregamento.

    ![Pá de carga CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)

4. Introduza o caminho completo de cada activo que pretende carregar (por exemplo, `/pictures/kitten.png`) na caixa de texto **caminho** .

    > [AZURE.TIP] Caixas de texto de **caminho** mais irão aparecer depois de introduzir texto para permitem-lhe criar uma lista de elementos de múltiplos.  Pode eliminar elementos a partir da lista ao clicar no botão reticências (…).
    >
    > Caminhos têm de ser um URL relativo que se adequa a seguinte [expressão regular](https://msdn.microsoft.com/library/az24scfc.aspx): `^(?:\/[a-zA-Z0-9-_.\u0020]+)+$`.  Cada activo tem de ter os suas próprias caminho.  Não existe sem universais a funcionalidade de activos previamente carregamento.

    ![Botão carregar](./media/cdn-preload-endpoint/cdn-load-paths.png)

5. Clique no botão **carregar** .

    ![Botão carregar](./media/cdn-preload-endpoint/cdn-load-button.png)

> [AZURE.NOTE] Existe um limite de pedidos de carga 10 por minuto por perfil CDN.

## <a name="see-also"></a>Consulte também
- [Limpar um ponto final Azure CDN](cdn-purge-endpoint.md)
- [Referência de CDN REST API Azure - limpar ou previamente carregar um ponto final](https://msdn.microsoft.com/library/mt634451.aspx)
