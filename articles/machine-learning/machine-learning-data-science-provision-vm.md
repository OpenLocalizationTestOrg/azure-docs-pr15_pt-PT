<properties 
    pageTitle="Aprovisionar o Microsoft dados ciência Virtual Machine | Microsoft Azure" 
    description="Configurar e crie uma máquina de Virtual de ciências de dados no Azure fazer: análise e formação de computador." 
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
    ms.date="09/07/2016" 
    ms.author="bradsev" />


# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Aprovisionar a Máquina Virtual da ciência dados da Microsoft

O Microsoft dados ciência Virtual Machine é uma imagem de máquina virtual Azure (VM) pré-instalado e configurado com várias ferramentas populares utilizados frequentemente de aprendizagem automática e de análise de dados. As ferramentas incluídas são:

- Microsoft R Server Developer Edition
- Distribuição de Python anaconda
- Jupyter bloco de notas (R, Python kernels)
- Visual Studio Community Edition
- Ambiente de trabalho do Power BI
- SQL Server 2016 Developer Edition
- Formação máquinas-ferramentas
    - [Toolkit de rede utilizaria (CNTK)](https://github.com/Microsoft/CNTK): uma profundidade toolkit de software da Microsoft Research de formação.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): uma máquina fast sistema que suporte técnicas como online, hashing, allreduce, reduções, learning2search, ativo, de formação e aprendizagem interativa.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta de implementação rápida e precisas aumentado da árvore de fornecer.
    - [Rattle](http://rattle.togaware.com/) (o R Analytical ferramenta para saber facilmente): uma ferramenta que facilita a introdução ao automática R fácil, com informações detalhadas de dados com base em interface gráfica de formação e de modelação com geração de código R automática e de análise de dados.
    - [mxnet](https://github.com/dmlc/mxnet): um quadro de aprendizagem abrangente concebido para eficiência e flexibilidade
- Bibliotecas no R e Python para utilizam no Azure máquina formação e outros serviços do Azure
- Git incluindo Git festa para trabalhar com repositórios de código fonte incluindo o GitHub, serviços de equipa do Visual Studio
- Portas do Windows de vários populares Linux da linha de comandos utilitários (incluindo awk, sed, perl, grep, localizar, wget, Laço etc) acessível através de linha de comandos. 


Efetuar ciência dados envolve iteração de uma sequência de tarefas: localizar, carregamento e pré-processamento dados, construir testes modelos e implementar modelos para consumo nas aplicações inteligentes. Cientistas dados utilizam uma variedade de ferramentas para concluir as seguintes tarefas. Pode ser bastante demorado para localizar as versões adequadas do software e, em seguida, transfira e instale-os. O Microsoft dados ciência Virtual Machine podem facilitar os encargos ao fornecer uma imagem de prontos a utilizar que pode ser aprovisionada no Azure com todas as ferramentas de populares várias pré-instalado e configurado. 

O Microsoft dados ciência Virtual Machine jump-starts projeto analytics. Permite-lhe trabalhar em tarefas em vários idiomas incluindo R, Python, SQL e c#. O Visual Studio fornece um IDE para desenvolver e testar o seu código que seja fácil de utilizar. O SDK do Azure incluído na VM permite-lhe criar aplicações utilizando vários serviços de plataforma de nuvem da Microsoft. 

Não existem sem taxas de software para esta imagem VM de ciências de dados. Apenas pagar para as taxas de utilização Azure depende qual o tamanho da máquina virtual que aprovisionar. Obter mais detalhes sobre as taxas de cluster podem encontrar na secção de detalhes do preços na página [dados ciência Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 


## <a name="prerequisites"></a>Pré-requisitos

Pode criar uma máquina ciência Virtual dados da Microsoft, tem de ter o seguinte procedimento:

- **Azure uma subscrição**: para obter um, consulte o artigo [versão de avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Azure uma conta de armazenamento**: para criar uma, consulte o artigo [criar uma conta de armazenamento Azure](storage-create-storage-account.md#create-a-storage-account). Em alternativa, a conta de armazenamento pode ser criada como parte do processo de criação da VM se não pretender utilizar uma conta existente.


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Criar o máquina de Virtual ciência dados da Microsoft

Eis os passos para criar uma instância de dados ciência Máquina Virtual da Microsoft:

1.  Navegue para a máquina virtual listar no [Azure portal](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.   Selecione o botão **Criar** na parte inferior para ser tidos num assistente. ![configurar-dados-ciência-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.   O assistente utilizado para criar o Microsoft dados ciência Virtual Machine requer **entradas** para cada um dos **cinco passos** enumeradas à direita nesta figura. Eis as entradas necessárias para configurar a cada um dos seguintes passos:
    
    1.   **Noções básicas**
        1.   **Nome**: nome do seu servidor de ciências de dados está a criar.
        2.   **Nome de utilizador**: id de início de sessão de conta de administrador.
        3.   **Palavra-passe**: palavra-passe da conta de administrador.
        4.   **Subscrição**: Se tiver mais do que uma subscrição, selecione a uma no qual o computador está a ser criado e faturada.
        5.   **Grupo de recursos**: pode criar um novo ou utilizar um grupo existente.
        6.   **Localização**: selecione o Centro de dados que é o mais adequado. Normalmente, é o Centro de dados que tem a maior parte dos seus dados ou que seja mais próximo da sua localização física para o mais rápido acesso de rede.
         
    2.   **Tamanho**: selecione um dos tipos de servidor que cumpre os requisitos funcionais e restrições de custo. Pode obter mais opções de tamanhos VM selecionando "Ver todos".
    
    3.   **Definições**:
        1.   **Tipo de disco**: selecione Premium se preferir uma unidade de estado sólido (SSD), escolha de que outra "Padrão".
        2.   **Conta de armazenamento**: pode criar uma nova conta de armazenamento Azure na sua subscrição ou utilize um já existente na mesma *localização* que foi escolhidos no passo **Noções básicas** do assistente.
        3.   **Outros parâmetros**: normalmente utilizar apenas os valores predefinidos. Pode passar o rato sobre a ligação informativa para obter ajuda sobre os campos específicos caso pretenda que a considerar a utilização de valores não predefinida.

    4.   **Resumo**: Certifique-se de que todas as informações introduzidas estão corretas.
    5.   **Comprar**: clique em **comprar** para iniciar o aprovisionamento. Uma ligação é fornecida para os termos da transação. A VM não tem qualquer taxas adicionais para além das cluster para o tamanho do servidor que escolheu no passo **tamanho** . 


>[AZURE.NOTE] O aprovisionamento deve ter cerca de 10 a 20 minutos. O estado do aprovisionamento é apresentado no portal do Azure.

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Como aceder ao Microsoft dados ciência Virtual Machine

Quando a VM estiver criada, pode ambiente de trabalho remoto para o mesmo utilizando as credenciais de conta de administrador que configurou na secção anterior **Noções básicas** . 

Assim que a VM é criada e aprovisionada, está pronto para começar a utilizar as ferramentas que são instaladas e configuradas no mesmo. Existem mosaicos de menu Iniciar e ícones de ambiente de trabalho para muitas das ferramentas. 

## <a name="how-to-create-a-strong-password-on-the-jupyter-notebook-server"></a>Como criar uma palavra-passe segura no servidor Jupyter bloco de notas 

Para criar a sua própria palavra-passe segura para o servidor de bloco de notas Jupyter instalado no computador, execute o seguinte comando a partir de uma linha de comandos na máquina de Virtual de ciências de dados.

    c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Selecione uma palavra-passe segura quando lhe for pedido.

Consulte o hash de palavra-passe no formato "sha1:xxxxxx" no resultado. Copie esta hash de palavra-passe e substituir o hash existente que está no seu ficheiro de configuração do bloco de notas que se encontra no: **C:\ProgramData\jupyter\jupyter_notebook_config.py** com um nome de parâmetro ***c.NotebookApp.password***.

Substitua apenas a parte (xxxxxx) do valor hash existente nas aspas. As aspas e o ***sha1:*** prefixo para o valor do parâmetro ambos precisa de ser mantidos.

Por fim, tem de parar e reiniciar o servidor de Jupyter, que está a ficar com a VM como uma tarefa agendada do windows denominada **Start_IPython_Notebook**. Se a sua nova palavra-passe não é aceite após reiniciar esta tarefa, experimente matar os processos python em execução a partir do Gestor de tarefas e, em seguida, reinicie a tarefa agendada. Em alternativa, tente reiniciar a máquina virtual.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Ferramentas instaladas no dados ciência Máquina Virtual da Microsoft

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Developer Edition
Se pretender utilizar R para sua análise, a VM tem edition de programador do Microsoft R Server instalado. Microsoft R Server é uma plataforma de análise de ficarem com versões enterprise de classe executiva com base no R que é suportado, dimensionável e segura. Suportar uma variedade de estatísticas de dados grande, modelação de aspeto do Office e funcionalidades de aprendizagem automática, R servidor suporta as diversas analytics – informações detalhadas, visualização, análise e modelação. Utilizando e expandir abrir origem R, Microsoft R Server é totalmente compatível com R scripts, funções e pacotes CRAN, para analisar dados em escala do enterprise. Também resolve as limitações na memória da abrir origem R, adicionando o processamento de paralelo e por blocos de dados. Permite-lhe executar a análise de dados muito maior do que o que se adequa na memória principal.  O Visual Studio Comunidade Edition incluído na VM contém as ferramentas de R para extensão do Visual Studio que fornece um IDE completo para trabalhar com R. Também pode transferir e utilizar outros comunicação IDES:, assim como [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Para o desenvolvimento utilizando Python, distribuição de Anaconda Python 2.7 e 3.5 ter sido instalada. Esta distribuição contém o Python base juntamente com cerca de 300 os pacotes de análise de matemática, engenharia e dados mais populares. Pode utilizar as ferramentas de Python para Studio Visual (PTVS) que é instalado dentro da edição de Comunidade de 2015 do Visual Studio ou da comunicação IDES: agrupados com Anaconda como inactivo ou Spyder. Pode iniciar um dos seguintes procedimentos através da procura na barra de pesquisa (**ganham** + tecla**S** ).

>[AZURE.NOTE] Para apontar as ferramentas de Python para Visual Studio Anaconda Python 2.7 e 3.5, é necessário criar ambientes personalizados para cada versão. Para definir destes caminhos de ambiente o Visual Studio 2015 Comunidade Edition, navegue para **Ferramentas** -> **Python ferramentas** -> **Python ambientes** e, em seguida, clique em **+ personalizada**. 

Anaconda Python 2.7 está instalado em C:\Anaconda e Anaconda Python 3.5 está instalado em c:\Anaconda\envs\py35. Consulte a [documentação de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para obter passos detalhados. 

### <a name="jupyter-notebook"></a>Jupyter bloco de notas
Distribuição anaconda também inclui um bloco de notas Jupyter, um ambiente para partilhar código e análise. Um servidor de bloco de notas Jupyter ter sido configurado previamente com Python 2, Python 3 e R kernels. Existe um ícone de ambiente de trabalho com o nome "Jupyter bloco de notas para iniciar o browser para aceder ao servidor de bloco de notas. Se estiver a VM através do ambiente de trabalho remoto, também pode visitar [https://localhost:9999 /](https://localhost:9999/) para aceder ao servidor de bloco de notas Jupyter quando tem sessão iniciada VM.
 
>[AZURE.NOTE] Continue a se obter os avisos de certificado. 

Vamos tiver compactado blocos de notas de exemplo no Python e no R. Os blocos de notas Jupyter mostram como trabalhar com o Microsoft R Server, SQL Server 2016 R Services (analytics na base de dados), Python e outras tecnologias Azure, uma vez que iniciar sessão para Jupyter. Pode ver a ligação para os exemplos na home page do bloco de notas após autenticar ao bloco de notas Jupyter utilizando a palavra-passe que criou num passo anterior. 

### <a name="visual-studio-2015-community-edition"></a>Edição do Visual Studio Comunidade de 2015
Visual Studio Comunidade. a edição instalada na VM. É uma versão gratuita do IDE popular da Microsoft que pode utilizar para fins de avaliação e para pequenas equipas. Pode verificar os termos de licenciamento [aqui](https://www.visualstudio.com/support/legal/mt171547).  Abrir o Visual Studio fazendo duplo clique no ícone de ambiente de trabalho ou no menu **Iniciar** . Também pode procurar programas com **ganham** + **S** e se introduzir "Visual Studio". Uma vez que pode criar projetos no idiomas como c#, Python, R, node.js. Plug-ins também são instalados que a tornam conveniente para funcionar com o Azure serviços como o catálogo de dados do Azure, Azure HDInsight (Hadoop, motores) e Azure dados Lake. 

>[AZURE.NOTE] Poderá receber uma mensagem a informar que o seu período de avaliação expirou. Introduza as suas credenciais de conta Microsoft ou criar uma nova conta gratuita para aceder a Visual Studio Comunidade Edition. 

### <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer edition
Uma versão de programador do SQL Server 2016 com os serviços de R para executar a análise na base de dados é fornecida na VM. Serviços de R fornecem uma plataforma para desenvolver e implementar aplicações inteligentes. Pode utilizar o idioma de R rico e poderoso e os pacotes de muitos da Comunidade para criar modelos e gerar previsões para os seus dados do SQL Server. Pode manter a análise de próximo dos dados porque R serviços (na base de dados) integrar o idioma de R com o SQL Server. Isto elimina os custos e riscos de segurança associados movimento de dados.

>[AZURE.NOTE] A edição de programador do SQL Server 2016 só pode ser utilizada para fins de teste de desenvolvimento e. Tem uma licença para executá-la no produção. 

Pode aceder ao SQL server, iniciando o **SQL Server Management Studio**. Nome do seu VM é povoada como o nome do servidor. Utilize a autenticação do Windows quando tem sessão iniciada como administrador no Windows. Assim que estiver no SQL Server Management Studio pode criar outros utilizadores, criar bases de dados, importar dados e executar consultas de SQL. 

Para permitir a análise na base de dados utilizando o Microsoft R, execute o seguinte comando como uma ação no SQL Server management studio depois de iniciar a sessão como administrador do servidor de tempo. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure 
Várias ferramentas Azure estão instaladas na VM:

- Existe um atalho para aceder a documentação do Azure SDK de ambiente de trabalho. 
- **AzCopy**: utilizado para mover dados e terminar a sua conta de armazenamento do Microsoft Azure. Para ver a utilização, escreva **Azcopy** numa linha de comandos para ver a utilização. 
- **Explorador de armazenamento do Microsoft Azure**: utilizado para navegar pelos objetos que tenham armazenados nos seus dados de conta de armazenamento do Azure e a transferência de armazenamento Azure e para. Pode escreva **Explorador de armazenamento** na pesquisa ou localizá-lo no menu Iniciar do Windows para aceder a esta ferramenta. 
- **Adlcopy**: utilizadas para mover dados para Azure dados Lake. Para ver a utilização, escreva **adlcopy** numa linha de comandos. 
- **dtui**: utilizado para mover dados para e a partir do Azure DocumentDB, uma base de dados NoSQL na nuvem. Escreva **dtui** na linha de comandos. 
- **Microsoft Data Management Gateway**: permite movimento de dados entre as origens de dados no local e na nuvem. É utilizada dentro de ferramentas de como o Azure fábrica de dados. 
- **Microsoft Azure Powershell**: uma ferramenta utilizada para administrar a sua Azure recursos no Powershell linguagem de scripts também é instalada no seu VM. 

###<a name="power-bi"></a>Power BI

Para ajudar a criar dashboards e visualizações excelentes, o **Power BI Desktop** ter sido instalado. Utilize esta ferramenta para solicitar dados de origens diferentes, para criar os relatórios e dashboards e publicá-los na nuvem. Para obter informações, consulte o site do [Power BI](http://powerbi.microsoft.com) . Pode encontrar o ambiente de trabalho do Power BI no menu Iniciar. 

>[AZURE.NOTE] Precisa de uma conta do Office 365 para aceder ao Power BI. 

## <a name="additional-microsoft-development-tools"></a>Ferramentas adicionais de desenvolvimento do Microsoft
A [**Microsoft Web plataforma Installer**](https://www.microsoft.com/web/downloads/platform.aspx) pode ser utilizado para detetar e transferir outras ferramentas de desenvolvimento da Microsoft. Também existe um atalho para a ferramenta fornecida no ambiente de trabalho Microsoft dados ciência Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Diretórios importantes na VM

| Item                          | Diretório |
| ------------------------------| ---------------- |
|Configurações de servidor do bloco de notas Jupyter | C:\ProgramData\jupyter |
|Diretório de amostras Jupyter bloco de notas| c:\dsvm\notebooks|
|Outras amostras | c:\dsvm\samples|
| Anaconda (predefinido: Python 2.7) | c:\Anaconda |
| Ambiente de Python 3.5 anaconda | c:\Anaconda\envs\py35|
|Diretório de instância R servidor autónomo (instância predefinido R) | C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| Directório de instância do servidor de R na base de dados | C:\Program Files\Microsoft SQL Server\MSSQL13. MSSQLSERVER\R_SERVICES |
| Ferramentas de diversas | c:\dsvm\tools|

>[AZURE.NOTE] Instâncias de dados ciência Máquina Virtual da Microsoft criados antes de 1.5.0 (antes de Setembro de 3 de 2016) utilizado numa estrutura de directórios ligeiramente diferente que o especificado na tabela anterior. 

## <a name="next-steps"></a>Próximos passos
Aqui estão alguns passos para continuar a sua aprendizagem e informações detalhadas. 

* Explorar as ferramentas de ciências dados vários na ciência dados VM ao clicar no menu Iniciar e dar saída das ferramentas de listado no menu.
* Navegue para **C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** para obter exemplos utilizando a biblioteca de RevoScaleR R que suporte a análise de dados em escala do enterprise.  
* Leia o artigo: [10 coisas que pode fazer no ciência dados Máquina Virtual](http://aka.ms/dsvmtenthings)
* Saiba como criar soluções analíticas ponto a ponto sistemática utilizando o [Processo de ciências de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visite a [Galeria de informações da empresa Cortana](http://gallery.cortanaintelligence.com) para máquina aprendizagem e dados analytics exemplos que utilizam o conjunto de informações da empresa Cortana. Também fornecemos um ícone no menu **Iniciar** e no ambiente de trabalho da máquina virtual a esta galeria.

