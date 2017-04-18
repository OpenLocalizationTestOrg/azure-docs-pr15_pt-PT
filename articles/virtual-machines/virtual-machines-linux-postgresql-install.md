<properties
    pageTitle="Configurar o PostgreSQL numa VM Linux | Microsoft Azure"
    description="Saiba como instalar e configurar o PostgreSQL numa máquina virtual Linux no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


# <a name="install-and-configure-postgresql-on-azure"></a>Instalar e configurar o PostgreSQL no Azure

PostgreSQL é uma avançadas abrir origem base de dados semelhante às Oracle e DB2. Inclui funcionalidades pronto para enterprise como conformidade ÁCIDA completo, processamento transaccional fiável e controlo de versão com várias simultaneidade. Também suporta normas como ANSI SQL e SQL/MED (incluindo wrappers de dados externa para Oracle, MySQL, MongoDB e muitas outras). É vivamente extensible com o suporte para idiomas procedimentos mais de 12, índices GIN e GiST, suporte de dados espacial e várias funcionalidades de NoSQL gosto para JSON ou aplicações baseadas em valor de chave.

Neste artigo, irá obter informações sobre como instalar e configurar o PostgreSQL numa máquina virtual Azure executar Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="install-postgresql"></a>Instale o PostgreSQL

> [AZURE.NOTE] Já tem de ter uma máquina virtual Azure a executar Linux para poder concluir este tutorial. Para criar e configurar uma VM Linux antes de continuar, consulte o artigo o [Azure Linux VM tutorial](virtual-machines-linux-quick-create-cli.md).

Neste caso, utilize porta 1999 como a porta PostgreSQL.  

Ligar para o Linux VM que criou através do betumes. Se esta for a primeira vez que está a utilizar um VM de Linux Azure, veja [como utilizar SSH com Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md) para saber como utilizar betumes para ligar a uma VM Linux.

1. Execute o seguinte comando para mudar para a raiz (admin):

        # sudo su -

