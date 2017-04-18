<properties 
  pageTitle="Implementar o seu próprio registo Docker privado no Azure | Microsoft Azure"
  description="Descreve como pode utilizar o registo de Docker para alojar as imagens de contentor o serviço de armazenamento de Blobs do Azure."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="timlt"
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="09/27/2016" 
  ms.author="ahmetb" />

# <a name="deploying-your-own-private-docker-registry-on-azure"></a>Implementar o seu próprio registo Docker privado no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Este documento descreve o que Docker registo privado é e mostra como pode implementar uma imagem de contentor Docker registo 2.0 para um registo privado Docker no Microsoft Azure utilizando o armazenamento de Blobs do Azure.

Este documento assume:

1. Saber como utilizar Docker e têm Docker imagens para armazenar. (Não? [Saiba mais sobre Docker](https://www.docker.com))
2. Se tiver um servidor que tenha motor Docker instalado. (Não? [Fazê-lo rapidamente no Azure.](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/))


## <a name="what-is-a-private-docker-registry"></a>O que é um registo Docker privados?

Para poder enviar as suas aplicações em contentores na nuvem, construir uma imagem de contentor Docker e armazená-lo num local para que possa ser utilizado por si e por outras pessoas. 

Enquanto é fácil criar uma imagem de contentor e envio-lo na nuvem, é um desafio para armazenar imagem gerada forma fiável. Por que razão, Docker oferece um serviço centralizado denominado [Docker concentrador] [ docker-hub] para armazenar as imagens na nuvem e permite-lhe criar contentores a qualquer momento utilizar essas imagens o contentor.

Apesar do [Concentrador de Docker] [ docker-hub] é um serviço pago para armazenar o contentor privados da aplicação imagens, necessidades dos Docker aspectos programadores e fornece um conjunto de ferramentas Abrir origem para armazenar as imagens no seu próprio registo Docker privado atrás uma firewall ou no local sem atingir Internet pública.
Uma vez que o armazenamento de Blobs do Azure é fácil seguro, pode utilizá-lo rapidamente para criar e utilizar um registo Docker privado no Azure que pode controlar o seu próprio.

## <a name="why-should-you-host-a-docker-registry-on-azure"></a>Por que motivo que aloja um registo Docker no Azure?

Por que aloja a instância de registo de Docker no Microsoft Azure e armazenar as imagens no armazenamento de Blobs do Azure, pode ter várias vantagens:

**Segurança:** As imagens Docker não deixam Azure centros de dados, para que estes não passam a Internet pública tal como se estivesse a utilizar Docker concentrador.
  
**Desempenho:** As imagens Docker são armazenadas no Centro de dados do mesmo ou na região como as aplicações. Isto significa que as imagens serão importadas mais rápido e mais eficazmente em comparação com a Docker concentrador.

**Fiabilidade:** Ao utilizar o armazenamento de Blobs do Microsoft Azure, pode fazer utilizar muitas propriedades de armazenamento como elevada disponibilidade, redundância, o armazenamento de prémio (SSDs) e assim sucessivamente.

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>Configurar registo de Docker para utilizar o armazenamento de Blobs do Azure

(É recomendado para ler a [documentação Docker registo 2.0]de[documentos do registo] antes de continuar.)

Pode [Configurar] [ registry-config] o registo de Docker de duas maneiras diferentes.
Pode:

1. Utilizar um `config.yml` ficheiro. Neste caso terá de criar uma imagem Docker separada na parte superior de `registry` imagem.
2. Substituir o ficheiro de configuração de predefinido através de variáveis de ambiente: obtém coisas concluídas sem criar e manter numa imagem Docker separada.

Por razões de simplificar, este tópico segue opção 2, utilizando as variáveis de ambiente.

Para executar um registo Docker instância que:

* utiliza a sua conta de armazenamento do Azure para armazenar as imagens
* a Máquina Virtual porta 5000
* não tem nenhuma autenticação configurada (não recomendado, consulte a nota abaixo)

que precisa para executar o seguinte comando Docker no seu terminal festa (substituir `<storage-account>` e `<storage-key>` com as suas credenciais):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Assim que o comando sai, pode ver o contentor que aloja a instância de registo de Docker privada ao executar o `docker ps` comando no seu anfitrião:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] Configurar a segurança para o registo de Docker não é abrangido neste documento e o registo será acessível a qualquer pessoa sem autenticação por predefinição se abrir a porta à porta de registo no ponto final de Máquina Virtual ou Balanceador de carga, se utilizar o comando implementação acima.
>
> Leia o [Registo de Docker configurar] [ registry-config] documentação para saber como seguro a instância do registo e as imagens.

## <a name="next-steps"></a>Próximos passos

Assim que tiver o seu registo configurar, é hora para aceder a utilizá-la mais algumas. Comece com a docker de [documentos do registo]. 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[documentos do registo]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 
