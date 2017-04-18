<properties
    pageTitle="Configurar o Apache Tomcat numa VM Linux | Microsoft Azure"
    description="Saiba como configurar o Apache Tomcat7 com uma máquina virtual Azure (VM) com o Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="how-to-set-up-tomcat7-on-a-linux-virtual-machine-with-microsoft-azure"></a>Como configurar o Tomcat7 numa máquina Virtual Linux do Microsoft Azure

Apache Tomcat (ou simplesmente Tomcat, anteriormente também Jacarta Tomcat) é um servidor da web abrir origem e o contentor servlet desenvolvidos pelo Apache Software Foundation ASF (). Tomcat implementa o Servlet Java e as especificações JavaServer páginas (JSP) a partir do DOM Microsystems e fornece puro Java HTTP ambiente de servidor web na qual pretende executar código Java. A configuração mais simples, Tomcat é executado num processo único sistema operativo. Este processo é executado uma máquina de virtual Java (JVM). Cada pedido de HTTP através do browser para Tomcat é processado como um tópico separado do processo de Tomcat.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Neste guia, terá de instalar tomcat7 numa imagem Linux e implementá-lo no Microsoft Azure.  

Vai aprender:  

-   Como criar uma máquina virtual no Azure.
-   Como preparar a máquina virtual tomcat7.
-   Como instalar o tomcat7.

