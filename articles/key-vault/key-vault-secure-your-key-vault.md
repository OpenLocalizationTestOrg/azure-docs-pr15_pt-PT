<properties
    pageTitle="Seguro cofre chave | Microsoft Azure"
    description="Gerir permissões de acesso para cofre chave para gerir cofres e chaves e segredos. Modelo de autenticação e autorização para cofre chave e como seguro cofre chave"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/07/2016"
    ms.author="ambapat"/>


# <a name="secure-your-key-vault"></a>Seguro cofre chave

Azure chave Cofre é um serviço na nuvem que protege chaves de encriptação e segredos (tal como certificados, as cadeias de ligação, palavras-passe) para as suas aplicações na nuvem. Uma vez que estes dados são sensíveis e empresas crítica, que pretende proteger o acesso a sua chaves cofres para que apenas autorizados aplicações e os utilizadores podem aceder chave cofre. Este artigo fornece uma descrição geral do modelo de cofre chave do access, explica a autenticação e autorização e descreve como acesso seguro a cofre chave para as suas aplicações de nuvem com um exemplo.

## <a name="overview"></a>Descrição geral

Acesso a uma chave Cofre é controlado através de dois interfaces separadas: plano de gestão e plano de dados. Para ambos os planos autenticação inicial e autorização é necessária antes de um autor de chamada (um utilizador ou uma aplicação) pode obter acesso a chave cofre. Autenticação estabelece a identidade do autor da chamada, enquanto a autorização determina quais o autor da chamada é permitida para executar as operações.

Para a autenticação tanto o plano de gestão e o plano de dados, utilize o Azure Active Directory. Para autorização, plano de gestão utiliza o controlo de acesso baseado em funções (RBCA), enquanto que plano de dados utiliza a política de acesso de chave cofre.

Eis uma breve descrição geral dos tópicos abrangidos:

