<properties
    pageTitle="Batch quotas do serviço e os limites | Microsoft Azure"
    description="Saiba mais sobre as quotas de Azure lote predefinido, limites e restrições, e saiba como pedir quota aumenta"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/10/2016"
    ms.author="marsma"/>

# <a name="quotas-and-limits-for-the-azure-batch-service"></a>Quotas e os limites para o serviço do Azure Batch

Como com outros serviços do Azure, existem limites de determinados recursos associados ao serviço do lote. Muitos destes limites são aplicadas ao Azure subscrição ao nível de conta ou as quotas de predefinido. Este artigo fala sobre essas predefinições e como pode pedir quota aumenta.

Se planeia executar cargas de trabalho de produção no lote, poderá ter de aumentar um ou mais das quotas acima predefinido. Se pretende aumentar uma quota, pode abrir uma online [pedido do suporte ao cliente](#increase-a-quota) sem encargos.

>[AZURE.NOTE] Uma quota é um limite de crédito, não uma garantia capacidade. Se tiver necessidades da capacidade em grande escala, contacte o suporte Azure.

## <a name="subscription-quotas"></a>Quotas de subscrição
**Recurso**|**Limite predefinido**|**Limite máximo**
---|---|---
Contas do lote por região por subscrição | 1 | 50

## <a name="batch-account-quotas"></a>Quotas de conta do lote
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Outros limites
**Recurso**|**Limite máximo**
---|---
[Tarefas em simultâneo](batch-parallel-node-tasks.md) por cluster nó | número de x 4 de núcleos nó
[Aplicações](batch-application-packages.md) por conta Batch        | 20
Pacotes de aplicações por aplicação  | 40
Tamanho do pacote de aplicação (cada)       | Cerca 195GB<sup>1</sup>

Limite de armazenamento do Windows azure <sup>1</sup> para o tamanho de Blobs do bloco máximo

## <a name="view-batch-quotas"></a>Ver as quotas de Batch

Ver as quotas de conta do lote no [portal do Azure][portal].

1. Selecione **contas do lote** no portal do, em seguida, selecione a conta do lote que lhe interessa.

2. Selecione **Propriedades** no pá de menu a conta Batch

3. O pá propriedades apresenta as **quotas** atualmente aplicado à conta Batch

    ![Quotas de conta do lote][account_quotas]

## <a name="increase-a-quota"></a>Aumentar uma quota

Siga os passos abaixo para pedir uma quota aumentam utilizando o [Azure portal][portal].

1. Selecione o mosaico **Ajuda + suporte** no seu portal dashboard ou o ponto de interrogação (****?) no canto superior direito do portal.

2. Selecione **novo pedido de suporte** > **Noções básicas**.

3. No pá **Noções básicas** :

    um. **Tipo de problema** > **Quota**

    b. Selecione a sua subscrição.

    c. **Tipo de quota** > **Batch**

    d. **Plano de suporte** > **suporte de Quota - incluído**

    Clique em **seguinte**.

4. No pá **problema** :

    um. Selecione uma **gravidade** de acordo com o [impacto de empresas][support_sev].

    b. **Detalhes**, especifique a quota de cada que pretende alterar, o nome da conta lote e o novo limite.

    Clique em **seguinte**.

5. No pá **informações de contacto** :

    um. Selecione um **método de contacto preferido**.

    b. Verificar e introduza os detalhes de contactos necessários.

    Clique em **Criar** para submeter o pedido de suporte.

Uma vez que já utilizou ao submeter o pedido de suporte, Azure suporte irá contactá-lo. Note que a concluir o pedido pode demorar até 2 dias úteis.

## <a name="related-topics"></a>Tópicos relacionados

* [Criar uma conta de lote Azure utilizando o portal do Azure](batch-account-create-portal.md)

* [Descrição geral de funcionalidade do Azure Batch](batch-api-basics.md)

* [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
