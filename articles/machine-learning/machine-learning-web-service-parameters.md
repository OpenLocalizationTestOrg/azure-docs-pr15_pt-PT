<properties 
    pageTitle="Utilizar o Azure Machine parâmetros do serviço Web de aprendizagem | Microsoft Azure" 
    description="Como utilizar parâmetros do serviço Web Azure máquina formação para modificar o comportamento do seu modelo quando o serviço web é acedido." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="raymondl;garye"/>

#<a name="use-azure-machine-learning-web-service-parameters"></a>Utilizar o Azure Machine parâmetros do serviço Web de aprendizagem

Um serviço web de aprendizagem do Azure máquina é criado por uma experiência que contém módulos com parâmetros configuráveis de publicação. Em alguns casos, poderá querer alterar o comportamento de módulo enquanto o serviço web estiver em execução. *Parâmetros de serviço Web* permitem-lhe executar esta tarefa. 

Um exemplo comum está a configurar a [Importar dados] [ reader] módulo para que o utilizador do serviço web publicada pode especificar uma origem de dados diferente, quando o serviço web é acedido. Ou configurar a [Exportar dados] [ writer] módulo para que pode ser especificado um destino diferente. Outros exemplos incluem alterar o número de bits para a [Funcionalidade Hashing] [ feature-hashing] módulo ou o número de pretendido funcionalidades para a [seleção de funcionalidades do filtro de baseado] [ filter-based-feature-selection] módulo. 

Pode definir parâmetros do serviço Web e associá-las a um ou mais parâmetros de módulo na sua experiência e pode especificar se forem necessárias ou opcional. O utilizador do serviço web, em seguida, pode fornecer os valores para estes parâmetros quando chamada do serviço web. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##<a name="how-to-set-and-use-web-service-parameters"></a>Como configurar e utilizar parâmetros do serviço Web

Definir um parâmetro de serviço Web clicando no ícone junto ao parâmetro para um módulo e selecionando "Definir como parâmetro do serviço web". Este procedimento cria um novo serviço parâmetro da Web e liga-lo a esse parâmetro módulo. Em seguida, quando o serviço web é acedido, o utilizador pode especificar um valor para o parâmetro do serviço Web e o mesmo é aplicado ao parâmetro de módulo.

Depois de definir um parâmetro de serviço Web, este fica disponível para qualquer outro parâmetro módulo na experiência. Se definir um parâmetro de serviço Web associados a um parâmetro para um módulo, pode utilizar esse mesmo parâmetro do serviço Web para outros módulo, desde que o parâmetro espera o mesmo tipo de valor. Por exemplo, se o parâmetro do serviço Web for um valor numérico, em seguida, pode apenas ser utilizada para os parâmetros de módulo esperava um valor numérico. Quando o utilizador define um valor para o parâmetro do serviço Web, será aplicada a todos os parâmetros de módulo associado.

Pode tomar a decisão de fornecer um valor predefinido para o parâmetro do serviço Web. Se fizer, o parâmetro é opcional para o utilizador do serviço web. Se não fornecer um valor predefinido, o utilizador é necessário introduzir um valor quando o serviço web é acedido.

A documentação da API para o serviço web inclui informações para o utilizador do serviço web sobre como especificar o parâmetro do serviço Web através de programação ao aceder ao serviço web.

>[AZURE.NOTE] A documentação da API para um serviço web clássica é fornecida através da ligação **API página de ajuda** no serviço web do **DASHBOARD** no máquina aprendizagem Studio. A documentação da API para um novo serviço web é fornecida através do portal de [Serviços Web do Azure máquina aprendizagem](https://services.azureml.net/Quickstart) nas páginas **consumir** e **Swagger API** do seu serviço web.


##<a name="example"></a>Exemplo

Por exemplo, imaginemos temos uma experiência com um [Exportar dados] [ writer] módulo que envia informações ao armazenamento de Blobs do Azure. Vamos irá definir um parâmetro de serviço Web com o nome "Blob caminho" que permite que o utilizador do serviço web alterar o caminho para o armazenamento de BLOBs quando o serviço é acedido.

1.  No computador aprendizagem Studio, clique em [Exportar dados] [ writer] módulo para selecioná-la. São apresentadas as respetivas propriedades no painel Propriedades à direita da tela experiência.

2.  Especifique o tipo de armazenamento:

    - Em **Especifique o destino de dados**, selecione "Armazenamento de Blobs do Azure".
    - Em **Especifique o tipo de autenticação**, selecione "Conta".
    - Introduza as informações da conta para o armazenamento de Blobs do Azure. 
    <p />

3.  Clique no ícone à direita do **caminho para blob que começa com o parâmetro do contentor**. Será apresentada da seguinte forma:

    ![Ícone de parâmetro de serviço Web][icon]

    Selecione "Definir como parâmetro do serviço web".

    É adicionada uma entrada em **Parâmetros do serviço Web** na parte inferior do painel de propriedades com o nome "Caminho para o início com um contentor de blob". Este é o parâmetro do serviço Web que está agora associado estes [Exportar dados] [ writer] parâmetro módulo.

4.  Para mudar o nome o parâmetro do serviço Web, clique no nome, introduza "Blob caminho" e prima a tecla **Enter** . 
 
5.  Para fornecer um valor predefinido para o parâmetro do serviço Web, clique no ícone à direita do nome, selecione "Fornecer valor predefinido", introduza um valor (por exemplo, "container1/output1.csv") e prima a tecla **Enter** .

    ![Parâmetro do serviço Web][parameter]

6.  Clique em **Executar**. 

7.  Clique em **Implementar o serviço Web** e selecione **Implementar o serviço Web [clássica]** ou **Implementar [novo] serviço Web** para implementar o serviço web.

O utilizador do serviço web pode agora especificar um novo destino para a [Exportar dados] [ writer] módulo ao aceder ao serviço web.

##<a name="more-information"></a>Obter mais informações

Por exemplo um mais detalhado, consulte a entrada de [Parâmetros de serviço Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) de [Máquina aprendizagem blogue](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Para mais informações sobre como aceder a um serviço de web máquina de formação, consulte o artigo [como consumir uma serviço web de aprendizagem automática publicada](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 
