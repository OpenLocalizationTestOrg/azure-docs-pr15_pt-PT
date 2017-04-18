<properties
    pageTitle="Implementar modelos com o Visual Studio Azure empilhados | Microsoft Azure"
    description="Saiba como implementar modelos com o Visual Studio na pilha de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Implementar modelos de empilhados Azure utilizando o Visual Studio

Utilize o Visual Studio para implementar modelos de Gestor de recursos do Azure o conceito de pilha Azure.

Modelos de Gestor de recursos implementar e aprovisionar todos os recursos para a sua aplicação numa operação de única e coordenada.

1.  Abra o Visual Studio 2015 Update 1.

2.  Clique em **ficheiro**, clique em **Novo**e, na caixa de diálogo **Novo projeto** , clique em **Grupo de recursos do Azure**.

3.  Introduza um **nome** para o novo projeto e, em seguida, clique em **OK**.

4.  Na caixa de diálogo **Seleccionar Azure modelo** , clique em **Windows Virtual Machine**e, em seguida, clique em **OK**.

  No novo projeto, pode ver uma lista dos modelos disponíveis expandindo o nó de **modelos** no painel de **Explorador de soluções** .

5.  No painel de **Solução Explorer** , com o botão direito no nome do seu projeto, clique em **Implementar**, em seguida, clique em **Nova implementação**.

6.  Na caixa de diálogo **Implementar a grupo de recursos** , no menu pendente **subscrição** , selecione a sua subscrição do Microsoft Azure pilha.

7.  Na lista de **Grupo de recursos** , selecione um grupo de recursos existente ou crie um novo.

8.  Na lista **localização do grupo de recursos** , escolha uma localização e, em seguida, clique em **Implementar**.

9.  Na caixa de diálogo **Editar parâmetros** , introduza os valores para os parâmetros (que variam consoante o modelo) e, em seguida, clique em **Guardar**.

## <a name="next-steps"></a>Próximos passos

[Implementar modelos com a linha de comandos](azure-stack-deploy-template-command-line.md)
