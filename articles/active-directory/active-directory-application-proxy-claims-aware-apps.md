<properties
    pageTitle="Trabalhar com em afirmações deverá ter em consideração aplicações no Proxy de aplicação"
    description="Aborda como começar a trabalhar rapidamente com o Proxy de aplicação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>



# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabalhar com em afirmações deverá ter em consideração aplicações no Proxy de aplicação

Em afirmações deverá ter em consideração aplicações executar um redirecionamento para segurança Token serviço (STS), que por sua vez os pedidos de credenciais de utilizador em troca de um token antes de redirecionar o utilizador para a aplicação. Para ativar o Proxy de aplicação trabalhar com estes redirecionamentos, os passos seguintes têm ser encaminhado.

## <a name="prerequisites"></a>Pré-requisitos
Antes de executar este procedimento, certifique-se de que o STS a aplicação deverá ter em consideração em afirmações redireciona para está disponível fora da sua rede no local.

## <a name="azure-classic-portal-configuration"></a>Configuração do portal clássica Azure

1. Publica a sua aplicação de acordo com as instruções descritas [publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md).
2. Na lista de aplicações, selecione a aplicação deverá ter em consideração em afirmações e clique em **Configurar**.
3. Se tiver optado por **pass-through** como o seu **Método de pré-autenticação**, certifique-se selecionar **HTTPS** como o esquema de **URL externo** .
4. Se tiver optado por **Azure Active Directory** como o seu **Método de pré-autenticação**, selecione **nenhum** o seu **Método de autenticação interno**.


## <a name="adfs-configuration"></a>Configuração de ADFS

1. Abra a gestão de ADFS.
2. Vá para **Confiar festa fidedignidades**, clique com o botão direito do rato sobre a aplicação que está a publicar o Proxy de aplicação e selecione **Propriedades**.  
  ![Confia dependente de festa clique direito do rato no nome da aplicação - screentshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. No separador **pontos finais** , em **tipo de ponto final**, selecione **WS Federação**.
4. Em **Fidedignos URL** , introduza o URL introduzido no Proxy de aplicação em **URL externo** e clique em **OK**.  
  ![Adicionar um ponto final - definir valor de URL fidedignos - captura de ecrã](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>Consulte também

- [Publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Resolução de problemas que ocorram com Proxy de aplicação](active-directory-application-proxy-troubleshoot.md)
- [Ativar as aplicações de cliente nativo interagir com aplicações do proxy](active-directory-application-proxy-native-client.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)
