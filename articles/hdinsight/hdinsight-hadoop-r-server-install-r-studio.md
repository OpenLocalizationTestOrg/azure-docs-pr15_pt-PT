<properties
    pageTitle="Instalar RStudio com o servidor de R na HDInsight (pré-visualização) | Microsoft Azure"
    description="Como instalar RStudio com o servidor de R na HDInsight (pré-visualização)."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# <a name="installing-rstudio-with-r-server-on-hdinsight-preview"></a>Instalação RStudio com o servidor de R na HDInsight (pré-visualização)

Existem vários ambientes de desenvolvimento integrado (IDE) disponíveis para R hoje, incluindo da Microsoft recentemente anunciada [R ferramentas para o Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS), uma família das ferramentas de ambiente de trabalho e de servidor de [RStudio](https://www.rstudio.com/products/rstudio-server/)ou com base em Eclipse do Walware [StatET](http://www.walware.de/goto/statet). Entre mais populares no Linux é o uso do [Servidor de RStudio](https://www.rstudio.com/products/rstudio-server/) que fornece um IDE baseada no browser para utilização por clientes remotos.  Instalar RStudio servidor no nó do limite de um cluster de HDInsight Premium fornece uma experiência de IDE completa para o desenvolvimento e execução dos R scripts com R servidor no cluster e pode ser consideravelmente mais produtivo que utilização predefinida da consola do R.

Este artigo vai aprender instalar a versão de (gratuito) da Comunidade do servidor de RStudio no nó do limite de um cluster utilizando um script personalizado. Se preferir a versão de Pro comercialmente licenciada do servidor de RStudio, tem de seguir as instruções de instalação a partir [Do servidor RStudio](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] Os passos neste documento requer um servidor de R num cluster de HDInsight e não irá funcionar corretamente se estiver a utilizar um cluster de HDInsight onde R foi instalado utilizando a [Ação de Script de R instalar](hdinsight-hadoop-r-scripts-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster de Azure HDInsight com R Server instalado. Para obter instruções, consulte o artigo [Introdução ao servidor de R sobre clusters de HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Um cliente SSH. Para as distribuições Linux e Unix ou Macintosh OS X, a `ssh` comando é fornecido com o sistema operativo. Para o Windows, recomendamos que [Cygwin](http://www.redhat.com/services/custom/cygwin/) com a [opção de antes OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU)ou [betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  


## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Instalar o RStudio num cluster utilizando um script personalizado

1. Identifica o nó do limite do cluster. Para um cluster de HDInsight com o servidor de R, segue-se a Convenção de nomenclatura para nó cabeça e nó de limite.

    * Nó de cabeça-`CLUSTERNAME-ssh.azurehdinsight.net`
    * Nó de limite-`R-Server.CLUSTERNAME-ssh.azurehdinsight.net` 

2. SSH para o nó do limite do cluster utilizando o padrão de nomenclatura acima. 
 
    * Se estiver a ligar de um cliente Linux, consulte o artigo [ligar a um cluster de baseado em Linux HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
    * Se estiver a ligar de um cliente do Windows, consulte o artigo [ligar a um cluster de baseado em Linux HDInsight utilizando betumes](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Assim que estiver ligado, tornam-se um utilizador de raiz no cluster. Na sessão SSH, utilize o seguinte comando.

        sudo su -

4. Transferir o script personalizado para instalar RStudio. Utilize o seguinte comando.

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Alterar as permissões no ficheiro de script personalizado e executar o script. Utilize os comandos seguintes.

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Se utilizou uma palavra-passe SSH ao criar um cluster de HDInsight com o servidor de R, pode ignorar este passo e prossiga para o próximo. Se tiver utilizado uma tecla SSH para criar o cluster, tem de definir uma palavra-passe para o utilizador SSH. Terá esta palavra-passe quando se liga ao RStudio. Execute os seguintes comandos. Quando lhe for pedido para a **palavra-passe Kerberos atual**, basta premir **ENTER**.  Tenha em atenção que tem de substituir `USERNAME` com um utilizador SSH para o seu cluster HDInsight.

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:
        
    Se a palavra-passe estiver definida com êxito, deverá ver uma mensagem como esta.

        passwd: password updated successfully


    Sair da sessão SSH.

7. Criar um túnel SSH ao cluster mapeando `localhost:8787` no cluster HDInsight para o computador cliente. Tem de criar um túnel SSH antes de abrir uma nova sessão do browser.

    * Num cliente Linux ou um cliente do Windows com [Cygwin](http://www.redhat.com/services/custom/cygwin/) , em seguida, abra uma sessão de terminal e utilize o seguinte comando.

            ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
            
        Substitua o **nome de utilizador** por um utilizador SSH para o seu cluster HDInsight e substituir o **nome de cluster** com o nome do seu cluster HDInsight também pode utilizar uma chave SSH em vez de uma palavra-passe ao adicionar`-i id_rsa_key`     

    * Se utilizar um cliente do Windows com betumes, em seguida

        1.  Abra betumes e introduza as suas informações de ligação. Se não estiver familiarizado com betumes, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como utilizá-la com HDInsight.
        2.  Na secção de **categoria** à esquerda da caixa de diálogo, expanda a **ligação**, expanda **SSH**e, em seguida, selecione **túneis**.
        3.  Forneça as seguintes informações no formulário de **Opções de reencaminhamento de portas SSH de controlar** :

            * **Porta de origem** - a porta no cliente que pretende reencaminhar. Por exemplo, **8787**.
            * **Destino** - o destino que deve ser mapeado para o computador cliente local. Por exemplo, **localhost:8787**.

            ![Criar um túnel SSH] (./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Criar um túnel SSH")

        4. Clique em **Adicionar** para adicionar as definições e, em seguida, clique em **Abrir** para abrir uma ligação de SSH.
        5. Quando lhe for pedido, inicie sessão no servidor. Isto irá estabelecer uma sessão SSH e ativar o túnel.

8. Abra um browser e introduza o seguinte URL com base na porta que introduziu para o túnel.

        http://localhost:8787/ 

9. Vai ser-lhe para introduzir o SSH nome de utilizador e palavra-passe para ligar ao cluster. Caso tenha utilizado uma chave SSH ao criar o cluster, tem de introduzir a palavra-passe que criou no passo 5 acima.

    ![Ligar a R Studio] (./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Criar um túnel SSH")

10. Para testar a instalação RStudio foi efetuada com êxito, pode executar um teste script que executa R MapReduce e motores de tarefas com base no cluster. Regresse à consola SSH e introduza os seguintes comandos para transferir o script de teste para ser executado no RStudio.

    * Se tiver criado um cluster de Hadoop com R, utilize este comando.
        
            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

    * Se tiver criado um cluster de motores com R, utilize este comando.

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. RStudio, verá o script de teste que transferiu. Faça duplo clique no ficheiro para abri-lo, selecionar o conteúdo do ficheiro e, em seguida, clique em **Executar**. Deverá ver o resultado no painel da **consola** .
 
    ![Testar a instalação] (./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Testar a instalação")

Outra opção seria escrever `source(testhdi.r)` ou `source(testhdi_spark.r)` para executar o script.

## <a name="see-also"></a>Consulte também

- [Opções de contexto para o servidor de R em HDInsight clusters de calcular](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure opções de armazenamento para o servidor de R em HDInsight premium](hdinsight-hadoop-r-server-storage.md)


 
