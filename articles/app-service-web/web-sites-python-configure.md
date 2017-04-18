<properties 
    pageTitle="Configurar Python Azure de aplicação de serviço Web Apps" 
    description="Neste tutorial descreve as opções para a criação e configurar um servidor Web básico aplicação Python compatível com do Gateway Interface (WSGI) no Azure aplicação de serviço Web Apps." 
    services="app-service" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/26/2016" 
    ms.author="huvalo"/>




# <a name="configuring-python-with-azure-app-service-web-apps"></a>Configurar Python Azure de aplicação de serviço Web Apps

Neste tutorial descreve as opções para a criação e de configuração básica Web servidor Gateway Interface (WSGI) em conformidade Python da aplicação de no [Azure aplicação de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Descreve funcionalidades adicionais de implementação de Git, como o ambiente virtual e utilizar requirements.txt de instalação do pacote.


## <a name="bottle-django-or-flask"></a>Garrafa, Django ou balão?

Azure Marketplace contém modelos para os quadros garrafa, Django e balão. Se estiver a desenvolver a aplicação web do primeira na aplicação de serviço de Azure ou se não estiver familiarizado com Git, recomendamos que siga um nestes tutoriais, que incluem instruções passo a passo para criar uma aplicação de trabalho a partir da galeria utilizando Git implementação a partir do Windows ou Mac:

- [Criar aplicações web com garrafa](web-sites-python-create-deploy-bottle-app.md)
- [Criar aplicações web com Django](web-sites-python-create-deploy-django-app.md)
- [Criar aplicações web com balão](web-sites-python-create-deploy-flask-app.md)


## <a name="web-app-creation-on-azure-portal"></a>Criação de aplicação Web no Portal do Azure

Neste tutorial assume uma subscrição existente do Azure e aceder ao Portal do Azure.

Se não tiver uma aplicação web existente, pode criar um partir do [Azure Portal](https://portal.azure.com).  Clique no botão novo no canto superior esquerdo, em seguida, clique em **Web + Mobile** > **Web app**.

## <a name="git-publishing"></a>Publicação de Git

Configure a publicação de Git para a sua aplicação web recentemente criado, seguindo as instruções no [Local Git implementação para a aplicação de serviço de Azure](app-service-deploy-local-git.md). Neste tutorial utiliza Git para criar, gerir e publicar nossa aplicação web do Python Azure aplicação de serviço.

Assim que Git publicação está configurada, um repositório de Git será criado e associado com a aplicação web. URL do repositório será apresentada e passar pode ser utilizado para transmitir dados do ambiente de desenvolvimento local para a nuvem. Para publicar aplicações através de Git, certifique-se de que um cliente de Git também é instalado e utilize as instruções fornecidas para transmitir o conteúdo da aplicação web para a aplicação de serviço de Azure.


## <a name="application-overview"></a>Descrição geral da aplicação

As secções seguintes, são criados os seguintes ficheiros. Estes devem ser colocados na raiz do repositório Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Processador de WSGI

WSGI é uma norma de Python descrita por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) que define uma interface entre o servidor web e Python. -Fornece uma interface de padronizado para escrever publicações de várias aplicações web e quadros utilizando Python. Quadros de web Python populares hoje utilizam WSGI. Azure aplicação serviço Web Apps oferece-lhe que suporta para qualquer quadros; Além disso, os utilizadores avançados podem mesmo autor os seus próprios desde que a alça de personalizado segue as diretrizes de especificação WSGI.

Eis um exemplo de um `app.py` que define um processador personalizado:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Pode executar esta aplicação localmente com `python app.py`, em seguida, procure `http://localhost:5555` no seu browser.


## <a name="virtual-environment"></a>Ambiente virtual

Apesar de não necessita de quaisquer pacotes externos a aplicação de exemplo acima, é provável que a aplicação requerem algumas.

Para ajudar a gerir dependências externas pacote, implementação do Azure Git suporta a criação de ambientes virtuais.

Quando o Azure Deteta uma requirements.txt na raiz do repositório, cria automaticamente um ambiente virtual denominado `env`. Isto só ocorre no processo de implementação primeira, ou durante a qualquer implementação após a Python selecionado runtime foi alterado.

Provavelmente serão pretende criar um ambiente virtual localmente para o desenvolvimento, mas não inclui-los no seu repositório Git.


## <a name="package-management"></a>Gestão de pacote

Pacotes listados em requirements.txt serão instalados automaticamente no ambiente virtual utilizando pip. Isto acontece na cada implementação, mas pip irá ignorar a instalação, se já estiver instalado um pacote.

Exemplo `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Versão Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Exemplo `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web. config

Terá de criar um ficheiro de Web. config para especificar como o servidor deverá lidar pedidos.

Tenha em atenção que, se tiver um ficheiro de web.x.y.config no seu repositório, onde x. y corresponde ao runtime Python selecionado, em seguida, Azure irá automaticamente copiar o ficheiro adequado como Web. config.

Os exemplos seguintes da Web. config dependem um script de proxy ambiente virtual, que é descrito na secção seguinte.  Funcionam com o processador WSGI utilizado no exemplo `app.py` acima.

Exemplo `web.config` para Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Exemplo `web.config` para Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Ficheiros estáticos serão efetuados pelo servidor web diretamente, sem ter de aceder através de código Python, para um desempenho melhorado.

Nos exemplos anteriores, a localização dos ficheiros estáticos no disco deve corresponder a localização no URL. Isto significa que um pedido para `http://pythonapp.azurewebsites.net/static/site.css` irá servir o ficheiro no disco na `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER`é onde pode especificar o processador WSGI. Nos exemplos anteriores, que tem `app.wsgi_app` porque o processador é uma função com o nome `wsgi_app` no `app.py` na pasta raiz.

`PYTHONPATH`pode ser personalizada, mas se instalar todos os seus dependências no ambiente virtual ao especificá-los na requirements.txt, não deva necessário alterá-la.


## <a name="virtual-environment-proxy"></a>Ambiente virtual Proxy

O seguinte script é utilizado para obter o processador WSGI, ativar os erros de ambiente e inicie sessão virtuais. Foi concebido para ser genérico e utilizadas sem alterações.

Conteúdos de `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Personalizar Git implementação

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## <a name="troubleshooting---package-installation"></a>Resolução de problemas - instalação do pacote

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Resolução de problemas - ambiente Virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do Python](/develop/python/).

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)





 
