<properties 
    pageTitle="Como instalar Apache Qpid protão-C numa VM Linux | Microsoft Azure"
    description="Como criar uma VM de Linux CentOS utilizando máquinas virtuais do Azure e como criar e instalar a biblioteca de Apache Qpid protão-C."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Instalar o Apache Qpid protão-C numa VM Linux Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Esta secção mostra como criar uma VM de Linux CentOS utilizando máquinas virtuais do Azure e como transferir, criar e instalar a biblioteca de Apache Qpid protão-C juntamente com as ligações de idioma Python e PHP. Depois de concluir estes passos, será capaz de executar as amostras Python e PHP incluídas com este guia.

O primeiro passo é executado utilizando o [portal clássica Azure][]. Captura de ecrã seguinte mostra como é criada uma VM CentOS com o nome "scott centos":

![Protão numa VM Linux Azure][0]

Depois de aprovisionamento, o portal apresenta o seguinte:

![Protão numa VM Linux Azure][1]

Para poder iniciar sessão no computador, tem de conhecer a porta de ponto final para SSH. Pode obter este valor a partir do [portal clássica Azure][] , selecionando a VM recentemente criada e clicando no separador **pontos finais** . A seguinte captura de ecrã mostra que a porta SSH pública neste computador está 57146.

![Protão numa VM Linux Azure][2]

Agora pode ligar para o computador utilizar SSH. Este exemplo utiliza a ferramenta para, tal como a captura de ecrã seguinte:

![Protão numa VM Linux Azure][3]

Para as aplicações Python e PHP, este exemplo utiliza as bibliotecas do cliente protão a partir do Apache. Estas bibliotecas estão disponíveis para transferência a partir do [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). O ficheiro Leia-me o pacote de distribuição explica os passos necessários para instalar as dependências e criar protão. Eis um resumo dos passos:

1.  Editar o ficheiro de configuração de yum (/ etc/yum.conf) e comenta a exclusão existência de actualizações a cabeçalhos de kernel (\# excluir = kernel\*). Isto é necessário para instalar o compilador gcc.

2.  Instale os pacotes de pré-requisito:

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  Transferir a biblioteca de protão:

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  Extrai o código de protão do arquivo de distribuição:

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  Criar e instale o código através dos seguintes passos tirados do ficheiro Leia-me:

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

Após efetuar estes passos, protão instalado no computador e está pronto para utilização.

## <a name="next-steps"></a>Próximos passos

Está pronto para saber mais? Visite a seguinte ligação:

- [Descrição geral do serviço Bus AMQP][]

[Descrição geral do serviço Bus AMQP]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure portal clássico]: http://manage.windowsazure.com


