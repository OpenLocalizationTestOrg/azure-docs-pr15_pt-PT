Este artigo descreve um conjunto de provas dadas práticas para executar uma máquina de virtual do Windows (VM) no Azure, com atenção para escalabilidade, disponibilidade, capacidade de gestão e segurança. 

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos do Azure] [ resource-manager-overview] e clássico. Este artigo utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Não recomendamos que utilize uma única VM para cargas de trabalho de produção, porque não existe nenhuma contrato de nível de serviço de cima hora (SLA) para VMs única no Azure. Para obter o SLA, tem de implementar VMs vários num [conjunto de disponibilidade][availability-set]. Para obter mais informações, consulte o artigo [executar várias Windows VMs no Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagrama de arquitectura

Aprovisionamento uma VM no Azure envolve mais mover partes que apenas a próprio VM. Existem elementos cluster, redes e armazenamento.

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama está ativada "cluster - única página VM.

![[0]][0]


- **Grupo de recursos.** Um [_grupo de recursos_] [ resource-manager-overview] é um contentor que detém recursos relacionados. Crie um grupo de recursos para manter os recursos para este VM.

- **VM**. Pode aprovisionar uma VM a partir de uma lista de imagens publicadas ou de um ficheiro de disco rígido virtual (VHD) que carregar para o armazenamento de Blobs do Azure.

- **Disco SO.** O disco de sistema operativo está um VHD armazenado no [Azure armazenamento][azure-storage]. Isto significa que persistir, mesmo se o computador anfitrião vai para baixo.

- **Disco temporário.** A VM é criada com um disco temporário (a `D:` unidade no Windows). Este disco está armazenado numa unidade física no computador anfitrião. É _não_ guardadas no Azure armazenamento e poderá desaparecer durante a ser reiniciado e outros eventos de ciclo de vida VM. Utilize este disco apenas para dados temporários, como ficheiros de página ou trocar.

- **Discos de dados.** Um [disco dados] [ data-disk] é um VHD persistente utilizado para os dados de aplicação. Discos de dados são armazenados no Azure armazenamento, como o disco SO.

- **Rede virtual (VNet) e sub-rede.** Cada VM no Azure é implementada para um VNet que ainda está a ser dividido em sub-redes.

- **Endereço IP público.** Um endereço IP público é necessário para comunicar com a VM&mdash;por exemplo, ao longo de ambiente de trabalho remoto (RDP).

- **Interface da rede (NIC)**. NIC permite a VM comunicar com a rede virtual.

- **Grupo de segurança de rede (NSG)**. O [NSG] [ nsg] é utilizado para permitir/negar o tráfego de rede à sub-rede. Pode associar uma NSG com um NIC individual ou com uma sub-rede. Se associá-la com uma sub-rede, as regras NSG aplicam a todos os VMs nessa sub-rede.
 
- **Diagnósticos.** Registo de diagnóstico é crucial para gerir e resolução de problemas a VM.

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar a sua própria arquitetura de referência deve seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="vm-recommendations"></a>Recomendações VM

Recomendamos que a DS - e s-série de uma vez que estes tamanhos de máquina suportam [Premium armazenamento][premium-storage]. Selecione uma destes tamanhos de máquina, exceto se tiver uma carga de trabalho especializada como computação de alto desempenho. Para obter mais detalhes, consulte o artigo [tamanhos de Máquina Virtual][virtual-machine-sizes]. Quando mover uma carga de trabalho existente para Azure, comece com o tamanho da memória virtual que melhor corresponde aos servidores no local. Em seguida, medir o desempenho da sua carga de trabalho real relativamente às disco, memória e CPU entrada/saída operações por segundo (IOPS) e ajustar o tamanho, se necessário. Além disso, se precisar de múltiplas NIC, tenha em atenção o limite da imagem para cada tamanho.  

Quando aprovisionar a VM e outros recursos, tem de especificar uma localização. Em geral, escolha uma localização mais próxima aos seus utilizadores internos ou clientes. No entanto, não em todos os tamanhos VM poderão estar disponíveis em todas as localizações. Para obter detalhes, consulte a [secção serviços por região][services-by-region]. Para listar os tamanhos VM disponíveis numa determinada localização, execute o seguinte comando de interface de comandos Azure (CLI):

```
    azure vm sizes --location <location>
```

Para obter informações sobre como escolher uma imagem VM publicada, consulte o artigo [navegar e selecionar máquina virtual Azure imagens][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recomendações de disco e de armazenamento

Para um melhor desempenho no disco e/s, recomendamos que [Premium armazenamento][premium-storage], que armazena os dados em unidades de estado sólido (SSDs). Custo é baseado no tamanho do disco aprovisionado. IOPS e débito dependem também tamanho do disco, por isso, de aprovisionar um disco, tenha em atenção todos os três fatores (capacidade, IOPS e débito). 

Uma conta de armazenamento pode suportar VMs de 1 a 20.

Adicione um ou mais discos de dados. Quando cria um novo VHD, é não formatado. Inicie sessão na VM para formatar o disco.

Se tiver um grande número de discos de dados, tenha em atenção dos limites e/s total da conta de armazenamento. Para obter mais informações, consulte o artigo [Limites de disco Máquina Virtual][vm-disk-limits].

Para um melhor desempenho, crie uma conta de armazenamento em separado para colocar em espera registos de diagnóstico. Uma conta de armazenamento localmente redundante (LRS) padrão é suficiente para registos de diagnóstico.

Sempre que possível, instale aplicações num disco dados, não o disco de SO. No entanto, algumas aplicações mais antigas poderão ter de instalar componentes na unidade c:. Nesse caso, pode [redimensionar o disco SO] [ resize-os-disk] através do PowerShell.

### <a name="network-recommendations"></a>Recomendações de rede

O endereço IP público pode ser dinâmico ou estático. A predefinição é dinâmica.

- Reservar um [endereço IP estático] [ static-ip] se terá de um endereço IP fixo que não altera &mdash; por exemplo, se necessárias para criar um registo em DNS no ou precisa do endereço IP para ser whitelisted.

- Também pode criar um nome de domínio completamente qualificado (FQDN) para o endereço IP. Em seguida, pode registar um [registo CNAME] [ cname-record] no DNS que aponta para o FQDN. Para mais informações, consulte o artigo [criar um nome de domínio completamente qualificado no portal do Azure][fqdn].

Todos os NSGs contêm um conjunto de [regras predefinidas][nsg-default-rules], incluindo uma regra que bloqueia todo o tráfego de entrada da Internet. As regras de predefinidas não podem ser eliminadas, mas outras regras podem substituí-los. Para permitir o tráfego da Internet, crie regras que permitam tráfego de entrada para portas específicas &mdash; por exemplo, a porta 80 para HTTP.  

Para ativar RDP, adicione uma regra de NSG que permite que o tráfego de entrada para a porta TCP 3389.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Pode dimensionar uma VM para cima ou para baixo ao [alterar o tamanho da memória virtual][vm-resize]. 

Para dimensionar saída horizontalmente, coloque VMs duas ou mais para uma disponibilidade definir atrás de um balanceador de carga. Para obter mais detalhes, consulte o artigo [executar várias Windows VMs no Azure][multi-vm].

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Tal como indicado acima, não existe nenhuma SLA para uma única VM. Para obter o SLA, tem de implementar VMs vários um conjunto de disponibilidade.

A VM pode ser afectada pela [manutenção planeada] [ planned-maintenance] ou [manutenção imprevistas][manage-vm-availability]. Pode utilizar [VM reinicie registos] [ reboot-logs] para determinar se um reinício VM foi causado pela manutenção planeada.

VHDs de segurança abrange os por [Armazenamento do Windows Azure][azure-storage], que é replicado para durabilidade e disponibilidade.

Para proteger contra a perda de dados acidentais durante operações normais (por exemplo, devido a erro de utilizador), também deve implementar cópias de segurança em qualquer altura, utilização de [instantâneos BLOBs] [ blob-snapshot] ou outra ferramenta.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

**Grupos de recursos.** Colocar recursos intimamente ao forma que partilham o mesmo ciclo de vida para um [grupo de recursos]de mesmo[resource-manager-overview]. Grupos de recursos permitem-lhe implementar e monitorizar recursos como um grupo e agregar faturação custos ao grupo de recursos. Também pode eliminar recursos como um conjunto, que é muito útil para implementações de teste. Atribua recursos significativos nomes. Que torna mais fácil localizar um recurso específico e compreender o seu papel. Consulte o artigo [recomendadas convenções de atribuição de recursos Azure][naming conventions].

**Diagnósticos VM.** Ativar monitorização e diagnósticos, incluindo métricas de estado de funcionamento básicas, registos de infraestrutura de diagnóstico e [Diagnósticos de arranque][boot-diagnostics]. Arranque diagnóstico pode ajudar a diagnosticar uma falha de arranque se obtém a sua VM para o estado não são de arranque. Para obter mais informações, consulte o artigo [Ativar a monitorização e diagnósticos de][enable-monitoring]. Utilizar a [Coleção de registo do Azure] [ log-collector] extensão para recolher registos de plataforma Azure e carregue-os para armazenamento Azure.   

O seguinte comando clip permite diagnóstico:

```
    azure vm enable-diag <resource-group> <vm-name>
```

**Parar uma VM.** Azure faz uma distinção entre Estados "Parado" e "Deallocated". São cobradas quando o estado VM é "parado". Não são cobrados quando a VM desatribuído.

Utilize o seguinte comando clip para retirar uma VM a:

```
    azure vm deallocate <resource-group> <vm-name>
```

O botão **Parar** no portal do Azure retira também a atribuição da VM. No entanto, se pode encerrar através do sistema operativo enquanto tinha sessão iniciada, a VM está parada mas _não_ desatribuído, por isso, será ainda cobrado.

**Eliminar uma VM.** Se eliminar uma VM, os VHDs não são eliminados. Isto significa que pode ser removidos a VM sem perder os dados. No entanto, pode ainda será cobrada pelo armazenamento. Para eliminar o VHD, elimine o ficheiro a partir do [armazenamento de BLOBs][blob-storage].

Para evitar eliminações acidentais, utilize um [lock recurso] [ resource-lock] para bloquear os recurso inteiro grupo ou bloquear recursos individuais, tais como a VM. 

## <a name="security-considerations"></a>Considerações de segurança

Utilizar o [Centro de segurança do Azure] [ security-center] para obter uma vista do Estado de segurança dos seus recursos Azure central. Centro de segurança monitoriza potenciais problemas de segurança, tais como o sistema actualiza antimalware e fornece uma imagem completa do Estado de funcionamento de segurança da sua implementação. 

- Centro de segurança está configurado por subscrição Azure. Ativar a recolha de dados de segurança, tal como descrito no [Centro de segurança de utilização].

- Quando a recolha de dados é ativada, o Centro de segurança analisa automaticamente qualquer VMs criados nessa subscrição.

**Gestão de patches.** Se ativada, o Centro de segurança verifica se a segurança e atualizações críticas estão em falta. Utilize [as definições de política de grupo] [ group-policy] no VM para ativar as atualizações automáticas do sistema.

**Antimalware.** Se ativada, o Centro de segurança verifica se o antimalware software está instalado. Também pode utilizar o Centro de segurança para instalar o software de antimalware a partir de dentro do Azure portal.

Utilizar [o controlo de acesso baseado em funções] [ rbac] RBCA () para controlar o acesso aos recursos Azure que implementar. RBAC permite-lhe atribuir funções de autorização a membros da sua equipa DevOps. Por exemplo, a função de leitor pode ver os recursos Azure, mas não criar, gerir ou eliminá-los. Algumas funções são específicas para tipos de recursos Azure específico. Por exemplo, a função Contribuinte Máquina Virtual pode reiniciar ou retirar uma VM, repor a palavra-passe de administrador, criar uma nova VM e assim sucessivamente. Outras [funções incorporadas de RBAC] [ rbac-roles] que poderá ser útil para esta arquitetura de referência incluir [DevTest Labs utilizador] [ rbac-devtest] e [Rede contribuinte][rbac-network]. Um utilizador pode ser atribuído a várias funções e pode criar funções personalizadas para ainda mais extensivamente permissões.

> [AZURE.NOTE] RBAC não limita as ações que pode executar um utilizador com sessão iniciado uma VM. Essas permissões são determinadas pelo tipo de conta de convidado SO.   

Para repor a palavra-passe de administrador local, execute o `vm reset-access` comando clip Azure.

```
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
```

Utilizar [registos de auditoria] [ audit-logs] para ver ações e outros eventos VM de aprovisionamento.

Considere [Azure disco encriptação] [ disk-encryption] se precisar de encriptar discos SO e os dados. 

## <a name="solution-deployment"></a>Implementação da solução

Uma [implementação] [ github-folder] para uma referência a arquitetura demonstra estas práticas recomendadas está disponível. Esta arquitetura de referência inclui uma rede virtual (VNet), grupo de segurança de rede (NSG) e uma única máquina de virtual (VM).

Existem várias formas para implementar esta arquitetura de referência. É a forma mais fácil seguir os passos seguintes: 

1. Clique com o botão direito no botão abaixo e selecione um dos "Abrir hiperligação num novo separador" ou "Abrir hiperligação numa nova janela".  
[![Implementar Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)

2. Assim que tiver aberto a ligação no portal do Azure, tem de introduzir valores para algumas das definições: 
    - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-single-vm-rg` na caixa de texto.
    - Selecione a região a partir da caixa pendente **localização** .
    - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
    - Selecione o **Tipo de SO** da caixa, o **windows**pendente.
    - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
    - Clique no botão **Compra** .

3. Aguarde para a implementação concluir.

4. Os ficheiros de parâmetro incluem codificada administrador nome de utilizador e palavra-passe e, recomenda-se vivamente que imediatamente altere ambos. Clique no VM denominada `ra-single-vm0 `no Portal do Azure. Em seguida, clique em **Repor palavra-passe** no pá de **suporte + resolução de problemas** . Selecione **Repor palavra-passe** na caixa de lista pendente **modo** , em seguida, selecione um novo **nome de utilizador** e **palavra-passe**. Clique no botão de **atualização** para manter o novo nome de utilizador e palavra-passe.

Para obter informações sobre formas adicionais de implementar esta arquitetura de referência, consulte o ficheiro Leia-me na [orientação-único-vm][github-folder]] Github pasta. 

## <a name="customize-the-deployment"></a>Personalizar a implementação

Se precisar de alterar a implementação para corresponder às suas necessidades, siga as instruções no [ficheiro Leia-me][github-folder]. 

## <a name="next-steps"></a>Próximos passos

Para que o [SLA para máquinas virtuais] [ vm-sla] para aplicar, tem de implementar duas ou mais ocorrências num conjunto de disponibilidade. Para obter mais informações, consulte o artigo [executar várias VMs no Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[github-folder]: http://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm
[group-policy]: https://technet.microsoft.com/en-us/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/en-us/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[security-center]: https://azure.microsoft.com/en-us/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Utilize o Centro de segurança]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Arquitetura de Windows VM única no Azure"
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[blocks]: https://github.com/mspnp/template-building-blocks
