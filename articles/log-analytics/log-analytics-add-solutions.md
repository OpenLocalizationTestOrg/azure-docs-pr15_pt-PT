<properties
    pageTitle="Adicionar soluções de análise de registo a partir da Galeria de soluções | Microsoft Azure"
    description="Soluções de análise de registo são que uma coleção de lógica, visualização e aquisição de dados de regras que fornecem métricas deslocadas à volta de uma área do problema específico."
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

# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>Adicionar soluções de análise de registo a partir da Galeria de soluções

Soluções de análise de registo são uma colecção de **lógica**, **visualização** e **regras de aquisição de dados** que fornecem métricas deslocadas à volta de uma área do problema específico. Soluções de listas neste artigo suportada a análise de registo e mostra-lhe como adicionar e removê-los através da Galeria de soluções.

Soluções permitem mais aprofundadas informações para:

- ajudar a investigar e resolver problemas de operacionais mais rápido
- recolher e correlacionar vários tipos de dados de computador
- ajudar a ser pro-activos com atividades como planeamento da capacidade, relatórios de estado de patches e auditoria de segurança.


>[AZURE.NOTE] Análise de registo inclui a funcionalidade de registo de pesquisa, pelo que não necessita de instalar uma solução para ativá-la. No entanto, pode obter dados, pesquisas sugeridas e das visualizações otimizadas ao adicionar soluções da Galeria de soluções.

Depois de adicionar uma solução, os dados são recolhidos a partir dos servidores na sua infraestrutura e enviados para o serviço OMS. Transformação pela OMS serviço normalmente demora alguns minutos a uma hora. Depois do serviço processa os dados, pode vê-lo OMS.

Pode facilmente remover uma solução quando já não é necessária. Quando remove uma solução, os respetivos dados não são enviados para OMS, que pode reduzir a quantidade de dados utilizadas pelo seu quota diária, se tiver um.


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Soluções suportadas pelo Microsoft monitorização Agent

Neste momento, os servidores que estão ligados à OMS utilizando o Microsoft monitorização Agent podem utilizar a maioria das soluções disponíveis, incluindo:

- Avaliação do Active Directory
- Gestão de alerta (sem alertas SCOM)
- Antimalware
- Registo de alterações
- Segurança
- Avaliação de SQL
- Actualizações do sistema

No entanto, as seguintes soluções são *não* são suportadas com o agente de monitorização da Microsoft e exigir agente de Gestor de operações de centro do sistema (SCOM).

- Gestão de alerta (incluindo alertas SCOM)
- Gestão de capacidade
- Avaliação de configuração

Consulte [Ligar o Gestor de operações para a análise de registo](log-analytics-om-agents.md) para informações sobre como ligar o agente SCOM a análise de registo.

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>Para adicionar uma solução através da Galeria de soluções

1. Na página Descrição geral da OMS, clique no mosaico da **Galeria de soluções** .    
    ![Galeria de soluções](./media/log-analytics-add-solutions/sol-gallery.png)
2. Na página Galeria de soluções OMS, saiba mais sobre cada solução disponível. Clique no nome da solução que pretende adicionar a OMS.
3. Na página para a solução que tenha escolhido, informações detalhadas sobre a solução são apresentadas. Clique em **Adicionar**.
4. Um novo mosaico para a solução que tenha adicionado é apresentado a descrição geral do página nas OMS e pode começar a utilizá-la depois do serviço OMS processa os seus dados.

## <a name="to-configure-solutions"></a>Para configurar soluções
1. Terá de configurar algumas soluções. Por exemplo, terá de configurar automatização, Azure Site recuperação e cópias de segurança antes de poder utilizá-los.
2. Para qualquer uma dessas soluções, clique em respetivo mosaico na página Descrição geral.  
    ![Configurar solução](./media/log-analytics-add-solutions/configure-additional.png)
