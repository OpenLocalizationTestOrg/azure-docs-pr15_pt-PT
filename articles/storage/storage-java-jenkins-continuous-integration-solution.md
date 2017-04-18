<properties 
    pageTitle="Utilizar o armazenamento Azure com uma solução de integração contínuo Jenkins | Microsoft Azure" 
    description="Neste tutorial mostrar como utilizar o serviço de Blobs do Azure como um repositório de compilação artefactos criados por uma solução de integração contínua Jenkins." 
    services="storage" 
    documentationCenter="java" 
    authors="dineshmurthy" 
    manager="jahogg" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="dinesh"/>

# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Utilizar o armazenamento do Windows Azure com uma solução de integração de contínua Jenkins

## <a name="overview"></a>Descrição geral

As seguintes informações mostram como utilizar armazenamento de BLOBs como um repositório de compilação artefactos criado por uma solução de integração contínua Jenkins (CI), ou como uma origem de ficheiros transferíveis para ser utilizado no processo de criação. Um dos cenários onde pretende encontrar esta útil é quando estiver a codificação num ambiente de desenvolvimento ágil (utilizando Java ou noutros idiomas), compilações são executadas com baseadas num integração contínua e precisar de um repositório para sua artefactos compilação, para que poderia, por exemplo, partilhá-los com outros membros da organização, os seus clientes, ou manter um arquivo. Cenário outra é quando a tarefa de compilação propriamente dito requer noutros ficheiros, por exemplo, dependências para transferir como parte da compilação de entrada.

Neste tutorial vai utilizar o plug-in de armazenamento do Azure para CI Jenkins disponibilizados pela Microsoft.

## <a name="overview-of-jenkins"></a>Descrição geral de Jenkins ##

Jenkins permite a integração contínua de um projeto de software, permitindo que os programadores integrar facilmente as suas alterações de código e tenham compilações produzidos automaticamente e frequentemente, portanto aumentar a produtividade dos programadores. Compilações têm versões e compilação artefactos podem ser carregados para vários repositórios. Este tópico irá mostrar como utilizar o armazenamento de Blobs do Azure como o repositório dos erros compilação. Mostrará também como transferir dependências do armazenamento de Blobs do Azure.