[Autenticação utilizando o Azure Active Directory](#authentication-using-azure-active-direcrory) - esta secção explica como um autor de chamada autentica com o Azure Active Directory para aceder à chave cofre através do plano de gestão e plano de dados. 

[Plano de gestão e plano de dados](#management-plane-and-data-plane) - plano de gestão e plano de dados estão dois planos do access utilizados para aceder a sua chave cofre. Cada plano access suporta operações específicas. Esta secção descreve os pontos finais de acesso, operações suportadas e aceder ao método de controlo utilizado por cada plano. 

[Controlo de acesso de plano de gestão](#management-plane-access-control) - nesta secção que Abordaremos permissão do acesso ao operações de plano de gestão de utilizar o controlo de acesso baseado em funções.

[Controlo de acesso de plano de dados](#data-plane-access-control) - esta secção descreve como utilizar a política de acesso do cofre chave para controlar o acesso de plano de dados.

[Exemplo](#example) - este exemplo descreve como configurar o controlo de acesso para a sua chave cofre permitir que três equipas diferentes (a equipa de segurança, os programadores/operadores e auditores) para executar tarefas específicas para desenvolver, gerir e monitorizar uma aplicação no Azure.


## <a name="authentication-using-azure-active-directory"></a>Autenticação utilizando o Azure Active Directory

Quando cria uma chave cofre numa subscrição do Azure, é automaticamente associado com inquilino do Azure Active Directory da subscrição. Todos os autores das chamadas (utilizadores e aplicações) tem de estar registados deste inquilino para aceder a esta chave cofre. Uma aplicação ou por um utilizador tem de autenticar com o Azure Active Directory para aceder à chave cofre. Aplica-se ambas as gestão plano e plano acesso a dados. Em ambos os casos, uma aplicação pode aceder à chave Cofre de duas maneiras:

-  **utilizador + app aceda** - normalmente, isto é para aplicações que acedem cofre chave em nome de um utilizador com sessão iniciada. Azure PowerShell, Azure Portal são exemplos deste tipo de acesso. Existem duas formas para conceder acesso aos utilizadores: é uma forma para conceder acesso aos utilizadores para que pode aceder cofre chave a partir de qualquer aplicação e a outra forma conceder acesso a um utilizador para cofre chave apenas quando utilizam uma aplicação específica (designado por identidade composta). 
-  **acesso só de aplicação** - para as aplicações que execute serviços daemon, fundo tarefas, etc. A identidade da aplicação é concedida acesso ao Cofre de palavras chave.

Em ambos os tipos de aplicações, a aplicação autentica com o Azure Active Directory utilizar qualquer um dos [métodos de autenticação suportados](../active-directory/active-directory-authentication-scenarios.md) e adquire um token. Método de autenticação utilizado depende do tipo de aplicação. Em seguida, a aplicação utiliza esta token e envia o pedido de REST API cofre chave. Em caso de acesso de plano de gestão de pedidos são encaminhados através de ponto final do Gestor de recursos do Azure. Ao aceder ao plano de dados, o falam aplicações diretamente uma chave de Cofre de palavras ponto final. Ver mais detalhes sobre o [fluxo de autenticação de todo](../active-directory/active-directory-protocols-oauth-code.md). 

O nome do recurso para o qual a aplicação pede um token é diferente, dependendo se a aplicação está a aceder ao plano de gestão ou plano de dados. Por conseguinte, o nome do recurso é um dos gestão plano ou dados plano ponto final descrito na tabela numa secção posterior, consoante o ambiente do Azure.

Ter um único mecanismo para autenticação, gestão e plano de dados tem as suas próprias vantagens:

- As organizações centralmente podem controlar o acesso a todos os cofres chaves na sua organização
- Se um utilizador sai, eles instantaneamente perdem acesso a todos os cofres chaves na organização
- As organizações podem personalizar a autenticação através de opções no Azure Active Directory (por exemplo, permita autenticação multifator para segurança adicional)

## <a name="management-plane-and-data-plane"></a>Plano de gestão e plano de dados

Azure chave Cofre é um serviço Azure disponível através do modelo de implementação do Azure o Gestor de recursos. Quando criar uma chave cofre, receberá um contentor virtual dentro da qual pode criar outros objetos como chaves, segredos e certificados. Em seguida, pode aceder cofre chave utilizando o plano de gestão e plano de dados para efectuar operações específicas. Interface do plano de gestão é utilizado para gerir a sua chave cofre propriamente dito, tais como criar, eliminar, atributos de chave cofre a atualizar e configurar políticas de acesso para o plano de dados. Interface do plano de dados é utilizada para adicionar, eliminar, modificar e utilize as teclas, segredos e certificados armazenados no cofre chave.

Interfaces de plano de gestão plano e os dados são acedidas através de pontos finais diferentes (ver tabela). A segunda coluna na tabela descreve os nomes de DNS para estes pontos finais em diferentes ambientes Azure. A terceira coluna descreve as operações que pode efetuar a partir de cada plano de acesso. Cada plano access também tem o seu próprio mecanismo de controlo de acesso: para o controlo de acesso de plano de gestão estiver definido com o controlo de acesso de Azure Resource Manager Role-Based (RBAC), enquanto para controlo de acesso de plano de dados definido utilizando a política de acesso de chave cofre.

| Plano de acesso | Pontos finais de acesso | Operações | Mecanismo de controlo de acesso|
|--------------|------------------|--------------------|--------|
| Plano de gestão|**Global:**<br> Management.Azure.com:443<br><br> **Azure China:**<br> Management.chinacloudapi.CN:443<br><br> **Azure Governo dos Estados Unidos:**<br> Management.usgovcloudapi.NET:443<br><br> **Azure Alemanha:**<br> Management.microsoftazure.de:443 | Criar/leitura/Actualizar/Eliminar chave Cofre <br> Configurar políticas de acesso para cofre chave<br>Conjunto de etiquetas para cofre chave | Controlo de acesso baseado em funções de Gestor de recursos Azure RBCA) |
| Plano de dados | **Global:**<br> &lt;nome do cofre&gt;. vault.azure.net:443<br><br> **Azure China:**<br> &lt;nome do cofre&gt;. vault.azure.cn:443<br><br> **Azure Governo dos Estados Unidos:**<br> &lt;nome cofre&gt;. vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;nome do cofre&gt;. vault.microsoftazure.de:443 | Para chaves: Desencriptar, encriptar, UnwrapKey, WrapKey, verifique se, inicie sessão, obter, lista, atualizar, criar, importar, eliminar, cópia de segurança, restaurar<br><br> Para segredos: obter, lista, conjunto, eliminar | Política de acesso do cofre chave|

A gestão plano e dados plano aceder a controlos funcionam independentemente. Por exemplo, se pretende conceder acesso a uma aplicação para utilizar teclas num cofre chave, só precisa de conceder permissões de acesso de plano de dados utilizando as políticas de acesso do cofre chave e sem acesso de plano de gestão é necessária para esta aplicação. Por outro lado, se pretender que um utilizador possam ler propriedades cofre e etiquetas, mas não tenha acesso à chaves, segredos ou certificados, pode conceder este utilizador 'acesso de leitura' utilizando RBAC e sem acesso ao plano de dados é necessário.

## <a name="management-plane-access-control"></a>Controlo de acesso de plano de gestão

O plano de gestão é composta por operações que afetam o Cofre chave própria. Por exemplo, pode criar ou eliminar uma chave cofre. Pode obter uma lista de cofres numa subscrição. Pode recuperar as propriedades do cofre chave (como SKU, etiquetas) e definir cofre chave políticas de acesso que os utilizadores e as aplicações que podem aceder teclas e segredos no cofre chave de controlo. Controlo de acesso de plano de gestão utiliza RBAC. Consulte a lista completa de operações de cofre chave que podem ser executadas através do plano de gestão na tabela anterior secção. 

### <a name="role-based-access-control-rbac"></a>Controlo de acesso baseado em funções RBCA)
Cada subscrição Azure tem um Azure Active Directory. Utilizadores, grupos e aplicações a partir deste directório a podem ser concedidas acesso a gerir os recursos na subscrição do Azure que utilizam o modelo de implementação do Azure o Gestor de recursos. Este tipo de controlo de acesso é referido como controlo de acesso baseado em funções RBCA (). Para gerir o acesso a este, pode utilizar o [Azure portal](https://portal.azure.com/), as [Ferramentas do Azure clip](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou do [Azure Gestor de recursos REST APIs](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Com o modelo de Gestor de recursos do Azure, criar Cofre de palavras chave num recurso grupo e controlar o acesso ao plano de gestão deste cofre chave utilizando o Azure Active Directory. Por exemplo, pode conceder aos utilizadores ou uma capacidade de grupo para gerir cofres chaves num grupo de recursos específico.

Pode conceder acesso a utilizadores, grupos e aplicações de um âmbito específico ao atribuir funções RBAC adequadas. Por exemplo, para conceder acesso a um utilizador para gerir cofres chaves atribuir uma função predefinida 'chave cofre Contribuinte' a este utilizador num âmbito específico. O âmbito neste caso seria uma subscrição, um grupo de recursos ou apenas uma chave cofre específico. Uma função atribuída ao nível de subscrição aplica-se a todos os grupos de recursos e recursos dentro desse subscrição. Uma função atribuída ao nível de grupo de recursos aplica-se a todos os recursos nesse grupo de recursos. Uma função atribuída para um recurso específico só se aplica a esse recurso. Existem várias funções predefinidas (consulte o artigo [RBAC: funções incorporadas](../active-directory/role-based-access-built-in-roles.md)), e se as funções predefinidas não corresponder às suas necessidades também poderá definir o seus próprio funções.

>[AZURE.IMPORTANT] Repare que se um utilizador tem contribuinte permissões (RBCA) para um plano de gestão do cofre chave, she podem conceder si aceder ao plano de dados, por definir cofre chave política de acesso, que controla o acesso ao plano de dados. Por conseguinte, recomenda-se para intimamente controlar quem tem acesso de 'Contribuinte' ao seu cofres chaves para garantir que apenas pessoas autorizadas podem aceder e gerir o seu cofres chaves, chaves, segredos e certificados.

## <a name="data-plane-access-control"></a>Controlo de acesso de plano de dados

O plano de dados do cofre chave consiste em operações que afetam os objetos num cofre chave, tais como chaves, segredos e certificados.  Isto inclui chave operações tal como criar, importar, atualizar, lista, cópia de segurança e restaurar as teclas, operações de criptografia, tais como o início de sessão, verifique se, encriptar, desencriptar, moldar e moldagem de e definir etiquetas e outros atributos de teclas. Da mesma forma, para segredos inclui, obter, definir, lista, eliminar.

Acesso de plano de dados é concedido através da definição de políticas de acesso para uma chave cofre. Um utilizador, grupo ou uma aplicação, tem de ter permissões de contribuinte (RBCA) para o plano de gestão do cofre chave conseguir configurar políticas de acesso para essa tecla cofre. Um utilizador, grupo ou aplicação pode lhe ser concedida acesso para executar operações específicas para segredos ou teclas num cofre chave. chave cofre suporte até 16 entradas de política de acesso para uma chave cofre. Criar um grupo de segurança do Azure Active Directory e adicionar utilizadores a esse grupo para conceder acesso a dados plano a vários utilizadores para uma chave cofre.

### <a name="key-vault-access-policies"></a>chave cofre políticas de acesso

políticas de acesso do cofre chave conceder permissões a chaves, segredos e certificados em separado. Por exemplo, pode dar acesso de utilizador a apenas chaves, mas não existem permissões para segredos. No entanto, as permissões de acesso ao teclas ou segredos ou certificados são ao nível do cofre. Por outras palavras, política de acesso do cofre chave não suporta permissões ao nível do objeto. Pode utilizar o [Azure portal](https://portal.azure.com/), as [Ferramentas do Azure clip](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou a [chave cofre gestão REST APIs](https://msdn.microsoft.com/library/azure/mt620024.aspx) para definir políticas de acesso para uma chave cofre.

>[AZURE.IMPORTANT] Tenha em atenção que as políticas de acesso do cofre chave aplicam-se ao nível do cofre. Por exemplo, quando um utilizador é concedido permissão para criar e eliminar chaves, posteriormente, pode executar essas operações em todas as teclas nesse cofre chave.

## <a name="example"></a>Exemplo

Digamos que está a desenvolver uma aplicação que utiliza um certificado para SSL, armazenamento Azure para armazenar dados e utiliza uma tecla RSA 2048-bit para operações de início de sessão. Digamos que esta aplicação está em execução numa VM (ou um conjunto de escala de VM). Pode utilizar uma chave cofre para armazenar todos os segredos de aplicação e utilize cofre chave para armazenar o certificado de arranque do sistema que é utilizado pela aplicação para autenticar com o Azure Active Directory.

Por isso, eis um resumo de todas as teclas e segredos a ser armazenado num cofre chave.

- **Certificado SSL** - utilizada para SSL
- **Chave de armazenamento** - utilizada para obter acesso à conta de armazenamento
- **Chave RSA 2048-bit** - utilizado para operações de início de sessão
- **Certificado de arranque do sistema** - utilizada para autenticar Azure Active Directory, para aceder a chave cofre para obter a chave de armazenamento e utilize a tecla RSA para assinar.

Agora encontro as pessoas que são gerir, implementar e auditorias esta aplicação. Vamos utilizar três funções neste exemplo.

- **A equipa de segurança** - estes são normalmente, a equipa de TI partir do office' o ASC (director de segurança) ou equivalente, responsável para a salvaguarda inicial de segredos como chaves RSA utilizados para assinar, as cadeias de ligação para bases de dados, as teclas de conta de armazenamento, certificados SSL.
- **Os programadores/operadores** - estas são as pessoas que desenvolver esta aplicação e, em seguida, implementação-lo no Azure. Normalmente, estes não fazem parte da equipa segurança e, consequentemente, não devem ter acesso a quaisquer dados importantes, como os certificados SSL, chaves RSA, mas a aplicação que implementarem deve ter acesso às.
- **Auditores** - este nome costuma um conjunto diferente de pessoas, isolados a partir de programadores e a equipa de TI geral. Para rever utilização adequada e manutenção de certificados, chaves, etc. e certifique-se de conformidade com as normas de segurança de dados a sua responsabilidade se. 

Existe uma função mais do que esteja fora do âmbito desta aplicação, mas aqui relevante para ser mencionado e que seria a subscrição (ou grupo de recursos) administrador. Administrador de subscrição configura permissões de acesso inicial para a equipa de segurança. Aqui vamos partem do princípio de que o administrador de subscrição concedeu acesso para a equipa de segurança para um grupo de recursos nas quais todos os recursos necessários para residam esta aplicação.

Agora vamos ver o quê cada função executa no contexto desta aplicação.

- **Equipa de segurança**
    - Criar cofres chaves
    - Desativa na chave de registo do Cofre de palavras
    - Adicionar teclas/segredos
    - Criar cópia de segurança de teclas para recuperação de falhas
    - Definir política de acesso do cofre chave para conceder permissões aos utilizadores e aplicações para efectuar operações específicas
    - Periodicamente filmar teclas/segredos
- **Os programadores/operadores**
    - Obter referências de arranque do sistema e certificados SSL (miniaturas), a chave de armazenamento (secreta URI) e iniciar sessão chave (chave URI) da equipa de segurança
    - Desenvolver e implementar a aplicação que acede aos teclas e segredos através de programação
- **Auditores**
    - Rever os registos de utilização para confirmar a utilização da tecla/secreta adequada e conformidade com as normas de segurança de dados

Agora vamos ver que permissões de acesso para cofre chave são necessários por cada função (e a aplicação) para executarem as tarefas atribuídas. 

| Função de utilizador    | Permissões de plano de gestão | Permissões de plano de dados |
|--------------|------------------------------|------------------------|
|Equipa de segurança|chave cofre contribuinte|Chaves: fazer cópia de segurança, criar, eliminar, obter, importar, lista, restaurar <br> Segredos: todos os|
|Os programadores/operador| chave cofre implementar permissão para que VMs implementarem o que podem obter segredos do Cofre de palavras chave | Nenhum |
|Auditores| Nenhum | Teclas: lista<br>Segredos: lista|
|Aplicação| Nenhum | Teclas: início de sessão<br>Segredos: obter |

>[AZURE.NOTE] Auditores precisam de permissão de listar para chaves e segredos, de modo que podem inspecionar atributos de teclas e segredos que não são emitidos nos registos, tais como etiquetas, datas de ativação e expiração.

Para além de permissão para cofre chave, todas as funções de três também precisam de acesso a outros recursos. Por exemplo, para ser capaz de implementar VMs (ou Web Apps etc.) Os programadores/operadores também precisa de acesso de 'Contribuinte' aos tipos de recursos. Auditores tem acesso de leitura para a conta de armazenamento onde estão armazenados os registos do cofre chave.

Uma vez que o foco deste artigo é proteger o acesso aos seus cofre chave, iremos apenas ilustram as partes relevantes relativos a este tópico e ignorar detalhes relativamente ao implementar certificados, aceder a teclas e segredos através de programação, etc. Os detalhes já estejam tapados noutro local. Implementação de certificados armazenados num cofre chave para VMs é abrangido numa [mensagem no blogue](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)e exista [exemplos de código](https://www.microsoft.com/download/details.aspx?id=45343) disponíveis que ilustra como utilizar o certificado de arranque para autenticar Azure AD para aceder a chave cofre.

A maior parte das permissões de acesso pode ser concedido através do portal Azure, mas para conceder permissões granular, poderá ter de utilizar o Azure PowerShell (ou clip de Azure) para obter o resultado pretendido. 

Vamos assumir os PowerShell fragmentos seguintes:

- O administrador do Azure Active Directory tenha criado grupos de segurança que representam as funções de três, nomeadamente Contoso a equipa de segurança, Contoso aplicação Devops, Contoso aplicação auditores. O administrador também adicionou utilizadores aos grupos que pertencem.

- **ContosoAppRG** é o grupo de recursos onde residem todos os recursos. **contosologstorage** é onde estão armazenados os registos. 

- Conta de armazenamento e **ContosoKeyVault** do cofre chave utilizada para cofre chave registos **contosologstorage** tem de ser na mesma localização Azure


Primeiro o administrador de subscrição atribui cofre contribuinte de chave e funções ' Administrador de acesso do utilizador ' para a equipa de segurança. Esta opção permite-a equipa de segurança gerir o acesso a outros recursos e gerir cofres chaves no grupo de recursos ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

O script seguinte ilustra como a equipa de segurança pode criar uma chave cofre, registo e a definir permissões de acesso para outras funções e a aplicação de configuração. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionToKeys backup,create,delete,get,import,list,restore -PermissionToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devlopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $role

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionToKeys list -PermissionToSecrets list
```

A função personalizada definida, só é pode ser atribuído à subscrição onde o grupo de recursos ContosoAppRG é criado. Se as mesmas funções personalizadas serão utilizadas para outros projetos em outras subscrições, do âmbito poderia ter mais subscrições adicionadas.

A atribuição de funções personalizadas para os programadores operadores para a permissão de "implementar/ação" está confinada a grupo de recursos. Desta forma apenas as VMs criados no grupo de recursos 'ContosoAppRG' receberão os segredos (certificado SSL e orientação do diapositivo notas arranque). Qualquer VMs que cria um membro da equipa Dev Center/ops no outro grupo de recursos não conseguir obter estes segredos mesmo se estes estivessem a URIs secreta.

Este exemplo ilustra um cenário simples. Cenários de vida real poderão ser mais complexos e poderá ter de ajustar permissões para a sua chave cofre com base nas suas necessidades. Por exemplo, no nosso exemplo, vamos partem do princípio de que a equipa de segurança irá fornecer a tecla e referências secretas (URIs e miniaturas) dessa equipa os programadores/operadores necessitar de referenciar nas suas aplicações. Por conseguinte, não precisa conceder os programadores/operadores qualquer acesso de plano de dados. Além disso, tenha em atenção que este exemplo foca-se sobre como proteger a sua chave cofre. Considerações semelhantes devem ser fornecidas para proteger a [sua VMs](https://azure.microsoft.com/services/virtual-machines/security/), [contas de armazenamento](../storage/storage-security-guide.md) e outros recursos Azure demasiado.

>[AZURE.NOTE] Nota: Este exemplo mostra como chave cofre acesso será bloqueado para baixo de produção. Os programadores devem ter os seus próprios subscrição ou resourcegroup onde tenham permissões completos para gerir as respetivas cofres, VMs e conta de armazenamento onde estes desenvolvem a aplicação.


## <a name="resources"></a>Recursos

-   [Controlo de acesso baseado em funções de Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    Este artigo explica o controlo de acesso baseado em Azure Active Directory função e como funciona.

-   [RBAC: Criadas em funções](../active-directory/role-based-access-built-in-roles.md)

    Este artigo fornece detalhes sobre todas as funções incorporadas disponíveis no RBAC.

-   [Noções sobre a implementação do Gestor de recursos e implementação clássica](../resource-manager-deployment-model.md)

    Este artigo explica a implementação do Gestor de recursos e modelos de implementação clássica e explica as vantagens de utilizar os grupos de Gestor de recursos e de recursos

-    [Gerir o controlo de acesso baseado em funções com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)

     Este artigo explica como gerir o controlo de acesso baseado em funções com PowerShell do Azure

-   [Gerir o controlo de acesso baseado em funções com a API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Este artigo mostra como utilizar a API REST para gerir RBAC.

-   [Controlo de acesso baseado em funções para Microsoft Azure a partir do Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Esta é uma hiperligação a um vídeo do canal 9 da conferência Ignite 2015 MS. Nesta sessão, falar sobre aceder a gestão e as capacidades de relatórios no Azure e explorar práticas recomendadas para proteger o acesso às subscrições do Azure utilizando o Azure Active Directory.

-   [Autorize acesso às aplicações web que utilizam OAuth 2.0 e o Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)

    Este artigo descreve concluído fluxo OAuth 2.0 para autenticar com o Azure Active Directory.

-   [chave cofre gestão REST APIs](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Este documento é a referência para o resto APIs gerir o seu Cofre chave através de programação, incluindo a definição de política de acesso de chave cofre.

-   [chave cofre REST APIs](https://msdn.microsoft.com/library/azure/dn903609.aspx)

    Ligar a documentação de referência REST API do Cofre de palavras chave.

-   [Controlo de acesso chave](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

    Ligar a documentação de referência de controlo de acesso de chave.

-   [Controlo de acesso secreta](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

    Ligar a documentação de referência de controlo de acesso de chave.

-   [Definir](https://msdn.microsoft.com/library/mt603625.aspx) e [Remover](https://msdn.microsoft.com/library/mt619427.aspx) do Cofre de palavras chave política de acesso através do PowerShell

    Ligações para fazer referência a documentação para os cmdlets do PowerShell gerir a política de acesso de chave cofre.

## <a name="next-steps"></a>Próximos passos

Para obter um tutorial introdução ao obter para que um administrador, consulte o artigo [Introdução ao Azure cofre chave](key-vault-get-started.md).

Para mais informações acerca da utilização de registo do cofre chave, consulte o artigo [registo do cofre chave Azure](key-vault-logging.md).

Para mais informações sobre como utilizar teclas e segredos com Azure cofre chave, consulte o artigo [sobre chaves e segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Se tiver questões sobre cofre chave, visite o [Azure cofre chave nos fóruns](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
