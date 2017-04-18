<properties
   pageTitle="Implementar um nó 3 Deis cluster | Microsoft Azure"
   description="Este artigo descreve como criar um nó 3 Deis cluster no Azure utilizando um modelo de Gestor de recursos do Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="HaishiBai"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# <a name="deploy-a-3-node-deis-cluster"></a>Implementar um nó 3 Deis cluster

Este artigo explica o aprovisionamento um [Deis](http://deis.io/) cluster no Azure. Cobrir todos os passos de criar os certificados necessários para implementar e escalar uma aplicação de **Ir** de exemplo no cluster aprovisionado recentemente.

O diagrama seguinte mostra a arquitetura do sistema implementada. Um administrador de sistema gere cluster utilizar Deis ferramentas, como **deis** e **deisctl**. Ligações são estabelecidas através de um balanceador de carga Azure, que reencaminha as ligações para um do membro nós no cluster. Aceder a clientes implementada através do Balanceador de carga de aplicações. Neste caso, o Balanceador de carga reencaminha o tráfego para um Deis malha router, que routs ainda mais o tráfego para o correspondentes contentores de Docker alojado no cluster.

  ![Diagrama de arquitectura de cluster Desis implementado](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

Para poder executar através dos seguintes passos, vai precisar de:

 * Uma subscrição ativa do Azure. Se não tiver uma, pode obter um fiável gratuito no [azure.com](https://azure.microsoft.com/).
 * Um id escolar ou profissional para utilizar grupos de recursos Azure. Se tiver uma conta pessoal e inicie sessão com um id do Microsoft, tem de [criar um id de trabalho a partir do seu um pessoal](virtual-machines-windows-create-aad-work-id.md).
 * Quer – dependendo do seu sistema operativo cliente – o [Azure PowerShell](../powershell-install-configure.md) ou o [Clip do Azure para Mac, Linux e Windows](../xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL é utilizada para gerar os certificados necessários.
 * Um cliente de Git como [Git festa](https://git-scm.com/).
 * Para testar a aplicação de exemplo, também terá de um servidor de DNS. Pode utilizar quaisquer servidores DNS ou os serviços que suportam os registos de caracteres universais.
 * Um computador para executar Deis ferramentas de cliente. Pode utilizar um computador local ou uma máquina virtual. Pode executar estas ferramentas no quase todas as distribuição Linux, mas as instruções seguintes utilizam Ubuntu.

## <a name="provision-the-cluster"></a>Aprovisionar o cluster

Nesta secção, irá utilizar um modelo de [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) a partir do repositório de abrir origem [modelos do guia de introdução do azure](https://github.com/Azure/azure-quickstart-templates). Em primeiro lugar, irá copiar para baixo o modelo. Em seguida, crie um novo SSH par para autenticação. E, em seguida, irá configurar um novo identificador para cluster. E, por fim, irá utilizar o script de Shell ou o script do PowerShell para aprovisionar o cluster.

1. Clonar do repositório: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Vá para a pasta de modelo:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Crie um novo SSH par utilizando ssh keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Gere um certificado utilizando a chave privada acima:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Aceda à [https://discovery.etcd.io/new](https://discovery.etcd.io/new) para gerar um novo token de cluster, que algo com o seguinte aspeto:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />
Cada cluster CoreOS tem de ter um exclusivo token deste serviço gratuito. Consulte o artigo [CoreOS documentação](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) para obter mais detalhes.

6. Modificar o ficheiro **na nuvem config.yaml** para substituir o token de **deteção** existente com o token de novo:

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Modificar o ficheiro **azuredeploy parameters.json** : abrir o certificado que criou no passo 4 num editor de texto. Copiar todo o texto entre `----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----` para o parâmetro **sshKeyData** (terá de remover todos os carateres de nova linha).

8. Modifica o parâmetro **newStorageAccountName** . Esta é a conta de armazenamento para discos VM SO. Este nome da conta tem de ser exclusivo global.

9. Modifica o parâmetro **publicDomainName** . Irão tornar-se parte do nome do DNS associado com o IP público do Balanceador de carga. O FQDN final terão o formato do _[valor deste parâmetro]_. _[Região]_. cloudapp.azure.com. Por exemplo, se especifique o nome como deishbai32 e o grupo de recursos é implementado à região de ocidental dos EUA, em seguida, o FQDN final para Balanceador de carga será deishbai32.westus.cloudapp.azure.com.

10. Guarde o ficheiro de parâmetro. E, em seguida, pode aprovisionar o cluster através do Azure PowerShell:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  ou clip Azure:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Assim que o grupo de recursos está aprovisionado, pode ver todos os recursos no grupo no Azure portal clássico. Como apresentado na captura de ecrã seguinte, o grupo de recursos contém uma rede virtual com três VMs, que são associadas ao mesmo conjunto de disponibilidade. O grupo também contém um balanceador de carga, que tem um endereço de IP público associado.

  ![O grupo de recursos aprovisionada no Azure portal clássico](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>Instalar o cliente

Precisa de **deisctl** para controlar o Deis cluster. Apesar de deisctl é instalado automaticamente em todos os nós de cluster, é aconselhável utilizar deisctl num computador administrativo em separado. Além disso, uma vez que todos os nós estiverem configurados com endereços IP privados, terá de utilizar SSH túnel através do Balanceador de carga, que tem um endereço IP público, para ligar para as máquinas nó. Seguem-se os passos de configuração de deisctl numa Ubuntu separada física ou máquina virtual.

1. Instalar deisctl:mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Adicione a chave privada para ssh agente:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Configure deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

O modelo define as regras de entrada na NAT que mapeiam 2223 para instância 1, 2224 a instância 2 e 2225 a instância 3. Isto redundância para utilizar a ferramenta de deisctl. Pode examinar estas regras no portal clássica Azure:

![Regras NAT no balanceador de carga](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [AZURE.NOTE] Atualmente o modelo suporta apenas clusters de nó 3. Este é devido a uma limitação no modelo de Gestor de recursos do Azure definição de regra NAT, não suporta a ligação de sintaxe.

## <a name="install-and-start-the-deis-platform"></a>Instalar e iniciar o Deis plataforma

Agora, pode utilizar deisctl para instalar e iniciar o Deis plataforma:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] Iniciar a plataforma leva-o até tempo (quanto 10 minutos). Especialmente, iniciar o serviço builder pode demorar muito tempo. E, por vezes, ocorre o mais algumas tentativas ser concluída com êxito: se a operação parece deixar de responder, experimente escrever `ctrl+c` para interromper a execução do comando e volte a tentar.

Pode utilizar `deisctl list` para verificar se todos os serviços estão a ser executado:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Parabéns! Agora que tem um corrente Deis clsuter no Azure! Em seguida, vamos implementar uma aplicação de ir para ver o cluster da ação de amostra.

## <a name="deploy-and-scale-a-hello-world-application"></a>Implementar e dimensionar uma aplicação Olá mundo

Os seguintes passos mostram como implementar um "Olá mundo" aceda a aplicação para o cluster. Os passos são baseados em [Deis documentação](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Para encaminhar malha funcione corretamente, terá de ter um registo de caracteres universais A para o seu domínio apontar para o endereço IP público do Balanceador de carga. A seguinte captura de ecrã mostra o registo a para um registo de domínio de exemplo na GoDaddy:

    ![Registo da Godaddy](media/virtual-machines-linux-deis-cluster/go-daddy.png)
<p />
2. Instalar deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Criar uma nova chave SSH e, em seguida, adicionar a chave pública GitHub (obviamente, pode também reutilizar suas chaves existentes). Para criar um novo SSH par, utilize:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Adicione id_rsa.pub ou a chave pública da sua escolha, a GitHub. Pode fazê-lo, utilizando o adicionar SSH chave botão no seu ecrã de configuração de teclas SSH:

  ![Chave Github](media/virtual-machines-linux-deis-cluster/github-key.png)
<p />
5. Registe um novo utilizador:

        deis register http://deis.[your domain]
<p />
6. Adicione a chave SSH:

        deis keys:add [path to your SSH public key]
  <p />      
7. Crie uma aplicação.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8.A operação git push irá acionar imagens de Docker a ser criadas e implementado, que irão demorar alguns minutos. A partir do meu experiência, ocasionalmente, passo 10 (Pushing imagem para o repositório privado) poderá deixar de responder. Quando isto acontece, pode parar o processo, remover a aplicação utilizando ' deis aplicações: destroy – um <application name> ` to remove the application and try again. You can use `deis apps:list' para descobrir o nome da sua aplicação. Se tudo está a funcionar, deverá ver algo semelhante ao seguinte no final do comando saídas:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Verifique se a aplicação está a funcionar:

        curl -S http://[your application name].[your domain]
  Deverá visualizar:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Escala de 3 instâncias a aplicação:

        deis scale cmd=3
<p />
11. Opcionalmente, pode utilizar deis informações para examinar detalhes da sua aplicação. Os resultados seguintes são a partir do meu implementação da aplicação:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>Próximos passos

Este artigo efectuei todos os passos para aprovisionar um novo Deis cluster no Azure utilizando um modelo de Gestor de recursos do Azure. O modelo suporta redundância numa ferramenta de ligações, bem como balanceamento de carga para aplicações implementadas. O modelo também evita utilizando IPs público em nós de membro, que guarda preciosas recursos públicos de IP e fornece um ambiente mais seguro das aplicações do anfitrião. Para saber mais, consulte os artigos seguintes:

[Descrição geral do Gestor de recursos do Azure] [resource-group-overview]  
[Como utilizar o clip do Azure] [azure-command-line-tools]  
[Utilizar o Azure PowerShell com o Gestor de recursos Azure] [powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md
