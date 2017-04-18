<properties
    pageTitle="Atualizar a solução de gestão de OMS | Microsoft Azure"
    description="Este artigo destina-se para o ajudar a compreender como utilizar esta solução para gerir as atualizações para os computadores Windows e Linux."
    services="operations-management-suite"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="operations-management-suite"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="magoedte"/>

# <a name="update-management-solution-in-omsmediaoms-solution-update-managementupdate-management-solution-iconpng-update-management-solution-in-oms"></a>![Solução de gestão de atualização no OMS](./media/oms-solution-update-management/update-management-solution-icon.png) Solução de gestão de atualização no OMS

A solução de gestão de atualização no OMS permite-lhe gerir as atualizações para os computadores Windows e Linux.  Pode rapidamente avalie o estado de atualizações disponíveis em todos os computadores agente e iniciar o processo de instalar as atualizações necessárias para os servidores. 

## <a name="prerequisites"></a>Pré-requisitos

-   Agentes de Windows quer tem de ser configurados para comunicar com um servidor do Windows Server Update Services (WSUS) ou ter acesso ao Microsoft Update.  

    >[AZURE.NOTE] O agente do Windows não pode ser gerido em simultâneo ao Gestor de configuração do Centro de sistema.  
  
-   Agentes de Linux tem de ter acesso a um repositório de atualização.  Agente do OMS para Linux podem ser transferido das [GitHub](https://github.com/microsoft/oms-agent-for-linux). 

## <a name="configuration"></a>Configuração

Execute os seguintes passos para adicionar a solução de gestão de atualização para a área de trabalho OMS e adicionar agentes de Linux.  Agentes de Windows são automaticamente adicionados com nenhuma configuração adicional.

1.  Adicione a solução de gestão de atualização para a área de trabalho OMS utilizando o processo descrito na [soluções OMS adicionar](../log-analytics/log-analytics-add-solutions.md) a partir da Galeria de soluções.  
2.  No portal do OMS, selecione **Definições** e, em seguida, **Ligado origens**.  Tenha em atenção o **ID da área de trabalho** e a **chave primária** ou **chave secundária**.
3.  Execute os seguintes passos para cada computador Linux.

    um.  Instale a versão mais recente do agente OMS para Linux executando os seguintes comandos.  Substituir <Workspace ID> com o ID da área de trabalho e <Key> com a chave primária ou secundária.

        cd ~
        wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.2.0-75/omsagent-1.2.0-75.universal.x64.sh
        sudo bash omsagent-1.2.0-75.universal.x64.sh --upgrade -w <Workspace ID> -s <Key>

     b. Para remover o agente, execute o seguinte comando.

        sudo bash omsagent-1.2.0-75.universal.x64.sh --purge

## <a name="management-packs"></a>Pacotes de gestão

Se o seu grupo de gestão do Gestor de operações do Centro de sistema estiver ligado à área de trabalho OMS, em seguida, os seguintes packs gestão serão instalados no Gestor de operações quando adiciona esta solução. Não existe nenhuma configuração ou a manutenção destes packs gestão necessários. 

-   Centro de sistema do Microsoft Advisor Update avaliação Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
-   Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
-   Atualizar a implementação MP

Para mais informações sobre como os pacotes de gestão de solução são atualizados, consulte o artigo [Ligar o Gestor de operações para a análise de registo](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Recolha de dados

### <a name="supported-agents"></a>Agentes suportados

A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

Origem ligada | Suportada | Descrição|
----------|----------|----------|
Agentes do Windows | Sim | A solução recolhe informações sobre as atualizações de sistema do agentes do Windows e inicia a instalação das atualizações necessárias.|
Agentes de Linux | Sim | A solução recolhe informações sobre as atualizações de sistema do agentes Linux.|
Grupo de gestão do Gestor de operações | Sim | A solução recolhe informações sobre as atualizações de sistema do agentes num grupo de gestão ligada.<br>Não é necessária uma ligação direta do agente de Gestor de operações de análise de registo. Dados são reencaminhados do grupo de gestão para o repositório OMS.|
Conta de armazenamento Azure | N | Armazenamento Azure não inclui informações sobre as atualizações do sistema.|  

### <a name="collection-frequency"></a>Frequência de coleções de sites

Para cada computador Windows gerido, uma análise é executada duas vezes por dia.  Quando uma atualização estiver instalada, as suas informações são atualizadas dentro de 15 minutos.  

Para cada computador Linux gerido, uma análise é executada cada 3 horas.  

## <a name="using-the-solution"></a>Utilizar a solução

Quando adiciona a solução de gestão de atualização para a área de trabalho OMS, o mosaico de **Gestão de atualização** será adicionado ao OMS dashboard. Este mosaico apresenta uma contagem e uma representação gráfica do número de computadores no seu ambiente atualmente que exige o actualizações do sistema.<br><br>
![Mosaico de resumo de gestão de actualização](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>Avaliações de atualização de visualização

Clique no mosaico **Update Management** para abrir o dashboard de **Gestão de atualização** . O dashboard inclui as colunas na tabela seguinte. Cada coluna lista até dez itens que correspondam a critérios dessa coluna para o âmbito especificado e o intervalo de tempo. Pode executar uma pesquisa de registo devolve todos os registos ao clicar em **Ver todos os** na parte inferior da coluna ou ao clicar no cabeçalho de coluna.

Coluna | Descrição|
----------|----------|
**Computadores actualizações em falta** ||
Crítico ou atualizações de segurança | Listas de topo dez computadores que estão em falta atualiza ordenado pelo número de atualizações estiverem em falta. Clique no nome do computador para executar uma pesquisa de registo devolver que todas atualizar os registos nesse computador.|
Críticas ou atualizações de segurança mais de 30 dias| Identifica o número de computadores que estão em falta crítico ou atualizações de segurança agrupadas pelo comprimento do tempo desde a actualização foi publicada. Clique das entradas para executar uma pesquisa de registo devolver todas as atualizações em falta e críticas.|
**Obrigatório atualizações em falta**||
Crítico ou atualizações de segurança | Lista classificações de atualizações que computadores estão em falta ordenados pelo número de computadores actualizações na categoria em falta. Clique numa classificação para executar uma pesquisa de registo devolver que atualizar todos os registos para essa classificação.|
**Atualizar híbridas**||
Atualizar híbridas | Número de implementações da atualização agendada e a duração até a execução agendada seguinte.  Clique no mosaico para ver agendas, atualmente em execução e atualizações concluídas ou para agendar uma nova implementação.|  
<br>  
![Dashboard de resumo de gestão de actualização](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Atualizar a vista de computador do Dashboard de gestão](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Atualizar a vista de pacote do Dashboard de gestão](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Instalar atualizações

Assim que atualizações foram avaliadas para todos os computadores do Windows no seu ambiente, pode ter necessário actualizações instaladas através da criação de uma *Implementação de atualização*.  Uma implementação de atualização é uma instalação agendada do atualizações necessárias para um ou mais computadores do Windows.  Especifique a data e hora para a implementação para além de um computador ou grupo de computadores que devem ser incluídos.  

As atualizações são instaladas pelo runbooks no Azure automatização.  Não é possível visualizar atualmente estes runbooks e não necessitam de qualquer configuração.  Quando é criada uma implementação atualizar, que cria uma agenda no que começa um livro de execuções actualização principal ao tempo especificado para que os computadores incluídos.  Este livro de execuções mestra inicia um livro de execuções subordinado na cada agente do Windows que efetua a instalação das atualizações necessárias.  

### <a name="viewing-update-deployments"></a>Implementações de atualização de visualização

Clique no mosaico de **Implementação de atualizar** para ver a lista de implementações de atualização existentes.  Estes são agrupadas por estado – **agendada**, **Executar**e **concluídas**.<br><br> ![Atualizar a página de agenda híbridas](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

As propriedades apresentadas para cada implementação de atualização são descritas na seguinte tabela.

Propriedade | Descrição|
----------|----------|
Nome | Nome de implementação de atualização.|
Agenda | Tipo de agenda.  *OneTime* atualmente é o valor só é possível.|
Hora de início|Data e hora em que a implementação de atualização está agendada para começar.|
Duração | Número de minutos que a implementação de atualização é permitida para executar.  Se todas as atualizações não estão instaladas dentro esta duração, em seguida, as restantes atualizações tem de aguardar até a implementação de atualização seguinte.|
Servidores de | Número de computadores afetadas por de implementação de atualização.|
Estado | Estado atual de implementação de atualização.<br><br>Valores possíveis são:<br>-Não iniciou<br>-Em execução<br>-Concluído|  

Clique numa implementação de atualizar para ver o ecrã de detalhe que inclui as colunas na tabela seguinte.  Estas colunas não serão preenchidas se a implementação de atualização ainda não começou.<br>

Coluna | Descrição|
----------|----------|
**Resultados de computador**||
Foi concluída com êxito | Apresenta o número de computadores na implementação atualização por Estado.  Clique num estado para executar uma pesquisa de registo devolver que todas atualizar os registos com estado para a implementação de atualizar.|
Estado de instalação do computador| Lista os computadores envolvidos de implementação de atualização e a percentagem de atualizações que instalado com êxito. Clique das entradas para executar uma pesquisa de registo devolver todas as atualizações em falta e críticas.|
**Atualizar os resultados de instância**||
Estado da instalação instância | Lista classificações de atualizações que computadores estão em falta ordenados pelo número de computadores actualizações na categoria em falta. Clique num computador para executar uma pesquisa de registo devolver que todas atualizar os registos nesse computador.|  
<br><br> ![Descrição geral dos resultados de implementação de actualização](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Criar uma implementação de actualização

Crie uma nova implementação de atualização ao clicar no botão **Adicionar** na parte superior do ecrã para abrir a página de **Implementação de actualização de novo** .  Tem de fornecer valores para as propriedades da tabela seguinte.

Propriedade | Descrição|
----------|----------|
Nome | Nome exclusivo para identificar a implementação de atualização.|
Fuso horário | Fuso horário para utilizar para a hora de início.|
Hora de início | Data e hora para iniciar a implementação de atualização.|
Duração | Número de minutos que a implementação de atualização é permitida para executar.  Se todas as atualizações não estão instaladas dentro esta duração, em seguida, as restantes atualizações tem de aguardar até a implementação de atualização seguinte.|
Computadores | Nomes dos computadores ou grupos para incluir a implementação de atualização.  Selecione uma ou mais entradas de lista pendente.|
<br><br> ![Nova página de implementação de actualização](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Intervalo de tempo

Por predefinição, o âmbito de dados analisados na solução de gestão de atualização do é todos os grupos de gestão ligada gerado dentro do último dia de 1. 

Para alterar o intervalo de tempo dos dados, selecione **dados com base** na parte superior do dashboard. Pode selecionar registos criada ou atualizada situada nos últimos 7 dias, 1 dia ou 6 horas. Ou pode selecionar **personalizado** e especificar um intervalo de datas personalizado.<br><br> ![Opção de intervalo de tempo personalizado](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Análise de registos

Solução de gestão de atualização do cria dois tipos de registos no repositório de OMS.

### <a name="update-records"></a>Atualizar os registos

É criado um registo com um tipo de **Atualizar** para cada actualização de que está instalada ou necessário em cada computador. Atualizar registos tem as propriedades da tabela seguinte.

Propriedade | Descrição|
----------|----------|
Tipo | *Actualização*|
SourceSystem | A origem aprovado instalação da atualização.<br>Valores possíveis são:<br>-Microsoft Update<br>-Windows Update<br>-SCCM<br>-Os servidores de Linux (obtidos os gestores de pacote)|
Aprovado | Especifica se a atualização foi aprovada para a instalação.<br> Para os servidores de Linux isto é opcional atualmente como corrigir não seja gerido pelo OMS.|
Classificação para Windows | Classificação da atualização.<br>Valores possíveis são:<br>-Aplicações<br>-Atualizações críticas<br>-Atualizações de definição<br>-Pacotes de funcionalidade<br>-Atualizações de segurança<br>-Os service Packs<br>-Update Rollups<br>-Atualizações|
Classificação para Linux | Cassification da atualização.<br>Valores possíveis são:<br>-Atualizações críticas<br>-Atualizações de segurança<br>-Outras atualizações|
Computador | Nome do computador.|
InstallTimeAvailable | Especifica se a hora de instalação está disponível a partir de outros agentes que instalaram a atualização do mesmo.|
InstallTimePredictionSeconds | Tempo de instalação estimado em segundos com base em outros agentes que instalaram a atualização do mesmo.|
KBID | ID do artigo da BDC que descreva a atualização.|
ManagementGroupName | Nome do grupo de gestão de agentes SCOM.  Para os outros agentes, esta é AOI -<workspace ID>.|
MSRCBulletinID | ID do boletim de segurança da Microsoft que descrevem a atualização.|
MSRCSeverity | Gravidade do boletim de segurança da Microsoft.<br>Valores possíveis são:<br>-Crítica<br>-Importante<br>-Definições de impressão|
Opcional | Especifica se a atualização é opcional.|
Produto | Nome do produto que a atualização destina-se.  Clique em **vista** para abrir o artigo num browser.|
PackageSeverity | Gravidade da vulnerabilidade fixada nesta atualização, à medida que comunicado pelas fornecedores de distro Linux. | 
PublishDate | Data e hora em que a atualização foi instalada.|
RebootBehavior | Especifica se a atualização força reiniciar o computador.<br>Valores possíveis são:<br>-canrequestreboot<br>-neverreboots|
RevisionNumber | Número de revisão da atualização.|
SourceComputerId | GUID para identificar exclusivamente o computador.|
TimeGenerated | Data e hora em que o registo foram atualizados pela última vez.|
Título | Título da atualização.|
ID de actualização | GUID para identificar exclusivamente a atualização.|
UpdateState | Especifica se a atualização é instalada neste computador.<br>Valores possíveis são:<br>-A atualização instalado - está instalada neste computador.<br>-A atualização necessário - não está instalada e é necessária neste computador.|  

<br>
Ao efetuar qualquer procura de registo que devolve registos com um tipo de **atualização** pode selecionar a vista de **atualizações** que apresenta um conjunto de mosaicos resumir as atualizações devolvidas pela pesquisa. Pode clicar nas entradas na **em falta e atualizações aplicadas** e **atualizações necessárias e opcionais** mosaicos para organizar a vista para esse conjunto de atualizações. Selecione a vista de **lista** ou **tabela** para devolver os registos individuais.<br> 

![Vista de atualização de pesquisa de registo com a atualização de tipo de registo](./media/oms-solution-update-management/update-la-view-updates.png)  

Na vista de **tabela** , pode clicar no **KBID** para qualquer registo para abrir um browser com o artigo BDC. Permite-lhe rapidamente Leia sobre os detalhes da atualização específico.<br> 

![Vista de tabela de procura de registo com as atualizações de tipo de registo de mosaicos](./media/oms-solution-update-management/update-la-view-table.png)

Na vista de **lista** , clique em ligação da **vista** ao lado de KBID para abrir o artigo BDC.<br>

![Vista de lista de pesquisa de registo com as atualizações de tipo de registo de mosaicos](./media/oms-solution-update-management/update-la-view-list.png)


###<a name="updatesummary-records"></a>UpdateSummary registos

Um registo com um tipo de **UpdateSummary** é criado para cada computador de agente do Windows. Este registo é atualizado sempre que o computador é pesquisado procurar atualizações. Registos de **UpdateSummary** tem as propriedades da tabela seguinte.

Propriedade | Descrição|
----------|----------|
Tipo | UpdateSummary|
SourceSystem | OpsManager |
Computador | Nome do computador.|
CriticalUpdatesMissing | Número de atualizações críticas em falta no computador.|
ManagementGroupName | Nome do grupo de gestão de agentes SCOM. Para os outros agentes, esta é AOI -<workspace ID>.|
NETRuntimeVersion | Versão do runtime .NET instalado no computador.|
OldestMissingSecurityUpdateBucket | Foi publicado balde para categorizar o tempo de uma vez que a mais antiga em falta actualização de segurança neste computador.<br>Valores possíveis são:<br>-Mais antigos<br>-há 180 dias<br>-há 150 dias<br>-há 120 dias<br>-cerca de 90 dias há<br>-há 60 dias<br>-Aceda 30 dias<br>-Recentes|
OldestMissingSecurityUpdateInDays | Número de dias desde a mais antiga em falta actualização de segurança neste computador foi publicado.|
OsVersion | Versão do sistema operativo instalado no computador.|
OtherUpdatesMissing | Número de outras atualizações em falta no computador.|
SecurityUpdatesMissing | Número de segurança atualizações em falta no computador.|
SourceComputerId | GUID para identificar exclusivamente o computador.|
TimeGenerated | Data e hora em que o registo foram atualizados pela última vez.|
TotalUpdatesMissing |Número total de atualizações em falta no computador.|
WindowsUpdateAgentVersion | Número de versão do Windows Update agent no computador.|
WindowsUpdateSetting | A definição para como o computador irá instalar atualizações importantes.<br>Valores possíveis são:<br>-Desativado<br>-Notificar antes da instalação<br>-Agendada para a instalação|
WSUSServer | Servidor de URL de WSUS se o computador está configurado para utilizar uma.|  

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte fornece as pesquisas de registo de exemplo para atualizar os registos recolhidas por esta solução. 

Consulta | Descrição|
----------|----------|
Todos os computadores com as atualizações em falta | Tipo = atualização UpdateState = necessário opcional = false & #124; Selecione o computador, título, KBID, classificação, UpdateSeverity, PublishedDate|
Atualizações em falta de computador "COMPUTER01.contoso.com" (substitua com o seu próprio nome de computador) | Tipo = atualização UpdateState = necessário opcional = computador falso = "COMPUTER01.contoso.com" & #124; Selecione o computador, título, KBID, produto, UpdateSeverity, PublishedDate|
Todos os computadores com em falta críticos ou atualizações de segurança | Tipo = atualização UpdateState = necessário opcional = false (classificação = "Actualizações de segurança" ou classificação = "Atualizações críticas")|
Atualizações críticas ou de segurança necessárias por máquinas onde atualizações manualmente são aplicadas | Tipo = atualização UpdateState = necessário opcional = false (classificação = "Actualizações de segurança" ou classificação = "Atualizações críticas") no computador {tipo = UpdateSummary WindowsUpdateSetting = Manual & #124; Computador distinto} & #124; KBID distinto|
Eventos de segurança ou máquinas que têm em falta crítico erro obrigatório atualizações | Tipo = evento EventLevelName = erro no computador {tipo = Update (classificação = "Actualizações de segurança" ou classificação = "Atualizações críticas") UpdateState = necessário opcional = false & #124; Computador distinto}|
Todos os computadores com em falta rollups de actualização | Tipo = atualização opcional = classificação falsa = "Update Rollups" UpdateState = necessário & #124; Selecione o computador, título, KBID, classificação, UpdateSeverity, PublishedDate|
Atualizações em falta distintas em todos os computadores | Tipo = atualização UpdateState = necessário opcional = false & #124; Título distinto|
Associação de computador WSUS | Tipo = UpdateSummary & #124; medir count() por WSUSServer|
Configuração de atualização automática | Tipo = UpdateSummary & #124; medir count() por WindowsUpdateSetting|
Computadores com a atualização automática desativada | Tipo = UpdateSummary WindowsUpdateSetting = Manual|  
Lista de todos os computadores Linux quais têm uma atualização de pacote disponível | Tipo = atualização e OSType = Linux e UpdateState! = "Não necessárias" & #124; medir count() pelo computador|
Lista de todos os computadores Linux quais têm uma atualização de pacote disponível que resolve vulnerabilidade críticas ou de segurança | Tipo = atualização e OSType = Linux e UpdateState! = "Não necessárias" e (classificação = "Atualizações críticas" ou classificação = "Actualizações de segurança") & #124; medir count() pelo computador|
Lista de todos os pacotes que têm uma atualização disponível | Tipo = atualização e OSType = Linux e UpdateState! = "Não necessárias"|
Lista de todos os pacotes que têm uma atualização disponível que resolve vulnerabilidade críticas ou de segurança | Tipo = atualização e OSType = Linux e UpdateState! = "Não necessárias" e (classificação = "Atualizações críticas" ou classificação = "Actualizações de segurança")|
Lista de todos os computadores de "Ubuntu" com qualquer atualização disponível | Tipo = atualização e OSType = Linux e OSName = Ubuntu & #124; medir count() pelo computador|

## <a name="next-steps"></a>Próximos passos

- Utilize o registo de pesquisas na [Análise de registo](../log-analytics/log-analytics-log-searches.md) para ver detalhadas atualizar dados.

- [Criar os seus próprios dashboards](../log-analytics/log-analytics-dashboards.md) a mostrar a actualização de conformidade para os computadores geridas.

- [Criar alertas](../log-analytics/log-analytics-alerts.md) quando atualizações críticas são detectadas como faltam computadores ou um computador tem as atualizações automáticas desativadas.  




