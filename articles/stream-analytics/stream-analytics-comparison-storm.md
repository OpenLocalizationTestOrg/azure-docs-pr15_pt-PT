<properties
    pageTitle="Plataformas de análise: comparação Apache tempestade para a análise da cadeia | Microsoft Azure"
    description="Veja orientações escolhendo uma plataforma de análise na nuvem ao utilizar uma comparação Apache tempestade a análise da cadeia. Compreenda as diferenças e funcionalidades."
    keywords="plataforma Analytics, plataformas de análise, plataforma de análise de nuvem, comparação tempestade"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Ajudar a escolher uma plataforma de análise em sequência: comparação Apache tempestade para a análise da cadeia de Azure

Veja orientações escolher uma plataforma de análise na nuvem ao utilizar esta comparação Apache tempestade para a análise da cadeia de Azure. Compreenda a casos de utilização de propostas de valor de análise da cadeia versus Apache tempestade como um serviço gerido no Azure HDInsight, para que possa escolher a solução direita para a sua empresa.

Ambas as plataformas de análise fornecem vantagens de uma solução PaaS, existem alguns principais distintivas as funcionalidades que diferencia. Capacidades, bem como as limitações um destes serviços estão indicadas abaixo para o ajudar a é direcionado sobre a solução que precisa para alcançar os seus objetivos.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Destrua comparação para a análise de sequência: funcionalidades gerais ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de sequência Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Abrir origem</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não, a análise da cadeia de Azure é uma especialidades Microsoft oferta.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim, Apache tempestade é uma tecnologia de Apache licenciado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft suportado</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisitos de hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não existem requisitos hardware. Azure a análise da cadeia é um serviço de Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não existem requisitos hardware. Apache tempestade é um serviço de Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unidade de nível superior</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Com os clientes de análise da cadeia de Azure implementar e monitorizar transmissão de tarefas.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Com Apache tempestade no HDInsight clientes implementar e monitorizar um cluster de todo, pode alojar várias tarefas tempestade, bem como outras das cargas de trabalho (lote incluindo).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Preço</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Análise da cadeia um preço por volume dos dados processados e o número de unidades em sequência (por hora que a tarefa está a ser executado) necessários.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">Ainda mais informações sobre os preços podem ser encontrados aqui.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Para Apache tempestade no HDInsight, a unidade de compra é baseado em cluster e é cobrada com base na data e hora que cluster estiver em execução, independentemente da tarefas implementadas.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">Ainda mais informações sobre os preços podem ser encontrados aqui.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Criação de cada plataforma analytics##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de sequência Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacidades: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim, um suporte de idiomas SQL fáceis de utilizar está disponível.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não, os utilizadores tem de escrever código no Java c# ou utilizar Trident APIs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacidades: Operadores Temporal</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Em agregados e temporais associações são suportadas terminar a caixa.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Operadores temporais tem de ser implementada pelo utilizador.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Experiência de desenvolvimento</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interativo de criação e depuração experiência de utilização do através do Portal do Azure dados de exemplo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Desenvolvimento, depuração e monitorização experiência são fornecido através do Visual Studio experiência para os utilizadores .NET, para Java e de outros programadores idiomas pode utilizar o IDE da sua escolha.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte de depuração</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Análise da cadeia oferece estado das tarefas básicas e registos de operações como uma forma de depuração, mas não oferecer flexibilidade no que acontece/como muito está incluído nos registos do ie: o modo verboso.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Estão disponíveis para fins de depuração registos detalhados. Existem duas formas para trazer os registos ao utilizador, através do visual Studio ou o utilizador pode RDP num cluster para aceder a registos.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte para UDF (funções definidas pelo utilizador)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Neste momento não existe suporte para UDFs.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
UDFs podem ser escritos em c#, Java ou o idioma da sua escolha.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Expansível - código personalizado</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não existe suporte para extensible código na análise da cadeia.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim, é disponibilidade para escrever código personalizado no c#, Java ou outros idiomas suportados no tempestade.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Origens de dados e resultados##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de sequência Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Origens de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Origens de entrada suportadas são Azure evento concentradores e Blobs do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Existem conectores disponíveis para o evento concentradores, Bus de serviço, Kafka, etc. Conectores não suportadas podem ser executadas através do código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Formatos de teclado suportados são Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualquer formato pode ser executado através do código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Resultados de</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Uma tarefa de transmissão poderá ter várias saídas. Resultados de suportados: Evento Azure concentradores, armazenamento de Blobs do Azure, Azure tabelas, DB do Azure SQL e obter.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Suporte para muitos saídas numa topologia, cada saída poderá ter lógica personalizada para processamento descendentes. Terminar a caixa tempestade inclui conectores para obter, Azure evento concentradores, armazenamento de Blobs do Azure, Azure DocumentDB, SQL e HBase. Conectores não suportadas podem ser executadas através do código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de codificação de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Análise da cadeia requer o formato dos dados UTF-8 para ser utilizada.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualquer formato de codificação de dados pode ser executado através do código personalizado.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Gestão e operações##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de sequência Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modelo de implementação de tarefa</strong>
                </p>
                <p>
                    - <strong>Portal do Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Implementação é implementada através do Portal do Azure, PowerShell e REST APIs.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Depolyment é implementada através do Portal do Azure, PowerShell, Visual Studio e REST APIs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitorização (estatística, contadores, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Monitorização é implementada através do Portal do Azure e os REST APIs.
                </p>
                <p>
O utilizador também pode configurar alertas Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Monitorização é implementada através de IU tempestade e os REST APIs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Escalabilidade</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Número de unidades transmissão para cada tarefa. Cada unidade de transmissão processa até 1 MB/s. Máximo de 50 unidades por predefinição. Chamada para aumentar o limite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Número de nós no cluster HDI tempestade. Sem limite de número de nós (limite superior definida pela sua quota Azure). Chamada para aumentar o limite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites de processamento de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os utilizadores poderão dimensionar para cima ou para baixo o número de unidades transmissão para aumentar o processamento de dados ou otimizar os custos.
                </p>
                <p>
Dimensionar até 1 GB/s </p>
            </td>
            <td width="246" valign="top">
                <p>
Utilizador pode dimensionar para cima ou para baixo de tamanho de cluster para corresponder às necessidades.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Parar/currículo</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interromper e retomar a partir do último local parado.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Parar de apresentação e currículo a partir do último colocar parado com base na marca d'água.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Atualização de serviço e de arquitetura</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Correcção automática com sem indisponibilidade.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Correcção automática com sem indisponibilidade.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuidade do negócio através de um serviço disponível altamente da garantia SLA</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
SLA de 99,9% tempo de utilização </p>
                <p>
Recuperação automática a partir de falhas </p>
                <p>
Recuperação de operadores temporais com estado está incorporada.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
SLA de 99,9% tempo de utilização do cluster tempestade. Apache tempestade é uma plataforma de transmissão de tolerância a falhas de falhas no entanto, cabe dos clientes para garantir que as tarefas de transmissão ser executado sem interrupções.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Funcionalidades avançadas##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análise de sequência Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache tempestade no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Chegada de atrasada e processamento de eventos de ordem</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Políticas de configuráveis incorporadas para reordenar, largue eventos ou ajustar a hora do evento.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Utilizador tem de implementar lógica para processar este cenário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dados de referência</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dados de referência disponíveis a partir de Blobs do Azure com um tamanho máximo de 100 MB da cache de pesquisa na memória. Atualização de dados de referência é gerido pelo serviço.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sem limites de tamanho de dados. Conectores disponíveis para HBase, DocumentDB, SQL Server e Azure. Conectores não suportadas podem ser executadas através do código personalizado.
                </p>
                <p>
Atualização de dados de referência tem de ser tratada pelo código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integração com o computador aprendizagem</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Ao configurar publicado modelos de aprendizagem do Azure máquina como funções durante a ASA tarefa criação <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(pré-visualização privada)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponível através de tempestade parafusos.
                </p>
            </td>
        </tr>
    </tbody>
</table>
