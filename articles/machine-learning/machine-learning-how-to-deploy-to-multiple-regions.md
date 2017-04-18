<properties
    pageTitle="Como implementar um serviço Web para várias regiões | Microsoft Azure"
    description="Passos para implementar regiões (cópia) um novo serviço Web para outra."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Como implementar um serviço Web para várias regiões

Os novos serviços de Web do Azure permitem-lhe implementar facilmente um serviço web para várias regiões sem necessitar de múltiplas subscrições ou áreas de trabalho. 

Preços são região específico, por conseguinte, tem de definir um plano de faturação para cada região em que irá implementar o serviço web.

## <a name="to-create-a-plan-in-another-region"></a>Para criar um plano de outro região

1. Inicie sessão no [computador do Microsoft Azure serviços Web de aprendizagem](https://services.azureml.net/).
2. Clique na opção de menu **planos** .
3. Sobre os planos sobre a página de vista, clique em **Novo**.
4. Na lista pendente de **subscrição** , selecione a subscrição em que o novo plano residirão.
5. No menu pendente **região** , selecione uma região para o novo plano. As opções do plano para a região seleccionada será apresentada na secção **Opções do plano** da página.
6. No menu pendente do **Grupo de recursos** , selecione um grupo de recursos para o plano. A partir de mais informações sobre os grupos de recursos, consulte o artigo [Gerir Azure recursos através do portal](../azure-portal/resource-group-portal.md).
7. No **Nome do plano** , escreva o nome do plano.
8. Em **Opções de plano**, clique no nível de faturação para o novo plano.
9. Clique em **Criar**.


## <a name="deploying-the-web-service-to-another-region"></a>Implementar o serviço web para outro região

1. Clique na opção de menu de **Serviços Web** .
2. Selecione o serviço Web estiver a implementar uma nova região.
3. Clique em **Copiar**.
4. Em **Nome de serviço Web**, escreva um novo nome para o serviço web.
5. Na **Descrição do serviço Web**, escreva uma descrição para o serviço web.
6. Na lista pendente de **subscrição** , selecione a subscrição em que o novo serviço web residirão.
7. No menu pendente do **Grupo de recursos** , selecione um grupo de recursos para o serviço web. A partir de mais informações sobre os grupos de recursos, consulte o artigo [Gerir Azure recursos através do portal](../azure-portal/resource-group-portal.md).
8. No menu pendente **região** , selecione a região em que pretende implementar o serviço web.
9. Na lista **conta de armazenamento** pendente, selecione uma conta de armazenamento no qual pretende armazenar o serviço web.
10. No menu pendente do **Preço plano** , selecione um plano na região que selecionou no passo 8.
11. Clique em **Copiar**.

