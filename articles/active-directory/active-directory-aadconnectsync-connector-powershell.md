<properties
   pageTitle="Conector do PowerShell | Microsoft Azure"
   description="Este artigo descreve como configurar Windows PowerShell conector da Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="windows-powershell-connector-technical-reference"></a>Referência de técnica de conector do Windows PowerShell
Este artigo descreve o conector do Windows PowerShell. O artigo aplica-se aos seguintes produtos:

- Gestor de identidades do Microsoft 2016 (MIM2016)
- Gestor de identidades do Forefront 2010 R2 (FIM2010R2)
    -   Tem de utilizar correcção 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como uma transferência a partir do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Descrição geral da conexão PowerShell
O conector de PowerShell permite-lhe integrar o serviço de sincronização com sistemas externos que oferecem que Windows PowerShell baseado APIs. A conexão fornece uma bridge entre as funcionalidades do agente de gestão de conectividade extensible baseada em chamada 2 framework (ECMA2) e Windows PowerShell. Para mais informações sobre a arquitetura ECMA, consulte a [Referência do agente de gestão de 2.2 do Extensible conectividade](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar a conexão, certifique-se de que tem o seguinte procedimento no servidor de sincronização:

- 4.5.2 do Microsoft .NET Framework ou posterior
- O Windows PowerShell 2.0, 3.0 ou 4.0

A política de execução do servidor de serviço de sincronização tem de ser configurada para permitir que a conexão executar scripts do Windows PowerShell. A menos que os scripts o será executado conexão é assinado digitalmente, configurar a política de execução ao executar este comando:  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Criar uma nova conexão
Para criar um conector do Windows PowerShell no serviço de sincronização, tem de fornecer uma série de scripts do Windows PowerShell que execute os passos pedidos pelo serviço de sincronização. Consoante a origem de dados se liga ao e a funcionalidade que requerem, os scripts tem de implementar varia. Esta secção descreve cada um dos scripts que pode ser implementada e quando são necessários.

O conector do Windows PowerShell foi concebido para armazenar cada um dos scripts dentro da base de dados do serviço de sincronização. Enquanto é possível executar scripts que estão armazenados no sistema de ficheiros, é mais fácil inserir o corpo de cada script diretamente na configuração do conector.

Para criar um conector de PowerShell, no **Serviço de sincronização** , selecione **Agente de gestão** e de **Criar**. Selecione a conexão **PowerShell (Microsoft)** .

![Criar conector](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Conectividade
Forneça os parâmetros de configuração para ligar a um sistema remoto. Estes valores em segurança armazenados ao serviço de sincronização e são disponibilizados para scripts do Windows PowerShell quando o conector é executado.

![Conectividade](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Pode configurar os parâmetros de conectividade seguintes:

**Conectividade**

Parâmetro | Valor predefinido | Objetivo
--- | --- | ---
Servidor | <Blank> | Nome do servidor que a conexão deve ligar a.
Domínio | <Blank> | Domínio de credenciais para armazenar para utilizar quando o conector é executado.
Utilizador | <Blank> | Nome de utilizador da credencial para armazenar para utilizar quando o conector é executado.
Palavra-passe | <Blank> | Palavra-passe a credencial para armazenar para utilizar quando o conector é executado.
Representar conexão conta | FALSO | Quando for VERDADEIRO, o serviço de sincronização é executado os scripts do Windows PowerShell no contexto das credenciais fornecido. Sempre que possível, recomenda-se que o parâmetro **$Credentials** lhe é transmitido a cada script é utilizado em vez de representação. Para mais informações sobre permissões adicionais que são necessários para utilizar esta opção, consulte [Configuração adicionais para representação](#additional-configuration-for-impersonation).
Carregar o perfil de utilizador quando a representar | FALSO | Indica ao Windows para carregar o perfil de utilizador das credenciais o conector de durante a representação. Se o utilizador representado tem um perfil guardado no servidor, a conexão não carregar o perfil guardado no servidor. Para mais informações sobre permissões adicionais que são necessários para utilizar este parâmetro, consulte [Configuração adicionais para representação](#additional-configuration-for-impersonation).
Tipo de início de sessão quando a representar | Nenhum | Tipo de início de sessão durante a representação. Para obter mais informações, consulte o [dwLogonType] [ dw] documentação.
Apenas os Scripts com a sessão iniciada | FALSO | Se for VERDADEIRO, o conector do Windows PowerShell valida que cada script tem uma assinatura digital válida. Se for FALSO, certifique-se de que a política de execução do Windows PowerShell o servidor de serviço de sincronização é RemoteSigned ou sem restrições.

**Módulo comum**  
A conexão permite-lhe armazenar um módulo Windows PowerShell partilhado na configuração. Quando o conector é executado um script, o módulo Windows PowerShell é extraído para o sistema de ficheiros, para que pode ser importado por cada script.

Scripts importar, exportar e sincronização de palavra-passe, o módulo comuns é extraído para a pasta de MAData a conexão. Scripts esquema, validação, hierarquia e partição deteção, o módulo comuns é extraído para a pasta % TEMP %. Em ambos os casos, o script módulo comum extraído chama-se de acordo com a definição de nome de Script do módulo comum.

Para carregar um módulo chamado FIMPowerShellConnectorModule.psm1 a partir da pasta MAData, utilize a seguinte instrução:`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Para carregar um módulo chamado FIMPowerShellConnectorModule.psm1 a partir de pasta % TEMP %, utilize a seguinte instrução:`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validação de parâmetros**  
O Script de validação é um script do Windows PowerShell opcional que pode ser utilizado para garantir que os parâmetros de configuração do conector fornecidos pelo administrador de são válidos. Servidor de validar, credenciais de ligação e parâmetros de conectividade são utilizações comuns de script de validação. Script de validação chama-se após seguintes separadores e caixas de diálogo são modificadas:

- Conectividade
- Parâmetros globais
- Configuração da partição

Script de validação recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | O separador Configuração ou a caixa de diálogo que o acionou o pedido de validação.
ConfigParameters | [KeyedCollection] [ keyk] [cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.

Script de validação deve devolver um único objeto ParameterValidationResult à tubagem.

**Deteção de esquema**  
O script de deteção de esquema é obrigatório. Este script devolve os tipos de objeto, atributos e restrições de atributo que utiliza o serviço de sincronização ao configurar regras de fluxo de atributo. O script de deteção de esquema é executado durante a criação de conexão e preenche o esquema da conexão. Também é utilizado pela ação de atualização de esquema no Gestor de serviço de sincronização.

O script de deteção de esquema recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection] [ keyk] [cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.

O script tem de devolver um único [esquema] [ schema] objeto para as em curso. O objeto de esquema é composto por [SchemaType] [ schemaT] objectos que representam os tipos de objeto (por exemplo: os utilizadores e grupos). O objeto SchemaType retém uma coleção de [SchemaAttribute] [ schemaA] objectos que representam os atributos (por exemplo: nome próprio, apelido e endereço postal) do tipo de.

**Parâmetros adicionais**  
Para além das definições de configuração padrão, pode definir definições de configuração personalizado adicionais que são específicas para a instância da conexão. Estes parâmetros podem ser especificados à connector, partição, ou executar passo níveis e acedido a partir do script do Windows PowerShell relevante. Definições de configuração personalizado podem ser armazenadas numa base de dados de serviço de sincronização no formato de texto simples ou possam ser encriptados. Encripta automaticamente o serviço de sincronização e desencripta seguro definições de configuração de quando for necessário.

Para especificar as definições de configuração personalizado, separe o nome de cada parâmetro com uma vírgula (,).

Para aceder às definições de configuração personalizado a partir de um script, tem de sufixo o nome com um sublinhado ( \_ ) e o âmbito do parâmetro (Global, partição ou RunStep). Por exemplo, para aceder ao parâmetro nome de ficheiro Global, utilize este fragmento de código:`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Capacidades de
No separador de funcionalidades do estruturador de fluxos de agente de gestão de define o comportamento e a funcionalidade de linhas com marcadores. Não não possível modificar as seleções efetuadas neste separador quando for criada a conexão. Esta tabela lista as definições de funcionalidade.

![Capacidades de](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

Capacidade de | Descrição |
--- | --- |
[Estilo de nome distinto][dnstyle] | Indica se a conexão suportar nomes distintos e se Sim, o que de estilo.
[Exportar o tipo][exportT] | Determina o tipo de objetos que são apresentados para o script de exportação. <li>AttributeReplace – inclui o conjunto completo de valores para um atributo múltiplos quando altera o atributo.</li><li>AttributeUpdate – inclui apenas as deltas para um atributo múltiplos quando altera o atributo.</li><li>MultivaluedReferenceAttributeUpdate - inclui um conjunto completo de valores para os atributos de múltiplos valores que não sejam referência e deltas apenas para os atributos de referência de múltiplos valores.</li><li>ObjectReplace – inclui todos os atributos de um objeto quando qualquer alterações de atributo</li>
[Normalização de bases de dados][DataNorm] | Indica o serviço de sincronização a normalizar atributos de âncora antes de são fornecidas para scripts.
[Confirmação de objeto][oconf] | Configura o comportamento de importação pendentes no serviço de sincronização. <li>Normal – comportamento predefinido que espera exportadas todas as alterações aos confirmado através da importação</li><li>NoDeleteConfirmation – quando é eliminado um objeto, não existe sem importar pendente gerado.</li><li>NoAddAndDeleteConfirmation – quando um objeto é criado ou eliminado, não existe sem importar pendente gerado.</li>
Utilizar DN como âncora | Se o estilo de nome distinto estiver definido para LDAP, o atributo âncora para o espaço de conexão também é o nome exclusivo.
Operações em simultâneo de várias conectores | Quando selecionada, vários conectores do Windows PowerShell podem executar em simultâneo.
Partições | Quando selecionada, o conector suporta vários partições e deteção de partição.
Hierarquia | Quando selecionada, o conector suporta uma estrutura hierárquica de estilo LDAP.
Ativar a importação | Quando selecionada, o conector importa dados através de scripts de importação.
Ativar Delta importar | Quando selecionada, o conector pode pedir deltas a partir dos scripts de importação.
Activar exportação | Quando selecionada, o conector exporta dados através de scripts de exportação.
Activar exportação completa | Quando selecionada, os scripts de exportação suportam o espaço de conexão toda a exportar. Para utilizar esta opção, a ativar exportar tem de ser dada.
Sem valores de referência numa fase de exportação do primeiro | Quando selecionada, são exportados atributos de referência numa segunda fase de exportação.
Ativar mudar o nome do objeto | Quando selecionada, podem ser modificados nomes distintos.
Eliminar adicionar como substituir | Quando selecionada, eliminar-adicione operações são exportadas como uma única substituição.
Ativar operações de palavra-passe | Quando selecionada, scripts de sincronização de palavra-passe são suportadas.
Ativar a palavra-passe de exportação numa fase de primeiro | Quando selecionada, definido durante o aprovisionamento de palavras-passe são exportadas quando é criado o objeto.

### <a name="global-parameters"></a>Parâmetros globais
No separador parâmetros globais no estruturador de agente de gestão de permite-lhe configurar os scripts do Windows PowerShell que são executados pelo conector. Também pode configurar os valores globais para as definições de configuração personalizado foi definidas no separador de conectividade.

**Deteção de partição**  
Uma partição é um espaço de nomes em separado dentro de um esquema partilhado. Por exemplo, no Active Directory em todos os domínios são uma partição dentro de uma floresta. Uma partição é o agrupamento lógico para importação e exportação operações. Importação e exportação tem partição como um contexto e todas as operações acontece neste contexto. Partições são suposto existirem para representar uma hierarquia no LDAP. O nome de uma partição de distinto é utilizado na importação para verificar se todos os objetos devolvidos são no âmbito de uma partição de. O nome distinto partição também é utilizado durante o aprovisionamento do metaverse para o espaço de conexão para determinar a partição de que um objeto deve ser associado durante a exportação.

O script de deteção de partição recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters  | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.

O script tem de devolver um quer uma única [partição] [ part] objeto ou uma lista [T] de objetos de partição à tubagem.

**Deteção de hierarquia**  
O script de deteção de hierarquia só é utilizado quando a capacidade de estilo de nome distinto é LDAP. O script é utilizado para permitem-lhe procurar e selecione um conjunto de membros em contentores que é considerado ou reduzir o âmbito para importar e exportar operações. O script apenas deve fornecer uma lista de nós subordinados directo de nó de raiz fornecido para o script.

O script de deteção de hierarquia recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.
ParentNode | [HierarchyNode][hn] | O nó de raiz da hierarquia em qual o script deve devolver directos subordinados.

O script tem de devolver um quer um objeto de HierarchyNode subordinado única ou uma lista [T] de objetos de HierarchyNode subordinado à tubagem.

#### <a name="import"></a>Importar
Tem de conectores que suportam operações de importação implementar três scripts.

**Iniciar a importação**  
O script de importação de início é executado no início de um passo de importar executar. Durante este passo, pode estabelecer uma ligação para o sistema de origem e siga os passos de preparação antes de importar dados a partir do sistema ligado.

O script de importação de início recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de importar executar (delta ou completo), partição, hierarquia, marca d'água e tamanho da página esperado.
Tipos de | [Esquema][schema] | Esquema para o espaço de conexão que é importado.

O script tem de devolver um único [OpenImportConnectionResults] [ oicres] objeto à tubagem, por exemplo:`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importar dados**  
O script de dados de importação chama pelo conector até que o script indica que não haja mais dados para importar. O conector do Windows PowerShell tem um tamanho de página de 9.999 objetos. Se o script devolver mais do que 9.999 objetos para importar, tem de suportar paginação. Expõe o conector chama-se uma propriedade de dados personalizados que pode utilizar para um arquivo de uma marca d'água para que cada vez que o script de dados de importação, o script currículos importar objetos onde parou.

O script de dados de importação recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.
GetImportEntriesRunStep | [ImportRunStep][irs] | Contém a marca d'água (CustomData) que pode ser utilizada durante paginada importações e delta importa.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de importar executar (delta ou completo), partição, hierarquia, marca d'água e tamanho da página esperado.
Tipos de | [Esquema][schema] | Esquema para o espaço de conexão que é importado.

O script de dados de importar uma lista tem de ser escrita [[CSEntryChange][csec]] objeto para as em curso. Nesta coleção de é composta por atributos CSEntryChange que representam cada objeto a ser importado. Durante a execução de importação completa, nesta coleção de deve ter um conjunto completo de objetos de CSEntryChange que tenham todos os atributos para cada objeto. Durante uma variação de importar, o objeto CSEntryChange quer deve conter os deltas nível atributo para cada objeto para importar ou uma representação completa os objetos que foram alteradas (modo substituir).

**Importar de fim**  
No final da importação executar, o script fim importar é executado. Este script deve efetuar as tarefas de limpeza necessárias (por exemplo, fechar ligações para sistemas) e responder a falhas.

O script de importação de fim recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de importar executar (delta ou completo), partição, hierarquia, marca d'água e tamanho da página esperado.
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Informa o script sobre o motivo que foi terminada a importação.

O script tem de devolver um único [CloseImportConnectionResults] [ cicres] objeto à tubagem, por exemplo:`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exportar
Idêntico à arquitetura de importação de linhas com marcadores, conexões que suportam exportar tem de implementar três scripts.

**Começar a exportação**  
O script de exportação Iniciar é executado no início de um passo de exportação executar. Durante este passo, pode estabelecer uma ligação para o sistema de origem e realizar qualquer passos de preparação antes de exportar dados para o sistema ligado.

O script de exportação inicial recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de exportação executar (delta ou completo), partição, hierarquia e tamanho da página esperado.
Tipos de | [Esquema][schema] | Esquema para o espaço de conexão que é exportado.

O script não deve devolver qualquer saída à tubagem.

**Exportar dados**  
O serviço de sincronização chamadas o script de exportação de dados as vezes que é necessário para processar todas as exportações pendentes. Se o espaço de conexão tiver mais exportações pendentes que tamanho da página a conexão, o script de dados de exportação poderá denominado várias vezes e possivelmente várias vezes para o mesmo objeto.

O script de dados de exportação recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.
CSEntries | IList[CSEntryChange][csec] | Lista de todos os espaços de conexão objectos com pendentes exportações para serem processados durante esta fase.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de exportação executar (delta ou completo), partição, hierarquia e tamanho da página esperado.
Tipos de | [Esquema][schema] | Esquema para o espaço de conexão que é exportado.

O script de dados de exportação tem de devolver um [PutExportEntriesResults] [ peeres] objeto para as em curso. Este objeto não é necessário incluir informações de resultado para cada conector exportado, a menos que ocorre um erro ou uma alteração ao atributo âncora. Por exemplo, para devolver um objeto de PutExportEntriesResults à tubagem:`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Exportar de fim**  
No final da exportação executar, o script de exportação do fim para executar. Este script deve efetuar as tarefas de limpeza necessárias (por exemplo, fechar ligações para sistemas) e responder a falhas.

O script de exportação de fim recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de exportação executar (delta ou completo), partição, hierarquia e tamanho da página esperado.
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Informa o script sobre o motivo que foi terminada a exportação.

O script não deve devolver qualquer saída à tubagem.

#### <a name="password-synchronization"></a>Sincronização de palavra-passe
Conectores do Windows PowerShell podem ser utilizados como um destino para as alterações/repõe de palavra-passe.

O script de palavra-passe recebe os seguintes parâmetros do conector de:

Nome | Tipo de dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credenciais | [PSCredential][pscred] | Contém quaisquer credenciais introduzidas pelo administrador no separador de conectividade.
Partição | [Partição][part] | Partição do directório de que está a CSEntry no.
CSEntry | [CSEntry][cse] | Entrada do espaço de conexão para o objeto que é recebida uma alteração de palavra-passe ou repor.
OperationType | Cadeia | Indica se a operação é uma reposição (**SetPassword**) ou uma alteração (**ChangePassword**).
PasswordOptions | [PasswordOptions][pwdopt] | Sinalizadores que especificam a palavra-passe se destina repor comportamento. Este parâmetro só está disponível se OperationType for **SetPassword**.
Antiga | Cadeia | Povoada com palavra-passe antiga o objeto para alterações de palavra-passe. Este parâmetro só está disponível se OperationType for **ChangePassword**.
NewPassword | Cadeia | Povoada com nova palavra-passe o objeto que deve configurar o script.

O script de palavra-passe não é esperado para devolver resultados à tubagem do Windows PowerShell. Caso ocorra um erro no script de palavra-passe, o script deve accionar um dos seguintes exceções para informar o serviço de sincronização sobre o problema:

- [PasswordPolicyViolationException] [ pwdex1] – devolvido se a palavra-passe não cumpre a política de palavra-passe no sistema ligado.
- [PasswordIllFormedException] [ pwdex2] – devolvido se a palavra-passe não for aceitável para o sistema ligado.
- [PasswordExtension] [ pwdex3] – iniciadas para todos os outros erros no script de palavra-passe.

## <a name="sample-connectors"></a>Conectores de exemplo
Para obter uma descrição completa dos conectores exemplo disponíveis, consulte o artigo [Colecção de conector do Windows PowerShell conexão exemplo][samp].

## <a name="other-notes"></a>Outras notas

### <a name="additional-configuration-for-impersonation"></a>Configuração adicionais para representação
Conceda ao utilizador que está representada no servidor de serviço de sincronização, as seguintes permissões:

Acesso de leitura seguintes chaves de registo:

- HKEY_USERS\\\Software\Microsoft\PowerShell [SynchronizationServiceServiceAccountSID]
- HKEY_USERS\\\Environment [SynchronizationServiceServiceAccountSID]

Para determinar o identificador de segurança (SID) da conta de serviço do serviço de sincronização, execute os seguintes comandos do PowerShell:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Acesso de leitura para as seguintes pastas de sistema do ficheiro:

- %ProgramFiles%\Microsoft forefront identidade Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft forefront identidade Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront identidade Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Substitua o nome do conector do Windows PowerShell para o marcador de posição de {ConnectorName}.

## <a name="troubleshooting"></a>Resolução de problemas

-   Para obter informações sobre como ativar o registo resolver a conexão, consulte o artigo [como ativar ETW Tracing para os conectores](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
