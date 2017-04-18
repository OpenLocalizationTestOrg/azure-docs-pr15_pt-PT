
<properties
    pageTitle="Utilizando os atributos para criar regras avançadas | Microsoft Azure"
    description="Como-para do criar regras avançadas para um grupo, incluindo suportadas parâmetros e operadores expressão da regra."
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
    ms.date="08/15/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules"></a>Utilizando os atributos para criar regras avançadas

Portal clássico do Azure fornece a capacidade de criar regras avançadas para ativar a mais complexas com base no atributo dinâmicas associações para grupos do Azure Active Directory (Azure AD).  

Quando os atributos de uma alteração de utilizador, o sistema avalia todas as regras de grupo dinâmicos num diretório para ver se a alteração de atributo do utilizador seria acionar qualquer grupo adiciona ou remove. Se um utilizador satisfizer uma regra num grupo, que sejam adicionados como um membro a esse grupo. Se já não satisfizerem a regra de um grupo são membro, estes são removidos como um membros a partir desse grupo.

## <a name="to-create-the-advanced-rule"></a>Para criar a regra avançada

1. No [portal clássica Azure](https://manage.windowsazure.com), selecione **Do Active Directory**e, em seguida, abra o diretório da sua organização.

2. Selecione o separador de **grupos** e, em seguida, abra o grupo que pretende editar.

3. Selecione o separador **Configurar** , selecione a opção de **regras avançadas** e, em seguida, introduza a regra avançada na caixa de texto.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construir o corpo de uma regra avançada

A regra avançada que pode criar para os membros dinâmicos para grupos é, essencialmente, uma expressão binária que consiste em três partes e produz um resultado verdadeiro ou FALSO. As três partes são:

- Parâmetro para a esquerda
- Operador binário
- Constante à direita

Uma regra avançada concluída aspeto semelhante a este: (leftParameter binaryOperator "RightConstant"), onde a abertura e o parêntese de fecho são necessários para toda a expressão binária, aspas são necessárias para a direita constante e, a sintaxe para o parâmetro esquerdo é user.property. Uma regra avançada pode ter mais do que um binários expressões separadas por- e- ou e - os operadores lógicos não.
Seguem-se exemplos de uma regra construída avançada:

- (user.department - eq "Vendas")- ou (user.department - eq "Marketing")
- (user.department - eq "Vendas")- e - não (user.jobTitle-contém "SDE")

Para a lista completa dos parâmetros suportados e operadores de regra de expressões, consulte o artigo secções abaixo.

O comprimento total do corpo da sua regra avançada não pode exceder os 2048 caracteres.

> [AZURE.NOTE]
>As operações de cadeia e regex são maiúsculas e minúsculas. Também pode executar verificações de nulo, utilizando $null como uma constante, por exemplo, user.department - eq $null.
Cadeias que contém as propostas "escape utilizando ' caráter, por exemplo, user.department - eq \`"Vendas".

## <a name="supported-expression-rule-operators"></a>Operadores de regra de expressões suportadas
A tabela seguinte lista todos os operadores de regra de expressão suportados e os respetivos sintaxe para ser utilizado no corpo da regra avançada:

| Operador        | Sintaxe         |
|-----------------|----------------|
| Não é igual a      | -            |
| É igual a          | -eq            |
| Não começa com | -notStartsWith |
| Começa com     | -startsWith    |
| Não contém    | -notContains   |
| Contém        | -contém      |
| CORRESP não       | -notMatch      |
| CORRESP           | -corresponder         |


## <a name="query-error-remediation"></a>Remediação de erros de consulta
A tabela seguinte lista os possíveis erros e como corrigi-los se ocorrem

| Erro de análise de consulta     | Utilização de erro       | Utilização corrigida             |
|-----------------------|-------------------|-----------------------------|
| Erro: Atributo não suportado.                                      | (user.invalidProperty - eq "Valor")       | (user.department - eq "valor")<br/>Propriedade deve corresponder a um partir do [suportadas lista propriedades](#supported-properties).                          |
| Erro: O operador não é suportado no atributo.                       | (user.accountEnabled-contém VERDADEIRO)                                                                               | (user.accountEnabled - eq verdadeiro)<br/>Propriedade é escreva booleano. Utilize os operadores suportados (-eq ou -) no booleano tipo na lista acima.                                                                                                                                   |
| Erro: Erro de compilação de consulta.                                      | (user.department - eq "Vendas")- e (user.department - eq "Marketing")(user.userPrincipalName-match"*@domain.ext") | (user.department - eq "Vendas")- e (user.department - eq "Marketing")<br/>Operador lógico deve corresponder a um partir da lista de propriedades suportadas acima. (user.userPrincipalName-corresponder ".*@domain.ext")or(user.userPrincipalName -corresponder "@domain.ext$")Error na expressão regular. |
| Erro: A expressão binário não está no formato correto.                     | (user.department – eq "Vendas") (user.department - eq "Vendas") (user.department-eq "Vendas")                             | (user.accountEnabled - eq verdadeiro)- e (user.userPrincipalName-contém"alias@domain")<br/>Consulta tem vários erros. Parêntese não no local certo.                                                                                                                            |
| Erro: Ocorreu um erro desconhecido durante a configuração de associações dinâmicas. | (user.accountEnabled - eq "True" AND user.userPrincipalName-contém"alias@domain")                               | (user.accountEnabled - eq verdadeiro)- e (user.userPrincipalName-contém"alias@domain")<br/>Consulta tem vários erros. Parêntese não no local certo.                                                                                                                            |

## <a name="supported-properties"></a>Propriedades suportadas
Seguem-se todas as propriedades de utilizador que pode utilizar na sua regra avançada:

### <a name="properties-of-type-boolean"></a>Propriedades de escreva booleano

Operadores permitidos

* -eq


* -


| Propriedades     | Valores permitidos  | Utilização                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | Verdadeiro Falso      | user.accountEnabled - eq verdadeiro)  |
| dirSyncEnabled | VERDADEIRO null FALSO | (user.dirSyncEnabled - eq verdadeiro) |

### <a name="properties-of-type-string"></a>Propriedades do tipo cadeia

Operadores permitidos

* -eq


* -


* -notStartsWith


* -StartsWith


* -contém


* -notContains


* -corresponder


* -notMatch

| Propriedades                 | Valores permitidos                                                                                        | Utilização                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Cidade                       | Qualquer valor de cadeia ou $null                                                                           | (user.city - eq "valor")                                   |
| país/região                    | Qualquer valor de cadeia ou $null                                                                            | (user.country - eq "valor")                                |
| departamento                 | Qualquer valor de cadeia ou $null                                                                          | (user.department - eq "valor")                             |
| DisplayName em grupos                | Qualquer valor de cadeia                                                                                 | (user.displayName - eq "valor")                            |
| facsimileTelephoneNumber   | Qualquer valor de cadeia ou $null                                                                           | (user.facsimileTelephoneNumber - eq "valor")               |
| givenName                  | Qualquer valor de cadeia ou $null                                                                           | (user.givenName - eq "valor")                              |
| Cargo                   | Qualquer valor de cadeia ou $null                                                                           | (user.jobTitle - eq "valor")                               |
| correio                       | Qualquer valor de cadeia ou $null (endereço SMTP do utilizador)                                                  | (user.mail - eq "valor")                                   |
| mailNickName               | Qualquer valor de cadeia (alias de correio do utilizador)                                                            | (user.mailNickName - eq "valor")                           |
| dispositivos móveis                     | Qualquer valor de cadeia ou $null                                                                           | (user.mobile - eq "valor")                                 |
| ID de objecto                   | GUID do objeto do utilizador                                                                            | (user.objectId - eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies           | Nenhum DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |   (user.passwordPolicies - eq "DisableStrongPassword")                                                      |
| physicalDeliveryOfficeName | Qualquer valor de cadeia ou $null                                                                            | (user.physicalDeliveryOfficeName - eq "valor")             |
| código postal                 | Qualquer valor de cadeia ou $null                                                                            | (user.postalCode - eq "valor")                             |
| preferredLanguage          | Código ISO de 639-1                                                                                        | (user.preferredLanguage - eq "en-US")                      |
| sipProxyAddress            | Qualquer valor de cadeia ou $null                                                                            | (user.sipProxyAddress - eq "valor")                        |
| Estado                      | Qualquer valor de cadeia ou $null                                                                            | (user.state - eq "valor")                                  |
| streetAddress              | Qualquer valor de cadeia ou $null                                                                            | (user.streetAddress - eq "valor")                          |
| Apelido                    | Qualquer valor de cadeia ou $null                                                                            | (user.surname - eq "valor")                                |
| telephoneNumber            | Qualquer valor de cadeia ou $null                                                                            | (user.telephoneNumber - eq "valor")                        |
| usageLocation              | Duas indicativo com letras                                                                           | (user.usageLocation - eq "EUA")                             |
| userPrincipalName          | Qualquer valor de cadeia                                                                                     | (user.userPrincipalName - eq"alias@domain")               |
| userType                   | convidado membro $null                                                                                    | (user.userType - eq "Membro")                              |

### <a name="properties-of-type-string-collection"></a>Propriedades da coleção do tipo cadeia

Operadores permitidos

* -contém


* -notContains

| Poperties      | Valores permitidos                        | Utilização                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | Qualquer valor de cadeia                      | (user.otherMails-contém"alias@domain")           |
| proxyAddresses | SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses-contém "SMTP:alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensão e atributos personalizados
Atributos de extensão e atributos personalizados são suportados no regras de associação dinâmicos.

Extensão atributos são sincronizados a partir do local janela servidor AD e tirar o formato do "ExtensionAttributeX" onde X é igual a 1-15.
Um exemplo de uma regra que utiliza um atributo extensão seria

(user.extensionAttribute15 - eq "Marketing")

Atributos personalizados são sincronizados a partir do local Windows Server AD ou a partir de uma aplicação SaaS ligada e o formato do "user.extension_[GUID]\__ [atributo]", onde [GUID] é o identificador exclusivo no AAD para a aplicação que criou o atributo no AAD e [atributo] é o nome do atributo, tal como foi criada.
É um exemplo de uma regra que utiliza um atributo personalizado

User.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

Pode encontrar o nome do atributo personalizado no diretório consultando um utilizador do atributo através do Explorador do gráfico e procurar o nome do atributo.

## <a name="direct-reports-rule"></a>Regra de diretos
Agora pode preencher membros de um grupo com base no atributo do Gestor de um utilizador.

**Para configurar um grupo como um grupo de "Gestor"**

1. No portal do Azure clássico, clique em **Active Directory**e, em seguida, clique no nome do diretório da sua organização.

2. Selecione o separador de **grupos** e, em seguida, abra o grupo que pretende editar.

3. Selecione o separador de **Configurar** e, em seguida, selecione **Avançadas regra**.

4. Escreva a regra com a seguinte sintaxe:

    Direccionar relatórios para *diretos para {obectID_of_manager}*. É um exemplo de uma regra válido para diretos

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    onde "62e19b97-8b3d-4d4a-a106-4ce66896a863" é o ID de objecto do gestor. Pode encontrar o ID do objeto no Azure AD no **separador perfil** da página do utilizador para o utilizador que é o gestor.

3. Ao guardar esta regra, todos os utilizadores que satisfaçam a regra irão estar associados como membros do grupo. Pode demorar alguns minutos para o grupo preencher inicialmente.


## <a name="using-attributes-to-create-rules-for-device-objects"></a>Utilizando os atributos para criar regras para objectos de dispositivo

Também pode criar uma regra de que seleciona objetos de dispositivo para associação num grupo. É possível utilizar os seguintes atributos de dispositivo:

| Propriedades              | Valores permitidos                  | Utilização                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| DisplayName em grupos             | qualquer valor de cadeia                | (device.displayName - eq "Filipe Iphone")                       |
| deviceOSType            | qualquer valor de cadeia                | (device.deviceOSType - eq "IOS")                             |
| deviceOSVersion         | qualquer valor de cadeia                | (dispositivo. OSVersion - eq "9.1")                                |
| isDirSynced             | VERDADEIRO null FALSO                 | (device.isDirSynced - eq "true")                             |
| isManaged               | VERDADEIRO null FALSO                 | (device.isManaged - eq "falso")                              |
| isCompliant             | VERDADEIRO null FALSO                 | (device.isCompliant - eq "true")                             |
| deviceCategory          | qualquer valor de cadeia                | (device.deviceCategory - eq "")                              |
| deviceManufacturer      | qualquer valor de cadeia                | (device.deviceManufacturer - eq "Microsoft")                 |
| deviceModel             | qualquer valor de cadeia                | (device.deviceModel - eq "IPhone 7 +")                        |
| deviceOwnership         | qualquer valor de cadeia                | (device.deviceOwnership - eq "")                             |
| NomeDomínio              | qualquer valor de cadeia                | (device.domainName - eq "contoso.com")                       |
| enrollmentProfileName   | qualquer valor de cadeia                | (device.enrollmentProfileName - eq "")                       |
| isRooted                | VERDADEIRO null FALSO                 | (device.deviceOSType - eq "true")                            |
| managementType          | qualquer valor de cadeia                | (device.managementType - eq "")                              |
| organizationalUnit      | qualquer valor de cadeia                | (device.organizationalUnit - eq "")                          |
| deviceId                | um deviceId válida                | (device.deviceId - eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d" |

> [AZURE.NOTE]
> Estas regras de dispositivo não podem ser criadas utilizando no menu pendente "regra simples" no portal do Azure clássico.


## <a name="additional-information"></a>Informações adicionais
Estes artigos fornecem informações adicionais sobre o Azure Active Directory.

* [Associações dinâmicas para grupos de resolução de problemas](active-directory-accessmanagement-troubleshooting.md)

* [Gerir o acesso aos recursos com os grupos do Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory cmdlets para configurar as definições de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)

* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
