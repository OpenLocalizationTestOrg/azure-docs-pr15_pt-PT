<properties
    pageTitle="Instalar a Interface da linha de comandos do Azure | Microsoft Azure"
    description="Instalar a Interface da linha de comandos de Azure (CLI) para Mac, Linux e Windows começar a utilizar os serviços do Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"/>
    
# <a name="install-the-azure-cli"></a>Instalar o clip Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Clip Azure](xplat-cli-install.md)

Instale rapidamente a Interface de comandos do Azure (Azure CLI) para utilizar um conjunto de comandos baseados na shell de abrir origem para criar e gerir recursos no Microsoft Azure. Tem várias opções para instalar estas ferramentas em diferentes plataformas no seu computador: 

* **pacote npm** - execute npm (o Gestor de pacote para JavaScript) para instalar o pacote mais recente do Azure clip no seu distribuição Linux ou sistema operativo. Requer node.js e npm no seu computador.
* **Installer** – transferir um installer para fácil instalação no Mac ou no Windows.
* **Contentor docker** - começar a utilizar o clip num contentor de Docker pronto-e-use mais recente. Necessita de anfitrião Docker no seu computador.
    
Para obter mais opções e fundo, consulte o artigo o repositório de projeto [GitHub](https://github.com/azure/azure-xplat-cli). 

Quando o clip do Azure estiver instalado, [ligá-lo com a sua subscrição Azure](xplat-cli-connect.md) e executar os comandos do **azure** a partir do seu interface de comandos (festa, Terminal, linha de comandos e assim sucessivamente) para trabalhar com os recursos do Azure.



## <a name="option-1-install-an-npm-package"></a>Opção 1: Instalar um pacote de npm

Para instalar o clip a partir de um pacote de npm, certifique-se de que transferiu e instalou o [Node.js e npm mais recentes](https://nodejs.org/en/download/package-manager/). Em seguida, execute o **npm instalar** para instalar o pacote do azure clip: 

    npm install -g azure-cli

Em Linux distribuições, poderá ter de utilizar **sudo** para executar com êxito o comando __npm__ , da seguinte forma:

    sudo npm install -g azure-cli

> [AZURE.NOTE]Se precisar de instalar ou atualizar Node.js e npm no seu distribuição Linux ou sistema operativo, recomendamos que instale a versão mais recente do Node.js LTS (4. x). Se utilizar uma versão mais antiga, poderá obter erros de instalação. 

Se preferir, transfira o mais recente de Linux [ficheiro tar] [ linux-installer] para o pacote de npm localmente. Em seguida, instale o pacote de npm transferido da seguinte forma (no Linux distribuições que poderá ter de utilizar **sudo**):

    npm install -g <path to downloaded tar file>

## <a name="option-2-use-an-installer"></a>Opção 2: Utilizar um programa de instalação

Se utilizar um Mac ou computador Windows, os seguintes programas de instalação do clip estão disponíveis para transferência:

* [Programa de instalação do Mac OS X][mac-installer]

* [Windows MSI][windows-installer] 

>[AZURE.TIP]No Windows, também pode transferir o [Web plataforma Installer](https://go.microsoft.com/?linkid=9828653) para instalar o clip. Este programa de instalação dá-lhe a opção de instalação adicionais SDK do Azure e ferramentas de linha de comandos depois de instalar o clip. 


## <a name="option-3-use-a-docker-container"></a>Opção 3: Utilizar um contentor Docker

Se configurou o computador como um anfitrião [Docker](https://docs.docker.com/engine/understanding-docker/) , pode executar o clip mais recente do Azure num contentor de Docker. Execute o seguinte comando (no Linux distribuições que poderá ter de utilizar **sudo**):

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>Execute os comandos de clip do Azure
Depois do clip do Azure instalada, execute o comando **azure** a partir do seu interface de utilizador da linha de comandos (festa, Terminal, linha de comandos e assim sucessivamente). Por exemplo, para executar o comando de ajuda, escreva o seguinte:

```
azure help
```
> [AZURE.NOTE]Em algumas distribuições Linux, poderá receber um erro semelhante a `/usr/bin/env: ‘node’: No such file or directory`. Este erro provém recentes instalações do Node.js a ser instalada no /usr/bin/nodejs. Para corrigi-lo, crie uma ligação simbólica para /usr/bin/node ao executar este comando:

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para ver a versão do clip Azure instalou, escreva o seguinte:

```
azure --version
```

Agora, está pronto! Para aceder a todos os comandos de clip para funcionar com o seus próprio recursos, [ligar à sua subscrição do Azure a partir do clip o Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Quando utiliza o clip do Azure pela primeira vez, verá uma mensagem a perguntar se pretende permitir que a Microsoft recolher informações de utilização. A participação é voluntária. Se optar por participar, pode deixar em qualquer altura ao executar `azure telemetry --disable`. Para permitir a participação em qualquer altura, executar `azure telemetry --enable`.


## <a name="update-the-cli"></a>Atualizar o clip

A Microsoft disponibiliza frequentemente actualizados versões do clip Azure. Reinstalar o clip utilizando o instalador do seu sistema operativo ou executar o contentor Docker mais recente. Em alternativa, se tiver as mais recentes Node.js e npm instalado, atualizar, escrevendo o seguinte (no Linux distribuições que poderá ter de utilizar **sudo**).

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Activar a conclusão de separador

Conclusão do separador de comandos do clip é suportada para Mac e o Linux.

Para ativá-lo no zsh, execute:

```
echo '. <(azure --completion)' >> .zshrc
```

Para ativá-lo no festa, execute:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Próximos passos 

* [Ligar a partir do clip à sua subscrição do Azure](xplat-cli-connect.md) para criar e gerir recursos Azure.

* Para saber mais sobre o clip Azure, transferir código fonte, comunicar problemas, ou contribuírem para o projeto, visite o [GitHub repositório para o clip Azure](https://github.com/azure/azure-xplat-cli).

* Se tiver questões sobre como utilizar o clip do Azure ou Azure, visite os [Fóruns do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

* Se pretender, também pode experimentar com base em Python [Azure Clip 2.0 pré-visualização](https://github.com/azure/azure-cli).

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
