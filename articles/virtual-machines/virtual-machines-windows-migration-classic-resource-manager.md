<properties
    pageTitle="Plataformas suportadas migração dos recursos IaaS de clássico para o Gestor de recursos do Azure | Microsoft Azure"
    description="Este artigo explica a migração suportadas plataforma dos recursos de clássico para o Gestor de recursos do Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plataformas suportadas migração dos recursos IaaS de clássico para o Gestor de recursos do Azure

Neste artigo, iremos descrevem como podemos está a ativar migração infraestrutura como um serviço (IaaS) recursos a partir do clássica a modelos de implementação do Gestor de recursos. Pode ler mais sobre as [funcionalidades do Gestor de recursos do Azure e benefícios](../azure-resource-manager/resource-group-overview.md). Vamos de detalhe como ligar recursos a partir dos modelos de duas implementação coexistem na sua subscrição utilizando os gateways de site para o site de rede virtual. 

## <a name="goal-for-migration"></a>Objetivo de migração

Gestor de recursos permite implementar aplicações complexas através de modelos, configura máquinas virtuais utilizando VM extensões e incorpora gestão de acesso e de marcação. Gestor de recursos do Azure inclui dimensionável, paralela implementação para máquinas virtuais em conjuntos de disponibilidade. O novo modelo de implementação também fornece gestão de ciclo de vida de cluster, rede e armazenamento de forma independente. Por fim, existe um foco sobre ativar segurança por predefinição, com a imposição dos máquinas virtuais numa rede virtual.

Quase todas as funcionalidades do modelo clássico de implementação de são suportadas para cluster, rede e armazenamento em Gestor de recursos do Azure. Para beneficiar das novas capacidades no Gestor de recursos do Azure, pode migrar implementações existentes a partir do modelo de implementação de clássica.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Alterações às suas automatização e ferramentas após a migração

Como parte do migrar os recursos a partir do modelo de implementação de clássica ao modelo de implementação de Gestor de recursos, tem de atualizar as automatização existente ou ferramentas para se certificar de que este continua a trabalhar após a migração.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Significado de migração de IaaS recursos a partir do clássico para o Gestor de recursos

Antes de podemos desagregar os detalhes, vamos olhar a diferença entre operações de plano de dados e o plano de gestão de recursos IaaS.

- *Plano de gestão* descreve as chamadas que entram no plano de gestão ou a API para modificar recursos. Por exemplo, operações como criar uma VM, reiniciar uma VM e atualizar uma rede virtual com uma nova sub-rede gerir os recursos em execução. Não diretamente afeta estabelecer ligação às instâncias.
- *Plano de dados* (aplicação) descreve o tempo de execução da própria aplicação e envolve interação com instâncias que não atravessam a API do Azure. Aceder ao seu Web site ou no dados a partir de uma instância do SQL Server ou um servidor de MongoDB fosse considerado dados interação plano ou da aplicação. Copiar um blob a partir de uma conta de armazenamento e aceder a um endereço IP público para RDP ou SSH para a máquina virtual também são plano de dados. Estas operações mantêm a aplicação a executar ao longo de cluster, redes e armazenamento.

>[AZURE.NOTE] Em alguns cenários de migração, a plataforma Azure deixa, retira a atribuição e reinicia as máquinas virtuais. Tal como um tempo de inatividade breve do plano de dados.

## <a name="supported-scopes-of-migration"></a>Suportado âmbitos de migração

Existem três âmbitos de migração que principalmente alvo cluster, rede e armazenamento. 

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migração de máquinas virtuais (não está na rede virtual)

No modelo de implementação do Gestor de recursos, segurança é aplicada para as suas aplicações por predefinição. Todos os VMs necessitam de ser uma rede virtual no modelo de Gestor de recursos. O reinicia plataforma Azure (`Stop`, `Deallocate`, e `Start`) VMs como parte da migração. Tem duas opções para as redes virtuais:

- Pode pedir a plataforma para criar uma nova rede virtual e migrar a máquina virtual para a nova rede virtual.
- Pode migrar a máquina virtual para uma rede virtual existente no Gestor de recursos.

>[AZURE.NOTE] Neste âmbito de migração, as operações de plano de gestão e as operações de plano de dados podem não ser permitidas para um período de tempo durante a migração.

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migração de máquinas virtuais (numa rede virtual)

