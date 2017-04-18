<properties
    pageTitle="Ligar computadores e dispositivos para OMS utilizando o Gateway OMS | Microsoft Azure"
    description="Ligue o seu monitorizadas Gestor de operações computadores e dispositivos geridos OMS com o Gateway OMS para enviar dados para o serviço OMS quando não possui acesso à Internet."
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
    ms.date="10/26/2016"
    ms.author="banders"/>

# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>Ligar computadores e dispositivos para OMS utilizando o Gateway OMS

Este documento descreve como os monitorizadas SCOM de Gestor de operações do sistema Centro de computadores e dispositivos geridos OMS podem enviar dados para o serviço OMS quando não possui acesso à Internet. O Gateway OMS pode recolher os dados e enviá-la para o serviço OMS em nome dos utilizadores.

O gateway é um reencaminhar em proxy HTTP que suporte HTTP túnel utilizando o comando HTTP ligar. O gateway pode processar até 2000 dispositivos OMS em simultâneo ligado quando executados uma CPU 4-core, 16 GB servidor a executar o Windows.

Por exemplo, empresa ou organização grande poderá ter servidores com a conectividade da rede, mas poderá não ter ligação à Internet. Outro exemplo, poderá ter de ponto muitos dos dispositivos de venda (posição) com nenhum meio de monitorização-las diretamente. E outro exemplo, Gestor de operações pode utilizar o Gateway OMS como um servidor proxy. Nestes exemplos, o Gateway OMS pode transferir dados dos agentes que são instalados nestes servidores ou dispositivos de posição para OMS.

Em vez de cada agente individuais envio de dados diretamente para o OMS e que exige uma ligação à Internet direta, todos os dados de agente em vez disso, é enviada através de um único computador que tem uma ligação à Internet. Nesse computador é onde instalar e utilizar o gateway. Neste cenário, pode instalar o agentes em todos os computadores em que pretende recolher dados. O gateway, em seguida, transfere dados a partir dos agentes para OMS diretamente — o gateway não analisar os dados que são transferidos.

Para monitorizar o Gateway OMS e analisar desempenho ou os dados de evento para o servidor de onde está instalado, tem de instalar o agente OMS no computador onde o gateway também está instalado.

O gateway tem de ter acesso à Internet para carregar os dados para OMS. Cada agente também tem de ter a conectividade da rede para o respectivo gateway para que agentes automaticamente podem transferir dados de e para o gateway. Para obter melhores resultados, não instale o gateway num computador que também é um controlador de domínio.

Vejamos um diagrama que mostra o fluxo de dados do agentes diretos para OMS.

