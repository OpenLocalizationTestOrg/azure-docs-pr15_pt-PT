<properties
   pageTitle="Ligação do Azure AD: Estruturar conceitos | Microsoft Azure"
   description="Este tópico detalhes determinadas áreas de estrutura de implementação"
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.custom = "azure-ad-connect"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="09/13/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-design-concepts"></a>Ligação do Azure AD: Conceitos de Design
É o objetivo neste tópico descrever as áreas que devem ser consideradas através de durante a estrutura de implementação de ligação do Azure AD. Este tópico é um vôo picado abrangente em determinadas áreas e estes conceitos são brevemente descritos nos outros tópicos também.

## <a name="sourceanchor"></a>sourceAnchor
O atributo sourceAnchor é definido como *um atributo imutáveis durante o tempo de vida de um objeto*. Identifica exclusivamente um objeto, como sendo o mesmo objeto no local e no Azure AD. O atributo é também chamado **immutableId** e os dois nomes são utilizados intermutáveis.

A palavra imutáveis, que é "não pode ser alterado", é importante neste tópico. Uma vez que valor este atributo não pode ser alterado após ter sido definida-lo, é importante escolher um design que suporta o seu cenário.

O atributo é utilizado para os cenários seguintes:

- Quando um novo servidor do motor de sincronização é criado ou recompilado depois de um cenário de recuperação de falhas, este atributo ligações objetos existentes no Azure AD com objetos no local.
- Se mover a partir de uma identidade apenas na nuvem a um modelo de identidade sincronizada, em seguida, este atributo permite que os objectos objetos existentes "correspondência de disco rígido" no Azure AD com objetos no local.
- Se utilizar Federação, em seguida, este atributo juntamente com o **userPrincipalName** é utilizado na afirmação de para identificar um utilizador de forma exclusiva.

Este tópico apenas fala sobre sourceAnchor que diz respeito aos utilizadores. As mesmas regras de aplicarem a todos os tipos de objeto, mas é apenas para os utilizadores que este problema é normalmente uma questão.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Selecionar um atributo sourceAnchor boa
O valor do atributo tem de seguir as seguintes regras:

- Ter menos de 60 caracteres de comprimento
    - Codificado e contabilizada como 3 carateres de carateres que não estão a-z, A-Z ou 0-9
- Não contém um caráter especial: & #92;! # $ % & * + / = ? ^ & #96; { } | ~ (< >) '; : , [ ] " @ _
- Tem de ser exclusivo globalmente
- Tem de ser uma cadeia, número inteiro ou em binário
- Não deve ser baseado em nome de utilizador, estas alterações
- Não deve ser entre maiúsculas e minúsculas e evitar valores que podem variar de maiúsculas/minúsculas
- Devem ser atribuídas quando o objeto é criado

Se o sourceAnchor selecionado não for do tipo cadeia, em seguida, Azure AD ligar Base64Encode o valor do atributo para garantir que não existem carateres especiais são apresentados. Se utilizar outro servidor de federação que ADFS, certifique-se o servidor de também pode Base64Encode o atributo.

O atributo sourceAnchor é entre maiúsculas e minúsculas. Um valor de "JoãoSilva" não é igual a "JoãoSilva". Mas que não deve ter dois objetos diferentes, com apenas uma diferença de maiúsculas/minúsculas.

Se tiver uma única floresta no local, em seguida, o atributo deve utilizar é **GUID de objecto**. Também é utilizado quando utilizar as definições de express na ligação do Azure AD o atributo e também o atributo utilizado pelo DirSync.

Se tiver várias florestas e não mover utilizadores entre florestas e os domínios, em seguida, **GUID de objecto** é um bom atributo para utilizar o mesmo neste caso.

Se mover utilizadores entre florestas e os domínios, tem localizar um atributo que não é alterada ou pode ser movido com os utilizadores durante a mover. Uma abordagem recomendada é apresentar um atributo síntese. Um atributo que poderia detêm algo semelhante a um GUID seria adequado. Durante a criação de objeto, um novo GUID é criado e carimbo de data / no utilizador. Uma regra personalizada sincronização pode ser criada no servidor do motor de sincronização para criar este valor baseado o **GUID de objecto** e atualize o atributo seleccionado no adiciona. Quando mover o objeto, certifique-se de que também copiar o conteúdo deste valor.

