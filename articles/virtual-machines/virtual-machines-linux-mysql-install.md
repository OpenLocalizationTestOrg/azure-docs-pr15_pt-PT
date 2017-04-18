<properties
    pageTitle="Configurar o MySQL numa VM Linux | Microsoft Azure "
    description="Saiba como instalar a pilha de MySQL numa Linux máquina virtual (Ubuntu ou sistema RedHat família SO) no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


#<a name="how-to-install-mysql-on-azure"></a>Como instalar MySQL no Azure


Neste artigo, irá Saiba como instalar e configurar MySQL numa máquina virtual Azure executar Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##<a name="install-mysql-on-your-virtual-machine"></a>Instalar MySQL no seu computador virtual

> [AZURE.NOTE] Já tem de ter uma máquina de virtual do Microsoft Azure a executar Linux para poder concluir este tutorial. Consulte o artigo o [Azure Linux VM tutorial](virtual-machines-linux-quick-create-cli.md) , para criar e configurar uma VM Linux com `mysqlnode` como o nome VM e `azureuser` como utilizador antes de continuar.

Neste caso, utilize 3306 porta como a porta MySQL.  

Ligar para o Linux VM que criou através do betumes. Se esta for a primeira vez que utilizar Azure Linux VM, consulte o artigo como utilizar betumes ligar a uma VM Linux [aqui](virtual-machines-linux-mac-create-ssh-keys.md).

Vamos irá utilizar o pacote do repositório para instalar MySQL5.6 como exemplo neste artigo. Na verdade, MySQL5.6 tem mais melhoria no desempenho que MySQL5.5.  Obter mais informações [aqui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###<a name="how-to-install-mysql56-on-ubuntu"></a>Como instalar MySQL5.6 no Ubuntu
Vamos utilizar o Linux VM com Ubuntu a partir do Azure aqui.

- Passo 1: Instalar MySQL servidor 5.6 mudar para a `root` utilizador:

            #[azureuser@mysqlnode:~]sudo su -

    Instale o servidor do MySQL precisar 5.6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Durante a instalação, verá uma caixa de diálogo janela poping até ao perguntar para definir MySQL raiz abaixo palavra-passe e o precisa de configurar a palavra-passe aqui.

    ![imagem](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Introdução a palavra-passe novamente para confirmar.

    ![imagem](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Passo 2: Servidor do MySQL precisar de início de sessão

    Quando tiver terminado a instalação de servidor do MySQL, MySQL serviço será iniciado automaticamente. Pode iniciar sessão com o servidor do MySQL precisar `root` utilizador.
    Utilizar a abaixo comando a palavra-passe de início de sessão e de entrada.

             #[root@mysqlnode ~]# mysql -uroot -p

- Passo 3: Gerir o serviço do MySQL em execução

    (um) obter o estado do serviço do MySQL

             #[root@mysqlnode ~]# service mysql status

    (b) iniciar o serviço do MySQL

             #[root@mysqlnode ~]# service mysql start

    (c) parar o serviço do MySQL

             #[root@mysqlnode ~]# service mysql stop

    (d) reinicie o serviço do MySQL

             #[root@mysqlnode ~]# service mysql restart


###<a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Como instalar MySQL em vermelho chapéu SO família das como CentOS, Oracle Linux
Vamos utilizar o Linux VM com CentOS ou Oracle Linux aqui.

- Passo 1: Adicionar o repositório de MySQL Yum mudar para `root` utilizador:

            #[azureuser@mysqlnode:~]sudo su -

    Transfira e instale o pacote de lançamento do MySQL:

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Passo 2: Edite por baixo de ficheiro para ativar o repositório MySQL para transferir o pacote de MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Atualize a cada valor deste ficheiro para abaixo:

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Passo 3: MySQL de instalar a partir do MySQL repositório MySQL instalar:

           #[root@mysqlnode ~]#yum install mysql-community-server

    Serão instalados o pacote de rotações/minuto do MySQL e todos os pacotes relacionados.

- Passo 4: Gerir o serviço de MySQL em execução

    (um) verificar o estado de serviço do servidor do MySQL:

           #[root@mysqlnode ~]#service mysqld status

    (b) verifique se o servidor de porta do MySQL predefinido está em execução:

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) inicie o servidor do MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) pare o servidor do MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL conjunto para iniciar durante o arranque-up de sistema:

           #[root@mysqlnode ~]#chkconfig mysqld on


###<a name="how-to-install-mysql-on-suse-linux"></a>Como instalar MySQL no SUSE Linux
Vamos utilizar o Linux VM com OpenSUSE aqui.

- Passo 1: Transferir e instalar o servidor do MySQL

    Mudar para `root` utilizador através de abaixo comando:  

           #sudo su -

    Transfira e instale o pacote do MySQL:

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Passo 2: Gerir o serviço de MySQL em execução

    (um) verificar o estado do servidor do MySQL:

           #[root@mysqlnode ~]# rcmysql status

    (b) verificação se a porta predefinida do servidor do MySQL:

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) inicie o servidor do MySQL:

           #[root@mysqlnode ~]# rcmysql start

    (d) pare o servidor do MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL conjunto para iniciar durante o arranque-up de sistema:

           #[root@mysqlnode ~]# insserv mysql

###<a name="next-step"></a>Passo seguinte
Localize mais a utilização e informações relativas ao MySQL [aqui](https://www.mysql.com/).
