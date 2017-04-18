<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de estrutura - Definir estratégia de proteção de dados | Microsoft Azure"
    description="Vou definir a estratégia de proteção de dados para a sua solução de identidade híbrido cumprir os requisitos de empresas que definiu."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Definir estratégia de proteção de dados para a sua solução de identidade híbrido

Nesta tarefa, irá definem a estratégia de proteção de dados para a sua solução de identidade híbrido cumprir os requisitos de empresas que definiu no:

- [Determinar requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
- [Determinar os requisitos de gestão de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Determinar os requisitos de controlo de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Determinar requisitos do incidente de resposta](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Definir opções de proteção de dados
Tal como foi explicado a no [Determinar requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD pode ser sincronizadas com o Active Directory Domain Services (AD DS) localizada no local. Esta integração permite às organizações tirar partido do Azure AD para verificar as credenciais do utilizador quando está a tentar aceder aos recursos da empresa. Isto pode ser feito para ambos os cenários: dados no resto no local e na nuvem.  Acesso a dados no Azure AD requer autenticação de utilizador através de um serviço de tokens de segurança (STS).

Uma vez autenticado, nome principal de utilizador (UPN) é de leitura a partir do token de autenticação e a partição de replicadas e é determinado contentor correspondente ao domínio do utilizador. Obter informações sobre a existência do utilizador, estado activado e função são utilizadas pelo sistema autorização para determinar se o pedido acesso ao inquilino de destino está autorizado para este utilizador nesta aula. Determinadas ações autorizadas (especificamente, criar utilizador, reposição de palavra-passe) crie um registo de auditoria que pode ser utilizado pelo administrador inquilino para gerir esforços de conformidade ou inquéritos.

Mover dados a partir do seu centro de dados no local para o armazenamento do Windows Azure através de uma ligação à Internet não podem ser viáveis devido ao volume de dados, a disponibilidade de largura de banda ou a outras considerações de sempre. O [Serviço de importação/exportação de armazenamento do Azure](../storage/storage-import-export-service.md) fornece uma opção baseada em hardware para colocar o/obtenção de grandes volumes de dados no armazenamento blob do. Permite-lhe enviar [encriptados BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) unidades de disco rígido diretamente para um centro de dados Azure onde operadores nuvem irão carregar o conteúdo à sua conta de armazenamento ou poderá transferir os seus dados Azure para as unidades para voltar para si. Apenas discos encriptados são aceites para que este processo (utilizando uma chave de BitLocker gerada pelo serviço durante a configuração de tarefa). A chave de BitLocker é fornecida Azure separadamente, assim, fornecendo terminar partilha de chave de banda.

Uma vez que os dados estão em trânsito podem demorar local em cenários diferentes, é igualmente importante saber que o Microsoft Azure utiliza [a rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para identificar o tráfego dos inquilinos uns dos outros, que utilizem medidas como firewalls nível anfitrião e convidado, filtragem de pacotes IP, bloqueio da porta e os pontos finais HTTPS. No entanto, a maior parte das comunicações internas do Azure, incluindo infraestrutura de infraestrutura e infraestrutura de cliente (no local), também é encriptados. Outro cenário importante é o as comunicações Azure centros de dados; A Microsoft gere redes para assegurar que não VM pode representar ou interceptar o endereço IP do outro. TLS/SSL é utilizado quando aceder ao armazenamento do Windows Azure ou bases de dados SQL ou quando ligar aos serviços em nuvem. Neste caso, o administrador de cliente é responsável por obter um certificado TLS/SSL e implementá-lo à sua infraestrutura de inquilino. Dados tráfego mover entre máquinas virtuais da mesma implementação ou entre inquilinos numa única implementação através do Microsoft Azure Virtual rede pode ser protegido através de protocolos de comunicação encriptada como HTTPS, SSL/TLS ou outras pessoas.

Dependendo de como as perguntas em [requisitos de proteção de dados de determinar](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)respondida, deverá conseguir determinar como pretende proteger os seus dados e como a solução de identidade híbrido irão ajudá-lo no que. A tabela mostra as opções suportadas pelo Azure que estão disponíveis para cada cenário de proteção de dados.


| Opções de proteção de dados         | Em repouso na nuvem | No resto no local | Quando estão em trânsito |
|---------------------------------|----------------------|---------------------|------------|
| Encriptação de unidade BitLocker      | X                    | X                   |            |
| SQL Server para encriptar as bases de dados | X                    | X                   |            |
| Encriptação VM para VM             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
Leia [conformidade pela funcionalidade](https://azure.microsoft.com/support/trust-center/services/) no [Centro de fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/) para saber mais sobre as certificações que seja compatível com cada serviço Azure.
Uma vez que as opções de proteção de dados utilizam uma abordagem barreiras, comparação entre estas opções não são aplicáveis para esta tarefa. Certifique-se de que são tirar partido da todas as opções disponíveis para cada Estado que serão os dados.

## <a name="define-content-management-options"></a>Definir as opções de gestão de conteúdo
Uma das vantagens em utilizar Azure AD para gerir a infraestrutura de identidade um híbrido são que o processo é totalmente transparente perspetiva do utilizador final. O utilizador irá tentar aceder a um recurso partilhado, o recurso requer autenticação, o utilizador terá que enviar um pedido de autenticação para Azure AD para obter o token e aceder ao recurso. Este processo inteiro acontece em segundo plano, sem interação do utilizador. Também é possível conceder permissão a um [grupo](active-directory-manage-groups.md#getting-started-with-access-management) de utilizadores para permitir que este executar determinadas ações comuns.

As organizações que são normalmente preocupações privacidade de dados necessitam de classificação de dados para a sua solução. Se já está a utilizar infraestrutura no local atual classificação de dados, é possível tirar partido do Azure AD como o repositório principal para a identidade do utilizador. Uma ferramenta comuns que é utilizado no local para a classificação de dados é denominada [Toolkit de classificação de dados](https://msdn.microsoft.com/library/Hh204743.aspx) para o Windows Server 2012 R2. Esta ferramenta pode ajudar a identificar, classificar e proteger os dados em servidores de ficheiro no seu nuvem privado. Também é possível tirar partido da [Classificação de ficheiro automática](https://technet.microsoft.com/library/hh831672.aspx) no Windows Server 2012 para realizar esta tarefa.

Se a sua organização não tem a classificação de dados no local, mas tem de proteger ficheiros sensíveis sem adicionar novo servidores no local, podem utilizar do Microsoft [Azure serviço de gestão de direitos](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS utiliza encriptação, identidade e políticas de autorização para ajudar a proteger os seus ficheiros e e-mail e funciona em vários dispositivos — telemóveis, tablets e PCs. Uma vez que o Azure RMS é um serviço na nuvem, não é necessário para configurar explicitamente fidedignidades com outras organizações antes de poder partilhar conteúdo protegido com os mesmos. Se já tiver do Office 365 ou uma Azure AD do diretório, colaboração entre organizações automaticamente é suportada. Também pode sincronizar apenas os atributos de diretório que necessita de Azure RMS para suportar uma identidade comum para as suas contas do Active Directory no local, utilizando o Azure sincronização serviços do Active Directory (AAD Sync) ou ligação do Azure AD.

Uma parte essencial de gestão de conteúdo é compreender a quem está a aceder ao qual o recurso, por isso é importante para a solução de gestão de identidades uma funcionalidade de registo formatado. Azure AD fornece registo mais de 30 dias incluindo:

- Alterações no membro da função (ex: utilizador adicionado à função de Administrador Global)
- Atualizações de credenciais (ex: alterações de palavra-passe)
- Gestão de domínios (ex: verificar um domínio personalizado, remover um domínio)
- Adicionar ou remover aplicações
- Gestão de utilizadores (ex: adicionar, remover, atualizar um utilizador)
- Adicionar ou remover licenças

>[AZURE.NOTE]
Leia a [segurança do Microsoft Azure e gestão do registo de auditoria](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) para saber mais acerca das capacidades do registo no Azure.
Dependendo de como as perguntas em [requisitos de gestão de conteúdo de determinar](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)respondida, deverá conseguir determinar como pretende que o conteúdo ser gerido na sua solução de identidade híbrido. Apesar de todas as opções apresentadas no quadro 6 serem capazes de integração com o Azure AD, é importante definir o que é mais adequado às suas necessidades de negócio.

| Opções de gestão de conteúdo                                                               | Vantagens                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Desvantagens                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralizada no local (servidor de gestão de direitos do Active Directory)                      | Controlo total sobre a infraestrutura de servidor responsável pela classificar os dados <br> Funcionalidade incorporada no Windows Server, sem necessidade de licença extra ou de subscrição <br> Pode ser integrado com o Azure AD num cenário de híbrido <br> Suporta informações direitos (IRM) as capacidades da gestão de serviços da Microsoft Online como o Exchange Online e SharePoint Online, bem como Office 365 <br> Suporta produtos de servidor do Microsoft no local, como o Exchange Server, SharePoint Server e servidores de ficheiros que executem o Windows Server e infraestrutura de classificação de ficheiro (FCI). | Manutenção superior, (manter o com as atualizações, configuração e atualiza os potenciais), desde IT é o proprietário do servidor <br> Exigir uma infraestrutura server no local<br> Doesn'tleverage capacidades Azure nativo                                     |
| Centralizada na nuvem (Azure RMS)                                                     | Mais fácil de gerir comparada com a solução no local <br> Pode ser integrado com o AD DS num cenário de híbrido <br>  Totalmente integrado com o Azure AD <br> Não necessita de um servidor no local para implementar o serviço <br> Suporta no local produtos de servidor do Microsoft, como o Exchange Server, SharePoint, servidor e servidores de ficheiros que executem o Windows Server e o ficheiro de classificação, infraestrutura (FCI) <br> IT, pode ter controlo total chave do seu inquilino com capacidade BYOK.                                                                                    | Organização tem de ter uma subscrição de nuvem que suporta o RMS <br> Organização tem de ter um directory Azure AD para suportar a autenticação de utilizador para o RMS                                                                                  |
| Híbrido (Azure RMS integrado com o, no local Active Directory Rights Management Server) | Este cenário é acumulado as vantagens de ambos os, centralizado no local e na nuvem.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Organização tem de ter uma subscrição de nuvem que suporta o RMS <br> Organização tem de ter um directory Azure AD para suportar a autenticação de utilizador para o RMS, <br> Requer uma ligação entre o serviço em nuvem Azure e no local infraestrutura |

## <a name="define-access-control-options"></a>Definir opções de controlo de acesso
Ao tirar partido da autenticação, autorização e o acesso controlam capacidades disponíveis no Azure AD que poderão ativar a sua empresa para utilizar um repositório de identidade central permitindo aos utilizadores e parceiros para utilizar sessão único (SSO) conforme apresentado na figura abaixo:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Gestão centralizada e totalmente a integração com outros directórios

Azure Active Directory fornece serviço single sign-on para milhares de SaaS aplicações e no local aplicações web. Leia o [lista de compatibilidade de Federação do Azure Active Directory: fornecedores de identidade de terceiros que podem ser utilizados para implementar o início de sessão único](https://msdn.microsoft.com/library/azure/jj679342.aspx) artigo para obter mais detalhes sobre o terceiros SSO foram testadas pela Microsoft. Esta funcionalidade permite organização implementar uma variedade de cenários B2B ao tempo que mantém o controlo de gestão de identidade e de acesso. No entanto, durante a B2B estruturar processo é importante para compreender o método de autenticação que será utilizado pelo parceiro e valida se este método for suportado pela Azure. Atualmente, estes são métodos suportados pelo Azure AD:

- Segurança afirmação Markup Language (SAML)
- OAuth
- Kerberos
- Tokens
- Certificados


>[AZURE.NOTE] Leia os [Protocolos de autenticação do Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) para saber mais detalhes sobre cada protocolo e das suas capacidades no Azure.

Utilizar o suporte do Azure AD, aplicações empresariais móvel podem utilizar a mesma experiência de autenticação de serviços móveis fácil para permitir que os funcionários inicie sessão na suas aplicações móveis com as respetivas credenciais do Active Directory da empresa. Com esta funcionalidade é suportada Azure AD como um fornecedor de identidades nos serviços do Mobile juntamente com os outros fornecedores de identidade suportamos já (que incluem Accounts da Microsoft, ID do Facebook, Google ID e Twitter ID). Se as aplicações no local utiliza credenciais do utilizador que se encontra no AD DS da empresa, o acesso de utilizadores a chegar a partir da nuvem e parceiros deve ser transparente. Pode gerir o controlo de acesso condicional do utilizador para aplicações web (baseada na nuvem), da web API, serviços em nuvem da Microsoft, 3º aplicações de SaaS empresas e aplicações de cliente (telemóvel) nativos e ter as vantagens de segurança, auditoria, elaboração de relatórios num só local. No entanto, recomenda-se para validar isto num ambiente não produção ou com um período limitado de utilizadores.


>[AZURE.TIP] é importante no Office Accounting que Azure AD não tem de política de grupo como AD DS tem. Para poder impor políticas para dispositivos terá uma solução de gestão de dispositivos móveis como o [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).

Assim que o utilizador é autenticado utilizando Azure AD, é importante avaliar o nível de acesso que o utilizador terá-lo. O nível de acesso que o utilizador terá através de um recurso pode variar, enquanto Azure AD pode adicionar uma camada de segurança adicional ao controlo de acesso a alguns recursos, também terá de manter em mente que o recurso próprio também pode fazer com as suas próprias lista de controlo de acesso separado, tal como o controlo de acesso para ficheiros localizados no servidor de ficheiros. A figura abaixo resume os níveis de controlo de acesso que pode ter um cenário de híbrido:

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Cada interação no diagrama mostrava no X figura representa um cenário de controlo de acesso que pode ser coberto por Azure AD. Abaixo tem uma descrição de cada cenário:

1. acesso condicional a aplicações que são alojado no local: pode utilizar dispositivos registados com as políticas de acesso para aplicações que estão configuradas para utilizar o AD FS com o Windows Server 2012 R2. Para mais informações sobre como configurar o acesso condicional para no local, consulte o artigo [configurar o acesso a condicional no local com o Azure Active Directory dispositivo registo](active-directory-conditional-access-on-premises-setup.md).
2. controlo Azure portal de gestão de acesso de: Azure também tem a funcionalidade para controlar o acesso ao Portal de gestão utilizando RBAC (função controlo de acesso com base). Este método permite a empresa restringir a quantidade de operações que um indivíduo pode efetuar assim que ele tem acesso ao Portal de gestão do Azure. Ao utilizar RBAC para controlar o acesso ao portal do, administradores de TI AC acesso delegado utilizando as seguintes abordagens de gestão do access:

 - Atribuição de funções com base no grupo: pode atribuir acesso a grupos do Azure AD que podem ser sincronizados a partir do seu Active Directory local. Permite-lhe tirar partido de investimentos existentes em que a sua organização tenha feito em ferramentas e processos para gerir grupos. Também pode utilizar a funcionalidade de gestão de grupo delegado do Azure AD Premium.
 - Tirar partido criado em funções no Azure: pode utilizar funções de três — proprietário, contribuinte e Reader, para se certificar de que os utilizadores e grupos tem permissão para fazer apenas as tarefas que precisam de fazer o seu trabalho.
 - Granular acesso aos recursos: pode atribuir funções para utilizadores e grupos para uma subscrição em particular, grupo de recursos ou um recurso individual Azure tal como um Web site ou da base de dados. Desta forma, pode garantir que os utilizadores têm acesso a todos os recursos que necessitam e sem acesso aos recursos que não necessita de gerir.

>[AZURE.NOTE] Leia o [controlo de acesso baseado em funções no Azure](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) para saber mais detalhes sobre esta funcionalidade. Para os programadores que são criação de aplicações e para personalizar o controlo de acesso para os mesmos, também é possível utilizar funções de aplicação do Azure AD para autorização. Reveja esta [Web App-RoleClaims-DotNet exemplo](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) sobre como construir a sua aplicação para utilizar esta funcionalidade.

3. acesso condicional para aplicações do Office 365 com o Microsoft Intune: administradores IT podem aprovisionar o políticas de dispositivo de acesso condicional para proteger os recursos da empresa, enquanto ao mesmo tempo permitindo que os técnicos de informação no compatível com dispositivos para aceder os serviços. Para mais informações, consulte o artigo [Condicional políticas de dispositivo de acesso para serviços do Office 365](active-directory-conditional-access-device-policies.md).

4. acesso condicional para as aplicações de Saas: [Esta funcionalidade](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) permite-lhe configurar regras de acesso a autenticação multifator de por aplicação e a capacidade de bloquear o acesso de utilizadores não numa rede fidedigno. Pode aplicar as regras de autenticação multifator a todos os utilizadores que estão atribuídos para a aplicação ou apenas para os utilizadores dentro de grupos de segurança especificado. Os utilizadores podem ser excluídos do requisito de autenticação multifator se estão a aceder a aplicação a partir de um endereço IP que no dentro da organização de rede.

Uma vez que as opções para o controlo de acesso utilizam uma abordagem barreiras, comparação entre estas opções não são aplicáveis para esta tarefa. Certifique-se de que são tirar partido da todas as opções disponíveis para cada cenário que necessita de controlar o acesso aos seus recursos.

## <a name="define-incident-response-options"></a>Definir opções de resposta incidente
Pode dar assistência a Azure AD IT para identidade potencial riscos de segurança no ambiente por monitorizar a atividade de utilizador, pode IT tirar partido do Azure AD acesso e relatórios de utilização de funcionalidade para ganham visibilidade sobre a integridade e a segurança do diretório da sua organização. Com esta informação, um administrador de TI pode determinar melhor onde possíveis riscos de segurança podem situar-se para que possam adequadamente planear a mitigar esses riscos.  [Subscrição do Azure AD Premium](active-directory-get-started-premium.md) tem um conjunto de relatórios de segurança que pode ativar IT para obter estas informações. [Relatórios do Azure AD](active-directory-view-access-usage-reports.md) são categorizados como é mostrado abaixo:

- **Relatórios de anomalia**: contêm eventos que encontrámos para ser anómalo de início de sessão. Nosso objetivo é torná-lo em mente essa actividade e permite-lhe possam fazer uma imagem sobre se um evento é suspeito.
- **Relatório de aplicação integrada**: fornece informações para como nuvem aplicações estão a ser utilizadas na sua organização. Azure Active Directory oferece a integração com milhares de aplicações na nuvem.
- **Relatórios de erro**: indicar erros que podem ocorrer quando Aprovisiona o contas para aplicações externas.
- **Relatórios específicos de utilizador**: Mostrar o dispositivo/início de sessão dos dados de atividade para um utilizador específico.
- **Registos de atividade**: contêm um registo de todos os eventos auditados na últimas 24 horas, últimos 7 dias, ou últimos 30 dias, bem como alterações de atividade de grupo e atividade de reposição e registo de palavra-passe.

>[AZURE.TIP]
Outro relatório que também o pode ajudar a equipa de resposta do incidente a trabalhar num incidente é o relatório de [utilizador com credenciais fuga](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) .  Este relatório superfícies correspondências entre estas lista credenciais fuga e o inquilino.

Outros importantes incorporada em relatórios do Azure AD que pode ser utilizada durante um inquérito de resposta incidente e são:

- **Repor a palavra-passe atividade**: fornecer o administrador com informações para como ativamente reposição de palavra-passe está a ser utilizada na organização.
- **Repor a palavra-passe actividade de registo**: fornece informações no qual os utilizadores tem registado seus métodos para repor a palavra-passe, e quais os métodos de tiver selecionado.
- **Atividade de grupo**: Fornece um histórico das alterações ao grupo (ex: os utilizadores adicionados ou removidos) que foram iniciadas no painel de acesso.

Para além da capacidade de elaboração de relatórios de core disponível no Azure AD Premium que podem ser utilizadas durante um processo de inquérito de resposta do incidente, pode IT também tirar partido do relatório de auditoria para obter informações tais como:

- Alterações no membro da função (ex: utilizador adicionado à função de Administrador Global)
- Atualizações de credenciais (ex: alterações de palavra-passe)
- Gestão de domínios (ex: verificar um domínio personalizado, remover um domínio)
- Adicionar ou remover aplicações
- Gestão de utilizadores (ex: adicionar, remover, atualizar um utilizador)
- Adicionar ou remover licenças

Uma vez que as opções de resposta incidente utilizam uma abordagem barreiras, comparação entre estas opções não são aplicáveis para esta tarefa. Certifique-se de que são tirar partido da todas as opções disponíveis para cada cenário que necessita para utilizar a funcionalidade de elaboração de relatórios do Azure AD como parte do processo de resposta do incidente da sua empresa.

## <a name="next-steps"></a>Próximos passos
[Determinar tarefas de gestão de identidades híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)


## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
