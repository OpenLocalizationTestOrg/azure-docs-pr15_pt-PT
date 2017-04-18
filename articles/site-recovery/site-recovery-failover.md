<properties
    pageTitle="Activação pós-falha no Site recuperação | Microsoft Azure" 
    description="Recuperação de Site Azure coordenadas a replicação, activação pós-falha e recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre activação pós-falha para Azure ou um centro de dados secundário." 
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
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016" 
    ms.author="raynew"/>

# <a name="failover-in-site-recovery"></a>Activação pós-falha na recuperação de sites

O serviço de recuperação de sites do Azure contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou para um centro de dados secundária no local. Para obter uma descrição geral rápida ler [o que é o Azure recuperação de Site?](site-recovery-overview.md)

## <a name="overview"></a>Descrição geral

Este artigo fornece informações e instruções para recuperar (acima e na falta voltar a falhar) máquinas virtuais e servidores físicos que são protegidos com a recuperação de Site. 

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="types-of-failover"></a>Tipos de activação pós-falha

Depois de proteção estiver ativada para máquinas virtuais e servidores físicos e está a replicação pode executar activações pós-falha conforme necessário. Recuperação de sites suporta um número de tipos de activação pós-falha.

**Activação pós-falha** | **Quando executar** | **Detalhes** | **Processo**
---|---|---|---
**Teste activação pós-falha** | Executar para validar a sua estratégia de replicação ou efetuar uma pesquisa de recuperação de falhas | Sem perda de dados ou o tempo de inatividade.<br/><br/>Sem impacto na replicação<br/><br/>Sem impacto no seu ambiente de produção | Iniciar o activação pós-falha<br/><br/>Especificar como máquinas teste serão ligadas ao redes após activação pós-falha<br/><br/>Controlar o progresso no separador **tarefas** . Teste máquinas são criadas e comece a na localização secundária<br/><br/>Azure - ligar à máquina no portal do Azure<br/><br/>Secundário do site - aceder a máquina no mesmo anfitrião e na nuvem<br/><br/>Concluir testes e limpar automaticamente as definições de activação pós-falha de teste.
**Activação pós-falha planeada** | Executar para cumprir requisitos de conformidade<br/><br/>Executar para manutenção planeada<br/><br/>Executar a falha sobre os dados para manter das cargas de trabalho em execução em caso de falhas conhecidos - como uma falha de energia esperado ou Meteorologia extremas relatórios<br/><br/>Executar a reposição de recurso depois activação pós-falha a partir do principal para secundário | Sem perda de dados<br/><br/>Tempo de inatividade é suportado durante o tempo que demora a encerrar a máquina virtual na página principal e coloque-o na localização secundária.<br/><br/>Máquinas virtuais são impacto máquinas destino fica máquinas de origem depois activação pós-falha. | Iniciar o activação pós-falha<br/><br/>Controlar o progresso no separador **tarefas** . São encerrar máquinas de origem<br/><br/>Iniciar o máquinas réplica na localização secundária<br/><br/>Azure - ligar à máquina de réplica no portal do Azure<br/><br/>Secundário do site - aceder a máquina no anfitrião do mesmo e na nuvem mesmo<br/><br/>Consolidar a activação pós-falha
**Não planeado activação pós-falha** | Executar este tipo de activação pós-falha de uma forma reactivar quando um site principal fica inacessível devido a um incidente inesperado, tal como um ataque de vírus ou falha do power <br/><br/> Pode executar um não planeado activação pós-falha pode ser feito, mesmo se o site principal não está disponível. | Perda de dados dependente de definições de frequência de replicação<br/><br/>Os dados serão atualizados em conformidade com a hora da última que foi sincronizado | Iniciar o activação pós-falha<br/><br/>Controlar o progresso no separador **tarefas** . Opcionalmente, tente encerrar máquinas virtuais e sincronizar os dados mais recentes<br/><br/>Iniciar o máquinas réplica na localização secundária<br/><br/>Azure - ligar à máquina de réplica no portal do Azure<br/><br/>Acesso ao site secundário a máquina no anfitrião do mesmo e na nuvem mesmo<br/><br/>Consolidar a activação pós-falha


Os tipos de activações pós-falha que é suportadas dependem do seu cenário de implementação.

