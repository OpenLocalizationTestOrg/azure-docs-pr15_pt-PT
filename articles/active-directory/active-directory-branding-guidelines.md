<properties
   pageTitle="Imagem corporativa diretrizes para aplicações | Microsoft Azure"
   description="Um guia abrangente para recursos para programadores orientados para o Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# <a name="branding-guidelines-for-applications"></a>Imagem corporativa diretrizes para aplicações


Este tópico aborda as diretrizes de imagem corporativa que deve utilizar quando desenvolver aplicações com o Azure Active Directory (Azure AD). Estas diretrizes irão ajudá-direcionar os seus clientes quando que pretendem utilizar a sua conta escolar ou profissional, gerida no Azure AD, ou os respetivos pessoal de conta para inscrição e iniciar sessão para a sua aplicação.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Para contas pessoais do vs. trabalho ou contas escolares da Microsoft

A Microsoft gere dois tipos de contas de utilizador:

- **Para contas pessoais** (anteriormente denominada Windows Live ID). Nestas contas representam a relação entre os utilizadores *individuais* e a Microsoft e são utilizadas para aceder a dispositivos do consumidor e serviços da Microsoft. Nestas contas destinam-se para uso pessoal.

- **Contas escolar ou profissional.** Nestas contas são geridas pelo Microsoft em nome de empresas que utilizam o Azure Active Directory. Nestas contas são utilizadas para iniciar sessão no Office 365 e outros serviços de empresas da Microsoft.

Microsoft contas escolar ou profissional normalmente por são atribuídas a utilizadores finais (empregados, os estudantes, empregados federais) as suas organizações (empresa, escola, agência governamental). Nestas contas são conhece diretamente na nuvem, da plataforma do Azure AD, ou sincronizadas com o Azure AD num diretório no local, como o Windows Server Active Directory. A Microsoft está a *entidade de custódia* das contas escolar ou profissional, mas as contas estão propriedade e controladas pela organização.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Que fazem referência a contas do Azure AD na sua aplicação

Microsoft não expõe os utilizadores finais para o Azure ou os nomes de marca corporativa do Active Directory e nenhuma deve.

- Assim que os utilizadores estão a sessão iniciados no, deve utilizar o nome e o logótipo quanto possível da organização. Esta é uma melhor utilização de termos genéricos como "a sua organização".

- Quando os utilizadores não tiver sessão iniciados no, deve referir-se para as respetivas contas como "trabalho ou escola contas" e utilize o logótipo da Microsoft para transmitir que nestas contas são geridas pelo Microsoft. Não utilize termos como "conta da empresa", "conta empresas" ou "uma conta empresarial" que criar confusões de utilizador.

## <a name="user-account-pictogram"></a>Pictogramas de conta de utilizador
Uma versão anterior destas diretrizes, é recomendável utilizando um pictograma "distintivo azul". Com base nos comentários de utilizador e programador, agora Recomendamos a utilização do logótipo da Microsoft em vez disso. Isto irá ajudar os utilizadores a compreender que eles podem reutilizar a conta que utilizam com o Office 365 ou a outro Microsoft os serviços de negócio para iniciar sessão na sua aplicação.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Inscrever-se e iniciar sessão com o Azure AD

A aplicação poderá apresentar caminhos separados para inscrição e iniciar sessão e as secções seguintes fornecem orientações visual para ambos os cenários.

**Se a sua aplicação suportar o início de sessão do utilizador final cima (por exemplo, gratuita ao modelo de versão de avaliação ou freemium)**: pode mostrar um botão de **início de sessão no** que permite aos utilizadores aceder à sua aplicação com a respetiva conta do trabalho ou a sua conta pessoal. Azure AD irá mostrar um pedido de consentimento a primeira vez que estas aceder à sua aplicação.