Na maioria das configurações de VM, apenas os metadados está a migrar entre os modelos de implementação clássica e o Gestor de recursos. Os VMs subjacentes estão a executar no mesmo hardware, na mesma rede e com o mesmo armazenamento. As operações de plano de gestão não podem ser permitidas para um determinado período de tempo durante a migração. No entanto, o plano de dados continua a funcionar. Isto é, suas aplicações a executar na parte superior de VMs (clássicos) não assumir tempo de inatividade durante a migração.

As seguintes configurações atualmente não são suportadas. Se for adicionado suporte no futuro, algumas VMs nesta configuração poderá implicam tempo de inatividade (ir através de paragem, retirar e reinicie o operações VM).

-   Tiver mais do que uma disponibilidade definir num serviço de nuvem único.
-   Tem um ou mais conjuntos de disponibilidade e VMs que não estão a ser uma disponibilidade definir num serviço de nuvem único.

>[AZURE.NOTE] Neste âmbito de migração, o plano de gestão não pode ser permitido para um período de tempo durante a migração. Para determinados configurações conforme descrito acima, ocorre o tempo de inatividade do plano de dados.

### <a name="storage-accounts-migration"></a>Migração de contas de armazenamento

Para permitir que a migração de forma totalmente integrada, pode implementar Gestor de recursos VMs numa conta clássico de armazenamento. Com esta capacidade, recursos de cluster e de rede podem e devem ser migrados independentemente dos existentes dela contas de armazenamento. Depois de migrar ao longo do seu máquinas virtuais e de rede Virtual, precisar de migrar sobre as suas contas de armazenamento para concluir o processo de migração. 

>[AZURE.NOTE] O modelo de implementação do Gestor de recursos não tem o conceito de imagens de clássica e discos. Quando a conta de armazenamento é migradas, clássicas de imagens e discos não estão visíveis na pilha de Gestor de recursos, mas a segurança VHDs permanecer na conta de armazenamento. 

## <a name="unsupported-features-and-configurations"></a>Configurações e funcionalidades não suportadas

Não atualmente suportamos algumas funcionalidades e configurações. As seguintes secções descrevem as nossas recomendações à volta dos mesmos.

### <a name="unsupported-features"></a>Funcionalidades não suportadas

As seguintes funcionalidades não são atualmente suportadas. Pode, opcionalmente, remover estas definições, migrar as VMs e, em seguida, voltar a ativar as definições no modelo de implementação de Gestor de recursos.

Fornecedor de recursos | Funcionalidade
---------- | ------------
Cluster | Não associados máquina virtual discos.
Cluster | Imagens de máquina virtual.
Rede | ACL de ponto final.
Rede | Gateways de rede virtual (sites, as Azure ExpressRoute, gateway de aplicação, aponte para o site).
Rede | Redes virtuais utilizando VNet efectuado. (Migrar VNet para processador, em seguida, peer) Saiba mais sobre [VNet efectuado] (... /virtual-Network/virtual-Network-peering-Overview.MD).
Rede | Perfis do Gestor de tráfego.

### <a name="unsupported-configurations"></a>Configurações não suportadas

As seguintes configurações atualmente não são suportadas.

