<properties 
   pageTitle="Adicionar armazenamento do Windows Azure utilizando serviços ligados no Visual Studio | Microsoft Azure"
   description="Adicionar armazenamento do Windows Azure a sua aplicação utilizando a caixa de diálogo do Visual Studio adicionar serviços ligados"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Adicionar armazenamento Azure utilizando o Visual Studio ligado Services

## <a name="overview"></a>Descrição geral

Com o Visual Studio 2015, pode ligar qualquer c#: serviço na nuvem, serviço móvel do .NET back-end, Web site do ASP.NET ou serviço, serviço de 5 de ASP.NET ou serviço Azure WebJob ao armazenamento do Azure utilizando a caixa de diálogo **Adicionar serviços ligados** . A funcionalidade do serviço ligada adiciona todas as referências conforme seja necessárias e código de ligação e modifica corretamente os seus ficheiros de configuração. A caixa de diálogo também leva-o a documentação mostra-lhe o que são os passos iniciar o armazenamento de BLOBs, filas e tabelas.

## <a name="supported-project-types"></a>Tipos de projecto suportados

Pode utilizar a caixa de diálogo Serviços ligados para ligar ao armazenamento do Azure nos seguintes tipos de projeto.

- Projetos de Web do ASP.NET

- ASP.NET 5 projetos

- Função de Web de serviço de nuvem .NET e projetos de função de trabalho

- Projetos de serviços móveis .NET

- Projetos Azure WebJob


## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Ligar ao armazenamento de Azure utilizando a caixa de diálogo Serviços ligados

1. Certifique-se de que tem uma conta Azure. Se não tiver uma conta Azure, pode inscrever-se para uma [versão de avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146). Assim que tiver uma conta Azure, pode criar contas de armazenamento, criar serviços móveis e configurar o Azure Active Directory.

1. Abra o projeto no Visual Studio, abra o menu de contexto para o nó de **referências** no Explorador de solução e, em seguida, selecione **Adicionar serviço ligado**.

    ![Adicionar um serviço ligado](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Na caixa de diálogo **Adicionar serviço ligado** , selecione o **Armazenamento do Windows Azure**e, em seguida, selecione o botão **Configurar** . Poderá ser-lhe pedido que inicie sessão no Azure se ainda não o tiver feito.

    ![Adicionar a caixa de diálogo Serviço ligado - armazenamento](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. Na caixa de diálogo **Armazenamento do Windows Azure** , selecione uma conta de armazenamento existente e selecione **Adicionar**.

    Se precisar de criar uma nova conta de armazenamento, avance para o passo seguinte. Caso contrário, avance para o passo 6.

    ![Caixa de diálogo armazenamento Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. Para criar uma nova conta de armazenamento: 

    1. Selecione o botão **criar uma nova conta de armazenamento** na parte inferior da caixa de diálogo de armazenamento do Windows Azure.

    1. Preencha a caixa de diálogo **Criar conta de armazenamento** e, em seguida, selecione o botão **Criar** .
    
        ![Caixa de diálogo armazenamento Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Quando estiver novamente na caixa de diálogo **Armazenamento do Windows Azure** , novo armazenamento aparece na lista.

    1. Selecione o armazenamento de novo na lista e selecione **Adicionar**.

1. O serviço de armazenamento ligado surge sob o nó de referências de serviço do seu projeto WebJob.

    ![Azure armazenamento no project de tarefas da web](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Reveja a página de introdução que aparece e descubra como o seu projeto foi modificado. Sempre que adiciona um serviço ligado, é apresentada uma página de introdução no seu browser. Pode rever os passos seguintes sugestões e exemplos de código ou mudar para a página o que aconteceu para ver que referências foram adicionadas ao seu projeto e como os seus ficheiros de código e configuração foram modificados.

## <a name="how-your-project-is-modified"></a>Como o seu projeto é modificado

Quando terminar a caixa de diálogo, o Visual Studio adiciona referências e modifica determinados ficheiros de configuração. As alterações específicas variam consoante o tipo de projecto. 

 - Para ASP.NET projetos, consulte o artigo [o que aconteceu – ASP.NET projetos](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
 - Para ASP.NET 5 projetos, consulte o artigo [o que aconteceu – ASP.NET 5 projetos](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
 - Para a nuvem projetos de serviço (funções da web e funções de trabalho), consulte o artigo [o que aconteceu – projetos de serviço na nuvem](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
 - Para WebJob projetos, consulte o artigo [o que aconteceu - WebJob projetos](./storage/vs-storage-webjobs-what-happened.md).

## <a name="next-steps"></a>Próximos passos

1. Utilizar os exemplos de código introdução como guia, criar o tipo de armazenamento que pretende e, em seguida, comece a escrever código para aceder à sua conta de armazenamento!

1. Colocar questões e obter ajuda
     - [Fórum MSDN: Armazenamento Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Armazenamento azure.microsoft.com](https://azure.microsoft.com/services/storage/)

     - [Documentação de armazenamento em azure.microsoft.com](https://azure.microsoft.com/documentation/services/storage/)

