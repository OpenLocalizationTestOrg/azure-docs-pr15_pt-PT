<properties
   pageTitle="Utilizar o código de VS com modelos de Gestor de recursos | Microsoft Azure"
   description="Mostra como configurar o código do Visual Studio para criar modelos de Gestor de recursos do Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>

# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Trabalhar com modelos de Gestor de recursos do Azure no código do Visual Studio

Modelos de Gestor de recursos Azure são ficheiros JSON que descrevem um recurso e dependências relacionadas. Estes ficheiros podem ser por vezes grandes e complicado pelo ferramenta suporte é importante. Código do Visual Studio é um editor de código de novo, leve, abrir origem, em diferentes plataformas. Suporta a criação e edição de modelos de Gestor de recursos através de uma [nova extensão](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Código de VS é executado em todo o lado e não necessita de acesso à Internet, a menos que também pretende implementar os modelos de Gestor de recursos.

Se ainda não tiver VS código, pode instalá-lo na [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Instale a extensão do Gestor de recursos

Para trabalhar com os modelos JSON no código VS, tem de instalar uma extensão. Os passos seguintes transfira e instale o suporte de idiomas para os modelos de Gestor de recursos JSON:

1. Iniciação VS código 
2. Abrir abrir rápida (Ctrl + P) 
3. Execute o seguinte comando: 

        ext install azurerm-vscode-tools

4. Reinicie o código de VS quando lhe for pedido para ativar a extensão. 

 Trabalho concluído!

## <a name="set-up-resource-manager-snippets"></a>Configurar o Gestor de recursos fragmentos

Os passos anteriores instalado o suporte de ferramentas, mas agora precisamos de configurar o código de VS para utilizar fragmentos de modelo JSON.

1. Copie o conteúdo do ficheiro a partir do repositório [azure-xplat processador ferramentas](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) para sua área de transferência.
2. Iniciação VS código 
3. No código VS, pode abrir o ficheiro de fragmentos JSON navegando quer para **ficheiro** -> **Preferências** -> **Utilizador fragmentos** -> **JSON**, ou ao selecionar **F1** e introduzir **Preferências** até que pode selecionar **Preferências: fragmentos**.

    ![fragmentos preferência](./media/resource-manager-vs-code/preferences-snippets.png)

    A partir das opções, selecione **JSON**.

    ![Selecione json](./media/resource-manager-vs-code/select-json.png)

4. Colar o conteúdo do ficheiro no passo 1 para o seu ficheiro de fragmentos utilizador antes do final "}" 
5. Certifique-se a JSON parece OK e não existem sem linha ondulante em qualquer lugar. 
6. Guarde e feche o ficheiro de fragmentos do utilizador.

É tudo o que tem necessários para começar a utilizar os Gestor de recursos de fragmentos. Em seguida, podemos irá colocar esta configuração para o teste.

## <a name="work-with-template-in-vs-code"></a>Trabalhar com modelos no código VS

A forma mais fácil para começar a trabalhar com um modelo é Pegue dos rápida iniciar modelos disponíveis no [Github](https://github.com/Azure/azure-quickstart-templates) ou utilize um dos seus próprios. Pode facilmente [Exportar um modelo](resource-manager-export-template.md) para qualquer um dos seus grupos de recursos através do portal. 

1. Se exportar um modelo a partir de um grupo de recursos, abra os ficheiros extraídos no código VS.

    ![Mostrar os ficheiros](./media/resource-manager-vs-code/show-files.png)

2. Abra o ficheiro de template.json para que possa editá-lo e adicionar alguns recursos adicionais. Depois do **"recursos": [** prima a tecla enter para iniciar uma nova linha. Se escrever **processador**, ser-lhe-á apresentado com uma lista de opções. Estas opções estão fragmentos o modelo que tiver instalado. Deverá ter com aspeto da seguinte forma: 

    ![Mostrar fragmentos](./media/resource-manager-vs-code/type-snippets.png)

3. Selecione o fragmento de que deseja. Este artigo, posso estou a escolher **ip processador** para criar um novo endereço IP público. Colocar um ponto e vírgula após o parêntese de fecho "}" do recurso recentemente criado para se certificar de que o modelo de sintaxe é válida.

     ![Adicionar uma vírgula](./media/resource-manager-vs-code/add-comma.png)

4. Código de VS tem IntelliSense incorporado. À medida que edita os modelos, código VS sugere valores disponíveis. Por exemplo, para adicionar uma secção de variáveis ao seu modelo, adicione **""** (duas aspas) e selecione **Ctrl + barra de espaço** entre esses propostas. Será apresentada com opções incluindo **variáveis**.

    ![adicionar variáveis](./media/resource-manager-vs-code/add-variables.png)

5. IntelliSense também pode sugerir valores disponíveis ou funções. Para definir uma propriedade para um valor de parâmetro, crie uma expressão com **"[]"** e **Ctrl + barra de espaço**. Pode começar a escrever o nome de uma função. Selecione o **separador** quando tiver encontrado na função que pretende.

    ![adicionar o parâmetro](./media/resource-manager-vs-code/select-parameters.png)

6. Selecione novamente **Ctrl + barra de espaço** dentro da função para ver uma lista dos parâmetros disponíveis no seu modelo.

    ![adicionar o parâmetro](./media/resource-manager-vs-code/select-avail-parameters.png)

7. Se tiver problemas de validação de esquema no seu modelo, verá a linha ondulante familiares no editor. Pode ver a lista de erros e avisos escrever **Ctrl + Shift + M** ou ao selecionar as imagens na barra de estado para a esquerda inferior.

    ![erros](./media/resource-manager-vs-code/errors.png)

    Validação do seu modelo pode ajudá-lo se detetar problemas de sintaxe; No entanto, também poderá ver erros que pode ignorar. Em alguns casos, o editor é comparar o seu modelo de um esquema que, por conseguinte, relatórios um erro, apesar de saber a estiver correto e não estiver atualizado. Por exemplo, imaginemos uma função recentemente foi adicionada ao Gestor de recursos, mas não tenha sido atualizado o esquema. O editor de relatórios um erro apesar da que função funciona corretamente durante a implementação.

    ![mensagem de erro](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Implementar os novos recursos

Quando o modelo estiver pronto, pode implementar os novos recursos de seguindo as instruções abaixo: 

### <a name="windows"></a>Windows

1. Abra uma linha de comandos do PowerShell 
2. Para tipo de início de sessão: 

        Login-AzureRmAccount 

3. Se tiver múltiplas subscrições, obter uma lista de subscrições com:

        Get-AzureRmSubscription

    E selecione a subscrição para utilizar.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. Actualizar os parâmetros do ficheiro parameters.json
5. Executar Deploy.ps1 para implementar o seu modelo no Azure

### <a name="osxlinux"></a>Os x/Linux

1. Abra uma janela de terminal 
2. Para tipo de início de sessão:

        azure login 

3. Se tiver múltiplas subscrições, selecione a subscrição à direita com:

        azure account set <subscriptionNameOrId> 

4. Atualize os parâmetros no ficheiro parameters.json.
5. Para implementar o modelo, execute:

        azure group deployment create -f <PathToTemplate> 

## <a name="next-steps"></a>Próximos passos

- Para saber mais acerca dos modelos, consulte o artigo [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md).
- Para saber mais sobre as funções de modelo, consulte [funções de modelo de Gestor de recursos do Azure](resource-group-template-functions.md).
- Para obter mais exemplos de como trabalhar com o código do Visual Studio, consulte o artigo [compilação nuvem aplicações com o código do Visual Studio](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) a ligar [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para obter mais tutoriais a partir de demonstração HealthClinic.biz, consulte o artigo [Tutoriais de ferramentas de programador do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
