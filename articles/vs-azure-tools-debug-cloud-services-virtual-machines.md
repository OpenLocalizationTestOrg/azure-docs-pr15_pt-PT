<properties 
    pageTitle="Depuração de um serviço na nuvem Azure ou máquina virtual no Visual Studio | Microsoft Azure"
    description="Depuração de um serviço na nuvem ou Máquina Virtual no Visual Studio"
    services="visual-studio-online"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />
<tags 
    ms.service="visual-studio-online"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/15/2016"
    ms.author="tarcher" />

# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Depuração de um serviço na nuvem Azure ou máquina virtual no Visual Studio

Visual Studio dá-lhe opções diferentes para a depuração de serviços em nuvem Azure máquinas virtuais.



## <a name="debug-your-cloud-service-on-your-local-computer"></a>Depurar o seu serviço de nuvem no seu computador local

Pode poupar tempo e dinheiro utilizando o Azure calcular emulador para depurar o seu serviço de nuvem, num computador local. Por depuração de um serviço localmente antes da implementação, pode melhorar a fiabilidade e desempenho sem paga por hora cluster. No entanto, podem ocorrer algumas erros apenas quando executa um serviço na nuvem no Azure própria. Pode depurar estes erros, se Ativar depuração remota ao publicar o seu serviço e em seguida, anexe o depurador para uma instância de função.

