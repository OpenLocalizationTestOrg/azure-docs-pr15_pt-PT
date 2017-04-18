<properties
    pageTitle="Resolução de problemas Retraining de um serviço Web de clássico de aprendizagem do Azure máquina | Microsoft Azure"
    description="Identificar e corrigir encounted de problemas comuns quando são Reciclagem o modelo para um serviço Web do Azure máquina aprendizagem."
    services="machine-learning"
    documentationCenter=""
    authors="VDonGlover"
   manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>A Reciclagem de um serviço Web de clássico de aprendizagem do Azure máquina de resolução de problemas

## <a name="retraining-overview"></a>Descrição geral de reciclagem

Quando implementar uma experiência de aspeto do Office como um serviço web pontuação é um modelo de estático. Novos dados fica disponíveis ou quando o consumidor da API do tem os seus próprios dados, o modelo tem de ser retrained. 

Para concluir obter instruções sobre o processo readaptação de um serviço Web clássico, consulte o artigo [Reciclagem máquina aprendizagem modelos através de programação](machine-learning-retrain-models-programmatically.md).

## <a name="retraining-process"></a>Reciclagem do processo

Quando precisar de reciclagem o serviço Web, tem de adicionar algumas partes adicionais:

* Um serviço Web implementado a partir da experiência de formação. A experiência tem de ter um módulo de **Saída de serviço Web** anexado a saída do módulo **Modelo comboio** .  

    ![Anexe a saída de serviço web ao modelo de comboio.][image1]

* Um novo ponto final de adicionado ao seu serviço Web pontuação.  Pode adicionar o ponto final através de programação utilizando o código de exemplo referenciado nos modelos de formação de máquina de retransmissão através de programação tópico ou através do portal do Azure clássico.

Em seguida, pode utilizar o c# código de exemplo a partir da página de ajuda de API o serviço de Web de formação para reciclagem modelo. Assim que tiver avaliada os resultados e estiver satisfeito com os mesmos, pode atualizar o modelo de formação pontuação serviço web utilizando o novo ponto final que adicionou.

Com todos os blocos num local, os passos principais que deve tomar para o modelo de retransmissão são:

1.  Chamar o serviço Web de formação: A chamada é para o lote de execução do serviço (BES), não a pedir resposta serviço (RRS). Pode utilizar o c# código de exemplo na página de ajuda API para efetuar uma chamada. 
2.  Localizar os valores para o *BaseLocation*, *RelativeLocation*e *SasBlobToken*: estes valores são devolvidos no resultado da sua chamada para o serviço Web de formação. 
      ![a apresentar o resultado da amostra readaptação e os valores BaseLocation, RelativeLocation e SasBlobToken.][image6]
3.  Atualizar o adicionado ponto final de serviço web pontuação com o novo modelo de formação: utilizar o código de exemplo fornecido nos modelos de formação de máquina de retransmissão através de programação, atualizar o novo ponto final que adicionou ao modelo de pontuação com o modelo recentemente formação de serviço Web formação.

## <a name="common-obstacles"></a>Obstáculos comuns

### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Verifique se tem o URL correto correcção

O URL de correcção que estiver a utilizar o tem de ser um associada pontuação novo ponto final que adicionou ao serviço Web pontuação. Existem várias maneiras para obter o URL de correcção:

**Opção 1: forma programática**

Para obter o URL correto correcção:

1.  Execute o código de exemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2.  O resultado de AddEndpoint, encontrar o valor de *HelpLocation* e copie o URL.

    ![HelpLocation no resultado do exemplo addEndpoint.][image2]

3.  Cole o URL para um browser para navegar para uma página que fornece ligações de ajuda para o serviço Web.
4.  Clique na ligação de **Recursos de atualização** para abrir a página de ajuda de patches.

**Opção 2: Utilizar o portal do clássico Azure**

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2.  Abra o separador de máquina aprendizagem. 
     ![Separador de condiçġes máquina.][image4]
3.  Clique em seu nome de área de trabalho, em seguida, **Web Services**.
4.  Clique no serviço Web pontuação que estiver a trabalhar com. (Se não modificar o nome predefinido do serviço web, irá terminem em [pontuação Exp]..)
5.  Clique em **Adicionar ponto final**.
6.  Depois de adicionar o ponto final, clique no nome do ponto final. Em seguida, clique em **Recursos de atualização** para abrir a página de ajuda patch.

>[AZURE.NOTE] Se tiver adicionado o ponto final para o serviço Web de formação em vez do serviço Web de aspeto do Office, vai receber a seguinte mensagem de erro quando clicar na ligação de **Recursos de atualização** : Pedimos desculpa, mas esta funcionalidade não está disponível neste contexto ou suportados. Este serviço Web tem não existem recursos atualizável. Pedimos desculpa e estiver a trabalhar num melhorar este fluxo de trabalho.

![Novo dashboard de ponto final.][image3]

A página de ajuda de PATCHES contém o URL de correcção tem de utilizar e fornece exemplos de código que pode utilizar para ligá-lo.

![URL de patches.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Verifique que está a atualizar o ponto final de pontuação correto

* Não correcção o serviço Web de formação: tem de ser efetuada a operação de patches no serviço Web pontuação.
* Não correcção o ponto final predefinido no serviço Web: deve ser efetuada a operação de patches no novo pontuação Web ponto final do serviço que adicionou.

Pode verificar qual o ponto final está ativada por visitar o portal clássico Azure o serviço Web. 

>[AZURE.NOTE] Certifique-se de que está a adicionar o ponto final para o serviço Web aspeto do Office, não o serviço Web de formação. Se tiver corretamente implementado uma formação e um serviço Web aspeto do Office, deverá ver duas serviços Web em separado listados. O serviço Web de aspeto do Office deve terminar com "[exp previsão.]".

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2.  Abra o separador de máquina aprendizagem. 
     ![Área de trabalho de formação de máquina IU.][image4]
3.  Selecione a área de trabalho.
4.  Clique em **Serviços Web**.
5.  Selecione o seu serviço Web aspeto do Office.
6.  Certifique-se de que o seu novo ponto final de foi adicionado ao serviço Web.

### <a name="check-the-workspace-that-your-web-service-is-in-to-ensure-it-is-in-the-correct-region"></a>Verificar a área de trabalho que o serviço web garantir que estão na região correta

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com).
2.  Selecione máquina formação a partir do menu.
      ![Região de aprendizagem máquina IU.][image4]
3.  Verifique se a localização da área de trabalho.

<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/machine-learning-troubleshooting-retraining-a-model/web-services-tab.png