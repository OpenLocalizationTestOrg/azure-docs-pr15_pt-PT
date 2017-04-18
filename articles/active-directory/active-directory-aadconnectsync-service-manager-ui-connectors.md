<properties
    pageTitle="Sincronização do Azure AD Connect: Gestor do serviço de sincronização IU | Microsoft Azure"
    description="Compreenda o separador de conectores no Gestor de serviço de sincronização para a ligação do Azure AD."
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
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Sincronização do Azure AD Connect: Gestor do serviço de sincronização

[Operações](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Estruturador de fluxos de Metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Pesquisa de Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

No separador de conectores é utilizado para gerir todos os sistemas que o motor de sincronização está ligado.

## <a name="connector-actions"></a>Ações de conexão

Ação | Comentário
--- | ---
Criar | Não utilize. Para estabelecer ligação ao florestas AD adicionais, utilize o Assistente de instalação.
Propriedades | Utilizado para o domínio e filtragem de or.
[Eliminar](#delete) | Utilizado para quer eliminar os dados no espaço conexão ou para eliminar ligação para uma floresta.
[Configurar perfis de executar](#configure-run-profiles) | Domínios filtragem, nada para configurar aqui. Pode utilizar esta ação para ver os perfis de executar já configurados.
Executar | Utilizado para iniciar uma única execução de um perfil.
Parar | Deixa de uma conexão atualmente em execução um perfil.
Conexão de exportação | Não utilize.
Importar conexão | Não utilize.
Conexão de actualização | Não utilize.
Atualizar o esquema | Atualiza o esquema em cache. É preferencial para utilizar a opção no Assistente de instalação em vez disso, desde que também atualizações sincronizar regras.
[Espaço de conector de pesquisa](#search-connector-space) | Utilizado para localizar objetos e a [seguir um objeto e os seus dados através do sistema](#follow-an-object-and-its-data-through-the-system).

### <a name="delete"></a>Eliminar
A ação de eliminar é utilizada para duas coisas diferentes.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

A opção **Eliminar o espaço de conexão só** remove todos os dados, mas manter a configuração.

A opção **Eliminar conexão e o espaço de conexão** remove os dados e a configuração. Esta opção é utilizada quando não pretende ligar a uma floresta deixem.

Ambas as opções de sincronizar todos os objetos e atualize os objetos metaverse. Esta ação é uma operação de execução longa.

### <a name="configure-run-profiles"></a>Configurar perfis de executar
Esta opção permite-lhe ver os perfis de executar configurados para uma conexão.

![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Espaço de conector de pesquisa
A ação de espaço do conector de pesquisa é útil para localizar objetos e resolução de problemas de dados.

![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Comece por selecionar um **âmbito**. Pode procurar com base nos dados (RDN, DN, âncora, sub-árvore), ou estado do objeto (todas as outras opções).  
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Se efetuar uma pesquisa de sub-árvore por exemplo, obtém todos os objetos no um OU.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) a partir desta grelha pode selecionar um objeto, selecione **Propriedades**e [segui-lo](#follow-an-object-and-its-data-through-the-system) a partir do espaço de conexão de origem, através de metaverse e para o espaço de conector de destino.

## <a name="follow-an-object-and-its-data-through-the-system"></a>Siga um objeto e os seus dados através do sistema
Quando estiver a resolver um problema com os dados, siga um objeto do espaço de conexão de origem, para o metaverse e para o destino espaço de conexão é um procedimento chave para compreender por que motivo dados não tem os valores esperados.

### <a name="connector-space-object-properties"></a>Propriedades do objeto de espaço de conexão
**Importar**  
Quando abre um objeto de cs, existem vários separadores na parte superior. O separador **Importar** mostra os dados que estão testados após uma importação.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) o **Valor antigo** mostra o que atualmente está armazenado no sistema e o **Novo valor** que recebeu a partir do sistema de origem e ainda não foi aplicado. Neste caso, uma vez que existe um erro de sincronização, não pode ser aplicada a alteração.

**Erro**  
A página de erro apenas está visível, se existir um problema com o objeto. Ver os detalhes na página de operações para obter mais informações sobre como [resolver problemas de erros de sincronização](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Linhagem da**  
O separador de linhagem mostra como o objeto de espaço de conexão está relacionada com o objeto metaverse. Pode ver quando o conector importada última uma alteração do sistema ligado e quais são as regras aplicadas para preencher dados a metaverse.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) na coluna **ação** , pode ver existe uma regra de sincronização de **entrada** com a ação **aprovisionar**. Que indica que, desde que este objeto de espaço de conexão estiver presente, o objeto metaverse permanece. Se a lista de regras de sincronização em vez disso, mostra uma regra de sincronização com a direcção de **saída** e **aprovisionar**, indica que este objeto é eliminado quando o objeto metaverse é eliminado.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) também pode ver na coluna **PasswordSync** que o espaço de conector de receção pode contribuir alterações à palavra-passe uma vez que uma regra de sincronização tem o valor **Verdadeiro**. Esta palavra-passe, em seguida, é enviado para Azure AD através da regra de saída.

A partir do separador linhagem, pode aceder à metaverse ao clicar em [Propriedades do objeto Metaverse](#metaverse-object-properties).

Na parte inferior de todos os separadores, existem dois botões: **Pré-visualizar** e **Inicie sessão**.

**Pré-visualização**  
A página de pré-visualização é utilizada para sincronizar um único objeto. É útil se são algumas regras de sincronização de cliente de resolução de problemas e pretender ver o efeito de uma alteração num único objeto. Pode selecionar entre **Sincronização completa** e **sincronização Delta**. Também pode selecionar entre **Gerar pré-visualizar**, que apenas mantém a alteração na memória e **Consolidar pré-visualização**, as fases todas as alterações ao espaços de conector de destino.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) pode inspecionar o objeto e qual a regra aplicados para um fluxo de atributo específico.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Registo**  
A página registo é utilizada para ver o estado de sincronização de palavra-passe e o histórico, consulte o artigo [resolver problemas de sincronização de palavras-passe](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization) para obter mais informações.

### <a name="metaverse-object-properties"></a>Propriedades do objeto Metaverse
**Atributos**  
No separador atributos, pode ver os valores e qual dos conectores a contribuição.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**conectores**  
O separador de conectores mostra todos os espaços de conexão que tenham uma representação do objeto.
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) este separador também permite-lhe navegar para o [objeto de espaço de conexão](#connector-space-object-properties).

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