Serviço | Configuração | Recomendação
---------- | ------------ | ------------
Gestor de recursos | Função com base no Access controlo RBCA () para recursos clássicos | Uma vez que o URI dos recursos é modificado após a migração, é recomendável que planeia as atualizações de política RBAC que precisam de ocorrer após a migração.
Cluster | Várias sub-redes associados a uma VM | Atualize a configuração de sub-rede para fazer referência a apenas sub-redes.
Cluster | Máquinas virtuais que pertencem a uma rede virtual, mas não tiver uma sub-rede explícita atribuída | Opcionalmente, pode eliminar a VM.
Cluster | Máquinas virtuais que tenham alertas, Autoscale políticas | A migração analise e estas definições são ignoradas. É vivamente recomendado que avalie o seu ambiente antes de efetuar a migração. Em alternativa, pode reconfigurar as definições de alerta após a migração estiver concluída.
Cluster | Extensões de XML VM (BGInfo 1.*, Visual Studio Debugger, Web implementar e depuração remota) | Não é suportada. É recomendado que remova estas extensões de máquina virtual para continuar a migração ou vão ser largadas automaticamente durante o processo de migração.
Cluster | Diagnósticos de arranque com armazenamento Premium | Desative a funcionalidade diagnósticos de arranque para os VMs antes de prosseguir com a migração. Pode ativar novamente a diagnósticos de arranque na pilha de Gestor de recursos após a migração está concluída. Para além disso, blobs que estão a ser utilizados para captura de ecrã e registos de série devem ser eliminadas, de modo que já não é cobrados para esses blobs.
Cluster | Serviços em nuvem que contenham funções web/trabalho | Isto não é atualmente suportado.
Rede | Redes virtuais que contêm máquinas virtuais e funções web/trabalho |  Isto não é atualmente suportado.
Azure de aplicação de serviço | Redes virtuais que contêm os ambientes de aplicação de serviço | Isto não é atualmente suportado.
Azure HDInsight | Redes virtuais que contêm HDInsight serviços | Isto não é atualmente suportado.
Serviços de ciclo de vida do Microsoft Dynamics | Redes virtuais que contêm máquinas virtuais que são geridas pelo Dynamics ciclo de vida Services | Isto não é atualmente suportado.
Cluster | Extensões de centro de segurança do Azure com um VNET que tem um VPN gateway ou gateway ER com o servidor de DNS no local | Centro de segurança do Azure instala automaticamente extensões no seu máquinas virtuais para monitorizar a sua segurança e elevar alertas. Estas extensões normalmente obtenham instaladas automaticamente se a política do Centro de segurança do Azure estiver ativada da subscrição. À medida migração de gateway não é suportada atualmente e o gateway tem de ser eliminada antes de prosseguir com consolidar a migração, o acesso à internet a conta de armazenamento VM é perdido quando o gateway é eliminado. A migração não irá continuar quando isto acontece como não é possível preencher o blob de estado do agente de convidado. Recomenda-se para desativar política de centro de segurança do Azure a subscrição 3 horas antes de prosseguir com a migração.

## <a name="the-migration-experience"></a>A experiência de migração

Antes de começar a experiência de migração, é recomendado o seguinte procedimento:

- Certifique-se de que os recursos que pretende migrar não utilizam qualquer configurações ou funcionalidades não suportadas. Normalmente, a plataforma Deteta estes problemas e gera um erro.
- Se tiver VMs que não estão numa rede virtual, estes serão parados e desatribuídos como parte da operação de preparar. Se não quiser perder o endereço IP público, olhe para o endereço IP a reservar antes de acionar a operação de preparação. No entanto, se estiverem as VMs na rede virtual, não são parados e desatribuídos.
- Planear a sua migração durante não-horário comercial para acomodar para qualquer falhas inesperadas que poderão ocorrer durante a migração.
- Transfira a configuração atual do seu VMs ao utilizar o PowerShell, comandos de interface de comandos (CLI) ou REST APIs para que seja mais fácil para validação após o passo preparar está concluído.
- Atualize o seu scripts de automatização/operationalization para processar o modelo de implementação do Gestor de recursos antes de iniciar a migração. Opcionalmente, pode efetuar operações de obter quando os recursos estão no estado de preparada.
- Avalie as políticas RBAC que estão configuradas nos recursos IaaS clássicos e planear após a migração está concluída.

O fluxo de trabalho de migração é a seguinte

