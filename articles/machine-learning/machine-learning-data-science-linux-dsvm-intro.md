<properties
    pageTitle="Aprovisionar o Linux dados ciência Virtual Machine | Microsoft Azure"
    description="Configurar e crie uma máquina de Virtual Linux dados ciência no Azure fazer: análise e formação de computador."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />

# <a name="provision-the-linux-data-science-virtual-machine"></a>Aprovisionar a Máquina Virtual da ciência Linux dados

Máquina Linux dados ciência Virtual é uma máquina de virtual Azure que vem com um conjunto de ferramentas de pré-instalados. Estas ferramentas são utilizadas frequentemente para efetuar análises de dados e formação de computador. Os componentes de software chave incluídos são:

- Microsoft R Server Developer Edition
- Distribuição de Python anaconda (versões 2.7 e 3.5), incluindo bibliotecas de análise de dados mais populares
- JupyterHub - um servidor de bloco de notas de Jupyter vários utilizadores suporte R, Python, kernels Paula
- Explorador de armazenamento Azure
- Azure interface da linha de comandos (CLI) para gerir os recursos Azure
- PostgresSQL base de dados
- Formação máquinas-ferramentas
    - [Toolkit de rede utilizaria (CNTK)](https://github.com/Microsoft/CNTK): uma profundidade toolkit de software da Microsoft Research de formação.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): uma máquina fast sistema que suporte técnicas como online, hashing, allreduce, reduções, learning2search, ativo, de formação e aprendizagem interativa.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): uma ferramenta de implementação rápida e precisas aumentado da árvore de fornecer.
    - [Rattle](http://rattle.togaware.com/) (o R Analytical ferramenta para saber facilmente): uma ferramenta que facilita a introdução ao automática R fácil, com informações detalhadas de dados com base em interface gráfica de formação e de modelação com geração de código R automática e de análise de dados.
- Azure SDK em Java, Python, node.js, Rubi, PHP
- Bibliotecas no R e Python para utilizam no Azure máquina formação e outros serviços do Azure
- Ferramentas e de desenvolvimento editores (Eclipse, Emacs, gedit, vi)

Efetuar ciência dados envolve iteração de uma sequência de tarefas:

1. Localizar, carregamento e pré-processamento de dados
2. Criar e testar modelos
3. Implementar modelos para consumo nas aplicações inteligentes

Cientistas dados utilizam várias ferramentas para concluir as seguintes tarefas. Pode ser bastante demorado para localizar as versões do software adequadas e, em seguida, para transferir, compilar e instalar estes versões.

Linux dados ciência Virtual Machine podem facilitar os encargos substancialmente. Utilizá-lo a começar rapidamente o seu projeto de análise. Permite-lhe trabalhar em tarefas em diversos idiomas, incluindo R, Python, SQL, Java e C++. Eclipse fornece um IDE para desenvolver e testar o seu código que seja fácil de utilizar. O SDK do Azure incluído na VM permite-lhe criar aplicações ao utilizar vários serviços no Linux para a plataforma de nuvem da Microsoft. Além disso, tiver acesso a outros idiomas como Rubi, Perl, PHP e node.js que também são instaladas previamente.

Não existem sem taxas de software para esta imagem VM de ciências de dados. Pode pagar apenas as taxas de utilização de Azure hardware são avaliadas com base em tamanho da máquina virtual que aprovisionar com uma imagem em VM. Obter mais detalhes sobre as taxas de cluster podem encontrar na [VM listar página no Azure Marketplace ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).


## <a name="prerequisites"></a>Pré-requisitos

Pode criar uma máquina de Virtual Linux dados ciência, tem de ter o seguinte procedimento:

- **Azure uma subscrição**: para obter um, consulte o artigo [versão de avaliação gratuita do Azure obter](https://azure.microsoft.com/free/).
- **Azure uma conta de armazenamento**: para criar uma, consulte o artigo [criar uma conta de armazenamento Azure](storage-create-storage-account.md#create-a-storage-account). Em alternativa, a conta de armazenamento pode ser criada como parte do processo de criação de VM, se não pretender utilizar uma conta existente.


## <a name="create-your-linux-data-science-virtual-machine"></a>Criar máquina Linux dados ciência Virtual

Eis os passos para criar uma instância de Máquina Virtual Linux dados ciência:

1.  Navegue para a máquina virtual listar no [portal do Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.   Clique em **Criar** (na parte inferior) para ativar o assistente. ![configurar-dados-ciência-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.   As secções seguintes fornecem as entradas para cada um dos passos do assistente (enumerado à direita da figura anterior) utilizadas para criar o Microsoft dados ciência Virtual Machine. Eis as entradas necessárias para configurar a cada um dos seguintes passos:

    um. **Noções básicas**:

  - **Nome**: nome do seu servidor de ciências de dados está a criar.
  - **Nome de utilizador**: primeira conta de início de sessão no ID.
  - **Palavra-passe**: primeira conta palavra-passe (que pode utilizar chave pública SSH em vez de palavra-passe).
  - **Subscrição**: Se tiver mais do que uma subscrição, selecione a uma no qual o computador está a ser criado e faturada. Tem de ter privilégios de criação de recursos para esta subscrição.
  - **Grupo de recursos**: pode criar um novo ou utilizar um grupo existente.
  - **Localização**: selecione o Centro de dados que é o mais adequado. Normalmente, é o Centro de dados com a maioria dos seus dados, ou que está mais próximo da sua localização física para o mais rápido acesso de rede.

    b. **Tamanho**:

  - Selecione um dos tipos de servidor que cumpre os requisitos funcionais e restrições de custo. Selecione **Ver todos** para ver mais opções de tamanhos VM.

    c. **Definições**:

  - **Tipo de disco**: escolha **Premium** se preferir numa unidade de estado sólido (SSD). Caso contrário, selecione **padrão**.
  - **Conta de armazenamento**: pode criar uma nova conta de armazenamento Azure na sua subscrição ou utilize um já existente na mesma localização que foi escolhido no passo **Noções básicas** do assistente.
  - **Outros parâmetros**: na maioria dos casos, utilizar apenas os valores predefinidos. A ter em conta os valores que não sejam predefinidos, Paire o cursor sobre a ligação informativa para obter ajuda sobre os campos específicos.

    d. **Resumo**:

  - Certifique-se de que todas as informações introduzidas estão corretas.

    "e". **Comprar**:

  - Para iniciar o aprovisionamento, clique em **comprar**. Uma ligação é fornecida para os termos da transação. A VM não tem qualquer taxas adicionais para além das cluster para o tamanho do servidor que escolheu no passo **tamanho** .

O aprovisionamento deve ter cerca de 10 a 20 minutos. O estado do aprovisionamento é apresentado no portal do Azure.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Como aceder ao máquina Linux dados ciência Virtual

Quando a VM estiver criada, pode iniciar sessão para a mesma utilizando SSH. Utilize as credenciais de conta que criou na secção **Noções básicas** do passo 3 para a interface da shell de texto. No Windows, pode transferir uma ferramenta de cliente SSH como [betumes](http://www.putty.org). Se preferir um ambiente de trabalho gráfica (X sistema do Windows), pode utilizar X11 reencaminhamento de chamadas no betumes ou instalar o cliente do X2Go.

>[AZURE.NOTE] O cliente X2Go executado significativamente melhor do X11 reencaminhamento na testes. Recomendamos que utilize o cliente X2Go para uma interface gráfica de ambiente de trabalho.


## <a name="installing-and-configuring-x2go-client"></a>Instalar e configurar o cliente de X2Go

A VM Linux já está aprovisionada com X2Go servidor e preparado para aceitar as ligações de cliente. Para ligar ao ambiente de trabalho Linux VM estruturador, faça o seguinte no seu cliente:

1. Transfira e instale o cliente de X2Go para a sua plataforma do cliente da [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Executar o cliente X2Go e selecione **Nova sessão**. Esta é aberta uma janela de configuração com vários separadores. Introduza os parâmetros de configuração seguintes:
    * **Separador sessão**:
        - **Anfitrião**: O nome do anfitrião ou o endereço IP do seu VM de ciências dados Linux.
        - **Início de sessão**: nome de utilizador na VM Linux.
        - **Porta de SSH**: deixá-la com 22, o valor predefinido.
        - **Tipo de sessão**: Altere o valor para XFCE. Atualmente a VM Linux apenas suporta o ambiente de trabalho XFCE.
    * **Separador de multimédia**: pode desativar som suporte e cliente impressão se não precisa de utilizá-los.
    * **Pastas partilhadas**: Se pretender directórios a partir do seu computadores cliente instalados na VM Linux, adicionar os directórios do computador cliente que pretende partilhar com a VM neste separador.

Após iniciar sessão para a VM utilizando o cliente SSH ou XFCE gráfica ao ambiente de trabalho através do cliente X2Go, está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. No XFCE, pode ver atalhos do menu aplicações e ícones de ambiente de trabalho para muitas das ferramentas.


## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Ferramentas instaladas no Máquina Virtual Linux dados ciência

### <a name="microsoft-r-open"></a>Abrir o Microsoft R
R é um dos idiomas mais populares de análise de dados e aprendizagem de máquina. Se pretender utilizar R para sua análise, a VM tem Microsoft R abrir (MRO) com a biblioteca de Kernel matemática (MKL). O MKL optimiza operações matemáticas comuns no algoritmos analíticos. MRO é 100 por cento compatível com CRAN-R e qualquer uma das bibliotecas de R publicadas no CRAN pode ser instalado no MRO. Pode editar os programas de R dos editores predefinido, como vi, Emacs ou gedit. Também pode transferir e utilizar outros comunicação IDES:, tal como [RStudio](http://www.rstudio.com). Para sua comodidade, é fornecido um script simple (installRStudio.sh) no diretório **/dsvm/tools** que instala RStudio. Se estiver a utilizar o editor de Emacs, nota que o Emacs compactar ESS (Emacs Speaks estatísticas das), simplifica a trabalhar com ficheiros de R dentro do editor de Emacs, foi pré-instalada.

Para iniciar o R, escreva apenas **R** na shell de. Isto leva-o para um ambiente interativo. Para desenvolver o seu programa de R, normalmente, utilize um editor como Emacs ou vi ou gedit e, em seguida, execute os scripts dentro R. Se instalar RStudio, tem um ambiente do IDE gráfico completa para desenvolver o seu programa de R.

Também existe um script R que pode instalar os [pacotes de início 20 R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) se pretender. Este script pode ser executado depois de serem na interface de interativo R, que pode ser introduzido (tal como mencionado) ao escrever **R** na shell de.  

### <a name="python"></a>Python
Para o desenvolvimento utilizando Python, distribuição de Anaconda Python 2.7 e 3.5 ter sido instalada. Esta distribuição contém o Python base juntamente com cerca de 300 os pacotes de análise de matemática, engenharia e dados mais populares. Pode utilizar os editores de texto predefinido. Além disso, pode utilizar Spyder, um IDE Python que está agrupado com distribuições Anaconda Python. Spyder necessita de um ambiente de trabalho gráfica ou X11 reencaminhamento de chamadas. Um atalho para Spyder é fornecido no ambiente de trabalho do gráfico.

Uma vez que temos Python 2.7 e 3.5, tem de ativar especificamente a versão Python pretendida que pretende trabalhar na sessão actual. O processo de ativação define a variável de caminho para a versão de Python pretendida.

Para ativar Python 2.7, execute o seguinte partir da shell:

    source /anaconda/bin/activate root

Python 2.7 está instalado em */anaconda/bin*.

Para ativar Python 3.5, execute o seguinte partir da shell:

    source /anaconda/bin/activate py35


Python 3.5 é instalado ao */anaconda/envs/py35/bin*.

Para invocar uma sessão de interactiva Python, basta escreva **python** a shell de. Se estiver a uma interface gráfica ou tiver X11 conjunto de reencaminhamento para cima, pode escrever **spyder** para iniciar o IDE Python.

### <a name="jupyter-notebook"></a>Jupyter bloco de notas

A distribuição Anaconda também inclui um bloco de notas Jupyter, um ambiente para partilhar código e análise. O bloco de notas Jupyter é acedido através de JupyterHub. Sessão com o seu nome de utilizador Linux local e a palavra-passe.

O servidor de bloco de notas Jupyter ter sido configurado previamente com Python 2, Python 3 e R kernels. Existe um ícone de ambiente de trabalho com o nome "Jupyter blocos de notas" para iniciar o browser para aceder ao servidor de bloco de notas. Se estiver a VM através do cliente SSH ou X2Go, também pode visitar [https://localhost:8000 /](https://localhost:8000/) para aceder ao servidor de bloco de notas Jupyter.

>[AZURE.NOTE] Continue a se obter os avisos de certificado.

Pode aceder ao servidor de bloco de notas Jupyter a partir de qualquer anfitrião. Basta escrever *https://\<VM DNS nome ou endereço IP\>: 8000 /*

>[AZURE.NOTE] Porta 8000 é aberta na firewall por predefinição quando a VM está aprovisionada.

Vamos tiver compactado blocos de notas de exemplo – Python numa e outra em R. Pode ver a ligação para os exemplos na home page do bloco de notas após autenticar ao bloco de notas Jupyter utilizando o seu nome de utilizador Linux local e a palavra-passe. Pode criar um novo bloco de notas ao selecionar **Novo**e, em seguida, o kernel de idiomas apropriado. Se não vir o botão **Novo** , clique no ícone de **Jupyter** no canto superior esquerdo para ir para a home page do servidor de bloco de notas.


### <a name="ides-and-editors"></a>Editores e comunicação IDES:

Tem uma opção de várias editores de código. Isto inclui vi/VIM, Emacs, gEdit e Eclipse. gEdit Eclipse são editores gráficas e precisa de ter sessão iniciada no ambiente de trabalho gráfica utilizá-las. Estes editores tem ambiente de trabalho e a aplicação atalhos do menu Iniciação-los.

**VIM** e **Emacs** são editores baseado em texto. Emacs, podemos tiver instalado um pacote de suplemento denominado Emacs Speaks estatísticas (ESS) que facilita a trabalhar com R no editor do Emacs. Podem encontrar mais informações em [ESS](http://ess.r-project.org/).

**Eclipse** é uma origem abrir, IDE extensible que suporta vários idiomas. A edição de programadores Java é a instância instalada a VM. Plug-ins estão disponíveis para vários idiomas populares que podem ser instalados para expandir o ambiente Eclipse. Também temos um plug-in instalado no Eclipse denominado **Toolkit de Azure para Eclipse**. Permite-lhe criar, desenvolver, testar e implementar aplicações Azure utilizando o ambiente de desenvolvimento Eclipse que suporte idiomas como Java. Também existe uma **Azure SDK para Java** que permita aceder ao diferentes serviços a partir do Azure dentro de um ambiente do Java. Podem encontrar mais informações sobre o Azure toolkit para Eclipse no [Azure Toolkit para Eclipse](../azure-toolkit-for-eclipse.md).

**Látex** está instalado através da embalagem texlive juntamente com um pacote de [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) suplemento Emacs, qual simplifica a criação de documentos látex dentro Emacs.  

### <a name="databases"></a>Bases de dados

#### <a name="postgres"></a>Postgres
A base de dados de origem abrir **Postgres** está disponível no VM, com os serviços em execução e initdb já concluída. Ainda precisa de criar bases de dados e os utilizadores. Para mais informações, consulte a [documentação de Postgres](https://www.postgresql.org/docs/).  

####  <a name="graphical-sql-client"></a>Gráfico SQL client
**SQL de exemplo**, um cliente SQL gráfico, foi fornecido para ligar a bases de dados diferentes (como o Microsoft SQL Server, Postgres e MySQL) e executar consultas de SQL. Pode executar esta a partir de uma sessão de ambiente de trabalho gráfica (utilizando o cliente de X2Go, por exemplo). Para invocar SQL de exemplo, pode iniciá-la a partir do ícone no ambiente de trabalho ou execute o seguinte comando na shell.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Antes da primeira utilização, configure os controladores e os aliases da base de dados. Os controladores JDBC estão localizados na:

*/usr/share/Java/jdbcdrivers*

Para mais informações, consulte o artigo [SQL de exemplo](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas de linha de comandos para aceder ao Microsoft SQL Server

O pacote de controladores ODBC para SQL Server também é fornecido com duas ferramentas de linha de comandos:

**BCP**: em volume utilitário bcp copia os dados entre uma instância do Microsoft SQL Server e um ficheiro de dados num formato especificado pelo utilizador. O utilitário bcp pode ser utilizado para importar grandes quantidades de novas linhas para tabelas do SQL Server ou para exportar dados de tabelas para ficheiros de dados. Para importar dados para uma tabela, tem de utilizar um ficheiro de formato criado para essa tabela ou compreender a estrutura da tabela e os tipos de dados que são válidos para as suas colunas.

Para mais informações, consulte o artigo [ligar com bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SQLCMD**: pode introduzir instruções Transact-SQL com a sqlcmd utility, bem como procedimentos de sistema e ficheiros na linha de comandos de script. Este utilitário utiliza ODBC para executar lotes Transact-SQL.

Para mais informações, consulte o artigo [ligar com sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

>[AZURE.NOTE] Existem algumas diferenças deste utilitário entre plataformas Linux e Windows. Consulte a documentação para obter detalhes.


#### <a name="database-access-libraries"></a>Bibliotecas de acesso de base de dados

Existem bibliotecas disponível no R e Python a bases de dados do access.

- No R, o pacote **RODBC** ou o pacote de **dplyr** permite-lhe de consulta ou executar instruções SQL no servidor de base de dados.
- No Python, a biblioteca de **pyodbc** fornece acesso de base de dados com ODBC como a camada subjacente.  

Para aceder às **Postgres**:

- A partir de r: Utilize o pacote **RPostgreSQL**.
- A partir do Python: Utilize a biblioteca de **psycopg2** .


### <a name="azure-tools"></a>Ferramentas Azure
As seguintes ferramentas Azure são instaladas na VM:

- **Interface de comandos Azure**: O clip do Azure permite-lhe criar e gerir recursos Azure através de comandos da shell. Para invocar as ferramentas de Azure, basta escreva **Ajuda azure**. Para mais informações, consulte a [página de documentação do clip Azure](../virtual-machines-command-line-tools.md).
- **Explorador de armazenamento do Microsoft Azure**: Explorador de armazenamento do Microsoft Azure é uma ferramenta gráfica que é utilizada para navegar pelos objetos que tenham armazenados na sua conta de armazenamento Azure e para carregar e transferir dados de blobs do Azure e para. Pode aceder a Explorador de armazenamento a partir do ícone de atalho do ambiente de trabalho. Pode invocá-lo a partir de um pedido de shell escrevendo **StorageExplorer**. Tem de ter a sessão iniciada a partir de um cliente X2Go ou se tiver X11 conjunto de reencaminhamento para cima.
- **Bibliotecas de Azure**: seguem-se algumas das bibliotecas de pré-instaladas.

 - **Python**: bibliotecas relacionados com o Azure Python que é instaladas são **azure**, **azureml**, **pydocumentdb**e **pyodbc**. Com as três primeiras bibliotecas, pode aceder a serviços de armazenamento Azure, Azure máquina aprendizagem e Azure DocumentDB (uma NoSQL base de dados no Azure). Biblioteca de quarta, pyodbc (juntamente com o Microsoft controlador ODBC para SQL Server), ativa o acesso do SQL Server, base de dados do SQL Azure e armazém de dados do SQL Azure a partir do Python utilizando uma interface ODBC. Introduza **lista pip** para ver todas as bibliotecas listadas. Certifique-se de que executa este comando em 3,5 ambientes e o 2.7 Python.
 - **R**: bibliotecas relacionados com o Azure no R que são instaladas são **AzureML** e **RODBC**.
 - **Java**: A lista de bibliotecas do Azure Java pode ser encontrada no diretório **/dsvm/sdk/AzureSDKJava** na VM. As bibliotecas de chaves são Azure gestão de armazenamento e de APIs, DocumentDB e JDBC controladores para o SQL Server.  

Pode aceder ao [Azure portal](https://portal.azure.com) a partir do browser Firefox pré-instalado. No portal do Azure, pode criar, gerir e monitorizar recursos Azure.

### <a name="azure-machine-learning"></a>Formação de máquina Azure

Azure máquina formação é um serviço de nuvem totalmente geridas que permite-lhe construir, implementar e partilhar soluções de análise de aspeto do Office. Construir a sua experiências e os modelos da partir do Azure máquina aprendizagem Studio. Pode ser acedido a partir de um browser na máquina dados ciência virtual acedendo a [Formação do Microsoft Azure máquina](https://studio.azureml.net).

Depois de iniciar sessão no Azure máquina de formação Studio, tem acesso a uma tela pioneira qual pode criar um fluxo de lógico para o algoritmos de aprendizagem automática. Também tiver acesso a um bloco de notas Jupyter alojado no Azure máquina aprendizagem e pode trabalhar de forma totalmente integrada com as experiências no máquina aprendizagem Studio. Operationalize a modelos de ter criado por moldagem-los na interface de serviço web de aprendizagem automática. Permite que os clientes escritos em qualquer linguagem invocar previsões a partir de modelos de aprendizagem automática. Para mais informações, consulte a [documentação de máquina aprendizagem](https://azure.microsoft.com/documentation/services/machine-learning/).

Pode também criar os modelos de R ou Python na VM e, em seguida, implementá-los em produção no Azure máquina aprendizagem. Vamos ter instalado a bibliotecas no R (**AzureML**) e Python (**azureml**) para ativar esta funcionalidade.

Para obter informações sobre como implementar modelos de R e Python para Azure máquina formação, consulte o artigo [dez que pode fazer no ciência dados Máquina Virtual](machine-learning-data-science-vm-do-ten-things.md) (em particular, a secção "Criar modelos que utilizem R ou Python e Operationalize-los com o Azure formação de máquina").

>[AZURE.NOTE] Estas instruções foram escritas para a versão do Windows da VM ciência dados. Mas as informações fornecidas sobre como implementar modelos para Azure máquina formação existe aplicáveis a VM Linux.

### <a name="machine-learning-tools"></a>Formação máquinas-ferramentas

A VM vem com alguns máquina algoritmos que tenham sido previamente compilados e pré-instalados localmente e ferramentas de formação. Estes incluem:

* **CNTK** (Utilizaria Toolkit de rede do Microsoft Research): uma profundidade toolkit de formação.
* **Vowpal Wabbit**: um algoritmo de aprendizagem rapidamente online.
* **xgboost**: uma ferramenta que fornece algoritmos otimizada, aumentado árvore.
* **Python**: Anaconda Python fornecido juntamente com algoritmos de aprendizagem máquina com bibliotecas como saber Scikit. Pode instalar outras bibliotecas utilizando o `pip install` comando.
* **R**: uma biblioteca de funções de aprendizagem automática avançada está disponível para R. Algumas das bibliotecas que estão pré-instalada são lm, glm, randomForest, rpart. Outras bibliotecas podem ser instaladas ao executar:

        install.packages(<lib name>)

Eis algumas informações adicionais sobre as ferramentas de formação de máquina três primeira na lista.

#### <a name="cntk"></a>CNTK
Esta é uma fonte abrir, abrangente toolkit de formação. É uma ferramenta de linha de comandos (cntk) e já está a ser o caminho.

Para executar uma amostra básica, execute os seguintes comandos na shell de:

    # Copy samples to your home directory and execute cntk
    cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
    cd cntkdemo/Data
    cntk configFile=../Config/Simple.cntk

O resultado de modelo está no *~/cntkdemo/Output/Models*.

Para mais informações, consulte a secção CNTK de [GitHub](https://github.com/Microsoft/CNTK)e [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).


#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit é uma máquina sistema que utiliza técnicas de allreduce online, hashing, reduções, learning2search, ativo, de formação e aprendizagem interativa.

Para executar a ferramenta de um exemplo de muito básica, faça o seguinte:

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Existem outras, maiores demos nesse directório. Para mais informações sobre VW, consulte o artigo [nesta secção da GitHub](https://github.com/JohnLangford/vowpal_wabbit)e [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Esta é uma biblioteca que foi concebida e optimizada para algoritmos aumentado (árvore). Objectivo desta biblioteca é transmitir os limites de cálculo do máquinas para os extremos necessários para fornecer árvore em grande escala aumentar que é dimensionáveis, portáteis e precisa.

É fornecido como uma linha de comandos, bem como uma biblioteca de R.

Para utilizar esta biblioteca no R, pode iniciar uma sessão de R interativa (apenas escrevendo **R** na shell) e carregar a biblioteca.

Eis um exemplo simples que pode executar no pedido de R:

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Para executar a linha de comandos xgboost, aqui estão os comandos para executar na shell de:

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Um ficheiro de .model destina-se para o directório especificado. Pode encontrar informações sobre este exemplo de demonstração [sobre GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Para mais informações sobre xgboost, consulte a [página de documentação xgboost](https://xgboost.readthedocs.org/en/latest/)e respectivo [Github repositório](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Roca
Roca (a **R** **A**nalytical **T**tinta **T**the **L**ganhar **"e"**asily) utiliza informações detalhadas de dados com base em interface gráfica e de modelação. Apresenta estatísticos e visuais resumos dos dados, as transformações que podem ser facilmente modelados, constrói deficiente e controlados modelos de dados, apresenta o desempenho do modelos graficamente, e conjuntos de dados nova pontuações. Também gera código R, replicação as operações na interface de utilizador que podem ser executar diretamente no R ou utilizadas como ponto de partida para análise mais aprofundada.

Para executar roca, tem de ser numa gráfica ambiente de trabalho de entrada sessão. No terminal, escreva ```R``` para introduzir o ambiente de R. Na linha de comandos R, introduza os seguintes comandos:

    library(rattle)
    rattle()

Agora uma interface gráfica disponibilizado com um conjunto de separadores. Eis os passos de guia de introdução no roca necessária para utilizar um conjunto de dados do exemplo meteorologia e construa um modelo. Em alguns dos passos abaixo, lhe for pedido para instalar e carregar algumas pacotes R necessários que não estejam no sistema automaticamente.

>[AZURE.NOTE] Se não tiver acesso a instalar o pacote no diretório sistema (a predefinição), poderá ver uma linha de comandos na janela de consola do R para instalar pacotes à sua biblioteca pessoal. Se vir estes pedidos de resposta *y* .

1. Clique em **Executar**.
2. Uma caixa de diálogo é apresentado, perguntar se pretender utilizar o conjunto de dados da meteorologia de exemplo. Clique em **Sim** para carregar o exemplo.
3. Clique no separador **modelo** .
4. Clique em **Executar** para criar uma árvore de decisões.
5. Clique em **Desenhar** para apresentar a árvore de decisões.
6. Clique no botão de opção **floresta** e clique em **Executar** para criar uma floresta aleatória.
7. Clique no separador **avaliar** .
8. Clique no botão de opção **risco** e clique em **Executar** para apresentar duas representações de desempenho de risco (cumulativo).
9. Clique no separador de **registo** para mostrar o código de gerar R para as operações anteriores.
(Devido a um erro na versão atual do roca, necessitar de inserir uma *#* carácter à frente de *exportar este registo …* no texto do registo.)
10. Clique no botão **Exportar** para guardar o ficheiro de script de R *weather_script com o nome. R* para a pasta principal.

Pode sair roca e R. Agora pode modificar o script R gerado ou utilizá-lo, tal como está executá-la em qualquer altura para repetir tudo o que foi efetuado dentro IU Rattle. Especialmente para principiantes no R, este é uma forma fácil de fazer análise e máquina aprendizagem numa interface gráfica simple, enquanto automaticamente a gerar código em R para modificar e/ou Saiba rapidamente.


## <a name="next-steps"></a>Próximos passos
Eis como pode continuar a sua aprendizagem e informações detalhadas:

* Tutorial [ciência de dados no Máquina Virtual Linux dados ciência](machine-learning-data-science-linux-dsvm-walkthrough.md) mostra-lhe como efetuar várias tarefas comuns de ciências dados com a VM de ciências Linux dados aprovisionado aqui. 
* Explore as várias ferramentas de ciências de dados no ciência dados VM por experimentar as ferramentas que é descritas neste artigo. Também pode executar *dsvm-mais-informações* sobre a shell de máquina virtual para uma introdução básica e ponteiros para obter mais informações sobre as ferramentas de instalado na VM.  
* Saiba como criar soluções analíticas ponto a ponto sistemática utilizando o [Processo de ciências de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visite a [Galeria de análise de Cortana](http://gallery.cortanaanalytics.com) para máquina aprendizagem e dados analytics exemplos que utilizam o conjunto de aplicações de análise de Cortana.
