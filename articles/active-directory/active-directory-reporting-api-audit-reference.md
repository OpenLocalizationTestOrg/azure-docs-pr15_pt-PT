<properties
    pageTitle="Auditoria de Azure Active Directory referência da API | Microsoft Azure"
    description="Como começar com a API de auditoria Azure Active Directory"
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
    ms.date="10/24/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-audit-api-reference"></a>Auditoria de Azure Active Directory referência da API

Este tópico faz parte de uma coleção de tópicos sobre o Azure Active Directory API de elaboração de relatórios.  
Azure AD relatórios fornece uma API que lhe permita aceder a dados de registo de auditoria utilizando código ou ferramentas relacionadas.
O âmbito deste tópico é fornecer-lhe informações de referência sobre **API de auditoria**.

Consulte o artigo:

- [Registos de auditoria](active-directory-reporting-azure-portal.md#audit-logs) para obter mais informações conceptuais
- [Introdução ao Azure Active Directory relato API](active-directory-reporting-api-getting-started.md) para obter mais informações sobre a API do relata.

Para as questões, problemas ou comentários, contacte o suportehttp [AAD relato ajudar](mailto:aadreportinghelp@microsoft.com).


## <a name="who-can-access-the-data"></a>Quem pode aceder aos dados?

- Utilizadores na função de administrador de segurança ou leitor de segurança

- Administradores globais

- Qualquer aplicação do que tem autorização para aceder a API (autorização de aplicação pode ser configuração apenas com base em permissões de Administrador Global)



## <a name="prerequisites"></a>Pré-requisitos

Para poder aceder a este relatório através da API elaboração de relatórios, tem de ter:

- Um [Azure Active Directory gratuita ou melhor edition](active-directory-editions.md)

- Concluir a [Pré-requisitos para aceder ao Azure AD API de elaboração de relatórios](active-directory-reporting-api-prerequisites.md). 
 

##<a name="accessing-the-api"></a>Aceder a API

Ou pode aceder a esta API através do [Explorador do gráfico](https://graphexplorer2.cloudapp.net) ou através de programação a utilizar, por exemplo, PowerShell. Ordem para o PowerShell para corretamente interpretar a sintaxe de filtro de OData utilizada em chamadas AAD Graph resto, tem de utilizar o backtick (também conhecidos por: acento grave) caráter "escape" o caráter $. O caráter backtick serve como [caráter de escape do PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permitindo que o PowerShell para fazer uma interpretação literal de um caráter de $ e evitar confusa-lo como um nome de variável PowerShell (ie: $filter).

O foco deste tópico é no Explorador do gráfico. Por exemplo um PowerShell, consulte o artigo este [script do PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

 
 

## <a name="api-endpoint"></a>Ponto final de API


Pode aceder a esta API utilizando o URI seguinte:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Não existe nenhum limite o número de registos devolvidos pela API de auditoria do Azure AD (utilizando OData paginação).
Para limites de retenção de dados de relatórios, consulte o artigo [Políticas de retenção de elaboração de relatórios](active-directory-reporting-retention.md).

Esta chamada devolve os dados em lotes. Cada lote tem um máximo de 1000 registos.  
Para obter o próximo lote de registos, utilize a hiperligação seguinte. Obter as informações de skiptoken do primeiro conjunto de registos devolvidos. O token de ignorar será no final do resultado definido.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtros suportados

Pode limitar o número de registos que são devolvidos por uma API chamar no formulário de um filtro.  
Para iniciar sessão API dados relacionados com a, os filtros seguintes são suportados:

- **$top =\<número de registos para ser devolvido\> ** - para limitar o número de registos devolvidos. Esta é uma operação dispendiosa. Não deve utilizar este filtro se pretender que seja devolvido milhares de objetos.     
- **$filter =\<seu extrato de filtro\> ** - para especificar, com base nos campos de filtro suportado, os tipos de registos que lhe interessam



## <a name="supported-filter-fields-and-operators"></a>Campos de filtro suportados e dos operadores

Para especificar o tipo de registos que lhe interessam, pode criar uma instrução de filtro que pode conter um ou uma combinação dos seguintes campos de filtro:

- [activityDate](#activitydate) - define uma data ou um intervalo de datas
- [activityType](#activitytype) - define o tipo de uma atividade
- [atividade](#activity) - define a atividade como cadeia  
- [nome do ator /](#actorname) - define o ator no formulário do nome do ator
- [ID de objecto/ator](#actorobjectid) - define o ator no formulário de ID do ator   
- [ator/upn](#actorupn) - define o ator no formulário princípio do ator nome de utilizador (UPN) 
- [nome do destino](#targetname) - define o destino no formulário do nome do ator
- [ID de destino/objecto](#targetobjectid) - define o destino da forma de ID de destino  
- [destino/upn](#targetupn) - define o ator no formulário princípio do ator nome de utilizador (UPN)   




----------

### <a name="activitydate"></a>activityDate

**Operadores suportados**: eq, página, le, gt, lt

**Exemplo**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Notas**:

data/hora deve estar no formato de UTC

----------

### <a name="activitytype"></a>activityType

**Operadores suportados**: eq

**Exemplo**:

    $filter=activityType eq 'User'  

**Notas**:

maiúsculas/minúsculas

----------

### <a name="activity"></a>atividade

**Operadores suportados**: eq, contém, startsWith

**Exemplo**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Notas**:

maiúsculas/minúsculas

----------

### <a name="actorname"></a>ator/nome

**Operadores suportados**: eq, contém, startsWith

**Exemplo**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Notas**:

maiúsculas e minúsculas

    

----------
### <a name="actorobjectid"></a>ator/ID de objecto

**Operadores suportados**: eq

**Exemplo**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

----------
### <a name="targetname"></a>nome do destino

**Operadores suportados**: eq, contém, startsWith

**Exemplo**:

    $filter=targets/any(t: t/name eq 'some name')   

**Notas**:

Maiúsculas e minúsculas

----------

### <a name="targetupn"></a>destino/upn

**Operadores suportados**: eq, startsWith

**Exemplo**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Notas**:

- Maiúsculas e minúsculas
- Tem de adicionar espaço de nomes completo quando consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

----------

### <a name="targetobjectid"></a>ID de destino/objecto

**Operadores suportados**: eq

**Exemplo**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

----------

### <a name="actorupn"></a>ator/upn

**Operadores suportados**: eq, startsWith

**Exemplo**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Notas**:

- Maiúsculas e minúsculas 
- Tem de adicionar espaço de nomes completo quando consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

----------




## <a name="next-steps"></a>Próximos passos

- Pretende ver exemplos de atividades de sistema filtrada? Consulte o artigo o [Azure Active Directory auditoria amostras API](active-directory-reporting-api-audit-samples.md).

- Pretende saber mais sobre o Azure AD relatórios API? Consulte o artigo [Introdução ao Azure Active Directory relato API](active-directory-reporting-api-getting-started.md).