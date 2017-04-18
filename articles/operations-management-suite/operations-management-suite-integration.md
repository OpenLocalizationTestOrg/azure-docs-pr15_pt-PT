<properties
   pageTitle="Integrar com o conjunto de gestão de operações (OMS) | Microsoft Azure"
   description="Além de utilizar as funcionalidades padrão do OMS, pode integrar o-lo com outras aplicações de gestão e uma experiência de serviços para fornecer um ambiente híbrido de gestão, para fornecer exclusivos do seu ambiente de cenários de gestão personalizada ou para fornecer uma gestão personalizada para os seus clientes.  Este artigo fornece uma descrição geral de diferentes opções de integração com OMS e ligações para artigos fornecer informações técnicas detalhadas."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="bwren" />

# <a name="integrating-with-operations-management-suite-oms"></a>Integrar com o conjunto de gestão de operações (OMS)

Conjunto de aplicações de gestão de operações é baseado na nuvem IT solução de gestão da Microsoft de que o ajuda a gerir e proteger o seu no local e na nuvem infraestrutura.  Além de utilizar as funcionalidades padrão do OMS, pode integrar o-lo com outras aplicações de gestão e uma experiência de serviços para fornecer um ambiente híbrido de gestão, para fornecer exclusivos do seu ambiente de cenários de gestão personalizada ou para fornecer uma gestão personalizada para os seus clientes.  Este artigo fornece uma descrição geral de diferentes opções de integração com os serviços OMS e ligações para artigos fornecendo informações detalhadas de técnicas. 



## <a name="log-analytics"></a>Análise de registo
Dados de gestão recolhidos pelo registo de análise são armazenados num repositório de que está alojado no Azure.  Todos os dados armazenados no repositório está disponível em pesquisas de registo que disponibiliza a análise rápida em extremamente grandes quantidades de dados.  Requisitos de integração poderá para preencher o repositório por novos dados disponibilizar para uma análise de ou para extrair dados no repositório para fornecer uma nova visualização ou para integrar com outra ferramenta de gestão.

Cada porção de dados no repositório está armazenada como um registo.  Quando povoar o repositório, deve fornecer aos utilizadores com o tipo de registo que utiliza a solução e uma descrição das suas propriedades.  Quando obtém dados, é necessário estas informações sobre os dados que está a trabalhar com.

![Preencher o repositório OMS](media/operations-management-suite-integration/repository.png)


### <a name="populate-the-log-analytics-repository"></a>Preencher o repositório de análise de registo
Existem vários métodos para preencher o repositório OMS.  O método que utilizar irá depender de fatores como onde se encontra a origem de dados, o formato dos dados e o que precisa para suporte de clientes.  Assim que os dados são armazenados no repositório, não fizer diferença como foram recolhido.

As seguintes secções descrevem as diferentes opções para preencher o repositório OMS.

#### <a name="connected-sources-and-data-sources"></a>Ligada origens e origens de dados 
Origens ligadas são as localizações onde podem obter dados para o repositório OMS.  Origens de dados e soluções executadas em ligado origens e definem os dados específicos que são recolhidos.  Se a sua aplicação escreve dados para uma das seguintes origens de dados, pode recolhê-lo ao configurar a origem de dados.  Por exemplo, se a sua aplicação cria eventos do sistema, em seguida, estes podem ser recolhidos pela origem de dados do sistema num agente do Linux.

