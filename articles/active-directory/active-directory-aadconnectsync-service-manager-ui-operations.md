<properties
    pageTitle="Sincronização do Azure AD Connect: Gestor do serviço de sincronização IU | Microsoft Azure"
    description="Compreenda o separador de operações no Gestor de serviço de sincronização para a ligação do Azure AD."
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

![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

No separador de operações mostra os resultados de operações a mais recentes. Este separador é fundamental para compreender e resolução de problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Compreender as informações visíveis no separador de operações
Metade superior apresenta todas as execuções ordem crónica. Por predefinição, o registo de operações mantém informações sobre últimos sete dias, mas esta definição pode ser alterada com o [scheduler](active-directory-aadconnectsync-feature-scheduler.md). Que pretende procurar qualquer executar que não apresentam um Estado de sucesso. Pode alterar a ordenação ao clicar nos cabeçalhos.

Coluna **Estado** é a informação mais importante e mostra o problema mais grave para uma execução. Eis um resumo rápido dos Estados mais comuns de por ordem de prioridade para investigar (onde * indicar várias cadeias de erros possíveis).

Estado | Comentário
--- | ---
parado-* | Não foi possível concluir a executar. Por exemplo, se o sistema remoto está em baixo e não pode ser contactado.
parado--limite de erros | Existem mais de 5.000 erros. Executar automaticamente foi parada devido ao grande número de erros.
concluída -\*-erros | Executar concluída, mas existem erros (menos de 5.000) que devem ser pesquisados.
concluída -\*-avisos | A executar concluída, mas alguns dados não está no estado esperado. Se tiver de erros, em seguida, esta mensagem é normalmente apenas um sintoma. Até que tenha resolvido erros, não deve investigar avisos.
sucesso | Sem problemas.

Quando seleciona uma linha, a parte inferior atualizações para mostrar os detalhes da que são executadas. Para extremidade esquerda da parte inferior, poderá ter uma lista dizer **passo #**. Esta lista só aparece se tiver múltiplos domínios na sua floresta onde cada domínio for representado por um passo. Pode encontrar o nome de domínio no cabeçalho **partição**. Em **As estatísticas de sincronização**, pode encontrar mais informações sobre o número de alterações que foram processadas. Pode clicar nas ligações para obter uma lista dos objetos alteradas. Se tiver objetos com erros, esses erros mostram em **Erros de sincronização**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Resolução de problemas no separador operações
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Quando tiver erros, tanto o objeto no erro e o erro propriamente dito são ligações que fornece mais informações.

Comece por clicar a cadeia de erro (a**sincronização-regra-erro-função-acionou** de na imagem). Em primeiro lugar são apresentadas com uma descrição geral do objeto. Para ver o erro real, clique no botão **Rastreio da pilha**. Este rastreio fornece informações de nível de depuração para o erro.

**Sugestão:** Pode com o botão direito na caixa de **informações da pilha de chamadas** , selecione **Selecionar tudo**e **Copiar**. Em seguida, pode copiar a pilha e veja o erro no seu editor favorito, como o bloco de notas.

- Se for o erro de **SyncRulesEngine**, em seguida, as informações de pilha de chamada pela primeira vez tem uma lista de todos os atributos no objeto. Deslocar para baixo até ver o cabeçalho **InnerException = >**.  
![Gestor de serviços de sincronização](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
A linha após mostra o erro. Na imagem acima, o erro é a partir de um Fabrikam regra de sincronização personalizado criado.

Se o erro propriamente dito não lhe derem informações suficientes, em seguida, é hora para ver os dados propriamente ditos. Pode clicar na ligação com o identificador de objeto e, em seguida, [siga um objeto e os seus dados através do sistema](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
