<properties
   pageTitle="Implementar um cluster de serviço de contentor Azure | Microsoft Azure"
   description="Implemente um cluster de serviço de contentor Azure utilizando o portal do Azure, para o clip do Azure ou PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contentores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="deploy-an-azure-container-service-cluster"></a>Implementar um cluster de serviço de contentor do Azure

O serviço de contentor Azure fornece uma implementação rápida do contentor de abrir origem popular soluções clusters e orchestration. Ao utilizar o serviço de contentor Azure, pode implementar CC/SO e Docker Swarm clusters com modelos de Gestor de recursos do Azure ou o portal do Azure. Implementar destes clusters utilizando conjuntos de escala de Máquina Virtual do Azure e clusters tirar partido das ofertas de rede e armazenamento Azure. Para aceder ao serviço de contentor Azure, tem uma subscrição do Azure. Se não tiver um, em seguida, pode inscrever-se para uma [versão de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Este documento irá guiar pelo implementar um cluster de serviço de contentor Azure utilizando o [Azure portal](#creating-a-service-using-the-azure-portal), a [interface de comandos Azure (CLI)](#creating-a-service-using-the-azure-cli)e o [módulo Azure PowerShell](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Criar um serviço, utilizando o portal do Azure

Inicie sessão portal do Azure, selecione **Novo**e procure o Azure Marketplace **Azure o serviço de contentor**.

![Criar implementação 1](media/acs-portal1.png)  <br />

Selecione **O serviço de contentor Azure**e clique em **Criar**.

![Criar implementação 2](media/acs-portal2.png)  <br />

Introduza as seguintes informações:

- **Nome de utilizador**: Este é o nome de utilizador que será utilizado para uma conta em cada uma das máquinas virtuais e conjuntos de escala de máquina virtual no cluster Azure o serviço de contentor.
- **Subscrição**: selecione uma subscrição do Azure.
- **Grupo de recursos**: selecione um grupo de recursos existente ou crie um novo.
- **Localização**: selecione uma região Azure para a implementação do Azure Service de contentor.
- **Chave pública SSH**: adicionar a chave pública que será utilizada para autenticação contra máquinas virtuais de serviço do Azure contentor. É muito importante que esta tecla contém sem quebras de linha, e que inclui o prefixo 'ssh-rsa' e o 'username@domain' postfix. -Deverá ter um aspeto algo semelhante ao seguinte: **ssh rsa AAAAB3Nz … <>...... UcyupgH azureuser@linuxvm **. Para obter orientações sobre a criação de teclas de Shell seguro (SSH), consulte os artigos [Linux]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) e [Windows]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) .

Clique em **OK** quando estiver pronto para continuar.

![Criar implementação 3](media/acs-portal3.png)  <br />

Selecione um tipo de Orchestration. As opções são:

- **Cc/SO**: implementar um cluster de CC/SO.
- **Swarm**: implementar um cluster Docker Swarm.

Clique em **OK** quando estiver pronto para continuar.

![Criar implementação 4](media/acs-portal4.png)  <br />

Introduza as seguintes informações:

- **Contar o número de modelo global de**: O número de modelos globais no cluster.
- **Contagem de agente**: para Docker Swarm, este será o número inicial de agentes do conjunto de escala agente. Para CC sistema operativo, este será o número inicial de agentes num conjunto de escala privado. Para além disso, é criado um conjunto de escala público, que contém um número predeterminado de agentes. O número de agentes neste conjunto de escala público é determinado por modelos globais quantas foram criadas no cluster – um agente do público para uma forma mestre e duas agentes públicos para três ou cinco modelos globais.
- **Tamanho de máquina virtual agente**: O tamanho das máquinas virtuais agente.
- **Prefixo DNS**: um nome exclusivo do mundo que será utilizado para partes importantes dos nomes de domínio completamente qualificado para o serviço do prefixo.

Clique em **OK** quando estiver pronto para continuar.

![Criar implementação 5](media/acs-portal5.png)  <br />

Após ter sido concluído validação do serviço, clique em **OK** .

![Criar implementação 6](media/acs-portal6.png)  <br />

Clique em **Criar** para iniciar o processo de implementação.

![Criar implementação 7](media/acs-portal7.png)  <br />

Se tiver eleito afixar a implementação para o portal do Azure, pode ver o estado da implementação.

![Criar implementação 8](media/acs-portal8.png)  <br />

Quando tiver concluído a implementação, cluster o serviço de contentor Azure é pronto a utilizar.

## <a name="create-a-service-by-using-the-azure-cli"></a>Criar um serviço, utilizando o clip do Azure

