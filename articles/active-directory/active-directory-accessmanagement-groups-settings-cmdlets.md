<properties
    pageTitle="Azure Active Directory cmdlets para configurar as definições de grupo | Microsoft Azure"
    description="Como gerir as definições de grupos de utilizar os cmdlets do Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="curtand"/>


# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory cmdlets para configurar as definições de grupo

As seguintes definições para grupos unificados podem ser configuradas no seu diretório:

1.  Classificações: separados por vírgulas lista de classificações de que os utilizadores podem definir num grupo. Exemplos seria "Classificados", "Secreta" e "Superior segredo."

2.  A utilização diretrizes URL: um URL que aponta utilizadores para os termos de utilização para utilizar grupos de Unified, tal como foi definido pela sua organização. Este URL irá aparecer na interface de utilizador, onde os utilizadores utilizam o grupos.

3.  Agrupar criação ativada: Se nenhuma, alguns ou todos os utilizadores têm permissão para criar grupos Unified. Quando ativada, todos os utilizadores podem criar grupos. Quando definida para desligado, os utilizadores não podem criar grupos. Quando terminar, também pode especificar um grupo de segurança cujos utilizadores que ainda estão autorizados a criar grupos.

Estas definições são configuradas utilizando definições de um e SettingsTemplate objetos. Inicialmente, não verá os objetos de definições no seu diretório. Isto significa que o seu diretório está configurado com as predefinições. Para alterar as predefinições, irá criar um novo objeto definições através de um modelo de definições. Definições do modelos são definidas pela Microsoft.

Pode transferir o módulo que contém os cmdlets utilizados para estas operações a partir do [site da Microsoft ligar](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).

## <a name="create-settings-at-the-directory-level"></a>Criar definições ao nível do diretório

Estes passos criam definições ao nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Se não souber qual SettingTemplate para utilizar, este cmdlet devolve a lista de modelos de definições:

    `Get-MsolAllSettingTemplate`

    ![Lista de modelos de definições](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. Para adicionar um URL de orientação de utilização, pela primeira vez que precisa para obter o objeto SettingsTemplate que define o valor de URL de orientação de utilização; Isto é, o modelo de Group.Unified:

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. Em seguida, crie um novo objeto definições com base nesse modelo:

    `$setting = $template.CreateSettingsObject()`

4. Em seguida, atualize o valor de orientação de utilização:

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. Por fim, aplicam-se as definições:

    `New-MsolSettings –SettingsObject $setting`

    ![Adicionar um URL de orientação de utilização](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

Seguem-se as definições de na Group.Unified SettingsTemplate.

 **Definição**                          | **Descrição**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>Tipo: cadeia<li>Predefinição: ""                  | Uma lista de valores de classificação válida que podem ser aplicadas a grupos Unified delimitado por vírgulas.                
 <ul><li>EnableGroupCreation<li>Tipo: booleana<li>Predefinido: VERDADEIRO              | O sinalizador que indica se a criação de grupos de unificado é permitida no diretório.                               
 <ul><li>GroupCreationAllowedGroupId<li>Tipo: cadeia<li>Predefinição: ""         | GUID do grupo de segurança que é permitido para criar grupos Unified mesmo quando EnableGroupCreation = = false.
 <ul><li>UsageGuidelinesUrl<li>Tipo: cadeia<li>Predefinição: ""                  | Uma ligação para as diretrizes de utilização do grupo.                                                                       

## <a name="read-settings-at-the-directory-level"></a>Definições ao nível do diretório de leitura

Estes passos leia definições ao nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Ler todas as definições de diretório existentes:

    `Get-MsolAllSettings`

2. Ler todas as definições para um grupo específico:

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. Definições do directório específico, utilizando SettingId GUID de leitura:

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![GUID do ID de definições](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>Atualizar definições ao nível do diretório

Estes passos atualizar as definições ao nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Obter o objeto de definições existente:

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. Obter o valor que pretende atualizar:

    `$value = $Setting.GetSettingsValue()`

3. Atualize o valor:

    `$value["AllowToAddGuests"] = "false"`

4. Atualize a definição:

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>Remover as definições ao nível do diretório

Este passo remove definições ao nível do diretório, que se aplicam a todos os grupos do Office no diretório.

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Referência do cmdlet sintaxe

Pode encontrar mais documentação do Azure Active Directory PowerShell no [Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260).

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>Referência de objetos do SettingsTemplate (Group.Unified SettingsTemplate objecto)

- "nome": "EnableGroupCreation", "tipo": "Foi", "ValorPredefinido": "true", "Descrição": "Um sinalizador booleano que indica se a funcionalidade de criação de grupo unificado está."

- "nome": "GroupCreationAllowedGroupId", "tipo": "System", "ValorPredefinido": "", "Descrição": "GUID do grupo de segurança é whitelisted para criar grupos de unificado."

- "nome": "ClassificationList", "tipo": "String", "ValorPredefinido": "", "Descrição": "Uma delimitado por vírgulas lista de valores de classificação válida que podem ser aplicadas a grupos unificado."

- "nome": "UsageGuidelinesUrl", "tipo": "String", "ValorPredefinido": "", "Descrição": "Uma ligação para as diretrizes de utilização do grupo."

nome | tipo | ValorPredefinido | Descrição
----------  | ----------  | ---------  | ----------
"EnableGroupCreation"  | "Foi"  | "true"  | "Um sinalizador booleano que indica se a funcionalidade de criação de grupo unificado está."
"GroupCreationAllowedGroupId"  | "System"  | ""  | "GUID do grupo de segurança é whitelisted para criar grupos Unified."
"ClassificationList"  | "String"  | ""  | "Uma delimitado por vírgulas lista de valores de classificação válida que podem ser aplicadas a grupos Unified."
"UsageGuidelinesUrl"  | "String"  | ""  | "Uma ligação para as diretrizes de utilização do grupo."

## <a name="next-steps"></a>Próximos passos

Pode encontrar mais documentação do Azure Active Directory PowerShell no [Azure Active Directory Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=808260).

Obter instruções adicionais a partir do Gestor de programa do Microsoft Filipe de Jong estão disponível em [Grupos blogue Filipe](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad).

* [Gerir o acesso aos recursos com os grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
