<properties
   pageTitle="Implementação do Azure máquina virtual com chefe | Microsoft Azure"
   description="Saiba como utilizar chefe fazer implementação de automatizado máquinas virtuais e configuração do Microsoft Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines-windows" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>A automatização de implementação do Azure máquina virtual com chefe

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Chefe é uma ótima ferramenta para fornecer automatização e pretender configurações de estado.

Com os nossos mais recente versão api na nuvem, chefe fornece integração de forma totalmente integrada com o Azure, que lhe dá a possibilidade de aprovisionar e implementar Unidos configuração através de um único comando.

Neste artigo, posso mostrar-lhe como configurar o seu ambiente do chefe aprovisionar o Azure máquinas virtuais e orientá-lo na criação de uma política de ou "Livro de culinária" e, em seguida, implementar este livro de culinária para uma máquina virtual Azure.

Vamos começar!

## <a name="chef-basics"></a>Noções básicas do chefe

Antes de começar, posso sugerir a rever os conceitos básicos dos chefe. Existe excelente material <a href="http://www.chef.io/chef" target="_blank">aqui</a> e recomenda se tiver uma leitura rápida antes de tentar este tutorial. Posso no entanto recap Noções básicas sobre a antes de Vamos começar.

O diagrama seguinte ilustra a arquitetura de chefe de alto nível.

![][2]

Chefe tem três componentes principais de arquitecturais: servidor chefe, chefe cliente (nó) e chefe estação de trabalho.

O servidor de chefe é nosso ponto de gestão e existem duas opções para o servidor de chefe: uma solução alojada ou de uma solução no local. Vamos utilizar uma solução alojada.

O cliente de chefe (nó) é o agente que encontra-se nos servidores que está a gerir.

A estação de trabalho do chefe é nossa estação de trabalho de administração onde podemos criar políticas do nossas e executar o nossos comandos de gestão de. Vamos executar o comando **faca** a partir do chefe estação de trabalho para gerir a nossa infraestrutura.

Também é o conceito de "Cookbooks" e "Receitas". Estes são eficazmente as políticas que definir e aplicar a nossa servidores.

## <a name="preparing-the-workstation"></a>Preparar a estação de trabalho

Em primeiro lugar, permite-preparação estação de trabalho. Estou a utilizar uma padrão estação de trabalho do Windows. É necessário criar um diretório para armazenar os nossos ficheiros config e cookbooks.

Primeiro, crie um diretório chamado C:\chef.

Em seguida, crie um segundo diretório chamado c:\chef\cookbooks.

Precisamos de transferir o nosso ficheiro de definições Azure para que chefe pode comunicar com os nossos subscrição Azure agora.

