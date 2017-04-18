<properties
    pageTitle="Sincronização do Azure AD Connect: compreender as expressões de aprovisionamento declarativa | Microsoft Azure"
    description="Explica as expressões de aprovisionamento declarativas."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Sincronização do Azure AD Connect: compreender as expressões de aprovisionamento declarativa
Sincronização do Azure AD Connect constrói no aprovisionamento declarativa introduzidos em primeiro lugar no Gestor de identidades do Forefront 2010. Permite-lhe implementar lógica de negócio de integração de identidade completa sem ser necessário para escrever código compilado.

Uma peça essencial do aprovisionamento declarativa é a linguagem da expressão utilizada nos fluxos de atributo. O idioma utilizado é um subconjunto do Microsoft® Visual Basic for Applications (VBA). Este idioma é utilizado no Microsoft Office e os utilizadores com experiência de VBScript também irão reconhecê-lo. A linguagem da expressão aprovisionamento declarativa apenas está a utilizar funções e não é uma linguagem estruturada. Não existem métodos ou declarações. Funções em vez disso, são aninhadas para fluxo de um programa express.

Para obter mais detalhes, consulte o artigo [Bem-vindo ao Visual Basic for referência da linguagem de aplicações para o Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Os atributos estão a negrito. Uma função aceita apenas atributos do tipo correcto. Também é sensível às maiúsculas. Nomes de função e nomes de atributo tem de ter invólucro adequado ou é devolvido um erro.

## <a name="language-definitions-and-identifiers"></a>Definições de idioma e identificadores

- Funções têm um nome seguido argumentos escritos entre parênteses: nomedafunção (argumento 1, o argumento N).
- Atributos são identificados por entre parênteses Retos: [NomeAtributo]
- Parâmetros são identificados por sinais de percentagem: % ParameterName %
- As constantes da cadeia estão rodeadas por aspas: por exemplo, "Contoso" (Nota: tem de utilizar aspas direitas "" e não curvas "")
- Valores numéricos são expresso sem as aspas e esperados para ser decimal. Valores hexadecimais são o prefixo & H. Por exemplo, 98052 & HFF
- Os valores booleanos são expresso com constantes: VERDADEIRO, FALSO.
- Constantes incorporadas e literais são expresso com apenas o nome: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funções
Aprovisionamento declarativa utiliza muitas funções para ativar a possibilidade de transformar valores de atributo. Estas funções podem ser aninhadas para que o resultado de uma função lhe é transmitido na outra função.

`Function1(Function2(Function3()))`

Pode encontrar a lista completa de funções na [referência de funções](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parâmetros
Um parâmetro é definido por uma conexão ou por um administrador através do PowerShell. Parâmetros normalmente contêm valores que são diferentes a partir de um sistema para, por exemplo o nome do domínio, o utilizador está localizado no. Estes parâmetros podem ser utilizados em fluxos de atributo.

O Active Directory Connector fornecido seguintes parâmetros para as regras de sincronização de entrada:

| Nome do parâmetro | Comentário |
| --- | --- |
| Domain.Netbios | Formato de NetBIOS do domínio que está a ser importado atualmente, por exemplo FABRIKAMSALES |
| Domain.FQDN | Formato FQDN do domínio que está a ser importado atualmente, por exemplo sales.fabrikam.com |
| Domain.LDAP | Formato LDAP do domínio atualmente a ser importado, por exemplo CC = vendas, Cc = fabrikam, CC = com |
| Forest.Netbios | NetBIOS formato do nome floresta atualmente a ser importado, por exemplo FABRIKAMCORP |
| Forest.FQDN | Formato FQDN do nome floresta atualmente a ser importado, por exemplo fabrikam.com |
| Forest.LDAP | Formato LDAP do nome floresta atualmente a ser importado, por exemplo CC = fabrikam, CC = com |

O sistema fornece o parâmetro seguinte, o que é utilizado para obter o identificador de conexão atualmente em execução:  
`Connector.ID`

Eis um exemplo que preenche o domínio de atributo metaverse com o nome de domínio onde está localizado o utilizador netbios:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operadores
É possível utilizar os seguintes operadores:

- **Comparação**: <, < =, <>, =, >, > =
- **Matemática**: +, -, \*, -
- **Cadeia**: & (concatenar)
- **Lógica**: & & (e), | | (ou)
- **Ordem de avaliação**:)

Os operadores são avaliados da esquerda para a direita e têm a mesma prioridade de avaliação. Isto é, a \* (multiplicador) não for avaliada antes de - (subtracção). 2\*(5 + 3) não é igual a 2\*5 + 3. O (entre parênteses) são utilizados para alterar a ordem de avaliação quando esquerda para a ordem de avaliação direita não é adequada.

## <a name="multi-valued-attributes"></a>Atributos de múltiplos valores
As funções podem funcionar em atributos de valor único e múltiplos valores. Para atributos de múltiplos valores, a função opera sobre cada valor e aplica-se a mesma função a cada valor.

Por exemplo:  
`Trim([proxyAddresses])`Faça uma cortar de cada valor no atributo proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Para cada valor com um @-sign, substituir o domínio com @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Procure o endereço de SIP e removê-lo a partir dos valores.

## <a name="next-steps"></a>Próximos passos

- Leia mais sobre o modelo de configuração no [Noções sobre declarativa aprovisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Consulte o artigo como declarativa aprovisionamento é utilizado out-de-box no [Noções sobre a configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md).
- Veja como efetuar uma alteração prática utilizar aprovisionamento declarativa no [artigo como efetuar uma alteração para a configuração predefinida](active-directory-aadconnectsync-change-the-configuration.md).

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)

**Tópicos de referência**

- [Sincronização do Azure AD Connect: referência de funções](active-directory-aadconnectsync-functions-reference.md)