2. Algumas distribuições tem dependências que tem de instalar antes de instalar o PostgreSQL. Verificar a existência de seu distro nesta lista e executar o comando adequado:

    - Linux base vermelho é:

            # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

    - Debian base Linux:

            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

    - SUSE Linux:

            # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Transferir o PostgreSQL para o diretório de raiz e, em seguida, deszipar o pacote de:

        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

        # tar jxvf  postgresql-9.3.5.tar.bz2

    O que precede é um exemplo. Pode encontrar o endereço de transferir mais detalhado no [índice de /pub/origem /](https://ftp.postgresql.org/pub/source/).

4. Para começar a criar, execute estes comandos:

        # cd postgresql-9.3.5

        # ./configure --prefix=/opt/postgresql-9.3.5

5. Se pretender criar tudo o que pode ser criado, incluindo a documentação (HTML e homem páginas) e os módulos adicionais (contribuir), execute o seguinte comando em vez disso:

        # gmake install-world

    Deverá receber a seguinte mensagem de confirmação:

        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurar o PostgreSQL

1. (Opcional) Crie uma ligação simbólica para abreviar a referência PostgreSQL não inclui o número da versão:

        # ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Crie um diretório para a base de dados:

        # mkdir -p /opt/pgsql_data

3. Criar um utilizador que não sejam raiz e modificar o perfil desse utilizador. Em seguida, mude para a este novo utilizador (denominado *postgres* no nosso exemplo):

        # useradd postgres

        # chown -R postgres.postgres /opt/pgsql_data

        # su - postgres

   > [AZURE.NOTE] Por motivos de segurança, PostgreSQL utiliza um utilizador que não sejam raiz para iniciar, comece ou encerrar a base de dados.


4. Edite o ficheiro *bash_profile* introduzindo os comandos abaixo. Para o fim do ficheiro *bash_profile* serão adicionadas destas linhas:

        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF

5. Execute o ficheiro *bash_profile* :

        $ source .bash_profile

6. Valide a sua instalação utilizando o seguinte comando:

        $ which psql

    Se a sua instalação for bem sucedida, irá ver a seguinte resposta:

        /opt/pgsql/bin/psql

7. Também pode verificar a versão do PostgreSQL:

        $ psql -V

8. Iniciar a base de dados:

        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

    Deverá receber o seguinte resultado:

![imagem](./media/virtual-machines-linux-postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurar o PostgreSQL

<!--    [postgres@ test ~]$ exit -->

Execute os seguintes comandos:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modificar duas variáveis no ficheiro /etc/init.d/postgresql. O prefixo está definido para o caminho de instalação do PostgreSQL: **/opt/pgsql**. PGDATA está definido para o caminho de armazenamento de dados do PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![imagem](./media/virtual-machines-linux-postgresql-install/no2.png)

Altere o ficheiro para torná-lo executável:

    # chmod +x /etc/init.d/postgresql

Inicie o PostgreSQL:

    # /etc/init.d/postgresql start

Verifique se o ponto final do PostgreSQL está:

    # netstat -tunlp|grep 1999

Deverá visualizar o seguinte resultado:

![imagem](./media/virtual-machines-linux-postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Ligar à base de dados Postgres

Mude para o utilizador postgres mais uma vez:

    # su - postgres

Crie uma base de dados Postgres:

    $ createdb events

Ligar à base de dados eventos que acabou de criar:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Criar e eliminar uma tabela de Postgres

Agora que tem ligados à base de dados, pode criar tabelas na mesma.

Por exemplo, crie uma nova tabela Postgres de exemplo, utilizando o seguinte comando:

    CREATE TABLE potluck (name VARCHAR(20), food VARCHAR(30),   confirmed CHAR(1), signup_date DATE);

Configurou uma tabela de quatro colunas com as restrições e os seguintes nomes de coluna:

1. A coluna "nome" foi limitada pelo comando VARCHAR para ser 20 em carateres de comprimento.
2. A coluna "alimentação" indica o item de alimentação moldados cada pessoa. VARCHAR limita este texto para ser em 30 caracteres.
3. A coluna "confirmada" registos se a pessoa tem RSVP'd para o convívio. Os valores aceitáveis são "Y" e "N".
4. A coluna de "data" mostra quando inscreveu para o evento. Postgres requer que sejam escritos datas como aaaa-mm-dd.

Deverá visualizar o seguinte se a sua tabela foi criada com êxito:

![imagem](./media/virtual-machines-linux-postgresql-install/no4.png)

Também pode verificar a estrutura da tabela utilizando o seguinte comando:

![imagem](./media/virtual-machines-linux-postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Adicionar dados a uma tabela

Em primeiro lugar, inserir informações para uma linha:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Deverá visualizar este resultado:

![imagem](./media/virtual-machines-linux-postgresql-install/no6.png)

Pode adicionar mais pessoas algumas à tabela também. Aqui estão algumas opções ou pode criar o seu próprio:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Mostrar as tabelas

Utilize o seguinte comando para mostrar uma tabela:

    select * from potluck;

O resultado é:

![imagem](./media/virtual-machines-linux-postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Eliminar dados numa tabela

Utilize o seguinte comando para eliminar dados numa tabela:

    delete from potluck where name=’John’;

Este procedimento elimina toda a informação na linha "João". O resultado é:

![imagem](./media/virtual-machines-linux-postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Atualizar dados numa tabela

Utilize o seguinte comando para atualizar dados numa tabela. Para este um Sandy confirmou que ela é participar, pelo que mudam respetivos RSVP de "N" para "Y":

    UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##<a name="get-more-information-about-postgresql"></a>Obter mais informações sobre PostgreSQL
Agora que concluiu a instalação do PostgreSQL numa VM de Linux Azure, que possa desfrutar de utilizá-lo no Azure. Para saber mais sobre PostgreSQL, visite o [Web site do PostgreSQL](http://www.postgresql.org/).
