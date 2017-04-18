<properties
   pageTitle="Configurar um cluster de serviço ferro utilizando o Visual Studio | Microsoft Azure"
   description="Descreve como configurar um cluster de serviço ferro utilizando o modelo de Gestor de recursos do Azure criado por um projeto de grupo de recursos do Azure no Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configurar um cluster de serviço ferro utilizando o Visual Studio
Este artigo descreve como configurar um cluster de Azure Service ferro utilizando o Visual Studio e de um modelo de Gestor de recursos do Azure. Vamos irá utilizar um projeto de grupo de recursos do Visual Studio Azure para criar o modelo. Depois do modelo foi criado, pode ser implementada diretamente para o Azure a partir do Visual Studio. Também pode ser utilizado um script, ou como parte de Facilidade de integração contínua (CI).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Criar um modelo de cluster de serviço ferro utilizando um projeto de grupo de recursos Azure
Para começar, abrir o Visual Studio e crie um projeto de grupo de recursos Azure (-está disponível na pasta **na nuvem** ):

![Caixa de diálogo Nova projeto com o projeto de grupo de recursos do Azure selecionada][1]

Pode criar uma nova Visual Studio solução para este projeto ou adicioná-la a uma solução existente.

>[AZURE.NOTE] Se não vir o projeto de grupo de recursos Azure sob o nó na nuvem, não tiver o SDK do Azure instalado. Iniciação Web plataforma Installer ([instalá-lo agora](http://www.microsoft.com/web/downloads/platform.aspx) se ainda não o fez) e, em seguida, procure "Azure SDK para .NET" e instalar a versão que seja compatível com a sua versão do Visual Studio.

Depois de clicar no botão OK, o Visual Studio irá pedir-lhe para selecionar o modelo de Gestor de recursos que pretende criar:

![Selecione diálogo Azure modelo com o modelo de serviço de Cluster ferro seleccionado][2]

Selecione o modelo de serviço de Cluster ferro e clicar no botão OK novamente. Agora tem sido criados o projecto e o modelo de Gestor de recursos.

## <a name="prepare-the-template-for-deployment"></a>Preparar o modelo para implementação
Antes do modelo é implementado para criar o cluster, tem de fornecer valores para os parâmetros de modelo necessários. Estes valores de parâmetros são lidos a partir de `ServiceFabricCluster.parameters.json` ficheiro, que se encontra na `Templates` pasta do projeto de grupo de recursos. Abra o ficheiro e forneça os seguintes valores:

|Nome do parâmetro           |Descrição|
|-----------------------  |--------------------------|
|adminUserName            |O nome da conta de administrador para máquinas ferro de serviço (nós).|
|certificateThumbprint    |A impressão digital do certificado que protege cluster.|
|sourceVaultResourceId    |O *ID do recurso* do cofre chave localização onde armazenou o certificado que protege cluster.|
|certificateUrlValue      |O URL do certificado de segurança cluster.|

O modelo de Gestor de recursos do Visual Studio serviço ferro cria um cluster seguro que está protegido por um certificado. Este certificado está identificado pelos parâmetros de modelo últimas três (`certificateThumbprint`, `sourceVaultValue`, e `certificateUrlValue`), e tem de existir num **Azure chave cofre**. Para obter mais informações sobre como criar o certificado de segurança de cluster, consulte o artigo [cenários de segurança do serviço ferro cluster](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) artigo.

## <a name="optional-change-the-cluster-name"></a>Opcional: alterar o nome do cluster
Cluster cada serviço ferro tem um nome. Quando é criado um cluster de ferro no Azure, o nome do cluster determina (juntamente com o Azure região) o nome do sistema de nomes de domínio (DNS) para o cluster. Por exemplo, se o nome do seu cluster `myBigCluster`e a localização (Azure região) do grupo de recursos que será o anfitrião o novo cluster for Leste dos EUA, o nome DNS do cluster será `myBigCluster.eastus.cloudapp.azure.com`.

Por predefinição, o nome do cluster é gerado automaticamente e efetuado exclusivo por como anexar um sufixo aleatório a um prefixo "cluster". Isto torna muito fácil de utilizar o modelo como parte de um sistema de **integração contínua** (CI). Se quiser utilizar um nome para o seu cluster específico, definir uma que tenha significado para si, o valor da `clusterName` variável no ficheiro de modelo de Gestor de recursos (`ServiceFabricCluster.json`) para o seu nome de que selecionou. É a primeira variável definida desse ficheiro.

## <a name="optional-add-public-application-ports"></a>Opcional: adicionar portas de aplicação público
Poderá também pretende alterar as portas da aplicação público para o cluster antes da implementação. Por predefinição, o modelo abre-se para cima apenas dois públicas as portas TCP (80 e 8081). Se precisar de mais para as suas aplicações, modificar a definição do Balanceador de carga Azure no modelo. A definição está armazenada no ficheiro de modelo principal (`ServiceFabricCluster.json`). Abrir o ficheiro e procure `loadBalancedAppPort`. Cada porta está associada a três artefactos:

1. Uma variável de modelo que define o valor da porta TCP para a porta:

    ```json
    "loadBalancedAppPort1": "80"
    ```

2. *Sonda* que define a frequência e quanto tempo no carregamento Azure balanceador tenta utilizar um nó ferro serviço específico antes de falhar sobre a outro. As sondas fazem parte do recurso Balanceador de carga. Segue-se a definição de sonda para a primeira porta de aplicação predefinida:

    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```

3. Uma *regra de balanceamento de carga* que une a porta e a sonda, que permite aos balanceamento de carga através de um conjunto de nós de cluster ferro de serviço:

    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
Se as aplicações que planeia para implementar o cluster precisam de mais portas, pode adicioná-los ao criar sonda adicional e definições de regras de balanceamento de carga. Para mais informações sobre como trabalhar com Balanceador de carga Azure através de modelos de Gestor de recursos, consulte o artigo [começar a criar um balanceador de carga interno através de um modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Implementar o modelo utilizando o Visual Studio
Depois de ter guardado todos os valores de parâmetro obrigatório na`ServiceFabricCluster.param.dev.json` ficheiro, está pronto para implementar o modelo e criar o seu cluster de serviço ferro. Com o botão direito no projeto de grupo de recursos no Explorador de solução do Visual Studio e selecione **Implementar | Nova implementação …**. Se for necessário, Visual Studio irá mostrar a caixa de diálogo **Implementar a grupo de recursos** , pedir-lhe para autenticar Azure:

![Implementar a caixa de diálogo grupo de recursos][3]

A caixa de diálogo permite-lhe escolher um grupo de recursos do Gestor de recursos existente para o cluster e fornece-lhe a opção para criar um novo. Normalmente faz sentido para utilizar um grupo de recursos separada para um cluster de serviço ferro.

Depois de clicar no botão implementar, Visual Studio irá pedir-lhe para confirmar os valores de parâmetro de modelo. Clicar no botão **Guardar** . Um parâmetro não tem um valor persistente: a palavra-passe da conta administrativa para o cluster. Tem de fornecer um valor de palavra-passe quando Visual Studio pede-lhe para um.

>[AZURE.NOTE] A partir do Azure SDK 2.9, Visual Studio suporta palavras-passe de leitura do **Azure chave cofre** durante a implementação. Na caixa de diálogo parâmetros modelo Repare que o `adminPassword` caixa de texto de parâmetro tem um pequeno ícone de "teclas" no lado direito. Este ícone permite-lhe selecionar uma palavra-passe cofre chave existentes como a palavra-passe administrativa para o cluster. Certifique-se apenas ao Gestor de recursos do Azure access para implementação de modelo nas políticas de acesso avançadas do Cofre de chave de ativar primeiro. 

Pode monitorizar o progresso do processo de implementação na janela de saída do Visual Studio. Assim que a implementação do modelo estiver concluída, o seu novo cluster está pronto a utilizar!

>[AZURE.NOTE] Se nunca foi utilizado o PowerShell para administrar Azure a partir do computador que está a utilizar agora, terá de fazer um pouco sobre organização.
>1. Ativar a criação de scripts PowerShell ao executar o [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) comando. Para máquinas de desenvolvimento, é normalmente aceitável política "sem restrições".
>2. Decida se pretende permitir a recolha de dados de diagnóstico a partir dos comandos do PowerShell do Azure e executar [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) ou [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) conforme necessário. Isto irá evitar pedidos desnecessários durante a implementação do modelo.

Se existirem quaisquer erros, aceda ao [portal do Azure](https://portal.azure.com/) e abra o grupo de recursos que implementada no. Clique em **todas as definições**e, em seguida, clique em **implementações** no pá definições. Uma implementação do grupo de recursos falha deixa diagnóstico obter informações detalhadas aí.

>[AZURE.NOTE] Serviço ferro clusters necessitam de um determinado número de nós estar para cima manter a disponibilidade e preservar estado - designado "manutenção de quórum." Por conseguinte, não é seguro encerrar todas as máquinas no cluster, a menos que tiver executado em primeiro lugar uma [cópia de segurança completa do Estado](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Próximos passos
- [Saiba mais sobre a configuração cluster de serviço ferro utilizando o portal do Azure](service-fabric-cluster-creation-via-portal.md)
- [Saiba como gerir e implementar aplicações de serviço ferro utilizando o Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png
