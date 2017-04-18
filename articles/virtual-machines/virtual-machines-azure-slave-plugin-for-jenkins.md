<properties
    pageTitle="Como utilizar a Azure secundária Plug-in com integração contínua Jenkins | Microsoft Azure"
    description="Descreve como utilizar a Azure secundária Plug-in com integração contínua Jenkins."
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

# <a name="how-to-use-the-azure-slave-plug-in-with-jenkins-continuous-integration"></a>Como utilizar a Azure secundária Plug-in com integração contínua Jenkins

Pode utilizar a Azure secundária Plug-in para Jenkins para aprovisionar secundária nós no Azure quando executar distribuído constrói.

## <a name="install-the-azure-slave-plug-in"></a>Instalar a Plug-in de secundária Azure

1. No dashboard de Jenkins, clique em **Gerir Jenkins**.

1. Na página **Gerir Jenkins** , clique em **Gerir plug-ins**.

1. Clique no separador **disponíveis** .

1. No campo de filtro acima da lista de plug-ins disponíveis, escreva **Azure** para limitar a lista para plug-ins do relevantes.

    Se optar por percorra a lista de plug-ins disponíveis, irá encontrar a Azure secundária Plug-in na secção **Gestão Cluster e crie a distribuído** .

1. Selecione a caixa de verificação do **Plug-in do Azure secundária** .

1. Clique em **instalar sem reiniciar** ou **agora a transferir e instalar o após reiniciar**.

Agora que está instalado o plug-in, são os passos para configurar o plug-in com o seu perfil de subscrição Azure e para criar um modelo que será utilizado na criação de máquina virtual para o nó secundária.


## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurar a Azure secundária Plug-in com o seu perfil de subscrição

Um perfil de subscrição, também conhecido como publicar definições, é um ficheiro XML que contém as credenciais seguras e algumas informações adicionais que terá de trabalhar com Azure no seu ambiente de desenvolvimento. Para configurar a Azure secundária Plug-in, tem de:

* O id da subscrição
* Um certificado de gestão para a sua subscrição

Estes podem encontrar no seu [perfil de subscrição]. Segue-se um exemplo de um perfil de subscrição.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID value>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Depois de ter o seu perfil de subscrição, siga estes passos para configurar a Azure secundária Plug-in:

1. No dashboard de Jenkins, clique em **Gerir Jenkins**.

1. Clique em **configurar o sistema**.

1. Desloque-se para baixo na página para localizar a secção **na nuvem** .

1. Clique em **Adicionar nova nuvem > Microsoft Azure**.

    ![secção de nuvem][cloud section]

    Isto vai mostrar os campos onde precisa de introduzir os detalhes da subscrição.

    ![configuração de subscrição][subscription configuration]

1. Copie os valores de certificado de gestão de id e de subscrição do seu perfil de subscrição e cole-as nos campos adequados.

    Ao copiar o certificado de id e gestão de subscrição, não inclua as propostas que incluir os valores.

1. Clique em **verificar a configuração**.

1. Quando a configuração é verificada parece correta, clique em **Guardar**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurar um modelo de máquina virtual para a Azure secundária Plug-in

Um modelo de máquina virtual define os parâmetros de que o plug-in irá utilizar para criar um nó subordinado no Azure. Os passos seguintes, vamos criar um modelo para uma máquina de virtual Ubuntu.

1. No dashboard de Jenkins, clique em **Gerir Jenkins**.

1. Clique em **configurar o sistema**.

1. Desloque-se para baixo na página para localizar a secção **na nuvem** .

1. Na secção de **nuvem** , encontrar **Adicionar modelo Máquina Virtual do Azure**e, em seguida, clique em **Adicionar**.

    ![Adicionar vm modelo][add vm template]

    Isto vai mostrar os campos onde introduzir detalhes sobre o modelo que está a criar.

    ![configuração geral em branco][blank general configuration]

1. Na caixa **nome** , introduza um nome de serviço de nuvem Azure. Se o nome que introduziu se referir a um serviço de nuvem existente, a máquina virtual será aprovisionada desse serviço. Caso contrário, Azure irá criar um novo.

1. Na caixa **Descrição** , introduza o texto que descreve o modelo que está a criar. Este é apenas para os seus registos e não é utilizada na aprovisionamento uma máquina virtual.

1. A caixa **etiquetas** é utilizada para identificar o modelo que está a criar e subsequentemente é utilizada para o modelo de referência ao criar uma tarefa Jenkins. Para o nosso objetivo, introduza **linux** nesta caixa.

1. Na lista de **região** , clique na região onde a máquina virtual será criada.

1. Na lista de **Tamanho de Máquina Virtual** , clique no tamanho adequado.

