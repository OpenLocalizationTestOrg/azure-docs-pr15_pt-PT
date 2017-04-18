<properties
    pageTitle="Utilizar nós de limite vazia no HDInsight | Microsoft Azure"
    description="Como para adicionar um nó de limite ampty a cluster de HDInsight que pode ser utilizado como um cliente e para as aplicações de HDInsight o teste/anfitrião."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="use-empty-edge-nodes-in-hdinsight"></a>Utilizar nós de limite vazia no HDInsight

Saiba como adicionar um nó vazia limite a um cluster de baseado em Linux HDInsight. Um nó limite branco é uma máquina de virtual Linux com as mesmas ferramentas de cliente instalado e configurado tal como o headnodes, mas com serviços sem hadoop em execução. Pode utilizar o nó do limite para aceder ao cluster, testar as suas aplicações de cliente e alojamento suas aplicações de cliente. 

Pode adicionar um nó vazia limite a um cluster de HDInsight existente, para um novo cluster quando criar o cluster. Adicionar um limite branco nó é feito utilizando o Gestor de recursos do Azure modelo.  O exemplo seguinte demonstra como o fazer através de um modelo:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Como é mostrado no exemplo, opcionalmente, pode ligar a uma [ação de script](hdinsight-hadoop-customize-cluster-linux.md) para executar a configuração adicional, como instalar [Apache matiz](hdinsight-hadoop-hue-linux.md) no nó de limite.

Depois de ter criado um nó de margem, pode ligar-se para o nó do limite utilizando SSH e executar as ferramentas de cliente para aceder ao cluster Hadoop no HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adicionar um nó de limite a um cluster existente

Nesta secção, utilize um modelo de Gestor de recursos para adicionar um nó limite a um cluster de HDInsight existente.  O modelo de Gestor de recursos pode ser encontrado na [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). O modelo de Gestor de recursos chama um script de ação de script que se encontra no https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. O script não realizar ações.  Este é para demonstrar a ação de script de chamada a partir de um modelo de Gestor de recursos.

**Para adicionar um nó vazia limite a um cluster existente**