![Captura de ecrã que mostra o fluxo de trabalho de migração](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Todas as operações descritas nas secções seguintes são idempotent. Se tiver um problema diferente de uma funcionalidade não suportada ou um erro de configuração, recomenda-se que repita preparar, interrupção ou a consolidação de operação. A plataforma Azure tentará novamente a ação.

### <a name="validate"></a>Validar

A operação de validar é o primeiro passo no processo de migração. O objetivo deste passo é analisar dados em segundo plano para os recursos em migração e voltar a sucesso/falha se os recursos são capazes de migração.

Selecionar a rede virtual ou o serviço alojado (se não for uma rede virtual) que pretende validar migração.

* Se o recurso não for capaz de migração, a plataforma Azure lista todos os motivos para por que motivo não é suportada para a migração.

### <a name="prepare"></a>Preparar

A operação de preparar é o segundo passo o processo de migração. O objetivo deste passo é simular a transformação dos recursos de clássico IaaS para recursos de Gestor de recursos e apresentar este lado a lado para conseguir visualizar.

Selecionar a rede virtual ou o serviço alojado (se não for uma rede virtual) que pretende que preparar a migração.

* Se o recurso não for capaz de migração, a plataforma Azure interrompe o processo de migração e listas a razão pela qual a operação de preparação falhou.
* Se o recurso for capaz de migração, os bloqueios primeiros plataforma Azure para baixo para as operações de plano de gestão para os recursos ao abrigo da migração. Por exemplo, não estão possam adicionar um disco de dados a uma VM em migração.

A plataforma Azure, em seguida, começa a migração de metadados da clássico para o Gestor de recursos para os recursos migrar.

Depois da operação de preparação estiver concluída, tem a opção de visualizar os recursos no ambas as classic e o Gestor de recursos. Para cada serviço na nuvem no modelo de implementação clássico, a plataforma Azure cria um nome de grupo de recursos que tem o padrão de `cloud-service-name>-migrated`.

>[AZURE.NOTE] Máquinas virtuais que não estão numa rede Virtual clássica são paradas deallocated nesta fase de migração.

### <a name="check-manual-or-scripted"></a>Verificação (manual ou script)

No passo verificação, opcionalmente, pode utilizar a configuração que tenha transferido anteriormente para validar que a migração parece correta. Em alternativa, pode iniciar sessão para o portal e a verificação de spot as propriedades e recursos para validar que gostar migração de metadados.

Se estiver a migrar uma rede virtual, a maior parte dos configuração das máquinas virtuais não for reiniciada. Para as aplicações nesses VMs, pode validar que a aplicação é continuam a funcionar.

Pode testar o seu monitorização/automatização e scripts operacionais para ver se a VMs estão a funcionar como esperado e se os scripts actualizados está a funcionar corretamente. Operações de obter só são suportadas quando os recursos estão no estado preparado.

Não existe nenhuma janela de tempo conjunto antes da qual precisa de consolidar a migração. Pode demorar tanto tempo que desejar neste estado. No entanto, o plano de gestão está bloqueado para estes recursos até que a interrupção ou a consolidação.

Se vir quaisquer problemas, pode cancelar a migração e voltar ao modelo de implementação clássica. Depois de voltar, a plataforma Azure abrirá as operações de plano de gestão de recursos para que pode retomar a operações normais nesses VMs no modelo de implementação clássica.

### <a name="abort"></a>Cancelar

Interrupção passo é opcional que pode utilizar para reverter as alterações ao modelo de implementação clássica e parar a migração.

>[AZURE.NOTE] Não pode ser executada esta operação após ter acionou a operação de consolidação.  

### <a name="commit"></a>Consolidar

Depois de concluir a validação, pode consolidar a migração. Recursos não aparecem deixem no classic e apenas estão disponíveis no modelo de implementação de Gestor de recursos. Os recursos migrados podem ser geridos apenas no novo portal.

>[AZURE.NOTE] Esta é uma operação de idempotent. Se a falhar, é recomendável que repetir a operação. Se este continua a falhar, crie uma bilhetes de suporte ou um fórum com uma etiqueta de ClassicIaaSMigration no nosso [Fórum VM](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Este plano de migração afeta qualquer um dos meus serviços existentes ou aplicações que são executadas em máquinas virtuais Azure?**

Não. Os VMs (clássicos) são totalmente suportados serviços na disponibilidade geral. Pode continuar a utilizar estes recursos para expandir a sua ambiental no Microsoft Azure.

**O que acontece aos meus VMs se posso não planeia migrar no futuro próximo?**

Vamos não são preterir a API clássico existentes e o modelo de recursos. Pretendemos facilitar a migração, considerar as funcionalidades avançadas que estão disponíveis no modelo de implementação de Gestor de recursos. Recomendamos vivamente que revê [algumas dos avanços](virtual-machines-windows-compare-deployment-models.md) que fazem parte de IaaS em Gestor de recursos.

**O que significa este plano de migração para a minha ferramentas existente?**

Atualizar o seu ferramentas para o modelo de implementação do Gestor de recursos é uma das alterações mais importantes que ter para ter em conta os planos de migração.

**Quanto tempo vai o tempo de inatividade do plano de gestão de ser?**

Depende do número de recursos que estão a ser migrado. Para implementações mais pequenas (alguns dezenas de VMs), a migração toda deve ter menos de uma hora. Para implementações em grande escala (centenas de VMs), a migração poderá demorar algumas horas.

**Posso recuperar novamente depois dos meus recursos migrar são consolidados no Gestor de recursos?**

Pode interromper a sua migração desde que os recursos estão no estado preparado. Não é suportada anulação depois dos recursos foram migrados com êxito através da operação de consolidação.

**Pode posso recuperar meu migração se a operação de consolidação falhar?**

Não é possível interrupção migração se a operação de consolidação falhar. Todas as operações de migração, incluindo a operação de consolidação, são idempotent. Por isso, recomendamos que repita a operação após um breve momento. Se continuar a enfrentar um erro, crie uma bilhetes de suporte ou um fórum com a etiqueta ClassicIaaSMigration no nosso [Fórum VM](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**Tenho de ter que comprar outro circuito de encaminhar express se tenho de ter para utilizar IaaS em Gestor de recursos?**

Não. Vamos recentemente ativada [Mover ExpressRoute circuitos de clássico ao modelo de implementação de Gestor de recursos](../expressroute/expressroute-move.md). Não tem de comprar um novo circuito ExpressRoute se já tiver um.

**O que acontece se posso tinha configurado políticas de controlo de acesso baseado em funções para os meus recursos IaaS clássicos?**

Durante a migração, os recursos transformam a partir do clássico para o Gestor de recursos. Por isso, recomendamos que planeia as atualizações de política RBAC que precisam de ocorrer após a migração.

**O que acontece se estou a utilizar o Azure recuperação de sites ou de cópia de segurança do Azure hoje?**

Para migrar o seu Máquina Virtual que estejam ativados para cópia de segurança, consulte o artigo [posso ter cópia de segurança meu VMs clássicos num cofre de cópia de segurança. Agora quiser migrar meu VMs a partir do modo clássico para o modo de Gestor de recursos. Como pode posso fazer cópia de segurança-los num cofre de serviços de recuperação?](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**Posso validar minha subscrição ou recursos para ver se estiverem capazes de migração?**

Sim. Na opção suportada plataforma de migração, o primeiro passo preparar-se para migração é validar se os recursos são capazes de migração. Caso a operação de validar falhar, irá receber mensagens para todos os motivos que não pode ser concluída a migração.

**O que acontece se posso ocorrer um erro de quota para preparar os recursos IaaS para migração?**

Recomendamos que cancelar a sua migração e, em seguida, inicie sessão um pedido de suporte para aumentar as quotas na região onde quer esteja a migrar as VMs. Depois do pedido de quota for aprovado, pode começar a executar os passos de migração novamente.

**Como posso denunciar um problema?**

Publicar o seu problemas e perguntas sobre a migração para o nosso [Fórum VM](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows), com a palavra-chave ClassicIaaSMigration. Recomendamos que publicar todas as suas perguntas no fórum deste. Se tiver um contrato de suporte, está bem-vindo ao iniciar sessão, assim um bilhetes de suporte.

**O que acontece se não como o os nomes dos recursos de que a plataforma escolhida durante a migração?**

Todos os recursos que fornece explicitamente nomes para no modelo de implementação clássica são retidos durante a migração. Em alguns casos, são criados novos recursos. Por exemplo: é criada uma interface de rede para cada VM. Atualmente não suportamos a capacidade para controlar os nomes destes recursos novos criados durante a migração. Inicie sessão seu votos para esta funcionalidade no [Fórum de comentários Azure](http://feedback.azure.com).

* *recebi uma mensagem *"VM está a comunicar o estado de agente geral prontos como não. Por conseguinte, não pode ser migrada a VM. Certifique-se de que o agente VM é a identificação de estado de agente global como pronto"* ou *"o VM contém extensão não está a ser comunicado cujo estado a partir da VM. Por conseguinte, este VM não pode ser migrado."***

Esta mensagem é recebida quando a VM não tenha conectividade de saída à internet. O agente VM utiliza conectividade de saída para alcançar a conta de armazenamento Azure para atualizar o estado de agente em cinco minutos.


## <a name="next-steps"></a>Próximos passos
Agora que compreender a migração de clássicas IaaS recursos para Gestor de recursos, pode começar a migração de recursos.

- [Técnico abrangente vôo picado no suportadas plataforma migração de clássico para o Gestor de recursos do Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Utilizar o PowerShell para migrar IaaS recursos de clássico para o Gestor de recursos do Azure](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Utilizar o clip migrar IaaS recursos de clássico para o Gestor de recursos do Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonar uma máquina virtual clássica para o Gestor de recursos do Azure utilizando scripts de PowerShell de Comunidade](virtual-machines-windows-migration-scripts.md)
