<properties
    pageTitle="Criar uma solução IoT utilizando a análise da cadeia | Microsoft Azure"
    description="Introdução ao tutorial, para a solução IoT de análise da cadeia de um cenário de cabina"
    keywords="solução IOT, funções de janela"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Criar uma solução IoT utilizando a análise da cadeia

## <a name="introduction"></a>Introdução

Neste tutorial, irá obter informações sobre como utilizar a análise da cadeia de Azure para obter informações em tempo real a partir de dados. Os programadores facilmente podem combinar sequências de dados, como a eventos gerados pelo dispositivo, clique em fluxos e registos com registos históricos ou dados de referência para obter informações de negócio. Como um serviço de cálculo da cadeia em tempo real, totalmente geridas que está alojado no Microsoft Azure, Azure da cadeia Analytics fornece RDP incorporado, baixa latência e escalabilidade para obter que para cima e a ser executada em minutos.

Depois de concluir este tutorial, será possível:

-   Familiarizar-se com o portal do Azure da cadeia Analytics.
-   Configurar e implementar uma tarefa de transmissão.
-   Articular reais problemas e resolvê-los ao utilizar o idioma de consulta de análise da cadeia.
-   Desenvolva a transmissão soluções para os seus clientes utilizando a análise da cadeia com confiança.
-   Utilize a monitorização e a experiência de registo para resolução de problemas.

## <a name="prerequisites"></a>Pré-requisitos

Irá precisar dos pré-requisitos seguintes para concluir este tutorial:

