As organizações estiver a utilizar mais aplicações de [Software, como um serviço (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) para produtividade porque tecnologia na nuvem e ferramentas são a tornar-se mais facilmente disponíveis. À medida que aumenta o número de SaaS aplicações, torna-se um desafio para os administradores gerir contas e direitos de acesso e para os utilizadores para que não se esqueça de palavra-passe diferente. Gerir estas aplicações individualmente cria trabalho extra e é menos seguro.


- Os funcionários que têm para controlar várias palavras-passe tendem para utilizar seguro menos métodos para se lembrá-las, quer escrever para baixo de palavras-passe ou utilizar as mesmas palavras-passe em várias contas.

- Quando chega um novo colaborador ou um deixa, todas as suas contas tem de ser individualmente aprovisionadas ou anular aprovisionadas.

- Para além disso, os empregados podem começar a utilizar o SaaS aplicações para o seu trabalho sem ter de aceder através de IT, o que significa que estão a criar os seus próprios contas nos sistemas de que os administradores de TI ainda não aprovada e não são monitorização.  

É uma solução para todos estes desafios sessão único (SSO). É a forma mais simples para gerir várias aplicações e fornecer aos utilizadores uma experiência de início de sessão consistente. Azure Active Directory (Azure AD) fornece uma solução SSO robusta e tem muitas aplicações integradas previamente disponíveis, com a tutoriais para administradores configurar uma nova aplicação e iniciar o aprovisionamento de utilizadores rapidamente.


## <a name="how-does-azure-active-directory-integrate-apps"></a>Como é que o Azure Active Directory integrar aplicações?  

Azure AD permite-lhe integrar a suas aplicações e aprovisionadas contas. Isto pode ser feito através de uma das duas abordagens.

- Se a aplicação for previamente integrada na Galeria de aplicação, pode aceder através do portal desse para configurar as aplicações e configurar as definições para permitir SSO. Para qualquer aplicação de galeria, pode começar por siga as simples passo a passo as instruções apresentadas na Galeria de aplicação e no portal do Azure para permitir o início de sessão único.

- Se a aplicação não estiver na galeria, ainda pode configurar a maioria das aplicações no Azure AD como uma aplicação personalizada. Isto requer um pouco mais técnico conhecimentos para configurar. Pode adicionar qualquer aplicação compatível com SAML 2.0 como uma aplicação federada ou qualquer aplicação que tenha uma HTML páginas baseada no início de sessão no como uma aplicação SSO de palavra-passe.

No caso onde os utilizadores tem criado os seus próprios contas para as aplicações de SaaS que não são geridas pelo IT, a ferramenta de [Deteção de aplicação de nuvem](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) fornece uma solução. Esta ferramenta monitoriza o tráfego da web para identificar que aplicações estão a ser utilizadas em toda a organização e o número de pessoas que utilizam cada um deles. IT pode utilizar estas informações para saber quais as aplicações os utilizadores preferem e decidir qual pretende integrar o Azure AD para SSO.  

Ao integrar uma aplicação do Azure AD, pode mapear identidades de aplicação estabelecida dos utilizadores para as respectivas identidades do Azure AD.  