1. Na caixa **Nome da conta de armazenamento** , especifique uma conta de armazenamento onde a máquina virtual será criada. Certifique-se de que é na mesma região como o serviço de nuvem que estará a utilizar. Se pretender que o novo armazenamento seja criado, pode deixar esta caixa em branco.

1. Tempo de retenção Especifica o número de minutos antes de Jenkins elimina uma idle secundária. Deixe este com o valor predefinido de 60. Também pode optar por encerrar secundária em vez de eliminá-la quando está inactivo. Para fazê-lo, selecione a caixa de verificação **Apenas de encerramento (não elimine) depois de tempo de retenção** .

1. Na lista de **utilização** , clique na condição adequada quando este nó subordinado será utilizada. Por agora, clique em **Utilize este nó quanto possível**.

    Neste momento, o formulário deverá ter o aspeto algo semelhante ao seguinte:

    ![ponto de verificação modelo geral config][checkpoint general template config]

    O passo seguinte é fornecer detalhes sobre a imagem do sistema operativo que pretende que o seu secundária ser criada em.

1. Na caixa de **imagem família ou Id** , tem de especificar a imagem que sistema será instalada no seu computador virtual. Pode selecionar a partir de uma lista de famílias de tipos de imagem ou especifique uma imagem personalizada.

    Se pretender selecionar a partir de uma lista de famílias de tipos de imagem, introduza o primeiro caráter (entre maiúsculas e minúsculas) do nome de família da imagem. Por exemplo, escrever **U** irá apresentar uma lista de famílias de tipos de servidor de Ubuntu. Depois de selecionar a partir da lista, Jenkins irá utilizar a versão mais recente da imagem que sistema a partir dessa família quando Aprovisiona o seu máquina virtual.

    ![Exemplo de lista de imagem do sistema operativo][OS Image list sample]

    Se tiver uma imagem personalizada que pretende utilizar como alternativa, introduza o nome da imagem personalizada. Nomes de imagem personalizada não são apresentados numa lista, pelo que tem para se certificar de que o nome é introduzido corretamente.

    Para este tutorial, escreva **U** para ativar uma lista de imagens de Ubuntu e, em seguida, clique em **Ubuntu Server 14.04 LTS**.

1. Na lista de **Método de iniciação** , clique em **SSH**.

1. Copie o script abaixo e cole-a na caixa de **Script de inicialização** .

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

    O script de inicialização será executado quando a máquina virtual é criada. Neste exemplo, o script instala Java, Git e conselhos.

1. Nas caixas **nome de utilizador** e **palavra-passe** , introduza os valores do preferido para a conta de administrador que será criada no seu computador virtual.

1. Clique em **Verificar modelo** para verificar se os parâmetros que especificou são válidos.

1. Clique em **Guardar**.


## <a name="create-a-jenkins-job-that-runs-on-a-slave-node-on-azure"></a>Criar uma tarefa de Jenkins que é executada num nó subordinado no Azure

Nesta secção, que está a criar uma tarefa de Jenkins que será executada num nó subordinado no Azure. Terá de se tiver o seu próprio projecto para cima no GitHub a seguir ao longo.

1. No dashboard de Jenkins, clique em **Novo Item**.

1. Introduza um nome para a tarefa que estiver a criar.

1. Para o tipo de projeto, clique em **projeto Freestyle**.

1. Clique em **Ok**.

1. Na página de configuração da tarefa, selecione **restringir onde este projeto pode ser executado**.

1. Na caixa **Expressão de etiqueta** , introduza **linux**. Na secção anterior, criámos um modelo de secundária que a que chamamos **linux**, que é o que vamos especificar aqui.

1. Na secção **Criar** , clique em **Adicionar compilar passo** e selecione **shell de executar**.

1. Edite o seguinte script, substituir **(o nome da conta GitHub)**, **(o seu nome de projeto)**e **(o seu diretório de projeto)** com valores adequados e colar o script editado na área de texto que aparece.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e (your project directory) ]; then

            cd (your project directory)

            git pull origin master

        else

            git clone https://github.com/(your GitHub account name)/(your project name).git

        fi

        # change directory to project

        cd $currentDir/(your project directory)

        #Execute build task

        ant

1. Clique em **Guardar**.

1. No dashboard de Jenkins, paire sobre a tarefa que acabou de criar e clique na seta pendente para apresentar as opções de tarefa.

1. Clique em **criar agora**.

Jenkins, em seguida, irá criar um nó subordinado utilizando o modelo que criou na secção anterior e executar o script que especificou no passo compilação para esta tarefa.

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java].

<!-- URL List -->

[Centro de programadores do Azure Java]: https://azure.microsoft.com/develop/java/
[perfil de subscrição]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png