O emulador simula o serviço de calcular Azure e é executado no seu ambiente do local para que possa testar e depurar o seu serviço de nuvem antes da implementação. O emulador trata o ciclo de vida do seu instâncias de função e fornece acesso aos recursos simulados, como o armazenamento local. Quando depurar ou executar o seu serviço a partir do Visual Studio, inicia o emulador automaticamente como uma aplicação de fundo e, em seguida, implementa o seu serviço para o emulador. Pode utilizar o emulador para ver o seu serviço quando é executada no ambiente local. Pode executar a versão completa ou a versão do emulador express. (Começando Azure 2.3, a versão do emulador express é a predefinição.) Consulte o artigo [utilizar emulador Express para executar e depurar um serviço na nuvem localmente](https://msdn.microsoft.com/library/dn339018.aspx).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Depurar o seu serviço de nuvem no seu computador local

1. Na barra de menus, selecione **Depurar**, **Iniciar depuração** para executar o seu projeto do serviço de nuvem Azure. Como alternativa, pode prima F5. Verá uma mensagem que está a iniciar o emulador calcular. Quando o emulador é iniciado, o ícone no tabuleiro do sistema confirma-lo.

    ![Azure emulador no tabuleiro do sistema](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

1. Apresentar a interface de utilizador para o emulador cluster ao abrir o menu de atalho para o ícone do Azure na área de notificação e, em seguida, selecione **Mostrar IU emulador calcular**.

    Painel do lado esquerdo da IU mostra os serviços que estão atualmente implementados para o emulador cluster e as instâncias de funções que cada serviço está em execução. Pode escolher o serviço ou funções de administrador a apresentar o ciclo de vida, registo e informações de diagnóstico no painel direito. Se a colocar o foco na margem superior de uma janela incluída, expande-se para preencher o painel do lado direito.

1. Passo através da aplicação ao selecionar os comandos no menu **Depurar** e definir pontos de interrupção no seu código. Como passo através da aplicação no depurador, os painéis são atualizados com o estado atual da aplicação. Quando para de depuração, é eliminada a implementação de aplicações. Se a aplicação inclui uma função de web e tiver definido a propriedade de ação de arranque para iniciar o browser, o Visual Studio inicia a aplicação web no browser. Se alterar o número de ocorrências de uma função na configuração do serviço, deve parar o serviço de nuvem e em seguida, reinicie a depuração de modo a que pode depurar estas novas instâncias da função.

    **Nota:** Quando para de executar ou depuração seu serviço, o local cluster emulador e emulador de armazenamento não estão parou. Tem de pará-los explicitamente da área de notificação.


## <a name="debug-a-cloud-service-in-azure"></a>Depuração de um serviço na nuvem no Azure

Para depurar um serviço na nuvem a partir de um computador remoto, tem de ativar essa funcionalidade explicitamente quando implementar o seu serviço de nuvem para que obrigatório serviços (msvsmon.exe, por exemplo) estão instalados nas máquinas virtuais que executar as instâncias de função. Se não Ativar depuração remota quando publicado no serviço, tem de voltar a publicar o serviço com depuração remota ativado.

Se ativar a depuração remota para um serviço na nuvem, não apresentar um desempenho degradado ou implicam taxas adicionais. Não deva utilizar depuração remota um serviço de produção, uma vez que os clientes que utilizem o serviço poderão ser adverso afectados.

>[AZURE.NOTE] Durante a publicação de um serviço na nuvem a partir do Visual Studio, pode ativar a **IntelliTrace** para qualquer funções desse serviço que o .NET Framework 4 ou o 4,5 Framework .NET de destino. Ao utilizar **IntelliTrace**, pode examinar eventos que ocorreram no passado uma instância de função e reproduza o contexto partir desse momento. Consulte o artigo [depuração de um serviço de nuvem publicados com IntelliTrace e Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) e [Utilizar IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Para ativar a depuração remoto para um serviço na nuvem

1. Abrir o menu de atalho para o projeto Azure e, em seguida, selecione **Publicar**.

1. Selecione o ambiente de **teste** e a configuração **de depuração** .

    Este é apenas uma orientação. Pode optar por permitir para executar os ambientes de teste num ambiente de produção. No entanto, adverso poderá afetar os utilizadores se ativar a depuração remota no ambiente de produção. Pode escolher a configuração da versão, mas a configuração de depuração torna mais fácil de depuração.

    ![Escolha a configuração de depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

1. Siga os passos habituais, mas selecione a caixa de verificação **Ativar depurador remoto para todas as funções** no separador **Definições avançadas** .

    ![Depurar configuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Para anexar o depurador num serviço na nuvem no Azure

1. No Explorador do servidor, expanda o nó do seu serviço de nuvem.

1. Abrir o menu de atalho para a função ou instância de função à qual pretende anexar e, em seguida, selecione **Anexar Depurador**.

    Se depurar uma função, o Visual Studio debugger liga a cada instância dessa função. Irá interromper o depurador num ponto de interrupção para a primeira instância de função que é executado nessa linha de código e condições qualquer esse ponto de interrupção. Se depurar uma instância, anexa o depurador apenas a essa instância e as quebras de um ponto de interrupção apenas quando essa instância específica é executado nessa linha de código e condições do ponto de interrupção.

    ![Anexar Depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

1. Depois do depurador anexa numa instância, depurar como costuma fazer. O depurador anexa automaticamente para o processo do anfitrião adequado para o seu perfil. Consoante o que é a função, o depurador anexa w3wp.exe, WaWorkerHost.exe ou WaIISHost.exe. Para verificar o processo ao qual o depurador está ligado, expanda o nó instância no Explorador do servidor. Consulte o artigo [Arquitetura de função Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) para obter mais informações sobre os processos Azure.

    ![Selecione a caixa de diálogo Tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Para identificar os processos à qual o depurador está ligado, abra a caixa de diálogo de processos por, na barra de menus, escolher depurar, Windows, processos. (Teclado: Ctrl + Alt + Z) Para desanexar um processo específico, abra o respectivo menu de atalho e, em seguida, selecione **Desanexar processo**. Ou, localize o nó instância no Explorador do servidor, localize o processo, abrir o menu de atalho e, em seguida, selecione **Desanexar processo**.

    ![Processos de depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

>[AZURE.WARNING] Evitar marcas longas em pontos de interrupção quando remoto depuração. Azure trata um processo que está parado durante mais de alguns minutos como responder e deixa de enviar tráfego para essa instância. Se deixar de durante muito tempo, msvsmon.exe separa-se do processo.

Para desanexar depurador a partir de todos os processos no seu instância ou função, abrir o menu de atalho para a função ou instância que está a depuração e, em seguida, selecione **Desanexar depurador**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Limitações da depuração remota no Azure

A partir do Azure SDK 2.3, depuração remota tem as seguintes limitações.

- Com a depuração remota ativado, não é possível publicar um serviço na nuvem na qual qualquer função tem mais do que 25 instâncias.

- O depurador utiliza portas 30400 para 30424, 31400 para 31424 e 32400 para 32424. Se tentar utilizar qualquer um dos seguintes portas, não poderá publicar o seu serviço e uma das seguintes mensagens de erro serão apresentados no registo de atividade Azure: 

    - Erro ao validar o ficheiro .cscfg relativamente ao ficheiro .csdef. 
    O porta reservada intervalo 'intervalo' para o ponto final Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector da função 'função' sobrepõe-se com uma porta já definida ou um intervalo.
    - Falha na atribuição. Volte a tentar mais tarde, tentar reduzir o tamanho da memória virtual ou o número de ocorrências de função ou tente implementar para uma região diferente.


## <a name="debugging-azure-virtual-machines"></a>Depuração máquinas virtuais Azure

Pode depurar programas que abertos no Azure máquinas virtuais através do Explorador de servidor no Visual Studio. Quando ativa a depuração remota num computador virtual Azure, Azure instala a extensão de depuração remoto na máquina virtual. Em seguida, pode anexar a processos na máquina virtual e depurar como faria normalmente.

>[AZURE.NOTE] Máquinas virtuais criadas através da pilha de Gestor de recursos Azure pode possível remotamente depurar através do Explorador da nuvem no Visual Studio 2015. Para mais informações, consulte o artigo [Gerir o Azure recursos com o Explorador de nuvem](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Depurar uma máquina virtual Azure

1. No Explorador do servidor, expanda o nó máquinas virtuais e selecione o nó da máquina virtual ao qual pretende depurar.

1. Abra o menu de contexto e selecione **Ativar depuração**. Quando lhe for perguntado se tiver a certeza se pretender ativar a depuração na máquina virtual, selecione **Sim**.

    Azure instala a extensão de depuração remoto na máquina virtual para ativar a depuração.

    ![Comando depuração de ativar máquina virtual](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Registo de atividade Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Depois da extensão de depuração remoto concluir a instalação, abra o menu de contexto a máquina virtual e selecione **Anexar depurador...**

    Azure obtém uma lista dos processos na máquina virtual e mostra-los na anexar à caixa de diálogo do processo.

    ![Comando depurador anexar](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. Na caixa de diálogo **anexar para processo** , selecione **Selecionar** para limitar a lista de resultados para mostrar apenas os tipos de código que pretende depurar. Pode depurar código de 32 ou 64 bits gerido, código nativo ou ambas.

    ![Selecione a caixa de diálogo Tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Selecione os processos que pretende depurar na máquina virtual e, em seguida, selecione **anexar**. Por exemplo, poderá escolher o processo de w3wp.exe se pretendia depurar uma aplicação web na máquina virtual. Para mais informações, consulte [depurar um ou mais processos no Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) e [Azure arquitetura de função](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) .

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Criar um projeto de web e uma máquina virtual para depuração

Antes de publicar o seu projeto Azure, poderá achar útil para testá-la num ambiente contidas que suporta depurar e testar cenários e onde pode instalar o teste e programas de monitorização. Uma forma para fazer isto é depurar remotamente a sua aplicação numa máquina virtual.

Projetos do visuais Studio ASP.NET fornecem uma opção para criar uma máquina virtual útil que pode utilizar para testar a aplicação. A máquina virtual inclui os pontos finais frequentemente necessário como PowerShell, ambiente de trabalho remoto e WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Para criar um projeto de web e uma máquina virtual para depuração

1. No Visual Studio, crie uma nova aplicação de Web do ASP.NET.

1. Na caixa de diálogo novo projeto de ASP.NET, na secção Azure, selecione **Máquina Virtual** na caixa de lista pendente. Deixe a caixa de verificação **Criar remotos recursos** selecionada. Selecione **OK** para continuar.

    É apresentada a caixa de diálogo **Criar máquina de virtual no Azure** .


    ![Criar a caixa de diálogo de projeto de web do ASP.NET](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Nota:** Vai ser-lhe pedido para iniciar sessão na sua conta Azure se ainda não iniciou sessão.

1. Selecione as diferentes definições para a máquina virtual e, em seguida, clique **em OK**. Para mais informações, consulte [máquinas virtuais]( http://go.microsoft.com/fwlink/?LinkId=623033) .

    O nome que introduziu para nome de DNS será o nome da máquina virtual. 

    ![Criar máquina virtual na caixa de diálogo Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure cria a máquina virtual e, em seguida, disposições e configura os pontos finais, como o ambiente de trabalho remoto e implementar Web



1. Depois da máquina virtual está totalmente configurada, selecione nó a máquina virtual no Explorador do servidor.

1. Abra o menu de contexto e selecione **Ativar depuração**. Quando lhe for perguntado se tiver a certeza se pretender ativar a depuração na máquina virtual, selecione **Sim**. 

    Azure instala a extensão de depuração remoto à máquina virtual para ativar a depuração.

    ![Comando depuração de ativar máquina virtual](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Registo de atividade Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

1. Publicar o projeto, conforme descrito no [como: implementar um Web utilizando com um clique de publicação do projecto no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Porque pretende depurar na máquina virtual, na página **Definições** do Assistente de **Publicar Web** , selecione **Depurar** , como a configuração. Isto torna-se de que os símbolos de código estão disponíveis durante a depuração.

    ![Definições de publicação](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

1. Nas **Opções de publicar de ficheiros**, selecione **Remover ficheiros adicionais ao destino** se o projeto já foi implementado numa hora anterior.

1. Depois do projeto publica, no menu de contexto a máquina virtual no Explorador do servidor, selecione **Anexar depurador...**

    Azure obtém uma lista dos processos na máquina virtual e mostra-los na anexar à caixa de diálogo do processo.

    ![Comando depurador anexar](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

1. Na caixa de diálogo **anexar para processo** , selecione **Selecionar** para limitar a lista de resultados para mostrar apenas os tipos de código que pretende depurar. Pode depurar código de 32 ou 64 bits gerido, código nativo ou ambas.

    ![Selecione a caixa de diálogo Tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

1. Selecione os processos que pretende depurar na máquina virtual e, em seguida, selecione **anexar**. Por exemplo, poderá escolher o processo de w3wp.exe se pretendia depurar uma aplicação web na máquina virtual. Consulte o artigo [depurar um ou mais processos no Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) para obter mais informações.

## <a name="next-steps"></a>Próximos passos

- Utilize **Intellitrace** para recolher um registo das chamadas e eventos de um servidor de lançamento. Consulte o artigo [depuração de um serviço de nuvem publicados com IntelliTrace e o Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).
- Utilize os **Diagnósticos do Azure** para registar informações detalhadas de execução de código num funções, se estiver a executar as funções no ambiente de desenvolvimento ou no Azure. Consulte o artigo [recolher dados utilizando os diagnósticos do Azure de registo](http://go.microsoft.com/fwlink/p/?LinkId=400450).
