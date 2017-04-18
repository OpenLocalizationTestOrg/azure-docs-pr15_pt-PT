<properties
    pageTitle="Configurar atento de dados Oracle numa VMs | Microsoft Azure"
    description="Visualizar passo a passo um tutorial para configurar e implementar atento de dados Oracle em máquinas virtuais Azure para a recuperação de disponibilidade e falhas alta."
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

#<a name="configuring-oracle-data-guard-for-azure"></a>Configurar atento de dados Oracle para Azure


Neste tutorial demonstra como configurar e implementar atento de dados Oracle no ambiente de máquinas virtuais do Azure para elevada disponibilidade e recuperação de falhas. O tutorial foca-se em unidirecional replicação de bases de dados Oracle não RAC.

Atento de dados Oracle suporta a proteção de dados e recuperação de falhas de base de dados Oracle. É uma simples, alto desempenho, solução queda livre para recuperação de falhas, proteção de dados e elevada disponibilidade para a base de dados Oracle inteira.

Neste tutorial assume que já tem conhecimento teórico e prático sobre os conceitos de disponibilidade de alto de base de dados do Oracle e recuperação de falhas. Para informações, consulte o [web site da Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) e também [os conceitos de atento de dados Oracle e guia de administração](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm).

Além disso, o tutorial assume que já implementou os pré-requisitos seguintes:

- A secção elevada disponibilidade e considerações de recuperação de falhas no tópico das [imagens de máquina de Virtual Oracle - considerações diversos](virtual-machines-windows-classic-oracle-considerations.md) já ter revisto. Azure suporta instâncias de base de dados Oracle autónomo, mas não Oracle Real aplicação Clusters (Oracle RAC) atualmente.


- Criou duas máquinas virtuais (VMs) no Azure utilizando a mesma plataforma fornecida imagem Oracle Enterprise Edition. Certifique-se as máquinas virtuais no [serviço na nuvem mesmo](virtual-machines-windows-load-balance.md) e na mesma rede Virtual para se certificar de que podem aceder entre si através o endereço IP privado persistente. Para além disso, recomenda-se para colocar os VMs no mesmo [conjunto de disponibilidade](virtual-machines-windows-manage-availability.md) para permitir que o Azure colocá-los para domínios falhas separadas e actualizar domínios. Atento de dados Oracle só está disponível com Enterprise Edition da base de dados Oracle. Cada computador tem de ter pelo menos de 2 GB de memória e 5 GB de espaço em disco. Para obter informações mais atualizadas a plataforma fornecidos tamanhos VM, consulte o artigo [Tamanhos de Máquina Virtual para Azure](virtual-machines-windows-sizes.md). Se precisar de volume do disco adicionais para o seu VMs, pode anexar discos adicionais. Para obter informações, consulte o artigo [como anexar um disco de dados para uma Máquina Virtual](virtual-machines-windows-classic-attach-disk.md).



- Que definiu os nomes de Máquina Virtual como "MÁQUINA1" para a VM e "MÁQUINA2" para a suspensão VM principal no portal do Azure clássico.

