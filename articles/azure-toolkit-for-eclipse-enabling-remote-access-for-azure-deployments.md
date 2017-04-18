<properties
    pageTitle="Activar o acesso remoto para implementações Azure no Eclipse"
    description="Saiba como ativar o acesso remoto para implementações Azure utilizando o Toolkit de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Activar o acesso remoto para implementações Azure no Eclipse

Para ajudar a resolver o seu híbridas, pode ativar e utilizar acesso remoto para ligar à máquina virtual que aloja a sua implementação. A funcionalidade de acesso remoto baseia-se no protocolo de ambiente de trabalho remoto (RDP). Pode configurar o acesso remoto para a sua implementação depois de ter publicado-Azure, ou se estiver a utilizar Eclipse com um sistema operativo Windows, pode configurar o acesso remoto antes de publicar Azure. Tenha em atenção que terá de um cliente de ambiente de trabalho remoto é compatível com o seu sistema operativo para poder ligar a máquina de virtual sua implementação no Azure.

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Como ativar o acesso remoto antes de implementá para Azure

> [AZURE.NOTE] Para ativar o acesso remoto antes de implementar a aplicação para Azure, tem de estar a executar o Eclipse no Windows.

A imagem seguinte mostra as propriedades de **Acesso remoto** diálogo utilizado para ativar o acesso remoto.

![][ic719494]

Existem duas formas para apresentar a caixa de diálogo de propriedades de **Acesso remoto** :

* Clique na ligação **Avançadas** na secção **Acesso remoto** da caixa de diálogo **publicar no Azure** .
* Abra a caixa de diálogo **Propriedades** do seu projeto Azure.

Quando cria um novo projeto de implementação do Azure, o projeto não terá acesso remoto ativada por predefinição. No entanto, pode facilmente activar o acesso remoto ao especificar o nome de utilizador e palavra-passe na caixa de diálogo **publicar no Azure** . A palavra-passe de acesso remoto está encriptada utilizando x. 509 certificados. Se não utilizar fornecer o seu próprio certificado de encriptação depende de um certificado autoassinado fornecido com o plug-in do Azure Eclipse. Este certificado autoassinado é na pasta de **orientação do diapositivo notas** do seu projeto Azure, armazenado como um ficheiro de certificado pública (SampleRemoteAccessPublic.cer) e como um ficheiro de certificado de intercâmbio de informações pessoais (PFX) (SampleRemoteAccessPrivate.pfx). O último contém a chave privada para o certificado e tiver uma palavra-passe predefinida, **Palavra-passe1**. No entanto, uma vez que esta palavra-passe é conhecimento público, o certificado predefinido deve ser utilizado apenas para fins, não para uma implementação de produção de formação. Por isso, de para fins de formação, quando pretender com capacidade de sessões remotas para sua implementações, deverá clica na ligação **Avançadas** na caixa de diálogo **publicar no Azure** para especificar o seu próprio certificado. Tenha em atenção que terá de carregar a versão PFX do certificado para o serviço alojado no interior do Portal de gestão do Azure, para que esse Azure pode desencriptar a palavra-passe de utilizador.

O resto do tutorial mostra-lhe como ativar o acesso remoto para um projecto de implementação do Azure que inicialmente foi criado com acesso remoto desativado. Para fins neste tutorial, vamos criar um novo certificado autoassinado e o ficheiro. pfx terá uma palavra-passe da sua escolha. Também tem a opção de utilização de um certificado emitido por uma autoridade de certificação.

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Como ativar o acesso remoto depois de ter implementado para Azure

Para ativar o acesso remoto depois de ter implementado para Azure, utilize os seguintes passos:

1. Inicie sessão no portal de gestão Azure utilizando a sua conta Azure
1. Na sua lista de **Serviços em nuvem**, selecione o seu serviço de nuvem implementado
1. Na página web do serviço de nuvem, clique na ligação **Configurar**
1. Na parte inferior da página de configuração, clique na ligação **remoto**
1. Quando é apresentada a caixa de diálogo de pop-up:
    * Especifique a função para a qual pretende ativar a acesso remoto
    * Clique para selecionar a caixa de verificação **Ativar o ambiente de trabalho remoto**
    * Especifique um nome de utilizador e palavra-passe que pretende utilizar para acesso remoto
    * Selecione o certificado para utilizar
1. Clique em **OK** 

Verá uma mensagem a informar que a alteração de configuração está em curso, que pode demorar alguns minutos a concluir. Quando tiver concluído a alteração de configuração, siga os passos na secção **para iniciar sessão remotamente** neste artigo.
    
## <a name="how-to-enable-remote-access-in-your-package"></a>Como ativar o acesso remoto no seu pacote

