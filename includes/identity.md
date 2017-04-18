Gerir identidade é tão importante na nuvem pública tal como está no local. Para ajudar com o seguinte, o Azure suporta várias tecnologias de identidade da nuvem diferente. Incluem estes:

- Pode executar o Windows Server Active Directory (geralmente denominado apenas AD) na nuvem utilizando máquinas virtuais criadas com máquinas virtuais do Azure. Esta abordagem faz sentido quando estiver a utilizar o Azure para expandir o seu centro de dados no local para a nuvem.


- Pode utilizar o Azure Active Directory para dar ao seu utilizadores serviço single sign-on para as aplicações de [Software, como um serviço (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) . Office 365 do Microsoft utiliza esta tecnologia, por exemplo, e aplicações em execução no Azure ou noutras plataformas nuvem também podem utilizá-lo.


- Aplicações em execução na nuvem ou no local podem utilizar o controlo de acesso do Azure Active Directory para permitir que os utilizadores registo na utilização de identidades do Facebook, Google, Microsoft e outros fornecedores de identidade.


Este artigo descreve as três das seguintes opções.

## <a name="table-of-contents"></a>Índice

- [Executar o Active Directory do Windows Server em máquinas virtuais](#adinvm)

- [Utilizar o Azure Active Directory](#ad)

- [Utilizar o controlo de acesso do Azure Active Directory](#ac)


## <a name="adinvm"></a>Executar o Active Directory do Windows Server em máquinas virtuais

Executar o Windows Server AD em máquinas virtuais do Azure é muito semelhante a executá-lo no local. [Figura 1](#fig1) mostra um exemplo típico de este aspeto.

![Azure Active Directory na máquina Virtual](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Figura 1: Pode executar o Active Directory do Windows Server em máquinas virtuais do Azure ligadas ao centro de dados no local de uma organização utilizando rede Virtual Azure.

No exemplo mostrado aqui, Windows Server AD está em execução VMs criadas utilizando máquinas virtuais do Azure, tecnologia de IaaS a plataforma. Estes VMs e algumas outras pessoas estão agrupadas em rede virtual ligada a um centro de dados no local utilizando rede Virtual do Azure. A rede virtual carves saída de um grupo de máquinas virtuais na nuvem que interagem com a rede no local através de uma ligação de rede privada virtual (VPN). Efetuar permite-estes máquinas virtuais Azure aspeto apenas outra sub-rede aceda ao centro de dados no local. Como na figura mostra, dois esses VMs estão a executar controladores de domínio do Windows Server AD. As outras máquinas virtuais na rede virtual poderá estar em execução aplicações, como o SharePoint ou a ser utilizado no outro modo, tal como do desenvolvimento e teste. O Centro de dados no local também está em execução dois controladores de domínio do Windows Server AD.

Existem várias opções para ligar os controladores de domínio na nuvem com as em execução no local:

- Certifique todos-las para parte de um único domínio do Active Directory.

- Criar separado AD domínios no local e na nuvem que fazem parte da mesma floresta.

- Criar florestas AD separadas na nuvem e no local e, em seguida, ligar florestas utilizando floresta cruzada fidedignidades ou Windows Server ativo serviços de Federação Directory (AD FS), que podem também ser executados em máquinas virtuais em Azure.

Independentemente da sua escolha é tornada, um administrador deve certificar-se de que os pedidos de autenticação de utilizadores no local aceda a nuvem controladores de domínio apenas quando for necessário, uma vez que a ligação para a nuvem é provável que seja mais lento do que redes no local. Outro factor a ter em conta na nuvem conexão e controladores de domínio no local é o tráfego gerado pelo replicação. Controladores de domínio na nuvem são normalmente no seu próprio site de AD, que permite que um administrador agendar com que frequência replicação estiver concluída. Azure taxas para o tráfego enviado fora de um centro de dados do Azure, apesar de não bytes enviadas no, que pode afectar opções de replicação do administrador. Também é património destacam que enquanto Azure fornecer as suas próprias suporte do sistema de nomes de domínio (DNS), este serviço não é apresentado funcionalidades de uma ferramenta necessária ao Active Directory (como suporte para registos de DNS dinâmico e SRV). Por esta razão, com o Windows Server AD no Azure requer para configurar o seus próprio servidores DNS na nuvem.

Executar o Windows Server AD no Azure VMs pode fazer sentido em várias situações diferentes. Aqui estão alguns exemplos:

- Se estiver a utilizar máquinas virtuais do Azure como uma extensão do seu próprio Centro de dados, poderá executar aplicações na nuvem que necessitam de controladores de domínio local para processar coisas como pedidos de autenticação integrada do Windows ou consultas LDAP. SharePoint, por exemplo, o interage frequentemente com o Active Directory e, por isso, enquanto que é possível executar um farm do SharePoint no Azure utilizando num diretório no local, a configurar controladores de domínio na nuvem vai significativamente melhorar o desempenho. (É importante para aperceba que esta não é necessariamente necessária, no entanto; bastante aplicações pode executar com êxito na nuvem utilizando apenas os controladores de domínio no local.)

- Suponha que uma filial distante não possui os recursos para executar a suas própria controladores de domínio. Hoje em dia, os utilizadores devem autenticar-se para controladores de domínio no outro lado do mundo - inícios de sessão são lentas. Executar o Active Directory no Azure no Centro de dados mais perto do Microsoft pode acelerar o processo sem necessidade de servidores mais no office ramo.

- Uma organização que utilize Azure para recuperação de falhas poderá manter em conjuntos pequenos de VMs ativas na nuvem, incluindo um controlador de domínio. Em seguida, pode ser preparada para expandir este site, conforme necessário para tomar para falhas noutro local.

Também existem outras possibilidades. Por exemplo, não está necessários para ligar o Windows Server AD na nuvem para um centro de dados no local. Se quisesse executar um farm do SharePoint que servida um determinado conjunto de utilizadores, por exemplo, todas quem quiser iniciar sessão no exclusivamente com identidades baseado na nuvem, poderá criar uma floresta autónomo no Azure. Como utilizar esta tecnologia depende quais são os seus objetivos. (Para uma orientação mais detalhada sobre como utilizar o Windows Server AD com Azure, [consulte o artigo aqui](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).)

## <a name="ad"></a>Utilizar o Azure Active Directory

À medida que aplicações SaaS ficam mais comuns, eles elevar uma questão óbvia: que tipo de serviço de diretório devem utilizar estas aplicações baseadas na nuvem? Resposta da Microsoft a essa pergunta é Azure Active Directory.

Existem duas opções principais para utilizar este serviço de diretório na nuvem:

- Indivíduos e organizações que utilizem apenas as aplicações SaaS podem depender do Azure Active Directory como o seu serviço de diretório exclusiva.

- As organizações que executam o Windows Server Active Directory podem ligar o seu diretório no local para o Azure Active Directory, em seguida, utilizá-la para dar os respetivos utilizadores serviço single sign-on a aplicações de SaaS.


[Figura 2](#fig2) ilustra a primeira destas duas opções, onde o Azure Active Directory é tudo o que necessário.

![Azure Active Directory na máquina Virtual](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Figura 2: Azure Active Directory dá-utilizadores serviço single sign-on uma organização a aplicações SaaS, incluindo o Office 365.

Como na figura mostra, Azure AD é um serviço de inquilino com várias. Isto significa que pode suportar em simultâneo muitas organizações diferentes, armazenar diretório obter informações sobre os utilizadores em cada um deles. Neste exemplo, um utilizador na organização A está a tentar aceder a uma aplicação de SaaS. Esta aplicação poderá fazer parte do Office 365, como o SharePoint Online, ou poderá ser outra coisa - aplicações que não sejam da Microsoft, também podem utilizar esta tecnologia. Visto que Azure AD suporta o protocolo SAML 2.0, tudo necessário a partir de uma aplicação é a capacidade para interagir com esta norma da indústria. (Na verdade, podem executar as aplicações que utilizam o Azure AD em qualquer centro de dados, não apenas um centro de dados Azure.)

O processo começa quando o utilizador acede uma aplicação de SaaS (passo 1). Para utilizar esta aplicação, o utilizador tem de apresentar um token emitido por Azure AD.

Este token contém informações que identifica o utilizador e está assinado digitalmente por Azure AD. Para obter o token, o utilizador autentica próprio para Azure AD ao fornecer um nome de utilizador e palavra-passe (passo 2). Azure AD, em seguida, devolve o token ele precisa (passo 3).

Este token, em seguida, é enviado para a aplicação de SaaS (passo 4), que valida assinatura do token e utiliza os seus conteúdos (passo 5). Normalmente, a aplicação irá utilizar as informações de identidade que o token contém decida que o utilizador tem permissão para o access e talvez de outras formas de informações.

Se a aplicação necessitar de mais informações sobre o utilizador que o que estão contido num token, pode pedir-isto diretamente a partir do Azure AD utilizar a API do Azure AD Graph (passo 6). Na versão inicial do Azure AD, o esquema do directory é bastante simple: contém apenas utilizadores e grupos e relações entre elas. Aplicações podem utilizar estas informações para saber mais sobre as ligações entre os utilizadores. Por exemplo, suponha que precisa saber quem é o gestor deste utilizador decidir se ele tem permissão de acesso para alguns segmento de dados de uma aplicação. Pode saber isto consultando Azure AD através da API do gráfico.

Graph API utiliza um protocolo RESTful normal, facilitando simples utilizar a maioria dos clientes, incluindo dispositivos móveis. API também suporta as extensões definidas pelo OData, adicionar coisas como um idioma de consulta para permitir que os dados do access clientes das formas mais úteis. (Para saber mais sobre OData, consulte [Introdução ao OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf)). Uma vez que a API do gráfico pode ser utilizada para saber mais sobre relações entre os utilizadores, permite-aplicações compreender o gráfico de rede social que esteja incorporado no esquema do Azure AD para uma determinada organização (que é o motivo pelo qual chama-se a API Graph). E para se autenticar para Azure AD para pedidos de API do gráfico, uma aplicação utiliza OAuth 2.0.

Se uma organização não utiliza o Windows Server Active Directory - -não possui servidores no local ou domínios - e baseia-se apenas em aplicações de nuvem que utilizam o Azure AD, utilizar apenas este diretório na nuvem seria dar utilizadores serviço single sign-on a empresa às mesmas. Ainda enquanto este cenário obtém mais comuns diariamente, a maioria das organizações utilização de ainda no local domínios criados com o Windows Server Active Directory. Azure AD tem uma função útil para reproduzir aqui também, tal como ilustrado [figura 3](#fig3) .

![Azure Active Directory na máquina Virtual](./media/identity/identity_03_AD.png)
<a id="fig3"></a>figura 3: uma organização pode federar Active Directory do Windows Server com o Azure Active Directory dar às sua utilizadores serviço single sign-on a SaaS aplicações.

Neste cenário, um utilizador na organização B deseje aceder a uma aplicação de SaaS. Antes de ela faz isto, os administradores de diretório da empresa tem de estabelecer uma relação de federação com Azure AD utilizar os AD FS, conforme mostrado figura. Os administradores também tem de configurar a sincronização de dados no local Windows Server a organização AD e Azure AD. Isto copia automaticamente informações do grupo de utilizador e do diretório no local para Azure AD. Repare que esta opção permite-: na verdade, a organização é expandindo o seu diretório no local para a nuvem. Combinar Windows Server AD e Azure AD desta forma dá a organização num serviço de diretório que pode ser gerido como uma única entidade, enquanto ainda está a ter um ambiental ambas no local e na nuvem.

Para utilizar Azure AD, o utilizador inicia pela primeira vez seu domínio do Active Directory no local como habitualmente (passo 1). Quando tenta ela aceder à aplicação SaaS (passo 2), o processo de Federação resulta no Azure AD emissão-lhe um token para esta aplicação (passo 3). (Para saber mais sobre como funciona a Federação, consulte o artigo [baseada em afirmações identidade para o Windows: tecnologias e cenários](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx).) À medida antes, este token contém informações que identifica o utilizador e está assinado digitalmente por Azure AD. Este token, em seguida, é enviado para a aplicação de SaaS (passo 4), que valida assinatura do token e utiliza os seus conteúdos (passo 5). E é o cenário anterior, SaaS aplicação pode utilizar a API do gráfico para saber mais sobre este utilizador se necessário (passo 6).

Hoje em dia, Azure AD não concluída para o local Windows Server AD. Tal como já mencionado diretório nuvem tem um esquema muito mais simples e também falta coisas como política de grupo, a capacidade para armazenar informações sobre máquinas e suporte para LDAP. (Na verdade, uma máquina de Windows não pode ser configurada para permitir que os utilizadores, inicie sessão na mesma com nada mas Azure AD - esta não é um cenário suportado.) Em vez disso, os objetivos iniciais do Azure AD incluem permitindo que aplicações de acesso de utilizadores empresariais na nuvem, sem manter um início de sessão em separado e eliminar documentos numa biblioteca no local Administradores do directório de sincronizar manualmente o seu diretório no local com todas as aplicações de SaaS a que sua organização utiliza. Ao longo do tempo, no entanto, achar que este serviço de diretório na nuvem para endereçar uma vasta gama de cenários.

## <a name="ac"></a>Utilizar o controlo de acesso do Azure Active Directory

Tecnologias de identidade baseado na nuvem podem ser utilizadas para resolver uma variedade de problemas. Azure Active Directory conceder utilizadores serviço single sign-on de uma organização às várias aplicações SaaS, por exemplo. Mas tecnologias de identidade na nuvem podem também ser utilizadas de outras formas.

Por exemplo, imaginemos que pretenda permitir que os seus utilizadores uma aplicação inicie sessão com tokens emitidos por vários *fornecedores de identidade (IdPs)*. Muitos dos fornecedores de identidade diferente de existirem hoje em dia, incluindo o Facebook, Google, Microsoft e outras pessoas, e aplicações frequentemente permitir que os utilizadores, inicie sessão com uma das seguintes identidades. Por que motivo devo uma aplicação de se preocupar em para manter a sua própria lista de utilizadores e palavras-passe quando em vez disso, pode depender de identidades que já existam? Aceitar identidades existentes facilita a vida mais simples ambos para os utilizadores que têm um menos o nome de utilizador e palavra-passe para não se esqueça de e para as pessoas que criam a aplicação, que já não precisam de manter os seus próprios listas de nomes de utilizador e palavras-passe.

Mas enquanto cada fornecedor de identidades problemas algum tipo do token, essas tokens não estão padrão - cada IdP contém a sua própria formatação. Além disso, as informações nesses tokens também não estão padrão. Uma aplicação que pretenda aceitar tokens emitidos por, diga, Facebook, Google e Microsoft é é confrontada com o desafio da gravação de código exclusivo para processar cada um dos seguintes formatos diferentes.

Mas por que motivo fazer isto? Por que motivo não em vez disso, criar um intermédio que pode gerar um único formato token com uma representação comuns de informações de identidade? Esta abordagem tornaria vida mais simples para os programadores de criar aplicações, uma vez que precisam agora processar apenas um tipo de token. Azure Active controlo de acesso de diretório faz exatamente isto, fornecendo um intermédio na nuvem para trabalhar com tokens diversificados. [Figura 4](#fig4) mostra como funciona

![Azure Active Directory na máquina Virtual](./media/identity/identity_04_IdentityProviders.png)
<a id="fig4"></a>figura 4: controlo de acesso do Azure Active Directory, é mais fácil para as aplicações aceitar tokens de identidade emitidos por fornecedores de identidade diferente.

O processo começa quando um utilizador tenta aceder à aplicação a partir de um browser. A aplicação redireciona lhe um IdP da sua escolha (e que a aplicação também confia). Posteriormente, autentica si para este IdP, tal como ao introduzir um nome de utilizador e palavra-passe (passo 1), e o IdP devolve um token que contém informações sobre o (passo 2).

Como na figura mostra, o controlo de acesso suporta um intervalo de diferentes IdPs baseado na nuvem, incluindo as contas criadas pelo Google, Yahoo, Facebook, Microsoft (anteriormente denominada Windows Live ID) e qualquer fornecedor de OpenID. Também suporta identidades criadas utilizando o Azure Active Directory e, através de federação com o Windows Server Active Directory do AD FS. O objetivo é cobrir as identidades utilizadas com mais frequência hoje em dia, se de que está a emitido por IdPs na nuvem ou no local.

Assim que o browser do utilizador tem um token de IdP a partir do seu escolhido IdP, envia isto token para controlo de acesso (passo 3). Controlo de acesso valida o token, certificando-se de que-realmente foi emitido por este IdP, em seguida, cria um novo token de acordo com as regras que foram definidas para esta aplicação. Como Azure Active Directory, controlo de acesso é um serviço de inquilino com várias, mas os inquilinos são aplicações em vez de organizações de cliente. Cada aplicação pode obter a sua própria espaço de nomes, conforme mostrado figura e pode definir várias regras sobre autorização e muito mais.

Estas regras permitir que o administrador de cada aplicação definir como tokens a partir de vários IdPs devem ser transformados num token de controlo de acesso. Por exemplo, se IdPs diferentes utilizam diferentes tipos para representar liste, regras de controlo de acesso podem transformar-todas estas para um tipo de nome de utilizador comum. Controlo de acesso, em seguida, envia este novo token de volta para o browser (passo 4), que submete-a para a aplicação (passo 5). Assim que tiver o token de controlo de acesso, verifica se a aplicação que este token realmente foi emitido por controlo de acesso, em seguida, utiliza as informações contidas (passo 6).

Enquanto este processo pode parecer complicado um pouco, na realidade torna a vida significativamente mais simples para o criador de blocos da aplicação. Em vez de processar diversificados tokens que contém informações diferentes, a aplicação pode aceitar identidades emitidas por vários fornecedores de identidade enquanto apenas um único token com informações familiares continuar a receber. Além disso, em vez de introduzir cada aplicação para ser configurado para confiar IdPs vários, estas relações de fidedignidade em vez disso, são mantidas ao controlo de acesso - uma aplicação precisa apenas confiar.

Vale destacam que nada sobre o controlo de acesso está associado ao Windows - apenas também podem ser utilizada por uma aplicação de Linux aceites apenas identidades do Google e o Facebook. E, apesar de controlo de acesso faz parte da família do Azure Active Directory, poderá pensá-lo como um serviço completamente distinto daquilo que foi descrito na secção anterior. Enquanto ambas as tecnologias a trabalhar com a identidade, resolveram os problemas bastante diferentes (embora Microsoft tem disse que espera-se que integrar as duas em algumas ponto).

Trabalhar com identidade é importante para quase todas as aplicações. É o objetivo de controlo de acesso para que seja mais fácil para os programadores criar aplicações que aceitar identidades dos fornecedores de identidade diversificados. Ao colocar este serviço na nuvem, Microsoft tem à disposição qualquer aplicação em execução no qualquer plataforma.

##<a name="about-the-author"></a>Acerca da autora

David Chappell é Chappell de Principal e parceiros [www.davidchappell.com](http://www.davidchappell.com) em Castelo branco, Califórnia.
