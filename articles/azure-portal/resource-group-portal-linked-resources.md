<properties 
    pageTitle="Recursos relacionados e ligados na Galeria de mosaico" 
    description="Saiba mais sobre recursos relacionados e ligados que são apresentados na Galeria de mosaico do portal do Azure pré-visualização." 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Recursos relacionados e ligados na Galeria de mosaico

A Galeria de mosaico permite-lhe localizar os mosaicos de um determinado recurso e arraste-os para o seu pá atual. Utilizar a Galeria de mosaico, pode criar vistas de gestão que aparecem em recursos. Para qualquer recurso especificado, os recursos relacionados incluem todos os recursos que partilham o mesmo grupo de recursos, como o recurso e quaisquer recursos que liguem para ou a partir do recurso.

## <a name="linked-resources-in-azure-resource-manager"></a>Recursos ligados no Gestor de recursos do Azure

A ligação é uma funcionalidade do Gestor de recursos Azure.  Permite-lhe declarar relações entre recursos, mesmo que não residem no mesmo grupo de recursos. Ligação não tem impacto no runtime dos recursos, sem impacto na faturação e sem impacto acesso baseado em funções.  É simplesmente um mecanismo que pode utilizar para representar relações para que uma experiência de ferramentas, tal como a Galeria de mosaico pode fornecer uma gestão avançada.  As ferramentas podem inspecionar as ligações utilizando as ligações de API e fornecer a gestão das relações personalizado experiências também. 

## <a name="how-do-i-link-my-resources"></a>Como posso ligar os meus recursos?

Quando cria recursos através do portal do ou ao implementar um modelo através do Azure PowerShell ou clip de Azure, ligações são criadas automaticamente para alguns recursos dependentes. Pode ligar através de programação também recursos utilizando [Ligadas recursos REST API](https://msdn.microsoft.com/library/azure/mt238499.aspx) ou declarar as relações no modelo. Para um debate completo de trabalhar com recursos ligados, consulte o artigo [recursos de ligação no Gestor de recursos do Azure](../resource-group-link-resources.md).

## <a name="next-steps"></a>Próximos passos

- Se precisar de uma introdução para escrever o Gestor de recursos do Azure modelos, consulte o artigo [modelos de criação](../resource-group-authoring-templates.md).
- Para ficar aceder aos maior detalhes sobre como criar ligações entre recursos, consulte o artigo [recursos de ligação no Gestor de recursos do Azure](../resource-group-link-resources.md).
- Para compreender mais sobre como trabalhar com grupos de recursos através do portal de pré-visualização, consulte o artigo [utilizar o Portal de pré-visualização do Azure para gerir os recursos do Azure](resource-group-portal.md).
