<properties
    pageTitle="Criar um bloco de notas Jupyter/IPython | Microsoft Azure"
    description="Saiba como implementar o bloco de notas Jupyter/IPython numa máquina virtual Linux criada com o modelo de implementação do Gestor de recursos no Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# <a name="jupyter-notebook-on-azure"></a>Bloco de notas de Jupyter no Azure

O [Jupyter project](http://jupyter.org), anteriormente [IPython projeto](http://ipython.org), fornece um conjunto de ferramentas para computação científico utilizando as poderosas conchas interativas que combinam execução de código com a criação de um documento utilizaria direto. Estes ficheiros de bloco de notas podem conter texto arbitrários, fórmulas matemáticas, códigos de entrada, os resultados, gráficos, vídeos e qualquer outro tipo de multimédia que um browser web moderna é compatível com a apresentar. Se estiver totalmente novo para Python e pretende sabê-lo num ambiente divertida e interativo ou fazer algumas grave computação de paralelamente/técnico, o bloco de notas Jupyter é uma excelente escolha.

![Captura de ecrã](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) pacotes utilizando SciPy e Matplotlib para analisar a estrutura de uma gravação de som.


## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter duas formas: Azure blocos de notas ou implementação personalizada
Azure fornece um serviço que pode utilizar para [começar a utilizar Jupyter rapidamente ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  Ao utilizar o serviço de bloco de notas do Azure, pode facilmente aceder a interface de web acessível do Jupyter para recursos utilizaria dimensionáveis com todas as capacidades de Python e suas bibliotecas muitos.  Uma vez que a instalação é processada pelo serviço, os utilizadores podem aceder estes recursos sem necessidade de administração e configuração pelo utilizador.

Se o serviço de bloco de notas não funciona para o seu cenário continue a ler este artigo que irá mostrar-lhe como implementar o bloco de notas Jupyter no Microsoft Azure utilizando máquinas virtuais do Linux (VMs).

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Criar e configurar uma VM no Azure

O primeiro passo é criar uma máquina de virtual (VM) em execução no Azure.
Este VM é um sistema operativo concluído na nuvem e será utilizada para executar o bloco de notas Jupyter. Azure é capaz de executar Linux e Windows máquinas virtuais e Abordaremos na configuração do Jupyter em ambos os tipos de máquinas virtuais.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Criar uma VM Linux e abrir uma porta para Jupyter

Siga as instruções indicado [aqui] [ portal-vm-linux] para criar uma máquina virtual da distribuição *Ubuntu* . Neste tutorial, utiliza Ubuntu Server 14.04 LTS. Vamos irá partem do pressuposto de nome de utilizador *azureuser*.

Depois de ser implementada máquina virtual precisamos de abrir uma regra de segurança no grupo de segurança de rede.  A partir do portal do Azure, aceda aos **Grupos de segurança de rede** e abrir o separador grupo de segurança correspondente à sua VM. Tem de adicionar uma regra de segurança de entrada com as seguintes definições: **TCP** para o protocolo, **\*** para a porta de origem (público) e **9999** para a porta de destino (privado).

![Captura de ecrã](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

Enquanto estiver no seu grupo de segurança de rede, clique em **Interfaces de rede** e tome nota do **Endereço IP público** conforme vai ser necessário para ligar à sua VM no próximo passo.

## <a name="install-required-software-on-the-vm"></a>Instalar o software necessário na VM

Para executar o bloco de notas Jupyter no nosso VM, podemos tem primeiro de instalar Jupyter e as respectivas dependências. Ligar a sua linux vm utilizando ssh e emparelhar o nome de utilizador/palavra-passe escolheu quando criou a vm. Neste tutorial, iremos utilizar betumes e ligar a partir do Windows.

### <a name="installing-jupyter-on-ubuntu"></a>Instalar o Jupyter Ubuntu
Instale Anaconda, uma distribuição de python de ciências dados popular, utilizando uma das ligações fornecidas a partir da [Sua continuidade, desde a análise](https://www.continuum.io/downloads).  Partir escrita deste documento, a seguir ligações são mais por excesso para versões de data.

#### <a name="anaconda-installs-for-linux"></a>Instalações anaconda para Linux
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>Instalar o Anaconda3 2.3.0 64 bits no Ubuntu
Por exemplo, esta é como pode instalar o Anaconda Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Captura de ecrã](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### <a name="configuring-jupyter-and-using-ssl"></a>Configurar Jupyter e utilizar SSL
Depois de instalar precisamos de observar os ficheiros de configuração do programa de configuração para Jupyter. Se tiver presença com a configuração Jupyter pode ser útil ver a [Documentação de Jupyter para executar um servidor de bloco de notas](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Seguinte podemos `cd` no directório de perfil para criar um certificado SSL nosso e editar o ficheiro de configuração de perfis.

No Linux utilize o seguinte comando.

    cd ~/.jupyter

Utilize o seguinte comando para criar o certificado SSL (Linux e Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Tenha em atenção que, uma vez que estamos a criar um certificado SSL autoassinado, quando ligar para o bloco de notas do browser irá dar-lhe um aviso de segurança.  Para utilização de produção a longo prazo, que irá que pretende utilizar um certificado corretamente assinado associado à sua organização.  Uma vez que é a gestão de certificado para além do âmbito este demonstração, podemos irão continuar a um certificado autoassinado por agora.

Além de utilizar um certificado, também deve fornecer uma palavra-passe para proteger o seu bloco de notas a partir do uso não autorizado.  Por motivos de segurança Jupyter utiliza palavras-passe encriptadas no seu ficheiro de configuração, pelo que terá encriptar pela primeira vez a sua palavra-passe.  IPython fornece um utilitário para fazê-lo; numa linha de comandos, execute o seguinte comando.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Isto irá pedir-lhe para uma palavra-passe e confirmação e, em seguida, irá imprimir a palavra-passe. Nota Esta opção para o passo seguinte.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Em seguida, podemos irá editar o ficheiro de configuração do perfil, que é o `jupyter_notebook_config.py` ficheiro no diretório estiver numa.  Nota a que este ficheiro pode não existir – criar apenas se for esse o caso.  Este ficheiro tem um número de campos e por predefinição todos são colocada como comentário.  Pode abrir este ficheiro com editores de texto daquilo que pretende e deve Certifique-se de que tem, pelo menos, o seguinte conteúdo. **Certifique-se de que substituir c.NotebookApp.password na janela config com sha1 no passo anterior**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Executar o bloco de notas Jupyter

Neste momento podemos está prontos iniciar o bloco de notas Jupyter. Para executar esta tarefa, navegue para o diretório que pretende armazenar os blocos de notas na e iniciar o servidor de Jupyter bloco de notas com o seguinte comando.

    /anaconda3/bin/jupyter-notebook

Agora deverá conseguir aceder ao seu bloco de notas Jupyter no endereço `https://[PUBLIC-IP-ADDRESS]:9999`.

Quando pela primeira vez aceder ao seu bloco de notas, a página de início de sessão pede a palavra-passe. E uma vez que iniciar sessão, verá "Jupyter bloco de notas Dashboard de", que é o Centro de ontrolo para todas as operações de bloco de notas.  A partir desta página pode criar novos blocos de notas e abrir já existentes.

![Captura de ecrã](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>Utilizar o bloco de notas Jupyter

Se clicar no botão **Novo** , verá a seguinte página de abertura.

![Captura de ecrã](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

A área marcada com um `In []:` é linha de comandos da área de introdução e aqui pode escrever qualquer código Python válido e executará quando clicar em `Shift-Enter` ou clique no ícone «Reproduzir» (o triângulo apontando para a direita na barra de ferramentas).

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>Um poderoso paradigma: live utilizaria documentos com multimédia avançada

Deverá sentir muito natural a qualquer pessoa que tenha utilizado Python e um processador de texto, o bloco de notas porque está a ser algumas formas uma mistura dos dois: pode executar blocos de código Python, mas também pode manter a notas e outro texto alterando o estilo de uma célula de "Código" para "Markdown" utilizando o menu pendente na barra de ferramentas.

Jupyter é muito mais do que um processador de texto como-permite a mistura de cálculo e RTF elementos de multimédia (texto, gráficos, vídeo e praticamente nada que pode apresentar um browser web moderna). Pode a misturar, texto, código, vídeos e muito mais!

![Captura de ecrã](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

E com a potência de bibliotecas excelentes muitos do Python para obter informações técnicas e científico informática, na captura de ecrã seguinte, que um cálculo simples pode ser executado com a mesma facilidade como uma análise de rede complexa, tudo num ambiente de um.

Este paradigma de misturar potência de web moderna com cálculo direto oferece muitas possibilidades e ideais para o cloud; pode ser utilizado o bloco de notas:

* Como um bloco de notas utilizaria para gravar exploratória trabalhe num problema.

* Para partilhar resultados com colegas, seja no formulário utilizaria 'live' ou em formatos impresso (HTML, PDF).

* Para distribuir e apresentar os materiais de ensino direto que envolvam cálculo, para que os estudantes imediatamente podem experimentar o código real, modificá-lo e voltar executá-la de forma interativa.

* Para fornecer "executáveis papéis" que apresentam os resultados da pesquisa de uma forma que pode ser imediatamente reproduzida, validada e prolongada por outras pessoas.

* Como uma plataforma para computação de colaboração de: vários utilizadores podem iniciar sessão no mesmo servidor de bloco de notas para partilhar uma sessão de utilizaria directo.


Se aceder à código de IPython origem [repositório][], irá encontrar um directório inteiro com exemplos de bloco de notas que pode transferir e, em seguida, experimentar no seu próprio VM de Jupyter Azure.  Simplesmente transfira a `.ipynb` ficheiros a partir do site e carregue-os para o dashboard do bloco de notas Azure VM (ou transferi-los diretamente para a VM).

## <a name="conclusion"></a>Conclusão

O bloco de notas Jupyter fornece uma interface poderosa para aceder a forma interativa power do ecossistema Python no Azure.  Cobrir uma vasta gama de casos de utilização incluindo informações detalhadas simples e aprendizagem Python, análise de dados e visualização, simulação e a computação paralelo. Os documentos de bloco de notas resultantes contêm um registo completo dos cálculos que são executadas e podem ser partilhadas com outros utilizadores Jupyter.  O bloco de notas Jupyter pode ser utilizado como uma aplicação local, mas é ideais para implementações da nuvem no Azure

As funcionalidades principais do Jupyter também estão disponíveis no interior do Visual Studio através de de [Ferramentas de Python para Visual Studio][] (PTVS). PTVS é uma gratuita e integração de informática de plug-in da Microsoft que transforma o Visual Studio um ambiente de desenvolvimento de Python avançado que inclui um editor avançado com o IntelliSense, depuração, paralelas e criação de perfis de abrir origem.

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repositório]: https://github.com/ipython/ipython
[Ferramentas de Python para Visual Studio]: http://aka.ms/ptvs
