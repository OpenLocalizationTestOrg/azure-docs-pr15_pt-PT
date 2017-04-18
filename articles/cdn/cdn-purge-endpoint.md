<properties
    pageTitle="Limpar um ponto final Azure CDN | Microsoft Azure"
    description="Saiba como limpar todo o conteúdo em cache a partir de um ponto final de CDN."
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

# <a name="purge-an-azure-cdn-endpoint"></a>Limpar um ponto final Azure CDN

## <a name="overview"></a>Descrição geral

Azure nós de limite CDN irão colocar em cache activos até time to live (TTL) o activo expira.  Depois de TTL o activo expira, quando um cliente pede activo a partir do nó de margem, o nó do limite, irá obter uma nova cópia atualizada do elemento para servir o pedido do cliente e arquivo Atualize a cache.

Por vezes, poderá pretender limpar o conteúdo em cache de todos os nós de limite e forçar-os para obter novos activos atualizados.  Isto pode estar relacionado actualizações para a aplicação web ou para atualizar rapidamente elementos que contêm informações incorretas.

> [AZURE.TIP] Tenha em atenção que apenas limpar limpa o conteúdo em cache nos servidores edge CDN.  Qualquer caches descendentes, tal como servidores proxy e de local browser, poderão ainda mantenha uma cópia do ficheiro em cache.  É importante não se esqueça de isto quando define um ficheiro time to live.  Pode forçar um cliente descendentes para pedir a versão mais recente do seu ficheiro, atribuindo-lhe um nome exclusivo sempre atualiza ou tirando partido da [Colocação em cache de cadeia de consulta](cdn-query-string.md).  

Neste tutorial orienta limpar elementos a partir de todos os nós de limite de um ponto final.

## <a name="walkthrough"></a>Instruções passo a passo

1. No [Portal do Azure](https://portal.azure.com), navegue para o perfil CDN que contém o ponto final que pretende limpar.

2. A partir do pá de perfil CDN, clique no botão Remover.

    ![Pá de perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)

    A remoção pá é aberta.

    ![Pá de remoção CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. No pá remover, selecione o endereço de serviço que pretende remover do menu pendente de URL.

    ![Limpar o formulário](./media/cdn-purge-endpoint/cdn-purge-form.png)

    > [AZURE.NOTE] Também pode aceder à pá a remoção ao clicar no botão **Limpar** no pá de ponto final de CDN.  Nesse caso, o campo de **URL** será previamente povoado com o endereço de serviço desse ponto final específico.

4. Selecione que elementos que pretende remover a partir de nós do limite.  Se pretender limpar todos os recursos, clique na caixa de verificação **Remover todos** .  Caso contrário, escreva o caminho completo de cada activo que pretende limpar (por exemplo, `/pictures/kitten.png`) na caixa de texto **caminho** .

    > [AZURE.TIP] Caixas de texto de **caminho** mais irão aparecer depois de introduzir texto para permitem-lhe criar uma lista de elementos de múltiplos.  Pode eliminar elementos a partir da lista ao clicar no botão reticências (…).
    >
    > Caminhos têm de ser um URL relativo que se ajustam a seguinte [expressão regular](https://msdn.microsoft.com/library/az24scfc.aspx): `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)*\*$";`.  Para **CDN Azure a partir do Verizon** (padrão e Premium), asterisco (\*) podem ser utilizados como um carácter universal (por exemplo, `/music/*`).  Carateres universais e **Limpar todos os** não são permitidas com **CDN Azure a partir do Akamai**.
    
5. Clique no botão **Limpar** .

    ![Remover botão](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] Pedidos de remoção demoram cerca de 2 a 3 minutos para processar com **CDN Azure a partir do Verizon** (padrão e Premium) e aproximadamente 7 minutos com **CDN Azure a partir do Akamai**.  Azure CDN tem um limite de 50 em simultâneo limpar pedidos a qualquer momento. 

## <a name="see-also"></a>Consulte também
- [Carregar previamente elementos de um ponto final Azure CDN](cdn-preload-endpoint.md)
- [Referência de CDN REST API Azure - limpar ou previamente carregar um ponto final](https://msdn.microsoft.com/library/mt634451.aspx)
