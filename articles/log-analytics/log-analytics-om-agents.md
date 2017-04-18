<properties
    pageTitle="Ligar o Gestor de operações a análise de registo | Microsoft Azure"
    description="Para manter o seu investimento existente no Gestor de operações do Centro de sistema e utilizar funcionalidades adicional com a análise de registo, pode integrar o Gestor de operações com a área de trabalho OMS."
    services="log-analytics"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="magoedte"/>

# <a name="connect-operations-manager-to-log-analytics"></a>Ligar o Gestor de operações a análise de registo

Para manter o seu investimento existente no Gestor de operações do Centro de sistema e utilizar funcionalidades adicional com a análise de registo, pode integrar o Gestor de operações com a área de trabalho OMS.  Esta opção permite-lhe tirar partido de oportunidades de OMS enquanto continua a utilizar o Gestor de operações para:

- Continuar a saúde dos seus serviços de TI com o Gestor de operações de monitorização
- Manter a integração com o seu soluções ITSM gestão problema e incidente de suporte
- Gerir o ciclo de vida de agentes implementado no local e máquinas virtuais do público nuvem IaaS que monitorizar com o Gestor de operações

Integrar com o Gestor de operações do Centro de sistema soma o valor para a sua estratégia de operações de serviço, tirando partido a velocidade e eficiência da OMS no recolher, armazenar e analisar dados a partir do Gestor de operações.  OMS ajuda correlacionar e trabalham para identificar as falhas de problemas e para revestimentos reoccurrences apoio o processo de gestão de problema existente.   A flexibilidade do motor de busca para examinar desempenho, eventos e dados do alerta, com dashboards avançados e as capacidades de relatórios para expor estes dados de formas significativas, demonstra a intensidade do que OMS traz completar o Gestor de operações.

Agentes de elaboração de relatórios ao grupo de gestão de Gestor de operações recolhem dados de servidores com base nas origens de dados de análise de registo e as soluções que ativou na sua subscrição do OMS.  Dependendo da solução activou, dados destas soluções são quer enviadas diretamente a partir de um servidor de gestão do Gestor de operações para o serviço de web OMS ou devido ao volume de dados recolhidos no sistema gerido agente, são enviadas diretamente do agente de serviço web OMS. Servidor de gestão de reencaminha diretamente os dados OMS ao serviço web OMS, é nunca escrita à base de dados OperationsManager ou OperationsManagerDW.  Quando um servidor de gestão perde conectividade com o serviço de web OMS,-coloca em cache os dados localmente até comunicação for restabelecida com OMS.  Se o servidor de gestão está offline devido a indisponibilidade não planeada ou de manutenção planeada, servidor de gestão de outro no grupo gestão será retomada conectividade com OMS.  

O diagrama seguinte ilustra a ligação entre os servidores de gestão e agentes num grupo de gestão do Gestor de operações do Centro de sistema e OMS, incluindo as portas e direção.   

