<properties
    pageTitle="Como migrar lógica aplicações para o esquema versão 2015-08-01-pré-visualizar | Aplicação de serviço do Microsoft Azure"
    description="Pode facilmente migrar as suas aplicações de lógica para a versão mais recente do esquema. Basta seguir estes passos."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Como migrar lógica aplicações para o esquema versão 2015-08-01-pré-visualização

Para mover as suas aplicações lógica existente para o novo esquema, faça o seguinte:  
1. Abra a sua aplicação de lógica no portal do Azure  
2. Clique em esquema de atualização:

 ![Ícone de API][step1]   
A página Update esquema apresenta e fornece uma ligação a um documento que fornecem detalhes sobre os melhoramentos no esquema de novo: ![API do ícone][step2]

>[AZURE.NOTE] Quando selecionar **O esquema de atualização**, podemos automaticamente executar os passos de migração e forneça a saída de código para si. Pode utilizar esta opção para atualizar a sua definição, no entanto, certifique-se de que segue bons processos de codificação como aqueles descritos na secção de **melhores práticas** abaixo.

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Melhores práticas ao migrar as suas aplicações de lógica para a versão mais recente do esquema:  

- Copiar o script migrado para uma nova aplicação de lógica - não substituir antigo um até ter concluído os testes e confirmado a aplicação migrada funciona conforme esperado.
- Teste o seu lógica aplicação **antes de** colocação de produção
- Após a conclusão da migração, comece a atualizar as suas aplicações lógica para utilizar a [API gerido](./apis-list.md) sempre que possível. Por exemplo, pode começar a utilizar o Dropbox v2, ser estiver a utilizar o DropBox v1.


## <a name="whats-next"></a>O que se segue
-  [Saiba como migrar manualmente as suas aplicações de lógica](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






