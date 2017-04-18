<properties
    pageTitle="Tomcat numa máquina virtual | Microsoft Azure"
    description="Neste tutorial utiliza recursos criados com o modelo clássico de implementação e mostra como criar uma Máquina Virtual do Windows e configure-o para executar o Apache Tomcat application server."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Como executar um servidor de aplicação Java numa máquina virtual criada com o modelo clássico de implementação

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Com o Azure, pode utilizar uma máquina virtual para fornecer capacidades de servidor. Por exemplo, uma máquina de virtual em execução no Azure pode ser configurada para um servidor de aplicação Java, tal como Apache Tomcat do anfitrião. Depois de concluir este guia, terá a compreensão de como criar uma máquina de virtual em execução no Azure e configure-o para executar um Java application server.

Vai aprender:

* Como criar uma máquina virtual que tem um Java Development Kit (JDK) já instalados.
* Como iniciar sessão remotamente no seu computador virtual.
* Como instalar um servidor de aplicação de Java no seu computador virtual.
* Como criar um ponto final para a sua máquina virtual.
* Como abrir uma porta na firewall para o servidor de aplicações.

Para efeitos neste tutorial, um servidor de aplicações Apache Tomcat será instalado num computador virtual. A instalação concluída irá resultar numa instalação Tomcat como as seguintes.

