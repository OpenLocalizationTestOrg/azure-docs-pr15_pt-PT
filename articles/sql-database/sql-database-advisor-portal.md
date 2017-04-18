<properties 
   pageTitle="Advisor de base de dados SQL Azure através do portal Azure | Microsoft Azure" 
   description="Pode utilizar a classificação de base de dados do SQL Azure no portal do Azure para rever e implementar recomendações para as bases de dados SQL existente que podem melhorar o desempenho da consulta atual." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="sql-database-advisor-using-the-azure-portal"></a>Advisor de base de dados SQL Azure no portal

> [AZURE.SELECTOR]
- [Descrição geral de classificação de base de dados SQL](sql-database-advisor.md)
- [Portal](sql-database-advisor-portal.md)

Pode utilizar a classificação de base de dados do SQL Azure no portal do Azure para rever e implementar recomendações para as bases de dados SQL existente que podem melhorar o desempenho da consulta atual.

## <a name="viewing-recommendations"></a>Recomendações de visualização

A página de recomendações é onde é visualizar as recomendações superiores com base no seu impacto potencial para melhorar o desempenho. Também pode ver o estado das operações do históricos. Selecione uma recomendação ou estado para ver mais detalhes.

Para ver e aplicar as recomendações, tem as permissões de [controlo de acesso baseado em funções](../active-directory/role-based-access-control-configure.md) de correto no Azure. **Leitor**, permissões de **SQL DB contribuinte** são necessárias para recomendações de vista e o **proprietário**, **SQL DB contribuinte** permissões necessárias para executar as ações; Criar ou remover índices e cancelar a criação de índice remissivo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **mais serviços** > **bases de dados SQL**e selecione a base de dados.
5. Clique em **Recomendação de desempenho** para ver recomendações disponíveis para a base de dados seleccionada.

> [AZURE.NOTE] Para obter recomendações uma base de dados, tem de ter sobre um dia de utilização e precisa de ser algumas atividade. Não existem também tem de ser algumas atividade consistente. A classificação de base de dados SQL do mais facilmente pode otimizar para padrões de consulta consistentes que-lo para a rajada spotty aleatória da atividade. Se não estiverem disponíveis recomendações, a página de **Recomendação desempenho** deve fornecer uma mensagem a explicar o motivo pelo qual.

![Recomendações](./media/sql-database-advisor-portal/recommendations.png)

Eis um exemplo de recomendação "Corrigir o problema de esquema" no portal do Azure.

