<properties
    pageTitle="O que é uma máquina de Virtual ciência dados? | Microsoft Azure"
    description="Saiba os cenários chaves, funcionalidades e como começar com dados ciência máquinas virtuais, um ambiente e toolkit pronto para análise."
    keywords="ferramentas de ciências de dados, dados ciência virtual máquina, ferramentas para ciência de dados, linux ciência de dados"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="bradsev" />


# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introdução ao baseado na nuvem dados ciência Máquina Virtual para Linux e Windows

A Máquina Virtual da ciência dados é uma imagem VM personalizada Azure na nuvem do Microsoft criada especificamente para efetuar ciência de dados. Tiver muitos ciência de dados populares e outras ferramentas de pré-instalado e configurado previamente começar rapidamente a criação de aplicações inteligentes para análise avançadas. Está disponível no Windows Server 2012 ou em versões com base em OpenLogic 7.2 CentOS Linux. 

Este tópico explica o que pode fazer com a VM de ciências de dados, descreve alguns dos cenários principais para utilizar a VM, itemizes principais funcionalidades disponíveis nas versões do Windows e Linux e fornece instruções sobre como começar a utilizá-los.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>O que posso fazer com ciência Virtual Machine dados?

É o objetivo da ciência Virtual Machine dados fornecer profissionais de dados em todos os níveis de competências e funções com um ambiente do ciência fricção de royalties dados. Este VM poupa-lhe tempo considerável que iria despendeu se tinha lançadas num ambiente comparável sozinho. Em vez disso, inicie o projeto de ciências dados imediatamente numa instância VM recentemente criada. 

A VM de ciências de dados está concebida e configurada para trabalhar com um cenários de utilização abrangentes. Pode dimensionar o seu ambiente para cima ou para baixo que o projeto necessidades mudam. Está podem utilizar o idioma preferido às tarefas de ciências de dados do programa. Pode instalar outras ferramentas e personalizar o sistema para as suas necessidades exatas.
 
## <a name="key-scenarios"></a>Alguns dos cenários principais
Esta secção sugere alguns cenários chaves para o qual a VM de ciências de dados pode ser implementada.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Ambiente de trabalho de análise pré-configurado na nuvem

A VM de ciências dados fornece uma configuração do plano base para as equipas de ciências de dados está à procura para substituir os respetivos ambientes de trabalho locais com um ambiente de trabalho geridos na nuvem. Linha base garante que todos os cientistas de dados de uma equipa têm uma configuração consistente com as quais quer verificar experiências e promover a colaboração. Reduz também os custos reduzindo os encargos sysadmin e guardando o tempo necessário para avaliar, instalar e manter vários pacotes de software necessários para fazer a análise avançadas.  

### <a name="data-science-training-and-education"></a>Formação de ciências de dados e educação

Formadores de empresa e professores que ensinar ciência dados classes normalmente fornecem uma imagem de máquina virtual para garantir que os seus alunos têm uma configuração consistente e que as amostras trabalhar seja previsível. A VM de ciências dados cria um ambiente a pedido com uma configuração consistente que facilita os desafios incompatibilidade e suporte. Casos onde estes ambientes precisa de ser criado com frequência, especialmente para o endereço mais curtos classes de formação, beneficiam substancialmente.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade de flexível a pedido para projetos em grande escala

Dados ciência hackathons/competições ou modelação de dados em grande escala e informações detalhadas exigem dimensionada saída capacidade de hardware, normalmente de curta duração. A VM de ciências de dados pode ajudar a criar uma réplica do ambiente de ciências dados rapidamente a pedido, em servidores com escala saída que permitem experiências que exige o recursos informáticos tecnicamente desenvolvidos para ser executado.

### <a name="short-term-experimentation-and-evaluation"></a>Pioneira curto e avaliação

A VM de ciências dados podem ser utilizadas para avaliar ou saiba ferramentas, como o Microsoft R Server, SQL Server, ferramentas do Visual Studio, Jupyter, profundas aprendizagem / ML conjuntos de ferramentas e novas ferramentas populares na Comunidade com mínimas esforço de configuração. Uma vez que a VM de ciências de dados pode ser configurada rapidamente, podem ser aplicada em outras situações de utilização curto prazo como replicação experiências publicadas, executar demos, tutoriais seguintes sessões online ou a tutoriais de conferência.


## <a name="whats-included-in-the-data-science-vm"></a>O que está incluído na VM ciência dados?

A Máquina Virtual da ciência dados tem muitas ferramentas de ciências populares dados já instalado e configurado. Também inclui ferramentas que tornam mais fácil trabalhar com vários produtos de dados e análise Azure. Pode explorar e construir modelos aspeto do Office nos conjuntos de dados em grande escala utilizando o servidor da Microsoft R ou utilizando o SQL Server 2016. Um anfitrião de outras ferramentas da Comunidade abrir origem e da Microsoft também estão incluído, bem como amostra código e blocos de notas. A tabela seguinte itemizes e compara os componentes principais incluídos nas edições do Windows e Linux da ciência Virtual Machine dados.


