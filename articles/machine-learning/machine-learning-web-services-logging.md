<properties 
    pageTitle="Registo para os serviços web de aprendizagem máquina | Microsoft Azure" 
    description="Saiba como ativar o registo de serviços web de aprendizagem automática. Registo fornece informações adicionais para ajudar a resolver as APIs." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>

# <a name="enable-logging-for-machine-learning-web-services"></a>Ativar o registo de serviços web de aprendizagem automática  

Este documento fornece informações sobre a funcionalidade de registo de serviços Web clássica. Activar o registo nos serviços Web fornece informações adicionais, para além das apenas um número de erro e uma mensagem, que pode ajudar a resolver problemas das chamadas para APIs de aprendizagem do computador.  

Para ativar o registo no serviços Web no portal do Azure clássico:   

1.  Inicie sessão no [portal clássica Azure](https://manage.windowsazure.com/)
2.  Na coluna funcionalidades para a esquerda, clique em **Formação do computador**.
3.  Clique em área de trabalho, em seguida, **WEB SERVICES**.
4.  Na lista de serviços Web, clique no nome do serviço Web.
5.  Na lista os pontos finais, clique no nome do ponto final.
6.  Clique em **Configurar**.
7.  Definir o **Nível de rastreio de diagnóstico** para *erro* ou *todos os*e, em seguida, clique em **Guardar**.

Para ativar o início de sessão no portal do Azure máquina aprendizagem Web Services.

1. Inicie sessão no portal do [Azure máquina aprendizagem Web Services](https://services.azureml.net) .
2. Clique em serviços Web clássica.
3.  Na lista de serviços Web, clique no nome do serviço Web.
4.  Na lista os pontos finais, clique no nome do ponto final.
5.  Clique em **Configurar**.
6.  Defina o **registo** para o *erro* ou *todos os*e, em seguida, clique em **Guardar**.

## <a name="the-effects-of-enabling-logging"></a>Os efeitos da ativação de registo

Quando o registo é activado, todas as de diagnóstico e erros a partir do ponto final selecionado são registados para a conta de armazenamento do Azure associadas a área de trabalho do utilizador. Pode ver esta conta de armazenamento no portal do clássico Azure a vista de Dashboard (parte inferior da secção rapidamente rápida) da sua área de trabalho.  

Os registos podem ser visualizados utilizando qualquer uma das várias ferramentas disponíveis para uma conta de armazenamento do Azure 'explorar'. Poderão ser as mais fáceis de navegar simplesmente para a conta de armazenamento no portal do Azure clássico e, em seguida, clique em **contentores de**. Em seguida, verá um contentor denominado **ml diagnósticos**. Neste contentor guarda todas as informações de diagnóstico para todos os web serviço pontos finais para todas as áreas de trabalho associados esta conta de armazenamento. 
 
## <a name="log-blob-detail-information"></a>Informações de detalhe de Blobs do registo

Cada blob no contentor guarda as informações de diagnóstico para o exatamente um dos seguintes procedimentos:

-   Uma execução do método lote execução  
-   Uma execução do método de pedido de resposta  
-   Inicialização de um contentor de pedido de resposta
  
O nome de cada blob tem um prefixo da seguinte forma: 

{Id da área de trabalho}-{o serviço Web Id}-{Id de ponto final} / {tipo de registo}  

Onde o tipo de registo é uma das seguintes valores:  

- batch  
- / pedidos de pontuação  
- pontuação/inicialização  