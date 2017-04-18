<properties 
    pageTitle="Descrição geral de contas de integração e o pacote de integração de Enterprise | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
    description="Saiba tudo sobre contas de integração, o pacote de integração de empresa e aplicações de lógica" 
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
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-integration-accounts"></a>Descrição geral de contas de integração

## <a name="what-is-an-integration-account"></a>O que é uma conta de integração?
Uma conta de integração é uma conta Azure que permite a integração da empresa aplicações gerir artefactos incluindo esquemas, mapas, certificados, parceiros e os acordos. Qualquer aplicação integração que cria serão tem de utilizar uma conta de integração para poder aceder a um esquema, o mapa ou o certificado, por exemplo.

## <a name="create-an-integration-account"></a>Criar uma conta de integração 
1. Selecione **Procurar**   
![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)  
2. Introduza **integração** na caixa de pesquisa de filtro e selecione **Contas de integração** da lista de resultados     
 ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)  
3. Selecione o botão *Adicionar* a partir do menu na parte superior da página      
![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)  
4. Introduza o **nome**, selecione a **subscrição** que pretende utilizar, crie um novo **grupo de recursos** ou selecione um grupo de recursos existente, selecione uma **localização** onde sua conta de integração serão alojados, selecione uma **camada de preços**, em seguida, selecione o botão **Criar** .   

  Neste momento, a conta de integração será aprovisionada na localização que selecionou. Isto deve concluída dentro de um minuto.    
![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)  
5. Atualize a página. Irá ver a sua nova conta de integração listada. Parabéns!  
![](./media/app-service-logic-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>Como associar uma conta de integração a uma aplicação de lógica
Ordem para as suas aplicações lógica aceder ao mapas, esquemas, acordos e outros artefactos que estão localizados na sua conta de integração, tem primeiro de ligar a conta de integração para a sua aplicação de lógica.

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>Eis os passos para associar uma conta de integração a uma aplicação de lógica 

#### <a name="prerequisites"></a>Pré-requisitos
- Uma conta de integração
- Uma aplicação de lógica

>[AZURE.NOTE]Certifique-se de que a conta de integração e a aplicação de lógica se encontram na **mesma localização Azure** antes de começar

1. Selecione **as definições de** ligação a partir do menu da sua aplicação de lógica  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)   
2. Selecione o item de **Integração de conta** a partir do pá definições  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)   
3. Selecione a conta de integração que pretende ligar à sua caixa de lista pendente aplicação a partir de **selecionar uma conta de integração** de lógica  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)   
4. Guardar o seu trabalho  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)   
5. Irá ver uma notificação que indica que a sua conta de integração tem foi ligada para a sua aplicação de lógica e que todas as artefactos na sua conta de integração estão agora disponíveis para a sua aplicação de lógica.  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)   

Agora que a sua conta de integração está ligada para a sua aplicação de lógica, pode utilizar Ir para a sua aplicação de lógica e utilize B2B conectores como validação XML, ficheiro simples Codificar/descodificar ou transformação para criar aplicações com funcionalidades B2B.  
    
## <a name="how-to-delete-an-integration-account"></a>Como eliminar uma conta de integração?
1. Selecione **Procurar**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Introduza **integração** na caixa de pesquisa de filtro e selecione **Contas de integração** da lista de resultados     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Selecione a **conta de integração** que pretende eliminar  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Selecione a ligação **Eliminar** que se encontra no menu   
![](./media/app-service-logic-enterprise-integration-accounts/delete.png)  
5. Confirmar a sua escolha    

## <a name="how-to-move-an-integration-account"></a>Como mover uma conta de integração?
Pode mover facilmente uma conta de integração para uma nova subscrição e um novo grupo de recursos. Se precisar de deslocar-se a sua conta de integração, siga estes passos:

>[AZURE.IMPORTANT] Terá de atualizar todos os scripts para utilizar o novo recurso IDs depois de mover uma conta de integração.

1. Selecione **Procurar**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Introduza **integração** na caixa de pesquisa de filtro e selecione **Contas de integração** da lista de resultados     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Selecione a **conta de integração** que pretende eliminar  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Selecione a ligação **Deslocar-se** que se encontra no menu   
![](./media/app-service-logic-enterprise-integration-accounts/move.png)  
5. Confirmar a sua escolha    

## <a name="next-steps"></a>Próximos passos
- [Saber mais sobre os acordos] (./app-service-logic-enterprise-integration-agreements.md "Saiba mais sobre acordos de integração de empresa")  


 