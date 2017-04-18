<properties
    pageTitle="Criar uma réplica máquinas virtuais de VMware e servidores físicos para Azure com Azure Site recuperação (Legado) | Microsoft Azure" 
    description="Descreve como criar uma réplica VMs no local e recuperação do Azure de sites a utilizar o Windows/Linux servidores físicos para Azure numa implementação legada no portal do clássico." 
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Criar uma réplica máquinas virtuais de VMware e servidores físicos para Azure com a recuperação de Site Azure utilizando o portal clássico (Legado)

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmware-to-azure.md)
- [Portal clássico](site-recovery-vmware-to-azure-classic.md)
- [Portal clássico (Legado)](site-recovery-vmware-to-azure-classic-legacy.md)


Bem-vindo ao Azure Site recuperação! Este artigo descreve uma implementação legada para a replicação de máquinas de virtuais VMware no local ou servidores físicos Windows/Linux Azure utilizando a recuperação de Site do Azure no portal do clássico.

## <a name="overview"></a>Descrição geral

As organizações devem uma estratégia BCDR que determina como dados, das cargas de trabalho e apps manter-se em execução e disponível durante o tempo de inatividade planeado e não em recuperar a normal condições de trabalho mais cedo possível. Sua estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis e garantir que das cargas de trabalho permanecem continuamente disponíveis quando ocorre falhas.

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de BCDR por orquestrar replicação de servidores físicos no local e máquinas virtuais na nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem falhas na sua localização principal, falhar ao longo para a localização secundária para manter as aplicações e das cargas de trabalho disponíveis. Falhar voltar a sua localização principal quando devolve a operações normais. Saiba mais em [o que é o Azure recuperação de Site?](site-recovery-overview.md)


>[AZURE.WARNING] Este artigo contém **instruções legadas**. Não utilize para implementações novas. Em alternativa, [siga estas instruções](site-recovery-vmware-to-azure.md) para implementar recuperação de sites no portal do Azure ou [utilizar estas instruções](site-recovery-vmware-to-azure-classic.md) para configurar a implementação melhorada no portal do clássico. Se já tiver implementado utilizando o método descrito neste artigo, recomendamos que migrar para a implementação melhorada no portal do clássico.


## <a name="migrate-to-the-enhanced-deployment"></a>Migrar para a implementação melhorada

Esta secção só é relevante caso já tenha implementado recuperação de sites utilizando as instruções neste artigo.

Para migrar a sua implementação existente, que vai precisar de:

1. Implemente novos componentes de recuperação de Site no seu site no local.
2. Configure as credenciais para deteção automática de VMware VMs no servidor de configuração de novo.
3. Descubra os servidores de VMware com o novo servidor de configuração.
3. Crie um novo grupo de proteção com o novo servidor de configuração.


Antes de começar:

- Recomendamos que configurados numa janela de manutenção para a migração.
- A opção de **Migrar máquinas** só está disponível se tiver grupos proteção existente que foram criados durante uma implementação legada.
- Depois de concluir os passos de migração, pode demorar 15 minutos ou mais longa para atualizar as credenciais para descobrir e atualizar máquinas virtuais para que pode adicioná-los a um grupo de proteção. Pode atualizar manualmente em vez de em espera. 

Migrar da seguinte forma:

