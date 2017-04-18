<properties
   pageTitle="Criar uma regra com base no caminho para um gateway aplicação utilizando o portal | Microsoft Azure"
   description="Saiba como criar uma regra com base no caminho para um gateway aplicação utilizando o portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Criar uma regra com base no caminho para um gateway aplicação utilizando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-url-route-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-url-route-arm-ps.md)

Encaminhamento de baseado no caminho de URL permite-lhe associar rotas com base no caminho do URL de pedido de Http. Verifica se existir uma rota para um conjunto de dados back-end configurado para as listas de URL no Gateway de aplicação e enviar o tráfego de rede para o conjunto de dados back-end definido. Uma utilização comum para o encaminhamento de baseados em URL consiste em carregar saldo pedidos para diferentes tipos de conteúdo para conjuntos de servidor de back-end diferente.

Baseada em URL encaminhamento introduz um novo tipo de regra para gateway de aplicação. Gateway aplicação tem dois tipos de regra: regras baseadas no caminho e básicas. Tipo de regra básicas fornece suporte de round robin para os conjuntos de back-end enquanto as regras baseadas no caminho para além de distribuição round robin, atende também padrão de caminho do URL pedido ao escolher o agrupamento de back-end.

## <a name="scenario"></a>Cenário

Analise o cenário que se segue ao criar uma regra com base no caminho num gateway de aplicação existente.
O cenário assume que já tenha seguido os passos para [criar um Gateway de aplicação](application-gateway-create-gateway-portal.md).

![rota de URL][scenario]

## <a name="createrule"></a>Criar a regra com base no caminho

Uma regra com base no caminho requer a sua própria escuta, antes de criar a regra Certifique-se verificar se tem uma escuta disponível para utilizar.

### <a name="step-1"></a>Passo 1

Navegue para http://portal.azure.com e selecione um gateway de aplicação existente. Clique em **regras**

![Descrição geral de Gateway de aplicação][1]

### <a name="step-2"></a>Passo 2

Clique em botão **baseado no caminho** para adicionar uma nova regra com base no caminho.

### <a name="step-3"></a>Passo 3

A **Adicionar regra com base no caminho** pá tem duas secções. A primeira secção é onde definido a escuta, o nome da regra e as predefinições do caminho. São as predefinições de caminho para rotas que não se a rota com base no caminho personalizada. A segunda secção do pá a **Adicionar regra com base no caminho** é onde pode definir as regras baseadas no caminho próprios.

**Definições básicas**

- **Nome** - este é um nome amigável para a regra que é acessível no portal.
- **Escuta** - este é o serviço de escuta que é utilizado para a regra.
- **Agrupamento de back-end predefinido** - esta definição é a definição que define back-end para ser utilizado para a regra predefinida
- **Definições de HTTP predefinido** - esta definição é a definição que define as definições de HTTP para ser utilizado para a regra predefinida.

**Regras baseadas no caminho**

- **Nome** - este é um nome amigável para uma regra com base no caminho.
- **Caminhos** - esta definição define o caminho que a regra será o aspeto de quando o tráfego de reencaminhamento de chamadas
- **Agrupamento de back-end** - esta definição é a definição que define back-end para ser utilizado para a regra
- **Definição de HTTP** - esta definição é a definição que define as definições de HTTP para ser utilizado para a regra.

>[AZURE.IMPORTANT] Caminhos: A lista de padrões de caminho para corresponder ao. Cada tem de iniciar com / e o local apenas um "\*" é permitido se encontra no fim. Exemplos válidos são /xyz, /xyz* ou /xyz/*.  

![Adicionar pá regra com base no caminho com informações preenchidas][2]

Adicionar uma regra com base no caminho para um gateway de aplicação existente é um processo fácil através do portal. Assim que tiver sido criada uma regra com base no caminho, pode editá-lo para adicionar facilmente regras adicionais. 

![adicionar regras baseadas no caminho adicionais][3]

## <a name="next-steps"></a>Próximos passos

Para saber como configurar SSL descarregar com Azure aplicação Gateway consulte o artigo [Configurar SSL descarregar](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png