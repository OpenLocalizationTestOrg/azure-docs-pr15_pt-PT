<properties
    pageTitle="Instalação do Azure AD ligar-se do Estado de funcionamento agente | Microsoft Azure"
    description="Esta é a página de Azure AD ligar-se do Estado de funcionamento que descreva a instalação do agente para o AD FS e sincronização."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>


# <a name="azure-ad-connect-health-agent-installation"></a>Ligação do Azure AD instalação do agente de estado de funcionamento

Este documento orienta-o através de instalar e configurar o Azure AD ligar Health Agents. Pode transferir os agentes do [aqui](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

##  <a name="requirements"></a>Requisitos de
A tabela que se segue é uma lista de requisitos de utilização do Azure AD ligar-se do Estado de funcionamento.

| Requisito de registo | Descrição|
| ----------- | ---------- |
|Azure AD Premium| Azure AD ligar o estado de funcionamento do é uma funcionalidade do Azure AD Premium e requer Azure AD Premium. </br></br>Para mais informações, consulte o artigo [Introdução ao Azure AD Premium](active-directory-get-started-premium.md) </br>Para iniciar uma avaliação de 30 dias gratuita, consulte o artigo [Iniciar uma versão de avaliação.](https://azure.microsoft.com/trial/get-started-active-directory/)|
|Tem de ser um administrador global do seu Azure AD para começar a utilizar o Azure AD ligar-se do Estado de funcionamento|Por predefinição, apenas os administradores globais podem instalar e configurar os agentes de estado de funcionamento para começar, aceder ao portal e executar todas as operações Azure AD ligar-se do Estado de funcionamento. Para mais informações, consulte [administrar no diretório da sua Azure AD](active-directory-administer.md). <br><br> Pode utilizar a função de controlo de acesso com base para permitir o acesso ao Azure AD ligar-se do Estado de funcionamento a outros utilizadores na sua organização. Para obter mais informações, consulte o artigo [função controlo de acesso com base para Azure AD ligar-se do Estado de funcionamento.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Importantes:** A conta que utilizou ao instalar os agentes tem de ser uma conta escolar ou profissional. Não pode ser uma conta Microsoft. Para obter mais informações, consulte o artigo [Inscrever-se no Azure como uma organização](sign-up-organization.md)
|Azure agente do AD ligar-se do Estado de funcionamento está instalado em cada servidor alvo| Azure AD ligar o estado de funcionamento do requer uma instalação do agente nos servidores, para fornecer os dados que são visualizados no portal. </br></br>Por exemplo, para obter dados a partir da sua infraestrutura do AD FS no local, o agente tem de estar instalado nos servidores AD FS, Proxy do AD FS e Proxy de aplicação Web. Da mesma forma, para obter os dados nas suas instalações infraestrutura de AD DS, o agente tem de estar instalado nos controladores de domínio. </br></br>**Importantes:** A conta que utilizou ao instalar os agentes tem de ser uma conta escolar ou profissional. Não pode ser uma conta Microsoft. Para obter mais informações, consulte o artigo [Inscrever-se no Azure como uma organização](sign-up-organization.md)|
|Conectividade de saída para os pontos finais de serviço do Azure|Durante a instalação e runtime, o agente necessita de conectividade ao pontos finais de serviço do Azure AD ligar-se do Estado de funcionamento. Se estiver bloqueada conectividade de saída, certifique-se de que os pontos finais seguintes são adicionados à lista de permitidos: </br></br><li>& #42;. blob.Core.Windows.NET </li><li>& #42;. Queue.Core.Windows.NET</li><li>adhsprodwus.servicebus.Windows.NET - porta: 5671 </li><li>https://Management.Azure.com </li><li>https://S1.adhybridhealth.Azure.com/</li><li>https://policykeyservice.DC.AD.MSFT.NET/</li><li>https://login.Windows.NET</li><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li> |
|Portas da firewall no servidor a executar o agente.| O agente requer as seguintes portas da firewall para ser aberto para que o agente comunicar com os pontos finais de serviço de estado de funcionamento do Azure AD.</br></br><li>Porta 443 do TCP/UDP recomendadas</li><li>Portas TCP/UDP recomendadas 5671</li>
|Permitir que os Web sites seguintes se segurança avançada do IE é activada| Se segurança avançada do IE estiver ativada, os Web sites seguintes tem permitidos no servidor que vai possui o agent instalado.</br></br><li>https://login.microsoftonline.com</li><li>https://Secure.aadcdn.microsoftonline-p.com</li><li>https://login.Windows.NET</li><li>Servidor de Federação para a sua organização ao Azure Active Directory. Por exemplo: https://sts.contoso.com</li>




## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalar o Azure AD ligar agente de estado de funcionamento do AD FS
Para iniciar a instalação do agente, faça duplo clique no ficheiro de .exe que transferiu. No primeiro ecrã, clique em instalar.

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health-requirements/install1.png)

Quando a instalação estiver concluída, clique em configurar agora.

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health-requirements/install2.png)

Uma linha de comandos é iniciada, seguido de algumas PowerShell que executa Register AzureADConnectHealthADFSAgent. Quando lhe for pedido para iniciar sessão no Azure, vá para a frente e inicie sessão.

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health-requirements/install3.png)


