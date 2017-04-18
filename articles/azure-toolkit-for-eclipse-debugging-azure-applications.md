<properties
    pageTitle="Depuração de aplicações do Azure no Eclipse"
    description="Saiba mais sobre as aplicações de Azure depuração utilizando o Toolkit de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# <a name="debugging-azure-applications-in-eclipse"></a>Depuração de aplicações do Azure no Eclipse #

Utilizar o Toolkit de Azure para Eclipse, pode depurar as suas aplicações se que estiver a executar no Azure ou no emulador de cluster se estiver a utilizar o Windows como o sistema operativo. A imagem seguinte mostra as propriedades de **depuração de** caixa de diálogo utilizada para ativar a depuração remota:

![][ic719504]

Neste tutorial assume que já tem uma aplicação que foi criada com êxito e está familiarizado com o emulador cluster e implementar a Azure.

Vamos utilizar a aplicação a partir do tutorial [utilizando a biblioteca de tempo de execução do Azure Service no JSP][] como ponto de partida para este tópico. Antes de continuar, crie dessa aplicação se ainda não o tiver feito.

## <a name="to-debug-your-application-while-running-in-azure"></a>Para depurar sua aplicação ao executar no Azure ##

>[AZURE.WARNING] Suporte de actual o toolkit para depuração de Java remota destina-se principalmente para implementações a ser executada em emulador de cluster Azure. Uma vez que a ligação depuração não estiver segura, não deve activar depuração remota nas implementações de produção. Se precisar de uma aplicação em execução na nuvem Azure de depuração, utilize uma implementação transição, mas aperceba que acesso não autorizado a sua sessão de depuração é possível se alguém conhecer o endereço IP da sua implementação na nuvem, mesmo se for uma transição implementação.

1. Criar o seu projeto para testar o emulador: Project Explorer do Eclipse com sessão no, com o botão direito **MyAzureProject**, clique em **Propriedades**, clique em **Azure**e definir **Construir para** a **implementação para o cloud**.
1. Reconstruir o projeto: a partir do Eclipse menu, clique em **projeto**, em seguida, clique em **Criar tudo**.
1. Implemente a aplicação *de transição* no Azure.
    >[AZURE.IMPORTANT] Tal como mencionado acima, recomenda-se vivamente que que depurar no emulador de cluster na maioria dos casos, em seguida, no ambiente de teste de depuração apenas se é necessária uma depuração adicionais. Recomenda-se para não depurar no ambiente de produção.
1. Assim que a sua implementação estiver pronta no Azure, obter o nome DNS para a implementação a partir do [Portal de gestão do Azure][]. Uma implementação transição tem um nome DNS em forma de http://*&lt;guid&gt;*. cloudapp.net, onde * &lt;guid&gt; * for um valor GUID atribuído pelo Azure.
1. No Explorador de projeto do Eclipse, **WorkerRole1**com o botão direito, clique em **Azure**e, em seguida, clique na **depuração**.
1. Na caixa de diálogo **Propriedades para WorkerRole1 depuração** :
    1. Verificar **Activar depuração remota para esta função.**
    1. **Ponto final de introdução para utilizar**, utilize **a depuração (público: 8090, privado: 8090)**.
    1. Certifique-se de que **JVM iniciar no modo de suspenso, a aguardar uma ligação de depurador** está desmarcada.
        >[AZURE.IMPORTANT] A opção **Iniciar JVM no modo de suspenso, a aguardar uma ligação de depurador** destina-se para avançada depuração de cenários no emulador cluster apenas (não para implementações de nuvem). Se for utilizada a opção **Iniciar JVM no modo de suspenso, a aguardar uma ligação de depurador** , irá suspender processo de arranque do servidor, até que o depurador Eclipse está ligado à sua JVM. Enquanto que pode utilizar esta opção para uma sessão de depuração utilizando o emulador cluster, não utilizá-lo para uma sessão de depuração numa implementação na nuvem. Inicialização de um servidor ocorre o mais numa tarefa arranque Azure e na nuvem Azure não disponibilizar os pontos finais públicos até que a tarefa de arranque seja concluída. Por conseguinte, um processo de arranque será não concluída com êxito se esta opção estiver ativada numa implementação na nuvem, porque não ser capaz de receber uma ligação de um cliente Eclipse externo.
1. Clique em **criar configurações de depuração**.
1. Na caixa de diálogo **Configuração de depurar Azure** :
    1. Para o **Projeto Java para depurar**, selecione o projeto **MyHelloWorld** .
    1. Para **Configurar a depuração para**, selecione a **nuvem Azure (de transição)**.
    1. Certifique-se de que o **Azure calcular emulador** está desmarcada.
    1. Para **anfitrião**, introduza o nome DNS da sua implementação faseada, mas sem o anterior **http://**. Por exemplo (utilize o seu GUID em vez do GUID mostrado aqui): **4e616d65-6f6e-6 d 65-6973-526f62657274.cloudapp.net**
