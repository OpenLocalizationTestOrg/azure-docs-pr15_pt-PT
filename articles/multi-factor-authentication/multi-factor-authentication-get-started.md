<properties
    pageTitle="Servidor de vs de nuvem do Azure MFA | Microsoft Azure"
    description="Selecione a autenticação multifator secutiry solução para a direita por si pela perguntar que am i tentar seguro e onde estão os meus utilizadores localizados.  Em seguida, selecione nuvem, MFA servidor ou o AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>

#<a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Selecione a solução de autenticação Multifator de Azure por si

Uma vez que existem vários existem tipos de autenticação do Azure Multifator (MFA), podemos tem de responder a algumas perguntas descobrir que versão é o inicial maiúscula que pretende utilizar.  Estas perguntas são:

-   [O que estou a tentar seguro](#what-am-i-trying-to-secure)
-   [Onde estão localizados os utilizadores](#where-are-the-users-located)
- [Que funcionalidades de preciso?](#what-featured-do-i-need)

As secções seguintes fornecem orientações sobre como determinar cada uma das seguintes respostas.

## <a name="what-am-i-trying-to-secure"></a>O que estou a tentar seguro?

Para determinar a solução de verificação de dois passos correto, pela primeira vez podemos tem responder à pergunta que estão está a tentar seguro com um segundo método de autenticação.  É uma aplicação que esteja no Azure?  Ou um sistema de acesso remoto?  Por para determinar o que recomendamos está a tentar seguro, podemos pode responder à pergunta de onde a autenticação Multifator tem de estar ativada.  


O que está a tentar seguro| Autenticação Multifator na nuvem|Servidor de autenticação Multifator
------------- | :-------------: | :-------------: |
Aplicações do Microsoft originais|● |● |
Aplicações de SaaS na Galeria de aplicação|● |● |
Aplicações do IIS publicadas através de Proxy de aplicação do Azure AD|● |● |
Aplicações do IIS não publicadas através de Proxy de aplicação do Azure AD | |● |
Acesso remoto como VPN, RDG| |● |



## <a name="where-are-the-users-located"></a>Onde estão localizados os utilizadores

Em seguida, olhar onde estão localizados os nossos utilizadores ajuda a determinar a solução correta para utilizar, se na nuvem ou no local com o servidor de MFA.



Localização do utilizador| Autenticação Multifator na nuvem|Servidor de autenticação Multifator
------------- | :-------------: | :-------------: |
Azure Active Directory|● | |
Azure AD e no local com a Federação com o AD FS de AD|● |● |
Azure AD e no local AD utilizando o DirSync, Azure AD Sync, ligação do Azure AD - sem sincronização de palavra-passe|● |● |
Azure AD e no local AD utilizando o DirSync, Azure AD Sync, ligação do Azure AD - com a sincronização de palavra-passe|● | |
Active Directory no local| |● |

## <a name="what-features-do-i-need"></a>Que funcionalidades de preciso?

A tabela que se segue é uma comparação das funcionalidades que estão disponíveis com autenticação Multifator na nuvem e com o servidor de autenticação Multifator.

 | Autenticação Multifator na nuvem | Servidor de autenticação Multifator
------------- | :-------------: | :-------------: |
Notificação de aplicação móvel como um fator de segundo | ● | ● |
Código de verificação da aplicação móvel como um fator de segundo | ● | ●
Chamada telefónica como segundo factor | ● | ●
SMS unidirecional como segundo factor | ● | ●
SMS bidireccional como segundo factor |  | ●
Tokens de hardware como segundo factor |  | ●
Palavras-passe de aplicação para clientes que não suportam MFA | ● |  
Controlo de administração sobre métodos de autenticação | ● | ●
Modo PIN |  | ●
Alerta de fraude | ● | ●
Relatórios MFA | ● | ●
Ignorar único |  | ●
Saudações personalizadas para chamadas telefónicas | ● | ●
ID do autor da chamada personalizáveis para chamadas telefónicas | ● | ●
IPs de confiança | ● | ●
Lembre-se MFA para dispositivos fidedignos  | ● |  
Acesso condicional | ● | ●
Cache |  | ●

Agora que foi determinado se deve utilizar autenticação multifator de nuvem ou o MFA servidor no local, vamos começar a configurar e utilizando a autenticação Multifator do Azure. **Selecione o ícone que representa o seu cenário!**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>
