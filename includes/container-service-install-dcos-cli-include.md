<properties
   pageTitle="Instalar o clip de CC/SO | Microsoft Azure"
   description="Instale o clip de CC/SO."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contentores, Micro-services, Cc/sistema operativo, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Este é para trabalhar com o Centro de dados/SO-based ACS clusters. Não é necessário efetuar este procedimento para ACS com base em enxame clusters.

Em primeiro lugar, [ligar ao seu cluster ACS com base no Centro de dados/SO](../articles/container-service/container-service-connect.md). Assim que tiver feito, pode instalar o clip de CC/SO no seu computador cliente com os comandos abaixo:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Se estiver a utilizar uma versão antiga de Python, poderá notar algumas "InsecurePlatformWarnings". Pode ignorar estes em segurança.

Para poder começar a utilizar sem ter de reiniciar a shell, execute:

```bash
source ~/.bashrc
```

Este passo não será necessário ao iniciar o novas conchas.

Agora pode confirmar que está instalado o clip:

```bash
dcos --help
```