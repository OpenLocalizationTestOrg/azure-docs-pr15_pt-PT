<properties
   pageTitle="Referência de arquitectura Azure - IaaS: Expandir o Active Directory para Azure | Microsoft Azure"
   description="Como implementar uma arquitetura de rede híbrido seguro com autorização do Active Directory no Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="telmos"/>

# <a name="extending-active-directory-directory-services-adds-to-azure"></a>Expandir serviços do Active Directory (adiciona) para Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve as melhores práticas para expandir o seu ambiente do Active Directory (AD) para Azure, para fornecer serviços de autenticação distribuídas ao utilizar [Os serviços de domínio do Active Directory (AD DS)][active-directory-domain-services]. Esta arquitetura expande que descritos nos artigos [Implementar uma arquitetura de rede seguro híbrido no Azure] [ implementing-a-secure-hybrid-network-architecture] e [Implementar uma arquitetura de rede híbrido seguro com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Esta arquitetura de referência utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Utilizar o AD DS para autenticar identidades. Estas identidades podem pertencer a utilizadores, computadores, aplicações ou outros recursos que fazem parte de um domínio de segurança. Pode alojar o AD DS no local, mas num cenário de híbrido onde os elementos de uma aplicação do estão localizados no Azure poderá ser mais eficaz para criar uma réplica esta funcionalidade e o repositório de AD na nuvem. Esta abordagem pode ajudar a reduzir a latência causada pela enviar autenticação e pedidos de autorização local a partir da nuvem novamente para o AD DS em execução no local. 

Existem duas formas de aloja os seus serviços de diretório no Azure:

1. Pode utilizar o [Azure Active Directory (AAD)] [ azure-active-directory] para criar um novo domínio AD na nuvem e ligá-lo para no local domínio do AD. Em seguida, configurar [ligação do Azure AD] [ azure-ad-connect] Promessas para criar uma réplica identidades contidas no repositório no local para a nuvem. Tenha em atenção que o diretório na nuvem é **não** uma extensão do sistema no local, em vez disso é uma cópia que contém as identidades do mesmo. As alterações efetuadas para estas identidades no local onde será copiado para a nuvem, mas as alterações efectuadas a nuvem **não** ser replicadas novamente para o domínio no local. Por exemplo, de palavra-passe deve ser executado no local e utilizar a ligação do Azure AD para copiar a alteração para a nuvem. Além disso, tenha em atenção que a mesma instância do AAD pode ser ligada a mais do que uma instância do AD DS; AAD irá conter identidades do repositório cada AD aos quais está ligado.

    AAD é útil para situações onde a rede no local e a rede virtual Azure que aloja os recursos de nuvem não estão diretamente ligados ao utilizar um túnel VPN ou ExpressRoute circuito. Apesar desta solução é simple, não poderá adequado para sistemas onde componentes migrar entre o limite no-local/na nuvem como este pode requerer reconfiguração do AAD. Além disso, AAD trata apenas a autenticação de utilizador em vez de autenticação de computador. Algumas aplicações e serviços, como o SQL Server, poderão precisar de autenticação de computador caso em que esta solução não é adequada.

2. Pode implementar uma VM em execução AD DS como um controlador de domínio no Azure, expandir a sua infraestrutura de AD existente a partir do seu centro de dados no local. Esta abordagem é mais comum para cenários onde estão ligados à rede no local e à rede virtual Azure por uma ligação VPN e/ou ExpressRoute. Esta solução também suporta bidirecional replicação, permitindo-lhe efetuar alterações na nuvem e no local, onde quer que seja mais adequado. Dependendo do seu os requisitos de segurança, pode ser a instalação do AD na nuvem:
    - parte do mesmo domínio que esse mantido no local
    - um novo domínio dentro de uma floresta partilhada
    - floresta separada

<!-- we might want to add info on how to choose from the three options above -->

Esta arquitetura foca-se a solução 2, utilizando o mesmo domínio AD DS no Azure e no local.

Casos de utilização típica para esta arquitectura incluem:

- Aplicações híbridas onde das cargas de trabalho executar parcialmente no local e parcialmente no Azure.

- Aplicações e serviços que executem a autenticação através da utilização do Active Directory.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte realça os componentes importantes nesta arquitectura (*clique para ampliar*). Para obter mais informações sobre os elementos a cinzento, leia o artigo [Implementar uma arquitetura de rede seguro híbrido no Azure] [ implementing-a-secure-hybrid-network-architecture] e [Implementar uma arquitetura de rede híbrido seguro com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]:

[! [0]][0]

- **Rede no local.** A rede no local inclui servidores AD locais que podem efetuar autenticação e autorização para componentes localizadas no local.

- **Servidores de AD.** Estes são os controladores de domínio implementar os serviços de diretório (AD DS) em execução como VMs na nuvem. Nestes servidores podem fornecer a autenticação de componentes de executar na sua rede virtual Azure.

- **Sub-rede diretório ativo.** Os servidores de AD DS estão alojados numa sub-rede separada. Regras NSG protegem os servidores de AD DS e podem fornecer uma firewall contra tráfego a partir de origens inesperados.

- **Sincronização do azure Gateway e AD.**. O gateway Azure fornece uma ligação entre a rede no local e o Azure VNet. Isto pode ser uma [ligação VPN] [ azure-vpn-gateway] ou [Azure ExpressRoute][azure-expressroute]. Todos os pedidos de sincronização entre os servidores de AD na nuvem e no local passam o gateway. Rotas definidas pelo utilizador (UDRs) alça de encaminhamento para o tráfego de sincronização que transmite diretamente para o servidor de AD na nuvem e não atravessa os existente rede virtual eletrodomésticos (NVAs) utilizados neste cenário.

    Para obter mais informações sobre como configurar UDRs e os NVAs, consulte o artigo [Implementar uma arquitetura de rede seguro híbrido no Azure][implementing-a-secure-hybrid-network-architecture].

