<properties
    pageTitle="Sincronização do Azure AD Connect: Noções sobre a configuração predefinida | Microsoft Azure"
    description="Este artigo descreve a configuração de predefinida na ligação do Azure AD sync."
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
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronização do Azure AD Connect: Noções sobre a configuração predefinida
Este artigo explica as regras de configuração da out of box. -Documentos as regras e como estas regras afeta a configuração. -Lo também orienta a configuração predefinida de ligação do Azure AD sync. O objetivo é que o leitor de compreende como o modelo de configuração, com o nome de aprovisionamento declarativos, está a trabalhar num exemplo real. Este artigo assume que já tem instalado e configurar a ligação do Azure AD sync utilizando o Assistente de instalação.

Para compreender os detalhes do modelo de configuração, leia [Noções sobre declarativa aprovisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regras de caixa de saída no local para Azure AD
As expressões seguintes podem ser encontradas na configuração da out of box.

### <a name="user-out-of-box-rules"></a>Regras de fora da caixa de utilizador
Estas regras também se aplicam ao tipo de objecto iNetOrgPerson.

Um objeto de utilizador deve satisfazer o seguinte procedimento para ser sincronizado:

- Tem de ter um sourceAnchor.
- Depois de ter sido criado no objeto no Azure AD, não é possível alterar sourceAnchor. Se o valor for alterado no local, o objeto deixa de sincronizar até que seja alterado o sourceAnchor novamente para o seu valor anterior.
- Tem ter o atributo accountEnabled (userAccountControl) preenchido. Com no local Active Directory, este atributo está sempre presente e povoada.

Os seguintes objectos de utilizador são **sincronizadas no Azure AD** :

- `IsPresent([isCriticalSystemObject])`. Certifique-se de muitos objectos out of box no Active Directory, tal como a conta de administrador incorporada, não estão sincronizados.
- `IsPresent([sAMAccountName]) = False`. Certifique-se de que os objetos de utilizador com nenhum atributo sAMAccountName não estão sincronizados. Neste caso apenas praticamente seria ocorrer num domínio a partir de NT4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não são sincronizados a conta de serviço utilizada pelo ligação do Azure AD sync e as suas versões anteriores.
- Não são sincronizados contas do Exchange que não irá funcionar no Exchange Online.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- Não são sincronizados objetos que não irá funcionar no Exchange Online.
`CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
Esta máscara de bits (& H21C07000) seriam filtrarem os objectos seguintes:
    - Pastas públicas com capacidade de correio
    - Caixa de correio de atendedor automático de sistema
    - Caixa de correio de caixa de correio da base de dados (caixa de correio do sistema)
    - Grupo de segurança universal (não seria aplicam-se para um utilizador, mas é apresentar por razões de legado)
    - Grupo Universal não (não iria aplicam-se para um utilizador, mas é apresentar por razões de legado)
    - Plano de caixa de correio
    - Caixa de correio de deteção
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não são sincronizados quaisquer objetos de vítima de replicação.

As seguintes regras de atributo aplicam-se:

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. O atributo sourceAnchor não é contribuiu com a partir de uma caixa de correio ligada. É considerada que se sido encontrou uma caixa de correio ligada a conta real está associada mais tarde.
- Exchange relacionado com atributos são sincronizados apenas se o atributo **mailNickName** tenha um valor.
- Quando existirem várias florestas, atributos são consumidos pela seguinte ordem:
    1. Atributos relacionados com a sessão (por exemplo userPrincipalName) são contribuiu com da floresta com uma conta ativada.
    2. Atributos que podem ser encontrados na GAL Exchange (lista de endereços Global) são contribuiu com a partir da floresta com uma caixa de correio do Exchange.
    3. Se a caixa de correio de não pode ser encontrada, em seguida, estes atributos podem ter qualquer floresta.
    4. Exchange relacionados com atributos (técnicos atributos não está visíveis na lista de endereços global) são contributos da floresta onde `mailNickname ISNOTNULL`.
    5. Se existirem várias florestas que iria satisfaçam uma destas regras, em seguida, a ordem de criação (data/hora) dos conectores (florestas) é utilizada para determinar qual floresta contribui os atributos.

### <a name="contact-out-of-box-rules"></a>Contactos out of box regras
Um objeto de contacto deve satisfazer o seguinte procedimento para ser sincronizado:

- O contacto tem de ser com capacidade de correio. É verificado com as seguintes regras:
    - `IsPresent([proxyAddresses]) = True)`. Atributo proxyAddresses tem de ser preenchido.
    - Pode encontrar um endereço de e-mail principal no atributo proxyAddresses ou o atributo de correio. A presença de um @ é utilizado para confirmar que o conteúdo é um endereço de e-mail. Uma destas dois regras deve ser avaliada como True.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe uma entrada com "SMTP:" e se existir, pode um @ encontrado na cadeia de?
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. É o atributo correio povoada e se for, pode um @ encontrado na cadeia de?

Os seguintes objectos de contactos são **sincronizadas no Azure AD** :

- `IsPresent([isCriticalSystemObject])`. Certifique-se de que não existem objectos contactos marcados como críticos são sincronizados. Não devem ser qualquer um com uma configuração predefinida.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Estes objectos não iria funcionar no Exchange Online.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não são sincronizados quaisquer objetos de vítima de replicação.

### <a name="group-out-of-box-rules"></a>Regras de fora da caixa de grupo
Um objeto do grupo satisfazer o seguinte procedimento para ser sincronizado:

- Tem de ter menos do que 50.000 membros. Contagem deste é o número de membros do grupo no local.
    - Se tiver mais membros antes de sincronização ser iniciado pela primeira vez, o grupo não está sincronizado.
    - Se o número de Membros aumentar a partir do quando foi inicialmente criada, em seguida, quando atingir 50.000 membros deixa de sincronizar até a contagem de associação é inferior a 50000 novamente.
    - Nota: A contagem de 50.000 associação também é aplicada ao Azure AD. Não é possível sincronizar grupos com mais membros mesmo modificar ou remover esta regra.
- Se o grupo for um **Grupo de distribuição**, em seguida, tem também de ser com capacidade de correio. Consulte o artigo [regras de caixa de saída de contacto](#contact-out-of-box-rules) para esta regra é aplicada.

Os seguintes objectos de grupo são **sincronizadas no Azure AD** :

- `IsPresent([isCriticalSystemObject])`. Certifique-se de muitos objectos out of box no Active Directory, tal como o grupo de administradores incorporados, não estão sincronizados.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo legado utilizado pelo DirSync.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de funções.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não são sincronizados quaisquer objetos de vítima de replicação.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regras de out of box ForeignSecurityPrincipal
FSP é associadas a "qualquer" (\*) objeto na metaverse. Na realidade, este associação acontece apenas para utilizadores e grupos de segurança. Esta configuração assegura que floresta cruzada associações são resolvidas e são representadas corretamente no Azure AD.

### <a name="computer-out-of-box-rules"></a>Regras de out of box do computador
Um objeto de computador deve satisfazer o seguinte procedimento para ser sincronizado:

- `userCertificate ISNOTNULL`. Apenas os computadores Windows 10 povoar este atributo. Todos os objetos de computador com um valor neste atributo são sincronizados.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Noções sobre o cenário de regras de caixa de saída
Neste exemplo, estamos a utilizar uma implementação com uma conta floresta (A), uma floresta de recursos (R) e um diretório do Azure AD.

![Imagem com descrição de cenário](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Nesta configuração, é considerada que existe uma conta ativada na floresta de conta e uma conta desativada na floresta de recursos com uma caixa de correio ligada.

Nosso objetivo com a configuração predefinida é:

- Atributos relacionados com início de sessão no são sincronizados a partir da floresta com a conta activada.
- Atributos que podem encontrar na lista de endereços global (lista de endereços Global) são sincronizados a partir da floresta com a caixa de correio. Se a caixa de correio de não pode ser encontrada, é utilizada qualquer outra floresta.
- Se uma caixa de correio ligada for encontrada, a conta ligada activada tem encontrou para o objeto a serem exportadas para Azure AD.

### <a name="synchronization-rule-editor"></a>Editor de regras de sincronização
A configuração possam ser visualizada e alterada com a ferramenta de sincronização regras Editor (SRE) e um atalho para o mesmo pode ser encontrado no menu Iniciar.

![Editor de regras de ícone](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

O SRE é uma ferramenta de kit de recursos e é instalado com a ligação do Azure AD sync. Para poder iniciá-lo, tem de ser um membro do grupo ADSyncAdmins. Quando é iniciado, verá algo parecido com:

![Regras de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Neste painel, vê todas as regras de sincronização criado para a configuração. Cada linha da tabela é uma regra de sincronização. À esquerda em tipos de regra, são listados os dois tipos diferentes: entrada e saída. Entrada e saída é a partir da vista do metaverse. Principalmente passar para o foco em regras de entrada nesta descrição geral. A lista real de regras de sincronização depende do esquema de detectado no AD. Na imagem acima, floresta de conta (fabrikamonline.com) não tem qualquer serviços, como o Exchange e o Lync, e regras de sincronização sem ter sido criadas para estes serviços. No entanto, na floresta de recursos (res.fabrikamonline.com) localizar regras de sincronização para estes serviços. O conteúdo das regras é diferente consoante a versão detectada. Por exemplo, numa implementação com o Exchange 2013 existem mais fluxos de atributo configurados que no Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regra de sincronização
Uma regra de sincronização é um objeto de configuração com um conjunto de atributos a fluir quando uma condição é cumprida. Também é utilizado para descrever como um objeto num espaço de conexão está relacionada com a um objeto no metaverse, conhecida como **participar** ou **corresponder**. As regras de sincronização ter um valor de precedência que indica como se relacionam umas às outras. Uma regra de sincronização com um valor numérico inferior tem uma precedência mais elevada e num conflito de fluxo de atributo, precedência wins a resolução de conflitos.

Por exemplo, observe a regra de sincronização **na partir do AD – utilizador AccountEnabled**. Marcar o SRE esta linha e selecione **Editar**.

Uma vez que esta regra é uma regra de caixa de saída, receber um aviso quando abre a regra. Não deve efetuar quaisquer [alterações para fora da caixa regras](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), para que lhe for perguntado quais a operação. Neste caso, apenas pretende ver a regra. Selecione **não**.

![Sincronização de aviso de regras](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Uma regra de sincronização tem quatro secções de configuração: descrição, controlo do âmbito filtrar, regras de associação e transformações.

#### <a name="description"></a>Descrição
A primeira secção fornece informações básicas, tais como um nome e descrição.

![Descrição do separador editor de regras sincronizado ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Pode também encontrar informações sobre o qual é o sistema ligado esta regra está relacionada com a, que tipo no sistema ligado que aplica-se ao e o tipo de objeto de metaverse do objeto. O tipo de objecto metaverse é sempre pessoa independentemente quando o tipo de objeto de origem for um utilizador, iNetOrgPerson ou contacto. O tipo de objecto metaverse nunca deve ser alterados por é criado como um tipo de genérico. O tipo de ligação pode ser definido para participar, StickyJoin ou aprovisionar. Esta definição funciona em conjunto com a secção participar regras e é abrangida mais tarde.

Também pode ver que esta regra de sincronização é utilizada para a sincronização de palavra-passe. Se for um utilizador no âmbito para esta regra de sincronização, a palavra-passe é sincronizada a partir no local à nuvem (partindo do princípio de que ativou a funcionalidade de sincronização de palavra-passe).

#### <a name="scoping-filter"></a>Controlo do âmbito filtro
A secção filtro de controlo do âmbito é utilizada para configurar quando uma regra de sincronização deve ser aplicada. Uma vez que o nome da regra a sincronização está a observar indica deve ser aplicada apenas para utilizadores ativados, o âmbito está configurado para o atributo de AD **userAccountControl** não deve ter o bit 2 definir. Quando o motor de sincronização localiza um utilizador no AD, aplica esta regra de sincronização quando **userAccountControl** está definido para o valor decimal 512 (utilizador normal ativado). Não aplicar a regra quando o utilizador tem **userAccountControl** definido para 514 (utilizador normal desativado).

![Controlo do âmbito separador no editor de regras de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

O filtro âmbito tem grupos e cláusulas que podem ser aninhadas. Tem de ser cumpridas cláusulas ALL dentro de um grupo para uma regra de sincronização aplicar. Quando são definidos vários grupos, em seguida, pelo menos um grupo tem de ser cumprido para a regra seja aplicável. Ou seja, um ou lógico é avaliado entre os grupos e uma lógica e é avaliado dentro de um grupo. Um exemplo desta configuração pode ser encontrado na regra de sincronização saída **fora para AAD – grupo participar**. Existem vários grupos de filtro de sincronização, por exemplo, uma para grupos de segurança (`securityEnabled EQUAL True`) e outro para grupos de distribuição (`securityEnabled EQUAL False`).

![Controlo do âmbito separador no editor de regras de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Esta regra é utilizada para definir os grupos que devem ser aprovisionados para Azure AD. Grupos de distribuição têm de ser correio ativado a ser sincronizada com o Azure AD, mas para grupos de segurança não é necessária uma mensagem de e-mail.

#### <a name="join-rules"></a>Participar em regras
A terceira secção é utilizada para configurar como objetos no espaço conexão se relacionam com a objetos a metaverse. A regra que tenha visualizou anterior não tem qualquer configuração para participar regras, por isso, em vez disso, passar para ver **na partir do AD – utilizador aderir**.

![Participar no separador regras da pasta no editor de regra de sincronização ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

O conteúdo da regra associação depende da opção correspondente seleccionada no Assistente de instalação. Para uma regra de entrada, a avaliação começa com um objeto no espaço de conexão de origem e cada grupo, nas regras de associação é avaliado numa sequência. Se um objeto de origem é avaliado para corresponder exatamente um objeto no metaverse utilizando um das regras de associação, os objetos são associados. Se todas as regras tenham sido avaliadas e não haja nenhuma correspondência, em seguida, é utilizado o tipo de ligação na página de descrição. Se esta configuração estiver definida para **aprovisionar**, é criado um novo objeto de destino, o metaverse. Aprovisionar um novo objeto para o metaverse é também conhecido como **projeto** um objeto para o metaverse.

As regras de associação só são avaliadas uma vez. Quando são associadas um objeto de espaço de conexão e um objeto de metaverse, permanecerão associadas desde o âmbito da regra sincronização seja continuam a ser satisfeito.

Ao avaliar regras de sincronização, tem de ser apenas uma regra de sincronização com regras de associação definidas no âmbito. Se forem encontradas múltiplas regras de sincronização com regras de associação para um objeto, é devolvido um erro. Por este motivo, é a melhor prática ter apenas uma regra de sincronização com associação definida quando estão várias regras de sincronização no âmbito de um objeto. Na configuração out of box para ligação do Azure AD sync, estas regras podem ser encontrado verificando o nome e localizar aqueles com o word **participar** no final do nome. Uma regra de sincronização sem todas as regras de associação definidas aplica-se os fluxos de atributo quando outra regra de sincronização associado a objetos em conjunto ou aprovisionado um novo objeto de destino.

Se ver imagem acima, pode ver que a regra está a tentar participar **objectSID** com **msExchMasterAccountSid** (Exchange) e o **Atributo msRTCSIP-OriginatorSid** (Lync), que é o podemos esperar na topologia de floresta recurso de conta. Localize a mesma regra em todas as florestas. Partem do princípio de é que cada floresta dever floresta uma conta ou recurso. Esta configuração também funciona se tiver contas que live numa única floresta e não tem de ser associadas.

#### <a name="transformations"></a>Transformações
A secção transformação define todos os fluxos de atributo aplicam ao objeto destino quando os objetos são associados e o filtro do âmbito é cumprido. Ir novamente a **na partir do AD – utilizador AccountEnabled** regra de sincronização, encontrar as transformações seguintes:

![Transformações separador editor de regras sincronizado ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Para colocar esta configuração no contexto, de uma implementação floresta conta recurso, espera-se para encontrar uma conta activada em floresta de conta e uma conta desactivada na floresta de recursos com as definições do Exchange e o Lync. A regra de sincronização que esteja a visualizar contém os atributos necessários para o início de sessão e estes atributos devem fluir da floresta onde não existe uma conta ativada. Todos os fluxos de atributo são colocados em conjunto numa regra de sincronização.

Uma transformação pode ter tipos diferentes: constante, Direct e expressão.

- Obter um caudal constante sempre os fluxos de um valor. Neste caso, sempre define o valor **Verdadeiro** a metaverse atributo denominada **accountEnabled**.
- Um fluxo de direto flua sempre o valor do atributo na origem para o atributo de destino como-é.
- O tipo de fluxo de terceiro é expressão e permite para configurações mais avançadas.

A linguagem da expressão é VBA (Visual Basic for Applications), pelo que as pessoas com experiência do Microsoft Office ou VBScript irá reconhecer o formato. Atributos estão entre parênteses Retos, [NomeAtributo]. Nomes de atributo e nomes de função estão entre maiúsculas e minúsculas, mas o Editor de regras de sincronização avalia as expressões e fornecer um aviso se a expressão não for válida. Todas as expressões são representadas numa única linha com funções aninhadas. Para mostrar o poder do idioma de configuração, eis o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Consulte o artigo [Compreender as expressões de aprovisionamento declarativos](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) para mais informações sobre a linguagem da expressão para fluxos de atributo.

### <a name="precedence"></a>Precedência
Agora que tem visualizou algumas regras de sincronização do individuais, mas as regras de trabalham em conjunto com a configuração. Em alguns casos, um valor do atributo é contribuiu a partir de múltiplas regras de sincronização para o mesmo atributo de destino. Neste caso, a precedência de atributo é utilizada para determinar que atributo wins. Por exemplo, observe o atributo sourceAnchor. Este atributo é um atributo importante possam iniciar sessão no Azure AD. Pode encontrar um fluxo de atributo deste atributo nos duas regras de sincronização diferente, **na partir do AD – utilizador AccountEnabled** e **na partir do AD – utilizador comuns**. Devido a precedência de regras de sincronização, o atributo sourceAnchor é contribuiu com da floresta com uma conta activada pela primeira vez quando existem vários objetos de associadas ao objeto metaverse. Se existirem contas ativadas, em seguida, o motor de sincronização utiliza a regra de sincronização de capturas tudo **na partir do AD – utilizador comuns**. Esta configuração garante que mesmo para contas que estão desativadas, ainda existe um sourceAnchor.

![Regras de sincronização de entrada](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

A precedência de regras de sincronização é definida no grupos pelo Assistente de instalação. Todas as regras de num grupo têm o mesmo nome, mas estiverem ligados à directórios ligados diferentes. O Assistente de instalação dá a regra **na partir do AD – utilizador aderir** mais altos precedência e itera sobre todos os ligado directórios AD. Em seguida, continua a com a seguintes grupos de regras numa ordem predefinida. Dentro de um grupo, as regras são adicionadas pela ordem que as conexões foram adicionadas no assistente. Se for adicionado outro conector através do assistente, as regras de sincronização são reordenadas e regras do novo conector são inseridas última em cada grupo.

### <a name="putting-it-all-together"></a>Reunir tudo
Vamos agora sabe suficiente sobre regras de sincronização do possam compreender como a configuração funciona com as regras de sincronização diferente. Caso veja um utilizador e os atributos que são contribuição para o metaverse, as regras são aplicadas pela seguinte ordem:

Nome | Comentário
:------------- | :-------------
Na partir do AD – associação de utilizador | Regra de participação objetos de espaço de conector com metaverse.
Na partir do AD – com capacidade de conta | Atributos necessários para o início de sessão no Azure AD e no Office 365. Pretendemos estes atributos da conta de ativada.
Na partir do AD – comum de utilizador a partir do Exchange | Foram encontrados atributos na lista de endereços Global. Vamos assumir que a qualidade de dados é melhor na floresta onde tenham encontrámos caixa de correio do utilizador.
Na partir do AD – comum de utilizador | Foram encontrados atributos na lista de endereços Global. No caso de encontrámos uma caixa de correio, qualquer outro objeto associado pode contribuir o valor do atributo.
Na partir do AD – Exchange do utilizador | Existe apenas se detetou do Exchange. Fluxos de todos os atributos do Exchange de infraestrutura.
Na partir do AD – Lync de utilizador | Existe apenas se detetou do Lync. Fluxos de todos os atributos do Lync de infraestrutura.

## <a name="next-steps"></a>Próximos passos

- Leia mais sobre o modelo de configuração no [Noções sobre declarativa aprovisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Leia mais sobre a linguagem da expressão em [Expressões de aprovisionamento declarativa Noções sobre](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Continue a ler como a configuração de out of box funciona no [Noções sobre utilizadores e contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md)
- Veja como efetuar uma alteração prática utilizar aprovisionamento declarativos no [artigo como efetuar uma alteração para a configuração predefinida](active-directory-aadconnectsync-change-the-configuration.md).

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