- [Origens de dados no registo de análise](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Soluções

Soluções expandem as funcionalidades do OMS.  Uma solução poderá recolher dados de origem de ligada ou -pode efetuar análise de nos já recolhidos no repositório de registos.  Todas as soluções fornecidas pela Microsoft tem um artigo individual que fornece os detalhes sobre os dados que recolhe-lo.

- [Soluções de análise de registo](../log-analytics/log-analytics-add-solutions.md)



#### <a name="http-data-collector-api"></a>HTTP recolha API

Registo de análise HTTP dados Recolectores API é uma REST API que permite-lhe adicionar dados JSON para o repositório de análise de registo.  Pode tirar partido esta API quando tiver uma aplicação que não fornece dados através de uma das outras origens de dados ou soluções.  Pode ser utilizada para preencher o repositório a partir de qualquer cliente que pode ligar a API e não depender da agenda de coleções de sites de origem de dados ou de solução.

- [Inicie sessão Analytics HTTP recolha API](../log-analytics/log-analytics-data-collector-api.md)


### <a name="retrieve-data-from-the-log-analytics-repository"></a>Obter dados a partir do repositório de análise de registo

Existem vários métodos para obter dados a partir do repositório OMS.  Poderá pretender que os utilizadores para obter dados utilizando a consola OMS e forneça-lhe diferentes tipos de visualizações e análise.  Também pode obter os dados a partir de um processo externo como outra solução de gestão.

#### <a name="log-searches"></a>Registo de pesquisas

Todos os dados armazenados no repositório de OMS está disponível através de pesquisas de registo.  Os utilizadores podem executar os seus próprios análises ad hoc na consola do OMS ou criar um dashboard com uma visualização para uma pesquisa de registo específico.  Soluções podem conter vistas personalizadas com visualizações com base em pesquisas predefinidas.  Pode utilizar a API do registo de pesquisa para aceder aos dados no repositório de OMS a partir de uma ferramenta de aplicação ou gestão externa.  

- [Pesquisas de registo no registo de análise](../log-analytics/log-analytics-log-searches.md)
- [Pesquisa de registo de análise do registo REST API](../log-analytics/log-analytics-log-search-api.md)
- [Cmdlets de análise de registo](https://msdn.microsoft.com/library/mt188224.aspx)



#### <a name="custom-views"></a>Vistas personalizadas 
O estruturador de vistas permite-lhe criar vistas personalizadas na consola do OMS que permitem aos utilizadores com visualização e análise dos dados na sua solução.  Cada vista inclui um mosaico que é apresentado na página principal da consola e qualquer número de partes de visualização que são baseados nas pesquisas de registo que pode definir.
  
- [Estruturador de vista de análise de registo](../log-analytics/log-analytics-view-designer.md)


#### <a name="power-bi"></a>Power BI

Análise de registo pode automaticamente exportar dados do repositório de OMS Power BI para que pode tirar partido respetivas visualizações e ferramentas de análise.  Executa este exportar uma agenda para que os dados são mantidos atualizados. 

- [Exportar dados de análise de registo para o Power BI](../log-analytics/log-analytics-powerbi.md)




## <a name="automation"></a>Automatização

OMS pode automatizar processos para responder a dados recolhidos ou para efetuar outras funções de gestão.  Poderá recolher dados a partir da sua aplicação e inseri-lo no repositório de OMS ou podem automatizar a correção de um problema conhecido em resposta ao dados que se encontram no repositório. 

![Automatização](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks

Runbooks no Azure automatização executar scripts de PowerShell e fluxos de trabalho na nuvem Azure.  Pode utilizá-los para gerir os recursos no Azure ou outros recursos que podem ser acedidos a partir da nuvem.  Também pode ser executada Runbooks num centro de dados local híbrido livro execuções trabalhador a utilizar.  Pode iniciar um livro de execuções a partir do portal do Azure ou de processos externos utilizar um número de métodos como PowerShell ou a API de automatização.

- [Iniciar um livro de execuções no Azure automatização](../automation/automation-starting-a-runbook.md)
- [Cmdlets de automatização Azure](https://msdn.microsoft.com/library/dn690262.aspx)
- [Automatização REST API](https://msdn.microsoft.com/library/mt662285.aspx)
- [Automatização .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertas

Regras de alertas executar automaticamente as pesquisas de registo de acordo com uma agenda.  Se os resultados corresponder aos critérios determinados o alerta resultante pode iniciar um livro de execuções no Azure automatização ou uma chamada um webhook qual pode iniciar um processo externo.  Ambas as respostas a estas podem incluir detalhes do alerta, incluindo os dados devolvidos pela pesquisa de registo.

- [Alertas no registo Analytics](../log-analytics/log-analytics-alerts.md)
- [API de alerta de análise de registo](../log-analytics/log-analytics-api-alerts.md)


## <a name="backup-and-site-recovery"></a>Cópia de segurança e recuperação de sites

Azure cópia de segurança e recuperação de sites fornecem serviços para proteger os seus dados empresariais, garantindo a disponibilidade de servidores e aplicações.  Pode tirar partido destes serviços para efetuar destes cenários como fornecer serviços de cópia de segurança para a sua aplicação ou iniciar uma activação pós-falha de uma máquina virtual.

- [Cmdlets de cópia de segurança Azure](https://msdn.microsoft.com/library/mt619253.aspx)
- [Recuperação de Azure Site REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
- [Cmdlets de recuperação de sites Azure](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Soluções personalizadas

Pode incorporar lógica integração numa solução personalizadas para executar na área de trabalho ou na área de trabalho de um cliente.  A solução pode incluir qualquer um dos métodos integração neste artigo para além de outros recursos para fornecer um cenário de gestão completa.  Os recursos da solução são fornecidos dessa quando a solução é removida, todos os recursos que tenha criado são removidos da área de trabalho OMS e subscrição Azure.

Por exemplo, a solução pode incluir um livro de execuções automatização para recolher e processar dados e, em seguida, povoar o repositório de análise de registo com a API de Recolectores de dados de HTTP.  Também pode incluir uma vista personalizada que apresenta e analisa os dados recolhidos.  

- Criação de soluções personalizadas (brevemente)    

## <a name="next-steps"></a>Próximos passos
- O [OMS SDK](operations-management-suite-sdk.md) para obter informações técnicas sobre a automatização de serviços OMS de referência.  
