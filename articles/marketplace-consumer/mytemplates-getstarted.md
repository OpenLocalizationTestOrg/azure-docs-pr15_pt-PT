<properties
   pageTitle="Começar a trabalhar com modelos privados | Microsoft Azure"
   description="Adicionar, gerir e partilhar os seus modelos privados com o portal do Azure, o clip do Azure ou PowerShell."
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Começar a trabalhar com modelos privados no Portal do Azure

Um modelo de [Gestor de recursos do Azure](../resource-group-authoring-templates.md) é um modelo declarativa utilizado para definir a sua implementação. Pode definir os recursos para implementar uma solução e especificar parâmetros e variáveis que permitem-lhe valores para diferentes ambientes de entrada. O modelo é composta por JSON e expressões que pode utilizar para construir valores para a sua implementação.

Pode utilizar a nova funcionalidade de **modelos** no [Portal do Azure](https://portal.azure.com) juntamente com o fornecedor de recurso **Microsoft.Gallery** como uma extensão do [Azure Marketplace](https://azure.microsoft.com/marketplace/) para permitir que os utilizadores criar, gerir e implementar modelos de privada de uma biblioteca pessoal.

Este documento orienta-o através de adicionar, gerir e partilhar um privado **modelo** utilizando o Portal do Azure.

## <a name="guidance"></a>Orientação

As seguintes sugestões irão ajudá-lo a tirar o máximo partido dos **modelos de** quando trabalha com as soluções:

- Um **modelo** é um recurso encapsulating que contém um modelo de Gestor de recursos e metadados adicionais. -Comporta-se de forma muito semelhante a um item no mercado. A diferença de chave é que é um item privado por oposição os itens de Marketplace públicos.
- A biblioteca de **modelos** funciona bem com utilizadores que precisam de personalizar os respetivos implementações.
- **Modelos de** funcionar bem para utilizadores que precisam de um repositório simple dentro do Azure.
- Comece com um modelo de Gestor de recursos existente. Localize modelos no [github](https://github.com/Azure/azure-quickstart-templates) ou [Exportar modelo](../resource-manager-export-template.md) a partir de um grupo de recursos existente.
- **Modelos** estão associados ao utilizador que publica-los. O nome do publisher está visível para todos os utilizadores que tenha acesso de leitura à mesma.
- **Os modelos** são recursos de Gestor de recursos e não pode ser mudado uma vez publicado.

## <a name="add-a-template-resource"></a>Adicionar um recurso de modelo

Existem duas formas para criar um recurso de **modelo** no portal do Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Método 1: Criar um novo recurso de modelo a partir de um grupo de recursos em execução

1. Navegue para um grupo de recursos existente no Portal do Azure. Selecione **Exportar modelo** nas **Definições**.
2. Depois de exportados o modelo de Gestor de recursos, utilize o botão **Guardar modelo** guardá-lo para o repositório de **modelos** . Localizar os detalhes completos para exportar modelo [aqui](../resource-manager-export-template.md).
<br /><br />
![Exportar do grupo de recursos](media/rg-export-portal1.PNG)  <br />

3. Selecione o botão de comando **Guardar no modelo** .
<br /><br />

4. Introduza as seguintes informações:

    - Nome – nome do objeto modelo (Nota: Este é o nome de um Gestor de recursos do Azure com base. Aplicam a todas as restrições de nomenclatura e não pode ser alterada depois de criado).
    - Descrição – resumo rápido sobre o modelo.

    ![Guardar modelo](media/save-template-portal1.PNG)  <br />

5. Clique em **Guardar**.

    > [AZURE.NOTE] O pá de modelo de exportação mostra notificações quando o modelo de Gestor de recursos exportado tem erros, mas ainda ser capaz de guardar este modelo de Gestor de recursos para os modelos. Certifique-se de que verificar e corrigir problemas de modelo de qualquer gestor de recursos antes de Reimplementar o modelo de Gestor de recursos exportado.

### <a name="b-method-2--add-a-new-template-resource-from-browse"></a>B. Método 2: Adicionar um novo recurso de modelo de procurar

Também pode adicionar um novo **modelo** a partir do rascunho utilizando o + adicionar o botão de comando num **Procurar > modelos de**. Terá de fornecer um nome, descrição e o modelo de Gestor de recursos JSON.

![Adicionar modelo](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery é que um inquilino com base no fornecedor de recursos Azure. O recurso de modelo está associado ao utilizador que criou. Não está associada a nenhuma subscrição específica. Uma subscrição tem de ser escolhido apenas quando implementar um modelo.

## <a name="view-template-resources"></a>Ver os recursos de modelo

Todos os **modelos** disponíveis para podem ver visto na **Procurar > modelos de**. Isto inclui **modelos** que criou assim como fases, que tenham sido partilhados consigo com vários níveis de permissões. Obter mais detalhes na secção de [controlo de acesso](#access-control-for-a-tenant-resource-provider) abaixo.

![Vista de modelo](media/view-template-portal1.PNG)  <br />

Pode ver os detalhes de um **modelo** ao clicar num item na lista.

![Vista de modelo](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Editar um recurso de modelo

Pode iniciar o fluxo de edição para um **modelo** ao clicar com o botão direito do rato no item na lista Procurar ou ao selecionar o botão de comando Editar.

![Editar modelo](media/edit-template-portal1a.PNG)  <br />

Pode editar a descrição ou texto de modelo de Gestor de recursos. Não pode editar o nome de uma vez que é o nome do recurso um Gestor de recursos. Quando o utilizador editar o modelo de Gestor de recursos JSON podemos irá validar para se certificar de que é válida JSON. Selecione **OK** e, em seguida, **Guardar** para guardar o modelo atualizado.

![Editar modelo](media/edit-template-portal2a.PNG)  <br />

Assim que o **modelo** é guardado irá ver uma notificação de confirmação.

![Editar modelo](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Implementar um recurso de modelo

Pode implementar qualquer **modelo** que tem as permissões de **leitura** no. O fluxo de implementação inicia a pá de implementação do Azure modelo padrão. Preencha os valores para os parâmetros de modelo de Gestor de recursos continuar com a implementação.

![Implementar o modelo](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Partilhar um recurso de modelo

Um recurso de **modelo** pode ser partilhado com dos seus colegas. Partilha comporta-se de forma semelhante a [atribuição de funções para todos os recursos no Azure](../active-directory/role-based-access-control-configure.md). O proprietário do **modelo** fornece as permissões para outros utilizadores que podem interagir com um recurso de modelo. A pessoa ou grupo de pessoas que partilham o **modelo** com poderão ver o modelo de Gestor de recursos e as respetivas propriedades de galeria.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Controlo de acesso para os recursos de Microsoft.Gallery

Função | Permissões
---|----
Proprietário | Permite que o controlo total sobre o recurso de modelo, incluindo a partilhar
Leitor | Permite a leitura e Execute(Deploy) do recurso de modelo
Contribuinte | Permite a permissão para editar e eliminar o recurso de modelo. Utilizador não pode partilhar o modelo com outras pessoas

Clique em **partilhar** no item procurar clicando em botão direito do rato ou no pá a vista de um item específico. Isto inicia uma experiência de partilha.

![Modelo de partilhar](media/share-template-portal1a.png)  <br />

 Agora pode escolher uma função e um utilizador ou grupo para fornecer acesso a um determinado **modelo**. As funções disponíveis estão proprietário, leitor e contribuinte. Obter mais detalhes na secção [controlo de acesso](#access-control-for-a-tenant-resource-provider) acima.

![Modelo de partilhar](media/share-template-portal2b.png)  <br />

![Modelo de partilhar](media/share-template-portal3b.png)  <br />

Clique em **Ok**e **Selecione** . Agora pode ver os utilizadores ou grupos que adicionou ao recurso.

![Modelo de partilhar](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] Um modelo apenas pode ser partilhado com utilizadores e grupos ao mesmo inquilino Azure Active Directory. Se partilhar um modelo com um endereço de e-mail que não está no seu inquilino, um convite será enviado perguntar ao utilizador para participar o inquilino como convidado.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre a criação de modelos de Gestor de recursos, consulte o artigo [modelos de criação](../resource-group-authoring-templates.md)
- Para compreender as funções que pode utilizar um modelo de Gestor de recursos, consulte [funções de modelo](../resource-group-template-functions.md)
- Para obter orientações sobre estruturar os seus modelos, consulte o artigo [melhores práticas para criar modelos de Gestor de recursos do Azure](../best-practices-resource-manager-design-templates.md)
