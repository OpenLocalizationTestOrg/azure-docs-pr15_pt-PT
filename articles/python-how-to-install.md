<properties
    pageTitle="Instalar Python e o SDK - Azure"
    description="Saiba como instalar Python e o SDK para utilizar com o Azure."
    services=""
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="installing-python-and-the-sdk"></a>Instalar o Python e o SDK

Python é fácil de configuração no Windows e é fornecido pré-instaladas no Mac, Linux e [Festa para Windows](https://msdn.microsoft.com/commandline/wsl/about). Este guia orienta instalação e preparar o computador para utilizar com o Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Qual é o Python Azure SDK?

O SDK do Azure para Python inclui componentes que permitem-lhe desenvolver, implementar e gerir pedidos de Python Azure. Especificamente, o SDK do Azure para Python inclui o seguinte:

* **Bibliotecas de gestão de**. Estas bibliotecas de classe fornecem uma interface gestão de recursos Azure, como contas de armazenamento, máquinas virtuais.

* **Bibliotecas de Runtime**. Estas bibliotecas de classe fornecem uma interface para aceder a funcionalidades Azure, como o serviço de armazenamento e bus.

## <a name="which-python-and-which-version-to-use"></a>Quais Python e qual a versão para utilizar

Existem várias versões de intérpretes Python disponíveis - alguns exemplos incluem:

* CPython - interpretação de Python utilizada com mais frequência e padrão
* PyPy - implementação alternativa rápida e em conformidade para CPython
* IronPython - interpretação Python que é executada no .net/CLR
* Jython - interpretação Python que é executada no Java Virtual Machine

**CPython** v2.7 ou v3.3 + e PyPy 5.4.0 testado e suportados para o SDK do Azure Python.

## <a name="where-to-get-python"></a>Onde obter Python?

Existem várias formas de obter CPython:

* Diretamente a partir do [www.python.org][]
* A partir de um distro seguras e nas como [www.continuum.io][], [www.enthought.com][] ou [www.activestate.com][]
* Crie a partir de origem!

Se não tiver uma necessidade específica, recomendamos que as duas primeiras opções.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Instalação SDK no Windows, Linux e MacOS (apenas para bibliotecas do cliente)

Se já tiver Python instalado, pode utilizar pip para instalar um conjunto de todas as bibliotecas de cliente no seu existente Python 2.7 ou ambiente Python 3.3 +. Isto irá transferir os pacotes do [Índice de pacote Python][] (PyPI).

Poderá ter direitos de administrador:

- Linux e MacOS, utilize o `sudo` comando: `sudo pip install azure-mgmt-compute`.
- Windows: Abra a PowerShell/linha de comandos como administrador

Pode instalar individualmente cada biblioteca para cada serviço Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Pacotes de pré-visualização podem ser instalados utilizando o `--pre` sinalizador:

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Também pode instalar um conjunto de bibliotecas Azure uma única linha ao utilizar o `azure` meta pacote. Uma vez que não em todos os pacotes neste pacote meta são publicados ainda, como estável a `azure` meta pacote ainda se encontra na pré-visualização. No entanto, os pacotes de core, a partir do perspetivas de qualidade/integridade código podem ser considerados "estáveis" neste momento
- -lo será oficialmente identificada como tal sincronizado com outros idiomas mais cedo possível. Não podemos planear no quaisquer alterações ainda mais importantes até, em seguida.

Uma vez que é uma versão de pré-visualização, tem de utilizar o `--pre` sinalizador:

```console
   $ pip install --pre azure
```
   
ou diretamente

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Obter mais pacotes

O [Índice de pacote Python][] (PyPI) tem uma seleção avançada de bibliotecas Python.  Se tiver optado por instalar um Distro, já terá a maior parte dos bits interessantes para vários cenários de desenvolvimento da web para computação técnicos.


## <a name="python-tools-for-visual-studio"></a>Ferramentas de Python para Visual Studio

[Ferramentas de Python para Visual Studio][] (PTVS) é um plug-in de livre/OSS da Microsoft, que se transforme VS um IDE Python desenvolvido:

![How-para-instalar-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Utilizar PTVS é opcional, mas recomenda-se como dá-lhe suporte Python e solução e Project Web, depuração, criação de perfis, janela interativa, edição do modelo e Intellisense.

PTVS também torna mais fácil implementar Microsoft Azure, com o suporte para implementação para [Web sites][]e [Serviços em nuvem][] .

PTVS funciona com o seus existente Visual Studio 2013 ou instalação de 2015.  Para documentação, transferências e debates, consulte o artigo [Python ferramentas para o Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python Azure cenários para Linux e MacOS

Para o Linux ou MacOS, cenários Azure principais que são suportados:

1. Consumir dos serviços do Azure utilizando as bibliotecas do cliente para Python

2. Executar a aplicação numa VM Linux

3. Desenvolver e a Web sites Azure utilizando Git de publicação

O primeiro cenário permite-lhe criar aplicações web formatado que tirar partido das capacidades de Azure PaaS como [armazenamento de BLOBs][], [armazenamento de fila de espera][], [armazenamento de tabela][] etc através do Pythonic wrappers para APIs do resto do Azure. Estes funciona idêntico no Windows, Mac e Linux.  Também pode utilizar estas bibliotecas do cliente a partir do seu computador de desenvolvimento local ou uma VM Linux em execução no Azure.

Para o cenário VM, simplesmente inicie uma VM Linux da sua escolha (Ubuntu, CentOS, Suse) e executar/gerir que goste.  Por exemplo, pode executar [IPython][] REPL/bloco de notas no seu computador Mac/Windows/Linux e aponte o seu browser para um Linux ou Windows multi proc VM executando o motor de IPython no Azure. Consulte o tutorial de [Bloco de notas de IPython no Azure][] para obter mais informações.

Para obter informações sobre como configurar uma VM Linux, consulte o artigo o tutorial de [Criar Linux de executar uma Máquina Virtual][] .

Utilizar Git implementação, pode desenvolver uma aplicação web do Python e publicá-lo para um Web site Azure a partir de qualquer sistema operativo.  Quando prime o repositório para Azure, cria automaticamente um ambiente virtual e pip instale os pacotes necessários.

Para obter mais informações sobre como desenvolver e Azure sites de publicação, consulte os tutoriais para [Criação de Web sites com Django][], [Criar Web sites com garrafa][]e [Criação de Web sites com balão][]. Para mais informações gerais sobre como utilizar qualquer framework compatível com WSGI, consulte o artigo [Configurar Python com sites públicos do Azure][].


## <a name="additional-software-and-resources"></a>Software adicional e recursos:

* [Azure SDK para Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK para Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Amostras Azure oficiais para Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Contínuas, desde Analytics Python distribuição][]
* [Distribuição Enthought Python][]
* [Distribuição ActiveState Python][]
* [SciPy - um conjunto de aplicações de bibliotecas Python científico][]
* [NumPy - uma biblioteca de valores numéricos para Python][]
* [Django Project - um quadro/CMS maduro web][]
* [IPython - um REPL/bloco de notas avançado para Python][]
* [Bloco de notas de IPython no Azure][]
* [Ferramentas de Python para Visual Studio no GitHub][]
* [Centro de programadores do Python](/develop/python/)

[Contínuas, desde Analytics Python distribuição]: http://continuum.io
[Distribuição Enthought Python]: http://www.enthought.com
[Distribuição ActiveState Python]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.IO]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.ActiveState.com]: http://www.activestate.com
[SciPy - um conjunto de aplicações de bibliotecas Python científico]: http://www.scipy.org
[NumPy - uma biblioteca de valores numéricos para Python]: http://www.numpy.org
[Django Project - um quadro/CMS maduro web]: http://www.djangoproject.com
[IPython - um REPL/bloco de notas avançado para Python]: http://ipython.org
[IPython]: http://ipython.org
[Bloco de notas de IPython no Azure]: virtual-machines-linux-jupyter-notebook.md
[Serviços em nuvem]: cloud-services-python-ptvs.md
[Web sites]: web-sites-python-ptvs-django-mysql.md
[Ferramentas de Python para Visual Studio]: http://aka.ms/ptvs
[Ferramentas de Python para Visual Studio no GitHub]: https://github.com/microsoft/ptvs
[Índice de pacote Python]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Criar uma máquina de Virtual com Linux]: virtual-machines-linux-quick-create-cli.md
[A criação de Web sites com Django]: web-sites-python-create-deploy-django-app.md
[A criação de Web sites com garrafa]: web-sites-python-create-deploy-bottle-app.md
[A criação de Web sites com balão]: web-sites-python-create-deploy-flask-app.md
[Configurar Python com sites públicos do Azure]: web-sites-python-configure.md
[armazenamento de tabela]: storage-python-how-to-use-table-storage.md
[armazenamento de fila de espera]: storage-python-how-to-use-queue-storage.md
[armazenamento de BLOBs]: storage-python-how-to-use-blob-storage.md
