<properties
    pageTitle="Descrição geral de aplicações Web de pilha Azure | Microsoft Azure"
    description="Descrição geral das aplicações Web do Azure empilhados"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="azure-stack-web-apps-overview"></a>Descrição geral do Azure pilha Web Apps
    
> [AZURE.NOTE] As seguintes informações só se aplica a implementações do Azure pilha TP1.

Azure pilha Web Apps é o primeiro elemento de oferta a aplicação de serviço do Azure importado para Azure pilha. O instalador do Azure pilha Web Apps criará uma instância de cada um dos cinco tipos de função necessários e também irá criar um servidor de ficheiros. Apesar de poder adicionar mais ocorrências para cada um dos tipos de funções, lembre-se de que não existe quantidade de espaço para VMs no 1 de pré-visualização técnica. As capacidades atuais para Azure pilha Web Apps são principalmente foundation as funcionalidades que são necessários para gerir as aplicações de web do sistema e o anfitrião.

![Pilha Azure aplicação de serviço Web Apps no Azure empilhar Portal][1]

## <a name="limitations-of-the-technical-preview"></a>Limitações da pré-visualização técnica

Não existe suporte para as versões de pré-visualização do serviço de aplicação do Azure pilha. Não coloque cargas de trabalho de produção nesta versão de pré-visualização. Também não existe nenhuma actualização entre versões de pré-visualização do serviço de aplicação do Azure pilha. Efeitos principais destas edições de pré-visualização são para mostrar o que estamos a fornecer e obter comentários. 

## <a name="what-is-an-app-service-plan"></a>O que é um plano de serviço de aplicação?

O fornecedor de recursos do Azure pilha Web Apps utiliza o mesmo código que utiliza a funcionalidade de Azure Web Apps no serviço de aplicação do Azure. Como resultado, alguns conceitos comuns são património descrever. Nas aplicações Web, o contentor comparar para web apps é designado pelo plano de serviço de aplicação. Representa o conjunto de dedicada máquinas virtuais utilizado para guardar as suas aplicações. Dentro de uma subscrição determinada, pode ter vários planos de aplicação de serviço. Também é verdadeiro no Azure pilha Web Apps. 

No Azure, existem trabalhadores dedicados e partilhados. Trabalhador partilhado suporta alojamento de aplicação web multi-inquilino densidade e existe apenas um conjunto de trabalhadores partilhados. Servidores dedicados apenas são utilizados por um inquilino e tamanhos de três: pequena, médios e grandes. As necessidades de clientes no local sempre não podem ser descritas utilizando os termos. No Azure pilha Web Apps, os administradores do fornecedor de recursos podem definir as camadas de trabalho que pretendem disponibilizar assim que tiverem vários conjuntos de trabalhadores partilhados ou diferentes conjuntos de trabalhadores dedicados com base nas sua necessidades de alojamento na web exclusiva. Utilizar essas definições de camada de trabalho, podem, em seguida, definir os seus próprios preços SKUs.

## <a name="portal-features"></a>Funcionalidades de portais

Como é verdadeiro com o back-end, Azure pilha Web Apps utiliza a mesma IU que utiliza o Azure Web Apps. Algumas funcionalidades são desativadas e não ainda estão funcionais na pilha de Azure. Isto ocorre porque as expectativas do Azure específicas ou serviços que essas funcionalidades requerem que não estão ainda disponíveis na pilha de Azure. 

## <a name="next-steps"></a>Próximos passos

- [Antes de começar com o Azure pilha Web Apps](azure-stack-webapps-before-you-get-started.md)
- [Instalar o fornecedor de recursos de aplicações Web](azure-stack-webapps-deploy.md)

Também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md), como o [fornecedor de recursos do SQL Server](azure-stack-sql-rp-deploy-short.md) ou o [fornecedor de recursos do MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-overview/AppService_Portal.png
