
<properties
    pageTitle="Utilizar certificados com o pacote de integração de Enterprise | Microsoft Azure"
    description="Saiba como utilizar certificados com o pacote de integração de empresa e Apps de lógica"
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
    ms.date="09/06/2016"
    ms.author="deonhe"/>

# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Saiba mais sobre certificados e Enterprise Integration Pack

## <a name="overview"></a>Descrição geral
Integração da empresa utiliza certificados para proteger as comunicações B2B. Pode utilizar dois tipos de certificados nas aplicações de integração com o seu enterprise:

- Certificados pública, tem de ser adquiridos a partir de uma autoridade de certificação (AC).
- Privado, certificados que pode emitir si mesmo. Estes certificados são por vezes designados autoassinados certificados.


## <a name="what-are-certificates"></a>O que são certificados?
Os certificados são documentos digitais que verificar a identidade dos participantes comunicações Eletrónicos e que também proteger as comunicações eletrónicos.

## <a name="why-use-certificates"></a>Porquê utilizar certificados?
Por vezes B2B comunicações devem ser mantidas confidenciais. Integração da empresa utiliza certificados para proteger estas comunicações de duas maneiras:

- Ao encriptá o conteúdo das mensagens
- Ao assinar mensagens digitalmente  

## <a name="how-do-you-upload-certificates"></a>Como carregar certificados

### <a name="public-certificates"></a>Certificados de pública
Para utilizar um *certificado pública* as suas aplicações lógica com capacidades B2B, tem primeiro carregar o certificado para a sua conta de integração. Para utilizar um *certificado autoassinado*, outro lado, tem pela primeira vez carregá-lo para [Azure chave cofre](../key-vault/key-vault-get-started.md "Saiba mais sobre chave cofre").

Depois de carregar um certificado, este fica disponível para ajudar a proteger as suas mensagens B2B quando define as respectivas propriedades nos [acordos](./app-service-logic-enterprise-integration-agreements.md) que criar.  

Eis os passos detalhados para carregar os seus certificados públicos para a sua conta de integração depois de iniciar sessão portal do Azure:

1. Selecione **Procurar**.  
    ![Selecione procurar](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  

2. Introduza **integração** na caixa de pesquisa de filtro e, em seguida, selecione **Contas de integração** da lista de resultados.     
    ![Selecione contas de integração](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. Selecione a conta de integração ao qual pretende adicionar o certificado.  
    ![Selecione a conta de integração ao qual pretende adicionar o certificado](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4.  Selecione o mosaico de **certificados** .  
    ![Selecione o mosaico de certificados](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. Na pá de **certificados** que se abre, selecione o botão **Adicionar** .
    ![Selecione o botão Adicionar](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Introduza um **nome** para o certificado e, em seguida, selecione o tipo de certificado. (Neste exemplo, utilizámos o tipo de certificado público.) Em seguida, selecione o ícone de pasta no lado direito da caixa de texto de **certificado** .

7. Quando abre o selecionador de ficheiro, localize e selecione o ficheiro de certificado que pretende carregar para a sua conta de integração.

8. Selecione o certificado e, em seguida, selecione **OK** no Seletor de ficheiro. Isto valida e carrega o certificado à sua conta de integração.

8. Por fim, novamente no pá **Adicionar certificado** , selecione o botão **OK** .  
    ![Selecione o botão OK](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  

9. No cerca de um minuto, irá ver uma notificação que indica que o carregamento de certificado está concluído.

10. Selecione o mosaico de **certificados** . Deverá ver o certificado recentemente adicionado.  
    ![Ver o novo certificado](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Certificados privados
Pode carregar certificados privados para a sua conta de integração ao efetuar os passos seguintes:  

1. [Carregar a sua chave cofre a chave privada] (../key-vault/key-vault-get-started.md "Saiba mais sobre chave cofre").  

    > [AZURE.TIP] Tem de autorizar a funcionalidade de lógica aplicações de serviço de aplicação do Azure para executar operações em chave cofre. Pode conceder acesso ao capital de serviço de lógica aplicações utilizando o comando PowerShell seguinte:`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  

2. Crie um certificado privado.  

3. Carregue o certificado privado à sua conta de integração.

Após ter tomado os passos anteriores, pode utilizar o certificado privado para criar contratos.

Seguem-se os passos detalhados para carregar os seus certificados privados para a sua conta de integração depois de iniciar sessão portal do Azure:  

1. Selecione **Procurar**.  
    ![Carregue os seus certificados privados para a sua conta de integração](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

2. Introduza **integração** na caixa de pesquisa de filtro e, em seguida, selecione **Contas de integração** da lista de resultados.     
    ![Selecione contas de integração](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  

3. Selecione a conta de integração ao qual pretende adicionar o certificado.  
    ![Selecione a conta de integração ao qual pretende adicionar o certificado](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4. Selecione o mosaico de **certificados** .  
    ![Selecione o mosaico de certificados](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  

5. Na pá de **certificados** que se abre, selecione o botão **Adicionar** .
    ![Selecione o botão Adicionar](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Introduza um **nome** para o certificado e, em seguida, selecione o tipo de certificado. (Neste exemplo, utilizámos o tipo de certificado público.) Em seguida, selecione o ícone de pasta no lado direito da caixa de texto de **certificado** .

7. Quando abre o selecionador de ficheiro, localize e selecione o ficheiro de certificado que pretende carregar para a sua conta de integração.

8. Depois de selecionar o certificado, selecione **OK** no Seletor de ficheiro. Esta ação valida o certificado e carrega-lo à sua conta de integração.

9. Por fim, novamente no pá **Adicionar certificado** , selecione o botão **OK** .  
    ![Adicionar o certificado](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  

10. No cerca de um minuto, irá ver uma notificação que indica que o carregamento de certificado está concluído.

11. Selecione o mosaico de **certificados** . Deverá ver o certificado recentemente adicionado.
    ![Ver o novo certificado](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

Depois de carregar um certificado, este fica disponível para ajudar a proteger as suas mensagens B2B quando define as respectivas propriedades em [acordos](./app-service-logic-enterprise-integration-agreements.md).  

## <a name="next-steps"></a>Próximos passos
- [Criar uma aplicação de lógica que utiliza funcionalidades B2B](./app-service-logic-enterprise-integration-b2b.md)  
- [Criar um contrato B2B](./app-service-logic-enterprise-integration-agreements.md)  
- [Saber mais sobre cofre chave] (../key-vault/key-vault-get-started.md "Saiba mais sobre cofre chave")  
