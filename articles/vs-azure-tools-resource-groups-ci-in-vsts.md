<properties
    pageTitle="Integração contínua nos serviços de equipa VS utilizando projetos de grupo de recursos do Azure | Microsoft Azure"
    description="Descreve como configurar integração contínua no Visual Studio Team Services utilizando projectos de implementação do grupo de recursos do Azure no Visual Studio."
    services="visual-studio-online"
    documentationCenter="na"
    authors="mlearned"
    manager="erickson-doug"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/01/2016"
    ms.author="mlearned" />

# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Integração contínua no Visual Studio Team Services utilizando projectos de implementação do grupo de recursos do Azure

Para implementar um modelo do Azure, tem de executar tarefas até as várias fases: construir, teste, copiar para Azure (também denominada "Transição") e implementar o modelo.  Existem duas formas diferentes para fazê-lo no Visual Studio Team Services (VS serviços de equipa). Ambos os métodos fornecer os mesmos resultados, por isso, escolha aquele que melhor se adequa o fluxo de trabalho.

-   Adicione um único passo a sua definição de compilação que executa o script do PowerShell que vem incluído no projeto de implementação do grupo de recursos do Azure (implementar AzureResourceGroup.ps1). O script copia artefactos e, em seguida, implementa o modelo.
-   Adicione que vários serviços de equipa VS construir passos, cada um deles efetuar uma tarefa de fase.

Este artigo demonstra como utilizar a primeira opção (utilize uma definição de compilação para executar o script PowerShell). Uma desta opção vantagens é que o script utilizado pelos programadores no Visual Studio é o mesmo script que é utilizado pelos serviços de equipa VS. Este procedimento assume que já tem um projeto de implementação do Visual Studio selecionado para serviços de equipa VS.

## <a name="copy-artifacts-to-azure"></a>Copiar artefactos para Azure 

Independentemente do cenário, se tiver qualquer artefactos que são necessários para a implementação do modelo, terá de conceder acesso de Gestor de recursos do Azure às mesmas. Estes artefactos podem incluir ficheiros tal como:

-   Modelos aninhados
-   Scripts de configuração e DSC
-   Binários da aplicação

### <a name="nested-templates-and-configuration-scripts"></a>Modelos aninhados e configuração do Scripts
Quando utilizar os modelos fornecidos pelo Visual Studio (ou criadas com fragmentos Visual Studio), o script do PowerShell fases não só erros, -lo também parameterizes URI para os recursos para implementações diferentes. O script, em seguida, copia erros a um contentor seguro no Azure, cria um token de SA para desse contentor e, em seguida, transmite essas informações para a implementação do modelo. Consulte o artigo [criar uma implementação do modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx) para saber mais acerca dos modelos de aninhadas.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Configurar a implementação contínua nos serviços de equipa VS

Para ligar o script do PowerShell no VS Team Services, tem de atualizar a sua definição de compilação. Em resumo, os passos são: 

1.  Edite a definição de compilação.
1.  Configure o Azure autorização nos serviços de equipa VS.
1.  Adicione um passo de compilação do Azure PowerShell que referencia o script do PowerShell no projeto de implementação do Azure grupo de recursos.
1.  Defina o valor do parâmetro *- ArtifactsStagingDirectory* para trabalhar com um projeto compilado nos serviços de equipa VS.

### <a name="detailed-walkthrough"></a>Obter instruções detalhadas sobre

Os passos seguintes irão guiá-lo os passos necessários para configurar a implementação contínua nos serviços de equipa do VS 

1.  Editar a sua definição de compilação VS serviços de equipa e adicione um passo de compilação Azure PowerShell. Selecione a definição de compilação na categoria **definições de criar** e, em seguida, selecione **Editar** ligação.

    ![][0]

1.  Adicionar um novo passo de compilação do **Azure PowerShell** para a definição de compilação e, em seguida, selecione **Adicionar construir passo...** botão.

    ![][1]

1.  Selecione a categoria de **tarefa de implementação** , selecione a tarefa de **PowerShell do Azure** e, em seguida, selecione o botão **Adicionar** .

    ![][2]

1.  Selecione o passo de compilação do **PowerShell do Azure** e, em seguida, preencha os valores.

    1.  Se já tiver um ponto final de serviço Azure adicionado à VS Team Services, selecione a subscrição na caixa de listagem pendente **Azure subscrição** e, em seguida, avance para a secção seguinte. 

        Se não tiver um ponto final de serviço Azure nos serviços de equipa VS, terá de adicionar um. Presente subsecção guiá-lo durante o processo. Se a sua conta Azure utiliza uma conta Microsoft (tal como o Hotmail), terá de siga os passos seguintes para obter uma autenticação do Principal do serviço.

    1.  Selecione a caixa de lista pendente a ligação **Gerir** junto a **Subscrição do Azure** .

        ![][3]

    1. Escolha **Azure** na caixa de lista pendente **Novo ponto final de serviço** .

        ![][4]

    1.  Na caixa de diálogo **Adicionar Azure subscrição** , selecione a opção **Principal de serviço** .

        ![][5]

    1.  Adicione as informações de subscrição Azure à caixa de diálogo **Adicionar Azure subscrição** . Terá de fornecer os seguintes itens:
        -   Id da subscrição
        -   Nome da subscrição
        -   Id de serviço de capital
        -   Tecla do Principal do serviço
        -   Id de inquilino

    1.  Adicione um nome da sua preferência para a caixa de nome da **subscrição** . Este valor irão aparecer posteriormente no **Azure subscrição** lista pendente, nos serviços de equipa VS. 

    1.  Se não souber que o seu ID da subscrição Azure, pode utilizar um dos seguintes comandos para obtê-lo.
        
        Para scripts de PowerShell, utilize:

        `Get-AzureRmSubscription`

        Para Azure clip, utilize:

        `azure account show`
    

    1.  Para obter um ID do Principal do serviço, chave Principal do serviço e ID de inquilino, siga o procedimento na [aplicação de criar o Active Directory e principal de serviço através do portal](resource-group-create-service-principal-portal.md) ou [um principal com o Gestor de recursos do Azure de serviço de autenticação](resource-group-authenticate-service-principal.md).

    1.  Adicionar os valores de ID do Principal do serviço, chave Principal do serviço e ID de inquilino para a caixa de diálogo **Adicionar Azure subscrição** e, em seguida, selecione o botão **OK** .

        Tem agora um Principal de serviço válido a utilizar para executar o script do Azure PowerShell.

