<properties
   pageTitle="Como integrar com o Azure Active Directory | Microsoft Azure"
   description="Um guia para benefícios da e recursos para integração com o Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="integrating-with-azure-active-directory"></a>Integração com o Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory fornece as organizações com gestão de identidades empresarial para aplicações na nuvem.  Integração do Azure AD dá os seus utilizadores uma experiência de início de sessão simplificada e ajuda-o a sua aplicação está em conformidade com a política de IT.

## <a name="how-to-integrate"></a>Como integrar o

Existem várias formas para a sua aplicação integrar com o Azure AD.  Tire partido das como muitos ou como poucas estes cenários conforme adequado para a sua aplicação.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Azure AD como uma forma de início de sessão para a sua aplicação de suporte

**Reduzir o início de sessão fricção e reduzir os custos de suporte.** Ao utilizar o Azure AD para iniciar sessão na sua aplicação, os utilizadores não tem um nome mais e palavra-passe que se deve lembrar.  Como um programador, terá uma menos palavra-passe para armazenar e proteger.  Não ter de alça de palavra-passe esquecida poderá ser uma poupanças significativas sozinho.  Azure AD oferece início de sessão para alguns dos mais populares nuvem aplicações parte do mundo, incluindo o Office 365 e Microsoft Azure.  Com centenas de milhões utilizadores a partir do milhões de organizações, provavelmente o utilizador já tiver sessão iniciada Azure AD.  Saiba mais sobre [Adicionar suporte para Azure AD início de sessão](../active-directory-authentication-scenarios.md).