1. Crie um cluster de HDInsight se que ainda não tem um.  Consulte o artigo [Hadoop tutorial: começar a utilizar com base em Linux Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem seguinte para iniciar sessão no Azure e abra o modelo de Gestor de recursos do Azure no portal do Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configure as seguintes propriedades:

    - Nome de cluster: Introduza o nome de um cluster de HDInsight existente.
    - EDGENODESIZE: Selecione uma dos tamanhos VM.
    - EDGENODEPREFIX: O valor predefinido é **Novo**.  Utilizar o valor predefinido, o nome do nó limite é **Novo edgenode**.  Pode personalizar o prefixo a partir do portal. Também pode personalizar o nome completo do modelo.


4. Clique em **OK** para guardar as alterações.
5. No **grupo de recursos**, selecione um grupo de recursos.
6. Clique em **Rever legais termos**e, em seguida, clique em **comprar**.
7. Selecione **Afixar ao dashboard**e, em seguida, clique em **Criar**.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adicionar um nó de limite ao criar um cluster de

Nesta secção, utilize um modelo de Gestor de recursos para criar HDInsight cluster com um nó de limite.  Pode encontrar o modelo de Gestor de recursos num [contentor de armazenamento de Blobs do Azure](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)público. O modelo de Gestor de recursos chama um script de ação de script que se encontra no https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. O script não realizar ações.  Este é para demonstrar a ação de script de chamada a partir de um modelo de Gestor de recursos.

**Para adicionar um nó vazia limite a um cluster existente**

1. Crie um cluster de HDInsight se que ainda não tem um.  Consulte o artigo [Hadoop tutorial: começar a utilizar com base em Linux Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Clique na imagem seguinte para iniciar sessão no Azure e abra o modelo de Gestor de recursos do Azure no portal do Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configure as seguintes propriedades:
        
    - Nome de cluster: Introduza um nome para o novo cluster criar.
    - CLUSTERLOGINUSERNAME: Introduza o nome de utilizador Hadoop HTTP.  O nome predefinido é **admin**.
    - CLUSTERLOGINPASSWORD: Introduza a palavra-passe de utilizador do Hadoop HTTP.
    - SSHUSERNAME: Introduza o nome de utilizador SSH. O nome predefinido é **sshuser**.
    - SSHPASSWORD: Introduza a palavra-passe de utilizador do SSH.
    - LOCALIZAÇÃO: Introduza a localização do nome do grupo de recursos, o cluster e a conta de armazenamento predefinida.
    - CLUSTERTYPE: O valor predefinido é **hadoop**.
    - CLUSTERWORKERNODECOUNT: O valor predefinido é 2.
    - EDGENODESIZE: Selecione uma dos tamanhos VM.
    - EDGENODEPREFIX: O valor predefinido é **Novo**.  Utilizar o valor predefinido, o nome do nó limite é **Novo edgenode**.  Pode personalizar o prefixo a partir do portal. Também pode personalizar o nome completo do modelo.

4. Clique em **OK** para guardar as alterações.
5. No **grupo de recursos**, introduza um novo nome do grupo de recursos.
6. Clique em **Rever legais termos**e, em seguida, clique em **comprar**.
7. Selecione **Afixar ao dashboard**e, em seguida, clique em **Criar**. 


## <a name="access-an-edge-node"></a>Aceder a um nó de margem

O limite nó ssh ponto final é <EdgeNodeName>. <ClusterName>-ssh.azurehdinsight.net:22.  Por exemplo, novo-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

O nó do limite aparece como uma aplicação no portal do Azure.  O portal dá-lhe as informações para aceder a nó limite SSH a utilizar.

**Para verificar o ponto final do limite nó SSH**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de limite.
3. Clique em **aplicações** a partir do pá cluster. Deverá visualizar o nó de limite.  O nome predefinido é **Novo edgenode**.
4. Clique no limite nó. Deve ver o ponto final SSH.

**Utilizar o ramo no nó do limite**

5. Utilize SSH para ligar para o nó do limite.  Consulte o artigo [utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix, ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Utilize SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Depois de ter ligado para utilizar SSH o nó do limite, utilize o seguinte comando para abrir a consola ramo:

        hive
7. Execute o seguinte comando para mostrar as tabelas de ramo no cluster:

        show tables;

## <a name="delete-an-edge-node"></a>Eliminar um nó de margem

Pode eliminar um nó margem a partir do portal Azure.

**Para aceder a um nó de margem**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Abra o cluster HDInsight com um nó de limite.
3. Clique em **aplicações** a partir do pá cluster. Deverá ver uma lista de nós do limite.  
4. Botão direito do rato no nó de limite que pretende eliminar e, em seguida, clique em **Eliminar**.
5. Clique em **Sim** para confirmar.

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu como adicionar um nó de limite e como pode aceder ao nó do limite. Para saber mais, consulte os artigos seguintes:

- [HDInsight instalar aplicações](hdinsight-apps-install-applications.md): Saiba como instalar uma aplicação do HDInsight a sua clusters.
- [Instalar aplicações personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como implementar uma aplicação do HDInsight não publicada ao HDInsight.
- [HDInsight publicar aplicações](hdinsight-apps-publish-applications.md): Saiba como publicar HDInsight aplicações personalizadas ao Azure Marketplace.
- [MSDN: instalar uma aplicação de HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Saiba como definir HDInsight aplicações.
- [Baseado em Personalizar Linux HDInsight clusters utilizando Script acção](hdinsight-hadoop-customize-cluster-linux.md): Saiba como utilizar a ação de Script para instalar as aplicações adicionais.
- [Baseado em criar Linux Hadoop clusters no HDInsight utilizar modelos de Gestor de recursos](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como ligar a modelos de Gestor de recursos para criar clusters de HDInsight.