- Que definiu a variável de ambiente de **ORACLE_HOME** para apontarem para o mesmo caminho de instalação de raiz de oracle nos primário e suspensão máquinas virtuais, tais como `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Inicie sessão como um membro do grupo de **administradores** ou um membro do grupo **ORA_DBA** no seu servidor do Windows.

Neste tutorial, irá:

Implementar o ambiente de base de dados em modo de espera física

1. Criar uma base de dados principal

2. Preparar a base de dados principal para a criação de base de dados em espera

    1. Ativar o registo de forçada

    2. Criar um ficheiro de palavra-passe

    3. Configurar um registo da suspensão Refazer

    4. Activar o arquivo

    5. Definir parâmetros de inicialização da base de dados principal

Criar uma base de dados em modo de espera física

1. Preparar um ficheiro de parâmetro de inicialização para base de dados em espera

2. Configurar o escuta e tnsnames para suportar a base de dados em máquinas principais e em espera

    1. Configurar listener.ora em ambos os servidores para colocar em espera entradas para ambas as bases de dados

    2. Para colocar em espera entradas para bases de dados principais e em espera, configure Tnsnames em máquinas virtuais principal e em espera. 

    3. Iniciar a escuta e marque tnsping em ambas as máquinas virtuais para ambos os serviços.

3. A instância suspensão no Estado nomount de arranque

4. Utilize RMAN para clonar a base de dados e para criar uma base de dados em espera

5. Iniciar a base de dados em modo de espera físico no modo de recuperação geridos

6. Verifique se a base de dados em modo de espera física

> [AZURE.IMPORTANT] Neste tutorial foi configurar e testar contra a configuração do hardware e software seguinte:
>
>|                      | **Base de dados principal**                      | **Base de dados em espera**                      |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Lançamento de Oracle**   | Oracle11g Enterprise lançamento (11.2.0.4.0) | Oracle11g Enterprise lançamento (11.2.0.4.0) |
>| **Nome do computador**     | MÁQUINA1                                  | MÁQUINA2                                  |
>| **Sistema operativo** | Windows 2008 R2                           | Windows 2008 R2                           |
>| **Oracle SID**       | TESTE                                      | TESTE\_STBY                                |
>| **Memória**           | Mínimo 2 GB                                  | Mínimo 2 GB                                  |
>| **Espaço em disco**       | Mínimo 5 GB                                  | Mínimo 5 GB                                  |

Para as edições subsequentes de base de dados Oracle e atento de dados Oracle, poderá haver algumas alterações adicionais que precisa para implementar. Para informações de específicas da versão mais recentes, consulte a documentação de [Dados atento](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) e [Base de dados Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) Oracle web site.

##<a name="implement-the-physical-standby-database-environment"></a>Implementar o ambiente de base de dados em modo de espera física
### <a name="1-create-a-primary-database"></a>1. a criar uma base de dados principal

- Crie uma base de dados principal "Testar" na máquina Virtual principal. Para obter informações, consulte o artigo criar e configurar uma base de dados Oracle.
- Para ver o nome da base de dados, ligar à sua base de dados como o utilizador sobre com a função de SYSDBA no SQL * mais comandos e executar a seguinte instrução:

        SQL> select name from v$database;

        The result will display like the following:

        NAME
        ---------
        TEST
- Em seguida, os nomes dos ficheiros da base de dados a partir da vista de sistema dba_data_files de consulta:

        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2. Preparar-se a base de dados principal para a criação de base de dados em espera

Antes de criar uma base de dados em espera, é recomendável que garantir que a base de dados principal está configurado corretamente. Segue-se uma lista dos passos que precisa para executar:

1. Ativar o registo de forçada
2. Criar um ficheiro de palavra-passe
3. Configurar um registo da suspensão Refazer
4. Activar o arquivo
5. Definir parâmetros de inicialização da base de dados principal

#### <a name="enable-forced-logging"></a>Ativar o registo de forçada

Para implementar a uma base de dados do modo de espera, precisamos de ativar a opção 'Forçada registo' na base de dados principal. Esta opção garante que, mesmo se uma operação de 'nologging' estiver concluída, forçar registo terá precedência e todas as operações sessão iniciadas nos registos de Refazer. Por conseguinte, podemos Certifique-se de que tudo na base de dados principal tem sessão iniciado e a replicação para a suspensão inclui todas as operações na base de dados principal. Para ativar o registo de forçar, execute a declaração de base de dados alter:

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>Criar um ficheiro de palavra-passe

Para poder enviar e aplicar registos arquivados a partir do servidor principal para o servidor em espera, a palavra-passe sobre deve ser idêntica em servidores principais e em espera. Razão pela qual criar um ficheiro de palavra-passe da base de dados principal e copiá-lo para o servidor em espera.

>[AZURE.IMPORTANT] Ao utilizar a base de dados Oracle 12c, existe um novo utilizador, **SYSDG**, que pode utilizar para administrar atento de dados Oracle. Para mais informações, consulte o artigo [alterações na base de dados Oracle 12 c lançamento](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404).

Além disso, certifique-se de que o ORACLE\_ambiente base já está definido no MÁQUINA1. Caso não esteja, defini-la como uma variável de ambiente utilizando a caixa de diálogo de variáveis de ambiente. Para aceder a esta caixa de diálogo, inicie o utilitário de **sistema** fazendo duplo clique no ícone de sistema do **Painel de controlo**; em seguida, clique no separador **Avançadas** e selecione **Variáveis de ambiente**. Para definir as variáveis de ambiente, clique no botão **Novo** em **Variáveis do sistema**. Depois de configurar as variáveis de ambiente, feche a linha de comandos existente do Windows e abra um novo.

Execute a seguinte instrução para mudar para o Oracle\_diretório de base, tal como c:\\OracleDatabase\\produto\\11.2.0\\dbhome\_1\\base de dados.

    cd %ORACLE_HOME%\database

Em seguida, crie um ficheiro de palavra-passe com o utilitário de criação de ficheiro de palavra-passe, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). No mesmo Windows linha de comandos no MÁQUINA1, execute o seguinte comando ao definir o valor de palavra-passe como a palavra-passe **sobre**:

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Este comando cria um ficheiro de palavra-passe, denominado como PWDTEST.ora, no ORACLE\_casa\\diretório de base de dados. Deverá copiar este ficheiro para % ORACLE\_base %\\de base de dados diretório no MÁQUINA2 manualmente.

#### <a name="configure-a-standby-redo-log"></a>Configurar um registo da suspensão Refazer

Em seguida, tem de configurar um registo Refazer em espera para que a página principal corretamente possa receber a Refazer quando que se torne uma suspensão. Previamente criá-las aqui também permite que os registos de espera Refazer seja criada automaticamente da configuração em espera. É importante configurar o modo de espera Refazer registos (SRL) com o mesmo tamanho como o online Refazer registos. O tamanho dos ficheiros de registo Refazer espera atual tem de corresponder exatamente o tamanho dos ficheiros de registo de Refazer online de base de dados primária atual.

Executar a seguinte instrução SQL\*PLUS linha de comandos no MÁQUINA1. O ficheiro de registo de $ v é uma vista de sistema que contenha informações sobre os ficheiros de registo de Refazer.

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


Note que 52428800 é 50 megabytes.

Em seguida, no SQL\*Plus janela, execute as seguintes instruções para adicionar um novo grupo de ficheiro de registo de Refazer em espera para uma base de dados em espera e especificar um número que identifica o grupo utilizando a cláusula de grupo. Utilizar números de grupo pode efetuar administrar grupos de ficheiros de registo de espera Refazer mais fácil:

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

Em seguida, execute a vista seguinte do sistema para listar informações sobre Refazer ficheiros de registo. Esta operação também verifica que foram criados os grupos de ficheiros de registo de espera Refazer:

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>Activar o arquivo

Em seguida, ativar arquivo executando os seguintes declarações para colocar a base de dados principal no modo de ARCHIVELOG e activar o arquivo automático. Pode ativar o modo de registo de arquivo ao montagem a base de dados e, em seguida, executar o comando de archivelog.

Em primeiro lugar, inicie sessão como sysdba. Numa linha de comandos do Windows, execute:

    sqlplus /nolog

    connect / as sysdba

Em seguida, encerramento a base de dados SQL\*Plus linha de comandos:

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

Em seguida, execute o comando de montagem de arranque para montagem a base de dados. Este procedimento garante que Oracle associa a instância de base de dados especificada.

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

Em seguida, execute:

    SQL> alter database archivelog;
    Database altered.

Em seguida, execute o Alter declaração de base de dados com a cláusula abrir para tornar a base de dados disponíveis para utilização normal:

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>Definir parâmetros de inicialização da base de dados principal

Para configurar a guarda de dados, tem de criar e configurar os parâmetros suspensão num pfile normal (ficheiro de parâmetro de inicialização texto) primeiro. Quando o pfile estiver pronta, tem de convertê-la para um ficheiro de parâmetro de servidor (SPFILE).

Pode controlar o ambiente de dados atento utilizando os parâmetros na Inicialização. Ficheiro de ORA. Quando seguir este tutorial, é necessário atualizar a Inicialização de base de dados principal. ORA, de modo que a mesma pode conter ambas as funções: principal ou modo de espera.

    SQL> create pfile from spfile;
    File created.

Em seguida, tem de editar pfile para adicionar os parâmetros em espera. Para executar esta tarefa, abra o INITTEST. ORA de ficheiros na localização de % ORACLE\_base %\\base de dados. Em seguida, anexe seguintes declarações para o ficheiro INITTEST.ora. A Convenção de nomenclatura para sua Inicialização. Ficheiro de ORA é Inicialização\<YourDatabaseName\>. ORA.

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


O bloco de declaração anterior inclui três itens de configuração importantes:
-   **LOG_ARCHIVE_CONFIG...:** Definir os ids exclusivos da base de dados utilizando este instrução.
-   **LOG_ARCHIVE_DEST_1...:** Definir a localização da pasta arquivo local utilizando este instrução. Recomendamos que crie um novo directório para necessidades de arquivamento a base de dados e especifique a localização do arquivo local utilizando este instrução explicitamente em vez de utilizar predefinido pasta % do Oracle ORACLE_HOME%\database\archive.
-   **LOG_ARCHIVE_DEST_2... LGWR ASSÍNCRONA...:** pode definir um processo de sénior registo assíncrona (LGWR) para recolher dados Refazer da transação e transmiti-lo para destinos em espera. Aqui, o DB_UNIQUE_NAME Especifica um nome exclusivo para a base de dados no servidor em modo de espera de destino.

Assim que o novo ficheiro de parâmetro estiver pronto, tem de criar o spfile a partir dos mesmos.

Em primeiro lugar, encerramento a base de dados:

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

Em seguida, execute o comando de nomount de arranque da seguinte forma:

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

Agora, crie um spfile:

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

Em seguida, encerramento a base de dados:

    SQL> shutdown immediate;

    ORA-01507: database not mounted

Em seguida, utilize o comando de arranque para iniciar uma instância:

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

##<a name="create-a-physical-standby-database"></a>Criar uma base de dados em modo de espera física
Esta secção foca-se os passos que deve efetuar no MÁQUINA2 para preparar a base de dados em modo de espera física.

Em primeiro lugar, tem de ambiente de trabalho remoto MÁQUINA2 através do portal do Azure clássico.

Em seguida, no servidor em modo de espera (MÁQUINA2), criar todas as pastas necessárias para a base de dados em espera, tal como c:\\\<YourLocalFolder\>\\teste. Ao seguir este tutorial, certifique-se de que a estrutura de pastas corresponde a estrutura de pastas no MÁQUINA1 para manter todos os ficheiros necessários, como ficheiros controlfile, dos ficheiros de dados, redologfiles, udump, bdump e cdump. Além disso, definir o ORACLE\_casa e ORACLE\_variáveis de ambiente de BASE MÁQUINA2. Caso não esteja, defina-las como uma variável de ambiente utilizando a caixa de diálogo de variáveis de ambiente. Para aceder a esta caixa de diálogo, inicie o utilitário de **sistema** fazendo duplo clique no ícone de sistema do **Painel de controlo**; em seguida, clique no separador **Avançadas** e selecione **Variáveis de ambiente**. Para definir as variáveis de ambiente, clique no botão **Novo** em **Variáveis do sistema**. Depois de configurar as variáveis de ambiente, tem de fechar a linha de comandos existente do Windows e abra um novo para ver as alterações.

Em seguida, siga estes passos:

1. Preparar um ficheiro de parâmetro de inicialização para base de dados em espera

2. Configurar o escuta e tnsnames para suportar a base de dados em máquinas principais e em espera

    1. Configurar listener.ora em ambos os servidores para colocar em espera entradas para ambas as bases de dados

    2. Configurar o Tnsnames nas máquinas virtuais principal e em espera para colocar em espera entradas para bases de dados principais e em espera

    3. Iniciar a escuta e marque tnsping em ambas as máquinas virtuais para ambos os serviços.

3. A instância suspensão no Estado nomount de arranque

4. Utilize RMAN para clonar a base de dados e para criar uma base de dados em espera

5. Iniciar a base de dados em modo de espera físico no modo de recuperação geridos

6. Verifique se a base de dados em modo de espera física

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1. a preparar um ficheiro de parâmetro de inicialização para base de dados em espera

Esta secção demonstra como preparar um ficheiro de parâmetro de inicialização para a base de dados em espera. Para executar esta tarefa, copie primeiro a INITTEST. ORA do ficheiro de máquina 1 para MÁQUINA2 manualmente. Deverá conseguir ver o INITTEST. ORA de ficheiros na pasta % ORACLE\_base %\\pasta de base de dados em ambas as máquinas. Modificar, em seguida, o ficheiro INITTEST.ora MÁQUINA2 para configurá-la para a função espera conforme especificado abaixo:

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


O bloco de declaração anterior inclui dois itens importantes configuração:

-   **\*. LOG_ARCHIVE_DEST_1:** necessárias para criar manualmente a pasta c:\OracleDatabase\TEST_STBY\archives no MÁQUINA2.
-   **\*. LOG_ARCHIVE_DEST_2:** este passo é opcional. Defina esta conforme poderá ser necessário quando o computador principal estiver na manutenção e a suspensão máquina torna-se uma base de dados principal.

Em seguida, tem de iniciar a instância em espera. No servidor de base de dados em espera, introduza o seguinte comando na linha de comandos do Windows para criar uma instância de Oracle através da criação de um serviço do Windows:

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

O comando **Oradim** cria uma instância de Oracle mas não é iniciado. Pode encontrá-lo a c:\\OracleDatabase\\produto\\11.2.0\\dbhome\_1\\directório BIN.

##<a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>Configurar o escuta e tnsnames para suportar a base de dados em máquinas principais e em espera
Antes de criar uma base de dados em espera, é necessário para se certificar de que as bases de dados principais e em espera na configuração podem falar umas às outras. Para executar esta tarefa, tem de configurar escuta tanto TNSNames manualmente ou utilizando o utilitário de configuração de rede NETCA. Este é uma tarefa obrigatória ao utilizar o Gestor de recuperação de utilitário (RMAN).

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>Configurar listener.ora em ambos os servidores para colocar em espera entradas para ambas as bases de dados

Ambiente de trabalho remoto MÁQUINA1 e editar o ficheiro listener.ora como especificado abaixo. Quando edita o ficheiro listener.ora, certifique-se sempre que a abertura e o parêntese de fecho linha para cima na mesma coluna. Pode localizar o ficheiro listener.ora na seguinte pasta: c:\\OracleDatabase\\produto\\11.2.0\\dbhome\_1\\rede\\administração\\.

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

Ambiente de trabalho remoto, seguinte para MÁQUINA2 e editar o listener.ora ficheiro da seguinte forma: # listener.ora ficheiro de configuração de rede: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>Configurar o Tnsnames nas máquinas virtuais principal e em espera para colocar em espera entradas para bases de dados principais e em espera

Ambiente de trabalho remoto MÁQUINA1 e editar o ficheiro Tnsnames como especificado abaixo. Pode localizar o ficheiro Tnsnames na seguinte pasta: c:\\OracleDatabase\\produto\\11.2.0\\dbhome\_1\\rede\\administração\\.

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

Ambiente de trabalho remoto MÁQUINA2 e editar o Tnsnames de ficheiro da seguinte forma:

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>Iniciar a escuta e marque tnsping em ambas as máquinas virtuais para ambos os serviços.

Abra uma nova linha de comandos do Windows em máquinas virtuais do principal e em espera e execute as seguintes instruções:

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


##<a name="start-up-the-standby-instance-in-nomount-state"></a>A instância suspensão no Estado nomount de arranque
Para configurar o ambiente para suportar a base de dados em modo de espera na máquina em modo de espera Virtual (MÁQUINA2).

Em primeiro lugar, copie o ficheiro de palavra-passe a partir do computador (MÁQUINA1) principal para a suspensão máquina (MÁQUINA2) manualmente. Isto é necessário, tal como a palavra-passe **sobre** tem de ser idêntica em ambas as máquinas.

Em seguida, abra a linha de comandos do Windows no MÁQUINA2 e configurar as variáveis de ambiente para apontar para a base de dados do modo de espera da seguinte forma:

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

Em seguida, iniciar a base de dados do modo de espera num Estado de nomount e, em seguida, gerar um spfile.

Inicie a base de dados:

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


##<a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>Utilize RMAN para clonar a base de dados e para criar uma base de dados em espera
Pode utilizar o Gestor de recuperação de utilitário (RMAN) para tomar qualquer cópia de segurança da base de dados principal para criar a base de dados em modo de espera física.

Cadeia de ambiente de trabalho remoto em modo de espera Virtual máquina (MÁQUINA2) e ao executar o utilitário RMAN ao especificar uma ligação completa para o (base de dados primária, MÁQUINA1) de destino e AUXILIAR (em espera base de dados, MÁQUINA2) instâncias.

>[AZURE.IMPORTANT] Não utilize a autenticação do sistema operativo que não exista o nenhuma base de dados no computador servidor em espera ainda.

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

##<a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>Iniciar a base de dados em modo de espera físico no modo de recuperação geridos
Neste tutorial demonstra como criar uma base de dados em modo de espera física. Para obter informações sobre como criar uma base de dados em modo de espera lógico, consulte a documentação Oracle.

Abrir o SQL\*Plus comandos e ativar o atento de dados no modo de espera Virtual Machine ou servidor (MÁQUINA2) da seguinte forma:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Quando abre a base de dados em modo de espera no modo **de montagem** , o registo de arquivo continua de envio e o processo de recuperação geridos continua registo aplicar na base de dados em espera. Este procedimento garante que a base de dados em modo de espera permanece atualizado com a base de dados principal. Tenha em atenção que a base de dados em modo de espera não pode ser acessível para elaboração de relatórios durante este período de tempo.

Quando abre a base de dados em modo de espera em modo **Só de leitura** , o arquivo envio de registos do continua. Mas deixa o processo de recuperação gerida. Isto faz com que a base de dados em espera para se tornar cada vez desatualizada até que o processo de recuperação geridos é retomado. Pode aceder a base de dados em espera para elaboração durante este período de tempo, mas dados não podem refletir as alterações mais recentes.

Em geral, recomendamos que mantenha a base de dados em modo de espera no modo **de montagem** para manter os dados na base de dados em modo de espera atualizado se existir uma falha da base de dados principal. No entanto, pode manter a base de dados em modo de espera em modo **Só de leitura** para elaboração dependendo requisitos da sua aplicação. Os passos seguintes demonstram como ativar o atento de dados no modo só de leitura utilizando SQL\*Plus:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


##<a name="verify-the-physical-standby-database"></a>Verifique se a base de dados em modo de espera física
Esta secção demonstra como verificar a configuração de elevada disponibilidade como administrador.

Abrir o SQL\*Plus janela de linha de comandos e verificação arquivados Refazer registo no modo de espera Máquina Virtual (MÁQUINA2):

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

Abrir o SQL * Plus linha de comandos janela e mudar ficheiros de registo no computador principal (MÁQUINA1):

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

Verifique o registo de refazer arquivados no modo de espera Máquina Virtual (MÁQUINA2):

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

Procurar qualquer intervalo: controla no modo de espera Máquina Virtual (MÁQUINA2):

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

Outro método de confirmação pode ser activação pós-falha à base de dados em espera e, em seguida, teste se é possível reposição de recurso para a base de dados principal. Para ativar a base de dados em espera, como uma base de dados principal, utilize as seguintes instruções:

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Se não tiver ativado flashback na base de dados principal original, é recomendado que remover a base de dados principal original e recrie como uma base de dados em espera.

Recomendamos que permitem a base de dados de flashback na página principal e as bases de dados em espera. Quando uma activação pós-falha acontece, a base de dados principal pode ser piscou novamente para o tempo antes da activação pós-falha e rapidamente convertida numa base de dados em espera.

##<a name="additional-resources"></a>Recursos adicionais
[Imagens de Máquina Virtual Oracle para Azure](virtual-machines-windows-classic-oracle-images.md)
