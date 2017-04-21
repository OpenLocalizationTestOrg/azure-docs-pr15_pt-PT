Siga estes passos para instalar e executar MongoDB num computador a executar o CentOS Linux virtual.

> [AZURE.WARNING] Funcionalidades de segurança de MongoDB, como a autenticação e ligação do endereço IP, não estão ativadas por predefinição. Funcionalidades de segurança devem estar ativadas antes de implementar MongoDB para ambiente de produção.  Para mais informações, consulte [segurança e autenticação](http://www.mongodb.org/display/DOCS/Security+and+Authentication) .

1. Configure o sistema de gestão de pacote (YUM) para que possa instalar MongoDB. Crie um ficheiro de */etc/yum.repos.d/10gen.repo* para reter informações sobre o repositório e adicione o seguinte:

        [10gen]
        name=10gen Repository
        baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
        gpgcheck=0
        enabled=1

2. Guarde o ficheiro repo e, em seguida, execute o seguinte comando para atualizar a base de dados do pacote local:

        $ sudo yum update

3. Para instalar o pacote, execute o seguinte comando para instalar a versão mais recente de estável do MongoDB e as ferramentas de associado:

        $ sudo yum install mongo-10gen mongo-10gen-server

    Aguarde enquanto MongoDB transfere e instala.

4. Crie um diretório de dados. Por predefinição MongoDB armazena os dados no diretório */data/db* , mas terá de criar esse diretório. Para criá-lo, execute:

        $ sudo mkdir -p /srv/datadrive/data
        $ sudo chown `id -u` /srv/datadrive/data

    Para obter mais informações sobre como instalar MongoDB Linux, consulte o artigo [Guia de introdução Unix][QuickstartUnix].

5. Para iniciar a base de dados, execute:

        $ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

    Todas as mensagens de registo ser serão direcionadas para o ficheiro */srv/datadrive/data/mongod.log* como MongoDB servidor inícios e preallocates ficheiros de diário. Poderá demorar vários minutos MongoDB para pré-alocar os ficheiros de diário e começar a escutar para ligações.

6. Para começar a shell de administrativa MongoDB, abra uma janela em separado do SSH ou betumes e executar:

        $ mongo
        > db.foo.save ( { a:1 } )
        > db.foo.find()
        { _id : ..., a : 1 }
        > show dbs  
        ...
        > show collections  
        ...  
        > help  

    A base de dados é criada pelo inserir.

7. Quando estiver instalada MongoDB tem de configurar um ponto final para que possa ser acedida remotamente MongoDB. No Portal de gestão, clique em **máquinas virtuais**, em seguida, clique no nome da sua nova máquina virtual, em seguida, clique em **pontos finais**.
    
    ![Pontos finais][Image7]

8. Clique em **Adicionar ponto final** na parte inferior da página.
    
    ![Pontos finais][Image8]

9. Adicione um ponto final com as seguintes definições:

 - **Nome**: Mongo
 - **Protocolo**: TCP
 - **Porta de público**: 27017
 - **Porta de privado**: 27017
 
 Isto irá permitir MongoDB ser acedida remotamente.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png
