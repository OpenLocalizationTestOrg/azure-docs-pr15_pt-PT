<properties
    pageTitle="Script de desenvolvimento de ação com baseado em Linux HDInsight | Microsoft Azure"
    description="Como personalizar baseado em Linux HDInsight clusters com Script ação. Acções de script são uma forma para personalizar o Azure HDInsight clusters especificar definições de configuração de cluster ou instalando serviços adicionais, ferramentas ou outro software no cluster. "
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="larryfr"/>

# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ação de script com HDInsight

Acções de script são uma forma para personalizar o Azure HDInsight clusters especificar definições de configuração de cluster ou instalando serviços adicionais, ferramentas ou outro software no cluster. Pode utilizar as ações de script durante a criação de cluster ou num cluster em execução.

> [AZURE.NOTE] As informações neste documento são específicas a clusters baseado em Linux HDInsight. Para obter informações sobre como utilizar ações de script com clusters baseados no Windows, consulte o artigo [desenvolvimento de ação de Script com HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## <a name="what-are-script-actions"></a>Quais são as ações de script?

Acções de script são scripts de festa que executa o Azure em nós de cluster para efetuar alterações na configuração ou instalar software. Uma ação de script é executada como raiz e fornece os direitos de acesso completo a nós de cluster.

Acções de script podem ser aplicadas através dos seguintes métodos:

| Utilize esta opção para aplicar um script... | Durante a criação de cluster... | Num cluster em execução... |
| ----- |:-----:|:-----:|
| Portal do Azure | ✓ | ✓ |
| Azure PowerShell | ✓ | ✓ |
| Clip Azure | &nbsp; | ✓ |
| HDInsight .NET SDK | ✓ | ✓ |
| Modelo do Gestor de recursos do Azure | ✓ | &nbsp; |

Para mais informações sobre como utilizar estes métodos para aplicar acções de script, consulte o artigo [Personalizar HDInsight clusters utilizar ações de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Práticas recomendadas para o desenvolvimento de script

Quando desenvolver um script personalizado para um cluster de HDInsight, existem várias melhores práticas a ter em conta:

- [A versão de Hadoop-alvo](#bPS1)
- [A versão do SO-alvo](#bps10)
- [Fornecer estáveis ligações para recursos de script](#bPS2)
- [Utilizar recursos previamente compilados](#bPS4)
- [Certifique-se de que o script de personalização de cluster é idempotent](#bPS3)
- [Certifique-se de elevada disponibilidade da arquitetura de cluster](#bPS5)
- [Configurar os componentes personalizados para utilizar o armazenamento de Blobs do Azure](#bPS6)
- [Escrever informações STDOUT e STDERR](#bPS7)
- [Guardar ficheiros como ASCII com terminações das linhas LF](#bps8)
- [Utilizar a lógica de repetição para recuperar da breves erros](#bps9)

> [AZURE.IMPORTANT] Acções de script tem de concluir dentro de 60 minutos, ou irão tempo limite. Durante o nó aprovisionamento, o script é executado em simultâneo com outros processos de instalação e configuração. Concorrência em relação a recursos, tais como a largura de banda de CPU, hora ou de rede pode causar o script para demorar mais tempo a conclusão de que faz no seu ambiente de desenvolvimento.

### <a name="bPS1"></a>A versão de Hadoop-alvo

Versões diferentes do HDInsight têm diferentes versões do Hadoop serviços e componentes instalados. Se o script espera uma versão específica de um serviço ou componente, deve utilizar apenas o script com a versão do HDInsight, que inclui os componentes necessários. Pode encontrar informações sobre as versões de componente incluído com o HDInsight utilizando o documento de [controlo de versões do HDInsight componente](hdinsight-component-versioning.md) .

###<a name="bps10"></a>A versão do SO-alvo

Baseado em Linux HDInsight baseia-se a distribuição Ubuntu Linux. Versões diferentes do HDInsight dependem de versões diferentes do Ubuntu, que poderá efeito como o script comporta-se. Por exemplo, HDInsight 3.4 e versões anterior são baseados em versões de Ubuntu que utilizem Upstart. Versão 3.5 é baseado Ubuntu 16.04, que utiliza Systemd. Systemd e Upstart dependem comandos diferentes, para que o script deve ser escrito para trabalhar com ambos.

Outra diferença importante entre HDInsight 3.4 e 3.5 é que `JAVA_HOME` agora aponta para o Java 8.

Pode verificar a versão do SO utilizando `lsb_release`. Os seguinte fragmentos de código de script de instalação matiz demonstra como determinar se o script está em execução no Ubuntu 14 ou 16:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi
    ...
    if [[ $OS_VERSION == 16* ]]; then
        echo "Using systemd configuration"
        systemctl daemon-reload
        systemctl stop webwasb.service    
        systemctl start webwasb.service
    else
        echo "Using upstart configuration"
        initctl reload-configuration
        stop webwasb
        start webwasb
    fi
    ...
    if [[ $OS_VERSION == 14* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    elif [[ $OS_VERSION == 16* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    fi

Pode encontrar o script completo que contém estas fragmentos na https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para a versão do Ubuntu que é utilizado pelo HDInsight, consulte o documento [HDInsight componente versão](hdinsight-component-versioning.md) .

Para compreender as diferenças entre Systemd e Upstart, consulte o artigo [Systemd para utilizadores de Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornecer estáveis ligações para recursos de script

Deve certificar-se de que os scripts e recursos utilizados pelo script permanecem disponíveis em todo o tempo de vida do cluster e que as versões destes ficheiros não mudarem para a duração. Estes recursos são necessários se novos nós são adicionados ao cluster durante a dimensionamento operações.

É a melhor prática transferir e arquivar tudo numa conta de armazenamento do Windows Azure na sua subscrição.

> [AZURE.IMPORTANT] A conta de armazenamento utilizada tem de ser a conta de armazenamento predefinida para cluster ou de um contentor público, só de leitura no qualquer outra conta de armazenamento.

Por exemplo, os exemplos fornecidos pelo Microsoft são armazenados na conta de armazenamento de [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) , que é um contentor público, só de leitura mantido pela equipa HDInsight.

### <a name="bPS4"></a>Utilizar recursos previamente compilados

Para reduzir o tempo que demora a executar o script, evite operações compilar recursos de código fonte. Em vez disso, previamente compilar os recursos e armazenar a versão binária no armazenamento de Blobs do Azure para que-lo rapidamente pode ser transferido para o cluster a partir do seu script.

### <a name="bPS3"></a>Certifique-se de que o script de personalização de cluster é idempotent

Scripts tem de ser concebidos para ser idempotent no sentido em que se for o script executou várias vezes, -deve Certifique-se de que o cluster é devolvido para o mesmo estado sempre que é executado.

Por exemplo, se um script personalizado instala uma aplicação do /usr/local/bin em executar a sua primeira, em seguida, em cada execução subsequente o script deve verificar se a aplicação já existe na localização /usr/local/bin antes de prosseguir com os outros passos no script.

### <a name="bPS5"></a>Certifique-se de elevada disponibilidade da arquitetura de cluster

Baseado em Linux clusters HDInsight fornece dois nós de cabeça ativos dentro do cluster e executou o script são ações em ambos os nós. Se os componentes de que instalar o esperado apenas um nó cabeça, tem de estruturar um script que apenas instalará o componente sobre uma das duas nós cabeça cluster.

> [AZURE.IMPORTANT] Serviços de predefinidos instalados como parte do HDInsight foram concebidos para falhar ao longo entre os dois nós de cabeça, conforme necessário, no entanto esta funcionalidade não é expandida para componentes personalizados instalados, através de script ctions. Se precisar de componentes instalados, através de uma ação script altamente disponível, tem de implementar o seu próprio mecanismo activação pós-falha que utiliza duas nós cabeça disponíveis.

### <a name="bPS6"></a>Configurar os componentes personalizados para utilizar o armazenamento de Blobs do Azure

Componentes de que instala no cluster poderão ter uma configuração predefinida que utiliza o armazenamento de distribuído ficheiro Hadoop sistema (HDFS). HDInsight utiliza armazenamento de Blobs do Azure (WASB) como o armazenamento de predefinido. Este procedimento fornece um sistema de ficheiro compatíveis HDFS que persistir dados mesmo se cluster é eliminado. Deve configurar componentes instalar utilizar WASB em vez de HDFS.

Por exemplo, o seguinte procedimento copia o giraph examples.jar ficheiro a partir do sistema de ficheiros local para WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Escrever informações STDOUT e STDERR

As informações escritas STDOUT e STDERR durante a execução de script tem sessão iniciadas e podem ser visualizadas através da web Ambari IU.

> [AZURE.NOTE] Ambari só estará disponível se o cluster é criado com êxito. Se utilizar uma ação de script durante a criação de cluster e falha de criação, consulte a secção Resolução de problemas [que personalizar HDInsight clusters de utilizar a ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para outras formas de aceder a informações com sessão iniciada.

A maior parte dos utilitários e pacotes de instalação já escreverá informações para STDOUT e STDERR, no entanto, poderá pretender adicionar registo adicional. Para enviar texto para utilizar STDOUT `echo`. Por exemplo:

        echo "Getting ready to install Foo"

Por predefinição, `echo` irá enviar a cadeia para STDOUT. Para encaminhá-lo para STDERR, adicione `>&2` antes de `echo`. Por exemplo:

        >&2 echo "An error occurred installing Foo"

Isto redireciona as informações enviadas para STDOUT (1, que é a predefinição, não listada aqui,) para STDERR (2). Para mais informações sobre redirecionamento IO, consulte o artigo [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Para mais informações sobre como visualizar informações registadas pelo acções de script, consulte o artigo [Personalizar HDInsight clusters utilizando a ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

###<a name="bps8"></a>Guardar ficheiros como ASCII com terminações das linhas LF

Scripts de festa devem ser armazenados como formato ASCII, com linhas terminadas por LF. Se os ficheiros são guardados como UTF-8, que pode incluir uma marca de ordem Byte no início do ficheiro ou em conjunto com terminações das linhas de CRLF, que é comum editores do Windows, o script irá falhar com erros semelhantes ao seguinte:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a>Utilizar a lógica de repetição para recuperar da breves erros

Quando a transferência de ficheiros, instalar pacotes utilizando possam obter ou outras ações que transmitir dados através da internet, a ação poderá falhar devido a breves erros de redes. Por exemplo, pode ser recurso remoto que estão a comunicar com a uma falha ao longo para um nó de cópia de segurança.

Para fazer o script e são erros breves, pode implementar a lógica de repetição. Segue-se um exemplo de uma função que vai executar qualquer comando passado para o mesmo e (se o comando falhar,) Repetir até três vezes. -Lo irá aguardar dois segundos entre cada tentativa.

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

Seguem-se exemplos de como utilizar esta função.

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>Métodos de Helper para scripts personalizados

métodos de helper de ação de script são utilitários que pode utilizar ao escrever scripts personalizados. Estas são definidas em [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)e podem ser incluídas no seu scripts utilizando o seguinte procedimento:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Isto faz com que o seguintes assistentes disponíveis para utilização no seu script:

| Utilização de ajuda | Descrição |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Transfere um ficheiro a partir do URL de origem para o caminho do ficheiro especificado. Por predefinição, não irá substituir um ficheiro existente. |
| `untar_file TARFILE DESTDIR` | Extrai um ficheiro de alcatrão (utilizando `-xf`,) ao directório de destino. |
| `test_is_headnode` | Se executou num nó cabeça cluster, devolve 1; caso contrário, 0. |
| `test_is_datanode` | Se o nó atual for uma dados (trabalho), devolve um 1; caso contrário, 0. |
| `test_is_first_datanode` | Se o nó atual é o primeiro nó de dados (trabalhador) (denominado workernode0), devolve um 1; caso contrário, 0. |
| `get_headnodes` | Devolve o nome de domínio completamente qualificado da headnodes no cluster. Nomes estão delimitado por vírgulas. Uma cadeia vazia é devolvida erros. |
| `get_primary_headnode` | Obtém o nome de domínio completamente qualificado da headnode principal. Uma cadeia vazia é devolvida erros. |
| `get_secondary_headnode` | Obtém o nome de domínio completamente qualificado da headnode secundário. Uma cadeia vazia é devolvida erros. |
| `get_primary_headnode_number` | Obtém o sufixo numérico do headnode principal. Uma cadeia vazia é devolvida erros. |
| `get_secondary_headnode_number` | Obtém o sufixo numérico do headnode secundário. Uma cadeia vazia é devolvida erros. |

## <a name="commonusage"></a>Padrões de utilização comum

Esta secção fornece orientações sobre como implementar a algumas dos padrões de utilização comuns que poderão ocorrer ao escrever o seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Passar parâmetros para um script

Em alguns casos, o script precisem de parâmetros. Por exemplo, poderá ter a palavra-passe de administrador para o cluster para obter informações a partir de Ambari REST API.

Parâmetros passados para o script são conhecidos como _parâmetros posicionais_e são atribuídos a `$1` para o primeiro parâmetro `$2` para a segunda e para que-on. `$0`contém o nome do próprio script.

Valores passados para o script como parâmetros devem ser colocada entre plicas (') para o valor transmitido é tratado como um literal e tratamento especial não está atribuído a incluir carateres, como '!'.

### <a name="setting-environment-variables"></a>Definição de variáveis do ambiente

Definir uma variável de ambiente é executado pelos seguintes procedimentos:

    VARIABLENAME=value

Onde VARIABLENAME é o nome da variável. Para aceder a variável de após estes, utilize `$VARIABLENAME`. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável de ambiente denominada palavra-passe, utilizaria o seguinte procedimento:

    PASSWORD=$1

Em seguida, pode utilizar subsequente acesso às informações `$PASSWORD`.

Variáveis de ambiente definidas dentro do script só existirem no âmbito do script. Em alguns casos, poderá ter de adicionar variáveis de ambiente e uma altura de sistema que vão permanecer após ter sido concluído o script. Normalmente isto é para que os utilizadores que ligam ao cluster através do SSH possam utilizar os componentes instalados pelo seu script. Consegui-lo adicionando a variável de ambiente para `/etc/environment`. Por exemplo, o seguinte adiciona __HADOOP\_as por acesso Telefónico\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a localizações onde estão armazenados os scripts personalizados

Para um scripts utilizadas para personalizar um cluster de precisa de estar da conta de armazenamento predefinida para cluster ou, se na outra conta de armazenamento, num contentor público só de leitura. Se o script acede recursos localizados noutro local estes também necessitam de ser um acessível publicamente (público, pelo menos, só de leitura). Por exemplo poderá pretender transferir um ficheiro para o cluster utilizando `download_file`.

Armazenar o ficheiro numa conta de armazenamento Azure acessível pelo cluster (tal como a conta de armazenamento predefinida), irá fornecer acesso rápido, tal como este armazenamento está no interior da rede Azure.

### <a name="checking-the-operating-system-version"></a>Verificar a versão do sistema operativo

Versões diferentes do HDInsight dependem de versões específicas do Ubuntu. Poderão existir diferenças entre versões do sistema operativo que tem de dar entrada para o script. Por exemplo, poderá ter de instalar um binário que está associado a versão do Ubuntu.

Para verificar a versão do SO, utilize `lsb_release`. Por exemplo, o seguinte demonstra como fazer referência a um ficheiro de alcatrão diferente consoante a versão do SO:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi

## <a name="deployScript"></a>Lista de verificação para implementar uma acção de script

Eis os passos que seguiu quando a preparar para implementar estes scripts:

- Coloca os ficheiros que contêm os scripts personalizados num local que esteja acessível por nós do cluster durante a implementação. Isto pode ser qualquer um dos predefinido ou contas adicionais de armazenamento especificadas no momento da implementação de cluster ou qualquer outro contentor de armazenamento acessível publicamente.

- Adicione controlos para scripts para se certificar de que executam impotently, para que o script pode ser executado várias vezes no mesmo nó.

- Utilize um /tmp do diretório de ficheiros temporários para manter os ficheiros transferidos utilizados pelos scripts e, em seguida, limpá-los depois de tem executado scripts.

- Na eventualidade de ficheiros de configuração de serviço de Hadoop ou definições ao nível do SO foram alterados, poderá pretender reiniciar HDInsight serviços para que pode atende a qualquer definições ao nível do sistema operativo, tais como as variáveis de ambiente definir os scripts.

## <a name="runScriptAction"></a>Como executar uma ação de script

Pode utilizar as ações de script para personalizar HDInsight clusters utilizando o portal do Azure, Azure PowerShell, modelos de Gestor de recursos do Azure (processador) ou o .NET SDK HDInsight. Para obter instruções, consulte o artigo [como utilizar a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemplos de scripts personalizados

A Microsoft fornece scripts de exemplo para instalar componentes num HDInsight cluster. Os scripts de exemplo e obter instruções sobre como utilizá-las estão disponíveis nas ligações em baixo:

- [Instalar e utilizar matiz no HDInsight clusters](hdinsight-hadoop-hue-linux.md)
- [Instalar e utilizar R no HDInsight Hadoop clusters](hdinsight-hadoop-r-scripts-linux.md)
- [Instalar e utilizar Solr no HDInsight clusters](hdinsight-hadoop-solr-install-linux.md)
- [Instalar e utilizar Giraph no HDInsight clusters](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] Os documentos ligados acima são específicos a clusters baseado em Linux HDInsight. Para scripts que funcionam com o HDInsight baseados no Windows, consulte o artigo [desenvolvimento de ação de Script com HDInsight (Windows)](hdinsight-hadoop-script-actions.md) ou utilize as ligações disponíveis na parte superior de cada artigo.

##<a name="troubleshooting"></a>Resolução de problemas

Seguem-se que poderá encontrar ao utilizar scripts que ter desenvolvido de erros:

__Error__: `$'\r': command not found`. Por vezes, seguido de `syntax error: unexpected end of file`.

_Problema_: Este erro é causado quando as linhas de um script terminam com CRLF. Sistemas UNIX esperam apenas LF como o fim da linha.

Este problema com mais frequência ocorre quando o script é criado num ambiente do Windows, tal como CRLF é uma linha comuns a acabar para muitos editores de texto no Windows.

_Resolução_: se for uma opção no seu editor de texto, selecione o formato Unix ou LF para o fim da linha. Também pode utilizar os seguintes comandos num sistema Unix para alterar o CRLF para um LF:

> [AZURE.NOTE] Os comandos seguintes são praticamente equivalentes em que se devem alteram respectivo linha CRLF para LF. Selecione uma com base nos utilitários disponíveis no seu sistema.

| Comando | Notas |
| ------- | ----- |
| `unix2dos -b INFILE` | O ficheiro original vai ser cópia de segurança com um. Extensão BAK |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE irá conter uma versão com apenas os terminações LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Isto irá modificar o ficheiro diretamente sem criar um novo ficheiro |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` | OUTFILE irá conter uma versão com apenas os terminações LF.

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_Problema_: Este erro ocorre quando o script foi guardado como UTF-8 com uma marca de ordem Byte (BOM).

_Resolução_: guardar o ficheiro como ASCII ou como UTF-8 sem um BOM. Também pode utilizar o comando seguinte num sistema Linux ou Unix para criar um novo ficheiro sem BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Para o comando acima, substitua __INFILE__ o ficheiro que contém a BOM. __OUTFILE__ deve ser um novo nome de ficheiro, que irá conter o script sem BOM.

## <a name="seeAlso"></a>Próximos passos

* Saiba como [Personalizar HDInsight clusters utilizando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)

* Utilizar o [HDInsight .NET SDK referência](https://msdn.microsoft.com/library/mt271028.aspx) para obter mais informações sobre a criação de aplicações do .NET gerir HDInsight

* Utilize a [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) para saber como utilizar o resto para executar ações de gestão sobre clusters de HDInsight.