**Se a sua aplicação necessita de permissões que apenas os administradores podem consentimento, ou se a sua aplicação requer licenciamento organizacional**: deverá separar aquisição de administrador a partir do utilizador sessão. O **botão "obter esta aplicação"** irá redirecioná-administradores para iniciar sessão, em seguida, peça-lhes para conceder consentimento em nome dos utilizadores na sua organização. Isto tem a vantagem de suprimir utilizadores finais consentimento pedidos para a sua aplicação.

## <a name="visual-guidance-for-app-acquisition"></a>Orientações Visual para aquisição de aplicação

Ligação do seu "obter a aplicação" tem de redirecionar o utilizador conceder acesso a Azure AD (autorizar) página, para permitir que administrador uma organização autorizar a sua aplicação para ter acesso aos dados da respetiva organização que estão alojados pela Microsoft. Mais informações sobre como pedir acesso são abordadas no artigo [Aplicações de integração com o Azure Active Directory](active-directory-integrating-applications.md) .

Depois de administradores consentimento para a sua aplicação, pode optar por adicioná-la a aplicação iniciador experiência do Office 365 dos seus utilizadores (acessível a partir do ícone de waffle e a partir do [https://portal.office.com/myapps](https://portal.office.com/myapps)). Se pretender anunciar esta capacidade, pode utilizar termos, como "Adicionar esta aplicação para a sua organização" e mostrar um botão da seguinte forma:

![Tipos de aplicações e aos cenários](./media/active-directory-branding-guidelines/add-to-my-org.png)

No entanto, recomendamos que escrever o texto explicativo em vez de depender de botões. Por exemplo:
> *Se já utiliza o Office 365 ou outro serviço de empresas da Microsoft, pode simplesmente conceder < your_app_name > acesso aos dados da sua organização. Isto permite que os utilizadores aceder a < your_app_name > com as respetivas contas de trabalho existente.*


## <a name="visual-guidance-for-sign-in"></a>Orientações Visual para iniciar sessão
A aplicação deverá apresentar um sinal no botão que redireciona utilizadores para o início de sessão no ponto final que corresponde ao protocolo que utilizar para integrar com o Azure AD. A secção seguinte fornece detalhes sobre que botão devo aspeto.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictogramas e "Iniciar sessão com a Microsoft"
É a associação do logótipo da Microsoft e os termos de "Início de sessão em com Microsoft" representa exclusivamente Azure AD a outros fornecedores de identidade poderão suportar a sua aplicação. Se não tiver o espaço suficiente para "Início de sessão em com Microsoft", é ok para abreviá-lo para "Início de sessão".

![Tipos de aplicações e aos cenários](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Tipos de aplicações e aos cenários](./media/active-directory-branding-guidelines/sign-in-light.png)

Também pode utilizar um esquema de cores escuro para os botões.

![Tipos de aplicações e aos cenários](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Tipos de aplicações e aos cenários](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Imagem corporativa coisas a fazer e que não deve fazer

**EFETUE** utilize "conta escolar ou profissional" em conjunto com o botão "Início de sessão em com Microsoft" para fornecer explicação adicional para ajudar os utilizadores finais reconhecer se podem utilizá-lo. **Não** utilizar outros termos como "conta da empresa", "conta empresas" ou "conta empresarial."

**Não** utilize "ID do Office 365" ou "ID do Azure". Office 365 também é o nome de um consumidor perguntar se da Microsoft que não utiliza o Azure AD para autenticação.

**Não** altera o logótipo da Microsoft.

**Não** expõem os utilizadores finais para as marcas ou o Azure Active Directory. Ok no entanto, é para utilizar estes termos com os programadores, profissionais de TI e administradores.

## <a name="navigation-dos-and-donts"></a>Navegação coisas a fazer e que não deve fazer

**EFETUE** fornecem uma forma para os utilizadores terminar sessão e mudar para outra conta de utilizador. Enquanto a maioria das pessoas têm uma única conta pessoal da Microsoft/Facebook/Google/Twitter, as pessoas frequentemente estão associadas a mais do que uma organização. Suporte para múltiplos utilizadores tiver sessão iniciada no é brevemente.
