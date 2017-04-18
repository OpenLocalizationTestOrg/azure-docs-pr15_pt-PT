<properties
    pageTitle="Sincronização do Azure AD Connect: Noções sobre declarativa aprovisionamento | Microsoft Azure"
    description="Explica o modelo de configuração aprovisionamento declarativa na ligação do Azure AD."
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
    ms.date="08/29/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronização do Azure AD Connect: Noções sobre declarativos aprovisionamento
Este tópico explica o modelo de configuração no ligação do Azure AD. O modelo de chama-se de aprovisionamento declarativa e permite-lhe efetuar uma alteração com facilidade de configuração. Muitas coisas descritas neste tópico são avançadas e não é necessário para a maioria dos cenários de cliente.

## <a name="overview"></a>Descrição geral
Aprovisionamento declarativos está a processar objetos provenientes de um diretório de ligada de origem e determina a forma como o objeto e os atributos devem ser transformados a partir de uma origem para um destino. Um objeto é processado em pipeline de sincronização e pipeline de é o mesmo para regras de entrada e saídas. Uma regra de entrada é a partir de um espaço de conexão para o metaverse e uma regra de saída é a partir do metaverse para um espaço de conexão.

![Pipeline de sincronização](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

A tubagem tem várias módulos diferentes. Cada um deles é responsável por um conceito da sincronização de objeto.

![Pipeline de sincronização](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

- Origem, o objeto de origem
- [Âmbito](#scope), localiza todas as regras de sincronização que estão no âmbito
- [Participar](#join), determina de relação entre o espaço de conexão e metaverse
- [Transformar](#transform), calcula como atributos devem ser transformados e fluxo
- [A precedência](#precedence), resolve em conflito contribuições de atributo
- Destino, o objeto de destino

## <a name="scope"></a>Âmbito
O módulo do âmbito é avaliação de um objeto e determina as regras que estão no âmbito e deverão ser incluídas no processamento. Consoante os valores de atributos no objeto, regras de sincronização diferente são avaliadas para estar no âmbito. Por exemplo, um utilizador desativado com sem caixa de correio do Exchange têm regras diferentes do que um utilizador ativado com uma caixa de correio.  
![Âmbito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

O âmbito é definido como grupos e cláusulas. Cláusulas estão dentro de um grupo. Um e lógico é utilizado entre todos os cláusulas num grupo. Por exemplo, (departamento = TI e país = Dinamarca). Um ou lógico é utilizado entre grupos.

![Âmbito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
O âmbito nesta imagem deve ser lido como (departamento = TI e país = Dinamarca) ou (país = Suécia). Se o grupo de 1 ou grupo 2 é avaliado como true, em seguida, a regra é no âmbito.

O módulo do âmbito suporta as seguintes operações.

Operação | Descrição
--- | ---
IGUAL, NOTEQUAL | Comparação de cadeia que é avaliada se valor for igual ao valor no atributo. Para atributos de múltiplos valores, consulte o artigo ISIN e ISNOTIN.
LESSTHAN, LESSTHAN_OR_EQUAL | Comparar uma cadeia que é avaliada se valor for inferior a do valor no atributo.
CONTÉM, NOTCONTAINS | Comparação de cadeia que é avaliada se o valor pode ser encontrado algures dentro de valor no atributo.
STARTSWITH, NOTSTARTSWITH | Comparação de cadeia que é avaliada se valor for no início do valor no atributo.
ENDSWITH, NOTENDSWITH | Comparação de cadeia que é avaliada se valor for no final do valor no atributo.
GREATERTHAN, GREATERTHAN_OR_EQUAL | Comparação de cadeia que é avaliada se valor for maior que o valor no atributo.
ÉNULO, ISNOTNULL | Avalia se o atributo está ausente do objeto. Se o atributo não apresentar e, consequentemente, nulo, em seguida, a regra é no âmbito.
ISIN, ISNOTIN | Avalia se o valor for presente no atributo definido. Esta operação é a variação de igual e NOTEQUAL múltiplos valor. O atributo é suposto seja um atributo múltiplos e se o valor pode ser encontrado em qualquer um dos valores de atributo, em seguida, a regra está no âmbito.
ISBITSET, ISNOTBITSET | Avalia se um determinado bit é definido. Por exemplo, pode ser utilizado para avaliar os bits na userAccountControl para ver se um utilizador está ativado ou desativado.
ISMEMBEROF, ISNOTMEMBEROF | O valor deve conter um DN a um grupo no espaço de conexão. Se o objeto é um membro do grupo especificado, a regra é no âmbito.

## <a name="join"></a>Associação
O módulo participar no pipeline de sincronização é responsável por localizar a relação entre o objeto na origem e um objeto de destino. Numa regra de entrada, esta relação seria um objeto num espaço de conexão localizar uma relação a um objeto de metaverse.  
![Participar entre cs e mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
O objetivo é ver que se existir um objeto que já existe na metaverse, criado por outro conector, devem ser associado com. Por exemplo, numa floresta conta recurso o utilizador a partir do floresta de conta deverá ser associado com o utilizador a partir do floresta de recursos.

Associações externas são utilizadas principalmente em regras de entrada para juntar ao mesmo objeto metaverse objetos de espaço de conexão.

Associações são definidas como um ou mais grupos. Dentro de um grupo, tem de cláusulas. Um e lógico é utilizado entre todos os cláusulas num grupo. Um ou lógico é utilizado entre grupos. Os grupos são processados na ordem de cima para baixo. Quando um grupo tem encontrado exatamente uma correspondência com um objeto de destino, em seguida, são avaliadas outras regras de associação. Se zero ou mais do que um objeto for encontrado, processamento continua para o grupo seguinte das regras. Por este motivo, as regras devem ser criada pela ordem primeiro mais explícito e mais nítidos no final.  
![Aderir a definição](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Associações nesta imagem são processadas de cima para baixo. Vê pela primeira vez pipeline de sincronização se existe uma correspondência com o campo IDdeEmpregado. Caso contrário, a segunda regra vê se o nome da conta pode ser utilizado para juntar os objetos. Se isso não for uma correspondência quer, a regra de terceira e final é uma correspondência mais nítida utilizando o nome do utilizador.

Se tiverem sido avaliadas todas as regras de associação e exatamente não existe uma correspondência, é utilizado o **Tipo de ligação** na página de **Descrição** . Se esta opção estiver definida para **aprovisionar**, em seguida, é criado um novo objeto de destino.  
![Aprovisionar ou uma associação](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Um objeto só deve ter uma regra de sincronização única com regras de associação no âmbito. Se existirem várias regras de sincronização, onde a associação é definida, ocorre um erro. A precedência não é utilizada para resolver conflitos de associação. Um objeto tem de ter uma regra de associação no âmbito atributos fluxo com a direcção de entrada/saída mesmo. Se precisar de atributos do fluxo entrada e saída ao mesmo objeto, tem de ter uma entrada e uma regra de saída de sincronização com associação.

Associação saída tem um comportamento especial ao tentar aprovisionar um objeto para um espaço de conector de destino. O atributo DN é utilizado para uma associação inversa tente em primeiro lugar. Se já existe um objeto no espaço de conexão destino com o mesmo DN, os objetos são associados.

Módulo de associação é avaliado apenas uma vez quando uma nova regra de sincronização entra em âmbito. Quando um objeto aderiu, não é retirar mesmo se os critérios de associação já não é cumprida. Se pretender terminar um objeto, tem de aceder a regra de sincronização que aderiu os objetos fora do âmbito.

### <a name="metaverse-delete"></a>Eliminar Metaverse
Um objeto de metaverse permanece tal como longo como existe uma regra de sincronização no âmbito com o **Tipo de ligação** definida como **aprovisionar** ou **StickyJoin**. Um StickyJoin é utilizado quando uma conexão não é permitida para aprovisionar um novo objeto para o metaverse, mas quando foi associado, devem ser eliminado na origem de antes do objecto metaverse é eliminado.

Quando é eliminado um objeto de metaverse, todos os objetos associados a uma regra de saída sincronização marcada para **aprovisionar** estão marcados para um eliminar.

## <a name="transformations"></a>Transformações
As transformações são utilizadas para definir como atributos devem fluxo da origem para o destino. Os fluxos de podem ter um dos seguintes **tipos de fluxo**: diretos, expressão ou uma constante. Um valor do atributo como os fluxos de um fluxo de direto,-é com sem transformações adicionais. Um valor da constante define o valor especificado. Uma expressão utiliza a linguagem da expressão aprovisionamento declarativa para expressar como deve ser a transformação. Os detalhes da linguagem da expressão podem ser encontrados no tópico das [Noções sobre declarativos aprovisionamento linguagem da expressão](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Aprovisionar ou uma associação](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

Define a caixa de verificação **aplicar uma vez** que o atributo só deverá ser definido quando o objeto é criado inicialmente. Por exemplo, esta configuração pode ser utilizada para definir uma palavra-passe inicial para um novo objeto de utilizador.

### <a name="merging-attribute-values"></a>Intercalar valores de atributo
Os fluxos de atributo não existe uma definição para determinar se deverão ser intercalados atributos de múltiplos valores a partir de várias conexões diferentes. O valor predefinido é de **atualização**, que indica que a regra de sincronização com a precedência mais alta deve ganhar.

![Tipos de impressão em série](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Também existe **Intercalar** e **MergeCaseInsensitive**. Estas opções permitem-lhe intercalar valores de origens diferentes. Por exemplo, pode ser utilizado para intercalar o atributo membro ou proxyAddresses a partir de várias florestas diferentes. Quando utilizar esta opção, todas as regras de sincronização no âmbito de um objeto tem de utilizar o mesmo tipo de impressão em série. Não é possível definir **atualização** a partir de um conector e a **impressão em série** a partir de outra. Se tentar, receberá um erro.

A diferença entre a **impressão em série** e **MergeCaseInsensitive** é como processam os valores dos atributos duplicados. O motor de sincronização assegura que valores duplicados não são inseridos o atributo de destino. Com **MergeCaseInsensitive**, duplicar valores com apenas uma diferença caso não irão estar presentes. Por exemplo, deverá não ver ambos "SMTP:bob@contoso.com" e "smtp:bob@contoso.com" no atributo de destino. **Impressão em série** é apenas olhar para os valores exatos e vários valores onde não existe apenas uma diferença de maiúsculas/minúsculas podem estar presentes.

A opção de **Substituir** é igual a **atualização**, mas não é utilizada.

### <a name="control-the-attribute-flow-process"></a>Controlar o processo de fluxo de atributo
Quando são configuradas múltiplas regras de entrada de sincronização de contribuição para o mesmo atributo metaverse, precedência é utilizada para determinar o vencedor. A regra de sincronização com a precedência mais alta (valor numérico mais baixo) vai para o valor de contribuir. O mesmo acontece para regras de saída. A sincronizar com o mais alto wins da precedência de regras e contribuir o valor para o directório ligado.

Em alguns casos, em vez de contribuir um valor, a regra de sincronização deve determinar como devem se comportam os outras regras. Existem algumas literais especiais utilizados para este incidente.

Para regras de sincronização de entrada, a literal **Nulo** pode ser utilizados para indicar que o fluxo não tem um valor para contribuir. Outra regra com precedência inferior pode contribuir um valor. Se nenhuma regra contribuiu com um valor, em seguida, o atributo metaverse é removido. Para uma regra de saída, se **Nulo** é o valor final depois de terem sido processadas todas as regras de sincronização, em seguida, o valor é removido no diretório ligado.

O literal **AuthoritativeNull** é semelhante à **Nulo** mas com a diferença que sem as regras de prioridade baixa podem contribuir um valor.

Um fluxo de atributo também pode utilizar **IgnoreThisFlow**. É semelhante ao NULL no sentido em que indica que não há nada para contribuir. A diferença é que não remove um valor já existente de destino. É como o fluxo de atributo que nunca tenha sido aí.

Eis um exemplo:

No *fora para AD - implementação híbrida do Exchange do utilizador* pode ser encontrado o fluxo seguinte:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Esta expressão deve ser lido como: se a caixa de correio de utilizador está localizada no Azure AD, em seguida, um fluxo do atributo a partir do Azure AD para AD. Caso contrário, não um fluxo de nada para o Active Directory. Neste caso,-seria tenha o valor existente em AD.

### <a name="importedvalue"></a>ImportedValue
A função ImportedValue é diferente de todas as outras funções, uma vez que o nome do atributo deve ser colocada entre aspas em vez de entre parênteses Retos:  
`ImportedValue("proxyAddresses")`.

Normalmente, durante a sincronização um atributo utiliza o valor de esperado, mesmo se ainda não foram exportado ainda ou se foi recebido um erro durante a exportação ("parte superior da torre"). Uma sincronização entrada assume que um atributo que ainda não atingido eventualmente um diretório ligado atinge-lo. Em alguns casos, é importante sincronizar apenas um valor que tenha sido confirmado pelo diretório ligado ("holograma e delta importar Torre").

Um exemplo desta função pode ser encontrado na regra de sincronização fora da caixa *na partir do AD – comuns de utilizador a partir do Exchange*. No Exchange híbrido, o valor acrescentado pelo Exchange online apenas deve ser sincronizado quando confirmado de que o valor foi exportado com êxito:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedência
Quando tentarem várias regras de sincronização contribuir o mesmo valor do atributo para o destino, é utilizado o valor de prioridade para determinar o vencedor. A regra com precedência mais alta, mais baixos valor numérico, vai para o atributo um conflito de contribuir.

![Tipos de impressão em série](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Esta ordenação pode ser utilizado para definir mais precisos de atributo de fluxos para um pequeno subconjunto de objetos. Por exemplo, a out-de-caixa-regras Certifique-se de que os atributos de uma conta ativado (**AccountEnabled de utilizador**) têm a precedência de outras contas.

A precedência pode ser definida entre conexões. Conectores que permite com dados melhor para contribuir valores pela primeira vez.

### <a name="multiple-objects-from-the-same-connector-space"></a>Vários objetos a partir do mesmo espaço de conexão
Se tiver vários objectos no mesmo espaço de conexão associado ao mesmo objeto metaverse, tem de ser ajustada precedência. Existam vários objetos no âmbito da mesma regra de sincronização, em seguida, o motor de sincronização não é possível determinar a precedência. É ambígua que objeto de origem deve contribuir o valor para o metaverse. Esta configuração é comunicada como ambíguos mesmo se os atributos na origem de tem o mesmo valor.  
![Vários objetos associadas ao mesmo objeto mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Neste cenário, terá de alterar o âmbito das regras de sincronização, para que os objetos de origem tem regras de sincronização diferente no âmbito. Que permite-lhe definir a precedência de diferentes.  
![Vários objetos associadas ao mesmo objeto mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Próximos passos

- Leia mais sobre a linguagem da expressão em [Expressões de aprovisionamento declarativa Noções sobre](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Consulte o artigo como declarativa aprovisionamento é utilizado out-de-box no [Noções sobre a configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md).
- Veja como efetuar uma alteração prática utilizar aprovisionamento declarativa no [artigo como efetuar uma alteração para a configuração predefinida](active-directory-aadconnectsync-change-the-configuration.md).
- Continue a ler como funcionam em conjunto os utilizadores e contactos no [Noções sobre utilizadores e os contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)

**Tópicos de referência**

- [Sincronização do Azure AD Connect: referência de funções](active-directory-aadconnectsync-functions-reference.md)
