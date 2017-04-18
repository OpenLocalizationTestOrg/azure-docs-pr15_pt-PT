<properties
    pageTitle="O que é o Azure automatização | Microsoft Azure"
    description="Saiba que valor fornece de automatização do Azure e obter respostas a perguntas frequentes para que pode começar a criar, utilizar runbooks e Azure automatização DSC."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="o que é a automatização, automatização azure e exemplos de automatização azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# <a name="azure-automation-overview"></a>Descrição geral de automatização Azure

Microsoft Azure automatização fornece uma forma para os utilizadores automatizar as tarefas manuais, execução longa, passíveis de erro e frequentemente repetidas que são executadas frequentemente num ambiente na nuvem e enterprise. Poupa tempo e aumenta a fiabilidade das tarefas administrativas normais e até mesmo agenda-los a ser executado automaticamente a intervalos regulares. Pode automatizar processos utilizando runbooks ou automatizar a gestão de configuração utilizando a configuração de estado pretendido. Este artigo fornece breve descrição geral de automatização do Azure e responde a algumas perguntas comuns. Pode referir-se para outros artigos nesta biblioteca para obter informações mais detalhadas sobre os tópicos diferentes.


## <a name="automating-processes-with-runbooks"></a>Automatizar processos com runbooks

Um livro de execuções é um conjunto de tarefas que efetuar algumas processo automatizado no Azure automatização. Pode ser um processo simples como iniciar uma máquina virtual e criar uma entrada de registo ou poderá ter um livro de execuções complexo que combina outras runbooks mais pequeno para executar um processo complexo através de vários recursos ou até mesmo vários nuvens e em ambientes local.  

Por exemplo, pode poderá ter um processo manual existente para uma base de dados do SQL a truncar se está a chegar tamanho máximo que inclui vários passos como ligar ao servidor de ligação à base de dados, obter o tamanho da base de dados atual, verifique se tiver excedido limiar e, em seguida, truncá-lo e notifique utilizador. Em vez de executar cada um destes passos manualmente, podia criar um livro de execuções seria executar todas estas tarefas como um único processo. Seria iniciar o livro de execuções, forneça as informações necessárias, como o nome do servidor SQL, o nome da base de dados e o correio electrónico do destinatário e, em seguida, sentar novamente enquanto o processo for concluído. 


## <a name="what-can-runbooks-automate"></a>O que pode automatizar runbooks?

Runbooks no Azure automatização são baseados no Windows PowerShell ou fluxo de trabalho do Windows PowerShell, para que fazem tudo o que pode fazer PowerShell. Se uma aplicação ou serviço tem uma API, em seguida, um livro de execuções pode trabalhar com o mesmo. Se tiver um módulo do PowerShell para a aplicação, pode carregar esse módulo no Azure automatização e incluir esses cmdlets no seu livro de execuções. Azure automatização runbooks executar na nuvem Azure e pode aceder a qualquer nuvem recursos ou recursos externos que podem ser acedidos a partir da nuvem. Utilizar o [Trabalho de livro execuções híbrido](automation-hybrid-runbook-worker.md), pode executar runbooks no Centro de dados locais para gerir recursos locais. 


## <a name="getting-runbooks-from-the-community"></a>Obter runbooks a partir da Comunidade

