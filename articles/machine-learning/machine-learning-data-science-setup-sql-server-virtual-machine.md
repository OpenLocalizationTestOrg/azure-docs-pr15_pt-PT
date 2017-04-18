<properties
    pageTitle="Configurar uma máquina de virtual do SQL Server como um servidor de bloco de notas IPython | Microsoft Azure"
    description="Defina o uma dados ciência Máquina Virtual com SQL Server e IPython Server."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev" 
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurar uma máquina de virtual Azure SQL Server como um servidor de IPython bloco de notas para análise avançadas

Este tópico mostra-lhe como pode aprovisionar e configurar uma máquina de virtual do SQL Server para ser utilizado como parte de um ambiente de ciências dados baseado na nuvem. A máquina virtual do Windows está configurada com ferramentas como IPython bloco de notas, Explorador de armazenamento do Azure e AzCopy, bem como outras utilitários que são úteis para projetos de ciências de dados de suporte. Azure Explorador de armazenamento e AzCopy, por exemplo, fornecem formas convenientes para carregar os dados ao armazenamento de Blobs do Azure a partir do seu computador local ou para transferi-lo para o seu computador local a partir do armazenamento de Blobs.

A Galeria de máquina virtual Azure inclui várias imagens que contêm Microsoft SQL Server. Selecione uma imagem do SQL Server VM adequado às suas necessidades de dados. Imagens recomendadas são:

- SQL Server 2012 SP2 Enterprise para pequenas e tamanhos de dados médios
- SQL Server 2012 SP2 Enterprise otimizada para DataWarehousing das cargas de trabalho para tamanhos grandes para muito grandes dados

 > [AZURE.NOTE] SQL Server 2012 SP2 Enterprise imagem **não inclui um disco de dados**. Terá de adicionar e/ou anexar um ou mais virtual disco rígido para armazenar os seus dados. Quando cria uma máquina virtual Azure, tem um disco para o sistema operativo mapeado para a unidade C e um disco temporário mapeados para a unidade D. Não utilize na unidade D para armazenar dados. Como o nome implica, fornece armazenamento temporário apenas. Oferece sem cópia de segurança ou redundância porque-não reside na armazenamento Azure.


##<a name="Provision"></a>Ligar ao Portal clássica do Azure e aprovisionar uma máquina de virtual do SQL Server

