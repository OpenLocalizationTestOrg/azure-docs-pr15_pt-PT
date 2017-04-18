<properties
   pageTitle="Descrição geral de segurança do Azure máquinas virtuais | Microsoft Azure"
   description=" Azure máquinas virtuais dar-lhe a flexibilidade de Virtualização sem ter de comprar e manter o hardware físico que é executada a máquina virtual.  Este artigo fornece uma descrição geral das principais funcionalidades de segurança Azure que podem ser utilizadas com máquinas virtuais do Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-virtual-machines-security-overview"></a>Descrição geral de segurança do Azure máquinas virtuais

Azure máquinas virtuais permite-lhe implementar uma vasta gama de computação soluções de uma forma ágil. Com o suporte para o Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e dos serviços do Azure BizTalk, pode implementar qualquer carga de trabalho e qualquer idioma em praticamente qualquer sistema operativo.

Uma máquina virtual Azure dá-lhe a flexibilidade de Virtualização sem ter de comprar e manter o hardware físico que é executada a máquina virtual.  Pode criar e implementar as aplicações com assurance que os seus dados são protegida e estão seguros no nossos altamente seguros centros de dados.

Com o Azure, pode criar soluções com segurança avançada, compatível com que:

- Proteger o seu máquinas virtuais contra vírus e software maligno
- Encriptar os dados confidenciais
- Proteger o tráfego de rede
- Identificar e detectar ameaças
- Cumprir requisitos de conformidade

O objetivo deste artigo é fornecer uma descrição geral das principais funcionalidades de segurança Azure que podem ser utilizadas com máquinas virtuais. Fornecemos também ligações para artigos que fornecem detalhes de cada funcionalidade, de modo que pode obter mais informações.  

As principais Azure Virtual Machine capacidades de segurança para ser abordados neste artigo:

- Antimalware
- Módulo de hardware de segurança
- Encriptação de disco máquina virtual
- Cópia de segurança de máquina virtual
- Recuperação de Azure Site
- Funcionamento em rede virtual
- Gestão de políticas de segurança e relatórios
- Conformidade

## <a name="antimalware"></a>Antimalware

Com o Azure, pode utilizar o software de antimalware de fornecedores de segurança como Microsoft, Symantec, tendência Micro, McAfee e Kaspersky para proteger a sua máquinas virtuais do ficheiros maliciosos, adware e outras ameaças. Consulte a secção obter mais informações abaixo para localizar artigos sobre o parceiro de soluções.

Microsoft Antimalware para serviços em nuvem Azure e máquinas virtuais é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares.  Microsoft Antimalware fornece configuráveis alertas quando conhecido tentativas de software malicioso ou indesejado para instalar a próprio ou executar no seu sistemas Azure.

Microsoft Antimalware é uma solução de agente de único para aplicações e ambientes de inquilino, concebidos para ser executado em segundo plano sem intervenção humano. Pode implementar proteção com base nas necessidades da sua aplicação das cargas de trabalho, com um dos básica seguro por predefinição ou configuração personalizada, incluindo a monitorização de antimalware avançada.

Quando implementar e activar o Microsoft Antimalware, as seguintes funcionalidades principais estão disponíveis:

- Proteção em tempo real - monitores atividade em serviços em nuvem e em máquinas virtuais para detetar e bloquear a execução de software maligno.
- Análise agendada - periodicamente executa alvo de pesquisa para detetar software maligno, incluindo programas em execução ativamente.
- Software maligno remediação - leva automaticamente ação no software maligno detectado, como eliminar ou colocar em quarentena ficheiros maliciosos e limpar entradas de registo malicioso.
- Atualizações de assinatura - automaticamente instalações as assinaturas de proteção mais recentes (definições de vírus) para se certificar de proteção está atualizado numa determinada previamente frequência.
- Atualizações de Antimalware Engine – automaticamente actualiza o motor de Microsoft Antimalware.
- Atualizações de plataforma antimalware – automaticamente atualiza a plataforma Microsoft Antimalware.
- Proteção ativa - relatórios aos metadados de telemetria Azure sobre ameaças detectadas e recursos suspeitos para se certificar de resposta rápida e permite em tempo real assinatura síncrono entrega através do sistema Microsoft da proteção ativa (mapas).
- Exemplos de elaboração de relatórios - fornece relatórios e amostras ao serviço Microsoft Antimalware para o ajudar a otimizar o serviço e ativar a resolução de problemas.
- Exclusões – permite a aplicação e administradores do serviço configurar determinados ficheiros, processos e unidades para exclui-los a partir de proteção e pesquisa de desempenho e outras razões.
- Recolha de eventos antimalware - regista o estado de funcionamento do serviço antimalware, atividades suspeitas e ações remediação tomadas no registo de eventos de sistema operativo e recolhe-las para a conta de armazenamento do Windows Azure do cliente.