1.  Editar a definição de compilação e selecione o passo de compilação **Azure PowerShell** . Selecione a subscrição na caixa de listagem pendente **Azure subscrição** . (Se a subscrição não aparecer, selecione o botão de **atualização** do seguinte a ligação **Gerir** .) 

    ![][8]

1.  Forneça um caminho para o script do PowerShell AzureResourceGroup.ps1 implementar. Para fazer isto, selecione o botão de reticências (…) junto à caixa **Caminho do Script** , navegue para o script PowerShell implementar AzureResourceGroup.ps1 na pasta **Scripts** do seu projeto, selecioná-la e, em seguida, selecione o botão **OK** . 

    ![][9]

1. Depois de selecionar o script, atualize o caminho para o script para que é executado a partir do Build.StagingDirectory (o mesmo directório que *ArtifactsLocation* está definido para). Pode fazê-lo adicionando "$(Build.StagingDirectory)/"para o início do caminho do script.

    ![][10]

1.  Na caixa **Argumentos de Script** , introduza os seguintes parâmetros (numa única linha). Quando executa o script no Visual Studio, pode ver como VS utiliza os parâmetros na janela de **saída** . Pode utilizar este como ponto de partida para definir os valores de parâmetro no passo de compilação.

  	| Parâmetro | Descrição|
  	|---|---|
  	| -ResourceGroupLocation           | O valor de geo localização onde está localizado, tal como **eastus** ou **Dos leste e.u.a.**o grupo de recursos. (Adicionar plicas se existir um espaço no nome). Para mais informações, consulte [Azure regiões](https://azure.microsoft.com/en-us/regions/) .|                                                                                                                                                                                                                              |
  	| -ResourceGroupName               | O nome do grupo de recursos utilizado para esta implementação.|                                                                                                                                                                                                                                                                                                                                                                                                                |
  	| -UploadArtifacts                 | Este parâmetro, quando presentes, especifica que artefactos precisa de ser carregado para Azure a partir do sistema local. Só tem de configurar este parâmetro se sua implementação do modelo requer artefactos extra que pretende fase utilizando o script PowerShell (como configuração scripts ou aninhados modelos).                                                                                                                                                                 |
  	| -StorageAccountName              | O nome da conta de armazenamento utilizado para artefactos fase para esta implementação. Este parâmetro é necessário apenas se estiver a copiar artefactos para Azure. Esta conta de armazenamento não será automaticamente criada pela implementação, tem de existir.|                                                                                                                                                                                                                     |
  	| -StorageAccountResourceGroupName | O nome do grupo de recursos associado à conta de armazenamento. Este parâmetro é necessário apenas se estiver a copiar artefactos para Azure.|                                                                                                                                                                                                                                                                                                                               |
  	| -ModeloEtiquetas                    | O caminho para o ficheiro de modelo do projeto de implementação do Azure grupo de recursos. Para melhorar a flexibilidade, utilize um caminho para este parâmetro que é relativo a localização do script do PowerShell em vez de um caminho absoluto.|
  	| -TemplateParametersFile          | O caminho para o ficheiro de parâmetros do projeto de implementação do Azure grupo de recursos. Para melhorar a flexibilidade, utilize um caminho para este parâmetro que é relativo a localização do script do PowerShell em vez de um caminho absoluto.|
  	| -ArtifactStagingDirectory        | Este parâmetro permite o PowerShell script sabe a pasta de onde devem ser copiados ficheiros binários do projeto. Este valor substitui o valor predefinido utilizado pelo script do PowerShell. Para utilizar serviços de equipa VS, defina o valor para: - ArtifactStagingDirectory $(Build.StagingDirectory)                                                                                                                                                                                              |

    Eis um exemplo de argumentos de script (linha quebrada para legibilidade):

    ``` 
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)' 
    ```

    Quando tiver terminado, a caixa **Argumentos de Script** deverá assemelhar-se o seguinte.

    ![][11]

1.  Depois de adicionar que todos os itens necessários para o PowerShell Azure construir passo, selecione a **fila** construir botão construir o projeto. O ecrã **construir** apresenta o resultado do script do PowerShell.

## <a name="next-steps"></a>Próximos passos

[Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Gestor de recursos do Azure e grupos de recursos Azure de leitura.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
