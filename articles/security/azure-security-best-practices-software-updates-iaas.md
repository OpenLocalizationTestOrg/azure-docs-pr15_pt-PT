<properties
   pageTitle="Melhores práticas para atualizações de Software no Microsoft Azure IaaS | Microsoft Azure"
   description="Artigo fornece uma coleção de melhores práticas para atualizações de software num ambiente do Microsoft Azure IaaS.  Destina-se para profissionais de TI e os analistas de segurança quem lidar com alterar controlo, gestão de atualização e elementos de software em diariamente, incluindo os responsáveis esforços de segurança e conformidade da respetiva organização."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Práticas recomendadas para atualizações de software no Microsoft Azure IaaS

Antes de consultar qualquer tipo de debate sobre procedimentos recomendados para um ambiente do Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) , é importante compreender os cenários quais que terá gerir atualizações de software e as responsabilidades. O diagrama abaixo deve ajudar a compreender estes limites:

![Modelos de nuvem e responsabilidades](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

A coluna mais à esquerda mostra as sete responsabilidades (definidas nas secções que se seguem) que as organizações deverá tomar em consideração, as quais contribuam para a segurança e privacidade um ambiente informático.
 
Classificação de dados e responsabilidade e proteção de ponto final & cliente são as responsabilidades que estão exclusivamente no domínio de clientes e responsabilidades física, anfitrião e de rede estão no domínio de fornecedores de serviço de nuvem nos modelos PaaS e SaaS. 

As responsabilidades restantes são partilhadas entre os clientes e na nuvem fornecedores de serviço. Algumas responsabilidades requerem o CSP e o cliente gerir e administrar a responsabilidade em conjunto, incluindo auditoria dos seus domínios. Por exemplo, considere identidade e aceder a gestão de quando utilizar o Azure Active Directory Services; a configuração de serviços como o autenticação multifator é por excesso para o cliente, mas assegurar a funcionalidade efectiva é da responsabilidade do Microsoft Azure.

> [AZURE.NOTE] Para obter mais informações sobre as responsabilidades partilhadas na nuvem, leia o artigo [Responsabilidades partilhada para computação de nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) 

Estes princípios mesmo aplicam-se num cenário de híbrido onde sua empresa está a utilizar o Azure IaaS VMs que comunicar com os recursos no local, conforme mostrado no diagrama abaixo.

![Cenário de típica híbrida com o Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>Avaliação inicial

Mesmo que a sua empresa já está a utilizar um sistema de gestão de atualização e já tiver políticas de atualização de software no local, é importante frequentemente revisitar avaliações política anteriores e atualizá-las com base nos seus requisitos atuais. Isto significa que precisa de estar familiarizado com o estado atual de recursos na sua empresa. Para chegar este estado, tem de saber:

-   Os computadores físicos e virtuais na sua empresa.

-   Sistemas operativos e versões em execução em cada um destes computadores físicos e virtuais.

-   Atualizações de software atualmente instaladas em cada computador (versões de service pack, atualizações de software e outras modificações).

-   A função de cada computador executa na sua empresa.

-   As aplicações e programas em execução em cada computador.

-   A propriedade e informações de contacto para cada computador.

-   Apresentam os ativos no seu ambiente e os respetivos valor relativa a determinar quais as áreas necessita de mais atenção e proteção.

-   Problemas conhecidos segurança e processos da empresa tem no local para identificar problemas de segurança novo ou alterações de nível de segurança.

-   Contra-medidas que foram implementadas a proteger o seu ambiente.

Deverá actualizar estas informações regularmente e deve ser facilmente acessíveis às envolvidos do processo de gestão de atualização de software.

## <a name="establish-a-baseline"></a>Estabelecer uma linha base

Uma parte do processo de gestão de atualização de software importante está a criar iniciais instalações padrão de versões do sistema operativo, aplicações e hardware para computadores da empresa; Estes são chamados planos base. Um plano base é a configuração de um produto ou sistema estabelecido num ponto específico no tempo. Uma aplicação ou um plano base do sistema operativo, por exemplo, fornece a capacidade de recriar um computador ou serviço para um estado específico.

Linhas de base fornecem a base para localizar e corrigir problemas potenciais e simplificam o processo de gestão de atualização de software, reduzindo o número de atualizações de software, que tem de implementar da empresa e ao aumentar a sua capacidade para monitorizar a conformidade.

Após efetuar a auditoria inicial da sua empresa, deve utilizar as informações que são obtidas a partir de auditoria para definir uma linha de base operacional para os componentes IT dentro do ambiente de produção. Um número de linhas de base pode ser necessário, consoante os diferentes tipos de hardware e software implementado para produção.

Por exemplo, alguns servidores requerem uma atualização de software para as impedir de pendente quando o mesmo entra o processo de encerramento quando a executar o Windows Server 2012. Uma linha base para estes servidores deve incluir esta atualização de software.

Em grandes organizações, muitas vezes é útil dividir os computadores da empresa em categorias de elementos e manter cada categoria numa linha de base padrão, utilizando as mesmas versões do software e atualizações de software. Em seguida, pode utilizar estas categorias de elementos no dar prioridade de uma distribuição de atualização de software.

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>Subscrever os serviços de notificação de atualização de software adequado

Depois de efectuar uma auditoria inicial do software utilizado na sua empresa, deve determinar o melhor método de receber notificações de novas atualizações de software para cada produto de software e versão. Consoante o produto de software, o melhor método de notificação poderá notificações por correio electrónico, Web sites ou publicações do computador.

Por exemplo, a segurança resposta centro MSRC (Microsoft) responde a todas as preocupações relacionadas com segurança sobre produtos da Microsoft e fornece o serviço de boletins de segurança no Microsoft, uma notificação de correio electrónico gratuito de vulnerabilidades recentemente identificadas e atualizações de software que são lançadas para resolva este vulnerabilidade. Pode subscrever este serviço na http://www.microsoft.com/technet/security/bulletin/notify.mspx.

## <a name="software-update-considerations"></a>Considerações de actualização de software

Depois de efectuar uma auditoria inicial do software utilizado na sua empresa, deve determinar os requisitos de configurar o seu sistema de gestão de atualização de software, que depende do sistema de gestão de atualização de software que está a utilizar. Para WSUS ler [Melhores práticas com os serviços de atualização do Windows Server](https://technet.microsoft.com/library/Cc708536), Centro de sistema do leia [planeamento atualizações de Software no Gestor de configuração](https://technet.microsoft.com/library/gg712696).

No entanto, existem algumas considerações gerais e as melhores práticas que pode aplicar, independentemente da solução que está a utilizar como é mostrado nas secções que se segue.

### <a name="setting-up-the-environment"></a>Configurar o ambiente

Considere as seguintes práticas ao planear a configurar o software atualizar o ambiente de gestão:

-   **Coleções de atualização de software de produção criar com base em critérios estáveis**: em geral, a utilização de critérios estáveis criar coleções de sites para o seu inventário de atualização de software e distribuição ajudarão para simplificar a todas as fases do processo de gestão de atualização de software. Podem incluir os critérios estáveis a versão do sistema operativo cliente instalado e nível do service pack, a função de sistema ou organização de destino.

-   **Criar coleções de pré-produção que incluem computadores de referência**: A coleção de pré-produção deve incluir representante de configurações das versões do sistema operativo, linha de software de negócio e outro software em execução da empresa.

Também deve considerar onde o servidor de atualização de software será localizado, se irá estar no infraestrutura do Azure IaaS na nuvem ou se irá estar no local. Esta é uma decisão importante porque tem de avaliar a quantidade de tráfego entre recursos no local e infraestrutura Azure. Leia [ligar uma rede no local para uma rede virtual do Microsoft Azure](https://technet.microsoft.com/library/Dn786406.aspx) para obter mais informações sobre como ligar a sua infraestrutura no local ao Azure.

As opções de estrutura que vai determinar onde o servidor de atualização será localizado também variam de acordo com a sua infraestrutura atual e o sistema de atualização de software que está atualmente a utilizar. Para WSUS leia o artigo [Implementar o Windows Server Update Services na sua organização](https://technet.microsoft.com/library/hh852340.aspx) e para o Gestor de configuração do Centro de sistema [Planear Sites e hierarquias no Gestor de configuração](https://technet.microsoft.com/library/Gg712681.aspx)de ler.

### <a name="backup"></a>Cópia de segurança

Cópias de segurança regulares são importantes não apenas para a software actualização de plataforma de gestão de propriamente dito mas também para os servidores que será atualizado. As organizações que têm um [processo de gestão de alterar](https://technet.microsoft.com/library/cc543216.aspx) num local irão necessitar IT para justificar das suas razões porque é que o servidor tem de ser atualizados, o tempo de inatividade estimado e impacto possível. Para se certificar de que tem uma configuração anulação num local no caso de falha de uma atualização, certifique-se agregar o sistema regularmente.

Algumas opções de cópia de segurança para Azure IaaS incluem:

-   [Proteção de carga de trabalho IaaS Azure utilizando o Gestor de proteção de dados](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Criar uma cópia de segurança máquinas virtuais Azure](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>Monitorização

Deverá ser possível executar relatórios normais para monitorizar o número de em falta ou instalado atualizações, ou em atualizações com o estado incompleto, para cada atualização de software que está autorizado. Da mesma forma, relatórios atualizações de software que ainda não estão autorizados podem facilitar a decisões de implementação mais fácil.

Também deverá tomar em consideração as seguintes tarefas:

-   Realize uma auditoria das atualizações de segurança aplicável e instalado para todos os computadores na sua empresa.

-   Autorize e implementar as atualizações para os computadores adequados.

-   Monitorizar o inventário e atualize o estado de instalação e o progresso de todos os computadores na sua empresa.

Considerações gerais para além para que foram explicados neste artigo, também deve considerar cada produto se melhor prática, por exemplo: Se tiver uma VM no Azure com o SQL Server, certifique-se de que está a seguir a recomendação de atualizações de software desse produto.

## <a name="next-steps"></a>Próximos passos

Utilize as orientações descritas neste artigo para ajudá-lo para determinar as melhores opções atualizações de software para máquinas virtuais do Azure IaaS. Existem muitas semelhanças entre software atualização melhores práticas para um centro de dados tradicional versus Azure IaaS, por conseguinte, é recomendado que avalie as políticas de atualização de software atual para incluir Azure VMs e incluir as práticas recomendadas relevantes partir deste artigo do processo de atualização de software geral.
