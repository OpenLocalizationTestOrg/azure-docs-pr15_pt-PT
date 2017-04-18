<properties
    pageTitle="Configurar uma máquina de virtual como um servidor de bloco de notas IPython | Microsoft Azure"
    description="Defina o uma Azure Virtual Machine para utilização num ambiente de ciências de dados com o servidor de IPython para análise avançadas."
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
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurar uma máquina virtual Azure como um servidor de IPython bloco de notas para análise avançadas

Este tópico mostra como aprovisionar e configurar uma máquina de virtual Azure para análise avançadas que pode ser utilizados para ser utilizado como parte de um ambiente de ciências de dados. A máquina virtual do Windows está configurada com ferramentas, como tal como o bloco de notas IPython, Explorador de armazenamento do Azure, AzCopy, bem como outros utilitários que são úteis para projetos de análise avançadas de suporte. Azure Explorador de armazenamento e AzCopy, por exemplo, fornecem formas convenientes para carregar os dados ao armazenamento de Blobs do Azure a partir do seu computador local ou para transferi-lo para o seu computador local a partir do armazenamento de Blobs.

## <a name="create-vm"></a>Passo 1: Criar um objetivo geral Azure máquina virtual

Se já tiver uma máquina de virtual Azure e apenas quiser configurar um servidor de IPython bloco de notas no mesmo, pode ignorar este passo e prossiga para [passo 2: adicionar um ponto final para IPython blocos de notas para uma máquina virtual existente](#add-endpoint).

Antes de iniciar o processo de criação de uma máquina virtual no Azure, tem de determinar o tamanho do computador que é necessário para processar os dados para o seu projeto. Máquinas mais pequenas têm menos memória e menos núcleos CPU que máquinas maiores, mas também são menos dispendiosos. Para uma lista dos tipos de máquina e preços, consulte a página <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Máquinas virtuais preços</a>

1. Inicie sessão no <a href="https://manage.windowsazure.com" target="_blank">Portal clássica do Azure</a>e clique em **Novo** no canto inferior esquerdo. Uma será apresentada a janela. Selecione a **Calcular** -> **máquina VIRTUAL** -> **De GALERIA**.

    ![Criar a área de trabalho][24]

2. Selecione uma das seguintes imagens:

    * Centro de dados do Windows Server 2012 R2
    * Experiência do Windows Server Essentials (Windows Server 2012 R2)

    Em seguida, clique na seta apontando para a direita na parte inferior direita para ir para a próxima página de configuração.

    ![Criar a área de trabalho][25]

3. Introduza um nome para a máquina virtual que pretende criar, selecionar o tamanho do computador (predefinido: A3) com base no tamanho dos dados de computador vai processo e poderosas como pretende que o computador para ser (tamanho de memória e o número de cluster núcleos), introduza um nome de utilizador e palavra-passe para o computador. Em seguida, clique na seta a apontar para a direita para ir para a próxima página de configuração.

    ![Criar a área de trabalho][26]

4. Selecione a **Região/AFINIDADE grupo/VIRTUAL rede** que contém a **Conta de armazenamento** que está a planear utilizar para esta máquina virtual e, em seguida, selecione essa conta de armazenamento. Adicione um ponto final à parte inferior do campo **pontos finais** ao introduzir o nome do ponto final ("IPython" aqui). Pode escolher qualquer cadeia como o **nome** do ponto final e qualquer número inteiro entre 0 e 65536 que está **disponível** como a **Porta pública**. A **Porta privado** tem de ser **9999**. Os utilizadores devem **evitar** utilizar portas públicas que já foram atribuídas para serviços de internet. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Portas para serviços de Internet</a> fornece uma lista das portas que lhe foram atribuídos e deve ser evitada.

    ![Criar a área de trabalho][27]

5. Clique na marca de verificação para iniciar o processo de aprovisionamento do virtual machine.

    ![Criar a área de trabalho][28]


Poderá demorar 25 de 15 minutos para concluir o processo de aprovisionamento do virtual machine. Depois de ter sido criada a máquina virtual, deve mostrar o estado de nesta máquina como **em execução**.

![Criar a área de trabalho][29]

## <a name="add-endpoint"></a>Passo 2: Adicionar um ponto final para IPython blocos de notas para uma máquina virtual existente

Se criou a máquina virtual ao seguir as instruções no passo 1, o ponto final para IPython bloco de notas já foi adicionado e este passo pode ser ignorado.

Se já existe a máquina virtual e tem de adicionar um ponto final para IPython bloco de notas que vai instalar no passo 3 abaixo, primeiro registo para o Portal clássica Azure, selecione a máquina virtual e adicionar o ponto final para servidor de IPython bloco de notas. A figura seguinte contém uma captura de ecrã do portal depois do ponto final para IPython bloco de notas foi adicionado a uma máquina de virtual do Windows.

![Criar a área de trabalho][17]

## <a name="run-commands"></a>Passo 3: Instalar o bloco de notas IPython e outras ferramentas de suporte

Quando a máquina virtual estiver criada, utilize o protocolo de ambiente de trabalho remoto (RDP) para iniciar sessão no computador Windows virtual. Para obter instruções, consulte o artigo [como iniciar sessão numa máquina Virtual a executar o Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md). Abra a **linha de comandos** (**não na janela de comando do Powershell**) como um **administrador** e execute o seguinte comando.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Quando a instalação estiver concluída, o servidor de IPython bloco de notas é iniciado automaticamente na *c:\\utilizadores\\\<nome de utilizador\>\\documentos\\IPython blocos de notas* diretório.

Quando lhe for pedido, introduza uma palavra-passe para o bloco de notas IPython e a palavra-passe de administrador do computador. Permite que o bloco de notas IPython executar como um serviço no computador.

## <a name="access"></a>Passo 4: Acesso IPython os blocos de notas a partir de um browser
Para aceder ao servidor IPython bloco de notas, abra uma web browser e entrada *nome de DNS de máquina https://&#60;virtual >: & #60; número de porta público >* na caixa de texto de URL. Aqui, o *& #60; número de porta público >* deve ser o número da porta que especificou quando o ponto final IPython bloco de notas foi adicionado.

O *& #60; máquina virtual nome de DNS >* pode ser encontrado em Portal clássica do Azure. Depois de início de sessão no portal do clássico, clique em **máquinas virtuais**, selecione a máquina que criou e, em seguida, selecione **DASHBOARD**, o nome de DNS será apresentado da seguinte forma:

![Criar a área de trabalho][19]

Vai encontrar um aviso a informar que _existe um problema com o certificado de segurança do Web site_ (Internet Explorer) ou a _sua ligação é não privada_ (Chrome), conforme apresentado nos seguintes valores. Clique em **continuar para este Web site (não recomendado)** (Internet Explorer) ou em **Avançadas** e, em seguida * *prosseguir para & #60;* Nome de DNS*> (não seguras) * * (Chrome) para continuar. Em seguida, introduza a palavra-passe que especificou anteriormente para aceder a blocos de notas IPython.

**Internet Explorer:**
![criar área de trabalho][20]

**Chrome:**
![criar área de trabalho][21]

Depois de iniciar sessão para o bloco de notas IPython, um diretório *DataScienceSamples* mostrará no browser. Este directório contém exemplo IPython blocos de notas partilhados pela Microsoft para ajudar os utilizadores realizar tarefas de ciências de dados. Estes blocos de notas do exemplo IPython são com saída dada a partir do [**repositório de Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) para as máquinas virtuais durante o servidor de bloco de notas IPython configurar o processo. Microsoft mantém e actualiza este repositório frequentemente. Os utilizadores podem visitar o repositório de Github para obter os blocos de notas de IPython de exemplo actualizado mais recentemente.
![Criar a área de trabalho][18]

## <a name="upload"></a>Passo 5: Carregar uma IPython bloco de notas existente a partir de um computador local para o servidor de IPython bloco de notas

Blocos de notas IPython fornecem uma forma fácil de utilizadores carregar um bloco de notas existente IPython nas respectivas máquinas locais para o servidor de IPython bloco de notas em máquinas virtuais. Depois de utilizadores iniciam sessão para o bloco de notas IPython num browser, clique com o **diretório** que o bloco de notas IPython vai ser carregado para. Em seguida, selecione um ficheiro de .ipynb IPython bloco de notas para carregar a partir do computador local no **Explorador de ficheiros**e arraste e largue-a para o diretório IPython bloco de notas no web browser. Clique no botão **carregar** , para carregar o ficheiro .ipynb para o servidor de IPython bloco de notas. Outros utilizadores, em seguida, podem começar a utilizar-na partir do seus browsers.

![Criar a área de trabalho][22]

![Criar a área de trabalho][23]


##<a name="shutdown"></a>Encerramento e retirar a atribuição de máquina virtual quando não estiver em utilização

Azure máquinas virtuais são preços como **pagar apenas para que utiliza**. Para se certificar de que lhe não é a serem faturadas quando não utilizar o seu máquina virtual,-tem de estar no estado de **parado (Deallocated)** quando não estiver em utilização.

> [AZURE.NOTE] Se encerrar máquina virtual do dentro da VM (utilizando as opções de energia do Windows), a VM está parada, mas permanece atribuída. Para se certificar de que não continuar a ser faturada, pare sempre máquinas virtuais a partir do [Azure clássica Portal](http://manage.windowsazure.com/). Também pode deixar a VM através do Powershell ao contactar o **ShutdownRoleOperation** com "PostShutdownAction" igual a "StoppedDeallocated".

Para encerramento e retirar a máquina virtual:

1. Inicie sessão no [Portal clássica Azure](http://manage.windowsazure.com/) utilizando a sua conta.  

2. Selecione **máquinas virtuais** a partir da barra de navegação à esquerda.

3. Na lista de máquinas virtuais, clique no nome do seu máquina virtual, em seguida, aceda à página do **DASHBOARD** .

4. Na parte inferior da página, clique em **encerramento**.

![VM encerramento][15]

A máquina virtual será desatribuída mas não eliminada. Pode reiniciar o seu máquina virtual em qualquer altura a partir do Portal de clássica Azure.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Está pronta a utilizar a VM Azure: o que se segue?

Máquina virtual está agora pronta para utilizar no seu exercícios de ciências de dados. A máquina virtual também está pronta para ser utilizado como um servidor de IPython bloco de notas para informações detalhadas e processamento de dados e outras tarefas em conjunto com o Azure máquina aprendizagem e o processo de ciências de dados de equipa.

Os passos no processo de ciências de dados de equipa são mapeados no [Caminho de formação](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e podem incluir passos que mover dados para o HDInsight, processam e de exemplo-lo lá preparação para a formação dos dados de com Azure máquina de formação.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png
