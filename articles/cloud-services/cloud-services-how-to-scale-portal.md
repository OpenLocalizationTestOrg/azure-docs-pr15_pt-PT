<properties
    pageTitle="Automática dimensionar um serviço na nuvem no portal do | Microsoft Azure"
    description="Saiba como utilizar o portal para configurar regras de escala automática para uma função de web do serviço na nuvem ou trabalhador no Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="how-to-auto-scale-a-cloud-service"></a>Como Dimensionar automaticamente um serviço na nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-scale-portal.md)
- [Azure portal clássico](cloud-services-how-to-scale.md)

Condições podem ser definidas para uma função de trabalho de serviço na nuvem acionar uma escala de cores ou reduzir a operação. As condições para a função podem ser baseadas na CPU, disco ou carga na rede da função. Também pode definir um conditation com base na fila de mensagens ou uma métrica de alguns recursos Azure associada à sua subscrição.

>[AZURE.NOTE] Este artigo foca-se em funções de web e trabalhador serviço na nuvem. Quando cria uma máquina de virtual (clássico) diretamente, está alojado num serviço de nuvem. Pode dimensionar uma máquina virtual padrão associando-o com um [conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-classic-configure-availability.md) e ativá-los manualmente ou desativar.

## <a name="considerations"></a>Considerações sobre

Deverá tomar em consideração as seguintes informações antes de configurar o dimensionamento para a sua aplicação:

- Dimensionamento é afetada pela utilização core. Instâncias de função maiores utilizam núcleos mais. É possível dimensionar uma aplicação apenas dentro do limite de núcleos para a sua subscrição. Por exemplo, se a sua subscrição tem um limite de vinte núcleos e executar uma aplicação com duas médio dimensionada cloud services (um total de quatro núcleos), pode apenas dimensionar o outras implementações do serviço de nuvem na sua subscrição por dezasseis núcleos. Consulte o artigo [Tamanhos de serviço de nuvem](cloud-services-sizes-specs.md) para obter mais informações sobre os tamanhos.

- Pode dimensionar com base num determinado limiar de mensagem de fila de espera. Para obter mais informações sobre como utilizar filas, consulte o artigo [como utilizar o serviço de armazenamento de fila de espera](../storage/storage-dotnet-how-to-use-queues.md).

- Também pode dimensionar outros recursos associados com a sua subscrição.

- Para ativar elevada disponibilidade da sua aplicação, deverá Certifique-se de que é implementada com duas ou mais ocorrências de função. Para mais informações, consulte o artigo [Acordos do nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="where-scale-is-located"></a>Onde está localizada escala

Depois de selecionar o seu serviço de nuvem, deverá ter o pá de serviço de nuvem visível.

1. No pá de serviço na nuvem, no mosaico **funções e instâncias** , selecione o nome do serviço em nuvem.   
**Importante**: Certifique-se que clique na função de serviço de nuvem, não a instância de função que está abaixo da função.

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)

2. Selecione o mosaico de **escala** .

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Escala automática

Pode configurar as definições de escala para uma função com dois modos **manual** ou **Automático**. Manual é como esperado, definir a contagem de referências absoluta de instâncias. Automático no entanto permite-lhe definir regras que governem como e por muito deve dimensionar.

Defina a opção de **escala por** para **regras de agenda e o desempenho**.

![Definições de escala dos serviços na nuvem com o perfil e regra](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Um perfil existente.
2. Adicione uma regra para o perfil principal.
3. Adicione outro perfil.

Selecione **Adicionar perfil**. O perfil determina o modo em que pretende utilizar para a escala: **sempre**, **Periodicidade**, **data fixa**.

Depois de ter configurado o perfil e regras, selecione o ícone **Guardar** na parte superior.

#### <a name="profile"></a>Perfil

O perfil de conjuntos de instâncias de mínimas e máximas para a escala, e também quando este intervalo de escala está ativo.

* **Sempre**

    Manter sempre este intervalo de instâncias do disponíveis.  

    ![Serviço de nuvem sempre Dimensionar](./media/cloud-services-how-to-scale-portal/select-always.png)
    
* **Periodicidade**

    Selecione um conjunto de dias da semana para dimensionar.

    ![Escala de serviço de nuvem com uma agenda de periodicidade](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
    
* **Data fixa**

    Um intervalo de datas fixo para dimensionar a função.

    ![Escala de serviço de nuvem com uma data fixa](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Depois de ter configurado o perfil, selecione o botão **OK** na parte inferior da pá perfil.

#### <a name="rule"></a>Regra

As regras são adicionadas a um perfil e representam uma condição que irá acionar a escala. 

Acionar a regra é baseado uma métrica de serviço em nuvem (utilização da CPU, actividade do disco ou actividade de rede) para o qual pode adicionar um valor condicional. Para além disso pode ter o accionador com base na fila de mensagens ou uma métrica de alguns recursos Azure associada à sua subscrição.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Depois de ter configurado a regra, selecione o botão **OK** na parte inferior da pá a regra.

## <a name="back-to-manual-scale"></a>Voltar a escala manual

Navegue para as [definições de escala](#where-scale-is-located) e defina a opção de **escala por** **uma contagem de instância posso introduzir manualmente**.

![Definições de escala dos serviços na nuvem com o perfil e regra](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Esta ação remove automatizado dimensionamento da função e, em seguida, pode definir a contagem de instância diretamente. 

1. A opção de (manual ou automatizado) de escala.
2. Um função instância controlo de deslize para definir as instâncias para dimensionar para.
3. Instâncias da função para dimensionar para.

Depois de ter configurado as definições de escala, selecione o ícone **Guardar** na parte superior.

