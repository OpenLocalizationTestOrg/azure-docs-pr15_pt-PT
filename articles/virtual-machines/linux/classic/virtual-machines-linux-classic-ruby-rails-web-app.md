<properties
    pageTitle="Alojar uma Rubi no carris Web site numa VM Linux | Microsoft Azure"
    description="Configurar e alojar uma Rubi no Web site com base em carris no Azure utilizando uma máquina de virtual Linux."
    services="virtual-machines-linux"
    documentationCenter="ruby"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="web"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Rubi na aplicação Web do carris numa VM Azure

Este tutorial mostra como alojar uma Rubi no Web site de carris no Azure utilizando uma máquina de virtual Linux.  

Neste tutorial foi validado utilizando Ubuntu Server 14.04 LTS. Se utilizar uma distribuição Linux diferente, poderá ter de modificar os passos para instalar carris.

> [AZURE.IMPORTANT] Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de recursos e clássica](../../../resource-manager-deployment-model.md).  Este artigo aborda a utilizar o modelo de implementação clássica. A Microsoft recomenda a que a maioria das novas implementações utilizam o modelo de Gestor de recursos.

## <a name="create-an-azure-vm"></a>Criar um VM Azure

Comece por criar uma VM Azure com uma imagem em Linux.

Para criar a VM, pode utilizar o portal clássico Azure ou Azure Interface da linha de comandos (CLI).

### <a name="azure-management-portal"></a>Portal de gestão do Azure

1. Inicie sessão no [portal clássica Azure](http://manage.windowsazure.com)
2. Clique em **Novo** > **Calcular** > **Máquina Virtual** > **criação rápida**. Seleccione uma imagem Linux.
3. Introduza uma palavra-passe.

Depois da VM está aprovisionada, clique no nome do VM e clique em **Dashboard**. Localize o ponto final de SSH, listado em **SSH detalhes**.

### <a name="azure-cli"></a>Clip Azure

Siga os passos no [artigo criar um Linux de execução de Máquina Virtual][vm-instructions].

Depois da VM está aprovisionada, pode obter o ponto final SSH executando o seguinte comando:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Instalar o Rubi no carris

1. Utilize SSH para ligar para a VM.

2. Da sessão SSH, utilize os comandos seguintes para instalar o Rubi na VM:

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

    A instalação, pode demorar alguns minutos. Quando for concluída, utilize o seguinte comando para confirmar que é instalado Rubi:

        ruby -v

    Isto devolve a versão do Rubi que foi instalado.

3. Utilize o seguinte comando para instalar carris:

        sudo gem install rails --no-rdoc --no-ri -V

    Utilizar – não rdoc e – não ri sinalizadores para ignorar a instalar a documentação, que é mais rápida.
    Este comando provavelmente irá demorar muito tempo a executar, para que adicionar -V irá apresentar informações sobre o progresso da instalação.

## <a name="create-and-run-an-app"></a>Criar e executar uma aplicação

Enquanto ainda tem sessão iniciada através do SSH, execute os seguintes comandos:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

O comando [Novo](http://guides.rubyonrails.org/command_line.html#rails-new) cria uma nova aplicação carris. O comando [do servidor](http://guides.rubyonrails.org/command_line.html#rails-server) é iniciado o servidor web WEBrick incluído no carris. (Para utilização de produção, que seria provavelmente pretende utilizar um servidor diferente, tal como unicórnio ou passageiros.)

Deverá ver o resultado semelhante ao seguinte.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Adicionar um ponto final

1. Aceda ao [portal clássica Azure] [ management-portal] e selecione o seu VM.

    ![lista de máquina virtual][vmlist]

2. Selecione **os pontos finais** em parte superior da página e, em seguida, clique em **+ ponto final de adicionar** na parte inferior da página.

    ![página pontos finais][endpoints]

3. Na caixa de diálogo **Adicionar ponto final** , selecione "Adicionar um ponto final de autónomo" e clique na seta **seguinte** .

    ![caixa de diálogo novo ponto final][new-endpoint1]

3. Na página de diálogo seguinte, introduza as seguintes informações:

    * **Nome**: HTTP

    * **Protocolo**: TCP

    * **Porta de público**: 80

    * **Porta de privado**: 3000

    Esta opção criará uma porta pública de 80 irá encaminhar o tráfego para o porto privado de 3000, onde o servidor de carris está a escutar.

    ![caixa de diálogo novo ponto final][new-endpoint]

4. Clique na marca de verificação para guardar o ponto final.

5. Deverá apresentada uma mensagem que diz **Actualização em curso**. Assim que esta mensagem desaparece, o ponto final está ativo. Agora pode testar a aplicação ao navegar para o nome DNS do seu máquina virtual. O Web site deverá ser semelhante ao seguinte:

    ![página de carris predefinida][default-rails-cloud]

## <a name="next-steps"></a>Próximos passos

Neste tutorial, que efetuou a maior parte dos passos manualmente. Num ambiente de produção, teria de escrever a sua aplicação num computador desenvolvimento e implemente-o a VM Azure. Além disso, a maior parte dos ambientes de produção alojam a aplicação de carris em conjunto com outro processo do servidor como Apache ou NginX, as alças de pedido de encaminhamento para várias instâncias da aplicação carris e servir recursos estáticos. Para mais informações, consulte o artigo http://rubyonrails.org/deploy/.

Para saber mais sobre Rubi sobre carris, visite o [Rubi no carris guias][rails-guides].

Para utilizar os serviços Azure a partir da sua aplicação Rubi, consulte o artigo:

* [Armazenar dados não estruturados utilizando blobs][blobs]

* [Pares de chave/valor loja utilizando tabelas][tables]

* [Servir conteúdo de largura de banda alta com a rede de entrega de conteúdos][cdn-howto]

<!-- WA.com links -->
[blobs]: ../../../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../../../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: ../../virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
