
<properties
    pageTitle="Gerir os recursos com o clip do Azure | Microsoft Azure"
    description="Utilizar a Interface da linha de comandos do Azure (CLI) para gerir grupos e recursos Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="danlep"/>

# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Utilize o clip do Azure para gerir grupos de recursos e recursos Azure


> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md) 
- [Clip Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [REST API](resource-manager-rest-api.md)


A Interface de comandos do Azure (Azure CLI) é uma das várias ferramentas que pode utilizar para implementar e gerir recursos com o Gestor de recursos. Este artigo apresenta comuns formas de gerir grupos de recursos e de recursos Azure utilizando o clip do Azure no modo de Gestor de recursos. Para obter informações sobre como utilizar o clip para implementar recursos, consulte o artigo [recursos implementar modelos de Gestor de recursos e clip Azure](resource-group-template-deploy-cli.md). Para informações gerais sobre recursos Azure e o Gestor de recursos, visite a [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md).

>[AZURE.NOTE] Para gerir Azure recursos com o clip do Azure, tem de [instalar o clip do Azure](xplat-cli-install.md)e [Iniciar sessão no Azure](xplat-cli-connect.md) utilizando o `azure login` comando. Certifique-se o clip está no modo de Gestor de recursos (executar `azure config mode arm`). Se já tiver concluído estes pontos, está pronto para ir.



## <a name="get-resource-groups-and-resources"></a>Obter grupos de recursos e recursos

### <a name="resource-groups"></a>Grupos de recursos

Para obter uma lista de todos os grupos de recurso a sua subscrição e os seus locais, execute este comando.

    azure group list
    

### <a name="resources"></a>Recursos
 Para listar todos os recursos num grupo, tal como uma com nome *testRG*, utilize o seguinte comando.

    azure resource list testRG

Para ver um recurso individual dentro do grupo, tal como uma VM denominada *MyUbuntuVM*, utilize um comando semelhante ao seguinte.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
Repare que o parâmetro **Microsoft.Compute/virtualMachines** . Este parâmetro indica o tipo de recurso que está a pedir informações.
    
>[AZURE.NOTE]Ao utilizar os comandos do **azure recurso** que não seja o comando de **lista** , tem de especificar a versão da API do recurso com o parâmetro **+ o** . Se não tem a certeza sobre a versão de API, consulte o ficheiro de modelo e localize o campo apiVersion para o recurso. Para mais informações sobre as versões API no Gestor de recursos, consulte o artigo [fornecedores de Gestor de recursos, regiões, versões API e esquemas](resource-manager-supported-services.md).

Quando ver detalhes de um recurso, muitas vezes é útil para utilizar o `--json` parâmetro. Este parâmetro torna mais legível, o resultado porque alguns valores são estruturas aninhadas, ou coleções de sites. O exemplo seguinte demonstra devolver os resultados do comando **Mostrar** como um documento JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] Se pretender, guardar os dados JSON ficheiro utilizando a &gt; caráter para direcionar o resultado para um ficheiro. Por exemplo:
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### <a name="tags"></a>Etiquetas

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Adicionar e utilizar recursos


Para adicionar um recurso, tal como uma conta de armazenamento a um grupo de recursos, execute um comando semelhante a:

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"
    
Para especificar a versão da API do recurso, com o parâmetro **+ o** , além de utilizar o parâmetro **-p** para passar uma cadeia formatados como JSON com as propriedades necessárias ou adicionais.
    
    
Para eliminar um recurso existente como um recurso de máquina virtual, utilize um comando semelhante ao seguinte.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Para mover os recursos existentes para outra subscrição ou grupo de recursos, utilize o comando **Mover azure recurso** . O exemplo seguinte mostra como mover uma Cache Redis para um novo grupo de recursos. O parâmetro **-i** , fornece que uma lista separados por vírgulas do id do recurso estiverem mover.


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Controlar o acesso aos recursos

Pode utilizar o clip do Azure para criar e gerir políticas para controlar o acesso aos recursos Azure. Para informações gerais sobre definições de política e atribuir políticas para recursos, consulte o artigo [utilizar a política de recursos de gerir e controlar o acesso](resource-manager-policy.md).

