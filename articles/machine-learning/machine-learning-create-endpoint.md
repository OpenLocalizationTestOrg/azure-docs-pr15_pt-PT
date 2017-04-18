<properties
    pageTitle="Criar pontos finais de serviço Web em aprender máquina | Microsoft Azure"
    description="Criar pontos finais de serviço Web no Azure máquina aprendizagem"
    services="machine-learning"
    documentationCenter=""
    authors="hiteshmadan"
    manager="padou"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="10/04/2016"
    ms.author="himad"/>


# <a name="creating-endpoints"></a>Criar os pontos finais

>[AZURE.NOTE] Este tópico descreve técnicas aplicáveis a um serviço Web clássico.

Ao criar serviços Web que vende reencaminhar aos seus clientes, tem de fornecer formação modelos para cada cliente que ainda estão ligadas a experiência a partir do qual foi criado o serviço Web. Além disso, quaisquer atualizações a experiência devem ser aplicadas seletivamente para um ponto final sem substituir as personalizações.

Para realizar esta tarefa, o Azure máquina aprendizagem permite-lhe criar vários pontos finais para um serviço Web implementado. Cada ponto final no serviço Web independentemente é resolvida, limitada e gerido. Cada ponto final é um URL exclusivo e uma chave de autorização que pode distribuir aos seus clientes.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Adicionar pontos finais para um serviço Web

Existem três formas de adicionar um ponto final a um serviço Web.

* Através de programação
* Através do portal de serviços Web do Azure máquina aprendizagem
* Apesar de portal clássico do Azure

Quando o ponto final estiver criado, pode consumi-lo através de API síncrono, lote APIs e folhas de cálculo do excel. Para além de adicionar os pontos finais através deste UI, também pode utilizar as APIs da gestão de ponto final para adicionar através de programação pontos finais.

 >[AZURE.NOTE] Se tiver adicionado os pontos finais adicionais para o serviço Web, não pode eliminar o ponto final predefinido.

## <a name="adding-an-endpoint-programmatically"></a>Adicionar um ponto final através de programação

Pode adicionar um ponto final no seu serviço Web programaticamente utilizando o código de exemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Adicionar um ponto final utilizando o portal de serviços Web do Azure máquina aprendizagem

1. Na máquina aprendizagem Studio, na coluna da navegação do lado esquerdo, clique em serviços Web.
2. Na parte inferior do dashboard de serviço Web, clique em **pontos finais de gerir**. O portal de serviços Web do Azure máquina aprendizagem abre-se para a página pontos finais para o serviço Web.
3. Clique em **Novo**.
4. Escreva um nome e descrição para o novo ponto final. Nomes de ponto final tem de ser 24 carácter ou menos de comprimento e tem de ser constituídos por alfabetos minúsculas ou números. Selecione o nível de registo e se os dados de exemplo estão ativados. Para mais informações sobre a funcionalidade de registo, consulte o artigo [Ativar o registo de serviços Web de aprendizagem do computador](machine-learning-web-services-logging.md).

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Adicionar um ponto final através do portal clássico Azure


1. Inicie sessão no [portal clássica Azure](http://manage.windowsazure.com), clique em **Máquina formação** na coluna da esquerda. Clique em área de trabalho que contém o serviço Web na qual que lhe interessam.

    ![Navegue até à área de trabalho](./media/machine-learning-create-endpoint/figure-1.png)

2. Clique em **Serviços Web**.

    ![Navegue para serviços Web](./media/machine-learning-create-endpoint/figure-2.png)

3. Clique no serviço Web que lhe interessa ver a lista dos pontos finais disponíveis.

    ![Navegue até ao ponto final](./media/machine-learning-create-endpoint/figure-3.png)

4. Na parte inferior da página, clique em **Adicionar ponto final**. Escreva um nome e descrição, certifique-se de que não existem outros pontos finais com o mesmo nome neste serviço Web. Deixe o nível de limitação com o valor predefinido, a menos que tenha requisitos especiais. Para saber mais sobre limitação, consulte o artigo [Pontos finais API do dimensionamento](machine-learning-scaling-webservice.md).

    ![Criar o ponto final](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>Próximos passos

[Como consumir um serviço Web de aprendizagem do Azure máquina publicado](machine-learning-consume-web-services.md).