## <a name="recommendations"></a>Recomendações

Esta secção resume recomendações para a implementação do AD DS no Azure, que abrange que:

- Recomendações VM.

- Recomendações de funcionamento em rede.

- Recomendações de segurança. 

- Recomendações de Site de diretório ativas.

- Recomendações de posicionamento activas Directory FSMO.

>[AZURE.NOTE] O documento [diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais do Azure] [ ad-azure-guidelines] contém informações mais detalhadas sobre muitos destes pontos.

### <a name="vm-recommendations"></a>Recomendações VM

Crie VMs com recursos suficientes para processar o volume de tráfego esperado. Utilize o tamanho das máquinas alojamento AD DS no local, como ponto de partida. Monitorizar a utilização do recurso; Pode redimensionar os VMs e Dimensionar para baixo se forem demasiado grandes. Para mais informações sobre dimensionamento controladores de domínio AD DS, consulte o artigo [Planeamento de capacidade para serviços de domínio do Active Directory][capacity-planning-for-adds].

Crie um disco dados virtual separado para armazenar a base de dados, registos de início e SYSVOL para AD. Não armazene estes itens no mesmo disco, como o sistema operativo. Note que por predefinição, discos de dados que estejam anexados a uma VM utilizam escrita através da cache. No entanto, esta forma de colocação em cache pode entrar em conflito com os requisitos do AD DS. Por este motivo, defina a definição *Preferência de Cache de anfitrião* do disco de dados para *nenhum*. Para obter mais informações, consulte o artigo [posicionamento da base de dados do Windows Server AD DS e SYSVOL][adds-data-disks].

