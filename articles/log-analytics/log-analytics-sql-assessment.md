<properties
    pageTitle="Otimizar o seu ambiente com a solução de avaliação do SQL no registo de análise | Microsoft Azure"
    description="Pode utilizar a solução de avaliação de SQL para avaliar a risco e estado de funcionamento do seu os ambientes servidor num intervalo normal."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="optimize-your-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Otimizar o seu ambiente com a solução de avaliação do SQL no registo de análise


Pode utilizar a solução de avaliação de SQL para avaliar a risco e estado de funcionamento do seu os ambientes servidor num intervalo normal. Este artigo irá ajudá-lo instalar a solução para que pode efetuar ações lentes de potenciais problemas.

Esta solução fornece uma lista com prioridades das recomendações específicas à sua infraestrutura de servidor implementada. As recomendações são categorizadas através de seis áreas de foco que ajudá-lo rapidamente compreensão do risco e tomar medidas lentes.

As recomendações são baseadas nos dados de conhecimento e experiência por engenheiros da Microsoft a partir de milhares de visitas de cliente. Cada recomendação fornece orientações sobre por que motivo um problema poderá importante para si e sobre como aplicar as alterações sugeridas.

Pode escolher as áreas de foco que são mais importantes para a sua organização e controlar o seu progresso face a executar um ambiente de gratuito e saudável risco.

Depois de que adicionou a solução e uma avaliação está concluída, resumo informações para as suas áreas de foco são apresentadas no dashboard de **Avaliação de SQL** para a infraestrutura no seu ambiente. As secções seguintes descrevem como utilizar as informações no dashboard de **Avaliação de SQL** , onde pode ver e, em seguida, efetuar ações recomendadas para sua infraestrutura do SQL server.

