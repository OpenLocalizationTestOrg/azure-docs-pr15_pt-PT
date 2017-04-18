<properties
    pageTitle="Configurar Oracle GoldenGate numa VMs | Microsoft Azure"
    description="Visualizar passo a passo um tutorial para configurar e implementar Oracle GoldenGate no VMs de servidor do Windows Azure para a recuperação de disponibilidade e falhas alta."
    services="virtual-machines-windows"
    authors="rickstercdn"
    manager="timlt"
    documentationCenter=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/06/2016"
    ms.author="rclaus" />


#<a name="configuring-oracle-goldengate-for-azure"></a>Configurar Oracle GoldenGate para Azure


Neste tutorial demonstra como configurar Oracle GoldenGate para ambiente de máquinas virtuais do Azure para elevada disponibilidade e recuperação de falhas. O tutorial foca-se em [bidirecional replicação](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) de bases de dados Oracle não RAC e requer que ambos os sites estão ativos.

Oracle GoldenGate suporta a distribuição dos dados e integração de dados. Permite-lhe configurar uma distribuição de dados e a solução de sincronização de dados através da configuração de replicação Oracle Oracle e fornece uma solução flexível elevada disponibilidade. Oracle GoldenGate suplementos atento de dados Oracle com as suas capacidades de replicação para ativar as atualizações de distribuição e o tempo de inatividade zero de informações de toda a empresa e migrações. Para obter informações detalhadas, consulte o artigo [Utilizar GoldenGate Oracle com atento de dados Oracle](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate contém os seguintes componentes principais: extrair dados bomba, Replicat, pistas ou extrair ficheiros, pontos de verificação, Gestor e recolectores. Para que a replicação bidirecional entre dois locais, tem de criar e comece a todos os componentes em ambos os sites. Para obter informações detalhadas sobre Oracle GoldenGate arquitetura, consulte o artigo [Guia de GoldenGate Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

Neste tutorial assume que já tem teórico e prático conhecimentos em conceitos de disponibilidade de elevada de base de dados Oracle e recuperação de falhas, bem como [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Para mais informações, consulte o [web site da Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

Além disso, o tutorial assume que já implementou os pré-requisitos seguintes:

- A secção elevada disponibilidade e considerações de recuperação de falhas no tópico das [imagens de máquina de Virtual Oracle - considerações diversos](virtual-machines-windows-classic-oracle-considerations.md) já ter revisto. Tenha em atenção que Azure suporta instâncias de base de dados Oracle autónomo, mas não Oracle Real aplicação Clusters (Oracle RAC) atualmente.

- Transferir o software Oracle GoldenGate partir do web site [Transferências Oracle](http://www.oracle.com/us/downloads/index.html) . Selecionar o software produto Pack Oracle fusão intermédio – integração de dados. Em seguida, selecionou Oracle GoldenGate no Oracle v11.2.1 Media Pack para o Microsoft Windows x64 (64 bits) para uma base de dados do Oracle 11g. Em seguida, transfira Oracle GoldenGate V11.2.1.0.3 para Oracle 11g de 64 bits no Windows 2008 (64 bits).

- Criou duas máquinas virtuais (VMs) no Azure utilizando Oracle Enterprise Edition no Windows Server. Certifique-se de que as máquinas virtuais são no [serviço na nuvem mesmo](virtual-machines-linux-load-balance.md) e na mesma [Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para se certificar de que podem aceder entre si através o endereço IP privado persistente.

- Que definiu os nomes de Máquina Virtual como "MachineGG1" para um Site e "MachineGG2" para o Site B no portal do Azure clássico.

- Que criou bases de dados de teste "TestGG1" no local A e "TestGG2" no local B.

- Inicie sessão como um membro do grupo de administradores ou um membro do grupo **ORA_DBA** no seu servidor do Windows.

Neste tutorial, irá:

1. Base de dados no local A e B de Site do programa de configuração  

    1. Executar o carregamento dos dados inicial

2. Preparar o Site A e B de Site para a replicação de bases de dados

3. Criar todos os objetos necessários para suportar DDL replicação

4. Configurar o Gestor de GoldenGate no locais A e B

5. Processos criar grupo extrair e aumente de dados no local A e B de Site

    1. Criar processos extrair e aumente de dados no local A

    2. Criar uma tabela de ponto de verificação GoldenGate no Site B

    3. Adicionar REPLICAT no Site B

    4. Criar processos extrair e aumente de dados no Site B

    5. Criar uma tabela de ponto de verificação GoldenGate num Site A

    6. Adicionar REPLICAT no local A

    7. Adicionar trandata no local A e B de Site

    8. Iniciar processos extrair e aumente de dados num Site de respostas

    9. Iniciar processos extrair e aumente de dados no Site B

    10. Iniciar processo REPLICAT num Site de respostas

    11. Iniciar processo de REPLICAT no Site B

6. Verifique se o processo de replicação bidirecional

>[AZURE.IMPORTANT] Neste tutorial foi configuração e testado contra a configuração de software seguintes:
>
>|                        | **Uma base de dados do site**              | **Base de dados local B**              |
>|------------------------|----------------------------------|----------------------------------|
>| **Lançamento de Oracle**     | Oracle11g lançamento 2 – (11.2.0.1) | Oracle11g lançamento 2 – (11.2.0.1) |
>| **Nome do computador**       | MachineGG1                       | MachineGG2                       |
>| **Sistema operativo**   | Windows 2008 R2                  | Windows 2008 R2                  |
>| **Oracle SID**         | TESTGG1                          | TESTGG2                          |
>| **Esquema de replicação** | SCOTT                            | SCOTT                            |

Para as edições subsequentes de base de dados Oracle e Oracle GoldenGate, poderá haver algumas alterações adicionais que precisa para implementar. Para as informações específicas versão mais recentes, consulte a documentação de [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) e [Base de dados Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) Oracle web site. Por exemplo, para uma base de dados de origem do lançamento 11.2.0.4 e posterior, a captura de DDL é executada pelo servidor logmining modo assíncrono e requer sem accionadores especiais, tabelas ou outros objetos de base de dados para ser instalado. Atualiza os GoldenGate do Oracle podem ser efectuados sem parar aplicações de utilizador. A utilização de um accionador DDL e objetos de suporte é necessária quando extrair está no modo integrado com uma Oracle 11g origem base de dados é anterior à versão 11.2.0.4. Para uma orientação detalhada, consulte [instalar e configurar GoldenGate Oracle para base de dados Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##<a name="1-setup-database-on-site-a-and-site-b"></a>1. a configuração da base de dados no local A e B de Site
Esta secção explica como efetuar as pré-requisitos de base de dados no local A e b do Site. Deve efetuar todos os passos desta secção em ambos os sites: local A e b do Site.

Em primeiro lugar, remoto ambiente de trabalho para local A e B do Site através do portal do Azure clássico. Abrir uma linha de comandos do Windows e crie um diretório para ficheiros de configuração de Oracle GoldenGate:

    mkdir C:\OracleGG

Em seguida, deszipar e instale o software de Oracle GoldenGate nesta pasta. Após neste passo, pode iniciar o GoldenGate Software comando interpretação (GGSCI) executando o seguinte comando:

    C:\OracleGG\.\ggsci

Pode utilizar [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) para executar vários comandos que configurar, controlar e monitorizar Oracle GoldenGate.

Em seguida, execute o seguinte comando para criar todas as sub-pastas de a partir do pacote de instalação:

    GGSCI (Hostname) 1> CREATE SUBDIRS

Execute o seguinte comando para sair da linha de comandos GGSCI:

    GGSCI (Hostname) 1> EXIT

Em seguida, tem de criar um utilizador de base de dados, que será utilizado por processos Oracle GoldenGate Gestor, extrair e a replicação. Tenha em atenção que pode criar utilizadores individuais para cada processo ou configurar apenas um utilizador comuns. Neste tutorial, recomendamos criar um utilizador chama-se como ggate. Em seguida, podemos conceder esse utilizador os privilégios necessários. Tenha em atenção que deve efetuar as seguintes operações do Site A e b do Site.

Abrir o SQL\*Plus janela de comando no local A e B de Site com privilégios de administrador da base de dados com **SYSDBA**, tal como:

    Enter username: / as sysdba

E executar:

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

Em seguida, localize a Inicialização\<DatabaseSID\>. ORA de ficheiros na pasta % ORACLE\_base %\\pasta no local A e B de Site de base de dados e acrescentar os seguintes parâmetros de base de dados a INITTEST.ora:

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

Para obter uma lista completa de todos os comandos de Oracle GoldenGate GGSCI, consulte o artigo [referência para Oracle GoldenGate para Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### <a name="perform-initial-data-load"></a>Executar o carregamento dos dados inicial

Pode executar o carregamento de dados inicial da base de dados através de vários métodos. Por exemplo, pode utilizar o [Oracle GoldenGate direta carga](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) ou utilitários de exportação e importação normais para exportar os dados da tabela a partir do Site A para b do Site.

Para demonstrar o processo de replicação Oracle GoldenGate, este tutorial demonstra criar uma tabela no local A e B do site através dos comandos seguintes.

Primeiro, abra o SQL\*Plus janela de comando e execute o seguinte comando para criar uma tabela do inventário em bases de dados do Site A e B de Site:

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

Em seguida, adicione um constrangimento à tabela recentemente criada no Site de respostas e bases de dados do Site B:

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Conceder, em seguida, todos os privilégios sobre a nova tabela inventário ggate o utilizador do Site A e b Site:

    grant all on scott.inventory to ggate;

Em seguida, criar e ativar um acionador de base de dados, INVENTORY_CDR_TRG, na tabela para se certificar de que todas as operações para a nova tabela são registadas se o utilizador não é ggate recentemente criado. Executar esta operação do Site A e b do Site.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


##<a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2. Preparar o Site A e B de Site para a replicação de bases de dados
Esta secção explica como preparar o Site A e B de Site para a replicação de bases de dados. Deve efetuar todos os passos desta secção em ambos os sites: local A e b do Site.

Em primeiro lugar, remoto ambiente de trabalho para local A e B do Site através do portal do Azure clássico. Mudar da base de dados para o modo de archivelog utilizando o SQL * Plus janela de comando:

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


Em seguida, ativar mínimas [suplementar registo](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) da seguinte forma:

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Em seguida, prepare a base de dados para suportar a replicação DDL (linguagem de definição de dados):

    SQL> alter system set recyclebin=off scope=spfile;

Em seguida, encerrar e reiniciar a base de dados:

    sql>shutdown immediate
    sql>startup


##<a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3. criar todos os objetos necessários para suportar DDL replicação
Esta secção apresenta os scripts que precisa para utilizar para criar todos os objetos necessários para suportar a replicação DDL. Que precisa para executar os scripts especificados nesta secção no local A e b do Site.

Abra o uma linha de comandos do Windows e navegue para a pasta Oracle GoldenGate, tal como c:\\OracleGG. Iniciar SQL\*Plus linha de comandos com privilégios de administrador da base de dados, tal como utilizar o **SYSDBA** no local A e b do Site.

Em seguida, execute os seguintes scripts de:

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Ferramenta de GoldenGate Oracle requer um início de sessão de nível de tabela para obter suporte DDL (linguagem de definição de dados). Esse é porquê, ativar registo ao nível da tabela utilizando o comando Adicionar TRANDATA suplementar. Abrir uma janela de interpretação Oracle GoldenGate comando, início de sessão para a base de dados e, em seguida, execute o comando Adicionar TRANDATA:

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

##<a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4. configurar o Gestor de GoldenGate no locais A e B
O Gestor de GoldenGate Oracle executa um número de funções como começar os outros processos de GoldenGate, gestão de ficheiros de registo de fiável e relatórios.

Tem de configurar o processo do Gestor de GoldenGate Oracle no local A e b do Site. Para fazer isto, execute os seguintes passos no local A e b do Site.

Janelas abertas comando janela e iniciar a interpretação comando Oracle GoldenGate:

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Inicia a sessão GGSCI para uma base de dados para que possa ser executado comandos que afetam a base de dados:

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Apresenta o estado e de atraso (onde relevantes) para todos os processos de Gestor, extrair e Replicat num sistema:

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Abra o ficheiro de parâmetro com o comando Editar parâmetros e, em seguida, acrescentar as seguintes informações:

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Apresenta o estado e de atraso (onde relevantes) para todos os processos de Gestor, extrair e Replicat num sistema:

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Inicia a sessão GGSCI para uma base de dados para que possa ser executado comandos que afetam a base de dados:

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Inicie o processo de gestor:

    GGSCI (HostName) 48> start manager
    Manager started.

##<a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5. Crie extrair processos de grupo e aumente de dados no local A e B de Site

###<a name="create-extract-and-data-pump-processes-on-site-a"></a>Criar processos extrair e aumente de dados no local A

Tem de criar os processos extrair e aumente de dados no local A e Site B. ambiente de trabalho remoto local A e B do Site através do portal do Azure clássico. Abrir uma janela de interpretação GGSCI comando. Execute os seguintes comandos na Site:

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Abra o ficheiro de parâmetro com o comando Editar parâmetros e, em seguida, acrescentar as seguintes informações: GGSCI (MachineGG1) 18 > Editar parâmetros ext1 EXTRAIR ext1 ID de utilizador ggate, palavra-passe ggate scott.inventory de tabela de ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER, GETBEFORECOLS (Sucessivamente ATUALIZAÇÃO KEYINCLUDING (prod_category, qty_in_stock, last_dml), Sucessivamente eliminar KEYINCLUDING (prod_category, qty_in_stock, last_dml));

Abra o ficheiro de parâmetro com o comando Editar parâmetros e, em seguida, acrescentar as seguintes informações:

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-b"></a>Criar uma tabela de ponto de verificação GoldenGate no Site B

A seguir, tem de adicionar uma tabela de ponto de verificação no local B. Para fazer isto, terá de abrir uma janela de interpretação comandos GoldenGate e executar:

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

E, em seguida, adicione a tabela de ponto de verificação à base de dados, onde ggate é o proprietário:

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Adicione o nome da tabela de ponto de verificação para o ficheiro GLOBALS no servidor de destino, que é o Site B Neste passo. Editar o ficheiro GLOBALS b Site:

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Acrescente, em seguida, o parâmetro CHECKPOINTTABLE ao ficheiro GLOBALS existente:

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Como um passo final, guarde e feche o ficheiro de parâmetro GLOBALS.


###<a name="add-replicat-on-site-b"></a>Adicionar REPLICAT no Site B
Esta secção descreve como adicionar um processo REPLICAT "REP2" no local B.

Utilizar o comando Adicionar REPLICAT para criar um grupo de Replicat no b Site:

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Abra o ficheiro de parâmetro com o comando Editar parâmetros e, em seguida, acrescentar as seguintes informações:

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

###<a name="create-extract-and-data-pump-processes-on-site-b"></a>Criar processos extrair e aumente de dados no Site B

Esta secção descreve como criar um novo processo extrair "EXT2" e um processo de bomba dados novos "DPUMP2" no b Site:

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Abra o ficheiro de parâmetro com o comando Editar parâmetros e, em seguida, acrescentar as seguintes informações:

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Abra o ficheiro de parâmetro com o comando Editar parâmetros e, em seguida, acrescentar as seguintes informações:

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-a"></a>Criar uma tabela de ponto de verificação GoldenGate num Site A

Abrir uma janela do Oracle GoldenGate comando interpretação e criar uma tabela de ponto de verificação:

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

Também precisa de adicionar o nome da tabela de ponto de verificação para o ficheiro GLOBALS no Site respostas.

Abrir uma janela de interpretação Oracle GoldenGate comando e editar o ficheiro GLOBALS no r Site:

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Guarde e feche o ficheiro de parâmetro GLOBALS.

###<a name="add-replicat-on-site-a"></a>Adicionar REPLICAT no local A

Esta secção descreve como adicionar um processo REPLICAT "REP1" no Site respostas.

O comando seguinte cria uma rep1 de grupo Replicat com o nome de um fiável e o checkpointtable associado.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Abra o ficheiro de parâmetro com o comando Editar parâmetros e, em seguida, acrescentar as seguintes informações:

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>Adicionar trandata no local A e B de Site

Ative o registo de suplementar ao nível da tabela utilizando o comando Adicionar TRANDATA. Abrir uma janela de interpretação Oracle GoldenGate comando, início de sessão para a base de dados e, em seguida, execute o comando Adicionar TRANDATA.

Ambiente de trabalho remoto para MachineGG1, abra o intérprete de comando Oracle GoldenGate e executar:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Ambiente de trabalho remoto para MachineGG2, abra o intérprete de comando Oracle GoldenGate e executar:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Apresentar informações sobre o estado da tabela ao nível suplementar registo:

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="add-trandata-on-site-a-and-site-b"></a>Adicionar trandata no local A e B de Site

Ative o registo de suplementar ao nível da tabela utilizando o comando Adicionar TRANDATA. Abrir uma janela de interpretação Oracle GoldenGate comando, início de sessão para a base de dados e, em seguida, execute o comando Adicionar TRANDATA.

Ambiente de trabalho remoto para MachineGG1, abra o Oracle GoldenGate interpretação de comando e executar:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Ambiente de trabalho remoto para MachineGG2, abra o Oracle GoldenGate interpretação de comando e executar:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="start-extract-and-data-pump-processes-on-site-a"></a>Iniciar processos extrair e aumente de dados num Site de respostas

Iniciar o ext1 de processo extrair na r Site:

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Iniciar o dados bomba processo dpump1 na Site:

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Apresentar informações sobre o ext1 de grupo extrair: GGSCI (MachineGG1) 32 > info extrair ext1 EXTRAIR EXT1 última iniciado 2013-11-25 08:03 estado a executar o ponto de verificação desfasamento 00:00:00 (atualizado 00:00:02 há) registo leitura ponto de verificação Oracle Refazer registos 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Apresenta o estado e de atraso (onde relevantes) para todos os processos de Gestor, extrair e Replicat num sistema:

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###<a name="start-extract-and-data-pump-processes-on-site-b"></a>Iniciar processos extrair e aumente de dados no Site B

Iniciar o ext2 de processo extrair na b Site:

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Iniciar o dados bomba processo dpump2 na b Site:

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Apresenta o estado e de atraso (onde relevantes) para todos os processos de Gestor, extrair e Replicat num sistema:

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>Iniciar processo REPLICAT num Site de respostas

Esta secção descreve como iniciar o processo REPLICAT "REP1" no Site respostas.

Iniciar o processo de Replicat no r Site:

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Apresenta o estado de um grupo de Replicat:

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

###<a name="start-replicat-process-on-site-b"></a>Iniciar processo de REPLICAT no Site B

Esta secção descreve como iniciar o processo REPLICAT "REP2" no local B.

Iniciar o processo de Replicat no b Site:

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Apresenta o estado de um grupo de Replicat:

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

##<a name="6-verify-the-bi-directional-replication-process"></a>6. Verifique se o processo de replicação bidirecional

Para verificar a configuração de Oracle GoldenGate, inserir uma linha para a base de dados no Site respostas. ambiente de trabalho remoto para Site respostas. abrir o SQL * Plus janela de comandos e executar: SQL > Selecionar nome a partir da base de dados do v$;

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

Em seguida, verifique se nessa linha é replicada no local B. Para fazer isto, ambiente de trabalho remoto para Site B. abrir uma cópia de janela SQL Plus e executar:

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Inserir um novo registo ao b Site:

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Ambiente de trabalho remoto ao Site A e verifique se a replicação foi redirecionado local:

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

##<a name="additional-resources"></a>Recursos adicionais
[Imagens de Máquina Virtual Oracle para Azure](virtual-machines-linux-classic-oracle-images.md)
