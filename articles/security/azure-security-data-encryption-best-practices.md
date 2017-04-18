<properties
   pageTitle="Procedimentos recomendados de segurança dos dados e encriptação | Microsoft Azure"
   description="Este artigo fornece um conjunto de melhores práticas para a segurança dos dados e utilizar encriptação criada no Azure capacidades."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yuridio"/>

#<a name="azure-data-security-and-encryption-best-practices"></a>Melhores práticas de segurança dos dados Azure e encriptação

Uma das teclas de proteção de dados na nuvem é accounting para os Estados possíveis em que podem ocorrer os seus dados e controlos de que estão disponíveis para esse Estado. Para dados Azure práticas recomendadas de segurança e encriptação as recomendações será à volta de Estados dos seguintes dados:

- No resto: Isto inclui todas as informações de objetos de armazenamento, contentores e tipos de que existe estática no suportes físicos, ser-magnético ou disco óptico.

- Em trânsito: Quando os dados são transferidos entre componentes, localizações ou programas, tais como através da rede, através de um bus de serviço (a partir no local para o cloud e vice versa, incluindo as ligações de híbrido como ExpressRoute), ou durante o processo de entrada/saída, é considerada a ser em movimento.

Neste artigo discutimos um conjunto de dados Azure segurança e encriptação melhores práticas. Estas práticas recomendadas deriva encontram nossa experiência com a segurança dos dados Azure e encriptação e as experiências de clientes, como o seu próprio.

Para cada prática recomendada, explicaremos:

- O que é a melhor prática
- Por que motivo pretende ativar essa prática recomendada
- O que poderá o resultado se falhar ativar a melhor prática
- Alternativas possíveis para a melhor prática
- Como pode saber ativar a melhor prática

Este artigo de segurança dos dados Azure e as melhores práticas de encriptação baseia um parecer consenso e as capacidades de plataformas Azure e conjuntos de funcionalidade, à medida que existam ao tempo que este artigo foi escrito. Opiniões tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

Dados Azure segurança e encriptação melhores práticas outros fabricantes referidas neste artigo incluem:

- Impor a autenticação multifator
- Controlo de acesso (RBAC) baseado em funções de utilização
- Encriptar máquinas virtuais Azure
- Utilizar os modelos de segurança de hardware
- Gerir com seguros postos de trabalho
- Activar a encriptação de dados SQL
- Proteger os dados estão em trânsito
- Impor encriptação de dados ao nível do ficheiro


## <a name="enforce-multi-factor-authentication"></a>Impor a autenticação multifator

O primeiro passo no access de dados e de controlo no Microsoft Azure são autenticar o utilizador. [Azure Multifator autenticação (MFA)](../multi-factor-authentication/multi-factor-authentication.md) é um método de verificar a identidade do utilizador ao utilizar outro método que apenas um nome de utilizador e palavra-passe. Este autenticação método ajuda-o a salvaguardar informações acesso aos dados e aplicações enquanto o utilizador a procura de um processo de início de sessão simples da reunião.

Ao ativar o Azure MFA para os seus utilizadores, que está a adicionar uma segunda camada de segurança para suplementos de início de sessão do utilizador e operações. Neste caso, uma transação poderá estar a aceder a um documento localizado num servidor de ficheiros ou o SharePoint Online. Azure MFA também ajuda-o a IT para reduzir a probabilidade que uma credencial comprometida terá acesso aos dados da organização.

Por exemplo: se impor Azure MFA para os seus utilizadores e configure-o para utilizar uma chamada telefónica ou de uma mensagem de texto como verificação, se for comprometida credenciais do utilizador, o intruso não poderá aceder a qualquer recurso uma vez que ele não terão acesso ao telefone do utilizador. As organizações que não adicione esta extra camada de proteção de identidade são mais sensíveis para ataque roubo de credenciais, que pode conduzir a comprometer de dados.

Uma alternativa para as organizações que pretende manter o autenticação controlo no local é utilizar o [Servidor de autenticação Multifator Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), também denominado MFA no local. Utilizando este método ainda conseguir impor a autenticação multifator, mantendo a MFA servidor no local.

