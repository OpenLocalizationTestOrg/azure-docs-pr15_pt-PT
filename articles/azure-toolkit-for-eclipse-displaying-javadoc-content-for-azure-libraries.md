<properties
    pageTitle="Apresentar conteúdo de Javadoc no Eclipse para o pacote de bibliotecas Azure para Java"
    description="Como apresentar o conteúdo de Javadoc para as bibliotecas de Azure Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Apresentar conteúdo de Javadoc no Eclipse para o pacote de bibliotecas Azure para Java #

O conteúdo de Javadoc para as bibliotecas do Azure para Java pode ser visualizado no seu ambiente Eclipse associando o conteúdo de Javadoc às bibliotecas Azure para Java. Os passos seguintes mostram-lhe como utilizar esta funcionalidade dentro Eclipse.

Este procedimento assume que já adicionou a biblioteca do Azure para Java para o seu caminho de compilação.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Para apresentar conteúdo Javadoc Eclipse para as bibliotecas do Azure para Java ##

* Dentro Project Explorer do Eclipse, na secção **Referenciados bibliotecas** do projeto, abra o menu de contexto para a biblioteca do Azure para Java JAR. Por exemplo, **microsoft-windowsazure api 0.1.0.jar** (o número da versão pode ser diferentes, dependentes qual a versão que tiver instalado).
* Clique em **Propriedades**.
* Na caixa de diálogo **Propriedades** , no painel do lado esquerdo, clique em **Localização Javadoc**. É apresentada a caixa de diálogo **Localização Javadoc** .
* Pode especificar um **URL de Javadoc**ou um **Javadoc arquivo**.
    * Se optar por especificar um **URL de Javadoc**, utilize os URLs como **http://dl.windowsazure.com/javadoc** ou **http://dl.windowsazure.com/storage/javadoc**.
    * Se optar por utilizar **Javadoc arquivo**, pode especificar um ficheiro externo ou um ficheiro de área de trabalho.
    Faça a sua escolha e procurar/validar conforme necessário. O exemplo seguinte associa as bibliotecas do Azure para Java a correspondente Javadoc JAR que tenha sido transferida localmente para uma pasta denominada **c:\MyAzureJARs**.
    ![][ic553487]
* *Opcional*: clique em **Validar**. Potenciais problemas com a Javadoc JAR podem ser apresentados aqui.
* Clique em **OK**.

Depois de associada à biblioteca, o conteúdo de Javadoc deverá apresentar no seu IDE Eclipse. Por exemplo, se `blob` é definida do tipo de `CloudBlockBlob` no seu código, segue-se um exemplo de conteúdo de Javadoc que é apresentado quando escreve `blob.acquireLease` no código:

![][ic553488]

## <a name="see-also"></a>Consulte também ##

[Toolkit Azure para Eclipse][]

[Criar uma aplicação do mundo Olá Azure no Eclipse][]

[Instalar o Toolkit Azure para Eclipse][] 

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png