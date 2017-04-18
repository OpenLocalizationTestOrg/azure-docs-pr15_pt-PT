<properties
   pageTitle="Como as aplicações são adicionadas ao Azure Active Directory."
   description="Este artigo descreve como as aplicações são adicionadas a uma instância do Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Como e porquê aplicações são adicionadas à Azure AD

Uma das coisas inicialmente confusos ao visualizar uma lista de aplicações na sua instância do Azure Active Directory é Noções sobre onde as aplicações é proveniente e por que motivo estão lá.  Este artigo irá fornecer uma descrição geral de alto nível da forma como as aplicações são representadas no diretório e fornecerem-lhe contexto que irão ajudá-lo no Noções sobre como uma aplicação veio a ser no seu diretório.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Que serviços Azure AD fornecer às aplicações?

Aplicações são adicionadas à Azure AD para tirar partido de um ou mais dos serviços que fornece.  Desses serviços incluem:

* Autenticação da aplicação e autorização
* Autenticação de utilizador e autorização
* Serviço Single sign-on (SSO) utilizando a Federação ou palavra-passe
* & Sincronização de aprovisionamento do utilizador
* Controlo de acesso baseado em funções; Utilize o directório para definir as funções de aplicação para executar funções com base verificações de autorização numa aplicação.
* Serviços de autorização de oAuth (utilizados pelo Office 365 e outras aplicações do Microsoft para autorizar o acesso aos recursos/APIs).
* Publicação de aplicação e proxy; Publicar uma aplicação a partir de uma rede privada à internet

## <a name="how-are-applications-represented-in-the-directory"></a>Como são representadas aplicações no diretório?

As aplicações são representadas no Azure AD utilizar 2 objetos: um objeto de aplicação e um objecto principal de serviço.  Existe um objeto de aplicação, registado no "base" / "proprietário" ou "publicação" diretório e um ou mais objectos principais que representa a aplicação em cada directório na qual age de serviço.  

O objeto application descreve na aplicação para Azure AD (o serviço do inquilino com várias) e pode incluir qualquer um dos seguintes: (*Nota*: esta não é uma lista exaustiva.)

* Nome, logótipo e Publisher
* Segredos (simétricas e/ou assimétricos teclas utilizados para autenticar a aplicação)
* Dependências de API (oAuth)
* APIs/recursos/âmbitos publicados (oAuth)
* Funções de aplicação RBCA)
* SSO metadados e de configuração (SSO)
* Aprovisionamento de metadados e configuração do utilizador
* Metadados do proxy e a configuração

O capital de serviço é um registo da aplicação no cada diretório, onde a aplicação Age incluindo o seu diretório.  O capital de serviço:

* Refere-se novamente a um objeto de aplicação através da propriedade de id da aplicação
* Utilizador local registos e atribuições de funções de aplicação de grupo
* Registos concedidas permissões de utilizador e o administrador locais para a aplicação
    * Por exemplo: permissões para a aplicação aceder a um e-mail de utilizadores específicos
* Políticas locais registos incluindo a política de acesso condicional
* Registos alternativas locais definições local para uma aplicação
    * Regras de transformação de afirmações
    * Mapeamentos de atributos (aprovisionamento de utilizador)
    * Funções de aplicação específica de inquilinos (se a aplicação suporta funções personalizadas)
    * Nome/logótipo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Um diagrama de objectos da aplicação e principais de serviço nos diretórios

![Um diagrama que ilustra como a aplicação de objetos e principais existente com instâncias do Azure AD de serviço.][apps_service_principals_directory]

Como pode ver a partir do diagrama acima.  A Microsoft mantém dois directórios internamente (à esquerda) utiliza para publicar aplicações.

* Um para Microsoft Apps (directório de serviços da Microsoft)
* Um para aplicações de terceiros 3º previamente integradas (directório de Galeria de aplicações)

Aplicação fabricantes/os fornecedores integrar com o Azure AD são necessários para ter um diretório de publicação.  (Directório algumas SAAS).

As aplicações que pode adiciona-se incluem:

* Aplicações que tenha desenvolvido (integradas com o AAD)
* Aplicações ligado por única-sign-on
* Aplicações publicado utilizando o proxy de aplicação do Azure AD.

### <a name="a-couple-of-notes-and-exceptions"></a>Algumas notas e exceções

* Nem todas as principais de serviço apontem novamente para objectos de aplicação.  Huh? Quando o Azure AD foi criado originalmente os serviços fornecidos para aplicações foram muito mais limitados e o capital de serviço foi o suficiente para estabelecer uma identidade de aplicação.  O serviço original principal foi mais perto na forma a conta de serviço do Active Directory do Windows Server.  Por este motivo estiverem continua a ser possível criar principais de serviço, utilizando o PowerShell do Azure AD sem criar primeiro um objeto de aplicação.  Graph API requer um objeto de aplicação antes de criar um serviço principal.
* Não de todas as informações descritas acima atualmente exposta através de programação.  Seguem-se apenas disponível na interface de utilizador:
    * Regras de transformação de afirmações
    * Mapeamentos de atributos (aprovisionamento de utilizador)