1.  Inicie sessão no [Portal clássica Azure](http://manage.windowsazure.com/) utilizando a sua conta.
    Se não possui uma conta Azure, visite o [Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

2.  No Portal clássica do Azure, na parte inferior esquerda da página web, clique em **+ Novo**, clique em **Calcular**, clique em **máquina VIRTUAL**e, em seguida, clique em **De GALERIA**.

3.  Na página **criar uma Máquina Virtual** , selecione uma imagem de máquina virtual que contém o SQL Server com base nas suas necessidades de dados e, em seguida, clique na seta seguinte na parte inferior direita da página. Para obter informações mais atualizadas sobre as imagens suportadas do SQL Server no Azure, consulte [Introdução ao SQL Server em máquinas virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) tópico documentação do [SQL Server em máquinas virtuais do Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) definir.

    ![Selecione VM do SQL Server][1]

4.  Na primeira página de **Configuração de Máquina Virtual** , forneça as seguintes informações:

    -   Forneça um **nome de máquina VIRTUAL**.
    -   Na caixa **Novo nome de utilizador** , escreva o nome de utilizador exclusivo para a conta de administrador local VM.
    -   Na caixa **Nova palavra-passe** , escreva uma palavra-passe segura. Para mais informações, consulte o artigo [Forte as palavras-passe](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Na caixa **Confirmar palavra-passe** , escreva novamente a palavra-passe.
    -   Selecione o **tamanho** de adequado a partir da lista pendente.

     > [AZURE.NOTE] O tamanho da máquina virtual é especificado durante o aprovisionamento: A2 é o tamanho mais pequeno recomendado para cargas de trabalho de produção. O tamanho mínimo recomendado para uma máquina virtual é A3 quando utilizar o SQL Server Enterprise Edition. Selecione A3 ou superior ao utilizar o SQL Server Enterprise Edition. Selecione A4 quando utilizar o SQL Server 2012 ou 2014 Enterprise otimizado para imagens transaccionais das cargas de trabalho.
Selecione A7 quando utilizar o SQL Server 2012 ou 2014 Enterprise otimizado para imagens cargas de trabalho de armazenamento de dados. O tamanho seleccionado limita o número de discos de dados, que pode configurar. Para obter informações mais atualizadas sobre tamanhos de máquina virtual disponível e o número de discos de dados que pode anexar a uma máquina virtual, consulte o artigo [Tamanhos de Máquina Virtual para Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para informações sobre os preços, consulte o artigo [VIrtual Macines preços](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Clique na seta no canto inferior direito para continuar a seguinte.

    ![Configuração de VM][2]

5.  Na segunda página de **configuração de Máquina Virtual** , configure recursos para funcionamento em rede, armazenamento e disponibilidade:

    -   Na caixa **Serviço na nuvem** , selecione **criar um novo serviço na nuvem**.
    -   Na caixa **Nome de DNS do serviço de nuvem** , fornecer a primeira parte de um nome de DNS da sua escolha, para que o que é concluído um nome no formato de **TESTNAME.cloudapp.net**
    -   Na caixa **Da rede grupo/VIRTUAL região/AFINIDADE** , selecione uma região onde será alojada esta imagem virtual.
    -   Na **Conta de armazenamento**, selecione uma conta existente do armazenamento ou selecione uma gerado automaticamente.
    -   Na caixa **Conjunto de disponibilidade** , selecione **(nenhum)**.
    -   Leia e aceite informações sobre preços.

6.  Na secção **pontos finais** , clique na lista pendente vazio em **nome**e selecione **MSSQL** , em seguida, escreva o número de porta da instância do motor de base de dados (**1433** para a instância predefinida).

7.  A VM do SQL Server também pode servir um servidor de bloco de notas IPython, que irão ser configuradas de um passo posterior.
    Adicione um novo ponto final para especificar a porta a utilizar para o seu servidor de IPython bloco de notas. Introduza um nome na coluna **nome** , selecione um número de porta da sua escolha para a porta pública e 9999 para a porta privada.

    Clique na seta no canto inferior direito para continuar a seguinte.

    ![Selecione as portas MSSQL e IPython][3]

8.  Aceitar a opção de **agente de instalar VM** predefinida selecionada e clique na marca de verificação no canto inferior direito do Assistente para concluir o processo de aprovisionamento de VM.

    `![Opções de finais VM][4]

9.  Aguarde enquanto o Azure prepara máquina virtual. Espera o estado de máquina virtual avance:

    -   Iniciar a (aprovisionamento)
    -   Parado
    -   Iniciar a (aprovisionamento)
    -   Executar (aprovisionamento)
    -   Em execução


##<a name="RemoteDesktop"></a>Abra a máquina virtual com o ambiente de trabalho remoto e concluir a configuração

1.  Quando aprovisiona o concluir, clique no nome do seu máquina virtual para ir para a página do DASHBOARD. Na parte inferior da página, clique em **Ligar**.

2.  Selecione para abrir o ficheiro de rpd utilizando o programa de ambiente de trabalho remoto do Windows (`%windir%\system32\mstsc.exe`).

3.  Na caixa de diálogo **Segurança do Windows** , forneça a palavra-passe da conta de administrador local que especificou num passo anterior.
    (Lhe poderá ser-lhe pedido para verificar as credenciais da máquina virtual.)

4.  A primeira vez que iniciar sessão para esta máquina virtual, poderão ter vários processos concluir, incluindo a configuração do seu ambiente de trabalho, atualizações do Windows e conclusão das tarefas de configuração inicial do Windows (sysprep). Após completa sysprep do Windows, o programa de configuração do SQL Server conclui tarefas de configuração. Estas tarefas poderão causar um atraso de alguns minutos, enquanto que concluem. `SELECT @@SERVERNAME`podem não devolver o nome correto até conclusão da configuração do SQL Server e não pode ser visable na página inicial do SQL Server Management Studio.

Assim que estiverem ligados à máquina virtual com o ambiente de trabalho remoto do Windows, a máquina virtual funciona muito semelhante a qualquer outro computador. Ligar a instância predefinida do SQL Server com SQL Server Management Studio (em execução no computador virtual) da forma normal.


##<a name="InstallIPython"></a>Instalar o bloco de notas IPython e outras ferramentas de suporte

Para configurar o seu novo VM de servidor de SQL para servir um servidor de IPython bloco de notas e instalar ferramentas adicionais de suporte essa AzCopy, Explorador de armazenamento do Azure, pacotes de dados ciência Python úteis e outras pessoas, é fornecido um script de personalização especial para si. Para instalar o:

- Botão direito do rato no ícone de **Iniciar do Windows** e clique em **linha de comandos (Admin)**
- Copie os seguintes comandos e cole na linha de comandos.

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Quando lhe for pedido, introduza uma palavra-passe da sua preferência para o servidor de IPython bloco de notas.
- O script de personalização automatiza vários procedimentos de instalação pós, que incluem:
    + Instalação e configuração do servidor de IPython bloco de notas
    + Abrir as portas TCP na firewall do Windows para os pontos finais criada anteriormente:
    + Para conectividade remota do SQL Server
    + Para conectividade remota de servidor IPython bloco de notas
    + Obter a blocos de notas do exemplo IPython e scripts SQL
    + Transferir e instalar pacotes de dados ciência Python úteis
    + Transferir e instalar o Azure ferramentas, como AzCopy e o Explorador de armazenamento do Azure  
<br>
- Pode aceder e executar IPython bloco de notas a partir de qualquer browser local ou remota utilizando um URL do formulário `https://<virtual_machine_DNS_name>:<port>`, onde porta é a porta de pública IPython que selecionou enquanto aprovisionamento máquina virtual.
- Servidor de IPython bloco de notas está a funcionar como um serviço de fundo e será reiniciado automaticamente quando reiniciar a máquina virtual.

##<a name="Optional"></a>Anexe o disco de dados, conforme necessário

Se a sua imagem VM não inclui discos de dados, por exemplo, discos diferente de unidade C (disco SO) e na unidade D (disco temporário), tem de adicionar um ou mais discos de dados para armazenar os seus dados. A imagem VM para SQL Server 2012 SP2 Enterprise otimizada para DataWarehousing cargas de trabalho é fornecido pré-configurada com discos adicionais para ficheiros de dados e de registo do SQL Server.

 > [AZURE.NOTE] Não utilize na unidade D para armazenar dados. Como o nome implica, fornece armazenamento temporário apenas. Oferece sem cópia de segurança ou redundância porque-não reside na armazenamento Azure.

Para anexar discos dados adicionais, siga os passos descritos no artigo [como anexar um disco de dados para uma máquina de Virtual do Windows](virtual-machines-windows-classic-attach-disk.md), que irá guiá-lo através de:

1. Anexar discos vazios para a máquina virtual aprovisionada nos passos anteriores
2. Inicialização de discos novo na máquina virtual


##<a name="SSMS"></a>Ligar ao SQL Server Management Studio e ativar a autenticação do modo misto

Motor de base de dados do SQL Server não é possível utilizar a autenticação do Windows sem ambiente do domínio. Para ligar para o motor de base de dados a partir de outro computador, configure o SQL Server para autenticação do modo misto. Autenticação do modo misto permite a autenticação do SQL Server e a autenticação do Windows. Modo de autenticação de SQL for necessário para ingerir esta última dados diretamente a partir do seu bases de dados do SQL Server VM no [Azure máquina aprendizagem Studio](https://studio.azureml.net) utilizar o módulo importar dados.

1.  Enquanto estiver ligado à máquina virtual utilizando o ambiente de trabalho remoto, utilize o painel de **pesquisa** do Windows e escreva o **SQL Server Management Studio** (SMSS). Clique aqui para iniciar o SQL Server Management Studio (SSMS). Pretende adicionar um atalho para SSMS no ambiente de trabalho para utilização futura.

    ![Iniciar SSMS][5]

    A primeira vez que abrir Management Studio-tem de criar o ambiente de Management Studio utilizadores. Isto pode demorar alguns minutos.

2.  Quando tentar abrir, Management Studio apresenta a caixa de diálogo **ligar ao servidor** . Na caixa **nome** de servidor, escreva o nome da máquina virtual para ligar para o motor de base de dados com o Explorador de objeto.
    (Em vez do nome de máquina virtual também pode utilizar **(local)** ou um único período como o **nome do servidor**. Selecione **A autenticação do Windows**e deixe ** *seu\_VM\_nome*\\seu\_local\_administrador** na caixa **nome de utilizador** . Clique em **Ligar**.

    ![Ligar ao servidor][6]

    <br>

     > [AZURE.TIP] Pode alterar o modo de autenticação do SQL Server utilizando uma alteração chave de registo do Windows ou utilizando o SQL Server Management Studio. Para alterar o modo de autenticação utilizando a alteração de chave de registo, iniciar uma **Nova consulta** e execute o seguinte script:

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    Para alterar o modo de autenticação utilizando a gestão do SQL Server Studio:

3.  No **Explorador de objeto do SQL Server Management Studio**, com o botão direito no nome da instância do SQL Server (o nome de máquina virtual) e, em seguida, clique em **Propriedades**.

    ![Propriedades do servidor][7]

4.  Na página de **segurança** , em **autenticação de servidor**, selecione **do SQL Server e o modo de autenticação do Windows**e, em seguida, clique em **OK**.

    ![Selecione o modo de autenticação][8]

5.  Na caixa de diálogo **SQL Server Management Studio** , clique em **OK** para confirmar o requisito de registo para reiniciar o SQL Server.

6.  No **Explorador de objectos**, com o botão direito do servidor e, em seguida, clique em **reiniciar**. (Se estiver a executar agente do SQL Server,-tem também de ser reiniciado.)

    ![Reinicie o][9]

7.  Na caixa de diálogo **SQL Server Management Studio** , clique em **Sim** para aceitar a que pretende reinicie o SQL Server.

##<a name="Logins"></a>Criar inícios de sessão do SQL Server autenticação

Para ligar para o motor de base de dados a partir de outro computador, terá de criar pelo menos um início de sessão de autenticação do SQL Server.  

Pode criar novos inícios de sessão do SQL Server através de programação ou a utilizar o SQL Server Management Studio. Para criar um novo utilizador sysadmin com autenticação de SQL através de programação, inicie uma **Nova consulta** e execute o seguinte script. Substituir < novo nome de utilizador\> e < nova palavra-passe\> com a escolha de *nome de utilizador* e *palavra-passe*. 


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


Ajuste a política de palavra-passe, conforme necessário (a exemplo código desliga expiração de palavra-passe e de verificação de política). Para mais informações sobre os inícios de sessão do SQL Server, consulte o artigo [criar um início de sessão](http://msdn.microsoft.com/library/aa337562.aspx).  

Para criar novos inícios de sessão do SQL Server utilizando o SQL Server Management Studio:

1.  No **Explorador de objeto do SQL Server Management Studio**, expanda a pasta da instância de servidor no qual pretende criar o novo início de sessão.

2.  Botão direito do rato na pasta de **segurança** , aponte para **Novo**e selecione **Iniciar sessão...**.

    ![Novo início de sessão][10]

3.  Na caixa de diálogo de **início de sessão - novo** , na página **Geral** , introduza o nome do novo utilizador na caixa **nome de início de sessão** .

4.  Selecione a **autenticação do SQL Server**.

5.  Na caixa **palavra-passe** , introduza uma palavra-passe para o novo utilizador. Introduza novamente a palavra-passe na caixa **Confirmar palavra-passe** .

6.  Para impor a opções de política de palavra-passe de complexidade e imposição, selecione **impor políticas de palavra-passe** (recomendado). Esta é uma opção de predefinida quando está selecionada a autenticação do SQL Server.

7.  Para impor a opções de política de palavra-passe para expiração, selecione **impor expiração de palavra-passe** (recomendado). Impor políticas de palavra-passe tem de estar selecionada para ativar esta caixa de verificação. Esta é uma opção de predefinida quando está selecionada a autenticação do SQL Server.

8.  Para forçar o utilizador para criar uma nova palavra-passe após a primeira vez que é utilizado o início de sessão, selecione o **utilizador tem de alterar a palavra-passe no início de sessão seguinte** (recomendado se este login que alguém a utilizar. Se for o início de sessão para utilização própria, não selecione esta opção.) Impor expiração de palavra-passe tem de estar selecionada para ativar esta caixa de verificação. Esta é uma opção de predefinida quando está selecionada a autenticação do SQL Server.

9.  A partir da lista de **base de dados predefinida** , selecione uma base de dados predefinido para o início de sessão. a predefinição para esta opção é **principal** . Se ainda não criou uma base de dados de utilizador, deixe configurado no **modelo global**.

10. Na lista **idioma predefinido** , deixe **predefinida** como o valor.

    ![Propriedades de início de sessão][11]

11. Se este for o primeiro login que estiver a criar, poderá querer designar este início de sessão como administrador do SQL Server. Se Sim, na página de **Funções de servidor** , selecione **sysadmin**.

    > [AZURE.IMPORTANT] Os membros fixo sysadmin têm controlo total do motor de base de dados. Por motivos de segurança, deverá cuidadosamente restringir a associação desta função.

    ![sysadmin][12]

12. Clique em OK.

##<a name="DNS"></a>Determinar o nome DNS da máquina virtual

Para se ligar ao motor de base de dados do SQL Server a partir de outro computador, tem de saber o nome do sistema de nomes de domínio (DNS) da máquina virtual.

(Este é o nome que na internet utiliza para identificar a máquina virtual. Pode utilizar o endereço IP, mas o endereço IP poderá alterar quando o Azure move recursos para redundância ou manutenção. O nome de DNS será estável uma vez que pode ser redirecionado para um novo endereço IP.)

1.  No Portal clássica do Azure (ou a partir do passo anterior), selecione **máquinas virtuais**.

2.  Na página **Instâncias de máquina VIRTUAL** , na coluna **Nome de DNS** , localize e copie o nome de DNS para o computador virtual que aparece precedido **http://**. (A interface de utilizador poderá não apresentar o nome completo, mas pode com o botão direito no mesmo e selecione copiar.)

##<a name="cde"></a>Ligar para o motor de base de dados a partir de outro computador

1.  Num computador ligado à internet, abra o SQL Server Management Studio.

2.  Na caixa de diálogo **ligar ao servidor de** ou **ligar ao motor de base de dados** , na caixa **nome** de servidor, introduza o nome DNS de máquina virtual (determinado na tarefa anterior) e um número de porta ponto final público no formato de *NomeDNS, NúmeroPorta* como **tutorialtestVM.cloudapp.net,57500**.

3.  Na caixa de **autenticação** , selecione a **Autenticação do SQL Server**.

4.  Na caixa de **início de sessão** , escreva o nome de um início de sessão que criou numa tarefa anterior.

5.  Na caixa **palavra-passe** , escreva a palavra-passe de início de sessão que criar uma tarefa anterior.

6.  Clique em **Ligar**.

##<a name="amlconnect"></a>Ligar para o motor de base de dados a partir do Azure máquina aprendizagem

No posteriores fases do processo de ciências de dados de equipa, que irá utilizar o [Azure máquina de formação Studio](https://studio.azureml.net) para criar e implementar modelos de aprendizagem automática. Para ingerir esta última dados a partir de bases de dados do SQL Server VM diretamente para o Azure máquina formação de cursos de formação ou pontuação, utilizar o módulo de **Importar dados** de uma nova experiência do [Azure máquina aprendizagem Studio](https://studio.azureml.net) . Este tópico é abrangido mais detalhes nas ligações de guia de processo de ciências de dados de equipa. Para obter uma introdução, consulte o artigo [o que é o Azure máquina aprendizagem Studio?](machine-learning-what-is-ml-studio.md).

2.  No painel de **Propriedades** do [módulo importar dados](https://msdn.microsoft.com/library/azure/dn905997.aspx), selecione a **Base de dados do SQL Azure** na lista pendente de **Origem de dados** .

3.  Na caixa de texto **nome de servidor de base de dados** , introduza`tcp:<DNS name of your virtual machine>,1433`

4.  Introduza o nome de utilizador SQL na caixa de texto **nome de conta de utilizador do servidor** .

5.  Introduza a palavra-passe de utilizador de sql na caixa de texto **palavra-passe do conta de utilizador do servidor** .

    ![Azure ML importar dados][13]

##<a name="shutdown"></a>Encerramento e retirar máquina virtual quando não estiver em utilização

Azure máquinas virtuais são preços como **pagar apenas para que utiliza**. Para se certificar de que lhe não é a serem faturadas quando não utilizar o seu máquina virtual, tem de estar no estado **parado (Deallocated)** .

> [AZURE.NOTE] Encerrar a máquina virtual a partir de dentro (utilizando as opções de energia do Windows), a VM está parada mas permanece atribuído. Para se certificar de que não está a ser faturada, pare sempre máquinas virtuais a partir do [Azure clássica Portal](http://manage.windowsazure.com/). Também pode deixar a VM através do Powershell ao contactar o ShutdownRoleOperation com "PostShutdownAction" igual a "StoppedDeallocated".

Para encerramento e retirar a máquina virtual:

1. Inicie sessão no [Portal clássica Azure](http://manage.windowsazure.com/) utilizando a sua conta.  

2. Selecione **máquinas virtuais** a partir da barra de navegação à esquerda.

3. Na lista de máquinas virtuais, clique no nome do seu máquina virtual, em seguida, aceda à página do **DASHBOARD** .

4. Na parte inferior da página, clique em **encerramento**.

![VM encerramento][15]

A máquina virtual será desatribuída mas não eliminada. Pode reiniciar o seu máquina virtual em qualquer altura a partir do Portal de clássica Azure.

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>Está pronta a utilizar a VM do Azure SQL Server: o que se segue?

Máquina virtual está agora pronta para utilizar no seu exercícios de ciências de dados. A máquina virtual também está pronta para ser utilizado como um servidor de IPython bloco de notas para informações detalhadas e processamento de dados e outras tarefas em conjunto com o Azure máquina aprendizagem e o processo de ciências de dados de equipa (TDSP).

Os passos no processo de ciências de dados são mapeados do [Processo de ciências de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir passos que mover dados para o HDInsight, processam e de exemplo-lo lá preparação para a formação dos dados de com Azure máquina de formação.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 
