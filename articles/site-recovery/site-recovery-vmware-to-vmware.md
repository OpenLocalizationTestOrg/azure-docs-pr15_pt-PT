<properties
    pageTitle="Replicação ou no local máquinas virtuais de VMware servidores físicos para um site secundário | Microsoft Azure"
    description="Utilize este artigo para criar uma réplica VMware VMs ou Windows/Linux servidores físicos para um site secundário com Azure recuperação de Site."
    services="site-recovery"
    documentationCenter=""
    authors="nsoneji"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="nisoneji"/>


# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Criar uma réplica no local VMware virtual máquinas ou servidores físicos para um site secundário


## <a name="overview"></a>Descrição geral

InMage Scout no Azure Site recuperação fornece a replicação em tempo real entre sites de VMware no local. InMage Scout é incluída no subscrições do serviço de recuperação de sites do Azure.


## <a name="prerequisites"></a>Pré-requisitos

**Conta Azure**: terá de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.


## <a name="step-1-create-a-vault"></a>Passo 1: Criar um cofre
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em Novo > gestão > cópia de segurança e recuperação de sites (OMS). Em alternativa, pode clicar em Procurar > recuperação serviços cofre > Adicionar.
3. Em **nome** , especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione uma delas.
4. No **grupo de recursos** criar um novo grupo de recursos ou selecione uma existente. Especifique uma região do Azure para concluir os campos obrigatórios. 
5.  Na **localização**, selecione a região geográfica para o cofre. Para verificar regiões suportadas, consulte o artigo [Preços de recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Se quiser para aceder rapidamente o cofre do Dashboard clique em Afixar ao dashboard e, em seguida, clique em criar.
6. O novo cofre irão aparecer no Dashboard > todos os recursos e nos serviços de recuperação principal cofres pá.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Passo 2: Configurar o Cofre e transferir InMage Scout componentes
7. Nos serviços de recuperação pá cofres selecione cofre e clique em definições.
8. Nas **Definições** > **Introdução** clique em **Recuperação de Site** > Passo 1: **Preparar infraestrutura** > **objectivo de proteção**.
9. Na **objectivo de proteção** selecione ao site de recuperação e selecione Sim, com VMware vSphere hipervisor. Em seguida, clique em OK.
10. Em **configuração Scout**, clique em transferir para transferir das versões de InMage Scout 8.0.1 DG software e registo chave. Os ficheiros de configuração para todos os componentes necessários estão no ficheiro. zip transferidos.


## <a name="step-3-install-component-updates"></a>Passo 3: Instalar atualizações de componente

Leia sobre as mais recentes [atualizações](#updates). Irá instalar os ficheiros de actualização em servidores pela seguinte ordem:

1. Servidor de recepção se existir um
2. Servidores de configuração
3. Servidores de processo
3. Servidores de destino principal
4. servidores de vContinuum
5. Servidor de origem (Windows e Linux servidor)

Instale as atualizações da seguinte forma:

1. Transferir o ficheiro. zip [Atualizar](https://aka.ms/asr-scout-update4) . Este ficheiro. zip contém os seguintes ficheiros:

    - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.GZ
    - CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
    - UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
    - UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
    - vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
    - Bits de update4 UA para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 
    
2. Extrai os ficheiros. zip.<br>
3. **Servidor de recepção**: copiar **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** para o servidor de recepção e extrai-lo. Na pasta extraída, execute o **instalar**.<br>
4. **Para o servidor do processo do servidor de configuração**: copiar **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** ao servidor de configuração e ao servidor do processo. Faça duplo clique para executá-la.<br>
5. **Servidor de destino do modelo global para Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** para o servidor de destino principal. Faça duplo clique para executá-la. Note que o agente unificado também é aplicável ao servidor de origem. Deverá instalá-lo no servidor de origem, bem como mencionado mais tarde nesta lista.<br>
7. **Para o servidor de vContinuum**: copiar **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** para o servidor de vContinuum.  Certifique-se de que fechou o Assistente de vContinuum. Faça duplo clique no ficheiro executá-la.<br>
8. **Servidor de destino principal para Linux**: para atualizar o agente unificado, copie **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** para o servidor de destino principal e extrai-lo. Na pasta extraída, execute o **instalar**.<br>
9. **Servidor de origem para o Windows**: para atualizar o agente unificado, copie **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** para o servidor de origem. Faça duplo clique para executá-la.<br>
10. **Servidor de origem para Linux**: para atualizar o agente unificado, copie uma versão de ficheiro UA correspondente para o servidor de Linux e extrai-lo. Na pasta extraída, execute o **instalar**.  Exemplo: RHEL 6,7 64 bits servidor, copie **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** no servidor e extrai-lo. Na pasta extraída, execute o **instalar**.

## <a name="step-4-set-up-replication"></a>Passo 4: Configurar a replicação
1. Configurar a replicação entre a origem e destino VMware sites.
2. Para obter orientações, utilize a documentação de InMage Scout é transferida com o produto. Em alternativa, pode aceder a documentação da seguinte forma:

    - [Notas de lançamento](https://aka.ms/asr-scout-release-notes)
    - [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
    - [Guia de utilizador](https://aka.ms/asr-scout-user-guide)
    - [Guia de utilizador de recepção](https://aka.ms/asr-scout-rx-user-guide)
    - [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)


## <a name="updates"></a>Atualizações

### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site recuperação Scout 8.0.1 atualização 4
Actualização Scout 4 é um acumulado. Tiver todas as correções de update1 até update3 e melhoramentos e correções de erros de novas seguintes.

**Novo suporte de plataforma** 

- Foi adicionado suporte para vCenter/vSphere 6.0, 6.1 e 6.2
- Foi adicionado suporte para seguintes sistemas operativos de Linux
    - Chapéu vermelho Enterprise Linux (RHEL) 7.0, 7.1 e 7.2 
    - CentOS 7.0, 7.1 e 7.2
    - Chapéu vermelho Enterprise Linux (RHEL) 6.8
    - CentOS 6.8

>[AZURE.NOTE]
>
> RHEL/CentOS 7 de 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** é compactados com base das versões de Scout DG compactar **InMage_Scout_Standard_8.0.1 GA.zip**. Transferir o pacote das versões de Scout DG a partir do portal tal como mencionado no [passo1](site-recovery-vmware-to-vmware.md#Step 1: Create a vault).

**Melhoramentos e correções de erros** 

- Encerramento melhorado processamento para seguintes Linux SO e clones para evitar problemas de indesejados voltar a sincronizar.
    - Chapéu vermelho Enterprise Linux (RHEL) 6. x
    - Oracle Linux (OL) 6. x
- Para Linux, execute o acesso à pasta as permissões no directório de instalação do agente unificado são agora restritas apenas para o utilizador local.
- No Windows temporização saída problema enquanto emissão marca de livro de endereços de consistência distribuído comuns no fortemente carregado aplicações distribuídas como clusters de SQL e o ponto de partilha.
- Adicionado registo relacionado com o fix no instalador base CX.
- Ligação de transferência do VMware vCLI 6.0 é adicionada à installer base do modelo global de Windows destino.
- Adicionado mais controlos e registos para alterações de configurações de rede durante activação e DR Brocas.
- Informações de retenção sometime não são comunicadas à CX.  
- Para cluster física, volume operação novamente o tamanho do Assistente de vContinuum está a falhar quando aconteceu encolher de volume de origem.
- Proteção de cluster falhou com o erro "Falha ao encontrar a assinatura do disco" quando cluster disco é PRDM.
- Falha de sistema do servidor de transporte cxps devido à exceção de fora do intervalo. 
- Nome do servidor e as colunas IP são agora dimensionáveis na página de instalação push do Assistente de vContinuum.
- Melhoramentos de API de recepção
    - Fornece cinco mais recentes disponíveis comuns consistência pontos (apenas se garante etiquetas).
    - Fornece a capacidade e detalhes de espaço livre para todos os dispositivos protegidos.
    - Fornece o estado do controlador de Scout no servidor de origem. 
    
>[AZURE.NOTE] 
>
>- Pacote de base **InMage_Scout_Standard_8.0.1_GA.zip** agora atualizou CX base installer **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** e modelo global de Windows destino base installer **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Para todos os nova instalação utilize novos bits CX e de destino de modelo global de Windows e das versões DG.
>- Atualização 4 pode ser aplicada diretamente no 8.0.1 GA.
>- As atualizações de recepção e servidor de configuração não podem ser rejeitadas novamente depois de que está a ser aplicados no sistema.

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site recuperação Scout 8.0.1 actualização 3
Actualização 3 inclui as seguintes correções de erros e melhoramentos:

- O servidor de configuração e recepção falharem para se registar para o Cofre de recuperação de Site quando estiverem atrás o proxy.
- O número de horas que não for cumprido o objectivo de ponto de recuperação (RPO) não é introdução atualizado no relatório de estado de funcionamento.
- O servidor de configuração não está a sincronizar com recepção quando os detalhes do hardware ESX ou os detalhes de rede contenham carateres UTF-8.
- Controladores de domínio do Windows Server 2008 R2 falharem iniciar após a recuperação.
- Sincronização offline não está a funcionar como esperado.
- Depois de máquina virtual (VM) falha na ligação, eliminação de replicação par fica bloqueada na IU CX muito tempo e os utilizadores não é possível concluir a reposição de recurso ou retomar a operação.
- Em geral instantâneo operações que são efetuadas pela tarefa de consistência tem sido otimizadas para ajudar a reduzir a aplicação desliga como clientes de SQL.
- O desempenho da ferramenta de consistência (VACP.exe) foi melhorado, reduzindo a utilização da memória que é necessária para a criação de instantâneos no Windows.
- A operação push instalar causa uma falha de serviço quando a palavra-passe for maior do que 16 carateres.
- vContinuum não está a verificar e solicitar novas vCenter credenciais quando as credenciais são alteradas.
- No Linux, o Gestor de cache de destino principal (cachemgr) não está a transferir ficheiros a partir do servidor de processo, que resulta numa replicação par limitação.
- Quando a ordem de disco activação pós-falha física cluster (MSCS) não é igual em todos os nós, replicação não está definida para alguns dos cluster volumes.
<br/>Tenha em atenção que a cluster necessita reprotected para tirar partido deste fix.  
- Funcionalidade SMTP não está a funcionar como esperado depois de recepção é atualizada da versão Scout 7.1 para Scout 8.0.1.
- Foram adicionadas mais estatísticas no registo de para a operação de reposição da versão anterior registar o tempo-tem despendido para executá-la.
- Suporte foi adicionado para sistemas operativos de Linux no servidor de origem:
    - Atualização vermelho chapéu Enterprise Linux (RHEL) 6 7
    - CentOS 6 atualizar 7
- A CX e recepção IU agora podem mostrar a notificação para o par, que entra em modo de mapa de bits.
- As correções de segurança seguintes foram adicionadas na recepção:

**Descrição do problema**|**Procedimentos de implementação**
---|---
Ignorar a autorização através do adulteração de parâmetro|Acesso restrito aos utilizadores que não aplicável.
Falsificação de pedido de publicação em vários sites|Implementado o conceito de token de página, que gera aleatoriamente para cada página. <br/>Com esta situação, irá ver: <li> Existe apenas uma única sessão instância para o mesmo utilizador.</li><li>Atualização não funciona –-lo irá redirecioná-se para o dashboard.</li>
Carregamento de ficheiro malicioso|Ficheiros restritos a determinadas extensões. Permitido extensões são: acetato 7z, aiff, asf, avi, bmp, csv, documento, docx, sinalizador, flv, gif, gz, gzip, jpeg, jpg, registo, seg. texto, mov, mp3, mp4, mpc, mpeg, mpg, ods odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, alcatrão, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e postal.
Scripting persistente de publicação em vários sites | Adicionado validações de entrada.


>[AZURE.NOTE]
>
>-  Todas as atualizações de recuperação de sites são cumulativas. Actualização 3 tem todas as correções de atualização 1 e 2 de atualização. Actualização 3 pode ser aplicada diretamente no 8.0.1 GA.
>-  As atualizações de recepção e servidor de configuração não podem ser rejeitadas novamente depois de que está a ser aplicados no sistema.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site recuperação Scout 8.0.1 Update 2 (atualizar 03 15 de dez)

Correções no Update 2 incluem:

- **Servidor de configuração**: corrigir um problema que impedido a funcionalidade de medição gratuita 31 dias de trabalho como esperado quando o servidor de configuração foi registado no recuperação de sites.
- **Agente de unificado**: corrigir para um problema no que resultou na atualização não a ser instalada no servidor de destino principal quando-foi atualizado de versão 8.0 para 8.0.1 Update 1.


### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site recuperação Scout 8.0.1 atualização 1

A atualização 1 inclui as seguintes correções de erros e novas funcionalidades:

- 31 dias de proteção gratuito por instância de servidor. Permite-lhe testar a funcionalidade ou configurar uma prova de conceito.
    - Todas as operações no servidor, incluindo activação e reposição de recurso, são gratuitas para os primeiros 31 dias, a partir do momento em que um servidor estiver protegido pela primeira vez com Scout de recuperação do Site.
    - A partir do 32nd dia em diante, cada servidor protegida será cobrado à taxa padrão instância para protecção Azure recuperação de Site a um site de propriedade de cliente.
    - Em qualquer altura, o número de servidores protegidos que estão atualmente a ser cobrada está disponível na página Dashboard do cofre Azure recuperação de sites.
- Adicionado suporte para vSphere Interface de comandos (vCLI) 5,5 Update 2.
- Suporte adicionada para sistemas operativos de Linux no servidor de origem:
    - RHEL 6 atualizar 6
    - RHEL 5 atualizar 11
    - Actualização centOS 6 6
    - Actualização centOS 5 11
- Correções de erros para corrigir os seguintes problemas:
    - Registo do cofre falhar-se para o servidor de configuração ou servidor de recepção.
    - Volumes de cluster não aparecem como esperado quando agrupadas máquinas virtuais são reprotected quando estes retomar.
    - Reposição de recurso falha quando o servidor de destino mestra está alojado num servidor ESXi diferente a partir do máquinas virtuais produção no local.
    - Permissões de ficheiro de configuração são alteradas ao atualizar para o 8.0.1, o que afeta a proteção e operações.
    - O limiar de nova sincronização não está imposto como esperado, que leva ao replicação inconsistente comportamento.
    - As definições de RPO não são apresentadas corretamente na interface do servidor de configuração. O valor de dados não comprimidas incorretamente mostra o valor comprimido.
    -  A operação de remoção, não elimina como esperado no Assistente de vContinuum e a replicação não é eliminada a partir da interface do servidor de configuração.
    -  No Assistente de vContinuum, o disco está desmarcado automaticamente quando clica em **Detalhes** na vista de disco durante a proteção de máquinas virtuais MSCS.
    - Durante o cenário (P2V) física-para-virtual, serviços HP necessários, tais como CIMnotify e CqMgHost, não são movidos para manual na máquina virtual recuperação. Isto resulta em tempo de arranque adicionais.
    - Proteção de máquina virtual Linux falha quando existem mais do que 26 discos no servidor de destino principal.

## <a name="next-steps"></a>Próximos passos

Registe quaisquer questões que tem no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
