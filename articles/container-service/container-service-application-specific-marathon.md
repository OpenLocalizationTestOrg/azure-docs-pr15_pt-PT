<properties
   pageTitle="Aplicação ou serviço de Marathon específicas do utilizador | Microsoft Azure"
   description="Criar uma aplicação ou serviço de Marathon específicas do utilizador"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contentores, Marathon, Micro-services, Cc/SO, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# <a name="create-an-application-or-user-specific-marathon-service"></a>Criar uma aplicação ou serviço de Marathon específicas do utilizador

O serviço de contentor Azure fornece um conjunto de servidores principais no qual podemos pré-configurar Apache Mesos e Marathon. Estes podem ser utilizados para orquestrar as aplicações no cluster, mas é melhor não está a utilizar os servidores principais para esse efeito. Por exemplo, optimização da configuração do Marathon requer o registo para os servidores principais eles próprios e que faça alterações – este encoraje exclusivos servidores principais que são um pouco diferentes do padrão e têm de ser tratados e gerido de forma independente. Para além disso, a configuração de uma ferramenta necessária a uma equipa poderão não estar a configuração ideal para outra equipa.

Neste artigo, explicaremos como adicionar uma aplicação ou serviço de Marathon específicas do utilizador.

Uma vez que este serviço irá pertencer a um único utilizador ou equipa, são gratuitos configurá-lo de qualquer forma que eles pretendem. Além disso, o serviço de contentor Azure irá Certifique-se de que o serviço continua a ser executado. Se o serviço falhar, o serviço de contentor Azure irá reinicie-o por si. A maioria das vezes não mesmo notará que tinha tempo de inatividade.

## <a name="prerequisites"></a>Pré-requisitos

[Implementar uma instância do Azure contentor serviço](container-service-deployment.md) com o tipo de orchestrator CC/SO e [Certifique-se de que o cliente pode ligar ao seu cluster](container-service-connect.md). Além disso, execute os passos seguintes.

[AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Criar uma aplicação ou serviço de Marathon específicas do utilizador

Comece por criar um ficheiro de configuração de JSON que define o nome do serviço de aplicação que pretende criar. Aqui utilizamos `marathon-alice` como o nome de quadro. Guarde o ficheiro como algo parecido com `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Em seguida, utilize o clip de CC/SO para instalar a instância Marathon com as opções que são definidas no ficheiro de configuração:

```bash
dcos package install --options=marathon-alice.json marathon
```

Deverá agora visualizar o `marathon-alice` serviço em execução no separador Serviços da sua IU CC/SO. IU será `http://<hostname>/service/marathon-alice/` se pretender aceder diretamente à mesma.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Definir o clip de CC/SO para aceder ao serviço

Opcionalmente, pode configurar o seu clip CC/SO para aceder a este novo serviço definindo a `marathon.url` propriedade para apontarem para o `marathon-alice` instância da seguinte forma:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Pode verificar a instância do Marathon com o seu clip está a trabalhar contra a `dcos config show` comando. Pode reverter para utilizar o seu serviço de Marathon principal com o comando `dcos config unset marathon.url`.
