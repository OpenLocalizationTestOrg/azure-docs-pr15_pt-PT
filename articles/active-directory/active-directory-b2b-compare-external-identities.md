<properties
   pageTitle="Comparar as funcionalidades de gestão de identidades externas utilizando o Azure Active Directory | Microsoft Azure"
   description="Se compara colaboração Azure Active Directory B2B, B2C e aplicação com várias inquilino para suporte de autenticação e autorização para identidades externas"
   services="active-directory"
   documentationCenter="" 
   authors="arvindsuthar"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/24/2016"
   ms.author="asuthar"/>

# <a name="comparing-capabilities-for-managing-external-identities-using-azure-active-directory"></a>Comparar as funcionalidades de gestão de identidades externas utilizando o Azure Active Directory

Para além de gerir o acesso de força de trabalho móvel às aplicações SaaS, Azure Active Directory (Azure AD) pode ajudar a sua organização partilhar recursos com parceiros de negócios e entregar aplicações para empresas e consumidores.

## <a name="developing-applications-for-businesses"></a>Desenvolver aplicações para empresas

Fornecem um serviço ou aplicação, tal como um serviço de folha de pagamentos, para empresas? Azure AD fornece a plataforma de identidade que lhe permite construir aplicações integram totalmente com milhões de organizações que já tenham sido configurada Azure AD como parte do implementar o Office 365 ou outros serviços do enterprise.

**Exemplo:** Um distribuidor farmacêutico fornece médicas e sistemas de informação da indústria cuidados de saúde. São necessitavam para uma aplicação de analytics para práticas médicas e clientes pretendidos para gerir os seus próprios identidades de campo. Esta empresa escolheu Azure AD como a plataforma de identidade da sua aplicação de gestão de exercícios práticos, fornecendo identidades do Azure AD aos seus clientes arroba para cima quando for necessário. Para obter mais informações, consulte o [Guia do programador do Azure Active Directory](active-directory-developers-guide.md).

## <a name="enabling-business-partner-access-to-your-corporate-resources"></a>Ativar o acesso de parceiro empresas para os recursos da empresa

Possui parceiros de negócios ou outras pessoas fora da sua empresa que precisam de aceder aos recursos da empresa, tal como um site do SharePoint ou o seu sistema ERP? Azure AD permite aos administradores para conceder aos utilizadores externos (que podem ou não podem existir em Azure AD) único início de sessão acesso às aplicações da empresa. Este procedimento melhora a segurança, tal como os utilizadores perdem o acesso ao deixarem a organização do parceiro, enquanto estiver a controlar as políticas de acesso da sua organização. Isto também simplifica a administração à medida que não precisa para gerir um parceiro externo directório ou por relações de Federação do parceiro.

**Exemplo:** Uma empresa Imagens fornece revendedores com fotografia imagens serviços e opera à rede de revenda maior de quiosques impressão. Se optar por Azure AD para ativar a milhares de utilizadores na suas parceiros de negócios de revenda utilizar das suas próprias credenciais para transferir os materiais de marketing de parceiro mais recentes e reordenar fotografia consumíveis de fornecedor a empresa extranet de processamento. Para mais informações, consulte o artigo [colaboração Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md).

## <a name="developing-applications-for-consumers"></a>Desenvolver aplicações para consumidores

Necessita de forma segura e forma rentável publicar aplicações onlinehttps, como uma frente de loja de retalho, milhões de consumidores? Azure AD fornece uma plataforma ativar social, bem como de início de sessão no nome de utilizador/palavra-passe, com marca corporativa personalizada inscrever-se e repor a palavra-passe personalizada para consumidores da sua aplicação. Isto aumenta conveniência para os seus consumidores reduzindo carga os programadores.

**Exemplo:** O \#franchise no mundo necessário diretamente participar com o seus ventoinhas 450 milhões de desporto 1. Para fazer isto, eles criadas numa aplicação móvel com o Azure AD para armazenamento de autenticação e de perfil de utilizador. Ventoinhas obter registo simplificado e iniciar sessão através da utilização de contas de rede sociais como o Facebook ou podem utilizar palavras-passe tradicional nome de utilizador para uma experiência totalmente integrada entre iOS, Android e Windows telefones. Construir a estabelecida no plataforma do Azure AD significativamente reduzido código personalizado, enquanto que lhe dá uma franchise personalizado imagem corporativa e atenuar preocupações sobre segurança, violações de dados e escalabilidade. Para mais informações, consulte o artigo [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).

## <a name="comparison-of-azure-ad-capabilities"></a>Comparação das funcionalidades do Azure AD

Cada um dos cenários já outros fabricantes referidos neste artigo é os destinatários pelas capacidades do Azure AD. Esta tabela deve ajudar a clarificar quais as capacidades de são mais relevantes para si:

| **Considere este produto...**       | [Aplicação de SaaS de inquilino com várias do Azure AD](active-directory-developers-guide.md)    | [Azure AD B2B colaboração](active-directory-b2b-what-is-azure-ad-b2b.md)        | [Azure AD B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)                |
|-----------------------|-------------------------|----------------------------|------------------------|
| **Se necessitar de fornecer...** | um serviço para empresas | acesso de parceiros para as minhas aplicações  | um serviço para os consumidores |
| **E estou semelhante a...**  | Distribuidor Pharma      | Imagem da empresa            | Franchise desportos       |
| **Implementar uma aplicação para o...**  | Gestão de exercícios práticos     | Fornecedor extranet          | Ventoinhas de futebol            |
| **Filtragem de...**        | Escritórios do médico        | Parceiros de negócios aprovado | Qualquer pessoa com o e-mail      |
| **Acessível quando...**      | Autorize de administração do cliente | Meu convites de administrador           | O consumidor se inscreve      |
