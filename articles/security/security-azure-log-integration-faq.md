<properties
   pageTitle="Integração do registo Azure FAQ | Microsoft Azure"
   description="Estas FAQs respondem a questões sobre a integração do Azure registo."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Integração do registo Azure perguntas mais frequentes (FAQ)

Este artigo responde a questões sobre integração com o registo Azure, um serviço que permite-lhe integrar registos não processados por parte dos seus recursos Azure os sistemas de gestão de evento (SIEM) e informações de segurança no local. Esta integração fornece um dashboard unificado para todos os seus ativos, no local ou na nuvem, para que possa agregar, correlacionar, analisar e alertar para eventos de segurança associados com as suas aplicações.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Como posso ver as contas de armazenamento a partir do qual integração do Azure registo é no registos Azure VM a partir do?

Execute o comando **azlog lista de origem**.

## <a name="how-can-i-update-the-proxy-configuration"></a>Como posso atualiza a configuração de proxy?

Se a sua definição de proxy não permitir acesso ao armazenamento Azure diretamente, abra o **AZLOG. EXE. CONFIG** ficheiro em **c:\Program Files\Microsoft Azure registo integração**. Actualize o ficheiro para incluir a secção **defaultProxy** com o endereço proxy da sua organização. Após atualização estiver concluída, pare e iniciar o serviço utilizando comandos **azlog líquido parar** e **Iniciar líquido azlog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Como posso ver as informações da subscrição no eventos do Windows?

Acrescente a **subscriptionid** para o nome amigável ao adicionar a origem.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

O evento XML tem os metadados, conforme apresentado abaixo, incluindo o id da subscrição.

![Evento XML][1]

## <a name="error-messages"></a>Mensagens de erro

### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Quando executar o comando **azlog createazureid**, porque é que recebo o erro seguinte?

Erro:

  *Ocorreu uma falha ao criar a aplicação de AAD - inquilinos 72f988bf-86f1-41af-91ab-2d7cd011db37-motivo = 'Proibido' - mensagem = 'Privilégios suficientes para concluir a operação'.*

**Azlog createazureid** tenta criar um principal de serviço no todos os inquilinos Azure AD para as subscrições na qual o início de sessão Azure tem acesso ao. Se o início de sessão Azure for um utilizador de convidado nesse inquilino do Azure AD, em seguida, o comando falhar com 'Privilégios suficientes para concluir a operação'. Pedir o administrador de inquilino para adicionar a sua conta como um utilizador no inquilino.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>Quando a executar o comando **azlog autorizar**, por que motivo recebo o erro seguinte?

Erro:

  *Aviso de criação de atribuição de funções - AuthorizationFailed: O cliente janedo@microsoft.com' com objeto id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write' ao longo do âmbito '/ subscrições/70 d 95299-d689-4c 97-b971-0d8ff0000000'.*

Comando **Azlog autorizar** atribui à função de leitor ao serviço do Azure AD principal (criada com **Azlog createazureid**) para as subscrições fornecidas. Se o início de sessão Azure não for um administrador de cocriação ou um proprietário da subscrição, falhar com a autorização falhou mensagem de erro. O controlo de acesso baseado em funções Azure (RBCA) da cocriação administrador ou proprietário é necessário para concluir esta ação.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>Onde posso encontrar a definição das propriedades no registo de auditoria?

Consulte o artigo:

- [Operações de auditoria com o Gestor de recursos](../resource-group-audit.md)
- [Listar os eventos de gestão de uma subscrição no Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Onde pode localizar detalhes sobre alertas do Centro de segurança do Azure?

Consulte o artigo [Gerir e a responder a alertas de segurança no Centro de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Como posso modificar o que é recolhido com diagnóstico VM?

Consulte o artigo [Utilizar o PowerShell para ativar o Azure diagnóstico numa máquina virtual a executar o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) para obter detalhes sobre como obter, modificar e definir o diagnóstico do Azure no Windows *(WAD)* configuração. Segue-se um exemplo:

### <a name="get-the-wad-config"></a>Obter o config WAD

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>Modificar a configuração WAD

O exemplo seguinte é uma configuração onde apenas IDdoevento 4624 e IDdoevento 4625 são recolhidas a partir do registo de eventos de segurança. Microsoft Antimalware eventos são recolhidos a partir do registo de eventos do sistema. Consulte o artigo [consumir eventos] (https://msdn.microsoft.com/library/windows/desktop/dd996910 (v=vs.85) para obter detalhes sobre a utilização de expressões XPath.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>Definir a configuração de WAD

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Depois de efetuar alterações, selecione a conta de armazenamento para se certificar de que os eventos corretos são recolhidos.

Se tiver questões sobre a integração de registo do Azure, envie um e-mail para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
