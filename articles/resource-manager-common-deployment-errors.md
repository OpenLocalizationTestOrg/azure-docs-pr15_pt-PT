<properties
   pageTitle="Resolver erros comuns de implementação do Azure | Microsoft Azure"
   description="Descreve como resolver erros comuns quando implementar recursos Azure utilizando o Gestor de recursos do Azure."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="Erro de implementação, implementação do azure, implementar azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Resolver erros comuns de implementação do Azure com o Gestor de recursos do Azure

Este tópico descreve como pode resolver algumas comuns que poderá encontrar de erros de implementação do Azure. Se precisar de mais informações sobre o que correu mal com a sua implementação, consulte pela primeira vez [operações de implementação de vista](resource-manager-troubleshoot-deployments-portal.md) e, em seguida, regresse deste artigo para obter ajuda para resolver o erro. O código de erro, que verá uma lista as secções neste tópico.

## <a name="invalid-template"></a>Modelo inválido

Este erro pode resultar a partir de vários tipos de erros. 

### <a name="syntax-error"></a>Erro de sintaxe

Se receber uma mensagem de erro que indica a validação de modelo falhou, poderá ter um problema de sintaxe no seu modelo. 

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Este erro é fácil de fazer uma vez que expressões de modelo podem ser complexas. Por exemplo, a atribuição de nome seguinte para uma conta de armazenamento contém um conjunto de parênteses, três funções, três conjuntos de parênteses, um conjunto de aspas simples e uma propriedade:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Se não fornecer a sintaxe de correspondência, o modelo produz um valor que é diferente do seu intenção.

Quando recebe este tipo de erro, reveja cuidadosamente a sintaxe da expressão. Considere utilizar um editor de JSON como o [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou o [Código do Visual Studio](resource-manager-vs-code.md), o qual pode avisá-lo sobre erros de sintaxe. 

### <a name="incorrect-segment-lengths"></a>Comprimento de segmento incorretos

Outro modelo inválido erro ocorre quando o nome do recurso não está no formato correto.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

Um recurso de nível de raiz tem de ter um menos segmento no nome que tipo de recurso. Cada segmento é diferenciar por uma barra. No exemplo seguinte, o tipo de tem dois segmentos e o nome tiver um segmento, pelo que é um **nome válido**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",
      ...
    }

Mas o exemplo seguinte é **não um nome válido** , porque tem o mesmo número de segmentos de como o tipo.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",
      ...
    }

Para obter recursos subordinado, o tipo e nome tem o mesmo número de segmentos de. Este número de segmentos de faz sentido porque o nome completo e tipo para a criança inclui o nome do principal e o tipo. Assim, o nome completo ainda tem um menos segmento de que o tipo de total. 

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",
                    ...
                }
            ]
        }
    ]

Introdução aos segmentos direita pode ser difícil com tipos de Gestor de recursos que são aplicados ao longo de fornecedores de recursos. Por exemplo, aplicar um cadeado recurso a um web site requer um tipo com quatro segmentos. Por conseguinte, o nome é três segmentos:

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
        ...
    }

### <a name="copy-index-is-not-expected"></a>Índice de cópia não é esperado

Encontrar este erro **InvalidTemplate** quando tiver aplicado o elemento de **Copiar** a uma parte do modelo que não suporte este elemento. Só pode aplicar o elemento de cópia para um tipo de recurso. Não é possível aplicar copiar para uma propriedade dentro de um tipo de recurso. Por exemplo, aplicar copiar para uma máquina virtual, mas não é possível aplicar aos discos SO para uma máquina virtual. Em alguns casos, pode converter um recurso de subordinado a um recurso de elemento principal para criar um ciclo de cópia. Para mais informações sobre como utilizar a cópia, consulte o artigo [criar várias instâncias de recursos no Gestor de recursos do Azure](resource-group-create-multiple.md).

### <a name="parameter-is-not-valid"></a>Parâmetro não é válido

Se o modelo especifica valores permitidos um parâmetro e fornecer um valor que não é uma desses valores, recebe uma mensagem semelhante ao seguinte erro:

    Code=InvalidTemplate; 
    Message=Deployment template validation failed: 'The provided value {parameter vaule} 
    for the template parameter {parameter name} is not valid. The parameter value is not 
    part of the allowed value(s)