![Corrigir o problema de esquema](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Recomendações são ordenadas pelo respetivo potencial impacto no desempenho nas seguintes quatro categorias:

| Impacto | Descrição |
| :--- | :--- |
| Alto | Recomendações de elevado impacto devem fornecer o impacto no desempenho mais significativo. |
| Médio | Impacto médio recomendações devem melhorar o desempenho, mas não substancialmente. |
| Baixa | Recomendações de baixa impacto devem fornecer um melhor desempenho que sem, mas melhorias poderão não estar significativas. 


### <a name="removing-recommendations-from-the-list"></a>Remover recomendações a partir da lista

Se a sua lista de recomendações contém itens que pretende remover da lista, pode eliminar a recomendação:

1. Selecione uma recomendação na lista de **recomendações**.
2. Clique em **Rejeitar** a pá de **Detalhes** .


Se pretender, pode adicionar itens eliminados novamente para a lista de **recomendações** :

1. No pá **recomendações** clique em **vista eliminadas**.
1. Selecione um item rejeitado a partir da lista para ver os detalhes.
1. Opcionalmente, clique em **Anular rejeitar** para adicionar o índice novamente para a lista principal das **recomendações**.



## <a name="applying-recommendations"></a>Aplicar recomendações

Classificação de base de dados do SQL fornece-lhe controlo total sobre como são recomendações com permissão para utilizar qualquer um dos seguintes três opções: 

- Aplica recomendações individuais um de cada vez.
- Activar a classificação de aplicar automaticamente recomendações (atualmente aplica-se ao apenas recomendações de índice remissivo).
- Para implementar uma recomendação manualmente, execute o script de T-SQL recomendado relativamente a sua base de dados.

Selecione qualquer recomendação para ver os detalhes e, em seguida, clique em **Ver script** para rever os detalhes exatos do modo como a recomendação é criada.

A base de dados permanece online enquanto o advisor aplica-se a recomendação – utilização da classificação de base de dados do SQL nunca coloca uma base de dados offline.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individual

Pode rever e aceitar recomendações um de cada vez.

1. No pá **recomendações** , clique numa recomendação.
2. Os **Detalhes** pá, clique em **Aplicar**.

    ![Aplicar recomendação](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Ativar a gestão automática de índices

Pode definir a classificação de base de dados do SQL para implementar recomendações automaticamente. À medida que recomendações ficam disponíveis estes serão aplicadas automaticamente. Como com todas as operações de índice remissivo geridas pelo serviço se o impacto no desempenho for negativo recomendação será revertida.

1. No pá **recomendações** , clique em **automatizar**:

    ![Definições de classificação](./media/sql-database-advisor-portal/settings.png)

2. Defina o advisor para automaticamente **Criar** ou **Largar os** índices:

    ![Recomendado índices](./media/sql-database-advisor-portal/automation.png)


### <a name="manually-run-the-recommended-t-sql-script"></a>Executar o script T-SQL recomendado manualmente

Selecione qualquer recomendação e, em seguida, clique em **Ver script**. Execute este script contra a sua base de dados para aplicar a recomendação manualmente.

*Índices que são executados manualmente não são monitorizados e validados para impacto no desempenho pelo serviço* para que sugerido monitorizar a esses índices após a criação para verificar que fornecem ganhos de desempenho e ajustar ou eliminá-los, se necessário. Para obter detalhes sobre como criar índices, consulte o artigo [Criar índice (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### <a name="canceling-recommendations"></a>Cancelar recomendações

Recomendações que estão num estado **pendente** **Verificar**de **sucesso** podem ser canceladas. Não não possível cancelar recomendações com o estado do **Executing** .

1. Selecione uma recomendação na área **De sintonização histórico** para abrir o separador de **Detalhes de recomendações** .
2. Clique em **Cancelar** para cancelar o processo de aplicar a recomendação.



## <a name="monitoring-operations"></a>As operações de controlo

Aplicar uma recomendação poderá não ocorrer instantaneamente. O portal fornece detalhes sobre o estado de operações de recomendação. Seguem-se possíveis Estados que pode ser um índice remissivo no:

| Estado | Descrição |
| :--- | :--- |
| Pendente | Aplica recomendação comando foi recebido e está agendado para execução. |
| Em execução | Está a ser aplicada a recomendação. |
| Sucesso | Recomendação foi aplicada com êxito. |
| Erro | Ocorreu um erro durante o processo de aplicar a recomendação. Isto pode ser um problema temporário ou possivelmente num esquema altere para a tabela e o script já não é válido. |
| Reverter | Recomendação foi aplicada, mas tenha sido considerada não performant e está a ser revertida automaticamente. |
| Revertido | Recomendação foi revertida. |

Clique numa recomendação no processo a partir da lista para ver mais detalhes:

![Recomendado índices](./media/sql-database-advisor-portal/operations.png)


### <a name="reverting-a-recommendation"></a>Reverter uma recomendação

Se tiver utilizado o advisor para aplicar a recomendação (o que significa que manualmente não executou o script de T SQL)-lo será automaticamente revertida-lo se encontra o impacto no desempenho para ser negativo. Se por qualquer motivo simplesmente quiser reverter uma recomendação que pode fazer o seguinte procedimento:


1. Selecione uma recomendação aplicada com êxito na área **Tuning histórico** .
2. Clique em **Reverter** a pá **Detalhes de recomendação** .

![Recomendado índices](./media/sql-database-advisor-portal/details.png)


## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorizar o impacto no desempenho das recomendações de índice remissivo

Depois de recomendações com êxito são implementadas (atualmente, indexar operações e parametrizar apenas recomendações de consultas) pode clicar em **Informações de consulta** na pá de detalhes de recomendação para abrir a [Consulta informações de desempenho](sql-database-query-performance.md) e ver o impacto no desempenho das suas consultas superiores.

![Impacto no desempenho do monitor](./media/sql-database-advisor-portal/query-insights.png)



## <a name="summary"></a>Resumo

Classificação de base de dados do SQL fornece recomendações para melhorar o desempenho de base de dados do SQL. Ao fornecer scripts T SQL, assim como individual e totalmente automático (atualmente apenas um índice), a classificação do fornece assistência útil em otimizar a sua base de dados e finalmente melhorar o desempenho da consulta.



## <a name="next-steps"></a>Próximos passos

Monitorizar o seu recomendações e continue a aplicá-los para otimizar o desempenho. Cargas de trabalho de base de dados são dinâmicas e alterar forma contínua. Advisor de base de dados SQL irão continuar a monitorização e fornecer recomendações potencialmente podem melhorar o desempenho da sua base de dados. 

 - Consulte o artigo [Advisor de base de dados do SQL](sql-database-advisor.md) para uma descrição geral da classificação de base de dados do SQL.
 - Consulte o artigo [Informações de desempenho de consulta](sql-database-query-performance.md) para saber mais sobre como ver o impacto no desempenho das suas consultas superiores.

## <a name="additional-resources"></a>Recursos adicionais

- [Arquivo de consulta](https://msdn.microsoft.com/library/dn817826.aspx)
- [CRIAR ÍNDICE](https://msdn.microsoft.com/library/ms188783.aspx)
- [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-configure.md)