1. Clique em **OK** para fechar a caixa de diálogo de **Configuração de depurar Azure** .
1. Clique em **OK** para fechar a caixa de diálogo de **Propriedades para WorkerRole1 depuração** .
1. Se não tiver um ponto de interrupção já definido na index.jsp, configurá-lo:
    1. Dentro Project Explorer do Eclipse, expanda **MyHelloWorld**, expanda **ConteúdoWeb**e faça duplo clique em **index.jsp**.
    1. Dentro de index.jsp, com o botão direito na barra azul à esquerda do seu código Java e clique em **Mostrar/Ocultar pontos de interrupção**, conforme mostrado no seguinte: ![][ic551537]
1. No menu do Eclipse, clique em **Executar** e, em seguida, clique em **Configurações depurar**.
1. Na caixa de diálogo **Configurações depurar** , expanda **Aplicação Java remota** no painel do lado esquerdo, selecione **Azure na nuvem (WorkerRole1)**e clique em **Depurar**.
1. No seu browser, executar a sua aplicação faseada, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, substituindo o GUID a partir do seu nome DNS para * &lt;guid&gt;*. Se lhe for pedido por uma caixa de diálogo **Confirmar mudar perspetiva** , clique em **Sim**. Agora deverá ser executado a depurar sessão para a linha de código onde o ponto de interrupção foi definido.

>[AZURE.NOTE] Se está a tentar iniciar um controlo remoto depuração de ligação para uma implementação que tenha várias instâncias de funções em execução, atualmente não é possível controlar qual instância o depurador será inicialmente ligado, tal como o Balanceador de carga Azure escolha uma instância aleatoriamente. Quando está ligado com essa instância, no entanto, continuará depuração na mesma instância. Nota para além disso, se existir um período de inatividade de mais do que 4 minutos (por exemplo, quando estiver a parado num ponto de interrupção durante muito tempo), Azure pode fechar a ligação.

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Depuração de uma instância de função específica numa implementação com várias instâncias ##

Quando a sua implementação está em execução na nuvem, quando irá provavelmente ser a executá-la em várias cluster, ou função instâncias. Isto permite-lhe tirar partido de garantia de disponibilidade do Azure 99.95% e por escalar para fora da sua aplicação.

Em destes cenários, poderá precisar depurar remotamente a sua aplicação de Java numa instância de função específica. No entanto, se ativar a apenas um normal entrado ponto final para depuração, Balanceador de carga Azure irá torná-la praticamente impossibilita para poder ligar o depurador para uma instância de função específica. Em vez disso-irão ligá-lo para uma instância de função-escolhe aleatoriamente.

Este é o tipo de cenário de onde tomar partido pontos finais de entrada instância irá facilitar para si depurar uma instância de função específica.

Digamos que planeia executar até 5 instâncias de função da sua implementação. Utilizar página **pontos finais** de propriedade na caixa de diálogo de propriedades da função, crie um extremo entrada instância e atribuí-lo um intervalo de portas públicos, em vez de um número de porta única. Por exemplo, na caixa de entrada de **Porta pública** , especifique **81 85**.

Depois de implementar a aplicação com este ponto final instância, Azure atribuir um número de porta exclusivo deste intervalo para cada uma das funções instâncias. Em seguida, para saber qual instância tem atribuída qual o número de porta, pode utilizar a variável de ambiente de**_PUBLICPORT** *InstanceEndpointName*(onde *InstanceEndpointName* é o nome atribuído quando criou o ponto final instância) configurada automaticamente pelo toolkit na sua implementação (por exemplo, devolvendo valor correspondente no rodapé de uma página Web, para que poderia lê-la ao navegar para a mesma).

Quando já souber qual o número de porta público tiver sido atribuída essa instância, pode referenciar na configuração depuração no Eclipse, por fixação-lo para o nome do anfitrião do seu serviço. Isto irá activar o depurador Eclipse ligar para essa instância específica e não qualquer uma das outras instâncias.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Apenas para o Windows: para depurar sua aplicação ao executar o emulador de cluster ##

>[AZURE.NOTE] O Azure emulador só está disponível no Windows. Ignore esta secção se estiver a utilizar um sistema operativo que não seja o Windows. 

