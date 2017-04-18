<properties
   pageTitle="Docker e compor numa máquina virtual | Microsoft Azure"
   description="Introdução a trabalhar com compor e Docker em máquinas virtuais de Linux no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="iainfou"/>

# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Introdução às Docker e compor definir e executar uma aplicação do contentor com várias numa máquina virtual Azure

Começar a utilizar Docker e [Compor](http://github.com/docker/compose) definir e executar uma aplicação complexa numa máquina virtual Linux no Azure. Com a compor, utilize um ficheiro de texto simples para definir uma aplicação que consiste vários contentores de Docker. Em seguida, giratório para cima da sua aplicação de um único comando faz tudo para implementar o seu ambiente definido. 

Por exemplo, este artigo mostra-lhe como configurar rapidamente um blogue WordPress com um base de dados do MariaDB SQL numa VM Ubuntu de back-end. Também pode utilizar compor para configurar as aplicações mais complexas.


## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Passo 1: Configurar um VM Linux como um anfitrião Docker

Pode utilizar vários procedimentos Azure e imagens disponíveis ou modelos de Gestor de recursos no Azure Marketplace para criar uma VM Linux e configure-o como um anfitrião Docker. Por exemplo, consulte o artigo [utilizando a extensão de VM Docker para implementar o seu ambiente](virtual-machines-linux-dockerextension.md) para criar rapidamente uma VM Ubuntu com a extensão do Azure Docker VM utilizando um [modelo de guia de introdução](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Quando utiliza a extensão Docker VM, a VM automaticamente está definida como um anfitrião Docker e compor já está instalado. O exemplo no artigo mostra-lhe como utilizar a [interface de comandos Azure para Mac, Linux e Windows](../xplat-cli-install.md) (Azure CLI) no modo de Gestor de recursos para criar a VM.

O comando básico do documento anterior cria um grupo de recursos com o nome `myResourceGroup` na `West US` localização e implementa uma VM com a extensão do Azure Docker VM instalada:

```bash
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>Passo 2: Certifique-se de que a compor está instalado

Quando a implementação estiver concluída, SSH para o novo anfitrião Docker utilizando o DNS dê um nome que fornecidos durante a implementação. Pode utilizar `azure vm show -g myDockerResourceGroup -n myDockerVM` para ver os detalhes da sua VM, incluindo o nome de DNS.

Para verificar que compor está instalado na VM, execute o seguinte comando:

```bash
docker-compose --version
```

Ver o resultado semelhante `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Se tiver utilizado outro método para criar um anfitrião Docker e precisa de instalar compor si, consulte a [documentação de compor](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Passo 3: Criar um ficheiro de configuração de docker compose.yml

Seguinte cria uma `docker-compose.yml` ficheiro, que é apenas texto ficheiro de configuração, para definir os contentores Docker para efetuar a VM. O ficheiro Especifica a imagem para executar no cada contentor (ou dever-se uma compilação a partir de um Dockerfile), variáveis de ambiente necessárias e dependências, portas e as ligações entre contentores. Para obter detalhes sobre sintaxe de ficheiro yml, consulte o artigo [referência de ficheiro compor](http://docs.docker.com/compose/yml/).

Criar o `docker-compose.yml` ficheiro da seguinte forma:

```bash
touch docker-compose.yml
```

Utilize o editor de texto Favoritos para adicionar alguns dados para o ficheiro. O exemplo seguinte utiliza a `vi` editor:

```bash
vi docker-compose.yml
```

Cole o exemplo seguinte no seu ficheiro de texto. Esta configuração utiliza imagens a partir do [Registo de DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o abrir origem blogues e conteúdo sistema de gestão) e uma base de dados MariaDB SQL de back-end ligada. Introduza o seu próprio `MYSQL_ROOT_PASSWORD` da seguinte forma:

```bash
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Passo 4: Iniciar os contentores com compor

No mesmo directório que o seu `docker-compose.yml` ficheiro, execute o seguinte comando (dependendo do seu ambiente, poderá ter de executar `docker-compose` utilizando `sudo`.):

```bash
docker-compose up -d

```

Este comando inicia os contentores de Docker especificados na `docker-compose.yml`. Bastam um minuto ou dois para este passo concluir. Consulte resultado semelhante ao exemplo seguinte:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

>[AZURE.NOTE] Certifique-se de que utilize a opção **-d** no arranque, para que os contentores de ser executado em segundo plano continuamente.

Para verificar se os contentores são para cima, escreva `docker-compose ps`. Deverá visualizar algo parecido com:

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Agora pode ligar a WordPress diretamente no VM na porta 80. Abra um browser e introduza o nome DNS do seu VM (tais como `http://myresourcegroup.westus.cloudapp.azure.com`). Deverá agora visualizar o WordPress ecrã de início, onde pode concluir a instalação e começar a trabalhar com a aplicação.

![Ecrã de início de WordPress][wordpress_start]


## <a name="next-steps"></a>Próximos passos

* Ir para o [Guia de utilizador do Docker VM extensão](https://github.com/Azure/azure-docker-extension/blob/master/README.md) para obter mais opções configurar Docker e compor na sua VM Docker. Por exemplo, uma das opções é colocar o ficheiro de yml compor (convertido para JSON) diretamente na configuração da extensão Docker VM.
* Consulte o artigo [referência da linha de comandos compor](http://docs.docker.com/compose/reference/) de e [Manual do utilizador](http://docs.docker.com/compose/) para obter mais exemplos de criar e implementar o contentores com várias aplicações.
* Utilizar um modelo de Gestor de recursos do Azure, quer o próprio ou um contribuiu da [Comunidade](https://azure.microsoft.com/documentation/templates/), para implementar uma VM Azure com Docker e uma aplicação configurado com compor. Por exemplo, o modelo de [Implementar um blogue WordPress com Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) utiliza Docker e compor para implementar rapidamente WordPress com back-end MySQL numa VM Ubuntu.
* Experimente integrar Docker compor um cluster [Docker Swarm](virtual-machines-linux-docker-swarm.md) . Consulte o artigo [Utilizar compor com enxame](https://docs.docker.com/compose/swarm/) para cenários.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png
