<properties
    pageTitle="Solução de dados no registo de análise do fio | Microsoft Azure"
    description="Dados de fio são dados consolidados de desempenho e de rede de computadores com agentes OMS, incluindo o Gestor de operações e ligado Windows agentes. Dados de rede são combinados com os seus dados de registo para o ajudar a correlacionar dados."
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

# <a name="wire-data-solution-in-log-analytics"></a>Solução de dados no registo de análise do fio

Dados de fio são dados consolidados de desempenho e de rede de computadores com agentes OMS, incluindo o Gestor de operações e ligado Windows agentes. Dados de rede são combinados com os seus dados de registo para o ajudar a correlacionar dados. Agentes OMS instalados em computadores nos seus dados de rede do monitor do IT infraestrutura enviados para e de outros computadores para níveis de rede 2-3 no [modelo OSI](https://en.wikipedia.org/wiki/OSI_model) incluindo os diversos protocolos e portas utilizadas.

>[AZURE.NOTE] A solução fio dados não está atualmente disponível para ser adicionada à áreas de trabalho. Clientes que já possuem a solução de dados de fio ativada podem continuar a utilizar a solução fio dados.

Por predefinição, OMS recolhe dados registados para dados de desempenho de rede, disco, memória e CPU do contadores incorporadas no Windows. Outros recolha de dados e de rede é efetuado em tempo real para cada agente, incluindo sub-redes e protocolos de nível da aplicação que está a ser utilizados pelo computador. Pode adicionar outros contadores de desempenho na página definições no separador de registos.

Caso tenha utilizado [sFlow](http://www.sflow.org/) ou outro software com o [protocolo de NetFlow do Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), em seguida, as estatísticas e os dados que vai ver a partir de dados de fio irão estar familiarizados para si.

Alguns dos tipos de consultas de pesquisa incorporadas do registo incluem:

- Agentes que fornece dados fio
- Endereço IP do agentes de fio dados
- Comunicações por endereços IP de saída
- Número de bytes enviados por protocolos de aplicações
- Número de bytes que foi enviado por um serviço de aplicação
- Bytes recebidos pelo protocolos diferentes
- Total de bytes enviadas e recebidas por IP
- Endereços IP que tenham comunicado com agentes na sub-rede 10.0.0.0/8
- Latência de em média para ligações que foram medido sujeito
- Processos do computador que iniciou ou recebido tráfego de rede
- Quantidade de tráfego de rede para um processo

Ao procurar utilizando fio dados, pode filtrar e agrupar dados para ver informações sobre o agentes superiores e os protocolos superiores. Ou pode procurar para quando determinados computadores (endereços de MAC/endereços IP) comunicadas entre si, como o tempo e a quantidade de dados foi enviada – basicamente, vê metadados sobre o tráfego de rede, que é baseados na pesquisa.

No entanto, uma vez que está a ver metadados, não é necessariamente útil para resolução de problemas aprofundada. Dados de esboço em OMS não não uma captura completa dos dados da rede. Por isso, não se destina para resolução de problemas em nível de pacotes.
A vantagem de utilizar o agente, comparado com outros métodos de coleções de sites, é que não tem de instalar eletrodomésticos, reconfigurar comutadores de rede ou faz configurações complicadas. Dados de fio são simplesmente agente baseado – instalar o agente num computador e irá monitorizar a sua própria tráfego de rede. Outra vantagem é quando pretender monitorizar das cargas de trabalho em execução no fornecedores de nuvem ou fornecedor de serviços de alojamento ou Microsoft Azure, onde o utilizador não possui a camada ferro.

Em contrapartida, não tem uma visibilidade completa do que acontece na rede se não instalar agentes em todos os computadores na sua infraestrutura de rede.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

- A solução de dados de fio adquire dados a partir de computadores que executam o Windows Server 2012 R2, Windows 8.1 e sistemas operativos posteriores.
- Microsoft .NET Framework 4.0 ou posterior é necessários nos computadores em que pretende adquirir fio dados a partir de.
- Adicione a solução de dados de fio à área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  Não existe nenhuma configuração mais obrigatória.
- Se pretender ver fio dados para obter uma solução específica, terá de ter a solução já adicionou à área de trabalho OMS.

## <a name="wire-data-data-collection-details"></a>Fio detalhes de recolha de dados de dados

Dados de fio recolhe metadados sobre o tráfego de rede utilizando os agentes de que ativou.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para fio dados.


| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows (2012 R2 / 8.1 ou posterior)|![Sim](./media/log-analytics-wire-data/oms-bullet-green.png)|![Sim](./media/log-analytics-wire-data/oms-bullet-green.png)|![N](./media/log-analytics-wire-data/oms-bullet-red.png)|            ![N](./media/log-analytics-wire-data/oms-bullet-red.png)|![N](./media/log-analytics-wire-data/oms-bullet-red.png)| minuto 1|


## <a name="combining-wire-data-with-other-solution-data"></a>Combinar dados de fio com outros dados de solução

Os dados devolvidos a partir das consultas incorporadas mostradas acima poderá interessantes por si. No entanto, a utilidade dos dados fio é realizada quando está a combinar com informações a partir de outras soluções OMS. Por exemplo, pode utilizar os dados de evento de segurança recolhidos pela solução de segurança e de auditoria e combiná-lo com dados de fio para procurar tentativas de início de sessão de rede invulgares para processos com nome.  Neste exemplo, teria de utilizar os operadores de IN e DISTINCT para participar em pontos de dados na sua consulta de pesquisa.

Requisitos: Para poder utilizar o exemplo seguinte, terá de ter a solução de segurança e auditoria instalada. No entanto, pode utilizar dados a partir de outras soluções para combinar com dados de fio para alcançar resultados semelhantes.

### <a name="to-combine-wire-data-with-security-events"></a>Para combinar os dados de fio com eventos de segurança

1. Na página Descrição geral, clique no mosaico **WireData** .
2. Na lista de **Consultas WireData comuns**, clique em **Quantidade de tráfego de rede (em Bytes) por processo** para ver a lista de processos devolvidos.
    ![consultas de wiredata](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Se a lista de processos é demasiado longa para visualizar facilmente, pode modificar a consulta de pesquisa para um aspeto semelhante ao:

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    Mostrado no exemplo abaixo é um processo com o nome DancingPigs.exe, que poderá aparecer suspeita.
    ![resultados da procura wiredata](./media/log-analytics-wire-data/oms-wiredata-02.png)

4. Utilizar os dados devolvidos na sua lista, clique num processo com nome. Neste exemplo, foi clicado DancingPigs.exe. Os resultados mostrados abaixo descrevem o tipo de tráfego de rede como comunicação de saída através de vários protocolos.
    ![resultados de wiredata que mostra um processo com nome](./media/log-analytics-wire-data/oms-wiredata-03.png)

5. Uma vez que a solução de segurança e auditoria está instalada, pode pesquisar para os eventos de segurança que têm o mesmo valor do campo nomeprocesso por modificar a consulta de pesquisa utilizando operadores de consulta de pesquisa IN e DISTINCT. Pode fazê-em seguida, quando os seus dados fio e outros registos de solução têm valores no mesmo formato. Modificar a sua consulta de pesquisa para um aspeto semelhante ao:

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![wiredata resultados com combinados dados](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. Nos resultados, verá que as informações da conta são apresentadas. Agora pode otimizar a consulta de pesquisa para saber com que frequência a conta, que mostra dados de segurança e de auditoria, foi utilizada pelo processo de com uma consulta que se assemelhe:        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![resultados de wiredata a mostrar dados de conta](./media/log-analytics-wire-data/oms-wiredata-05.png)



## <a name="next-steps"></a>Próximos passos

- [Registos de pesquisa](log-analytics-log-searches.md) para ver registos de pesquisa de dados de fio detalhadas.
- Consulte o artigo [Publicar utilizando dados de fio blogue de operações de gestão de conjunto de aplicações do registo de pesquisa de Manuel](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) tem informações adicionais sobre com que frequência os dados são recolhidos e como pode modificar propriedades da coleção de agentes do Gestor de operações.
