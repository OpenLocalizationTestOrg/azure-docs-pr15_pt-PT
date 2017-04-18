<properties 
   pageTitle="Melhores práticas para matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve os procedimentos recomendados para implementar e gerir a matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-best-practices"></a>Matriz Virtual StorSimple melhores práticas

## <a name="overview"></a>Descrição geral

Matriz virtuais do Microsoft Azure StorSimple é uma solução de armazenamento integrada gere armazenamento tarefas entre um dispositivo de virtual no local a executar numa hipervisor e armazenamento em nuvem Microsoft Azure. Matriz Virtual StorSimple é uma alternativa eficiente, rentável à matriz física 8000 série. A matriz virtual pode ser executados em sua infraestrutura de hipervisor existente, suporta o iSCSI e os protocolos SMB e é bem adequada para cenários do office remote office/ramo. Para obter mais informações sobre as soluções StorSimple, consulte [Descrição geral do Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Este artigo abrange as melhores práticas implementadas durante a configuração inicial, implementação e gestão da matriz StorSimple Virtual. Estas práticas recomendadas fornecem validadas diretrizes para a configuração e gestão do seu matriz virtual. Este artigo é orientado para os administradores de TI que implementar e gerir as matrizes virtuais no respetivos centros de dados.

Recomendamos consultar periódico as melhores práticas para ajudar a garantir o que seu dispositivo ainda se encontra na conformidade quando forem efetuadas alterações ao fluxo de configuração ou operação. Que encontrar problemas durante a execução estes procedimentos recomendados na sua matriz virtual, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência.

## <a name="configuration-best-practices"></a>Melhores práticas de configuração 

Estas práticas recomendadas abrangem as diretrizes que precisam de ser seguidos durante a configuração inicial e implementação das matrizes virtuais. Estas práticas recomendadas incluem os relacionados com o aprovisionamento de máquina virtual, definições de política de grupo, redimensionamento, a configurar a criação de redes, a configurar contas de armazenamento e criar partilhas e volumes para a matriz virtual. 

### <a name="provisioning"></a>Aprovisionamento 

Matriz Virtual StorSimple é uma máquina de virtual (VM) aprovisionada o hipervisor (Hyper-V ou VMware) do seu servidor de anfitrião. Quando aprovisiona o virtual machine, certifique-se de que o seu anfitrião é capaz a dedicar recursos suficientes. Para obter mais informações, consulte [requisitos mínimos dos recursos](storsimple-ova-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements) de aprovisionamento de uma matriz. 

Implementa as melhores práticas seguintes quando Aprovisiona a matriz virtual:


|                        | Hyper-V                                                                                                                                        | VMware                                                                                                               |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| **Tipo de máquina virtual**   | **Geração de 2** VM para utilização com o Windows Server 2012 ou posterior e de uma imagem de *.vhdx* . <br></br> **Geração de 1** VM para utilização com um Windows Server 2008 ou posterior e de uma imagem *. vhd* .                                                                                                              | Utilize máquina virtual versão 8-11 com imagem *.vmdk* .                                                                      |
| **Tipo de memória**            | Configure como **memória estática**. <br></br> Não utilize a opção de **memória dinâmica** .            |                                                    |
| **Tipo de dados de disco**         | Aprovisionar como **dinamicamente expandir**.<br></br> **Tamanho fixo** demora muito tempo. <br></br> Não utilize a opção **de diferenciação** .                                                                                                                   | Utilize a opção de **aprovisionar fina** .                                                                                      |
| **Modificação de disco de dados** | Não é permitido expansão ou diminuir. Resultados de uma tentativa para fazê-lo na perda de todos os dados locais no dispositivo.                       | Não é permitido expansão ou diminuir. Resultados de uma tentativa para fazê-lo na perda de todos os dados locais no dispositivo. |

### <a name="sizing"></a>Redimensionamento

Quando a sua matriz de Virtual StorSimple de redimensionamento, considere os seguintes fatores:

- Reserva local para volumes ou partilhas. Cerca de 12% do espaço é reservado na camada local para cada volume em camadas aprovisionada ou a partilhar. Aproximadamente 10% do espaço também é reservado para um volume afixado localmente para o sistema de ficheiros.
- Instantâneo de custos gerais. Aproximadamente espaço de 15% na camada local é reservado para instantâneos.
- Necessidade de restaura. Se efetuar restaurar como uma nova operação, redimensionamento deve ter em consideração o espaço necessário para restaurar. O restauro é concluído para uma partilha ou o volume do mesmo tamanho ou maior.
- Algumas memória intermédia deve ser imputada para qualquer inesperado crescimento.

Basear os fatores anteriores, os requisitos de redimensionamento podem ser representados pela seguinte equação:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`


Os exemplos seguintes mostram como pode dimensionar uma matriz virtual com base nos seus requisitos.

#### <a name="example-1"></a>Exemplo 1:
Na sua matriz virtual, que pretende ser possível 

- Aprovisionar um 2 TB em camadas volume ou partilhar.
- Aprovisionar um 1 TB em camadas volume ou partilhar.
- Aprovisionar um 300 GB de volume localmente afixada ou partilhar.


Da volumes ou partilhas, diga-nos calcule os requisitos de espaço na camada local. 

Em primeiro lugar, para cada volume em camadas/partilhar, reserva local seria igual a 12% do tamanho do volume/partilhar. A partilha/volume localmente afixada, reserva local é 10% do tamanho localmente afixada volume/partilhar (para além do tamanho aprovisionado). Neste exemplo, é necessário

- Reserva local 240 GB (para uma 2 TB camadas volume/partilhar)
- Reserva local 120 GB (para uma 1 TB camadas volume/partilhar)
- 330 GB para volume localmente afixada ou partilhar (adicionar 10% de reserva local para o tamanho de 300 GB aprovisionado)

O espaço total necessário na camada local até ao momento é: 240 GB + 120 GB + 330 GB = 690 GB.

Em segundo lugar, é necessário, pelo menos, quantidade de espaço na camada local como a reserva única maior. Este valor extra é utilizado no caso de precisa de restaurar a partir de um instantâneo da nuvem. Neste exemplo, a maior reserva local é 330 GB (incluindo reserva para sistema de ficheiros), para que seria necessário adicionar que para os 690 GB: 690 GB + 330 GB = 1020 GB.
Se é executada subsequentes restaura adicionais, podemos pode sempre libertar espaço desde a operação de restaurar anterior.

Em terceiro lugar, é necessário 15% do total espaço local até ao momento para armazenar instantâneos locais, para que está disponível apenas 85% da mesma. Neste exemplo, que seria à volta de 1020 GB = 0.85&ast;dados aprovisionados disco TB. Sim, o disco de dados aprovisionada seria (1020&ast;(1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (arredondamento para quartil mais próximo)

Tendo como factor crescimento inesperado e restaura nova, deverá aprovisionar um disco local à volta de 1,25-1,5 TB.

> [AZURE.NOTE] Recomendamos também que o disco local pequenos está aprovisionado. Esta recomendação é porque o espaço de restaurar apenas é necessária para restaurar os dados que seja mais de cinco dias. Recuperação de ao nível do item permite-lhe restaurar os dados para os últimos cinco dias sem necessidade do espaço extra para restaurar.

#### <a name="example-2"></a>Exemplo 2: 
Na sua matriz virtual, que pretende ser possível 

- Aprovisionar um 2 TB camadas volume
- Aprovisionar um volume de 300 GB afixado localmente

Com base em 12% de reserva espaço local para volumes/partilhas em camadas e 10% para localmente afixadas volumes/partilhas, precisamos

- Reserva local 240 GB (para 2 TB camadas volume/partilhar)
- 330 GB para volume localmente afixada ou partilhar (adicionar 10% de reserva local para o espaço de 300 GB aprovisionado)

Espaço total necessário na camada local é: 240 GB + 330 GB = 570 GB

O espaço local mínimo necessário para restaurar os é 330 GB. 

15% do total do seu disco é utilizada para armazenar instantâneos para que 0.85 só está disponível. Portanto, é o tamanho do disco (900&ast;(1/0.85)) = 1.06 TB ~ 1,25 TB (arredondamento para quartil mais próximo) 

Tendo como factor qualquer crescimento inesperado, pode aprovisionar um disco local 1,25-1,5 TB.


### <a name="group-policy"></a>Política de grupo

Política de grupo é uma infraestrutura que lhe permite-lhe implementar configurações específicas para utilizadores e computadores. Definições de política de grupo estão contidas num objectos de política de grupo (GPO), que estão ligados aos seguintes contentores de serviços de domínio do Active Directory (AD DS): sites, domínios ou unidades organizacionais (ou). 

Se a matriz virtual estiver façam parte de um domínio, GPO pode ser aplicado à mesma. Estas GPO podem instalar aplicações como um software de antivírus que pode um impacto adverso na operação da matriz StorSimple Virtual.

Por conseguinte, recomendamos que:

-   Certifique-se de que o seu matriz virtual está na sua própria unidade organizacional (OU) para o Active Directory. 

-   Certifique-se de que nenhuma objectos de política de grupo (GPO) são aplicados a sua matriz virtual. Pode bloquear a herança para se certificar de que a matriz virtual (nó subordinado) não automaticamente herdam algum GPO do principal. Para obter mais informações, aceda a [Bloquear a herança](https://technet.microsoft.com/library/cc731076.aspx).


### <a name="networking"></a>Funcionamento em rede

A configuração de rede para o seu matriz virtual é feita através da IU da web local. Uma interface de rede virtual for ativada através de hipervisor na qual a matriz virtual está aprovisionada. Utilize a página de [Definições de rede](storsimple-ova-deploy3-fs-setup.md) para configurar o endereço IP da interface de rede virtual, sub-rede e gateway.  Também pode configurar as principal e secundário servidor DNS, definições de tempo e as definições de proxy opcional para o seu dispositivo. A maior parte da configuração de rede é uma configuração única. Reveja os [requisitos da rede StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implementar a matriz virtual.

Quando implementar o seu matriz virtual, recomendamos que siga estas melhores práticas:

-   Certifique-se de que a rede em que a matriz virtual é implementada sempre tem a capacidade a dedicar a largura de banda de Mbps Internet 5 (ou mais). 

    -   Necessidade de largura de banda de Internet varia dependendo do seu características de carga de trabalho e a taxa de alteração de dados.

    -   A alteração de dados que poderá ser resolvida é proporcional diretamente a largura de banda da Internet. Por exemplo quando efetuar uma cópia de segurança, uma largura de banda Mbps 5 pode acomodar uma alteração de dados de cerca de 18 GB no 8 horas. Com quatro vezes mais largura de banda (20 Mbps), pode processar quatro vezes mais alteração de dados (72 GB). 

-   Certifique-se à Internet está sempre disponível. Esporádica ou não fiáveis ligações à Internet para os dispositivos poderão resultar na perda de acesso aos dados na nuvem e poderá resultar numa configuração não suportada.

-   Se planeia implementar o seu dispositivo, como um servidor iSCSI: 
    -   Recomendamos que desativar a opção **automaticamente um endereço IP obter** (DHCP). 
    -   Configure endereços IP estáticos. Tem de configurar uma principal e um servidor DNS secundário.

    -   Se definir várias interfaces de rede no seu virtual de matriz, apenas a primeira interface de rede (por predefinição, esta interface é **Ethernet**) pode chegar a nuvem. Para controlar o tipo de tráfego, pode criar várias interfaces virtual de rede no seu matriz virtual (configurada como um servidor iSCSI) e ligar as interfaces a sub-redes diferentes.

-   Optimizar a largura de banda do nuvem apenas (utilizado pela matriz virtual), configurar a limitação no router ou a firewall. Se definir a limitação no seu hipervisor, irá optimizar a todos os protocolos incluindo iSCSI e SMB em vez de apenas a largura de banda na nuvem. 

-   Certifique-se que a sincronização de hora para hypervisors é activado. Se utilizar o Hyper-V, selecionar a matriz virtual no Gestor de Hyper-V, aceda a **definições &gt; serviços de integração de**e certifique-se de que a **sincronização de hora** está selecionada.

### <a name="storage-accounts"></a>Contas de armazenamento

Matriz Virtual StorSimple podem ser associado com uma conta de armazenamento única. Esta conta de armazenamento dever-se uma conta de armazenamento gerados automaticamente, uma conta na mesma subscrição do serviço, ou relacionados com uma conta de armazenamento para outra subscrição. Para obter mais informações, consulte o artigo como [Gerir contas de armazenamento para sua matriz virtual](storsimple-ova-manage-storage-accounts.md).

Utilize as seguintes recomendações para contas de armazenamento associadas a sua matriz virtual.

-   Quando a ligar a várias matrizes virtuais com uma conta de armazenamento única, factor na capacidade máxima (64 TB) para uma matriz virtual e o tamanho máximo (500 TB) para uma conta de armazenamento. Este procedimento limita o número de matrizes virtuais inteiro que podem ser associados essa conta de armazenamento para cerca de 7.

-   Quando criar uma nova conta de armazenamento
    -   Recomendamos que cria-lo na região mais próximo do escritório office remote/ramo onde a sua matriz de Virtual StorSimple é implementado para minimizar latências.

    -   Levar Lembre-se de que não é possível mover uma conta de armazenamento em diferentes regiões. Também não pode mover um serviço entre subscrições.

    -   Utilize uma conta de armazenamento implementa redundância entre os centros de dados. Geo Redundant armazenamento (GRS), zona redundantes armazenamento (ZRS) e localmente redundantes armazenamento (LRS) são todas suportadas para utilização com o seu matriz virtual. Para obter mais informações sobre os diferentes tipos de contas de armazenamento, aceda a [replicação de armazenamento Azure](../storage/storage-redundancy.md).


### <a name="shares-and-volumes"></a>Acções e volumes

Para sua matriz Virtual StorSimple, pode aprovisionar o partilhas quando está configurado como um servidor de ficheiros e volumes quando configurado como um servidor iSCSI. As melhores práticas para criar partilhas e volumes estão relacionados com o tamanho e o tipo de configurado.

#### <a name="volumeshare-size"></a>Tamanho de volume/partilhar

No seu matriz virtual, pode aprovisionar o partilhas quando está configurado como um servidor de ficheiros e volumes quando configurado como um servidor iSCSI. As melhores práticas para criar partilhas e volumes referem-se para o tamanho e o tipo de configurado. 

Tenha em atenção as seguintes melhores práticas quando Aprovisiona o partilhas ou volumes no seu dispositivo virtual.

-   Os tamanhos de ficheiro relativamente ao tamanho de uma partilha em camadas aprovisionado podem afetar o desempenho tiering. Trabalhar com ficheiros de grandes dimensões poderá resultar numa camada lenta saída. Ao trabalhar com ficheiros grandes, recomendamos que o ficheiro maior é mais pequeno do que 3% do tamanho partilhar.

-   Um máximo de 16/partilhas de volumes pode ser criado na matriz virtual. Se localmente afixado, pode ser volumes/partilhas entre 50 GB para 2 TB. Se camadas, volumes/partilhas tem de estar entre 500 GB para 20 TB. 

-   Ao criar um volume, factor no consumo de dados esperado, bem como crescimento futuro. Enquanto o volume não pode ser expandido mais tarde, pode sempre restaurar a um grande volume.

-   Assim que o volume ter sido criado, não é possível diminuir o tamanho do volume StorSimple.
   
-   Quando estiver a escrever a um volume em camadas no StorSimple, quando os dados do volume atingir um determinado limiar (relativamente ao espaço local reservado para o volume), é limitada a es. Continuar a escrever a este volume mais lento a es significativamente. Apesar de pode escrever um volume em camadas para além da sua capacidade aprovisionada (Recomendamos não ativamente pare o utilizador a partir de escrita para além da capacidade aprovisionada), verá uma notificação de alerta para o efeito que tem a oversubscribed. Quando vir o alerta, é obrigatório que tomar medidas de reparação como eliminar os dados do volume ou restaurar o volume um grande volume (expansão de volume atualmente não é suportado).

-   Para casos de utilização de recuperação de falhas, tal como o número de permitido partilhas/volumes é 16 e o número máximo de partilhas/volumes que pode ser processada em paralelo também é 16, o número de partilhas/volumes não influenciam no seu RPO e RTOs. 

#### <a name="volumeshare-type"></a>Tipo de volume/partilha

StorSimple suporta dois tipos de volume/partilhar com base na utilização de: localmente afixados e camadas. Localmente afixadas volumes/partilhas thickly configurações Considerando que as em camadas volumes/partilhas de pequenos configurações. 

Recomendamos que implementar as seguintes melhores práticas ao configurar StorSimple volumes/partilhas:

-   Identifique o tipo de volume com base nas cargas de trabalho que pretende implementar antes de criar um volume. Utilize volumes localmente afixados das cargas de trabalho que requerem locais garantias de dados (mesmo durante uma falha na nuvem) e que latências baixa na nuvem. Depois de criar um volume no seu virtual matriz, não é possível alterar o tipo de volume a partir do localmente afixados para em camadas ou *vice versa*. Por exemplo, criar volumes localmente afixados quando implementar cargas de trabalho de SQL ou das cargas de trabalho aloja máquinas virtuais (VMs); Utilize volumes em camadas para cargas de trabalho de partilha de ficheiro.

-   Selecione a opção para menos dados arquivo utilizadas com frequência recorrentes relacionadas com tamanhos de ficheiro de grandes dimensões. Um tamanho de fragmento de eliminação de duplicados de 512 K é utilizado quando esta opção estiver ativada para acelerar a transferência de dados para a nuvem.

#### <a name="volume-format"></a>Formato de volume

Depois de criar volumes de StorSimple no servidor iSCSI, tem de iniciar, montagem e formatar os volumes. Esta operação é executada no anfitrião do ligado ao seu dispositivo StorSimple. Práticas recomendadas seguintes são recomendadas quando montagem e formatar volumes no anfitrião do StorSimple.

-   Efectue uma formatação rápida em todos os volumes de StorSimple.

-   Ao formatar um volume StorSimple, utilize um tamanho de unidade de alocação (Austrália) de 64 KB (a predefinição é 4 KB). O 64 KB Austrália é com base em testes interna para comuns StorSimple cargas de trabalho e outros das cargas de trabalho.

-   Ao utilizar a matriz de Virtual StorSimple configurado como um servidor iSCSI, não utilize volumes expandidos ou discos dinâmicos como estes volumes ou discos não são suportados pelo StorSimple.

#### <a name="share-access"></a>Partilhar o acesso

Quando criar partilhas no servidor virtual matriz ficheiro, siga estas diretrizes:

-   Quando criar uma partilha, atribua um grupo de utilizador como administrador de partilhar em vez de um único utilizador.

-   Pode gerir as permissões de NTFS após a criação de partilhar os editando as partilhas através do Explorador do Windows.

#### <a name="volume-access"></a>Acesso de volume

Quando configurar os volumes de iSCSI na sua matriz de Virtual StorSimple, é importante controlar o acesso sempre que necessário. Para determinar quais os servidores anfitrião podem aceder a volumes, crie e associar registos de controlo de acesso (ACRs) StorSimple volumes.

Utilize as seguintes melhores práticas ao configurar ACRs para volumes de StorSimple:

-   Associe sempre pelo menos uma ACR um volume.

-   Defina várias ACRs apenas num ambiente agrupado.

-   Ao atribuir ACR mais do que um a um volume, certifique-se de que o volume não está exposto de uma forma onde pode ser em simultâneo acedido por mais do que um anfitrião que não sejam agrupadas. Se tiver atribuído ACRs vários para um volume, uma mensagem de aviso é apresentado para deseja rever a sua configuração.

### <a name="data-security-and-encryption"></a>A segurança dos dados e encriptação

A matriz de Virtual StorSimple tem funcionalidades de segurança e encriptação de dados que certifique-se de que a confidencialidade e a integridade dos dados. Ao utilizar estas funcionalidades, é recomendável que siga estas melhores práticas: 

-   Defina uma chave de encriptação de armazenamento na nuvem para gerar a encriptação AES 256 antes dos dados são enviados a partir do seu matriz virtual na nuvem. Esta chave não é necessária se os dados são encriptados para começar. A chave pode gerada e mantida segura com um sistema de gestão de chave como [Azure cofre chave](../key-vault/key-vault-whatis.md).

-   Quando configurar a conta de armazenamento através do serviço de Gestor de StorSimple, certifique-se de que ativar o modo SSL criar um canal seguro para comunicação de rede entre o seu dispositivo StorSimple e na nuvem.

-   Voltar a gerar as teclas para as suas contas de armazenamento (acedendo ao serviço de armazenamento do Windows Azure) periodicamente a conta para quaisquer alterações aceder com base na lista de administradores alterada.

-   Dados na sua matriz virtual são comprimidos e deduplicated antes de ter sido enviada para Azure. Recomendamos que não utilize o serviço de função de eliminação de dados duplicados no seu anfitrião do Windows Server.


## <a name="operational-best-practices"></a>Práticas recomendadas operacionais avançadas

As melhores práticas operacionais são diretrizes que deverão ser seguidas durante a operação da matriz virtual ou gestão no dia a dia. Estas práticas tarefas de gestão específicas, como tirar cópias de segurança, restaurar a partir de um conjunto de cópia de segurança, efetuar uma activação pós-falha de folha de rosto, desativá-los e eliminar a matriz, monitorizar a utilização do sistema e estado de funcionamento e, em execução vírus analisa no seu matriz virtual.

### <a name="backups"></a>Cópias de segurança

Os dados na sua matriz virtual cópias de segurança para a nuvem de duas maneiras, uma predefinida automatizado diária cópia de segurança do dispositivo toda a ser iniciadas na 22:30 ou através de uma cópia de segurança manual a pedido. Por predefinição, o dispositivo cria automaticamente diária instantâneos nuvem de todos os dados que residem no mesmo. Para mais informações, aceda a [cópia da sua matriz de Virtual StorSimple](storsimple-ova-backup.md).

Não não possível alterar a frequência e retenção associados com as cópias de segurança predefinidos, mas pode configurar o tempo no qual as cópias de segurança diárias são iniciadas diariamente. Quando configurar a hora de início para as cópias de segurança automatizadas, recomendamos que:

-   Agende as cópias de segurança das horas de expediente. Hora de início de cópia de segurança se não coincidir com vários anfitrião es.

-   Inicie uma cópia de segurança manual a pedido ao planear executar uma activação pós-falha do dispositivo ou prévia para a janela de manutenção para proteger os dados na sua matriz virtual.

### <a name="restore"></a>Restaurar

Pode restaurar a partir de uma cópia de segurança definir de duas maneiras: Restaurar para outro volume ou partilhar ou efectuar uma recuperação ao nível do item (disponível apenas numa matriz virtual configurada como um servidor do ficheiro). Recuperação de ao nível do item permite-lhe fazer uma recuperação granular de ficheiros e pastas a partir de uma cópia de segurança na nuvem de todas as acções no dispositivo StorSimple. Para obter mais informações, vá para [restaurar a partir de uma cópia de segurança](storsimple-ova-restore.md).

Quando executar uma operação de restauro, ter as seguintes diretrizes em conta:

-   A matriz de Virtual StorSimple não suporta restaurar no local. No entanto pode ser facilmente obtido por um processo de dois passos: adicionar espaço na virtual de matriz e, em seguida, restaurar para outra volume/partilha.

-   Quando restaurar a partir de um local volume, tenha em atenção o restauro estarão uma operação de execução longa. Apesar do volume rapidamente pode ficar online, os dados continua a ser hidratado em segundo plano.

-   O tipo de volume se mantém o mesmo durante o processo de restaurar. Um volume em camadas é restaurado para outro volume em camadas e um volume localmente afixado para outro localmente afixados volume.

-   Quando tentar restaurar um volume ou numa partilha a partir de um conjunto de cópia de segurança, se a tarefa de restauro falhar, um destino em volume ou partilhar ainda pode ser criado no portal. É importante que eliminar este volume de destino não utilizadas ou partilhar no portal para minimizar quaisquer problemas futuros decorrente deste elemento.

### <a name="failover-and-disaster-recovery"></a>Recuperação activação e falhas

Um dispositivo activação pós-falha permite-lhe migrar os dados a partir de um dispositivo de *origem* no Centro de dados para outro dispositivo de *destino* localizado na mesma ou noutra localização geográfica. O dispositivo activação pós-falha é para todo o dispositivo. Durante a falha na ligação, os dados de nuvem para o dispositivo de origem forem alterados que é o proprietário para que o dispositivo de destino.

Para sua matriz Virtual StorSimple, apenas pode falhar sobre para outra matriz virtual gerido pelo serviço de Gestor de StorSimple mesmo. Não é permitido um activação pós-falha para um dispositivo de 8000 série ou de uma matriz geridas pelo serviço StorSimple gestor diferente (para o dispositivo de origem). Para saber mais sobre as considerações activação pós-falha, aceda a [Pré-requisitos para o dispositivo activação pós-falha](storsimple-ova-failover-dr.md).

Quando executa uma falha ao longo para a sua matriz virtual, tenha em atenção o seguinte:

-   Para uma falha na ligação planeada, é recomendada prática recomendada para tirar todas as volumes/partilhas offline antes de iniciar a activação pós-falha. Siga as instruções específicas do sistema operativo para tirar as volumes/partilhas offline no anfitrião do primeiro e, em seguida, colocar aqueles offline no seu dispositivo virtual.

-   Para uma ficheiro falhas recuperação do servidor (DR), recomendamos que participar o dispositivo de destino para o mesmo domínio como a origem de modo a que as permissões de partilha são automaticamente resolvidas. Apenas a activação pós-falha para um dispositivo de destino no mesmo domínio é suportada neste lançamento.

-   Assim que a DR é concluído com êxito, é eliminado automaticamente o dispositivo de origem. Apesar do dispositivo já não se encontra disponível, a máquina virtual que aprovisionado no sistema anfitrião ainda está a consumir recursos. Recomendamos que elimine esta máquina virtual do seu sistema anfitrião para impedir que todos os encargos vencem ao.

-   Tenha em atenção que, mesmo se o activação pós-falha não tiver êxito, **os dados são sempre seguros na nuvem**. Tenha em consideração os três cenários seguintes na qual o activação pós-falha não foi concluída com êxito:

    -   Ocorreu uma falha na fase inicial da falha geral tal como quando as verificações de pré-lançamento DR estão a ser executadas. Esta situação, o seu dispositivo de destino é ainda utilizável. Pode repetir o activação pós-falha no mesmo dispositivo de destino.

    -   Ocorreu uma falha durante o processo de activação pós-falha real. Neste caso, o dispositivo de destino está marcado como inutilizável. Tem de aprovisionamento de e outra matriz virtual de destino de configurar e utilizar que para activação pós-falha.

    -   A activação pós-falha foi concluída após o que o dispositivo de origem foi eliminado mas o dispositivo de destino tem problemas e não é possível aceder a quaisquer dados. Os dados são ainda seguros na nuvem e podem ser facilmente obtidos através da criação de outra matriz virtual e, em seguida, utilizá-lo como um dispositivo de destino para o DR.

### <a name="deactivate"></a>Desativar

Quando desativa uma matriz Virtual StorSimple, pode Server a ligação entre o dispositivo e o serviço do Gestor de StorSimple correspondente. Desativação é uma operação **permanente** e não pode ser anulada. Um dispositivo desativado não pode estar registado junto do serviço do Gestor de StorSimple novamente. Para obter mais informações, aceda ao [desative e elimine a sua matriz de Virtual StorSimple](storsimple-deactivate-and-delete-device.md).

Mantenha as melhores práticas seguintes em mente quando desativá-los a sua matriz virtual:

-   Tire um instantâneo de nuvem da todos os dados antes de desativá-los um dispositivo virtual. Quando desativa uma matriz virtual, todos os dados de dispositivo local é perdida. Tirar um instantâneo de nuvem permite-lhe recuperar dados numa fase posterior.

-   Antes de desativar uma matriz Virtual StorSimple, certifique-se parar ou eliminar clientes e anfitriões que dependem do que o dispositivo.

-   Elimine um dispositivo desativado se já não estiver a utilizar para que não de imputação dos custos.

### <a name="monitoring"></a>Monitorização

Para certificar-se de que a sua matriz de Virtual StorSimple está num estado Saudável contínuo, é necessário monitorizar a matriz e certifique-se de que recebe informações a partir do sistema, incluindo alertas. Para monitorizar o estado de funcionamento geral da matriz virtual, implemente as melhores práticas seguintes:

- Configure a monitorização para controlar a utilização do disco do disco de dados de matriz virtual, bem como o disco SO. Se executar o Hyper-V, pode utilizar uma combinação de sistema Centro Máquina Virtual Gestor (SCVMM) e o Gestor de operações de centro do sistema (SCOM) para monitorizar os anfitriões virtualização.   

- Configure notificações de correio eletrónico no seu matriz virtual para enviar alertas em determinados níveis de utilização.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Procura no índice e vírus digitalizar aplicações

Uma matriz Virtual StorSimple automaticamente pode camada dados a partir da camada local para a nuvem do Microsoft Azure. Quando uma aplicação como uma pesquisa de índice remissivo ou uma análise de vírus é utilizada para analisar os dados armazenados no StorSimple, tem de encarregam-se que os dados da nuvem não aceder acedidos e movidos novamente para a camada local.

Recomendamos que implementar as seguintes melhores práticas ao configurar a digitalização índice de pesquisa ou vírus no seu virtual matriz:

-   Desative todas as operações configurada automaticamente pesquisa completa.

-   Para volumes em camadas, configure a aplicação de pesquisa de índice remissivo vírus ou de pesquisa, para efetuar uma pesquisa utilizarão. Isto seria pesquisar apenas os novos dados provável que residem na camada local. Os dados que são camados para a nuvem não são acedidos durante uma operação de utilizarão.

-   Certifique-se de que os filtros de pesquisa correto e as definições estão configuradas, para que apenas os tipos de ficheiro pretendidos obtenham. Por exemplo, ficheiros (JPEG, GIF e TIFF) de imagem e desenhos de engenharia não deve ser pesquisado durante a recompilação do índice utilizarão ou completo.

Se utilizar o Windows indexação processo, siga estas diretrizes:

-   Não utilize o Windows indexador para volumes em camadas como recupera grandes quantidades de dados (TBs) a partir da nuvem, se o índice recompilado frequentemente. O índice de a reconstruir seria obter todos os tipos de ficheiro para indexar o respetivo conteúdo.

-   Utilize o Windows processo para localmente afixados volumes de indexação só seria aceder a dados no locais camadas para construir o índice remissivo (não irão ser acedidos os dados na nuvem).

### <a name="byte-range-locking"></a>Byte bloqueio de intervalo

Aplicações podem bloquear um intervalo de bytes especificado nos ficheiros. Se o bloqueio de intervalo de byte está ativado nas aplicações que estão a escrever a sua StorSimple, tiering, em seguida, não funciona na sua matriz virtual. Para tiering para trabalhar, todas as áreas de ficheiros acedidos devem ser desbloqueadas. O bloqueio de intervalo de byte não é suportado com volumes em camadas no seu matriz virtual.

Medidas recomendadas a atenuar byte intervalo bloqueio incluem:

-   Desative o intervalo de bytes bloqueio na lógica da aplicação.

-   Utilizar localmente afixados volumes (em vez de camadas) para os dados associados com esta aplicação. Volumes localmente afixados não camada para a nuvem.

-   Quando localmente utilizando afixados volumes com o intervalo de byte bloqueio ativado, o volume pode ficar online antes da restaurar está concluída. Nestes casos, tem de aguardar para restaurar a ser concluída.

## <a name="multiple-arrays"></a>Várias matrizes

Várias matrizes virtuais poderão ter de ser implementada para ter em conta um conjunto de trabalho crescente de dados que podem ajustar para a nuvem assim que afetam o desempenho do dispositivo. Nestes casos, é melhor escala dispositivos à medida que aumenta o conjunto de trabalho. Isto requer um ou mais dispositivos a serem adicionados no Centro de dados no local. Ao adicionar os dispositivos, poderia:

-   Divida o conjunto de dados atual.
-   Implemente o novas das cargas de trabalho em dispositivos de novos.
-   Se implementar várias matrizes virtuais, recomendamos que a partir de balanceamento de carga perspetiva, distribuir a matriz por anfitriões hipervisor diferente.

-  Vários virtuais matrizes (quando configurado como um servidor de ficheiros ou num servidor de iSCSI) podem ser implementadas num distribuído ficheiro sistema espaço de nomes. Para obter passos detalhados, aceda ao [Distribuído ficheiro espaço de nomes solução de sistema com o guia de implementação de armazenamento do híbrido na nuvem](https://www.microsoft.com/download/details.aspx?id=45507). Replicação de sistema de ficheiros distribuído atualmente não é recomendada para utilização com a matriz virtual. 


## <a name="see-also"></a>Consulte também
Obter informações sobre como [administrar a sua matriz de Virtual StorSimple](storsimple-ova-manager-service-administration.md) através do serviço de Gestor de StorSimple.
