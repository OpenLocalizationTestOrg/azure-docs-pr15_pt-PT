<properties
    pageTitle="Sincronização do Azure AD Connect: Noções sobre utilizadores e contactos | Microsoft Azure"
    description="Explica os utilizadores e contactos na ligação do Azure AD sync."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-users-and-contacts"></a>Sincronização do Azure AD Connect: Noções sobre utilizadores e contactos

Existem várias razões pelas diferentes por que motivo tem várias florestas do Active Directory e existem várias topologias de implementação diferente. Modelos de comuns incluem uma implementação conta recurso e GAL sync'ed florestas após uma avaliação de aquisições & aquisição. Mas, mesmo se existirem puros modelos, modelos de híbrido são comuns também. A configuração de predefinida na ligação do Azure AD sync não partem do pressuposto de qualquer modelo específico mas dependendo de como a correspondência de utilizador foi selecionado no guia de instalação, podem ser respeitados comportamentos diferentes.

Neste tópico, iremos de como a configuração predefinida comporta-se em determinadas topologias. Vamos através da configuração e o Editor de regras de sincronização podem ser utilizado para observe a configuração.

Existem algumas regras gerais que a configuração assume:

- Independentemente de por que ordem vamos importar a partir da origem directórios ativo, o resultado final deve estar sempre o mesmo.
- Uma conta do active sempre irão contribuir com informações de início de sessão, incluindo **userPrincipalName** e **sourceAnchor**.
- Uma conta desactivada contribuir userPrincipalName e sourceAnchor, a menos que seja uma caixa de correio ligada, se não existe nenhuma conta active a ser encontrado.
- Uma conta com uma caixa de correio ligada nunca será utilizada para userPrincipalName e sourceAnchor. É considerada que uma conta do active vai encontrar mais tarde.
- Poderá ser aprovisionado um objecto de contacto para Azure AD como um contacto ou como um utilizador. Não sabe realmente até que tenham sido processadas todos os florestas do Active Directory de origem.

## <a name="contacts"></a>Contactos

Está com dificuldades contactos que representa um utilizador numa floresta diferente é comuns após uma avaliação de aquisições & aquisição onde uma solução GALSync é pontes duas ou mais florestas do Exchange. O objecto do contacto é sempre participar a partir do espaço de conexão para metaverse utilizando o atributo de correio. Se já existe um objecto contacto ou utilizador com o mesmo endereço de correio, os objetos são associados em conjunto. Isto é configurado na regra **na partir do AD – contacto participar**. Também existe uma regra com o nome **na partir do AD – contacto comuns** com um fluxo de atributo para o metaverse atributo **sourceObjectType** com o constante **contacto**. Esta regra tem prioridade muito baixa-lo se qualquer objeto de utilizador é aderido ao mesmo objeto metaverse, em seguida, a regra **na partir do AD – utilizador comuns** irão contribuir com o valor de utilizador para este atributo. Com esta regra, este atributo terá o contacto não se aderiu nenhum utilizador e o valor utilizador se tiver sido encontrado, pelo menos, um utilizador.

Para aprovisionar um objeto para Azure AD, a regra de saída **fora para AAD – aderir a contactar** irá criar um objecto de contacto, se o atributo de metaverse **sourceObjectType** estiver definido para **contactar**. Se este atributo está definido para **utilizador**, em seguida, a regra **fora para AAD – utilizador aderir** irá criar um objeto de utilizador em vez disso.
É possível que um objeto é promovido a partir de contacto ao utilizador quando mais directórios ativo de origem são importados e sincronizados.

Por exemplo, numa topologia GALSync podemos irá encontrar objectos de contacto para todas as pessoas da segunda floresta quando vamos importar da primeira floresta. Isto irá fase novos objetos de contactos no AAD Connector. Quando é mais tarde, importar e sincronizar da segunda floresta, iremos localizar os utilizadores reais e associá-los aos objetos metaverse existente. Iremos, em seguida, elimine o objeto na AAD contacto e criar um novo objeto de utilizador em vez disso.

Se tiver uma topologia de onde os utilizadores e representado como contactos, certifique-se de que selecione para que correspondam aos utilizadores sobre o atributo de correio no guia de instalação. Se selecionar outra opção, em seguida, terá uma configuração dependentes ordem. Objectos de contacto sempre irão aderir no atributo correio, mas objetos de utilizador serão apenas na associação o atributo de correio se esta opção foi seleccionada no guia de instalação. Pode terminar, em seguida, para cima com dois objetos diferentes no metaverse com o mesmo atributo de correio se o objeto contacto foram importado antes de objeto do utilizador. Durante a exportação do Azure AD, vai ser devolvido um erro. Este comportamento é por predefinição e seria indicar dados danificados ou que a topologia de não foi identificada corretamente durante a instalação.

## <a name="disabled-accounts"></a>Contas desativadas

Contas desativadas são sincronizadas também para Azure AD. Contas desativadas são comuns para representar recursos no Exchange, por exemplo salas de conferência. A exceção consiste em utilizadores com uma caixa de correio ligada; tal como mencionado anteriormente, estes serão aprovisionar nunca uma conta do Azure AD.

Partem do princípio de é que se uma conta de utilizador desativado for encontrada, em seguida, podemos não irá encontrar outra conta active mais tarde e o objeto está aprovisionado para Azure AD com o userPrincipalName e sourceAnchor encontrados. No caso de outra conta active irão aderir ao mesmo objeto metaverse, em seguida, respetivas userPrincipalName e sourceAnchor serão utilizadas.

## <a name="changing-sourceanchor"></a>Alterar sourceAnchor

Quando um objeto foi exportado para Azure AD, em seguida, não é permitida para alterar o sourceAnchor deixem. Quando o objeto foi exportado o metaverse atributo **cloudSourceAnchor** está definido com o valor de **sourceAnchor** aceite por Azure AD. Se **sourceAnchor** é alterado e não corresponder a **cloudSourceAnchor**, a regra de **saída para AAD – utilizador aderir** irá gerar o erro **sourceAnchor atributo foi alterado**. Neste caso, a configuração ou os dados tem de ser corrigidos para que o mesmo sourceAnchor faz o metaverse novamente antes do objeto pode ser sincronizado novamente.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure AD ligar sincronização: Opções de personalização de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