1. Leia sobre [implementação melhorada no portal do clássico](site-recovery-vmware-to-azure-classic.md#enhanced-deployment). Reveja as melhorado [arquitetura](site-recovery-vmware-to-azure-classic.md#scenario-architecture)e [Pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).
2. Desinstale o serviço de mobilidade de máquinas que está a replicação atualmente. Uma nova versão do serviço será instalada nos computadores quando adicioná-los para o novo grupo de proteção.
3. Transferir uma [chave de registo do cofre](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key) e, em seguida, [execute o Assistente de configuração de unified](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server) para instalar o servidor de configuração, servidor de processo e componentes do servidor de destino principal. Leia mais sobre o [Planeamento da capacidade](site-recovery-vmware-to-azure-classic.md#capacity-planning).
4. [Defina credenciais de](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server) recuperação esse Site pode utilizar para aceder ao servidor de VMware para detetar automaticamente VMware VMs. Saiba mais sobre [as permissões necessárias](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
5. Adicione [os servidores de vCenter ou vSphere anfitriões](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts). Pode demorar 15 minutos para obter mais informações para servidores que seja apresentado no portal do recuperação de sites.
6. Crie um [novo grupo de proteção](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Pode demorar até 15 minutos para o portal de atualizar para que as máquinas virtuais sejam detectadas e apareçam. Se não pretender esperar pode realçar o nome do servidor de gestão (não clique) > **Atualizar**.
7. Na proteção de nova grupo, clique em **Migrar máquinas**.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. Em **Selecione máquinas** selecione o grupo de proteção que pretende migrar a partir de e máquinas que pretende migrar.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. Nas **Definições de destino configurar** Especifique se pretende utilizar as mesmas definições para todos os computadores e selecione o servidor de processo e a conta de armazenamento Azure. Se não tiver um servidor de processo separado será assim o o endereço IP do servidor de configuração.


    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] [Migração de contas de armazenamento de](../resource-group-move-resources.md) grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportada para contas de armazenamento utilizadas para implementar o recuperação de sites.

10. **Especificar contas**, selecione a conta que criou para o servidor de processo aceder a máquina para transmitir a nova versão do serviço de mobilidade.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. Recuperação de site irá migrar os seus dados replicados para a conta de armazenamento Azure que forneceu. Opcionalmente, pode reutilizar a conta de armazenamento utilizado na implementação legada.
12. Após termina a tarefa de máquinas virtuais irá sincronizar automaticamente. Após a conclusão da sincronização pode eliminar as máquinas virtuais do grupo de legado proteção.
13. Depois de todos os computadores terem migrados pode eliminar o grupo de legado proteção.
14. Lembre-se especificar as propriedades de activação pós-falha para máquinas e as definições de rede Azure após a sincronização está concluída.
15. Se tiver os planos de recuperação existente, pode migrá-las para a implementação melhorada com a opção **Migrar planear de recuperação** . Apenas deve fazer isto depois de terem sido migrados todos os computadores protegidos. 

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] Depois de concluir a migração continuar com o [artigo melhorado](site-recovery-vmware-to-azure-classic.md). Os restantes deste artigo legado deixará de ser relevantes e não é necessário seguir os passos descritos na it * * qualquer mais.




## <a name="what-do-i-need"></a>O que preciso?

Este diagrama mostra os componentes de implementação.

![Novo cofre](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Eis o que precisa:

**Componente** | **Implementação** | **Detalhes**
--- | --- | ---
**Servidor de configuração** | Uma Azure padrão A3 máquina virtual na mesma subscrição como recuperação de sites. | O servidor de configuração coordenadas comunicação entre máquinas protegidas, o servidor de processo e os servidores de destino principal no Azure. Define o replicação e recuperação de coordenadas no Azure quando ocorre activação pós-falha.
**Servidor de destino principal** | Uma máquina virtual Azure — um servidor de Windows baseado no Windows Server 2012 R2 uma imagem da galeria (para proteger máquinas com o Windows) ou como um servidor de Linux com base numa imagem da Galeria OpenLogic CentOS 6.6 (para proteger Linux máquinas).<br/><br/> Opções de redimensionamento três estão disponíveis – A4 padrão, D14 padrão e DS4 padrão.<br/><br/> O servidor está ligado à mesma rede Azure como o servidor de configuração.<br/><br/> Configurar o no portal do recuperação de sites | -Recebe e retém dados replicados a partir do seu máquinas protegidas utilizando VHDs anexados criados no armazenamento de BLOBs na sua conta de armazenamento Azure.<br/><br/> Selecione DS4 padrão especificamente para configurar a protecção contra das cargas de trabalho que requerem consistente alto desempenho e latência baixa utilizando a conta de armazenamento Premium.
**Servidor de processo** | Um servidor no local virtual ou físico com o Windows Server 2012 R2<br/><br/> Recomendamos que estiver colocada na mesma rede e segmento LAN como as máquinas que quer proteger, mas pode executar uma rede diferente desde máquinas protegidas tenham L3 visibilidade de rede à mesma.<br/><br/> Configure-o e registe-se-lo para o servidor de configuração no portal do recuperação de sites. | Máquinas protegidas enviam dados de replicação para o servidor de processo no local. Tem de dados de replicação de cache para cache baseadas no disco que recebe. Executa um número de ações nesses dados.<br/><br/> -Optimiza dados através da colocação em cache, comprimir e encriptá-la antes de o enviar para o servidor de destino principal.<br/><br/> Alças de instalação de push do serviço de mobilidade.<br/><br/> Executa deteção automática de máquinas virtuais de VMware.
**Máquinas no local** | No local máquinas virtuais de VMware ou físicos servidores a executar o Windows ou Linux. | Configurar definições de replicação que se aplicam um ou mais máquinas. Pode falhar ao longo de uma máquina individual ou mais frequentemente, vários computadores recolher em conjunto para um plano de recuperação. 
**Serviço de mobilidade** | Instalado no cada máquina virtual ou servidor físico que pretende proteger<br/><br/> Pode ser instalado manualmente ou seguia e instaladas automaticamente pelo servidor de processo quando ativa a replicação para uma máquina. | O serviço de mobilidade envia dados para o servidor de processo durante a replicação inicial (sincronize novamente). Depois do computador está em estado protegido (depois de termina sincronize novamente) o serviço de mobilidade captura escritas disco na memória e envia-los para o servidor do processo. Applicationconsistency para servidores do Windows é obtida a utilizar o VSS.
**Azure Cofre de recuperação de sites** | Criar um cofre de recuperação de Site com uma subscrição do Azure e registar servidores no cofre. | O Cofre coordenadas e orquestra replicação de dados, activação pós-falha e recuperação entre o seu site no local e o Azure.
**Mecanismo de replicação** | **Através da Internet**— dados de comunica e repetições de protegida servidores no local para Azure utilizar SSL/TLS seguro do canal através da internet. Esta é a opção de predefinido.<br/><br/> **VPN/ExpressRoute**— comunica e repetições dados entre servidores no local e o Azure através de uma ligação VPN. Terá de configurar um site para o site VPN ou uma ligação de ExpressRoute entre o seu site no local e o Azure rede.<br/><br/> Irá Selecione como pretende criar uma réplica durante a implementação de recuperação de sites. Não pode alterar o mecanismo depois é configurado sem que afetam a replicação de máquinas existentes. | Nenhuma opção requer que abrir quaisquer portas de rede de entrada no máquinas protegidas. Todas as comunicações de rede são iniciada a partir do site no local. 

## <a name="capacity-planning"></a>Planeamento de capacidades

As áreas principais que terá da ter em conta:

- **Ambiente de origem**— VMware a infraestrutura, definições de máquina de origem e requisitos.
- **Servidores de componentes**— o processo server, servidor de configuração e destino mestra server 

### <a name="considerations-for-the-source-environment"></a>Considerações para o ambiente de origem

- **Tamanho máximo do disco**— o tamanho máximo atual do disco que pode ser anexado a uma máquina virtual é 1 TB. Assim, o tamanho máximo de um disco de origem que pode ser replicado também está limitado a 1 TB.
- **Tamanho máximo por origem**— o tamanho máximo de uma máquina de única origem é 31 TB (com 31 discos) e com uma instância de D14 aprovisionada para o servidor de destino principal. 
- **Número de origens por servidor de destino mestra**— vários computadores de origem podem ser protegidos com um servidor de destino mestra único. No entanto, uma máquina de única origem não é possível protegida por vários servidores de destino principal, porque como criar uma réplica da discos, um VHD que reflete o tamanho do disco é criado no armazenamento de Blobs do Azure e anexada como um disco de dados para o servidor alvo principal.  
- **Taxa por origem de alteração de máxima diariamente**— existirem três fatores que necessitam de ser considerados quando considerar a taxa de alteração recomendada por origem. Considerações de destino com base duas IOPS são necessários no disco de destino para cada operação na origem. Isto acontece porque de leitura de dados antigos e de escrita dos novos dados irão acontecer no disco de destino. 
    - **Diária alterar taxa suportada pelo servidor processo**— uma máquina de origem não pode abranger vários servidores do processo. Um servidor de um único processo pode suportar até 1 TB de alterar a velocidade de diárias. Por conseguinte, 1 TB é que os dados diários máximos alterar taxa suportada para uma máquina de origem. 
    - **Débito máximo suportados pelo disco de destino**— vasilha máximo por disco de origem não pode ser mais do que 144 GB/dia (com o tamanho de escrita de 8 K). Consulte a tabela na secção de destino principal para a débito e IOPs do alvo para vários tamanhos de escrita. Este número deve ser dividido por dois uma vez que cada origem IOP gera 2 IOPS no disco de destino. Leia sobre [Azure destinos escalabilidade e o desempenho](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) ao configurar o destino para contas de armazenamento premium.
    - **Débito máximo suportado pela conta de armazenamento**— uma origem não é possível do intervalo de várias contas de armazenamento. Dado que uma conta de armazenamento leva-o até um máximo de 20.000 pedidos por segundo e que cada origem IOP gera 2 IOPS no servidor de destino principal, recomendamos que mantenha o número de IOPS ao longo da origem para 10.000. Leia sobre [Azure destinos escalabilidade e o desempenho](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) ao configurar a origem para contas de armazenamento premium.

### <a name="considerations-for-component-servers"></a>Considerações para servidores de componentes

Tabela 1 resume os tamanhos de máquina virtual para a configuração e os servidores de destino principal.

**Componente** | **Instâncias Azure implementadas** | **Núcleos** | **Memória** | **Discos máximo** | **Tamanho do disco**
--- | --- | --- | --- | --- | ---
Servidor de configuração | A3 padrão | 4 | 7 GB | 8 | 1023 GB
Servidor de destino principal | A4 padrão | 8 | 14 GB | 16 | 1023 GB
 | D14 padrão | 16 | 112 GB | 32 | 1023 GB
 | DS4 padrão | 8 | 28 GB | 16 | 1023 GB

**Tabela 1**

#### <a name="process-server-considerations"></a>Considerações sobre o processo de servidor

Geralmente redimensionamento de servidor do processo depende a taxa de alteração diária através de todas as cargas de trabalho protegidas.


- Precisa de cluster suficiente para efetuar tarefas, como inline compressão e encriptação.
- O servidor de processo utiliza cache baseada no disco. Certifique-se o espaço de recomendado para a cache e débito do disco está disponível para facilitar as alterações aos dados armazenadas trabalho congestionamento de rede ou falha do. 
- Certifique-se largura de banda suficiente para que o servidor de processo pode carregar os dados para o servidor de destino principal para fornecer protecção contínua dos dados. 

Tabela 2 fornece um resumo das diretrizes de servidor do processo.

**Taxa de alteração de dados** | **CPU** | **Memória** | **Tamanho da cache do disco**| **Débito do disco cache** | **Largura de banda penetração/saída**
--- | --- | --- | --- | --- | ---
< 300 GB | 4 vCPUs (2 sockets * 2 núcleos @ 2,5 GHz) | 4 GB | 600 GB | 7 a 10 MB por segundo | 30 Mbps/21 Mbps
GB de 300 a 600 | 8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz) | 6 GB | 600 GB | 11 a 15 MB por segundo | 60 Mbps/42 Mbps
600 GB para 1 TB | 12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) | 8 GB | 600 GB | 16 para 20 MB por segundo | 100 Mbps/70 Mbps
> 1 TB | Implementar outro servidor de processo | | | | 

**Tabela 2**

Em que: 

- Penetração é a largura de banda de transferência (intranet entre o servidor de origem e de processo).
- Saída é carregamento largura de banda (internet entre o servidor de processo e servidor de destino principal). Números de saída presumir a compressão de servidor do processo de 30% média.
- Para a cache de disco um disco SO separado de GB 128 mínima é recomendado para todos os servidores de processo.
- Para débito do disco cache o armazenamento seguinte foi utilizado para direccionamento de caminhos: 8 unidades SA de 10 K rotações/minuto com a configuração de RAID 10.

#### <a name="configuration-server-considerations"></a>Considerações de configuração do servidor

Servidor de configuração de cada pode suportar até 100 máquinas de origem com volumes de 3-4. Se a sua implementação for maior Recomendamos que implementar o servidor de configuração da outra. Consulte a tabela 1 para as propriedades de máquina virtual predefinidas do servidor de configuração. 

#### <a name="master-target-server-and-storage-account-considerations"></a>Considerações de conta do servidor e armazenamento destino principal

O armazenamento de cada servidor mestra destino inclui um disco SO, um volume de retenção e de discos de dados. A unidade de retenção mantém o diário das alterações do disco para a duração da janela de retenção definida no portal do recuperação de sites.  Consulte a tabela 1 para as propriedades de máquina virtual do servidor de destino principal. Tabela 3 mostra como é utilizado no disco dos A4.

**Instância** | **Disco SO** | **Retenção** | **Discos de dados**
--- | --- | --- | ---
 | | **Retenção** | **Discos de dados**
A4 padrão | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 discos (15 * 1023 GB)
D14 padrão |  1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 31 discos (15 * 1023 GB)
DS4 padrão |  1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 discos (15 * 1023 GB)

**Tabela 3**

Capacidade de planeamento para o servidor de modelo global de destino depende da:

- Desempenho do armazenamento Azure e limitações
    - O número máximo de altamente utilizada discos uma VM camada padrão, mas está prestes a 40 (500/20.000 IOPS por disco) numa conta armazenamento única. Leia sobre [destinos escalabilidade para sccounts armazenamento padrão](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) e para [premium armazenamento sccounts](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-   Alterar a velocidade de diária 
-   Armazenamento de volume de retenção.

Tenha em atenção que:

- Uma origem não é possível abranger várias contas de armazenamento. Aplica-se para o disco de dados que aceda a contas de armazenamento seleccionadas quando configurar a protecção. O sistema operativo e os discos de retenção, normalmente, vá para a conta de armazenamento implementado automaticamente.
- O volume de armazenamento de retenção necessário depende a taxa de alteração diária e o número de dias de retenção. O armazenamento de retenção necessário por servidor de destino mestra = vasilha total de origem por dia * número de dias de retenção. 
- Cada servidor de destino mestra tem apenas uma volume de retenção. O volume de retenção é partilhado entre discos anexados ao servidor de destino principal. Por exemplo:
    - Se existir uma máquina de origem com 5 discos e cada disco gera 120 IOPS (tamanho de 8K) na origem, isto corresponde a função 240 IOPS por disco (2 operações no disco de destino por origem es). 240 IOPS está dentro Azure por limite IOPS disco de 500.
    - No volume de retenção, este torna-se 120 * 5 = 600 IOPS e isto podem tornar-se um colo garrafa. Neste cenário, uma boa estratégia seria adicionar mais discos o volume de retenção e abrangem-lo ao longo, como uma configuração de faixa RAID. Isto irá melhorar o desempenho porque os IOPS estão distribuídos múltiplas unidades. O número de unidades a serem adicionados ao volume de retenção será da seguinte forma:
        - Total IOPS a partir do ambiente de origem / 500
        - Total vasilha por dia a partir de ambiente de origem (não comprimida) / 287 GB. 287 GB é débito máximo suportado por um disco de destino por dia. Esta métrica irá variar consoante o tamanho de escrita com um fator de 8K, uma vez que neste caso 8K é apresentar assumido tamanho de escrita. Por exemplo, se o tamanho de escrita for 4K débito será 287/2. E se o tamanho de escrita é 16K, em seguida, débito serão 287 * 2.
- O número de contas de armazenamento necessárias = origem do total IOPs/10000.


## <a name="before-you-start"></a>Antes de começar

**Componente** | **Requisitos de** | **Detalhes**
--- | --- | --- 
**Conta Azure** | Terá de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Armazenamento Azure** | Terá de uma conta de armazenamento Azure para armazenar dados replicados<br/><br/> A conta deve ser uma [Conta de armazenamento de Premium](../storage/storage-premium-storage.md)ou uma [Conta de armazenamento Geo redundantes padrão](../storage/storage-redundancy.md#geo-redundant-storage) .<br/><br/> Tem na mesma região como o serviço de recuperação de sites do Azure e ser associadas a mesma subscrição. Não suportamos a mudança de contas de armazenamento criado utilizando o [novo portal Azure](../storage/storage-create-storage-account.md) através de grupos de recursos.<br/><br/> Para obter mais informações leia o artigo [Introdução ao Microsoft Azure armazenamento](../storage/storage-introduction.md)
**Azure rede virtual** | Terá de uma rede virtual Azure que serão implementadas o servidor de configuração e o servidor de destino principal. Deve ser na mesma subscrição e região como cofre Azure recuperação de sites. Se pretender criar uma réplica dados através de uma ligação ExpressRoute ou VPN a rede virtual Azure tem de estar ligada à rede no local através de uma ligação de ExpressRoute ou uma VPN do Site para o Site.
**Recursos Azure** | Certifique-se de que tem de recursos do Azure suficientes para implementar todos os componentes. Leia mais em [Limites de subscrição do Azure](../azure-subscription-service-limits.md).
**Azure máquinas virtuais** | Com a [Pré-requisitos Azure](site-recovery-best-practices.md)está em conformidade com deve máquinas virtuais que pretende proteger.<br/><br/> **Contagem de disco**— um máximo de 31 discos pode ser suportado num único servidor protegido<br/><br/> **Tamanhos de discos**— capacidade do disco individuais não deve ser mais do que 1023 GB<br/><br/> **Clusters**— agrupadas servidores não são suportados<br/><br/> **Arranque**— Firmware Interface (Extensible Unificado) / Extensible Firmware Interface(EFI) arranque não é suportada<br/><br/> **Volumes**— Bitlocker volumes encriptados não são suportados<br/><br/> **Nomes de servidor**— os nomes devem conter entre 1 e 63 carateres (letras, números e hífenes). O nome tem de iniciar com uma letra ou número e termina com uma letra ou número. Depois de uma máquina estiver protegida pode modificar o nome do Azure.
**Servidor de configuração** | Máquina de virtual A3 padrão com base numa imagem da Galeria de Azure Site recuperação Windows Server 2012 R2 será criada na sua subscrição para o servidor de configuração. É criado como a primeira instância num novo serviço de nuvem. Se selecionar Internet pública como o tipo de conectividade do servidor de configuração de serviço em nuvem será criado com um endereço IP público reservado.<br/><br/> O caminho de instalação deverá estar no apenas em inglês caracteres.
**Servidor de destino principal** | Máquina virtual Azure, A4, D14 ou padrão DS4.<br/><br/> O caminho de instalação deverá estar no apenas em inglês caracteres. Por exemplo, o caminho deve ser **/usr/local/ASR** para um servidor de destino principais a executar o Linux.
**Servidor de processo** | Pode implementar o servidor do processo na física ou máquina virtual com o Windows Server 2012 R2 com as atualizações mais recentes. Instalar num c: /.<br/><br/> Recomendamos que coloca o servidor na mesma rede e sub-rede como máquinas que pretende proteger.<br/><br/> Instale VMware vSphere clip 5.5.0 no servidor do processo. O componente de clip de vSphere VMware for necessário no servidor processo para descobrir máquinas virtuais geridas por um servidor de vCenter ou máquinas virtuais em execução num anfitrião ESXi.<br/><br/> O caminho de instalação deverá estar no apenas em inglês caracteres.<br/><br/> Sistema de ficheiros reFS não é suportado.
**VMware** | Um servidor de vCenter VMware gerir o seu hypervisors de vSphere VMware. -Deve estar em execução vCenter versão 5.1 ou 5.5 com as atualizações mais recentes.<br/><br/> Um ou mais hypervisors vSphere que contém máquinas virtuais de VMware que pretende proteger. O hipervisor deve ser executado ESX/ESXi versão 5.1 ou 5.5 com as atualizações mais recentes.<br/><br/> Máquinas virtuais de VMware deve ter VMware ferramentas instalada e em execução. 
**Máquinas com o Windows** | Os servidores físicos protegidos ou máquinas virtuais de VMware a executar o Windows tiverem um número de requisitos.<br/><br/> A suportadas sistema operativo de 64 bits: **Windows Server 2012 R2**, **Windows Server 2012**, ou **Windows Server 2008 R2 na SP1, pelo menos,**.<br/><br/> O nome de anfitrião, pontos de montagem, nomes de dispositivo, caminho de sistema do Windows (ex: C:\Windows) devem estar apenas em inglês.<br/><br/> O sistema operativo devem ser instalado na unidade C:\.<br/><br/> Apenas os discos básicos são suportados. Discos dinâmicos não são suportados.<br/><br/> Regras de firewall em máquinas protegidas deverão permiti-los alcançar os servidores de destino do modelo global e de configuração na Azure.p ><p>Terá de fornecer uma conta de administrador (tem de ser um administrador no computador Windows local) para push instalar o serviço de mobilidade nos servidores do Windows. Se a conta fornecida é uma conta de no domínio que não terá de desativar o controlo de acesso remoto do utilizador no computador local. Para fazer isto adicionar a entrada de registo LocalAccountTokenFilterPolicy DWORD com um valor de 1 em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Para adicionar a entrada de registo de um clip abrir cmd ou powershell e introduza **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Saiba mais](https://msdn.microsoft.com/library/aa826699.aspx) sobre o controlo de acesso.<br/><br/> Depois de falha na ligação, se pretender ligar ao Windows máquinas virtuais no Azure com o ambiente de trabalho remoto, certifique-se de que ambiente de trabalho remoto está ativada para o computador no local. Se não estiver a estabelecer ligação ao longo do VPN, as regras de firewall deverão permitir ligações de ambiente de trabalho remoto através da internet.
**Linux máquinas** | Um sistema operativo de 64 bits suportados: **Centos 6.4, 6.5, 6.6**; **6.4 no oracle Enterprise Linux, 6.5 executar o kernel compatível vermelho chapéu ou inviolável Enterprise Kernel lançamento 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Regras de firewall em máquinas protegidas deverão permiti-los alcançar a configuração e os servidores de destino principal no Azure.<br/><br/> ficheiros de /etc/hosts no máquinas protegidas devem conter entradas que mapeiam o nome de anfitrião local para endereços IP associados NIC todos os <br/><br/> Se pretender estabelecer ligação com uma máquina virtual Azure executar Linux após activação pós-falha a utilizar um seguro Shell cliente (ssh), certifique-se de que o serviço de Shell seguro no computador protegido está definido para iniciar automaticamente no arranque do sistema e que as regras de firewall permitem um ssh ligação à mesma.<br/><br/> O nome de anfitrião, pontos de montagem, nomes de dispositivo e caminhos de sistema Linux e nomes de ficheiro (ex/etc /; /usr) devem ser apenas em inglês.<br/><br/> Proteção pode ser ativada para máquinas no local com o armazenamento seguinte:-<br>Sistema de ficheiros: EXT3, ETX4, ReiserFS, XFS<br>Dispositivo i de software mapeamento de pontos (i)<br>Gestor de volume: LVM2<br>Servidores físicos com HP CCISS controlador de armazenamento não são suportados.
**Terceiros** | Alguns componentes de implementação neste cenário dependem de software de terceiros para funcionar corretamente. Para obter uma lista completa ver [avisos de software de terceiros e informações](#third-party)


### <a name="network-connectivity"></a>Conectividade da rede

Tem duas opções quando configurar a conectividade de rede entre o seu site no local e a rede virtual Azure no qual os componentes de infraestrutura (servidor de configuração, servidores alvo principal) são implementados. Terá de decidir a opção de conectividade de rede para utilizar antes de implementar o seu servidor de configuração. Terá de selecionar esta definição no momento da implementação. Não pode ser alterado mais tarde.

**Internet:** Comunicações e a replicação de dados entre os servidores no local (servidor de processo, máquinas protegidas) e os servidores de componentes de infraestrutura Azure (servidor de configuração, servidor de destino principal) acontece através de uma ligação SSL/TLS segura a partir no local para os pontos finais públicos nos servidores de destino de configuração e modelo global. (A única exceção é a ligação entre o servidor de processo e o servidor de destino mestra na porta TCP 9080 que é não encriptado. Controlo trocadas apenas informações relacionadas com o protocolo de replicação para a configuração de replicação são nesta ligação.)

![Diagrama de implementação de internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: comunicação e a replicação de dados entre os servidores no local (servidor de processo, máquinas protegidas) e os servidores de componentes de infraestrutura Azure (servidor de configuração, servidor de destino principal) acontece ao longo de uma ligação VPN entre a sua rede no local e a rede virtual Azure na qual são implementados os servidores de destino principal e o servidor de configuração. Certifique-se de que a rede no local está ligada à rede virtual Azure por uma ligação de ExpressRoute ou uma ligação VPN do site para o site.

![Diagrama de implementação VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## <a name="step-1-create-a-vault"></a>Passo 1: Criar um cofre

1. Inicie sessão no [Portal de gestão](https://portal.azure.com).


2. Expandir a **Serviços de dados** > **Serviços de recuperação** e clique em **Site recuperação cofre**.


3. Clique em **Criar novo** > **criação rápida**.

4. Em **nome**, introduza um nome amigável para identificar o cofre.

5. Na **região**, selecione a região geográfica para o cofre. Para verificar regiões suportadas Consulte geográficos disponibilidade no [Recuperação preços detalhes do Site Azure](https://azure.microsoft.com/pricing/details/site-recovery/)

6. Clique em **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Verifique a barra de estado para confirmar que o Cofre foi criado com êxito. O Cofre irá estar listado como **ativa** na página principal de **Serviços de recuperação** .

## <a name="step-2-deploy-a-configuration-server"></a>Passo 2: Implementar um servidor de configuração

### <a name="configure-server-settings"></a>Configurar definições de servidor

1. Na página de **Serviços de recuperação** , clique no cofre para abrir a página de início rápido. Guia de introdução do também pode ser aberto em qualquer altura utilizando o ícone.

    ![Guia de introdução do ícone](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. Na lista pendente, selecione **entre um site no local com servidores do VMware/física e Azure**.
3. Em **Preparar recursos Target(Azure)** clique em **Implementar o servidor de configuração**.

    ![Implementar o servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. Na **Nova detalhes de configuração de servidor** especifique:

    - Um nome para o servidor de configuração e credenciais para ligar à mesma.
    - No tipo de conectividade de rede lista pendente selecione **Público na Internet** ou **VPN**. Note que não é possível modificar esta definição depois de ser aplicada.
    - Selecione a rede Azure no qual o servidor deve ser localizado. Se estiver a utilizar o efetuar VPN certificar-se a rede Azure está ligada à rede no local conforme esperado. 
    - Especifique o endereço IP interno e sub-rede forem atribuída ao servidor. Tenha em atenção que os endereços IP primeiras quatro nos qualquer sub-rede são reservados para utilização interna de Azure. Utilize qualquer outro endereço IP disponível.
    
    ![Implementar o servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. Quando clica em **OK** uma máquina de virtual A3 padrão com base numa imagem da Galeria de Azure Site recuperação Windows Server 2012 R2 será criada na sua subscrição para o servidor de configuração. É criado como a primeira instância num novo serviço de nuvem. Se tiver selecionado para ligar através da internet o serviço de nuvem é criado com um endereço IP público reservado. Pode monitorizar o progresso no separador **tarefas** .

    ![Monitorizar progresso](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  Se estiver a estabelecer ligação através da internet, depois do servidor de configuração é implementada nota o endereço IP público atribuída à mesma na página **máquinas virtuais** no portal do Azure. Em seguida, nos **pontos finais** separador Nota: a public HTTPS porta mapeados à porta 443 do privado. Irá precisar esta informação mais tarde quando registar os servidores de processo de destino principal e com o servidor de configuração. O servidor de configuração é implementado com estes pontos finais:

    - HTTPS: Uma porta pública é utilizada para coordenar comunicações entre servidores de componentes e Azure através da internet. Privado porta 443 é utilizada para coordenar comunicações entre servidores de componentes e Azure sobre VPN.
    - Personalizada: Uma porta pública é utilizada para a comunicação da ferramenta de reposição de recurso através da internet. Porta privada 9443 é utilizada para a comunicação da ferramenta de reposição de recurso sobre VPN.
    - PowerShell: Porta privada 5986
    - Ambiente de trabalho remoto: porta privada 3389
    
    ![Pontos finais VM](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] Não elimine ou altere o número de porta público ou privado de qualquer os pontos finais criado durante a implementação do servidor de configuração.

O servidor de configuração está implementado num serviço de nuvem Azure criada automaticamente com um endereço IP reservado. O endereço reservado é necessário para assegurar que o endereço IP de serviço de nuvem de servidor de configuração permanece o mesmo através de ser reiniciado das máquinas virtuais (incluindo o servidor de configuração) o serviço de nuvem. O endereço IP público reservado terá de ser manualmente não reservada quando o servidor de configuração é encerrado ou irá permanecer reservada. Existe um limite predefinido de 20 endereços IP públicos reservados por subscrição. [Saiba mais](../virtual-network/virtual-networks-reserved-private-ip.md) sobre os endereços IP reservados. 

### <a name="register-the-configuration-server-in-the-vault"></a>Registe-se o servidor de configuração no Cofre

1. Na página de **Início rápido** , clique em **Recursos de destino preparar** > **Transferir uma chave de registo**. O ficheiro de chave é gerado automaticamente. É válido para 5 dias após é gerado. Copie-o para o servidor de configuração.
2. Em **máquinas virtuais do** selecione o servidor de configuração a partir da lista de máquinas virtuais. Abra o separador **Dashboard** e clique em **Ligar**. **Abrir** o ficheiro transferido do RDP para iniciar sessão no servidor de configuração utilizando o ambiente de trabalho remoto. Se estiver a utilizar VPN, utilize o endereço IP interno (o endereço que especificou quando o servidor de configuração implementado) de uma ligação de ambiente de trabalho remoto do site no local. O Assistente de configuração de servidor de configuração do Azure Site recuperação é executado automaticamente quando inicia sessão pela primeira vez.

    ![Registo](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. Na **Instalação de Software de terceiros** clique em **Aceitar posso** transferir e instalar MySQL.

    ![Instalar do MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. Detalhes de **servidor** do MySQL crie credenciais para iniciar sessão para a instância de servidor do MySQL.

    ![Credenciais do MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. Nas **Definições do Internet** especificar como o servidor de configuração irá ligar à internet. Tenha em atenção que:

    - Se pretender utilizar um proxy personalizado que deve configurá-lo antes de instalar o fornecedor.
    - Quando clica em **seguinte** um teste será executado para verificar a ligação de proxy.
    - Se utilizar um proxy personalizado ou o proxy predefinido requer autenticação que irá necessitar de introduzir os detalhes de proxy, incluindo o endereço, porta e as credenciais.
    - Os seguintes URLs devem ser acessíveis através do proxy:
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Se tiver, baseado no endereço IP regras de firewall Certifique-se de que as regras estão definidas para permitir a comunicação do servidor de configuração para os endereços IP descrito em [Intervalos de IP do Centro de dados de Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) e HTTPS protocolo (443). Teria intervalos IP lista branca da região Azure que planeia utilizar e que EUA Ocidental.

    ![Registo de proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. Nas **Definições de localização de mensagem de erro de fornecedor** especifique na qual o idioma que pretende mensagens de erro que seja apresentado.

    ![Registo de mensagem de erro](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. No **Registo de recuperação do Azure Site** procurar e selecione o ficheiro de chave copiado para o servidor.

    ![Registo de ficheiro de chave](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. Na página de conclusão do assistente seleccione estas opções:

    - Selecione **Iniciar a caixa de diálogo de gestão do conta** para especificar que a caixa de diálogo Gerir contas deve abrir depois de concluir o assistente.
    - Selecione **criar um ícone de ambiente de trabalho para Cspsconfigtool** para adicionar um atalho de ambiente de trabalho no servidor de configuração para que pode abrir a caixa de diálogo **Gerir contas** em qualquer altura sem necessitar de voltar a executar o assistente.

    ![Registo concluído](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. Clique em **Concluir** para concluir o assistente. É gerado uma frase de acesso. Copie-o numa localização segura. Terá de-lo para autenticar e registar os servidores de destino mestre e de processo com o servidor de configuração. Também é utilizado para assegurar a integridade canal comunicações de servidor de configuração. Pode voltar a gerar a frase de acesso, mas, em seguida, terá de voltar a registar o modelo global de destino e processar servidores utilizando a nova frase de acesso.

    ![Frase de acesso](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Após o registo de servidor de configuração será listado na página **Configuração de servidores** no cofre.

### <a name="set-up-and-manage-accounts"></a>Configurar e gerir contas

Durante a implementação recuperação de sites os pedidos de credenciais para as seguintes ações:

- Uma conta de VMware para thatSite recuperação automaticamente possa deteção VMs nos servidores do vCenter ou vSphere anfitriões. 
- Quando adiciona máquinas para protecção, para que a recuperação de sites pode instalar o serviço de mobilidade nos mesmos.

Depois de já se registou o servidor de configuração pode abrir a caixa de diálogo **Gerir contas** para adicionar e gerir as contas que devem ser utilizadas para estas ações. Existem algumas formas para fazer isto:

- Abra o atalho que optado por participar criado para a caixa de diálogo na última página da configuração para o servidor de configuração (cspsconfigtool).
- Abrir a caixa de diálogo de conclusão da configuração do servidor de configuração.

1. Em **Gerir contas** , clique em **Adicionar conta**. Também pode modificar e eliminar as contas existentes.

    ![Gerir contas](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. Em **Detalhes da conta** , especifique um nome de conta para utilizar no Azure e credenciais (nome de utilizador do domínio). 

    ![Gerir contas](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Ligar ao servidor de configuração 

Existem duas formas de ligar para o servidor de configuração:

- Através de uma ligação de ExpressRoute ou VPN site-para-site
- Através da internet 

Tenha em atenção que:

- Uma ligação à internet utiliza os pontos finais da máquina virtual em conjunto com o endereço IP público virtual do servidor.
- Uma ligação VPN utiliza o endereço IP interno do servidor, juntamente com as portas privada de ponto final.
- É uma decisão única para decidir se pretende ligar (dados de controlo e a replicação) a partir do seu servidores no local para os servidores de componentes vários (servidor de configuração, servidor de destino principal) em execução no Azure através de uma ligação VPN ou na internet. Não é possível alterar esta definição posteriormente. Se fizer terá de voltar a implementar o cenário e voltar a proteger os computadores.  


## <a name="step-3-deploy-the-master-target-server"></a>Passo 3: Implementar o servidor de destino principal

1. Clique em **Recursos de Target(Azure) preparar** > **servidor de destino mestra implementar**.
2. Especifique os detalhes do servidor de destino principal e as credenciais. O servidor será implementado no Azure mesma rede que o servidor de configuração. Quando clica em para concluir uma máquina virtual Azure será criada com uma imagem da Galeria Windows ou Linux.

    ![Definições de servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Tenha em atenção que os endereços IP primeiras quatro nos qualquer sub-rede são reservados para utilização interna de Azure. Especifique a qualquer outro endereço IP disponível.

>[AZURE.NOTE] Selecione DS4 padrão ao configurar a proteção por para das cargas de trabalho requerem consistente alto desempenho e/s e latência baixa para poder alojar e/s intensivos das cargas de trabalho com a [Conta de armazenamento Premium](../storage/storage-premium-storage.md).


3. Windows server destino VM é criado com estes pontos finais do modelo global. Note que os pontos finais públicos são criados apenas se a ligar através da internet.

    - Personalizar: Porta pública utilizada pelo servidor de processo para enviar dados de replicação através da internet. Porta privada 9443 é utilizada pelo servidor de processo para enviar dados de replicação para o servidor de destino mestra sobre VPN.
    - Especial 1: Porta pública utilizada pelo servidor de processo para enviar metadados através da internet. Porta privada 9080 é utilizada pelo servidor de processo para enviar metadados para o servidor de destino mestra sobre VPN.
    - PowerShell: Porta privada 5986
    - Ambiente de trabalho remoto: porta privada 3389

4. Um servidor de destino mestra Linux VM é criado com estes pontos finais. Note que o públicos pontos finais são criados apenas se estiver a ligar através da internet.

    - Personalizar: Porta pública utilizada pelo servidor de processo para enviar dados de replicação através da internet. Porta privada 9443 é utilizada pelo servidor de processo para enviar dados de replicação para o servidor de destino mestra sobre VPN.
    - Especial 1: Porta pública é utilizada pelo servidor de processo para enviar metadados através da internet. Porta privada 9080 é utilizada pelo servidor de processo para enviar metadados ao servidor de destino mestra através de VPN
    - SSH: Porta privada 22

    >[AZURE.WARNING] Não elimine ou altere o número da porta público ou privado de qualquer uma dos pontos finais criados durante a implementação de servidor de destino principal.

5. Em **máquinas virtuais** esperar até que a máquina virtual para começar.

    - Se for uma nota de servidor do Windows premida os detalhes de ambiente de trabalho remotos.
    - Se é um servidor de Linux e estiver a estabelecer ligação ao longo de nota VPN o endereço IP interno da máquina virtual. Se estiver a ligar através da internet tenha em atenção o endereço IP público.

6.  Iniciar sessão no servidor para concluir a instalação e registá-lo com o servidor de configuração. 
7.  Se estiver a executar o Windows:

    1. Inicie uma ligação de ambiente de trabalho remoto para a máquina virtual. A primeira vez que iniciar um script será executado numa janela do PowerShell. Não fechá-lo. Quando terminar a ferramenta de configuração do agente de anfitrião é apresentado automaticamente para registar o servidor.
    2. Na **Configuração do agente de anfitrião** especifique o endereço IP interno do servidor de configuração e porta 443. Pode utilizar o endereço interno e privada porta 443 mesmo se não estiver a ligar ao longo do VPN porque a máquina virtual está anexada a mesma Azure rede que o servidor de configuração. Deixe **Utilizar HTTPS** ativado. Introduza a frase de acesso para o servidor de configuração indicados anteriormente. Clique em **OK** para registar o servidor. Pode ignorar as opções de NAT. Não estão habituados.
    3. Se o requisito de unidade de retenção estimado é mais do que 1 TB pode configurar o volume de retenção (r) utilizando um disco virtual e [espaços de armazenamento](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
    
    ![Servidor de destino principal do Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Se estiver a executar o Linux:
    1. Certifique-se de que instalou o mais recente Linux integração serviços (lista de vertical) instalado antes de instalar o servidor de destino principal. Pode encontrar a versão mais recente da lista de vertical juntamente com instruções sobre como instalar [aqui](https://www.microsoft.com/download/details.aspx?id=46842). Reinicie o computador após instala a lista de vertical.
    2. Em **recursos de destino preparar (Azure)** clique em **Transferir e instalar o software adicional (apenas para o servidor de destino do modelo global de Linux)**. Copie o ficheiro transferido tar à máquina virtual utilizando um cliente de sftp. Em alternativa pode iniciar sessão servidor de destino mestra linux implementada e utilizar *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* para transferir o ficheiro.
    2. Inicie sessão no servidor de utilizar um cliente de Shell seguro. Se estiver ligado à rede Azure sobre VPN utilize o endereço IP interno. Caso contrário, utilize o endereço IP externo e o ponto final para um público de SSH.
    3. Extrair os ficheiros do instalador do gzipped executando: **alcatrão – xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
    ![Linux servidor de destino principal](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
    4. Certifique-se de que está a no diretório ao qual extraídas que o conteúdo do ficheiro tar.
    5. Copiar a frase de acesso do servidor de configuração para um ficheiro local utilizando o comando * *eco* `<passphrase>` * > passphrase.txt**
    6. Execute o comando "**sudo. instalar -t ambas as - a -R alojar /usr/local/ASR -d MasterTarget -i* `<Configuration server internal IP address>` * -p 443 -s y - c https -P passphrase.txt**".

    ![Registar o servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. Aguarde alguns minutos (10-15) e na página verificar que o servidor de destino mestra é listado como registado nos **servidores** > separador de **Detalhes do servidor** de**Configuração de servidores** . Se estiver a executar o Linux e não registar execute o anfitrião ferramenta de configuração do novamente a partir do /usr/local/ASR/Vx/bin/hostconfigcli. Terá de definir permissões de acesso ao executar chmod como raiz.

    ![Verifique se o servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] Pode demorar até 15 minutos depois de concluída para o servidor de destino mestra a ser listado no portal do registo. Para atualizar imediatamente, clique em **Atualizar** na página **Configuração Servers** .

## <a name="step-4-deploy-the-on-premises-process-server"></a>Passo 4: Implementar o servidor de processo no local

Antes de começar, recomendamos que configure um endereço IP estático no servidor processo para que garante ser persistente ser reiniciado.

1. Clique em Guia > **instalar o processo de servidor no local** > **Transferir e instalar o servidor do processo**.

    ![Instalar o servidor de processo](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  Copie o ficheiro zip transferido para o servidor no qual vai para instalar o servidor do processo. O ficheiro zip contém dois ficheiros de instalação:

    - Microsoft-ASR_CX_TP_8.4.0.0_Windows*
    - Microsoft-ASR_CX_8.4.0.0_Windows*

3. Deszipar o arquivo e copiar os ficheiros de instalação para uma localização no servidor.
4. Executar o **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** instalação de ficheiros e siga as instruções. Este procedimento instala necessários para a implementação de componentes de terceiros.
5. Em seguida, execute **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. Na página do **Modo de servidor** , selecione **Servidor do processo**.
7. Na página **Detalhes do ambiente** faça o seguinte:


    - Se quiser proteger VMware virtual máquinas clique em **Sim**
    - Se apenas pretende proteger servidores físicos e, por conseguinte, não precisa de vCLI VMware instalado no servidor processo. Clique em **não** e continue.

8. Tenha em atenção o seguinte procedimento ao instalar o VMware vCLI:

    - **Apenas VMware vSphere clip 5.5.0 é suportada**. O servidor de processo não funciona com outras versões ou atualizações de vSphere clip.
    - Transferir vSphere clip 5.5.0 a partir do [aqui.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
    - Se instalou o vSphere clip apenas antes de começou a instalar o servidor de processo e configuração não detetá-lo, aguarde até cinco minutos antes de tentar novamente o programa de configuração. Este procedimento assegura que todas as variáveis de ambiente necessário para deteção de clip vSphere foram inicializadas corretamente.

9.  Na **Seleção NIC para servidor de processo** selecione placa de rede que o servidor de processo deve utilizar.

    ![Selecione adaptador](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10. **Detalhes do servidor de configuração**:

    - O endereço IP e portas, se estiver a estabelecer ligação ao longo do VPN especifique o endereço IP interno do servidor de configuração e 443 para a porta. Caso contrário, especifique o endereço IP público virtual e mapeada público HTTP ponto final.
    - Escreva a frase de acesso do servidor de configuração.
    - Desmarque **assinatura de software de serviço de mobilidade confirmar** se pretende desativar a verificação quando utiliza push automática para instalar o serviço. Verificação de assinatura necessita de conectividade do servidor de processo da internet.
    - Clique em **seguinte**.

    ![Registe-se o servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. Na **unidade de instalação selecione** Selecione uma unidade de cache. O servidor de processo necessita de uma unidade de cache com, pelo menos, 600 GB de espaço livre. Em seguida, clique em **instalar**. 

    ![Registe-se o servidor de configuração](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. Tenha em atenção que poderá ter de reiniciar o servidor para concluir a instalação. Em **Servidor de configuração de** > **Detalhes do servidor** verifique que o servidor de processo é apresentado e está registado com êxito no cofre.

>[AZURE.NOTE]Pode demorar até 15 minutos depois de concluída para o servidor de processo a aparecer à medida listado no servidor de configuração de registo. Para atualizar imediatamente, atualizar o servidor de configuração, clicando no botão Atualizar na parte inferior da página de servidor de configuração
 
![Validar servidor de processo](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Se não tiver desativar a verificação de assinatura para o serviço de mobilidade quando se registou o servidor de processo pode fazê-lo mais tarde, da seguinte forma:

1. Iniciar sessão no servidor de processo como administrador e abra o ficheiro C:\pushinstallsvc\pushinstaller.conf para edição. Na secção **[PushInstaller.transport]** adicione esta linha: **SignatureVerificationChecks = "0"**. Guarde e feche o ficheiro.
2. Reinicie o serviço de instalação InMage autónoma.


## <a name="step-5-update-site-recovery-components"></a>Passo 5: Componentes de recuperação de sites de atualização

Componentes de recuperação de site são atualizadas a frequentemente. Quando estão disponíveis novas atualizações deve instalar-os pela seguinte ordem:

1. Servidor de configuração
2. Servidor de processo
3. Servidor de destino principal
4. Ferramenta de reposição de recurso (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Obter e instalar as atualizações


1. Pode obter atualizações para a configuração, processo e os servidores de destino principal a partir de recuperação de sites **Dashboard**. Instalação Linux extrair os ficheiros do instalador do gzipped e execute o comando "sudo. instalar" para instalar a atualização.
2. [Transfira](http://go.microsoft.com/fwlink/?LinkID=533813) a atualização mais recente para a tool(vContinuum) reposição de recurso.
3. Se estiver a executar o máquinas virtuais ou físicos servidores que já tem instalado o serviço de mobilidade, que possa receber atualizações para o serviço da seguinte forma:

    - **Opção 1**: transferir actualizações:
        - [Windows Server (apenas 64 bits)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
        - [CentOS 6.4,6.5,6.6 (apenas 64 bits)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
        - [Oracle Enterprise Linux 6.4,6.5 (apenas 64 bits)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
        - [SUSE Linux Enterprise Server SP3 (apenas 64 bits)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
        - Depois de actualizar o servidor de processo a versão actualizada do serviço de mobilidade estarão disponível na pasta C:\pushinstallsvc\repository no servidor do processo.
    - **Opção 2**: Se tiver uma máquina com uma versão anterior do serviço de mobilidade instalado, pode atualizar automaticamente o serviço de mobilidade no computador a partir do portal de gestão.

        1. Certifique-se de que o servidor de processo é atualizado.
        2. Certifique-se de que o computador protegido está em conformidade com a [Pré-requisitos](#install-the-mobility-service-automatically) para conduza automaticamente o serviço de mobilidade, para que a atualização funciona conforme esperado.
        2. Selecione o grupo de proteção, realçar a máquina protegida e clique em **serviço de mobilidade de atualização**. Este botão só está disponível se existir uma versão mais recente do serviço de mobilidade. 

            ![Selecione vCenter server](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

Nas contas selecionadas especificar a conta de administrador para ser utilizada para atualizar o serviço de mobilidade no servidor protegido. Clique em OK e aguarde que a tarefa acionada concluir.


## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Passo 6: Adicionar vCenter servidores ou vSphere anfitriões

1. Clique em **servidores** > **Servidores de configuração** > servidor de configuração >**Adicionar vCenter Server** para adicionar um anfitrião de servidor ou vSphere vCenter.

    ![Selecione vCenter server](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. Especificar os detalhes para o servidor ou anfitrião e selecione o servidor de processo que será utilizado para descobri-lo.

    - Se o servidor vCenter não estiver em execução na porta 443 predefinida especifique o número de porta no qual o servidor de vCenter está a ser executado.
    - O servidor de processo têm de estar na mesma rede que o anfitrião de servidor/vSphere vCenter e deve ter VMware vSphere clip 5.5.0 instalado.

    ![definições do servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. Após termina de deteção o servidor de vCenter estará listado nos detalhes de servidor de configuração.

    ![definições do servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. Se estiver a utilizar uma conta que não sejam de administrador para adicionar o servidor ou anfitrião, certifique-se que a conta tem os seguintes privilégios:

    - contas vCenter devem ter Centro de dados, arquivo de dados, pasta, anfitrião, rede, recursos, armazenamento vistas, Máquina Virtual e privilégios de parâmetro distribuído vSphere ativados.
    - contas de anfitrião vSphere devem têm o Centro de dados, arquivo de dados, pasta, anfitrião, rede, recursos, Máquina Virtual e vSphere distribuído mudar privilégios ativados



## <a name="step-7-create-a-protection-group"></a>Passo 7: Criar um grupo proteção

1. Abrir **Itens protegida** > **Grupo proteção** > **grupo de proteção de criar**.

    ![Criar grupo proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. Na página **Especificar as definições de proteção de grupo** , especifique um nome para o grupo e selecione o servidor de configuração no qual pretende criar o grupo.

    ![Definições de grupo proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. Na página **Especificar definições de replicação** de configurar as definições de replicação que serão utilizadas para todos os computadores no grupo.

    ![Replicação de grupo proteção](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. Definições:
    - **Multi VM consistência**: Se ativar este cria pontos de recuperação consistente para a aplicação partilhada através de máquinas no grupo proteção. Esta definição está mais relevante quando todas as máquinas no grupo proteção estiver a executar a carga de trabalho mesmo. Todos os computadores serão recuperados ao mesmo ponto de dados. Só está disponível para servidores do Windows.
    - **Limiar RPO**: alertas serão gerados quando a replicação de proteção de dados contínuos RPO excede o valor de limiar RPO configurado.
    - **Recuperação aponte retenção**: Especifica a janela de retenção. Podem ser recuperadas máquinas protegidas para qualquer ponto dentro nesta janela.
    - **Frequência de instantâneo aplicação consistente**: Especifica frequência pontos de recuperação que contém a aplicação consistente instantâneos serão criados.

Pode monitorizar o grupo de proteção como criaram na página **Itens protegida** .

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Passo 8: Configurar o máquinas que pretende proteger

Terá de instalar o serviço de mobilidade em máquinas virtuais e servidores físicos que pretende proteger. Pode fazê-lo de duas maneiras:

- Notificações push e instalar o serviço em cada máquina do servidor processo automaticamente.
- Instale manualmente o serviço. 

### <a name="install-the-mobility-service-automatically"></a>Instalar o serviço de mobilidade automaticamente

Quando adiciona máquinas a um grupo de proteção o serviço de mobilidade é automaticamente seguia e instalado em cada máquina pelo servidor de processo. 

**Push instalar automaticamente o serviço de mobilidade nos servidores do Windows:** 

1. Instalar as atualizações mais recentes para o servidor de processo, conforme descrito no [passo 5: Instale atualizações mais recentes](#step-5-install-latest-updates)e certifique-se de que o servidor de processo está disponível. 
2. Certifique-se de que existe conectividade de rede entre o computador de origem e o servidor de processo e de que o computador de origem acessível a partir do servidor de processo.  
3. Configure a firewall do Windows para permitir que **partilha ficheiros e impressoras** e **Instrumentação de gestão do Windows**. Em definições da Firewall do Windows, selecione a opção "Permitir uma aplicação ou funcionalidade através da Firewall" e selecione as aplicações, conforme mostrado na imagem abaixo. Para máquinas pertencerem a um domínio pode configurar a política de firewall com um GPO.

    ![Definições da firewall](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. A conta utilizada para efetuar a instalação push tem de ser no grupo Administradores no computador que pretende proteger. Estas credenciais são utilizadas apenas para a instalação push do serviço de mobilidade e irá fornecer quando adiciona uma máquina a um grupo de proteção.
5. Se a conta fornecida não é uma conta de domínio terá de desativar o controlo de acesso remoto do utilizador no computador local. Para fazer isto adicionar a entrada de registo LocalAccountTokenFilterPolicy DWORD com um valor de 1 em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Para adicionar a entrada de registo de um clip abrir cmd ou powershell e introduza **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Push instalar automaticamente o serviço de mobilidade nos servidores Linux:**

1. Instalar as atualizações mais recentes para o servidor de processo, conforme descrito no [passo 5: Instale atualizações mais recentes](#step-5-install-latest-updates)e certifique-se de que o servidor de processo está disponível.
2. Certifique-se de que existe conectividade de rede entre o computador de origem e o servidor de processo e de que o computador de origem acessível a partir do servidor de processo.  
3. Certifique-se que a conta é um utilizador de raiz do servidor de origem Linux.
4. Certifique-se de que o ficheiro de /etc/hosts do servidor de origem Linux contém as entradas que mapeiam o nome de anfitrião local para endereços IP associados NIC todos os.
5. Instalar o antes de openssh mais recente, servidor de antes openssh, openssl pacotes no computador que pretende proteger.
6. Certifique-se de que SSH é activado e em execução no Porta 22. 
7. Ativar a autenticação subsystem e palavra-passe SFTP no ficheiro sshd_config da seguinte forma: 

    - um) inicie sessão como raiz.
    - b) no ficheiro de /etc/ssh/sshd_config de ficheiro, localize a linha que começa com **PasswordAuthentication**.
    - c) remova os comentários da linha e altere o valor de "não" para "Sim".

        ![Mobilidade Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

    - d) localize a linha que começa com Subsystem e remova os comentários da linha.
    
        ![Mobilidade de push Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    

8. Certifique-se de que a variante de Linux de máquina de origem é suportada. 
 
### <a name="install-the-mobility-service-manually"></a>Instalar manualmente o serviço de mobilidade

Pacotes de software utilizados para instalar o serviço de mobilidade estão no servidor de processo na C:\pushinstallsvc\repository. Iniciar sessão no servidor do processo e copie o pacote de instalação adequado para o computador de origem com base na tabela abaixo:-

| Sistema operativo de origem                               | Pacote de serviço de mobilidade no servidor de processo                                                            |
|---------------------------------------------------    |------------------------------------------------------------------------------------------------------ |
| Windows Server (apenas 64 bits)                          | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe`         |
| CentOS 6.4, 6.5, 6.6 (apenas 64 bits)                    | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz`     |
| SUSE Linux Enterprise Server 11 SP3 (apenas 64 bits)     | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (apenas 64 bits)        | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz`       |


**Para instalar o serviço de mobilidade manualmente num servidor do Windows**, efetue o seguinte procedimento:

1. Copie o pacote do **Microsoft ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** do caminho do diretório processo servidor indicado na tabela acima para o computador de origem.
2. Instale o serviço de mobilidade ao executar o ficheiro executável no computador de origem.
3. Siga as instruções do programa de instalação.
4. Selecione o **serviço de mobilidade** que a função e clique em **seguinte**.
    
    ![Instalar o serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. Deixe o diretório de instalação, como o caminho de instalação predefinido e clique em **instalar**.
6. Na **Configuração do agente de anfitrião** especifique o endereço IP e porta HTTPS do servidor de configuração.

    - Se estiver a estabelecer ligação através da internet especifique o endereço IP virtual público e o ponto final HTTPS público como a porta.
    - Se estiver a estabelecer ligação ao longo do VPN especifique o endereço IP interno e 443 para a porta. Deixe **Utilizar HTTPS** selecionada.

    ![Instalar o serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. Especifique a frase de acesso do servidor de configuração e clique em **OK** para registar o serviço de mobilidade com o servidor de configuração.

**Para executar a partir da linha de comandos:**

1. Copiar a frase de acesso a partir do CX para o ficheiro "C:\connection.passphrase" no servidor e executa este comando. No nosso exemplo CX i 104.40.75.37 e à porta HTTPS é 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Instalar o serviço de mobilidade manualmente num servidor Linux**:

1. Copie o arquivo de alcatrão adequado com base na tabela acima, a partir do servidor de processo para o computador de origem.
2. Abra um programa de shell e extrair o arquivo de alcatrão compactados como um caminho local através da execução`tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Criar um ficheiro de passphrase.txt no diretório local para o qual extraídas os conteúdos do arquivo de alcatrão introduzindo *`echo <passphrase> >passphrase.txt`* a partir da shell de.
4. Instalar o serviço de mobilidade introduzindo *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP e portas:

    - Se estiver a ligar para o servidor de configuração através da internet especifique o servidor virtual público endereço IP de configuração e público HTTPS ponto final no `<IP address>` e `<port>`.
    - Se estiver a ligar através de uma ligação VPN especificar o endereço IP interno e 443.

**Para executar a partir da linha de comandos**:

1. Copiar a frase de acesso a partir do CX para o ficheiro "passphrase.txt" no servidor e execute este comandos. No nosso exemplo CX i 104.40.75.37 e à porta HTTPS é 62519:

Para instalar num servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Para instalar o servidor de destino:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] Quando adiciona máquinas a um grupo de proteção que já estão a executar uma versão adequada do serviço de mobilidade, em seguida, a instalação push é ignorado.


## <a name="step-9-enable-protection"></a>Passo 9: Proteção de ativar

Para ativar proteção Adicionar máquinas virtuais e servidores físicos a um grupo de proteção. Antes de começar, tenha em atenção que:

- Máquinas virtuais sejam detectadas a cada 15 minutos e, pode demorar até 15 minutos para que apareçam no Azure recuperação de Site após deteção.
- As alterações de ambiente na máquina virtual (tal como a instalação das ferramentas de VMware) também podem demorar até 15 minutos para ser atualizados no recuperação de sites.
- Pode verificar a última vez descoberta no **Último contacto no** campo para o anfitrião de servidor/ESXi vCenter na página **Configuração Servers** .
- Se tiver um grupo de proteção já tiver criado e adicione um anfitrião de servidor ou ESXi vCenter após que, ocorre o mais quinze minutos para o portal do Azure recuperação de sites para atualizar e para máquinas virtuais a ser listado na caixa de diálogo **Adicionar máquinas a um grupo de proteção** .
- Se gostaria de proceder imediatamente com a adição de máquinas ao grupo de proteção sem ter de esperar para a deteção agendada, realce o servidor de configuração (não clique) e clique no botão **Atualizar** .
- Quando adiciona máquinas virtuais ou máquinas físicas a um grupo de proteção, o servidor de processo emite automaticamente e instala o serviço de mobilidade no servidor de origem, se ainda não está instalado o it.
- A operação de push automática mecanismos para trabalhar Certifique-se de que configurou as máquinas protegidas, tal como descrito no passo anterior.

Adicione máquinas da seguinte forma:

1. Clique em **protegida itens** > **Grupo proteção** > **máquinas** > **Adicionar máquinas**. Como prática recomendada Recomendamos que grupos de proteção deverão ser espelhada seu das cargas de trabalho para que adicionar máquinas a executar uma aplicação específica do mesmo grupo.
2. Em **máquinas virtuais do selecione** se estiver a proteger servidores físicos, no assistente **Adicionar física máquinas** fornece o endereço IP e o nome amigável. Em seguida, selecione a família do sistema operativo.

    ![Adicionar o servidor do Centro de V](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. Em **máquinas virtuais do selecione** se estiver a proteger máquinas virtuais de VMware, selecione um servidor de vCenter que está a gerir o seu máquinas virtuais (ou anfitrião do EXSi no qual está a executar) e, em seguida, selecione as máquinas.

    ![Adicionar o servidor do Centro de V](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png) 
4. Em **Especificar recursos de destino** , selecione os servidores de destino principal e armazenamento utilizar para a replicação e selecione se as definições de devem ser utilizadas para todas as cargas de trabalho. Selecione a [Conta de armazenamento de Premium](../storage/storage-premium-storage.md) durante a configuração de proteção das cargas de trabalho requerem consistente alto desempenho IO e latência baixa para poder alojar das cargas de trabalho intensivos es. Se pretender utilizar uma conta de armazenamento de Premium para os seus discos de carga de trabalho, terá de utilizar o destino de modelo global de DS série. Não pode utilizar discos de armazenamento de Premium com um modelo global de destino de séries que não sejam DS.

    >[AZURE.NOTE] Não suportamos a mudança de contas de armazenamento criado utilizando o [novo portal Azure](../storage/storage-create-storage-account.md) através de grupos de recursos.

    ![servidor de vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. Nas **Contas especificar** selecione a conta que pretende utilizar para instalar o serviço de mobilidade no máquinas protegidas. As credenciais de conta são necessários para a instalação do serviço de mobilidade automática. Se não conseguir selecionar criar uma conta-se de que configura um conforme descrito no passo 2. Tenha em atenção que esta conta não pode ser acedida por Azure. Para o Windows server a conta deve ter privilégios de administrador do servidor de origem. Para Linux a conta tem de ser raiz.

    ![Linux credenciais](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. Clique na marca de verificação para terminar a adição máquinas para o grupo de proteção e para iniciar a replicação inicial para cada máquina. Pode monitorizar estado na página **tarefas** .

    ![Adicionar o servidor do Centro de V](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. Além disso pode monitorizar o estado de proteção ao clicar em **Itens protegido por** > nome do grupo proteção > **máquinas virtuais** . Depois de replicação inicial estar concluída e as máquinas estão a sincronizar dados mostrará estado **protegido** .

    ![Tarefas de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### <a name="set-protected-machine-properties"></a>Propriedades de máquina conjunto protegido

1. Depois de um computador tem um estado **protegido** pode configurar as respetivas propriedades activação pós-falha. Os detalhes do grupo de proteção selecione computador e abra o separador de **Configurar** .
2. Pode modificar o nome que será dada a máquina no Azure após activação e o tamanho do Azure máquina virtual. Também pode selecionar a rede Azure ao qual o computador irá estar ligado após activação pós-falha.

    > [AZURE.NOTE] [Migração das redes](../resource-group-move-resources.md) através de grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportado para as redes utilizadas para implementar o recuperação de sites.

    ![Definir as propriedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Tenha em atenção que:

- O nome do computador Azure deve cumprir os requisitos Azure.
- Por predefinição máquinas virtuais replicadas no Azure não estão ligadas a uma rede Azure. Se pretender replicada máquinas virtuais para comunicar Certifique-se definir a mesma rede Azure das mesmas.
- Se redimensionar um volume num servidor física ou máquina de virtual VMware vai para o estado crítica. Se necessitar de modificar o tamanho, faça o seguinte:

    - um) altere a definição de tamanho.
    - b) na caixa de diálogo **máquinas virtuais** , selecione a máquina virtual e clique em **Remover**.
    - c) na **Remover Máquina Virtual** selecione a opção **desativar a proteção contra (utilizar para a recuperação agregar e volume redimensionar)**. Esta opção desativa a proteção, mas mantém os pontos de recuperação no Azure.

        ![Definir as propriedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

    - d) reactive proteção para a máquina virtual. Quando reactivar proteção de dados para o volume redimensionado serão transferidos para Azure.

    

## <a name="step-10-run-a-failover"></a>Passo 10: Executar uma activação pós-falha

Atualmente apenas pode executar activações pós-falha não planeada para protegida VMware de máquinas virtuais e servidores físicos. Tenha em atenção o seguinte procedimento:



- Antes de iniciar uma activação pós-falha, certifique-se de que os servidores de destino do modelo global e de configuração estão em execução e saudável. Caso contrário activação pós-falha irá falhar.
- Máquinas de origem não são encerrar como parte de uma activação não planeado pós-falha. Efetuar uma activação não planeado pós-falha deixa de replicação de dados para os servidores de protegida. Terá de eliminar as máquinas do grupo de proteção e adicioná-los novamente para poder começar a proteger máquinas novamente depois de concluída a activação não planeado pós-falha.
- Se pretender falhar ao longo sem perder quaisquer dados, certifique-se de que as máquinas virtuais do site principal estão desactivadas antes de iniciar a activação pós-falha.

1. **Planos de recuperação de** página e adicione um plano de recuperação. Especificar os detalhes para o plano e selecione **Azure** como de destino.

    ![Configurar o plano de recuperação](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. Na **máquina de Virtual selecione** selecione um grupo de proteção e, em seguida, selecione máquinas no grupo para adicionar o plano de recuperação. [Leia mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. Se for necessário pode personalizar o plano para criar grupos e sequência a ordem na quais máquinas na recuperação plano é Ocorreu uma falha ao longo do. Também pode adicionar solicita ações manuais e scripts. Podem ser adicionados os scripts quando a recuperar Azure utilizando [Runbooks de automatização do Azure](site-recovery-runbook-automation.md).

4. Na página **Plano de recuperação** selecione o plano e clique em **Não planeado activação pós-falha**.
5. Na **Confirmar activação pós-falha** Verifique a direção de activação pós-falha (para Azure) e selecione o ponto de recuperação para sobre a falhar.
6. Esperar até que a tarefa de activação pós-falha concluir e, em seguida, certifique-se de que a activação pós-falha funcionou como esperado e que as máquinas virtuais replicadas iniciado com êxito no Azure.




## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Passo 11: Falhar novamente Ocorreu uma falha ao longo do máquinas a partir do Azure

[Saiba mais](site-recovery-failback-azure-to-vmware-classic-legacy.md) sobre como trazer o seu Falha ao longo do máquinas em execução no Azure novamente o seu ambiente no local.


## <a name="manage-your-process-servers"></a>Gerir os servidores de processo

O servidor de processo envia dados de replicação para o servidor de destino principal no Azure e detetar máquinas virtuais novas VMware, adicionadas a um servidor de vCenter. Nas seguintes circunstâncias poderá pretender alterar o servidor do processo na sua implementação:

- Se o servidor de processo actual vai para baixo
- Se a recuperação ponto aumenta objectivo (RPO) para um nível aceitável para a sua organização.

Se necessário, pode mover a replicação de alguns ou todos os seus no local VMware virtual máquinas e servidores físicos para um servidor de processo diferente. Por exemplo:

- **Falha**— se um servidor de processo falha ou não está disponível pode mover replicação de máquina protegida para outro servidor do processo. Metadados do computador de origem e máquina de réplica serão movidos para o novo servidor de processo e dados são sincronizados. O novo servidor de processo ligar-se-á automaticamente ao servidor vCenter para executar a deteção automática. Pode monitorizar o estado dos servidores de processo no dashboard de recuperação de sites.
- **Balanceamento de carga para ajustar RPO**— para melhorada balanceamento de carga pode selecionar um servidor de processo diferente no portal do recuperação de sites e mover a replicação de uma ou mais máquinas-lhe para balanceamento de carga manual. Neste caso metadados das máquinas origem e réplica selecionados são movido para o novo servidor do processo. O servidor de processo original mantém-se ligado ao servidor vCenter. 

### <a name="monitor-the-process-server"></a>Monitorizar o servidor de processo

Se é um servidor de processo num Estado crítica que ser apresentado um aviso de estado no Dashboard de recuperação de Site. Pode clicar no estado para abrir o separador eventos e, em seguida, desagregue tarefas específicas no separador tarefas. 

### <a name="modify-the-process-server-used-for-replication"></a>Modificar o servidor de processo utilizado para a replicação

1. Abrir **os servidores de** > **Servidores de configuração** > servidor de configuração > **Detalhes do servidor**.
2. Clique em **Servidores de processo** > **Mudar de servidor de processo** junto ao servidor que pretende modificar.

    ![Alterar o servidor de processo 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. Em **Servidor de processo de alteração** > **Servidor do processo de destino** seleccione o novo servidor que pretende utilizar e, em seguida, selecione as máquinas virtuais ao qual pretende criar uma réplica para o novo servidor. Clique no ícone de informações junto ao nome do servidor para obter mais detalhes de espaço livre e memória utilizada. O espaço de média que será exigido a criar uma réplica cada máquina virtual selecionada para o novo servidor de processo que é apresentado para ajudá-lo a tomar decisões de carregar.

    ![Alterar o servidor de processo 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. Clique na marca de verificação para começar a replicação para o novo servidor do processo. Note que se remover todas as máquinas virtuais a partir de um servidor de processo que foi crítico-deve já não apresentar um aviso crítico no dashboard.


## <a name="third-party-software-notices-and-information"></a>Avisos de terceiros software e informações

Não faça traduzir ou Localize

O software e firmware em execução no Microsoft produto ou serviço baseado ou incorpora material de projetos listados abaixo (constituem, "terceiros código").  A Microsoft está o autor não original do código de terceiros.  O aviso de copyright original e a licença, sob o qual a Microsoft recebido essas código de terceiros, são definidas quarta abaixo.

As informações na secção A são relacionadas com componentes de código de terceiros de projetos listados abaixo. Esses licenças e as informações são fornecidos apenas para fins informativos.  Este código de terceiros está a ser relicensed para si pela Microsoft em termos de licenciamento de software da Microsoft para o Microsoft produto ou serviço.  

As informações na secção B são relacionadas com componentes de código de terceiros que estão a ser disponibilizados para si pela Microsoft em termos de licenciamento originais.

O ficheiro concluído poderá encontrar no do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). A Microsoft reserva-se por sugestão, todos os direitos não expressamente concedidos mencionados, estoppel ou de outra forma.