|**Windows Edition** | **Linux Edition** |
|----------------|---------------|
|Microsoft R Server Developer Edition | Microsoft R Server Developer Edition |
|Anaconda Python 2.7, 3.5 | Anaconda Python 2.7, 3.5 |
|Servidor de Jupyter bloco de notas (R, Python) | JupyterHub: Jupyter de vários utilizadores blocos de notas (R, Python, Paula) |
|SQL Server 2016 Developer Edition: Análise de dimensionável na base de dados com os serviços de R | Postgres, exemplo SQL (ferramenta de bases de dados), controladores do SQL Server e linha de comandos (bcp, sqlcmd) |
|Comunidade do Visual Studio Edition 2015 (IDE) </br> -Azure HDInsight (Hadoop), Lake de dados, ferramentas de dados do SQL Server </br> -Ferramentas Node.js, Python e R para Visual Studio |Editores e comunicação IDES: </br> --Eclipse com Plug-in do Azure toolkit </br> -Emacs (com ESS, auctex) gedit |
|Ambiente de trabalho do Power BI | -- |
|Formação máquinas-ferramentas </br> -Integração com o Azure máquina aprendizagem </br> -CNTK (abrangente aprendizagem/AI) </br> -Xgboost (popular ML ferramenta competições de ciências dados) </br> -Vowpal Wabbit (aprendiz rapidamente online) </br> -Rattle (dados de guia visuais e ferramenta de análise) </br> -Mxnet (abrangente aprendizagem/AI) | Formação máquinas-ferramentas </br> -Integrações com formação de máquina Azure </br> -CNTK (abrangente aprendizagem/AI) </br> -Xgboost (popular ML ferramenta competições de ciências dados) </br> -Vowpal Wabbit (aprendiz rapidamente online) </br> -Rattle (dados de guia visuais e ferramenta de análise)  |
| SDK para aceder ao Azure e Cortana conjunto de informações da empresa de serviços | SDK para aceder ao Azure e Cortana conjunto de informações da empresa de serviços |
| Ferramentas para movimento de dados e gestão dos recursos Azure e Big Data: Explorador de armazenamento do Azure, clip, PowerShell, AdlCopy (Azure dados Lake), AzCopy, dtui (para DocumentDB), Microsoft Data Management Gateway | Ferramentas para movimento de dados e gestão dos recursos Azure e Big Data: Explorador de armazenamento do Azure, clip |
| Git, plug-in do Visual Studio Team Services | Git |
| Porta de Windows do mais populares utilitários de linha de comandos de Linux/Unix acessíveis através de GitBash/linha de comandos | -- |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Como começar com a VM de ciências de dados do Windows

- Crie uma instância da VM no Windows, navegue a [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) e selecione o botão **Criar Máquina Virtual** verde.
- Inicie sessão na VM a partir do seu ambiente de trabalho remoto utilizando as credenciais que especificou quando criou a VM.
- Para descobrir e iniciação ferramentas disponíveis, clique no menu **Iniciar** .


## <a name="get-started-with-the-linux-data-science-vm"></a>Começar a trabalhar com a VM de ciências Linux dados

- Crie uma instância da VM no Linux (baseado em OpenLogic CentOS) ao navegar a [esta página](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) e selecionar o botão **Criar Máquina Virtual** .
- Inicie sessão na VM a partir de um cliente SSH, tal como betumes ou SSH comando, utilizando as credenciais que especificou quando criou a VM.
- Na linha de comandos shell, introduza as informações de mais de dsvm.
- Para um ambiente de trabalho gráfica, transferir o cliente de X2Go para a sua plataforma de cliente [aqui](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) e siga as instruções no documento Linux dados ciência VM [aprovisionar o Linux dados ciência Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).


## <a name="next-steps"></a>Próximos passos

### <a name="for-the-windows-data-science-vm"></a>Para a Windows dados ciência VM

- Para mais informações sobre como executar específicas ferramentas disponíveis na versão do Windows, consulte o artigo [aprovisionar o Microsoft dados ciência Virtual Machine](machine-learning-data-science-provision-vm.md) e
-  Para mais informações sobre como realizar várias tarefas necessárias para o seu projeto de ciências de dados na VM do Windows, consulte o artigo [dez que pode fazer no ciência dados Máquina Virtual](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Para dados Linux ciência VM

- Para mais informações sobre como executar específicas ferramentas disponíveis na versão Linux, consulte o artigo [aprovisionar o Linux dados ciência Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md).
- Para obter instruções sobre que mostra-lhe como realizar várias tarefas de ciências dados comuns com a VM Linux, consulte o artigo [ciência de dados no Máquina Virtual Linux dados ciência](machine-learning-data-science-linux-dsvm-walkthrough.md).
