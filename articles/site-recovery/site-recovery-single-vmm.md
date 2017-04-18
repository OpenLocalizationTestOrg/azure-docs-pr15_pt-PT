
<properties
    pageTitle="Recuperação de Site Azure: Máquinas virtuais de Hyper-V repetições num único servidor VMM | Microsoft Azure"
    description="Este artigo descreve como criar uma réplica máquinas virtuais de Hyper-V quando tiver apenas um único servidor VMM."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="08/24/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-on-a-single-vmm-server"></a>Criar uma réplica máquinas virtuais de Hyper-V num único servidor VMM

Leia este artigo para saber como criar uma réplica máquinas virtuais de Hyper-V localizadas num servidor de anfitrião Hyper-V numa nuvem VMM quando tiver apenas um único servidor VMM na sua implementação.

Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Gestor de recursos do Azure e clássica. Azure também tem dois portais – o portal clássico Azure que suporta o modelo clássico de implementação e portal do Azure com o suporte para ambos os modelos de implementação. Este artigo contém as instruções de configuração de replicação no portal do Azure.


Se tiver dúvidas depois de ler este artigo, publique-os nos comentários Disqus na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Descrição geral

Se pretende criar uma réplica VMs Hyper-V localizado em anfitriões de Hyper-V no VMM e apenas tiver um único servidor VMM, pode [criar uma réplica para Azure](site-recovery-vmm-to-azure.md), ou entre nuvens no servidor VMM único.

Recomendamos que pode criar uma réplica para Azure porque activação e recuperação não estão totalmente integradas quando replicação entre nuvens e um número de passos manuais é necessários. Se pretender criar uma réplica utilizando apenas o servidor de VMM, pode fazer o seguinte:

- Criar uma réplica com um servidor VMM única autónomo
- Criar uma réplica com um único servidor VMM implementado num cluster esticada do Windows


## <a name="replicate-across-sites-with-a-single-standalone-vmm-server"></a>Criar uma réplica através de sites com um servidor VMM única autónomo

![Servidor VMM virtual autónomo](./media/site-recovery-single-vmm/single-vmm-standalone.png)

Neste cenário implementar o servidor de VMM single como uma máquina virtual no site principal e criar uma réplica este VM a um site secundário utilizando recuperação de sites e réplica Hyper-V.

1. Configure o VMM numa VM Hyper-V. Sugerimos que instalar instância do SQL Server utilizada pelo VMM sobre a mesma VM para poupar tempo. Se pretender utilizar uma instância do SQL Server e uma falha de remota ocorre, é necessário recuperar essa instância antes de pode recuperar VMM.
2. Certifique-se de que o servidor VMM tem, pelo menos, duas nuvens configurados. Uma nuvem contém os VMs que pretende criar uma réplica e outros nuvem serve a localização secundária. Na nuvem que contém os VMs que pretende proteger deve ter:

    - Um ou mais VMM anfitrião grupos que contém uma ou mais servidores de anfitrião de Hyper-V em cada grupo de anfitrião.
    - Pelo menos uma máquina de virtual Hyper-V num servidor de anfitrião cada Hyper-V.

3. Criar um cofre de serviços de recuperação, gerar e transferir uma chave de registo do cofre e registar o servidor VMM no cofre. Durante o registo que instalar o fornecedor de recuperação de Site do Azure no servidor VMM.
4. Configurar um ou mais nuvens na VM VMM e adicione os anfitriões Hyper-V para estes nuvens.
3. Configure definições de proteção para as nuvens. Especifique o nome do servidor VMM única como as localizações de origem e destino. Para configurar o mapeamento de rede, mapeie a rede VM para o cloud com VMs que pretende proteger à rede VM para a nuvem de replicação.
4. Ative a replicação inicial para VMs que pretende proteger através da rede porque ambas as nuvens estão localizados no mesmo servidor.
4. Na consola do Gestor de Hyper-V, ativar Hyper-V réplica no anfitrião do Hyper-V que contém a VM VMM e activar a replicação na VM. Certifique-se de que não adiciona a VM VMM para quaisquer nuvens que estejam protegidos por recuperação de sites. Este procedimento assegura que as definições de réplica Hyper-V não são substituídas por recuperação de sites.
5. Se que pretende criar planos de recuperação, especifique o mesmo servidor VMM para origem e destino.

