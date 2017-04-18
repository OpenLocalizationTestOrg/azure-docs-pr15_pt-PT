<properties
    pageTitle="Resolução de problemas: Criar e ligar a uma área de trabalho de aprendizagem máquina | Microsoft Azure"
    description="Soluções para problemas comuns em criar e ligar a uma área de trabalho do Azure máquina aprendizagem"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="garye"/>


# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guia de resolução de problemas: criar e ligar a uma área de trabalho de aprendizagem automática

Este guia fornece soluções para algumas perguntas se tenha deparado ou desafios quando estiver a configurar áreas de trabalho do Azure máquina aprendizagem.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Proprietário da área de trabalho

Quando cria uma nova área de trabalho de formação de máquina, o ID de introduzir o campo proprietário da área de trabalho tem de ser uma conta Microsoft válida (antigo Windows Live ID), por exemplo, john-contoso@live.com ou john-contoso@hotmail.com. Não pode ser uma conta que não sejam da Microsoft, como a sua conta de e-mail empresarial. Para criar uma conta Microsoft gratuita, aceda a [www.live.com](http://www.live.com).

Tenha em atenção que a conta que utilizou para iniciar sessão no portal do Azure para criar a área de trabalho não automaticamente tem permissão para *Abrir* a área de trabalho, a menos que especifique essa conta, como o proprietário. Para abrir uma área de trabalho no máquina aprendizagem Studio, tem de ter sessão iniciada na Account Microsoft que tenha sido definida como o proprietário da área de trabalho ou é necessário receber um convite ao proprietário para aderir a área de trabalho. A partir do portal clássico Azure pode, no entanto, *Gerir* a área de trabalho, que inclui a capacidade de alterar o proprietário e configurar o acesso.

Para mais informações sobre como gerir uma área de trabalho, consulte [Gerir uma área de trabalho do Azure máquina aprendizagem].

[Gerir uma área de trabalho do Azure máquina aprendizagem]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>Regiões permitidos

Formação de máquina neste momento não existe um número limitado de regiões. Se a sua subscrição não inclui uma das seguintes regiões, poderá ver a mensagem de erro, "Tem nenhum nas regiões permitidas."

Para pedir que uma região ser adicionado à sua subscrição, selecione **Contactar o suporte do Microsoft** a partir do Portal de clássica Azure, selecione **faturação** como o tipo de problema e siga os pedidos para submeter o pedido.

![Contacte o suporte da Microsoft][screen1]

## <a name="storage-account"></a>Conta de armazenamento

O serviço de aprendizagem do computador necessita de uma conta de armazenamento para armazenar dados. Pode utilizar uma conta existente do armazenamento ou pode criar uma nova conta de armazenamento quando criar nova área de trabalho formação de máquina (se tiver de quota para criar uma nova conta de armazenamento).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![Criar a área de trabalho][screen2]

Quando a nova área de trabalho de aprendizagem máquina estiver criada, pode iniciar sessão para máquina aprendizagem Studio utilizando a conta Microsoft que especificou como o proprietário da área de trabalho. Se encontrar a mensagem de erro, "Área de trabalho não encontrado" (semelhante ao seguinte captura de ecrã), utilize os seguintes passos para eliminar os cookies do browser.

![Área de trabalho não encontrada][screen3]

**Para eliminar cookies no browser**

Se utilizar o Internet Explorer, clique no botão **Ferramentas** no canto superior direito e selecione **Opções da Internet**.  

![Opções da Internet][screen4]

No separador **Geral** , clique em **eliminar...**

![Separador Geral][screen5]

Na caixa de diálogo **Eliminar histórico de navegação** , certifique-se de **que cookies e dados de Web site** está selecionada e clique em **Eliminar**.

![Eliminar os cookies][screen6]

Depois de serem eliminados os cookies, reinicie o browser e, em seguida, vá para a página de [Formação do Microsoft Azure máquina](https://studio.azureml.net) . Quando lhe for pedido um nome de utilizador e palavra-passe, introduza a mesma conta Microsoft que especificou como o proprietário da área de trabalho.

Nosso objetivo é tornar a experiência de aprendizagem máquina tão simples quanto possível. Publique quaisquer comentários e problemas durante o [Fórum do Azure máquina formação](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para Ajude-nos servi-lo melhor.

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