Por exemplo, definir a política de seguinte para negar todos os pedidos de onde localização não é US ocidental ou América do Norte Central-nos e guardá-lo a policy.json de ficheiro de definição de política:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

Em seguida, execute o comando **criar a definição de política** :

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
Este comando mostra o resultado semelhante ao seguinte.

    + Criar a definição de política MyPolicy dados: política: MyPolicy dados: PolicyDefinitionId: /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    dados: PolicyType: dados personalizados: DisplayName: não definido dados: Descrição: não definido dados: PolicyRule: campo = localização, no = [westus, northcentralus], efeito = negar

 Para atribuir uma política do âmbito que pretende, utilize **PolicyDefinitionId** devolvido a partir do comando anterior. No exemplo seguinte, este âmbito é a subscrição, mas pode reduzir a recursos individuais ou grupos de recursos:

    atribuição de política Azure criar MyPolicyAssignment -p /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/###-###-###-###-### /

Pode obter, alterar ou remover definições de política utilizando os comandos de **definição de política Mostrar**, **defina a definição de política**e **Eliminar definição de política** .

Da mesma forma, pode obter, alterar ou remover as atribuições de políticas utilizando os comandos **Mostrar atribuição da política**, **definir a atribuição de política**e **Eliminar atribuição da política** .


## <a name="export-a-resource-group-as-a-template"></a>Exportar um grupo de recursos, como um modelo

Para um grupo de recursos existente, pode ver o modelo de Gestor de recursos para o grupo de recursos. Exportar o modelo oferece duas vantagens:

1. Pode facilmente automatizar implementações futuras da solução porque infraestrutura do está definida no modelo.

2. Podem ficar familiarizado com a sintaxe de modelo verificando o JSON que representa a solução.

Utilizar o clip do Azure, pode exportar um modelo que representa o estado atual do seu grupo de recursos, ou transferir o modelo que foi utilizado para uma implementação específica.

* **Exportar o modelo para um grupo de recursos** - isto é útil quando efetuar as alterações a um grupo de recursos e precisa de obter a representação de JSON do seu estado atual. No entanto, o modelo gerado contém um número mínimo de parâmetros e sem variáveis. A maioria dos valores no modelo são codificada. Antes de implementar o modelo gerado, poderá pretender converter mais dos valores de parâmetros para que pode personalizar a implementação para diferentes ambientes.

    Para exportar o modelo para um grupo de recursos para um diretório local, execute o `azure group export` comando conforme mostrado no seguinte exemplo. (Substituir um diretório local adequado no seu ambiente do sistema operativo.)

        azure group export testRG ~/azure/templates/

* **Transferir o modelo para uma determinada implementação** – isto é útil quando precisar de ver o modelo real que foi utilizado para implementar recursos. O modelo inclui todos os parâmetros e variáveis definidas para a implementação original. No entanto, se alguém na sua organização efetuado alterações ao grupo de recursos fora a definição no modelo, este modelo não representa o estado atual do grupo de recursos.

    Para transferir o modelo utilizado para uma implementação específica para um diretório local, execute o `azure group deployment template download` comando. Por exemplo:

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] Exportar modelo está na pré-visualização e não em todos os tipos de recursos atualmente, exportar um modelo. Ao tentar exportar um modelo, poderá ver um erro que diz que não foram exportados alguns recursos. Se for necessário, defina manualmente estes recursos no seu modelo após transferi-lo.



## <a name="next-steps"></a>Próximos passos

* Para obter detalhes de operações de implementação e resolver problemas de erros de implementação com o clip do Azure, consulte o artigo [operações de implementação de vista com o clip do Azure](resource-manager-troubleshoot-deployments-cli.md).
* Se pretender utilizar o clip para configurar uma aplicação ou script para aceder a recursos, consulte o artigo [Utilizar o Azure clip para criar um serviço principal para aceder a recursos](resource-group-authenticate-service-principal-cli.md).


