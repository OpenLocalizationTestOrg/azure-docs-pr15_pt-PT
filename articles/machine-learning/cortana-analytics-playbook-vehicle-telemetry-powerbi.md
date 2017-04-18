<properties 
    pageTitle="Instruções de configuração do modelo de solução de análise do veículo telemetria obter Dashboard | Microsoft Azure" 
    description="Utilizar as funcionalidades de análise de Cortana para obter informações em tempo real e aspeto do Office no estado de funcionamento do veículo e condução hábitos." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-template-powerbi-dashboard-setup-instructions"></a>Instruções de configuração ao obter Dashboard do modelo de solução veículo telemetria analytics

Este **menu** contém ligações para os capítulos neste playbook. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


A solução de análise de telemetria veículo demonstra como distribuição de carro, fabricantes de automóveis e empresas de seguros podem tirar partido as capacidades de informações da empresa de Cortana para obter informações em tempo real e aspeto do Office no estado de funcionamento do veículo e uma experiência de condução hábitos a melhorias na unidade na área do cliente, R & D e campanhas de marketing. Este documento contém instruções passo a passo sobre como pode configurar a obter relatórios e dashboards assim que a solução é implementada na sua subscrição. 


## <a name="prerequisites"></a>Pré-requisitos
1.  Implementar a solução de análise de telemetria veículo ao navegar para [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)  
2.  [Instalar o ambiente de trabalho do Microsoft Power BI](http://www.microsoft.com/download/details.aspx?id=45331)
3.  Uma [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/). Se não tiver uma subscrição do Azure, introdução ao Azure subscrição gratuita
4.  Conta Microsoft PowerBI
    

## <a name="cortana-intelligence-suite-components"></a>Componentes de conjunto de aplicações do Cortana informações da empresa
Como parte do modelo de solução de análise de telemetria de veículo, os seguintes serviços Cortana Intelligence são implementados na sua subscrição.

- **Concentradores de evento** para ingesting milhões de eventos de telemetria veículo para Azure.
- S **Da cadeia analítico**para obterem informações em tempo real no estado de funcionamento do veículo e persistir esses dados para o armazenamento a longo prazo para análise de lote mais rica.
- **Formação de máquina** de detecção anomalia em tempo real e processamento batch para obter informações de aspeto do Office.
- **HDInsight** é utilizadas para transformar dados à escala
- **Dados fábrica** trata orchestration, agendamento, gestão de recursos e monitorização do pipeline de processamento batch.

**Power BI** dá-esta solução um dashboard formatado para dados em tempo real e visualizações de análise de aspeto do Office. 

A solução utiliza duas origens de dados diferentes: **sinais de veículo simulada e conjunto de dados de diagnóstico** e **catálogo veículo**.

Um simulator de telemática veículo é incluído como parte desta solução. Este procedimento emite informações de diagnóstico e sinais correspondente para o estado do veículo e condução padrão num determinado ponto no tempo. 

O catálogo de veículo é um conjunto de dados de referência que contenham VIN para o mapeamento de modelo


## <a name="powerbi-dashboard-preparation"></a>Preparação do Dashboard de obter

### <a name="deployment"></a>Implementação

Assim que a implementação estiver concluída, deverá ver o diagrama seguinte com todos estes componentes marcados verde. 

- Para navegar para os serviços correspondentes para validar se todas estas implementou com êxito, clique na seta no canto superior direito de nós verdes.
- Para transferir o pacote de simulator de dados, clique na seta no canto superior direito no nó **Veículo telemática Simulator** . Guarde e extrair os ficheiros localmente no seu computador. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

Agora, está pronto para configurar o dashboard obter com visualizações para obter em tempo real e aspeto do Office informações sobre o estado de funcionamento do veículo e condução hábitos. Demora cerca de 45 minutos para uma hora para criar todos os relatórios e configurar o dashboard. 


### <a name="setup-power-bi-real-time-dashboard"></a>Configurar o Dashboard em tempo real do Power BI

**Gerar simulados dados**

1. No seu computador local, vá para a pasta onde o pacote de veículo telemática Simulator que extraída![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2.  Execute a aplicação ***CarEventGenerator.exe***.
3.  Este procedimento emite informações de diagnóstico e sinais correspondente para o estado do veículo e condução padrão num determinado ponto no tempo. Isto é publicado numa instância do Azure evento concentrador que esteja configurado como parte da sua implementação.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
     
**Iniciar a aplicação de dashboard em tempo real**

A solução inclui uma aplicação que gera um dashboard em tempo real no obter. Esta aplicação recebe uma instância do concentrador de evento, a partir do qual a análise da cadeia publica os eventos continuamente. Para todos os eventos que recebe esta aplicação, processa os dados utilizando um ponto final pontuação de formação de máquina pedido-resposta. O conjunto de dados resultante é publicado no push a obter APIs para visualização. 

Para transferir a aplicação:

1.  Clique no nó obter na vista de diagrama e clique em **Transferir a aplicação de Dashboard em tempo real**' ligação no painel de propriedades.![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2.  Extrair e guarde a aplicação localmente![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.  Executar a aplicação **RealtimeDashboardApp.exe**
4.  Fornecer credenciais válidas do Power BI, início de sessão e clique em **Aceitar**
    
    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
    
    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)


### <a name="configure-powerbi-reports"></a>Configurar relatórios de obter
Os relatórios em tempo real e o dashboard demorar cerca de 30-45 minutos para concluir. Navegue até [http://powerbi.com](http://powerbi.com) e início de sessão.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

É gerado um novo conjunto de dados no Power BI. Clique no conjunto de dados **ConnectedCarsRealtime** .

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Guarde o relatório em branco utilizando **Ctrl + s**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Fornece o nome do relatório *veículo telemetria Analytics em tempo real - relatórios*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Relatórios em tempo real
Existem três relatórios em tempo real nesta solução:

1.  Veículos numa operação de
2.  Veículos que exige o manutenção
3.  Estatísticas de estado de funcionamento de veículos

Pode escolher configurar os três relatórios de em tempo real ou parar após todas as fases e avançar para a secção seguinte de configurar os relatórios do lote. Recomendamos que para criar todos os relatórios de três para visualizar as informações completos do caminho da solução em tempo real.  

### <a name="1-vehicles-in-operation"></a>1. veículos numa operação de
  
Faça duplo clique em **página 1** e mude o nome para a "Veículos numa operação de"  
    ![Ligado carros - veículos numa operação de](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Selecione campo **vin** de **campos** e escolha o tipo de visualização, como **"Cartão"**.  

Visualização do cartão é criada como apresentado na figura.  
    ![Carros ligados - selecionar vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Clique na área em branco para adicionar nova visualização.  

Selecione **Cidade** e **vin** de campos. Alterar a visualização para **"Mapa"**. Arraste **vin** na área valores. Arraste **Cidade** de campos para a área de **legenda** .   
    ![Ligado carros - visualização do cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Selecione a secção **formato** de **visualizações**, clique em **título** e alterar o **texto** para **"Veículos numa operação de por cidade"**.  
    ![Ligado carros - veículos numa operação de por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

Visualização final parece como apresentado na figura.    
    ![Carros ligados - Final visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Clique na área em branco para adicionar nova visualização.  

Selecione **Localidade** e **vin**, altere o tipo de visualização para **Gráfico de colunas agrupadas**. Certifique-se de campo **Cidade** na **área eixo** e **vin** na **área de valor**  

Ordenar gráfico **"Contagem de vin"**  
    ![Carros ligados - contagem de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Alterar o **título** de gráfico para **"Veículos numa operação de por cidade"**  

Clique na secção de **Formatar** , em seguida, selecione **Cores de dados**, clique na **"A fazer"** para **Mostrar tudo**  
    ![Carros ligados - Mostrar todas as cores de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Altere a cor de localidade individual, clicando no ícone de cor.  
    ![Ligado carros - alterar cores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Clique na área em branco para adicionar nova visualização.  

Selecione o **Gráfico de colunas agrupadas** visualização de visualizações, arraste o campo **Cidade** na área **eixo** , de **modelo** na área de **legenda** e **vin** na área de **valor** .  
    ![Carros ligados - gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Carros ligados - compor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Reorganize todos os efeitos visuais nesta página como apresentado na figura.  
    ![Carros ligados - visualizações](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Configurou o relatório em tempo real "Veículos numa operação de" com êxito. Pode continuar a criar o relatório em tempo real seguinte ou parar aqui e configurar o dashboard. 

### <a name="2-vehicles-requiring-maintenance"></a>2. veículos que exige o manutenção
  
Clique em ![adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar um novo relatório, mude o nome **"veículos que exige o** manutenção"

![Carros ligados - veículos que exige o manutenção](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Selecione campo **vin** e alterar o tipo de visualização para **cartão**.  
    ![Carros ligados - visualização do cartão de Vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

Temos um campo denominado "MaintenanceLabel" o conjunto de dados. Este campo pode ter um valor de "0" ou "1"." É definida pelo modelo de aprendizagem do Azure máquina aprovisionado como parte da solução e integrado com o caminho em tempo real. O valor "1" indica que necessita de um veículo manutenção. 

Para adicionar um filtro de **Nível de página** para mostrar dados veículos, o que são que exige o manutenção: 

1. Arraste o campo **"MaintenanceLabel"** para **Filtros ao nível de página**.  
![Carros ligados - filtros ao nível da página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Clique na **Filtragem básica** menu presente na parte inferior do filtro de nível de página MaintenanceLabel.  
![Ligado carros - a filtragem básica](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  

3.  Definir o valor de filtro para **"1"**    
![Carros ligados - o valor do filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  


Clique na área em branco para adicionar nova visualização.  

Selecione o **Gráfico de colunas agrupadas** visualizações  
![Carros ligados - visualização do cartão de Vind](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Carros ligados - gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Arraste o campo **modelo** para área **eixo** , **Vin** à área de **valor** . Em seguida, ordene visualização por **contagem de vin**.  Alterar o **título** de gráfico para **"Veículos que exige o manutenção pelo modelo"**  

Arrastar campos **vin** para **Saturação da cor** presentes nos **campos** ![campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) secção do separador de **visualização**  
![Carros ligados - saturação da cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Alterar **As cores de dados** nas visualizações da secção de **formato**  
Alterar a cor de mínimo: **F2C812**  
Alterar a cor de máximo: **FF6300**  
![Ligado carros - alterações de cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Ligado carros - novas cores de visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Clique na área em branco para adicionar nova visualização.  

Selecione o **gráfico de colunas agrupadas** visualizações, arraste **vin** campo para área de **valor** , arraste o campo **Cidade** para área de **eixo** . Ordenar o gráfico **"Contagem de vin"**. Alterar o **título** de gráfico para **"Veículos que exige o manutenção por cidade"**   
![Carros ligados - veículos que exige o manutenção por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Clique na área em branco para adicionar nova visualização.  

Selecione a visualização do **Cartão de linha com várias** visualizações, arraste o **modelo** e **vin** para a área de **campos** .  
![Carros ligados - cartão com várias linha](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

Reorganizar todas da visualização, o relatório final será apresentada da seguinte forma:  
![Carros ligados - cartão com várias linha](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Configurou o relatório em tempo real "Veículos que exige o manutenção" com êxito. Pode continuar a criar o relatório em tempo real seguinte ou parar aqui e configurar o dashboard. 

### <a name="3-vehicles-health-statistics"></a>3. estatísticas de estado de funcionamento de veículos
  
Clique em ![adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar novo relatório, mude o nome **"** estatísticas de estado de funcionamento do veículos"  

Selecione a visualização de **indicador** de visualizações e, em seguida, arraste o campo de **velocidade** para áreas de **valor, o valor mínimo, o valor máximo** .  
![Carros ligados - cartão com várias linha](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Alterar a agregação predefinida da **velocidade** na **área de valor** para **médio** 

Alterar a agregação predefinida da **velocidade** na **área de mínimo** no **mínimo**

Alterar a agregação predefinida do **velocidade** na **área máximo** a **máximo**

![Carros ligados - cartão com várias linha](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Mudar o nome do **Título de indicador** para **"Velocidade média"** 
 
![Carros ligados - indicador](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Clique na área em branco para adicionar nova visualização.  

Da mesma forma, adicione um **indicador** para o **motor média Adamastor**, **combustível média**e **média motor temperada**.  

Alterar a agregação predefinida de campos na cada indicador como por acima os passos em **"Velocidade média"** avaliar.

![Carros ligados - indicadores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Clique na área em branco para adicionar nova visualização.

Selecione **linha e de gráfico de colunas agrupadas** visualizações, em seguida, arraste o campo **Cidade** para o **Eixo partilhados**, arraste **velocidade**, **tirepressure e engineoil campos** na área de **Valores de coluna** , altere o tipo de agregação para **média**. 

Arraste o campo **engineTemperature** para área **Valores da linha** , altere o tipo de agregação para **média**. 

![Carros ligados - visualizações campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Altere o **título** do gráfico para **"velocidade média, pneus pressão, Adamastor motor e temperatura motor"**.  

![Carros ligados - visualizações campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Clique na área em branco para adicionar nova visualização.

Selecione a visualização de **Treemap** de visualizações, arraste o campo de **modelo** para a área de **grupo** e arraste o campo **MaintenanceProbability** para a área **valores** .

Altere o **título** do gráfico para **"Modelos de veículo que exige o manutenção"**.

![Ligado carros - alterar o título do gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Clique na área em branco para adicionar nova visualização.

Selecione o **gráfico de barras empilhadas a 100%** de visualização, arraste o campo **Localidade** para a área de **eixo** e arraste **MaintenanceProbability**, **RecallProbability** campos para a área de **valor** .

![Carros ligados - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Clique em **Formatar**, selecione **Cores de dados**e defina a cor de **MaintenanceProbability** para o valor **"F2C80F"**.

Altere o **título** do gráfico para **"probabilidade de veículo manutenção & resgatar por cidade"**.

![Carros ligados - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Clique na área em branco para adicionar nova visualização.

Selecione o **Gráfico de área** de visualização de visualizações, arraste o campo de **modelo** para a área de **eixo** e arraste os campos **engineOil, tirepressure, velocidade e MaintenanceProbability** para a área de **valores** . Altere o tipo de agregação para **"Média"**. 

![Ligado carros - alterar tipo de agregação](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Altere o título do gráfico para **"Adamastor do motor de média, ficar cansado probabilidade pressão, velocidade e manutenção pelo modelo"**.

![Ligado carros - alterar o título do gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Clique na área em branco para adicionar nova visualização:

1. Selecione o **Gráfico de dispersão** visualização de visualizações.
2. Arraste o campo de **modelo** para a área de **Detalhes** e a **legenda** .
3. Arraste o campo de **combustível** para a área de **eixo x** , altere a agregação para **média**.
4. Arraste **engineTemparature** para **área de eixo dos YY**, altere a agregação para **médio**
5. Arraste o campo **vin** para a área de **tamanho** .


![Carros ligados - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Altere o **título** do gráfico para **"Médias de combustível, temperatura do motor pelo modelo"**.

![Ligado carros - alterar o título do gráfico](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

O relatório final terá o seguinte aspeto conforme apresentado abaixo.

![Relatório de carros Final ligado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Visualizações de PIN dos relatórios ao dashboard em tempo real
  
Crie um dashboard em branco ao clicar no ícone de adição junto a Dashboards. Pode atribuir um nome-"Telemetria a análise de um veículo"

![Dashboard de carros ligada](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Afixe a visualização dos relatórios acima para o dashboard. 
 
![Dashboard de carros ligada](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

O dashboard deverá ter o aspeto da seguinte forma quando são criados os três relatórios e as visualizações correspondentes são afixadas ao dashboard. Se não tiver criado todos os relatórios, o dashboard poderá estar diferente. 

![Dashboard de carros ligada](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Parabéns! Criou o dashboard em tempo real com êxito. Como continuar a executar CarEventGenerator.exe e RealtimeDashboardApp.exe, deverá ver atualizações direto no dashboard. Deverá demorar cerca de 10 a 15 minutos para concluir os passos seguintes.

 
##  <a name="setup-power-bi-batch-processing-dashboard"></a>Dashboard do Power BI lote processamento de configuração

>[AZURE.NOTE] Demora cerca de duas horas (a partir da conclusão de implementação) para pipeline de processamento batch de fim para fim concluir a execução e processar um ano vale de dados gerados. Por isso, aguarde que a transformação concluir antes de prosseguir com os passos seguintes. 

**Transferir o ficheiro de estruturador obter**

-   Um ficheiro de estruturador obter pré-configurada é incluído como parte da implementação
-   Clique no nó de obter na vista de diagrama e clique em **Transferir o ficheiro estruturador obter** ligação no painel de propriedades![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)

-   Guardar localmente

**Configurar relatórios de obter**

-   Abra o ficheiro Estruturador 'VehicleTelemetryAnalytics - Report.pbix de ambiente de trabalho' com obter ambiente de trabalho. Se não já possui, instale o ambiente de trabalho de obter a partir do [ambiente de trabalho de obter instalar](http://www.microsoft.com/download/details.aspx?id=45331). 

-   Clique em **Editar consultas**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

- Faça duplo clique sobre a **origem**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- Atualize a cadeia de ligação de servidor com o servidor do Azure SQL que tem aprovisionado como parte da implementação. Clique no nó do Azure SQL no diagrama e ver o nome do servidor do painel de propriedades.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

- Deixe a **base de dados** como *connectedcar*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Clique em **OK**.
- Irá ver separador **credenciais do Windows** selecionado por predefinição, altere-o para **as credenciais de base de dados** clicando no separador **base de dados** à direita.
- Fornece o **nome de utilizador** e a **palavra-passe** da sua base de dados do SQL Azure que foi especificada durante a sua configuração de implementação.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Clique em **Ligar**
- Repita os passos acima para cada uma das três consultas restantes presentes no painel direito e, em seguida, atualize os detalhes de ligação de origem de dados.
- Clique em **Fechar e carregar**. Conjuntos de dados do Power BI Desktop ficheiro estão ligados às tabelas de base de dados do SQL Azure.
- **Fechar** Ficheiro do Power BI Desktop.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Clique em botão **Guardar** para guardar as alterações. 
 
Agora ter configurado todos os relatórios correspondente para o caminho de processamento batch da solução. 


## <a name="upload-to-powerbicom"></a>Carregar para *powerbi.com*
 
1.  Navegue para o portal de web obter na http://powerbi.com e inicie sessão.
2.  Clique em **obter dados**  
3.  Carregue o ficheiro do Power BI Desktop.  
4.  Para carregar, clique em **-obter dados > obter ficheiros -> Ficheiro Local**  
5.  Navegue para a **"VehicleTelemetryAnalytics – Report.pbix ambiente de trabalho"**  
6.  Assim que o ficheiro é carregado, será direccionado para o seu espaço de trabalho do Power BI.  

Um conjunto de dados, relatório e um dashboard em branco, serão criados por si.  
 

Gráficos PIN para o dashboard existente **a análise de telemetria de um veículo** no **Power BI**. Clique em dashboard em branco criado anteriormente e, em seguida, navegue para o clique de secção **relatórios** o relatório carregado recentemente.  

![PowerBI.com de telemetria veículo](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 


**Tenha em atenção que o relatório tem seis páginas:**  
Página 1: Densidade de veículo  
2 de página: Estado de funcionamento de veículo em tempo real  
Página 3: Agressivamente esforço veículos   
Página 4: Recordar veículos  
Página 5: Combustível veículos eficazmente com  
6 de página: Logótipo da Contoso  

![PowerBI.com de carros ligada](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**A partir de página 3**, afixar o seguinte:  

1.  Contagem de VIN  
    ![PowerBI.com de carros ligada](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 

2.  Agressivamente pelo esforço veículos pelo modelo – gráfico de cascata  
    ![Veículo telemetria - Pin gráficos 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**A partir de 5 de página**, afixar o seguinte: 
 
1.  Contagem de vin    
    ![Veículo telemetria - Pin gráficos 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2.  Veículos eficientes de combustível por modelo: gráfico de colunas agrupadas  
    ![Veículo telemetria - Pin gráficos 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**A partir de página 4**, afixar o seguinte:  

1.  Contagem de vin  
    ![Veículo telemetria - Pin gráficos 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.  Veículos resgatados por cidade, modelo: Treemap  
    ![Veículo telemetria - Pin gráficos 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**A partir de 6 de página**, afixar o seguinte:  

1.  Logótipo contoso motores  
    ![Veículo telemetria - Pin gráficos 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organizar o dashboard**  

1.  Navegue para o dashboard
2.  Paire o cursor sobre cada gráfico e mudar o nome-la com base em nomenclatura fornecidos na imagem abaixo dashboards completa. Também mova os gráficos para ver como o dashboard abaixo.  
    ![Veículo telemetria - organizar Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
    ![PowerBI.com de telemetria veículo](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3.  Se tiver criado todos os relatórios tal como mencionado neste documento, o dashboard concluído final deverá ter um aspeto como na figura seguinte. 

![Veículo telemetria - organizar Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Parabéns! Criou com êxito os relatórios e dashboards para obter o aspeto do Office, em tempo real e batch informações sobre o estado de funcionamento do veículo e condução hábitos.  
