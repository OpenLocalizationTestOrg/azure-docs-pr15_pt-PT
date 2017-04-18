<properties
    pageTitle="Saiba como codificar ou descodificar ficheiros simples, utilizar as aplicações do pacote de integração de empresa e lógica | Aplicação de serviço do Microsoft Azure | Microsoft Azure"
    description="Utilize as funcionalidades de aplicações empresariais Integration Pack e lógica para codificar ou descodificar ficheiros simples"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="msftman"
    manager="erikre"
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-flat-files"></a>Integração da empresa com os ficheiros simples

## <a name="overview"></a>Descrição geral

Pretende codificar conteúdos XML antes de enviar para um parceiro de negócio num cenário de negócio para empresas (B2B). Numa aplicação de lógica efetuadas pela funcionalidade de lógica aplicações do serviço de aplicação do Azure, pode utilizar o ficheiro simples a codificação de conexão para o fazer. A lógica que criar pode obter a aplicação respetivo XML conteúdo de uma variedade de origens, incluindo a partir de um acionador de pedido HTTP, a partir de outra aplicação ou mesmo a partir de um dos muitos [conectores](../connectors/apis-list.md). Para mais informações sobre aplicações de lógica, consulte a [documentação de aplicações lógica](./app-service-logic-what-are-logic-apps.md "Saiba mais sobre aplicações de lógica").  

## <a name="how-to-create-the-flat-file-encoding-connector"></a>Como criar o ficheiro simples a codificação de conexão

Siga estes passos para adicionar um ficheiro simples a codificação de conexão para a sua aplicação de lógica.

1. Crie uma aplicação de lógica e, em seguida, [ligá-lo à sua conta de integração](./app-service-logic-enterprise-integration-accounts.md "aprender a associar uma conta de integração para uma aplicação de lógica"). Esta conta contém o esquema que irá utilizar para codificar os dados XML.  
2. Adicione um accionador **pedido - pedido de HTTP an quando for recebido** para a sua aplicação de lógica.  
![Captura de ecrã de accionador para selecionar](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
3. Adicione o ficheiro simples a codificação de ação, da seguinte forma:

    um. Selecione o sinal de **mais** .

    b. Selecione a ligação **Adicionar uma ação** (aparece depois de selecionar o sinal de adição).

    c. Na caixa de pesquisa, introduza *simples* para filtrar todas as ações ao que pretende utilizar.

    d. Selecione a opção **De codificação ficheiro simples** a partir da lista.   
![Captura de ecrã da simples ficheiro codificação de opção](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
4. Na caixa de diálogo **Codificação de ficheiro simples** , selecione a caixa de texto de **conteúdo** .  
![Captura de ecrã de conteúdo caixa de texto](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)  
5. Selecione a etiqueta de corpo como o conteúdo que pretende codificar. A etiqueta de corpo irão povoar o campo de conteúdo.     
![Captura de ecrã da etiqueta do corpo](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)  
6. Selecione a caixa de listagem de **Nome de esquema** e escolha o esquema que pretende utilizar para codificar o conteúdo de entrada.    
![Caixa de listagem de captura de ecrã do nome do esquema](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)  
7. Guarde o seu trabalho.   
![Captura de ecrã de guardar ícone](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)  

Neste momento, terminar a configurar a conexão de codificação de ficheiro simples. Numa aplicação do mundo real, pretende armazenar os dados codificados numa aplicação de linha de negócio, tal como a equipa de vendas. Ou pode enviar que codificado dados para um negociação do parceiro. Pode adicionar facilmente uma ação para enviar o resultado da ação codificação para Salesforce ou para o seu parceiro negociação, utilizando qualquer um dos outros conectores fornecidos.

Agora pode testar a conexão ao efetuar um pedido para o ponto final HTTP e, incluindo o conteúdo XML no corpo do pedido.  

## <a name="how-to-create-the-flat-file-decoding-connector"></a>Como criar o ficheiro simples descodificar conexão

>[AZURE.NOTE] Para concluir estes passos, tem de ter um ficheiro de esquema já carregado para a conta integração.

1. Adicione um accionador **pedido - pedido de HTTP an quando for recebido** para a sua aplicação de lógica.  
![Captura de ecrã de accionador para selecionar](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)    
2. Adicione ficheiro simples descodificar ação, da seguinte forma:

    um. Selecione o sinal de **mais** .

    b. Selecione a ligação **Adicionar uma ação** (aparece depois de selecionar o sinal de adição).

    c. Na caixa de pesquisa, introduza *simples* para filtrar todas as ações ao que pretende utilizar.

    d. Selecione a opção **Descodificar de ficheiro simples** a partir da lista.   
![Captura de ecrã da simples ficheiro descodificar opção](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)   
- Selecione o controlo de **conteúdo** . Isto gera uma lista de conteúdo a partir de passos anteriores que pode utilizar como o conteúdo para descodificar. Repare que está disponível para ser utilizado como o conteúdo para descodificar do *corpo* do pedido de HTTP recebido. Também pode introduzir o conteúdo para descodificar diretamente para o controlo de **conteúdo** .     
- Selecione a etiqueta do *corpo* . Aviso da tag do corpo está agora no controlo de **conteúdo** .
- Selecione o nome do esquema que pretende utilizar para descodificar o conteúdo. A seguinte captura de ecrã mostra que *OrderFile* é o nome do esquema selecionado. Este nome do esquema tivesse sido carregado na conta do integração anteriormente.

 ![Captura de ecrã da simples ficheiro descodificar caixa de diálogo](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)    
- Guarde o seu trabalho.  
![Captura de ecrã de guardar ícone](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)    

Neste momento, terminar configurar o seu ficheiro simples descodificar conexão. Numa aplicação do mundo real, pretende armazenar os dados descodificados numa aplicação de linha de negócio como Salesforce. Pode adicionar facilmente uma ação para enviar o resultado da ação descodificação a equipa de vendas.

Agora pode testar a conexão ao efetuar um pedido para o ponto final HTTP e incluindo o conteúdo XML que pretende codificar no corpo do pedido.  

## <a name="next-steps"></a>Próximos passos
- [Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa").  
