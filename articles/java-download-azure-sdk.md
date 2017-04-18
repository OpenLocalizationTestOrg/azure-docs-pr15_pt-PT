<properties 
    pageTitle="Transferir o Azure SDK para Java" 
    description="Saiba como transferir o SDK do Azure para Java, com o código de exemplo fornecido para projetos Maven e passos básicos de instalação para o Tookit Azure para Eclipse." 
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

# <a name="download-the-azure-sdk-for-java"></a>Transferir o Azure SDK para Java

Este artigo contém as instruções para transferir e instalar as bibliotecas do Azure para Java.

**Nota:** As bibliotecas do Azure para Java são distribuídas na [licença Apache, versão 2.0][license].

## <a name="azure-libraries-for-java---manual-download"></a>Azure bibliotecas para Java - transferir Manual

Para instalar manualmente as bibliotecas do Azure para Java, clique em <http://go.microsoft.com/fwlink/?LinkId=690320> para transferir um ficheiro ZIP que contém todas as bibliotecas e todas as dependências.

Assim que tiver transferido o ficheiro zip para o seu computador, extrair o conteúdo e utilize uma das seguintes opções para adicionar os ficheiros para caixa ao seu projeto:

* Importe os ficheiros para caixa para o seu projeto Java no Eclipse.

* Configure o **Construir o caminho** para o seu projeto Java no Eclipse para incluir o caminho para os ficheiros para caixa.

Para obter informações detalhadas sobre como configurar caminhos de compilação no Eclipse, consulte o artigo [Java construir caminho] no Web site da Eclipse.

**Nota:** Consulte o License e ThirdPartyNotices.txt do ficheiro dentro de postal para licença e outras informações.

## <a name="azure-libraries-for-java---building-with-maven"></a>Azure bibliotecas para Java - construir com Maven

### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Passo 1 - configurar o seu projeto para utilizar Maven para construir

Para criar Maven projetos no Eclipse que utilizam as Azure bibliotecas para Java, seguindo as instruções em [Introdução ao Azure as bibliotecas de gestão de Java] [ maven-getting-started] artigo. 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Passo 2 - configurar as definições de Maven com as dependências necessárias

Assim que o projeto tiver sido configurado para utilizar Maven para construir, pode adicionar o as dependências necessárias para o seu ficheiro de pom.xml utilizando sintaxe como o exemplo seguinte. Tenha em atenção que é necessário adicionar todas as dependência que se encontra listada no seguinte exemplo; só tem de adicionar as dependências específicas que exige o seu projeto.

> [AZURE.NOTE] Dentro de cada `<version>` elemento no exemplo seguinte, substitua os marcadores de posição de "n.n.n" neste exemplo com os números de versão válida, que podem ser obtidos a partir do [Azure repositório de bibliotecas no Maven].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalar o Toolkit Azure para Eclipse

Esta secção contém as instruções básicas para instalar o Toolkit de Azure para Eclipse; Para obter instruções detalhadas, consulte o artigo [instalar o Toolkit de Azure para Eclipse].

### <a name="prerequisites"></a>Pré-requisitos

1. Sistemas de operting Windows listados no artigo [o que há de novo no Toolkit de Azure para Eclipse] .
1. Macintosh ou Linux sistemas operting listados no artigo [o que há de novo no Toolkit de Azure para Eclipse] .
1. -Eclipse IDE para os programadores de EE Java, Indigo ou posterior. Isto pode ser transferido das <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Passos básicos de instalação

1. Na Eclipse, no menu **Ajuda** , selecione **Instalar Software de novo**.
1. Introduza a localização de site <http://dl.microsoft.com/eclipse> e prima **Enter**.
1. Selecione os itens para ser instalado e clique em **Concluir**.

O Toolkit de Azure para Eclipse utiliza a versão mais recente do Azure SDK. Isto pode ser transferido utilizando o instalador do plataforma Web (WebPI) no <http://go.microsoft.com/fwlink/?LinkID=252838>. No entanto, se não tiver instalada, quando cria projeto implementação Azure primeiro, o Toolkit de Azure para Eclipse instalará automaticamente a versão adequada do Azure SDK.

## <a name="see-also"></a>Consulte também

[Toolkit Azure para Eclipse]

[Instalar o Toolkit Azure para Eclipse] 

[Criar uma aplicação do mundo Olá Azure no Eclipse]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Repositório de bibliotecas Azure no Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Caminho de compilação Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Quais são as novidades no Azure Toolkit para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333