É assumido que o leitor já tem uma subscrição do Azure.  Se não se pode inscrever para uma avaliação gratuita na [http://azure.microsoft.com](https://azure.microsoft.com/). Se tiver uma subscrição do MSDN, consulte o artigo [preços especiais do Microsoft Azure: MSDN, MPN e benefícios Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Para saber mais sobre Azure, consulte o artigo [o que é o Azure?](https://azure.microsoft.com/overview/what-is-azure/).

Este tópico assume que tem conhecimento de trabalho básico de tomcat e Linux.  

##<a name="phase-1-create-an-image"></a>Fase 1: Criar uma imagem
Nesta fase, irá criar uma máquina de virtual utilizando uma imagem de Linux no Azure.  

###<a name="step-1-generate-an-ssh-authentication-key"></a>Passo 1: Gerar uma chave de autenticação SSH
SSH é uma ferramenta importante para administradores de sistema. No entanto, a configurar a segurança do access com base numa palavra-passe determinado humanos não está prática recomendada. Podem interromper maliciosos utilizadores no seu sistema com base no nome de utilizador e uma palavra-passe fraca.

As boas notícias são que existe uma forma de deixe de acesso remoto aberta e não tem de se preocupar sobre palavras-passe. Consiste o método de autenticação com criptografia assimétrica. Chave privada do utilizador é o que concede a autenticação. Ainda pode bloquear a conta de utilizador para não permitir a autenticação de palavra-passe completamente.

Outra vantagem deste método é que não necessita de palavras-passe diferentes para iniciar sessão no servidores diferentes. É possível autenticar a utilizar a chave privada pessoal em todos os servidores, que impede o ter de se lembrar várias palavras-passe.

Também é possível iniciar sessão com exigir uma palavra-passe com este método.

Siga estes passos para gerar a chave de autenticação SSH.

1.  Transferir e instalar puttygen a partir da seguinte localização: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.  Execute PUTTYGEN. EXE.
3.  Clique em **Gerar** para gerar as teclas. No processo pode aumentar aleatoriedade ao mover o cursor do rato sobre a área em branco da janela.  
![][1]
4.  Após o processo de gerar Puttygen.exe irá apresentar a sua chave gerada. Por exemplo:  
![][2]
5.  Selecione e copie a chave pública na **chave** e guardá-lo num ficheiro denominado publicKey.pem. Não clique em **Guardar chave pública**, porque o formato de ficheiro a guardado uma chave pública é a diferença entre a chave pública que queremos.
6.  Clique em **Guardar chave privada** e guardá-lo num ficheiro denominado privateKey.ppk.

###<a name="step-2-create-the-image-in-the-azure-portal"></a>Passo 2: Crie a imagem no portal do Azure.
No [portal do Azure](https://portal.azure.com/), clique em **Novo** na barra de tarefas para criar uma imagem, escolher a imagem Linux com base nas suas necessidades. O exemplo seguinte utiliza a imagem Ubuntu 14.04.
![][3]

Para o **Nome do anfitrião** especifique o nome para o URL que que e os clientes da Internet, irão utilizar para aceder a esta máquina virtual. Definir a última parte do nome do DNS, por exemplo tomcatdemo e Azure irá gerar o URL como tomcatdemo.cloudapp.net.  

**Chave de autenticação SSH**, copie o valor de chave do ficheiro de **publicKey.pem** , que contém a chave pública gerada pelo puttygen.  
![][4]

Configurar outras definições conforme necessário e, em seguida, clique em criar.  

##<a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: Preparar o computador virtual para Tomcat7
Nesta fase, terá de configurar um ponto final para o tráfego de tomcat e, em seguida, ligar a sua nova máquina virtual.
###<a name="step-1-open-the-http-port-to-allow-web-access"></a>Passo 1: Abrir a porta HTTP para permitir o acesso de web
Os pontos finais no Azure composto por um protocolo (TCP ou UDP), juntamente com uma porta pública e privada. A porta privada é a porta que o serviço está a escutar máquina virtual. A porta pública é a porta que o serviço de nuvem Azure está a escutar externamente para o tráfego de entrada, baseados na Internet.  

Portas TCP 8080 são o número de porta predefinida na qual escuta tomcat. Abrir este porta com um ponto final Azure permitirá outros acesso e à Internet clientes para tomcat páginas.  

1.  No portal do Azure, clique em **Procurar** -> **Máquina Virtual**e, em seguida, clique na máquina de virtual que criou.  
![][5]
2.  Para adicionar um ponto final para o seu computador virtual, clique na caixa **pontos finais** .
![][6]
3.  Clique em **Adicionar**.  
    1.  Para o **ponto final**, escreva um nome para o ponto final no ponto final e, em seguida, escreva 80 na **Porta pública**.  

        Se definiu para 80, não precisa de incluir o número de porta no URL que permite-lhe aceder tomcat. Por exemplo, http://tomcatdemo.cloudapp.net.    

        Se definir outro valor, tal como 81, tem de adicionar o número da porta para o URL para aceder a tomcat. Por exemplo, http://tomcatdemo.cloudapp.net:81 /.
    2.  Escreva 8080 na porta privado. Por predefinição, tomcat recebe na porta TCP 8080. Se tiver alterado a predefinição ouvir porta do tomcat, deverá actualizar porta privado para ser que igual o tomcat ouvir porta.  
    ![][7]

4.  Clique em **OK** para adicionar o ponto final para o seu computador virtual.



###<a name="step-2-connect-to-the-image-you-created"></a>Passo 2: Ligar a imagem que criou.
Pode escolher qualquer ferramenta SSH para ligar à sua máquina virtual. Neste exemplo, utilizamos betumes.  

Em primeiro lugar, obtenha o nome DNS do seu máquina virtual do portal do Azure. **Clique em Procurar** -> **máquinas virtuais** -> o nome do seu máquina virtual -> **Propriedades**e, em seguida, procure no campo **Nome de domínio** do mosaico de **Propriedades** .  

Obter o número da porta para ligações de SSH do campo **SSH** . Eis um exemplo.  
![][8]

Transfira betumes a partir de [aqui](http://www.putty.org/) .  

Depois de transferir, clique no ficheiro executável BETUMES. EXE. Configurar as opções de básicas com o nome do anfitrião e obtido a partir das propriedades de máquina virtual do número da porta. Eis um exemplo:  
![][9]

No painel esquerdo, clique em **ligação** -> **SSH** -> **Auth** e, em seguida, clique em **Procurar** para especificar a localização do ficheiro **privateKey.ppk** , que contém a chave privada gerado pelo puttygen na fase 1: criar uma imagem. Eis um exemplo:  
![][10]

Clique em **Abrir**. Lhe poderá ser alertado por uma caixa de mensagem. Se tiver configurado o nome de DNS e número de porta corretamente, clique em **Sim**.
![][11]  


Deverá visualizar o seguinte procedimento:  
![][12]

Introduza o nome de utilizador especificado quando criou a máquina virtual na fase 1: criar uma imagem. Verá algo semelhante ao seguinte:  
![][13]





##<a name="phase-3-install-software"></a>Fase 3: Instalar software
Nesta fase, instale o ambiente do runtime Java, tomcat e outros componentes de tomcat.  

###<a name="java-runtime-environment"></a>Ambiente de runtime Java
Tomcat escrito Java. Existem dois tipos de Java Development Kits (JDKs) (OpenJDK e Oracle JDK) e pode escolher aquele que pretende.  

>AZURE. Nota: Ambas as JDKs tem quase o mesmo código para as classes na Java API, mas o código para a máquina virtual é realmente diferente. Quando chegar a bibliotecas, OpenJDK tem tendência para utilizar bibliotecas abertas enquanto Oracle tem tendência para utilizar os fechada. No entanto, Oracle JDK tem mais aulas e algumas fixa erros e Oracle JDK é mais estável que OpenJDK.

Os seguintes comandos transferir os JDKs diferentes.  

Abrir jdk   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  

Oracle jdk  

-   Para transferir o JDK a partir do Oracle:  

        wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-   Para criar um diretório para conter os ficheiros JDK:  

        sudo mkdir /usr/lib/jvm  

-   Para extrair os ficheiros JDK para/usr/biblioteca/jvm/diretório:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-   Para predefinir Oracle JDK JVM:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####<a name="test"></a>Teste:
Pode utilizar um comando semelhante ao seguinte para testar se o ambiente do runtime Java estiver instalado corretamente:  

    java -version  

Se instalou o jdk abrir, deverá ver uma mensagem como o seguinte procedimento:![][14]

Se instalou o oracle jdk, deverá ver uma mensagem como o seguinte procedimento:![][15]

###<a name="tomcat7"></a>Tomcat7
Utilizar o seguinte comando para instalar tomcat7:  

    sudo apt-get install tomcat7  

Se não estiver a utilizar tomcat7, utilize a variação adequada deste comando.  

####<a name="test"></a>Teste:

Para verificar se tomcat7 é instalado com êxito, navegue para o nome de DNS do seu servidor tomcat (http://tomcatexample.cloudapp.net/ é o URL de exemplo neste artigo). Se vir uma página semelhante ao seguinte, tem tomcat7 instalado correto.
![][16]


###<a name="install-other-tomcat-components"></a>Instalar outros componentes Tomcat
Existem outros componentes de tomcat opcional que também é possível instalar.  

Utilize o comando **sudo possam cache pesquisa tomcat7** para ver todos os componentes disponíveis. Os comandos seguintes são exemplos para instalar algumas partes úteis.  

    sudo apt-get install tomcat7-admin      #admin web applications
    sudo apt-get install tomcat7-user         #tools to create user instances  

##<a name="phase-4-configure-tomcat"></a>Fase de 4: Configurar Tomcat
Nesta fase, administrar tomcat.
###<a name="start-and-stop-tomcat7"></a>Iniciar e parar tomcat7
O servidor de tomcat7 iniciará automaticamente quando o instalar. Também pode iniciar-se com o seguinte comando:   

    sudo /etc/init.d/tomcat7 start

Para parar tomcat7:  

    sudo /etc/init.d/tomcat7 stop

Para ver o estado de tomcat7:  

    sudo /etc/init.d/tomcat7 status

Para reiniciar tomcat serviços:  

    sudo /etc/init.d/tomcat7 restart

###<a name="tomcat-administration"></a>Administração Tomcat
Pode editar o ficheiro de configuração de utilizador Tomcat configurar as suas credenciais de administrador com o seguinte comando:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Eis um exemplo:  
![][17]  

>AZURE. Nota: Crie uma palavra-passe segura para o nome de utilizador do administrador.  

Depois de editar este ficheiro, deve reiniciar tomcat7 serviços com o seguinte comando para se certificar de que as alterações entrem em:  

    sudo /etc/init.d/tomcat7 restart  

Abra o browser e introduza o URL **http://<your tomcat server DNS name>/Gestor/html**. Por exemplo neste artigo, o URL é http://tomcatexample.cloudapp.net/manager/html.  

Depois de ligar, deverá ver algo semelhante ao seguinte:  
![][18]

##<a name="common-issues"></a>Problemas comuns

###<a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Não consegue aceder a máquina virtual com Tomcat e Moodle da Internet

-   **Sintoma**  
Está a ser executado Tomcat mas não consigo ver a página de predefinida Tomcat com o seu browser.
-   **Caso de raiz possíveis**   
    1.  A porta de ouvir tomcat não for igual a porta privado do ponto final do seu computador virtual para o tráfego de tomcat.  

        Verificar as suas definições de ponto final as portas públicas e privadas e certifique-se que a porta privado é que igual o tomcat ouvir porta. Consulte o artigo fase 1: Criar uma imagem para obter instruções sobre como configurar os pontos finais para o seu computador virtual.  

        Para determinar a porta de ouvir tomcat, abra /etc/httpd/conf/httpd.conf (lançamento chapéu vermelho) ou /etc/tomcat7/server.xml (lançamento Debian). Por predefinição, a porta de ouvir tomcat é 8080. Eis um exemplo:  

            <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

        Se estiver a utilizar uma máquina virtual como Debian ou Ubuntu e pretende alterar a predefinição porta de Tomcat ouvir (por exemplo 8081), também deve abrir a porta para o sistema operativo. Em primeiro lugar, abra o perfil:  

            sudo vi /etc/default/tomcat7  

        Em seguida, remova os comentários da última linha e altere "não" para "Sim".  

            AUTHBIND=yes

    2.  A firewall tiver desativado a porta ouvir do tomcat.

        Se só pode ver a página de predefinida Tomcat anfitrião do local, o problema é provável que a porta que é ouvida pelo tomcat está bloqueada pela firewall. Pode utilizar a ferramenta de w3m para procurar a página web. Os seguintes comandos instalar w3m e navegue para a página de predefinido Tomcat:  

            sudo yum  install w3m w3m-img
            w3m http://localhost:8080  

-   **Solução**
    1. Se o tomcat ouvir porta não for igual a porta privado do ponto final para o tráfego para a máquina virtual, precisa de alterar a porta privado para ser que igual o tomcat ouvir porta.   

    2.  Se o problema for causado por firewall/iptables, adicione as linhas seguintes para /etc/sysconfig/iptables:  

            -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
            -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

        Tenha em atenção que a segunda linha só é necessário para o tráfego de https.  

        Certifique-se de é acima quaisquer linhas que globalmente seriam restringir o acesso, como as seguintes:  

            -A INPUT -j REJECT --reject-with icmp-host-prohibited  

        Para recarregar o iptables, execute o seguinte comando:  

            service iptables restart  

        Este foi testado no CentOS 6.3.

###<a name="permission-denied-when-upload-you-project-files-to-varlibtomcat7webapps"></a>Permissão negado quando o carregamento de ficheiros para /var/lib/tomcat7/webapps que projeto /  

-   **Sintoma**  
Quando utilizar qualquer cliente SFTP (como FileZilla) para ligar a sua máquina virtual e navegue até /var/lib/tomcat7/webapps/para publicar o seu site, obtém uma mensagem de erro semelhante ao seguinte:  

        status: Listing directory /var/lib/tomcat7/webapps
        Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
        Error:  /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
        Error:  File transfer failed

-   **Caso de raiz possíveis** Não tem permissões para aceder à pasta /var/lib/tomcat7/webapps.  
-   **Solução**  
Precisa de obter permissão da conta de raiz. Pode alterar a propriedade da pasta raiz para o nome de utilizador que utilizou quando Aprovisiona o computador. Eis um exemplo com o nome da conta azureuser:  

        sudo chown azureuser -R /var/lib/tomcat7/webapps

    Utilize a opção -R para aplicar as permissões para todos os ficheiros dentro de um diretório demasiado.  

    Tenha em atenção que este comando também funciona para directórios. A opção -R altera as permissões para todos os ficheiros e directórios dentro de diretório. Eis um exemplo:  

        sudo chown -R username:group directory  

    Este comando altera a propriedade (utilizador e grupo) para todos os ficheiros e directórios dentro de diretório e próprio directório.  

    O seguinte comando apenas altera a permissão do directório pasta mas deixa os ficheiros e pastas no interior do diretório.  

        sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