Saiba mais: para obter mais informações sobre software antimalware para proteger a sua máquinas virtuais, consulte:

- [Microsoft Antimalware para serviços em nuvem Azure e máquinas virtuais](../security/azure-security-antimalware.md)
- [Implementar soluções Antimalware em máquinas virtuais Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Como instalar e configurar tendência Micro abrangente segurança como um serviço numa VM do Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Novas opções de Antimalware para proteger máquinas virtuais Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [Soluções de segurança no Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Segurança do hardware módulo

Encriptação e autenticação não melhorar a segurança, a menos que as teclas próprios estão protegidas. Pode simplificar a gestão e a segurança dos seus segredos críticos e chaves, guardando-los no Azure chave cofre. Chave cofre fornece a opção para armazenar as suas chaves nos módulos de segurança do hardware (Os HSMs) certificados para FIPS normas de nível 2 140-2. Chaves de encriptação do SQL Server para cópia de segurança ou [encriptação de dados transparente](https://msdn.microsoft.com/library/bb934049.aspx) podem todas as ser armazenadas num chave cofre com qualquer teclas ou segredos das suas aplicações. Permissões e acesso a estes itens protegidas são geridos através do [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Saiba mais:

- [O que é o Azure chave Cofre?](../key-vault/key-vault-whatis.md)
- [Introdução ao Azure chave Cofre](../key-vault/key-vault-get-started.md)
- [Blogue de chave cofre Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Encriptação de disco máquina virtual

Encriptação do disco Azure é uma nova funcionalidade que permite-lhe encriptar discos do Windows e Linux Azure Virtual Machine. Encriptação do disco Azure utiliza a funcionalidade de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e a funcionalidade de [encriptação dm](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume do sistema operativo e os discos de dados.

A solução está integrada com o Azure chave cofre para o ajudar a controlar e gerir as chaves de encriptação do disco e segredos na sua subscrição do cofre chave, garantindo que todos os dados no discos máquina virtual são encriptados em repouso no seu armazenamento Azure.

Saiba mais:

- [Encriptação de Azure do disco para Windows e Linux IaaS VMs](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
- [Encriptação de Azure do disco para Linux e máquinas virtuais de Windows](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
- [Encriptar uma máquina virtual](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Cópia de segurança de máquina virtual

Cópia de segurança do Azure é uma solução dimensionável que protege os dados da aplicação com zero investimento de capital e os custos de funcionamento mínimos. Erros de aplicação podem danificar os seus dados e podem apresentar erros humanos se erros nas suas aplicações. Com cópia de segurança do Azure, são protegidas máquinas virtuais que executem o Windows e Linux.

Saiba mais:

- [O que é o Azure cópia de segurança?](../backup/backup-introduction-to-azure-backup.md)
- [Caminho de formação de cópia de segurança Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [Serviço de cópia de segurança Azure - perguntas mais frequentes](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Recuperação de Azure Site

Uma parte importante da estratégia BCDR da sua organização é perceber como manter das cargas de trabalho da empresa e apps para cima e em execução quando ocorrem falhas planeadas e não. Recuperação de Site Azure ajuda orquestrar replicação, activação pós-falha e recuperação de cargas de trabalho e aplicações para que estão disponíveis a partir de uma localização secundária se a sua localização principal vai para baixo.

Recuperação de sites:

- **Simplifica sua estratégia de BCDR** — recuperação de sites torna mais fácil lidar replicação, activação pós-falha e recuperação de múltiplos cargas de trabalho de negócio e apps a partir de uma única localização. Recuperação de sites orquestra replicação e activação pós-falha mas não interceptar os dados da aplicação ou tiver qualquer informação acerca do mesmo.
- **Fornece a replicação flexível** — utilizando recuperação de sites pode criar uma réplica das cargas de trabalho em execução em máquinas virtuais de Hyper-V, máquinas virtuais de VMware e servidores físicos Windows/Linux.
- **Activação de suporte e recuperação** — recuperação de Site fornece activações pós-falha de teste para suportar Brocas de recuperação de falhas sem afetar os ambientes de produção. Também pode executar activações pós-falha planeada com uma perda de dados de zero esperado em caso de falhas ou activações pós-falha não planeada com perda de dados mínima (consoante a frequência de replicação) para catástrofes inesperados. Depois de falha na ligação, pode reposição de recurso aos seus sites principais. Recuperação de site fornece os planos de recuperação que podem incluir scripts e livros de automatização Azure, para que pode personalizar activação e recuperação de aplicações com várias camadas.
- **Centro de dados secundário elimina** — pode criar uma réplica para um site secundário no local ou para Azure. Utilizar o Azure como um destino para recuperação de falhas elimina o custo e complexidade da manutenção de um site secundário. Dados replicados são armazenados no armazenamento do Windows Azure.
- **Integra-se com tecnologias BCDR existentes** — recuperação de sites parceiros com outras funcionalidades BCDR de aplicação. Por exemplo, pode utilizar a recuperação de sites para proteger o SQL Server back-end de cargas de trabalho da empresa. Isto inclui suporte nativo para SQL Server AlwaysOn gerir o activação pós-falha de grupos de disponibilidade.

Saiba mais:

- [O que é o Azure recuperação de Site?](../site-recovery/site-recovery-overview.md)
- [Como funciona a recuperação de Azure Site?](../site-recovery/site-recovery-components.md)
- [O que das cargas de trabalho estão protegidas por Azure recuperação de Site?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Funcionamento em rede virtual

Máquinas virtuais precisa de conectividade de rede. Para suportar esse requisito, Azure requer máquinas virtuais de estar ligado a uma rede Virtual Azure. Uma rede Virtual Azure é uma construção lógica incorporada na parte superior o ferro rede Azure física. Cada rede Virtual do Azure lógica está isolada de todas as outras Azure Virtual redes. Este isolamento ajuda a garantir que o tráfego de rede no seu implementações não está acessível a outros clientes do Microsoft Azure.

Saiba mais:

- [Descrição geral de segurança de rede Azure](security-network-overview.md)
- [Descrição geral de rede virtual](../virtual-network/virtual-networks-overview.md)
- [Funcionalidades de rede e parcerias para cenários de empresa](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestão de políticas de segurança e relatórios

Centro de segurança do Azure ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre, a segurança dos seus recursos Azure. Fornece a segurança integrada monitorização e gestão de políticas entre as subscrições do Azure, ajuda a Deteta ameaças caso contrário, aceda não ser e funciona com uma Ecossistema vasto de soluções de segurança.

Centro de segurança do Azure ajuda-o a otimizar e monitorizar a segurança de máquina virtual por:

- Fornecer máquina virtual [recomendações de segurança](../security-center/security-center-recommendations.md) de tais como aplicar atualizações de sistema, configurar pontos finais de ACL, ativar antimalware, ativar grupos de segurança de rede e aplicar encriptação do disco.
- Monitorizar o estado da sua máquinas virtuais

Saiba mais:

- [Introdução ao centro de segurança do Azure](../security-center/security-center-intro.md)
- [Perguntas mais frequentes sobre o Centro de segurança do Azure](../security-center/security-center-faq.md)
- [Planeamento do Centro de segurança do Azure e operações](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformidade

Azure máquinas virtuais está certificada para FISMA, FedRAMP, HIPAA, PCI DSS nível 1 e outros programas de conformidade chave. Desta certificação torna mais fácil para as suas próprias aplicações Azure cumprir requisitos de conformidade e para a sua empresa endereçar uma vasta gama de requisitos de regulamentação internacionais e domésticos.

Saiba mais:

- [Centro de fidedignidade do Microsoft: conformidade](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [Nuvem fidedigna: Microsoft Azure segurança, privacidade e conformidade](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