Após iniciar sessão, PowerShell irão continuar. Assim que for concluída, pode fechar PowerShell e a configuração está concluída.

Neste momento, os serviços devem ser iniciados automaticamente permitindo que o agente monitorizar e recolher dados. Se não tiver cumprida pré-requisitos descritos nas secções anteriores, avisos aparecem na janela do PowerShell. Certifique-se de que concluir os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar o agente. A captura de ecrã seguinte é um exemplo destes erros.

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health-requirements/install4.png)

Para verificar que o agente de ter sido instalado, procure os seguintes serviços no servidor. Se tiver concluído a configuração, eles devem já estar em execução. Caso contrário, estes são parados até que a configuração está concluída.

- Estado de funcionamento AD FS diagnósticos serviço de ligação do Azure AD
- Serviço de informações de estado de funcionamento AD FS de ligação do Azure AD
- Estado de funcionamento AD FS monitorizar o serviço de ligação do Azure AD

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health-requirements/install5.png)


### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Instalação do agente em servidores do Windows Server 2008 R2

Passos para servidores do Windows Server 2008 R2:

1. Certifique-se de que o servidor está em execução a Service Pack 1 ou superior.
1. Desative o IE ESC para instalação do agente:
1. Instale o Windows PowerShell 4.0 em cada um dos servidores antes de instalar o agente do Estado de funcionamento do AD. Para instalar o Windows PowerShell 4.0:
 - Instale o [Microsoft .NET Framework 4,5](https://www.microsoft.com/download/details.aspx?id=40779) utilizando a seguinte ligação para transferir o Windows installer offline.
 - Instalar o ISE do PowerShell (a partir de funcionalidades do Windows)
 - Instalar o [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
 - Instalar o Internet Explorer versão 10 ou posterior no servidor. (Obrigatório pelo Estado de funcionamento do serviço para autenticar, com as suas credenciais de administrador do Azure).
1. Para obter mais informações sobre como instalar o Windows PowerShell 4.0 no Windows Server 2008 R2, consulte o artigo wiki [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Activar a auditoria do AD FS

> [AZURE.NOTE] Esta secção aplica-se apenas para os servidores de Federação do AD FS.

Ordem para a funcionalidade de análise de utilização recolher e analisar dados, o agente do Azure AD ligar-se do Estado de funcionamento tem as informações nos registos de auditoria do AD FS. Por predefinição, estes registos não estão ativados. Utilize os seguintes procedimentos para ativar o AD FS auditoria e para localizar registos de auditoria AD FS, nos seus servidores de AD FS.

#### <a name="to-enable-auditing-for-ad-fs-20"></a>Para permitir a auditoria do AD FS 2.0

1. Clique em **Iniciar**, aponte para **programas**, aponte para **Ferramentas administrativas**e, em seguida, clique em **Política de segurança Local**.
2. Navegue para a pasta de **Gestão de direitos de políticas de definições de segurança** e, em seguida, faça duplo clique em gerar auditorias de segurança.
3. No separador **Definição de segurança Local** , certifique-se de que a conta do AD FS 2.0 serviço está listada. Se não estiver presente, clique em **Adicionar utilizador ou grupo** e adicioná-lo para a lista e, em seguida, clique em **OK**.
4. Para ativar a auditoria, abra uma linha de comandos com privilégios elevados e execute o seguinte comando:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Feche a política de segurança Local e, em seguida, abra o snap-in Gestão. Para abrir o snap-in Gestão, clique em **Iniciar**, aponte para **programas**, aponte para **Ferramentas administrativas**e, em seguida, clique em AD FS 2.0 gestão.
6. No painel de ações, clique em Editar propriedades de serviços de Federação.
7. Na caixa de diálogo **Propriedades de serviços de Federação** , clique no separador **eventos** .
8. Selecione as caixas de verificação **sucesso auditorias** e **auditorias falha** .
9. Clique em **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para permitir a auditoria do AD FS no Windows Server 2012 R2

1. Abra a **Política de segurança Local** ao abrir o **Gestor de servidor** no ecrã Iniciar ou Gestor de servidor na barra de tarefas no ambiente de trabalho, em seguida, clique em **Ferramentas de/Local de política de segurança**.
2. Navegue para a pasta de **Definições de segurança \ atribuição de direitos** e, em seguida, faça duplo clique em **Gerar auditorias de segurança**.
3. No separador **Definição de segurança Local** , certifique-se de que está listada a conta de serviço do AD FS. Se não estiver presente, clique em **Adicionar utilizador ou grupo** e adicioná-lo para a lista e, em seguida, clique em **OK**.
4. Para ativar a auditoria, abra uma linha de comandos com privilégios elevados e execute o seguinte comando:<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Feche a **Política de segurança Local**e, em seguida, abra o snap-in de **Gestão do AD FS** (no Gestor de servidor, clique em ferramentas e, em seguida, selecione gestão do AD FS).
6. No painel de ações, clique em **Editar propriedades de serviços de Federação**.
7. Na caixa de diálogo Propriedades de serviços de Federação, clique no separador **eventos** .
8. Selecione as caixas de verificação **sucesso auditorias e falha auditorias** e, em seguida, clique em **OK**.






#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para localizar a auditoria de AD FS os registos


1. Abra o **Visualizador de eventos**.
2. Ir para os registos do Windows e selecione **segurança**.
3. À direita, clique em **Registos atual do filtro**.
4. Em origem do evento, selecione **Auditoria do AD FS**.

![Registos de auditoria de AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Se existir uma política de grupo é desativar o AD FS auditoria, não consegue recolher informações Azure agente do AD ligar-se do Estado de funcionamento. Certifique-se de que não tem uma política de grupo que pode ser desativar auditoria.

[//]: # (Start of Agent Proxy Configuration Section)

## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalar o agente para a sincronização do Azure AD ligar-se do Estado de funcionamento
O agente do Azure AD ligar-se do Estado de funcionamento para a sincronização é instalado automaticamente na compilação mais recente da ligação do Azure AD. Para utilizar a ligação do Azure AD para a sincronização, é necessário transferir a versão mais recente da ligação do Azure AD e instalá-lo. Pode transferir a versão mais recente [aqui](http://www.microsoft.com/download/details.aspx?id=47594).

Para verificar que o agente de ter sido instalado, procure os seguintes serviços no servidor. Se tiver concluído a configuração, eles devem já estar em execução. Caso contrário, estes são parados até que a configuração está concluída.

- Serviço de informações de sincronização do Estado de funcionamento de ligação do Azure AD
- Monitorizar o serviço de sincronização de estado de funcionamento de ligação do Azure AD

![Verifique a ligação do Estado de funcionamento do Azure AD para a sincronização](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] Lembre-se de que utilizar o Azure AD ligar-se do Estado de funcionamento requer Azure AD Premium. Se não tiver Azure AD Premium, não conseguir concluir a configuração no portal do Azure. Para mais informações, consulte a [página requisitos](active-directory-aadconnect-health-agent-install.md#requirements).


## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Manual Azure AD ligar o estado de funcionamento para o registo de sincronização
Se o Azure AD ligar o estado de funcionamento de registo do agente de sincronização falha após a instalação do ligação do Azure AD com êxito, pode utilizar o seguinte comando do PowerShell para registar o agente manualmente.

>[AZURE.IMPORTANT] Utilizar este comando do PowerShell só é necessário se o registo de agente falhar depois de instalar a ligação do Azure AD.

O PowerShell seguinte comando é necessário apenas quando o registo do agente de estado de funcionamento falha mesmo após uma instalação com êxito e a configuração da ligação do Azure AD. Os serviços do Azure AD ligar-se do Estado de funcionamento serão iniciada após o agente foi registado com êxito.

Pode registar manualmente agente do Azure AD ligar-se do Estado de funcionamento para sincronizar utilizando o seguinte comando PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

O comando leva-o até das seguintes parâmetros:

- AttributeFiltering: $true (predefinição) - se a ligação do Azure AD não está a sincronizar o atributo predefinido defina e foi personalizado para utilizar um conjunto de atributo filtrada. $false contrário.
- StagingMode: $false (predefinição) - se o servidor de ligação do Azure AD não está no modo, $true de transição, se o servidor está configurado para ser no modo de transição.

Quando lhe for pedido para autenticação deve utilizar a mesma conta de administrador global (tais como admin@domain.onmicrosoft.com) que foi utilizado para configurar a ligação do Azure AD.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalar o Azure AD ligar-se do Estado de funcionamento agente para o AD DS
Para iniciar a instalação do agente, faça duplo clique no ficheiro de .exe que transferiu. No primeiro ecrã, clique em instalar.

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Quando a instalação estiver concluída, clique em configurar agora.

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Uma linha de comandos é iniciada, seguido de algumas PowerShell que executa Register AzureADConnectHealthADDSAgent. Quando lhe for pedido para iniciar sessão no Azure, vá para a frente e inicie sessão.

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Após iniciar sessão, PowerShell irão continuar. Assim que for concluída, pode fechar PowerShell e a configuração está concluída.

Neste momento, os serviços devem ser iniciados automaticamente permitindo que o agente monitorizar e recolher dados. Se não tiver cumprida pré-requisitos descritos nas secções anteriores, avisos aparecem na janela do PowerShell. Certifique-se de que concluir os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) antes de instalar o agente. A captura de ecrã seguinte é um exemplo destes erros.

![Verifique a ligação do Estado de funcionamento do Azure AD para o AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Para verificar que o agente de ter sido instalado, procure os seguintes serviços no controlador de domínio.

- Serviço de informações de estado de funcionamento AD DS ligação do Azure AD
- Estado de funcionamento AD DS monitorizar o serviço de ligação do Azure AD

Se tiver concluído a configuração, estes serviços devem já estar em execução. Caso contrário, estes são parados até que a configuração está concluída.

![Verificar o estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds-on-server-core"></a>Instalar o Azure AD ligar-se do Estado de funcionamento agente para o AD DS no servidor Core.
Depois de instalar o ficheiro .exe, pode concluir o processo de registo, utilizando o comando PowerShell seguinte:

`Register-AzureADConnectHealthADDSAgent -Credential $cred`

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurar Azure AD ligar-se do Estado de funcionamento agentes de utilizar o HTTP Proxy
Pode configurar Azure AD ligar-se do Estado de funcionamento agentes para trabalhar com um servidor HTTP Proxy.

>[AZURE.NOTE]
- Utilizar o comando "Netsh WinHttp set ProxyServerAddress" não é suportada à medida que o agente utiliza System.Net para tornar os pedidos de web em vez do Microsoft Windows HTTP Services.
- O endereço Http Proxy configurado é utilizada para pass-through mensagens encriptadas do Https.
- Proxies autenticados (utilizando HTTPBasic) não são suportados.

### <a name="change-health-agent-proxy-configuration"></a>Configuração do Proxy de agente de estado de funcionamento de alteração
Tem as seguintes opções para configurar o Azure AD ligar estado de funcionamento do serviço para utilizar um servidor HTTP Proxy.

>[AZURE.NOTE]Todos os serviços do agente de estado de funcionamento ligar Azure AD tem de ser reiniciados, para que as definições de proxy para ser atualizados. Execute o seguinte comando:<br>
Restart-Service AdHealth *

#### <a name="import-existing-proxy-settings"></a>Importar definições de proxy existente

##### <a name="import-from-internet-explorer"></a>Importar a partir do Internet Explorer
Definições de proxy HTTP do Internet Explorer podem ser importadas para ser utilizado pelo Azure AD ligar Health Agents. Em cada um dos servidores a executar o agente de estado de funcionamento, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importar do WinHTTP
Definições de proxy WinHTTP podem ser importadas para ser utilizado pelo Azure AD ligar Health Agents. Em cada um dos servidores a executar o agente de estado de funcionamento, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Especificar manualmente os endereços Proxy
Pode especificar manualmente um servidor proxy em cada um dos servidores a executar o agente de estado de funcionamento, executando o seguinte comando PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemplo: *conjunto AzureAdConnectHealthProxySettings - HttpsProxyAddress meu_servidor_proxy: 443*

- "endereço" pode ser um nome de servidor passíveis de resolução de DNS ou de um endereço IPv4
- pode ser omitido "porto". Se for omitido, em seguida, 443 é escolhido como porta predefinida.

#### <a name="clear-existing-proxy-configuration"></a>Limpar configuração do proxy existente
Pode limpar a configuração do proxy existente, executando o seguinte comando:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Leia as definições de proxy actuais
Pode ler as definições de proxy atualmente configurado, executando o seguinte comando:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testar a conectividade para Azure AD ligar-se do Estado de funcionamento serviço
É possível que podem surgir problemas que causam o agente de Azure AD ligar-se do Estado de funcionamento perder a ligação com o serviço do Azure AD ligar-se do Estado de funcionamento. Estes incluem problemas de rede, problemas de permissão ou diversas outras razões.

Se o agente não conseguir enviar dados para o serviço do Azure AD ligar-se do Estado de funcionamento durante mais de duas horas, é indicado com o seguinte alerta no portal do: "dados do Estado de funcionamento de serviço não não atualizados." Pode confirmar se o afetado Azure AD Connect agente de estado de funcionamento é poderá carregar os dados para o serviço do Azure AD ligar-se do Estado de funcionamento ao executar o seguinte comando PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

O parâmetro de função atualmente leva-o até os seguintes valores:

- ADFS
- Sincronização
- ADICIONA

Pode utilizar o sinalizador - ShowResults no comando para ver os registos detalhados. Utilize o exemplo seguinte:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Para utilizar a ferramenta de conectividade, primeiro tem de concluir o registo de agente. Se não conseguir concluir o registo de agente, certifique-se de que se satisfeitos todos os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) do Azure AD ligar-se do Estado de funcionamento. Este teste de conectividade será executado por predefinição durante o registo do agente.



## <a name="related-links"></a>Ligações relacionadas

* [Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health.md)
* [Operações de estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas mais frequentes do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-faq.md)
* [Histórico de versões do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-version-history.md)