Transferir o seu publicar definições das [aqui](https://manage.windowsazure.com/publishsettings/).

Guarde o ficheiro de definições de publicar no C:\chef.

##<a name="creating-a-managed-chef-account"></a>Criar uma conta do chefe gerida

Inscrever-se para uma conta do chefe hosted [aqui](https://manage.chef.io/signup).

Durante o processo de inscrição no, será pedido para criar uma nova organização.

![][3]

Assim que a sua organização estiver criada, transfira o kit starter.

![][4]

> [AZURE.NOTE] Se receber um pedido de aviso que irão ser repostas suas chaves, é ok para prosseguir como temos sem infraestrutura existente como ainda configurada.

Este ficheiro de postal de kit de starter contém os ficheiros de configuração de organização e as teclas.

##<a name="configuring-the-chef-workstation"></a>Configurar a estação de trabalho do chefe

Extrai o conteúdo do chefe-starter.zip para C:\chef.

Copie todos os ficheiros em chefe-starter\chef-repo\.chefe ao diretório da sua c:\chef.

Diretório da sua deve agora ter um aspeto semelhante ao exemplo seguinte.

![][5]

Deverá agora ter quatro ficheiros, incluindo o ficheiro de publicação Azure na raiz da c:\chef.

Os ficheiros PEM contêm empresa e chaves privadas do administrador para a comunicação enquanto o ficheiro de knife.rb contém a sua configuração faca. Precisamos de editar o ficheiro knife.rb.

Abra o ficheiro no seu editor escolha e modificar "cookbook_path" removendo a /... / do caminho para que seja apresentada como é mostrado seguinte.

    cookbook_path  ["#{current_dir}/cookbooks"]

Adicione também o seguinte linha refletir o nome do seu Azure publica o ficheiro de definições.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

O ficheiro knife.rb deverá agora ter um aspeto semelhante ao exemplo seguinte.

![][6]

Estas linhas irão Certifique-se de que referencia diretório cookbooks em c:\chef\cookbooks faca e também utiliza os nossos ficheiro de definições de publicar Azure durante o Azure operações.

## <a name="installing-the-chef-development-kit"></a>Instalar o chefe Development Kit

Seguinte [Transferir e instalar](http://downloads.getchef.com/chef-dk/windows) o ChefDK (chefe Development Kit) para configurar a sua estação de trabalho do chefe.

![][7]

Instale a localização predefinida dos c:\opscode. Esta instalação irá demorar cerca de 10 minutos.

Confirmar que a variável de PATH contém as entradas para C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Se não estiverem lá, certifique-se de que adiciona estes caminhos!

*NOTA A ORDEM DO CAMINHO É IMPORTANTE!* Se o seu caminhos opscode não estão na ordem correta terá problemas.

Reinicie o seu estação de trabalho antes de continuar.

Em seguida, podemos irá instale a extensão de faca Azure. Este procedimento fornece faca com o "Azure Plug-in".

Execute o seguinte comando.

    chef gem install knife-azure ––pre

> [AZURE.NOTE] O argumento – pre assegura que está a receber a versão mais recente RC o plug-in de Azure faca que fornece acesso ao conjunto mais recente das APIs.

É provável que um número de dependências também será instalado ao mesmo tempo.

![][8]


Para assegurar que tudo está configurado corretamente, execute o seguinte comando.

    knife azure image list

Se tudo o que está corretamente configurado, irá ver uma lista de imagens Azure disponíveis deslocar-se.

Parabéns. A estação de trabalho está configurada!

##<a name="creating-a-cookbook"></a>Criar um livro de culinária

Um livro de culinária é utilizado por chefe para definir um conjunto de comandos que pretende executar no seu cliente gerido. Criar um livro de culinária é simples e utilizamos o comando **chefe gerar o livro de culinária** para gerar o nosso modelo de livro de culinária. Posso vai ser chamar o meu servidor de web do livro de culinária como gostaria de uma política que implementa o IIS automaticamente.

No diretório da sua C:\Chef execute o seguinte comando.

    chef generate cookbook webserver

Isto irá gerar um conjunto de ficheiros em diretório C:\Chef\cookbooks\webserver. Agora precisamos de definir o conjunto de comandos que Gostaríamos nosso cliente chefe executar na nossa máquina virtual gerida.

Os comandos são armazenados no default.rb ficheiro. Neste ficheiro, posso vai ser que define um conjunto de comandos que instala IIS, inicia o IIS e copia um ficheiro de modelo para a pasta de wwwroot.

Modificar o ficheiro C:\chef\cookbooks\webserver\recipes\default.rb e adicione as linhas seguintes.

    powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
        action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
    end

Assim que tiver terminado, guarde o ficheiro.

## <a name="creating-a-template"></a>Criar um modelo

Tal como mencionado anteriormente, é necessário gerar um ficheiro de modelo que será utilizado como nossa página default.

Execute o seguinte comando para gerar o modelo.

    chef generate template webserver Default.htm

Agora, navegue para o ficheiro de C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Edite o ficheiro ao adicionar algum código "Olá mundo" HTML simple e, em seguida, guarde o ficheiro.



## <a name="upload-the-cookbook-to-the-chef-server"></a>Carregar o livro de culinária para o servidor do chefe

Neste passo, o que efetuar uma cópia do livro de culinária que criámos no computador local com os nossos e carregá-lo para o servidor de alojada chefe. Depois de carregado, o livro de culinária irão aparecer em separador de **política** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implementar uma máquina de virtual com faca Azure

Iremos agora implementar uma máquina virtual Azure e aplicar o livro de culinária "ServidorWeb" que irá instalar o nosso IIS serviço e predefinido página web web.

Para executar esta tarefa, utilize o comando **Criar servidor faca azure** .

Estou a seguir aparece exemplo do comando.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Os parâmetros são facilmente compreensíveis. Substituir as variáveis específicas e execute.

> [AZURE.NOTE] Através de linha de comandos, posso estou também automatizar as minhas regras de filtro de rede ponto final utilizando o parâmetro – tcp-os pontos finais. Que foi aberto o portas 80 e 3389 para fornecer acesso a minha página web e sessão RDP.

Depois de executar o comando, aceda ao portal do Azure e irá ver o seu computador começar aprovisionar o.

![][13]

Linha de comandos é apresentada a seguinte.

![][10]

Uma vez concluída a implementação, podemos deve ser possível ligar ao serviço web através da porta 80 à medida que recomendamos tinha abertos a porta quando podemos aprovisionado máquina virtual com o comando faca Azure. Como esta máquina virtual é a única máquina virtual no meu serviço na nuvem, irá ligá-lo com o url do serviço na nuvem.

![][11]

Como pode ver, recebi creative com o meu código HTML.

Não se esqueça que Recomendamos também pode ligar através de uma sessão RDP a partir do portal clássica Azure através da porta 3389.

Posso Espero que tenha sido úteis! Vá e comece a sua infraestrutura de como o percurso de código com Azure hoje!


<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->
