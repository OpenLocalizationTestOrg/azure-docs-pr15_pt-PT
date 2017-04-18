<properties
   pageTitle="Recurso Azure Explorer | Microsoft Azure"
   description="Descreve Explorer de recursos do Azure e como pode ser utilizado para ver e atualizar as implementações através do Gestor de recursos do Azure"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Utilizar o Explorador de recursos do Azure para ver e modificar recursos
O [Explorador de recursos do Azure](https://resources.azure.com) é uma ótima ferramenta para consultar recursos que já criou na sua subscrição. Ao utilizar esta ferramenta, é possível compreender como os recursos estão estruturados e ver as propriedades atribuídas a cada recurso. Pode obter informações sobre as operações de REST API e os cmdlets do PowerShell que estão disponíveis para um tipo de recurso e pode emitir comandos através da interface. Explorador do recurso pode ser bastante úteis quando estiver a criar modelos de Gestor de recursos porque permite-lhe ver as propriedades para recursos existentes.

A origem de para a ferramenta de Explorer recurso está disponível no [github](https://github.com/projectkudu/ARMExplorer), que fornece uma referência útil se precisar de implementar um comportamento semelhante nas suas próprias aplicações.

## <a name="view-resources"></a>Ver recursos
Navegue para [https://resources.azure.com](https://resources.azure.com) e inicie sessão com as mesmas credenciais que utilizaria para o [Portal do Azure](https://portal.azure.com).

Depois de carregado, treeview à esquerda permite-lhe desagregar suas subscrições e grupos de recursos:

![TreeView](./media/resource-manager-resource-explorer/are-01-treeview.png)

Como explorar um grupo de recursos irá ver os fornecedores de para as quais não existem recursos nesse grupo:

![fornecedores](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

A partir daqui, pode começar a explorar nas instâncias de recurso. Na captura de ecrã abaixo pode ver o `sltest` instância do SQL Server em treeview. No lado direito, pode ver informações sobre os pedidos de REST API que pode utilizar com esse recurso. Ao navegar para o nó de um recurso, o recurso Explorer toma automaticamente o pedido GET para obter informações sobre o recurso. Na área de texto de grandes dimensões abaixo o URL, verá a resposta a partir da API. 

À medida que se familiarize com modelos de Gestor de recursos é iniciado o conteúdo de corpo procurar familiares! Na secção **Propriedades** da resposta corresponde os valores que pode fornecer na secção **Propriedades** do seu modelo.

![do SQL server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Recurso Explorer permite-lhe manter desagregar para explorar recursos subordinado, no caso do servidor de base de dados SQL, existem recursos de subordinado para coisas como bases de dados e as regras de firewall.

Explorar uma base de dados mostra-nas propriedades para essa base de dados. Na captura de ecrã abaixo podemos ver que a base de dados `edition` é `Standard` e o `serviceLevelObjective` (ou camada de base de dados) for `S1`.

![base de dados SQL](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>Alterar os recursos

Assim que tiver compreendê-las a um recurso, pode selecionar o botão Editar para tornar o conteúdo JSON editável. Em seguida, pode utilizar o Explorador de recursos para editar o JSON e enviar um pedido de colocar para alterar o recurso. Por exemplo, a imagem abaixo mostra a camada de base de dados alterada para `S0`:

![base de dados - colocar pedido](./media/resource-manager-resource-explorer/are-05-database-put.png)

Ao selecionar a **colocar** submeter o pedido. 

Assim que o pedido foi submetido recurso Explorer problemas novamente o pedido GET para atualizar o estado. Neste caso pode Vemos que o `requestedServiceObjectiveId` foi atualizado e é a diferença entre o `currentServiceObjectiveId` que indica que uma operação de dimensionamento está em curso. Pode clicar no botão obter para atualizar manualmente o estado.

![base de dados - obter request2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>Realizar ações em recursos

No separador **ações** permite-lhe ver e executar operações de resto adicionais. Por exemplo, depois de selecionar um recurso de web site, no separador ações apresenta uma longa lista de operações disponíveis, algumas das quais são apresentadas abaixo.

![Web - pedido de mensagem](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>Invocar a API através do PowerShell
No separador do PowerShell no Explorador do recurso mostra-lhe os cmdlets, para utilizar para interagir com o recurso que está atualmente a explorar. Dependendo do tipo de recurso que selecionou, o script do PowerShell apresentado pode variar entre os cmdlets do simples (tal como `Get-AzureRmResource` e `Set-AzureRmResource`) para cmdlets mais complicados (como trocar faixas num web site). 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Para mais informações sobre os cmdlets do Azure PowerShell, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](powershell-azure-resource-manager.md)

## <a name="summary"></a>Resumo
Quando trabalhar com o Gestor de recursos, o Explorador do recurso pode ser uma ferramenta extremamente útil. É uma excelente forma de encontrar formas de utilizar o PowerShell para consultar e efetuar alterações. Se está a trabalhar com a API REST é uma excelente forma de começar a utilizar e testar rapidamente chamadas à API antes de começar a escrever o código. E se estiver a escrever modelos pode ser uma excelente forma de compreender a hierarquia de recursos e localizar o local onde pretende guardar configuração - pode efetuar uma alteração no Portal do e, em seguida, localize as entradas correspondentes no Explorador do recurso!

Para obter mais informações, veja a [9 de canal de vídeo com Scott Hanselman e David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)