![Máquina virtual executar Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2. Clique em **Novo**, clique em **Calcular**, clique em **Máquina Virtual**e, em seguida, clique na **Partir da Galeria**.
3. Na caixa de diálogo **Selecionar imagem de Máquina Virtual** , selecione **JDK 7 Windows Server 2012**.
Tenha em atenção que **JDK 6 Windows Server 2012** está disponível se tiver aplicações legadas que não estão prontas para ser executado no JDK 7.
4. Clique em **seguinte**.
5. Na caixa de diálogo **configuração de Máquina Virtual** :
    1. Especifique um nome para a máquina virtual.
    2. Especifique o tamanho a utilizar para a máquina virtual.
    3. Introduza um nome para o administrador no campo **Nome de utilizador** . Lembre-se de que este nome e a palavra-passe que irá introduzir seguinte, irá utilizar quando remotamente ao iniciar sessão no computador virtual.
    4. Introduza uma palavra-passe no campo de **nova palavra-passe** e volte a introduzir no campo **Confirmar** . Esta é a palavra-passe da conta de administrador.
    5. Clique em **seguinte**.
6. Na caixa de diálogo **configuração de Máquina Virtual** seguinte:
    1. Para **o serviço de nuvem**, utilize a predefinição de **criar um novo serviço na nuvem**.
    2. O valor para **nome DNS do serviço de nuvem** tem de ser exclusivo ao longo cloudapp.net. Se for necessário, modifique este valor para que esse Azure indica que seja exclusivo.
    2. Especifique uma região, grupo afinidade ou rede virtual. Efeitos neste tutorial, especifique uma região como **E.u.a. Ocidental**.
    2. Para a **Conta de armazenamento**, selecione **utilizar uma conta de armazenamento gerado automaticamente**.
    3. Para **Definir de disponibilidade**, selecione **(nenhum)**.
    4. Clique em **seguinte**.
7. No final caixa de diálogo de **configuração de Máquina Virtual** :
    1. Aceite as entradas de ponto final predefinidas.
    2. Clique em **concluído**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Para iniciar sessão remotamente no seu computador virtual

1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2. Clique em **máquinas virtuais**.
3. Clique no nome da máquina virtual ao qual pretende iniciar sessão no.
4. Depois de ter começado a máquina virtual, um menu de pop-up na parte inferior da página permite ligações.
5. Clique em **Ligar**.
6. Responda às perguntas, conforme necessário para ligar à máquina virtual. Este deverá implicar guardar ou abrir o ficheiro. rdp que contém os detalhes de ligação. Poderá ter que copiar a url: porta como a última parte da primeira linha do ficheiro. rdp e cole-a numa aplicação de início de sessão remota.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Para instalar um servidor de aplicação de Java no seu computador virtual

Pode copiar um servidor de aplicação Java para o seu computador virtual ou pode instalar um servidor de aplicação de Java através de um programa de instalação.

Para efeitos neste tutorial, Tomcat será instalado.

1. Quando tiver iniciado sessão seu computador virtual, abra uma sessão de browser para [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Faça duplo clique na ligação para o **Windows de 32 bits/64-bits serviço Installer**. Ao utilizar esta técnica, Tomcat será instalado como um serviço do Windows.
3. Quando lhe for pedido, selecione executar o programa de instalação.
4. No Assistente de **Configuração de Tomcat Apache** , siga os pedidos para instalar Tomcat. Efeitos neste tutorial, aceitar as predefinições está bem formatado. Quando chegar a caixa de diálogo **Concluir o Assistente de configuração de Tomcat Apache** , opcionalmente, pode verificar **Executar Tomcat Apache** ter Tomcat iniciar agora. Clique em **Concluir** para concluir o processo de configuração de Tomcat.

## <a name="to-start-tomcat"></a>Para iniciar Tomcat
Se não tiver escolhido executar Tomcat na caixa de diálogo **Concluir o Assistente de configuração de Tomcat Apache** , comece-ao abrir uma linha de comandos no seu computador virtual e executar **líquido iniciar Tomcat7**.

Agora deverá ver Tomcat executar se executar o browser a máquina virtual e abrir <http://localhost:8080>.

Para ver Tomcat executar o máquinas externas, é necessário criar um ponto final e abrir uma porta.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Para criar um ponto final para a sua máquina virtual
1. Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2. Clique em **máquinas virtuais**.
3. Clique no nome da máquina virtual que esteja a executar o servidor de aplicações Java.
4. Clique em **pontos finais**.
5. Clique em **Adicionar**.
6. Na caixa de diálogo **Adicionar endpoint** , certifique-se de **ponto final de autónomo adicionar** está selecionada e, em seguida, clique em **seguinte**.
7. Na caixa de diálogo **novos detalhes de ponto final** :
    1. Especifique um nome para o ponto final; Por exemplo, **HttpIn**.
    2. Especificar **TCP** para o protocolo.
    3. Especifique **80** para a porta pública.
    4. Especifique **8080** para a porta privada.
    5. Clique no botão de **concluída** para fechar a caixa de diálogo. O ponto final agora será criado.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Para abrir uma porta na firewall de máquina virtual
1. Inicie sessão no seu computador virtual.
2. Clique em **Iniciar do Windows**.
3. Clique em **Painel de controlo**.
4. Clique em **sistema e segurança**, clique em **Firewall do Windows**e, em seguida, clique em **Definições avançadas**.
5. Clique em **Regras de entrada**e, em seguida, clique em **Nova regra**.
 ![Nova regra de entrada][NewIBRule]
6. Para o **Tipo de regra**, selecione **porta**e, em seguida, clique em **seguinte**.
 ![Porta de nova regra de entrada][NewRulePort]
7. No ecrã de **todas as portas e protocolo** , selecione **TCP**, especificar **8080** como a **porta local específica**e, em seguida, clique em **seguinte**.
 ![Nova regra de entrada][NewRuleProtocol]
8. No ecrã de **ação** , selecione **Permitir a ligação**e, em seguida, clique em **seguinte**.
 ![Nova ação de regra de entrada][NewRuleAction]
9. No ecrã de **perfil** , certifique-se de **domínio**, **privado**e **público** são selecionadas e, em seguida, clique em **seguinte**.
 ![Novo perfil de regra de entrada][NewRuleProfile]
10. No ecrã **nome** , especifique um nome para a regra, como **HttpIn** (o nome da regra não é necessária para corresponder ao nome do ponto final, no entanto) e, em seguida, clique em **Concluir**.  
 ![Novo nome da regra de entrada][NewRuleName]

Neste momento, o Web site Tomcat deve ser visto a partir de um browser externo utilizando um URL do formulário * *http://*seu\_DNS\_nome*. cloudapp.net**onde ** *seu\_DNS\_nome** * é o nome DNS que especificou quando criou a máquina virtual.

## <a name="application-lifecycle-considerations"></a>Considerações sobre do ciclo de vida de aplicações
* Pode criar o seu próprio arquivo de aplicação web (guerra) e adicioná-lo para a pasta **webapps** . Por exemplo, criar um projeto de web dinâmico Java serviço página (JSP) básicas e exportá-lo como um ficheiro de guerra, copie a guerra para a pasta de **webapps** Apache Tomcat na máquina virtual, em seguida, executá-la num browser.
* Por predefinição quando o serviço de Tomcat estiver instalado, está definido para iniciar manualmente. Pode alterná-lo para iniciar automaticamente utilizando o snap-in de serviços. Inicie o snap-in Serviços ao clicar em **Iniciar do Windows**, **As ferramentas administrativas**e, em seguida, **Serviços**. Faça duplo clique sobre o serviço de **Apache Tomcat** e definir o **tipo de arranque** para **Automático**.

    ![Definir um serviço para iniciar automaticamente][service_automatic_startup]

    A vantagem de ter iniciar Tomcat automaticamente é que será iniciado se a máquina virtual for reiniciada (por exemplo, depois de atualizações de software que requerem um reinício estão instaladas).

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre outros serviços (como o armazenamento do Windows Azure, bus de serviço e base de dados SQL) que podem pretende incluir com as suas aplicações Java, veja as informações disponíveis no [Centro de programadores do Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png
