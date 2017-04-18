<properties
    pageTitle="Como utilizar a Azure secundária Plug-in com integração contínua Hudson | Microsoft Azure"
    description="Descreve como utilizar a Azure secundária Plug-in com integração contínua Hudson."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Como utilizar a Azure secundária Plug-in com integração contínua Hudson

A Azure secundária Plug-in para Hudson permite-lhe aprovisionar nós secundária no Azure quando executar distribuído constrói.

## <a name="install-the-azure-slave-plug-in"></a>Instalar o plug-in de secundária Azure

1. No dashboard de Hudson, clique em **Gerir Hudson**.

1. Na página **Gerir Hudson** , clique em **Gerir plug-ins**.

1. Clique no separador **disponíveis** .

1. Clique em **Procurar** e escreva **Azure** para limitar a lista para plug-ins do relevantes.

    Se optar por percorra a lista de plug-ins disponíveis, encontrará a Azure secundária Plug-in na secção **Gestão Cluster e crie a distribuído** no separador **outras pessoas** .

1. Selecione a caixa de verificação para **Plug-in do Azure secundária**.

1. Clique em **instalar**.

1. Reinicie o Hudson.

Agora que está instalado o plug-in, seria os passos para configurar o plug-in com o seu perfil de subscrição Azure e para criar um modelo que será utilizado na criação de VM para o nó secundária.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurar o secundária Azure Plug-in com o seu perfil de subscrição

Um perfil de subscrição, também conhecido como publicar definições, é um ficheiro XML que contém as credenciais seguras e algumas informações adicionais que terá de trabalhar com Azure no seu ambiente de desenvolvimento. Para configurar a Azure secundária Plug-in, tem de:

* O id da subscrição
* Um certificado de gestão para a sua subscrição

Estes podem encontrar no seu [perfil de subscrição]. Segue-se um exemplo de um perfil de subscrição.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Assim que tiver o seu perfil de subscrição, siga estes passos para configurar a Azure secundária Plug-in.

1. No dashboard de Hudson, clique em **Gerir Hudson**.

1. Clique em **configurar o sistema**.

1. Desloque-se para baixo na página para localizar a secção **na nuvem** .

1. Clique em **Adicionar nova nuvem > Microsoft Azure**.

    ![Adicionar nova na nuvem][add new cloud]

    Isto vai mostrar os campos onde precisa de introduzir os detalhes da subscrição.

    ![configurar o perfil][configure profile]

1. Copie o certificado de id e gestão de subscrição do seu perfil de subscrição e cole-as nos campos adequados.

    Ao copiar o certificado de id e gestão de subscrição, **não** incluir as propostas que incluir os valores.

1. Clique em **Verificar configuração**.

1. Quando a configuração é verificada com êxito, clique em **Guardar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurar um modelo de máquina virtual para o Azure secundária Plug-in

Um modelo de máquina virtual define os parâmetros que o plug-in irá utilizar para criar um nó subordinado no Azure. Os passos seguintes podemos vai ser criando modelo para uma VM Ubuntu.

1. No dashboard de Hudson, clique em **Gerir Hudson**.

1. Clique em **configurar o sistema**.

1. Desloque-se para baixo na página para localizar a secção **na nuvem** .

1. Na secção de **nuvem** , localize **Adicionar Azure Máquina Virtual modelo** e clique no botão **Adicionar** .

    ![Adicionar vm modelo][add vm template]

1. Especifique um nome de serviço de nuvem no campo **nome** . Se o nome que especificar se referir a um serviço de nuvem existente, a VM será aprovisionada desse serviço. Caso contrário, Azure irá criar um novo.

1. No campo **Descrição** , introduza o texto que descreve o modelo que está a criar. Esta informação é apenas para fins de documentos e não é utilizada na aprovisionamento uma VM.

1. No campo de **etiquetas** , introduza **linux**. Esta etiqueta é utilizada para identificar o modelo que está a criar e subsequentemente é utilizada para o modelo de referência ao criar uma tarefa de Hudson.

1. Selecione uma região onde a VM será criada.

1. Selecione o tamanho da memória virtual adequado.

