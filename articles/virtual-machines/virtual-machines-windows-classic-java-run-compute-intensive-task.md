<properties
    pageTitle="Aplicação de Java com muitos cluster numa VM | Microsoft Azure"
    description="Saiba como criar uma máquina virtual Azure que é executada uma aplicação de Java com muitos cluster que pode ser monitorizada por outra aplicação Java."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Como executar uma tarefa com muitos cluster no Java numa máquina virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Com o Azure, pode utilizar uma máquina virtual para lidar com muitos cluster tarefas. Por exemplo, uma máquina virtual pode processar tarefas e entregar resultados para computadores cliente ou aplicações móveis. Depois de ler este artigo, terá a compreensão de como criar uma máquina de virtual que é executada uma aplicação de Java com muitos cluster que pode ser monitorizada por outra aplicação Java.

Neste tutorial assume saber como criar aplicações de consola de Java, pode importar bibliotecas à aplicação Java e pode gerar um arquivo de Java (para caixa). Sem dados de conhecimento da Microsoft Azure são considerado.

Vai aprender:

* Como criar uma máquina virtual com um Java Development Kit (JDK) já instalados.
* Como iniciar sessão remotamente seu máquina virtual.
* Como criar um espaço de nomes do serviço bus.
* Como criar uma aplicação Java que efetua uma tarefa com muitos cluster.
* Como criar uma aplicação de Java monitoriza o progresso da tarefa com muitos cluster.
* Como executar as aplicações de Java.
* Como parar as aplicações de Java.

Neste tutorial irá utilizar o problema de Traveling vendedor para a tarefa de cluster-a com um grau elevado. Segue-se um exemplo da aplicação Java executar a tarefa de cluster-a com um grau elevado.

![Solver Traveling vendedor problema][solver_output]

Segue-se um exemplo da aplicação Java a tarefa com muitos cluster de monitorização.

