<properties
    pageTitle="Azure Active Directory serviços de domínio: Cenários de implementação | Microsoft Azure"
    description="Cenários de implementação para serviços de domínio do Azure AD"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>


# <a name="deployment-scenarios-and-use-cases"></a>Cenários de implementação e casos de utilização
Nesta secção, vamos observar alguns cenários de e casos de utilização que beneficiem das vantagens dos serviços de domínio do Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Seguro, administração fácil de máquinas virtuais Azure
Pode utilizar o Azure Active Directory Domain Services para gerir o seu máquinas virtuais Azure de forma sequencial. Azure máquinas virtuais pode ser aderidas ao domínio gerido, permitindo assim que a utilizar as suas credenciais AD da empresa para iniciar sessão. Esta abordagem ajuda a evitar transtornos de gestão de credenciais como manutenção de contas de administrador local em cada uma das suas máquinas virtuais Azure.

Também pode ser geridas e protegidas utilizando a política de grupo máquinas virtuais servidor que são associadas ao domínio gerido. Pode aplicar linhas de base de segurança necessária a sua máquinas virtuais Azure e bloqueando-as para baixo em conformidade com diretrizes de segurança da empresa. Por exemplo, pode utilizar funcionalidades de gestão de política de grupo para restringir os tipos de aplicações que podem ser iniciados nestes máquinas virtuais do.

