<properties
    pageTitle="Relatório de início de sessão no atividade do Azure Active Directory referência da API | Microsoft Azure"
    description="Referência para a API do Azure Active Directory atividade de início de sessão no relatório"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Relatório de início de sessão no atividade do Azure Active Directory referência da API


Este tópico faz parte de uma coleção de tópicos sobre o Azure Active Directory API de elaboração de relatórios.  
Azure AD relatórios fornece uma API que lhe permita aceder a dados de relatório de início de sessão no atividade utilizando código ou ferramentas relacionadas.
O âmbito deste tópico é fornecer-lhe informações de referência sobre o **início de sessão no API do relatório de atividade**.

Consulte o artigo:

- [Atividades de início de sessão](active-directory-reporting-azure-portal.md#sign-in-activities) para obter mais informações conceptuais
- [Introdução ao Azure Active Directory relatórios API](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API do relata.

Para as questões, problemas ou comentários, contacte o suportehttp [AAD relatórios ajudar](mailto:aadreportinghelp@microsoft.com).



## <a name="who-can-access-the-api-data"></a>Quem pode aceder os dados de API?

- Utilizadores na função de administrador de segurança ou leitor de segurança

- Administradores globais

- Qualquer aplicação do que tem autorização para aceder a API (autorização de aplicação pode ser configuração apenas com base em permissões de Administrador Global)



## <a name="prerequisites"></a>Pré-requisitos

Para aceder a este relatório através da API elaboração de relatórios, tem de ter:

- Um [Azure Active Directory Premium P1 ou P2 edition](active-directory-editions.md)

- Concluir a [Pré-requisitos para aceder ao Azure AD API de elaboração de relatórios](active-directory-reporting-api-prerequisites.md). 


##<a name="accessing-the-api"></a>Aceder a API

Ou pode aceder a esta API através do [Explorador do gráfico](https://graphexplorer2.cloudapp.net) ou através de programação a utilizar, por exemplo, PowerShell. Ordem para o PowerShell para corretamente interpretar a sintaxe de filtro de OData utilizada em chamadas AAD Graph resto, tem de utilizar o backtick (também conhecidos por: acento grave) caráter "escape" o caráter $. O caráter backtick serve como [caráter de escape do PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permitindo que o PowerShell para fazer uma interpretação literal de um caráter de $ e evitar confusa-lo como um nome de variável PowerShell (ie: $filter).

O foco deste tópico é no Explorador do gráfico. Por exemplo um PowerShell, consulte o artigo este [script do PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).


## <a name="api-endpoint"></a>Ponto final de API

Pode aceder a esta API utilizando o URI base seguinte:  
    
    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



Devido ao volume de dados, esta API tem um limite de um milhão devolvido registos. 

Esta chamada devolve os dados em lotes. Cada lote tem um máximo de 1000 registos.  
Para obter o próximo lote de registos, utilize a hiperligação seguinte. Obter as informações de [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) do primeiro conjunto de registos devolvidos. O token de ignorar será no final do resultado definido.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtros suportados

Pode limitar o número de registos que são devolvidos por uma API chamar no formulário de um filtro.  
Para iniciar sessão API dados relacionados com a, os filtros seguintes são suportados:

- **$top =\<número de registos para ser devolvido\> ** - para limitar o número de registos devolvidos. Esta é uma operação dispendiosa. Não deve utilizar este filtro se pretender que seja devolvido milhares de objetos.  
- **$filter =\<seu extrato de filtro\> ** - para especificar, com base nos campos de filtro suportado, os tipos de registos que lhe interessam



## <a name="supported-filter-fields-and-operators"></a>Campos de filtro suportados e dos operadores

Para especificar o tipo de registos que lhe interessam, pode criar uma instrução de filtro que pode conter um ou uma combinação dos seguintes campos de filtro:

- [signinDateTime](#signindatetime) - define uma data ou um intervalo de datas

- [ID de utilizador](#userid) - define um específico de utilizador com base ID. do utilizador

- [userPrincipalName](#userprincipalname) - define uma específicas do utilizador com base no nome principal de utilizador do utilizador (UPN)

- [appId](#appid) - define um específico aplicação com base ID da aplicação

- [appDisplayName](#appdisplayname) - define um específico aplicação com base no nome a apresentar a aplicação

- [loginStatus](#loginStatus) - define o estado dos inícios de sessão (sucesso / falha)


> [AZURE.NOTE] Quando através do Explorador do gráfico, é necessário utilizar as maiúsculas/minúsculas correta para cada letra é os campos de filtro.


Para limitar o âmbito dos dados devolvidos, pode criar combinações dos filtros suportados e os campos de filtro. Por exemplo, a seguinte instrução devolve os registos de 10 superiores entre Julho de 1. º de 2016 e de 2016 6a de Julho de:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### <a name="signindatetime"></a>signinDateTime

**Operadores suportados**: eq, página, le, gt, lt

**Exemplo**:

Utilizar uma data específica

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z  



Utilizar um intervalo de datas    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Notas**:

O parâmetro datetime deve estar no formato UTC 


----------

### <a name="userid"></a>ID de utilizador

**Operadores suportados**: eq

**Exemplo**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Notas**:

O valor de ID de utilizador for um valor de cadeia



----------

### <a name="userprincipalname"></a>userPrincipalName

**Operadores suportados**: eq

**Exemplo**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Notas**:

O valor de userPrincipalName for um valor de cadeia

----------

### <a name="appid"></a>appId

**Operadores suportados**: eq

**Exemplo**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Notas**:

O valor de appId for um valor de cadeia

----------


### <a name="appdisplayname"></a>appDisplayName

**Operadores suportados**: eq

**Exemplo**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Notas**:

O valor de appDisplayName for um valor de cadeia

----------

### <a name="loginstatus"></a>loginStatus

**Operadores suportados**: eq

**Exemplo**:

    $filter=loginStatus+eq+'1'  


**Notas**:

Existem duas opções para o loginStatus: 0 - sucesso, 1 - Falha

----------



## <a name="next-steps"></a>Próximos passos

- Pretende ver exemplos de atividades de início de sessão filtradas? Consulte o artigo as [amostras de relatório API de atividade de início de sessão no Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).

- Pretende saber mais sobre o Azure AD relatórios API? Consulte o artigo [Introdução ao Azure Active Directory relatórios API](active-directory-reporting-api-getting-started.md).