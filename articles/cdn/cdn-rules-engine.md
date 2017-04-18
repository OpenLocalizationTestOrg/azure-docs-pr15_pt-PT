<properties
    pageTitle="Substituir o comportamento HTTP predefinido no Azure CDN utilizando o motor de regras | Microsoft Azure"
    description="O motor de regras permite-lhe personalizar pedidos de HTTP que forma são processados pelo Azure CDN, tal como a bloquear a entrega de determinados tipos de conteúdo, definir a política de colocação em cache e modificar cabeçalhos de HTTP."
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

# <a name="override-default-http-behavior-using-the-rules-engine"></a>Substituir o comportamento HTTP predefinido utilizando o motor de regras

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

O motor de regras permite-lhe personalizar como são processados os pedidos de HTTP, como a bloquear a entrega de determinados tipos de conteúdo, definir uma política de colocação em cache e modificar cabeçalhos de HTTP.  Neste tutorial vai demonstrar a criação de uma regra que irá alterar o comportamento de activos CDN colocação em cache.  Também existe conteúdo de vídeo disponíveis na secção "[Consulte também](#see-also)".

## <a name="tutorial"></a>Tutorial

1. A partir do pá de perfil CDN, clique no botão **Gerir** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

    O portal de gestão de CDN é aberta.

2. Clique no separador **HTTP grandes** , seguido do **Motor de regras**.

    São apresentadas as opções para uma nova regra.

    ![Novas opções de regra CDN](./media/cdn-rules-engine/cdn-new-rule.png)

    >[AZURE.IMPORTANT] A ordem pela qual são listadas múltiplas regras afeta a forma como são processados. Uma regra subsequente poderá substituir as ações especificadas por uma regra anterior.
    
3. Introduza um nome na **nome / descrição** caixa de texto.

4. Identifique o tipo de pedidos da que regra será aplicada a.  Por predefinição, a condição de correspondência **sempre** está selecionada.  Irá utilizar **sempre** para este tutorial, por isso, deixe que selecionados.

    ![Condição de correspondência CDN](./media/cdn-rules-engine/cdn-request-type.png)

    >[AZURE.TIP] Existem vários tipos de correspondência de condições disponíveis na lista pendente.  Clicando no ícone azul informativo à esquerda da condição de correspondência irá explicar a condição detalhadamente atualmente selecionada.
    >
    >Para a lista completa de condições de correspondência em detalhes, consulte o artigo [regras motor corresponder condição e detalhes de funcionalidade](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0).

5.  Clique na **+** botão ao lado de **funcionalidades** para adicionar uma nova funcionalidade.  Na lista pendente à esquerda, selecione **Forçar interno Max-idade**.  Na caixa de texto que é apresentada, introduza **300**.  Deixe os valores predefinidos restantes.

    ![Funcionalidade CDN](./media/cdn-rules-engine/cdn-new-feature.png)

    >[AZURE.NOTE] Como com condições de correspondência, clicando no ícone azul informativo à esquerda da nova funcionalidade irá apresentar detalhes sobre esta funcionalidade.  No caso de **Forçar interno máximo de idade**, podemos são substituir os cabeçalhos de **Cache-Control** e **expira** o activo para controlar quando o nó do limite CDN atualizarão ativo a partir da origem.  Nosso exemplo de 300 segundos significa que o nó do limite CDN será em cache elemento 5 minutos antes de atualizar os elementos da sua origem.
    >
    >Para a lista completa de funcionalidades em maior detalhe, consulte o artigo [regras motor correspondência condição e detalhes de funcionalidade](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1).

6.  Clique no botão **Adicionar** para guardar a nova regra.  A nova regra está agora a aguardar aprovação. Assim que foram aprovada, o estado irá alterar a partir de **XML pendentes** para **XML ativo**.

    >[AZURE.IMPORTANT] Alterações de regras poderão demorar até 90 minutos a serem propagadas através a CDN.

## <a name="see-also"></a>Consulte também
* [Sextas-feiras azure: novas e poderosas Premium funcionalidades do Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)
* [Condição de correspondência do motor de regras e detalhes de funcionalidade](https://msdn.microsoft.com/library/mt757336.aspx)
