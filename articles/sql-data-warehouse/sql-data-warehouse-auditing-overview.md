<properties
   pageTitle="Auditoria no armazém de dados do Azure SQL | Microsoft Azure"
   description="Introdução ao auditoria no armazém de dados do SQL Azure"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016" 
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="auditing-in-azure-sql-data-warehouse"></a>Auditoria no armazém de dados do Azure SQL

> [AZURE.SELECTOR]
- [Auditoria](sql-data-warehouse-auditing-overview.md)
- [Deteção de ameaça](sql-data-warehouse-security-threat-detection.md)

Auditoria armazém de dados SQL permite-lhe a registo de eventos na base de dados para uma auditoria iniciar sessão na sua conta de armazenamento do Windows Azure. Auditoria pode ajudar a manter a conformidade de regulamentação, compreender atividade de base de dados e obter visão discrepâncias e anomalias que indiquem preocupações empresariais ou suspeitas violações de segurança. Armazém de dados SQL auditoria também integra-se com o Microsoft Power BI para desagregação relatórios e análise.

Ferramentas de auditoria ativar e facilitar adesão aos padrões de conformidade, mas não garante a conformidade. Para mais informações sobre os programas do Azure que suporta a conformidade normas, consulte o <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro de fidedignidade do Azure</a>.

+ [Noções básicas de auditoria de base de dados]
+ [Configurar o auditoria para a sua base de dados]
+ [Analisar os registos de auditoria e relatórios]

##<a id="subheading-1"></a>Noções básicas de auditoria da base de dados de armazém de dados do SQL Azure


Auditoria de base de dados do armazém de dados SQL permite-lhe:

- **Manter** um registo de auditoria de eventos selecionados. Pode definir categorias das ações de base de dados para serem auditados.
- **Relatório** na atividade de base de dados. Pode utilizar pré-configurado relatórios e um dashboard para começar a trabalhar rapidamente com atividade e relatórios do evento.
- Relatórios de **Analisar** . Pode encontrar eventos suspeitos, atividade invulgar e tendências.

Pode configurar a auditoria para as seguintes categorias de evento:

**SQL simples** e **Parametrizadas SQL** para as quais os registos de auditoria recolhida são classificados como  

- **Acesso aos dados**
- **Alterações de esquema linguagem DDL)**
- **Alterações de dados (LMG)**
- **Contas, funções e permissões (DCL)**
- **Procedimento armazenado**, **início de sessão** e **Gestão da transação**.

Para cada categoria de evento, auditoria de **sucesso** e operações de **Falha** estão configurados separadamente.

Para obter mais detalhes sobre as atividades e eventos auditados, consulte a <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referência de formato de registo de auditoria (transferência do ficheiro de documento)</a>.

Registos de auditoria são armazenados na sua conta de armazenamento Azure. Pode definir um período de retenção do registo de auditoria.

Pode ser definida uma política de auditoria para uma base de dados ou como uma política de servidor predefinida. Uma política de auditoria de servidor predefinida serão aplicadas a todas as bases de dados num servidor que não têm uma específico da base de dados auditoria política de substituição definida.

