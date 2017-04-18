<properties
   pageTitle="Azure Active Directory mapas - pré-visualizar | Microsoft Azure"
   description="Lista os vários relatórios disponíveis de pré-visualização do Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="markvi"/>

# <a name="azure-active-directory-reporting---preview"></a>Azure Active Directory mapas - pré-visualização

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-reporting-azure-portal.md)
- [Azure portal clássico](active-directory-reporting-guide.md)

*Esta documentação faz parte do [Azure relato Guia do Active Directory](active-directory-reporting-guide.md).*

Com relatórios na pré-visualização do Azure Active Directory, obter todas as informações que necessita para determinar como seu ambiente está a fazer. [Qual é a pré-visualização?](active-directory-preview-explainer.md)

Existem duas áreas principais de relatórios:

- **Início de sessão no atividades** – informações sobre a utilização de aplicações geridas e atividades de início de sessão de utilizador

- **Registos de auditoria** - informações do sistema atividade sobre utilizadores e gestão de grupos, as aplicações geridas e atividades de diretório

Consoante o âmbito dos dados que procura, pode aceder a estes relatórios ou ao clicar em **utilizadores e grupos** ou **aplicações empresariais** na lista de serviços no [portal do Azure](https://portal.azure.com).

## <a name="sign-in-activities"></a>Início de sessão no atividades

### <a name="user-sign-in-activities"></a>Atividades de início de sessão de utilizador

Com as informações fornecidas pelo relatório de início de sessão de utilizador, encontrar as respostas a perguntas como:

- O que é o padrão de início de sessão de um utilizador?
- Quantos utilizadores tenham utilizadores sessão iniciada no através de uma semana?
- O que é o estado destes suplementos início de sessão?

O ponto de entrada para estes dados é o utilizador início de sessão no graph na secção **Descrição geral** em **utilizadores e grupos**.

 ![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/05.png "Elaboração de relatórios")

O utilizador início de sessão no gráfico mostra semanais agregações de início de sessão ins para todos os utilizadores de um determinado período de tempo. A predefinição para o período de tempo é 30 dias.

![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/02.png "Elaboração de relatórios")

Quando clica num dia no início de sessão no gráfico, receberá uma lista detalhada das atividades iniciar sessão.

![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/03.png "Elaboração de relatórios")

Cada linha na lista de início de sessão no actividades dá-lhe informações detalhadas sobre o início de sessão-in seleccionado tais como:

- Quem tem sessão iniciada no?

- Qual foi o UPN relacionado?

- Que aplicações era o destino do iniciar sessão?

- O que é o endereço IP do iniciar sessão?

- Qual foi o estado de início de sessão no?

### <a name="usage-of-managed-applications"></a>Utilização da aplicações geridas

Com uma vista centrados em aplicação dos seus dados de início de sessão, pode responder a perguntas como:

- Quem é utilizando as minhas aplicações?

- Quais são as aplicações de 3 superiores na sua organização?

- Posso ter recentemente lançadas numa aplicação. Como está-lo a fazer?


O ponto de entrada para estes dados é as aplicações de 3 superiores na sua organização dentro o último relatório de 30 dias na secção **Descrição geral** em **aplicações empresariais**.

 ![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/06.png "Elaboração de relatórios")


As aplicação a utilização graph semanal agregações de sessão ins para as suas aplicações de início 3 um determinado período de tempo. A predefinição para o período de tempo é 30 dias.

![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/78.png "Elaboração de relatórios")

Se pretender, pode definir o foco numa aplicação específica.

![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Elaboração de relatórios")


Quando clica num dia no gráfico a utilização de aplicação, receberá uma lista detalhada das atividades iniciar sessão.


![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Elaboração de relatórios")



A opção de **início de sessão-ins** dá-lhe uma descrição geral completa de todos os eventos de início de sessão para as suas aplicações.

![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/85.png "Elaboração de relatórios")

Ao utilizar o Seletor de coluna, pode selecionar os campos de dados que pretende apresentar.

![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/column_chooser.png "Elaboração de relatórios")



### <a name="filtering-sign-ins"></a>Filtragem inícios de sessão

Pode filtrar inícios de sessão por um intervalo de tempo para limitar a quantidade de dados apresentados.

![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/927.png "Elaboração de relatórios")


Outro método para filtrar as entradas das atividades de início de sessão no consiste para procurar entradas específicas.
O método de pesquisa permite-lhe os início de sessão-ins à volta específicos **utilizadores**, **grupos** ou **aplicações**do âmbito.


![Elaboração de relatórios] (./media/active-directory-reporting-azure-portal/84.png "Elaboração de relatórios")

## <a name="audit-logs"></a>Registos de auditoria

Os registos de auditoria no Azure Active Directory fornecem registos de atividades de sistema para conformidade.

Existem três categorias principais para auditoria actividades relacionadas no portal do Azure:

- Utilizadores e grupos   

- Aplicações

- Diretório   


Para obter uma lista completa das atividades de relatório de auditoria, consulte a [lista de eventos de relatório de auditoria](active-directory-reporting-audit-events.md#list-of-audit-report-events).


O ponto de entrada para todos os dados de auditoria é **registos de auditoria** , na secção **atividade** do **Azure Active Directory**.


![Auditoria] (./media/active-directory-reporting-azure-portal/61.png "Auditoria")


Um registo de auditoria tem uma vista de lista que mostra os intervenientes (quem), as atividades de (o que) e as metas.


![Auditoria] (./media/active-directory-reporting-azure-portal/345.png "Auditoria")


Ao clicar num item na vista de lista, pode obter mais detalhes acerca do mesmo.

![Auditoria] (./media/active-directory-reporting-azure-portal/873.png "Auditoria")




### <a name="users-and-groups-audit-logs"></a>Utilizadores e grupos de registos de auditoria


Com o utilizador e relatórios de auditoria com base no grupo, pode obter as respostas a perguntas como:

- O que tipos de atualizações foram aplicados os utilizadores?

- Quantos utilizadores foram alterados?

- Foram alteradas quantas palavras-passe?

- O que tenha um administrador feito num diretório?

- Quais são os grupos que tenham sido adicionados?

- Existem grupos com alterações de associação?

- Os proprietários do grupo foram alterados?

- Que licenças foram atribuídas a um grupo ou por um utilizador?


Se apenas quiser rever dados de auditoria que está relacionada com a utilizadores e grupos, pode encontrar uma vista filtrada em **registos de auditoria** na secção de **atividade** de **utilizadores e grupos**.


![Auditoria] (./media/active-directory-reporting-azure-portal/93.png "Auditoria")


### <a name="application-audit-logs"></a>Registos de auditoria de aplicação

Com baseada na aplicação de relatórios de auditoria, pode obter as respostas a perguntas como:

- Quais são as aplicações que foram adicionadas ou atualizadas?

- Quais são as aplicações que foram removidas?

- Um princípio de serviço para uma aplicação tiver sido alterada?

- Os nomes das aplicações foram alterados?

- Quem deu consentimento para uma aplicação?


Se apenas quiser rever dados de auditoria que está relacionada com a aplicações, pode encontrar uma vista filtrada em **registos de auditoria** na secção de **atividade** da **aplicações empresariais**.


![Auditoria] (./media/active-directory-reporting-azure-portal/134.png "Auditoria")


### <a name="filtering-audit-logs"></a>Filtrar registos de auditoria

Pode filtrar um relatório de auditoria por um intervalo de tempo para limitar a quantidade de dados apresentados.

![Auditoria] (./media/active-directory-reporting-azure-portal/324.png "Auditoria")

Outro método para filtrar as entradas de registo de auditoria é para procurar entradas específicas.

![Auditoria] (./media/active-directory-reporting-azure-portal/237.png "Auditoria")

## <a name="next-steps"></a>Próximos passos

Consulte o artigo o [Azure Active Directory relato guia](active-directory-reporting-guide.md).