Para criar uma instância do serviço de contentor Azure utilizando a linha de comandos, tem uma subscrição do Azure. Se não tiver um, em seguida, pode inscrever-se para uma [versão de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Também tem de ter [instalado](../xplat-cli-install.md) e [configurado](../xplat-cli-connect.md) o clip do Azure.

Para implementar um cluster de Docker Swarm ou Cc/SO, selecione um dos seguintes modelos de GitHub. Tenha em atenção que ambos estes modelos são os mesmos, com a exceção da seleção de orchestrator predefinida.

* [Modelo do Centro de dados/SO](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Modelo de enxame](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Em seguida, certifique-se de que o clip do Azure foi ligado a uma subscrição do Azure. Pode fazê-lo utilizando o seguinte comando:

```bash
azure account show
```
Se não for devolvida uma conta Azure, utilize o seguinte comando para iniciar sessão o clip no Azure.

```bash
azure login -u user@domain.com
```

Em seguida, configure as ferramentas de clip Azure a utilizar o Gestor de recursos do Azure.

```bash
azure config mode arm
```

Criar um grupo de recursos Azure e cluster de serviço de contentor com o seguinte comando, onde:

- **RESOURCE_GROUP** é o nome do grupo de recursos que pretende utilizar para este serviço.
- **Localização** é o Azure região onde o grupo de recursos e implementação do Azure contentor Service serão criados.
- **TEMPLATE_URI** é a localização do ficheiro implementação. Tenha em atenção que tem de ser o ficheiro não processado, não num ponteiro de para a IU GitHub. Para localizar este URL, selecione o ficheiro azuredeploy.json no GitHub e clique no botão **matéria-prima** .

> [AZURE.NOTE] Quando executa este comando, a shell de irá pedir-lhe para valores de parâmetros de implementação.

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Fornecer parâmetros de modelo

Esta versão do comando requer que definir parâmetros de forma interativa. Se quiser fornecer parâmetros, tal como uma cadeia de formatados como JSON, pode fazê-lo ao utilizar o `-p` mudar. Por exemplo:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

Em alternativa, pode fornecer um ficheiro de parâmetros formatados como JSON utilizando o `-e` mudar:

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Para ver um ficheiro de parâmetros de exemplo com o nome `azuredeploy.parameters.json`, localize-o com os modelos de serviço de contentor Azure GitHub.

## <a name="create-a-service-by-using-powershell"></a>Criar um serviço utilizando o PowerShell

Também pode implementar um cluster de serviço de contentor Azure com PowerShell. Este documento baseia-se a versão 1.0 [módulo Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Para implementar um cluster de Docker Swarm ou Cc/SO, selecione um dos seguintes modelos. Tenha em atenção que ambos estes modelos são os mesmos, com a exceção da seleção de orchestrator predefinida.

* [Modelo do Centro de dados/SO](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Modelo de enxame](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Antes de criar um cluster na sua subscrição do Azure, certifique-se de que a sessão do PowerShell tenha sido sessão iniciada no Azure. Pode fazê-lo com a `Get-AzureRMSubscription` comando:

```powershell
Get-AzureRmSubscription
```

Se for necessário iniciar sessão no Azure, utilize o `Login-AzureRMAccount` comando:

```powershell
Login-AzureRmAccount
```

Se está a implementar a um novo grupo de recursos, primeiro tem de criar o grupo de recursos. Para criar um novo grupo de recursos, utilize o `New-AzureRmResourceGroup` comando e, especifique um nome e de destino região grupo de recursos:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Depois de criar um grupo de recursos, pode criar o seu cluster com o seguinte comando. URI do modelo pretendido será especificado para o `-TemplateUri` parâmetro. Quando executa este comando, PowerShell irá pedir-lhe para valores de parâmetros de implementação.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Fornecer parâmetros de modelo

Se estiver familiarizado com o PowerShell, sabe que pode percorrer os parâmetros disponíveis para um cmdlet ao escrever um sinal de subtração (-) e, em seguida, prima a tecla de tabulação. Esta funcionalidade mesmo também funciona com parâmetros definidos no seu modelo. Assim que escrever o nome do modelo, o cmdlet obtém o modelo, analisa os parâmetros e adiciona os parâmetros de modelo para o comando dinamicamente. Isto torna muito fácil especificar os valores de parâmetro de modelo. E, caso se esqueça de um valor de parâmetro necessários, PowerShell pede-lhe para o valor.

Segue-se o comando completo, com parâmetros incluídos. Poderá fornecer o seus próprio valores para os nomes dos recursos.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Próximos passos

Agora que tem um cluster de funcionamento, consulte estes documentos para ligação e detalhes de gestão:

- [Ligar a um cluster de serviço de contentor do Azure](container-service-connect.md)
- [Trabalhar com o serviço de contentor Azure e Cc/SO](container-service-mesos-marathon-rest.md)
- [Trabalhar com o serviço de contentor Azure e Docker enxame](container-service-docker-swarm.md)
