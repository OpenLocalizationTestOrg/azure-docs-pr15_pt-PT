<properties
   pageTitle="Instalar o MongoDB numa VM Linux | Microsoft Azure"
   description="Saiba como instalar e configurar MongoDB numa máquina virtual Linux no Azure utilizando o modelo de implementação do Gestor de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/29/2016"
   ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Instalar e configurar o MongoDB uma VM Linux no Azure
[MongoDB](http://www.mongodb.org) é uma popular abrir origem alto desempenho NoSQL base de dados. Este artigo mostra-lhe como instalar e configurar o MongoDB numa VM Linux no Azure utilizando o modelo de implementação do Gestor de recursos. Exemplos são apresentados como esse nível de detalhe para:

- [Instalar e configurar uma instância de MongoDB básica manualmente](#manually-install-and-configure-mongodb-on-a-vm)
- [Criar uma instância de MongoDB básica utilizando um modelo de Gestor de recursos](#create-basic-mongodb-instance-on-centos-using-a-template)
- [Criar um MongoDB complexa cluster sharded com réplica define através de um modelo de Gestor de recursos](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="prerequisites"></a>Pré-requisitos
Este artigo requer o seguinte:

- uma conta Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).
- [Clip do Azure](../xplat-cli-install.md) tem sessão iniciada com`azure login`
- o clip do Azure *tem de ser* na utilização do modo de Gestor de recursos do Azure`azure config mode arm`


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instale e configure MongoDB numa VM manualmente
MongoDB [fornecem instruções de instalação](https://docs.mongodb.com/manual/administration/install-on-linux/) para distros Linux incluindo vermelho chapéu / CentOS, SUSE, Ubuntu e Debian. O exemplo seguinte cria uma `CoreOS` VM utilizando uma tecla SSH armazenada em `.ssh/azure_id_rsa.pub`. Responda os pedidos para nome da conta de armazenamento, nome de DNS e credenciais de administrador:

```bash
azure vm quick-create --ssh-publickey-file .ssh/azure_id_rsa.pub --image-urn CentOS
```

Iniciar sessão para a VM utilizando o endereço IP público apresentado no fim do passo de criação de VM anterior:

```bash
ssh ops@40.78.23.145
```

Para adicionar as origens de instalação para MongoDB, criar uma `yum` ficheiro do repositório da seguinte forma:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Abra o ficheiro de repo MongoDB para edição. Adicione as linhas seguintes:

```bash
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Instalar o MongoDB utilizando `yum` da seguinte forma:

```bash
sudo yum install -y mongodb-org
```

Por predefinição, SELinux é aplicada no imagens de CentOS que o impede de aceder ao MongoDB. Instalar ferramentas de gestão de política e configurar SELinux para permitir que MongoDB trabalhar com na sua porta TCP predefinida 27017 da seguinte forma. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço de MongoDB da seguinte forma:

```bash
sudo service mongod start
```

Verifique se a instalação MongoDB através da utilização do local de ligação `mongo` cliente:

```bash
mongo
```

Agora, teste a instância MongoDB ao adicionar alguns dados e, em seguida, procurar:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Caso deseje, configure MongoDB para iniciar automaticamente durante um reinício do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Criar a instância de MongoDB básica no CentOS através de um modelo
Pode criar uma instância de MongoDB básica sobre uma única VM de CentOS utilizando o modelo de guia de introdução Azure seguintes a partir do Github. Este modelo utiliza a extensão de Script personalizado para Linux para adicionar um `yum` repositório à sua recentemente criado CentOS VM e, em seguida, instalar MongoDB.

- [Instância MongoDB básicas no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `WestUS` região. Introduza os seus próprios valores da seguinte forma:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [AZURE.NOTE] O clip do Azure devolve numa linha de comandos dentro de aguardar alguns segundos da criação de implementação, mas a instalação e configuração demora alguns minutos a concluir. Verificar o estado de implementação com `azure group deployment show myResourceGroup`, introduzir o nome do seu grupo de recursos, consoante adequado. Aguarde até que a `ProvisioningState` mostra 'Com êxito' antes de tentar SSH para a VM.

Assim que a implementação estiver concluída, SSH para a VM. Obter o endereço IP do seu VM utilizando o `azure vm show` comando tal como no exemplo seguinte:

```bash
azure vm show --resource-group myResourceGroup --name myVM
```

Junto ao fim do resultado, o `Public IP address` é apresentada. SSH para sua VM com o endereço IP do seu VM:

```bash
ssh ops@138.91.149.74
```

Verifique se a instalação MongoDB através da utilização do local de ligação `mongo` cliente da seguinte forma:

```bash
mongo
```

Agora, teste a instância ao adicionar alguns dados e procurar da seguinte forma:

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Criar um Cluster de Sharded MongoDB complexa CentOS através de um modelo
Pode criar um cluster de sharded MongoDB complexo utilizando o modelo de guia de introdução Azure seguintes a partir do Github. Este modelo segue as [práticas recomendadas do MongoDB sharded cluster](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para fornecer redundância e disponibilidade de alta. O modelo cria duas shards, com três nós em cada conjunto de réplica. Também são criada um config server conjunto de réplicas com três nós, plus dois `mongos` servidores de router para fornecer consistência para aplicações a partir de ao longo de shards.

- [Cluster de Sharding MongoDB CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [AZURE.WARNING] Implementar este cluster de sharded MongoDB complexa requer mais do que 20 tarolos, que é normalmente a contagem de core predefinida por região para uma subscrição. Abra um pedido de suporte Azure para aumentar a contagem de core.

O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `WestUS` região. Introduza os seus próprios valores da seguinte forma:

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [AZURE.NOTE] O clip do Azure devolve numa linha de comandos dentro de aguardar alguns segundos da criação de implementação, mas a instalação e configuração podem demorar mais de uma hora para concluir. Verificar o estado de implementação com `azure group deployment show myResourceGroup`, ajustando o nome do seu grupo de recursos, consoante adequado. Aguarde até que a `ProvisioningState` mostra 'Com êxito' antes de ligar para os VMs.


## <a name="next-steps"></a>Próximos passos
Nestes exemplos, se ligar para a instância MongoDB localmente a partir da VM. Se pretender estabelecer ligação com a instância MongoDB de outro VM ou rede, certifique-se de adequado [são criadas as regras de grupo de segurança de rede](virtual-machines-linux-nsg-quickstart.md).

Para mais informações sobre a criação de utilização de modelos, consulte o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Os modelos de Gestor de recursos do Azure utilizam a extensão de Script de personalizada para transferir e executar scripts na sua VMs. Para mais informações, consulte o artigo [utilizar a extensão de Script do Azure personalizada com Linux Virtual máquinas](virtual-machines-linux-extensions-customscript.md).