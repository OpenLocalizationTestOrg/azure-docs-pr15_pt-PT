<properties
    pageTitle="Configurar definições de proxy e a firewall no registo de análise | Microsoft Azure"
    description="Configure definições de proxy e de firewall quando os seus agentes ou serviços OMS tem de utilizar portas específicas."
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
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="banders;magoedte"/>

# <a name="configure-proxy-and-firewall-settings-in-log-analytics"></a>Configurar definições de proxy e a firewall no registo de análise

Ações necessárias para configurar o proxy e as definições da firewall para análise de registo no OMS diferem ao utilizar o Gestor de operações e os seus agentes versus monitorização agentes da Microsoft que se ligam diretamente para servidores. Reveja as secções seguintes para o tipo de agente que utiliza.

## <a name="configure-proxy-and-firewall-settings-with-the-microsoft-monitoring-agent"></a>Configurar definições de proxy e de firewall com o agente de monitorização da Microsoft

Para o Microsoft monitorização agente ligar e registe-se com o serviço OMS, tem de ter acesso ao número de porta dos seus domínios e os URLs. Se utilizar um servidor proxy para a comunicação entre o agente e o serviço OMS, terá de assegurar que os recursos adequados são acessíveis. Se utilizar uma firewall para restringir o acesso à Internet, terá de configurar a firewall para permitir o acesso a OMS. As tabelas seguintes listam as portas que necessita de OMS.

|**Agente de recursos**|**Portas**|**Ignorar inspeção HTTPS**|
|--------------|-----|--------------|
|\*. ods.opinsights.azure.com|443|Sim|
|\*. oms.opinsights.azure.com|443|Sim|
|\*. blob.core.windows.net|443|Sim|
|ODS.systemcenteradvisor.com|443| |