* Para obter mais informações sobre o capital de serviço detalhadas e objectos de aplicação, consulte a documentação de referência do Azure AD Graph REST API.  *Sugestão*: documentação da API do Azure AD Graph destina-se a coisa mais próxima para uma referência do esquema do Azure AD neste momento não existe.  
    * [Aplicação](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Serviço de capital](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Como são adicionadas aplicações para o meu instância do Azure AD?
Existem várias formas que pode ser adicionada uma aplicação para Azure AD:

* Adicionar uma aplicação a partir da [Galeria de aplicação do Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Início de sessão para cima/para um 3 festa aplicação integrado com o Azure Active Directory (por exemplo: [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * Durante a inscrição para cima/em utilizadores-lhe pedido para dar permissão para a aplicação para aceder ao seu perfil e outras permissões.  A primeira pessoa dar o seu consentimento faz com que um principal de serviço que representa a aplicação seja adicionada ao diretório.
* Iniciar sessão para cima/para serviços online da Microsoft como o [Office 365](http://products.office.com/)
    * Quando subscrever ao Office 365 ou começar uma versão de avaliação principais de serviço de um ou mais são criados no diretório que representa os vários serviços que são utilizados para entregar todas as funcionalidades associada ao Office 365.
    * Alguns serviços do Office 365 como o SharePoint criar principais de serviço numa base no curso para permitir a comunicação segura entre os componentes incluindo fluxos de trabalho.
* Adicionar uma aplicação que está a desenvolver no veja Portal de gestão do Azure: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Adicione uma aplicação que está a desenvolver utilizando o Visual Studio consulte:
    * [Métodos de autenticação do ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Serviços ligados](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Adicionar uma aplicação para utilizar para utilizar o [Proxy de aplicação do Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Ligar uma aplicação para o single sign-on utilizando SAML ou SSO de palavra-passe
* Muitos outros incluindo vários programador experiências no Azure e/no Explorador de API experiências em vários centros de programador

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Quem tem permissão para adicionar aplicações para o meu instância do Azure AD?

Apenas os administradores globais podem:

* Adicionar aplicações a partir da Galeria de aplicação do Azure AD (aplicações integradas previamente de festa 3º)
* Publicar uma aplicação utilizando o Proxy de aplicação do Azure AD

Todos os utilizadores no seu diretório tem direitos para adicionar as aplicações que está a desenvolver e critério sobre as aplicações que são partilhar/dão acesso aos seus dados organizacionais.  *Não se esqueça de utilizador de início de sessão para cima/de uma aplicação e conceder permissões pode resultar num principal de serviço que está a ser criado.*

Este poderá ser inicialmente som que dizem respeito à, mas manter o seguinte em mente:

* Aplicações foram capaz de tirar partido do Active Directory do Windows Server para autenticação de utilizador para muitos anos sem necessidade da aplicação esteja registado/gravadas no diretório.  Agora a organização irá ter melhorado visibilidade exatamente quantas aplicações estiver a utilizar o directório e o que for.
* Não é necessário para a administração condicionada pelo processo de publicação/registo de aplicação.  Com os serviços de Federação do Active Directory tiver sido provável que um administrador tinha que adicionar uma aplicação como uma parte dependente em nome de programadores.  Agora programadores fazê-lo personalizada.
* Iniciar sessão numa/até aplicações utilizar as respetivas contas de organização para fins de negócio de utilizadores é uma boa ideia.  Se deixarem subsequentemente a organização que irão perder o acesso à sua conta na aplicação estivessem a utilizá.
* Ter um registo de dados que foram partilhados com o qual a aplicação é uma boa ideia.  Os dados são mais transportáveis que nunca e ter um registo limpar de quem partilhou quais os dados com as aplicações que é útil.
* As aplicações que utilizem o Azure AD para oAuth decidir exatamente que permissões que os utilizadores consigam conceder a aplicações e que necessitam de permissões de administrador para aceitar.  Devem ir a sem dizer que apenas os administradores podem consentimento para âmbitos maiores e mais significativas permissões.
* Utilizadores de adicionar e permitir que as aplicações aceder aos respetivos dados são auditados eventos para que possa ver os relatórios de auditoria no interior do portal do Azure cmdlet para determinar como uma aplicação foi adicionada ao diretório.

**Nota:** *Microsoft propriamente dito foi operativo com a configuração predefinida para muitos meses agora.*

Com todos os outros que diz-é possível impedir que os utilizadores no seu diretório adicionar aplicações e a partir do exercício critério sobre que informações que partilham com aplicações modificando configuração do directório no portal de gestão do Azure.  A seguinte configuração pode ser acedida no interior do portal de gestão do Azure no separador do seu diretório "Configurar".

![Uma captura de ecrã da IU para configurar as definições de aplicação integrada][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como adicionar aplicações para o Azure AD e como configurar os serviços de aplicações.

* Os programadores: [Saiba como integrar uma aplicação com AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Os programadores: [exemplos de código rever para aplicações integrado com o Azure Active Directory no Github](https://github.com/AzureADSamples)
* Os programadores e para profissionais de TI: [Rever a documentação REST API do Azure Active Directory Graph API](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* De TI: [Saiba como utilizar as aplicações integradas previamente do Azure Active Directory a partir da Galeria de aplicação](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Profissionais de TI: [encontrar tutoriais para configurar as aplicações de pré-integradas específicas](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* De TI: [Saiba como publicar uma aplicação a utilizar o Azure Active Directory aplicação Proxy](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Consulte também

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