Outra solução é escolher um atributo existente, que sabe que não é alterada. Atributos utilizados mais frequentemente incluem **o campo IDdeEmpregado**. Se considerar um atributo que contém as letras, certifique-se existe que sem hipótese as maiúsculas/minúsculas (maiúsculas vs. minúsculas) pode alterar valor o atributo. Atributos incorretas que não devem ser utilizados incluem essas atributos com o nome do utilizador. Num casamento ou divórcio, o nome é esperado para alterar, que não é permitido deste atributo. Também é um motivo por que motivo atributos como **userPrincipalName**, **correio**e **targetAddress** não são mesmo possíveis selecionar no Assistente de ligação do Azure AD instalação. Também contêm esses atributos de @-character, que não é permitido na sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Alterar o atributo sourceAnchor
O valor do atributo sourceAnchor não pode ser alterado após o objeto foi criado no Azure AD e a identidade está sincronizada.

Por este motivo, as restrições seguintes aplicam-se para a ligação do Azure AD:

- O atributo sourceAnchor só pode ser definido durante a instalação inicial. Se executar novamente o Assistente de instalação, esta opção é só de leitura. Se precisar de alterar esta definição, tem de desinstalar e reinstalar o.
- Se instalar outro servidor de ligação do Azure AD, tem de selecionar o mesmo atributo sourceAnchor utilizado como anteriormente. Se anterior têm DirSync utilizando e deslocar-se para a ligação do Azure AD, em seguida, tem de utilizar **GUID de objecto** uma vez que é utilizado por DirSync o atributo.
- Se o valor para sourceAnchor for alterado após o objeto foi exportado para Azure AD, em seguida, ligação do Azure AD sync emitir um erro e não permitir a quaisquer alterações mais em que o objeto antes do problema foi corrigido e o sourceAnchor é alterado novamente no directório de origem.

## <a name="azure-ad-sign-in"></a>Azure AD iniciar sessão
Ao integrar o seu diretório no local com o Azure AD, é importante compreender como as definições de sincronização podem afetar o utilizador de forma autentica. Azure AD utiliza userPrincipalName (UPN) para autenticar o utilizador. No entanto, quando sincroniza os seus utilizadores, tem de escolher o atributo para ser utilizado para o valor de userPrincipalName cuidadosamente.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Escolher o atributo userPrincipalName
Quando estiver a selecionar o atributo para fornecer deve Certifique-se o valor de UPN para ser utilizado numa Azure

- Os valores de atributo está em conformidade com a sintaxe UPN (RFC 822), que é deverá ser do formatousername@domain
- Corresponde a sufixo os valores para um dos domínios verificados personalizados no Azure AD

Nas definições express, a opção assumida para o atributo é userPrincipalName. Se o atributo userPrincipalName não contém o valor que pretende que os utilizadores para iniciar sessão no Azure, em seguida, tem de escolher **Instalação personalizada**.

### <a name="custom-domain-state-and-upn"></a>Estado do domínio personalizado e UPN
É importante para se certificar de que existe um domínio verificado para o sufixo UPN.

João é um utilizador com contoso.com. Pretende que o João para utilizar o UPN no local john@contoso.com para iniciar sessão no Azure depois de ter sincronizado os utilizadores a sua contoso.onmicrosoft.com de diretório do Azure AD. Para fazê-lo, tem de adicionar e verificar contoso.com como um domínio personalizado no Azure AD antes de poder começar a sincronizar os utilizadores. Se o sufixo UPN do João, por exemplo, contoso.com, não corresponder a um domínio verificado no Azure AD, em seguida, Azure AD substitui o sufixo UPN contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Não encaminháveis no local domínios e UPN para Azure AD
Algumas organizações têm de domínios não encaminháveis, como Contoso ou domínios de etiqueta única simples como contoso. Não é possível verificar um domínio não encaminháveis no Azure AD. Ligação do Azure AD pode sincronizar para apenas um domínio verificado do Azure AD. Quando cria um directory Azure AD, que cria um domínio encaminhável que torna-se de domínio predefinido para o Azure AD, por exemplo, contoso.onmicrosoft.com. Por isso, se tornar necessária verificar a qualquer outro domínio encaminhável num cenário caso não pretenda que a sincronizar com o domínio onmicrosoft.com predefinido.

Leia a [Adicionar o seu nome de domínio personalizado para o Azure Active Directory](active-directory-add-domain.md) para obter mais informações sobre como adicionar e verificar domínios.

Ligação do Azure AD Deteta se estiver a executar o num ambiente de domínio não encaminháveis e seria corretamente avisá-lo a partir de começar a utilizar para a frente definições express. Se estiver a trabalhar num domínio não encaminháveis, em seguida, é provável que o UPN dos utilizadores têm também não encaminháveis sufixos. Por exemplo, se estiver a utilizar em Contoso, ligação do Azure AD sugere a utilizar definições personalizadas em vez de utilizar as definições de express. Utilizar definições personalizadas, é capaz de especificar o atributo que deve ser utilizado como UPN para iniciar sessão no Azure depois dos utilizadores são sincronizados para Azure AD.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