Duplo, verifique os valores permitidos no modelo e fornecer um durante a implementação.

## <a name="not-found-or-resource-not-found"></a>Não foi encontrado (ou não encontrar o recurso)

Quando o modelo incluir o nome de um recurso que não pode ser resolvido, recebe um erro semelhante a:

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Se estiver a tentar implementar o recurso em falta no modelo, verifique se tem de adicionar uma dependência. Gestor de recursos optimiza implementação através da criação de recursos em paralelo, sempre que possível. Se um recurso tem de ser implementado após a outro recurso, terá de utilizar o elemento **dependsOn** no seu modelo para criar uma dependência o outro recurso. Por exemplo, quando implementar uma aplicação web, o plano de serviço de aplicação tem de existir. Se não tiver especificado que a aplicação web depende do plano de serviço de aplicação, o Gestor de recursos cria ambos os recursos ao mesmo tempo. Receber um erro a indicar que não é possível localizar o recurso de plano de serviço de aplicação, porque não existe ainda ao tentar definir uma propriedade da aplicação web do. Evitar este erro ao definir a dependência no web app.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }
 
Também vê este erro quando o recurso existe num grupo de recursos diferente daquele que está a ser implementada. Nesse caso, utilize a [função resourceId](resource-group-template-functions.md#resourceid) para obter o nome completamente qualificado do recurso.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    } 

Se tentar para utilizar as funções de [referência](resource-group-template-functions.md#referenc) ou [listKeys](resource-group-template-functions.md#listkeys) com um recurso que não pode ser resolvido, recebe a seguinte mensagem de erro:

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource 
    group {resource group name} was not found.

Procure uma expressão que incluem a função de **referência** . Duplo Verifique se os valores de parâmetro estão corretos.

## <a name="storage-account-already-exists-or-already-taken"></a>Armazenamento conta já existe (ou já tomadas)

Para contas de armazenamento, tem de fornecer um nome para o recurso que seja exclusivo ao longo do Azure. Se não fornecer um nome exclusivo, receberá um erro como:

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

Pode criar um nome exclusivo ao concatenar seu Convenção de nomenclatura com o resultado da função [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Se implementar uma conta de armazenamento com o mesmo nome como uma conta de armazenamento existente na sua subscrição, mas fornecer uma localização diferente, receberá um erro que indica que a conta de armazenamento já existe numa localização diferente. Ou eliminar a conta de armazenamento existente ou fornecer na mesma localização que a conta de armazenamento existente.

## <a name="account-name-invalid"></a>Nome da conta inválido

Consulte o erro **AccountNameInvalid** quando tentar dar um nome que inclui uma conta de armazenamento proibidas carateres. Nomes de conta de armazenamento tem de estar entre 3 e 24 carateres de comprimento e utilizar números e letras minúsculas apenas.

## <a name="no-registered-provider-found"></a>Nenhum fornecedor registado encontrado

Quando implementar o recurso, poderá receber a mensagem e o seguinte código de erro:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation} 
    and API version {api-version} for type {resource-type}.

Receberá este erro para um dos três motivos:

1. Localização não suportada para o tipo de recurso
2. Versão da API não suportado para o tipo de recurso
3. Não foi registado o fornecedor de recursos para a sua subscrição

A mensagem de erro deve dar-lhe sugestões para as versões de API e localizações suportadas. Pode alterar o modelo para um dos valores sugeridos. Maioria dos fornecedores são registadas automaticamente ao portal do Azure ou a interface de comandos que está a utilizar, mas não todos. Se não tiver utilizado um fornecedor de recurso em particular antes, poderá ter de registar esse fornecedor. Pode descobrir mais acerca dos fornecedores de recursos através do PowerShell ou clip de Azure.

### <a name="powershell"></a>PowerShell

Para ver o seu estado de registo, utilize **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Para registar um fornecedor, utilize **Register AzureRmResourceProvider** e fornecer o nome do fornecedor de recursos que deseja registar.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Para obter as localizações suportadas para um tipo específico de recursos, utilize:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Para obter as versões de API suportadas para um tipo específico de recursos, utilize:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### <a name="azure-cli"></a>Clip Azure

Para ver se o fornecedor está registado, utilize o `azure provider list` comando.

    azure provider list

Para registar um fornecedor de recursos, utilize o `azure provider register` comando e especifique o *espaço de nomes* para registar.

    azure provider register Microsoft.Cdn

Para ver as localizações suportadas e as versões de API para um fornecedor de recursos, utilize:

    azure provider show -n Microsoft.Compute --json > compute.json

## <a name="operation-not-allowed"></a>Operação não é permitida

Poderá ter problemas quando implementação excede uma quota, que pode ser por grupo de recursos, subscrições, contas e outros âmbitos. Por exemplo, a sua subscrição poderá ser configurada para limitar o número de tarolos para uma região. Se tentar implementar uma máquina de virtual com mais tarolos que a quantidade de permitidos, receberá um erro a indicar que a quota de tenha sido ultrapassada.
Para obter informações completas quota, consulte o artigo [subscrição Azure e limites de serviço, quotas e as restrições](azure-subscription-service-limits.md).

Para examinar às quotas sua subscrição de núcleos, pode utilizar o `azure vm list-usage` no clip do Azure comando. O exemplo seguinte ilustra a que a quota de principais de uma conta de avaliação gratuita é 4:

    azure vm list-usage

Qual devolve:

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Se implementar um modelo que cria núcleos mais do que quatro na região ocidental dos EUA, obtém um erro de implementação que tem a seguinte apresentação:

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core. 
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

Ou no PowerShell, pode utilizar o cmdlet **Get-AzureRmVMUsage** .

    Get-AzureRmVMUsage

Qual devolve:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

Nestes casos, deve aceda ao portal do e ficheiros um problema de suporte para elevar a sua quota de região no qual pretende implementar.

> [AZURE.NOTE] Lembre-se de que para grupos de recursos, a quota de é para cada região individual, não para toda a subscrição. Se precisar de implementar 30 núcleos nos EUA Ocidental, terá de pedir 30 núcleos de Gestor de recursos no ocidental dos e.u.a.. Se precisar de implementar 30 núcleos em qualquer uma das regiões à qual tem acesso, deverá pedir para 30 Gestor de recursos núcleos em todas as regiões.

## <a name="invalid-content-link"></a>Ligação de conteúdo inválida

Quando receber a mensagem de erro:

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

Provavelmente ter tentado ligar a um modelo aninhado que não está disponível. Verificação de dupla o URI fornecido para o modelo aninhado. Se o modelo de existir numa conta de armazenamento, certifique-se o URI acessível. Poderá ter de ser efetuada com um token de SA. Para mais informações, consulte o artigo [utilizar modelos ligados com o Gestor de recursos do Azure](resource-group-linked-templates.md).

## <a name="authorization-failed"></a>Falha de autorização

Poderá receber um erro durante a implementação, uma vez que a conta ou principal tentar implementar os recursos de serviço não tiver acesso a executar essas ações. Azure Active Directory permite-lhe ou o administrador para controlar quais identidades pode aceder quais os recursos com um grau excelente de precisão. Por exemplo, se a sua conta é atribuída a função de leitor, não será possível criar recursos. Nesse caso, verá uma mensagem de erro indicando que autorização falhou.

Para mais informações sobre o controlo de acesso baseado em funções, consulte o artigo [Azure Role-Based o controlo de acesso](./active-directory/role-based-access-control-configure.md).

Para além de controlo de acesso baseado em funções, as suas ações de implementação poderão estar limitadas por políticas da subscrição. Através das políticas, o administrador pode impor convenções de todos os recursos implementados a subscrição. Por exemplo, um administrador pode exigir que é fornecido um valor de etiqueta específico para um tipo de recurso. Se não cumprir os requisitos da política, receberá um erro durante a implementação. Para mais informações sobre as políticas, consulte o artigo [Utilizar a política para gerir os recursos e controlar o acesso](resource-manager-policy.md).

## <a name="troubleshooting-virtual-machines"></a>Resolução de problemas máquinas virtuais

| Erro | Artigos |
| -------- | ----------- |
| Erros de extensão dos scripts personalizados | [Falhas de extensão VM do Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />ou<br />[Falhas de extensão Linux VM](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Imagem do sistema operativo erros de aprovisionamento | [Novos erros VM do Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />ou<br />[Novos erros Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md ) |
| Falhas de atribuição | [Falhas de alocação VM do Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />ou<br />[Falhas de alocação Linux VM](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Seguro erros de Shell (SSH) ao tentar ligar-se | [Ligações de Shell seguras Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Ligar a aplicação em execução no VM de erros | [Aplicação em execução no Windows VM](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />ou<br />[Aplicação em execução numa VM Linux](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Erros de ligação de ambiente de trabalho remoto | [Ligações de ambiente de trabalho remotas para VM do Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Erros de ligação resolvidos pelo Reimplementar | [Implementar Máquina Virtual para novo nó Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Erros do serviço de nuvem | [Problemas de implementação do serviço na nuvem](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## <a name="troubleshooting-other-services"></a>Resolução de problemas de outros serviços

A tabela seguinte não é uma lista completa dos tópicos de resolução de problemas para Azure. Em vez disso,-foca-se em problemas relacionados com implementar ou configurar recursos. Se precisar de ajuda de resolução de problemas de tempo de execução com um recurso, consulte a documentação para esse serviço Azure.

| Serviço | Artigo |
| -------- | -------- |
| Automatização | [Sugestões de resolução de problemas para erros comuns nas automatização do Azure](./automation/automation-troubleshooting-automation-errors.md) |
| Pilha Azure | [Resolução de problemas do Microsoft Azure pilha](./azure-stack/azure-stack-troubleshooting.md) |
| Pilha Azure | [Web Apps e pilha Azure](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md ) |
| Fábrica de dados | [Resolução de problemas de fábrica de dados](./data-factory/data-factory-troubleshoot.md) |
| Serviço ferro | [Resolver problemas comuns quando implementar serviços no ferro de serviço do Azure](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Recuperação de sites | [Monitorizar e resolver problemas de proteção para máquinas virtuais e servidores físicos](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Armazenamento | [Monitorizar, diagnosticar e resolver problemas de armazenamento do Windows Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Resolução de problemas de implementação do dispositivo StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Base de dados SQL | [Resolução de problemas de ligação à base de dados do SQL Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| Armazém de dados SQL | [Resolução de problemas armazém de dados do Azure SQL](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="understand-when-a-deployment-is-ready"></a>Compreender quando uma implementação está pronta

Gestor de recursos do Azure relatórios sucesso uma implementação quando todos os fornecedores de voltar a partir de implementação com êxito. No entanto, esta mensagem não seja necessariamente significa que o seu grupo de recursos está "ativa e pronto para os seus utilizadores." Por exemplo, uma implementação poderá ter de transferir atualiza os, aguarde no modelo de que não sejam de recursos ou instalar scripts complexos. Gestor de recursos não souber quando estas tarefas concluir, porque não estiverem atividades que controla um fornecedor. Nestes casos, pode ser algum tempo até que os recursos estão prontos a utilizar reais. Como resultado, que deve esperar que o estado da implementação com êxito algum tempo até que pode ser utilizada a sua implementação.

Pode impedir que Azure relatórios implementação com êxito, no entanto, criando um script personalizado para o modelo personalizado. O script sabe como monitorizar a implementação completa para todo o sistema de preparação e com êxito devolve apenas quando os utilizadores podem interagir com a implementação completa. Se quiser certificar-se de que a sua extensão é o último para executar, utilize a propriedade **dependsOn** no seu modelo.

## <a name="next-steps"></a>Próximos passos

- Para obter informações acerca da auditoria ações, consulte o artigo [operações de auditoria com o Gestor de recursos](resource-group-audit.md).
- Para saber mais sobre ações para determinar os erros durante a implementação, consulte o artigo [operações de implementação de vista](resource-manager-troubleshoot-deployments-portal.md).
