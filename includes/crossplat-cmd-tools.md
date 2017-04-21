#<a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Como utilizar as ferramentas de linha de comandos do Azure para Mac e o Linux

Este guia descreve como utilizar as ferramentas de linha de comandos do Azure para Mac e o Linux para criar e gerir serviços no Azure. Os cenários abrangidos incluam **instalar as ferramentas**, **Importar as definições de publicação**, **criar e gerir sites públicos do Azure**e **criar e gerir máquinas virtuais do Azure**. Para a documentação de referência abrangente, consulte o artigo [Azure ferramenta de linha de comandos para Mac e documentação do Linux][reference-docs]. 

##<a name="table-of-contents"></a>Índice
* [Quais são as ferramentas de linha de comandos do Azure para Mac e o Linux](#Overview)
* [Como instalar as ferramentas de linha de comandos do Azure para Mac e o Linux](#Download)
* [Como criar uma conta do Azure](#CreateAccount)
* [Como transferir e importar definições de publicação](#Account)
* [Como criar e gerir um Web Site do Azure](#WebSites)
* [Como criar e gerir uma máquina de Virtual do Azure](#VMs)


<h2><a id="Overview"></a>Quais são as ferramentas de linha de comandos do Azure para Mac e o Linux</h2>

As ferramentas de linha de comandos do Azure para Mac e o Linux são um conjunto de ferramentas da linha de comandos para implementar e gerir serviços Azure.
 
As tarefas suportadas incluem o seguinte:

* Importe definições de publicação.
* Criar e gerir sites públicos do Azure.
* Criar e gerir máquinas virtuais do Azure.

Para uma lista completa dos comandos suportados, escreva `azure -help` na linha de comandos depois de instalar as ferramentas, ou consulte a [documentação de referência][reference-docs].

<h2><a id="Download">Como instalar as ferramentas de linha de comandos do Azure para Mac e o Linux</a></h2>

A lista seguinte contém informações para instalar as ferramentas de linha de comandos, dependendo do seu sistema operativo:

* **Mac**: Transferir o [instalador do Azure SDK][mac-installer]. Abra o ficheiro transferido .pkg e concluir os passos de instalação, tal como lhe for pedido.

* **Linux**: instalar a versão mais recente do [Node.js] [ nodejs-org] (consulte o artigo [Instalar Node.js através do Gestor de pacotes][install-node-linux]), em seguida, execute o seguinte comando:

        npm install azure-cli -g

    **Nota**: poderá ter de executar este comando com privilégios elevados:

        sudo npm install azure-cli -g

* **Windows**: executar o instalador Winows (ficheiro. msi), que se encontra disponível aqui: [Ferramentas de linha de comandos do Azure][windows-installer].


Para testar a instalação, escreva `azure` na linha de comandos. Se a instalação foi efetuada com êxito, irá ver uma lista de todos os disponíveis `azure` comandos.

<h2><a id="CreateAccount"></a>Como criar uma conta do Azure</h2>

Para utilizar as ferramentas de linha de comandos do Azure para Mac e o Linux, terá uma conta Azure.

Abra um browser e navegue para [http://www.windowsazure.com] [ windowsazuredotcom] e clique em **versão de avaliação gratuita** no canto superior direito.

![Web Site da Azure][Azure Web Site]

Siga as instruções para criar uma conta.

<h2><a id="Account"></a>Como transferir e importar definições de publicação</h2>

Para começar a utilizar, tem de transferir pela primeira vez e importe as definições de publicação. Isto permitirá utilizar as ferramentas para criar e gerir dos serviços do Azure. Para transferir as definições de publicação, utilize o `account download` comando:

    azure account download

Isto irá abrir o browser predefinido e pedir-lhe iniciar sessão no Portal de gestão. Após iniciar sessão, seu `.publishsettings` ficheiro será transferido. Tome nota do qual este ficheiro está guardado.

Em seguida, importe a `.publishsettings` ficheiro executando o seguinte comando, substituindo `{path to .publishsettings file}` com o caminho para o `.publishsettings` ficheiro:

    azure account import {path to .publishsettings file}

Pode remover todas as informações armazenadas pela <code>import</code> comando utilizando o <code>account clear</code> comando:

    azure account clear

Para ver uma lista das opções para `account` comandos, utilize o `-help` opção:

    azure account -help

Após importar as definições de publicação, deverá eliminar a `.publishsettings` ficheiro por motivos de segurança.

> [AZURE.NOTE] Quando importar definições de publicação, as credenciais para aceder a sua subscrição do Azure são armazenadas dentro da sua `user` pasta. O `user` pasta está protegida ao seu sistema operativo. No entanto, é recomendável que seguir passos adicionais para encriptar o `user` pasta. Pode fazê-lo das seguintes formas:    
> 
> - No Windows, modifique as propriedades da pasta ou utilizar o BitLocker.
> - No Mac, ative FileVault para a pasta.
> - No Ubuntu, utilize a funcionalidade de diretório base encriptado. Outros distribuições Linux fornecem funcionalidades equivalentes.

Agora está pronto para ser criar e gerir sites públicos do Azure e máquinas virtuais do Azure.  

<h2><a id="WebSites"></a>Como criar e gerir um site do Azure</h2>

###<a name="create-a-website"></a>Criar um Web site

Para criar um Web site Azure, criar um directório vazio denominado `MySite` e navegue para esse diretório.

Em seguida, execute o seguinte comando:

    azure site create MySite --git

O resultado deste comando irá conter o URL predefinido para o Web site recentemente criado. O `--git` opção permite-lhe utilizar git para publicar o seu Web site através da criação de git repositórios no ambas as diretório da sua aplicação local e no Centro de dados do seu Web site. Tenha em atenção que, se a sua pasta local já está um repositório de git, o comando irá adicionar um novo remoto para o repositório existente, apontar para o repositório no Centro de dados do seu Web site.

Tenha em atenção que pode executar o `azure site create` comando com qualquer uma das seguintes opções:

* `--location [location name]`. Esta opção permite-lhe especificar a localização do Centro de dados em que o Web site é criado (por exemplo, "oeste nos EUA"). Se omitir esta opção, será promted para escolher uma localização.
* `--hostname [custom host name]`. Esta opção permite-lhe especificar um nome de anfitrião personalizado para o seu site.

Em seguida, pode adicionar conteúdo ao diretório do seu Web site. Utilizar o fluxo de git normais (`git add`, `git commit`) para confirmar o seu conteúdo. Utilize o seguinte comando git para transmitir o conteúdo do Web site para Azure: 

    git push azure master

###<a name="set-up-publishing-from-github"></a>Configurar o publicação a partir do GitHub

Para configurar o contínua publicação a partir de um repositório de GitHub, utilize o `--GitHub` opção ao criar um site:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Se tiver um clonar local de um repositório de GitHub, ou se tiver um repositório de com uma única referência remota para um repositório de GitHub, este comando publicar automaticamente código no repositório GitHub ao seu site. De, em seguida, em quaisquer alterações seguia para o repositório GitHub automaticamente serão publicadas ao seu site.

Quando configurar a publicação de GitHub, o ramo predefinido utilizado é o ramo principal. Para especificar um ramo de diferentes, execute o seguinte comando a partir do seu local repositório:

    azure site repository <branch name>

###<a name="configure-app-settings"></a>Configurar definições de aplicação

As definições da aplicação estão pares valor da chave que estão disponíveis para a sua aplicação o tempo de execução. Quando definido para um Web site Azure, valores de definição da aplicação irão substituir as definições com a mesma chave que são definidos no ficheiro do seu site Web. config. Para as aplicações Node.js e PHP, as definições da aplicação estão disponíveis como variáveis de ambiente. O exemplo seguinte mostra-lhe como configurar um par de valor de chave:

    azure site config add <key>=<value> 

Para ver uma lista de todos os pares de valor/chave, utilize o seguinte:

    azure site config list 

Ou se souber a chave de e para ver o valor, pode utilizar:

    azure site config get <key> 

Se pretender alterar o valor de uma chave existente tem desmarque primeiro a chave existente e, em seguida, adicioná-lo novamente. O comando limpar é:

    azure site config clear <key> 

###<a name="list-and-show-sites"></a>Sites de lista e a apresentação

Para listar os Web sites públicos, utilize o seguinte comando:

    azure site list

Para obter informações detalhadas sobre um site, utilize o `site show` comando. O exemplo seguinte mostra os detalhes do `MySite`:

    azure site show MySite

###<a name="stop-start-or-restart-a-site"></a>Parar, iniciar ou reiniciar um site

Pode parar, iniciar ou reiniciar um site com o `site stop`, `site start`, ou `site restart` comandos:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

###<a name="delete-a-site"></a>Eliminar um site

Por fim, pode eliminar um site com o `site delete` comando:

    azure site delete MySite

Tenha em atenção que, se estiver a executar qualquer um dos acima comandos de dentro da pasta onde executou `site create`, não precisa de especificar o nome do site `MySite` como o último parâmetro.

Para ver uma lista completa dos `site` comandos, utilize o `-help` opção:

    azure site -help 

<h2><a id="VMs"></a>Como criar e gerir uma máquina de Virtual do Azure</h2>

uma máquina de Virtual Azure é criada a partir de uma imagem de máquina virtual (um ficheiro. vhd) que fornece ou que está disponível na Galeria de imagem. Para ver imagens que estão disponíveis, utilize o `vm image list` comando:

    azure vm image list

Pode aprovisionar e iniciar uma máquina virtual a partir de uma das imagens disponíveis com a `vm create` comando. O exemplo seguinte mostra como criar uma máquina de virtual Linux (denominado `myVM`) a partir de uma imagem na Galeria de imagem (CentOS 6.2). Raiz nome de utilizador e palavra-passe para a máquina virtual são `myusername` e `Mypassw0rd` respetivamente. (Tenha em atenção que a `--location` parâmetro especifica o Centro de dados no qual a máquina virtual é criada. Se omitir o `--location` parâmetro, ser-lhe-á pedido para escolher uma localização.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Pode considerar a passar o `--ssh` sinalizador (Linux) ou `--rdp` sinalizar (Windows) para `vm create` para ativar as ligações remotas à máquina de virtual recentemente criado.

Se preferir seria aprovisionar uma máquina de virtual a partir de uma imagem personalizada, pode criar uma imagem a partir de um ficheiro. VHD com o `vm image create` comando, em seguida, utilize o `vm create` comando aprovisionamento de máquina virtual. O exemplo seguinte mostra como criar uma imagem Linux (denominado `myImage`) a partir de um ficheiro. vhd local. (O `--location` parâmetro especifica os dados na qual a imagem está armazenada.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Em vez de criar uma imagem a partir de um local VHD, pode criar uma imagem a partir de um VHD armazenados no armazenamento de Blobs do Azure. Pode fazê-lo com a `blob-url` parâmetro:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Depois de criar uma imagem, pode aprovisionar o uma máquina virtual da imagem utilizando `vm create`. O comando abaixo cria uma máquina de virtual denominada `myVM` partir da imagem que criou acima (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Depois de ter aprovisionado uma máquina virtual, poderá querer criar os pontos finais para permitir o acesso remoto para o seu computador virtual (por exemplo). O exemplo seguinte utiliza a `vm create endpoint` comando para abrir a porta externa 22 e porta local 22 no `myVM`:

    azure vm endpoint create myVM 22 22

Pode obter informações detalhadas sobre uma máquina de virtual (incluindo o endereço IP, nome de DNS e informações de ponto final) com o `vm show` comando:

    azure vm show myVM

Para encerramento, iniciar, ou reiniciar o computador virtual, utilize um dos seguintes comandos:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

E, por fim, para eliminar a VM, utilize o `vm delete` comando:

    azure vm delete myVM

Para obter uma lista completa de comandos para criar e gerir máquinas virtuais, utilize o `-h` opção:

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