**Direcção de activação pós-falha** | **Teste activação pós-falha** | **Activação pós-falha planeada** | **Não planeado activação pós-falha**
---|---|---|---
Primária VMM site para VMM secundário site | Suportada | Suportada | Suportada 
Site VMM secundário ao site VMM principal | Suportada | Suportada | Suportada
Nuvem na nuvem (única VMM server) |  Suportada | Suportada | Suportada
Site VMM para Azure | Suportada | Suportada | Suportada 
Azure ao VMM site | Não suportadas | Suportada | Não suportadas 
Site de Hyper-V para Azure | Suportada | Suportada | Suportada
Azure ao site Hyper-V | Não suportadas | Suportada | Não suportadas
Site de VMware para Azure | Suportados (cenário avançado)<br/><br/> Não suportado (cenário legado) |Não suportadas | Suportada
Servidor físico para Azure | Suportados (cenário avançado)<br/><br/> Não suportado (cenário legado) | Não suportadas | Suportada

## <a name="failover-and-failback"></a>Activação e reposição de recurso

Falha ao longo do máquinas virtuais para um site secundário no local ou para Azure, dependendo da sua implementação. É criada uma máquina que falha ao longo do Azure como uma máquina virtual Azure. Pode falhar ao longo de uma única máquina de virtual ou servidor físico ou um plano de recuperação. Planos de recuperação for constituída por um ou mais encomendou grupos que contêm protegidas máquinas virtuais ou servidores físicos. Estão habituados a orquestrar activação pós-falha de vários máquinas que falharem através de acordo com o grupo se encontram. [Leia mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação. 

Depois de activação pós-falha for concluído e as máquinas estão a trabalhar numa localização secundária tenha em atenção que:

- Se lhe Ocorreu uma falha ao longo do Azure, depois das máquinas activação pós-falha não são protegidos ou replicação na localização principal ou secundária. No Azure as máquinas virtuais são armazenadas no armazenamento de replicadas geo que fornece RDP, mas não replicação.
- Caso tenha gravado uma activação pós-falha não planeado para um site secundário, depois das máquinas activação pós-falha na localização secundária não estão protegidas ou a replicação.
- Caso tenha gravado uma activação pós-falha planeado para um site secundário, após as máquinas activação pós-falha na localização secundária estão protegidos.
 

### <a name="failback-from-secondary-site"></a>Reposição de recurso a partir do site secundário

Reposição de recurso a partir de um site secundário para um principal utiliza o mesmo processo como activação em pós-falha da página principal para secundário. Depois da activação pós-falha a partir do principal ao centro de dados secundário está empenhada e concluída, pode iniciar replicação inversa quando o site principal fica disponível. Replicação inversa inicia a replicação entre o site secundário e a página principal ao sincronizar os dados de variação. Replicação inversa reunindo as máquinas virtuais a um estado protegido, mas o Centro de dados secundário ainda é a localização ativa. Para tornar o site principal para a localização ativa iniciar activação pós-falha planeada de secundário para principal, seguido de replicação inversa outra.

### <a name="failback-from-azure"></a>Reposição de recurso a partir do Azure

Se tiver falhado sobre para Azure máquinas virtuais estão protegidas pelas funcionalidades de RDP Azure para máquinas virtuais. Para tornar o site principal original a localização ativa executar uma activação planeada pós-falha. Pode falhar novamente para a localização original ou para uma localização alternativa se o site original não estiver disponível. Para iniciar a replicação novamente depois de reposição de recurso para a localização principal iniciar uma replicação inversa.

### <a name="failover-considerations"></a>Considerações de activação pós-falha

- **Endereço IP depois de activação pós-falha**— por predefinição uma falha ao longo do computador terá um endereço IP diferente a máquina de origem. Se pretende manter o mesmo IP endereço, consulte: 
    - **Site secundário**— se está a falhar através de um site secundário e pretende manter um endereço IP, [Leia](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) este artigo. Tenha em atenção que pode manter um endereço IP público se o seu ISP suporta-lo.
    - **Azure**— se está a falhar ao longo do Azure pode especificar o endereço IP que pretende atribuir no separador **Configurar** as propriedades de máquina virtual. Não consegue reter um endereço IP público após activação pós-falha ao Azure. Pode manter RFC de 1918 espaços de endereços que são utilizados como endereços internos.
- **Activação pós-falha parcial**— se pretende falhar ao longo da parte de um site em vez de um site inteiro tenha em atenção que: 
    - **Site secundário**— se falhar ao longo da parte de um site principal para um site secundário e que pretende ligar novamente para o site principal, utilize uma ligação VPN do site para o site para se ligar Falha ao longo de aplicações no site secundário para componentes de infraestrutura em execução no site principal. Se uma sub-rede completa falhar ao longo do pode manter o endereço IP máquina virtual. Se falhar ao longo de uma sub-rede parcial não consegue reter o endereço IP máquina virtual porque não podem ser divididas sub-redes entre sites.
    - **Azure**— se falhar ao longo de um site parcial para Azure e pretende ligar novamente para o site principal, pode utilizar um site para o site VPN para ligar uma falha ao longo de aplicação no Azure para componentes de infraestrutura em execução no site principal. Tenha em atenção que, se o falha de sub-rede completa sobre o pode manter o endereço IP máquina virtual. Se falhar ao longo de uma sub-rede parcial não consegue reter o endereço IP máquina virtual porque não podem ser divididas sub-redes entre sites.
 
- **Letra da unidade**— se pretende manter a letra da unidade em máquinas virtuais após activação pós-falha pode definir a política de SAN para a máquina virtual **no**. Máquina virtual discos ficarem online automaticamente. [Leia mais](https://technet.microsoft.com/library/gg252636.aspx).
- **Encaminhar os pedidos de cliente**— recuperação de sites funciona com o Gestor de tráfego Azure para encaminhar os pedidos de cliente à aplicação após activação pós-falha.




## <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

Quando executa um teste activação pós-falha ser-lhe-á pedido para selecionar as definições de rede para máquinas réplica de teste. Tem um número de opções.  

**Opção de activação pós-falha de teste** | **Descrição** | **Verificação de activação pós-falha** | **Detalhes**
---|---|---|---
**Sobre a Azure falhar — sem rede** | Não selecione um alvo de rede Azure | Verificações de activação pós-falha testar inícios de máquina virtual como esperado no Azure | Todas as máquinas virtuais teste num plano de recuperar são adicionadas num serviço de nuvem único e pode ligar-se entre si<br/><br/>Máquinas não estão ligadas a uma rede Azure após activação pós-falha.<br/><br/>Os utilizadores podem ligar os computadores de teste com um endereço IP público
**Sobre a Azure falhar — com a rede** | Selecione um alvo de rede Azure | Activação pós-falha verifica que teste máquinas estão ligadas à rede | Crie uma rede Azure que tem isolados da sua rede de produção Azure e configurar a infraestrutura de replicadas máquina virtual para funcionar como esperado.<br/><br/>A sub-rede da máquina virtual teste é baseada no sub-rede no qual o Falha ao longo de máquina virtual é esperada para ligar à caso ocorra um activação planeado ou não planeado pós-falha.
**Sobre a um site VMM secundário falhar — sem rede** | Não selecione uma rede VM | Activação pós-falha verifica que são criadas máquinas de teste.<br/><br/>O teste virtual machine será criado no anfitrião do mesmo como o anfitrião no qual existe máquina réplica virtual. Não é adicionada a na nuvem na qual a máquina de virtual réplica está localizada. | <p>A falha ao longo de máquina não estar ligada a uma rede.<br/><br/>Computador pode ser ligado a uma rede VM depois de ter sido criado
**Sobre a um site VMM secundário falhar — com a rede** | Selecione uma rede VM existente um | Activação pós-falha verifica que são criadas máquinas virtuais | O teste virtual machine será criado no anfitrião do mesmo como o anfitrião no qual existe máquina réplica virtual. Não é adicionada a na nuvem na qual a máquina de virtual réplica está localizada.<br/><br/>Criar uma rede VM que tem isoladas da sua rede de produção<br/><br/>Se estiver a utilizar uma rede baseada em VLAN Recomendamos que criar uma rede lógica separada (não utilizada na produção) no VMM para esse efeito. Esta rede lógica é utilizada para criar redes VM para teste activação pós-falha.<br/><br/>A rede lógica deve ser associada com pelo menos um dos adaptadores de rede do todos os servidores de Hyper-V aloja máquinas virtuais.<br/><br/>Para redes lógicas VLAN, os sites de rede que adiciona à rede lógica devem ser isolados.<br/><br/>Se estiver a utilizar uma rede lógica com base no Virtualização de rede do Windows, o Azure recuperação de Site cria automaticamente redes VM isoladas.
**Sobre a um site VMM secundário falhar — criar uma rede** | Uma rede de teste temporário será criada automaticamente com base na definição que especificou na **Rede lógica** e os sites de rede relacionados | Activação pós-falha verifica que são criadas máquinas virtuais | Utilize esta opção se o plano de recuperação utiliza mais do que uma rede VM. Se estiver a utilizar redes Virtualização de rede do Windows, esta opção pode criar automaticamente redes VM com as mesmas definições (sub-redes e conjuntos de endereços IP) na rede da máquina réplica virtual. Estas redes VM são automaticamente limpa depois do teste activação pós-falha está concluída.</p><p>O teste virtual machine será criado no anfitrião do mesmo como o anfitrião no qual existe máquina réplica virtual. Não é adicionada a na nuvem na qual a máquina de virtual réplica está localizada.

>[AZURE.NOTE] O endereço IP atribuído a uma máquina virtual durante o ensaio activação pós-falha é o mesmo que o endereço IP que pretende receber quando a fazer uma planeado ou não planeado activação pós-falha (partindo do princípio de que o endereço IP está disponível na rede de activação pós-falha de teste. Se o mesmo endereço IP não estiver disponível na rede activação pós-falha teste máquina virtual receberá outro endereço IP disponível na rede de activação pós-falha de teste.



### <a name="run-a-test-failover-from-on-premises-to-azure"></a>Executar um teste activação pós-falha a partir no local para Azure

Este procedimento descreve como executar um teste activação pós-falha para um plano de recuperação. Em alternativa pode executar o activação pós-falha para uma única máquina no separador **máquinas virtuais** .

1. Selecione a **Recuperação planos** > *recoveryplan_name*. Clique em **activação pós-falha** > **teste activação pós-falha**.
2. Na página **Activação pós-confirmar teste falha** , especifique como máquinas réplica serão ligadas a uma rede Azure após activação pós-falha.
3. Se está a falhar ao longo do Azure e encriptação de dados está ativada para a nuvem, selecione o certificado que foi emitido quando activou encriptação durante a instalação do fornecedor de dados na **Chave de encriptação** . 
4. Controlar o progresso de activação pós-falha no separador **tarefas** . Deverá conseguir ver a máquina de réplica teste no portal do Azure.
5. Pode aceder máquinas réplica no Azure a partir do seu site no local iniciar uma ligação RDP à máquina virtual. porta 3389 terá de ser aberto no ponto final para a máquina virtual.
5. Quanto terminar, quando o activação pós-falha atinge a fase de **testes concluída** , clique em **Teste concluído** para concluir.
5. Nas **notas** gravar e guardar quaisquer observações associadas a activação de teste pós-falha.
8. Clique em **a activação de teste pós-falha está concluída** para limpar automaticamente o ambiente de teste. Após esta operação estiver concluída a activação de teste pós-falha irá mostrar o estado de**omplete** C.

> [AZURE.NOTE] Se um teste activação pós-falha persistir durante mais de duas semanas vai ser concluída por força. Qualquer elementos ou máquinas virtuais criadas automaticamente durante a mudança de teste serão eliminadas.
  

### <a name="run-a-test-failover-from-a-primary-on-premises-site-to-a-secondary-on-premises-site"></a>Executar um teste activação pós-falha a partir de um site principal no local para um site secundário no local

Terá de fazer um número de coisas para executar uma falha na ligação teste, incluindo efetuar uma cópia do controlador de domínio e colocar os servidores de teste DHCP e DNS no seu ambiente de teste. Pode fazê-lo de duas formas:

- Se pretender executar um teste activação pós-falha a utilizar uma rede existente, prepare, do Active Directory, DHCP e DNS nessa rede.
- Se pretender executar um teste activação pós-falha utilizando a opção para criar redes VM automaticamente, adicione manual passo antes de 1 de grupo no plano de recuperação que vai utilizar para o teste activação pós-falha e, em seguida, adicione os recursos de infraestrutura à rede criada automaticamente antes de executar o teste activação pós-falha.

#### <a name="things-to-note"></a>Aspetos a nota

- Quando de replicadas para um site secundário, o tipo de rede utilizada pelo computador réplica não necessite de corresponder ao tipo de rede lógico utilizada para activação pós-falha de teste, mas algumas combinações de poderão não funcionar. Se a réplica utiliza DHCP e com base em VLAN isolamento, a rede VM para a réplica não precisar de um conjunto de endereço IP estático. Para que utilizar o Virtualização de rede do Windows para o teste activação pós-falha não seria trabalhar porque não conjuntos de endereços estão disponíveis. Além disso teste activação pós-falha não irá funcionar se a rede de réplica estiver sem isolamento e a rede de teste for Virtualização de rede do Windows. Isto acontece porque a rede de isolamento sem não tem as sub-redes necessárias para criar uma rede Virtualização de rede do Windows.
- A forma na qual réplica máquinas virtuais estão ligadas a mapeado redes VM após activação pós-falha depende como a rede VM está configurada na consola do VMM:
    - **Rede VM configurada sem isolamento nem isolamento VLAN**— se DHCP está definido para a rede VM, máquina virtual réplica será ligada ao ID da VLAN utilizando as definições que são especificadas para o site de rede na rede lógico associado. A máquina virtual irão receber o seu endereço IP do servidor DHCP disponível. Não precisa de um conjunto de endereço IP estático definido para a rede VM de destino. Se um conjunto de endereço IP estático é utilizado para a rede VM máquina virtual réplica irá estar ligada ao ID da VLAN utilizando as definições que são especificadas para o site de rede na rede lógico associado. A máquina virtual irão receber o seu endereço IP do conjunto de definidos para a rede VM. Se um conjunto de endereço IP estático não é definido na rede VM de destino, a atribuição de endereços IP irá falhar. O conjunto de endereços IP deve ser criado em servidores VMM de origem e de destino que vai utilizar para protecção e recuperação.
    - **Rede VM com o Windows de rede virtualização**— se uma rede VM está configurada com esta definição deve ser definido um conjunto de estático para a rede VM de destino, independentemente de se a rede VM de origem está configurada para utilizar DHCP ou um endereço IP estático conjunto de endereços. Se definir DHCP, o servidor VMM de destino irá funcionar como um servidor DHCP e fornecer um endereço IP do conjunto que está definido para a rede VM de destino. Se a utilização de um conjunto de endereço IP estático é definida para o servidor de origem, o servidor VMM de destino atribuir um endereço IP do conjunto. Em ambos os casos, a atribuição de endereços IP falhará se não estiver definido um conjunto de endereço IP estático.

#### <a name="run-test"></a>Executar teste

Este procedimento descreve como executar um teste activação pós-falha para um plano de recuperação. Em alternativa pode executar o activação pós-falha para uma única máquina virtual ou servidor físico no separador **máquinas virtuais** .

1. Selecione a **Recuperação planos** > *recoveryplan_name*. Clique em **activação pós-falha** > **teste activação pós-falha**.
2. Na página **Confirmar testar activação pós-falha** , especifique como máquinas virtuais deve estar ligadas a redes após o teste activação pós-falha.
3. Controlar o progresso de activação pós-falha no separador **tarefas** . Quando a activação pós-falha atinge a fase de** testes concluída** , clique em **Testar concluído** para concluir o teste activação pós-falha.
4. Clique em **notas** para gravar e guardar quaisquer observações associadas a activação de teste pós-falha.
4. Após a conclusão Certifique-se de que as máquinas virtuais inicie com êxito.
5. Depois de verificar que máquinas virtuais inicie com êxito, execute o teste activação pós-falha de limpar o ambiente do isoladas. Se optar por criar redes VM automaticamente, limpeza elimina todos os máquinas virtuais o teste e testar redes.

> [AZURE.NOTE] Se um teste activação pós-falha persistir durante mais de duas semanas vai ser concluída por força. Qualquer elementos ou máquinas virtuais criadas automaticamente durante a mudança de teste serão eliminadas.


#### <a name="prepare-dhcp"></a>Preparar DHCP

Se as máquinas virtuais, normalmente utilizar o teste activação pós-falha DHCP, um servidor DHCP teste deve ser criado dentro da rede isolada que é criada para teste activação pós-falha.


### <a name="prepare-active-directory"></a>Preparar o Active Directory
Para executar um teste activação pós-falha para testes de aplicação, terá uma cópia do ambiente do Active Directory de produção no seu ambiente de teste. Aceda a secção [Testar activação pós-falha considerações para o active directory](site-recovery-active-directory.md#considerations-for-test-failover) para obter mais detalhes. 


### <a name="prepare-dns"></a>Preparar o DNS

Prepare um servidor de DNS para o teste activação pós-falha da seguinte forma:

- **DHCP**— se máquinas virtuais utilizar DHCP, o endereço IP do teste DNS deve ser atualizado no servidor DHCP de teste. Se estiver a utilizar um tipo de rede de Virtualização de rede do Windows, o servidor VMM age como o servidor DHCP. Por conseguinte, o endereço IP do DNS deve ser atualizado na rede de activação pós-falha de teste. Neste caso, as máquinas virtuais registará próprios para o servidor DNS relevantes.
- **Endereço estático**— se máquinas virtuais utilizar um endereço IP estático, o endereço IP do servidor DNS de teste deve ser atualizado na rede de activação pós-falha de teste. Poderá ter de atualizar o DNS com o endereço IP do máquinas virtuais teste. Pode utilizar o seguinte script de exemplo para o efeito: 

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-planned-failover-primary-to-secondary"></a>Executar (principal para secundário) activação pós-falha planeada

 Este procedimento descreve como executar activação pós-falha planeado para um plano de recuperação. Em alternativa pode executar o activação pós-falha para uma única máquina de virtual no separador **máquinas virtuais** .

1. Antes de começar, certifique-se de todas as máquinas virtuais que pretende falhar ao longo do tiver concluído a replicação inicial.
2. Selecione a **Recuperação planos** > *recoveryplan_name*. Clique em **activação pós-falha** > **planeado activação pós-falha**. 
3. Na página **Confirmar planeado activação pós-falha **, selecione as localizações de origem e destino. Tenha em atenção a direção de activação pós-falha.

    - Se tiver funcionado activações pós-falha anterior como esperado e todos os servidores de máquina virtual estão localizados numa localização de origem ou de alvos, os detalhes de direção activação pós-falha destinam-se apenas as informações. 
    - Se máquinas virtuais estiver ativas localizações de origem e de destino, é apresentada no botão **Alterar a direcção** . Utilize este botão para alterar e especificar a direção na qual deve ocorrer a activação pós-falha.

5. Se está a falhar ao longo do Azure e encriptação de dados está ativada para a nuvem, selecione o certificado que foi emitido quando activou encriptação de dados durante a instalação do fornecedor no servidor VMM na **Chave de encriptação** . 
6. Quando começa um activação planeada pós-falha o primeiro passo é encerrar as máquinas virtuais para se certificar de sem perda de dados. Pode seguir o progresso de activação pós-falha no separador **tarefas** . Se ocorre um erro no activação pós-falha (quer seja uma máquina virtual ou um script que vem incluído no plano de recuperação), deixa de planeada activação pós-falha de um plano de recuperação. Pode iniciar o activação pós-falha novamente.
8. Após a criação de máquinas virtuais de réplica se encontram uma consolidação pendentes estado. Clique em **consolidar** para consolidar a activação pós-falha. 
9. Depois da replicação é conclua o máquinas virtuais de arranque na localização secundário. 

## <a name="run-an-unplanned-failover"></a>Executar uma activação não planeado pós-falha

Este procedimento descreve como executar uma activação pós-falha não planeado para um plano de recuperação. Em alternativa pode executar o activação pós-falha para uma única máquina virtual ou servidor físico no separador **máquinas virtuais** .

1. Selecione a **Recuperação planos** > *recoveryplan_name*. Clique em **activação pós-falha** > **activação pós-falha não planeado**. 
3. Na página **Confirmar não planeado activação de pós-falha **, selecione as localizações de origem e destino. Tenha em atenção a direção de activação pós-falha.

    - Se tiver funcionado activações pós-falha anterior como esperado e todos os servidores de máquina virtual estão localizados numa localização de origem ou de alvos, os detalhes de direção activação pós-falha destinam-se apenas as informações. 
    - Se máquinas virtuais estiver ativas localizações de origem e de destino, é apresentada no botão **Alterar a direcção** . Utilize este botão para alterar e especificar a direção na qual deve ocorrer a activação pós-falha.

4. Se está a falhar ao longo do Azure e encriptação de dados está ativada para a nuvem, selecione o certificado que foi emitido quando activou encriptação de dados durante a instalação do fornecedor no servidor VMM na **Chave de encriptação** . 
5. Selecione **Encerrar máquinas virtuais e sincronizar os dados mais recentes** para especificar que deverá tentar recuperação de sites encerrar as máquinas virtuais protegidas e sincronizar os dados para que a versão mais recente dos dados serão Ocorreu uma falha ao longo do. Se não selecionar esta opção ou a tentativa de não ter êxito a activação pós-falha serão do ponto de recuperação disponíveis mais recente para a máquina virtual.
6. Pode seguir o progresso de activação pós-falha no separador **tarefas** . Tenha em atenção que mesmo se ocorrerem erros durante uma activação de pós-falha não planeada, o plano de recuperação é executada até estar concluída.
7. Após a falha na ligação, as máquinas virtuais são num estado **consolidar pendentes** . Clique em **consolidar** para consolidar a activação pós-falha.
8. Se configurar replicação para utilizar vários pontos de recuperação, no ponto de recuperação de alteração pode selecionar para utilizar como ponto de recuperação que não seja o mais recente (mais recente é utilizado por predefinição). Depois de que a consolidação adicionais pontos de recuperação serão removidos.
9. Depois de concluída a replicação as máquinas virtuais de arranque e estiver a executar o na localização secundária. No entanto que não sejam replicação ou protegida. Quando o site principal está disponível novamente com infraestrutura subjacente do mesmo, clique em **Criar uma réplica da inversa** para começar a replicação inversa. Isto garante que todos os dados é replicada novamente para o site principal e que a máquina virtual está pronta para activação pós-falha novamente. Inverter a replicação após uma activação não planeado pós-falha como uma sobrecarga de transferência de dados. A transferência irá utilizar o mesmo método que esteja configurado para as definições de replicação inicial na nuvem.

## <a name="failback-from-secondary-to-primary"></a>Reposição de recurso a partir do secundário para principal

 Depois de falha na ligação a partir da principal localização secundário, replicadas máquinas virtuais não são protegidas por recuperação de Site e, a localização secundária agora está a agir como a página principal. Siga estes procedimentos para falhar novamente para o site principal original. Este procedimento descreve como executar activação pós-falha planeado para um plano de recuperação. Em alternativa pode executar o activação pós-falha para uma única máquina de virtual no separador **máquinas virtuais** .

1. Selecione a **Recuperação planos** > *recoveryplan_name*. Clique em **activação pós-falha** > **planeado activação pós-falha**.
2. Na página **Confirmar planeado activação pós-falha **, selecione as localizações de origem e destino. Tenha em atenção a direção de activação pós-falha. Se tiver funcionado a activação pós-falha a partir do principal como esperado e todas as máquinas virtuais se encontram na localização secundária que destina-se para apenas as informações.
3. Se está a falhar novamente a partir do Azure selecione definições de **Sincronização de dados**:

    - **Sincronizar dados antes de activação pós-falha (Synchonize delta apenas as alterações)**— esta opção minimiza tempo de inatividade para máquinas virtuais como-sincroniza sem encerrá-los. Que faz o seguinte procedimento:
        - Fase 1: Tira instantâneo da máquina virtual no Azure e copiá-lo para o anfitrião o Hyper-V no local. Computador continua a ser executada em Azure.
        - Fase 2: Encerra máquina virtual no Azure para que não existem novas alterações ocorrerem aí. O conjunto final das alterações são transferidas para o servidor no local e a máquina de virtual no local for iniciada para cima.
    

    - **Sincronizar dados durante apenas activação pós-falha (transferir completo)**— Utilize esta opção se tiver sido executar no Azure muito tempo. Esta opção é mais rápida porque é esperado que foi alterado a maior parte do disco e podemos não quiser perder tempo no cálculo da soma de verificação. Executa uma transferência do disco. Também é útil quando foi eliminada máquina virtual no local.
    
    > [AZURE.NOTE] Recomendamos que utilize esta opção se tiver sido executar Azure para o tempo (um mês ou mais) ou a VM no local foi eliminada. Esta opção não efetuar quaisquer cálculos de soma de verificação.
    
5. Se está a falhar ao longo do Azure e encriptação de dados está ativada para a nuvem, selecione o certificado que foi emitido quando activou encriptação de dados durante a instalação do fornecedor no servidor VMM na **Chave de encriptação** . 
5. Por predefinição é utilizado o último ponto de recuperação, mas o **Ponto de recuperação de alteração** pode especificar um ponto de recuperação diferente. 
6. Clique em marca de verificação para iniciar a reposição de recurso.  Pode seguir o progresso de activação pós-falha no separador **tarefas** . 
7. f que selecionou a opção para sincronizar os dados antes da falha na ligação, assim que a sincronização inicial dos dados estiver concluída e estiver pronto para encerrar as máquinas virtuais no Azure, clique em **tarefas**  >  <planned failover job name> **Activação pós-falha concluída**. Isto encerra a Azure máquina, transfere as alterações mais recentes para a máquina de virtual no local e inicia-lo.
8. Agora pode iniciar sessão no computador do virtual para validar está disponível como esperado. 
9. A máquina virtual está numa consolidação pendentes estado. Clique em **consolidar** para consolidar a activação pós-falha.
10. Agora para concluir a reposição de recurso clique em **Criar uma réplica reverter** para começar a proteger a máquina virtual no site principal.



## <a name="failback-to-an-alternate-location"></a>Reposição de recurso para uma localização alternativa

Caso tenha implementado proteção entre um [site de Hyper-V e Azure](site-recovery-hyper-v-site-to-azure.md) tem de capacidade de reposição de recurso a partir do Azure para uma alternativa no local localização. Isto é útil se precisar de configurar o novo hardware no local. Eis como pode fazê-lo.

1. Se estiver a configurar novo hardware instale o Windows Server 2012 R2 e a função Hyper-V no servidor.
2. Crie um parâmetro de rede virtual com o mesmo nome que tinham no servidor original.
3. Selecionar **Itens protegida** -> **Grupo proteção**  ->  <ProtectionGroupName>  ->  <VirtualMachineName> que pretende voltar a falhar e selecione **Planeado activação pós-falha**.
4. Na **Confirmar planeado activação pós-falha** selecione **máquina criar no local virtual se não existir**. 
5. Em **Nome do anfitrião** selecione o servidor de anfitrião Hyper-V novo no qual pretende colocar a máquina virtual.
6. Na sincronização de dados, que recomendamos selecione a opção **sincronizar os dados antes da activação pós-falha**. Tempo de inatividade para máquinas virtuais Isto minimiza como-sincroniza sem encerrá-los. Que faz o seguinte procedimento:

    - Fase 1: Tira instantâneo da máquina virtual no Azure e copiá-lo para o anfitrião o Hyper-V no local. Computador continua a ser executada em Azure.
    - Fase 2: Encerra máquina virtual no Azure para que não existem novas alterações ocorrerem aí. O conjunto final das alterações são transferidas para o servidor no local e a máquina de virtual no local for iniciada para cima.
    
7. Clique em marca de verificação para começar a activação pós-falha (reposição de recurso).
8. Depois de termina a sincronização inicial e estiver pronto para encerrar a máquina virtual no Azure, clique em **tarefas** > <planned failover job> > **Activação pós-falha concluída**. Esta encerra a Azure máquina, transfere as alterações mais recentes para a máquina de virtual no local e inicia-lo.
9. Pode iniciar sessão no computador de virtual no local para confirmar que tudo está a funcionar como esperado. Em seguida, clique em **consolidar** para concluir a activação pós-falha.
10. Clique em **Criar uma réplica reverter** para começar a proteger a máquina de virtual no local.

    >[AZURE.NOTE] Se cancelar a tarefa de reposição de recurso enquanto ele está num passo de sincronização de dados, a VM no local será num Estado estão danificados. Isto acontece porque cópias de sincronização de dados, os dados mais recentes do Azure VM discos aos discos de dados no local e até à sincronização for concluída, o disco de dados podem não estar num estado consistente. Se a VM no local é iniciada após a sincronização de dados foi cancelada, não poderá arrancar. Acionar novamente activação pós-falha para concluir a sincronização de dados.
 