1. No painel do Project Explorer de Eclipse, com o botão direito do projeto Azure e clique em **Propriedades**.

1. Na caixa de diálogo **Propriedades** , expanda **Azure** no painel do lado esquerdo e clique em **Acesso remoto**.

1. Na caixa de diálogo **De acesso remoto** , certifique-se de que **Ativar todas as funções aceitar as ligações de ambiente de trabalho remoto com estas credenciais de início de sessão** está selecionada.

1. Especifique um nome de utilizador para a ligação de ambiente de trabalho remoto.

1. Especificar e confirme a palavra-passe para o utilizador. Os valores de nome e palavra-passe de utilizador definir nesta caixa de diálogo serão utilizados quando faz uma ligação de ambiente de trabalho remoto. (Tenha em atenção que se trata de uma palavra-passe em separado da sua palavra-passe PFX.)

1. Especifique a data de expiração da conta de utilizador.

1. Clique em **Novo** para criar um certificado autoassinado novo. (Em alternativa, pode selecionar um certificado do seu sistema de área de trabalho ou um ficheiro através dos botões de **área de trabalho** ou **sistema de ficheiros** , respetivamente, mas para fins deste tutorial vamos criar um novo certificado.)

    * Na caixa de diálogo **Novo certificado** , especificar e confirme a palavra-passe que irá utilizar para o seu ficheiro PFX.

    * Aceitar o valor fornecido para **Nome (CN)**ou utilize um nome personalizado.

    * Especifique o caminho e nome de ficheiro onde o novo certificado, no formulário. cer, será guardado. Para este passo e o próximo passo, pode utilizar a pasta de **orientação do diapositivo notas** do seu projeto Azure, mas é recomendável gratuito escolher outra localização. Para fins neste tutorial, vamos utilizar **c:\mycert\mycert.cer**. (Criar a pasta **c:\mycert** antes de continuar, ou utilize uma pasta existente se pretender.)

    * Especifique o caminho e nome de ficheiro onde o novo certificado e a sua chave privada, no formulário. pfx, serão guardados. Para fins neste tutorial, vamos utilizar **c:\mycert\mycert.pfx**. A caixa de diálogo do **Certificado novo** deverá ter um aspeto semelhante ao seguinte (atualizar os caminhos de pasta se não utilizou **c:\mycert**):

        ![][ic712275]

    * Clique em **OK** para fechar a caixa de diálogo **Novo certificado** .

1. O diálogo de **Acesso remoto** deverá ter um aspeto semelhante ao seguinte:</p>

    ![][ic719495]

1. Clique em **OK** para fechar a caixa de diálogo de **Acesso remoto** .
    
Recriar a sua aplicação, com a compilação definido para a implementação na nuvem.

## <a name="to-log-in-remotely"></a>Para iniciar sessão remotamente

Assim que a instância de função estiver pronta, que pode remotamente iniciar sessão para a máquina virtual que aloja a aplicação.

* Se estiver a utilizar Eclipse no Windows e selecionada a opção de **ambiente de trabalho remoto do início no implementar** durante a sua implementação do Azure, será apresentada com um ecrã de início de sessão de ligação de ambiente de trabalho remoto quando inicia a sua implementação. Quando lhe for pedido para o nome de utilizador e palavra-passe, introduza os valores que especificou para o utilizador remoto e poderão iniciar sessão.

* Outra forma de iniciar sessão no remotamente é através do <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Portal de gestão do Azure</a>:

    * Na vista de **Serviços em nuvem** do portal de gestão do Azure, clique em seu serviço de nuvem, clique em **instâncias**, clique numa instância específica e, em seguida, clique no botão **Ligar** . O botão de **Ligar** é apresentado como o seguinte na barra de comandos:

        ![][ic659273]

    * Depois de clicar no botão **Ligar** , será pedido para abrir um ficheiro RDP. Abra o ficheiro e siga os pedidos. (Lhe foi também guardar este ficheiro para o seu computador local e, em seguida, execute o ficheiro fazendo duplo clique na mesma ao registo remoto no seu máquina virtual sem ser necessário especificá-las no portal de gestão de aceder primeiro.)

    * Quando lhe for pedido para o nome de utilizador e palavra-passe, introduza os valores que especificou para o utilizador remoto e poderão iniciar sessão.

> [AZURE.NOTE] Se estiver a um sistema operativo não Windows, terá de utilizar um cliente de ambiente de trabalho remoto é compatível com o seu sistema operativo e siga os passos para configurar que o cliente com as definições no ficheiro RDP que transferiu.

## <a name="see-also"></a>Consulte também

[Toolkit Azure para Eclipse][]

[Criar uma aplicação do mundo Olá Azure no Eclipse][]

[Instalar o Toolkit Azure para Eclipse][] 

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png
