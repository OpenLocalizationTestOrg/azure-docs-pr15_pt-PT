<properties
    pageTitle="Python web app com Django | Microsoft Azure"
    description="Neste tutorial ensina-o alojar um Web site com base em Django no Azure utilizando uma máquina de virtual do Centro de dados do Windows Server 2012 R2 utilizando o modelo de implementação clássica."
    services="virtual-machines-windows"
    documentationCenter="python"
    authors="huguesv"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>


<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="web" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="huvalo"/>


# <a name="django-hello-world-web-application-on-a-windows-server-vm"></a>Aplicação web da Django Olá mundo numa VM de servidor do Windows

> [AZURE.SELECTOR]
- [Windows](virtual-machines-windows-classic-python-django-web-app.md)
- [Mac/Linux](virtual-machines-linux-python-django-web-app.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Neste tutorial descreve como um Web site com base em Django no Microsoft Azure utilizando uma máquina de virtual do Windows Server do anfitrião. Neste tutorial assume que tem sem experiência prévia utilizando Azure. Depois de concluir este tutorial, terá uma aplicação baseada na Django para cima e a execução de na nuvem.

Irá obter informações sobre como:

* Configure uma máquina virtual Azure ao anfitrião Django. Enquanto este tutorial explica como realizar esta tarefa em Windows Server, a mesma também podem ser efectuada com um Linux VM alojado no Azure.
* Crie uma nova aplicação de Django a partir do Windows.

Ao seguir este tutorial, irá criar uma aplicação de web Olá mundo simple. A aplicação irá estar alojada numa máquina virtual Azure.

Uma captura de ecrã da aplicação concluída é apresentada a seguinte.

![Apresentar a página do mundo Olá no Azure numa janela do browser][1]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="creating-and-configuring-an-azure-virtual-machine-to-host-django"></a>Criar e configurar uma máquina virtual Azure ao anfitrião Django

1. Siga as instruções indicado [aqui](virtual-machines-windows-classic-tutorial.md) para criar uma máquina virtual Azure da distribuição Centro de dados do Windows Server 2012 R2.

1. Indicar ao Azure para direcionar tráfego 80 porta a partir da web para porta 80 na máquina virtual:
 - Navegue até ao seu máquina virtual recentemente criada no portal clássica do Azure e clique no separador **pontos finais** .
 - Clique no botão **Adicionar** na parte inferior do ecrã.
    ![adicionar o ponto final](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-addendpoint.png)

 - Abrir o o protocolo **TCP** estiverem **público porta 80** como **privada porta 80**.
![][port80]
1. A partir do separador **DASHBOARD** , clique em **ligar-se** para utilizar o **Ambiente de trabalho remoto** para iniciar sessão na máquina virtual Azure recentemente criada remotamente.  

**Nota importante:** Todas as instruções abaixo partem do princípio de que tem sessão iniciada na máquina virtual corretamente e estão a emitir existem comandos em vez de no computador local.

## <a id="setup"> </a>Instalar Python, Django, WFastCGI

**Nota:** Para transferir a utilizar o Internet Explorer, poderá ter de configurar as definições de ESC do IE (início/administrativo ferramentas/Gestor/Local de servidor, em seguida, clique em **Configuração de segurança avançada IE**, defina para desligado).

1. Instale o 2.7 Python mais recente ou 3.4 a partir de [python.org][].
1. Instale os pacotes de wfastcgi e django utilizando pip.

    Para Python 2.7, utilize o seguinte comando.

        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django

    Para Python 3.4, utilize o seguinte comando.

        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="installing-iis-with-fastcgi"></a>Instalar o IIS com FastCGI

1. Instale o IIS com FastCGI suporte.  Isto poderá demorar vários minutos executar.

        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="creating-a-new-django-application"></a>Criar uma nova aplicação Django

1.  A partir da *C:\inetpub\wwwroot*, introduza o seguinte comando para criar um novo projeto de Django:

    Para Python 2.7, utilize o seguinte comando.

        C:\Python27\Scripts\django-admin.exe startproject helloworld

    Para Python 3.4, utilize o seguinte comando.

        C:\Python34\Scripts\django-admin.exe startproject helloworld

    ![O resultado do comando novo AzureService](./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-cmd-new-azure-service.png)

1.  O comando **django administração** gera uma estrutura básica para Web sites baseados em Django:

  -   **helloworld\manage.PY** ajuda-o a iniciar o alojamento e parar a alojar o seu site com base em Django
  -   **helloworld\helloworld\settings.PY** contém Django definições para a sua aplicação.
  -   **helloworld\helloworld\urls.PY** contém o código de mapeamento entre cada url e a sua vista.

1.  Crie um novo ficheiro chamado **views.py** no diretório *C:\inetpub\wwwroot\helloworld\helloworld* . Isto irá conter na vista que compõe a página "Olá". Inicie o editor de e introduza o seguinte:

        from django.http import HttpResponse
        def home(request):
            html = "<html><body>Hello World!</body></html>"
            return HttpResponse(html)

1.  Substitua o conteúdo do ficheiro urls.py com o seguinte.

        from django.conf.urls import patterns, url
        urlpatterns = patterns('',
            url(r'^$', 'helloworld.views.home', name='home'),
        )

## <a name="configuring-iis"></a>Configurar o IIS

1. Desbloquear a secção processadores a Config global.  Isto permitirá a utilização do processador python no seu Web. config.

        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers

1. Ative WFastCGI.  Isto irá adicionar uma aplicação para o Config global que refere-se a sua interpretação Python executável e o script wfastcgi.py.

    Python 2.7:

        c:\python27\scripts\wfastcgi-enable

    Python 3.4:

        c:\python34\scripts\wfastcgi-enable

1. Crie um ficheiro de Web. config *C:\inetpub\wwwroot\helloworld*.  O valor da `scriptProcessor` atributo deverá corresponder o resultado do passo anterior.  Ver a página para [wfastcgi][] no pypi para mais definições wfastcgi ativado.

    Python 2.7:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

    Python 3.4:

        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>

1. Atualize a localização do IIS Web Site predefinido para apontar para a pasta do projecto django.

        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"

1. Por fim, carregue a página web no seu browser.

![Apresentar a página do mundo Olá no Azure numa janela do browser][1]


## <a name="shutting-down-your-azure-virtual-machine"></a>Encerrar o computador virtual Azure

Quando tiver terminado neste tutorial, encerrar e/ou remover o seu máquina virtual Azure recentemente criada para libertar recursos de outros tutoriais e evitar sempre custos de utilização Azure.

[1]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/virtual-machines-windows-classic-python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