![OMS-operações-Gestor--diagrama de integração](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## <a name="system-requirements"></a>Requisitos de sistema
Antes de começar, reveja os seguintes detalhes para verificar que cumprem os pré-requisitos.

- OMS suporta apenas o Gestor de operações 2012 SP1 UR6 e superiores e 2012 de Gestor de operações R2 UR2 e superiores.  Suporte de proxy foi adicionado no Gestor de operações 2012 SP1 UR7 e 2012 de Gestor de operações R2 UR3.
- Todos os agentes de Gestor de operações tem de cumprir requisitos de suporte mínimo. Certifique-se de que agentes são até a atualização mínima, caso contrário, o tráfego de agente do Windows irá falhar e muitos erros poderão preencher o registo de eventos do Gestor de operações.
- Uma subscrição do OMS.  Para obter mais informações, reveja a [começar a trabalhar com a análise de registo](log-analytics-get-started.md).

## <a name="connecting-operations-manager-to-oms"></a>Ligar o Gestor de operações a OMS
Execute as seguintes séries de passos para configurar o seu grupo de gestão do Gestor de operações para ligar a uma das suas áreas de trabalho OMS.

1. Na consola do Gestor de operações de, selecione a área de trabalho de **Administração** .
2. Expanda o nó do conjunto de aplicações de gestão de operações e clique em **ligação**.
3. Clique na ligação em **Registe-se ao conjunto de aplicações de gestão de operações** .
4. No **Assistente de ativação do conjunto de aplicações de gestão de operações: autenticação** da página, introduza o endereço de e-mail ou número de telefone e palavra-passe da conta de administrador que está associada com a sua subscrição OMS e clique em **Iniciar sessão**.
5. Depois de que forem autenticados com êxito, pelo **Assistente de ativação do conjunto de aplicações de gestão de operações: selecione a área de trabalho** da página, ser-lhe-á pedido para selecionar a área de trabalho OMS.  Se tiver mais do que uma área de trabalho, selecione a área de trabalho que pretende registe-se com o grupo de gestão do Gestor de operações a partir da lista pendente e, em seguida, clique em **seguinte**.

    >[AZURE.NOTE] Gestor de operações suporta apenas uma área de trabalho OMS cada vez. A ligação e os computadores que foram registados para OMS com a área de trabalho anterior são removidos da OMS.

6. No **Assistente de ativação do conjunto de aplicações de gestão de operações: Resumo** da página, confirmar as suas definições e se estiverem corretas, clique em **Criar**.
7. No **Assistente de ativação do conjunto de aplicações de gestão de operações: concluir** página, clique em **Fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar gerido agente computadores
Depois de configurar integração com a área de trabalho OMS, este procedimento só estabelece uma ligação com OMS, não são recolhidos dados a partir de agentes de elaboração de relatórios ao seu grupo de gestão. Isto não ocorrer até depois de configurar os computadores que gerido agente específicos irão recolher dados para análise de registo. Pode selecionar os objetos de computador individualmente ou pode selecionar um grupo que contém objetos de computador Windows. Não pode selecionar um grupo que contém as instâncias da outra classe, tal como discos lógicos ou bases de dados SQL.

1. Abra a consola do Gestor de operações e selecione a área de trabalho de **Administração** .
2. Expanda o nó do conjunto de aplicações de gestão de operações e clique em **ligação**.
3. Clique na ligação **Adicionar um computador/grupo** sob as ações no lado direito do painel de cabeçalho.
4. Na caixa de diálogo **Pesquisa de computador** pode procurar computadores ou grupos controlados pelo Gestor de operações. Selecione computadores ou grupos para incorporada para OMS, clique em **Adicionar**e, em seguida, clique em **OK**.

Pode ver computadores e configurados para recolher dados do nó gerido computadores em conjunto de aplicações de gestão de operações na área de trabalho **Administração** da consola do operações de grupos.  A partir daqui pode adicionar ou remover computadores e grupos, conforme necessário.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Configurar definições de proxy OMS na consola operações
Se for um servidor proxy interno entre o grupo de gestão e o serviço web OMS, execute os seguintes passos.  Estas definições centralmente são geridas a partir do grupo de gestão e distribuídas aos sistemas geridos agente que estão incluídos no âmbito para recolher dados de OMS.  Isto é útil para quando determinadas soluções ignoram o servidor de gestão e enviar dados diretamente para o serviço web OMS.

1. Abra a consola do Gestor de operações e selecione a área de trabalho de **Administração** .
2. Expanda o conjunto de aplicações de gestão de operações e, em seguida, clique em **ligações**.
3. Na vista de OMS ligação, clique em **Configurar um servidor Proxy**.
4. No **Assistente de conjunto de aplicações de gestão de operações: servidor Proxy** da página, selecione **utilizar um servidor proxy para o conjunto de gestão de operações de aceder**e, em seguida, escreva o URL com o número da porta, por exemplo, http://corpproxy:80 e, em seguida, clique em **Concluir**.

Se necessitar de autenticação de servidor proxy, execute os seguintes passos para configurar definições que precisam de serem propagadas para computadores geridos que apresentará um relatório ao OMS no grupo gestão e de credenciais.

1. Abra a consola do Gestor de operações e selecione a área de trabalho de **Administração** .
2. Em **Configuração RunAs**, selecione **perfis**.
3. Abra o perfil de **Sistema Centro Advisor executar como Proxy do perfil** .
4. No executar como perfil assistente, clique em Adicionar para utilizar uma conta executar como. Pode criar uma nova [conta executar como](https://technet.microsoft.com/library/hh321655.aspx) ou utilizar uma conta existente. Esta conta tem de ter permissões suficientes para passar através do servidor proxy.
5. Para definir a conta para gerir, escolha **uma classe seleccionada, o grupo ou o objeto**, clique em **seleccionar...** e, em seguida, clique em **Agrupar...** Para abrir a caixa de **Pesquisa de grupo** .
6. Procurar e, em seguida, selecione o **Grupo do servidor de monitorização Microsoft sistema Centro Advisor**.  Após selecionar o grupo para fechar a caixa de **Pesquisa de grupo** , clique em **OK** .
7.  Clique em **OK** para fechar a caixa **Adicionar uma conta de executar como** .
8.  Clique em **Guardar** para concluir o assistente e guarde as alterações.

Depois da ligação é criada e configurar agentes de que serão recolher e dados do relatório para OMS, a configuração seguinte é aplicada no grupo gestão, não necessariamente por ordem:

- A conta executar como **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** é criada.  É associado com o perfil executar como **Microsoft sistema Centro Advisor executar como perfil Blob** e filtragem de duas classes - **Servidor de recolha** e **Grupo de gestão do Gestor de operações**.
- Dois conectores são criadas.  O primeiro é denominado **Microsoft.SystemCenter.Advisor.DataConnector** e é configurado automaticamente com uma subscrição que reencaminhará todos os alertas gerados a partir da instâncias de classes todos no grupo gestão para OMS a análise de registo. Segundo conector é **Advisor conexão**, que é responsável para comunicar com o serviço web OMS e partilha de dados.
- Agentes e grupos que selecionou para recolher dados no grupo Gestão serão adicionados ao **Grupo de servidor de monitorização Microsoft sistema Centro Advisor**.

## <a name="management-pack-updates"></a>Atualizações de pacote de gestão
Após a configuração está concluída, o grupo de gestão do Gestor de operações estabelece uma ligação com o serviço OMS.  Servidor de gestão de irá sincronizar com o serviço web e receber informações sobre a configuração atualizada sob a forma de pacotes de gestão para as soluções ativou o que se integram com o Gestor de operações.   Gestor de operações irá procurar atualizações para estes gestão packs transferir automaticamente e importação-los quando forem disponibilizadas.  Existem duas regras em particular que controlar este comportamento:

- **Microsoft.SystemCenter.Advisor.MPUpdate** - atualiza os pacotes de gestão de OMS base. Por predefinição para ser executada todas as horas doze (12).
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - pacotes de gestão de solução atualizações ativados na área de trabalho. É executado em minutos cinco (5) por predefinição.

Pode substituir estas duas regras para impedir que a transferência automática ao desativá-los ou modificar a frequência com que frequência o servidor de gestão sincroniza com OMS para determinar se um pacote de gestão de novo está disponível e deve ser transferido.  Siga os passos [como substituir uma regra ou o Monitor](https://technet.microsoft.com/library/hh212869.aspx) modificar o parâmetro de **frequência** com um valor em segundos para alterar a agenda de sincronização ou modifique o parâmetro **ativada** para desativar as regras.  Substitui a todos os objetos de classe de grupo de gestão do Gestor de operações de destino.

Se pretender continue a seguir o processo de controlo de alteração existente para controlar versões de pacote de gestão no seu grupo de gestão de produção, pode desativar as regras e ativá-las durante as horas específicas quando são permitidas actualizações. Se tiver uma desenvolvimento ou grupo de gestão das perguntas e respostas no seu ambiente e que tem ligação à Internet, pode configurar esse grupo gestão com uma área de trabalho OMS para suportam este cenário.  Isto irá permitem-lhe rever e avaliar as versões dos OMS gestão packs iterativas antes de soltá-los para o seu grupo de gestão de produção.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Mudar de um grupo do Gestor de operações para uma nova área de trabalho OMS
1. Inicie sessão na sua subscrição OMS e criar nova área de trabalho no [Conjunto de aplicações do Microsoft operações gestão](http://oms.microsoft.com/).
2. Abra a consola do Gestor de operações com uma conta que é um membro da função de administradores de Gestor de operações de e selecione a área de trabalho de **Administração** .
3. Expanda o conjunto de aplicações de gestão de operações e selecione **ligações**.
4. Selecione a ligação **Configurar novamente operação de conjunto de gestão de aplicações** no lado meio do painel.
5. Siga o **Assistente de ativação do conjunto de aplicações de gestão de operações** e introduza o endereço de e-mail ou número de telefone e palavra-passe da conta de administrador que está associada a sua nova área de trabalho OMS.

    > [AZURE.NOTE] O **Assistente de ativação do conjunto de aplicações de gestão de operações: selecione a área de trabalho** página irá apresentar a área de trabalho existente que está a ser utilizado.


## <a name="validate-operations-manager-integration-with-oms"></a>Validar a integração do Gestor de operações com OMS
Existem algumas maneiras diferentes, pode verificar que a sua OMS à integração do Gestor de operações é efetuada com êxito.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Para confirmar a integração a partir do portal OMS

1.  No portal do OMS, clique no mosaico **Definições**
2.  Selecione **ligado origens**.
3.  Na tabela na secção de Gestor de operações do Centro de sistema, deverá ver o nome do grupo de gestão de quando será listado com o número de agentes e estado dados última foram recebidos.

    ![OMS-definições-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

4.  Tenha em atenção o valor de **ID de área de trabalho** no lado esquerdo da página de definições.  Irá validá-lo contra o grupo de gestão de Gestor de operações abaixo.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Para confirmar a integração a partir da consola de operações

1.  Abra a consola do Gestor de operações e selecione a área de trabalho de **Administração** .
2.  Selecione **Pacotes de gestão** e, na **procure:** tipo de caixa de texto **Advisor** ou **informações da empresa**.
3.  Consoante as soluções que ativou, irá ver um pacote de gestão de correspondente listado nos resultados de pesquisa.  Por exemplo, se tiver ativado a solução de gestão de alerta, o pacote de gestão de gestão de classificação de centro de sistema do Microsoft alerta será na lista.
4.  A partir da vista de **monitorização** , navegue para a vista de **Estado de Suite\Health da gestão de operações** .  Selecione um servidor de gestão no painel de **Estado de gestão do servidor** e, no painel de **Vista de detalhes de** confirme que o valor de propriedade **URI de serviço de autenticação** corresponde ao ID da área de trabalho OMS.

    ![OMS-opsmgr-mg-authsvcuri-Property-MS](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)


## <a name="remove-integration-with-oms"></a>Remover a integração com OMS
Quando já não necessitar de integração entre o seu grupo de Gestor de operações de gestão e a área de trabalho OMS, existem vários passos necessários para corretamente remover a ligação e configuração do grupo de gestão de. O procedimento seguinte terá atualizar a área de trabalho OMS ao eliminar a referência do seu grupo de gestão, eliminar as conexões OMS e, em seguida, elimine os pacotes de gestão de suporte OMS.   

1.  Abra a Shell de comandos do Gestor de operações com uma conta que é um membro da função administradores de Gestor de operações.

    >[AZURE.WARNING] Verifique se não tem qualquer pacotes de gestão personalizada com o word Advisor ou IntelligencePack no nome antes de continuar, caso contrário, os passos seguintes irão eliminá-los do grupo de gestão de.

2.  A partir da shell linha de comandos, escreva`Get-SCOMManagementPack -name "*advisor*" | Remove-SCOMManagementPack`

3.  Tipo de seguinte,`Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack`

4.  Abra a consola de operações de Gestor de operações com uma conta que é um membro da função administradores de Gestor de operações.
5.  Em **Administração**, selecione o nó de **Pacotes de gestão** e, na **procure:** , escreva **Advisor** e verifique os seguintes packs gestão continuam a ser importados no seu grupo de gestão:

    - Centro de sistema do Microsoft Advisor
    - Centro de sistema do Microsoft Advisor interno

6. No portal do OMS, clique no mosaico **Definições** .
7.  Selecione **ligado origens**.
8.  Na tabela na secção de Gestor de operações do Centro de sistema, deverá ver o nome do grupo de gestão que pretende remover da área de trabalho.  Em coluna **Última dados**, clique em **Remover**.  

    >[AZURE.NOTE] **Remover** ligação não estará disponível em até após 14 dias, caso não haja nenhuma actividade detectada do grupo de gestão ligada.  
   
9.  Aparece uma janela que lhe pede para confirmar que pretende continuar com a remoção.  Clique em **Sim** para continuar. 

Para eliminar as duas conexões - Microsoft.SystemCenter.Advisor.DataConnector Advisor Connector, guarde o script do PowerShell abaixo para o seu computador e executar com os exemplos seguintes.

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

>[AZURE.NOTE] O computador está a executar este script a partir de, se não estiver num servidor de gestão, deverá ter o comando shell operações Gestor 2012 SP1 ou R2 instalado dependendo da versão do seu grupo de gestão.

```
    `param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

No futuro se planeia a reestabelecer ligação do seu grupo de gestão para uma área de trabalho OMS, é necessário voltar a importar o `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` ficheiro de pacote de gestão de atualizações mais recentes aplicado ao seu grupo de gestão.  Pode localizar este ficheiro na `%ProgramFiles%\Microsoft System Center 2012` ou o `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` pasta.

## <a name="next-steps"></a>Próximos passos

- [Soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md) para adicionar a funcionalidade e reunir dados.
- [Configurar as definições de proxy e a firewall no registo de análise](log-analytics-proxy-firewall.md) se a sua organização utiliza uma firewall ou servidor proxy para que agentes possam comunicar com o serviço de análise de registo.
