<properties
    pageTitle="Sincronização do Azure AD Connect: como efetuar uma alteração para a configuração predefinida | Microsoft Azure"
    description="Explica como efetuar uma alteração para a configuração na ligação do Azure AD sync."
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
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Sincronização do Azure AD Connect: como efetuar uma alteração para a configuração predefinida
O objetivo deste tópico é guiá-lo como fazer alterações na configuração predefinido na ligação do Azure AD sync. Fornece passos para alguns cenários comuns. Com este conhecimentos, deverá conseguir efetuar algumas alterações simples a sua própria configuração com base nas suas próprias regras de negócio.

## <a name="synchronization-rules-editor"></a>Editor de regras de sincronização
O Editor de regras de sincronização é utilizado para ver e alterar a configuração predefinida. Pode encontrá-lo no Menu Iniciar no grupo de **ligação do Azure AD** .  
![Menu Iniciar com o Editor de regra de sincronização](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Quando o abrir, verá as regras de out of box predefinidas.

![Editor de regras de sincronização](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegar no editor de
Os menus pendentes na parte superior do editor de permitem-lhe localizar rapidamente uma regra específica. Por exemplo, se pretender ver as regras onde está incluído no atributo proxyAddresses, teria altere os-ou filtros pendentes para o seguinte:  
![SRE filtragem](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Para repor a filtragem e carregar uma configuração fresca, prima **F5** no teclado.

Para o canto superior direito, tem um botão **Adicionar nova regra**. Este botão é utilizado para criar o seu próprio regra personalizada.

Na parte inferior, tem de botões para agir numa regra de sincronização selecionado. **Editar** e **Eliminar** , faça o esperado. **Exportar** gera um script PowerShell para recriar a regra de sincronização. Este procedimento permite-lhe mover uma regra de sincronização a partir de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Criar a primeira regra personalizada
A alteração mais comuns é alterações para os fluxos de atributo. Os dados no seu diretório de origem não podem ser vistos Azure AD. No exemplo nesta secção, que pretende certificar-se de que o nome de indicado de um utilizador é sempre com maiúscula **inicial**.

### <a name="disable-the-scheduler"></a>Desativar o programador
O [scheduler](active-directory-aadconnectsync-feature-scheduler.md) é executada a cada 30 minutos por predefinição. Pretende certificar-se de que não estiver iniciar enquanto estiver a efectuar alterações e as suas novas regras de resolução de problemas. Para desativar temporariamente o programador, inicie o PowerShell e executar`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Desativar o programador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Criar a regra

1. Clique em **Adicionar nova regra**.
2. Na página **Descrição** , introduza o seguinte:  
![Regra de filtragem de entrada](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
    - Nome: Dar um nome descritivo-a regra.
    - Descrição: Algumas esclarecer para que outra pessoa pode compreender o que é a regra para.
    - Ligado sistema: O sistema o objeto pode ser encontrado na. Neste caso, podemos selecione o conector do Active Directory.
    - Tipo de objeto de sistema/Metaverse ligado: Selecione **utilizador** e a **pessoa** respetivamente.
    - Tipo de ligação: Altere este valor para **participar**.
    - Prioridade: Fornece um valor que seja exclusivo no sistema. Um valor numérico inferior indica precedência.
    - Etiqueta: Deixe em branco. Apenas fora da caixa regras da Microsoft devem ter esta caixa povoada com um valor.
3. Na página **Scoping filtro** , introduza **givenName ISNOTNULL**.  
![Regra de controlo do âmbito filtro de entrada](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
Esta secção é utilizada para definir os objectos que a regra deve ser aplicada. Se deixado em branco, a regra seria aplicar a todos os objetos de utilizador. Mas que deverá incluir salas de conferência, contas de serviço e outros objetos de utilizador que não sejam pessoas.
4. No **participar regras**, deixá-la vazia.
5. Na página **transformações** , altere o FlowType para **expressão**. Selecione o atributo de destino **givenName**e na origem introduza `PCase([givenName])`.
![Transformações de regra de entrada](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
O motor de sincronização é maiúsculas e minúsculas no nome da função e o nome do atributo. Se escrever alguma coisa mal, verá um aviso quando adiciona a regra. O editor de permite-lhe guardar e continuar, para que tem de voltar a abrir a regra e corrija a regra.
6. Clique em **Adicionar** para guardar a regra.

Nova regra personalizada deve estar visível com as regras de sincronização do sistema.

### <a name="verify-the-change"></a>Verifique se a alteração
Com esta alteração nova, que pretende Certifique-se de que está a funcionar como esperado e não é deitar erros. Consoante o número de objetos que tem, existem duas formas diferentes para efetuar este passo.

1. Executar uma sincronização completa em todos os objetos
2. Executar uma pré-visualizar e sincronização completa num único objeto

Inicie o **Serviço de sincronização** a partir do menu Iniciar. Os passos nesta secção estão todos esta ferramenta.

1. **Sincronizar completo em todos os objetos**  
Selecione **conectores** na parte superior. Identificar o conector que efetuou uma alteração na secção anterior, neste caso o Active Directory Domain Services, e selecione-o. Selecione **Executar** ações e selecione **Sincronização completa** e em **OK**.
![Sincronização completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
Agora, os objetos são atualizados na metaverse. Agora pretende observe o objeto na metaverse.

2. **Pré-visualizar e sincronização completa num único objeto**  
Selecione **conectores** na parte superior. Identificar o conector que efetuou uma alteração na secção anterior, neste caso o Active Directory Domain Services, e selecione-o. Selecione o **espaço de conector de pesquisa**. Utilize o âmbito para localizar um objeto que pretende utilizar para testar a alteração. Selecione o objeto e clique em **pré-visualização**. No ecrã novo, selecione **a consolidação pré-visualização**.
![Consolidar pré-visualização](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
A alteração agora está empenhada no metaverse.

**Veja o objeto na metaverse**  
Agora pretende escolher alguns exemplo objetos para se certificar de que o valor é esperado e que a regra aplicada. Selecione **Pesquisa Metaverse** situado na parte superior. Adicione qualquer filtro de que necessita para localizar os objetos relevantes. O resultado de pesquisa, abra a um objeto. Observe os valores de atributo e também no, verifique se a coluna de **Regras de sincronização** que a regra aplicada como esperada.  
![Pesquisa de Metaverse](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  
### <a name="enable-the-scheduler"></a>Ativar o programador
Se estiver tudo como esperado, pode ativar o agendador novamente. A partir do PowerShell, execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações de fluxo de atributo comuns
A secção anterior descrita como efetuar alterações a um fluxo de atributo. Nesta secção, são fornecidos mais alguns exemplos. Os passos para saber como criar a regra de sincronização é abreviada, mas pode encontrar os passos completos na secção anterior.

### <a name="use-another-attribute-than-the-default"></a>Utilizar outro atributo que o predefinido
Em Fabrikam, existe uma floresta onde alfabeto local é utilizado para nome próprio, apelido e nome a apresentar. A representação de caracteres latinos destes atributos pode ser encontrada nos atributos da extensão. Quando criar a lista de endereços global no Azure AD e que o Office 365, a organização convida estes atributos para ser utilizado em vez disso.

Com uma configuração predefinida, um objeto de floresta local este aspeto:  
![Atributo fluxo 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributo, faça o seguinte:

- Inicie o **Editor de regra de sincronização** a partir do menu Iniciar.
- Com a **entrada** ainda selecionado para a esquerda, clique no botão **Adicionar nova regra**.
- Um nome e descrição, dê à regra. Selecione o Active Directory no local e os tipos de objeto relevantes.  Em **Tipo de ligação**, selecione **participar**. Para a precedência, escolha um número que não é utilizado por outra regra. As regras de fora da caixa Iniciar com 100, para que o valor 50 pode ser utilizado neste exemplo.
![Atributo fluxo 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Deixe o âmbito vazia (ou seja, deve ser aplicada a todos os objetos de utilizador na floresta).
- Deixe as regras de associação vazia (ou seja, permitir que a regra de saída da caixa processar todas as associações).
- Na transformações, crie os fluxos de seguintes:  
![Atributo fluxo 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Clique em **Adicionar** para guardar a regra.
- Aceda ao **Gestor de serviços de sincronização**. Em **conexões**, selecione a conexão onde adicionámos a regra. Selecione **Executar**e **sincronização completa**. Uma sincronização completa volta a calcular todos os objetos utilizando as regras atuais.

Este é o resultado para o mesmo objecto com esta regra personalizado:  
![Atributo fluxo 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento de atributos
Cadeia atributos são por predefinição definida para não ser indexáveis e o comprimento máximo é 448 carateres. Se estiver a trabalhar com os atributos de cadeia que podem conter mais, em seguida, certifique-se incluir o seguinte procedimento no fluxo de atributo:  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Alterar o userPrincipalSuffix
O atributo userPrincipalName no Active Directory sempre não é conhecido pelos utilizadores e poderão não ser adequado como o ID de início de sessão no. Ligação do Azure AD Assistente de instalação de sincronização permite selecionar um atributo diferente, por exemplo de correio. Mas em alguns casos o atributo tem de ser calculado. Por exemplo, a empresa Contoso tem dois directórios Azure AD, para produção e para testar a ligação. Pretendem os utilizadores na sua inquilino de teste para utilizar outro sufixo no ID de início de sessão no.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Esta expressão toma tudo para a esquerda do primeiro @-sign (Word) e concatenar por uma cadeia de fixa.

### <a name="convert-a-multi-value-to-a-single-value"></a>Converter um valor com várias para um valor único
Alguns atributos no Active Directory são múltiplos valores no esquema de apesar parecem-única avaliado de utilizadores do Active Directory e computadores. Um exemplo é o atributo Descrição.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Esta expressão caso o atributo tem um valor, podemos tirar o primeiro item (Item) no atributo, remover espaços extra (cortar) e e, em seguida, manter os carateres em primeiro lugar 448 (esquerda) na cadeia de.

### <a name="do-not-flow-an-attribute"></a>Não um fluxo de um atributo
Para informações gerais sobre o cenário para esta secção, consulte [controlar o processo de fluxo de atributo](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Existem duas formas de fluxo não um atributo. O primeiro está disponível no Assistente de instalação e permite-lhe para [remover os atributos selecionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Esta opção funciona se nunca foram sincronizadas o atributo antes. No entanto, se ainda tiver iniciado para sincronizar este atributo e removê-la mais tarde com esta funcionalidade, em seguida, as posições do motor de sincronização gerir o atributo e os valores existentes permanecem no Azure AD.

Se pretender remover o valor de um atributo e certifique-se de não fluxo no futuro, precisa de criar uma regra personalizada em vez disso.

Em Fabrikam, podemos ter realizados que alguns dos atributos que podemos sincronizar na nuvem não devem ser aí. Pretendemos para se certificar de que estes atributos são removidos do Azure AD.  
![Extensão incorretas atributos](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Criar uma nova regra de sincronização de entrada e a preencher a descrição ![descrições](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Crie fluxos de atributo de tipo de **expressão** e com a origem de **AuthoritativeNull**. O literal **AuthoritativeNull** indica que o valor deve ser vazio a MV mesmo se uma regra de sincronização a precedência inferior tenta povoar o valor.
![Transformação extensão atributos](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Guarde a regra de sincronização. Iniciar o **Serviço de sincronização**, localize a conexão, selecione **Executar**e **Sincronização completa**. Este passo volta a calcular todos os fluxos de atributo.
- Certifique-se de que as alterações se destina estão prestes a ser exportados ao procurar o espaço de conexão.
![Eliminar faseada](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="next-steps"></a>Próximos passos

- Leia mais sobre o modelo de configuração no [Noções sobre declarativa aprovisionamento](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Leia mais sobre a linguagem da expressão em [Expressões de aprovisionamento declarativos Noções sobre](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
