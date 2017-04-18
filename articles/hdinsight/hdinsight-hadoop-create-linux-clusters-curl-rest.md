<properties
    pageTitle="Criar clusters Hadoop, HBase ou tempestade no Linux no HDInsight com o laço e o Azure REST API | Microsoft Azure"
    description="Saiba como criar clusters baseado em Linux HDInsight com o laço, modelos de Gestor de recursos do Azure e o Azure REST API. Pode especificar o tipo de cluster (Hadoop, HBase ou tempestade) ou utilize scripts para instalar componentes personalizados."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>Criar clusters baseados em Linux no HDInsight com o laço e os REST API do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

O Azure REST API permite-lhe efetuar operações de gestão nos serviços hospedados no plataforma do Azure, incluindo a criação de novos recursos como clusters baseado em Linux HDInsight. Este documento, vai aprender a criar modelos de Azure o Gestor de recursos para configurar um cluster de HDInsight e associadas de armazenamento, em seguida, utilize o laço para implementar o modelo para o Azure REST API para criar um novo cluster de HDInsight.

> [AZURE.IMPORTANT] Os passos neste documento utilizam o número predefinido de nós do trabalho (4) para um cluster de HDInsight. Se planeia mais de 32 nós de trabalho, na criação de cluster ou por dimensionamento cluster após a criação, tem de selecionar um tamanho de cabeça nó com, pelo menos, 8 núcleos e 14GB de ram.
>
> Para mais informações sobre tamanhos de nó e custos associados, consulte o artigo [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Pré-requisitos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Clip azure__. O clip do Azure é utilizada para criar um serviço principal, em seguida, é utilizado para gerar tokens de autenticação para pedidos de à Azure REST API.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__. Este utilitário está disponível através do seu sistema de gestão de pacote ou pode ser transferido das [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Se estiver a utilizar o PowerShell para executar os comandos neste documento, primeiro tem de remover o `curl` alias que cria por predefinição. Este alias utiliza invocar-WebRequest, um cmdlet do PowerShell, em vez de Laço quando utiliza o `curl` comando a partir de uma linha de comandos do PowerShell e irá devolver erros para muitos dos comandos utilizados neste documento.
    > 
    > Para remover este alias, utilize o seguinte a partir de linha de comandos do PowerShell:
    >
    > `Remove-item alias:curl`
    >
    > Depois de ter sido removido o alias, deverá poder utilizar a versão do Laço que tenha instalado no seu sistema.

### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-a-template"></a>Criar um modelo

Modelos de gestão de recursos Azure os documentos JSON que descrevem um __grupo de recursos__ e todos os recursos na mesma (como HDInsight.) Esta abordagem de modelo com base permite-lhe para todos os recursos que precisa para HDInsight num modelo de definir e gerir as alterações ao grupo como um todo através de __implementações__ aplicar alterações ao grupo.

Modelos normalmente são fornecidos em duas partes; o próprio modelo e um ficheiro de parâmetros preencher com valores específicos para a sua configuração. Para exmaple, o nome do cluster, administração nome e palavra-passe. Ao utilizar diretamente REST API, tem de combinar estes num único ficheiro. O formato deste documento JSON é:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Por exemplo, segue-se uma avaliação de aquisições os ficheiros de modelo e parâmetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que cria um cluster baseado em Linux utilizando uma palavra-passe para proteger a conta de utilizador SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Este exemplo será utilizado nos passos neste documento. Tem de substituir o marcador de posição de _valores_ na secção __parâmetros__ no final do documento com os valores que pretende utilizar para o seu cluster.

##<a name="login-to-your-azure-subscription"></a>Início de sessão à sua subscrição do Azure

Siga os passos em [ligar a uma subscrição do Azure a partir da linha de comandos Interface o Azure (Azure CLI)](../xplat-cli-connect.md) -se documentados e ligar a sua subscrição através de `azure login` comando.

##<a name="create-a-service-principal"></a>Criar um principal de serviço

> [AZURE.NOTE] Estes passos são uma versão abreviada das informações fornecidas na secção _serviço autenticar principal com uma palavra-passe - Azure clip_ do documento [principal de um serviço com o Gestor de recursos do Azure de autenticação](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) . Estes passos, crie um novo serviço principal que podem ser utilizados para autenticar os pedidos de REST API utilizados para criar Azure recursos, tais como um cluster de HDInsight.

1. A partir da linha de comandos, sessão de terminal ou shell, utilize o seguinte comando para as suas subscrições Azure da lista.

        azure account list
        
    Na lista, selecione a subscrição que pretende utilizar e tenha em atenção a coluna __Id__ . Este é o __ID da subscrição__ e será utilizada na maior parte dos passos neste documento.

2. Crie uma nova aplicação no Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Substituir valores para o `--name`, `--home-page`, e `--identifier-uris` com os seus próprios valores. Fornece uma palavra-passe para a nova entrada do Active Directory.
    
    > [AZURE.NOTE] Uma vez que está a criar esta aplicação para a autenticação através de um capital de serviço, o `--home-page` e `--identifier-uris` valores não precisam de fazer referência uma página web real alojado na internet; precisam de ser exclusivo URIs.
    
    A partir dos dados devolvidos, guarde o valor __AppId__ .
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Crie um serviço principal utilizando o valor de __AppId__ devolvido anteriormente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     A partir dos dados devolvidos, guarde o valor de __Id do objeto__ .
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Atribua uma função __proprietário__ ao serviço principal utilizando o valor de __ID do objeto__ devolvido anteriormente. Também tem de utilizar o __ID da subscrição__ que tiver sido obtido anterior.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Assim que este comando for concluída, o serviço principal agora tem acesso do proprietário para o ID da subscrição especificada.

##<a name="get-an-authentication-token"></a>Obter um token de autenticação

1. Utilize o seguinte para localizar o __ID de inquilino__ para a sua subscrição.

        azure account show -s <subscription ID>
        
    Os dados devolvidos, localize o __ID do inquilino__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Gera um novo token de utilizar a API do resto do Azure.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Substitua os valores obtidos ou utilizado anteriormente __TenantID__, __AppID__e __palavra-passe__ .

    Se este pedido for bem sucedido, irá receber uma resposta de 200 série e o corpo da resposta irá conter um documento JSON.

    O documento JSON devolvido por este pedido irá conter um elemento denominado __access_token__; o valor deste elemento é o token de acesso que tem de utilizar a autenticação pedidos utilizados nas secções seguintes deste documento.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##<a name="create-a-resource-group"></a>Criar um grupo de recursos

Utilize o seguinte para criar um novo grupo de recursos. Tem de criar o grupo pela primeira vez antes de poder criar os recursos como HDInsight cluster. 

* Substitua __SubscriptionID__ o ID da subscrição recebido enquanto cria o capital de serviço.
* Substitua __AccessToken__ token de acesso que recebeu no passo anterior.
* Substitua o Centro de dados que pretende criar o grupo de recursos e recursos, no __DataCenterLocation__ . Por exemplo, 'Sul Central dos E.u.a.'. 
* Substitua __ResourceGroupName__ com o nome que pretende utilizar para este grupo:

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Se este pedido for bem sucedido, irá receber uma resposta de 200 série e o corpo da resposta irá conter um documento JSON que contém informações sobre o grupo. O `"provisioningState"` elemento irá conter um valor de `"Succeeded"`.

##<a name="create-a-deployment"></a>Criar uma implementação

Utilize o seguinte procedimento para implementar a configuração do cluster (valores modelo e um parâmetro,) para o grupo de recursos.

* Substitua __SubscriptionID__ e __AccessToken__ com os valores utilizados anteriormente. 
* Substitua __ResourceGroupName__ o nome do grupo de recursos que criou na secção anterior.
* Substitua __DeploymentName__ com o nome que pretende utilizar para esta implementação.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Se tiver guardado o documento JSON que contém o modelo e os parâmetros para um ficheiro, pode utilizar os seguintes `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Se este pedido for bem sucedido, irá receber uma resposta de 200 série e o corpo da resposta irá conter um documento JSON que contém informações sobre o funcionamento de implementação.

> [AZURE.IMPORTANT] Tenha em atenção que a implementação foi submetida, mas não foi concluída neste momento. Pode demorar alguns minutos, normalmente cerca de 15, para a implementação concluir.

##<a name="check-the-status-of-a-deployment"></a>Verificar o estado de uma implementação

Para verificar o estado da implementação, utilize o seguinte:

* Substitua __SubscriptionID__ e __AccessToken__ com os valores utilizados anteriormente. 
* Substitua __ResourceGroupName__ o nome do grupo de recursos que criou na secção anterior.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Isto irá devolver informações um documento JSON que contém informações sobre o funcionamento de implementação. O `"provisioningState"` elemento irá conter o estado de implementação; Se este contém um valor de `"Succeeded"`, em seguida, a implementação foi concluída com êxito. Neste momento, o seu cluster deverá estar disponível para utilização.

##<a name="next-steps"></a>Próximos passos

Agora que criou com êxito um cluster de HDInsight, utilize o seguinte para saber como trabalhar com o seu cluster. 

###<a name="hadoop-clusters"></a>Hadoop clusters

* [Utilizar ramo com HDInsight](hdinsight-use-hive.md)
* [Utilizar porco com HDInsight](hdinsight-use-pig.md)
* [Utilizar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase clusters

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clusters tempestade

* [Desenvolver topologias Java para tempestade no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizar componentes de Python tempestade no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementar e monitorizar topologias com tempestade no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
