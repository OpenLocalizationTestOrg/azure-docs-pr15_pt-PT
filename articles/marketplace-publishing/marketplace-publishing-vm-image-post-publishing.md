<properties
   pageTitle="Gerir a sua imagem de máquina virtual no Azure Marketplace | Microsoft Azure"
   description="Guia de detalhadas sobre como gerir a sua imagem de máquina virtual no Azure Marketplace depois da publicação inicial."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guia de produção pós para ofertas de máquina virtual no Azure Marketplace

Este artigo explica como pode atualizar uma oferta de Máquina Virtual direto no Azure Marketplace. Também orienta-o no processo de adicionar um ou mais novos SKUs a uma oferta existente e remover uma oferta de Máquina Virtual ou SKU direto do Azure Marketplace.

Assim que uma oferta/SKU está testado no [Portal do Azure](http://portal.azure.com), não é possível alterar os campos tendo em conta abaixo:

- **Oferecer identificador:** [Portal de publicação -> máquinas virtuais -> selecionar -> a sua oferta separador VM imagens -> oferecer identificador]
- **SKU identificador:** [Portal de publicação -> máquinas virtuais -> selecionar -> a sua oferta SKUs separador -> Adicionar um SKU]
- **Espaço de nomes do publisher:** [Portal de publicação -> máquinas virtuais -> guiadas separador -> diga-nos sobre a sua empresa (que se encontram em "Passo 2 registar a sua empresa") -> espaço de nomes do Publisher -> espaço de nomes]

Assim que a oferta/SKU estiver listada no [Azure Marketplace](http://azure.microsoft.com/marketplace), não é possível alterar os campos tendo em conta abaixo:

- **Oferecer identificador:** [Portal de publicação -> máquinas virtuais -> selecionar -> a sua oferta separador VM imagens -> oferecer identificador]
- **SKU identificador:** [Portal de publicação -> máquinas virtuais -> selecionar -> a sua oferta SKUs separador -> Adicionar um SKU]
- **Espaço de nomes do publisher:** [Portal de publicação -> máquinas virtuais -> Tutorial -> separador espaço de nomes do Publisher informar-nos sobre a sua empresa (que se encontram em passo 2 registar) -> espaço de nomes]
- **Portas** [Portal de publicação -> máquinas virtuais -> selecionar -> a sua oferta separador VM imagens -> abrir portas]
- **Preços mudança de SKU(s) listados**
- **Alteração de modelo de SKU(s) listados de faturação**
- **Remoção da faturação regiões do SKU(s) listados**
- **Alterar a contagem de disco de dados de SKU(s) listados**



## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. como atualizar os detalhes técnicos de um SKU

Pode adicionar uma nova versão para o SKU listado e voltar a publicar a sua oferta ao seguir os passos indicados abaixo:

1. Inicie sessão no [portal de publicação](https://publish.windowsazure.com).
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **VM imagens** .
4. A partir da secção **SKUs** do separador **VM imagens** , localize o SKU que pretende atualizar.
5. Após esta ação, adicione um novo número de versão do SKU e clique no botão **"+"** . A nova versão deve ser do formato de X.Y.Z onde X, Y e Z são inteiros. Alterações de versão só deverá utilizarão.
6. Na caixa **URL do SO VHD** , adicione a assinatura de acesso partilhado que URI criada para o sistema operativo VHD e guarde as alterações.

    >[AZURE.IMPORTANT] Qual é possível incremento/diminuir a contagem de disco de dados de um SKU listado. Tem de criar um novo SKU neste caso. Consulte a secção [3. Como adicionar um novo SKU numa oferta listada](#3-how-to-add-a-new-sku-under-a-live-offer) para uma orientação detalhada.

7. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md)
8. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. como atualizar os detalhes de uma oferta ou um SKU não técnicos

Pode atualizar o não técnicas (de marketing, legal, suporta, categorias) detalhes da oferta live ou SKU no Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 Atualize a descrição da oferta e logótipos

Pode atualizar os detalhes de oferta e voltar a publicar a sua oferta ao seguir os passos abaixo:

1. Inicie sessão no [portal de publicação](https://publish.windowsazure.com).
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **MARKETING** .
4. Clique no botão **Inglesa (e.u.a.)** .
5. A partir do menu do lado esquerdo, clique no separador **Detalhes** . Na secção *Descrição* do separador **Detalhes** pode atualizar o título da oferta, oferecer resumo, oferecer resumo longo e guardar as alterações.

    >[AZURE.NOTE] Fórum encarregam dos seguintes procedimentos enquanto está a atualizar os detalhes SKU.
    **Não introduza texto duplicado na descrição da oferta e a descrição da SKU. Não introduza texto duplicado sob o título SKU e a oferta tempo resumo. Não introduza texto duplicado sob o título SKU e o resumo de oferta.**

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. Na secção *LOGÓTIPOS* do separador **Detalhes** , pode atualizar os logótipos. No entanto, certifique-se de que os logótipos sigam as [diretrizes do Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte a secção passo 1: conteúdo de marketing de fornecer Marketplace -> detalhes -> diretrizes de logótipo do Azure Marketplace).

    >[AZURE.NOTE] Ícone de imagem é opcional. Pode optar por não carregar um ícone de imagem. No entanto, quando é carregado o ícone de imagem, em seguida, não existe nenhuma disposição para eliminá-la a partir de publicação portal. Nesse caso, terá de seguir as [diretrizes do ícone de imagem](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte a secção passo 1: conteúdo de marketing de fornecer Marketplace -> detalhes -> diretrizes adicionais para a faixa de logótipo herói).

7. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md).
8. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Atualize a descrição SKU

Pode atualizar os detalhes SKU e voltar a publicar a sua oferta ao seguir os passos abaixo:

1. Iniciar sessão no [portal de publicação](https://publish.windowsazure.com)
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **MARKETING** .
4. Clique no botão **Inglesa (e.u.a.)** .
5. A partir do menu do lado esquerdo, clique no separador **planos** . Na secção *SKUs* do separador **de planos** pode atualizar SKU título, SKU resumo e detalhes de descrição SKU e guardar as alterações.

    >[AZURE.NOTE] Fórum encarregam dos seguintes procedimentos enquanto está a atualizar os detalhes SKU. **Não introduza texto duplicado na descrição da oferta e a descrição da SKU. Não introduza texto duplicado em título o SKU e a oferta tempo resumo. Não introduza texto duplicado sob o título SKU e o resumo de oferta.**

6. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta ligação
7. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 alterar as ligações existentes ou adicionar ligações

Pode alterar as ligações existentes ou adicionar ligações e, em seguida, voltar a publicar a sua oferta ao seguir os passos abaixo:

1. Iniciar sessão no [portal de publicação](https://publish.windowsazure.com)
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **MARKETING** .
4. Clique no botão **Inglesa (e.u.a.)** .
5. A partir do menu do lado esquerdo, clique no separador **ligações** .
6. Se quiser adicionar uma ligação nova, em seguida, nas *ligações* secção clique no botão **Adicionar ligação** . Caixa de diálogo *"Adicionar ligação"* é aberto. Nesta caixa de diálogo, pode adicionar a ligação título e os campos de URL e guarde as alterações. Pode introduzir qualquer ligação que contém as informações que podem ajudar os clientes.
7. Se pretender atualizar ou eliminar uma ligação existente, em seguida, selecione a ligação adequada e clique no botão Editar ou no botão Eliminar em conformidade.

    >[AZURE.NOTE] Certifique-se de que as ligações que tenham entrado nesta secção estão a funcionar corretamente, tal como estas ligações obtenham validadas durante o processo de pedido de produção.

8. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md).
9. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 alterar uma imagem de exemplo existente ou adicionar uma nova imagem de exemplo

Pode alterar um imagens de exemplo existentes ou adicionar novas imagens de exemplo e, em seguida, voltar a publicar a sua oferta ao seguir os passos abaixo:

>[AZURE.NOTE] Imagem de apenas uma amostra é apresentada na [https://portal.azure.com](https://portal.azure.com).

1. Iniciar sessão no [portal de publicação](https://publish.windowsazure.com)
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **MARKETING** .
4. Clique no botão **Inglesa (e.u.a.)** .
5. A partir do menu do lado esquerdo, clique no separador **Imagens de exemplo** .
6. Se quiser adicionar uma nova imagem de exemplo, em seguida, na secção de *Imagens de exemplo* , clique no botão **Carregar A nova imagem** e, em seguida, guardar as alterações.

    >[AZURE.NOTE] Incluindo uma imagem de exemplo passo é opcional.

7. Se pretender atualizar ou eliminar uma imagem de exemplo existente, em seguida, localize a imagem de exemplo adequado e, em seguida, clique no botão **Substituir a imagem** ou no botão Eliminar em conformidade.

8. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md).
9. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2,5 atualizar o conteúdo legal

Pode atualizar o conteúdo legal e voltar a publicar a sua oferta ao seguir os passos abaixo:

1. Iniciar sessão no [portal de publicação](https://publish.windowsazure.com)
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **MARKETING** .
4. Clique no botão **Inglesa (e.u.a.)** .
5. A partir do menu do lado esquerdo, clique no separador **LEGAL** . Na secção *Legal* pode atualizar os políticas de termos de utilização. Introduza ou cole os termos/políticas na caixa de texto *Termos de utilização* e guardar as alterações.
6. O limite de carateres para os termos de utilização legais é 1.000.000 carateres.
7. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md)
8. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 atualizar as informações de suporte

Pode atualizar as informações de suporte e voltar a publicar a sua oferta ao seguir os passos abaixo:

1. Iniciar sessão no [portal de publicação](https://publish.windowsazure.com)
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador de **suporte** .
4. Na secção de *Engenharia de contacto* do separador de **suporte** pode atualizar os detalhes de contactos. Estes detalhes são utilizadas para a comunicação interna entre o parceiro e Microsoft apenas.
5. Na secção de *Suporte ao cliente* do separador de **suporte** pode atualizar os detalhes de contactos de suporte, como o **nome, o correio eletrónico, telefone** e o **URL de suporte**. Estes detalhes são utilizadas para a comunicação interna entre o parceiro e Microsoft apenas.

    >[AZURE.NOTE] Se quiser fornecer apenas o suporte por correio eletrónico, fornece um número de telefone fictícios na secção **Do suporte ao cliente** . Neste caso, o e-mail fornecido será utilizada como alternativa.

6. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md)
7. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 atualizar as categorias

Pode atualizar a secção de categorias para a sua oferta e voltar a publicar a sua oferta ao seguir os passos abaixo:

1. Iniciar sessão no [portal de publicação](https://publish.windowsazure.com)
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador de **categorias** .
4. Na secção *categorias* pode atualizar as categorias para a sua oferta e guardar as alterações. Pode selecionar até cinco categorias para a Galeria de Azure Marketplace.
5. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md)
6. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. como adicionar um novo SKU numa oferta listada

Pode adicionar uma nova SKU em sua oferta direto ao seguir os passos indicados abaixo:

1. Inicie sessão no [portal de publicação](https://publish.windowsazure.com).
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **SKUs** . Depois desse clicar no botão **Adicionar um SKU**.  Uma caixa de diálogo novo será aberto. Introduza um identificador SKU em letra minúscula. Marque a caixa de verificação trazer o-própria faturação model(BYOL) se pretende publicar o SKU novo com o modelo de faturação BYOL. Caso contrário, desmarque a caixa de verificação de BYOL. Após esse clique sobre a marca de escala na caixa de diálogo para criar um novo SKU. Se não optar por para o modelo de faturação BYOL para o novo SKU, em seguida, o modelo de faturação será automaticamente definido a hora a hora para o novo SKU. Se pretende ativar a versão de avaliação gratuita do 30days para modelo horário de faturação, em seguida, clique na opção de "Um mês" para "é uma versão de avaliação gratuita disponível?". Caso contrário, selecione "Sem versão de avaliação". [Nota: A opção "é uma versão de avaliação gratuita disponível?" só é mostrada se não tiver seleccionado BYOL na caixa de diálogo ao criar o novo SKU.]

    >[AZURE.IMPORTANT] A opção "Ocultar este SKU de mercado porque sempre deve ser comprada através de um modelo de solução" deve ser marcados como "Sim" apenas se sejam aprovados para publicar uma oferta de modelo de solução no Azure Marketplace. Caso contrário, esta opção sempre deve ser marcada como "Não".

4. Agora a partir do menu do lado esquerdo, clique no separador **Imagens VM** e descobrir o novo SKU que criou.
5. Para configurar o SKU novo, consulte o passo 5 desta [ligação](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) para obter orientações.
6. Para adicionar material de marketing para o novo SKU, consulte a secção passo 1: conteúdo de marketing de fornecer Marketplace -> detalhes -> números de vírgula 2 a 5 desta [ligação](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para adicionar informações sobre preços para o novo SKU, consulte a secção 2.1. Definir os preços VM desta [ligação](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Depois de efetuar as alterações, navegue para o separador **Publicar** e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md)
9. Assim que tiver testado a sua oferta em transição, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. como alterar a contagem de disco de dados para um SKU listado

Qual é possível incremento/diminuir a contagem de disco de dados de um SKU listado. Precisa de criar um novo SKU neste caso. Consulte a secção [3. Como adicionar um novo SKU numa oferta direto](#3-how-to-add-a-new-sku-under-a-live-offer) para uma orientação detalhada.

## <a name="5---how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5. como eliminar uma oferta listada do Azure Marketplace

Existem vários aspetos que precisam de ser tratados em caso de um pedido para remover uma oferta direto. Siga os passos abaixo para obter orientações da equipa do suporte para remover uma oferta listada do Azure Marketplace:

1.  Aumentar um bilhetes de suporte que utilizar esta [ligação](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2.  Selecione o tipo de problema como **"Gerir ofertas"** e selecione a categoria como **"Modificar uma oferta e/ou SKU que já existe na produção"**
3.  Submeter o pedido

A equipa de suporte irá guiá-lo durante o processo de eliminação de oferta/SKU.

>[AZURE.NOTE] Pode eliminar sempre a oferta enquanto está a ser um Estado de rascunho (ou seja, não em teste ou de produção) ao clicar no botão **REJEITAR rascunho** em separador **Histórico** .

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. como eliminar um SKU listado do Azure Marketplace

Pode eliminar um SKU listado do Azure Marketplace ao seguir os passos indicados abaixo:

1. Inicie sessão no [portal de publicação](https://publish.windowsazure.com).
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do painel do lado esquerdo, clique no separador **SKUS** .
4. Selecione o SKU que pretende eliminar e clique no botão Eliminar contra esse SKU.
5. Depois de concluir o processo, navegue até ao separador Publicar de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a oferta no Azure Marketplace.
6. Depois da oferta novamente é publicada no Azure Marketplace, o SKU será eliminado a partir do Azure Marketplace e o Portal do Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. como eliminar a versão atual de um SKU listado do Azure Marketplace

Pode eliminar a versão atual de um SKU listado do Azure Marketplace ao seguir os passos indicados abaixo. Assim que o processo for concluído, o SKU vai ser revertido para a versão anterior.

1. Inicie sessão no [portal de publicação](https://publish.windowsazure.com).
2.  Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3.  A partir do painel do lado esquerdo, clique no separador **VM imagens** .
4.  Selecione o SKU cuja versão atual que pretende eliminar e clique no botão Eliminar contra essa versão.
5.  Depois de concluir o processo, navegue até ao separador **Publicar** de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a oferta no Azure Marketplace.
6.  Depois da oferta novamente é publicada no Azure Marketplace, a versão atual do SKU listado será eliminada a partir do Azure Marketplace e o Portal do Azure. O SKU vai ser revertido para a versão anterior.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. como reverter o preço de lista para valores de produção
Alterei o ter os preços de um SKU listado (ou tenha removido as regiões de faturaçãohttps de um SKU listado). Uma vez que não é suportada no Azure Marketplace, quiser reverter as minhas alterações para os valores de produção. Como alcançar que?

Siga os passos indicados abaixo:

1. Inicie sessão no [portal de publicação](https://publish.windowsazure.com).
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador de **preços** .
4. No separador de preços, selecione uma região cujo preços que pretende repor.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. Em caso de SKUs com modelo horário de faturação, repor os preços para todos os tarolos como se encontram de produção para a região seleccionada. Para SKUs com modelo faturação BYOL, disponibilizar o SKU na região ao selecionar a caixa de verificação contra SKU na secção disponibilidade de SKU EXTERNALLY-LICENSED (BYOL) (consulte a captura de ecrã abaixo).

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. Agora, clique no botão **AUTOPRICE outros mercados com base em preços em UNITED Estados-membros**.

    >[AZURE.NOTE] Etiqueta no botão pode ser diferente dependendo da região que tiver selecionado. Uma vez que recomendamos tiver selecionado dos Estados Unidos enquanto cria este documento, por isso, o botão é identificado como "Automática preço outros mercados com base em preços nos Estados Unidos" na captura de ecrã abaixo.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. Será aberto o Assistente de preço automático. Primeira página apresenta a seleção para mercado base. Tornar a secção e deslocar-se para a página seguinte clicando no botão **"->"** .

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. Opção para selecionar o núcleos e planos será apresentada na página 2. Selecione os planos pretendidos e os tarolos e clique em-">" botão.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. Página 3 apresenta as regiões/mercados. Clique no botão Mostrar/Ocultar tudo para selecionar todas as regiões ou manualmente marque as caixas para região. Clique no botão "->" para mover para a página seguinte.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. Página 4 apresenta as taxas do exchange. Clique no botão Concluir para concluir os passos. O assistente irá repor os preços de acordo com a sua seleção.

11. Agora, navegue para o separador comparar e clique no botão "Ver resumo e alterações".
Selecione "Rascunho" na secção de "Vista versão" e "Produção" na secção "Comparar com" (consulte a captura de ecrã abaixo). Não se vir nenhuma diferença comparar, implica preços foi revertido para os valores de produção com êxito.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. Depois de efetuar as alterações, navegue para o separador Publicar e clique no botão **De emissão para transição**. Para uma orientação detalhada sobre como testar a sua oferta no ambiente de teste Consulte esta [ligação](marketplace-publishing-vm-image-test-in-staging.md)
13. Assim que tiver testado a sua oferta em transição, navegue até ao separador Publicar de publicação portal e clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. como reverter o modelo de faturação para valores de produção
Posso alterou o modelo de faturação de um SKU listado. Uma vez que não é suportada no Azure Marketplace, quiser reverter as minhas alterações para os valores de produção. Como alcançar que?

Siga os passos abaixo:

1. Inicie sessão no [portal de publicação](https://publish.windowsazure.com).
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **SKUS** .
4. Clique em botão Editar para reverter o modelo de faturação. Será apresentada uma janela. Selecione ou desmarque a caixa de verificação **'faturação e licenciamento é concluído externamente a partir do Azure (também conhecidos por trazer o próprio licença)'** em conformidade.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. Uma vez, consulte a resposta à pergunta 8 neste documento para reverter novamente os preços.
6. Depois de que navegue até ao separador **Publicar** de publicação portal e push a oferta de transição para testá-la. Uma vez pode terminar com a oferta de ensaio, em seguida, clique no botão **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. como reverter a definição de visibilidade de um SKU listado para o valor de produção

Siga os passos abaixo:

1. Inicie sessão no [portal de publicação](https://publish.windowsazure.com).
2. Navegue para o separador **máquinas virtuais** e selecione a sua oferta.
3. A partir do menu do lado esquerdo, clique no separador **SKUS** .
4. Selecione o seu SKU e reverter a definição de visibilidade do SKU para o valor de produção.

    ![desenho](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. Uma vez que terminar com as alterações e clique no botão de **Pedir aprovação para notificações PUSH para produção** para voltar a publicar a sua oferta no Azure Marketplace.

## <a name="see-also"></a>Consulte também
- [Introdução: Como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md)
- [Noções sobre informações vendedor elaboração de relatórios](marketplace-publishing-report-seller-insights.md)
- [Noções sobre payout elaboração de relatórios](marketplace-publishing-report-payout.md)
- [Como alterar o seu incentivos revendedor do fornecedor de solução da nuvem](marketplace-publishing-csp-incentive.md)
- [Resolução de problemas comuns de publicação no mercado](marketplace-publishing-support-common-issues.md)
- [Obter suporte como um fabricante](marketplace-publishing-get-publisher-support.md)
- [Criar uma imagem VM no local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Criar uma máquina de virtual a executar o Windows no portal do Azure pré-visualização](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