Obter mais informações sobre Jenkins podem ser encontradas em [Jenkins cumprir](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Vantagens de utilizar o serviço de BLOBs ##

Vantagens de utilizar o serviço BLOBs para alojar o seu artefactos de compilação de desenvolvimento ágil incluem:

- Elevada disponibilidade do seu artefactos de compilação e/ou dependências transferíveis.
- Desempenho quando a sua solução Jenkins CI os carregamentos pendentes seu artefactos de compilação.
- Desempenho quando seus clientes e parceiros transferir os seus artefactos de compilação.
- Controlo sobre políticas de acesso do utilizador, com uma escolha entre acesso anónimo, acesso de assinatura com base em expiração acesso partilhado, privado acesso, etc.

## <a name="prerequisites"></a>Pré-requisitos ##

Terá o seguinte procedimento para utilizar o serviço de Blobs com a sua solução Jenkins CI:

- Uma solução de integração de contínua Jenkins.

    Se não possui atualmente uma solução Jenkins CI, pode executar uma solução de Jenkins CI utilizando a seguinte técnica:

    1. Num computador com capacidade de Java, transfira jenkins.war da <http://jenkins-ci.org>.
    2. Numa linha de comandos que é aberto para a pasta que contém jenkins.war, execute:

        `java -jar jenkins.war`

    3. No seu browser, abra `http://localhost:8080/`. Isto vai abrir dashboard de Jenkins, que irá utilizar para instalar e configurar o plug-in de armazenamento do Windows Azure.

        Enquanto uma solução Jenkins CI típica seria ser configurada para executar como um serviço, executar a guerra Jenkins na linha de comandos serão suficiente para este tutorial.

- Uma conta Azure. Pode inscrever-se para uma conta Azure na <http://www.azure.com>.

- Uma conta de armazenamento Azure. Se ainda não tiver uma conta de armazenamento, pode criar um utilizando os passos no [criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

- Familiaridade com a solução Jenkins CI é recomendada, mas não é necessário, tal como o seguinte conteúdo irá utilizar um exemplo de básica para mostrar-lhe os passos necessários quando utilizar o serviço de BLOBs como um repositório de Jenkins CI construir artefactos.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Como utilizar o serviço BLOBs com Jenkins CI ##

Para utilizar o serviço de Blobs com Jenkins, terá de instalar o plug-in de armazenamento do Windows Azure, configurar o plug-in para utilizar a sua conta de armazenamento e, em seguida, crie uma ação de compilação pós que carrega os artefactos compilação à sua conta de armazenamento. Estes passos são descritos nas secções seguintes.

## <a name="how-to-install-the-azure-storage-plugin"></a>Como instalar o plug-in de armazenamento do Windows Azure ##

1. No dashboard de Jenkins, clique em **Gerir Jenkins**.
2. Na página **Gerir Jenkins** , clique em **Gerir plug-ins**.
3. Clique no separador **disponíveis** .
4. Na secção **Artefacto Uploaders** , selecione o **Plug-in de armazenamento do Windows Azure**.
5. Clique em **instalar sem reiniciar** ou **agora a transferir e instalar o após reiniciar**.
6. Reinicie o Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Como configurar o plug-in de armazenamento do Windows Azure para utilizar a sua conta de armazenamento ##

1. No dashboard de Jenkins, clique em **Gerir Jenkins**.
2. Na página **Gerir Jenkins** , clique em **Configurar o sistema**.
3. Na secção **Configuração da conta do Microsoft Azure armazenamento** :
    1. Introduza o seu nome de conta de armazenamento, que pode obter a partir do [Azure Portal](https://portal.azure.com).
    2. Introduza a sua chave de conta de armazenamento, também pode ser obtido a partir do [Azure Portal](https://portal.azure.com).
    3. Utilize o valor predefinido para o **URL do ponto final de serviço de BLOBs** se estiver a utilizar a nuvem Azure pública. Se estiver a utilizar uma nuvem Azure diferentes, utilize o ponto final conforme especificado no [Portal do Azure](https://portal.azure.com) para a sua conta de armazenamento. 
    4. Clique em **validar credenciais de armazenamento** para validar a sua conta de armazenamento. 
    5. [Opcional] Se tiver contas de armazenamento adicional que pretende disponibilizados para sua CI Jenkins, clique em **Adicionar mais contas de armazenamento**.
    6. Clique em **Guardar** para guardar as definições.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Como criar uma ação de compilação pós que carrega os artefactos compilação à sua conta de armazenamento ##

Para fins de instrução, primeiro irá precisamos de criar uma tarefa que irá criar vários ficheiros e, em seguida, adicionar a ação de compilação pós para carregar os ficheiros à sua conta de armazenamento.

1. No dashboard de Jenkins, clique em **Novo Item**.
2. Nome da tarefa **MyJob**, clique em **criar um projeto de software gratuitos de estilo**e, em seguida, clique em **OK**.
3. Na secção **Criar** da configuração da tarefa, clique em **Adicionar compilar passo** e selecione **executar o Windows batch comando**.
4. **Comando**, utilize os comandos seguintes:

        md text
        cd text
        echo Hello Azure Storage from Jenkins > hello.txt
        date /t > date.txt
        time /t >> date.txt
 
5. Na secção **Ações de compilação pós** da configuração da tarefa, clique em **Adicionar ação de compilação pós** e selecione **carregar artefactos ao armazenamento de Blobs do Azure**.
6. **Nome da conta de armazenamento**, selecione a conta de armazenamento para utilizar.
7. **Nome do contentor**, especifique o nome do contentor. (O contentor será criado se-ainda não existir quando são carregados erros compilação.) Pode utilizar variáveis de ambiente, por isso, para que este exemplo introduza **${JOB_NAME}** como o nome do contentor.

    **Sugestão:**
    
    Abaixo o **comando** secção onde introduziu um script para **executar o Windows batch comando** é uma ligação para as variáveis de ambiente reconhecido pelo Jenkins. Clique em dessa ligação para obter informações sobre os nomes de variáveis de ambiente e descrições. Tenha em atenção que as variáveis de ambiente que contêm carateres especiais, tais como a variável de ambiente de **BUILD_URL** , não são permitidas como um nome para o contentor ou caminho virtual comuns.

8. Clique em **tornar o novo contentor público por predefinição** para este exemplo. (Se quiser utilizar um contentor privado, terá criar uma assinatura de acesso partilhado para permitir o acesso. Que está fora do âmbito deste tópico. Pode obter mais informações sobre assinaturas de acesso partilhado ao [Utilizar partilhada acesso assinaturas (SA)](storage-dotnet-shared-access-signature-part-1.md).)
9. [Opcional] Clique em **Limpar contentor antes de carregar** , se pretender que o contentor para ser limpo do conteúdo antes da compilação artefactos são carregados (deixá-la desmarcada se não pretender limpar o conteúdo do contentor).
10. Para a **Lista de artefactos para carregar**, introduza * *texto /*. txt**.
11. Para **comuns caminho virtual para artefactos carregados**, para fins neste tutorial, introduza **${CONSTRUIR\_ID} / ${CONSTRUIR\_número}**.
12. Clique em **Guardar** para guardar as definições.
13. No dashboard de Jenkins, clique em **Criar agora** para executar **MyJob**. Examine a saída da consola de estado. Mensagens de estado para o armazenamento do Azure serão incluídas no resultado da consola quando inicia a ação de compilação pós para carregar artefactos de compilação.
14. Após a conclusão com êxito da tarefa, pode examinar compilação erros ao abrir o blob público.
    1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    2. Clique em **armazenamento**.
    3. Clique no nome de conta de armazenamento que utilizou para Jenkins.
    4. Clique em **contentores**.
    5. Clique no contentor chamado **myjob**, que é a versão do nome da tarefa que lhe atribuída quando criou a tarefa de Jenkins em minúsculas. Nomes de contentor e blob estão em minúsculas (e entre maiúsculas e minúsculas) no Azure armazenamento. Na lista de blobs do contentor denominada **myjob** deverá ver **hello.txt** e **date.txt**. Copie o URL para um destes itens e abra-o no seu browser. Irá ver o ficheiro de texto que foi carregado como um artefacto de compilação.

Apenas uma ação de compilação pós que os carregamentos pendentes artefactos ao armazenamento de Blobs do Azure pode ser criada por tarefa. Note que a ação de compilação pós única para carregar artefactos ao armazenamento de Blobs do Azure pode especificar ficheiros (incluindo carateres universais) e diferentes caminhos para ficheiros numa **Lista de artefactos para carregar** utilizando um ponto e vírgula como um separador. Por exemplo, se sua compilação Jenkins produz para caixa ficheiros e TXT na pasta de **Criar** a área de trabalho e que pretende carregar ambas ao armazenamento de Blobs do Azure, utilize o seguinte para o valor de **Lista de artefactos para carregar** : **construir /\*.jar; compilação /\*. txt**. Também pode utilizar dois pontos duplos sintaxe para especificar um caminho para utilizar dentro do nome de Blobs. Por exemplo, se pretender jarros obter carregado utilizando **binários** nos ficheiros TXT e o caminho BLOBs obter carregado utilizando **anúncios** no caminho BLOBs, utilize o seguinte procedimento para o valor de **Lista de artefactos para carregar** : **construir /\*. jar::binaries; compilar /\*. txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Como criar um passo de compilação as transferências do armazenamento de Blobs do Azure ##

Os seguintes passos mostram como configurar um passo de compilação para transferir itens a partir do armazenamento de Blobs do Azure. Seria útil se pretender incluir itens na sua compilação, por exemplo, jarros manter no Azure blob armazenamento.

1. Na secção **Criar** da configuração da tarefa, clique em **Adicionar compilar passo** e selecione **Transferir a partir do armazenamento de Blobs do Azure**.
2. **Nome da conta de armazenamento**, selecione a conta de armazenamento para utilizar.
3. **Nome do contentor**, especifique o nome do contentor que tenha blobs que pretende transferir. Pode utilizar variáveis de ambiente.
4. **Nome de BLOBs**, especifique o nome de Blobs. Pode utilizar variáveis de ambiente. Além disso, pode utilizar um asterisco, como um carácter universal depois de especificar da ou das letras iniciais do nome do blob. Por exemplo, **projeto\* ** seria especificar todos os blobs cujos nomes comecem com o **project**.
5. [Opcional] Para **Transferir o caminho**, especifique o caminho no computador onde pretende transferir ficheiros de armazenamento de Blobs do Azure Jenkins. Variáveis de ambiente podem também ser utilizadas. (Se não fornecer um valor para **Transferir o caminho**, os ficheiros a partir do armazenamento de Blobs do Azure serão transferidos para a área de trabalho do projecto.)

Se tiver itens adicionais que pretende transferir a partir do armazenamento de Blobs do Azure, pode criar passos adicionais de compilação.

Depois de executar uma compilação, pode verificar a saída da consola do histórico de compilação ou veja a sua localização de transferência, para ver se blobs esperado foram transferidos com êxito.  

## <a name="components-used-by-the-blob-service"></a>Componentes utilizados pelo serviço Blob ##

A seguinte fornece uma descrição geral dos componentes de serviço do Blob.

- **Conta de armazenamento**: todo o acesso ao armazenamento do Azure é feito através de uma conta de armazenamento. Este é o mais alto nível do espaço de nomes para aceder a blobs. Uma conta pode conter um número ilimitado de contentores, desde que o tamanho total é em 100TB.
- **Contentor**: um contentor fornece um agrupamento de um conjunto de blobs. Todos os blobs tem de ser um contentor. Uma conta pode conter um número ilimitado de contentores. Um contentor pode armazenar um número ilimitado de blobs.
- **BLOBs**: um ficheiro de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no armazenamento Azure: blobs bloco e de página. A maior parte dos ficheiros são blobs do bloco. Blob um único bloco pode ser 200GB de tamanho. Neste tutorial, utiliza blobs bloco. Página blobs, outro tipo de BLOBs, podem ser até 1TB em tamanho e são mais eficiente quando os intervalos de bytes num ficheiro forem modificados frequentemente. Para mais informações sobre blobs, consulte [Noções sobre bloco Blobs, acrescentar Blobs e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Formato de URL**: Blobs são endereçáveis utilizando o formato de URL seguinte:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
    
    (O formato acima aplica-se para a nuvem Azure pública. Se estiver a utilizar uma nuvem Azure diferentes, utilize o ponto final no interior do [Azure Portal](https://portal.azure.com) para determinar o seu ponto final do URL.)

    No formato de acima, `storageaccount` representa o nome da sua conta de armazenamento, `container_name` representa o nome do seu contentor, e `blob_name` representa o nome do seu blob, respetivamente. Dentro do nome do contentor, pode ter vários caminhos, separados por uma barra, **/**. O nome do contentor de exemplo neste tutorial foi **MyJob**, e **${CONSTRUIR\_ID} / ${CONSTRUIR\_número}** foi utilizado para o caminho virtual comuns, que resulta em blob ter um URL da seguinte forma:

    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="next-steps"></a>Próximos passos

- [Reunir Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins)
- [Armazenamento Azure SDK para Java](https://github.com/azure/azure-storage-java)
- [Referência SDK do cliente de armazenamento Azure](http://dl.windowsazure.com/storage/javadoc/)
- [Serviços de armazenamento Azure REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx )
- [Blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Para mais informações, consulte também o [Centro de programadores do Java](https://azure.microsoft.com/develop/java/).