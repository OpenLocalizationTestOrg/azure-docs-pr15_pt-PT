

Este artigo mostra-lhe como implementar um Azure Máquina Virtual escala definido utilizando uma implementação de grupo de recursos Studio Visual.


[Conjuntos de escala de Máquina Virtual Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) , são um recurso Azure calcular para implementar e gerir uma coleção de máquinas virtuais semelhantes com opções facilmente integradas para dimensionar automaticamente e balanceamento de carga. Pode aprovisionar e implementar conjuntos de escala VM utilizar [modelos de Gestor de recursos do Azure (processador)](https://github.com/Azure/azure-quickstart-templates). Modelos de processador podem ser implementados usando o resto do Azure clip, PowerShell e também diretamente a partir do Visual Studio. O Visual Studio fornece um conjunto de exemplo modelos que pode ser implementada como parte de um projeto de implementação de grupo de recursos do Azure.

Implementações do grupo de recursos do Azure são uma forma de agrupar e publicar um conjunto de recursos Azure relacionados numa operação de implementação única. Pode saber mais sobre os mesmos aqui: [criar e implementar o grupos de recursos Azure através do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy/).

## <a name="pre-requisites"></a>Pré-requisitos

Para começar a implementação de conjuntos de escala VM no Visual Studio precisa do seguinte:

- Visual Studio 2013 ou 2015
- Azure SDK 2.7 ou 2,8

Nota: Estas instruções partem do pressuposto de que está a utilizar o Visual Studio 2015 com [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Criar um projeto

1. Criar um novo projeto no Visual Studio 2015 ao selecionar **ficheiro | Novo | Project**.

    ![Ficheiro novo][file_new]

2. Em **Visual c# | Nuvem**, escolha **Gestor de recursos do Azure** para criar um projeto para implementar um modelo de processador.

    ![Criar projeto][create_project]

3.  A partir da lista de modelos, selecione o Linux ou modelo de conjunto do Windows Máquina Virtual escala.

    ![Selecione o modelo][select_Template]

4. Assim que o seu projeto é criado verá scripts de implementação do PowerShell, um modelo de Gestor de recursos do Azure e um ficheiro de parâmetro para o conjunto de escala de Máquina Virtual.

    ![Explorador de soluções][solution_explorer]

## <a name="customize-your-project"></a>Personalizar o seu projeto

Agora pode editar o modelo personalizá-lo para necessidades da sua aplicação, tal como adicionar propriedades da extensão VM ou editar regras de balanceamento de carga. Por predefinição, os modelos de conjunto de escala de VM estão configurados para implementar a extensão de AzureDiagnostics que torna mais fácil adicionar autoscale regras. Também são implementadas um balanceador de carga com um endereço IP público, configurado com as regras de entrada na NAT que permitem se liga às instâncias VM com SSH (Linux) ou RDP (Windows) – o intervalo de porta front-end começa em 50000, que significa no caso de Linux, se lhe SSH à porta 50000 do endereço IP público (ou nome de domínio) será encaminhado para porta 22 da primeira VM no conjunto de escala. Ligar à porta 50001 será encaminhado para porta 22 da segunda VM e assim sucessivamente.

 Uma boa forma de editar os seus modelos com o Visual Studio é utilizar o destaque JSON para organizar os parâmetros, variáveis e recursos. Com uma compreensão do esquema do Visual Studio pode apontar erros no seu modelo antes da implementação.

![JSON Explorer][json_explorer]

## <a name="deploy-the-project"></a>Implementar o projeto

6. Implemente o modelo de processador Azure para criar o recurso de conjunto de escala VM. Clique com o botão direito do rato sobre o nó do projeto, selecione **Implementar | Nova implementação**.

    ![Implementar o modelo][5deploy_Template]

7. Selecione a sua subscrição na caixa de diálogo "Implementar ao grupo de recursos".

    ![Implementar o modelo][6deploy_Template]

8. A partir daqui também pode criar um novo grupo de recursos do Azure para implementar o modelo para.

    ![Novo grupo de recursos][new_resource]

9. Em seguida selecione o botão **Editar parâmetros** para introduzir parâmetros que serão transmitidos ao seu modelo, determinados valores, como o nome de utilizador e palavra-passe para o SO são necessários para criar a implementação.

    ![Editar parâmetros][edit_parameters]

10. Agora, clique em **Implementar**. Na janela de **saída** irá mostrar o progresso de implementação. Tenha em atenção que à ação está a executar o script **AzureResourceGroup.ps1 implementar** .

    ![Janela de saída][output_window]

## <a name="exploring-your-vm-scale-set"></a>Explorar o seu conjunto de escala VM

Assim que a implementação for concluída, pode ver o novo conjunto de escala de VM no Visual Studio **Nuvem Explorer** (atualizar a lista). Nuvem Explorer permite-lhe gerir Azure recursos no Visual Studio durante o desenvolvimento de aplicações. Também pode ver o seu conjunto de escala de VM no Portal do Azure e Azure recurso Explorer.

![Nuvem Explorer][cloud_explorer]

 O portal fornece a melhor forma de visualmente gerir a sua infraestrutura Azure com um browser, enquanto Azure recurso Explorer fornece uma forma fácil de explorer e depurar Azure recursos, que lhe dá uma uma janela para a vista"instância" e também a apresentar comandos do PowerShell para os recursos que esteja a visualizar. Apesar de conjuntos de escala VM serem na pré-visualização, o Explorador do recurso irá mostrar a mais detalhada para os conjuntos de escala VM.

## <a name="next-steps"></a>Próximos passos

Assim que tiver implementado com êxito conjuntos de escala VM através do Visual Studio pode personalizar ainda mais o projeto de acordo com os requisitos da aplicação. Por exemplo a configurar autoscale a adicionar um recurso de informações, adicionando infraestrutura ao seu modelo como autónomo VMs ou implementar aplicações utilizando a extensão de scripts personalizados. Encontrará uma boa fonte de modelos de exemplo no repositório de GitHub [Modelos de guia de introdução do Azure](https://github.com/Azure/azure-quickstart-templates) (procurar "vmss").

[file_new]: ./media/virtual-machines-common-scale-sets-visual-studio/1-FileNew.png
[create_project]: ./media/virtual-machines-common-scale-sets-visual-studio/2-CreateProject.png
[select_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-common-scale-sets-visual-studio/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-common-scale-sets-visual-studio/8-EditParameter.png
[output_window]: ./media/virtual-machines-common-scale-sets-visual-studio/9-Output.png
[cloud_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/12-CloudExplorer.png