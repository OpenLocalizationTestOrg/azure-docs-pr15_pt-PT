<properties
    pageTitle="Otimizar o seu ambiente com a solução de avaliação do Active Directory no registo de análise | Microsoft Azure"
    description="Pode utilizar a solução de avaliação do Active Directory para avaliar a risco e estado de funcionamento do seu os ambientes servidor num intervalo normal."
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
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="optimize-your-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Otimizar o seu ambiente com a solução de avaliação do Active Directory no registo de análise

Pode utilizar a solução de avaliação do Active Directory para avaliar a risco e estado de funcionamento do seu os ambientes servidor num intervalo normal. Este artigo irá ajudá-lo instalar e utilizar a solução para que pode efetuar ações lentes de potenciais problemas.

Esta solução fornece uma lista com prioridades das recomendações específicas à sua infraestrutura de servidor implementada. As recomendações são categorizadas através de quatro áreas de foco que ajudá-lo rapidamente compreensão do risco e tomar medidas.

As recomendações são baseadas nos dados de conhecimento e experiência por engenheiros da Microsoft a partir de milhares de visitas de cliente. Cada recomendação fornece orientações sobre por que motivo um problema poderá importante para si e sobre como aplicar as alterações sugeridas.

Pode escolher as áreas de foco que são mais importantes para a sua organização e controlar o seu progresso face a executar um ambiente de gratuito e saudável risco.

Depois de que adicionou a solução e uma avaliação está concluída, resumo informações para as suas áreas de foco são apresentadas no dashboard de **avaliação de AD** para infraestrutura no seu ambiente. As secções seguintes descrevem como utilizar as informações no dashboard de **avaliação de AD** , onde pode ver e, em seguida, efetuar ações recomendadas para sua infraestrutura de servidor do Active Directory.

![imagem de mosaico de avaliação de SQL](./media/log-analytics-ad-assessment/ad-tile.png)

![imagem do dashboard de avaliação de SQL](./media/log-analytics-ad-assessment/ad-assessment.png)


## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar as soluções.

- Agentes tem de estar instalados no controladores de domínio que são membros do domínio de ser avaliada.
- A solução do Active Directory avaliação requer .NET Framework 4 instalado em cada computador que tem um agente OMS.
- Adicione a solução de avaliação do Active Directory para a área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  Não existe nenhuma configuração mais obrigatória.

    >[AZURE.NOTE] Depois de adicionar a solução, o ficheiro de AdvisorAssessment.exe é adicionado aos servidores com agentes. Dados de configuração são ler e, em seguida, enviados para o serviço OMS na nuvem para processamento. Lógica é aplicada aos dados recebidos e o serviço de nuvem regista os dados.

## <a name="active-directory-assessment-data-collection-details"></a>Detalhes da coleção de dados ativos avaliação de diretório

Avaliação do Active Directory recolhe dados WMI, dados do registo e dados de desempenho utilizando os agentes de que ativou.

A tabela seguinte mostra os métodos de recolha de dados para agentes, se o Gestor de operações (SCOM) é necessária e o modo como dados com frequência são recolhidos por um agente.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![N](./media/log-analytics-ad-assessment/oms-bullet-red.png)|   ![N](./media/log-analytics-ad-assessment/oms-bullet-red.png)|![Sim](./media/log-analytics-ad-assessment/oms-bullet-green.png)| 7 dias|


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

**Atualizar, migração e implementação** - esta área foco mostra recomendações para o ajudar a atualizar, migrar e implementar o Active Directory à sua infraestrutura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deverá visam pontuação 100% em todas as áreas de foco?

Não necessariamente. As recomendações são baseadas nos dados de conhecimento e experiências adquiridas pelo engenheiros da Microsoft em milhares de visitas de cliente. No entanto, sem infra-estruturas duas servidor são os mesmos e recomendações específicas podem ser mais ou menos relevantes para si. Por exemplo, algumas recomendações de segurança podem ser menos relevantes se máquinas virtuais não são expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem recolha de dados ad hoc de prioridade baixa e relatórios. Problemas que são importantes para um negócio maduro podem ser menor importantes para um arranque. Poderá querer identificar as áreas de foco são as prioridades e, em seguida, veja como o seu pontuações alterar ao longo do tempo.

Cada recomendação inclui orientações sobre por que motivo é importante. Deve utilizar estas orientações para avaliar se implementar a recomendação é adequado para si, tendo em conta a natureza dos seus serviços de TI e as necessidades de negócio da sua organização.

## <a name="use-assessment-focus-area-recommendations"></a>Utilizar as recomendações de área de foco avaliação

Antes de poder utilizar uma solução de avaliação no OMS, tem de ter a solução instalada. Para obter mais informações sobre como instalar soluções, consulte [Adicionar o registo de análise soluções a partir da Galeria de soluções](log-analytics-add-solutions.md). Depois da instalação, pode ver o resumo das recomendações utilizando o mosaico de avaliação na página Descrição geral na OMS.

