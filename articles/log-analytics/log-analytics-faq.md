<properties
    pageTitle="Iniciar sessão de perguntas mais frequentes sobre a análise | Microsoft Azure"
    description="Respostas às perguntas mais frequentes sobre o serviço de análise de registo."
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

# <a name="log-analytics-faq"></a>Perguntas mais frequentes sobre a análise de registo

Estas FAQs Microsoft é uma lista de perguntas mais frequentes sobre a análise de registo no conjunto de aplicações de gestão de operações do Microsoft (OMS). Se tiver dúvidas adicionais sobre a análise de registo, vá para o [Fórum de debate](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e publique as suas perguntas. Alguém a partir da nossa Comunidade irá ajudá-lo obter as suas respostas. Se uma pergunta frequentemente é pedida, vamos adicionar-este artigo para que pode ser encontrado forma rápida e fácil.

## <a name="general"></a>Geral

**Q: que controlos são executados pelo AD e avaliação de SQL soluções?**

RESPOSTAS. A seguinte consulta mostra uma descrição de todos os controlos atualmente executadas:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados, em seguida, podem ser exportados para o Excel para análise mais aprofundada.

* *P: por que motivo ver algo diferente *OMS* no SCOM Administration? * *

R: dependendo no que Update Rollup de SCOM que está, poderá ver um nó de *Classificação do Centro de sistema*, *Informações operacionais*ou *A análise de registo*.

A atualização de cadeia de texto para *OMS* é incluída num pacote de gestão, tem de ser importado manualmente. Siga as instruções no artigo da BDC de Rollup de atualização de SCOM mais recente e atualize a consola OMS para ver as atualizações mais recentes no nó *OMS* .

* *P: existe um *no local* versão do OMS? * *

R: não. Processa e armazena muito grandes quantidades de dados de análise de registo. Como um serviço na nuvem, a análise de registo é possível para cima escala se for necessário e evitar quaisquer impacto no desempenho do seu ambiente.

Além disso, a ser um serviço de nuvem significa que não precisa de manter a infraestrutura de análise de registo para cima e em execução e possa receber atualizações de funcionalidade frequente e correções.

## <a name="configuration"></a>Configuração
**Q: Posso alterar o nome do contentor tabela/blob utilizado para ler a partir do Azure diagnósticos (WAD)?**  

RESPOSTAS.  Não, esta não é possível atualmente, mas é planeada para um lançamento futuro.

**Q: endereços de IP o que fazer a utilização de serviços OMS? Como posso garantir que minha firewall apenas permite que o tráfego para os serviços de OMS?**  

RESPOSTAS. O serviço de análise de registo é criado na parte superior do Azure e os pontos finais recebem IPs que estão no [Intervalos de IP do Microsoft Azure Centro de dados](http://www.microsoft.com/download/details.aspx?id=41653).

Como são feitas implementações do serviço, altere os endereços IP reais dos serviços OMS. Os nomes de DNS para permitir que através da firewall encontram-se documentados ao [configurar as definições de proxy e a firewall no registo de análise](log-analytics-proxy-firewall.md).

**Q: Posso utilizar o ExpressRoute para estabelecer ligação ao Azure. Tráfego meu registo a análise utilizará a minha ligação de ExpressRoute?**  

RESPOSTAS. Os diferentes tipos de tráfego de ExpressRoute são descritos na [documentação ExpressRoute](./expressroute/expressroute-faqs.md#supported-services).

O tráfego para o registo de análise utiliza o circuito de ExpressRoute efectuado público.

**Q: existe uma maneira fácil e simple para mover uma área de trabalho de análise de registo existente para outra subscrição de área de trabalho/Azure de análise de registo?**  Temos OMS as áreas de trabalho vários clientes que recomendamos foram testes e trialing nossa subscrição Azure e estão a mover à produção por isso pedimos que para movê-las para os seus próprios subscrição Azure/OMS.  

RESPOSTAS. O `Move-AzureRmResource` cmdlet permitirá que mover uma área de trabalho de análise de registo bem como uma conta de automatização de uma subscrição Azure para outro. Para mais informações, consulte o artigo [Mover AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Esta alteração também pode ser efetuada no portal do Azure.

Não pode mover os dados de um registo a análise da área de trabalho para outro ou alterar a região que o registo de análise dados são armazenados numa.

**P: como posso adicionar OMS para SCOM?**

R: atualizar para as atualizações mais recentes e pacotes de gestão de importar permite-lhe ligar SCOM a análise de registo.

Note que a ligação SCOM registo Analytics só está disponível para SCOM 2012 SP1 e superior.

**P: como confirmar que é possível comunicar com a análise de registo de um agente?**

R: para se certificar de que o agente possam comunicar com OMS, aceda a: painel de controlo, segurança e definições, **Microsoft Agent monitorização**.

No separador de **Análise de registo de Azure (OMS)** , procure uma marca de verificação verde. Um ícone de marca de verificação verde confirma que é possível comunicar com o serviço OMS o agente.

Um ícone de aviso amarelo significa que o agente está a ter problemas de comunicação com OMS. Um motivo comuns é o serviço do Microsoft Agent monitorização foi parado e tem de ser reiniciado.

**P: como posso parar de um agente de comunicar com a análise de registo?**

R: no SCOM, remova o computador a partir da lista gerida OMS. Isto deixa de todas as comunicações através de SCOM para esse agente. Para agentes ligados diretamente OMS, pode impedir que lhes comunicar com OMS através de: painel de controlo, segurança e definições, **Microsoft Agent monitorização**.
Em **Análise de registo de Azure (OMS)**, remova todas as áreas de trabalho listadas.

## <a name="agent-data"></a>Dados de agente

**Q: quantidade de dados posso enviar através do agente de para a análise de registo? Existe uma quantidade máxima de dados por cliente?**  
RESPOSTAS. O plano gratuito define um remate diária de 500 MB por área de trabalho. Os planos padrão e premium não tem qualquer limite sobre a quantidade de dados que são carregados. Como um serviço na nuvem, a análise de registo no OMS concebido para automaticamente escala até alça o volume provenientes de um cliente – mesmo se for terabytes por dia.

O agente de análise de registo foi concebido para se certificar-tem um pequeno ambiental e faz alguma compressão de dados básico. Um dos nossos clientes escreveu realmente um blogue sobre os testes que são executadas em relação a nossa agente e como impressionados terem sido. O volume de dados serão variar consoante as soluções permite que os seus clientes. Pode encontrar informações detalhadas sobre o volume de dados e ver a divisão por solução em de **utilização** na página de descrição geral do OMS de mosaico.

Para mais informações, pode obter um [cliente blogue](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sobre a baixa menores do agente OMS.

**Q: quantidade largura de banda de rede é utilizada pelo agente de gestão do Microsoft (MMA) quando enviar dados para a análise de registo?**

RESPOSTAS. Largura de banda é uma função da quantidade de dados enviados. Os dados são comprimidos como ter sido enviada através da rede.

**Q: quantidade de dados é enviada por agente?**

RESPOSTAS. Isto em grande medida depende:

- as soluções que ativou
- o número de registos e contadores de desempenho a ser recolhidos
- o volume de dados nos registos do

A camada comparar gratuita é uma boa forma de incorporada vários servidores e o indicador do volume de dados típico. Em geral, a utilização é apresentada na página **utilização** .
Em computadores que sejam capazes de executar o agente de WireData, pode ver a quantidade de dados está a ser enviado utilizando a seguinte consulta:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Próximos passos

- [Começar a trabalhar com a análise de registo](log-analytics-get-started.md) para saber mais sobre a análise de registo e obter cima e a ser executado em minutos.
