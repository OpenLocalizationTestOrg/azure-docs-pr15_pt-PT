<properties 
   pageTitle="Testar o desempenho de um serviço na nuvem | Microsoft Azure"
   description="Teste o desempenho de um serviço de nuvem utilizando o Gestor de perfis do Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
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


# <a name="testing-the-performance-of-a-cloud-service"></a>Testar o desempenho de um serviço na nuvem 

##<a name="overview"></a>Descrição geral

Pode testar o desempenho de um serviço na nuvem das seguintes formas:

- Utilize os diagnósticos do Azure para recolher informações sobre ligações e os pedidos de e para rever as estatísticas dos sites que mostram como o serviço executa a partir de uma perspetiva de cliente. Para começar com, consulte o artigo [Configurar diagnósticos para serviços em nuvem Azure e máquinas virtuais]( http://go.microsoft.com/fwlink/p/?LinkId=623009).

- Utilize o Gestor de perfis do Visual Studio para obter uma análise aprofundada dos aspectos utilizaria da forma como o serviço é executado. Como este tópico descreve, pode utilizar o Gestor de perfis para medir o desempenho como um serviço é executado no Azure. Para obter informações sobre como utilizar o Gestor de perfis para medir o desempenho como um serviço é executado localmente num cluster emulador, consulte o artigo [testar o desempenho de um Azure nuvem serviço localmente a calcular emulador ao utilizar o Gestor de perfis Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## <a name="choosing-a-performance-testing-method"></a>Escolher um método de testar de desempenho

###<a name="use-azure-diagnostics-to-collect"></a>Utilize os diagnósticos do Azure para recolher:###

- Estatísticas sobre páginas web ou serviços, como os pedidos e ligações.

- Estatísticas sobre funções, tal como a frequência for reiniciada uma função.

- Em geral sobre a utilização de memória, tal como a percentagem de tempo que demora o lixo ou a memória conjunto de informações de uma função em execução.

###<a name="use-the-visual-studio-profiler-to"></a>Utilize o Gestor de perfis do Visual Studio para:###

- Determine quais funções demoram mais tempo.

- Meça quanto tempo demora de cada parte de um programa inviabiliza intenso.

- Compare relatórios de desempenho detalhadas para duas versões de um serviço.

- Analise alocação de memória em maior detalhe do que o nível de atribuições de memória individuais.

- Analise os problemas de simultaneidade no código multithreaded.

Quando utilizar o Gestor de perfis, pode recolher dados quando um serviço na nuvem é executado localmente ou no Azure.

###<a name="collect-profiling-data-locally-to"></a>Recolha dados perfis localmente para:###

- Teste o desempenho de uma parte de um serviço de nuvem, como a execução da função de trabalho específicos, o que não necessita de um carregamento simulado real.

- Teste o desempenho de um serviço na nuvem isoladamente, em condições controladas.

- Teste o desempenho de um serviço na nuvem antes de implementar Azure.

- Teste o desempenho de um serviço na nuvem em privado, sem perturbar as implementações existentes.

- Teste o desempenho do serviço de sem incorrer em custos para executar no Azure.

###<a name="collect-profiling-data-in-azure-to"></a>Recolha dados de perfis no Azure para:###

- Teste o desempenho de um serviço na nuvem num caso de carga simulado ou real.

- Utilize o método de instrumentação de recolha de dados de perfis, como este tópico descreve mais tarde.

- Teste o desempenho do serviço no mesmo ambiente como quando o serviço é executado de produção.

Normalmente, a simular uma carga para condições de limite ou serviços em nuvem teste em normal.

## <a name="profiling-a-cloud-service-in-azure"></a>Criação de perfis de um serviço na nuvem no Azure

Quando publicar o seu serviço de nuvem a partir do Visual Studio, pode o serviço de perfil e especificar as definições de perfis dar-lhe as informações que pretende. É iniciada uma sessão de perfis para cada instância de uma função. Para mais informações sobre como publicar o seu serviço a partir do Visual Studio, consulte o artigo [publicação para um serviço em nuvem Azure a partir do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Para compreender mais informações sobre a criação de perfis de desempenho no Visual Studio, consulte o artigo [Guia de principiantes para criação de perfis de desempenho](https://msdn.microsoft.com/library/azure/ms182372.aspx) e [Analisar o desempenho da aplicação ao utilizar ferramentas de criação de perfis](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE] Pode ativar a IntelliTrace ou criação de perfis quando publicar o seu serviço de nuvem. Não é possível ativar ambos.

###<a name="profiler-collection-methods"></a>Métodos de recolha de gerador de perfis

Pode utilizar métodos de coleções de sites diferente para a criação de perfis, com base no seu problemas de desempenho:

- **Recolha de CPU** - este método recolhe as estatísticas de aplicação que são úteis para uma análise de problemas de utilização da CPU inicial. CPU amostragem é o método sugerido para iniciar a maior parte dos investigações de desempenho. Existe um impacto baixo sobre a aplicação que são perfis quando recolher dados de amostragem CPU.

- **Instrumentação** -este método recolhe dados de temporização detalhada que são útil para uma análise com o foco e para analisar os problemas de desempenho de entrada/saída. O método de instrumentação registos cada entrada, sair e chamada de função das funções num módulo durante uma criação de perfis executar. Este método é útil para recolher informações de temporização detalhada sobre uma secção do seu código e para compreender o impacto das operações de entrada e saídas no desempenho da aplicação. Este método é desativado para um computador com um sistema operativo de 32 bits. Esta opção só está disponível quando executa o serviço de nuvem no Azure, não localmente no emulador de cluster.

- **Alocação de memória .NET** - este método recolhe dados de alocação de memória do .NET Framework utilizando a criação de perfis de método de amostragem. Os dados recolhidos incluem o número e o tamanho de objetos atribuídos.

- **Simultaneidade** - este método recolhe recurso contenção os dados e processo e tópico execução que é útil para analisar aplicações multithreaded e com múltiplos processos. O método de simultaneidade recolhe dados para cada evento que execução blocos do código, tal como quando um tópico aguarda bloqueado acesso a um recurso de aplicação para ser disponibilizado. Este método é útil para analisar aplicações multithreaded.

- Também pode ativar a **Criação de interação perfis de camadas**, que fornece informações adicionais sobre os tempos de execução de ADO.NET síncrono chama-se de funções várias camadas de aplicações de que comunicam com um ou mais bases de dados. É possível recolher dados de interação camada com qualquer um dos métodos perfis. Para mais informações sobre a criação de interação perfis de camadas, consulte o artigo [Camada interações vista](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Configurar definições de perfis

A ilustração seguinte mostra como configurar as definições de perfis da caixa de diálogo Publicar aplicação Azure.

![Configurar definições de criação de perfis](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE] Para permitir que a caixa de verificação **Ativar a criação de perfis** , tem de ter o Gestor de perfis instalado no computador local que está a utilizar para publicar o seu serviço de nuvem. Por predefinição, o Gestor de perfis é instalado quando instalar o Visual Studio.

### <a name="to-configure-profiling-settings"></a>Para configurar definições de perfis

1. No Explorador de solução, abra o menu de atalho para o seu projeto Azure e, em seguida, selecione **Publicar**. Para obter passos detalhados sobre como publicar um serviço na nuvem, consulte [publicar uma nuvem do serviço utilizando as ferramentas de Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. Na caixa de diálogo **Publicar aplicação Azure** , escolha o separador de **Definições avançadas** .

1. Para ativar a criação de perfis, selecione a caixa de verificação **Ativar a criação de perfis** .

1. Para configurar as definições de perfis, selecione a hiperligação **Definições** . É apresentada a caixa de diálogo Definições de perfil.

1. A partir dos botões de opção **que método de criação de perfis pretende utilizar** , escolha o tipo de criação de perfis de que precisa.

1. Para recolher os dados de perfis de interação camada, selecione a caixa de verificação **Permitir criação de interação perfis camada** .

1. Para guardar as definições, selecione o botão **OK** .

    Quando publicar esta aplicação, estas definições são utilizadas para criar a criação de perfis sessão para cada função.

## <a name="viewing-profiling-reports"></a>Ver relatórios de criação de perfis

É criada uma sessão de perfis para cada instância de uma função no seu serviço de nuvem. Para ver os seus relatórios perfis de cada sessão a partir do Visual Studio, pode ver a janela do Explorador de servidor e, em seguida, selecione o nó Azure calcular para selecionar uma instância de uma função. Em seguida, pode ver o relatório perfis conforme apresentado na seguinte ilustração.

![Ver perfis relatório a partir do Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Ver relatórios de perfis

1. Para ver a janela do Explorador de servidor no Visual Studio, na barra de menus escolha vista, servidor Explorer.

1. Escolha o nó calcular Azure e, em seguida, escolha o nó de implementação do Azure para o serviço de nuvem que selecionou ao perfil quando publicado do Visual Studio.

1. Ver relatórios de perfis de uma instância, selecione o papel no serviço, abrir o menu de atalho de uma instância específica e, em seguida, selecione **Relatório de criação de perfis de vista**.

    O relatório, um ficheiro de .vsp, agora é transferido do Azure e o estado de transferência é apresentada no registo de atividade de Azure. Quando a transferência terminar, o criação de perfis é apresentado um relatório num separador no editor do Visual Studio denominada <Role name> _<Instance Number>_ <identifier>.vsp. Dados de resumo para o relatório é apresentada.

1. Para visualizar diferentes vistas do relatório, na lista vista atual, selecione o tipo de vista que pretende. Para mais informações, consulte o artigo [Vistas de relatório de ferramentas de criação de perfis](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Próximos passos

[Depuração de serviços em nuvem](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicar num serviço de nuvem Azure a partir do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

