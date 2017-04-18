<properties
    pageTitle="Trabalhar com conexões de Proxy de aplicação do Azure AD | Microsoft Azure"
    description="Aborda como criar e gerir grupos de conectores não relacionados no Azure AD para o Proxy de aplicação."
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
    ms.date="09/09/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups---public-preview"></a>Publicar aplicações em redes separadas e localizações utilizar grupos de conexão - Public Preview

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure portal clássico](active-directory-application-proxy-connectors.md)


Grupos de conexão são úteis para vários cenários, incluindo:

- Sites com vários centros de dados interligados. Neste caso, que pretende manter todo o tráfego o Centro de dados possível, uma vez que o Centro de dados em várias ligações são dispendioso e lenta. Pode implementar os conectores em cada centro de dados para servir apenas as aplicações que residem dentro o Centro de dados. Esta abordagem minimiza Centro de dados em várias ligações e fornece uma experiência totalmente transparente aos seus utilizadores.
- Gerir aplicações instaladas em redes isoladas que não fazem parte da rede principal da empresa. Pode utilizar grupos de conexão para instalar conectores dedicadas redes isoladas para também isolar aplicações à rede.
- Para aplicações instaladas IaaS para acesso à nuvem, os grupos de conexão fornecem um serviço comum para proteger o acesso às todas as aplicações. Grupos de conexão não criar dependência adicional na rede da sua empresa ou fragmento a experiência de aplicação. Conectores podem ser instalados em cada centro de dados na nuvem e servem apenas as aplicações que residem na rede. Pode instalar várias conexões para alcançar elevada disponibilidade.
- Suporte para múltiplos floresta ambientes na qual conectores específicos podem ser implementadas por floresta e definir para servir aplicações específicas.
- Grupos de conexão podem ser utilizados em sites de recuperação de falhas para detetar quer activação pós-falha ou como cópia de segurança para o site principal.
- Grupos de conexão também podem ser utilizados para servir várias empresas a partir de um inquilino único.

## <a name="prerequisite-create-your-connectors"></a>Pré-requisito: Criar o seu conectores
Para agrupar as conexões, tem de Certifique-se de [instalou múltiplos conectores](active-directory-application-proxy-enable.md). Quando instalar uma nova conexão, associa automaticamente o grupo de conexão **predefinido** .

## <a name="step-1-create-connector-groups"></a>Passo 1: Criar grupos de conexão
Pode criar quantos grupos de conexão que pretende. Criação de grupos de conexão é realizada no [portal do Azure](https://portal.azure.com).

1. Selecione o **Azure Active Directory** para ir para o dashboard de gestão do seu diretório. A partir daí, selecione **aplicações empresariais** > **proxy de aplicação**.

2. Selecione o botão de **Grupos de conexão** . É apresentada a pá novo grupo de conexão.

3. Dê um nome ao seu novo grupo de conexão e, em seguida, utilize o menu pendente para selecionar as conexões pertencem neste grupo.

4. Selecione **Guardar** quando a conexão grupo estiver concluída.

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Passo 2: Atribuir aplicações aos grupos de conexão
O último passo é configurar cada aplicação para o grupo de conexão que irá servi-lo.

1. A partir do dashboard de gestão do seu diretório, selecione **aplicações empresariais** > **todas as aplicações** > a aplicação que pretende atribuir a um grupo de conexão > **Proxy de aplicação**.
2. Em **grupo de conexão**, utilize o menu pendente para selecionar o grupo que pretende que a aplicação para utilizar.
3. Selecione **Guardar** para aplicar a alteração.


## <a name="see-also"></a>Consulte também

- [Ativar o Proxy de aplicação](active-directory-application-proxy-enable.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Resolução de problemas que ocorram com Proxy de aplicação](active-directory-application-proxy-troubleshoot.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)