Ver as avaliações de conformidade resumidos para sua infraestrutura e, em seguida, desagregação para recomendações.


### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver as recomendações para uma área de foco e tomar medidas lentes

1. Na página **Descrição geral** , clique no mosaico de **avaliação** para sua infraestrutura de servidor.
2. Na página de **avaliação** , reveja as informações de resumo de uma das lâminas de área de foco e, em seguida, clique em para ver as recomendações para essa área foco.
3. Em qualquer uma das páginas da área de foco, pode ver as recomendações com prioridade alta para o seu ambiente. Clique numa recomendação em **Objetos afetados** para ver os detalhes sobre por que motivo é feita a recomendação.  
    ![Imagem das recomendações de avaliação](./media/log-analytics-ad-assessment/ad-focus.png)
4. Pode efetuar ações lentes sugeridas **Ações sugeridas**. Quando o item foi corrigido, avaliações posteriores gravará que recomendado ações foram tiradas e aumenta a sua pontuação de conformidade. Corrigido itens são apresentados como **Passou objetos**.

## <a name="ignore-recommendations"></a>Ignorar recomendações

Se tiver recomendações que pretende ignorar, pode criar um ficheiro de texto que OMS irá utilizar para impedir que as recomendações não apareça nos resultados da sua avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar recomendações irá ignorar

1.  Inicie sessão na sua área de trabalho e abra o registo de pesquisa. Utilize a seguinte consulta recomendações de lista que não tenham para computadores no seu ambiente.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Eis uma captura de ecrã que mostra a consulta de pesquisa de registo: ![Ocorreu uma falha de recomendações](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

2.  Selecione recomendações que pretende ignorar. Irá utilizar os valores para RecommendationId no procedimento seguinte.


### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt

1.  Crie um ficheiro com o nome IgnoreRecommendations.txt.
2.  Colar ou escreva cada RecommendationId para cada recomendação que pretende que a análise de registo para ignorar numa linha separada e, em seguida, guarde e feche o ficheiro.
3.  Coloque o ficheiro na seguinte pasta em cada computador onde pretende que OMS ignorar recomendações.
    - Em computadores com o Microsoft monitorização Agent (ligado diretamente ou através do Gestor de operações) - *unidade do sistema*: \Programas\Microsoft Agent\Agent de monitorização
    - O servidor de gestão do Gestor de operações - *unidade do sistema*: \Programas\Microsoft sistema Centro 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar que são ignoradas recomendações

Depois da próxima agendada será executado de avaliação, por predefinição, 7 dias, as recomendações especificadas são marcadas *ignorado* e não serão apresentados no dashboard de avaliação.

1. Pode utilizar as consultas de pesquisa do registo seguintes lista as recomendações ignoradas.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

2.  Se mais tarde decidir que pretende ver ignoradas recomendações, remova os ficheiros de IgnoreRecommendations.txt ou pode remover RecommendationIDs das mesmas.

## <a name="ad-assessment-solutions-faq"></a>Soluções de avaliação do AD perguntas mais frequentes

*Com que frequência é executado uma avaliação?*
- A avaliação é executado 7 dias.

*Existe uma forma para configurar com que frequência é executada a avaliação?*
- Não neste momento.

*Se outro servidor para seja descoberto após adicionei uma solução de avaliação, será-avaliado?*
- Sim, assim como é descoberta que é avaliada a partir de, em seguida, no, 7 dias.

*Se um servidor é encerrado, quando será-removido da avaliação?*
- Se o servidor não apresentar dados para 3 semanas, este é removido.

*O que é o nome do processo que é que a recolha de dados?*
- AdvisorAssessment.exe

*Quanto tempo demora para os dados para serem recolhidos?*
- A recolha de dados reais no servidor demora cerca de 1 hora. Poderá demorar mais tempo nos servidores que tenham um grande número de servidores do Active Directory.

*Tipo de dados é recolhido?*
- Os seguintes tipos de dados são recolhidos:
    - WMI
    - Registo
    - Contadores de desempenho

*Existe uma forma de configurar quando os dados recolhidos?*
- Não neste momento.

*Por que motivo para apresentar apenas as recomendações de 10 principais?*
- Em vez de que lhe dá uma lista confuso exaustiva das tarefas, recomendamos que focar endereçar as recomendações com prioridade alta pela primeira vez. Depois de endereço-las, recomendações adicionais irão tornar-se disponível. Se preferir ver a lista detalhada, pode ver todas as recomendações utilizando a pesquisa de registo.

*Existe uma forma de ignorar uma recomendação?*
- Sim, consulte o artigo [Ignorar recomendações](#ignore-recommendations) secção acima.


## <a name="next-steps"></a>Próximos passos

- Utilize [pesquisas de registo no registo Analytics](log-analytics-log-searches.md) para ver dados detalhados do AD avaliação e recomendações.