3. Em seguida, configure a solução com as informações necessárias e, em seguida, clique em **Guardar**.  
    ![Configurar solução](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>Para remover uma solução utilizando a Galeria de soluções

1. Na página Descrição geral da OMS, clique no mosaico **Definições** .
2. Na página Definições, no separador soluções, clique em **Remover** para a solução que pretende remover.
3. Na caixa de diálogo de confirmação, clique em **Sim** para remover a solução.

## <a name="data-collection-details-for-oms-features-and-solutions"></a>Detalhes de recolha de dados para as funcionalidades OMS e soluções

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para funcionalidades OMS e soluções. Agentes diretos e agentes SCOM são essencialmente os mesmos, no entanto, o agente direto inclui funcionalidade adicional para permiti-lo ligar à área de trabalho OMS e encaminhar através de um proxy. Se utilizar um agente do SCOM, tem de ser alvo como agente OMS para comunicar com OMS. SCOM nesta tabela, são OMS agentes que estão ligados à SCOM. Consulte o artigo [Ligar o Gestor de operações para a análise de registo](log-analytics-om-agents.md) para obter informações sobre como ligar o seu ambiente existente do SCOM à OMS.

>[AZURE.NOTE] O tipo de agente que utilizar determina como os dados são enviados para OMS, com as seguintes condições:

- Pode utiliza o agente direto ou um agente do anexado SCOM OMS.
- Quando é necessária uma SCOM, dados de agente SCOM para a solução sempre são enviados para OMS utilizando o grupo de gestão de SCOM. Para além disso, quando é necessária uma SCOM, apenas o agente SCOM é utilizado pela solução.
- Quando não é necessária SCOM e a tabela que mostra que dados de agente SCOM são enviados para OMS utilizando o grupo de gestão, em seguida, SCOM agente dados são sempre enviados para OMS utilizar grupos de gestão. Agentes diretos ignoram o grupo de gestão e enviar os respetivos dados diretamente para OMS.
- Ao dados do agente SCOM não são enviados utilizando um grupo de gestão, em seguida, os dados são são enviados diretamente para OMS — ignorando o grupo de gestão.


|tipo de dados| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|---|
|Avaliação de AD|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 dias|
|Estado de replicação de AD|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 dias|
|Alertas (Nagios)|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|em chegada|
|Alertas (Zabbix)|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minuto|
|Alertas (Gestor de operações)|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minutos|
|Antimalware|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| hora a hora|
|Gestão de capacidade|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| hora a hora|
|Registo de alterações|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| hora a hora|
|Registo de alterações|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|hora a hora|
|Avaliação de configuração (Advisor legado)|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| duas vezes por dia|
|ETW|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Registos do IIS|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Chave cofres|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Gateways de aplicação de rede|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Grupos de segurança de rede|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Office 365|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|notificação de|
|Contadores de desempenho|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|como agendada, mínimo de 10 segundos|
|Contadores de desempenho|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|como agendada, mínimo de 10 segundos|
|Serviço ferro|Windows|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Avaliação de SQL|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 dias|
|SurfaceHub|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|em chegada|
|Syslog|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|a partir do armazenamento Azure: dez minutos. a partir do agente: à chegada|
|Actualizações do sistema|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| pelo menos de 2 vezes por dia e 15 minutos depois de instalar uma atualização|
|Registos de eventos de segurança do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)| para o armazenamento do Azure: 10 min; para que o agente: à chegada|
|Registos de firewall do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)| em chegada|
|Registos de eventos do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| para o armazenamento do Azure: 1 min; para que o agente: à chegada|
|Dados de esboço|Windows (2012 R2 / 8.1 ou posterior)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)|![N](./media/log-analytics-add-solutions/oms-bullet-red.png)| minuto 1|

## <a name="log-analytics-preview-solutions-and-features"></a>Funcionalidades e soluções de pré-visualizar a análise de registo

Executando um serviço e seguir as práticas de devops estamos conseguir com os clientes para desenvolver as funcionalidades e soluções de parceiros.

Durante a pré-visualização privada Vamos dar um pequeno grupo de acesso de clientes para uma aplicação antecipada da funcionalidade ou solução para obter comentários e efetuar melhorias. Esta aplicação antecipada tem mínimas funcionalidades e capacidades operacionais.

Nosso objetivo é tentar coisas rapidamente para que os podermos localizar o que funciona e o que não funciona. Vamos iteração este processo até o comentários a partir dos clientes de pré-visualização privada informa-nos estamos prontos para obter uma pré-visualização pública.

Durante a pré-visualização pública, oferecemos da funcionalidade ou uma solução disponível para todos os utilizadores para obter mais comentários e validar o nosso dimensionamento e eficiência. Nesta fase:

- Funcionalidades de pré-visualização serão apresentada no separador Definições e podem ser ativadas por qualquer utilizador
- Soluções de pré-visualização podem ser adicionadas através da galeria ou utilizar um script publicado

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Que devem saber sobre as funcionalidades de pré-visualizar e soluções?