Pode utilizar o procedimento seguinte para configurar definições de proxy para o Microsoft monitorização Agent utilizando o painel de controlo. Terá de utilizar o procedimento para cada servidor. Se tiver muitos servidores que tem de configurar, poderá encontrar mais fácil de utilizar um script para automatizar este processo. Se Sim, consulte o seguinte procedimento [para configurar definições de proxy para o Microsoft monitorização Agent utilizando um script](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Para configurar definições de proxy para o Microsoft monitorização Agent utilizando o painel de controlo

1. Abra o **Painel de controlo**.

2. Abra o **Microsoft agente de monitorização**.

3. Clique no separador **Definições de Proxy** .<br>  
  ![separador Definições de proxy](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)

4. Selecione **utilizar um servidor proxy** e escreva o URL e porta número, se uma é semelhante ao exemplo apresentado, conforme seja necessário. Se necessitar de autenticação de servidor proxy, escreva o nome de utilizador e palavra-passe para aceder ao servidor proxy.

Utilize o seguinte procedimento para criar um script PowerShell que pode executar para definir as definições de proxy para cada agente que se liga diretamente aos servidores.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Para configurar definições de proxy para o Microsoft monitorização Agent utilizando um script

Copiar o exemplo seguinte, atualize-lo com informações específicas para o seu ambiente, guarde-o com uma extensão de nome de ficheiro PS1 e, em seguida, execute o script em cada computador que liga diretamente para o serviço OMS.

        
    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
        

## <a name="configure-proxy-and-firewall-settings-with-operations-manager"></a>Configurar definições de proxy e firewall com o Gestor de operações

Para um grupo de gestão de Gestor de operações ligar e registe-se com o serviço OMS, tem de ter acesso aos números de porta dos seus domínios e URLs. Se utilizar um servidor proxy para a comunicação entre o servidor de gestão do Gestor de operações e o serviço OMS, terá de assegurar que os recursos adequados são acessíveis. Se utilizar uma firewall para restringir o acesso à Internet, terá de configurar a firewall para permitir o acesso a OMS. Mesmo que um servidor de gestão do Gestor de operações não for um servidor proxy, poderá seus agentes. Neste caso, o servidor proxy deve ser configurado da mesma forma como agentes são para poder ativar e permitir a segurança e dados de solução de gestão do registo para são enviadas para o OMS serviço web.

Ordem de agentes de Gestor de operações comunicar com o serviço OMS, a sua infraestrutura de Gestor de operações (incluindo agentes) deve ter as definições de proxy correto e versão. A definição para agentes de proxy é especificada na consola do Gestor de operações. A versão deve ser um dos seguintes procedimentos:

- Operações Gestor 2012 SP1 atualizações 7 ou posterior
- Operações de 2012 Gestor R2 atualizações 3 ou posterior


As tabelas seguintes listam as portas relacionados com estas tarefas.

>[AZURE.NOTE] Alguns dos seguintes recursos refere Advisor e otimizadas operacionais avançados, foram ambas as versões anteriores do OMS. No entanto, os recursos listados irão mudar no futuro.

Eis uma lista de todas as portas e recursos de agente:<br>

|**Recurso de agente**|**Portas**|
|--------------|-----|
|\*. ods.opinsights.azure.com|443|
|\*. oms.opinsights.azure.com|443|
|\*.blob.Core.Windows.NET/\*|443|
|ODS.systemcenteradvisor.com|443|
<br>
Eis uma lista de recursos do servidor de gestão e portas:<br>

|**Recursos do servidor de gestão**|**Portas**|**Ignorar inspeção HTTPS**|
|--------------|-----|--------------|
|Service.systemcenteradvisor.com|443| |
|\*. service.opinsights.azure.com|443| |
|\*. blob.core.windows.net|443|Sim| 
|data.systemcenteradvisor.com|443| | 
|ODS.systemcenteradvisor.com|443| | 
|\*. ods.opinsights.azure.com|443|Sim| 
<br>
Eis uma lista de todas as portas e recursos de consola OMS e para o Gestor de operações.<br>

|**Recurso de consola OMS e para o Gestor de operações**|**Portas**|
|----|----|
|Service.systemcenteradvisor.com|443|
|\*. service.opinsights.azure.com|443|
|\*. live.com|Portas 80 e 443|
|\*. microsoft.com|Portas 80 e 443|
|\*. microsoftonline.com|Portas 80 e 443|
|\*. mms.microsoft.com|Portas 80 e 443|
|login.Windows.NET|Portas 80 e 443|
<br>

Utilize os seguintes procedimentos para registar o seu grupo de gestão do Gestor de operações com o serviço OMS. Se estiver a ter problemas de comunicação entre o grupo de gestão e o serviço OMS, utilize os procedimentos de validação para resolver problemas de transmissão de dados para o serviço OMS.

### <a name="to-request-exceptions-for-the-oms-service-endpoints"></a>Para pedir exceções para os pontos finais de serviço OMS

1. Utilize as informações da primeira tabela apresentada anteriormente para assegurar que os recursos necessários para o servidor de gestão do Gestor de operações são acessíveis através de qualquer firewalls poderá ter.
2. Utilize as informações da segunda tabela apresentada anteriormente para assegurar que os recursos necessários da consola de operações em Gestor de operações e OMS são acessíveis através de qualquer firewalls poderá ter.
3. Se utilizar um servidor proxy com o Internet Explorer, certifique-se de que está configurado e a funcionar corretamente. Para verificar, pode abrir uma ligação segura web (HTTPS), por exemplo [https://bing.com](https://bing.com). Se a ligação à web seguro não funciona num browser,-provavelmente não funciona na consola de gestão de Gestor de operações com os serviços web na nuvem.

### <a name="to-configure-the-proxy-server-in-the-operations-manager-console"></a>Para configurar o servidor de proxy na consola do Gestor de operações

1. Abra a consola do Gestor de operações e selecione a área de trabalho de **Administração** .

2. Expanda **Operacionais informações**e, em seguida, selecione **Operacionais de informações de ligação**.<br>  
    ![Ligação de OMS Gestor de operações](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. Na vista de OMS ligação, clique em **Configurar um servidor Proxy**.<br>  
    ![Ligação de OMS Gestor de operações configurar servidor Proxy](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. Em informações definições assistente operacionais avançados: Servidor Proxy, selecione **utilizar um servidor proxy para aceder ao serviço de Web operacionais informações**e, em seguida, escreva o URL com a porta número; por exemplo, **http://myproxy:80**.<br>  
    ![Endereço de proxy OMS Gestor de operações](./media/log-analytics-proxy-firewall/proxy-om03.png)


### <a name="to-specify-credentials-if-the-proxy-server-requires-authentication"></a>Para especificar as credenciais se o servidor de proxy requer autenticação
 Definições de credenciais de servidor proxy e tem a serem propagadas para computadores geridos que apresentará um relatório ao OMS. Os servidores devem ser no *Grupo do servidor de monitorização Microsoft sistema Centro Advisor*. Credenciais são encriptadas no registo de cada servidor no grupo.

1. Abra a consola do Gestor de operações e selecione a área de trabalho de **Administração** .
2. Em **Configuração RunAs**, selecione **perfis**.
3. Abra o perfil de **Sistema Centro Advisor executar como Proxy do perfil** .  
    ![imagem do perfil de sistema Centro Advisor executar como Proxy](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. No executar como perfil assistente, clique em **Adicionar** para utilizar uma conta executar como. Pode criar uma nova conta executar como ou utilizar uma conta existente. Esta conta tem de ter permissões suficientes para passar através do servidor proxy.  
    ![imagem do assistente executar como o perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. Para definir a conta para gerir, selecione **uma classe seleccionada, o grupo ou o objeto** para abrir a caixa de pesquisa do objeto.  
    ![imagem do assistente executar como o perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Procurar em seguida, selecione o **Grupo do servidor de monitorização Microsoft sistema Centro Advisor**.  
    ![imagem da caixa de pesquisa de objeto](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Clique em **OK** para fechar a caixa Adicionar um executar como conta.  
    ![imagem do assistente executar como o perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Concluir o assistente e guarde as alterações.  
    ![imagem do assistente executar como o perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)


### <a name="to-validate-that-oms-management-packs-are-downloaded"></a>Para validar essa gestão OMS packs são transferidos

Caso tenha adicionado soluções para OMS, pode vê-los na consola do Gestor de operações como pacotes de gestão em **Administração**. Procurar por *Classificação do Centro de sistema* localizar rapidamente.  
    ![pacotes de gestão transferidos](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png) ou, também pode procurar pacotes de gestão de OMS utilizando o seguinte comando Windows PowerShell no servidor de gestão de Gestor de operações:

    ```
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
    ```

### <a name="to-validate-that-operations-manager-is-sending-data-to-the-oms-service"></a>Para que o Gestor de operações de validar está a enviar dados para o serviço OMS

1. No servidor de gestão de Gestor de operações, abra o Monitor de desempenho (perfmon.exe) e selecione **Monitor de desempenho**.
2. Clique em **Adicionar**e, em seguida, selecione o **Estado de funcionamento do serviço de gestão de grupos**.
3. Adicione todos os contadores começam com **HTTP**.  
    ![Adicionar contadores](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Se a configuração do Gestor de operações for boa, que irá ver atividade para contadores de gestão de estado de funcionamento de serviço para eventos e outros itens de dados, com base nos pacotes de gestão que tenha adicionado no OMS e a política de recolha de registo configurada.  
    ![Desempenho monitorizar a mostrar a actividade](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)


## <a name="next-steps"></a>Próximos passos

- [Soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md) para adicionar a funcionalidade e reunir dados.
- Se familiarizar com [as pesquisas de registo](log-analytics-log-searches.md) ver informações detalhadas recolhidas pela soluções.
