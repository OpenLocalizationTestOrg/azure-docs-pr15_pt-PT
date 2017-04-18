<properties
    pageTitle="Ligação do Azure AD: Instalação personalizada | Microsoft Azure"
    description="Este documento apresenta detalhes sobre as opções de instalação personalizada para a ligação do Azure AD. Utilize estas instruções para instalar o Active Directory através da ligação do Azure AD."
    services="active-directory"
    keywords="o que é a ligação do Azure AD, instale o Active Directory, componentes necessários para Azure AD"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath"/>

# <a name="custom-installation-of-azure-ad-connect"></a>Instalação personalizada de ligação do Azure AD
Azure AD Connect **definições personalizadas** é utilizado quando pretende mais opções para a instalação. É utilizado se tiver várias florestas ou se pretender configurar funcionalidades opcionais não abrangidas na instalação express. É utilizada em todos os casos onde a opção de [**instalação rápida**](active-directory-aadconnect-get-started-express.md) não satisfaça sua implementação ou topologia.

Antes de iniciar a instalação de ligação do Azure AD, certifique-se para [Transferir a ligação do Azure AD](http://go.microsoft.com/fwlink/?LinkId=615771) e conclua exigidos pré passos no [ligação do Azure AD: Hardware e pré-requisitos](../active-directory-aadconnect-prerequisites.md). Também, certifique-se de que tem necessários contas disponíveis, tal como descrito nas [contas de ligação do Azure AD e permissões](active-directory-aadconnect-accounts-permissions.md).

Se definições personalizadas não corresponder a topologia, por exemplo para atualizar o DirSync, consulte o artigo [relacionado com a documentação](#related-documentation) para outros cenários.

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Instalação definições personalizadas de ligação do Azure AD

### <a name="express-settings"></a>Definições de Express
Nesta página, clique em **Personalizar** para iniciar uma instalação das definições personalizadas.

### <a name="install-required-components"></a>Instalar componentes necessários
Quando instalar os serviços de sincronização, pode deixar a secção de configuração opcional desmarcada e ligação do Azure AD configura tudo automaticamente. Configura uma instância do SQL Server 2012 Express LocalDB, criar grupos adequados e atribuir permissões. Se pretender alterar as predefinições, pode utilizar a tabela seguinte para compreender as opções de configuração opcional que estão disponíveis.

![Componentes necessários](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

Configuração opcional  | Descrição
------------- | -------------
Utilizar um servidor de SQL existente | Permite-lhe especificar o nome do SQL Server e o nome da instância. Escolha esta opção se já tiver um servidor de base de dados que pretende utilizar. Introduza o nome da instância seguido por um ponto e vírgula e número de porta no **Nome da instância** se o SQL Server não tiver ativado a navegação.
Utilizar uma conta de serviço existente | Por predefinição a ligação do Azure AD cria uma conta de serviço local para os serviços de sincronização utilizar. A palavra-passe é gerado automaticamente e desconhecido para a pessoa a ligação do Azure AD a instalar. Se utilizar um servidor SQL remoto ou utilize um proxy que precisa de autenticação, terá de um serviço de conta do domínio e souber a palavra-passe. Nestes casos, introduza a conta de serviço para utilizar. Certifique-se de que o utilizador a executar a instalação é uma SA no SQL, pelo que pode ser criado um início de sessão para a conta de serviço. Consulte o artigo [permissões e contas de ligação do Azure AD](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation)
Especifique os grupos de sincronização personalizado | Por predefinição ligação do Azure AD cria quatro grupos local para o servidor quando os serviços de sincronização estão instalados. Estes grupos são: grupo de administradores, grupo de operadores, grupo de procurar e o grupo de reposição de palavra-passe. Pode especificar o seus próprio grupos aqui. Os grupos tem de ser locais no servidor e não podem ser localizados no domínio.

### <a name="user-sign-in"></a>Utilizador iniciar sessão
Depois de instalar componentes necessários, são-lhe pedido para selecionar os seus utilizadores único início de sessão método. A tabela seguinte fornece uma breve descrição das opções disponíveis. Para obter uma descrição completa dos métodos de entrada, consulte o artigo [utilizador iniciar sessão](../active-directory-aadconnect-user-signin.md).

![Utilizador início de sessão](./media/active-directory-aadconnect-get-started-custom/usersignin.png)

Opção de início de sessão única | Descrição
------------- | -------------
Sincronização de palavra-passe | Os utilizadores estão iniciar sessão aos serviços em nuvem Microsoft, tal como o Office 365, utilizando a mesma palavra-passe utilizam na sua rede no local. As palavras-passe de utilizadores são sincronizadas para Azure AD como hash uma palavra-passe e autenticação ocorre na nuvem. Para mais informações, consulte a [sincronização de palavra-passe](../active-directory-aadconnectsync-implement-password-synchronization.md) .
Federação com o AD FS | Os utilizadores estão iniciar sessão aos serviços em nuvem Microsoft, tal como o Office 365, utilizando a mesma palavra-passe utilizam na sua rede no local.  Os utilizadores redirecionados para o seu local de AD FS instância para iniciar sessão e a autenticação ocorre no local.
Se não for configurada | Nem funcionalidade é instalada e configurada. Escolha esta opção se já tiver um servidor de Federação festa 3º ou outra solução existente no local.

### <a name="connect-to-azure-ad"></a>Ligar ao Azure AD
Em ligar ao ecrã do Azure AD, introduza uma conta de administrador global e a palavra-passe. Se tiver seleccionado **federação com o AD FS** na página anterior, não inicie sessão com uma conta num domínio que planear a ativação de para a Federação. Uma recomendação é utilizar uma conta de no domínio **onmicrosoft.com** predefinido, que é fornecido com o seu diretório do Azure AD.

Esta conta só é utilizada para criar uma conta de serviço no Azure AD e não é utilizada quando tiver concluído o assistente.  
![Utilizador início de sessão](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Se a sua conta de administrador global tem MFA ativado, em seguida, tem de fornecer a palavra-passe novamente no início de sessão no menu de contexto e conclua o desafio MFA. O desafio poderão ser um fornecer um código de verificação ou uma chamada telefónica.  
![Início de sessão do utilizador no MFA](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

A conta de administrador global também pode fazer [A gestão de identidades privilegiados](../active-directory-privileged-identity-management-getting-started.md) ativado.

Se receber um erro e a ter problemas com a conectividade, em seguida, consulte [resolução de problemas de conectividade](../active-directory-aadconnect-troubleshoot-connectivity.md).

## <a name="pages-under-the-section-sync"></a>Páginas na secção de sincronização

### <a name="connect-your-directories"></a>Ligar a sua diretórios
Para ligar ao seu serviço de domínio do Active Directory, a ligação do Azure AD tem as credenciais de uma conta com permissões suficientes. Pode introduzir a peça de domínio no formato de NetBios ou FQDN, ou seja, FABRIKAM\syncuser ou fabrikam.com\syncuser. Esta conta pode ser uma conta de utilizador normal, pois necessita apenas as permissões de leitura predefinidas. No entanto, dependendo do seu cenário, poderá ter mais permissões. Para obter mais informações, consulte o artigo [Azure AD ligar contas e permissões](../active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account)

![Ligar o directório](./media/active-directory-aadconnect-get-started-custom/connectdir.png)

### <a name="azure-ad-sign-in-configuration"></a>Azure AD início de sessão no configuração
Esta página permite-lhe para rever os domínios UPN presentes no local AD DS e quais foram verificadas no Azure AD. Esta página também permite-lhe configurar o atributo a utilizar para o userPrincipalName.

![Domínios não verificados](./media/active-directory-aadconnect-get-started-custom/aadsigninconfig.png)  
Rever todos os domínios marcados **Não adicionada** e **Não verificado**. Certifique-se de foram verificados desses domínios que utiliza no Azure AD. Quando tiver verificado os domínios do seu, clique no símbolo de atualização. Para obter mais informações, consulte o artigo [Adicionar e verificar o domínio](../active-directory-add-domain.md)

**UserPrincipalName** - o atributo userPrincipalName é que os utilizadores de atributo utilizar quando iniciar sessão para Azure AD e no Office 365. Os domínios utilizados, também conhecido como o-sufixo UPN, deverão ser verificados no Azure AD antes dos utilizadores estão sincronizados. A Microsoft recomenda-se para manter o userPrincipalName atributo predefinido. Se este atributo está não encaminháveis e não pode ser verificado, em seguida, é possível selecionar outro atributo. Pode, por exemplo, selecione correio eletrónico como o atributo mantém o ID de início de sessão no. Utilizar outro atributo que userPrincipalName é conhecido como **ID alternativo**. O valor do atributo ID alternativo deve segui-lo a norma RFC822. Um ID alternativo pode ser utilizado com a sincronização de palavra-passe e Federação.

>[AZURE.WARNING]
Utilizar um ID alternativo não é compatível com todas as cargas de trabalho do Office 365. Para obter mais informações, consulte [Configurar alternativo ID de início de sessão](https://technet.microsoft.com/library/dn659436.aspx).

### <a name="domain-and-ou-filtering"></a>Domínio e filtragem de or
Por predefinição é sincronizados todos os domínios e ou. Se existem alguns domínios ou ou que não pretende sincronizar para o Azure AD, pode desmarcar estas domínios e ou.  
![Filtragem DomainOU](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png) esta página no Assistente de está a configurar a filtragem baseada no domínio. Para mais informações, consulte o artigo [Filtrar com base no domínio](../active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering).

Também é possível que alguns domínios não são acessíveis devido a restrições de firewall. Estes domínios são desmarcados por predefinição e tem um aviso.  
![Domínios inacessíveis](./media/active-directory-aadconnect-get-started-custom/unreachable.png)  
Se vir este aviso, certifique-se de que estes domínios são facto inacessíveis e o aviso é esperado.

### <a name="uniquely-identifying-your-users"></a>Identifica os seus utilizadores
A correspondência entre funcionalidade florestas permite-lhe definir como os utilizadores a partir do seu florestas do AD DS são representados no Azure AD. Um utilizador ou pode ser representado apenas uma vez em todas as florestas ou ter uma combinação de contas ativadas e desativadas. O utilizador também pode ser representado como um contacto em algumas florestas.

![Exclusivo](./media/active-directory-aadconnect-get-started-custom/unique.png)

Definição | Descrição
------------- | -------------
[Os utilizadores são representados apenas uma vez em todas as florestas](../active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | Todos os utilizadores são criados como objectos individuais no Azure AD. Os objetos não são associados na metaverse.
[Atributo de correio](../active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | Esta opção associa os utilizadores e contactos se o atributo de correio tem o mesmo valor em florestas diferentes. Utilize esta opção quando os seus contactos foram criados utilizando GALSync.
[ObjectSID e msExchangeMasterAccountSID / atributo msRTCSIP-OriginatorSid](../active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest) | Esta opção associa um utilizador ativado na floresta conta com um utilizador numa floresta de recursos desativado. No Exchange, esta configuração é conhecida como uma caixa de correio ligada. Também pode ser utilizada esta opção se só utiliza o Lync e Exchange não estiver presente na floresta de recursos.
atributos sAMAccountName e MailNickName | Esta opção associa sobre os atributos de onde é esperado que o ID de início de sessão para o utilizador pode ser encontrado.
Um atributo específico | Esta opção permite-lhe selecionar o seu próprio atributo. **Limitação:** Certifique-se selecionar um atributo que já pode ser encontrado na metaverse. Se escolher um atributo personalizado (não está na metaverse), não poderá concluir o assistente.

**Origem de âncora** - sourceAnchor o atributo é um atributo que é imutáveis durante o tempo de vida de um objeto de utilizador. É a chave primária, o utilizador no local com o utilizador a ligar no Azure AD. Uma vez que o atributo não pode ser alterado, tem de planear um bom atributo utilizar. Um bom candidato é GUID de objecto. Este atributo não é alterado, a menos que a conta de utilizador é movida entre florestas/domínios. Num ambiente do floresta múltipla onde mover contas entre florestas, deve ser utilizado noutro atributo, tal como um atributo com o campo IDdeEmpregado. Evite atributos que pretende alterar quando uma pessoa casar ou alterar atribuições. Não é possível utilizar atributos com um @-sign, para e-mail e userPrincipalName não podem ser utilizados. O atributo também é sensível às maiúsculas assim quando mover um objeto entre florestas, certifique-se preservar maiúsculas/minúsculas. Atributos binários são codificado em base64, mas outros tipos de atributo permanecem no seu estado codificado. Em cenários de Federação e algumas interfaces do Azure AD, este atributo é também conhecido como immutableID. Podem encontrar mais informações sobre a âncora de origem nos [conceitos de design](../active-directory-aadconnect-design-concepts.md#sourceAnchor).

### <a name="sync-filtering-based-on-groups"></a>Sincronização filtragem baseada em grupos
A filtragem de funcionalidade grupos permite-lhe sincronizar apenas um pequeno subconjunto de objetos para um piloto. Para utilizar esta funcionalidade, crie um grupo para o efeito no Active Directory no local. Em seguida, adicione utilizadores e grupos que devem ser sincronizados Azure AD como membros diretos. Pode adicionar e remover utilizadores para este grupo para manter a lista de objetos que devem estar presentes no Azure AD mais tarde. Todos os objetos que pretende sincronizar tem de ser um membro do grupo direto. Os utilizadores, grupos, contactos e computadores/dispositivos têm de ser todos os membros diretos. Membros do grupo interno aninhado não for resolvido. Quando adiciona um grupo de um membro, apenas o grupo propriamente dito é adicionada e não os membros.

![Filtragem de sincronização](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING]
Esta funcionalidade só está destinada para suportar uma implementação piloto. Não utilize-la numa implementação full-blown de produção.

Na implementação de produção full-blown, que vai ser difícil manter um único grupo com todos os objetos para sincronizar. Em vez disso, deve utilizar um dos métodos na [filtragem de configurar](../active-directory-aadconnectsync-configure-filtering.md).

### <a name="optional-features"></a>Funcionalidades opcionais
Este ecrã permite-lhe selecionar as funcionalidades opcionais para a sua cenários específicos.

![Funcionalidades opcionais](./media/active-directory-aadconnect-get-started-custom/optional.png)

>[AZURE.WARNING]
Se tiver atualmente DirSync ou ativo do Azure AD Sync, não ative as funcionalidades de repetição de escrita na ligação do Azure AD.

Funcionalidades opcionais | Descrição
------------------- | -------------
Implementação híbrida do Exchange | A funcionalidade de implementação híbrida do Exchange permite coexistência de caixas de correio do Exchange ambas no local e no Office 365. Ligação do Azure AD está a sincronizar um conjunto específico de [atributos](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) a partir do Azure AD novamente no seu diretório no local.
Aplicação do Azure AD e filtragem de atributo | Ao ativar o Azure AD aplicação e filtragem de atributo, pode ser adaptado o conjunto de atributos sincronizados. Esta opção adiciona duas páginas de configuração mais para o assistente. Para mais informações, consulte o artigo [aplicação Azure AD e filtragem de atributo](#azure-ad-app-and-attribute-filtering).
Sincronização de palavra-passe | Se tiver seleccionado federação como a solução de início de sessão, pode ativar esta opção. Sincronização de palavra-passe, em seguida, pode ser utilizada como uma opção de cópia de segurança. Para obter informações adicionais, consulte o artigo [sincronização de palavra-passe](../active-directory-aadconnectsync-implement-password-synchronization.md).
Repetição de escrita de palavra-passe | Ao ativar repetição de escrita de palavra-passe, alterações de palavra-passe que têm no Azure AD escrito novamente para o seu diretório no local. Para mais informações, consulte o artigo [Introdução à gestão de palavra-passe](../active-directory-passwords-getting-started.md).
Grupo repetição de escrita | Se utilizar a funcionalidade de **Grupos do Office 365** , em seguida, pode ter estes grupos representados no Active Directory no local. Esta opção só está disponível se tiver o Exchange apresentar no Active Directory no local. Para mais informações, consulte o artigo [grupo escrita não consolidada](../active-directory-aadconnect-feature-preview.md#group-writeback).
Dispositivo repetição de escrita | Permite-lhe objetos de dispositivo de escrita não consolidada no Azure AD para o seu no local Active Directory para cenários de acesso condicional. Para mais informações, consulte o artigo [Ativando dispositivo a repetição de escrita na ligação do Azure AD](../active-directory-aadconnect-feature-device-writeback.md).
Da sincronização de diretórios extensão atributo | Ao ativar da sincronização de diretórios extensões atributo, atributos especificados são sincronizados para Azure AD. Para mais informações, consulte o artigo [extensões de diretório](../active-directory-aadconnectsync-feature-directory-extensions.md).

### <a name="azure-ad-app-and-attribute-filtering"></a>Aplicação do Azure AD e filtragem de atributo
Se pretende limitar os atributos que serão sincronizar para o Azure AD, em seguida, comece por selecionar quais os serviços que está a utilizar. Se efetuar alterações à configuração nesta página, um novo serviço tem de estar selecionada explicitamente executando novamente o Assistente de instalação.

![Funcionalidades opcionais aplicações](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

Com base nos serviços do selecionada no passo anterior, esta página mostra todos os atributos que são sincronizados. Esta lista é uma combinação de todos os tipos de objeto a ser sincronizada. Se existirem alguns atributos específicos que precisa para não sincronizar, pode desmarcar esses atributos.

![Funcionalidades opcionais atributos](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

>[AZURE.WARNING]
Remover atributos pode afetar a funcionalidade. Para melhores práticas e recomendações, consulte o artigo [atributos sincronizados](../active-directory-aadconnectsync-attributes-synchronized.md#attributes-to-synchronize).

### <a name="directory-extension-attribute-sync"></a>Da sincronização de diretórios extensão atributo
Pode expandir o esquema no Azure AD com os atributos personalizados adicionados pela sua organização ou outros atributos no Active Directory. Para utilizar esta funcionalidade, selecione **sincronizar de atributo da extensão do diretório** na página **Funcionalidades opcionais** . Pode selecionar mais atributos para sincronizar nesta página.

![Extensões de diretório](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Para mais informações, consulte o artigo [extensões de diretório](../active-directory-aadconnectsync-feature-directory-extensions.md).

## <a name="configuring-federation-with-ad-fs"></a>Configurar a Federação com o AD FS
Configurar o AD FS com a ligação do Azure AD é simple com apenas alguns cliques. É necessário o seguinte procedimento antes da configuração.

- Um servidor do Windows Server 2012 R2 para o servidor de federação com gestão remota activado
- Um servidor do Windows Server 2012 R2 para o servidor de Proxy de aplicação Web com a gestão remota activado
- Um certificado SSL para o nome do serviço de federação que pretende utilizar (por exemplo sts.contoso.com)

### <a name="ad-fs-configuration-pre-requisites"></a>Pré-requisitos de configuração do AD FS
Para configurar o seu farm do AD FS utilizando a ligação do Azure AD, certifique-se de que está ativado WinRM em servidores remotos. Além disso, percorra os requisitos de portas listados na [tabela 3 - ligação do Azure AD e os servidores de Federação/WAP](../active-directory-aadconnect-ports.md#table-3---azure-ad-connect-and-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Criar uma nova farm do AD FS ou utilizar um farm existente do AD FS
Pode utilizar um farm existente do AD FS ou pode optar por criar uma nova farm do AD FS. Se optar por criar um novo, são necessários para fornecer o certificado SSL. Se o certificado SSL estiver protegido por uma palavra-passe, lhe for pedido para a palavra-passe.

![AD FS Farm](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

Se optar por utilizar um farm existente do AD FS, são tomadas diretamente para a configurar a relação de confiança entre o AD FS e Azure AD ecrã.

### <a name="specify-the-ad-fs-servers"></a>Especificar os servidores de AD FS
Introduza os servidores que pretende instalar o AD FS no. Pode adicionar um ou mais servidores com base no seu necessidades em termos de planeamento de capacidade. Participar em todos os servidores ao Active Directory antes de efetuar esta configuração. A Microsoft recomenda a instalar um servidor do AD FS único para implementações teste e piloto. Em seguida, adicione e implementar os servidores de mais para corresponder às suas necessidades de dimensionamento ao executar a ligação do Azure AD novamente após a configuração inicial.

>[AZURE.NOTE]
Certifique-se de que todos os seus servidores são associados a um domínio de AD antes de efetuar esta configuração.

![Servidores do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Especificar os servidores Proxy de aplicação Web
Introduza os servidores que pretende como os servidores proxy de aplicação Web. O servidor de proxy de aplicação web está implementado no seu DMZ (extranet direcionado para o público) e suporta pedidos de autenticação a partir da extranet. Pode adicionar um ou mais servidores com base no seu necessidades em termos de planeamento de capacidade. A Microsoft recomenda instalar um servidor proxy de aplicação Web único para implementações teste e piloto. Em seguida, adicione e implementar os servidores de mais para corresponder às suas necessidades de dimensionamento ao executar a ligação do Azure AD novamente após a configuração inicial. Recomendamos que está a ter um número equivalente de servidores proxy para satisfazer autenticação a partir da intranet.

>[AZURE.NOTE]
<li> Se a conta que utiliza não for um administrador local nos servidores AD FS, em seguida, lhe for pedido para introduzir as credenciais de administrador.</li>
<li> Certifique-se de que existe HTTP/HTTPS conectividade entre o servidor de ligação do Azure AD e o servidor de Proxy de aplicação Web antes de executar este passo.</li>
<li> Certifique-se de que existe HTTP/HTTPS conectividade entre o servidor da aplicação Web e o servidor do AD FS para permitir os pedidos de autenticação para através de um fluxo de.</li>

![Aplicação Web](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

Lhe for pedido para introduzir as credenciais para que o servidor da aplicação web pode estabelecer uma ligação segura para o servidor do AD FS. Estas credenciais tem de ser um administrador local no servidor ADFS.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Especificar a conta de serviço para o serviço do AD FS
O serviço do AD FS requer uma conta de serviço de domínio para autenticar os utilizadores e informações de utilizador no Active Directory da pesquisa. Pode de suporte dois tipos de contas de serviço:

- **Grupo gerido conta de serviço** - introduzidos nos serviços de domínio do Active Directory com o Windows Server 2012. Este tipo de conta fornece serviços, como o AD FS, uma única conta sem ser necessário especificá-las atualizar a palavra-passe da conta regularmente. Utilize esta opção se já tiver o Windows Server 2012 controladores de domínio no domínio de que os servidores de AD FS pertencem.
- **Conta de utilizador domínio** - este tipo de conta requer que fornecem uma palavra-passe e regularmente atualizar a palavra-passe quando a palavra-passe for alterada ou expira. Utilize esta opção apenas quando não tiver controladores de domínio do Windows Server 2012 no domínio de que os servidores de AD FS pertencem.

Se tiver seleccionado o grupo de conta de serviço gerida e esta funcionalidade nunca tiver sido utilizada no Active Directory, lhe for pedido para introduzir as credenciais de administrador da empresa. Estas credenciais são utilizadas para iniciar o arquivo de chave e ativar a funcionalidade no Active Directory.

![Conta de serviço do AD FS](./media/active-directory-aadconnect-get-started-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Selecione o domínio do Azure AD que pretender federar
Esta configuração é utilizada para a relação de Federação entre AD FS e Azure AD do programa de configuração. Configura o problema tokens de segurança para Azure AD do AD FS e configura o Azure AD para confiar os tokens a partir desta instância específica do AD FS. Esta página apenas permite-lhe configurar um domínio único na instalação inicial. Pode configurar mais tarde mais domínios ao executar a ligação do Azure AD novamente.

![Domínio do Azure AD](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Verificar o domínio do Azure AD selecionado para a Federação
Quando seleciona o domínio para ser federada, ligação do Azure AD fornece-lhe as informações necessárias para verificar um domínio não verificado. Consulte o artigo [Adicionar e verificar o domínio](../active-directory-add-domain.md) para saber como utilizar estas informações.

![Domínio do Azure AD](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

>[AZURE.NOTE]
AD Connect tentará verificar o domínio durante a fase de configurar. Se continuar a configurar sem adicionar os registos DNS necessários, o assistente não é possível concluir a configuração.

## <a name="configure-and-verify-pages"></a>Configurar e verificar as páginas
A configuração acontece nesta página.

>[AZURE.NOTE]
Antes de continuar a instalação e se tiver configurado Federação, certifique-se de que configurou a [resolução de nomes para os servidores de Federação](../active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers).

![Pronto para configurar](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Modo de transição
É possível configurar um novo servidor de sincronização em paralelo com o modo de transição. Só é suportada para ter um servidor de sincronização a exportar para um diretório na nuvem. Mas se pretender mover a partir de outro servidor, por exemplo uma em execução DirSync, em seguida, pode ativar a ligação do Azure AD no modo de transição. Quando ativada, o motor de sincronização importar e sincronizar dados como normal, mas -exportar nada para Azure AD ou AD não. A funcionalidades palavra-passe sincronização e palavra-passe de escrita não consolidada são desativados enquanto estiver no modo de transição.

![Modo de transição](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

Enquanto estiver no modo de transição, é possível efetuar alterações necessárias para o motor de sincronização e rever o que está prestes a ser exportados. Quando a configuração agrado, execute o Assistente de instalação novamente e desativar o modo transição. Dados agora são exportados para Azure AD deste servidor. Certifique-se desativar o outro servidor ao mesmo tempo, por isso, apenas um servidor ativamente é exportar.

Para mais informações, consulte o [modo de teste](../active-directory-aadconnectsync-operations.md#staging-mode).

### <a name="verify-your-federation-configuration"></a>Verifique a configuração de Federação
Ligação do Azure AD verifica se as definições de DNS por si quando clica com o botão Verificar.

![Concluir](./media/active-directory-aadconnect-get-started-custom/completed.png)

![Verifique se](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

Além disso, execute os seguintes passos de verificação:

- Validar pode iniciar sessão a partir de um browser a partir de uma máquina de domínio associado na intranet da: ligar a https://myapps.microsoft.com e verifique se a iniciar sessão com a sua conta com sessão iniciada. A conta de administrador do AD DS incorporada não está sincronizada e não pode ser utilizada para verificação.
- Valide pode iniciar sessão a partir de um dispositivo da extranet. Numa máquina doméstica ou um dispositivo móvel, ligar a https://myapps.microsoft.com e forneça todas as suas credenciais.
- Valide cliente rich iniciar sessão. Ligar a https://testconnectivity.microsoft.com, selecione o separador do **Office 365** e escolha o **Office 365 único início de sessão teste**.

## <a name="next-steps"></a>Próximos passos
Após a instalação estiver concluída, terminar sessão e volte a iniciar sessão para o Windows antes de utilizar o Gestor de serviço de sincronização ou Editor de regras de sincronização.

Agora que tem ligação do Azure AD instalado pode [verificar a instalação e atribuir licenças](../active-directory-aadconnect-whats-next.md).

Saiba mais sobre estas funcionalidades que foram ativadas com a instalação: [impedir acidental eliminações](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) e [Azure AD ligar-se do Estado de funcionamento](../active-directory-aadconnect-health-sync.md).

Saiba mais sobre estes tópicos comuns: [scheduler e como acionar sincronização](../active-directory-aadconnectsync-feature-scheduler.md).

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Documentação relacionada

Tópico |  
--------- | ---------
Azure AD Connect descrição geral | [Integrar a suas identidades no local com o Azure Active Directory](../active-directory-aadconnect.md)
Instalar o utilizando as definições de Express | [Instalação rápida de ligação do Azure AD](active-directory-aadconnect-get-started-express.md)
Actualizar a partir do DirSync | [Atualizar a partir da ferramenta de sincronização do Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Contas utilizadas para a instalação | [Mais informações sobre permissões e contas de ligação do Azure AD](active-directory-aadconnect-accounts-permissions.md)