1. Especificar uma conta de armazenamento onde a VM será criada. Certifique-se de que é na mesma região como o serviço de nuvem que estará a utilizar. Se pretender que o novo armazenamento seja criado, pode deixe este campo em branco.

1. Tempo de retenção Especifica o número de minutos antes de Hudson elimina uma idle secundária. Deixe este com o valor predefinido de 60.

1. Em **utilização**, selecione a condição adequada quando este nó subordinado será utilizada. Por agora, selecione **Utilize este nó quanto possível**.

    Neste momento, o seu formulário seria algo semelhante ao seguinte:

    ![configuração do modelo][template config]

1. Na **imagem família ou Id** tem de especificar a imagem que sistema será instalada no seu VM. Pode selecionar a partir de uma lista de famílias de tipos de imagem ou especifique uma imagem personalizada.

    Se pretender selecionar a partir de uma lista de famílias de tipos de imagem, introduza o primeiro caráter (entre maiúsculas e minúsculas) do nome de família da imagem. Por exemplo, escrever **U** irá apresentar uma lista de famílias de tipos de servidor de Ubuntu. Depois de selecionar a partir da lista, Jenkins irá utilizar a versão mais recente da imagem que sistema a partir dessa família quando Aprovisiona o seu VM.

    ![Lista de família SO][OS family list]

    Se tiver uma imagem personalizada que pretende utilizar como alternativa, introduza o nome da imagem personalizada. Nomes de imagem personalizada não são apresentados numa lista para que tenha para se certificar de que o nome é introduzido corretamente.    

    Neste tutorial, escreva **U** para apresentar uma lista de imagens de Ubuntu e selecione **Ubuntu Server 14.04 LTS**.

1. **Método de iniciação**, selecione **SSH**.

1. Copie o script abaixo e cole no campo **inicialização script** .

        # Install Java

        sudo apt-get -y update

        sudo apt-get install -y openjdk-7-jdk

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y openjdk-7-jdk

        # Install git

        sudo apt-get install -y git

        #Install ant

        sudo apt-get install -y ant

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y ant

    O **script de inicialização** será executada quando a VM é criada. Neste exemplo, o script instala Java, git e conselhos.

1. Nos campos **nome de utilizador** e **palavra-passe** , introduza os valores do preferido para a conta de administrador que será criada no seu VM.

1. Clique em **Verificar modelo** para verificar se os parâmetros que especificou são válidos.

1. Clique em **Guardar**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Criar uma tarefa de Hudson que é executada num nó subordinado no Azure

Nesta secção, que está a criar uma tarefa de Hudson que será executada num nó subordinado no Azure.

1. No dashboard de Hudson, clique em **Nova tarefa**.

1. Introduza um nome para a tarefa que estiver a criar.

1. Para o tipo de tarefa, selecione **criar uma tarefa de software gratuitos de estilo**.

1. Clique em **OK**.

1. Na página de configuração da tarefa, selecione **restringir onde este projeto pode ser executado**.

1. Selecione o **menu nó e etiqueta** e selecione **linux** (Recomendamos especificado esta etiqueta quando criar o modelo de máquina virtual na secção anterior).

1. Na secção **Criar** , clique em **Adicionar compilar passo** e selecione **shell de executar**.

1. Edite o seguinte script, substituir **{github nome da sua conta}**, **{nome do seu projeto}**e **{diretório do seu projeto}** com valores adequados e colar o script editado na área de texto que aparece.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e {your project directory} ]; then

            cd {your project directory}

            git pull origin master

        else

            git clone https://github.com/{your github account name}/{your project name}.git

        fi

        # change directory to project

        cd $currentDir/{your project directory}

        #Execute build task

        ant

1. Clique em **Guardar**.

1. No dashboard de Hudson, localize a tarefa que acabou de criar e clique no ícone da **agenda uma compilação** .

Hudson, em seguida, irá criar um nó subordinado a utilizar o modelo que criou na secção anterior e executar o script que especificou no passo compilação para esta tarefa.

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java].

<!-- URL List -->

[Centro de programadores do Azure Java]: https://azure.microsoft.com/develop/java/
[perfil de subscrição]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