![imagem de mosaico de avaliação de SQL](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![imagem do dashboard de avaliação de SQL](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Avaliação de SQL funciona com todas as versões atualmente suportadas do SQL Server para as edições de padrão, programador e Enterprise.

Utilize as seguintes informações para instalar e configurar a solução.

- Agentes tem de estar instalados nos servidores que tenham o SQL Server instalado.
- A solução de SQL avaliação requer .NET Framework 4 instalado em cada computador que tem um agente OMS.
- Quando utilizar o Gestor de operações agent com avaliação de SQL, terá de utilizar uma conta do Run-As do Gestor de operações. Para mais informações, consulte [Gestor de operações como executar contas para OMS](#operations-manager-run-as-accounts-for-oms) abaixo.

    >[AZURE.NOTE] O agente MMA não suporta contas Run-As do Gestor de operações.

- Adicione a solução de avaliação de SQL para a área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md). Não existe nenhuma configuração mais obrigatória.

>[AZURE.NOTE] Depois de adicionar a solução, o ficheiro de AdvisorAssessment.exe é adicionado aos servidores com agentes. Dados de configuração são ler e, em seguida, enviados para o serviço OMS na nuvem para processamento. Lógica é aplicada aos dados recebidos e o serviço de nuvem regista os dados.

## <a name="sql-assessment-data-collection-details"></a>Detalhes de recolha de dados de avaliação de SQL

Avaliação de SQL recolhe dados, dados do registo, dados de desempenho e do SQL Server management dinâmico ver resultados utilizando os agentes de que ativou.

A tabela seguinte mostra os métodos de recolha de dados para agentes, se o Gestor de operações (SCOM) é necessária e o modo como dados com frequência são recolhidos por um agente.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Sim](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![N](./media/log-analytics-sql-assessment/oms-bullet-red.png)|    ![N](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Sim](./media/log-analytics-sql-assessment/oms-bullet-green.png)|   7 dias|

## <a name="operations-manager-run-as-accounts-for-oms"></a>Gestor de operações como executar contas para OMS

Análise de registo no OMS utiliza o grupo de gestão e de agente do Gestor de operações para recolher e enviar dados para o serviço OMS. OMS compilações relativamente a pacotes de gestão das cargas de trabalho fornecer valor-adicionar serviços. Cada carga de trabalho requer privilégios de carga de trabalho específicos para executar os pacotes de gestão no contexto de segurança diferente, tal como uma conta de domínio. Tem de fornecer informações de credenciais ao configurar uma conta de operações Gestor executar como.

Utilize as seguintes informações para definir a conta de executar como Gestor de operações de avaliação de SQL.

### <a name="set-the-run-as-account-for-sql-assessment"></a>Executar como uma conta de avaliação de SQL

 Se já estiver a utilizar o pacote de gestão do SQL Server, deve utilizar essa conta executar como.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar a conta SQL executar como na consola de operações

>[AZURE.NOTE] Se estiver a utilizar o agente direto OMS, em vez do agente SCOM, o pacote de gestão de sempre é executado no contexto de segurança da conta de sistema Local. Ignore os passos 1-5 abaixo e executar a T-SQL ou uma amostra de Powershell, especificando NT\Sistema como o nome de utilizador.

1. No Gestor de operações, abra a consola de operações e, em seguida, clique em **Administração**.

2. Em **Executar como configuração**, clique em **perfis**e abrir **OMS SQL avaliação executar como perfil**.

3. Na página **Executar como contas** , clique em **Adicionar**.

4. Selecione uma conta do Windows executar como que contém as credenciais necessárias para o SQL Server ou clique em **Novo** para criar uma.
    >[AZURE.NOTE] O tipo de conta executar como tem de ser Windows. Também têm de ser a conta executar como parte do grupo de administradores Local em todos os servidores de Windows alojamento instâncias do SQL Server.

5. Clique em **Guardar**.

6. Modificar e, em seguida, execute o seguinte exemplo T-SQL em cada instância do SQL Server para conceder as permissões mínimas necessárias para executar como conta para efetuar a avaliação de SQL. No entanto, não precisa de efetuar esta ação se executar como é uma conta do já parte da função de servidor sysadmin em instâncias do SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar a conta SQL executar como utilizar o Windows PowerShell

Abra uma janela do PowerShell e execute o seguinte script depois de atualizá-la com as suas informações:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Noções sobre como são prioridades recomendações

Cada recomendação efectuada é atribuída um valor de ponderação que identifica a importância relativa de recomendação. São apresentadas as dez recomendações mais importantes.

### <a name="how-weights-are-calculated"></a>Como são calculadas espessuras

Coeficientes são agregados valores com base em três factores de chaves:

- A *probabilidade* de um problema identificados causará problemas. Uma maior probabilidade equivale a uma maior pontuação geral para a recomendação.

- O *impacto* do problema na sua organização se a causar um problema. Um impacto superior equivale a uma maior pontuação geral para a recomendação.

- O *esforço* necessário para implementar a recomendação. Um esforço de superior equivale a uma pontuação geral mais pequena para a recomendação.

A ponderação para cada recomendação é expresso como uma percentagem do total pontuação disponível para cada área foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tiver uma pontuação de 5%, implementar a essa recomendação aumenta a sua % de pontuação pela 5 de segurança e conformidade geral.

### <a name="focus-areas"></a>Áreas de foco

**Segurança e conformidade** - esta área foco mostra recomendações para potenciais ameaças de segurança e violações, políticas de empresa e requisitos de conformidade técnico, regulatória e legal.

**Disponibilidade e continuidade do negócio** - esta área foco mostra recomendações para disponibilidade do serviço, RDP dos seus infraestrutura e proteção de empresas.

**Desempenho e escalabilidade** - esta área foco mostra recomendações para o ajudar a sua organização infraestrutura de TI crescem em sequência, certifique-se de que seu ambiente de TI cumpra os requisitos de desempenho atual e conseguir responder a alteração das suas necessidades de infraestrutura.

**Atualizar, migração e implementação** - esta área foco mostra recomendações para o ajudar a atualizar, migrar e implementar o SQL Server à sua infraestrutura existente.

**Monitorização e operações** - esta área foco mostra recomendações para o ajudar a simplificar as operações de IT, implementar manutenção prevenção e maximizar o desempenho.

**Alterar e gestão da configuração** - esta área foco mostra recomendações para o ajudar a proteger operações no dia a dia, certifique-se de que as alterações não negativa afetar a sua infraestrutura, estabelecer procedimentos de controlo de alterações e para controlar e de auditoria configurações do sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deverá visam pontuação 100% em todas as áreas de foco?

Não necessariamente. As recomendações são baseadas nos dados de conhecimento e experiências adquiridas pelo engenheiros da Microsoft em milhares de visitas de cliente. No entanto, sem infra-estruturas duas servidor são os mesmos e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se máquinas virtuais não são expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem recolha de dados ad hoc de prioridade baixa e relatórios. Problemas que são importantes para um negócio maduro podem ser menor importantes para um arranque. Poderá querer identificar as áreas de foco são as prioridades e, em seguida, veja como o seu pontuações alterar ao longo do tempo.

Cada recomendação inclui orientações sobre por que motivo é importante. Deve utilizar estas orientações para avaliar se implementar a recomendação é adequado para si, tendo em conta a natureza dos seus serviços de TI e as necessidades de negócio da sua organização.

## <a name="use-assessment-focus-area-recommendations"></a>Utilizar as recomendações de área de foco avaliação

Antes de poder utilizar uma solução de avaliação no OMS, tem de ter a solução instalada. Para obter mais informações sobre como instalar soluções, consulte [Adicionar o registo de análise soluções a partir da Galeria de soluções](log-analytics-add-solutions.md). Depois da instalação, pode ver o resumo das recomendações utilizando o mosaico de avaliação de SQL na página Descrição geral na OMS.

Ver as avaliações de conformidade resumidos para sua infraestrutura e, em seguida, desagregação para recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver as recomendações para uma área de foco e tomar medidas lentes

1. Na página **Descrição geral** , clique no mosaico de **Avaliação de SQL** .
2. Na página de **Avaliação de SQL** , reveja as informações de resumo de uma das lâminas de área de foco e, em seguida, clique em para ver as recomendações para essa área foco.
3. Em qualquer uma das páginas da área de foco, pode ver as recomendações com prioridade alta para o seu ambiente. Clique numa recomendação em **Objetos afetados** para ver os detalhes sobre por que motivo é feita a recomendação.  
    ![Imagem das recomendações de avaliação de SQL](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. Pode efetuar ações lentes sugeridas **Ações sugeridas**. Quando o item foi corrigido, avaliações posteriores gravará que recomendado ações foram tiradas e aumenta a sua pontuação de conformidade. Corrigido itens são apresentados como **Passou objetos**.

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se tiver recomendações que pretende ignorar, pode criar um ficheiro de texto que OMS irá utilizar para impedir que as recomendações não apareça nos resultados da sua avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar recomendações irá ignorar

1.  Inicie sessão na sua área de trabalho e abra o registo de pesquisa. Utilize a seguinte consulta recomendações de lista que não tenham para computadores no seu ambiente.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Eis uma captura de ecrã que mostra a consulta de pesquisa de registo: ![Ocorreu uma falha de recomendações](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.  Selecione recomendações que pretende ignorar. Irá utilizar os valores para RecommendationId no procedimento seguinte.


### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt

1.  Crie um ficheiro com o nome IgnoreRecommendations.txt.
2.  Colar ou escreva cada RecommendationId para cada recomendação que pretende que OMS para ignorar numa linha separada e, em seguida, guarde e feche o ficheiro.
3.  Coloque o ficheiro na seguinte pasta em cada computador onde pretende que OMS ignorar recomendações.
    - Em computadores com o Microsoft monitorização Agent (ligado diretamente ou através do Gestor de operações) - *unidade do sistema*: \Programas\Microsoft Agent\Agent de monitorização
    - O servidor de gestão do Gestor de operações - *unidade do sistema*: \Programas\Microsoft sistema Centro 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar que são ignoradas recomendações

1.  Depois da próxima agendada será executado de avaliação, por predefinição, 7 dias, as recomendações especificadas são marcadas ignorado e não serão apresentados no dashboard de avaliação.
2.  Pode utilizar as consultas de pesquisa do registo seguintes lista as recomendações ignoradas.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.  Se mais tarde decidir que pretende ver ignoradas recomendações, remova os ficheiros de IgnoreRecommendations.txt ou pode remover RecommendationIDs das mesmas.

## <a name="sql-assessment-solution-faq"></a>Solução de avaliação de SQL perguntas mais frequentes

*Com que frequência é executado uma avaliação?*
- A avaliação é executado 7 dias.

*Existe uma forma para configurar com que frequência é executada a avaliação?*
- Não neste momento.

*Se outro servidor para seja descoberto após adicionei a solução de avaliação de SQL, serão-avaliada?*
- Sim, assim como é descoberta que é avaliada a partir de, em seguida, no, 7 dias.

*Se um servidor é encerrado, quando será-removido da avaliação?*
- Se o servidor não apresentar dados para 3 semanas, este é removido.

*O que é o nome do processo que é que a recolha de dados?*
- AdvisorAssessment.exe

*Quanto tempo demora para os dados para serem recolhidos?*
- A recolha de dados reais no servidor demora cerca de 1 hora. Poderá demorar mais tempo nos servidores que tenham um grande número de instâncias do SQL ou bases de dados.

*Tipo de dados é recolhido?*
- Os seguintes tipos de dados são recolhidos:
    - WMI
    - Registo
    - Contadores de desempenho
    - Vistas de gestão de dinâmica SQL (das DMV).

*Existe uma forma de configurar quando os dados recolhidos?*
- Não neste momento.

*Por que motivo tenho de ter que configurar uma executar como conta?*
- Para o SQL Server, um pequeno número de consultas SQL é executado. Ordem das mesmas executar, deve ser utilizada um como conta executar com permissões de estado do servidor vista SQL.  Além disso, para poder WMI da consulta, administrador local são necessárias credenciais.

*Por que motivo para apresentar apenas as recomendações de 10 principais?*
- Em vez de que lhe dá uma lista confuso exaustiva das tarefas, recomendamos que focar endereçar as recomendações com prioridade alta pela primeira vez. Depois de endereço-las, recomendações adicionais irão tornar-se disponível. Se preferir ver a lista detalhada, pode ver todas as recomendações utilizando a pesquisa de registo OMS.

*Existe uma forma de ignorar uma recomendação?*
- Sim, consulte o artigo [Ignorar recomendações](#ignore-recommendations) secção acima.



## <a name="next-steps"></a>Próximos passos

- [Registos de pesquisa](log-analytics-log-searches.md) para ver dados detalhados de avaliação de SQL e recomendações.