![Cliente do problema Traveling vendedor][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual

1. Inicie a sessão [portal clássica Azure](https://manage.windowsazure.com).
2. Clique em **Novo**, clique em **Calcular**, clique em **Máquina Virtual**e, em seguida, clique na **Partir da Galeria**.
3. Na caixa de diálogo **Selecionar imagem de Máquina Virtual** , selecione **JDK 7 Windows Server 2012**.
Tenha em atenção que **JDK 6 Windows Server 2012** está disponível caso possua aplicações legadas que ainda não estão prontas para ser executado no JDK 7.
4. Clique em **seguinte**.
4. Na caixa de diálogo **configuração de Máquina Virtual** :
    1. Especifique um nome para a máquina virtual.
    2. Especifique o tamanho a utilizar para a máquina virtual.
    3. Introduza um nome para o administrador no campo **Nome de utilizador** . Lembre-se de que este nome e a palavra-passe que irá introduzir seguinte, irá utilizar ao iniciar sessão remotamente máquina virtual.
    4. Introduza uma palavra-passe no campo de **nova palavra-passe** e volte a introduzir no campo **Confirmar** . Esta é a palavra-passe da conta de administrador.
    5. Clique em **seguinte**.
5. Na caixa de diálogo **configuração de Máquina Virtual** seguinte:
    1. Para **o serviço de nuvem**, utilize a predefinição de **criar um novo serviço na nuvem**.
    2. O valor para **nome DNS do serviço de nuvem** tem de ser exclusivo ao longo cloudapp.net. Se for necessário, modifique este valor para que esse Azure indica que seja exclusivo.
    2. Especifique uma região, grupo afinidade ou rede virtual. Efeitos neste tutorial, especifique uma região como **EUA Ocidental**.
    2. Para a **Conta de armazenamento**, selecione **utilizar uma conta de armazenamento gerado automaticamente**.
    3. Para **Definir de disponibilidade**, selecione **(nenhum)**.
    4. Clique em **seguinte**.
5. No final caixa de diálogo de **configuração de Máquina Virtual** :
    1. Aceite as entradas de ponto final predefinidas.
    2. Clique em **concluído**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Para iniciar sessão remotamente na sua máquina virtual

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2. Clique em **máquinas virtuais**.
3. Clique no nome da máquina virtual ao qual pretende iniciar sessão no.
4. Clique em **Ligar**.
5. Responda às perguntas, conforme necessário para ligar à máquina virtual. Quando lhe for pedido para o nome de administrador e a palavra-passe, utilize os valores que forneceu quando criou a máquina virtual.

Repare que a funcionalidade de Azure Service Bus requer o certificado de Baltimore CyberTrust Root ser instalado como parte do **cacerts** arquivo seu JRE. Este certificado é automaticamente incluído no ambiente de Java Runtime (JRE) utilizado por este tutorial. Se não tiver este certificado no arquivo de **cacerts** JRE, consulte o artigo [Adicionar um certificado para o arquivo de certificados Java AC] [ add_ca_cert] para obter informações sobre como adicionar (, assim como informações sobre como visualizar os certificados no arquivo de cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Como criar um espaço de nomes do serviço bus

Para começar a utilizar filas Bus de serviço no Azure, primeiro tem de criar um espaço de nomes de serviço. Um espaço de nomes de serviço fornece um âmbito contentor para endereçar recursos de serviço Bus na aplicação.

Para criar um espaço de nomes de serviço:

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2.  No painel de navegação do canto inferior esquerdo do portal clássico Azure, clique em **serviço Bus, controlo de acesso e em cache**.
3.  No painel de canto superior esquerdo do portal clássico Azure, clique no **Serviço Bus** nó e, em seguida, clique no botão **Novo** .  
    ![Captura de ecrã do serviço Bus nó][svc_bus_node]
4.  Na caixa de diálogo **criar um novo espaço de nomes de serviço** , introduza um **espaço de nomes**e, em seguida, para se certificar de que seja exclusivo em, clique no botão **Verificar disponibilidade** .  
    ![Criar uma captura de ecrã do novo espaço de nomes][create_namespace]
5.  Após certificar-se de que o nome do espaço de nomes estiver disponível, selecione o país ou região em que o espaço de nomes deverão estar alojadas e, em seguida, clique no botão **Criar espaço de nomes** .  

    O espaço de nomes que criou, em seguida, será apresentada no portal clássico do Azure e leva-o até um pouco a incorporá para ativar. Aguarde até que o estado está **ativa** antes de prosseguir com o passo seguinte.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Obter as credenciais de gestão de predefinida para o espaço de nomes

Para realizar operações de gestão, tal como criar uma fila no novo espaço de nomes, tem de obter as credenciais de gestão para o espaço de nomes.

1.  No painel de navegação esquerdo, clique no nó **Bus de serviço** para apresentar a lista de espaços de nomes disponíveis.
    ![Captura de ecrã de espaços de nomes disponível][avail_namespaces]
2.  Selecione o espaço de nomes que acabou de criar a partir da lista apresentada.
    ![Captura de ecrã da lista de espaço de nomes][namespace_list]
3.  Painel **Propriedades** do lado direito lista as propriedades para o novo espaço de nomes.
    ![Captura de ecrã de painel de propriedades][properties_pane]
4.  A **Chave predefinida** estiver oculto. Clique no botão de **vista** para apresentar as credenciais de segurança.
    ![Captura de ecrã de chave de predefinido][default_key]
5.  Tome nota do **Emissor predefinida** e a **Chave predefinida** como irá utilizar estas informações abaixo para efetuar operações com o espaço de nomes.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Como criar uma aplicação Java que efetua uma tarefa com muitos cluster

1. No seu computador de desenvolvimento (que não tem de ser a máquina virtual que criou), transferir o [Azure SDK para Java](https://azure.microsoft.com/develop/java/).
2. Crie uma aplicação de consola Java utilizando o código de exemplo no final desta secção. Neste tutorial, vamos utilizar **TSPSolver.java** como o nome do ficheiro Java. Modificar a **seu\_serviço\_bus\_espaço de nomes**, **seu\_serviço\_bus\_proprietário**, e **seu\_serviço\_bus\_chave** marcadores de posição para utilizar o seu serviço bus **espaço de nomes**, **Emissor predefinida** e valores de **Chave predefinido** , respetivamente.
3. Depois de codificação, exportar a aplicação para um arquivo Java executáveis para (caixa) e compactar as bibliotecas necessárias para a para caixa gerada. Neste tutorial, vamos utilizar **TSPSolver.jar** como o nome para caixa gerado.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Como criar uma aplicação de Java monitoriza o progresso da tarefa com muitos cluster

1. No seu computador de desenvolvimento, crie uma aplicação de consola Java utilizando o código de exemplo no final desta secção. Neste tutorial, vamos utilizar **TSPClient.java** como o nome do ficheiro Java. Conforme mostrado anteriormente, modifique o **seu\_serviço\_bus\_espaço de nomes**, **seu\_serviço\_bus\_proprietário**, e **seu\_serviço\_bus\_chave** marcadores de posição para utilizar o seu serviço bus **espaço de nomes**, **Emissor predefinida** e valores de **Chave predefinido** , respetivamente.
2. Exportar a aplicação a uma para caixa executáveis e compactar as bibliotecas necessárias para a para caixa gerada. Neste tutorial, vamos utilizar **TSPClient.jar** como o nome para caixa gerado.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Como executar as aplicações de Java
Execute a aplicação com muitos cluster, primeiro para criar fila de espera, em seguida, para resolver o problema de Saleseman em viagem, irá adicionar a melhor rota atual para a fila de bus de serviço. Enquanto a aplicação de cluster-a com um grau elevado está em execução (ou posteriormente), executar o cliente para apresentar os resultados da fila de bus de serviço.

### <a name="to-run-the-compute-intensive-application"></a>Para executar a aplicação com muitos cluster

1. Inicie sessão no seu computador virtual.
2. Crie uma pasta onde será executar a sua aplicação. Por exemplo, **c:\TSP**.
3. Copiar **TSPSolver.jar** para **c:\TSP**,
4. Crie um ficheiro com o nome **c:\TSP\cities.txt** com o seguinte conteúdo.

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5. Numa linha de comandos, altere directórios para c:\TSP.
6. Certifique-se a que pasta de posição de JRE estiver numa variável de ambiente PATH.
7. Terá de criar fila de espera antes de executar as permutações solver TSP bus serviço. Execute o seguinte comando para criar fila de bus de serviço.

        java -jar TSPSolver.jar createqueue

8. Agora que a fila é criada, pode executar as permutações solver TSP. Por exemplo, execute o seguinte comando para executar o solver para 8 cidades.

        java -jar TSPSolver.jar 8

 Se não especificar um número, será executada para 10 cidades. Como o solver encontra rotas threads mais curta atuais, irá adicioná-los para a fila.

> [AZURE.NOTE]
> Maior número que especificou, o já será executado o solver. Por exemplo, a executar o de 14 cidades podem demorar vários minutos e poderá demorar algumas horas, a trabalhar rapidamente para 15 cidades. Aumentar a 16 ou mais cidades poderá resultar em dias do runtime (eventualmente semanas, meses e anos). Este é devido ao rápido aumento no número de permutações avaliada pelo solver como o número de cidades aumenta.

### <a name="how-to-run-the-monitoring-client-application"></a>Como executar a aplicação de cliente de monitorização
1. Inicie sessão no seu computador onde será executada a aplicação de cliente. Não é necessário ser mesmo computador que executa a aplicação de **TSPSolver** , apesar de poder ser.
2. Crie uma pasta onde será executar a sua aplicação. Por exemplo, **c:\TSP**.
3. Copiar **TSPClient.jar** para **c:\TSP**,
4. Certifique-se a que pasta de posição de JRE estiver numa variável de ambiente PATH.
5. Numa linha de comandos, altere directórios para c:\TSP.
6. Execute o seguinte comando.

        java -jar TSPClient.jar

    Opcionalmente, especifique o número de minutos em suspensão entre verificação fila de espera, por passagem num argumento da linha de comandos. O período de suspensão de predefinido para verificar a fila é 3 minutos, que é utilizado se nenhum argumento da linha de comandos lhe é transmitido **TSPClient**. Se quiser utilizar um valor diferente para o intervalo de sono das, por exemplo, um minuto, execute o seguinte comando.

        java -jar TSPClient.jar 1

    O cliente vai ser executadas até vê-lo a uma mensagem de fila de "Concluído". Tenha em atenção que, se executar várias ocorrências do solver sem que executem o cliente, que poderá precisa para executar o cliente várias vezes para esvaziar completamente fila de espera. Em alternativa, pode eliminar fila de espera e, em seguida, criá-lo novamente. Para eliminar fila de espera, execute o seguinte comando **TSPSolver** (não **TSPClient**).

        java -jar TSPSolver.jar deletequeue

    O solver será executada até que esteja concluída examinar todas as rotas.

## <a name="how-to-stop-the-java-applications"></a>Como parar as aplicações de Java
Para o solver e aplicações de cliente, pode premir **Ctrl + C** para sair, se pretender terminar antes de conclusão normal.


[solver_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../java-add-certificate-ca-store.md