**Simplificar o início de sessão para cima para a sua aplicação.**  Durante a inscrever-se para a sua aplicação, Azure AD pode enviar informações essenciais sobre um utilizador para que possa previamente preencher a formulário de inscrição ou eliminá-lo completamente.  Os utilizadores podem inscrever-se para a sua aplicação utilizando a respetiva conta Azure AD através de uma experiência familiar consentimento semelhante aos encontrados nas redes sociais e aplicações móveis.  Qualquer utilizador pode inscrever-se e inicie sessão para uma aplicação que está integrada com o Azure AD sem necessidade de participação IT.  Saiba mais sobre como [Iniciar sessão de segurança a sua aplicação de início de sessão do Azure AD conta](../../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Navegue para os utilizadores, gerir aprovisionamento de utilizador e controlar o acesso à aplicação

**Navegue para utilizadores no diretório.**  Utilizar a API do gráfico para ajudar os utilizadores a procurar e procure outras pessoas na sua organização ao convidar outras pessoas ou endereços de conceder acesso, em vez de exigi-los para escrever e-mail.  Os utilizadores podem procurar utilizando uma interface familiar endereço livro estilo, incluindo vejam os detalhes da hierarquia organizacional.  Saiba mais sobre a [API do gráfico](../active-directory-graph-api.md).

**Reutilização grupos do Active Directory e gere o seu cliente de listas de distribuição.**  Azure AD contém os grupos que o cliente é já utiliza para distribuição de correio eletrónico e gerir o acesso.  Voltar a utilizar a API do gráfico, utilize estes grupos em vez de que exige o seu cliente criar e gerir um conjunto de grupos na sua aplicação separado.  Informações de grupo também podem ser enviadas para a aplicação na tokens de início de sessão.  Saiba mais sobre a [API do gráfico](../active-directory-graph-api.md).

**Utilize o Azure AD para controlar quem tem acesso à sua aplicação.**  Os administradores e proprietários de aplicação no Azure AD podem atribuir acesso a aplicações para utilizadores e grupos específicos.  Utilizar a API do gráfico, pode ler esta lista e utilizá-lo para controlar aprovisionamento e anular o aprovisionamento de recursos e aceder à sua aplicação.

**Controlo de acesso baseado em utilização Azure AD para funções de.**  Os administradores e proprietários de aplicação podem atribuir utilizadores e grupos para funções que definem quando registar a aplicação no Azure AD.  Função informações são enviadas para a sua aplicação de tokens de início de sessão e também podem ser lidos com a API do gráfico.  Saiba mais sobre como [utilizar o Azure AD para autorização](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Obter acesso a perfil de utilizador, calendário, correio eletrónico, contactos, ficheiros e muito mais

**Azure AD é o servidor de autorização para Office 365 e outros serviços de empresas da Microsoft.**  Se suportar Azure AD para iniciar sessão na sua aplicação ou suporte ligar as suas contas de utilizador actual às contas de utilizador do Azure AD utilizando OAuth 2.0, pode pedir acesso de escrita para um perfil de utilizador, calendário, correio eletrónico, contactos, ficheiros e outras informações de leitura e.  Forma totalmente integrada pode escrever eventos ao calendário do utilizador e leia ou escrever ficheiros para o seu OneDrive.  Saiba mais sobre como [aceder às APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promover a sua aplicação na Azure e mercados do Office 365

**Promova a sua aplicação para os milhões de organizações que já estejam a utilizar o Azure AD.**  Os utilizadores que procurar e procure estes mercados já estiver a utilizar um ou mais serviços em nuvem, tornando-as qualificado clientes do serviço de nuvem.  Saiba mais sobre promover a sua aplicação no [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Quando os utilizadores se inscrevem para a sua aplicação, esta será apresentada no seu painel de acesso do Azure AD e o iniciador de aplicações do Office 365.**  Os utilizadores poderão para forma rápida e fácil regressar à aplicação mais tarde, melhorar Cativação de utilizador.  Saiba mais sobre o [Azure AD aceder ao painel](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Comunicação segura do serviço de dispositivos e serviço de serviço

**Utilizar o Azure AD para gestão de identidades dos serviços e dispositivos reduz o código que terá de escrever e permite IT para gerir o acesso.**  Serviços e dispositivos, podem obter tokens a partir do Azure AD utilizando OAuth e utilizar esses tokens para aceder ao web APIs.  Utilizar o Azure AD pode evitar escrever o código de autenticação complexas.  Uma vez que as identidades dos serviços e dispositivos são armazenadas no Azure AD, IT podem gerir teclas e revogação num único local em vez de o fazer separadamente na sua aplicação.

## <a name="benefits-of-integration"></a>Vantagens de integração

Integração com o Azure AD vem com benefícios que não exigem a escrever código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integração com gestão de identidade da empresa

**Ajuda a sua aplicação está em conformidade com políticas de TI.**  As organizações integram os respetivos sistemas de gestão de identidades do enterprise do Azure AD, para quando uma pessoa deixa de uma organização, estes serão automaticamente perder o acesso à aplicação sem IT necessário especificá-las efetuar passos adicionais.  IT pode gerir quem pode aceder a sua aplicação e determinar quais as políticas de acesso são necessárias - para autenticação multifator de exemplo - reduzir a necessidade de escrever código para cumprir as políticas da empresa complexas.  Azure AD fornece aos administradores com um registo de auditoria detalhadas dos quem sessão iniciada à aplicação por isso, IT pode controlar a utilização.

**Azure AD expande do Active Directory na nuvem para que a sua aplicação pode integrar com o AD.**  Muitas organizações em todo o mundo utilizam do Active Directory como as respetivas principal iniciar sessão e o sistema de gestão de identidade e exigem que os seus pedidos para funcionar com o AD.  Integrar com o Azure AD integra-se a aplicação com o Active Directory.

### <a name="advanced-security-features"></a>Funcionalidades de segurança avançadas

**Autenticação multifator.**  Azure AD fornece autenticação multifator nativa.  Os administradores de TI podem exigir autenticação multifator para aceder à sua aplicação, para que não tenha codificar este suporte.  Saiba mais sobre a [Autenticação Multifator](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Discordantes início de sessão deteção.**  Azure AD processa suplementos mais do que um mil milhões de início de sessão por dia, enquanto utiliza algoritmos de aprendizagem automática para detectar actividade suspeita e notificar os administradores de TI de possíveis problemas.  Ao suportar o início de sessão no Azure AD, a aplicação obtém o benefício este proteção. Saiba mais sobre a [visualização Azure Active Directory aceder ao relatório](../active-directory-view-access-usage-reports.md).

**Acesso condicional.**  Para além de autenticação multifator, os administradores podem exigir condições específicas ser cumpridos antes dos utilizadores possam iniciar sessão para a sua aplicação.  Condições que podem ser definidas incluir o intervalo de endereços IP de dispositivos cliente, associação a grupos especificados e o estado do dispositivo que está a ser utilizado para o access.  Saiba mais sobre [acesso condicional do Azure Active Directory](../active-directory-conditional-access.md).

### <a name="easy-development"></a>Desenvolvimento fácil

**Protocolos padrão da indústria.**  A Microsoft está empenhada em normas da indústria de suporte.  Azure AD suporta os protocolos de autenticação SAML 2.0, OpenID ligar 1.0, OAuth 2.0 e Federação WS 1.2.  A API do gráfico é OData 4.0 em conformidade.  Se a sua aplicação já suportar os protocolos SAML 2.0 ou OpenID ligar 1.0 para o início de sessão federados, pode ser muito simples adicionar suporte para Azure AD.  Saiba mais sobre [protocolos de autenticação do Azure AD suportado](../active-directory-authentication-protocols.md).

**Abra a bibliotecas de origem.**  A Microsoft fornece bibliotecas totalmente suportada abrir origem para idiomas populares e plataformas de desenvolvimento de velocidade.  O código de origem está licenciado sob Apache 2.0, e consoante esteja gratuito bifurcam e contribuir novamente para os projectos.  Saiba mais informações sobre [bibliotecas de autenticação do Azure AD](../active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Em todo o mundo presença e elevada disponibilidade

**Azure AD está implementada no centros de dados em todo o mundo e é gerido e monitorizada cerca a hora do dia.**  Azure AD é o sistema de gestão de identidades do Microsoft Azure e no Office 365 e está implementada no 28 centros de dados em todo o mundo.  Dados de directório são garante ser replicadas para pelo menos três centros de dados.  Balanceadores de carga global Certifique-se aos utilizadores acesso a cópia mais próxima do Azure AD que contém os respetivos dados e voltar direcionar automaticamente pedidos para outros centros de dados se é detetado um problema.

## <a name="next-steps"></a>Próximos passos

[Começar a escrever o código](../active-directory-developers-guide.md#getting-started).

[Utilizadores de iniciar sessão com o Azure AD](../active-directory-authentication-scenarios.md)