Para mais informações sobre Azure MFA, leia o artigo [Introdução ao Azure a autenticação Multifator na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Controlo de acesso (RBAC) baseado em funções de utilização
Restringir o acesso com base em princípios de segurança [saber](https://en.wikipedia.org/wiki/Need_to_know) e [privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) . Este é necessária para obter as organizações que pretendem impor políticas de segurança para acesso aos dados. Azure baseado em funções acesso controlo RBCA () pode ser utilizado para atribuir permissões a utilizadores, grupos e aplicações de um determinado âmbito. O âmbito de uma atribuição de funções pode ser uma subscrição, um grupo de recursos ou um único recurso.

Pode tirar partido de [funções RBAC incorporadas](../active-directory/role-based-access-built-in-roles.md) no Azure para atribuir privilégios aos utilizadores. Considere utilizar *Contribuinte de conta de armazenamento* para os operadores na nuvem que precisa para gerir contas de armazenamento e função *Clássica contribuinte de conta de armazenamento* para gerir contas de armazenamento clássica. Para os operadores na nuvem que necessita para gerir VMs e a conta de armazenamento, considere adicioná-las à função *Contribuinte Máquina Virtual* .

As organizações que não impor o controlo de acesso de dados por tirar partido da capacidades, tais como RBAC podem ser dar mais privilégios do que o necessário para os seus utilizadores. Isto pode levar a comprometer a dados por ter alguns utilizadores que tenham acesso aos dados que não devem tenham em primeiro lugar.

Pode obter mais informações sobre o Azure RBAC lendo o artigo [Azure Role-Based o controlo de acesso](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Encriptar máquinas virtuais Azure
Para organizações muitos, [encriptação de dados em repouso](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório no sentido e privacidade dos dados, conformidade soberania de dados. Encriptação do disco Azure permite que os administradores de TI encriptar discos Windows e Linux IaaS Máquina Virtual (VM). Encriptação do disco Azure tira partido a funcionalidade de BitLocker padrão do setor do Windows e a funcionalidade de encriptação DM do Linux para fornecer encriptação de volume do sistema operativo e os discos de dados.

Pode tirar partido do Azure disco encriptação para ajudar a proteger e proteger os seus dados para satisfazer as necessidades de conformidade e segurança organizacional. As organizações devem, também, considere utilizar a encriptação para ajudar a mitigar riscos acesso a dados relacionados com o não autorizado. Também é recomendável que encriptar unidades antes de a escrever dados sensíveis às mesmas.

Certifique-se encriptar volumes de dados do seu VM e o volume de arranque para proteger os dados no resto na sua conta de armazenamento Azure. Salvaguarde chaves de encriptação e segredos por tirar partido da [Azure chave cofre](../key-vault/key-vault-whatis.md).

Para os servidores do Windows no local, considere a encriptação seguinte melhores práticas:

- Utilizar o [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) para encriptação de dados
- Armazenar informações de recuperação em AD DS.
- Se existir qualquer preocupações que chaves do BitLocker tem sido comprometidas, recomendamos que quer formatar a unidade para remover todas as ocorrências dos metadados BitLocker a partir da unidade ou que desencriptar e encriptar toda a unidade de novamente.

As organizações que não impõem encriptação de dados estão mais predispostas exposta para problemas de integridade dos dados, tais como os utilizadores maliciosos ou rogue roubar dados e comprometida contas obterem acesso não autorizado aos dados no formato de limpar. Para além desses riscos, empresas que tem de cumprir normas da indústria, deve provar que são diligente e estiver a utilizar os controlos de segurança correta para melhorar a segurança dos dados.

Pode obter mais informações sobre o Azure disco encriptação lendo o artigo [Azure disco encriptação para Windows e Linux IaaS VMs](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Utilizar módulos de segurança do Hardware

Soluções de encriptação indústria utilizam teclas secretas para encriptar dados. Por isso, é importante que estas teclas são armazenadas em segurança. Gestão de chaves torna-se uma parte integrante proteção de dados, uma vez que irão ser utilizada para armazenar teclas secretas, que são utilizadas para encriptar dados.

Encriptação do disco Azure utiliza [Azure chave cofre](https://azure.microsoft.com/services/key-vault/) para ajudá-lo a controlar e gerir as chaves de encriptação do disco e segredos na sua subscrição do cofre chave, garantindo que todos os dados no discos máquina virtual são encriptados em repouso no seu armazenamento Azure. Deve utilizar Azure chave cofre chaves e a utilização de política de auditoria.

Existem muitos riscos inerentes relacionados com a não ter controlos de segurança adequado no local para proteger as teclas secretas que foram utilizadas para encriptar os seus dados. Se intrusos tem acesso às teclas secretas, eles poderão desencriptar os dados e potencialmente ter acesso às informações confidenciais.

Pode obter mais informações sobre recomendações gerais para a gestão de certificados no Azure lendo o artigo [Gestão de certificados no Azure: coisas a fazer e que não deve fazer](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Para obter mais informações sobre Azure chave cofre, leia o [artigo Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Gerir com seguros postos de trabalho

Uma vez que a maioria dos ataques de destino do utilizador final, o ponto final torna-se um dos pontos principais de ataque. Se um intruso comprometer o ponto final, ele pode tirar partido as credenciais do utilizador para aceder aos dados da organização. A maior parte dos ataques de ponto final são podem tomar partido do facto de que os utilizadores finais são os administradores na sua estações local de trabalho.

Pode reduzir esses riscos utilizando uma estação de trabalho Gestão seguro. Recomendamos que utilize um [Privilegiados acesso postos de trabalho (PÉGADAS)](https://technet.microsoft.com/library/mt634654.aspx) para reduzir a superfície de ataque postos de trabalho. Estes postos de trabalho Gestão seguro podem ajudá-lo mitigar algumas destas ataques ajudam a garantir que os seus dados são mais seguros. Certifique-se utilizar PÉGADAS proteger e bloquear estação de trabalho. Este é um passo importante para oferecem garantias de alta segurança para contas sensíveis, tarefas e proteção de dados.

Falta de proteção de ponto final poderá colocar os seus dados em risco, certifique-se para impor políticas de segurança em todos os dispositivos que são utilizados para consumir dados, independentemente da localização de dados (na nuvem ou no local).

Pode saber que mais sobre com privilégios de acesso estação de trabalho ao ler o artigo [Proteger privilegiados acesso](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Activar a encriptação de dados SQL

[Encriptação de dados transparente de base de dados do SQL Azure](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) ajuda a proteger contra a ameaça de atividade maliciosa executando encriptação em tempo real e desencriptar de base de dados, associadas cópias de segurança e ficheiros de registo da transação em repouso sem exigir alterações à aplicação.  TDE encripta o armazenamento dos toda uma base de dados utilizando uma chave simétrica denominada a chave de encriptação da base de dados.

Mesmo quando o armazenamento inteiro está encriptado, é muito importante também encriptar a base de dados. Esta é uma implementação de defesa no abordagem de profundidade para protecção de dados. Se estiver a utilizar [a base de dados do Azure SQL](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) e pretende proteger dados sensíveis a maiúsculas e como cartão de crédito ou números de segurança social, pode encriptar bases de dados com encriptação AES de 256 bit de 140-2 validado de FIPS que cumpra os requisitos de muitas normas da indústria (por exemplo, HIPAA, PCI).

É importante compreender que ficheiros relacionados com a [extensão do conjunto de dados de memória intermédia](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) não são encriptados quando uma base de dados é encriptado utilizando TDE. Tem de utilizar ferramentas de encriptação nível do sistema de ficheiros como BitLocker ou ficheiros relacionados com o [Sistema de ficheiros encriptados](https://technet.microsoft.com/library/cc700811.aspx) (EFS) para BPE.

Desde um utilizador autorizado tal como um administrador de segurança ou um administrador de base de dados pode aceder aos dados mesmo se a base de dados é encriptado com TDE, também deve seguir as recomendações abaixo:

- Autenticação do SQL ao nível da base de dados
- Azure AD autenticação utilizando funções RBAC
- Utilizadores e aplicações devem utilizar contas separadas para autenticar. Desta forma, pode limitar as permissões atribuídas a utilizadores e de aplicações e reduzir os riscos da atividade malicioso
- Implementar a segurança de nível de base de dados ao utilizar as funções de base de dados fixa (como db_datareader ou db_datawriter), ou pode criar funções personalizadas para a sua aplicação conceder permissões explícitas a objetos de base de dados selecionadas

As organizações que não estão a utilizar encriptação de nível de base de dados podem ser mais sensíveis para ataques poderão comprometer dados localizados nas bases de dados do SQL.

Pode obter mais informações sobre SQL TDE encriptação lendo o artigo [Transparente encriptação de dados com base de dados do SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Proteger os dados estão em trânsito

Proteger os dados estão em trânsito deve ser essencial parte da sua estratégia de proteção de dados. Uma vez que dados vai ser movidos e para trás a partir de muitas localizações, a recomendação geral é utilizar sempre protocolos SSL/TLS troca de dados em várias localizações diferentes. Em alguns casos, poderá querer identificar o canal de toda a comunicação entre o seu no local e na nuvem infraestrutura ao utilizar uma rede privada virtual (VPN).

Para mover entre o seu infraestrutura no local e o Azure de dados, deverá tomar em consideração garantias adequadas, como HTTPS ou VPN.

Para organizações que necessitam para proteger o acesso a partir de múltiplas estações de trabalho localizado no local para Azure, utilize o [Azure VPN do site para o site](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Para organizações que necessitam de acesso seguro a partir de uma estação de trabalho localizada no local a Azure, utilize [ponto-para-Site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Ligação de dados maiores conjuntos podem ser movidos através de uma WAN dedicada de alta velocidade como [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se optar por utilizar ExpressRoute, também pode encriptar os dados ao nível da aplicação utilizar [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para adicionado proteção.

Se está a interagir com armazenamento do Windows Azure através do Portal do Azure, todas as operações ocorrerem através de HTTPS. [Armazenamento REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) por HTTPS podem também ser utilizados para interagir com o [Armazenamento do Windows Azure](https://azure.microsoft.com/services/storage/) e [Base de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/).

As organizações que falharem de proteger os dados estão em trânsito são mais sensíveis para [homem no meio ataques](https://technet.microsoft.com/library/gg195821.aspx), [interceptem](https://technet.microsoft.com/library/gg195641.aspx) e utilização indevida de sessões. Estes ataques podem ser o primeiro passo no ganhar acesso a dados confidenciais.

Pode obter mais informações sobre a opção de Azure VPN lendo o artigo [planeamento e estrutura para o VPN Gateway](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Impor encriptação de dados ao nível do ficheiro

Outra camada de proteção que pode aumentar o nível de segurança para os seus dados está a encriptar o ficheiro propriamente dito, independentemente da localização do ficheiro.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os seus ficheiros e e-mail. Azure RMS funciona em vários dispositivos — telemóveis, tablets e PCs, protegendo dentro da sua organização e fora da sua organização. Esta funcionalidade é possível porque o Azure RMS adiciona um nível de proteção mantém-se com os dados, mesmo quando deixa limites da sua organização.

Quando utilizar o Azure RMS para proteger os seus ficheiros, está a utilizar norma da indústria criptografia com suporte completo da [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Quando aproveitar o Azure RMS para protecção de dados, tem a certeza de que a proteção fique com o ficheiro, mesmo se é copiado para o armazenamento que não seja sob o controlo de IT, tal como um serviço de armazenamento na nuvem. Ocorre o mesmo para ficheiros partilhados por correio eletrónico, o ficheiro estiver protegido como um anexo a uma mensagem de e-mail com instruções como abrir o anexo protegido.

Ao planear adoção do Azure RMS recomendamos o seguinte procedimento:

- Instale a [aplicação de partilha de RMS](https://technet.microsoft.com/library/dn339006.aspx). Esta aplicação integra-se com o Office aplicações instalando uma Office suplemento para que os utilizadores podem facilmente proteger ficheiros diretamente.
- Configurar aplicações e serviços para suportar o Azure RMS
- Crie [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que refletir as necessidades de negócio. Por exemplo: um modelo para dados secretos principais que devem ser aplicados em todos os principais secreta relacionados com mensagens de correio eletrónico.

As organizações que são fracas sobre proteção de [classificação de dados](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e o ficheiro poderão ser mais sensíveis às perdas de dados. Sem proteção de ficheiro adequado, organizações não será possível obter informações de negócio, monitorizar para abuse e evitar malicioso acesso aos ficheiros.

Pode obter mais informações sobre o Azure RMS lendo o artigo [Introdução ao Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
