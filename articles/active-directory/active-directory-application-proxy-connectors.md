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


# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicações em redes separadas e localizações utilizar grupos de conexão

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure portal clássico](active-directory-application-proxy-connectors.md)


Grupos de conexão são úteis para vários cenários, incluindo:

- Sites com vários centros de dados interligados. Neste caso, que pretende manter todo o tráfego o Centro de dados possível, uma vez que o Centro de dados em várias ligações costumam dispendioso e lenta. Pode implementar os conectores em cada centro de dados para servir apenas as aplicações que residem dentro o Centro de dados. Esta abordagem minimiza Centro de dados em várias ligações e fornece uma experiência totalmente transparente aos seus utilizadores.
- Gerir aplicações instaladas em redes isoladas que não fazem parte da rede principal da empresa. Pode utilizar grupos de conexão para instalar conectores dedicadas redes isoladas para também isolar aplicações à rede.
- Para aplicações instaladas IaaS para acesso à nuvem, os grupos de conexão fornecem um serviço comum para proteger o acesso às todas as aplicações. Grupos de conexão não criar dependência adicional na rede da sua empresa ou fragmento a experiência de aplicação. Conectores podem ser instalados em cada centro de dados na nuvem e servem apenas as aplicações que residem na rede. Pode instalar várias conexões para alcançar elevada disponibilidade.
- Suporte para múltiplos floresta ambientes na qual conectores específicos podem ser implementadas por floresta e definir para servir aplicações específicas.
- Grupos de conexão podem ser utilizados em sites de recuperação de falhas para detetar quer activação pós-falha ou como cópia de segurança para o site principal.
- Grupos de conexão também podem ser utilizados para servir várias empresas a partir de um inquilino único.

## <a name="prerequisite-create-your-connectors"></a>Pré-requisito: Criar o seu conectores
Para agrupar as conexões, têm Certifique-se de que [instalou múltiplos conectores](active-directory-application-proxy-enable.md)e esse nome-los e, em seguida, agrupá-los. Por fim tem de atribuí-los às aplicações específicas.

## <a name="step-1-create-connector-groups"></a>Passo 1: Criar grupos de conexão
Pode criar quantos grupos de conexão que pretende. Criação de grupos de conexão é feita no portal do Azure clássico.

1. Selecione o seu diretório e clique em **Configurar**.  
    ![Proxy de aplicação, configure a captura de ecrã - clique em Gerir grupos de conexão](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. Em Proxy de aplicação, clique em **Gerir grupos de conexão** e criar um novo grupo de conexão dê um nome ao grupo.  
    ![Aplicação proxy conexão grupos captura de ecrã - novo grupo de nome](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## <a name="step-2-assign-connectors-to-your-groups"></a>Passo 2: Atribuir conectores aos seus grupos
Quando são criados os grupos de conexão, avance as conexões para o grupo apropriado.

1. Em **Proxy de aplicação**, clique em **Gerir conectores**.
2. Em **grupo**, selecione o grupo que pretende para cada conector. Pode demorar os conectores até 10 minutos para se tornar ativa, no grupo novo.  
    ![Captura de conectores ecrã aplicação proxy - selecionar grupo a partir do menu pendente](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## <a name="step-3-assign-applications-to-your-connector-groups"></a>Passo 3: Atribuir aplicações aos grupos de conexão
O último passo é configurar cada aplicação para o grupo de conexão que irá servi-lo.

1. No portal clássico Azure, no seu diretório, selecione a aplicação que pretende atribuir ao grupo e clique em **Configurar**.
2. Em **grupo de conexão**, selecione o grupo que pretende que a aplicação para utilizar. Esta alteração é aplicada imediatamente.  
    ![Aplicação proxy conexão grupo captura de ecrã - selecionar grupo a partir do menu pendente](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## <a name="see-also"></a>Consulte também

- [Ativar o Proxy de aplicação](active-directory-application-proxy-enable.md)
- [Ativar o início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
- [Ativar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Resolução de problemas que ocorram com Proxy de aplicação](active-directory-application-proxy-troubleshoot.md)

Para obter as notícias mais recentes e um atualizações, consulte o [blogue do Proxy de aplicação](http://blogs.technet.com/b/applicationproxyblog/)