-   A versão mais recente do [PowerShell do Azure](../powershell-install-configure.md)
-   Visual Studio 2015 ou gratuito do [Visual Studio da Comunidade](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   Uma [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilégios administrativos no computador
-   Transferência de [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) a partir do Centro de transferências da Microsoft
-   Opcional: Código fonte do gerador de evento TollApp em [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>Introdução cenário: "Olá, número de serviço pago!"


Uma estação de serviço pago é um fenómeno comuns. Encontrá-los em muitos terá, pontes e túneis em todo o mundo. Cada estação de serviço pago tem vários stands de dimensão de serviço pago. Em stands de dimensão manuais, pode deixar de pagar o número de serviço pago para um attendant. No stands de dimensão automatizados, um sensor na parte superior de cada stand verifica um cartão de RFID que se encontra afixado para a pára-brisas do seu veículo à medida que passa a stand de serviço pago. É fácil de visualizar a passagem de veículos através destes estações de serviço pago como uma sequência de evento que podem ser efetuadas interessantes operações.

![Imagem de carros na stands dimensão de serviço pago](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Dados de entrada

Neste tutorial funciona com duas sequências de dados. Sensores instalados na entrada e saída dos postos de serviço pago produzem da primeira cadeia. A sequência de segunda é um conjunto de dados de pesquisa estático que tenha veículo registo dados.

### <a name="entry-data-stream"></a>Fluxo de dados de entrada

O fluxo de dados de entrada contém informações sobre carros à medida que introduzam estações de serviço pago.


| TollID | EntryTime               | LicensePlate | Estado | Fazer   | Modelo   | Apresentada | VehicleWeight | Número de serviço pago | Etiqueta       |
|--------|-------------------------|--------------|-------|--------|---------|-------------|---------------|------|-----------|
| 1      | 12:01:00.000 2014-09-10 | JNB 7001     | NY    | Honda  | CRV     | 1           | 0             | 7    |           |
| 1      | 12:02:00.000 2014-09-10 | YXZ 1001     | NY    | Assistência da Toyota | Camry   | 1           | 0             | 4    | 123456789 |
| 3      | 12:02:00.000 2014-09-10 | ABC 1004     | CT    | Ford   | Taurus  | 1           | 0             | 5    | 456789123 |
| 2      | 12:03:00.000 2014-09-10 | XYZ 1003     | CT    | Assistência da Toyota | Corolla | 1           | 0             | 4    |           |
| 1      | 12:03:00.000 2014-09-10 | BNJ 1007     | NY    | Honda  | CRV     | 1           | 0             | 5    | 789123456 |
| 2      | 12:05:00.000 2014-09-10 | CDE 1007     | NJ    | Assistência da Toyota | 4 x 4     | 1           | 0             | 6    | 321987654 |


Eis uma breve descrição das colunas:

| Coluna | Descrição |
|--------------|--------------------------------------------------------------------|
| TollID       | O ID do stand de serviço pago que identifica um stand de serviço pago            |
| EntryTime    | A data e hora de entrada do veículo o stand de serviço pago em UTC |
| LicensePlate | O número de licenças placa de veículo                            |
| Estado        | Um Estado nos Estados Unidos                                           |
| Fazer         | O fabricante da automóveis                                 |
| Modelo        | O número de modelo dos automóveis                                 |
| Apresentada  | Quer 1 para os veículos ou 2 para veículos comerciais       |
| WeightType   | Peso veículo no toneladas; 0 para os veículos                   |
| Número de serviço pago         | O valor de número de serviço pago em USD                                              |
| Etiqueta          | A etiqueta "e" de automóveis que automatiza pagamento; em branco onde o pagamento concluído manualmente |


### <a name="exit-data-stream"></a>Fluxo de dados de saída

O fluxo de dados de saída contém informações sobre carros sair estação de serviço pago.


| **TollId** | **ExitTime**                 | **LicensePlate** |
|------------|------------------------------|------------------|
| 1          | 2014-09-10T12:03:00.0000000Z | JNB 7001         |
| 1          | 2014-09-10T12:03:00.0000000Z | YXZ 1001         |
| 3          | 2014-09-10T12:04:00.0000000Z | ABC 1004         |
| 2          | 2014-09-10T12:07:00.0000000Z | XYZ 1003         |
| 1          | 2014-09-10T12:08:00.0000000Z | BNJ 1007         |
| 2          | 2014-09-10T12:07:00.0000000Z | CDE 1007         |

Eis uma breve descrição das colunas:


| Coluna | Descrição |
|--------------|-----------------------------------------------------------------|
| TollID       | O ID do stand de serviço pago que identifica um stand de serviço pago         |
| ExitTime     | A data e hora de saída do veículo do stand de serviço pago em UTC |
| LicensePlate | O número de licenças placa de veículo                         |

### <a name="commercial-vehicle-registration-data"></a>Dados de registo de veículo comercial

O tutorial utiliza um instantâneo estático de uma base de dados do registo de veículo comercial.


| LicensePlate | Atributos RegistrationId | Expirou |
|--------------|----------------|---------|
| SVT 6023     | 285429838      | 1       |
| XLZ 3463     | 362715656      | 0       |
| TRÁS 1005     | 876133137      | 1       |
| RIV 8632     | 992711956      | 0       |
| SNY 7188     | 592133890      | 0       |
| ELH 9896     | 678427724      | 1       |

Eis uma breve descrição das colunas:


| Coluna | Descrição |
|--------------|-----------------------------------------------------------------|
| LicensePlate | O número de licenças placa de veículo                         |
| Atributos RegistrationId     | ID de registo do veículo |
| Expirou | O estado de registo do veículo: 0 se o registo de veículo está ativo, 1 se tiver expirado registo                 |


## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurar o ambiente para análise de sequência do Azure

Para concluir este tutorial, precisa de uma subscrição do Microsoft Azure. A Microsoft oferece versão de avaliação gratuita para serviços do Microsoft Azure.

Se não possui uma conta Azure, pode [pedido de uma versão de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Para se inscrever para uma avaliação gratuita, precisa de um dispositivo móvel que possa receber mensagens de texto e um cartão de crédito válido.

Certifique-se de que siga os passos na secção "Limpar a sua conta Azure" no final deste artigo, para que pode fazer a melhor utilização do seu $200 livre crédito Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Aprovisionar Azure recursos necessários para o tutorial

Neste tutorial requer dois concentradores de evento para receber sequências de dados de *entrada* e *saída* . Base de dados SQL Azure exporta os resultados das tarefas da cadeia Analytics. Armazenamento Azure armazena dados de referência sobre registos de veículo.

Pode utilizar o script Setup.ps1 na pasta TollApp GitHub para criar todos os recursos necessários. Por razões de tempo, recomendamos que executá-la. Se gostaria de saber mais sobre como configurar estes recursos no portal do Azure, consulte o anexo "Configurar recursos de iniciação no Azure portal".

Transfira e guarde a pasta de [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) suporte e os ficheiros.

Abra um **Microsoft Azure PowerShell** janela _como administrador_. Se ainda não tiver Azure PowerShell, siga as instruções no [instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para o instalar.

Porque Windows automaticamente bloqueia. ps1,. dll e .exe ficheiros, tem de definir a política de execução antes de executar o script. Certifique-se que a janela do Azure PowerShell está em execução _como administrador_. Execute o **conjunto ExecutionPolicy sem restrições**. Quando lhe for pedido, escreva **Y**.

![Captura de ecrã de "Conjunto-ExecutionPolicy ilimitado" executar na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Execute **Get-ExecutionPolicy** para se certificar de que o comando trabalhou.

![Captura de ecrã de "Get-ExecutionPolicy" executar na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Vá para o directório que tem o scripts e a aplicação de gerador.

![Captura de ecrã de "cd .\TollApp\TollApp" executar na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Tipo de **.\\ Setup.ps1** para configurar a sua conta Azure, criar e configurar recursos todos necessários e começar a gerar eventos. O script escolhe aleatoriamente para cima uma região para criar os recursos. Para especificar explicitamente uma região, pode ser efetuada com o **-localização** parâmetro tal como no exemplo seguinte:

**. \\Setup.ps1-localização "Central (EUA)"**

![Captura de ecrã da página de início de sessão Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

O script abre a página de início de **Sessão** do Microsoft Azure. Introduza as suas credenciais de conta.

> [AZURE.NOTE] Se a sua conta tiver acesso a múltiplas subscrições, será pedido para introduzir o nome da subscrição que pretende utilizar para o tutorial.

O script pode demorar alguns minutos para ser executado. Depois de terminar, o resultado deve aspeto a captura de ecrã seguinte.

![Captura de ecrã do resultado do script na janela do PowerShell do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Também verá outra janela semelhante ao seguinte captura de ecrã. Esta aplicação está a enviar eventos para Azure evento concentradores, que é necessário para executar o tutorial. Sim, não parar a aplicação ou fechar esta janela até terminar o tutorial.

![Captura de ecrã de "Enviar dados de hub do evento"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Deverá poder ver todos os recursos criados no Azure portal agora. Aceda à <https://manage.windowsazure.com>e inicie sessão com as credenciais da conta.

### <a name="azure-event-hubs"></a>Evento Azure concentradores

Clique em **Serviço BUS** no lado esquerdo do Azure portal para ver concentradores evento que o script criado na secção anterior.

![Serviço Bus](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Irá ver todos os espaços de nomes disponíveis na sua subscrição. Clique no efeito que começa com *tolldata* (tolldata4637388511 no nosso exemplo). Clique no separador **Evento CONCENTRADORES** .

![Separador concentradores do evento no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Verá duas concentradores de evento com o nome *entrada* e *Sair* criada neste espaço de nomes.

![Captura de ecrã do concentradores de evento "entrada" e "saída" no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### <a name="azure-storage-container"></a>Contentor de armazenamento de Azure

1. Clique em **armazenamento** no lado esquerdo do Azure portal para ver o contentor de armazenamento do Windows Azure que é utilizado no tutorial.

    ![Item de menu de armazenamento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

2. Clique no efeito que começam com *tolldata* (tolldata4637388511 no nosso exemplo). Clique no separador **contentores** para ver o contentor criado.

    ![Separador contentores no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

3. Clique no contentor **tolldata** para ver o ficheiro JSON carregado com dados de registo de veículo.

    ![Captura de ecrã do ficheiro registration.json no contentor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### <a name="azure-sql-database"></a>Base de dados Azure SQL

1. Clique em **Bases de dados SQL** no lado esquerdo do Azure portal para ver a base de dados do SQL que será utilizada no tutorial.

    ![Bases de dados SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

2. Clique em **tolldatadb**.

    ![Captura de ecrã da base de dados SQL criado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

3. Copiar o nome do servidor sem o número da porta (*nomedoservidor*. database.windows.net, por exemplo).

## <a name="connect-to-the-database-from-visual-studio"></a>Ligar à base de dados a partir do Visual Studio

Utilize o Visual Studio para aceder aos resultados da consulta da base de dados de saída.

Ligar-se para a base de dados do SQL (o destino) a partir do Visual Studio:

1. Abrir o Visual Studio e, em seguida, clique em **Ferramentas de** > **ligar à base de dados**.

2. Se lhe for solicitado, clique em **Microsoft SQL Server** como origem de dados.

    ![Caixa de diálogo Alterar origem de dados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3. No campo **nome do servidor** , cole o nome que copiou na secção anterior do portal do Azure (ou seja, *nomedoservidor*. database.windows.net).

4. Clique em **utilizar autenticação do SQL Server**.

5. Introduza **tolladmin** no campo **nome de utilizador** e **123toll!** no campo de **palavra-passe** .

6. Clique em **Seleccionar ou introduza um nome de base de dados**e selecione **TollDataDB** como a base de dados.

    ![Adicionar a caixa de diálogo ligação](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7. Clique em **OK**.

8. Abra o Explorador de servidor.

    ![Explorador de servidor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9. Consulte o artigo quatro tabelas na base de dados TollDataDB.

    ![Tabelas na base de dados TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Gerador de evento: TollApp projeto de exemplo

O script do PowerShell é iniciado automaticamente enviar eventos ao utilizar o programa de aplicação de exemplo TollApp. Não é necessário efetuar quaisquer passos adicionais.

No entanto, se estiver interessado em detalhes de implementação, que pode encontrar o código de origem da aplicação TollApp na GitHub [Amostras/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Captura de ecrã do código de exemplo apresentado no Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa de análise da cadeia

1. No portal do Azure, abra a análise da cadeia e clique em **Novo** no canto inferior esquerdo da página para criar uma nova tarefa de análise.

    ![Botão novo](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

2. Clique em **criar rápida**. Selecione o mesmo região onde os outros recursos são criados pelo script.

3. Para a definição de **Conta de armazenamento monitorização REGIONAL** , selecione **Criar nova conta de armazenamento** e atribua um nome exclusivo. Azure da cadeia Analytics irá utilizar esta conta para armazenar informações de monitorização para todas as tarefas futuras.

4. Clique em **Criar tarefa do fluxo de análise** na parte inferior da página.

    ![Criar a opção de trabalho de análise da cadeia](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Defina as origens de entrada

1. Clique na tarefa de análise criado no portal.

    ![Captura de ecrã da tarefa a análise no portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

2. Clique no separador de **entradas** para definir a origem de dados.

    ![No separador de entradas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

3. Clique em **Adicionar uma entrada**.

    ![Adicionar uma opção de entrada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

4. Clique em **fluxo de dados** na primeira página.

    ![A opção de fluxo de dados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

5. Na segunda página do assistente, clique em **Evento CONCENTRADOR** .

    ![A opção de concentrador de evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

6. Introduza **EntryStream** como **ALIAS de entrada**.

7. Clique em pendente **Concentrador de evento** e selecione aquele que comecem por "TollData" (por exemplo, TollData9518658221).

8. Selecione a **entrada de** como o nome do concentrador de evento e **todos os** como o nome da política concentrador evento.

    As definições de terá o seguinte aspeto:

    ![Definições do Centro de evento](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

9. Na página seguinte, selecione **JSON** para **Formato de SERIALIZAÇÃO evento** e **UTF8** para **codificação**.

    ![Definições de serialização](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

10. Clique em **OK** na parte inferior da página para concluir o assistente.

    ![Captura de ecrã de EntryStream entrada no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

    Agora que criou a sequência de entrada, vai ter de seguir os mesmos passos para criar a sequência de saída. Na terceira página do assistente, certifique-se de que introduzir valores como a captura de ecrã seguinte.

    ![Definições para a sequência de saída](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

    Depois de definir duas sequências de entrada:

    ![Fluxos de entrada definidos no portal do Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

    Em seguida, irá adicionar a entrada de dados de referência para o ficheiro de BLOBs que contém os dados de registo de carro.

11. Clique em **Adicionar entrada**e, em seguida, clique em **Dados de referência**.

    ![Opções de "Adicionar uma entrada de" com dados de referência selecionada](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

12. Na página seguinte, selecione a conta de armazenamento que começa com **tolldata**. O nome do contentor deve ser **tolldata**e o nome de BLOBs em **Padrão de caminho** deve ser **registration.json**. Este nome de ficheiro é sensível a maiúsculas e deve estar em minúsculo.

    ![Definições do armazenamento de blogue](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

13. Selecione os valores tal como apresentado na captura de ecrã seguinte na página seguinte e, em seguida, clique em **OK** para concluir o assistente.

    ![Seleção de JSON para "formato de serialização par" e UTF8 para "Codificação"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Agora todas as entradas são definidas.

![Captura de ecrã das três entradas definidas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## <a name="define-output"></a>Definir a saída

1. Clique no separador de **saída** e, em seguida, clique em **Adicionar uma saída**.

    ![O separador de saída e a opção "Adicionar uma saída"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

2. Clique em **base de dados SQL**.

3. Selecione o nome do servidor que foi utilizado na secção "Ligar à base de dados a partir do Visual Studio" deste artigo. O nome da base de dados é **TollDataDB**.

4. Introduza **tolladmin** no campo **nome de utilizador** , **123toll!** no campo **palavra-passe** e **TollDataRefJoin** no campo da **tabela** .

    ![Definições de base de dados SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## <a name="azure-stream-analytics-query"></a>Consulta de análise da cadeia Azure

No separador da **consulta** contém uma consulta SQL que transformações dos dados de entrada.

![Uma consulta adicionada ao separador consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

Neste tutorial tentativas para responder a várias perguntas de negócio que estão relacionados com dados e construções da cadeia a análise de consultas que podem ser utilizadas para fornecer uma resposta relevante no Azure sequência de análise de serviço pago.

Antes de iniciar a tarefa de análise da cadeia primeira, vamos explorar alguns cenários e a sintaxe da consulta.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Introdução a linguagem de consulta do Azure da cadeia Analytics
-----------------------------------------------------

Digamos que precisa de contar o número de veículos que introduzir um stand de serviço pago. Uma vez que esta é uma sequência contínua de eventos, tem de definir um "período de tempo." Vamos modifique a pergunta para ser "quantos veículos introduzir um stand de serviço pago em três minutos?". Este é frequentemente referido como a contagem de tumbling.

Vamos ver a consulta de análise da cadeia de Azure que responde a esta pergunta:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Como pode ver, Azure da cadeia Analytics utiliza um idioma de consulta que é como SQL e adiciona as extensões de alguns para especificar os aspetos relacionados com a hora da consulta.

Para obter mais detalhes, leia sobre a [Gestão do tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e o [sistema baseado em janelas](https://msdn.microsoft.com/library/azure/dn835019.aspx) construções utilizadas na consulta a partir do MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Testar a análise do Azure da cadeia de consultas

Agora que tenha escrito a sua consulta de análise da cadeia de Azure primeira, é altura de testá-lo ao utilizar os ficheiros de dados de exemplo localizados na sua pasta TollApp no caminho seguinte:

**.. \\TollApp\\TollApp\\dados**

Esta pasta contém os seguintes ficheiros:

- Entry.JSON
- Exit.JSON
- Registration.JSON

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Pergunta 1: Número de veículos introduzir um stand de serviço pago

1. Abra o portal do Azure e vá para o seu trabalho Azure a análise da cadeia criado. Clique no separador da **consulta** e cole a consulta da secção anterior.

    ![Consulta colada no separador Query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

2. Para validar esta consulta relativamente a dados de exemplo, clique no botão de **teste** . Na caixa de diálogo que é aberta, aceda ao Entry.json, um ficheiro com dados de exemplo do fluxo de evento **EntryTime** .

    ![Captura de ecrã do ficheiro Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

3. Valide que é o resultado da consulta como esperado:

    ![Resultados do ensaio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Pergunta 2: Comunicar o tempo total da cada carro a passar o stand de serviço pago

O tempo médio necessário para um carro a passar o número de serviço pago ajuda para avaliar a eficiência do processo e a experiência do cliente.

Para localizar o tempo total, é necessário aderir a sequência de EntryTime com a sequência de ExitTime. Irá participar as sequências de colunas TollId e LicencePlate. O operador **PARTICIPAR** requer que especificar manobra temporal que descreva a diferença de tempo aceitável entre os eventos associados. Irá utilizar a função **DIFDATA** para especificar que eventos devem ser 15 minutos uns dos outros. Pode também se aplicarão a função **DIFDATA** para sair e tempos de entrada para calcular o tempo real que um carro passa na estação de serviço pago. Tenha em atenção a diferença entre a utilização de **DATEDIFF** quando for utilizado numa instrução **SELECT** em vez de uma condição **PARTICIPAR** .

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Para testar esta consulta, atualize a consulta no separador **consulta** do seu trabalho:

    ![Consulta actualizada no separador Query](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

2. Clique em **Testar** e especifique os ficheiros de entrada de exemplo para EntryTime e ExitTime.

    ![Captura de ecrã de ficheiros de entrada selecionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

3. Selecione a caixa de verificação para testar a consulta e ver o resultado:

    ![Resultado de teste](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Pergunta 3: Todos os veículos comerciais com registo expirado um relatório

Azure a análise da cadeia pode utilizar estáticos instantâneos dos dados para participar com sequências de dados temporal. Para demonstrar esta capacidade, utilize a seguinte pergunta de exemplo.

Se um veículo comercial está registado com a empresa de serviço pago, pode passar através do stand o número de serviço pago sem ser parado para o controlo. Irá utilizar a tabela de referência de registo de veículo comercial para identificar todos os veículos comerciais que expiraram registos.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Para testar uma consulta utilizando dados de referência, tem de definir uma origem de entrada para os dados de referência, que efetuou já.

1. Para testar esta consulta, cole a consulta no separador da **consulta** , clique em **Testar**e especificar as duas origens de entrada:

    ![Captura de ecrã de ficheiros de entrada selecionados](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

2. Ver o resultado da consulta:

    ![Captura de ecrã do resultado da consulta](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## <a name="start-the-stream-analytics-job"></a>Iniciar a tarefa de análise da cadeia


Está na altura para concluir a configuração e iniciar a tarefa. Guardar a consulta da pergunta 3, que vão produzir saída que corresponde ao esquema da tabela de saída **TollDataRefJoin** .

Vá para a tarefa de **DASHBOARD**e clique em **Iniciar**.

![Captura de ecrã do botão Iniciar no dashboard de tarefa](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Na caixa de diálogo que é aberta, altere a hora de **Saída iniciar** hora **personalizados**. Altere a hora a uma hora antes da hora atual. Esta alteração garante que todos os eventos do concentrador de evento são processados desde que iniciou gerar os eventos no início do tutorial. Agora, clique na marca de verificação para iniciar a tarefa.

![Seleção de hora personalizado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Iniciar a tarefa, pode demorar alguns minutos. Pode ver o estado na página de nível superior para a análise da cadeia.

![Captura de ecrã do Estado da tarefa](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## <a name="check-results-in-visual-studio"></a>Resultados da verificação de no Visual Studio

1. Abra o Explorador de servidor do Visual Studio e o botão direito do rato na tabela **TollDataRefJoin** .
2. Clique em **Mostrar dados da tabela** para ver os resultados da sua tarefa.

    ![Seleção de "Mostrar dados da tabela" no Explorador do servidor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Dimensionar saída Azure a análise de sequência de tarefas

Azure a análise da cadeia foi concebida para elastically escala, de modo a que possa tratar muitos dados. A consulta de análise da cadeia de Azure pode utilizar uma cláusula **PARTIÇÃO BY** para indicar ao sistema que este passo irá dimensionar saída. **PartitionId** é uma coluna especial que o sistema adiciona para fazer corresponder o ID da partição de entrada (concentrador de evento).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Parar a tarefa atual, atualize a consulta no separador **QUERY** e abrir o separador **escala** .

    **Unidades de transmissão** definem a quantidade de energia de cluster que possa receber a tarefa.

2. Mova o controlo de deslize para 6.

    ![Captura de ecrã de selecionar as unidades de transmissão de 6](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

3. Aceda ao separador **saídas** e altere o nome da tabela SQL para **TollDataTumblingCountPartitioned**.

Se começar a tarefa de agora, Azure da cadeia pode distribuir trabalho por mais de cluster de recursos e de análise atingir débito melhor. Tenha em atenção que a aplicação TollApp também está a enviar eventos divididos por TollId.

## <a name="monitor"></a>Monitor

No separador **MONITOR** contém estatísticas sobre o trabalho em execução.

![Captura de ecrã das estatísticas sobre como executar tarefas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Pode aceder a **Registos de operação** a partir do separador **DASHBOARD** .

![A opção "Operação registos"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![Captura de ecrã de registos de operação onde pode ver o estado das tarefas](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Para ver informações adicionais sobre um determinado evento, clique no evento e, em seguida, clique no botão **Detalhes** .

![Captura de ecrã de detalhes sobre um evento seleccionado](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## <a name="conclusion"></a>Conclusão

Neste tutorial introduzida o serviço Azure da cadeia Analytics. É demonstrar como configurar as entradas e saídas para a tarefa de análise da cadeia. Utilizar o cenário de dados de serviço pago, o tutorial explicado tipos comuns dos problemas que surja no espaço de dados em movimento e como pode ser resolvidos com consultas SQL gosto simples no Azure da cadeia Analytics. O tutorial descritas construções de extensão SQL para trabalhar com dados temporais. -Mostrava como participar sequências de dados, como enriquecer o fluxo de dados com dados de referência estático e como dimensionar saída de uma consulta para alcançar mais elevado débito.

Embora este tutorial fornece uma boa introdução, não é concluída por qualquer meio. Pode encontrar mais padrões de consulta com a linguagem de SAQL exemplos de [consulta para padrões de utilização de análise da cadeia comuns](stream-analytics-stream-analytics-query-patterns.md).
Consulte a [documentação online](https://azure.microsoft.com/documentation/services/stream-analytics/) para saber mais sobre a análise da cadeia de Azure.

## <a name="clean-up-your-azure-account"></a>Limpar a sua conta do Azure

1. Pare a tarefa de análise da cadeia no portal do Azure.

    O script Setup.ps1 cria duas concentradores de evento e uma base de dados do SQL. As instruções seguintes ajudá-lo limpar recursos no fim do tutorial.

2. Numa janela do PowerShell, escreva **.\\ CleanUp.ps1** para iniciar o script que elimina recursos utilizados no tutorial.

    > [AZURE.NOTE] Recursos estão identificados pelo nome. Certifique-se de que reveja cuidadosamente cada item antes de confirmar a remoção.

    ![Captura de ecrã do processo de limpeza](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)
