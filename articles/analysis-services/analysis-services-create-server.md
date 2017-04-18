<properties
   pageTitle="Criar um servidor do Analysis Services no Azure | Microsoft Azure"
   description="Saiba como criar uma instância de servidor do Analysis Services no Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="create-an-analysis-services-server"></a>Criar um servidor do Analysis Services
Este artigo explica o criar um novo recurso de servidor do Analysis Services na sua subscrição do Azure.

## <a name="before-you-begin"></a>Antes de começar
Para começar, é necessário:

- **Subscrição do Azure**: visita [Azure versão de avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
- **Grupo de recursos**: utilizar um grupo de recursos já tiver ou [crie um novo](../azure-resource-manager/resource-group-overview.md).

> [AZURE.NOTE] Criar um servidor do Analysis Services poderá resultar num novo serviço cobrar. Para saber mais, consulte o artigo preços do Analysis Services.

## <a name="create-an-analysis-services-server"></a>Criar um servidor do Analysis Services

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Clique em **+ Novo** > **Intelligence + analytics** > **Analysis Services**.

3. No pá **Analysis Services** , preencha os campos obrigatórios e, em seguida, prima **Criar**.

    ![Criar servidor](./media/analysis-services-create-server/aas-create-server-blade.png)

    - **Nome do servidor**: escreva um nome exclusivo utilizado para o servidor de referência.

    - **Subscrição**: selecione a subscrição a pagar para este servidor.

    - **Grupo de recursos**: estas são contentores concebidas para ajudar a gerir a coleção de recursos Azure. Para saber mais, consulte o artigo [grupos de recursos](../resource-group-overview.md).

    - **Localização**: localização do Centro de dados de Azure este aloja o servidor. Escolha uma localização mais próximo da sua base de utilizador maior.

    - **Camadas de preços**: selecione uma camada comparar. Até 100 GB de modelos de tabela são suportadas. Pode sempre alterar a camada preços mais tarde.

4. Clique em **Criar**.

Criar normalmente leva num minuto; muitas vezes apenas alguns segundos. Se tiver seleccionado o **Adicionar ao Portal**, navegue até ao seu portal para ver o seu servidor de novo. Ou, navegue até ao **mais serviços** > **Analysis Services** para ver se o servidor está pronto. Se não aparecer, atualize a lista.

 ![Dashboard](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Próximos passos
Depois de criar o servidor, pode [Implementar um modelo](analysis-services-deploy.md) para a mesma utilizando SSDT ou com SSMS.

Se a um modelo de que implementar ao seu servidor de se ligar a origens de dados no local, terá de instalar um [gateway de dados no local](analysis-services-gateway.md) num computador na sua rede.
