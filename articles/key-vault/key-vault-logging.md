<properties
    pageTitle="Registo do cofre chave Azure | Microsoft Azure"
    description="Utilize este tutorial para ajudá-lo a introdução ao Azure Cofre de chave de registo."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/31/2016"
    ms.author="cabailey"/>

# <a name="azure-key-vault-logging"></a>Registo do cofre chave Azure #
Azure chave Cofre está disponível na maioria das regiões. Para mais informações, consulte o artigo [chave cofre preços página](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução  
Depois de ter criado um ou mais cofres chaves, provavelmente pretenderá monitorizar como e quando o seu cofres chaves são acedidos e por quem. Pode fazê-lo ao ativar o registo do Cofre de palavras chave, que guarda as informações numa conta de armazenamento Azure que indicar. Um novo contentor denominado **informações-registos-auditevent** é criado automaticamente para a sua conta de armazenamento especificada e pode utilizar este mesma conta de armazenamento para recolher registos para vários cofres chaves.

Pode aceder as informações de registo no máximo, 10 minutos depois da chave de operação do Cofre de palavras. Na maioria dos casos, vai ser mais rápido superior a esta.  É a gerir os registos na sua conta de armazenamento:

- Utilize os métodos de controlo de acesso Azure padrão para proteger os registos através da restrição quem pode aceder aos mesmos.
- Elimine registos que já não pretende manter na sua conta de armazenamento.

Utilize este tutorial para ajudá-lo a introdução ao Azure Cofre de chave de registo para criar a sua conta de armazenamento, ativar o registo e interpretar as informações de registo recolhidas.  


>[AZURE.NOTE]  Neste tutorial não inclui instruções para saber como criar cofres chaves, chaves ou segredos. Para estas informações, consulte o artigo [Introdução ao Azure chave cofre](key-vault-get-started.md). Em alternativa, para obter instruções de Interface de comandos em diferentes plataformas, consulte o artigo [Este tutorial equivalente](key-vault-manage-with-cli.md).
>
>Atualmente, não é possível configurar Azure chave cofre no portal do Azure. Em alternativa, utilize estas instruções Azure PowerShell.

Os registos que recolher podem ser visualizados utilizando a análise de registo do conjunto de gestão do operações. Para mais informações, consulte o artigo [solução do Azure chave cofre (pré-visualização) no registo de análise](../log-analytics/log-analytics-azure-key-vault.md).

Para obter informações gerais sobre Azure chave cofre, consulte o artigo [o que é o Azure chave cofre?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter o seguinte procedimento:

- Um cofre chave existente que tenha sido a utilizar.  
- Azure PowerShell, **versão mínima do 1.0.1**. Para instalar o PowerShell do Azure e associá-la com a sua subscrição Azure, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Se já tiver instalado o PowerShell do Azure e não souber a versão, a partir da consola do Azure PowerShell, escreva `(Get-Module azure -ListAvailable).Version`.  
- Os registos de armazenamento suficiente no Azure para o Cofre chave.


## <a id="connect"></a>Ligar a suas subscrições ##

Iniciar uma sessão de PowerShell do Azure e inicie sessão na sua conta Azure com o seguinte comando:  

    Login-AzureRmAccount

Na janela do browser de pop-up, introduza o seu nome de utilizador da conta Azure e a palavra-passe. Azure PowerShell receberão todas as subscrições que estão associadas com esta conta e por predefinição, utiliza a primeira parte.

Se tiver múltiplas subscrições, poderá ter que especificar um específico que foi utilizado para criar o seu Cofre de chave do Azure. Escreva o seguinte para ver as subscrições para a sua conta:

    Get-AzureRmSubscription

Em seguida, para especificar a subscrição da qual está associada à sua cofre chave que vai iniciar sessão, escreva:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para mais informações sobre como configurar o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Criar uma nova conta de armazenamento para os registos ##

Embora possa utilizar uma conta existente do armazenamento para os registos, vamos criar uma nova conta de armazenamento que vai estar dedicada aos registos de chave cofre. Para sua comodidade para quando temos especificá-lo mais tarde, podemos irá armazenar os detalhes para uma variável denominada **sa**.

Para adicional facilidade de gestão, vamos também utilizar o mesmo grupo de recursos como aquele que contém os nossos chave cofre. [Introdução ao tutorial](key-vault-get-started.md), este grupo de recursos se chamar **ContosoResourceGroup** e Vamos continuar a utilizar a localização do leste asiático. Substitua estes valores para o seu próprio, conforme aplicável:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]  Se decidir utilizar uma conta existente do armazenamento, tem de utilizar a mesma subscrição como cofre chave e tem de utilizar o modelo de implementação do Gestor de recursos, em vez do modelo de implementação clássica.

## <a id="identify"></a>Identificar o Cofre chave para os registos ##

No nosso tutorial de introdução ao obter, o nosso nome de cofre chave era **ContosoKeyVault**, para que vamos continuar a utilizar esse nome e armazenar os detalhes para uma variável denominada **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Ativar o registo ##

Para ativar o registo do cofre chave, vamos utilizar o cmdlet conjunto AzureRmDiagnosticSetting, juntamente com as variáveis que criámos para nossa nova conta de armazenamento e os nossos chave cofre. Recomendamos também irá configurar o **-ativado** Sinalizar para **$true** e definir a categoria para AuditEvent (categoria apenas para o registo do cofre chave),:


    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

O resultado para que isto inclui:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Isto confirma que o registo é activado agora para o Cofre chave, guardar informações à sua conta de armazenamento.

Opcionalmente, também pode definir política de retenção para os registos assim que os registos mais antigos serão automaticamente eliminados. Por exemplo, defina a política de retenção utilizando **- RetentionEnabled** sinalizador para **$true** e defina **- RetentionInDays** parâmetro a **90** para que os registos mais antigos do que cerca de 90 dias serão automaticamente eliminados.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

O que tem sessão iniciado:

- Todos os pedidos de REST API autenticados sessão iniciados, que inclui os pedidos de falhados estatístico como resultado da permissões de acesso, erros do sistema ou pedidos de incorretas.
- Operações da chave do Cofre de palavras propriamente dito, que inclui criação, eliminação, as políticas de acesso do cofre chave definição, e atualizar os atributos do cofre chave tais como etiquetas.
- Operações em chaves e segredos no cofre chave, que inclui a criar, modificar ou eliminar estas teclas ou segredos; operações, tais como o início de sessão, certifique-se, encriptar, desencriptar, moldar e anular a moldagem de teclas, obtenha segredos, lista teclas e segredos e os respetivos versões.
- Pedidos não autenticados que resultam uma resposta 401. Por exemplo, pedidos de não possui uma ligação OAuth, ou mal ou expirado, com ou que tenham um token inválido.  


## <a id="access"></a>Aceder ao seu registos ##

Registos de chave cofre são armazenados no contentor de **informações de registos-auditevent** na conta de armazenamento que forneceu. Para listar todos os blobs neste contentor, escreva:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

O resultado terá o aspeto algo semelhante ao seguinte:

**Uri de contentor: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Nome**

**----**

**resourceId = / subscrições/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fornecedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId = / subscrições/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fornecedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

* * resourceId = / subscrições/361DA5D4-A47A-4C 79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/fornecedores/MICROSOFT. KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json***


Como pode ver a partir deste saída, os blobs siga uma convenção de nomenclatura: **resourceId =<ARM resource ID>/y =<year>/m =<month>/d =<day of month>/h =<hour>/m =<minute>/filename.json**

Os valores de data e hora utilizam UTC.

Uma vez que a mesma conta de armazenamento pode ser utilizada para recolher registos para vários recursos, o ID do recurso completo no nome do blob é muito útil para aceder a ou transferir apenas as blobs que necessita. Mas antes de podemos fazê-lo, pela primeira vez abordamos a transferência de todos os os blobs.

Em primeiro lugar, crie uma pasta para transferir os blobs. Por exemplo:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Obter uma lista de todos os blobs:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Encaminhar esta lista através de 'Get-AzureStorageBlobContent' para transferir os blobs para o nossa pasta de destino:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Quando executa este comando do segundo, o **/** delimitador nos nomes de BLOBs criar uma estrutura de pastas completo na pasta de destino e esta estrutura será utilizada para transferir e armazenar os blobs como ficheiros.

Para transferir seletivamente blobs, utilize carateres universais. Por exemplo:

- Se tiver várias cofres chaves e pretende transferir registos para apenas uma cofre chave, com o nome CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Se tiver vários grupos de recursos e pretende transferir registos para o grupo de recursos apenas uma, utilize `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Se pretende transferir todos os registos para o mês de Janeiro de 2016, utilize `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Agora está pronto para começar a consultar quais são os registos. Mas antes de passar para que, dois parâmetros mais para obter AzureRmDiagnosticSetting que poderá ter de saber:

- Para consultar o estado das definições de diagnóstico para o seu recurso de cofre chave:`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`

- Para desativar o registo para o seu recurso de cofre chave:`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpretar os registos de chave Cofre ##

Blobs individuais estão armazenados como texto, formatado como um blob JSON. Este é um exemplo de entrada de registo a execução `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


A tabela seguinte lista os nomes de campo e descrições.


| Nome do campo        | Descrição |
| ------------- |-------------|
| tempo      | Data e hora (UTC).|
| resourceId      | ID do Azure Gestor de recursos do recurso. Nos registos de chave cofre, esta é sempre o ID do recurso chave cofre.|
| operationName      | Nome da operação, conforme é documentado na tabela seguinte.|
| operationVersion      | Esta é a versão de REST API pedida pelo cliente.|
| categoria      | Nos registos de chave cofre, AuditEvent é o valor único, disponível.|
| resultType      | Resultado do pedido de REST API.|
| resultSignature      | Estado HTTP.|
| resultDescription     | Descrição adicional sobre o resultado, quando se encontra disponível.|
| durationMs      | Data/hora demorou de pedido de REST API, em milissegundos do serviço. Isto não inclui a latência da rede, para que o tempo de que medir do lado do cliente pode não corresponder a este período de tempo.|
| callerIpAddress      | Endereço IP do cliente quem fez o pedido.|
| correlationId      | Um GUID opcional que o cliente pode passar para correlacionar registos do lado do cliente com os registos do serviço lado (tecla cofre).|
| identidade      | Identidade do token que foi apresentado quando efetuar o pedido de REST API. Este nome costuma um "utilizador", "principal do serviço" ou "utilizador + appId" uma combinação tal como no caso de um pedido de resultantes um cmdlet do Azure PowerShell.|
| Propriedades      | Este campo irá conter informações diferentes, consoante a operação (operationName). Na maioria dos casos, contém informações de cliente (a cadeia useragent transmitida pelo cliente), o URI exato do pedido REST API e o código de estado HTTP. Além disso, quando um objeto é devolvido como resultado de um pedido de (por exemplo, KeyCreate ou VaultGet)-lo também conterá a chave de URI (como "id"), cofre URI ou URI secreta.|




Os valores do campo **operationName** estão no formato de ObjectVerb. Por exemplo:

- Todas as operações de cofre chave têm o ' cofre`<action>`' Formatar, tais como `VaultGet` e `VaultCreate`.

- Todas as operações de chaves tem o ' chave`<action>`' Formatar, tais como `KeySign` e `KeyList`.

- Todas as operações secretas tem o ' secreta`<action>`' Formatar, tais como `SecretGet` e `SecretListVersions`.

A tabela seguinte lista os operationName e o comando REST API correspondente.

| operationName        | Comando de API REST |
| ------------- |-------------|
| Autenticação      | Através do ponto final do Azure Active Directory|
| VaultGet      | [Obter informações sobre um cofre chave](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx)|
| VaultPut      | [Criar ou actualizar um cofre chave](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx)|
| VaultDelete      | [Eliminar um cofre chave](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx)|
| VaultPatch      | [Atualizar uma chave Cofre](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList      | [Listar todos os cofres chaves num grupo de recursos](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx)|
| KeyCreate      | [Criar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx)|
| KeyGet      | [Obter informações sobre uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx)|
| KeyImport      | [Importar uma chave para Cofre](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx)|
| KeyBackup      | [Cópia de segurança uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx).|
| KeyDelete      | [Eliminar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx)|
| KeyRestore      | [Restaurar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx)|
| KeySign      | [Inicie sessão com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx)|
| KeyVerify      | [Certifique-se com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx)|
| KeyWrap      | [Moldar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx)|
| KeyUnwrap      | [Moldagem de uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx)|
| KeyEncrypt      | [Encriptar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx)|
| KeyDecrypt      | [Desencriptar com uma chave](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx)|
| KeyUpdate      | [Actualizar uma chave](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx)|
| KeyList      | [Listar as teclas num cofre](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx)|
| KeyListVersions      | [As versões de uma chave de lista](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx)|
| SecretSet      | [Criar uma palavra-passe](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx)|
| SecretGet      | [Obter secreta](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx)|
| SecretUpdate      | [Atualizar uma palavra-passe](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx)|
| SecretDelete      | [Eliminar uma palavra-passe](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx)|
| SecretList      | [Lista de segredos num cofre](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx)|
| SecretListVersions      | [Versões de lista de uma palavra-passe](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx)|




## <a id="next"></a>Próximos passos ##

Para obter um tutorial que utiliza o Azure chave cofre numa aplicação web, consulte o artigo [Utilizar Azure chave cofre a partir de uma aplicação Web](key-vault-use-from-web-application.md).

Para programação referências, consulte o artigo [guia o programador Azure chave cofre](key-vault-developers-guide.md).

Para uma lista de cmdlets do Azure PowerShell 1.0 para o Azure chave cofre, consulte [Cmdlets do Azure chave cofre](https://msdn.microsoft.com/library/azure/dn868052.aspx).

Para obter um tutorial em rotação da chave e registo de auditoria com Azure chave cofre, veja [como a configuração do cofre chave com auditoria e rotação da chave do fim para fim](key-vault-key-rotation-log-monitoring.md).