A [Galeria de livro execuções](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contém runbooks da Microsoft e da Comunidade pode utilizar inalterada no seu ambiente ou personalizá-los para os seus próprios fins. Também são úteis para como referências para aprender a criar o seu próprio runbooks. Ainda podem contribuir o seu próprio runbooks na Galeria de que acha que outros utilizadores podem ser úteis. 


## <a name="creating-runbooks-with-azure-automation"></a>Criar Runbooks com automatização Azure 

Pode [criar o seu próprio runbooks](automation-creating-importing-runbook.md) do zero ou modificar runbooks a partir da [Galeria de livro execuções](http://msdn.microsoft.com/library/azure/dn781422.aspx) para os seus próprios requisitos. Existem três [tipos de livro execuções](automation-runbook-types.md) diferentes que pode escolher a partir com base no seu requisitos e uma experiência PowerShell. Se preferir trabalhar diretamente com o código do PowerShell, em seguida, pode utilizar um [livro de execuções do PowerShell](automation-runbook-types.md#powershell-runbooks) ou [livro de execuções do fluxo de trabalho de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) que editar offline ou com o [editor de textual](http://msdn.microsoft.com/library/azure/dn879137.aspx) no portal do Azure. Se preferir editar um livro de execuções sem expostos o código subjacente, em seguida, pode criar um [livro de execuções gráfico](automation-runbook-types.md#graphical-runbooks) utilizando o [editor de gráfico](automation-graphical-authoring-intro.md) no portal do Azure. 

Prefere está a assistir a leitura? Ter um olhar sobre a por baixo do vídeo a partir do Microsoft Ignite sessão de Maio de 2015. Nota: Enquanto os conceitos e funcionalidades de outros fabricantes referidas neste vídeo estão corretas, que Azure automatização tem muitas progresso desde que foi registado neste vídeo,-lo agora tem uma IU mais extensa no portal do Azure e suporta funcionalidades adicionais.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## <a name="automating-configuration-management-with-desired-state-configuration"></a>Gestão de configuração de automatização com a configuração de estado pretendido 

[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) é uma plataforma de gestão que permite-lhe gerir, implementar e impor configuração para máquinas virtuais utilizando uma sintaxe PowerShell declarativa e anfitriões físicas. Pode definir configurações num central DSC separar servidor que máquinas destino podem recuperar e aplicar automaticamente. DSC fornece um conjunto de cmdlets do PowerShell que pode utilizar para gerir as configurações e recursos.  

[DSC de automatização do Azure](automation-dsc-overview.md) é uma solução baseada na nuvem para DSC de PowerShell que fornece serviços que são necessários para os ambientes de empresa.  Pode gerir os seus recursos DSC no Azure automatização e aplicar as configurações ao virtuais ou físicas máquinas que obtê-los a partir de um servidor de separar DSC na nuvem Azure.  Também fornece serviços de elaboração de relatórios que informam-o de eventos importantes, tal como quando nós irregulares a partir da sua configuração atribuído e quando uma nova configuração de ser aplicado. 


## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Criar o seus próprio configurações de DSC com a automatização do Azure

[Configurações de DSC](automation-dsc-overview.md#azure-automation-dsc-terms) especifique o estado de um nó pretendido.  Vários nós podem aplicar a mesma configuração para garantir que todos mantêm um estado idêntico.  Pode criar uma configuração utilizando qualquer texto editor no seu computador local e, em seguida, importá-lo para automatização Azure onde pode compilá-lo e aplicá-la nós.


## <a name="getting-modules-and-configurations"></a>Introdução módulos e configurações 

Pode obter [módulos de PowerShell](automation-runbook-gallery.md#modules-in-powershell-gallery) que contém os cmdlets do que pode utilizar nos seus runbooks e configurações DSC a partir da [Galeria do PowerShell](http://www.powershellgallery.com/). Pode iniciar este Galeria a partir do portal do Azure e importar módulos diretamente para o Azure automatização ou pode transferir e importá-los manualmente. Não é possível instalar os módulos diretamente a partir do portal do Azure, mas pode transferi-los instalá-las, tal como faria com qualquer outro módulo. 


## <a name="example-practical-applications-of-azure-automation"></a>Aplicações práticas de exemplo de automatização do Azure 

Seguem-se apenas alguns exemplos de quais são os tipos de cenários de automatização com a automatização Azure. 

* Crie e copie máquinas virtuais no diferentes subscrições do Azure. 
* Agendar cópias de ficheiros a partir de um computador local a um contentor de armazenamento de Blobs do Azure. 
* Automatize funções de segurança, tal como recusar pedidos de um cliente quando é detetado um ataque de negação de serviço. 
* Certifique-se de que máquinas continuamente alinharem com a política de segurança configurados.
* Faça a gestão contínua implementação do código da aplicação na horizontal na nuvem e no infraestrutura local. 
* Crie uma floresta do Active Directory no Azure para o seu ambiente de laboratório. 
* Trunca uma tabela numa base de dados SQL se DB se está a aproximar tamanho máximo. 
* Actualize remotamente ambiente as definições para um Web site Azure. 


## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Como é que Azure automatização se relacionam com outras ferramentas de automatização?

[Serviço de gestão de automatização (SMA)](http://technet.microsoft.com/library/dn469260.aspx) destina-se para automatizar tarefas de gestão de na nuvem privada. É instalado localmente no Centro de dados como um componente do [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). SMA e Azure automatização utilizam o mesmo formato Livro execuções baseado no Windows PowerShell e fluxo de trabalho do Windows PowerShell, mas SMA não suporta [runbooks gráfica](automation-graphical-authoring-intro.md).  

[Sistema Centro 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) destina-se para a automatização de recursos no local. Utiliza um formato de livro de execuções diferentes automatização do Azure e automatização de gestão de serviço e tem uma interface gráfica para criar runbooks sem necessidade de qualquer scripting. Respetivo runbooks são compostos por atividades de pacotes de integração escritas especificamente para Orchestrator. 


## <a name="where-can-i-get-more-information"></a>Onde posso obter mais informações? 

Estão disponíveis para mais informações acerca de automatização do Azure e criar o seu próprio runbooks uma variedade de recursos. 

* **Biblioteca de automatização do Azure** é onde se neste momento. Os artigos nesta biblioteca fornecem documentação completa sobre a configuração e administração de automatização do Azure e para a sua própria runbooks de criação. 
* [Os cmdlets do Azure PowerShell](http://msdn.microsoft.com/library/jj156055.aspx) fornece informações para automatizar operações Azure através do Windows PowerShell. Runbooks utilizar estes cmdlets, para trabalhar com recursos Azure. 
* [Gestão de blogue](https://azure.microsoft.com/blog/tag/azure-automation/) fornece as informações mais recentes sobre automatização do Azure e outras tecnologias de gestão da Microsoft. Deve subscrever este blogue, para se manter atualizado com as mais recentes da equipa do Azure automatização. 
* [Fórum de automatização](http://go.microsoft.com/fwlink/p/?LinkId=390561) permite-lhe publicar perguntas acerca da automatização Azure endereçados pela Microsoft e a Comunidade de automatização. 
* [Cmdlets de automatização do Azure](https://msdn.microsoft.com/library/mt244122.aspx) fornece informações para automatizar tarefas de administração. Contém cmdlets para gerir contas de automatização, recursos, runbooks, DSC.


## <a name="can-i-provide-feedback"></a>Pode fornecer comentários? 

**Forneça comentários!** Se está a procurar uma solução de livro execuções Azure automatização ou um módulo de integração, publique um pedido de Script no Centro de Script. Se tiver comentários ou funcionalidade pedidos para Azure automatização, publique-os no [Voz do utilizador](http://feedback.windowsazure.com/forums/34192--general-feedback). Obrigado! 