![Diagrama de agente direta](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

Eis um diagrama que mostra o fluxo de dados a partir do Gestor de operações para OMS.

![Diagrama de Gestor de operações](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="install-the-oms-gateway"></a>Instalar o Gateway OMS

Instalar o este Gateway substitui versões anteriores do Gateway que instalou (registo Analytics reencaminhador).

Pré-requisitos: .net Framework 4,5, Windows Server 2012 R2 SP1 e superior

1. Transferir a versão mais recente do OMS Gateway a partir do [Centro de transferências da Microsoft](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi).
2. Para iniciar a instalação, faça duplo clique **OMS Gateway.msi**.
3. Na página Welcome, **seguinte**.  
    ![Assistente de configuração de gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)
4. Na página Contrato de licença, selecione **aceito os termos do contrato de licença** para aceitar o EULA e, em seguida, **seguinte**.
5. Na página de endereço proxy e porta:
    1. Escreva o número da porta TCP para ser utilizado para o gateway. Configuração abre este número de porta a partir da firewall do Windows. O valor predefinido é 8080.
    Intervalo válido do número da porta é 1-65535. Se a entrada de dados não se enquadra este intervalo, é apresentada uma mensagem de erro.
    2. Opcionalmente, se o servidor onde está instalado o gateway tem de utilizar um proxy, escreva o endereço do proxy onde o gateway tem de se ligar. Por exemplo, `http://myorgname.corp.contoso.com:80` se estiver em branco, o gateway irá tentar ligar à Internet diretamente. Caso contrário, o gateway liga-se para o proxy. Se necessitar de autenticação de servidor proxy, escreva o seu nome de utilizador e palavra-passe.
        ![Configuração de proxy do Assistente de gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
    3. Clique em **seguinte**
6. Se não tiver Microsoft Updates ativado, é apresentada a página Microsoft Update onde pode optar por ativar Updates da Microsoft. Efetuar uma seleção e, em seguida, clique em **seguinte**. Caso contrário, continue para o passo seguinte.
7. Na página de pasta de destino, deixe a predefinição pasta **%ProgramFiles%\OMS Gateway** ou escreva a localização onde pretende instalar o gateway e, em seguida, clique em **seguinte**.
8. No pronto para a página de instalação, clique em **instalar**. Um controlo de conta de utilizador poderá aparecer pedir permissão para instalar. Se Sim, clique em **Sim**.
9. Depois de concluir a configuração, clique em **Concluir**. Pode verificar que o serviço está a ser executada ao abrir o snap-in Services. msc e confirme que o **OMS Gateway** aparece na lista de serviços.  
    ![Serviços – OMS Gateway](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>Instalar um agente de dispositivos

Se for necessário, consulte o artigo [computadores com o Windows ligar-se para a análise de registo](log-analytics-windows-agents.md) para obter informações sobre como instalar diretamente ligados agentes. Este artigo descreve como pode instalar o agente de utilizar um Assistente de configuração ou ao utilizar a linha de comandos.

## <a name="configure-oms-agents"></a>Configurar agentes de OMS

Consulte o artigo [Configurar definições de proxy e de firewall com o agente de monitorização da Microsoft](log-analytics-proxy-firewall.md) para obter informações sobre como configurar um agente para utilizar um servidor proxy, que é neste caso, é o gateway.

Agentes de Gestor de operações enviam alguns dados tal como Gestor de operações alertas, avaliação de configuração, espaço instância e dados de capacidade, através do servidor de gestão. Outros dados de grande volume, tais como registos, desempenho e segurança do IIS são enviados diretamente para o Gateway OMS. Consulte o artigo [Adicionar o registo de análise soluções a partir da Galeria de soluções](log-analytics-add-solutions.md) para uma lista completa de dados que são enviadas através de cada canal.

>[AZURE.NOTE]
Se planeia utilizar o Gateway com balanceamento de carga, consulte [configurar, opcionalmente, balanceamento de carga de rede](#optionally-configure-network-load-balancing).

## <a name="configure-a-scom-proxy-server"></a>Configurar um servidor de proxy SCOM

Configurar o Gestor de operações para adicionar o gateway para funcionar como um servidor proxy. Quando atualiza a configuração do proxy, a configuração do proxy automaticamente é aplicada a todos os agentes reportar a Gestor de operações.

Para utilizar o Gateway para suportar o Gestor de operações, tem de ter:

- Agente de monitorização da Microsoft (versão agent – **8.0.10900.0** e posterior) instalado no servidor de Gateway e configurado para as áreas de trabalho OMS com o qual pretende comunicar.
- O gateway tem de ter ligação à Internet ou estar ligado a um servidor proxy que faz.

### <a name="to-configure-scom-for-the-gateway"></a>Para configurar SCOM para o gateway

1. Abra a consola do Gestor de operações e em **Conjunto de aplicações do operações gestão**, clique em **ligação** e, em seguida, clique em **Configurar um servidor Proxy**:  
    ![Gestor de operações – configurar servidor Proxy](./media/log-analytics-oms-gateway/scom01.png)
2. Selecione **utilizar um servidor proxy para o conjunto de gestão de operações de acesso** e, em seguida, escreva o endereço IP do servidor de OMS Gateway. Certifique-se de que começa com o `http://` prefixo:  
    ![Gestor de operações – endereço do servidor proxy](./media/log-analytics-oms-gateway/scom02.png)
3. Clique em **Concluir**. O servidor do Gestor de operações está ligado à área de trabalho OMS.

## <a name="configure-network-load-balancing"></a>Configurar balanceamento de carga

Pode configurar o gateway para utilizar rede balanceamento de carga ao criar um cluster de elevada disponibilidade. Cluster gere o tráfego a partir do seu agentes por redirecionar as ligações pedidas a partir dos agentes de monitorização do Microsoft nos seus nós. Se vai um servidor de Gateway para baixo, o tráfego redirecionado para outros nós.

1. Abra o Gestor de balanceamento de carga de rede e crie um cluster.
2. Com o botão direito do cluster antes de adicionar gateways e selecione **Propriedades do Cluster.** Configure o cluster de ter o seu próprio endereço IP:  
    ![Gestor de balanceamento de – endereços IP do Cluster de rede](./media/log-analytics-oms-gateway/nlb01.png)
3. Para ligar um servidor de OMS Gateway com o Microsoft monitorização Agent instalado, com o botão direito endereço IP do cluster e, em seguida, clique em **Adicionar anfitrião ao Cluster**.  
    ![Rede carregar o Gestor de balanceamento – Adicionar anfitrião Cluster](./media/log-analytics-oms-gateway/nlb02.png)
4. Introduza o endereço IP do servidor de Gateway ao qual pretende ligar:  
    ![Gestor de balanceamento de carga – de rede Adicionar anfitrião Cluster: ligar](./media/log-analytics-oms-gateway/nlb03.png)
5. Em computadores que não tem ligação à Internet, certifique-se de que utilize o endereço IP do cluster quando configurar as **Propriedades do agente de monitorização Microsoft**:  
    ![Microsoft monitorização propriedades do agente – definições de Proxy](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Configurar para os trabalhadores híbrido de automatização

Se tiver os trabalhadores de híbrido automatização no seu ambiente, os passos seguintes fornecem manuais, temporárias soluções para configurar o Gateway para suportá-los.

Nos passos seguintes, tem de conhecer o Azure região onde reside a conta de automatização. Para localizar a localização:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione o serviço de automatização do Azure.
3. Selecione a conta Azure automatização adequada.
4. Ver a sua região em **localização**.  
    ![Portal Azure – localização da conta de automatização](./media/log-analytics-oms-gateway/location.png)

Utilize as tabelas seguintes para identificar o URL para cada localização:

**URLs do serviço de dados de tempo de execução de tarefa**

| **localização** | **URL** |
| --- | --- |
| América do Norte Central (EUA) | ncus-jobruntimedata-ordem de produção-su1.azure-automation.net |
| Europa Ocidental | Pedimos-jobruntimedata-ordem de produção-su1.azure-automation.net |
| Sul Central (EUA) | scus-jobruntimedata-ordem de produção-su1.azure-automation.net |
| Leste dos EUA | Eus-jobruntimedata-ordem de produção-su1.azure-automation.net |
| Canadá central | Cc-jobruntimedata-ordem de produção-su1.azure-automation.net |
| Europa Norte | -jobruntimedata-ordem de produção-su1.azure-automation.net |
| Sudeste asiático | Mar-jobruntimedata-ordem de produção-su1.azure-automation.net |
| Índia central | CID-jobruntimedata-ordem de produção-su1.azure-automation.net |
| Japão | jpe-jobruntimedata-ordem de produção-su1.azure-automation.net |
| Austrália | auxiliar-jobruntimedata-ordem de produção-su1.azure-automation.net |

**URLs do serviço agente**

| **localização** | **URL** |
| --- | --- |
| América do Norte Central (EUA) | ncus-agentservice-ordem de produção-1.azure-automation.net |
| Europa Ocidental | Pedimos-agentservice-ordem de produção-1.azure-automation.net |
| Sul Central (EUA) | scus-agentservice-ordem de produção-1.azure-automation.net |
| Leste dos EUA | eus2-agentservice-ordem de produção-1.azure-automation.net |
| Canadá central | Cc-agentservice-ordem de produção-1.azure-automation.net |
| Europa Norte | -agentservice-ordem de produção-1.azure-automation.net |
| Sudeste asiático | Mar-agentservice-ordem de produção-1.azure-automation.net |
| Índia central | CID-agentservice-ordem de produção-1.azure-automation.net |
| Japão | jpe-agentservice-ordem de produção-1.azure-automation.net |
| Austrália | auxiliar-agentservice-ordem de produção-1.azure-automation.net |

Se o seu computador está registado como trabalhador híbrido automaticamente para a aplicação de patches utilizando a solução de gestão de atualização, utilize estes passos:

1. Adicione os URLs de serviço de dados de tempo de execução da tarefa para a lista de permitidos anfitrião do Gateway OMS. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Reinicie o serviço de Gateway OMS utilizando o seguinte cmdlet do PowerShell:`Restart-Service OMSGatewayService`

Se o computador está no-efectuar-se à automatização Azure utilizando o cmdlet de registo de trabalho híbrido, utilize estes passos:

1. Adicione o URL de registo de serviço do agente para a lista de permitidos anfitrião do Gateway OMS. Por exemplo:`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Adicione os URLs de serviço de dados de tempo de execução da tarefa para a lista de permitidos anfitrião do Gateway OMS. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Reinicie o serviço de Gateway OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets do PowerShell úteis

Cmdlets pode ajudar a concluir tarefas que são necessários para atualizar as definições de configuração do Gateway OMS. Antes de utilizá-las, certifique-se de que a:

1. Instale o Gateway OMS (MSI).
2. Abra a janela do PowerShell.
3. Para importar o módulo, escreva este comando:`Import-Module OMSGateway`
4. Se nenhum erro ocorrido no passo anterior, o módulo foram importado com êxito e os cmdlets podem ser utilizados. Tipo`Get-Module OMSGateway`
5. Depois de efetuar alterações ao utilizar os cmdlets, certifique-se de que reinicie o serviço de Gateway.

Se obtiver um erro no passo 3, o módulo não era importado. O erro poderá ocorrer ao PowerShell é não é possível localizar o módulo. Pode encontrá-lo no caminho de instalação do Gateway: c:\Programas\Microsoft File\Microsoft OMS Gateway\PowerShell.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplos** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` | Chave (obrigatório) <br> Valor | Altera a configuração do serviço | `Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` | Chave | Obtém a configuração do serviço | `Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` | Endereço <br> Nome de utilizador <br> Palavra-passe | Define o endereço (e credencial) do proxy (montante) reencaminhamento | 1. Defina um proxy de resposta e a credencial:`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Defina um proxy de resposta que não necessite de autenticação:`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. limpar o proxy de resposta definição, ou seja, não necessita de um proxy de responder:`Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` |   | Obtém o endereço do proxy (montante) reencaminhamento | `Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` | Anfitrião (obrigatório) | Adiciona o sistema anfitrião à lista de permitidos | `Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHos`t | Anfitrião (obrigatório) | Remove o anfitrião da lista de permitidos | `Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` |   | Obtém o anfitrião atualmente permitido (apenas configurada localmente permitido anfitrião, não inclui automaticamente transferidos anfitriões permitidos) | `Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` | Assunto (obrigatório) | Adiciona o certificado de cliente sujeito a lista de permitidos | `Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` | Assunto (obrigatório) | Remove o assunto do certificado de cliente da lista de permitidos | `Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificat`"e" |   | Obtém o cliente atualmente permitido assuntos certificado (apenas os configurada localmente assuntos permitidos, não inclua automaticamente transferidos assuntos permitidos) | `Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>Resolução de problemas

Recomendamos que instale o agente OMS em computadores que têm o gateway instalado. Em seguida, pode utilizar o agente para recolher os eventos que estão registados pelo gateway.

![Visualizador de eventos – OMS Gateway registo](./media/log-analytics-oms-gateway/event-viewer.png)

**As descrições e OMS Gateway os IDs de evento**

A tabela seguinte mostra os IDs de evento e as descrições para eventos de registo de Gateway OMS.

| **ID** | **Descrição** |
| --- | --- |
| 400 | Qualquer erro de aplicação que não tem um ID específico |
| 401 | Configuração errada. Por exemplo: listenPort = "texto" em vez de um número inteiro |
| 402 | Excepção em mensagens de handshake TLS de análise |
| 403 | Erro de funcionamento em rede. Por exemplo: é possível ligar ao servidor de destino |
| 100 | Informações gerais |
| 101 | Serviço foi iniciado |
| 102 | Deixou de serviço |
| 103 | Recebeu um comando HTTP ligar a partir do cliente |
| 104 | Não um comando Ligar HTTP |
| 105 | Servidor de destino não está na lista de permitidos ou a porta de destino não é porta segura (443) <br> <br> Certifique-se de que o agente MMA no seu servidor de Gateway e os agentes de comunicar com o Gateway estão ligadas à área de trabalho mesmo análise de registo.|
| 105 | Erro TcpConnection – certificado de cliente inválido: CN = Gateway <br><br> Certifique-se de que: <br> <br> & #149; Está a utilizar um Gateway com o número da versão 1.0.395.0 ou superior. <br> & #149; O agente MMA no seu servidor de Gateway e os agentes de comunicar com o Gateway está ligado à área de trabalho mesmo análise de registo. |
| 106 | Qualquer motivo que a sessão TLS é suspeita e rejeitada |
| 107 | A sessão TLS foi verificada |

**Contadores de desempenho para recolher**

A tabela seguinte mostra o desempenho dos contadores disponíveis para o Gateway OMS. Pode adicionar os contadores utilizando Monitor de desempenho.

| **Nome** | **Descrição** |
| --- | --- |
| Ligação do cliente OMS Gateway/ativo | Número de ligações de rede (TCP) de cliente activo |
| Contagem de Gateway/erros OMS | Número de erros |
| OMS ligado/Gateway cliente | Número de clientes ligados |
| Contagem de Gateway/rejeição OMS | Número de rejeições devido a qualquer erro de validação de TLS |

![Contadores de desempenho do OMS Gateway](./media/log-analytics-oms-gateway/counters.png)


## <a name="get-assistance"></a>Obter ajuda

Quando que está a sessão iniciada no portal do Azure, pode criar um pedido para obter assistência com o Gateway OMS ou qualquer outro serviço Azure ou funcionalidade de um serviço.
Para pedir assistência, clique no símbolo de ponto de interrogação no canto superior direito do portal e, em seguida, clique em **novo pedido de suporte**. Em seguida, conclua o novo formulário de pedido de suporte.

![Novo pedido de suporte](./media/log-analytics-oms-gateway/support.png)

Também pode deixar comentários sobre OMS ou registo Analytics durante o [Fórum do Microsoft Azure comentários](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Próximos passos

- [Adicionar origens de dados](log-analytics-data-sources.md) para recolher dados de origens ligadas na área de trabalho OMS e armazená-lo no repositório de OMS.