Antes de definição de cima auditoria verificação auditoria se estiver a utilizar um ["nível inferior cliente"](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configurar o auditoria para a sua base de dados

1. Inicie o <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. Navegue para a pá de configuração da base de dados SQL Data Warehouse / do SQL Server pretendidos para auditoria. Clique no botão **Definições** no início e, em seguida, na pá a definição e, selecione **auditoria**.

    ![][1]

3. No auditoria pá de configuração, primeiro desmarca a caixa de verificação **Herdar definições de auditoria de servidor** . Esta opção permite-lhe especificar as definições para uma base de dados específico.

    ![][2]

4. Em seguida, ative auditoria clicando no botão **no** .

    ![][3]

5. Na auditoria pá de configuração, selecione **Detalhes de armazenamento** para abrir o pá de armazenamento de registos de auditoria. Selecione a conta de armazenamento Azure onde serão guardados os registos de início e o período de retenção. **Sugestão:** Utilize a mesma conta de armazenamento para todas as bases de dados de auditoria para tirar o máximo partido os modelos de relatórios pré-configurado.

    ![][4]

6. Clique no botão **OK** para guardar a configuração de detalhes de armazenamento.


7. Em **Registo ao evento**, clique em com e **sem** **êxito** para registar todos os eventos ou selecione categorias de evento individuais.


8. Se estiver a configurar auditoria para uma base de dados, poderá ter de alterar a cadeia de ligação do seu cliente para se certificar de auditoria de dados é capturado corretamente. Verifique se o tópico de [Modificar FDQN servidor na cadeia de ligação](sql-data-warehouse-auditing-downlevel-clients.md) nível inferior para ligações de cliente.

9. Clique em **OK**.


##<a id="subheading-3">Analisar os registos de auditoria e relatórios</a>

Registos de auditoria são agregados numa coleção de arquivo de tabelas com um prefixo **SQLDBAuditLogs** na conta de armazenamento Azure que escolheu durante a configuração. Pode ver os ficheiros de registo utilizar uma ferramenta de como o <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorador de armazenamento do Azure</a>.

Um modelo de relatório de dashboard pré-configurado está disponível como uma <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">folha de cálculo do Excel transferível</a> para ajudá-lo rapidamente analisar os dados do registo. Para utilizar o modelo no seu registos de auditoria, é necessário o Excel 2013 ou posterior e o Power Query, pode transferir <a href="http://www.microsoft.com/download/details.aspx?id=39379">aqui</a>.

O modelo tiver dados de exemplo fictício no mesmo e, pode configurar o Power Query para importar o seu registo de auditoria diretamente a partir da sua conta de armazenamento Azure.

Para obter informações mais detalhadas sobre como trabalhar com o modelo de relatório, leia o <a href="http://go.microsoft.com/fwlink/?LinkId=506731">How To (transferir de documento)</a>.

![][5]


##<a id="subheading-4">Práticas para a utilização de produção</a>
A descrição nesta secção que se refere a capturas de ecrã acima. <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou <a href= "https://manage.windowsazure.com/" target="_bank">Clássico Portal clássica do Azure</a> pode ser utilizados.


##<a id="subheading-5"></a>Nova chave geração armazenamento

Produção são muito provavelmente atualizar as suas chaves de armazenamento periodicamente. Quando atualizar as suas chaves precisar de voltar a guardar a política. O processo é da seguinte forma:.


1. Na pá de configuração de auditoria (descrito acima configurar auditoria secção) mude a **Tecla de acesso de armazenamento** da *principal* para *secundário* e **Guardar**.
![][4]
2. Vá para o pá de configuração de armazenamento e, em seguida, **voltar a gerar** a *Chave primária do Access*.

3. Aceda novamente para auditoria pá de configuração, mude a **Tecla de acesso de armazenamento** da *secundário* para *principal* e prima **Guardar**.

4. Voltar para o armazenamento IU e, em seguida, **voltar a gerar** a *Chave de acesso secundária* (como preparação para o próximo ciclo de atualização de teclas.

##<a id="subheading-6"></a>Automatização
Existem várias os cmdlets do PowerShell que pode utilizar para configurar auditorias base de dados do SQL Azure. Para aceder aos cmdlets da auditoria tem de ser executar PowerShell no modo de Gestor de recursos do Azure.

> [AZURE.NOTE] O módulo do [Gestor de recursos do Azure](https://msdn.microsoft.com/library/dn654592.aspx) atualmente está na pré-visualização. Poderá não fornecer as mesmas capacidades de gestão do módulo Azure.

Quando estiver no modo de Gestor de recursos do Azure, executar `Get-Command *AzureSql*` para listar os cmdlets disponíveis.


<!--Anchors-->
[Noções básicas de auditoria de base de dados]: #subheading-1
[Configurar o auditoria para a sua base de dados]: #subheading-2
[Analisar os registos de auditoria e relatórios]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