Siga as instruções [neste artigo](site-recovery-vmm-to-vmm.md) para criar um cofre, registar o servidor e configurar o proteção.

### <a name="what-to-do-in-an-outage"></a>O que fazer: numa falha de

Se ocorre uma falha de concluída e precisa trabalhar com a partir do site secundário, faça o seguinte:

1.  Na consola do Gestor de Hyper-V no site secundário, execute uma activação pós-falha não planeado Falha ao longo da VM VMM a partir da página principal para o site secundário.
2.  Verifique se a VM VMM está a trabalhar no site secundário.
3.  No cofre serviços de recuperação, execute uma activação pós-falha não planeado falha sobre a carga de trabalho VMs a partir do principal para nuvens secundários. Para concluir a activação não planeado pós-falha dos VMs, consolidar a activação pós-falha ou selecione um ponto de recuperação diferentes conforme necessário.
4.  Depois da activação não planeado pós-falha estiver concluída, os utilizadores podem aceder a recursos de carga de trabalho no site secundário.

Quando o site principal está a funcionar normalmente novamente, faça o seguinte:

1.  Na consola do Gestor de Hyper-V, Active a replicação inversa para VM VMM, para começar a replicação-lo a partir do secundário para principal.
2.  Na consola do Gestor de Hyper-V, execute uma planeada activação pós-falha a falha voltar a VM VMM para o site principal. Consolide a activação pós-falha para executá-la. Em seguida, ativar replicação inversa iniciar a replicação do VMM a partir do principal secundário.
3.  No cofre serviços de recuperação, ativar replicação inversa para a carga de trabalho VMs, para começar a replicação-los a partir do secundário para principal.
4.  O Cofre de serviços de recuperação de ser executado activação pós-falha planeada falha voltar a carga de trabalho VMs para o site principal. Consolide a activação pós-falha para executá-la. Em seguida, ativar replicação inversa iniciar a replicação de carga de trabalho VMs a partir do principal secundário.



## <a name="replicate-across-sites-with-a-single-vmm-server-in-a-stretched-cluster"></a>Criar uma réplica através de sites com um único servidor VMM num cluster esticada

![Servidor VMM virtual agrupada](./media/site-recovery-single-vmm/single-vmm-cluster.png)

Em vez de implementar um servidor VMM autónomo como uma VM que replica para um site secundário, pode disponibilizar VMM altamente ao implementá-la como uma VM num cluster de activação pós-falha do Windows. Este procedimento fornece recuperação de carga de trabalho e proteção contra falha de hardware. Para implementar com a recuperação de Site a VM VMM deve ser implementada num cluster esticar através de sites centrados em separado. Para fazer isto:

1. Instalar o VMM num computador virtual num cluster de activação pós-falha do Windows e selecione a opção para executar o servidor como altamente disponíveis durante a configuração.
2. A instância do SQL Server que é utilizada pelo VMM deve ser replicada com grupos de Disponibilidade AlwaysOn do SQL Server para que não existe uma réplica da base de dados no site secundário.
3. Siga as instruções [neste artigo](site-recovery-vmm-to-vmm.md) para criar um cofre, registar o servidor e configurar o proteção. Tem de registar cada servidor VMM no cluster no cofre serviços de recuperação. Para fazer isto, instale o fornecedor de num nó ativo e registar o servidor VMM. Em seguida, instale o fornecedor em outros nós.

### <a name="what-to-do-in-an-outage"></a>O que fazer: numa falha de

Quando ocorre uma falha de, o servidor VMM e a sua base de dados do SQL Server correspondente estão Ocorreu uma falha ao longo do e acedidos a partir do site secundário.


## <a name="next-steps"></a>Próximos passos

[Saiba mais](site-recovery-vmm-to-vmm.md) sobre detalhada implementação de recuperação de sites para VMM para a replicação VMM.
