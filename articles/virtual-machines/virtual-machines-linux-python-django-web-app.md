<properties 
    pageTitle="Python web app com Django no Linux | Microsoft Azure" 
    description="Aprenda a alojar uma aplicação web baseada em Django no Azure utilizando uma máquina de virtual Linux." 
    services="virtual-machines-linux" 
    documentationCenter="python" 
    authors="huguesv" 
    manager="wpickett" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="huvalo"/>
    
# <a name="django-hello-world-web-application-on-a-linux-vm"></a>Aplicação web da Django Olá mundo numa VM Linux

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

Neste tutorial descreve como um Web site com base em Django no Microsoft Azure utilizando uma máquina de virtual Linux do anfitrião. Neste tutorial assume que tem sem experiência prévia utilizando Azure. Após concluir este guia, terá uma aplicação baseada na Django para cima e a execução de na nuvem.

Irá obter informações sobre como:

* Configure uma máquina virtual Azure ao anfitrião Django. Enquanto este tutorial explica como realizar esta tarefa em **Linux**, a mesma também podem ser efectuada com uma VM de servidor do Windows alojado no Azure. 
* Crie uma nova aplicação Django a partir de Linux.

Ao seguir este tutorial, irá criar uma aplicação de web Olá mundo simple. A aplicação irá estar alojada numa máquina virtual Azure.

Uma captura de ecrã da aplicação concluída é abaixo:

![Apresentar a página do mundo Olá no Azure numa janela do browser](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Criar e configurar uma máquina virtual Azure ao anfitrião Django

1. Siga as instruções indicado [aqui](virtual-machines-linux-quick-create-portal.md) para criar uma máquina virtual Azure da distribuição *Ubuntu Server 14.04 LTS* .  Se preferir, pode escolher autenticação de palavra-passe em vez de chave pública SSH.

1. Editar o grupo de segurança de rede para permitir o tráfego de http recebidas à porta 80 utilizando as instruções [aqui](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

1. Por predefinição, a sua nova máquina virtual não tem um nome de domínio completamente qualificado (FQDN).  Pode criar uma ao seguir as instruções [aqui](virtual-machines-linux-portal-create-fqdn.md).  Este passo é opcional para concluir este tutorial.

## <a id="setup"> </a>a configurar o ambiente de desenvolvimento

**Nota:** Se precisar de instalar Python ou gostaria de utilizar as bibliotecas do cliente, consulte o artigo do [Guia de instalação Python](../python-how-to-install.md).

A VM de Linux Ubuntu já vem com 2.7 Python pré-instalado, mas não tem Apache ou django instalado.  Siga estes passos para ligar à sua VM e instalar o Apache e django.

1.  Inicie uma nova janela de **Terminal** .
    
1.  Introduza o seguinte comando para ligar para a VM Azure.  Se não criar um FQDN, pode ligar utilizando o endereço IP público apresentado na máquina virtual resumo no portal do Azure clássico.

        $ ssh yourusername@yourVmUrl

1.  Introduza os seguintes comandos para instalar o django:

        $ sudo apt-get install python-setuptools python-pip
        $ sudo pip install django

1.  Introduza o seguinte comando para instalar o Apache com mod wsgi:

        $ sudo apt-get install apache2 libapache2-mod-wsgi


## <a name="creating-a-new-django-application"></a>Criar uma nova aplicação Django

1.  Abra a janela de **Terminal** que utilizou na secção anterior para ssh para o seu VM.
    
1.  Introduza os seguintes comandos para criar um novo projeto de Django:

        $ cd /var/www
        $ sudo django-admin.py startproject helloworld

    O script **django admin.py** gera uma estrutura básica para Web sites baseados em Django:
    -   **helloworld/Manage.PY** ajuda-o a iniciar o alojamento e parar a alojar o seu site com base em Django
    -   **helloworld/helloworld/Settings.PY** contém Django definições para a sua aplicação.
    -   **helloworld/helloworld/URLs.PY** contém o código de mapeamento entre cada url e a sua vista.

1.  Crie um novo ficheiro chamado **views.py** no diretório **/var/www/helloworld/helloworld** . Isto irá conter na vista que compõe a página "Olá". Inicie o editor de e introduza o seguinte:
        
        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Agora, substitua os conteúdos do ficheiro **urls.py** com o seguinte:

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )


## <a name="setting-up-apache"></a>Configurar o Apache

1.  Crie um de ficheiro de configuração de sistema anfitrião virtual Apache **/etc/apache2/sites-available/helloworld.conf**. Defina o conteúdo para o seguinte e substitua *yourVmName* com o nome real do computador estiver a utilizar (por exemplo *pyubuntu*).

        <VirtualHost *:80>
        ServerName yourVmName
        </VirtualHost>
        WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
        WSGIPythonPath /var/www/helloworld

1.  Ativar o site com o seguinte comando:

        $ sudo a2ensite helloworld

1.  Reinicie Apache com o seguinte comando:

        $ sudo service apache2 reload

1.  Por fim, carrega a página web no seu browser:

    ![Apresentar a página do mundo Olá no Azure numa janela do browser](./media/virtual-machines-linux-python-django-web-app/mac-linux-django-helloworld-browser.png)


## <a name="shutting-down-your-azure-virtual-machine"></a>Encerrar o computador virtual Azure

Quando tiver terminado com este tutorial, encerramento e/ou remover a máquina virtual Azure recentemente criada para libertar recursos de outros tutoriais e evitar sempre custos de utilização Azure.