Vamos celebrar sobre as novas funcionalidades e soluções e podemos adora trabalhar consigo desenvolvê-los.

Pré-visualização funcionalidades e soluções não são direita para todas as pessoas no entanto, pelo que antes de pedir para aderir a uma pré-visualização privada ou ativar uma pré-visualização pública Certifique-se de que está a trabalhar OK com algo que está em desenvolvimento.

Ao ativar uma funcionalidade de pré-visualização através do portal do que será ver um aviso para lembrá-lo que a funcionalidade está na pré-visualização.

#### <a name="for-both-private-and-public-preview"></a>Para pré-visualização do *públicos* e *privados*

A seguinte aplica-se para a pré-visualizações públicas e privadas:

- Coisas sempre poderão não a funcionar corretamente.
  - Problemas de intervalo sejam um incómodo secundário através de para algo que não está a funcionar em todos os
- Existe a possibilidade para a pré-visualização para ter um impacto negativo nos sistemas / ambiente
  - Vamos experimentar evitar coisas negativas passa para os sistemas de que está a utilizar com OMS mas por vezes inesperadas coisas ocorrer
- Perda de dados / poderão ocorrer danos
- Vamos pode a pedir-lhe recolher registos de diagnóstico ou outros dados para ajudar a resolver problemas
- A funcionalidade ou a solução poderão ser removida (temporariamente ou permanentemente)
  - Com base no nossos learnings durante a pré-visualização que podemos pode decidir não libertar a funcionalidade ou uma solução
- Pré-visualizações poderão não funcionar ou não poderão ter sido testados com todas as configurações e, a Microsoft poderá limitar:
  - Os sistemas operativos que podem ser utilizados (por exemplo, uma funcionalidade pode apenas serem aplicadas a Linux enquanto na pré-visualização)
  - O tipo de agente (MMA, SCOM) que pode ser utilizado (por exemplo, uma funcionalidade poderá não funcionar com SCOM enquanto na pré-visualização)  
- Soluções de pré-visualizar e funcionalidades não estejam tapadas pelo contrato de nível de serviço
- A utilização das funcionalidades de pré-visualização será implicam custos de utilização
- Funcionalidades ou capacidades de que precisa para a funcionalidade / solução para ser útil poderá estar em falta ou não concluída
- Funcionalidades / soluções poderão não estar disponíveis em todas as regiões
- Funcionalidades / soluções poderão não estar localizadas
- Funcionalidades / soluções podem ter um limite no número de clientes ou dispositivos que podem utilizá-lo
- Poderá ter de utilizar scripts para executar a configuração de e para ativar a funcionalidade/solução
- A interface de utilizador (IU) será incompleta e podem ser alteradas a partir de um dia para outro
- Pré-visualizações públicas podem não ser adequado para a sua produção / crítico sistemas

#### <a name="for-private-preview"></a>Pré-visualização *privado* do

Para além de itens acima, é específico de pré-visualizações privadas o seguinte:

- Vamos esperar que forneça-nos comentários sobre a sua experiência para que recomendamos pode melhorar a funcionalidade/solução
- Poderemos contactá-lo para comentários com inquéritos, chamadas telefónicas ou correio electrónico
- Coisas sempre não irá funcionar corretamente
- Vamos poderá necessitar de um contrato não divulgação (NDA) para participação ou podem incluir conteúdo confidencial
  - Antes de blogues, tweeting ou caso contrário, comunicar com terceiros verificar com o programa gestor responsável pela pré-visualização para compreender restrições à divulgação
- Não são executadas produção / crítico sistemas


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Como posso obter acesso às funcionalidades de pré-visualização privado e soluções?

Vamos convidar clientes para privado pré-visualizações através de várias maneiras diferentes, consoante a pré-visualização.

- Responder ao inquérito mensal do cliente e que dê-nos permissão para dar seguimento consigo melhora as suas hipóteses de que está a ser convidado para uma pré-visualização privada.
- A equipa de conta Microsoft, pode nomear lhe.
- Pode inscrever-se com base em detalhes publicadas no twitter [msopsmgmt](https://twitter.com/msopsmgmt)
- Pode inscrever-se com base em eventos de Comunidade partilhado detalhes – procure-na reunir ups, conferências e em Comunidades onlinehttps.


## <a name="next-steps"></a>Próximos passos

- [Registos de pesquisa](log-analytics-log-searches.md) para ver informações detalhadas recolhidas pela soluções.