![Administração simplificada do Azure máquinas virtuais](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Como servidores e outros infraestrutura atingir o fim de vida, Contoso está a movê muitas aplicações atualmente alojadas no local para a nuvem. Os respetivos padrão IT atual impõe que servidores que aloja aplicações empresariais têm de ser gerido utilizando a política de grupo e façam parte de um domínio. Contoso administrador de TI prefere máquinas virtuais associação de domínio implementadas no Azure, para facilitar a administração. Como resultado, administradores e utilizadores podem iniciar sessão com as respetivas credenciais da empresa. Ao mesmo tempo, máquinas podem ser configuradas para cumprir os planos base segurança necessária utilizando a política de grupo. Contoso se preferir não tem de implementar, monitorizar e gerir controladores de domínio no Azure para proteger máquinas virtuais Azure. Por conseguinte, serviços de domínio do Azure AD é uma ótima opção para este casos de utilização.

**Notas de implementação**

Tenha em atenção os seguintes pontos importantes para este cenário de implementação:

- Domínios geridos fornecidos pelos serviços de domínio do Azure AD fornecem uma estrutura de or (unidade organizacional) simples única por predefinição. Todos os computadores façam parte de um domínio residem numa única OU simples. No entanto poderá optar por criar ou personalizada.

- Serviços de domínio do Azure AD suporta simples política de grupo em forma de um incorporados GPO cada para os utilizadores e computadores contentores. Não é possível GP por or/departamento de destino, efetuar a filtragem de WMI ou criar GPO personalizado.

- Serviços de domínio do Azure AD suporta o esquema de objeto de computador AD base. Não é possível expandir esquema o objeto de computador.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Elevação-e-shift uma aplicação no local que utilize autenticação de vincular LDAP para serviços de infraestrutura do Azure

![Enlace LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso tem uma aplicação no local que foi comprada a partir de um ISV muitos anos atrás. A aplicação está atualmente no modo de manutenção pelo ISV e solicitar alterações à aplicação é proibitivamente para Contoso. Esta aplicação tem um frontend baseada na web que recolhe credenciais de utilizador utilizando um formulário web e, em seguida, autentica utilizadores através da execução de uma ligação LDAP no Active Directory da empresa. Contoso, optar por migrar esta aplicação para serviços de infraestrutura de Azure. É aconselhável que a aplicação funciona conforme for, sem necessidade de quaisquer alterações. Para além disso, os utilizadores devem ser possível autenticar utilizando as respetivas credenciais da empresa existentes e sem ter de reciclagem utilizadores fazer coisas de forma diferente. Por outras palavras, os utilizadores finais deve ser oblivious de onde está a ser executado a aplicação e a migração deve ser transparente às mesmas.

**Notas de implementação**

Tenha em atenção os seguintes pontos importantes para este cenário de implementação:

- Certifique-se de que a aplicação não é necessário modificar/escrita ao diretório. Acesso de escrita LDAP domínios geridos fornecida pelos serviços de domínio do Azure AD não é suportado.

- Não é possível alterar palavras-passe diretamente contra o domínio gerido. Os utilizadores finais pode alterar a palavra-passe quer utilizando mecanismo de alteração do Azure AD reposição personalizada de palavra-passe ou contra diretório no local. Estas alterações são automaticamente sincronizadas e estão disponíveis no domínio gerido.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Ler de uma aplicação no local que utiliza LDAP elevação e shift para aceder ao directório para serviços de infraestrutura do Azure
Contoso tem uma aplicação de linha de negócio (LOB) no local que foi desenvolvida quase uma década há. Esta aplicação for directório deverá ter em consideração e foi concebida para funcionar com o Windows Server AD. A aplicação utiliza LDAP (Lightweight Directory Access Protocol) para ler/atributos de informações sobre utilizadores do Active Directory. A aplicação não modificar os atributos ou caso contrário, escrita ao diretório. Contoso optar por migrar esta aplicação aos serviços de infraestrutura do Azure e retirar o hardware no local de envelhecimento atualmente alojamento esta aplicação. A aplicação não pode ser reprogramada para utilizar directório moderno APIs tal como a API do Azure AD Graph com base no resto. Por conseguinte, uma opção de elevação e shift é pretendida pelo qual a aplicação pode ser migrada para executar na nuvem, sem modificar código ou reescrever a aplicação.

**Notas de implementação**

Tenha em atenção os seguintes pontos importantes para este cenário de implementação:

- Certifique-se de que a aplicação não é necessário modificar/escrita ao diretório. Acesso de escrita LDAP domínios geridos fornecida pelos serviços de domínio do Azure AD não é suportado.

- Certifique-se de que a aplicação não necessita de um esquema do Active Directory personalizada/expandido. Extensões de esquema não são suportadas nos serviços de domínio do Azure AD.


## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrar uma aplicação de serviço ou daemon no local a serviços de infraestrutura do Azure
Algumas aplicações consistem várias camadas, onde uma das camadas tem de efetuar chamadas autenticadas para uma camada de back-end, tal como uma camada de base de dados. Contas de serviço do Active Directory são geralmente utilizadas para estes casos de utilização. Pode elevação e shift estas aplicações dos serviços de infraestrutura do Azure e a utilização de serviços de domínio do Azure AD para as necessidades de identidade destas aplicações. Pode optar por utilizar a mesma conta de serviço é sincronizada a partir do seu diretório no local para Azure AD. Em alternativa, pode criar primeiro um or personalizado e, em seguida, criar uma conta de serviço em separado nesse OU, para implementar estas aplicações.

![Conta de serviço utilizando WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso tem uma aplicação do cofre ao software que inclui uma front-end web, SQL server e um servidor de back-end FTP. Autenticação integrada do Windows de contas de serviço é utilizada para autenticar web front-end ao servidor de FTP. Front-end web está configurado para executar como uma conta de serviço. O servidor de back-end está configurado para autorizar o acesso a partir da conta de serviço para o front end da web. Contoso prefere não têm de implementar uma máquina de virtual do controlador de domínio na nuvem para mover esta aplicação para serviços de infraestrutura de Azure. Contoso administrador de TI pode implementar os servidores front-end web, SQL server e o servidor FTP ao Azure máquinas virtuais de alojamento. Estes máquinas, em seguida, são associadas a um domínio gerido de serviços de domínio do Azure AD. Em seguida, pode utilizam a mesma conta de serviço no seu diretório no local para fins de autenticação a aplicação. Esta conta de serviço está sincronizada com o domínio gerido de serviços de domínio do Azure AD e está disponível para utilização.

**Notas de implementação**

Tenha em atenção os seguintes pontos importantes para este cenário de implementação:

- Certifique-se de que a aplicação utiliza o nome de utilizador/palavra-passe para autenticação. Autenticação de certificado/smart card com base não é suportada pelos serviços de domínio do Azure AD.

- Não é possível alterar palavras-passe diretamente contra o domínio gerido. Os utilizadores finais pode alterar a palavra-passe quer utilizando mecanismo de alteração do Azure AD reposição personalizada de palavra-passe ou contra diretório no local. Estas alterações são automaticamente sincronizadas e estão disponíveis no domínio gerido.


## <a name="azure-remoteapp"></a>Azure RemoteApp
Azure RemoteApp permite administrador da Contoso criar uma colecção de domínio. Esta funcionalidade permite aplicações remotas fornecidas pelo RemoteApp do Azure executar em computadores façam parte de um domínio e para aceder a outros recursos utilizando a autenticação integrada do Windows. Contoso pode utilizar os serviços de domínio do Azure AD para fornecer um domínio gerido utilizado pelo RemoteApp do Azure façam parte de um domínio coleções de sites.

![Azure RemoteApp](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

Para mais informações sobre este cenário de implementação, consulte o artigo do blogue de serviços de ambiente de trabalho remoto intitulado [elevação e shift seu das cargas de trabalho com RemoteApp do Azure e dos serviços de domínio do Azure AD](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).