Implemente VMs, pelo menos, duas com o AD DS como controladores de domínio à sua rede virtual Azure por razões de [disponibilidade](#Availability-considerations) .

### <a name="networking-recommendations"></a>Recomendações de funcionamento em rede

Configure a interface de rede para cada um dos VMs alojamento AD DS com endereços IP estáticos privados. Esta configuração suporta melhor DNS em cada uma das VMs AD. Para obter mais informações, consulte o artigo [como configurar um endereço IP estático privado no portal do Azure][set-a-static-ip-address].

> [AZURE.NOTE] Não dar os endereços IP públicos AD DS VMs. Consulte o artigo [Considerações de segurança] [ security-considerations] para obter mais detalhes.

Tem de se certificar de que o tráfego pode fluxo livre entre os servidores de AD na nuvem e no local:

- Adicione regras NSG à sub-rede de AD permitir o tráfego de entrada no local. Para informações detalhadas sobre as portas que utilizam funcionalidades AD DS, consulte o artigo [do Active Directory e requisitos do porta de serviços de domínio do Active Directory][ad-ds-ports].

- Certifique-se de tabelas UDR não encaminhar o tráfego de AD DS através de NVAs utilizados neste cenário. Para suas própria implementações, dependendo do que NVAs utilizar, poderá querer redirecionar o tráfego de.

### <a name="security-recommendations"></a>Recomendações de segurança

Servidores do AD DS processar a autenticação e, consequentemente, são muito sensíveis itens. Impedir que exposição direta dos servidores AD DS na Internet. Coloque os servidores de AD DS sub-rede separada, com a sua própria firewall. Certifique-se de que as portas necessárias para utilizar o AD DS para autenticação e autorização e para os servidores de synchronzing permanecem abertas, mas fechar todas as outras portas. Para obter mais informações, consulte o artigo [do Active Directory e requisitos do porta de serviços de domínio do Active Directory][ad-ds-ports]. Os [componentes da solução] [ solution-components] secção mais tarde neste documento mostra o NSG regras que a solução de amostra utiliza para portas abertas.

Pode utilizar as regras NSG para criar uma firewall simple. Se necessitar de mais abrangente proteção que possa implementar um perímetro segurança adicional à volta de servidores utilizando um par de sub-redes e NVAs, tal como descrito pelo documento [Implementar uma arquitetura de rede híbrido seguro com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

Utilize a encriptação de disco do BitLocker ou Azure para encriptar o disco que aloja a base de dados do AD DS.

### <a name="active-directory-site-recommendations"></a>Ativas recomendações de Site de diretório

Pode utilizar sites no AD DS para controladores de domínio em conjunto de grupo que estão ligados por uma ligação rápida. Controladores de domínio no mesmo local do AD DS criar uma réplica as suas alterações de diretório automaticamente e é necessária para processar a replicação configuração pequeno.

PARA controlar o tráfego de replicação entre o Azure e o seu centro de dados no local, recomenda-se para adicionar um site de AD DS separado para representar o espaço de endereços utilizado pelo Azure. Pode configurar uma ligação de site entre o seu no local AD DS sites e controlar a replicação de publicação em vários sites forma mais eficaz.

Também pode utilizar a separação de site para aplicar diferentes política objectos de grupo (GPO) para associadas computadores no Azure e para tirar partido das aplicações que são "site deverá ter em consideração", tal como Gestor de configuração do Centro de sistema.

### <a name="active-directory-fsmo-placement-recommendations"></a>Recomendações de posicionamento do activas Directory FSMO

Os servidores de operação FSMO (Single Master) flexível são controladores de domínio especializadas, reposnsible para consistência dos dados ao longo de definições diferentes no AD DS, listados abaixo.

- **Modelo global de esquema**. Esta é uma função de toda a floresta que mantém a estrutura do esquema de utilizado pelo AD DS.

- **Modelo global de nomes do domínio**. Esta é uma função de toda a floresta gere informações sobre nomes de domínio dentro da floresta.

- **Controlador de domínio principal (PDC)**. Esta é uma função de todo o domínio. Trata o PDC atualizações de palavra-passe e bloqueios de conta. É consultado por outros CDs quando pedidos de serviço de autenticação têm nas palavras-passe. O PDC também processa actualizações de política de grupo e é o Centro de dados de destino para as aplicações mais antigas e algumas ferramentas de administração que efectuam algumas operações graváveis.

- **Modelo global de ID relativo (RID)**. RIDs são utilizadas para ajudar a identificar exclusivamente objetos dentro de um diretório. Este servidor é responsável por gerar um conjunto de todo o domínio de RIDs para utilização por todos os servidores de AD dentro do domínio.

- **Função de infraestrutura**. Um objeto de um domínio pode fazer referência um objeto no outro domínio. Esta função todo o domínio é responsável por actualizar um objeto SID e nome distinto numa referência de objeto de domínios. Note que um servidor implementar esta função não é possível agir também como um servidor de Catálogo Global (GC).

Para obter mais informações, consulte [funções de FSMO do Active Directory no Windows][AD-FSMO-roles-in-windows].

Para este cenário, recomendamos que evite implementar funções FSMO para os controladores de domínio no Azure. 

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Crie uma disponibilidade configurar para os servidores de AD. Certifique-se de que não existem, pelo menos, dois servidores do conjunto. Os servidores de AD na nuvem devem ser controladores de domínio dentro do mesmo domínio. Isto permitirá a replicação automática entre servidores.

Além disso, considere designar servidores como [modelos globais de operações inactivo] [ standby-operations-masters] no caso de falha de conectividade a um servidor de uma função FSMO.

## <a name="security-considerations"></a>Considerações de segurança

Se todas as tarefas de administração do domínio, provavelmente a ser executado utilizando os CDs no local, considere efetuar CDs na nuvem só de leitura. Um centro de dados só de leitura apenas mantém um subconjunto de credenciais dos utilizadores (suficiente para efectuar a autenticação localmente) e pode ser configurado para informações em cache apenas para utilizadores específicos. Por isso, se um centro de dados só de leitura está comprometido, apenas as informações para utilizadores em cache, serão afetadas, em vez dos detalhes de todas as contas no domínio. Para obter mais informações, consulte o artigo [Controladores de domínio só de leitura][read-only-dc].

Para ajudar a minimizar a vulnerabilidade de contas de utilizador individual e que impeçam tentativas de ataque, siga o melhor prática para configurar e manter as palavras-passe dos utilizadores em AD. Para obter mais informações, consulte o artigo [Melhores práticas para impor políticas de palavra-passe][best_practices_ad_password_policy]. Além disso, tenha cuidado para que grupos de atribuir aos utilizadores. Por exemplo, não torne utilizadores normais membros do grupo de administradores da empresa, grupo de administradores de esquema e grupo Domain Admins.

## <a name="scalability-considerations"></a>Considerações de escalabilidade

AD é automaticamente dimensionável controladores de domínio que fazem parte do mesmo domínio. Pedidos de são distribuídos por todos os controladores dentro de um domínio. Pode adicionar outro controlador de domínio e sincronizar automaticamente com o domínio. Não configure um balanceador de carga separada para direcionar tráfego para controladores dentro do domínio. Garantir que todos os controladores de domínio têm memória suficiente e recursos de armazenamento para processar a base de dados do domínio; tornar Idealmente, todos os controlador de domínio VMs do mesmo tamanho.

## <a name="management-considerations"></a>Considerações sobre a gestão

Não copie os ficheiros VHD controladores de domínio em vez de efetuar cópias de segurança regulares porque restaurá-los pode resultar em inconsistências no estado entre controladores de domínio.

Encerrar e reiniciar uma VM que é executada a função de controlador de domínio no Azure no sistema operativo convidado em vez de utilizar a opção Encerrar no Portal do Azure. Utilizando o Portal do Azure encerrar uma VM faz com que a VM remover. Esta ação repõe VM-GenerationID, que não é aconselhável para um controlador de domínio. Quando o GenerationID VM é reposta, invocationID do repositório AD também é reposta, o conjunto de RID é eliminado e SYSVOL está marcado como não autoritativas.

## <a name="monitoring-considerations"></a>Considerações de monitorização

A falhar a monitorização e manutenção numa rede de servidores AD pode resultar em problemas tais como:

- **Falha de início de sessão**. Falha de início de sessão pode ocorrer ao longo do domínio ou floresta se uma resolução de relação ou o nome de fidedignidade falhar, ou se um servidor de catálogo global não pode determinar os membros do grupo universal.

- **O bloqueio de conta**. Podem tornar-se bloqueada contas de utilizador e o serviço se o controlador de domínio principal está indisponível ou falha de replicação entre vários controladores de domínio.

- **Falha de controlador de domínio**. Se a unidade que contém o ficheiro dit for executado fora do espaço em disco, pode deixar o controlador de domínio de funcionar.

- **Falha de aplicação**. As aplicações que são críticas para o seu negócio, como o Microsoft Exchange ou de outra aplicação de correio electrónico, podem falhar se as consultas de livro de endereço no diretório falhar.

- **Dados de directório inconsistente**. Se a replicação falhar por um determinado período de tempo, objectos duplicados podem ser criados no diretório e pode ser necessário extenso diagnóstico e a hora de eliminar.

- **Falha de criação de conta**. Controlador de domínio não consegue criar contas de utilizador ou computador se esgota-abastecimento das IDs relativos e o modelo global de RID não está disponível.

- **Falha de política de segurança**. Se a pasta partilhada SYSVOL não replica corretamente, objetos de política de grupo e políticas de segurança não são corretamente aplicadas a clientes.

Monitorizar os servidores de AD cuidadosamente e estar preparado para fazer uma ação lentes rapidamente. Crie uma lista de verificação de tarefas a ser executado num intervalo de tempo adequado de monitorização. Por exemplo poderia agendar as seguintes tarefas críticas diariamente:

- Analisar e resolver alertas comunicadas por controladores de domínio, 

- Certifique-se de que podem comunicar todos os controladores de domínio e que a replicação está a funcionar.

- Certifique-se de que SYSVOL permanece partilhada.

- Corrigir problemas de sincronização do tempo.

- Verificar existência de espaço em disco nas unidades físicas utilizado pelo AD.

Pode executar outras tarefas de rotina menos frequentemente. Por exemplo, poderia rever as relações de fidedignidade e verificar a existência de fidedignidades obsoletas ou quebradas semanais e verificar que todos os controladores de domínio estão a utilizar o mesmo service packs e corrigir quente patches mensalmente.

Para obter mais informações, consulte o artigo [Monitoring Active Directory][monitoring_ad]. Pode instalar ferramentas, como o [Centro de sistemas da Microsoft] [ microsoft_systems_center] no servidor de monitorização (consulte o [diagrama de arquitectura][architecture]) para o ajudar a efetuar as seguintes tarefas. 

## <a name="solution-components"></a>Componentes da solução

A solução para este arquitetura cria uma rede híbrido seguro, tal como descrito pelos documentos a [execução de uma arquitetura de rede seguro híbrido no Azure] prevista[ implementing-a-secure-hybrid-network-architecture] e [Implementar uma arquitetura de rede híbrido seguro com acesso à Internet no Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access], mas com a adição dos seguintes itens:

- Um grupo de recursos Azure denominada *basename*- dns-rg, onde *basename* é um prefixo que especificar quando implementar a solução.

- Duas Azure VMs denominado *basename*-ad1-vm e *basename*-ad2-vm, criado no grupo de recursos *basename*rg - dns. Estes VMs estão configurados como servidores de AD com os serviços de diretório e DNS instalado e configurado.

- Um NSG denominada *basename*-ad-nsg no grupo *basename*- ntwk rg Azure recurso. Este grupo de recursos faz parte de infraestrutura do que constituem a rede híbrido seguro, mas o novo NSG é uma adição que define as regras de entrada de segurança para os servidores de AD conforme apresentado na seguinte tabela:


    Prioridade|Nome|Origem|Destino|Serviço|Ação|
    --------|----|------|-----------|-------|------|
    170|vnet para port53|10.0.0.0/16|Qualquer|Custom(ANY/53)|Permitir|
    180|vnet para port88|10.0.0.0/16|Qualquer|Custom(ANY/88)|Permitir|
    190|vnet para port135|10.0.0.0/16|Qualquer|Custom(ANY/135)|Permitir|
    200|vnet-para-port137-9|10.0.0.0/16|Qualquer|Custom(ANY/137-139)|Permitir|
    210|vnet para port389|10.0.0.0/16|Qualquer|Custom(ANY/389)|Permitir|
    220|vnet para port464|10.0.0.0/16|Qualquer|Custom(ANY/464)|Permitir|
    230|vnet para rpc dinâmicos|10.0.0.0/16|Qualquer|Custom(ANY/49152-65535)|Permitir|
    240|onprem ad para port53|192.168.0.0/24|Qualquer|Custom(ANY/53)|Permitir|
    250|onprem ad para port88|192.168.0.0/24|Qualquer|Custom(ANY/88)|Permitir|
    260|onprem ad para port135|192.168.0.0/24|Qualquer|Custom(ANY/135)|Permitir|
    270|onprem ad para port389|192.168.0.0/24|Qualquer|Custom(ANY/389)|Permitir|
    280|onprem ad para port464|192.168.0.0/24|Qualquer|Custom(ANY/464)|Permitir|
    290|gestão da rdp permitir|10.0.0.128/25|Qualquer|Custom(ANY/3389)|Permitir|
    300|permitir que o gateway|10.0.255.224/27|Qualquer|Custom(ANY/any)|Permitir|
    310|permitir personalizada|10.0.255.192/27|Qualquer|Custom(ANY/any)|Permitir|
    320|vnet-negar|Qualquer|Qualquer|Custom(ANY/any)|Permitir|

    AD DS utiliza portas 53, 89, 135, 389 e 464 para aceitar a replicação de entrada e de tráfego de autenticação. Nesta tabela, o controlador de domínio no local está na 192.168.0.0/24 espaço endereço (o espaço de endereço pode variar - especificar estas informações como um parâmetro para os modelos implementados pela solução.

    O NSG também define as seguintes regras de segurança de saída que permitem a sincronização de diretórios e autorização de fluxo de tráfego voltar à rede no local:

    Prioridade|Nome|Origem|Destino|Serviço|Ação|
    --------|----|------|-----------|-------|------|
    100|port53 de saída|Qualquer|192.168.0.0/24|Custom(ANY/53)|Permitir|
    110|port88 de saída|Qualquer|192.168.0.0/24|Custom(ANY/88)|Permitir|
    120|port135 de saída|Qualquer|192.168.0.0/24|Custom(ANY/135)|Permitir|
    130|port389 de saída|Qualquer|192.168.0.0/24|Custom(ANY/389)|Permitir|
    140|port445 de saída|Qualquer|192.168.0.0/24|Custom(ANY/445)|Permitir|
    150|port464 de saída|Qualquer|192.168.0.0/24|Custom(ANY/464)|Permitir|
    160|fora rpc-dinâmicos|Qualquer|192.168.0.0/24|Custom(ANY/49152-65535)|Permitir|

O script fornecido com a solução também efetuar as seguintes tarefas:

- Adiciona o *basename*-ad1-vm e *basename*-ad2-vm servidores como controladores de domínio para o domínio. É possível visualizar nestes servidores na consola de *utilizadores do Active Directory e computadores* o controlador de domínio no local:

![[1]][1]

- Cria uma nova sub-rede (10.0.0.0/16) para um site do AD denominado Azure-VNet-Ad-Site para o domínio. Este site contém o *basename*-ad1-vm e *basename*-ad2-vm servidores. 

- Adiciona as definições de transporte entre locais IP que configurar o intervalo de replicação entre o site no local e os controladores de domínio na nuvem. Pode ver as definições de sub-rede, sites e as definições de transporte na consola de *Sites do Active Directory e os servidores de* controlador de domínio no local:

![[2]][2]

## <a name="deployment"></a>Implementação

Solução da amostra tem os seguintes prerequsites:

- Já tiver configurado o seu domínio no local e tiver configurado o DNS e serviços de encaminhamento e acesso remoto para suportar uma VPN instalados ligar para o gateway Azure VPN.


- Ter instalado a versão mais recente do clip Azure. [Siga estas instruções para obter mais detalhes][cli-install].

- Se está a implementar a solução a partir do Windows, tem de instalar uma ferramenta que fornece uma shell de festa, como o [Ambiente de trabalho GitHub][github-desktop].

>[AZURE.NOTE] Se não tiver acesso a um domínio no local existente, pode criar um ambiente de teste com o [onpremdeploy.sh] [ onpremdeploy] festa de script. Este script cria uma rede e VM na nuvem que simula uma configuração muito básico no local. Editar este script antes de executar e definir as seguintes variáveis definidas no início do ficheiro:
>
> - **BASE_NAME**. Um prefixo definidos pelo utilizador para o grupo de recursos e VM criado pelo script. Este item deve ser **não pode ultrapassar 5 carateres** caso contrário que o script tentará gerar uma VM com um nome inválido e falhar.
> 
> - **Subscrição**. A ID da subscrição Azure. O grupo de recursos será criado neste suscription.
> 
> - **Localização**. A localização na qual pretende criar o grupo de recursos, tal como *eastus* ou *westus*Azure.
> 
> - **ADMIN_USER_NAME**. O nome para a conta de administrador a VM.
> 
> - **ADMIN_PASSWORD**. A palavra-passe da conta de administrador.

Execute os seguintes passos para criar a solução de exemplo:

1. transferir e editar o [azuredeploy.sh] [ azuredeploy] script e definir os seguintes parâmetros no início do ficheiro:

    - **BASE_NAME**. Um prefixo para os grupos de recursos e VMs criados pelo script definidos pelo utilizador. Como antes, este item deve ser **não pode ultrapassar 5 carateres**.

    - **Subscrição**. A ID da subscrição Azure.

    - **Tipo_so**. O sistema operativo (*Windows* ou *Linux*) para utilizar para a camada de acesso web, empresas e dados VMs. Note que todos os servidores de AD criados pelo script de executam o Windows Server 2012, independentemente desta definição.

    - **Nome_de_domínio**. O nome do domínio no local. Tenha em atenção que se utiliza o ambiente criado pelo onpremdeploy.sh script, tem de ser *contoso.com*.

    - **Localização**. A localização na qual pretende criar os grupos de recursos Azure.

    - **ADMIN_USER_NAME**. O nome a utilizar para as contas de administrador as vários VMs.

    - **ADMIN_PASSWORD**. A palavra-passe da conta de administrador.

    - **ON_PREMISES_PUBLIC_IP**. O endereço IP público do computador VPN no local.

    - **ON_PREMISES_ADDRESS_SPACE**. O espaço de endereços interno da rede no local. Se estiver a utilizar o ambiente criado pelo onpremdeploy.sh script, tem de ser 192.168.0.0/16.

    - **VPN_IPSEC_SHARED_KEY**. A IPSec chave partilhada utilizada para estabelecer a ligação VPN entre à rede no local e o gateway de Azure VPN.

    - **ON_PREMISES_DNS_SERVER_ADDRESS**. O endereço IP do servidor de DNS no local. Se estiver a utilizar o ambiente criado pelo onpremdeploy.sh script, tem de ser 192.168.0.4

    - **ON_PREMISES_DNS_SUBNET_PREFIX** O prefixo do endereço da sub-rede no local. Se estiver a utilizar o ambiente criado pelo onpremdeploy.sh script, tem de ser 192.168.0.0/24.

    >[AZURE.NOTE] Para guardar recursos e a hora, o script não cria as camadas de acesso de negócio ou de dados. Se necessitar destes itens, remova os comentários a secção seguinte o script azuredeploy.sh:
    >
    >
    > ```
    > #### # create biz tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_BIZ_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${BIZ_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${BIZ_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_BIZ_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > #### 
    > #### # create db tier
    > #### TEMPLATE_URI=${URI_BASE}/ARMBuildingBlocks/Templates/bb-ilb-backend-http-https.json
    > #### SUBNET_NAME_PREFIX=${DEPLOYED_DB_SUBNET_NAME_PREFIX}
    > #### ILB_IP_ADDRESS=${DB_ILB_IP_ADDRESS}
    > #### NUMBER_VMS=${DB_NUMBER_VMS}
    > #### 
    > #### RESOURCE_GROUP=${BASE_NAME}-${SUBNET_NAME_PREFIX}-tier-rg
    > #### VM_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VM_COMPUTER_NAME_PREFIX=${SUBNET_NAME_PREFIX}
    > #### VNET_RESOURCE_GROUP=${NTWK_RESOURCE_GROUP}
    > #### VNET_NAME=${DEPLOYED_VNET_NAME}
    > #### SUBNET_NAME=${DEPLOYED_DB_SUBNET_NAME}
    > #### PARAMETERS="{\"baseName\":{\"value\":\"${BASE_NAME}\"},\"vnetResourceGroup\":{\"value\":\"${VNET_RESOURCE_GROUP}\"},\"vnetName\":{\"value\":\"${VNET_NAME}\"},\"subnetName\":{\"value\":\"${SUBNET_NAME}\"},\"adminUsername\":{\"value\":\"${ADMIN_USER_NAME}\"},\"adminPassword\":{\"value\":\"${ADMIN_PASSWORD}\"},\"subnetNamePrefix\":{\"value\":\"${SUBNET_NAME_PREFIX}\"},\"ilbIpAddress\":{\"value\":\"${ILB_IP_ADDRESS}\"},\"osType\":{\"value\":\"${OS_TYPE}\"},\"numberVMs\":{\"value\":${NUMBER_VMS}},\"vmNamePrefix\":{\"value\":\"${VM_NAME_PREFIX}\"},\"vmComputerNamePrefix\":{\"value\":\"${VM_COMPUTER_NAME_PREFIX}\"}}"
    > #### 
    > #### echo
    > #### echo
    > #### echo azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > ####      azure group create --name ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}
    > #### echo
    > #### echo
    > #### echo azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ####      azure group deployment create --template-uri ${TEMPLATE_URI} -g ${RESOURCE_GROUP} -p ${PARAMETERS} --subscription ${SUBSCRIPTION}
    > ```

2. Abra um pedido de shell de festa e mover para a pasta que contém o script azuredeploy.sh.

3. Inicie sessão na sua conta Azure. Na shell de festa, introduza o seguinte comando:

    ```
    azure login
    ```

    Siga as instruções para ligar ao Azure.

4. Execute o comando `./azuredeploy.sh`e, em seguida, aguarde enquanto o script cria a infraestrutura de rede.

5. Na linha de comandos *Certifique-se de que o VNet foi criada*, utilize o Azure portal para verificar que foi criado um grupo de recursos com o nome *basename*- ntwk-rg e que contém itens semelhantes às apresentadas na seguinte imagem:

    ![[3]][3]

    >[AZURE.NOTE] Nos exemplos mostrados, *basename* foi definida *na nuvem* , quando o script foi executado.

    Clique em *basename*- vnet VNet, clique em *sub-redes*e certifique-se de que foram criadas sub-redes mostrados abaixo:

    ![[4]][4]

6. No pedido na janela da shell de festa, prima uma tecla e aguarde enquanto a camada de web e Balanceador de carga são criadas.

7. Na linha de comandos *Certifique-se de que a camada Web foi criada corretamente*, utilize o Azure portal para verificar que foi criado um grupo de recursos denominado *basename*web-camada-rg e que contém itens semelhantes das mostrado abaixo:

    ![[5]][5]

8. No pedido na janela da shell de festa, prima uma tecla e aguarde enquanto os NVAs são criadas.

9. Na linha de comandos *Certifique-se de que o NVA foi criada corretamente*, utilize o Azure portal para verificar se um grupo de recursos denominado *basename*- gestão-rg foi criado com o seguinte conteúdo:

    ![[6]][6]

10. No pedido na janela da shell de festa, prima uma tecla e aguarde enquanto o jumpbox é criada.

11. Na linha de comandos *Certifique-se de que o jumpbox foi criada corretamente*, utilize o portal do Azure para verificar que foram adicionados os seguintes itens para o grupo de recursos de gestão da - rg *basename*:

    - Um conjunto de disponibilidade denominado *basename*- jb-como.

    - Uma VM com o nome *basename*- jb-vm.

    - Uma interface de rede denominado *basename*- jb-NIC.

12. No pedido na janela da shell de festa, prima uma tecla e aguarde enquanto o gateway VPN do Azure e ligação são criadas. Tenha em atenção que este passo pode demorar até 30 minutos a concluir.

13. Na linha de comandos *Certifique-se de que o gateway VPN foi criado corretamente*, utilize o portal do Azure para verificar que os itens seguintes foram adicionados ao grupo de recursos rg - ntwk *basename*:

    - Um gateway de rede local chamado no-local-lgw.
    
    - Um gateway de rede virtual denominado *basename*- vpngw.

    - Uma ligação de gateway denominada *basename*- vnet-vpnconn. Tenha em atenção que o estado desta ligação poderá *não ligado* se ainda não tiver configurado o fim no local de ligação; irá resolver este problema mais tarde.

14. No pedido na janela da shell de festa, prima uma tecla e aguarde enquanto os VMs e outros recursos para a DMZ são criados.

15. Na linha de comandos *Certifique-se de que a DMZ foi criada corretamente*, utilize o Azure portal para verificar se um grupo de recursos denominado *basename*-dmz-rg foi criado com o seguinte conteúdo:

    ![[7]][7]

16. No pedido na janela da shell de festa, prima uma tecla. Deverão aparecer os pedidos seguintes:

    ```text
    Manual Step...

    Please configure your on-premises network to connect to the Azure VNet

    Make sure that you can connect to the on-premises AD server from the Azure VMs
    ```

    Iniciar sessão no seu computador local que se liga para o gateway Azure e configurar a ligação corretamente. Adicione rotas estáticas para o dispositivo de gateway no local que o direciona requestsfor o intervalo de endereços 10.0.0.0/16 através do gateway para a VNet. Os passos para este procedimento irão variar de acordo com como estiver a ligar. Consulte o artigo [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN] [ implementing-a-hybrid-network-architecture-with-vpn] para obter mais informações.

    Tenha em atenção que pode encontrar o endereço IP público do gateway Azure VPN utilizando o portal do Azure examinar *basename*- vpngw gateway no grupo de recursos do rg - ntwk *basename*:

    ![[8]][8]

    Pode determinar se tem corretamente estabelecer a ligação verificando o estado da ligação vpnconn - vnet *basename*. Deverá ser definido *ligado*.

    Para testar a ligação, abra uma ligação de ambiente de trabalho remoto para o jumpbox (10.0.0.254) a partir de uma máquina localizada na sua rede no local.

17. No pedido na janela da shell de festa, prima uma tecla. Na outra pedir, *prima qualquer tecla para atualizar a definição de VNet para VNet apontar para DNS no local*, prima uma tecla e aguarde enquanto as definições de DNS para o VNet são actualizadas para o valor especificado como parâmetro **ON_PREMISES_DNS_SERVER_ADDRESS** no azuredeploy.sh script.

18. Na linha de comandos, *Certifique-se de que a definição do servidor DNS na VNet foi atualizada*, utilize o portal do Azure para examinar a definição de *servidores DNS* da *basename*- vnet VNet no grupo de recursos rg - ntwk *basename*. Deverá ser definida para *Custom DNS*e o *servidor de DNS principal* deve ser o endereço do seu servidor DNS no local:

    ![[9]][9]

19. No pedido *prima qualquer tecla para criar o grupo de recursos para os servidores de AD* na janela da shell de festa, prima uma tecla e aguarde enquanto o grupo de recursos para a suspensão os servidores de AD na nuvem é criado.

20. No pedido *prima qualquer tecla para criar VMs para os servidores de AD* na janela da shell de festa, prima uma tecla e aguarde VMs ser criadas e adicionadas ao grupo de recursos.

21. Quando *prima qualquer tecla para participar VMs para o domínio no local* , aceda ao portal do Azure e certifique-se de que foi criado um grupo designado *basename*- dns-rg e que contém duas VMS (*basename*-ad1-vm e *basename*-ad2-vm):

    ![[10]][10]

22. No grupo de recursos *basename*- ntwk rg, verificação que foi criado um NSG denominado *basename*-ad-nsg. Examine as regras de entrada e saída de segurança para este NSG. Estas devem corresponder os listados nas tabelas dos [componentes da solução] [ solution-components] secção.

23. No pedido na janela da shell de festa, prima uma tecla e aguarde enquanto os VMs são adicionados ao domínio no local.

24. Na *aceda ao no local servidor dos AD para verificar que os computadores foram adicionados aos domínios* pedir, ligar para o seu computador no local e utilizar a consola *computadores e utilizadores do Active Directory* para verificar que ambos os VMs foram adicionados ao domínio:

    ![[11]][11]

25. No pedido na janela da shell de festa, prima uma tecla e aguarde enquanto o site de replicação do AD é criado no domínio.

26. Na *aceda ao no local servidor dos AD para verificar se o site de replicação foi criado* pedir, utilize consola do *Active Directory Sites e serviços* para verificar que um site de replicação denominado *Azure-Vnet-Ad-Site* foi criado com êxito, juntamente com uma ligação de transporte entre locais IP denominada *AzureToOnpremLink*e uma sub-rede que referencia a VNet:

    ![[12]][12]

27. No pedido na janela da shell de festa, prima uma tecla e aguarde enquanto o script instala os serviços de diretório e DNS em cada uma das VMs AD.

28. Quando for apresentada a linha de comandos do *Please início de sessão para cada server Azure AD para verificar que serviços de directório foi configurado com êxito* , abrir uma ligação de ambiente de trabalho remoto a partir de uma máquina no local para o jumpbox (*basename*vm - jb) e em seguida, abra outra ligação de ambiente de trabalho remota a jumpbox para o primeiro servidor de AD (*basename*-ad1-vm). Inicie sessão com a **nome_de_domínio**, **ADMIN_USER_NAME**e **ADMIN_PASSWORD** que especificou no script azuredeploy.sh. Utilizar o Gestor de servidor, certifique-se de que as funções de AD DS e DNS ambos foram adicionadas. Repita este processo para o segundo servidor de AD (*basename*-ad2-vm).

29. No pedido na janela da shell de festa, prima uma tecla. Quando o pedido *prima qualquer tecla para indicar as definições de DNS do Azure VNet para apontarem para o DNS no Azure* é apresentado, prima uma tecla e permitir que o script de atualizar as definições de DNS para o VNet.

30. Quando o pedido *Certifique-se de que o DNS VNet definição foi atualizado referência Azure VM DNS servidores* for apresentada, utilizando a verificação de portal Azure a definição de *Servidores DNS* da *basename*- vnet VNet no grupo de recursos rg - ntwk *basename*. Os servidores DNS principais e secundários agora devem fazer referência as VMs AD dois:

    ![[13]][13]

31. Reinicie cada uma das VMs AD antes de continuar. Este passo é necessário para se certificar de que cada atende as definições de DNS corretas a partir do Azure. Aguarde até que ambos os VMs estiver a executar o antes de continuar.

32. No pedido na janela da shell de festa, prima uma tecla. No pedido seguinte, *prima qualquer tecla para aplicar o gateway UDR à sub-rede do gateway (poderá ter sido removido)*, prima uma tecla e permitir que o script atualizar o gateway UDR.

33. Certifique-se de que o script é concluída com êxito.

## <a name="next-steps"></a>Próximos passos

- Aprender as melhores práticas para [criar uma floresta de recursos adiciona] [ adds-resource-forest] no Azure.

- Aprender as melhores práticas para [criar uma infraestrutura ADFS] [ adfs] no Azure.

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[adfs]: ./guidance-identity-adfs.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-hybrid-network-architecture-with-vpn]: ./guidance-hybrid-network-vpn.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[architecture]: #architecture_diagram
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[azure-vpn-gateway]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-vpngateways/
[azure-expressroute]: https://azure.microsoft.com/documentation/articles/expressroute-introduction/
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[capacity-planning-for-adds]: http://social.technet.microsoft.com/wiki/contents/articles/14355.capacity-planning-for-active-directory-domain-services.aspx
[ad-ds-ports]: https://technet.microsoft.com/library/dd772723(v=ws.11).aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048(v=ws.11).aspx
[powershell-ad]: https://technet.microsoft.com/en-us/library/ee617195.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[best_practices_ad_password_policy]: https://technet.microsoft.com/magazine/ff741764.aspx
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[microsoft_systems_center]: https://www.microsoft.com/server-cloud/products/system-center-2016/
[cli-install]: https://azure.microsoft.com/documentation/articles/xplat-cli-install
[github-desktop]: https://desktop.github.com/
[sssd-and-active-directory]: https://help.ubuntu.com/lts/serverguide/sssd-ad.html
[set-a-static-ip-address]: https://azure.microsoft.com/documentation/articles/virtual-networks-static-private-ip-arm-pportal/
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[adds-data-disks]: https://msdn.microsoft.com/library/azure/jj156090.aspx#BKMK_PlaceDB
[AD-FSMO-recommendations]: #active_directory_FSMO_placement_recommendations
[AD-FSMO-roles-in-windows]: https://support.microsoft.com/en-gb/kb/197132
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[transfer-FSMO-roles]: https://technet.microsoft.com/library/cc816946(v=ws.10).aspx
[view-fsmo-roles]: https://technet.microsoft.com/library/cc816893(v=ws.10).aspx
[read-only-dc]: https://technet.microsoft.com/library/cc732801(v=ws.10).aspx
[AD-sites-and-subnets]: https://blogs.technet.microsoft.com/canitpro/2015/03/03/step-by-step-setting-up-active-directory-sites-subnets-site-links/
[sites-overview]: https://technet.microsoft.com/library/cc782048(v=ws.10).aspx
[implementing-adfs]: ./guidance-iaas-ra-secure-vnet-adfs.md
[onpremdeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/onpremdeploy.sh
[azuredeploy]: https://github.com/mspnp/blueprints/blob/master/ARMBuildingBlocks/guidance-iaas-ra-ad-extension/azuredeploy.sh
[solution-components]: #solution_components

[0]: ./media/guidance-iaas-ra-secure-vnet-ad/figure1.png "Seguro arquitetura de rede híbrida com o Active Directory"
[1]: ./media/guidance-iaas-ra-secure-vnet-ad/figure2.png "Consola de utilizadores do Active Directory e computadores listar o VMs Azure duas como servidores"
[2]: ./media/guidance-iaas-ra-secure-vnet-ad/figure3.png "Consola do Active Directory Sites e serviços que mostra as definições de replicação para o site na nuvem"
[3]: ./media/guidance-iaas-ra-secure-vnet-ad/figure4.png "O conteúdo do grupo de recursos basename-ntwk-rg"
[4]: ./media/guidance-iaas-ra-secure-vnet-ad/figure5.png "As sub-redes na VNet basename vnet"
[5]: ./media/guidance-iaas-ra-secure-vnet-ad/figure6.png "Os itens na camada web"
[6]: ./media/guidance-iaas-ra-secure-vnet-ad/figure7.png "NVAs no grupo de recursos de gestão de basename-rg"
[7]: ./media/guidance-iaas-ra-secure-vnet-ad/figure8.png "Os recursos no grupo de recursos basename-dmz-rg"
[8]: ./media/guidance-iaas-ra-secure-vnet-ad/figure9.png "Localizar o endereço IP público do gateway VPN do Azure"
[9]: ./media/guidance-iaas-ra-secure-vnet-ad/figure10.png "As definições de servidor DNS para o * basename *-vnet VNet"
[10]: ./media/guidance-iaas-ra-secure-vnet-ad/figure11.png "O * basename *-dns-grupo de recursos rg que contém o servidor do AD VMs"
[11]: ./media/guidance-iaas-ra-secure-vnet-ad/figure12.png "Consola de utilizadores do Active Directory e computadores listar o servidor do AD VMs como membros do domínio"
[12]: ./media/guidance-iaas-ra-secure-vnet-ad/figure13.png "Consola do Active Directory Sites e serviços a mostrar o site de replicação para os servidores do Azure AD"
[13]: ./media/guidance-iaas-ra-secure-vnet-ad/figure14.png "As definições de servidor DNS para o VNet basename vnet referenciar os servidores de AD na nuvem"