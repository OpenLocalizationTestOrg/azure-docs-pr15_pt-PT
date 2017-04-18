<properties
    pageTitle="Automática dimensionar um serviço na nuvem no portal do | Microsoft Azure"
    description="(clássico) Saiba como utilizar o portal clássico configurar regras de escala de automática para uma função de web do serviço na nuvem ou trabalhador no Azure."
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

Na página do portal clássica Azure escala, manualmente pode dimensionar a sua função web ou trabalhador ou pode ativar a escala automática com base no carga da CPU ou uma fila de mensagens.

>[AZURE.NOTE] Este artigo foca-se em funções de web e trabalhador serviço na nuvem. Quando cria uma máquina de virtual (clássico) diretamente, está alojado num serviço de nuvem. Algumas destas informações aplica-se para estes tipos de máquinas virtuais. Dimensionamento um conjunto de disponibilidade de máquinas virtuais é realmente apenas encerrá-los e desativar com base em regras de escala que configura. Para mais informações sobre máquinas virtuais e conjuntos de disponibilidade, consulte o artigo [Gerir a disponibilidade de máquinas virtuais](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)

Deverá tomar em consideração as seguintes informações antes de configurar o dimensionamento para a sua aplicação:

- Dimensionamento é afetada pela utilização core. Maiores instâncias de função utilizam núcleos mais. É possível dimensionar uma aplicação apenas dentro do limite de núcleos para a sua subscrição. Por exemplo, se a sua subscrição tem um limite de vinte núcleos e executar uma aplicação com duas médio dimensionada cloud services (um total de quatro núcleos), pode apenas dimensionar o outras implementações do serviço de nuvem na sua subscrição por dezasseis núcleos. Consulte o artigo [Tamanhos de serviço de nuvem](cloud-services-sizes-specs.md) para obter mais informações sobre os tamanhos.

- Tem de criar uma fila e associá-lo a uma função antes de pode dimensionar uma aplicação baseada num limite de mensagem. Para mais informações, consulte o artigo [como utilizar o serviço de armazenamento de fila de espera](../storage/storage-dotnet-how-to-use-queues.md).

