<properties 
    pageTitle="Descrição geral da validação de XML no pacote de integração de Enterprise | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
    description="Saiba como funciona o validação nas aplicações empresariais Integration Pack e lógica" 
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

# <a name="enterprise-integration-with-xml-validation"></a>Integração da empresa com a validação de XML

## <a name="overview"></a>Descrição geral
Muitas vezes, B2B cenários, os parceiros para o contrato de um necessitam validar que as mensagens que trocarem entre si são válidas antes de poderem começar processamento dos dados. No pacote de integração de empresa, pode utilizar o conector de XML validação para validar documentos contra um esquema predefinido.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Como validar um documento com o conector de validação de XML
1. Crie uma aplicação de lógica e, em seguida, [ligá-lo à sua conta de integração](./app-service-logic-enterprise-integration-accounts.md "aprender a associar uma conta de integração para uma aplicação de lógica") que contém o esquema que irá utilizar para validar os dados XML.
2. Adicionar um accionador **pedido - pedido de HTTP an quando for recebido** para a sua aplicação de lógica  
![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)    
3. Adicionar a ação de **Validação de XML** ao primeiro seleccionando **Adicionar uma ação**  
4. Introduza *xml* na caixa de pesquisa para filtrar todas as ações ao que pretende utilizar 
5. Selecione a **validação de XML**     
![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)   
6. Selecione a caixa de texto de **conteúdo**  
![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Selecione a etiqueta de corpo como o conteúdo que será validado.   
![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)  
8. Selecione a caixa de listagem de **Nome de esquema** e escolha o esquema que pretende utilizar para validar o teclado *conteúdo* acima     
![](./media/app-service-logic-enterprise-integration-xml/xml-4.png) 
9. Guardar o seu trabalho  
![](./media/app-service-logic-enterprise-integration-xml/xml-5.png) 

Neste momento, terminar a configurar a conexão de validação. Numa aplicação do mundo real, poderá querer armazenar os dados validados numa aplicação LOB como SalesForce. Pode adicionar facilmente uma ação para enviar o resultado da validação da equipa de vendas. 

Agora pode testar a ação de validação ao efetuar um pedido para o ponto final HTTP.  

## <a name="next-steps"></a>Próximos passos

[Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa")   