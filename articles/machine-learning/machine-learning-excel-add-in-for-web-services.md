<properties
    pageTitle="Excel add-in para serviços Web de aprendizagem máquina | Microsoft Azure"
    description="Como utilizar os serviços Web de aprendizagem do Azure máquina diretamente no Excel sem escrever qualquer código."
    services="machine-learning"
    documentationCenter=""
    authors="tedway"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/05/2016"
    ms.author="tedway;garye" />

# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Suplemento do Excel para serviços Web de aprendizagem do Azure máquina

Excel torna mais fácil ligar serviços Web diretamente sem precisar de escrever qualquer código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Passos para utilizar um serviço Web existentes no livro

1. Abra o [ficheiro do Excel de exemplo](http://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados relativos passageiros a Titanic.
2. Selecione o serviço Web ao clicar na mesma-"Titanic sobrevivência Predictor (Excel Add-in amostra) [pontuação]" neste exemplo.

    ![Selecione o serviço Web][01]

3. Isto vai levá-lo para a secção **Predict** .  Este livro já contenha dados de exemplo, mas para um livro em branco que pode selecionar uma célula no Excel e clique em **dados de exemplo de utilização**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada.  Certifique-se que a caixa "os meus dados têm cabeçalhos" está selecionada.
5. Em **saída**, introduza o número de células onde pretende que a saída para ser, por exemplo "H1" aqui.
6. Clique em **prever**.

    ![Secção de previsão][02]

## <a name="steps-to-add-a-new-web-service"></a>Passos para adicionar um serviço Web novo

Implementar um serviço Web ou utilizar um serviço Web existente. Para obter mais informações sobre como implementar um serviço Web, consulte o artigo [tutorial passo 5: implementar o serviço Web de aprendizagem do Azure máquina](machine-learning-walkthrough-5-publish-web-service.md).

Obter a chave de API do seu serviço Web. Onde fazer isto depende se publicado um serviço Web de aprendizagem máquina clássico de um serviço Web de aprendizagem do novo computador.

**Utilizar um serviço Web clássico** 

1. No computador aprendizagem Studio, clique na secção de **Serviços WEB** no painel esquerdo e, em seguida, selecione o serviço Web.

    ![Selecione Studio um serviço Web][04]

2. Copie a chave de API para o serviço Web.

    ![Chave Studio API][05]

3. No separador **DASHBOARD** para o serviço Web, clique na ligação do **Pedido/resposta** .
4. Procure a secção **Pedir URI** .  Copiar e guarde o URL.

>[AZURE.NOTE] Agora é possível iniciar sessão no portal do [Azure máquina aprendizagem Web Services](https://services.azureml.net) para obter a chave de API para um serviço Web de aprendizagem máquina clássica.

**Utilizar um serviço Web novo**

1. No portal do [Azure máquina aprendizagem Web Services](https://services.azureml.net) , clique em **Serviços Web**, em seguida, selecione o seu serviço Web. 
2. Clique em **consumir**.
3. Procure a secção de **informações básicas de consumo** . Copiar e guarde a **Chave primária** e o URL do **Pedido de resposta** .


## <a name="steps-to-add-a-new-web-service"></a>Passos para adicionar um serviço Web novo

1. Implementar um serviço Web ou utilizar um serviço Web existente. Para obter mais informações sobre como implementar um serviço Web, consulte o artigo [tutorial passo 5: implementar o serviço Web de aprendizagem do Azure máquina](machine-learning-walkthrough-5-publish-web-service.md).
2. Clique em **consumir**.
3. Procure a secção de **informações básicas de consumo** . Copiar e guarde a **Chave primária** e o URL do **Pedido de resposta** .
2. No Excel, aceda à secção **Serviços Web** (se estiver na secção **Predict** , clique na seta para trás para aceder à lista de serviços Web).

    ![Aceda a seleção de serviço Web][03]
    
3. Clique em **Adicionar serviço Web**.
4. Cole o URL na caixa de texto do suplemento do Excel denominada **URL**.
5. Cole a chave primária/API para a caixa de texto com nome na **chave API**.
6. Clique em **Adicionar**.

    ![Tecla de URL e API para um serviço Web clássica.][06]

10. Para utilizar o serviço Web, siga as instruções anteriores, "Os passos para utilizar um serviço Web de existente".

## <a name="sharing-your-workbook"></a>Partilhar o seu livro

Se guardar o livro, a tecla API/principal para os serviços Web que adicionou também é guardada. Isto significa que só deverá partilhar o livro com pessoas que considere fidedignas.

Perguntas qualquer na secção comentário seguinte ou no nosso [Fórum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png
