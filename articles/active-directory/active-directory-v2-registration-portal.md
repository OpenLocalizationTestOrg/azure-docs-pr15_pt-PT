<properties
    pageTitle="Registo de aplicação Portal tópicos de ajuda | Microsoft Azure"
    description="Uma descrição das várias funcionalidades no portal de registo de aplicação do Microsoft."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="app-registration-reference"></a>Referência de registo de aplicação
Este artigo fornece contexto e descrições das várias funcionalidades que se encontram no Portal de registo de aplicação do Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>Minhas aplicações
Esta lista contém todas as aplicações registadas para utilização com o ponto final do Azure AD 2.0.  Estas aplicações têm a capacidade de utilizadores com ambas as contas pessoais conta Microsoft e contas de trabalho/escola a partir do Azure Active Directory do início de sessão.  Para saber mais sobre o ponto final do Azure AD 2.0, consulte o nosso [Descrição geral de 2.0](active-directory-appmodel-v2-overview.md).  Estas aplicações podem também ser utilizadas para integrar com o Microsoft conta autenticação ponto final, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Aplicações directo SDK
Esta lista contém todas as aplicações registadas para utilização exclusivamente com a conta Microsoft.  Não estão activados para utilização com o Azure Active Directory qualquer.  Este é onde irá encontrar todas as aplicações que anteriormente tinham foi registadas com o portal de programador do MSA em `https://account.live.com/developers/applications`.  Todas as funções que anteriormente efetuadas na `https://account.live.com/developers/applications` podem agora ser executadas no portal novo, `https://apps.dev.microsoft.com`.  Se tiver questões adicionais sobre as aplicações de conta Microsoft, contacte-nos.

## <a name="application-secrets"></a>Aplicação segredos
Aplicação segredos são credenciais que permitem a sua aplicação efetuar fiável [autenticação de cliente](http://tools.ietf.org/html/rfc6749#section-2.3) com Azure AD.  Em OAuth e OpenID ligar, segredos uma aplicação é geralmente denominado uma `client_secret`.  No protocolo 2.0, qualquer aplicação que recebe um token de segurança numa localização endereçáveis web (utilizando um `https` esquema) tem de utilizar secreta uma aplicação para identificar perante Azure AD relativamente a reembolso desse token de segurança.  Além disso, qualquer cliente nativo desse receberá tokens num dispositivo será proibido de utilizar o segredo uma aplicação para efetuar a autenticação de clientes, para impedir o armazenamento dos segredos nos ambientes inseguros.

Cada aplicação pode conter duas segredos aplicação válida em qualquer ponto determinado altura.  Mantendo duas segredos, tem de ablilty para efetuar periódica sobreposição chave ao longo de todo o ambiente da sua aplicação.  Depois de migrar a totalidade da sua aplicação para uma nova palavra-passe, pode eliminar o segredo antigo e aprovisionar um novo.

Neste momento, são permitidos apenas dois tipos de segredos de aplicação no portal do registo de aplicação.  Escolher **Gerar nova palavra-passe** gerar e armazenar um segredo partilhado na loja respetivos dados, que pode utilizar na sua aplicação.  Escolher **Gerar novo par de chave** irá criar um novo público/privado par que pode ser transferido e utilizado para autenticação do cliente para Azure AD.

## <a name="profile"></a>Perfil
A secção de perfil do portal de registo de aplicação pode ser utilizada para personalizar a página Iniciar sessão para a sua aplicação.  Neste momento, é possível alterar o início de sessão logótipo da aplicação da página, termos de URL do serviço e a declaração de privacidade.  O logótipo deve ser um transparente 48 x 48 ou 50 x 50 pixels imagem num ficheiro GIF, PNG ou JPEG que é 15 KB ou menor.  Experimente alterando os valores e visualizar o sinal resultante na página!

## <a name="live-sdk-support"></a>Suporte SDK ao vivo
Quando ativa "Live SDK suporte", serão aprovisionados segredos qualquer aplicação que cria para o Azure AD e Account Microsoft arquivos de dados.  Isto irá permitir a sua aplicação integrar diretamente com o serviço do Microsoft Account (login.live.com).  Se pretender criar uma aplicação utilizando Account Microsoft diretamente (por oposição a utilizar o ponto final do Azure AD 2.0), deve certificar-se de que suporte SDK Live está ativado.

Desativar o suporte do Live SDK irá garantir que o segredo aplicação apenas é escrito com os dados do Azure AD armazenar.  Os dados do Azure AD loja incorpora regulamentos de empresarial que permitem-lo satisfazer certas normas, tal como conformidade FISMA.  Se ativar o suporte do Live SDK, a aplicação não poderá alcançar conformidade com algumas dessas normas.

Se apenas planear utilizar o ponto final do Azure AD 2.0, pode desativar suporte Live SDK em segurança.

