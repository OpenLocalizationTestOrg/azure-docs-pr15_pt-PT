<properties
   pageTitle="Diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais Azure | Microsoft Azure"
   description="Se souber como implementar os serviços de domínio do AD e serviços de Federação do AD no local, saiba como funcionam em máquinas virtuais Azure."
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/27/2016"
   ms.author="femila"/>

# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais Azure

Este artigo explica as diferenças entre implementar Windows Server Active Directory Domain Services (AD DS) e os serviços de Federação do Active Directory (AD FS) no local versus a implementar o-los em máquinas virtuais do Microsoft Azure importantes.

## <a name="scope-and-audience"></a>Âmbito e audiência

O artigo destina-se para os utilizadores mais experientes já com a implementação do Active Directory no local. Cobrir as diferenças entre a implementação do Active Directory em redes virtuais do Microsoft Azure máquinas virtuais/Azure e tradicional implementações no local do Active Directory. Azure máquinas virtuais e redes virtuais Azure fazem parte de uma infraestrutura-como-a-Service (IaaS) oferta para organizações tirar partido recursos informáticos na nuvem.

Para aqueles que não estiver familiarizado com a implementação do AD, consulte o [Guia de implementação do AD DS](https://technet.microsoft.com/library/cc753963) ou a [Planear a implementação do AD FS](https://technet.microsoft.com/library/dn151324.aspx) conforme apropriado.

Este artigo assume que o leitor está familiarizado com os conceitos seguintes:

- Gestão e de implementação do Windows Server AD DS
- Implementação e configuração de DNS para suportar uma infraestrutura do Windows Server AD DS
- Windows Server AD FS implementação e gestão
- Implementar, configurar e gerir aplicações de terceiros (Web sites e serviços web) dependente que podem consumir tokens do Windows Server AD FS
- Conceitos de máquina virtual gerais, tais como como configurar uma máquina virtual, discos virtuais e redes virtuais

Este artigo realça os requisitos para um cenário de implementação híbrida no qual o Windows Server AD DS ou AD FS são parcialmente implementado no local e parcialmente implementado em máquinas virtuais Azure. O documento pela primeira vez abrange as diferenças entre em execução no AD DS e AD FS do Windows Server em máquinas virtuais Azure versus no local e decisões importantes que afetam a estrutura e implementação críticas. O resto do papel explica diretrizes para cada um dos pontos de decisão mais detalhadamente e como aplicar as diretrizes para vários cenários de implementação.

Este artigo não aborda a configuração do [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), que é um serviço baseado no resto que fornece gestão de identidades e capacidades de controlo de acesso para aplicações na nuvem. Azure Active Directory (Azure AD) e Windows Server AD DS são, no entanto, concebido para funcionar em conjunto para fornecer uma solução de gestão de identidade e de acesso para implementações hoje IT ambientes e aplicações modernas. Para ajudar a compreender as diferenças e relações entre Windows Server AD DS e Azure AD, considere o seguinte:

1. Poderá executar o Windows Server AD DS na nuvem em máquinas virtuais Azure quando estiver a utilizar o Azure para expandir o seu centro de dados no local para a nuvem.
2. Pode utilizar o Azure AD para dar ao seu utilizadores serviço single sign-on das aplicações do Software-como-a-Service (SaaS). Microsoft Office 365 utiliza esta tecnologia, por exemplo, e aplicações em execução no Azure ou noutras plataformas nuvem também podem utilizá-lo.
3. Poderá utilizar Azure AD (o serviço de controlo de acesso) para permitir que os utilizadores, inicie sessão na utilização de identidades do Facebook, Google, Microsoft e outros fornecedores de identidade para as aplicações que são alojadas na nuvem ou no local.

Para mais informações sobre estas diferenças, consulte o artigo [Azure identidade](fundamentals-identity.md).

## <a name="related-resources"></a>Recursos relacionados

Pode transferir e executar a [Avaliação de preparação do Azure Máquina Virtual](https://www.microsoft.com/download/details.aspx?id=40898). A avaliação será automaticamente inspecionar o seu ambiente no local e gerar um relatório personalizado com base na orientação que se encontram neste tópico para o ajudar a migrar o ambiente para o Azure.

Recomendamos que reveja também pela primeira vez a tutoriais, guias e vídeos que abrangem os seguintes tópicos:

- [Configurar uma rede Virtual apenas na nuvem no Portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [Configurar uma VPN do Site para o Site no Portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Instalar uma nova floresta do Active Directory numa rede virtual Azure](active-directory-new-forest-virtual-machine.md)
- [Instalar uma réplica do controlador de domínio do Active Directory no Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IT Pro IaaS: os conceitos básicos da Máquina Virtual (01)](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e a conectividade de publicação em local](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introdução

Os requisitos fundamentais para implementar o Active Directory do Windows Server em máquinas virtuais Azure diferem muito pouco na implementação de em máquinas virtuais no local (e, até certo ponto, máquinas físicas). Por exemplo, no caso do Windows Server AD DS, se os controladores de domínio (CDs) implementar em máquinas virtuais Azure são réplicas no existente no local da empresa/floresta de domínio, em seguida, a implementação do Azure em grande medida pode ser tratada da mesma forma como poderá são processados os qualquer outro site do Windows Server Active Directory adicional. Isto é, sub-redes devem ser definidas no Windows Server AD DS, um site criado, os sub-redes ligadas para esse site e ligado aos outros sites utilizar hiperligações de sites adequadas. No entanto, existem algumas diferenças que são comuns a todas as implementações Azure e algumas que variar de acordo com o cenário de implementação específico. Duas diferenças fundamentais são descritas abaixo:

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Azure máquinas virtuais poderá precisar de conectividade à rede empresarial no local.

Ligar uma rede de empresa no local do Azure máquinas virtuais requer o Azure virtual à rede, que inclui um site para o site ou site-a-ponto virtual privada (VPN) componente de rede capaz de forma totalmente integrada ligar máquinas virtuais Azure e no local máquinas. Este componente VPN também poderia permitir que os computadores de membros do domínio no local aceder a um domínio do Active Directory do Windows Server cujos controladores de domínio estão alojados exclusivamente no Azure máquinas virtuais. É importante ter em atenção que se a ligação VPN falhar, autenticação e outras operações que dependem do Active Directory do Windows Server também falhará. Enquanto os utilizadores podem poderão inicie sessão com uma existente em cache credenciais, todos os ponto a ponto ou tentativas de autenticação de servidor de cliente para o qual de bilhetes ainda tem de ser emitido ou ficaram obsoletos irão falhar.

Consulte o artigo [Rede Virtual](http://azure.microsoft.com/documentation/services/virtual-network/) para uma demonstração de vídeo e uma lista de tutoriais passo a passo, incluindo [configurar um Site para o Site VPN no portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Também pode implementar o Active Directory do Windows Server numa rede virtual Azure que não tenha conectividade com uma rede no local. As diretrizes neste tópico, no entanto, partem do princípio de que uma rede virtual Azure é utilizada uma vez que fornece endereçamento as funcionalidades que estão essenciais para o Windows Server IP.

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Endereços IP estáticos tem de ser configurados com o Azure PowerShell.

Endereços dinâmicos são atribuídos por predefinição, mas utilizam o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático em vez disso. Que define um endereço IP estático que vai permanecer através do serviço a reparação e VM encerramento/reiniciar. Para mais informações, consulte o artigo [interno endereço IP estático máquinas virtuais](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termos e definições

Segue-se uma lista exaustiva de termos para várias tecnologias Azure referenciar neste artigo.

- **Azure máquinas virtuais**: IaaS a oferta no Azure que permite que os clientes implementar VMs executar praticamente qualquer tradicionais no local carga de trabalho do servidor.

- **Azure rede virtual**: O serviço de rede no Azure que permite aos clientes criar e gerir redes virtuais no Azure e ligá-los em segurança para os seus próprios no local infraestrutura de rede ao utilizar uma rede privada virtual (VPN).

- **Endereço virtual IP**: um endereço IP de acesso à internet não está ligado a um cartão específico de interface de computador ou rede. Serviços em nuvem são atribuídos a um endereço IP virtual para o tráfego de rede que é redirecionado para uma VM Azure a receber. Um endereço IP virtual é uma propriedade de um serviço na nuvem que pode conter um ou mais máquinas virtuais Azure. Tenha em atenção que uma rede virtual Azure pode conter um ou mais-serviços em nuvem. Endereços IP virtuais fornecem capacidades de balanceamento de carga nativas.

- **Endereço IP dinâmico**: Este é o endereço IP interno que só se encontra. Que deve ser configurado como um endereço IP estático (ao utilizar o cmdlet Set-AzureStaticVNetIP) para VMs que alojam as funções de servidor do Centro de dados/DNS.

- **A reparação de serviço**: O processo na qual Azure automaticamente devolve um serviço para o estado de execução novamente depois de detetar que o serviço falhou. A reparação do serviço é um dos elementos do Azure que suporte a disponibilidade e RDP. Enquanto pouco, o resultado seguinte um serviço de reparação incidente para um centro de dados em execução numa VM é semelhante a um reinício não planeado, mas tem efeitos de lado alguns:

 - O adaptador de rede virtual na VM irá alterar
 - Irá alterar o endereço de MAC da placa de rede virtual
 - O ID de processador/CPU da VM irá alterar
 - A configuração de IP da placa de rede virtual não será alterada desde a VM está ligada a uma rede virtual e endereço IP a VM está estático.

 Nenhum destes comportamentos vai afetar o Active Directory do Windows Server, uma vez que este tenha sem dependência do endereço MAC ou o ID de processador/CPU e todas as implementações do Active Directory do Windows Server no Azure são recomendadas para ser executada numa rede virtual Azure conforme descrito acima.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>É seguro virtualizar em controladores de domínio do Active Directory do Windows Server?

Implementar o Windows Server Active Directory CDs em máquinas virtuais Azure está sujeitas as mesmas directrizes como a ser executada CDs no local numa máquina virtual. Executar CDs virtualizados é prática segura desde diretrizes para fazer cópias de segurança e restaurar CDs são seguidas. Para mais informações sobre restrições e diretrizes para executar o CDs virtualizados, consulte o artigo [Executar controladores de domínio no Hyper-V](https://technet.microsoft.com/library/dd363553).

Hypervisors fornecer ou trivialize tecnologias que podem causar problemas para muitos sistemas distribuídos, incluindo o Windows Server Active Directory. Por exemplo, num servidor físico, pode clonar um disco ou utilizar os métodos não suportados para reverter o estado de um servidor, incluindo a utilização de SANs e assim sucessivamente, mas fazê-lo num servidor físico é muito mais difícil que restaurar um instantâneo de máquina virtual num hipervisor. Azure oferece uma funcionalidade que pode resultar na mesma condição indesejável. Por exemplo, não deve copiar ficheiros VHD de CDs em vez de efetuar cópias de segurança regulares, uma vez que restaurá-los pode resultar numa situação semelhante à utilização de funcionalidades de restaurar instantâneo.

Essas reversões apresentam bolhas USN que podem levar a Estados-membros permanentemente divergentes entre CDs. Que podem causar problemas de tais como:

- Objetos persistentes
- Palavras-passe inconsistentes
- Valores de atributo inconsistente
- Diferenças de esquema, se o modelo global de esquema é revertido

Para mais informações sobre como são afetados CDs, consulte o artigo [USN e USN anulação](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

Com o Windows Server 2012, [garantias adicionais são criadas as AD DS](https://technet.microsoft.com/library/hh831734.aspx)do início. Estas garantias ajudam a proteger os controladores de domínio virtualizado contra os problemas acima referidos, desde que a plataforma hipervisor subjacente suporta VM GenerationID. Azure suporta VM-GenerationID, o que significa que controladores de domínio que executem o Windows Server 2012 ou posterior no Azure máquinas virtuais tem todas as garantias adicionais.

> [AZURE.NOTE] Deve encerrar e reiniciar uma VM que é executada a função de controlador de domínio no Azure no sistema operativo convidado em vez de utilizar a opção **Encerrar** no portal do Azure clássico. Hoje em dia, utilizando o portal clássico encerrar uma VM faz com que a VM remover. Uma VM deallocated tem a vantagem de não sempre encargos, mas também repõe VM-GenerationID, que não é aconselhável para um centro de dados. Quando o GenerationID VM é reposta, invocationID da base de dados AD DS também é reposta, o conjunto de RID é eliminado e SYSVOL é assinalado como não autoritativas. Para obter mais informações, consulte o artigo [Introdução às virtualização do Active Directory Domain Services (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) e [Virtualizar DFSR em segurança](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Por que motivo implementar o Windows Server AD DS em máquinas virtuais do Azure?

Muitos cenários de implementação do Windows Server AD DS são bem adequados para implementação como VMs no Azure. Por exemplo, suponha que tem uma empresa na Europa que necessita para autenticar os utilizadores numa localização remota na Ásia. A empresa anteriormente não implementada Windows Server Active Directory CDs na Ásia devido ao custo para implementar conhecimentos-los e limitado para gerir a implementação pós servidores. Como resultado, pedidos de autenticação de países da Ásia são processados pelo CDs na Europa, com resultados reduzidos. Neste caso, pode implementar um centro de dados numa VM especificadas tem de ser executado dentro o Centro de dados Azure na Ásia. Como anexar esse centro de dados a uma rede virtual Azure que está ligada diretamente para a localização remota irá melhorar o desempenho de autenticação.

Azure também é bem adequado como substituto a sites de recuperação (DR) falhas caso contrário dispendioso. O relativamente baixa-custo de alojamento um pequeno número de controladores de domínio e uma única rede virtual no Azure representa uma alternativa apelativa.

Por fim, poderá querer implementar uma aplicação de rede no Azure, tal como o SharePoint, que requer o Windows Server Active Directory, mas tem sem dependência na rede no local ou empresarial Windows Server Active Directory. Neste caso, a implementação floresta isolada no Azure para cumprir o SharePoint requisitos do servidor é ideal. Novamente, a implementação de aplicações de rede que necessitam de conectividade à rede no local e do Active Directory da empresa também é suportada.

> [AZURE.NOTE] Uma vez que fornece uma ligação de camada-3, o componente VPN fornece conectividade entre uma rede virtual Azure e uma rede no local também pode ativar os servidores de membro que são executados no local para tirar partido CDs que executar como Azure máquinas virtuais no Azure rede virtual. Mas, se a ligação VPN estiver disponível, comunicação entre no local computadores e controladores de domínio baseadas no Azure não irão funcionar, resultante em autenticação e vários outros erros.  

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Contrasta entre implementar controladores de domínio do Active Directory do Windows Server em máquinas virtuais do Azure versus no local

- Para qualquer cenário de implementação do Active Directory do Windows Server que inclui mais do que um única VM, é necessário utilizar uma rede virtual Azure consistência de endereço IP. Tenha em atenção que este guia assume que CDs estão a ficar com uma rede virtual Azure.

- Tal como acontece com CDs no local, são recomendados endereços IP estáticos. Um endereço IP estático só pode ser configurado utilizando o Azure PowerShell. Consulte o artigo [interno endereço IP estático VMs](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) para obter mais detalhes. Se tiver a monitorização de sistemas ou outras soluções que verificar a existência de configuração de endereço IP estático dentro do sistema operativo convidado, pode atribuir o mesmo endereço IP estático para as propriedades do adaptador de rede da VM. Mas, tenha em atenção que a placa de rede será eliminada se a VM sofre reparação do serviço ou encerrar no portal do clássico e tem o endereço desatribuído. Nesse caso, o endereço IP estático dentro de convidado terão de ser repostas.

- Implementar VMs numa rede virtual não significam (ou exigir) conectividade voltar a uma rede no local; a rede virtual permite meramente essa possibilidade. Tem de criar uma rede virtual para privado comunicação entre o Azure e a sua rede no local. Tem de implementar um ponto final de VPN na rede no local. A ligação VPN é aberta a partir do Azure à rede no local. Para mais informações, consulte o artigo [Descrição geral da rede Virtual](../virtual-network/virtual-networks-overview.md) e [Configurar uma VPN do Site para o Site no Portal do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Uma opção para [criar um site do ponto de VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) está disponível para ligar computadores baseados no Windows individuais diretamente a uma rede virtual Azure.

- Independentemente de criá um virtual de rede ou não, Azure taxas para o tráfego de saída, mas não penetração. Várias opções de estrutura do Active Directory do Windows Server podem afetar a quantidade tráfego de saída é gerado por uma implementação. Por exemplo, implementar um controlador de domínio só de leitura (RODC) limita tráfego de saída, porque não replica saída. Mas a decisão para implementar um RODC tem de ser ponderadas com a necessidade de executar operações de escrita contra o Centro de dados e a [compatibilidade](https://technet.microsoft.com/library/cc755190) com aplicações e serviços no site com RODCs. Para mais informações sobre as taxas de tráfego, consulte o artigo [Azure preços por rapidamente](http://azure.microsoft.com/pricing/).

- Apesar de ter concluída controle sobre que servidor recursos a utilizar para VMs no local, tal como RAM, tamanho do disco e assim sucessivamente, no Azure tem de selecionar a partir de uma lista de tamanhos de servidor pré-configurado. Para um centro de dados, um disco de dados é necessária, para além do disco do sistema operativo para poder armazenar a base de dados do Windows Server Active Directory.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Pode implementar o Windows Server AD FS em máquinas virtuais Azure?

Sim, pode implementar o Windows Server AD FS em máquinas virtuais Azure e o [melhores práticas para implementação do AD FS](https://technet.microsoft.com/library/dn151324.aspx) no local aplicam-se igualmente a implementação do AD FS no Azure. Mas alguns dos procedimentos recomendados, tais como balanceamento de carga e elevada disponibilidade exigir tecnologias para além daquilo que o AD FS oferece própria. Estes tem de ser fornecidos pela infraestrutura de subjacente. Vamos rever algumas dessas melhores práticas e veja como pode ser alcançar utilizando Azure VMs e uma rede virtual Azure.

1. **Nunca expor servidores de serviço (STS) de tokens de segurança diretamente à Internet.**

    Isto é importante porque o STS emite tokens de segurança. Como resultado, servidores STS como servidores de AD FS devem ser tratados com o mesmo nível de proteção como um controlador de domínio. Se um STS está comprometida, os utilizadores maliciosos tem a capacidade de tokens de acesso potencialmente que contém em afirmações da sua escolha a dependente de festa de aplicações e outros servidores de STS confiar em organizações de problema.

2. **Implemente controladores de domínio do Active Directory para todos os domínios de utilizador na mesma rede que os servidores de AD FS.**

    Os servidores do AD FS utilizam serviços de domínio do Active Directory para autenticar utilizadores. Recomenda-se para implementar controladores de domínio na mesma rede como os servidores de AD FS. Este procedimento fornece continuidade do negócio caso a ligação entre a rede do Azure e a sua rede no local é interrompida e permite latência inferior e aumento do desempenho para inícios de sessão.

3. **Implemente vários nós do AD FS para elevada disponibilidade e balanceamento de carga.**

    Na maioria dos casos, a falha de uma aplicação que permite AD FS for aceitável uma vez que as aplicações que necessitam de tokens de segurança são geralmente de missão crítica. Como resultado, e porque o AD FS encontra-se agora no caminho crítico para aceder a aplicações crítico missão, o serviço do AD FS tem de ser vivamente disponível através de vários proxies de AD FS e servidores de AD FS. Para alcançar a distribuição dos pedidos, balanceadores de carga são normalmente implementados à frente os Proxies do AD FS e os servidores de AD FS.

4. **Implemente um ou mais nós de Proxy de aplicação Web para aceder à internet.**

    Quando os utilizadores têm de aceder a aplicações protegidas pelo serviço AD FS, o serviço do AD FS tem de estar disponível a partir da internet. Pode fazê-lo a implementar o serviço de Proxy de aplicação Web. Recomendamos vivamente implementar mais do que um nó para fins de elevada disponibilidade e balanceamento de carga.

5. **Restringir o acesso a partir de nós do Proxy de aplicação Web para recursos de rede interna.**

    Para permitir que utilizadores externos para acederem AD FS da internet, tem de implementar nós de Proxy de aplicação Web (ou em versões anteriores do Windows Server Proxy do AD FS). Os nós de proxy de aplicação Web diretamente são expostos à Internet. Não são necessários para ser façam parte de um domínio e apenas têm acesso aos servidores do AD FS através das portas TCP 443 e 80. É vivamente recomendado que comunicação para todos os outros computadores (especialmente controladores de domínio) está bloqueada.

    Este é normalmente obtido no local através de uma DMZ. Firewalls utilizam o modo de funcionamento de uma lista branca para impedir que o tráfego a DMZ à rede no local (ou seja, só é permitido o tráfego de endereços IP especificados e através de portas especificadas e todos os outros o tráfego é bloqueado).

Mostra o diagrama seguinte por tradicional no local implementação do AD FS.

![Diagrama de uma implementação de serviços de Federação do Active Directory tradicional no local](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

No entanto, uma vez que não fornecem nativo Azure, capacidade de firewall completa, outras opções necessitam de ser utilizada para restringir o tráfego. A tabela seguinte mostra a cada opção e respectivas vantagens e desvantagens.

| Opção | Partido | Desvantagem |
| ------ | --------- | ------------ |
| [ACL de rede Azure](virtual-networks-acl.md) | Configuração inicial menos dispendiosa e muito mais simples | Configuração de ACL de rede adicionais necessários se qualquer VMs novos são adicionados de implementação |
| [Firewall barracuda GN](https://www.barracuda.com/products/ngfirewall) | Modo de lista branca da operação de e -não requer nenhuma configuração de ACL de rede | Aumento do custo e complexidade para a configuração inicial |

Neste caso os passos de alto nível para implementar o AD FS são os seguintes:

1. Crie uma rede virtual com a conectividade de publicação em local, utilizando um VPN ou [ExpressRoute](http://azure.microsoft.com/services/expressroute/).

2. Implemente controladores de domínio na rede virtual. Este passo é opcional, mas recomendado.

3. Implemente servidores de AD FS façam parte de um domínio da rede virtual.

4. Crie um [conjunto de balanceamento de carga interna](http://azure.microsoft.com/blog/internal-load-balancing/) que inclui os servidores de AD FS e utiliza um novo endereço IP privado no interior da rede virtual (um endereço IP dinâmico).

  1. Actualize o DNS para criar o FQDN para apontarem para o endereço IP privado (dinâmico) do conjunto de balanceamento de carga interno.

5. Crie um serviço na nuvem (ou uma rede virtual separada) para os nós de Proxy de aplicação Web.

6. Implementar nós Proxy de aplicação Web do serviço na nuvem ou na rede virtual

  1. Crie um conjunto de balanceamento de carga externo que inclua os nós de Proxy de aplicação Web.

  2. Atualize o nome DNS externo (FQDN) para apontarem para o cloud serviço endereço IP público (o endereço IP virtual).

  3. Configure os proxies de AD FS para utilizar o FQDN que corresponde ao conjunto de balanceamento de carga interna para os servidores de AD FS.

  4. Atualize baseada em afirmações web sites para utilizar o FQDN externo para o seu fornecedor de afirmações.

7. Restringir o acesso entre Proxy de aplicação Web para qualquer computador na rede virtual AD FS.

Para restringir o tráfego, o conjunto de balanceamento de carga para o Balanceador de carga interno Azure, tem de ser configurado para o tráfego apenas para portas TCP 80 e 443 e todos os outros tráfego para o endereço IP dinâmico interno do conjunto de balanceamento de carga é ignorado.

![Diagrama de rede de ADFS juntamente com TCP 443 + 80 permitido.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Tráfego para os servidores de AD FS seria permitido apenas pelos seguintes origens:

- O Balanceador de carga interno Azure.
- O endereço IP de um administrador de rede no local.

> [AZURE.WARNING] A estrutura tem impedir que nós de Proxy de aplicação Web chegar a qualquer outros VMs na rede virtual Azure ou a quaisquer localizações de rede no local. Que pode ser feito ao configurar regras de firewall numa aparelho no local para ligações de encaminhar Express ou o dispositivo VPN para ligações de VPN do site para o site.

Uma desvantagem para esta opção é a necessidade de configurar a rede de ACL para vários dispositivos, incluindo Balanceador de carga interno, os servidores de AD FS e quaisquer outros servidores que seja adicionados à rede virtual. Se qualquer dispositivo é adicionado a implementação sem configurar ACL de rede para restringir o tráfego para o mesmo, pode ser a implementação completa do risco. Se nunca alterar os endereços IP de nós do Proxy de aplicação Web, a rede ACL têm de ser reposta (o que significa que os proxies deverão estar configurados para utilizar [os endereços IP dinâmicos estáticos](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![ADFS no Azure com a rede ACL.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Outra opção é utilizar o aparelho [Barracuda GN Firewall](https://www.barracuda.com/products/ngfirewall) para controlar o tráfego entre servidores proxy do AD FS e os servidores de AD FS. Esta opção está em conformidade com as melhores práticas para segurança e elevada disponibilidade e requer menos administração após a configuração inicial, uma vez que o aparelho Barracuda GN Firewall fornece um modo lista branca da administração de firewall e pode ser instalado diretamente numa rede virtual Azure. Que elimina a necessidade para configurar a qualquer altura que é adicionado um novo servidor para a implementação ACL de rede. Mas esta opção adiciona complexidade de implementação inicial e os custos.

Neste caso, duas redes virtuais são implementados em vez de um. Irá chamamos-los VNet1 e VNet2. VNet1 contém os proxies e VNet2 contém os STSs e a ligação de rede voltar à rede da empresa. VNet1 é, portanto, física (embora praticamente), isolada de VNet2 e, por sua vez, a partir da rede da empresa. VNet1, em seguida, está ligada ao VNet2 utilizando uma tecnologia túnel especial conhecida como arquitetura de rede independentes (TÂNIA) transporte. O túnel TÂNIA é anexado às cada uma das redes virtuais utilizar uma firewall Barracuda GN — um Barracuda em cada uma das redes virtuais.  Para disponibilidade de alta, é recomendável que implemente duas Barracudas em cada rede virtual; uma ativar, a outros passiva. Estas funcionalidades oferecem extremamente ricos capacidades firewalling que permitem-nos imitar a operação de uma DMZ tradicional no local no Azure.

![ADFS no Azure com a firewall.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Para obter mais informações, consulte o artigo [AD FS: expandir uma aplicação de front-end deverá ter em consideração em afirmações no local à Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Uma alternativa a implementação do AD FS se o objetivo for o SSO do Office 365 individualmente

Existe outra alternativa para implementar o AD completamente FS se o seu objetivo é apenas ativar a iniciar sessão para o Office 365. Nesse caso, pode simplesmente implementar DirSync com palavra-passe sincronizar no local e o mesmo resultado final-com complexidade de implementação mínimas porque esta abordagem não requer o AD FS ou Azure.

A tabela seguinte compara como os processos de início de sessão no funcionam com e sem implementar o AD FS.

| Office 365 único início de sessão utilizando o AD FS e DirSync | Office 365 mesmo início de sessão no utilizando DirSync + sincronização de palavra-passe |
| ------------- | ------------- |
| 1. o utilizador inicia sessão a uma rede empresarial e está autenticado Active Directory do Windows Server. | 1. o utilizador inicia sessão a uma rede empresarial e está autenticado Active Directory do Windows Server. |
| 2. o utilizador tenta aceder ao Office 365 (Estou @contoso.com). | 2. o utilizador tenta aceder ao Office 365 (Estou @contoso.com). |
| 3. o office 365 redireciona o utilizador para Azure AD. | 3. o office 365 redireciona o utilizador para Azure AD. |
| 4. Dado que Azure AD não é possível autenticar o utilizador e compreende que existe uma fidedignidade com o AD FS no local, redireciona o utilizador para o AD FS. | 4. azure AD não consigo aceita bilhetes Kerberos diretamente e não existe nenhuma relação de fidedignidade para pedidos de que o utilizador introduzir credenciais. |
| 5. o utilizador envia uma permissão Kerberos para o STS do AD FS. | 5. o utilizador introduz a mesma palavra-passe no local e Azure AD valida-los contra o nome de utilizador e palavra-passe que foi sincronizado pela DirSync. |
| 6. AD FS transformações da permissão de Kerberos para as formato/afirmações tokens necessárias e redireciona o utilizador para Azure AD. | 6. azure AD redireciona o utilizador para o Office 365. |
| 7. o utilizador autentica para Azure AD (transformação outra ocorre). |  7. o utilizador pode iniciar sessão no Office 365 e OWA utilizando o token de Azure AD. |
| 8. azure AD redireciona o utilizador para o Office 365. |  |
| 9. o utilizador silenciosamente iniciou sessão Office 365. |  |

No Office 365 com DirSync com cenário de sincronização de palavra-passe (não AD FS), o início de sessão único, é substituído por "mesmo sign-on" onde "mesmo" simplesmente significa que os utilizadores tem de reintroduzir as mesmas credenciais no local ao aceder ao Office 365. Tenha em atenção que estes dados podem ser lembrados através de browser do utilizador para ajudar a reduzir pedidos subsequentes.

### <a name="additional-food-for-thought"></a>Pensamento de alimentação adicional

- Se implementar um proxy de AD FS num computador virtual Azure, é necessária conectividade aos servidores do AD FS. Se estiverem no local, é recomendável que tirar partido a conectividade VPN do site para o site fornecida pela rede para permitir que os nós de Proxy de aplicação Web comunicar com os respetivos servidores do AD FS virtual.

- Se implementar um servidor do AD FS num computador virtual Azure, é necessária conectividade a controladores de domínio do Active Directory do Windows Server, arquivos de atributo e bases de dados de configuração e também podem exigir uma ExpressRoute ou uma ligação VPN do site para o site entre a rede virtual Azure e à rede no local.

- São aplicadas taxas de todo o tráfego a partir do Azure máquinas virtuais (tráfego de saída). Se o custo é fator de condução, é aconselhável para implementar os nós de Proxy de aplicação Web no Azure, deixe o AD FS servidores no local. Se os servidores de AD FS são implementados em máquinas virtuais Azure, assim, os custos adicionais serão realizados para autenticar utilizadores no local. Tráfego de saída como um custo, independentemente da permissão ou não de percorrer a ExpressRoute ou a ligação de site para o site VPN.

- Se decidir utilizar native server balanceamento do Azure de carga capacidades para elevada disponibilidade dos servidores do AD FS, tenha em atenção que balanceamento de carga fornece sondas são utilizadas para determinar o estado de funcionamento das máquinas virtuais dentro do serviço de nuvem. No caso de máquinas virtuais Azure (por oposição a funções web ou de trabalho), uma sonda personalizada deve ser utilizada uma vez que o agente que responde às sondas predefinido não estiver presente em máquinas virtuais Azure. Para simplificar, poderá utilizar uma sonda TCP personalizada — requer apenas que com êxito possível estabelecer uma ligação de TCP (um segmento TCP SYN enviados e respondido com um segmento TCP SYN ACK) para determinar a saúde máquina virtual. Pode configurar a sonda personalizada para utilizar qualquer porta TCP para o qual as máquinas virtuais está ativamente a ouvir.

> [AZURE.NOTE] Máquinas que precisam de expor o mesmo conjunto de portas diretamente à Internet (como portas 80 e 443) não podem partilhar o mesmo serviço na nuvem. Recomenda-se, por conseguinte, que cria uma nuvem dedicada serviço para os servidores do Windows Server AD FS para evitar potenciais sobrepõe-se entre os requisitos de portas para uma aplicação e Windows Server AD FS.

## <a name="deployment-scenarios"></a>Cenários de implementação

A secção seguinte descreve os cenários de implementação conhecida para chamar a atenção para considerações importantes que deve ser encaminhado para a conta. Cada cenário tem ligações para mais detalhes sobre as decisões e fatores a ter em conta.

1. [AD DS: Implementar uma aplicação em atenção o AD DS com sem requisito para a conectividade da rede da empresa](#BKMK_CloudOnly)

    Por exemplo, um serviço de acesso à Internet SharePoint é implementado numa máquina virtual Azure. A aplicação não tem dependências de recursos de rede da empresa. A aplicação requer o Windows Server AD DS mas não requer o Windows Server AD DS da empresa.

2. [O AD FS: Expandir uma aplicação de front-end de deverá ter em consideração em afirmações no local à Internet](#BKMK_CloudOnlyFed)

    Por exemplo, uma aplicação deverá ter em consideração em afirmações que foram implementado com êxito no local e utilizado por utilizadores empresariais precisa para se tornar acessíveis a partir da Internet. A aplicação tem de ser acedidas diretamente através da Internet, ao utilizar os seus próprios identidades empresariais de parceiros de negócios e por utilizadores empresariais existentes.

3. [AD DS: Implementar uma aplicação do Windows Server AD DS-deverá ter em consideração que necessita de conectividade à rede empresarial](#BKMK_HybridExt)

    Por exemplo, uma aplicação deverá ter em consideração LDAP que suporta a autenticação integrada do Windows e utiliza o Windows Server AD DS como um repositório para os dados de configuração e de perfil de utilizador é implementada numa máquina virtual Azure. É conveniente para a aplicação tirar partido existente Windows Server AD DS empresarial e fornecer o início de sessão único. A aplicação não é deverá ter em consideração em afirmações.

### <a name="BKMK_CloudOnly"></a>1. AD DS: Implementar uma aplicação em atenção o AD DS com sem requisito para a conectividade da rede da empresa

![Apenas a nuvem e implementação do AD DS](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**figura 1**

#### <a name="description"></a>Descrição

SharePoint é implementado numa máquina virtual Azure e a aplicação não tem dependências de recursos de rede da empresa. A aplicação requer o Windows Server AD DS mas não *não* requer o Windows Server AD DS da empresa. Sem Kerberos ou fidedignidades federadas são necessárias porque os utilizadores estão personalizada aprovisionados através da aplicação para o domínio Windows Server AD DS que também está alojado na nuvem em máquinas virtuais Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações sobre o cenário e como áreas de tecnologia aplicam-se para o cenário

- [Topologia da rede](#BKMK_NetworkTopology): criar uma rede virtual Azure sem conectividade de publicação em local (também conhecido como connectivity de site para o site).

- [Configuração de implementação do Centro de dados](#BKMK_DeploymentConfig): implementar um novo controlador de domínio para uma nova domínio único, Active Directory do Windows Server floresta de. Isto deve ser implementado juntamente com o servidor de DNS do Windows.

- [Topologia de site do Active Directory do Windows Server](#BKMK_ADSiteTopology): Utilize o site predefinido do Active Directory do Windows Server (todos os computadores será nome-de-primeiro-Site-predefinido).

- [Endereçamento IP e DNS](#BKMK_IPAddressDNS):

 - Defina um endereço IP estático para o Centro de dados utilizando o cmdlet Set-AzureStaticVNetIP Azure PowerShell.
 - Instalar e configurar o DNS do Windows Server no domínio controlador (es) no Azure.
 - Configure as propriedades de rede virtual com o nome e endereço IP da VM que aloja as funções de servidor do Centro de dados e de DNS.

- [Catálogo global](#BKMK_GC): O Centro de dados primeiro na floresta tem de ser um servidor de catálogo global. CDs adicionais também devem ser configuradas como GC porque numa floresta único domínio, o catálogo global não requer qualquer trabalho adicional a partir do Centro de dados.

- [Posicionamento da base de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB): adicionar um disco de dados ao CDs em execução como Azure VMs para poder armazenar a base de dados, registos de início e SYSVOL do Active Directory do Windows Server.

- [Cópia de segurança e restauro](#BKMK_BUR): determinar onde pretende armazenar cópias de segurança de estado de sistema. Se necessário, adicione outro disco de dados para a VM CC para armazenar as cópias de segurança.

### <a name="BKMK_CloudOnlyFed"></a>ADFS 2: expandir uma aplicação de front-end deverá ter em consideração em afirmações no local à Internet

![Federação com a conectividade de publicação em local](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**figura 2**

#### <a name="description"></a>Descrição

Necessita de uma aplicação deverá ter em consideração em afirmações que foram implementado com êxito no local e utilizado por utilizadores empresariais para se tornar acessível diretamente a partir da Internet. A aplicação funciona como um frontend web a uma base de dados do SQL em que armazena os dados. Os servidores SQL utilizados pela aplicação também estão localizados na rede da empresa. Duas Windows Server AD FS STSs um balanceador de carga foram e implementado no local para fornecer acesso aos utilizadores empresariais. A aplicação agora tem de ser acedidas para além disso diretamente através da Internet ao utilizar os seus próprios identidades empresariais de parceiros de negócios e por utilizadores empresariais existentes.

Num esforço para simplificar e satisfazer as necessidades de configuração e implementação deste requisito novo, é decidido dois adicionais web frontends e dois servidores proxy de servidor do Windows AD FS ser instalado em máquinas virtuais Azure. Todos os quatro VMs serão apresentado quando se clica diretamente à Internet e serão fornecidos conectividade à rede no local com capacidade de VPN do site para o site da rede Azure Virtual.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações sobre o cenário e como áreas de tecnologia aplicam-se para o cenário

- [Topologia da rede](#BKMK_NetworkTopology): criar uma rede virtual Azure e [Configurar a conectividade de publicação em local](../vpn-gateway/vpn-gateway-site-to-site-create.md).

 > [AZURE.NOTE] Para cada um dos certificados Windows Server AD FS, certifique-se de que o URL definido no modelo de certificado e os certificados resultantes pode ser alcançado através de instâncias de FS Windows Server AD em execução no Azure. Isto pode necessitam de conectividade de publicação em local para partes da sua infraestrutura de PKI. Por exemplo se ponto final o LCR é baseado em LDAP e alojado exclusivamente no local, em seguida, cruzada local conectividade serão necessária. Se não for conveniente, poderá ser necessário utilizar certificados emitidos por uma AC cujo CRL está acessível através da Internet.

- [Configuração dos serviços de nuvem](#BKMK_CloudSvcConfig): Certifique-se de que tem dois serviços em nuvem por ordem fornecer dois balanceamento de carga virtuais endereços IP. Endereço IP virtual do serviço em nuvem primeiro será direcionado para os VMs de proxy do Windows Server AD FS duas portas 80 e 443. Windows AD FS proxy de servidor de VMs será configurado para apontarem para o endereço IP do Balanceador de carga no local que frentes STSs de FS o Windows Server AD. Endereço IP virtual do serviço em nuvem segunda será direcionado para os duas VMs executar novamente a frontend web nas portas 80 e 443. Configure uma sonda personalizada para garantir que o Balanceador de carga apenas encaminha o tráfego para o funcionamento Windows Server AD FS e proxy para a web frontend VMs.

- [Configuração do servidor de Federação](#BKMK_FedSrvConfig): configurar o Windows Server AD FS como um servidor de Federação (STS) para gerar tokens de segurança para floresta do Active Directory do Windows Server criado na nuvem. Federação relações de fidedignidade do fornecedor de afirmações com diferentes parceiros que pretende aceitar identidades a partir de e configurar as relações de fidedignidade festa dependente com as diferentes aplicações que pretende gerar tokens de.

    Na maioria dos cenários, os servidores proxy do Windows Server AD FS são implementados numa capacidade de acesso à Internet por motivos de segurança enquanto seus homólogos de Federação do Windows Server AD FS permanecem isolados de ligação à Internet direta. Independentemente do seu cenário de implementação, tem de configurar o seu serviço de nuvem com um endereço IP virtual que irá fornecer um endereço IP expostas publicamente e uma porta que é possível balanceamento de carga ao longo do seu duas instâncias do Windows Server AD FS STS ou instâncias de proxy.

- [Configuração de elevada disponibilidade do Windows Server AD FS](#BKMK_ADFSHighAvail): é aconselhável implementar um farm do Windows Server AD FS com, pelo menos, dois servidores para activação pós-falha e balanceamento de carga. Poderá querer ter em conta a utilizar o Windows interno da base de dados (WID) para dados de configuração do Windows Server AD FS e utilizar a funcionalidade de balanceamento de carga interna do Azure para distribuir pedidos recebidos pelos servidores do farm.

Para obter mais informações, consulte o [Guia de implementação do AD DS](https://technet.microsoft.com/library/cc753963).


### <a name="BKMK_HybridExt"></a>3. AD DS: Implementar uma aplicação do Windows Server AD DS-deverá ter em consideração que necessita de conectividade à rede empresarial

![Implementação do AD DS cruzada local](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**figura 3**

#### <a name="description"></a>Descrição

Uma aplicação deverá ter em consideração LDAP é implementada numa máquina virtual Azure. Suporta autenticação integrada do Windows e utiliza o Windows Server AD DS como um repositório para os dados de perfil de utilizador e de configuração. O objetivo é para a aplicação tirar partido de empresa existente de Windows Server AD DS e fornecer o início de sessão único. A aplicação não é deverá ter em consideração em afirmações. Os utilizadores também precisam de aceder à aplicação diretamente a partir da Internet. Para otimizar para desempenho e custo, se decidir que dois controladores de domínios adicionais que fazem parte do domínio empresarial ser implementada juntamente com a aplicação no Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considerações sobre o cenário e como áreas de tecnologia aplicam-se para o cenário

- [Topologia da rede](#BKMK_NetworkTopology): criar uma rede virtual Azure com [cruzada local conectividade](../vpn-gateway/vpn-gateway-site-to-site-create.md).

- [Método de instalação](#BKMK_InstallMethod): implementar réplica CDs a partir do domínio do Active Directory do Windows Server empresarial. Para uma réplica CC, pode instalar o Windows Server AD DS a VM e, opcionalmente, utilize a funcionalidade de instalar a partir de multimédia (IFM) para reduzir a quantidade de dados que tem de ser replicadas para o Centro de dados novo durante a instalação. Para obter um tutorial, consulte o artigo [instalar uma réplica do controlador de domínio do Active Directory no Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Mesmo que utilize IFM, poderá ser mais eficaz para construir o virtual Centro de dados no local e mudar o disco rígido Virtual inteira (VHD) para a nuvem em vez de replicadas Windows Server AD DS durante a instalação. Para segurança, é recomendável que elimine o VHD da rede no local, assim que foi copiado para o Azure.

- [Topologia de site do Windows Server Active Directory](#BKMK_ADSiteTopology): criar um novo site Azure nos Sites do Active Directory e serviços. Crie um objeto de sub-rede do Active Directory do Windows Server para representar a rede virtual Azure e adicionar a sub-rede para o site. Crie uma nova hiperligação de site que inclui o novo site Azure e o site no qual está localizada para poder controlar e optimizar o tráfego do Active Directory do Windows Server de Azure e para a rede virtual Azure ponto final VPN.

- [Endereçamento IP e DNS](#BKMK_IPAddressDNS):

 - Defina um endereço IP estático para o Centro de dados utilizando o cmdlet Set-AzureStaticVNetIP Azure PowerShell.
 - Instalar e configurar o DNS do Windows Server no domínio controlador (es) no Azure.
 - Configure as propriedades de rede virtual com o nome e endereço IP da VM que aloja as funções de servidor do Centro de dados e de DNS.

- [Distribuído geo CDs](#BKMK_DistributedDCs): Configure redes virtuais adicionais, conforme necessário. Se o seu topologia de site do Active Directory requer CDs no geografias que correspondem aos diferentes regiões Azure, que pretende criar sites do Active Directory em conformidade.

- [CDs só de leitura](#BKMK_RODC): poderá implementar um RODC no site do Azure, dependendo nos seus requisitos para efetuar escrever operações contra o Centro de dados e a compatibilidade de aplicações e serviços no site com RODCs. Para obter mais informações sobre a compatibilidade de aplicação, consulte o [Guia de compatibilidade da aplicação de controladores de domínio só de leitura](https://technet.microsoft.com/library/cc755190).

- [Catálogo global](#BKMK_GC): GC são necessários para pedidos de início de sessão em florestas com vários domínios. Se não implementar um GC no site do Azure, irá implicam custos de tráfego de saída como pedidos de autenticação causam consultas GC noutros locais. Para minimizar a que o tráfego, pode ativar a cache de associação de grupo universal para o site Azure Active Directory Sites e serviços.

    Se implementar um GC, configure hiperligações de sites e os custos de ligações de site para que o GC no site do Azure não é preferido como uma origem de centro de dados por outros GC que precisa de criar uma réplica as partições domínio parciais mesmo.

- [Posicionamento da base de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB): adicionar um disco de dados ao CDs a executar no Azure VMs para poder armazenar a base de dados, registos de início e SYSVOL do Active Directory do Windows Server.

- [Cópia de segurança e restauro](#BKMK_BUR): determinar onde pretende armazenar cópias de segurança de estado de sistema. Se necessário, adicione outro disco de dados para a VM CC para armazenar as cópias de segurança.

## <a name="deployment-decisions-and-factors"></a>Decisões de implementação e fatores

A tabela seguinte resume as áreas de tecnologia de Active Directory do Windows Server são afectadas no cenários anteriores e as decisões correspondentes a ter em conta com ligações para obter mais detalhes abaixo. Algumas áreas de tecnologia poderão não ser aplicáveis em cada cenário de implementação e algumas áreas de tecnologia poderão ser mais críticas para um cenário de implementação do que outras áreas de tecnologia de apoio.

Por exemplo, se implementar uma réplica CC numa rede virtual e floresta tem um único domínio, em seguida, selecione o implementar um servidor de um catálogo global nesse caso só estarão crítico o cenário implementação porque não irá criar quaisquer requisitos de replicação adicionais. Por outro lado, se a floresta tiver vários domínios, em seguida, a decisão para implementar um catálogo global numa rede virtual pode afetar largura de banda disponível, desempenho, autenticação, pesquisas no directório e assim sucessivamente.

| Área de tecnologia do Active Directory do Windows Server | Decisões | Fatores |
| ---- | ---- | ---- |
| [Topologia da rede](#BKMK_NetworkTopology) | Criar uma rede virtual? | <li>Requisitos para aceder a recursos de Emp.</li> <li>Autenticação</li> <li>Gestão de contas</li> |
| [Configuração de implementação do Centro de dados](#BKMK_DeploymentConfig) | <li>Implementar uma floresta separada sem qualquer fidedignidades?</li> <li>Implementar uma nova floresta com a Federação?</li> <li>Implementar uma nova floresta com fidedignidade de floresta do Active Directory do Windows Server ou Kerberos?</li> <li>Expandir floresta Corp ao implementar uma réplica Cc?</li> <li>Expandir floresta Corp ao implementar um novo domínio subordinado ou uma árvore de domínio?</li> | <li>Segurança</li> <li>Conformidade</li> <li>Custo</li> <li>RDP e tolerância a falhas</li> <li>Compatibilidade de aplicações</li> |
| [Topologia de site do Windows Server Active Directory](#BKMK_ADSiteTopology) | Como pode configurar o sub-redes, sites e hiperligações de sites com a rede Virtual do Azure para otimizar o tráfego e minimizar o custo? | <li>Definições de site e de sub-rede</li> <li>Propriedades de ligação de sites e notificação de alteração</li> <li>Compressão de replicação</li> |
| [Endereçamento IP e DNS](#BKMK_IPAddressDNS) | Como configurar endereços IP e resolução do nome? | <li>Utilize o cmdlet de utilizar o AzureStaticVNetIP conjunto para atribuir um endereço IP estático</li> <li>Instalar o servidor de DNS do Windows Server e configurar as propriedades de rede virtual com o nome e endereço IP da VM que aloja as funções de servidor do Centro de dados e DNS</li> |
| [Distribuído geo CDs](#BKMK_DistributedDCs) | Como criar uma réplica para CDs separada redes virtual? | Se o seu topologia de site do Active Directory requer CDs no geografias que corresponde ao diferentes regiões Azure, que pretende criar sites do Active Directory em conformidade. [Configurar a rede virtual a conectividade de rede virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para criar uma réplica entre controladores de domínio no redes virtuais separadas. |
| [CDs só de leitura](#BKMK_RODC) | Utilizar CDs só de leitura ou graváveis? | <li>HBI/PII atributos de filtro</li> <li>Filtrar segredos</li> <li>Limite o tráfego de saída</li> |
| [Catálogo global](#BKMK_GC) | Instalar o catálogo global? | <li>Floresta único domínio, certifique todos os CDs GC</li> <li>Para vários domínios floresta, GC são necessários para autenticação</li> |
| [Método de instalação](#BKMK_InstallMethod) | Como instalar o Centro de dados no Azure? | Quer: <li>Instalar o AD DS utilizar o Windows PowerShell ou Dcpromo</li> <li>Deslocar-se VHD de um centro de dados virtual no local</li> |
| [Posicionamento da base de dados do Windows Server AD DS e SYSVOL](#BKMK_PlaceDB) | Onde pretende guardar o Windows Server AD DS base de dados, registos de início e SYSVOL? | Altere os valores predefinidos Dcpromo.exe. Estes crítico do Active Directory ficheiros *deve* ser colocados no Azure dados discos em vez de discos do sistema operativo que implementam escrita em cache. |
| [Cópia de segurança e restauro](#BKMK_BUR) | Como salvaguardar e recuperar dados? | Criar cópias de segurança do Estado de sistema |
| [Configuração do servidor de Federação](#BKMK_FedSrvConfig) | <li>Implementar uma nova floresta com a Federação na nuvem?</li> <li>Implementar o AD FS no local e expor um proxy na nuvem?</li> | <li>Segurança</li> <li>Conformidade</li> <li>Custo</li> <li>Acesso a aplicações por parceiros de negócios</li> |
| [Configuração de serviços na nuvem](#BKMK_CloudSvcConfig) | Um serviço na nuvem implicitamente é implementado a primeira vez que criar uma máquina virtual. Necessita implementar os serviços em nuvem adicionais? | <li>Uma VM ou VMs necessitam de exposição direta na Internet?</li> <li> O serviço requer balanceamento de carga?</li> |
| [Requisitos do servidor de Federação para IP pública e privada endereçar (dinâmico IP vs. virtual IP)](#BKMK_FedReqVIPDIP) | <li>A instância do Windows Server AD FS necessitam de ser alcançadas diretamente a partir da Internet?</li> <li>A aplicação que está a ser implementada na nuvem requer as suas próprias endereço IP de acesso à Internet e porta?</li> | Criar um serviço na nuvem para cada endereço IP virtual necessário pela sua implementação |
| [Configuração do Windows Server AD FS elevada disponibilidade](#BKMK_ADFSHighAvail) | <li>Quantos nós no meu farm de servidores do Windows Server AD FS?</li> <li>Quantos nós para implementar o meu farm de proxy do Windows Server AD FS?</li> | RDP e tolerância a falhas |

### <a name="BKMK_NetworkTopology"></a>Topologia da rede

Para poder cumprir os requisitos de DNS do Windows Server AD DS e consistência de endereço IP, é necessário primeiro criar uma [rede virtual Azure](../virtual-network/virtual-networks-overview.md) e anexar o seu máquinas virtuais à mesma. Durante a sua criação, tem de decidir se alargar opcionalmente conectividade à rede empresarial no local, que liga Transparente Azure máquinas virtuais ao máquinas no local, este é obtida a utilizar tecnologias de VPN tradicionais e requer que um ponto final de VPN ser apresentado quando se clica no limite da rede da empresa. Isto é, a VPN é iniciada a partir do Azure à rede empresarial, não vice versa.

Tenha em atenção que aplicadas taxas adicionais ao expandir uma rede virtual à sua rede no local para além dos encargos padrão que se aplicam a cada VM. Especificamente, existem taxas para tempo de CPU do gateway rede Virtual do Azure e para o tráfego de saída geradas por cada VM que comunica com máquinas no local ao longo da VPN. Para mais informações sobre taxas de tráfego de rede, consulte o artigo [Azure preços por rapidamente](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuração de implementação do Centro de dados

A forma como configurar o Centro de dados depende os requisitos para o serviço que pretende executar no Azure. Por exemplo, poderá implementar uma nova floresta, isolada da sua própria floresta da empresa, para testar uma prova de conceito, uma nova aplicação ou algumas outras projeto de curto prazo que necessita de serviços de diretório, mas acesso não específico para recursos da empresa internos.

Como dos benefícios, floresta isolada que CC não replica no local CDs que resulta em gerados pelo sistema de si próprio, o tráfego de rede menos saída diretamente reduzir os custos. Para mais informações sobre taxas de tráfego de rede, consulte o artigo [Azure preços por rapidamente](http://azure.microsoft.com/pricing/).

Outro exemplo, suponha que possui requisitos de privacidade para um serviço, mas o serviço depende do acesso ao seu interno Windows Server Active Directory. Se forem permitidos para alojar dados para o serviço na nuvem, pode implementar um novo domínio subordinado para sua floresta interna no Azure. Neste caso, pode implementar um centro de dados para o novo domínio subordinado (sem o catálogo global para ajudar a preocupações de privacidade do endereço). Neste cenário, juntamente com uma réplica implementação do Centro de dados, necessita de uma rede virtual para conectividade com o seu CDs no local.

Se criar uma nova floresta, escolha se pretende utilizar [fidedignidades do Active Directory](https://technet.microsoft.com/library/cc771397) ou [fidedignidades de Federação](https://technet.microsoft.com/library/dd807036). Equilibre os requisitos de ditado pelo compatibilidade, segurança, conformidade, custo e RDP. Por exemplo, para tirar partido de [autenticação selective](https://technet.microsoft.com/library/cc755844) poderá optar por implementar uma nova floresta no Azure e criar uma fidedignidade do Active Directory do Windows Server entre floresta no local e floresta na nuvem. Se a aplicação estiver em atenção em afirmações, no entanto, poderá implementar fidedignidades da Federação em vez de fidedignidades do Active Directory floresta. Outro factor será o custo para criar uma réplica mais dados através do prolongamento da sua no local Active Directory do Windows Server na nuvem ou gerar o tráfego de saída mais como resultado de autenticação e carga da consulta.

Requisitos para disponibilidade e tolerância a falhas também afetam a sua escolha. Por exemplo, se a ligação for interrompida, aplicações tirar partido de uma fidedignidade Kerberos ou uma federação confiar são todas provavelmente totalmente discriminadas, a menos que tenham implementado infraestrutura suficiente no Azure. Configurações de implementação alternativo como réplica CDs (gravável ou RODCs) aumentar a probabilidade de ser capaz de tolerar falhas de ligação.

### <a name="BKMK_ADSiteTopology"></a>Topologia de site do Windows Server Active Directory

Tem de definir corretamente sites e hiperligações de sites para poder optimizar o tráfego e minimizar o custo. Sites, hiperligações de sites e sub-redes afetam a topologia de replicação entre CDs e o fluxo de tráfego de autenticação. Considere as seguintes taxas de tráfego e, em seguida, implementar e configurar CDs de acordo com os requisitos do seu cenário de implementação:

- Existe uma taxa nominal por hora para o gateway propriamente dito:

 - Pode ser iniciado e parado conforme pretender

 - Se parado, Azure VMs são isolados a partir da rede da empresa

- O tráfego de entrada é gratuito

- O tráfego de saída é cobrado acordo com [Azure preços por rapidamente](http://azure.microsoft.com/pricing/). O utilizador pode otimizar propriedades de ligação de site entre sites no local e a nuvem da seguinte forma:

 - Se estiver a utilizar várias redes virtuais, configure hiperligações de sites e os seus custos adequadamente para impedir que o Windows Server AD DS dar prioridade o Azure site sobre uma que podem fornecer os mesmos níveis de serviço sem encargos. Também pode considerar desativar Bridge todos os sites opção de ligação (BASL) (que está ativada por predefinição). Este procedimento garante que criar uma réplica uns com os outros sites apenas directamente ligados. CDs nos sites transitivamente ligadas já não se conseguir criar uma réplica diretamente entre si, mas devem criar uma réplica da através de um site comum ou de sites. Se os sites intermédios tornam-se indisponível por algum motivo, não irá ocorrer replicação entre CDs nos sites transitivamente ligadas mesmo se conectividade entre os sites está disponível. Por fim, onde as secções do comportamento da replicação verbos permanecem conveniente, crie site pontes de ligação que contêm os adequado de hiperligações de sites e sites, tal como no local, sites de rede da empresa.

 - [Configurar os custos de ligação site](https://technet.microsoft.com/library/cc794882) corretamente para evitar tráfego inesperado. Por exemplo, se estiver ativada **Experimente o seguinte Site mais próximo** definição, certifique-se dos que sites de rede virtual não estão a próxima mais próxima do local, aumentando o custo associado do objeto de ligação de site que liga à rede empresarial do site do Azure.

 - Configure sites ligação [intervalos](https://technet.microsoft.com/library/cc794878) e [agendas de](https://technet.microsoft.com/library/cc816906) acordo com os requisitos de consistência e taxa das alterações do objeto. Alinhe a agenda de replicação com tolerância latência. CDs replicam apenas o último Estado de um valor, para que diminuir o intervalo de replicação pode poupar nos custos se existir um objeto suficiente alterar taxa.

- Se minimizar custos for uma prioridade, certifique-se de que a replicação é agendada e notificação de alteração não estiver ativada. Esta é a configuração de predefinido quando replicação entre sites. Não é importante se estiver a implementar um RODC numa rede virtual porque o RODC não serão replicadas quaisquer alterações saídas. Mas se implementar um centro de dados gravável, deve certificar-se de que a ligação de site não está configurada para criar uma réplica atualizações com frequência desnecessária. Se implementar um servidor de um catálogo global (GC), certifique-se de que todos os sites que contém um GC replica a partições de domínio a partir de uma origem de centro de dados num site que está ligado com uma ligação do site ou site ligações que têm um custo inferior que o GC no site do Azure.


- É possível reduzir ainda mais o tráfego de rede gerado pelo replicação entre sites alterando o algoritmo de compressão de replicação. O algoritmo de compressão é controlado pelo algoritmo de compressão de HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator de entrada de registo REG_DWORD. O valor predefinido é 3, que relacionado com o algoritmo de Xpress comprimir. Pode alterar o valor para 2, que altera o algoritmo para MSZip. Na maioria dos casos, isto aumenta a compressão, mas que tanto faz cargo a utilização da CPU. Para mais informações, consulte o artigo [topologia de replicação do Active Directory como funciona](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Endereçamento IP e DNS

Azure máquinas virtuais são atribuídas "endereços dedicada DHCP" por predefinição. Uma vez que os endereços de rede virtual Azure dinâmicos persistirem com uma máquina virtual para o tempo de vida da máquina virtual, os requisitos do Windows Server AD DS são preenchidos.

Como resultado, quando utiliza um endereço dinâmico no Azure, está na prática utilizando um endereço IP estático porque é encaminhável para o período da locação e o período da locação é igual à duração do serviço na nuvem.

No entanto, o endereço dinâmico é desatribuído se a VM é encerramento. Para impedir que o endereço IP a ser desatribuído, pode [utilizar o conjunto-AzureStaticVNetIP para atribuir um endereço IP estático](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Para resolução de nomes, implementar o seu próprio (ou tirar partido do seu existente) infraestrutura de servidor DNS; Azure fornecido pela DNS não satisfazer as necessidades de resolução do nome avançadas do Windows Server AD DS. Por exemplo,-não suporta registos SRV dinâmicos e assim sucessivamente. Resolução do nome é um item de configuração crítico para CDs e os clientes façam parte de um domínio. CDs tem de ser capazes de registar registos de recursos e resolver registos de recursos de outros Cc.
Por razões de tolerância e desempenho de falha, é ideal para instalar o serviço de DNS do Windows Server no CDs em execução no Azure. Em seguida, configure as propriedades de rede virtual Azure com o nome e endereço IP do servidor de DNS. Quando começar a outros VMs da rede virtual, as definições de resolução DNS cliente serão configuradas com o servidor DNS como parte da atribuição de endereços IP dinâmica.

> [AZURE.NOTE] Não consegue participar computadores no local para um domínio do Active Directory do Windows Server AD DS que está alojado no Azure diretamente através da Internet. Requisitos de portas para Active Directory e a operação de associação de domínio compor-prático para diretamente expõem as portas necessárias e na prática, uma CC inteira à Internet.

VMs registam o nome DNS automaticamente no arranque ou caso haja uma alteração de nome.

Para mais informações sobre este exemplo e outro exemplo que mostra como aprovisionar a primeira VM e instalar o AD DS no mesmo, consulte o artigo [instalar uma nova floresta no Microsoft Azure Active Directory](active-directory-new-forest-virtual-machine.md). Para mais informações sobre como utilizar o Windows PowerShell, consulte o artigo [Instalar o Azure PowerShell](../powershell-install-configure.md) e [Cmdlets de gestão de Azure](https://msdn.microsoft.com/library/azure/jj152841).

### <a name="BKMK_DistributedDCs"></a>Distribuído geo CDs

Azure oferece vantagens quando alojamento CDs múltiplas redes virtual diferente:

- Múltiplos site-tolerância a falhas

- Proximidade física para filiais (latência da inferior)

Para obter informações sobre como configurar a comunicação direta entre virtuais redes, consulte o artigo [Configurar a rede virtual a conectividade de rede virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>CDs só de leitura

Tem de escolher se pretende implementar CDs só de leitura ou graváveis. Lhe poderá ser inclinado para implementar RODCs porque não terá físico controlo sobre os mesmos, mas RODCs foram concebidos para ser implementada em localizações onde a sua segurança física é conta e risco, tal como filiais.

Azure não apresentar o risco de segurança físico do office de um ramo, mas RODCs ainda poderão revelar-se para ser mais rentável porque as funcionalidades que fornecem estão bem adequadas a estes ambientes embora por motivos muito diferente. Por exemplo, RODCs tem sem replicação de saída e podem seletivamente povoar segredos (palavras-passe). A desvantagem a falta destes segredos poderá implicar a pedido o tráfego de saída para validá-los como um utilizador ou autentica do computador. Mas segredos podem ser seletivamente pré-povoada e em cache.

RODCs fornecem uma vantagem adicional no e por volta preocupações HBI e PII porque pode adicionar os atributos que contêm dados sensíveis a maiúsculas e para o RODC filtrado conjunto de atributos (. sem seguro). O. sem seguro é um conjunto de atributos que não são replicadas para RODCs personalizável. Pode utilizar o. sem seguro como salvaguarda caso não são permitidos ou não pretende armazenar PII ou HBI no Azure. Para obter mais informações, consulte o artigo [RODC o atributo filtrado definido [(https://technet.microsoft.com/library/cc753459)].

Certifique-se de que as aplicações será compatíveis com RODCs planeia utilizar. Muitas aplicações com capacidade de Active Directory do Windows Server funcionam bem com RODCs, mas algumas aplicações podem executar tornar ou falhar se não possui acesso a um centro de dados gravável. Para mais informações, consulte o artigo [Guia de compatibilidade da aplicação de CDs só de leitura](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catálogo global

Tem de escolher se pretende instalar um catálogo global (GC). Numa floresta único domínio, deve configurar todos os CDs como servidores de catálogo global. Não irá aumentar os custos porque não haverá sem tráfego de replicação adicional.

Numa floresta com vários domínios, GC são necessários para expandir os membros do grupo Universal durante o processo de autenticação. Se não implementar um GC, das cargas de trabalho da rede virtual que autenticar um centro de dados no Azure indiretamente irão gerar tráfego de autenticação de saída para consultar os GC no local durante cada tentativa de autenticação.

Os custos associados GC são previsíveis de menos uma vez que alojam todos os domínios (na parte). Se a carga de trabalho aloja um serviço de acesso à Internet e autentica utilizadores perante Windows Server AD DS, podem ser completamente inesperados os custos. Para ajudar a reduzir consultas GC fora do site a nuvem durante a autenticação, pode [Ativar a cache de associação de grupo Universal](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Método de instalação

Tem de escolher como instalar os CDs da rede virtual:

- Promova CDs novos. Para mais informações, consulte o artigo [instalar uma nova floresta do Active Directory numa rede virtual Azure](active-directory-new-forest-virtual-machine.md).

- Mova o VHD de um centro de dados virtual no local para a nuvem. Neste caso, tem de garantir que o Centro de dados virtual no local é "movido," não "copiados" ou "clonado."

Utilize apenas Azure máquinas virtuais para CDs (por oposição a Azure "web" ou "trabalhador" função VMs). São resistentes e durabilidade de estado para um centro de dados é necessária. Azure máquinas virtuais foram concebidas das cargas de trabalho como CDs.

Não utilize SYSPREP para implementar ou clonar CDs. A capacidade de clonar CDs está apenas disponível início no Windows Server 2012. A funcionalidade clonagem requer o suporte para VMGenerationID no hipervisor subjacente. Hyper-V no Windows Server 2012 e Azure redes virtuais ambos suportam VMGenerationID, tal como fornecedores de software de terceiros virtualização.

### <a name="BKMK_PlaceDB"></a>Posicionamento da base de dados do Windows Server AD DS e SYSVOL

Selecione onde localizar a base de dados do Windows Server AD DS, registos de início e SYSVOL. Tem de ser implementadas em discos de dados do Azure.

> [AZURE.NOTE] Azure discos de dados são restringidos a 1 TB.

Unidades de disco dados faça não cache escritas por predefinição. Unidades de disco de dados que estejam anexadas a uma VM utilize escrita através da cache. Escrita através da cache torna-se de que a escrita está empenhada em armazenamento Azure resistente antes da operação de é concluída da perspetiva do sistema operativo da VM. Fornece durabilidade, cargo escritas ligeiramente mais lentas.

Isto é importante para o Windows Server AD DS porque cache de disco escrita-behind invalida suposições feitas pelo centro de dados. Windows Server AD DS tentativas para desativar a escrita em cache, mas é por excesso para o disco sistema IO satisfazê-lo. Falha ao desativar a escrita em cache poderá, em determinadas circunstâncias, introduzir anulação USN que resulta em lingering objetos e outros problemas.

Como prática recomendada para CDs virtuais, faça o seguinte:

- Defina a definição preferência de Cache de anfitrião do disco de dados Azure para nenhum. Isto impede que problemas com a cache de escrita para operações de AD DS.

- Armazenar a base de dados, registos de início e SYSVOL num dos mesmos dados disco ou discos de dados separado. Normalmente, isto é um disco separado do disco utilizado para o próprio sistema operativo. A chave takeaway é que a base de dados do Windows Server AD DS e SYSVOL não devem ser armazenados num tipo de disco sistema operativo de Azure. Por predefinição, o processo de instalação do AD DS instala estes componentes na pasta do sistema pasta % %, que não é recomendada para Azure.

### <a name="BKMK_BUR"></a>Cópia de segurança e restauro

Tenha em atenção o que é e não é suportado para cópia de segurança e restauro de um centro de dados em geral e, mais especificamente, aqueles ser executada numa VM. Consulte o artigo [cópia de segurança e restauro considerações para CDs virtualizados](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Crie cópias de segurança do Estado de sistema ao utilizar apenas o software cópia de segurança que seja especificamente ciente dos requisitos de cópia de segurança para o Windows Server AD DS, tal como cópia de segurança do Windows Server.

Não copiar ou clonar ficheiros VHD de CDs em vez de efetuar cópias de segurança regulares. Um restauro nunca deverá obrigatório, fazê-lo utilizar VHDs clonados ou copiados sem Windows Server 2012 e um hipervisor suportado irá apresentar USN bolhas.

### <a name="BKMK_FedSrvConfig"></a>Configuração do servidor de Federação

A configuração de servidores de Federação do Windows Server AD FS (STSs) depende em parte se as aplicações que pretende implementar no Azure necessitam aceder a recursos na sua rede no local.

Se as aplicações de cumprem os seguintes critérios, pode implementar as aplicações no isolamento da sua rede no local.

- Aceitam tokens de segurança SAML

- São exposable à Internet

- Não acedem recursos no local

Neste caso, configure o Windows Server AD FS STSs da seguinte forma:

1. Configure o domínio único floresta isolada no Azure.

2. Fornece acesso federado à floresta através da configuração de um farm de servidores de Federação do Windows Server AD FS.

3. Configure o Windows Server AD FS (farm de servidores de Federação e farm de proxy de servidor de Federação) na floresta no local.

4. Estabelece uma relação de confiança de Federação entre no local e o Azure instâncias do Windows Server AD FS.

Por outro lado, se a aplicações requerem acesso aos recursos no local, poderia configurar Windows Server AD FS com a aplicação no Azure da seguinte forma:

1. Configure a conectividade entre redes no local e Azure.

2. Configure um farm de servidores de Federação do Windows Server AD FS na floresta no local.

3. Configure um farm de proxy de servidores de Federação do Windows Server AD FS no Azure.

Esta configuração tem a vantagem de como reduzir a exposição de recursos no local, semelhantes ao configurar o Windows Server AD FS com aplicações numa rede de perímetro.

Tenha em atenção que quer cenário, pode estabelecer relações fidedignidades com mais fornecedores de identidade, se for necessário o colaboração de negócio para empresas.

### <a name="BKMK_CloudSvcConfig"></a>Configuração de serviços na nuvem

Serviços em nuvem são necessários se pretender expor uma VM diretamente à Internet ou para expor uma aplicação de balanceamento de carga de acesso à Internet. O que é possível uma vez que cada serviço em nuvem oferece um único endereço IP configurável virtual.

### <a name="BKMK_FedReqVIPDIP"></a>Requisitos do servidor de Federação para IP pública e privada endereçar (dinâmico IP vs. virtual IP)

Cada máquina virtual Azure recebe um endereço IP dinâmico. Um endereço IP dinâmico é um endereço privado acessível apenas a partir do Azure. No entanto, na maioria dos casos, vai ser necessário configurar um endereço IP virtual para sua implementações do Windows Server AD FS. O IP virtual é necessário para expor os pontos finais de Windows Server AD FS à Internet e será utilizado por federados parceiros e clientes para a autenticação e a gestão contínua. Um endereço IP virtual é uma propriedade de um serviço na nuvem que contém uma ou mais máquinas virtuais Azure. Se a aplicação deverá ter em consideração em afirmações implementada no Azure e Windows Server AD FS acesso à Internet e portas comuns de partilhar, cada irão necessitar um endereço IP virtual da sua própria e, por conseguinte, será necessário criar um serviço em nuvem para a aplicação e uma segunda para o Windows Server AD FS.

Para definições dos termos endereço IP virtual e em endereço IP dinâmico, consulte o artigo [termos e definições](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuração do Windows Server AD FS elevada disponibilidade

Enquanto é possível implementar os serviços de Federação do Windows Server AD FS autónomo, recomenda-se para implementar um farm com, pelo menos, dois nós para STS do AD FS e os proxies de ambientes de produção.

Consulte [Considerações de topologia do AD FS 2.0 implementação](https://technet.microsoft.com/library/gg982489) a [AD FS 2.0 estrutura Guide](https://technet.microsoft.com/library/dd807036) decida que se adaptem às quais opções de configuração de implementação melhor as suas necessidades.

> [AZURE.NOTE] Para poder aceder balanceamento de carga para pontos finais de FS do Windows Server AD no Azure, configure todos os membros do farm Windows Server AD FS no serviço na nuvem mesmo e, utilize a função de balanceamento de carga dos Azure para HTTP (predefinição 80) e portas HTTPS (predefinição 443). Para mais informações, consulte o artigo [sonda Balanceador de carga Azure](https://msdn.microsoft.com/library/azure/jj151530).
Windows Server rede NLB balanceamento de carga () não é suportada no Azure.
