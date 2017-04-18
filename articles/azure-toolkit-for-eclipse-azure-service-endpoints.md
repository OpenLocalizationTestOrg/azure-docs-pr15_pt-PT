<properties
    pageTitle="Pontos finais de serviço do Azure"
    description="Descreve as definições de ponto final de serviço do Azure no Azure Toolkit para Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# <a name="azure-service-endpoints"></a>Pontos finais de serviço do Azure #

Pontos finais de serviço Azure determinam que se a aplicação é implementada e gerida pela plataforma Azure global, Azure operado pela 21Vianet na China, ou um privado a plataforma Azure. A caixa de diálogo **Pontos finais de serviço** permite-lhe especificar quais os pontos finais de serviço que pretende utilizar. Para abrir a caixa de diálogo **Pontos finais de serviço** , dentro de Eclipse, clique em **janela**, clique em **Preferências**, expanda **Azure**e, em seguida, clique em **Pontos finais de serviço**. Definição do campo **Ativo definir** determina quais os pontos finais de serviço Azure serão utilizados para os projectos Azure na área de trabalho atual.

A imagem seguinte mostra a caixa de diálogo **Pontos finais de serviço** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Para definir os pontos finais de serviço ##

Na caixa de diálogo **Pontos finais de serviço** , execute uma das seguintes ações:

* Se pretender utilizar a plataforma Azure global, na lista pendente **Definir ativo** , selecione **windowsazure.com** e clique em **OK**.
* Se pretender utilizar Azure operado pela 21Vianet na China, na lista pendente **Definir ativo** , selecione **windowsazure.cn (China)** e clique em **OK**.
* Se pretender utilizar uma plataforma Azure privada:
    1. Clique em **Editar**.
    2. É apresentada uma caixa de diálogo, que indica que a caixa de diálogo **Pontos finais de serviço** será fechada, e o ficheiro de conjuntos de preferência será aberto. Clique em **OK**.
    3. No ficheiro de preferencesets.xml, crie um novo `preferenceset` elemento. Para este elemento novo, crie `name`, `blob`, `management`, `portalURL` e `publishsettings` atributos e adicionar os valores das mesmas que correspondem à sua plataforma Azure privada. Pode utilizar os valores fornecidos para a existente `preferenceset` elementos como modelos. **Nota**: O valor utilizado para o `blob` atributo tem de conter o texto "blob" no URL.
    4. Guarde e feche preferencesets.xml.
    5. Voltar a abrir a caixa de diálogo **Pontos finais de serviço** .
    6. Na lista pendente **Definir ativo** , selecione o conjunto de activos que criou e clique em **OK**.
    7. Depois de criar a plataforma Azure privada `preferenceset` elemento, pode alterar os valores atribuídos à mesma ao clicar no botão **Editar** na caixa de diálogo **Ponto final de serviços** . Também pode criar múltiplas plataformas Azure privada `preferenceset` elementos, se pretender.

## <a name="see-also"></a>Consulte também ##

[Toolkit Azure para Eclipse][]

[Instalar o Toolkit Azure para Eclipse][] 

[Criar uma aplicação do mundo Olá Azure no Eclipse][]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png