- É possível dimensionar recursos que estão ligados no seu serviço de nuvem. Para obter mais informações acerca da ligação de recursos, consulte o artigo [como: ligar um recurso num serviço na nuvem](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- Para ativar elevada disponibilidade da sua aplicação, deverá Certifique-se de que é implementada com duas ou mais ocorrências de função. Para mais informações, consulte o artigo [Acordos do nível de serviço](https://azure.microsoft.com/support/legal/sla/).



## <a name="schedule-scaling"></a>Dimensionamento da agenda

Por predefinição, todas as funções não siga uma agenda específica. Por conseguinte, quaisquer definições alteradas aplicam a todas as horas e todos os dias ao longo do ano. Se pretender, pode configuração manual ou automática dimensionamento para:

- Dias úteis.
- Fins de semana
- Noites semana
- Manhãs semana
- Datas específicas
- Intervalos de datas específico

Este é conigured no [portal clássica Azure](https://manage.windowsazure.com/) na  
**Serviços em nuvem** > **\[seu serviço de nuvem\]** > **escala** > **\[produção ou transição\] ** página.

Clique no botão **Configurar agendar horas** para cada função que pretende alterar.

![Escala automática com base numa agenda de serviço em nuvem][scale_schedules]



## <a name="manual-scale"></a>Escala Manual

Na página de **escala** , pode aumentar ou diminuir o número de instâncias em execução num serviço de nuvem manualmente. Isto é configurado para cada agenda que criou ou para todo o tempo se não tiver criado uma agenda.

1. No [portal clássica Azure](https://manage.windowsazure.com/), clique em **Serviços em nuvem**e, em seguida, clique no nome do serviço em nuvem para abrir o dashboard.

    > [AZURE.TIP] Se não vir o seu serviço de nuvem, poderá ter de alterar a partir de **produção** para **transição** ou vice versa.

2. Clique em **escala**.

3. Selecione a agenda que pretende alterar as opções de dimensionamento para. Predefina *não horas agendadas* se tiver sem agendas definidas.

4. Localize a secção de **escala pela métrica** e selecione **NONE**. Esta é a predefinição para todas as funções.

5. Cada função no serviço de nuvem tem um controlo de deslize para alterar o número de instâncias para utilizar.

    ![Dimensionar manualmente uma função de serviço de nuvem][manual_scale]

    Se precisar de mais instâncias, poderá ter de alterar o [tamanho da máquina virtual do serviço na nuvem](cloud-services-sizes-specs.md).

6. Clique em **Guardar**.  
Instâncias de função vai ser adicionadas ou removidas com base nas suas seleções.

>[AZURE.TIP] Sempre que vir ![][tip_icon] mova o rato para a mesma e pode obter ajuda sobre como é que uma definição específica.


## <a name="automatic-scale---cpu"></a>Escala automática - CPU

Isto pode ser dimensionado se a percentagem média de utilização da CPU vai acima ou abaixo limiares especificados; instâncias de função são criadas ou eliminadas.

1. No [portal clássica Azure](https://manage.windowsazure.com/), clique em **Serviços em nuvem**e, em seguida, clique no nome do serviço em nuvem para abrir o dashboard.

    > [AZURE.TIP] Se não vir o seu serviço de nuvem, poderá ter de alterar a partir de **produção** para **transição** ou vice versa.

2. Clique em **escala**.

3. Selecione a agenda que pretende alterar as opções de dimensionamento para. Predefina *não horas agendadas* se tiver sem agendas definidas.

4. Localize a secção de **escala pela métrica** e selecione **CPU**.

5. Agora pode configurar um intervalo de mínimo e máximo de instâncias de funções, o destino de utilização da CPU (acionar uma escala de cores para cima) e quantas instâncias para dimensionar para cima e para baixo por.

![Dimensionar uma função de serviço de nuvem pela carga de cpu][cpu_scale]

>[AZURE.TIP] Sempre que vir ![][tip_icon] mova o rato para a mesma e pode obter ajuda sobre como é que uma definição específica.





## <a name="automatic-scale---queue"></a>Escala automática - fila de espera

Se o número de mensagens de uma fila vai acima ou abaixo de um limite especificado; isto escalas automaticamente instâncias de função são criadas ou eliminadas.

1. No [portal clássica Azure](https://manage.windowsazure.com/), clique em **Serviços em nuvem**e, em seguida, clique no nome do serviço em nuvem para abrir o dashboard.

    > [AZURE.TIP] Se não vir o seu serviço de nuvem, poderá ter de alterar a partir de **produção** para **transição** ou vice versa.

2. Clique em **escala**.

3. Localize a secção de **escala pela métrica** e selecione **CPU**.

4. Agora pode configurar um intervalo de mínimo e máximo de instâncias de funções, a fila de espera e o montante das mensagens de fila de espera para processar para cada instância e quantas instâncias para dimensionar para cima e para baixo por.

![Dimensionar uma função de serviço de nuvem por uma fila de mensagens][queue_scale]

>[AZURE.TIP] Sempre que vir ![][tip_icon] mova o rato para a mesma e pode obter ajuda sobre como é que uma definição específica.


## <a name="scale-linked-resources"></a>Dimensionar recursos ligados

Muitas vezes, quando dimensionar uma função, é vantajoso dimensionar a base de dados que a aplicação estiver a utilizar também. Se ligar à base de dados para o serviço de nuvem, pode aceder as definições de dimensionamento para esse recurso ao clicar na ligação adequada.

1. No [portal clássica Azure](https://manage.windowsazure.com/), clique em **Serviços em nuvem**e, em seguida, clique no nome do serviço em nuvem para abrir o dashboard.

    > [AZURE.TIP] Se não vir o seu serviço de nuvem, poderá ter de alterar a partir de **produção** para **transição** ou vice versa.

2. Clique em **escala**.

3. Localize a secção de **recursos ligados** e clicou na **escala de gerir para esta base de dados**.

    > [AZURE.NOTE] Se não vir uma secção de **recursos ligados** , provavelmente não possui quaisquer recursos ligados.

![][linked_resource]


[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
