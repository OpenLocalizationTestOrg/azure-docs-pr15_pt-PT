<properties
    pageTitle="Descrição geral de esquemas e o pacote de integração de Enterprise | Microsoft Azure"
    description="Saiba como utilizar esquemas com as aplicações empresariais Integration Pack e lógica"
    services="logic-apps"
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
    ms.date="07/29/2016"
    ms.author="deonhe"/>

# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Saiba mais sobre esquemas e o pacote de integração de empresa  

## <a name="why-use-a-schema"></a>Porquê utilizar um esquema?
Utilize esquemas para confirmar que recebe de documentos XML são válidos, com os dados esperados num formato predefinido. Os esquemas são utilizados para validar mensagens que são trocadas num cenário de B2B.

## <a name="add-a-schema"></a>Adicionar um esquema
A partir do Azure portal:  

1. Selecione **mais serviços**.  
![Portal de captura de ecrã do Azure, com "Mais serviços de" realçada](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    

2. Na caixa de filtro de pesquisa, introduza **integração**e selecione **Contas de integração** da lista de resultados.     
![Captura de ecrã da caixa de pesquisa de filtro](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Selecione a **conta de integração** ao qual adicionar o esquema.    
![Captura de ecrã da lista de contas de integração](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  

4. Selecione o mosaico de **esquemas** .  
![Captura de ecrã de IEP integração de conta, com "Esquemas" realçados](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Adicionar um ficheiro de esquema inferior a 2 MB  

1. No pá de **esquemas** aberto (a partir dos passos anteriores), selecione **Adicionar**.  
![Pá captura de ecrã de esquemas, com o botão "Adicionar" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  

2. Introduza um nome para o seu esquema. Em seguida, para carregar o ficheiro de esquema, selecione o ícone de pasta junto à caixa de texto de **esquema** . Depois de concluir o processo de carregamento, clique **em OK**.    
![Captura de ecrã de "Adicionar esquema", com "Ficheiro pequeno" realçado](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Adicionar um ficheiro de esquema com mais de 2 MB (até um máximo de 8 MB)  

O processo para este depende do nível de acesso do contentor de BLOBs: **público** ou **sem acesso anónimo**. Para determinar este nível de acesso no **Explorador de armazenamento do Azure**, em **Contentores de BLOBs**, selecione o contentor de BLOBs que pretende. Selecione **segurança**e selecione o separador de **Nível de acesso** .

1. Se o nível de acesso de segurança blob for **público**, siga estes passos.  
  ![Captura de ecrã do Azure Explorador de armazenamento, com "Blob contentores", "Segurança" e "Público" realçada](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

    um. Carregar o esquema para o armazenamento e copie o URI.  
    ![Captura de ecrã da conta de armazenamento, com o URI realçada](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

    b. No **Esquema de adicionar**, selecione **ficheiro de grandes dimensões**e fornecer o URI na caixa de texto de **URI de conteúdo** .  
    ![Captura de ecrã de esquemas, com o botão "Adicionar" e "Ficheiro de grandes dimensões" realçada](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

2. Se o nível de acesso de segurança blob **sem acesso anónimo**, siga estes passos.  
  ![Captura de ecrã do Azure Explorador de armazenamento, com "Blob contentores", "Segurança" e "Sem acesso anónimo" realçada](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

    um. Carregue o esquema ao armazenamento.  
    ![Captura de ecrã da conta de armazenamento](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

    b. Gera uma assinatura de acesso partilhado para o esquema.  
    ![Captura de ecrã do armazenamento sccount, com acesso partilhado assinaturas separador realçada](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

    c. No **Esquema de adicionar**, selecione **ficheiro de grandes dimensões**e fornecer a assinatura de acesso partilhado URI na caixa de texto de **URI de conteúdo** .  
    ![Captura de ecrã de esquemas, com o botão "Adicionar" e "Ficheiro de grandes dimensões" realçada](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

3. Na pá **esquemas** da conta de integração de EIP, agora, deverá ver o esquema recentemente adicionado.  
![Captura de ecrã de EIP integração de conta, com "Esquemas" e o novo esquema realçada](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)
  

## <a name="edit-schemas"></a>Editar esquemas
1. Selecione o mosaico de **esquemas** .  
2. Selecione o esquema que pretende editar a partir do pá **esquemas** que é aberta.
3. No pá **esquemas** , selecione **Editar**.  
![Pá captura de ecrã de esquemas](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Selecione o ficheiro de esquema que pretende editar, utilizando a caixa de diálogo Seletor de ficheiro que é aberta.
5. Selecione **Abrir** no Seletor de ficheiro.  
![Captura de ecrã de seleccionador de ficheiros](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Receber uma notificação que indica que o carregamento foi efetuada com êxito.  

## <a name="delete-schemas"></a>Eliminar esquemas
1. Selecione o mosaico de **esquemas** .  
2. Selecione o esquema que pretende eliminar a partir do pá **esquemas** que é aberta.  
3. No pá **esquemas** , selecione **Eliminar**.
![Pá captura de ecrã de esquemas](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  

4. Para confirmar a sua escolha, selecione **Sim**.  
![Captura de ecrã de mensagem de confirmação "Eliminar esquema"](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Por fim, repare que atualiza a lista de esquemas no pá **esquemas** , o esquema que eliminou já não está listado.  
![Captura de ecrã de EIP integração de conta, com "Esquemas" realçados](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Próximos passos

- [Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa").  