1. Criar o seu projeto para testar o emulador: Project Explorer do Eclipse com sessão no, **MyAzureProject**com o botão direito, clique em **Propriedades**, clique em **Azure**e definir **Construir para** **testar no emulador**.
1. Reconstruir o projeto: a partir do Eclipse menu, clique em **projeto**, em seguida, clique em **Criar tudo**.
1. No Explorador de projeto do Eclipse, **WorkerRole1**com o botão direito, clique em **Azure**e, em seguida, clique na **depuração**.
1. Na caixa de diálogo **Propriedades para WorkerRole1 depuração** :
    1. Verificar **Activar depuração remota para esta função.**
    1. **Ponto final de introdução para utilizar**, utilize o ponto final predefinido gerado automaticamente pelo toolkit, listado como **depuração (público: 8090, privado: 8090)**.
    1. Certifique-se de que a opção **Iniciar JVM no modo de suspenso, a aguardar uma ligação de depurador** está desmarcada.
        >[AZURE.IMPORTANT] A opção **Iniciar JVM no modo de suspenso, a aguardar uma ligação de depurador** destina-se para avançada depuração de cenários no emulador cluster apenas (não para implementações de nuvem). Se for utilizada a opção **Iniciar JVM no modo de suspenso, a aguardar uma ligação de depurador** , irá suspender processo de arranque do servidor, até que o depurador Eclipse está ligado à sua JVM. Enquanto que pode utilizar esta opção para uma sessão de depuração utilizando o emulador cluster, não utilizá-lo para uma sessão de depuração numa implementação na nuvem. Inicialização de um servidor ocorre o mais numa tarefa arranque Azure e na nuvem Azure não disponibilizar os pontos finais públicos até que a tarefa de arranque seja concluída. Por conseguinte, um processo de arranque será não concluída com êxito se esta opção estiver ativada numa implementação na nuvem, porque não ser capaz de receber uma ligação de um cliente Eclipse externo.
1. Clique em **criar configurações de depuração**.
1. Na caixa de diálogo **Configuração de depurar Azure** :
    1. Para o **Projeto Java para depurar**, selecione o projeto **MyHelloWorld** .
    1. Para **Configurar a depuração para**, selecione **Azure calcular emulador**.
1. Clique em **OK** para fechar a caixa de diálogo de **Configuração de depurar Azure** .
1. Clique em **OK** para fechar a caixa de diálogo de **Propriedades para WorkerRole1 depuração** .
1. Configurar um ponto de interrupção index.jsp:
    1. Dentro Project Explorer do Eclipse, expanda **MyHelloWorld**, expanda **ConteúdoWeb**e faça duplo clique em **index.jsp**.
    1. Dentro de index.jsp, com o botão direito na barra azul à esquerda do seu código Java e clique em **Mostrar/Ocultar pontos de interrupção**, conforme mostrado no seguinte: ![][ic551537]

       Um ponto de interrupção é definido se vir um ícone de ponto de interrupção dentro da barra azul à esquerda do seu código Java.
1. Inicie a aplicação no emulador de cluster ao clicar no botão **executar no Azure emulador** na barra de ferramentas Azure.
1. No menu do Eclipse, clique em **Executar** e, em seguida, clique em **Configurações depurar**.
1. Na caixa de diálogo **Configurações depurar** , expanda **Aplicação Java remota** no painel do lado esquerdo, selecione **Azure emulador (WorkerRole1)**e clique em **Depurar**.
1. Depois do emulador cluster indica que a aplicação estiver em execução, dentro do seu browser, execute **http://localhost:8080/MyHelloWorld**.
    Se lhe for pedido por uma caixa de diálogo **Confirmar mudar perspetiva** , clique em **Sim**.
    Agora deverá ser executado a depurar sessão para a linha de código onde o ponto de interrupção foi definido.

Isto mostrava como depurar no emulador cluster; a secção seguinte mostra como depurar uma aplicação implementada Azure.

## <a name="debugging-notes"></a>Depuração de notas ##

* Depois de depuração, pode mudar a perspetiva do **Depurar** para **Java** através de clicar no menu do Eclipse, ao clicar em **janela**, **Abrir perspetiva**e selecionar a perspetiva que pretende utilizar.
* Para ativar a depuração remoto no GlassFish, não utilize a funcionalidade de configuração depuração remoto do Azure Toolkit para Eclipse. Em vez disso, configure GlassFish manualmente. Devido a da forma que glassfish trata predefinidas no variáveis de ambiente de opções de Java, funcionalidade de configuração de depuração remoto do toolkit não funciona corretamente com GlassFish. Se estiver ativada a funcionalidade de configuração de depuração remoto do toolkit, pode impedir que o GlassFish comecem.

## <a name="see-also"></a>Consulte também ##

[Toolkit Azure para Eclipse][]

[Criar uma aplicação do mundo Olá Azure no Eclipse][]

[Instalar o Toolkit Azure para Eclipse][] 

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de gestão do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Utilizar a biblioteca de Runtime Azure Service no JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png
